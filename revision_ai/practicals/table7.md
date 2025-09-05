## **Sample Files Setup**

```
/home/user/exam_lab/
├── docs/
│   ├── report1.txt
│   ├── report2.txt
│   └── notes.txt
├── scripts/
│   ├── backup.sh
│   ├── deploy.sh
│   └── cleanup.sh
├── data/
│   ├── input1.csv
│   ├── input2.csv
│   └── empty_file.txt
└── misc/
    ├── old.log
    ├── temp/
    │   └── temp1.txt
    └── README.md
```
---

## **Scenario Tasks**

### **Scenario 1: Inspect Files**

1. Display `docs/notes.txt` with line numbers for all lines.
2. Display `docs/notes.txt` with line numbers for non-blank lines only.
3. Display tabs and special characters in `docs/notes.txt`.
4. Display `$` at the end of each line in `docs/notes.txt`.
5. Display non-printing characters and end-of-line markers in `docs/notes.txt`.
6. Display non-printing characters and tabs in `docs/notes.txt`.
7. Squeeze multiple blank lines into one in `docs/notes.txt`.

---

### **Scenario 2: Copy Files and Directories**

1. Copy `docs/` and `scripts/` to `backup/` preserving structure and attributes.
2. Copy `data/input1.csv` to `backup/data/` without overwriting existing files.
3. Copy `data/input2.csv` to `backup/data/` interactively, prompting before overwrite.
4. Force copy `misc/README.md` to `backup/misc/`.
5. Recursively copy `misc/` to `backup/misc/`.
6. Copy only if source is newer than destination.

---

### **Scenario 3: Move Files**

1. Move all CSV files from `data/` to `backup/data/` only if newer.
2. Move `backup/misc/README.md` to `backup/docs/` interactively.
3. Force move `backup/scripts/deploy.sh` to `backup/docs/`.
4. Move `backup/scripts/cleanup.sh` without overwriting existing files.

---

### **Scenario 4: Remove Files and Directories**

1. Remove `backup/data/empty_file.txt` interactively.
2. Remove directory `misc/temp/` recursively and force.
3. Remove more than three files in `misc/` prompting only once.
4. Remove empty directories.

---

### **Scenario 5: Directory Creation and File Timestamps**

1. Create nested directories `archive/temp/old_files/` printing messages for each.
2. Change access time of `backup/data/input2.csv`.
3. Change modification time of `backup/data/input2.csv`.

---

### **Scenario 6: Create Links**

1. Create symbolic link from `backup/docs/report1.txt` to `latest_report.txt`.
2. Create hard link from `backup/docs/notes.txt` to `notes_hardlink.txt`.

---

### **Scenario 7: Find and Locate**

1. Find all files in `backup/` larger than 1 KB.
2. Find files accessed in the last 30 minutes.
3. Find files modified in last 60 minutes.
4. Find files with status change in last 10 minutes.
5. Find files owned by a specific user and group.
6. Find files with 644 permissions.
7. Find files whose owner or group no longer exists.
8. Find empty files or directories.
9. Update `mlocate` database.
10. Locate all `.sh` files case-insensitive.
11. Locate files using regex starting with `report`.
12. Count number of `.txt` files in lab.
13. Locate files matching only basename.

---

### **Scenario 8: Show Directory Tree**

1. Show full tree of `backup/` including hidden files.
2. Show only directories.

---

### **Scenario 9: File Attributes**

1. Make `backup/docs/report2.txt` immutable.
2. Make `backup/scripts/deploy.sh` append-only.
3. Prevent atime updates on `backup/data/input1.csv`.
4. Mark `backup/data/input2.csv` for compression.
5. Set directory for case-insensitive lookups.
6. Mark file for secure deletion.
7. Make file recoverable after deletion.
8. List all attributes recursively in `backup/`.

---

### **Scenario 10: Bit-Level Copy**

1. Copy `docs/notes.txt` to `backup/data/notes_backup.txt` using 512-byte blocks showing progress.
2. Copy first 5 blocks of `docs/report1.txt` to `backup/data/report1_partial.txt`.

---

### **Scenario 11: Rsync**

1. Sync `backup/` to remote host preserving all attributes.
2. Compress data during transfer.
3. Skip newer files on destination.
4. Use verbose and human-readable output.
5. Preserve symbolic links, permissions, timestamps, group, owner, and devices.
6. Test remote-to-local transfer syntax.

---

### **Scenario 12: Archiving**

1. Create compressed archive of `scripts/` using gzip.
2. Create archive with bzip2.
3. Create archive with xz.
4. Update archive with new `.sh` files.
5. Compare archive with filesystem.
6. List archive contents.
7. Extract archive to `restore/scripts/`.
8. Create `cpio` archive from `docs/` to `docs_backup.cpio`.
9. Extract `cpio` archive to `restore/docs/`.
10. list content of `docs_backup.cpio`

---
---
---
---

## **Scenario 1: Inspect Files**

```bash
# 1. Line numbers for all lines
cat -n docs/notes.txt

# 2. Line numbers for non-blank lines only
cat -b docs/notes.txt

# 3. Show tabs and special characters
cat -vT docs/notes.txt

# 4. Show $ at end of each line
cat -E docs/notes.txt

# 5. Show non-printing characters + end-of-line markers
cat -e docs/notes.txt

# 6. Show non-printing characters + tabs
cat -t docs/notes.txt

# 7. Squeeze multiple blank lines into one
cat -s docs/notes.txt
```

---

## **Scenario 2: Copy Files and Directories**

