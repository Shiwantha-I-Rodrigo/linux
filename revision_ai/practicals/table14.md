### **A Day in the Life of Alex, the Linux SysAdmin**

**8:00 AM – Starting the Day**

Alex logs into the office server to check if everything has been running smoothly overnight. The first thing they do is check the **kernel logs** to see if there were any errors reported during the nightly batch jobs.

```bash
dmesg | tail -20
```

> **Context:** `dmesg` shows the kernel ring buffer. Using `tail -20` lets Alex quickly see the most recent 20 log entries for any potential hardware or driver issues.

---

**8:30 AM – Inspecting Hardware Information**

A colleague reports that the server’s CPU temperature seems high. Alex decides to pull hardware information straight from the BIOS using:

```bash
sudo dmidecode -t processor
```

> **Context:** `dmidecode` reads hardware information from the BIOS/SMBIOS. The `-t processor` option filters the output to only show CPU information.

Next, Alex wants a **detailed summary of all hardware**, including memory, storage, and network interfaces:

```bash
sudo lshw -short
```

> **Context:** `lshw -short` provides a concise overview of all hardware, which is easier to scan than the full verbose output.

---

**9:00 AM – Kernel Module Management**

Alex notices a custom network driver isn’t loaded. First, they list all currently loaded kernel modules:

```bash
lsmod
```

> **Context:** `lsmod` lists all active kernel modules. It helps identify if a module is missing or conflicting.

To get more information about a specific module:

```bash
modinfo e1000e
```

> **Context:** `modinfo` shows the version, dependencies, and author of a kernel module. Here, Alex checks the Intel NIC driver.

The driver isn’t loaded, so Alex inserts it manually:

```bash
sudo insmod /lib/modules/$(uname -r)/kernel/drivers/net/e1000e.ko
```

> **Context:** `insmod` inserts a module into the running kernel. Absolute paths are required.

Later, Alex decides to **remove a testing module**:

```bash
sudo rmmod test_module
```

> **Context:** `rmmod` removes a module from the kernel.

For modules with dependencies, Alex uses `modprobe` instead:

```bash
sudo modprobe -r old_network_module
sudo modprobe new_network_module
```

> **Context:** `modprobe` handles dependencies automatically. `-r` removes a module, while the second command loads the new one.

Finally, Alex updates the **module dependency map** to ensure everything loads correctly after a kernel update:

```bash
sudo depmod
```

---

**10:00 AM – System Overview**

Alex wants to quickly verify the **kernel version and uptime**:

```bash
uname -r
uptime
```

> **Context:** `uname -r` shows the kernel release. `uptime` shows how long the system has been running and the load averages.

Then they check **memory usage**:

```bash
free -h
```

> **Context:** `free -h` shows memory in human-readable format, making it easier to spot memory pressure.

---

**11:00 AM – File and Disk Checks**

Alex needs to check if a critical log file is accessible and healthy:

```bash
stat /var/log/syslog
file /var/log/syslog
```

> **Context:** `stat` shows file metadata (size, permissions, timestamps). `file` identifies the file type, ensuring it’s a normal text log and not corrupted.

Next, they check the **health of a disk** used for database storage:

```bash
sudo smartctl -a /dev/sda
```

> **Context:** `smartctl -a` outputs all SMART info, including bad sectors and temperature.

Alex also tunes disk power management:

```bash
sudo hdparm -B /dev/sda
```

> **Context:** `-B` shows the current APM (Advanced Power Management) level for the disk.

To test **disk latency**, Alex runs a quick benchmark:

```bash
ioping -c 10 /var/lib/mysql
```

> **Context:** `ioping` tests disk latency. `-c 10` sends 10 requests to check response times in the database directory.

---

**12:00 PM – Locating Commands**

Alex is documenting a new procedure and needs to locate the `modprobe` binary:

```bash
whereis modprobe
which -a modprobe
```

> **Context:** `whereis` finds the binary, source, and man pages. `which -a` shows all matching executables in PATH, not just the first one.

---

**1:00 PM – Wrapping Up**

After a morning of hardware checks, kernel module adjustments, and disk health verifications, Alex logs everything in the system maintenance report. They feel confident the servers are healthy and optimized for the afternoon’s workload.

---
