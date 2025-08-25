# 🔹 Scenarios

## **Scenario 1 – Data Recovery on a Faulty Ext4 Drive**

You are an on-call sysadmin for a media production company.
At 2 AM, an external hard drive used by video editors suddenly stops mounting.
The team reports important video files (`project1.mp4`, `project2.mp4`) are inside.
You need to:

1. Check if the disk has bad sectors.
2. Look inside the filesystem metadata interactively to confirm corruption.
3. Dump the filesystem superblock and group info to a text file for later auditing.
4. Attempt to restore lost video files from the corrupted filesystem.

A small `lostfiles.txt` list is also found on the disk, containing:

```
project1.mp4
project2.mp4
backup.mov
```

---

## **Scenario 2 – Expanding Storage for a Growing Application**

A SaaS startup runs PostgreSQL on an `ext4` volume, which is outgrowing.
Your manager attaches a bigger disk and asks you to:

1. Map newly created partitions from the device.
2. Resize the existing ext4 filesystem to use the new space.
3. Set a new descriptive label (`pgSQL-Data`) to identify the volume easily.
4. Verify the new filesystem size and UUID for inventory documentation.

---

## **Scenario 3 – Balancing and Checking Btrfs Storage Pool**

A research lab stores genomic datasets in a **Btrfs storage pool** with multiple drives.
Recently, performance slowed and researchers suspect imbalance.
You are tasked to:

1. Rebalance the filesystem data chunks across all devices.
2. Run an offline consistency check on the filesystem.
3. Add a new storage device to the pool.
4. Enable quotas to prevent a single user from filling the storage.
5. Rename the filesystem to `genomics_data` for clarity.

---

## **Scenario 4 – Recovering Files from Btrfs Disaster**

An intern mistakenly formatted a Btrfs volume where project reports were stored.
Your boss urgently wants you to:

1. Attempt recovery of files from the damaged Btrfs system.
2. Save all recovered files to `/mnt/recovered/`.
3. Document the attempt and provide a listing of restored files in `restore_log.txt`.

---

## **Scenario 5 – Repairing an XFS Partition after Power Failure**

A power outage corrupts an **XFS filesystem** holding financial records.
Your tasks are:

1. Show the filesystem details (block size, UUID).
2. Change the label to `Finance_2025`.
3. Run a repair utility to fix corruption.
4. Confirm again that the filesystem is usable and details updated.

---

## **Scenario 6 – Multipath Setup for Redundant Storage**

Your data center has a storage server with two paths to the same SAN device.
You need to:

1. Configure multipath I/O for the SAN.
2. Verify that the paths are correctly detected and mapped.
3. Document the multipath device name for future monitoring.

---

## **Scenario 7 – Preparing a New Disk with Thorough Testing**

Your company buys a batch of new SSDs. Before deploying one in production:

1. Perform a **non-destructive read/write bad block test** on `/dev/sdh`.
2. Save the list of detected bad blocks to `ssd_badblocks.txt`.
3. After verification, resize the ext4 partition to use the full space.
4. Label it `MediaStore2025` for easy identification.

---

## **Scenario 8 – Advanced Multipath Storage Verification**

A storage admin configured SAN multipathing, but you need to confirm it’s fully working:

1. Use device-mapper to create partition mappings.
2. Run multipath discovery in **detailed output mode**.
3. List multipath devices with **long-format listing** for documentation.
4. Save details to `multipath_report.txt`.

---

## **Scenario 9 – Deep Btrfs Maintenance**

Your AI research group reports performance degradation in `/mnt/ai_data` (Btrfs).
Tasks:

1. Start a **convert metadata balance** job.
2. Perform a **read-only check** of the filesystem for safety.
3. Replace a failing device (`/dev/sdi`) with a new one (`/dev/sdj`).
4. Confirm with `btrfs device stats`.
5. Display filesystem usage summary and verify it’s named `ai_cluster`.

---

# 🔹 Answer Keys

## **Scenario 1 – Data Recovery on a Faulty Ext4 Drive**

```bash
badblocks -v /dev/sdb > badblocks_report.txt
debugfs /dev/sdb
dumpe2fs /dev/sdb > fs_dump.txt
btrfs restore /dev/sdb1 /mnt/recovered/
```

*(using `lostfiles.txt` for reference to recovered files)*

---

## **Scenario 2 – Expanding Storage for a Growing Application**

```bash
kpartx -av /dev/sdc
resize2fs /dev/sdc1
e2label /dev/sdc1 pgSQL-Data
dumpe2fs /dev/sdc1 | grep -E "Filesystem size|UUID"
```

---

## **Scenario 3 – Balancing and Checking Btrfs Storage Pool**

```bash
btrfs balance start /mnt/genomics
btrfs check /dev/sdd1
btrfs device add /dev/sde1 /mnt/genomics
btrfs quota enable /mnt/genomics
btrfs filesystem label /mnt/genomics genomics_data
```

---

## **Scenario 4 – Recovering Files from Btrfs Disaster**

```bash
btrfs restore /dev/sdf1 /mnt/recovered/
ls -l /mnt/recovered/ > restore_log.txt
```

---

## **Scenario 5 – Repairing an XFS Partition after Power Failure**

```bash
xfs_info /dev/sdg1
xfs_admin -L Finance_2025 /dev/sdg1
xfs_repair /dev/sdg1
xfs_info /dev/sdg1
```

---

## **Scenario 6 – Multipath Setup for Redundant Storage**

```bash
multipath -v2
multipath -ll
```

---

## **Scenario 7 – Preparing a New Disk with Thorough Testing**

```bash
badblocks -n -o ssd_badblocks.txt /dev/sdh
resize2fs /dev/sdh1
e2label /dev/sdh1 MediaStore2025
```

---

## **Scenario 8 – Advanced Multipath Storage Verification**

```bash
kpartx -av /dev/san_disk
multipath -v3
multipath -ll > multipath_report.txt
```

---

## **Scenario 9 – Deep Btrfs Maintenance**

```bash
btrfs balance start -m /mnt/ai_data
btrfs check --readonly /dev/sdi1
btrfs device replace /dev/sdi /dev/sdj /mnt/ai_data
btrfs device stats /mnt/ai_data
btrfs filesystem df /mnt/ai_data
btrfs filesystem label /mnt/ai_data ai_cluster
```

---