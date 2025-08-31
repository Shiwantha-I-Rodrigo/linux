## 🔧 **Scenario 1: Disk Space Troubleshooting on a Web Server**

**Problem:**
Your Nginx server stops writing logs. A developer complains that `/var/log/nginx/access.log` is no longer updating.

**Steps:**

1. **Check overall disk usage.**

   ```bash
   df -h
   ```

   * `-h` → human-readable (most common).
     This shows that `/var` is on `/dev/sda3` and is **100% full**.

2. **Identify which directory is consuming space.**

   ```bash
   du -sh /var/*
   ```

   * `-s` → summary, don’t list subfolders individually.
   * `-h` → human-readable.
     You notice `/var/log` is huge.

3. **Drill down further.**

   ```bash
   du -sh /var/log/* | sort -h
   ```

   The biggest culprit is `/var/log/nginx/access.log` (50 GB).

4. **Resolve log bloat.**

   * Rotate/compress logs:

     ```bash
     logrotate -f /etc/logrotate.d/nginx
     ```
   * If immediate free space is needed:

     ```bash
     > /var/log/nginx/access.log
     ```

✅ **Result:** The service starts writing logs again, no downtime.

---

## 🔧 **Scenario 2: Attaching a New Disk for Backups**

**Problem:**
You attached a new block storage device `/dev/sdb` to create a backup partition.

**Steps:**

1. **List available block devices.**

   ```bash
   lsblk
   ```

   Confirms `/dev/sdb` is empty.

2. **Partition the disk (GPT).**

   ```bash
   gdisk /dev/sdb
   ```

   * `n` → create new partition.
   * `p` → print partition table.
   * `w` → write and exit.

3. **Create a filesystem.**

   ```bash
   mkfs.ext4 /dev/sdb1
   ```

4. **Mount the partition.**

   ```bash
   mkdir /backup
   mount /dev/sdb1 /backup
   ```

5. **Make it permanent in `/etc/fstab`.**

   ```bash
   blkid /dev/sdb1
   ```

   Get the UUID, then add an entry in `/etc/fstab`:

   ```
   UUID=xxxx-xxxx   /backup   ext4   defaults   0   2
   ```

6. **Verify.**

   ```bash
   df -h | grep backup
   ```

✅ **Result:** New disk is available at `/backup` and auto-mounts on reboot.

---

## 🔧 **Scenario 3: Fixing a Corrupted Filesystem After Crash**

**Problem:**
A sudden power loss makes `/dev/sdc1` unmountable.

**Steps:**

1. **Attempt unmount (if half-mounted).**

   ```bash
   umount /dev/sdc1
   ```

2. **Check and repair filesystem.**

   ```bash
   fsck -y /dev/sdc1
   ```

   * `-y` → automatically fix detected issues.

3. **Remount after repair.**

   ```bash
   mount /dev/sdc1 /mnt/data
   ```

4. **Verify data access.**

   ```bash
   ls /mnt/data
   ```

✅ **Result:** Disk repaired, data accessible again.

---

## 🔧 **Scenario 4: Running Out of RAM and Adding Swap**

**Problem:**
On a small VM (Ubuntu 20.04), OOM killer terminates services due to lack of memory.

**Steps:**

1. **Check swap usage.**

   ```bash
   swapon -s
   ```

   Shows no swap configured.

2. **Create swap file.**

   ```bash
   fallocate -l 2G /swapfile
   chmod 600 /swapfile
   mkswap /swapfile
   ```

3. **Enable swap.**

   ```bash
   swapon /swapfile
   ```

4. **Persist across reboots.**
   Add to `/etc/fstab`:

   ```
   /swapfile none swap sw 0 0
   ```

5. **Verify.**

   ```bash
   free -h
   ```

✅ **Result:** System has extra 2 GB swap, fewer crashes.

---

## 📌 **Summary of Most Used Options**

* `df -h` → human-readable disk usage.
* `du -sh` → summarize directory size (human-readable).
* `lsblk` → show devices.
* `blkid` → get UUID for `/etc/fstab`.
* `fdisk/gdisk`:

  * `n` (new partition), `p` (print), `w` (write).
* `mkfs.ext4` → create filesystem.
* `mount / umount` → attach/detach.
* `fsck -y` → auto-repair filesystem.
* `swapon -s`, `mkswap`, `swapon`, `swapoff`.

---

# 🔧 LVM Troubleshooting & Daily Operations Scenarios

---

## **Scenario 10: Creating an LVM Storage Area for Applications**

**Problem:**
You got a new disk `/dev/sdd` and want to create flexible storage for `/opt/apps`.

**Steps:**

1. **Initialize Physical Volume.**

   ```bash
   pvcreate /dev/sdd
   ```

2. **Create Volume Group.**

   ```bash
   vgcreate apps_vg /dev/sdd
   ```

