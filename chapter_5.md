# BOOT PROCESS

1. firmware start > POST (Power On Self Test) > search for bootable devices and a bootloader.
2. bootloader starts and determines which kernal to load.
3. kernal loads into memory and start daemons.

## dmesg

view boot messages.

> **only if available** since, the *kernal ring buffer* is circular and new kernal messges may overwrite boot messages.

## FIRMWARE START

1. BIOS (Basic Input / Output System)

original BIOS firmware could only read one sector of data from the hard disk, since this is not enought to load a full kernal, a *bootloader* program is loaded to initialize the nessasery hardware to find and run the kernal. bootloader has a config file with information to find the operating system.
bootloader can be stored in,
+ internal HD
+ external HD
+ CD/DVD
+ USB
+ ISO file
+ network server (NFS / HTTP / FTP)

**MBR** is the first sector of the first partition of a HD.

> bootloader is not required to point to a Kernal file, it could point to another program of any type, including another bootloader program.


2. UEFI (Unified Extensible Firmware Interface)

insted of the first sector , a full partiiton (EFI system partition) is allocated to store bootloaders. ESP uses microsofts FAT (file allocation table). UEFI firmware sometimes uses a built in program (boot manager) to control which bootloader to run.

> secure boot is only loading bootloders digitally signed by a known certificate, many systems recognize only microsoft certificates, hence many linux distros use **chainloading**, using a **shim bootloader** signed by microsoft to point to the real bootloader.

each bootloader file must be registered in the bootmanager for it to appear in the boot menu.

> doesn't have a fixed extention for efi files, but **.efi** is preffered.\
> stored in **/boot/efi/**.

## BOOTLOADER START

### LILO (Linux Loader)

The first linux bootloader.\
doesn't support UEFI\
config fie located at /etc/lilo.conf

### GRUB (GRUB Legacy)

GRUB *menu commands* are stored in a config file (/boot/grub/) called **menu.lst** or **grub.conf**.\
GRUB config file consist of 2 sections

#### Global definitions

    color       foregraound and background colors of the boot menu.
    default     default selected menu option.
    fallback    a secondary selection incase default fails.
    hiddenmenu  hides menu.
    splashimage menu background image.
    timeout     wait for deafult selection.

.

    default 0                     < default selected entry\
    timeout 10                    < select timeout\
    color white/blue yellow/blue  < foreground/background for normal and selected entries.


#### OS boot definitions

> after global definitions each OS must have its own definitions.

    title           boot menu entry.
    root            disk and partition of /boot folder.
    kernal          kernal image in the defined /boot folder
    initrd          define initial RAM disk file, drivers for kernal to interact with system hardware.
    rootnoverify    non linux boot partitions.

.

    title Arch Linux
    root (hd0,0)
    kernal (hd0,0)/boot/vmlinuz
    initrd /boot/initrd

    title Windows 7
    rootnoverify (hd0,4)

> GRUB uses a rather inconvinient number system for hard drives and partitions. **(hd0,0)** means first partition of first hard disk.\
> generally **sda3** in primary HD can be writen as **(hd0,2)**.\
> in **vmlinuz** the *z* means that the kernal is compressed with *bz*. uncompressed kernal is **vmlinux**. 

install grub after creating the config file.

`grub-install /dev/sda` or `grub-install '(hd0)'`

to install a copy of GRUB in the **bootsector of a partition** instead of the **mbr of a hard disk** for *chain loading*,

`grub-install /dev/sda2` or `grub-install '(hd0,1)'`

> no need to reinstall GRUB after config changes.

### GRUB2

mostly similar to GRUB, with a few changes.

config file > /boot/grub/***grub.cfg***

    menuentry "Arch Linux"{
        set root=(hd0,1)
        linux /boot/vmlinuz
        initrd /initrd
    }
    menuentry "Windows 7"{
        set root=(hd1,3)
    }

> GRUB2 numbering starts at **1 instead of 0 only for partitions**.\
>
> do not modify grub config file manually.\
> use ***/etc/grub.d/*** for individual boot options (seperate files).\
> use ***/etc/default/grub*** for global commands.

linux create new grub config files after certain events.\
use `grub2-mkconfig -o /boot/grub2/grub.cfg` to create the grub configuration.( **-o** redirect output to file instead of STDOUT).

> reinstall GRUB2 after config changes > `grub2-install /dev/sda3`\
> or instead of running two commands just run `update-grub2`

### ALTERNATIVE BOOTLOADERS

+ SYSLINUX *for FAT file systems (USB)*
+ EXTLINUX *for EXT / btrfs file systems*
+ ISOLINUX *for ISO file systems (LiveCD)*
    - isolinux.bin < bootloader >
    - isolinux.cfg < config file >
+ PXELINUX *for network file systems*
    - PXE uses DHCP to assign an address to workstation.
    - BOOTP loads the bootloader to the workstation using TFTP protocol (can be modified to use NFS / HTTP / FTP).
    - PXELINUX is stored in `/tftpboot/pxelinux.0` in TFTP server.
    - each workstation has own configuration files (name based on MAC) in `/tftpboot/pxelinux.cfg` **directory**.

+ MEMDISK *for older DOS systems*

# SYSTEM RECOVERY

+ KERNAL FAILURES (kernal panics)
    - select previous kernal
    - single user mode
        * add 'single' to linux line in grub config (press **E** at grub menu)
    - passing any other Kernal parameter.

+ DRIVE FAILURES
    1. load a rescue os to run from RAM
    2. inspect / fix root drive `fsck /dev/sda3`.\
    (run as many times needed to get a *''clean''* run. add `-y` option to auto answer repair prompts.)
    3. try mounting and reading the content after repair `mount /dev/sda3 /media`