```bash
# 1. Copy directories preserving all attributes and structure
cp -a docs/ backup/
cp -a scripts/ backup/

# 2. Copy file without overwriting existing
cp -n data/input1.csv backup/data/

# 3. Copy file interactively
cp -i data/input2.csv backup/data/

# 4. Force copy
cp -f misc/README.md backup/misc/

# 5. Recursively copy
cp -r misc/ backup/misc/

# 6. Copy only if source is newer
cp -u data/input1.csv backup/data/
```

---

## **Scenario 3: Move Files**

```bash
# 1. Move CSV files if newer
mv -u data/*.csv backup/data/

# 2. Move interactively
mv -i backup/misc/README.md backup/docs/

# 3. Force move
mv -f backup/scripts/deploy.sh backup/docs/

# 4. Move without overwriting
mv -n backup/scripts/cleanup.sh backup/docs/
```

---

## **Scenario 4: Remove Files and Directories**

```bash
# 1. Remove interactively
rm -i backup/data/empty_file.txt

# 2. Remove recursively and force
rm -rf misc/temp/

# 3. Prompt once for multiple files
rm -I misc/*

# 4. Remove empty directories
rm -d archive/temp/old_files/
```

---

## **Scenario 5: Directory Creation and File Timestamps**

```bash
# 1. Create nested directories with messages
mkdir -pv archive/temp/old_files/

# 2. Change access time
touch -a backup/data/input2.csv

# 3. Change modification time
touch -m backup/data/input2.csv
```

---

## **Scenario 6: Create Links**

```bash
# 1. Symbolic link
ln -s backup/docs/report1.txt latest_report.txt

# 2. Hard link
ln backup/docs/notes.txt notes_hardlink.txt
```

---

## **Scenario 7: Find and Locate**

```bash
# 1. Files larger than 1 KB
find backup/ -size +1k

# 2. Files accessed last 30 minutes
find backup/ -amin -30

# 3. Files modified last 60 minutes
find backup/ -mmin -60

# 4. Files with status change last 10 minutes
find backup/ -cmin -10

# 5. Files by user/group
find backup/ -user username -group groupname

# 6. Files with 644 permissions
find backup/ -perm 644

# 7. Files whose owner or group no longer exists
find backup/ -nouser
find backup/ -nogroup

# 8. Empty files/directories
find backup/ -empty

# 9. Update locate database
sudo updatedb

# 10. Locate .sh files case-insensitive
locate -i '*.sh'

# 11. Locate files starting with "report" using regex
locate -r '^report'

# 12. Count number of .txt files
locate -c '*.txt'

# 13. Match only basename
locate -b 'notes.txt'
```

---

## **Scenario 8: Show Directory Tree**

```bash
# 1. Full tree including hidden files
tree -a backup/

# 2. Only directories
tree -d backup/
```

---

## **Scenario 9: File Attributes**

```bash
# 1. Immutable
chattr +i backup/docs/report2.txt

# 2. Append-only
chattr +a backup/scripts/deploy.sh

# 3. No atime updates
chattr +A backup/data/input1.csv

# 4. Compressed
chattr +c backup/data/input2.csv

# 5. Case-insensitive directory
chattr +F backup/docs/

# 6. Secure deletion
chattr +s backup/data/input1.csv

# 7. Undeletable/recoverable
chattr +u backup/data/input2.csv

# 8. List all attributes recursively
lsattr -R backup/
```

---

## **Scenario 10: Bit-Level Copy**

```bash
# 1. Copy with 512-byte blocks, show progress
dd if=docs/notes.txt of=backup/data/notes_backup.txt bs=512 status=progress

# 2. Copy first 5 blocks
dd if=docs/report1.txt of=backup/data/report1_partial.txt bs=512 count=5
```

---

## **Scenario 11: Rsync**

```bash
# 1. Sync preserving all attributes
rsync -a backup/ user@remotehost:/mnt/remote_backup/

# 2. Compress during transfer
rsync -az backup/ user@remotehost:/mnt/remote_backup/

# 3. Skip newer files on destination
rsync -au backup/ user@remotehost:/mnt/remote_backup/

# 4. Verbose + human-readable
rsync -avh backup/ user@remotehost:/mnt/remote_backup/

# 5. Preserve links, permissions, timestamps, group, owner, devices
rsync -alptgoD backup/ user@remotehost:/mnt/remote_backup/

# 6. Remote-to-local transfer syntax
rsync -avh user@remotehost:/mnt/remote_backup/ backup/
```

---

## **Scenario 12: Archiving**

```bash
# 1. Create gzip archive
tar -czvf scripts_backup.tar.gz scripts/

# 2. Create bzip2 archive
tar -cjvf scripts_backup.tar.bz2 scripts/

# 3. Create xz archive
tar -cJvf scripts_backup.tar.xz scripts/

# 4. Update archive with new files
tar -uvf scripts_backup.tar.gz scripts/

# 5. Compare archive with filesystem
tar -df scripts_backup.tar.gz scripts/

# 6. List archive contents
tar -tf scripts_backup.tar.gz

# 7. Extract archive
tar -xf scripts_backup.tar.gz -C restore/scripts/

# 8a. Create cpio archive using stdout redirection
find docs/ | cpio -o > docs_backup.cpio

# 8b. Alternative creation using -O with input file list
find docs/ > filelist.txt
cpio -O docs_backup_alt.cpio < filelist.txt

# 9. Extract cpio archive into restore/docs/ (GNU syntax with -D)
mkdir -p restore/docs
cpio -i -I docs_backup.cpio -D restore/docs/

# 10. List contents of cpio archive
cpio -t -I docs_backup.cpio

---