3. **Create Logical Volume.**

   ```bash
   lvcreate -L 50G -n apps_lv apps_vg
   ```

4. **Format the LV.**

   ```bash
   mkfs.xfs /dev/apps_vg/apps_lv
   ```

5. **Mount it.**

   ```bash
   mkdir /opt/apps
   mount /dev/apps_vg/apps_lv /opt/apps
   ```

6. **Persist across reboots.**

   ```bash
   blkid /dev/apps_vg/apps_lv
   ```

   Add UUID to `/etc/fstab`.

✅ **Result:** A new flexible storage area `/opt/apps` created using LVM.

---

## **Scenario 11: Extending a Full Filesystem**

**Problem:**
Developers complain `/opt/apps` is full. The server has another free disk `/dev/sde`.

**Steps:**

1. **Check current LV usage.**

   ```bash
   df -h /opt/apps
   lvs
   ```

2. **Add new disk as PV.**

   ```bash
   pvcreate /dev/sde
   ```

3. **Extend VG with the new PV.**

   ```bash
   vgextend apps_vg /dev/sde
   ```

4. **Extend LV by 100 GB.**

   ```bash
   lvextend -L +100G /dev/apps_vg/apps_lv
   ```

5. **Grow filesystem.**

   * For **XFS**:

     ```bash
     xfs_growfs /opt/apps
     ```
   * For **EXT4**:

     ```bash
     resize2fs /dev/apps_vg/apps_lv
     ```

✅ **Result:** `/opt/apps` has 100 GB more space without downtime.

---

## **Scenario 12: Moving Data to a New Disk (Replace Old PV)**

**Problem:**
Old disk `/dev/sdb` is failing. You’ve added `/dev/sdf` and need to migrate data off safely.

**Steps:**

1. **Check existing PVs.**

   ```bash
   pvs
   ```

2. **Add the new disk.**

   ```bash
   pvcreate /dev/sdf
   vgextend apps_vg /dev/sdf
   ```

3. **Move data from failing disk.**

   ```bash
   pvmove /dev/sdb /dev/sdf
   ```

4. **Remove old PV from VG.**

   ```bash
   vgreduce apps_vg /dev/sdb
   pvremove /dev/sdb
   ```

✅ **Result:** Data fully migrated to new disk without downtime.

---

## **Scenario 13: Shrinking a Test Volume**

**Problem:**
Your `/testdata` LV is oversized (200G, but only 20G used). You want to reclaim space for other LVs.

⚠️ Shrinking is **risky**; always backup.

**Steps:**

1. **Check usage.**

   ```bash
   df -h /testdata
   ```

2. **Unmount.**

   ```bash
   umount /testdata
   ```

3. **Filesystem check.**

   ```bash
   e2fsck -f /dev/apps_vg/test_lv
   ```

4. **Resize filesystem first (e.g., to 50G).**

   ```bash
   resize2fs /dev/apps_vg/test_lv 50G
   ```

5. **Reduce LV size.**

   ```bash
   lvreduce -L 50G /dev/apps_vg/test_lv
   ```

6. **Mount again.**

   ```bash
   mount /dev/apps_vg/test_lv /testdata
   ```

✅ **Result:** `/testdata` reduced from 200G → 50G, space freed in VG.

---

## **Scenario 14: Recovering from Metadata Corruption**

**Problem:**
After a crash, LVM reports errors when scanning VGs.

**Steps:**

1. **Scan VGs.**

   ```bash
   vgscan
   ```

2. **Check metadata consistency.**

   ```bash
   vgck apps_vg
   ```

3. **Restore from backup.**

   ```bash
   vgcfgrestore apps_vg
   ```

✅ **Result:** VG restored to last known good state.

---

## **Scenario 15: Migrating a VG Between Servers**

**Problem:**
You need to move `/dev/sdg` (with `backup_vg`) from server A to server B.

**Steps on Server A:**

1. **Export VG.**

   ```bash
   vgexport backup_vg
   ```

2. **Physically move disk to Server B.**

**Steps on Server B:**

1. **Import VG.**

   ```bash
   vgimport backup_vg
   ```

2. **Activate VG.**

   ```bash
   vgchange -ay backup_vg
   ```

✅ **Result:** Volume group usable on the new server.

---

# 📌 Extended Summary of LVM Operations

* **PV**:

  * `pvcreate` (init disk), `pvremove`, `pvs`, `pvscan`, `pvmove`.
* **VG**:

  * `vgcreate`, `vgextend`, `vgreduce`, `vgs`, `vgdisplay`, `vgscan`, `vgck`, `vgexport`, `vgimport`, `vgcfgrestore`.
* **LV**:

  * `lvcreate`, `lvextend`, `lvresize`, `lvreduce`, `lvremove`, `lvs`, `lvdisplay`, `lvrename`.
* **FS grow/shrink**: `xfs_growfs`, `resize2fs`.

---
