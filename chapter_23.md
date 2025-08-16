# LINUX DEVICES

## COMMUNICATING WITH DEVICES

> **!** Linux uses installed modules to communicate with devices and supports various types of hardware interfaces.

### PCI BOARDS *( Peripheral Component Interconnect )*

**PCI Express** is the current itteration.

| **Device Type**           | **Connection/Standard**                   | **Notes**|
| -                         | -                                         | - |
| Internal Hard Drives      | SATA or SCSI (via PCI)                    | Common internal storage interfaces|
| External (Network) Drives | Fiber Channel (via PCI with HBA support)  | High-speed external storage|
| Network Interface Cards   | RJ-45 (Ethernet standard)                 | Used for wired networking|
| Wireless Cards            | IEEE 802.11 (Wi-Fi standard)              | Enables wireless connectivity|
| Bluetooth Devices         | Bluetooth standard                        | Wireless peripheral connectivity|
| Video Accelerators        | PCI-based (ie. GPUs)                      | Used for gaming, video processing, and blockchain |
| Audio Cards               | PCI or USB                                | Enhances sound processing and output|

---

### USB *( Universal Serial Bus )*

| **USB Version** | **Maximum Speed** |
| --------------- | ----------------- |
| 1.0             | 12 Mbps           |
| 2.0             | 480 Mbps          |
| 3.2 Gen 1       | 5 Gbps            |
| 3.2 Gen 2       | 10 Gbps           |
| 4               | 40 Gbps           |

> **!** Both the module for the **USB controller** and the module for the connected **USB device** must be loaded in that order.

---

### GPIO *( General Purpose Input Output )*

- popular with small utility Linux systems designed for controlling external devices.
- provides multiple I/O lines, that can control outputs to single bit level.
- read values from any analog to digital sensor.

---

### */dev* DIRECTORY

Linux communicates with devices through **interface files**, each created in the **/dev/** directory.\
These files allow applications to easily perform I/O operations with the devices.\
There are two types of device files:
* **Character device files** – transfer data one character at a time (ie. terminal, USB mouse, USB keyboard).
* **Block device files** – transfer data in blocks (ie. USB mass storage, network cards, HDD).
```
$ ls -al

brw-rw----  1   root    disk    8, 0 Feb 16 17:49 sda
brw-rw----  1   root    disk    8, 1 Feb 16 17:49 sda1
crw-rw-rw-  1   root    tty     5, 0 Feb 16 17:49 tty
crw--w----  1   root    tty     4, 0 Feb 16 17:49 tty0
```

> the first char of the output indicates the device file type.

**Special device files,**

| **Device File** | **Function**                                                            |
| --------------- | ----------------------------------------------------------------------- |
| `/dev/null`     | Discards all input sent to it; acts as a data sink.                     |
| `/dev/random`   | Provides random numbers; blocks until sufficient entropy is available.  |
| `/dev/urandom`  | Provides random numbers from available entropy; faster but less secure. |
| `/dev/zero`     | Outputs null characters (zeros).                                        |


> **!** In addition to device files, Linux also provides **device mapper** files.\
> **!** The device mapper maps **physical block devices** to **virtual block devices**,\
> **!** Allowing the kernel to intercept data and perform operations on it, either after reading from or before writing to the physical blocks.\
> **!** (ie. LVM, LUKS)

---

### */proc* DIRECTORY

The **/proc** directory is populated by the kernel with files that contain real-time system information.\
These files can be read using standard text commands.

**IRQ (Interrupt Requests)**

