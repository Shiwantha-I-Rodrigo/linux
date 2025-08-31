## **Workflow 1: Kernel and Hardware Diagnostics After a System Crash**

### Scenario:

A server suddenly crashed and rebooted. You need to check kernel messages and hardware details to pinpoint the issue.

**Steps:**

1. **Check kernel ring buffer logs**

   ```bash
   dmesg | less
   ```

   * Review recent kernel messages for errors or warnings.
   * Look for lines with `error`, `fail`, or `panic`.

2. **Check kernel version**

   ```bash
   uname -r
   ```

   * Confirms which kernel the system booted with.
   * Useful if a recent kernel update may have caused instability.

3. **Review loaded kernel modules**

   ```bash
   lsmod | grep <module_name>
   ```

   * Ensures necessary drivers are loaded.
   * Can identify modules causing problems (e.g., `nvme`, `ahci`).

4. **Inspect module info if a module seems suspicious**

   ```bash
   modinfo <module_name>
   ```

   * Displays version, dependencies, and file path.

5. **Check hardware via BIOS/SMBIOS**

   ```bash
   dmidecode -t memory
   dmidecode -t processor
   ```

   * Quickly identifies hardware components and firmware versions.
   * Detects mismatched memory or CPU errors.

6. **Detailed hardware overview**

   ```bash
   lshw -short
   ```

   * Confirms the devices recognized by Linux and their statuses.
   * Focus on `disk`, `cpu`, `memory`, and `network`.

7. **Disk health check if crash may relate to storage**

   ```bash
   smartctl -a /dev/sda
   ```

   * Shows SMART status and detects potential drive failures.
   * Look for `Reallocated_Sector_Ct` or `Pending_Sector` issues.

**Explanation:**
This workflow is typical after kernel panics, freezes, or crashes. Using `dmesg` plus `dmidecode`/`lshw` allows quick correlation between kernel messages and hardware status.

**Options coverage:**

* `dmesg`: 100% (no options commonly needed)
* `uname -r`: 100% (most used)
* `dmidecode -t`: 50% (other options less frequently used)
* `lshw -short`: 33% (other options like `-class` or `-businfo` are more niche)
* `smartctl -a`: 100% (standard)

---

## **Workflow 2: Memory and CPU Troubleshooting**

### Scenario:

A server is experiencing high memory usage, affecting application performance.

**Steps:**

1. **Check memory usage**

   ```bash
   free -h
   ```

   * Shows total, used, and available memory.
   * The `-h` flag gives human-readable sizes.

2. **Check system load and uptime**

   ```bash
   uptime
   ```

   * Provides load averages to correlate with memory consumption.

3. **Identify memory-hogging processes**

   ```bash
   ps aux --sort=-%mem | head -n 10
   ```

   * Shows top 10 processes by memory usage.

4. **Check hardware memory details**

   ```bash
   dmidecode -t memory
   ```

   * Confirms installed DIMMs, size, speed, and slot population.

5. **Optional: detailed hardware class filtering**

   ```bash
   lshw -class memory
   ```

   * Useful if multiple memory banks exist and specific attributes are needed.

**Explanation:**
Combining OS-level memory stats with hardware verification ensures that memory-related issues are identified both in software usage and physical components.

**Options coverage:**

* `free`: 100% (no options listed, `-h` standard in daily use)
* `uptime`: 100%
* `dmidecode -t`: 50%
* `lshw -class`: 33%

---

## **Workflow 3: Kernel Module Troubleshooting and Management**

### Scenario:

A new kernel module isn’t loading correctly, and an application fails to start.

**Steps:**

1. **Check if module is already loaded**

   ```bash
   lsmod | grep <module_name>
   ```

2. **View module info**

   ```bash
   modinfo <module_name>
   ```

   * Confirms version, dependencies, and author info.

3. **Load module manually (if missing)**

   ```bash
   sudo modprobe <module_name>
   ```

   * Automatically resolves dependencies.

4. **Remove module if malfunctioning**

   ```bash
   sudo modprobe -r <module_name>
   ```

   * Cleans up dependencies before reloading.

5. **Force insert module from path if needed**

   ```bash
   sudo insmod /lib/modules/$(uname -r)/kernel/drivers/<module_name>.ko
   ```

6. **Update module dependency map**

   ```bash
   sudo depmod -a
   ```

   * Ensures all modules dependencies are registered.

**Explanation:**
Module issues are common after kernel upgrades or driver changes. This workflow ensures modules are properly loaded and dependencies resolved.

**Options coverage:**

* `modprobe -r`: 50%
* Other commands (`lsmod`, `modinfo`, `insmod`, `depmod`): 100%

---

## **Workflow 4: File and Disk Investigation**

### Scenario:

You suspect a file corruption issue or disk latency problems.

**Steps:**

1. **Check file type and status**

   ```bash
   file /path/to/file
   stat /path/to/file
   ```

   * Ensures the file is not corrupted or misidentified.

2. **Test disk latency**

   ```bash
   ioping -c 10 /mnt/data
   ```

   * Measures disk response times for troubleshooting performance issues.

3. **Check disk power management**

   ```bash
   sudo hdparm -B /dev/sda
   ```

   * Verifies if aggressive APM settings are affecting performance.

**Explanation:**
This workflow is useful when performance or file corruption issues are suspected, combining file-level checks with disk diagnostics.

**Options coverage:**

* `ioping -c`: 100%
* `hdparm -B`: 100%
* `file`, `stat`: 100% (most commonly used without extra options)

---

## **Summary of Options Used in Workflows**

| Command   | Option(s) Used | % Coverage |
| --------- | -------------- | ---------- |
| dmesg     | none           | 100%       |
| uname     | -r             | 100%       |
| dmidecode | -t             | 50%        |
| lshw      | -short, -class | 33%        |
| lsmod     | none           | 100%       |
| modinfo   | none           | 100%       |
| insmod    | none           | 100%       |
| rmmod     | none           | 100%       |
| modprobe  | -r             | 50%        |
| depmod    | none           | 100%       |
| uptime    | none           | 100%       |
| free      | -h             | 100%       |
| stat      | none           | 100%       |
| file      | none           | 100%       |
| smartctl  | -a             | 100%       |
| hdparm    | -B             | 100%       |
| ioping    | -c             | 100%       |
| which     | -a             | 100%       |

---
