# 🔹 Scenarios 1

## **Scenario 1 – University Research Backup**

Dr. Kim is working on multiple research projects stored in folders:

* `climate_data/` (CSV files)
* `student_reports/` (text reports)
* `simulations/` (binary output files)

She needs to:

1. Archive all three folders into one file.
2. Ensure the archive is compressed using **different algorithms** for testing efficiency (gzip, bzip2, xz).
3. Verify the contents after creating the archives.
4. Later, extract the bzip2-compressed archive to restore files for a PhD student.
5. Finally, add new reports from `student_reports/new/` into the original tar archive without recreating it.

---

## **Scenario 2 – Startup Dev Team Deployment**

A startup team is preparing application deployment:

* They have config files: `app.conf`, `db.conf`, `cache.conf`.
* The team lead wants all configs packaged neatly.
* The package should also have a **list of contents** stored separately for review.
* Before release, they want to compare the archive contents with the filesystem to ensure no files were missed.
* During an urgent bug fix, the `cache.conf` was updated and needs to be added to the archive incrementally.

---

## **Scenario 3 – Digital Library Migration**

A university library is migrating old text-based catalogs:

* Files: `catalog1.txt`, `catalog2.txt`, `catalog3.txt`.
* They decide to compress the files individually using **gzip**, **bzip2**, and **xz** to see which gives the best ratio.
* Some librarians accidentally compressed the same file twice, so IT needs to decompress them correctly.
* For exchange with another branch, they need to create a **ZIP archive** of all the catalog files.
* Later, they need to **extract the ZIP archive** at the branch library system.

---

## **Scenario 4 – Government Department Data Exchange**

A department is preparing daily logs to send securely to another branch:

* Logs: `day1.log`, `day2.log`, `day3.log`.
* Instead of `tar`, they use `cpio`.
* They want to:

  1. Generate an archive with all logs.
  2. Save it into a file for transfer.
  3. On the receiving side, list the contents before extraction.
  4. Finally, extract the files from the archive into the working directory.

---

## **Scenario 5 – Disaster Recovery Simulation**

An enterprise IT team is simulating a server crash recovery:

* They keep weekly archives of `/etc`, `/var/log`, and `/home/projects`.
* They create **incremental backups** using snapshots.
* After a simulated crash, they restore the files by extracting.
* The compliance officer requests verification of the restored archive.

---

# 🔹 Example Files 1

For the scenarios, assume these text files exist:

**climate\_data/temp.csv**

```
date,temp
2023-01-01,5
2023-01-02,4
```

**student\_reports/report1.txt**

```
Student: Alice
Topic: Climate Modeling
```

**simulations/output1.bin**
(binary file placeholder)

**app.conf**

```
APP_ENV=production
PORT=8080
```

**db.conf**

```
DB_USER=admin
DB_PASS=secret
```

**cache.conf**

```
CACHE_SIZE=256MB
```

**catalog1.txt**

```
BookID: 1001
Title: Data Science 101
```

**day1.log**

```
[INFO] System check complete.
```

---

# 🔹 Answer Key 1

## **Scenario 1**

```bash
tar -cvf research.tar climate_data student_reports simulations
tar -czvf research.tar.gz climate_data student_reports simulations
tar -cjvf research.tar.bz2 climate_data student_reports simulations
tar -cJvf research.tar.xz climate_data student_reports simulations

tar -tvf research.tar
tar -tvf research.tar.bz2

tar -xvjf research.tar.bz2

tar -uvf research.tar student_reports/new
```

---

## **Scenario 2**

```bash
tar -cvf configs.tar app.conf db.conf cache.conf
tar -tvf configs.tar > configs_list.txt
tar -dvf configs.tar
tar -uvf configs.tar cache.conf
```

---

## **Scenario 3**

```bash
gzip catalog1.txt
bzip2 catalog2.txt
xz catalog3.txt

gunzip catalog1.txt.gz
bunzip2 catalog2.txt.bz2
unxz catalog3.txt.xz

zip library.zip catalog1.txt catalog2.txt catalog3.txt
unzip library.zip
```

---

## **Scenario 4**

```bash
ls day*.log | cpio -ov > logs.cpio
cpio -it < logs.cpio
cpio -id < logs.cpio
```

---

## **Scenario 5**

```bash
tar -cvf fullbackup.tar /etc /var/log /home/projects
tar -g snapshot.file -cvf incr_backup.tar /etc /var/log /home/projects

tar -xvf fullbackup.tar
tar -xvf incr_backup.tar

tar -Wvf fullbackup.tar
```

---
---
---

# 🔹 Scenarios 2

## **Scenario 1: The Researcher’s Backup**

Dr. Meera is working on a project about climate change. She has three data files:

* `temperature.txt`
* `rainfall.txt`
* `humidity.txt`

She wants to:

1. Bundle them together for archiving.
2. Check the archive’s contents before sending to her colleague.
3. Later, she realizes she has an updated `humidity.txt` and needs to add it without recreating the whole archive.
4. Before sending, she verifies the archive against the filesystem to ensure everything matches.
5. Finally, she extracts the files on her colleague’s computer.

