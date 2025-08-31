# 🔹 Scenarios

## **Story 1: The Student Project Space is Running Out**

You are the system administrator at a university.
One of the professors calls you because students in the “projects” folder are running out of disk space.

1. You first check how much disk space is available across all mounted filesystems.
2. Then you drill down to find out how much space the `/home/student/projects/` directory consumes.
3. To reproduce the issue and demonstrate to the professor, you create three dummy reports (`report1.txt`, `report2.txt`, `report3.txt`), each 50 MB in size.
4. You again check the usage of the projects directory and compare it to the overall filesystem usage before and after.

---

## **Story 2: Mounting a USB Drive for Research Data**

A researcher plugs in a USB drive with important data but says they only need **read-only access** first.

1. You check the available block devices in the system, ensuring you see parent disks before their partitions.
2. You check the UUID and filesystem type of the USB drive.
3. You mount the USB drive into `/mnt/testdrive` in **read-only mode** to prevent accidental modification.
4. Later, the researcher decides they want to **edit the files**. You remount the same drive in **read-write mode**.
5. Once done, you cleanly unmount the drive.

---

## **Story 3: Preparing a New Disk for Swap with `fdisk`**

The system is running low on swap space. You decide to prepare part of a new disk `/dev/sdb` for swap.

1. You open the partition tool and first bring up the help menu to recall the available commands.
2. To check compatibility, you list all possible partition types.
3. You clear the disk and create a fresh DOS partition table.
4. You create a new partition.
5. Since it will be swap, you change its type to “Linux swap.”
6. You verify the partition table to make sure there are no inconsistencies.
7. Satisfied, you write the changes and exit.

---

## **Story 4: Creating a Data Partition with `gdisk`**

Another disk, `/dev/sdc`, will be used for storing research datasets. This time you need a GPT layout.

1. You start the GPT partitioning tool.
2. You create a new GUID partition table.
3. You add a new partition.
4. To avoid confusion later, you name this partition **“DataDrive.”**
5. You look through the available partition types.
6. You assign it the type “Linux filesystem.”
7. You sort the partitions to maintain a clean order.
8. You run a verification to check for any issues.
9. Finally, you write the changes and exit.

---

## **Story 5: Formatting and Managing Swap**

Now that `/dev/sdb1` and `/dev/sdb2` exist:

1. You format `/dev/sdb1` with the `ext4` filesystem for normal data use.
2. You turn `/dev/sdb2` into swap space.
3. You enable swap on `/dev/sdb2`, but because this swap should be used before any others, you set its priority to **5**.
4. You verify that swap is active with a summary command.
5. Once testing is complete, you disable the swap again.

---

## **Story 6: Fixing a Dirty Filesystem**

After an unexpected power failure, the system reports that `/dev/sdb1` might be corrupted.

1. You run a filesystem check with repair enabled to make sure it’s healthy before mounting it again.

---

# 🔹 Example Files

```bash
mkdir -p /home/student/projects
cd /home/student/projects
dd if=/dev/zero of=report1.txt bs=1M count=50
dd if=/dev/zero of=report2.txt bs=1M count=50
dd if=/dev/zero of=report3.txt bs=1M count=50
```

---

# 🔹 Answer Keys

### **Story 1**

```bash
df -h
du -sh /home/student/projects/
# create files
du -sh /home/student/projects/
df -h
```

---

### **Story 2**

```bash
lsblk -s
blkid
mkdir -p /mnt/testdrive
mount -o ro /dev/sdb1 /mnt/testdrive
mount -o rw,remount /dev/sdb1 /mnt/testdrive
umount /mnt/testdrive
```

---

### **Story 3**

```bash
fdisk /dev/sdb
# inside fdisk interactive session:
m     # help menu
l     # list partition types
o     # create empty DOS partition table
n     # add new partition
t     # change type → Linux swap (82)
v     # verify table
w     # write and exit
```

---

### **Story 4**

```bash
gdisk /dev/sdc
# inside gdisk interactive session:
o     # create empty GUID partition table
n     # add new partition
c     # name partition "DataDrive"
l     # list partition types
t     # change type → Linux filesystem (8300)
s     # sort partitions
v     # verify disk
w     # write changes and exit
```

---

### **Story 5**

```bash
mkfs.ext4 /dev/sdb1
mkswap /dev/sdb2
swapon -p 5 /dev/sdb2
swapon -s
swapoff /dev/sdb2
```

---

### **Story 6**

```bash
fsck -y /dev/sdb1
```

---
