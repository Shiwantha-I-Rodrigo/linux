# TROUBLESHOOTING

## STORAGE ISSUES

| **Issue**               | **Description / Commands**                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Degraded Storage**    | Gradual decay of storage medium due to usage.                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **Degraded Mode**       | One or more disks have failed in the RAID array.                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **Missing Devices**     | - For network storage, troubleshoot the network. <br> - Scan PCI-attached devices --> `$ lspci -M` <br> - Check the **/dev/** directory. <br>       - Rebuild any corrupted device file --> `$ mknod /dev/sda b 8 16` <br>       - `b` = block device <br>       - `8` = major number (device type-driver, ie. `sd`, `tty`) <br>       - `16` = minor number (specific device, ie. `sda`, `sdb`) <br>       - Use distro's major / minor number list to verify correct values|
| **Missing Volumes**     | Often caused by unintentionally removed devices.                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **Missing Mount Point** | - Create the mount point --> `$ mkdir`. <br> - Before deleting a directory, check if it's a mount point --> `$ mountpoint myDir`.                                                                                                                                                                                                                                                                                                                                 |
| **Storage Integrity**   | - Bad blocks do not respond to I/O requests. <br> - Use `$ badblocks` instead of `$ fsck` for more thorough checks. <br>       - Unmount device/partition first. <br>       - non-destructive test --> `$ badblocks -nsv /dev/sda2`                                                                                                                                                                                                                                       |
| **Performance Issues**  | - `$ iostat`, `$ ioping`, `$ iotop` <br> - Check drive read speed (PATA/SATA) --> `$ hdparm`<br> - iostat with additional system stats --> `$ dstat`.                                                                                                                                                                                                                                                                                                 |

---

### SPECIALIZED ISSUES

- check if driver updates are available.
- check if kernal module is loaded. ---> `$ dmesg | grep sde`
```
[ 5.500454 ] sd 6:0:0:0: [ sde ] Attched SCSI disk
```
- look for **SCSI** drivers in **/sys/** directory. ---> `$ ls /sys/bus/scsi/drivers`
```
sd  sr
```
- confirm which driver is in use. ---> `$ udevadm info -an /dev/sde | grep DRIVERS | grep sd`
```
DRIVERS=="sd"
```
- check if the module is loaded. ---> `$ lsmod | grep sd`
```
sd_mod    46322  5
```
- if not loaded, check biult-in modules. ---> `$ cat /lib/modules/$(uname -r)/modules.builtin | grep sd_mod`

- if not loaded or built-in dynamically load it. ---> `$ modprobe`

---

### SATA

* **SATA drives** are typically self-configuring.
* **SATA drive failures** can occur due to frequent head loading and unloading.
    + You can check this using ---> `$ smartctl -a /dev/sda`
    + Look at the **Start\_Stop\_Count** value : it indicates the number of load / unload cycles.
    ```
    ...
    ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
    1 Raw_Read_Error_Rate      0x000f   114   099   006    Pre-fail  Always       -       81917440
    3 Spin_Up_Time             0x0003   097   097   000    Pre-fail  Always       -       0
    4 Start_Stop_Count         0x0032   099   099   020    Old_age   Always       -       1894
    5 Reallocated_Sector_Ct    0x0033   100   100   010    Pre-fail  Always       -       0
    7 Seek_Error_Rate          0x000f   071   060   030    Pre-fail  Always       -       111318453
    9 Power_On_Hours           0x0032   087   087   000    Old_age   Always       -       11875
    10 Spin_Retry_Count        0x0013   100   100   097    Pre-fail  Always       -       0
    12 Power_Cycle_Count       0x0032   100   100   020    Old_age   Always       -       377
    183 Runtime_Bad_Block      0x0032   100   100   000    Old_age   Always       -       0
    ...
    ```
* Check **Advanced Power Management** behavior with ---> `$ hdparm -B /dev/sda`
    ```
    /dev/sda:
    APM_level	= 254
    ```
    | Value | Meaning                                                            |
    | ----- | ------------------------------------------------------------------ |
    | `1`   | Minimum power usage, very aggressive head parking (can cause wear) |
    | `127` | Balanced performance and power saving (no spindown)                |
    | `254` | Maximum performance, APM enabled (no power saving)                 |
    | `255` | APM disabled completely                                            |
> **!** set a moderate power management level ---> `$ hdparm -B 127 /dev/sda`

---

### SCSI

* The **SCSI framework** consists of three layers:
    + **Upper layer** : Device driver (ie. **sd**)
    + **Middle layer** : SCSI routing layer (ie. **scsi_mod**)
    + **Lower layer** : Host Bus Adapter (HBA) driver layer (ie. **ahci**)

```
$ udevadm info -an /dev/sda | grep -i drivers

ATTRS{driver}=="sd"                     # Kernal Device driver
ATTRS{pci_subsys_driver}=="ahci"        # HBA driver
```
```
$ lsmod | grep ahci

ahci        34056  3        # ahci module is loaded to kernal
```

> **!** **ahci** driver allows hot plugging SATA drives, which are treated as SCSI devices.\
> **!** after hot-plugging it must be enabled by rebooting the system or finding the host number and forcing a scan.
```
$ lsblk -S
...
sde     6:0:0:0     disk ATA    VHARDDISK   1.0  sata
...
```
```
$ echo '---' > /sys/class/scsi_host/host6/scan  # force a scan on host 6
```

---

### RAID

RAID status ---> **`$ cat /proc/mdstat`**

```
Personalities : [raid1] [raid6] [raid5] [raid4]
md0 : active raid1 sdb1[1] sda1[0]
      976630336 blocks [2/2] [UU]
      
md1 : active raid5 sdc1[0] sdd1[1] sde1[2]
      1953260544 blocks level 5, 64k chunk, algorithm 2 [3/3] [UUU]
      
unused devices: <none>

```

---

## APPLICATION PERMISSIONS

In case of I/O errors,

- which account is running the application.
- discover the action that raised the error.
- obtain a full directory reference for any files the app was atempting r/w operations.
- record any additional applications, it was trying to launch.
- record any services (local/remote), it was trying to employ.

Steps,

1. verify ownership of files and directories involved with `$ ls -l`.
2. check groups the account is part of.
3. if the app is executable by the user account.
4. check for execution permissions for the whole directory tree at the working directory.
5. check inheritance for target directory via ACL's.
6. set ACL permissions for file if required.
7. check for sticky bit, in case of denied file deletion.

---

## APPLICATION DEPENDENCIES

### VERSIONING

- management of multiple software updates through a numbering process.

### UPDATING

- in case of issues always check for updates.
- apply updates to a test environment if possible, before applying to production.
- check the update history with :
    + APT --> **/var/log/apt/history.log**
    + RPM --> `$ rpm -q [ package ] --last`
