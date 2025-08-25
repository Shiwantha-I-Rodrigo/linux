# 🔹 Scenarios

## **Scenario 1: Kernel & Boot Troubleshooting**

After a reboot, you suspect that a recently connected USB device is not being detected by the kernel. You want to:

1. Inspect recent kernel messages.
2. Check which kernel modules are currently loaded.
3. View detailed information about the suspicious USB module.
4. Remove the USB module, then reload it with dependency management.
5. Rebuild the modules dependency map to ensure consistency.
6. Confirm the system’s kernel release version.

---

## **Scenario 2: Investigating Hardware Health**

Your company’s server has been showing slow performance and frequent freezes. You decide to:

1. Extract hardware details (CPU, memory, storage) in a concise format.
2. Limit output to only CPU-related details.
3. Display the system BIOS version using a specific system string.
4. Quietly dump the full BIOS information with minimal verbosity.
5. Save the raw hex-dump format of BIOS data for later review.
6. Use another utility to check detailed disk health (SMART data).
7. Measure disk latency with multiple requests.
8. Display the drive’s power management level.

---

## **Scenario 3: Performance Monitoring & Uptime Audit**

A customer reports that their web server feels “laggy.” You want to:

1. Check how long the server has been running and its load averages.
2. Measure free memory availability.
3. Confirm CPU usage and load averages through kernel logs again.
4. Cross-check the currently loaded modules to verify hardware driver status.
5. Ensure the disk isn’t under abnormal latency pressure with a test run.

---

## **Scenario 4: File & System Investigation**

You receive suspicious files from a developer’s directory. You need to:

1. Identify the type of each file.
2. Get detailed status information (permissions, ownership, timestamps).
3. Locate where a commonly used system binary is installed.
4. Confirm which executable version of a command will be executed when run from the shell.

**Example files (create these in `/tmp`):**

* `hello.txt` → contains: `Hello RockyOS / Ubuntu / Fedora!`
* `script.sh` → contains:

  ```bash
  #!/bin/bash
  echo "System check in progress..."
  ```
* `data.bin` → random binary file (simulate with `dd if=/dev/urandom of=/tmp/data.bin bs=1K count=1`)

---

## **Scenario 5: Advanced Kernel Module Testing**

You’re writing a custom kernel driver module for a lab test. You need to:

1. Insert your custom driver located in `/home/dev/test_driver.ko`.
2. Verify that the driver was properly inserted.
3. Get more info about the driver.
4. Test removing the driver manually.
5. Re-insert the driver automatically resolving dependencies.
6. Confirm that it was added back successfully.

---

## **Scenario 6: BIOS Dump & Analysis for Compliance**

You’re auditing server BIOS info for compliance. To avoid running live commands on production, you:

1. Dump the BIOS info to a binary file.
2. Later, read the binary dump file to extract hardware info.
3. Confirm the dump works by reloading it using the dedicated flag.

---

## **Scenario 7: PCI Device Mapping with Bus Info**

A technician needs to know exactly which PCI slot a NIC is plugged into. Display the bus mapping of hardware devices.

---

## **Scenario 8: Removing a Kernel Module with Dependencies**

Instead of manually unloading a driver, you want to remove it and its dependent modules cleanly.

---

# 🔹 Answer Key

## **Scenario 1: Kernel & Boot Troubleshooting**

```bash
dmesg | tail
lsmod
modinfo usb_storage
rmmod usb_storage
modprobe usb_storage
depmod
uname -r
```

*Output snippets:*

```
usb 1-1: new high-speed USB device detected
usb_storage: USB Mass Storage device detected
```

```
usb_storage  77824  1
```

```
filename: /lib/modules/.../usb-storage.ko.xz
description: USB Mass Storage driver
```

```
5.14.0-362.el8.x86_64
```

---

## **Scenario 2: Investigating Hardware Health**

```bash
lshw -short
lshw -class cpu
dmidecode -s bios-version
dmidecode -q
dmidecode -u
smartctl -a /dev/sda
ioping -c 5 /dev/sda
hdparm -B /dev/sda
```

*Output snippets:*

```
/0/0   memory  16GiB System memory
/0/1   cpu     Intel(R) Xeon(R) CPU E5-2670 v3
```

```
F34.B01
```

```
SMART overall-health test result: PASSED
Temperature_Celsius: 30
```

```
--- /dev/sda ioping statistics ---
5 requests completed in 4.5 ms, avg = 900 us
```

```
/dev/sda: APM_level = 254
```

---

## **Scenario 3: Performance Monitoring & Uptime Audit**

```bash
uptime
free -h
dmesg | tail -n 3
lsmod | grep e1000e
ioping -c 3 /
```

*Output snippets:*

```
14:55:32 up 15 days, load average: 0.25, 0.18, 0.12
```

```
Mem: 16G used=12G free=1.5G
```

```
eth0: NIC Link is Up 1000 Mbps
```

---

## **Scenario 4: File & System Investigation**

```bash
file /tmp/hello.txt /tmp/script.sh /tmp/data.bin
stat /tmp/hello.txt /tmp/script.sh /tmp/data.bin
whereis bash
which -a bash
```

*Output snippets:*

```
/tmp/hello.txt: ASCII text
/tmp/script.sh: Bourne-Again shell script
/tmp/data.bin: data
```

```
bash: /usr/bin/bash /usr/share/man/man1/bash.1.gz
```

```
/usr/bin/bash
/bin/bash
```

---

## **Scenario 5: Advanced Kernel Module Testing**

```bash
insmod /home/dev/test_driver.ko
lsmod | grep test_driver
modinfo test_driver
rmmod test_driver
modprobe test_driver
lsmod | grep test_driver
```

*Output snippets:*

```
test_driver   16384  0
```

```
description: Test Kernel Driver
author: Dev Engineer
```

---

## **Scenario 6: BIOS Dump & Analysis for Compliance**

```bash
dmidecode > /tmp/bios_dump.txt
dmidecode -d /tmp/bios_dump.txt
dmidecode --from-dump /tmp/bios_dump.txt
```

*Output snippet:*

```
BIOS Information
Vendor: American Megatrends Inc.
Version: F34.B01
```

---

## **Scenario 7: PCI Device Mapping with Bus Info**

```bash
lshw -businfo
```

*Output snippet:*

```
pci@0000:00:19.0  eth0  network  Ethernet Controller (Intel I219-LM)
pci@0000:00:1f.2  scsi@0:0.0  storage  SATA controller
```

---

## **Scenario 8: Removing a Kernel Module with Dependencies**

```bash
modprobe -r usb_storage
```

*Output:*

```
(usb_storage removed with dependencies automatically)
```

---