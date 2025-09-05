## **Sample Files (for all scenarios)**

1. `sample1.txt` – 100 KB text file
2. `sample2.log` – 500 KB log file
3. `data/` – directory containing:

   * `fileA.csv` – 200 KB
   * `fileB.csv` – 150 KB
   * `archive/` – nested folder with `old1.txt` (50 KB), `old2.txt` (30 KB)
4. `swapfile` – empty file to create swap space
5. `disk1.img`, `disk2.img` – blank 100 MB disk image files for partitioning and LVM

---

## **Scenario 1: Checking Disk Usage & Space**

1. Check free space on all mounted filesystems.
2. Estimate space used by `data/` directory and all nested files.
3. Display disk usage of `sample1.txt` and `sample2.log` in human-readable format.

---

## **Scenario 2: Mounting and Unmounting**

1. Mount `disk1.img` to `/mnt/disk1` as read-only.
2. List all block devices and their dependencies before mounting.
3. Unmount `/mnt/disk1`.
4. Display block device attributes after mounting.

---

## **Scenario 3: Partitioning a Disk**

1. Create a new empty DOS partition table on `disk1.img`.
2. Add two new partitions of 50 MB each.
3. Print partition table to verify.
4. Toggle bootable flag on the first partition.
5. Delete the second partition.

---

## **Scenario 4: GPT Partitioning & Backup**

1. Create a new empty GPT table on `disk2.img`.
2. Add a partition named `data-part`.
3. Change its type to `Linux filesystem`.
4. Back up GPT data.
5. Verify the disk table.

---

## **Scenario 5: Creating Filesystems & Swap**

1. Format the first partition of `disk1.img` with ext4.
2. Format the first partition of `disk2.img` with xfs.
3. Create a swap area in `swapfile`.
4. Enable the swap with highest priority and show summary.
5. Disable the swap afterward.

---

## **Scenario 6: Checking & Repairing Filesystems**

1. Run a filesystem check on `disk1.img` and auto-repair any errors.
2. Verify filesystem integrity of the swap file.

---

## **Scenario 7: LVM Setup**

1. Initialize `disk1.img` and `disk2.img` as physical volumes.
2. Create a volume group named `vg_data` with both PVs.
3. Create a logical volume named `lv_storage` of 80 MB.
4. Display summary and detailed info of PVs, VG, LV.

---

## **Scenario 8: LVM Management**

1. Extend `lv_storage` by 10 MB.
2. Reduce its size by 5 MB.
3. Rename `lv_storage` to `lv_main`.
4. Add a new PV to `vg_data` and extend the LV.
5. Move data from one PV to another.
6. Remove a PV from VG.
7. Backup and restore VG metadata.

---

## **Scenario 9: LVM Consistency & Scans**

1. Scan and list all PVs, VGs, LVs.
2. Verify consistency of PVs and VG metadata.
3. Activate and deactivate all VGs and LVs.

---
---
---

## **Scenario 1: Checking Disk Usage & Space**

```bash
# Check free space on all mounted filesystems
df -h

# Estimate space used by data/ directory and all nested files
du -sh data/

# Display disk usage of individual sample files
du -h sample1.txt sample2.log
```

---

## **Scenario 2: Mounting and Unmounting**

```bash
# List all block devices with dependencies before mounting
lsblk -s

# Mount disk1.img to /mnt/disk1 as read-only
mount -o ro disk1.img /mnt/disk1

# Display block device attributes after mounting
blkid

# Unmount the filesystem
umount /mnt/disk1
```

---

## **Scenario 3: Partitioning a Disk (MBR)**

```bash
# Start fdisk on disk1.img
fdisk disk1.img

# Commands inside fdisk interactive mode:
o   # Create new empty DOS partition table
n   # Add first new partition
   # Accept default start and size (50M)
n   # Add second new partition
   # Accept default start and size (50M)
p   # Print partition table to verify
a   # Toggle bootable flag on first partition
d   # Delete second partition
p   # Print partition table to verify changes
q   # Quit without saving changes
```

---

## **Scenario 4: GPT Partitioning**

```bash
# Start gdisk on disk2.img
gdisk disk2.img

# Commands inside gdisk interactive mode:
o   # Create new empty GPT table
n   # Add new partition
   # Accept default start/size
c   # Change partition name
   # Enter name: data-part
t   # Change partition type
   # Choose Linux filesystem type
b   # Back up GPT data
v   # Verify disk table
p   # Print partition table
q   # Quit without saving
```

---

## **Scenario 5: Creating Filesystems & Swap**

```bash
# Create ext4 filesystem on first partition of disk1.img
mkfs.ext4 /dev/sdX1

# Create xfs filesystem on first partition of disk2.img
mkfs.xfs /dev/sdY1

# Create swap space on swapfile
mkswap swapfile

# Enable swap with highest priority and show summary
swapon -p 10 swapfile
swapon -s

# Disable the swap
swapoff swapfile
```

---

## **Scenario 6: Checking & Repairing Filesystems**

```bash
# Check and repair filesystem on disk1.img
fsck /dev/sdX1

# Check filesystem integrity of swapfile (optional, usually not needed, but fsck can be used)
fsck swapfile
```

---

## **Scenario 7: LVM Setup**

```bash
# Initialize PVs
pvcreate /dev/sdX
pvcreate /dev/sdY

# Create VG with both PVs
vgcreate vg_data /dev/sdX /dev/sdY

# Create LV of 80 MB
lvcreate -L 80M -n lv_storage vg_data

# Show summary of PVs, VGs, LVs
pvs
vgs
lvs

# Show detailed info of PVs, VGs, LVs
pvdisplay
vgdisplay
lvdisplay
```

---

## **Scenario 8: LVM Management**

```bash
# Extend LV by 10 MB
lvextend -L +10M /dev/vg_data/lv_storage

# Reduce LV by 5 MB
lvreduce -L -5M /dev/vg_data/lv_storage

# Rename LV
lvrename /dev/vg_data/lv_storage lv_main

# Add new PV to VG and extend LV
vgextend vg_data /dev/sdZ
lvextend -l +100%FREE /dev/vg_data/lv_main

# Move data from one PV to another
pvmove /dev/sdX /dev/sdZ

# Remove PV from VG
vgreduce vg_data /dev/sdX

# Backup and restore VG metadata
vgcfgbackup vg_data
vgcfgrestore -f /etc/lvm/backup/vg_data vg_data
```

---

## **Scenario 9: LVM Consistency & Scans**

```bash
# Scan and list all PVs, VGs, LVs
pvscan
vgscan
lvscan

# Verify consistency of PVs and VG metadata
pvck /dev/sdX
vgck vg_data

# Activate and deactivate all VGs and LVs
vgchange -ay vg_data
lvchange -ay /dev/vg_data/lv_main
vgchange -an vg_data
lvchange -an /dev/vg_data/lv_main
```

---
