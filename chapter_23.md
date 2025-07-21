# LINUX DEVICES

## COMMUNICATING WITH DEVICES

- to communicate with devices Linux uses installed modules.
- linux support different types of hardware interfaces.

### PCI BOARDS

*( Peripheral Component Interconnect )*

**PCI Express** is the current itteration.

Client Devices,

- Internal Hard Drives.
    + using SATA or SCSI connectors often use PCI.
- External (Network) Hard Drives.
    + with fiber channels uses PCI boards that support Host Bus Adapter (HBA) standard.
- Network Interface Cards.
    + use common RJ-45 standard.
- Wireless Cards.
    + wifi cards that suport IEEE 802.11.
- Bluetooth Devices.
- Video Accelerators.
    + gaming / video processing.
    + block chain generation.
- Audio Cards.

### USB

*( Universal Serial Bus )*

    ver 1.0         : 12 Mbps
    ver 2.0         : 480 Mbps
    ver 3.2 Gen 1   : 5 Gbps
    ver 3.2 Gen 2   : 10 Gbps
    ver 4           : 40 Gbps

1. the module for the the usb controller must be loaded.
2. the module for the USB device connected must be loaded.

### GPIO

*( General Purpose Input Output )*

- popular with small utility Linux systems designed for controlling external devices.
- provides multiple I/O lines, that can control outputs to single bit level.
- read values from any analog to digital sensor.

### /dev DIRECTORY

- Linux kernal communicate with devices through interface files.
- each device create a interface file in **/dev/** directory.
- using these files applications can easily perform I/O opertaions with the devices.
- there are 2 types of device files,
    + Character device files : transfer one character at a time. ( terminal / usb mouse / usb keyboard / ... )
    + block device files : transfer blocks of data. ( usb mass storage / network cards / ... )

.

    $ ls -al sd* tty*

    brw-rw----  1   root    disk    8, 0 Feb 16 17:49 sda
    brw-rw----  1   root    disk    8, 1 Feb 16 17:49 sda1
    crw-rw-rw-  1   root    tty     5, 0 Feb 16 17:49 tty
    crw--w----  1   root    tty     4, 0 Feb 16 17:49 tty0

> the first char of the output indicates the device file type.

- special device files,
    + **/dev/null** : discard the input directed to this interface.
    + **/dev/random** & **/dev/urandom** : output random numbers.
        * **random** blocks access until enough randomness is generated.
        * **urnadom** provide a random number from availabe data (less accurate).
    + **/dev/zero** : output NULL char (zeros).

> Beside device files Linux also provide **device mapper** files.\
> It maps block devices to virtual block devices, allowing kernal to intercept the data\
> and perform some operation on them after reading or before writing to physical blocks.\
> ( ie, LVM / LUKS ).


### /proc DIRECTORY

- virtual directory.
- kernal populates it with files containing system information.
- these files can be read with standard text commands.

**IRQ (Interrupt Requests)**

- **/proc/interrupts** file.
- IRQ's allow hardware devices to indicate when they have data to send to the CPU.

    $ cat /proc/inturrupts

    0:      36      IO-APIC     2-edge      timer
    1:     297      IO-APIC     1-edge      i8042
    8:       0      IO-APIC     8-edge      rtc0
    ...

- first column indicates the IRQ assigned to the device.
- some IRQ's are reserved by the system ( ie, 0 for system timer).

**I/O Ports**

- **/proc/ioports** file.
- I/O ports are locations in memory.
- allow CPU to store and send data to and from hardware devices.
- each device is assigned a unique I/O port.
- ie, Keyboard / Mouse / Printer / NIC / ...

    $ cat /proc/ioports

    0000-0cf7 : PCI Bus 0000:00
    0000-001f : dmal
    0020-0021 : pic1
    ...

**Direct Memory Access**

- **/proc/dma** file.
- similar function to I/O ports.
- CPU setup the transfer but doesn't handle the actual data movement.
- faster than I/O ports.
- primarily a hardware process, software only initiates the process.
- ie, SSD / VGA / ...

### /sys DIRECTORY

- virtual directory.
- kernal populates it with files containing system information.
- any user can access the data.
- broken down to sub directories based on device and function.