- **/proc/interrupts** file.
- IRQ's allow hardware devices to indicate when they have data to send to the CPU.
- first column indicates the IRQ assigned to the device.
- some IRQ's are reserved by the system ( ie. 0 for system timer).
```
$ cat /proc/inturrupts

           CPU0       CPU1       CPU2       CPU3
  0:         45          0          0          0   IO-APIC-edge      timer
  1:          2          0          0          0   IO-APIC-edge      i8042
  8:          0          1          0          0   IO-APIC-edge      rtc0
  9:          0          0          0          0   IO-APIC-fasteoi   acpi
 12:         19          0          0          0   IO-APIC-edge      i8042
 16:      12345          0          0          0   IO-APIC-fasteoi   eth0
 23:          0          0          0          0   IO-APIC-fasteoi   ehci_hcd:usb1
 24:          0          0          0          0   PCI-MSI-edge      eth1
 25:          0          0          0          0   PCI-MSI-edge      snd_hda_intel
```

**I/O Ports**

- **/proc/ioports** file.
- I/O ports are locations in memory.
- allow CPU to store and send data to and from hardware devices.
- each device is assigned a unique I/O port.
- ie. Keyboard / Mouse / Printer / NIC / ...
```
$ cat /proc/ioports

0000-001f : dma1
0020-003f : pic1
0040-005f : timer0
0060-0060 : keyboard
0064-0064 : keyboard
0070-0077 : rtc0
0080-008f : dma2
00a0-00bf : pic2
00f0-00ff : dma page registers
0100-010f : i8042
03f6-03f6 : floppy
03f8-03ff : serial0
0378-037f : parallel0
0400-041f : video
04d0-04d1 : i8042
0cf8-0cff : pci configuration
```

**Direct Memory Access**

- **/proc/dma** file.
- similar function to I/O ports.
- CPU setup the transfer but doesn't handle the actual data movement.
- faster than I/O ports.
- primarily a hardware process, software only initiates the process.
- ie. SSD / VGA / ...

---

### */sys* DIRECTORY

- kernal populates it with files containing system information.
- any user can access the data.
- broken down to sub directories based on device and function.
```
$ ls -l /proc/sys

total 0
dr-xr-xr-x 1 root root 0 Aug 15 12:00 abi
dr-xr-xr-x 1 root root 0 Aug 15 12:00 kernel
dr-xr-xr-x 1 root root 0 Aug 15 12:00 net
dr-xr-xr-x 1 root root 0 Aug 15 12:00 vm
dr-xr-xr-x 1 root root 0 Aug 15 12:00 fs
dr-xr-xr-x 1 root root 0 Aug 15 12:00 debug
dr-xr-xr-x 1 root root 0 Aug 15 12:00 crypto
```

---

## WORKING WITH DEVICES

### FINDING DEVICES

---> **lsdev** ( *procinfo package* )
```
Device            DMA   IRQ  I/O Ports
------------------------------------------------
9  ELAN0515:00          71 
0000:01:00.0                     0000-0000
ACPI                             0000-0000     0000-0000     0000-0000     0000-0000   0000-0000
acpi                      9 
ahci[0000:06:00.1]         37 
amdgpu                   81 
cascade             4       
dma                            0000-0000
dma1                           0000-0000
dma2                           0000-0000
keyboard                       0000-0000   0000-0000
nvidia                   57 
```