- **broken dependencies AKA unmet dependencies** causes **broken packages**.
- look for unmet dependencies :
    + APT --> `$ apt-get check`
    + RPM --> `$ rpm -aV`

### PATCHING

- program changes or configuration file updates, issued **out of normal update cycle**. usually to fix serious problems or security vunerabilities.

### LIBRARIES

- Application functions are often split into seperate library files, so mutiple Applications can share the library.
- `$ lld [ program name ]` : list librarries used by a program.
```
$ which ssh
/usr/bin/ssh

$ lld /usr/bin/ssh > ssh_libs.txt

$ cat ssh_libs.txt
...
lib5crypto.so.3 ...
...

$ grep -B 2 -A 2 lib5crypto /var/log/apt/history.log        # A and B pulls additional lines Above and Below the result.

Start-Date: 2025-08-10  10:15:42
Commandline: apt install lib5crypto
Install: lib5crypto:amd64 (1.2.3-1ubuntu1)
End-Date: 2025-08-10  10:15:45
```

### ENVIRONMENT VARIABLES

- Check if app is availabe on $PATH.
- Mod environment variables 
    + everyone ---> **/etc/profile.d/** ( file must be owned by root and root group with others having permission to read )
    + certain users ---> **~/.profile** OR **~/.bash_profile** OR **~/.bash_login**.

### GCC COMPATIBILITY

- if there are issues compiling with **gcc**.
    + the system **C** lib might not be complient with the **ISO C** standards used by the **gcc**.
    + incompatibilities with **GNU C** and **non-ISO** versions of **C**.
    + **gcc** uses corrected versions of system header files.

### REPOSITORY ISSUES

| **Task**                                  | **APT (Debian/Ubuntu)**                                                           | **YUM (RHEL/CentOS)**|
| -                                         | -                                                                                 | -  |
| Check network connectivity                | `ping`                                                                            | `ping`|
| Clean temp downloads and package database | `apt clean`                                                                       | `yum clean all`|
| Upgrade without breaking dependencies     | `apt dist-upgrade`                                                                | (YUM handles this during `yum update`)|
| View repository list                      | `grep -v "#" /etc/apt/sources.list`                                               | `yum repolist`|
| Manually add repository                   | `echo "deb [URL] $(lsb_release -cs) main" \| tee -a /etc/apt/sources.list`        | `yum-config-manager --add-repo [URL]`|
| Manually import public repository GPG key | `curl -L [GPG URL] \| apt-key add -`                                              | `rpm --import [GPG URL]`|
| Update package database                   | `apt update`                                                                      | `yum update`|

---

## SELINUX CONTEXT VIOLATIONS

| **Tool**              | **Purpose**|
| -                     | -|
| `sealert`             | Tool from **setroubleshoot** package to analyzes SELinux Access Vector Cache denials and gives **human-readable** solutions|
| `ls -Z`               | Shows the **SELinux context labels** for files and directories|
| `chcon`               | Temporarily **change the SELinux context** of a file or directory|
| `semanage fcontext`   | Permanently define the correct context for files or paths.|
| `restorecon`          | Automatically **restore default SELinux labels** based on current policy|

---

## FIREWALL

### ACL's

- ACL reviews a packets control information,
    + source address / hostname.
    + destination address / hostname.
    + network protocols used.
    + inbound port.
    + outbound port.

- `$ firewall-cmd --get-default-zone`\
--> check default zone ( ie, drop will **drop** all incoming traffic )
```
public
```

---

### PORTS

re-check firewall rules,
- if the application rely on other system services.
- if the service runs on a not well known port.

---

### PROTOCOLS

- if the application uses a port (port 53).
- check **/etc/serices** for protocols used by the port.
```
$ grep 53 /etc/services

domain      53/tcp
domain      53/udp
```
- unblock port 53 for the required protocols ( **tcp** and **udp** ).

