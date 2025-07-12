# KERNAL MODULES

> modules are self contained library files that can be loaded and unloaded dynamically.

Types of modules,
- Device Drivers
    * facilitate communication with Hardware.
- Filesystem Drivers
    * for filesystem I/O.
- Network Drivers
    * for implementing network protocols.
- System Calls
    * additional functions for adding / modding system services.
- Executable Loaders
    * additional executable formats.

Modules are located at **/lib/modules/**.

config files for modules can be found at,
- /etc/modprobe.d/ or /etc/modules-load.d/
    * config files craeted at system installation or by an admin.

- /lib/modprobe.d/
    * config files for 3rd party packages.

- /usr/lib/modprobe.d/
    * hardlink to /lib/modprobe.d/

- /run/modprobe.d/
    config files generated at runtime.

> old OS'es had only /etc/modules.conf file to store configs.

**dmesg** - display current kernal ring buffer.\
**lsmod** - brief module info.\
**modinfo** - detailed module info.

on module errors at boot or runtime kernal messages are generated.\
kernal messages at boot may be stored in **/var/log/dmesg** file.

> ring buffer is a fixed size FIFO data structure.\
> old data is deleted to make room for new data.

    lsmod
    Module      Size        UsedBy
    af_packet   49154       4   bridge

    Module = module name
    Size = module size
    UsedBy = number of processes using the module + name of other modules using the module.

> kernal modules typically has a .ko extention

## INSTALLING KERNAL MODULES

**insmod**
- insert a single module to kernal.
- does not load dependecies.
- requires absolute file path.

`$ sudo insmod /lib/modules/kernal/drivers/joydev.ko`\

**modprobe**
- load required dependencies.
- use insmod to insert modules.
- use **/lib/modules/modules.dep** file to determine dependencies.

`$ grep - joydev /lib/modules/modules.dep`

    kernal/drivers/md/joydev.ko:
    kernal/drivers/md/joy-res.ko
    kernal/drivers/md/joy-aud.ko

    according to above output, joydev module requires,
    joy-res and joy-aud modules as dependencies.

`$ sudo modprobe joydev`

**depmod**

- scan for undetected devices.

`$ sudo depmod`

scans the system for new / undetected devices and determine any required modules,
determine depndencies, update modules.dep file.


## REMOVING KERNAL MODULES

**rmmod**

- does not remove dependencies.

`$ sudo rmmod joydev`

**modprobe**

`$ sudo modprobe -r joydev`