---> **lsblk**
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0   100G  0 disk 
├─sda1   8:1    0    50G  0 part /
├─sda2   8:2    0    30G  0 part /home
└─sda3   8:3    0    20G  0 part [SWAP]
sdb      8:16   0   500G  0 disk 
└─sdb1   8:17   0   500G  0 part /mnt/data
sr0     11:0    1   4.3G  0 rom  /media/cdrom
```

**\-S** : only display info about SCSI block devices.


---> **dmesg**
```
[    0.000000] Linux version 5.15.0-85-generic (buildd@lcy02-amd64-018) ...
[    0.200123] ACPI: bus type PCI registered
[    0.201045] PCI: Using configuration type 1 for base access
[    0.310567] usbcore: registered new interface driver usbfs
[    0.310580] usbcore: registered new interface driver hub
[    0.310600] usbcore: registered new device driver usb
[    0.512345] ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
[    0.512567] ehci-pci 0000:00:1a.0: EHCI Host Controller
[    0.512890] ehci-pci 0000:00:1a.0: new USB bus registered
[    0.713456] SCSI subsystem initialized
[    1.456789] scsi 0:0:0:0: Direct-Access     ATA      Samsung SSD 860 1B6Q PQ: 0 ANSI: 5
[    1.567890] sd 0:0:0:0: [sda] 976773168 512-byte logical blocks: (500 GB/465 GiB)
[    1.678901] sd 0:0:0:0: [sda] Write Protect is off
[    1.789012] sd 0:0:0:0: [sda] Mode Sense: 00 3a 00 00
[    1.890123] sd 0:0:0:0: [sda] Attached SCSI disk
[    2.012345] input: Logitech USB Optical Mouse as /devices/pci0000:00/0000:00:1d.0/usb3/3-1/3-1:1.0/input/input3
[    2.123456] hid-generic 0003:046D:C077.0001: input,hidraw0: USB HID v1.11 Mouse [Logitech USB Optical Mouse] on usb-0000:00:1d.0-1/input0
[    2.234567] usbcore: registered new interface driver usbhid
[    2.234568] usbhid: USB HID core driver
[    2.345678] EXT4-fs (sda1): mounted filesystem with ordered data mode. Opts: (null)
[    2.012345] input: Logitech USB Optical Mouse as /devices/pci0000:00/0000:00:1d.0/usb3/3-1/3-1:1.0/input/input3
```

The **kernel ring buffer** records kernel-level events and automatically overwrites old messages in a rotating manner.\
shows steps taken by kernal in recognizing the device and installing correct modules.

---

### PCI DEVICES

**lspci** ---> view currently installed PCI / PCIe devices.
```
00:18.5 Host bridge: Advanced Micro Devices, Inc. [AMD] Renoir Device 24: Function 5
00:18.6 Host bridge: Advanced Micro Devices, Inc. [AMD] Renoir Device 24: Function 6
00:18.7 Host bridge: Advanced Micro Devices, Inc. [AMD] Renoir Device 24: Function 7
01:00.0 3D controller: NVIDIA Corporation TU117M [GeForce GTX 1650 Mobile / Max-Q] (rev a1)
02:00.0 Non-Volatile memory controller: SK hynix Gold P31/BC711/PC711 NVMe Solid State Drive
03:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet Controller (rev 15)
04:00.0 Network controller: MEDIATEK Corp. MT7921 802.11ax PCI Express Wireless Network Adapter
05:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Lucienne (rev c2)
05:00.1 Audio device: Advanced Micro Devices, Inc. [AMD/ATI] Renoir Radeon High Definition Audio Controller
05:00.2 Encryption controller: Advanced Micro Devices, Inc. [AMD] Family 17h (Models 10h-1fh) Platform Security Processor
05:00.3 USB controller: Advanced Micro Devices, Inc. [AMD] Renoir/Cezanne USB 3.1
05:00.4 USB controller: Advanced Micro Devices, Inc. [AMD] Renoir/Cezanne USB 3.1
```

| **Option** | **Description**                                                       |
| ---------- | --------------------------------------------------------------------- |
| `-b`       | Displays connection information from the card's point of view.        |
| `-k`       | Shows the kernel driver and modules handling each PCI device.         |
| `-m`       | Outputs information in a machine-readable format.                     |
| `-n`       | Displays vendor and device information as numeric IDs.                |
| `-q`       | Queries the centralized PCI database for detailed device information. |
| `-t`       | Displays a tree diagram of PCI cards and buses.                       |
| `-v`       | Shows additional verbose information about each PCI device.           |
| `-x`       | Dumps a hex dump of the PCI configuration space for each card.        |

---

### USB DEVICES

**lsusb** ---> view currently connected USB devices.
```
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 002: ID 04f3:0c4f Elan Microelectronics Corp. ELAN:Fingerprint
Bus 001 Device 004: ID 04ca:3802 Lite-On Technology Corp. Wireless_Device
Bus 001 Device 010: ID 3206:109d yz EKSA E1000 WT
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 003 Device 002: ID 03f0:5341 HP, Inc HP Wireless Keyboard and Mouse
Bus 003 Device 003: ID 04f2:b72b Chicony Electronics Co., Ltd HD User Facing
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
```
| **Option** | **Description**                                                   |
| ---------- | ----------------------------------------------------------------- |
| `-d`       | Display devices from the specified vendor ID.                     |
| `-D`       | Display information from a device with the specified device file. |
| `-s`       | Show information for devices on the specified bus.                |
| `-t`       | Display device hierarchy in a tree format.                        |
| `-v`       | Show detailed (verbose) information about USB devices.            |
| `-V`       | Display the version of the `lsusb` program.                       |

---

### SUPPORTING MONITORS

| **Display Server** | **Notes**                                                                                                                                                                                                                                                                                               |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **X11**            | - Interfaces with the monitor, VGA, keyboard, and mouse to provide a standardized graphical environment. <br> - Used by desktop environments such as KDE, GNOME, and others. <br> - Serves as a replacement for **XFree86**. <br> - Configuration is text-based and typically located in **/etc/X11/**. |
| **Wayland**        | - Designed to be simpler and more secure than X11. <br> - User-specific configurations are stored in **\~/.config/weston.ini**.                                                                                                                                                                           |
| **Both**           | - Automatically detects hardware at boot and adjusts configuration files accordingly. <br> - If display hardware (ie. video card or monitor) is not recognized: <br>    • Defaults to generic video drivers. <br>    • May result in suboptimal, low-quality display output.                       |

---

### PRINTERS

* **Common Unix Printing System (CUPS)** provides a common interface for working with any type of printer.
* It accepts print jobs in the **PostScript** document format.
* Jobs are managed and sent using a print **queue system**.
* **Multiple queues** can be set up for different purposes, such as landscape, portrait, single-sided, double-sided, or black-and-white printing.
* **CUPS** uses the **Ghostscript** program to convert PostScript files into formats suitable for various printers.
* **Ghostscript** requires **individual drivers** for each printer model.
* **CUPS** comes with many **built-in drivers**.
* Configuration files are located in **/etc/cups**.
* A web interface is available at **[http://localhost:631](http://localhost:631)** for managing printers and jobs.
* Printers can be configured whether they are directly connected or networked.
* Network printers can be configured using protocols such as **IPP (Internet Printing Protocol)** or **SMB (Server Message Block)**.

Common CUPS commands:

* `$ lpc` ---> Start, stop, or pause a print queue.
* `$ lpq` ---> Display the status of the print queue and pending jobs.
* `$ lpr` ---> Submit a new print job.
* `$ lprm` ---> Remove a print job from the queue.
```
$ lpr -P EPSON_ET_3750 document.txt
$ lpq -P EPSON_ET_3750
    Rank    Owner   Job     File            Total Size
    actrive Adam    1       document.txt    1024 bytes
    ...
```

### HOT PLUGGABLE DEVICES

- COLD pluggable : connect / remove only when system is fully down.
- HOT pluggable : connect / remove when system is up.

---> **udev**

- the **udev device manager** is initiated by the **init** program.
- run at level 5 **/etc/rc5.d/udev**.
- when removing / adding devices kernal sends out notification event messages.
- udev listen to these messages and act accordingly.

`$ udevadm [ command ] [ options ]`

| Command | Description                               |
| ------- | ----------------------------------------- |
| control | Modify internal state of udev             |
| info    | Query the udev database for device info   |
| monitor | Listen for kernel events and display them |
| settle  | Watch udev event queue                    |
| test    | Simulate a udev event                     |
| trigger | Request device events from the kernel     |

`$ udevadm control -R` < reload rules in **/etc/udev/rules.d** >
