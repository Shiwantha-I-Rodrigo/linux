# STORAGE

1. Hard Disk Drives
2. Solid State Drives

## DRIVE CONNECTION TYPES

- Parallel Advanced Technology Attachement ( PATA )
    * 2 devices per adapter.
    * a parellel interface.
- Serial Advanced Technology Attachment ( SATA )
    * 4 devices per adapater.
    * a serial interface.
- Small Computer System Interface ( SCSI )
    * 8 devices per adapter.
    * a parellel interface, but with SATA speed.
- Nonvolatile Memory Express ( NVME )
    * 12 devices per adapter.
    * a parellel interface.

> when a device is connected kernal assigns the device a file in the **/dev** folder.\
> that file is called a **RAW DEVICE**.

PATA - /dev/**hdX**\
SATA / SCSI - /dev/**sdX**\
NVME - /dev/**nvmeX**

## PARTITIONS

1. BIOS uses MBR ( master boot record )
    * 4 primary partitions.
    * each primamry partition can be split in to mutiple extended partitions.
    * extended partitions start from 5 ( sda5, ... )
2. UEFI uses GPT ( GUID partition table )
    * 128 partitions

linux used to detect devices at boot, but with the spread of USB devices,\
the **udev** program is developed to detect devices on the go.\
**udev** creates files for devices automatically.\
**udev** also support **presistent** device files, to reatain file names for each device on reconnecting the device.\
this is achieved by creating files in **/dev/disk** including unique features of the device,\
and linking them to files in **/dev**\
these features include,
    - **/dev/disk/by-id** folder > manufacture make / model / serial number.
    - **/dev/disk/by-label** folder > device label.
    - **/dev/disk/by-path** folder > physical hardware port they are connected to.
    - **/dev/disk/by-uuid** folder > 128bit UUID assigned to the device.

### fdisk ( FOR MBR / GPT INDEX SYSTEMS )

> ! older versions did not support GPT.

`$ sudo fdisk /dev/sda`

    a   toggle bootflag.
    d   delete partition.
    g   new GUID partition table. (GPT)
    l   list known partition types.
    m   print help menu.
    n   new partition.
    o   new DOS partition table. (MBR)
    p   print partition table.
    q   quit without saving.
    t   change partition's system id.
    u   change / display entry units.
    v   verify partition table.
    w   wite changes and exit.
    
    b   edit BSD disk label.
    c   toggle DOS flag.
    G   new IRIX partition table.
    s   new SUN DISK label.
    x   extra functionality.

### gdisk ( FOR GPT INDEX SYSTEMS )

`$ sudo gdisk /dev/sda`

identifies the current formatting and offer option to convert to GPT.

    b   backup GPT data.
    c   change partition name.
    d   delete partition.
    i   show details on partition.
    l   list known partition types.
    n   add new partition.
    o   new GUID partition table.
    p   print partition table.
    q   quit with out saving.
    r   recovery and transformation.
    s   sort partitions.
    t   change partitions type code.
    v   verify disk.
    w   write changes and exit.
    x   extra functionality.
    ?   print help menu.

### parted

> ! can modify existing partitions.\
> contain **partprobe** utility to triger linux to reread the partition table for a specific device.

**Gparted (GNOME parted) is a GUI tool for the same porpose**

## FILE SYSTEMS

In linux all files reside within the virtual directory. unlike windows which uses drive letters to denote devices connected to the system.

### THE VIRTUAL DIRECTORY

*virtual directory contains a single folder called the root directory.*\
a **mount point** is a folder placeholder in the virtual directory that points to a physical device.


    Hard Drive 1
        |-bin
        |-etc               Hard Drive 2
        |-home----------------->|
        |-mnt                   |-kate
                                |-robert

#### THE FHS ( Filesystem Hierarchy Standard )

    /bin        executable programs required for the system in single user mode.
    /boot       bootloader files.
    /dev        device files.
    /etc        service configuration files.
    /home       user data.
    /lib        lib files required by executables.
    /media      mount point for removable devices.
    /mnt        mount point for removable devices.
    /opt        data for 3rd party programs.
    /proc       kernal and process information as files updated in realtime.
    /root       root user data.
    /sbin       executable programs required by the system.
    /sys        device , driver, kernal information as files updated in real time.
    /tmp        temp files created by users.
    /usr        data for standard programs.
    /usr/bin    local user executable programs.
    /usr/local  data unique to local installations of programms.
    /usr/sbin   data for system programs.
    /var        files whose content is expected to change frequently.

> `$ cd /home/robert`   - absolute path\
> `$ cd home/robert`    - relative path

### LINUX FILESYSTEMS

- brtfs
    * 16 EiB file / 16 EiB FS.
    * own ( Redundant Array of Inexpensive Disks ) RAID implmentation.
    * LVM ( Logical Volume Management ).
    * built in snapshots.
    * fault tollerence.
    * data compression.

- eCryptfs
    * applies POSIX complient encryption before data storage.
    * only the OS that created the FS can read it.

- Ext3
    * 2 TiB file / 16 TiB FS.
    * decendent of original linux FS
    * support journaling.
    * fast recovery and startup.

- Ext4
    * 16 TiB file / 1 EiB FS.
    * journaling.
    * improved performance.

- XFS
    * 8 EiB file.
    * High performance 64 bit journaling FS. 

- swap
    * not for presistent data.
    * virtual memory for system.

> Journaling - recording changes to the file system in a dedicated area called a journal (or log) \
> before those changes are applied to the main file system data structures ( write-ahead logging ).\
> By recording changes first, the file system can ensure that if a system crash occurs during a write operation,\
> the journal can be used to restore the file system to a consistent state after the system restarts.