---

## **Scenario 2: The System Administrator’s Incremental Backup**

On a Fedora 34 server, Alex maintains log files stored in `/var/logs/app/`.

He needs to:

1. Take a **full backup** of the logs on Monday.
2. On Tuesday, take an **incremental backup** (only changes since Monday).
3. Confirm what files are inside the backup archive.
4. Later, restore the files in case the system crashes.

---

## **Scenario 3: The Student’s Storage Problem**

Ravi has a collection of notes (`math.txt`, `physics.txt`, `chemistry.txt`).

He wants to:

1. Compress `math.txt` using gzip, `physics.txt` using bzip2, and `chemistry.txt` using xz to compare file sizes.
2. Later, he decompresses each of them to get back the original files.

---

## **Scenario 4: The Designer’s Project Delivery**

A graphic designer needs to deliver project files (`logo.png`, `banner.png`, `flyer.png`) to a client.

She wants to:

1. Create a ZIP archive of all three files.
2. Email it to the client.
3. The client extracts the ZIP on Ubuntu 20.04.

---

## **Scenario 5: The Legacy Engineer**

In an old RockyOS 8.5 environment, a legacy script generates a file list of configuration files:

```
/etc/hosts
/etc/fstab
/etc/passwd
```

The engineer needs to:

1. Archive these files using `cpio`.
2. List the contents of the archive.
3. Later, extract them back from the archive.

---

## **Scenario 6: The Photographer’s Cross-Platform Delivery**

A wildlife photographer has three high-res images (`tiger.jpg`, `lion.jpg`, `elephant.jpg`).

1. She wants to create a compressed archive using gzip directly via tar for quick transfer.
2. Then another archive using bzip2 for long-term storage.
3. Finally, one more using xz for maximum compression.
4. After creating the archives, she verifies that the gzip archive was written correctly.
5. Later, she extracts each compressed archive to confirm the files are intact.

---

## **Scenario 7: The Historian’s Old Archive Conversion**

A historian on RockyOS 8.5 finds an old text list of manuscripts (`manuscripts.txt`):

```
scroll1.txt
scroll2.txt
scroll3.txt
```

1. He creates a cpio archive and writes it explicitly using `-O`.
2. Later, he receives the same archive from another researcher and extracts it using `-I`.
3. Before extraction, he lists its contents.

---

# 🔹 Answer Keys 2

## **Scenario 1: The Researcher’s Backup**

```bash
# Create archive
tar -cvf research.tar temperature.txt rainfall.txt humidity.txt  

# List contents
tar -tf research.tar  

# Update archive with new humidity.txt
tar -uvf research.tar humidity.txt  

# Verify archive against filesystem
tar -d -f research.tar  

# Extract on colleague’s system
tar -xvf research.tar  
```

---

## **Scenario 2: The System Administrator’s Incremental Backup**

```bash
# Full backup (Monday)
tar -cvf full-backup.tar -g snapshot.file /var/logs/app/  

# Incremental backup (Tuesday)
tar -cvf incr-backup.tar -g snapshot.file /var/logs/app/  

# List contents
tar -tf incr-backup.tar  

# Restore from backup
tar -xvf incr-backup.tar  
```

---

## **Scenario 3: The Student’s Storage Problem**

```bash
# Compress with gzip
gzip math.txt  

# Decompress with gunzip
gunzip math.txt.gz  

# Compress with bzip2
bzip2 physics.txt  

# Decompress with bunzip2
bunzip2 physics.txt.bz2  

# Compress with xz
xz chemistry.txt  

# Decompress with unxz
unxz chemistry.txt.xz  
```

---

## **Scenario 4: The Designer’s Project Delivery**

```bash
# Create ZIP
zip project.zip logo.png banner.png flyer.png  

# Extract on client’s system
unzip project.zip  
```

---

## **Scenario 5: The Legacy Engineer**

```bash
# Create cpio archive
cat filelist.txt | cpio -o > configs.cpio  

# List contents
cpio -it < configs.cpio  

# Extract files
cpio -id < configs.cpio  
```

*(where `filelist.txt` contains `/etc/hosts /etc/fstab /etc/passwd` line by line)*

---

## **Scenario 6: The Photographer’s Cross-Platform Delivery**

```bash
# Create tar with gzip
tar -cvzf photos.tar.gz tiger.jpg lion.jpg elephant.jpg  

# Create tar with bzip2
tar -cvjf photos.tar.bz2 tiger.jpg lion.jpg elephant.jpg  

# Create tar with xz
tar -cvJf photos.tar.xz tiger.jpg lion.jpg elephant.jpg  

# Verify gzip archive after writing
tar -Wvf photos.tar.gz  

# Extract each archive
tar -xvzf photos.tar.gz  
tar -xvjf photos.tar.bz2  
tar -xvJf photos.tar.xz  
```

---

## **Scenario 7: The Historian’s Old Archive Conversion**

```bash
# Create archive with -O
cpio -o -O manuscripts.cpio < manuscripts.txt  

# List contents
cpio -t -I manuscripts.cpio  

# Extract from archive
cpio -i -I manuscripts.cpio  
```

---