## WORKING WITH DEVICES

### FINDING DEVICES


**lsdev**

    Device      DMA     IRQ     I/O Ports
    ...
    acpi                9
    dma                         0080-008f
    ...
    i8042       1       12
    ...

- part of **procinfo** package.


**lsblk**

    NAME        MAJ:MIN     RM      SIZE    RO      TYPE    MOUNTPOINT
    loop        7:0         0       34.6M   1       loop    /snap/gtk-common-theme
    ...
    sda         8:0         0       10G     0       disk
        sda1    8:1         0       10G     0       part
    ...

- **\-S** option display only info about SCSI block devices.


**dmesg**

- kernal ring buffer records kernal-level events.
- ring buffer rotates (overwrite) old messages.

.

    ...
    [ 2525.499216 ] usb 1-2: new full-speed USB device number 3 using ohci-pci
    [ 2525.791093 ] usb 1-2: config 1 interface 0 altset 0 endpoint 0x1 has ...
    ...

- shows steps taken by kernal in recognizing the device and installing correct modules.


### PCI DEVICES

**lspci** view currently installed PCI / PCIe devices.

    -b      Display connection information from cards POV.
    -k      Display kernal driver modules for each PCI card.
    -m      Display info in machine readable format.
    -n      Display vendor and device info as numbers.
    -q      Queries the centralized PCI db for info about installed PCI cards.
    -t      Display a tree diagram, cards and busses.
    -v      additional info about cards.
    -x      dump hex output of card info.

### USB DEVICES

**lsusb** view currently connected USB devices.

    -d      Display devices from the specific vendor ID.
    -D      Display info from devices with specified device file.
    -s      Display info from devices using specified bus.
    -t      Display info in a tree format.
    -v      additional info about usb devices.
    -V      lsusb program Version.

### SUPPORTING MONITORS

- X11
    + intercat with monitor / vga / keyboard / mouse and provide a standard interface.
    + desktop management software ( KDE / GNOME / etc.. ) use this interface.
    + replacement for **XFree86**.
    + text based configaration **/etc/X11/**.
- Wayland
    + simpler, more secure.
    + seperate config files for users **~/.config/weston.ini**.

- both automatically detect the hardware at boot and adjust the config files accordingly.
- in case theya are unable to identify the specific video card / monitor,
    + default into generic video drivers.
    + unoptimal low quality output.

### PRINTERS

- **Common Unix printing System (CUPS)** provide a common inetrface for working with any type of printer.
- accepts print jobs in **PostScript** document format.
- sends jobs using a print queue system.
- there can be multiple queues for landscape / portrait / single-side / double-side / B&W.
- CUPS use Ghostscript program to convert PostScript to suitable formats for various printers.
- Ghostscript require individual drivers for each printer model.
- CUPS install many drivers.
- **/etc/cups**.
- **http://localhost:631** web interface.
- can configure directly connected printers.
- can configure network printers using IPP (internet printing protocol) or SMB (microsoft server message block).

- `$ lpc` : Start / Stop / Pause print queue.
- `$ lpq` : display queue status / pending jobs.
- `$ lpr` : submit new print job.
- `$ lprm` : remove a print job.

.

    $ lpr -P EPSON_ET_3750 document.txt
    $ lpq -P EPSON_ET_3750
    ...
    Rank    Owner   Job     File            Total Size
    actrive Adam    1       document.txt    1024 bytes
    ...

### HOT PLUGGABLE DEVICES

- COLD pluggable : connect / remove only when system is fully down.
- HOT pluggable : connect / remove when system is up.

**udev**

- the **udev device manager** is initiated by the **init** program.
- run at level 5 **/etc/rc5.d/udev**.
- when removing / adding devices kernal sends out notification event messages.
- udev listen to these messages and act accordingly.

`$ udevadm [ command ] [ options ]`

    control     mod internal state of udev
    info        query the udev db for device info.
    monitor     listen for kernal events and display them.
    settle      watch udev event queue.
    test        simulate a udev event.
    trigger     request device events from the kernal.

`$ udevadm control -R` < reload rules in **/etc/udev/rules.d** >
