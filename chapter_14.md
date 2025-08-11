# KERNAL MODULES

> **!** Self-contained pieces of code that **extend the functionality** of the kernel and can be **loaded and unloaded** into the kernel **dynamically**, without requiring a reboot.\
> **!** Typically has a **.ko** extention

---> Types of Modules

| **Module Type**       | **Description** |
| -                     | - |
| **Device Drivers**    | Enable communication between the kernel and hardware devices |
| **Filesystem Drivers**| Handle input/output operations for filesystems |
| **Network Drivers**   | Implement network protocols and manage network interfaces|
| **System Calls**      | Provide additional or modified system service functions|
| **Executable Loaders**| Support additional executable file formats |

---> Configuration

| **File Location**                             | **Description**|
| -                                             | - |
| **/lib/modules/**                             | Directory where kernel modules are stored|
| **/etc/modprobed/**<br>**/etc/modules-loadd/**| Configuration files created during system installation or by an administrator|
| **/lib/modprobed/**                           | Configuration files provided by third-party packages|
| **/usr/lib/modprobed/**                       | Hard link to **/lib/modprobed/**|
| **/run/modprobed/**                           | Configuration files generated dynamically at runtime|

> **!** older OS'es had only **/etc/modules.conf** file to store configs.

| **Command**   | **Description** |
| -             | - |
| `dmesg`       | Displays the current kernel ring buffer messages |
| `lsmod`       | Shows a brief list of loaded kernel modules|
| `modinfo`     | Provides detailed information about a specific kernel module |

> **!** Module errors at boot and runtime kernal messages are generated and stored in a **ring buffer** and later saved to **/var/log/dmesg**.\
> **!** A ring buffer is a fixed-size FIFO structure in memory where old data is overwritten by new data when full.

    $ lsmod
    Module                  Size  Used by
    nf_conntrack_netlink    49152  0
    nfnetlink               16384  1 nf_conntrack_netlink
    xt_conntrack            16384  2
    nf_conntrack           139264  3 nf_conntrack_netlink,xt_conntrack
    xfs                   1105920  1
    libcrc32c               16384  1 xfs

**Used by** : Number of instances using this module and / or dependent modules.

---

## INSTALLING KERNAL MODULES

**`insmod `**`[module_absolute_path]`\
--> manually load a kernel module into the currently running kernel but does **not** handle **dependencies**.

---

**`modprobe `**`[option] [module_name]`\
--> insert or remove kernel modules, with automatic dependency management.

- use **-r** option to remove a module.
- use **/lib/modules/Robert/** to look for modules.
- use `insmod` internally.
- use **/lib/modules/modules.dep** file to determine dependencies.

```
$ grep - joydev /lib/modules/modules.dep`

kernal/drivers/md/joydev.ko:
kernal/drivers/md/joy-res.ko
kernal/drivers/md/joy-aud.ko
```

> **!** according to above output, **joydev** module requires, **joy-res** and **joy-aud** modules as dependencies.

---

**`depmod `**\
--> analyze kernel modules, generate dependencies and update dependency files (ie. **modules.dep**).

> **!** while **depmod** itself doesn’t identify new hardware, it creates the infrastructure that makes automatic hardware support possible.

1. `depmod` generates files like **modules.alias**, which maps device IDs (hardware identifiers) to kernel modules.
2. This mapping allows `modprobe` to know which module to load when a specific device is present.
3. `udev`, the device manager, uses this system to automatically load drivers for new hardware.

---

## REMOVING KERNAL MODULES

**`rmmod`**\
--> manually remove (unload) a kernel module from the currently running kernel.

- **Fails** if the module is in use (ie. by a device or another module).
- Does **not** handle **dependencies**, must remove dependent modules first.

> **!!!** Use `modprobe -r` instead.