### NON-LINUX FILESYSTEMS

- CIFS
    * Common Internet File System by microsoft.
    * For reading / writing data across a network.

- HFS
    * Hierarchial File System by Apple.
    * HFS+ is also supported in linux.

- ISO-9660
    * CD-ROM FS.

- NFS
    * Network File System is open source.
    * For reading / writing data across a network.

- NTFS
    * New Technology File System by microsoft.

- SMB
    * Server Message Block by microsoft.
    * proprietery FS for network storage and interacting with other network devices.

- UDF
    * Universal Disk Format for DVD-ROM's.

- VFAT
    * Virtual File Allocation Table.
    * extention of FAT FS.
    * commonly used on removable devices.

- ZFS
    * Zettabyte File System by Sun Microsystems.

### MANAGING FILESYSTEMS

> Creating the FS,

`$ mkfs.ext4 /dev/sda1`\
`$ mkfs -t ext4 /dev/sda1`\
`$ mkfs.fat -F 32 /dev/sda1` < mkfs.vfat / mkfs.msdos is also similar >


> Mounting FS,

`$ sudo mount /dev/sda1 /media/usb`\
`$ sudo mount -t ext4 /dev/sda1 /media/usb`
`$ sudo mount`  < list all mounted Fs'es >


> Automatic Mounting,

the **/etc/fstab** file contain info required to automount Fs'es on boot.\
**fstab** refernces devices by UUID.\
if the device does not exist on boot, it will generate errors.\


> Filesystem Stats,

    df          disk usage by partition.
    du          disk usage by directory.
    iostat      real-time chart of disk statics by partition.
    lsblk       current partition sizes and mount points.

`$ sudo du -d 1` < depth 1 >

> Other FS Tools,

    fsck        file system check ( may not fix XFS )

`$ sudo pacman -S e2fsprogs`

    blkid       info on block devices.
    chattr      change file attributes on the file system.
    debugfs     manually view/change fs structure. ( undelete a file or extract corrupted file, etc... )
    dumpe2fs    show block and superblock info.
    e2label     change FS label.
    rezise2fs   expand or shrink FS.

    XFS
    xfs_admin   display / changes attributes such as label or UUID
    xfs_info    view block sizes, UUID, etc...
    xfs_repair  repair the FS.

    BRTFS
    balance     balance FS chunks across multiple devices.
    check       perform an offline check.
    device      manage devices.
    quota       set quotas.
    filesystem  FS management.
    restore     restore files.

### ALTERNATIVE STORAGE

**MULTIPATH**

- multiple active paths between system and network storage.
- for redundency and fault tolerence.
- increased throughput.
- use /dev/mapper/mpathX file.
.

    dm-multipath        kernal module.
    multipath           view multipath devices.
    multipathd          daemon to monitor / activate / deactivate paths.
    kpartx              create multpath devices.


**LVM**

- use /dev/mapper.
- aggrgate multiple partitions into virtual volumes.

<img src='images/lvm.png'>

    VG      Volume Group.
    PV      Physical Volume.
    LV      Logical Volume.
    MP      Mount Point ( /home,  /usr, etc... )

LVM Tools,

    lvchange        mod LV settings.
    pvchange        mod PV settings.
    vgchange        mod VG settings.

    lvconvert       add /remove a mirror to non-mirrored LV.
    vgconvert       change LVM metadata from one format to another.

    lvcreate        create a LV.
    pvcreate        create a PV.
    vgcreate        create a VG.

    lvremove        remove an existing LV.
    pvremove        remove a PV.
    vmremove        remove a PV from a VG.

    lvdisplay       display info about a LV.
    pvdisplay       display info about a PV.
    vgdisplay       display info about a VG.

    pvck            consistency check for LVM metadata on PV.
    vgck            check integrity of of a VG.

    lvsscan         scan for LV.
    pvscan          scan for LVM / Non-LVM volumes.
    vgscan          scan for LVM groups.

    lvs             display LV stats.
    pvs             display PV stats.
    vgs             display VG stats.
    

    lvextend        add to existing LV.
    lvmdump         dump LVM setting to a tarball.
    lvrename        change LV name.
    lvresize        change LV size.
    pvmove          move used volumes from one device to another.
    vgcfbackup      text backup VG metadata.
    vgcfrestore     restore vg metadata from file.
    vgexport        export a VG
    vgextend        add PV to VG
    vgimport        import a VG
    vgimportclone   import and rename a duplicate VG.
    vgmerge         combine two VG's to one.
    vgrename        rename VG

`$ sudo gdisk /dev/sdb`\
`>> n` < create a new prtition >\
`>> w` < write changes and quit >

`$ sudo pvcreate /dev/sdb1`\
`$ sudo vgcreate VOL_1  /dev/sdb1`\
`$ sudo lvcreate -l 10G -n DISK_1 VOL_1`\
`$ sudo mkfs.ext4 /dev/mapper/VOL_1-DISK_1`

`$ sudo mkdir /media/LV`\
`$ sudo mount /dev/mapper/VOL_!-DISK_1 /media/LV`\

**RAID**

The **mdadm** tool allows specifying multiple partitions to be used as RAID.
RAID Hardware is expensive, therefore LINUX uses software RAID.
view current status with **/proc/mdstat**.

### ENCRYPTING PARTITIONS

`$ sudo cryptsetup -y -v luksFormat /dev/sdb1`\
`$ sudo cryptsetup -v luksOpen /dev/sdb1 luks1` < map opened partiton to /dev/mapper/luks1 file >

/dev/mapper/luks1 is now availabe as a partition.

`$ sudo cryptsetup -v luksClose /dev/mapper/luks1`