---

## HARDWARE ISSUES

**`dmidecode `**`[ option ]`\
--> Linux utility used to access **DMI/SMBIOS** data structures.

* The **Distributed Management Task Force (DMTF)** is a non-profit organization focused on simplifying the management of network-accessible technologies.
* The **Desktop Management Interface ( DMI )** consists of four components that provide information about system hardware.
* To access this information, a computer must be compliant with **DMI / System Management BIOS (SMBIOS)** standards and have a supporting software interface.
* **sysfs** is the virtual filesystem mounted at **/sys/** and does not store data on disk, but is generated at runtime by the kernal.
* **dmidecode** retrievs the information using **sysfs** from,
    + **/sys/firmware/dmi/tables/DMI** – the actual DMI table (raw binary data).
    + **/sys/firmware/dmi/tables/smbios_entry_point** – metadata that tells the system how to interpret the DMI data.

| Short | Long Option   | Argument      | Description|
| -     | -             | -             | - |
| `-d`  | `--dev-mem`   | `[FILE]`      | Read memory from the specified device file (ie. `/dev/mem`)|
| `-q`  | `--quiet`     |               | Less verbose output|
| `-s`  | `--string`    | `[KEYWORD]`   | Display the value of the specified DMI string|
| `-t`  | `--type`      | `[TYPE]`      | Display entries of the given type<br>- baseboard / bios / cache / chassis / connector<br>- memory / processor / slot / system|
| `-u`  | `--dump`      |               | Do not decode entries; just dump raw data|
|       | `--dump-bin`  | `[FILE]`      | Dump DMI data to a binary file|
|       | `--from-dump` | `[FILE]`      | Read DMI data from a previously saved binary file|
|       | `--no-sysfs`  |               | Do not attempt to read data from sysfs|
| `-V`  | `--version`   |               | Display the version and exit|

```
$ dmidecode -t system

# dmidecode 3.4
Getting SMBIOS data from sysfs.
SMBIOS 3.2.0 present.

Handle 0x0001, DMI type 1, 27 bytes
System Information
    Manufacturer: Dell Inc.
    Product Name: XPS 15 9570
    Version: Not Specified
    Serial Number: ABCD123
    UUID: 4c4c4544-0039-4410-8053-b4c04f544332
    Wake-up Type: Power Switch
    SKU Number: 0812
    Family: XPS
```

