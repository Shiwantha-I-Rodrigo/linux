## 📘 Real-Life Workflows & Troubleshooting Stories

---

### **Story 1 – Growing a Production Filesystem**

**Context:**
Your `/var` partition is filling up rapidly because of growing log files on an **ext4** filesystem.

**Steps:**

1. **Check filesystem usage**

   ```bash
   df -h /var
   ```

   → You see it’s 95% full.

2. **Verify disk health before resizing**
   Run `badblocks` to ensure the underlying device has no critical issues.

   ```bash
   badblocks -sv /dev/sdb1
   ```

   * `-s`: show progress
   * `-v`: verbose output
   * Scans and reports bad sectors.

   *Explanation:* If you resize without checking, latent hardware issues might cause corruption.

3. **Resize filesystem after extending LV**
   Suppose you extended the LVM by 20G:

   ```bash
   lvextend -L +20G /dev/vg_data/lv_var
   resize2fs /dev/vg_data/lv_var
   ```

   * `resize2fs` automatically grows the ext4 filesystem to fill the new space.

4. **Verify the change**

   ```bash
   df -h /var
   ```

   → Space increased, problem solved.

---

### **Story 2 – Debugging Strange Filesystem Corruption**

**Context:**
Users complain they cannot read certain files on an **ext4** partition.

**Steps:**

1. **Inspect filesystem details**

   ```bash
   dumpe2fs -h /dev/sdc1
   ```

   * `-h`: show only the superblock info (block size, inode size, last mount status).

   *Explanation:* Quick way to check if the FS has errors flagged.

2. **Dig into filesystem interactively**

   ```bash
   debugfs /dev/sdc1
   ```

   Inside `debugfs`, you might run:

   * `stat /corrupted/file` → check inode info.
   * `ls -l /` → list root directory entries.

   *Explanation:* Useful when the kernel refuses to mount or you suspect inode corruption.

3. **Repair if necessary**
   If clear corruption is seen:

   ```bash
   fsck.ext4 -y /dev/sdc1
   ```

   * Forces repair automatically.

   *Explanation:* `debugfs` is diagnostic; actual fix comes from `fsck`.

---

### **Story 3 – Working with Multipath Storage**

**Context:**
Your SAN team added new LUNs to the server. You need to make them available.

**Steps:**

1. **Check and reload multipath devices**

   ```bash
   multipath -ll
   ```

   * Shows all multipath devices, paths, and status.

2. **Reconfigure after new LUNs**

   ```bash
   multipath -r
   ```

   * Reloads multipath mappings.

3. **Update partition mappings with `kpartx`**

   ```bash
   kpartx -av /dev/mapper/mpatha
   ```

   * `-a`: add mappings
   * `-v`: verbose

   *Explanation:* Creates device mapper nodes for each partition on the multipath device.

---

### **Story 4 – Repairing an XFS Filesystem**

**Context:**
After a sudden power outage, an **XFS** filesystem fails to mount.

**Steps:**

1. **Try mounting**

   ```bash
   mount /dev/sdd1 /mnt
   ```

   → Error: "metadata corruption detected".

2. **Gather details**

   ```bash
   xfs_info /dev/sdd1
   ```

   * Displays geometry, UUID, block size → confirms device is correct.

3. **Repair filesystem**

   ```bash
   xfs_repair /dev/sdd1
   ```

   * Attempts to fix structural corruption.

   *Note:* Unlike `ext4`, XFS **must not** be checked with `fsck`; always use `xfs_repair`.

4. **Relabel if necessary**

   ```bash
   xfs_admin -L data_vol /dev/sdd1
   ```

   * Sets a human-friendly label for clarity in `/etc/fstab`.

---

### **Story 5 – Restoring Files from a Damaged Btrfs Volume**

**Context:**
A backup server using **Btrfs** suffered disk errors; you need to salvage critical logs.

**Steps:**

1. **Run a check**

   ```bash
   btrfs check --readonly /dev/sde1
   ```

   * `--readonly`: ensures no modifications are made.

2. **Attempt file recovery**

   ```bash
   btrfs restore -vi /dev/sde1 /mnt/recovery
   ```

   * `-v`: verbose
   * `-i`: ignore errors and try to continue

   *Explanation:* `restore` is non-destructive, good for extracting files from a broken FS.

---

## ✅ Summary of Commonly Used Options

* **badblocks** → `-sv` (progress + verbose)
* **resize2fs** → (no option, just device/LV)
* **debugfs** → interactive (`stat`, `ls`)
* **dumpe2fs** → `-h` (superblock info)
* **e2label** → `e2label /dev/sdX LABEL` (set label)
* **kpartx** → `-av` (add partitions, verbose)
* **multipath** → `-ll`, `-r` (list, reload)
* **btrfs check** → `--readonly`
* **btrfs restore** → `-vi`
* **xfs\_admin** → `-L` (label FS)
* **xfs\_info** → (no option, just device)
* **xfs\_repair** → (no option, just device)

---