> **!** the information from **dmidecode** is inaccurate in virtual systems.

---

**`lshw `**`[ OPTIONS ]`\
--> provides detailed information about the system’s hardware configuration.

| Option        |  Description|
| -             | - |
| `-short`      | Displays hardware information in a **table format**, showing path, device, class, and description|
| `-businfo`    | Shows information specific to **SCSI**, **USB**, **IDE**, and **PCI** buses|
| `-class`      | Displays information about a **specific class** of hardware (ie. `cpu`, `memory`, `network`)|

```
$ lshw -short
H/W path        Device      Class          Description
======================================================
                            system         Virtual Machine
/0                          bus            Motherboard
/0/0                        memory         4GiB System Memory
/0/0/0                      memory         4GiB DIMM DDR4 Synchronous
/0/1                        processor      Intel(R) Core(TM) i5-8250U CPU @ 1.60GHz
/0/1/0.1                    processor      Logical CPU
/0/1/0.2                    processor      Logical CPU
/0/1/0.3                    processor      Logical CPU
/0/1/0.4                    processor      Logical CPU
/0/1/0.5                    processor      Logical CPU
/0/1/0.6                    processor      Logical CPU
/0/1/0.7                    processor      Logical CPU
/0/2                        firmware       BIOS
/0/3                        memory         256KiB L2 cache
/0/4                        memory         1MiB L3 cache
/0/5                        memory         64KiB L1 cache
/0/6                        processor      CPU Core
/0/100                      bridge         Intel Corporation 8th Gen Core Processor Host Bridge/DRAM Registers
/0/100/1                    bridge         Intel Corporation UHD Graphics 620
/0/100/1.1      /dev/fb0    display        VGA compatible controller
/0/100/14       usb1        bus            xHCI Host Controller
/0/100/14/0                 input          USB Keyboard
/0/100/14/1                 input          USB Mouse
/0/100/15                   generic        Intel Corporation Serial IO I2C Host Controller
/0/100/1f                   bridge         Intel Corporation LPC Controller
/0/100/1f.2     /dev/sda    disk           256GB SSD
/0/100/1f.2/0   /dev/sda1   volume         EFI System Partition
```

> **!** `lshw` uses **/proc/** & **/sys/**.

---

### OTHER ISSUES

| **Category**              | **Checks / Symptoms**                                                                                                                             | **Commands / Tools**|
| -                         | -                                                                                                                                                 | - |
| **Memory**                | - System slows over time<br>- Hangs at boot or under load<br>- Kernel panics / segmentation faults<br>- Program installation fails    | - Memory usage: `free`, `vmstat`<br>- Test memory: `memtest` on boot|
| **Printers**              | - Driver issues<br>- Compatibility                                                                                                    | - Error Logs: `/var/log/cups/error_log`<br>- Web admin: `127.0.0.1:631`<br>- Available drivers: `lpinfo -m`|
| **Video**                 | - Graphics-related issues                                                                                                                         | - Kernel ring buffer: `dmesg`<br>- X11 log: `/var/log/Xorg.0.log`<br>- Wayland: `journalctl`<br>- Graphics drivers: `lspci -vnn`<br>- Search VGA: `lspci > file; grep vga file`<br>- Manufacturer tools: `nvidia-smi`, `nvidia-settings`|
| **Communication Ports**   | - Device not detected<br>- IRQ conflicts or missing IRQ                                                                                           | - Identify device: `dmesg\| grep ttyS`<br>- Check settings: `setserial -a [device-name]`<br>- IRQs: `/proc/interrupts`|
| **USB**                   | - Device not showing up<br>- USB not working                                                                                                      | - Check module: `lsmod\|grep usb`<br>- Load module: `modprobe [usb_module]`<br>- Live logs: `journalctl -f`<br>- Device list: `lsusb -v`<br>- Check buffer: `dmesg`<br>- Check device file for corruption|
| **Keyboard**              | - Incorrect key mapping                                                                                                                           | - View keymap: `localectl` (Red Hat)<br>- List maps: `localectl list-keymaps`<br>- Set map: `localectl set-keymap [name]`<br>- Debian config menu: `dpkg-reconfigure keyboard-configuration`|
