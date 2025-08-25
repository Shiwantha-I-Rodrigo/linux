# 🔹 Scenarios 1

## **Scenario 1: The Developer’s Backup Disaster**

You are a developer working on a project called `webapp`. Inside your home directory, you have:

* `index.html` (contains some HTML code)
* `config.txt` (contains database credentials with some extra blank lines and tabs)
* `README.md` (project notes)

Your boss asks you to:

1. Create a directory structure `backups/2025/august` in one go, ensuring you see confirmation of each directory made.
2. Copy the entire `webapp` directory into that new backup folder, but make sure symbolic links, permissions, timestamps, and attributes are preserved.
3. Accidentally, an old copy exists there. This time, don’t overwrite existing files, but only copy if your local files are newer.
4. After finishing, verify directory structure with a nice tree view (including hidden files).

---

## **Scenario 2: Cleaning Up Old Logs**

Your system has many log files in `/var/logs/custom/`. Some are empty, some are outdated.

You must:

1. Find and list all empty files inside `/var/logs/custom`.
2. Search for files accessed in the last 30 minutes.
3. Delete all empty log files, but before each deletion, ask for confirmation.
4. There’s also an old empty directory `old_logs/`. Delete it carefully (but only if it’s empty).

---

## **Scenario 3: The Secret File**

The security team asks you to protect a configuration file called `db.conf`.

1. Make it **immutable** so nobody can modify or delete it.
2. List its attributes to verify.
3. Later, you are told to remove the immutable attribute and instead set it to append-only mode so logs can only grow.
4. Recursively check all file attributes inside `/etc/security/` to confirm settings.

---

## **Scenario 4: File Investigation Mission**

You suspect there are duplicate or misplaced files.

1. Search for all files larger than 10 MB inside `/home/`.
2. Find files modified in the last 5 minutes in `/tmp/`.
3. Locate a file called `budget2025.txt` using the indexing database, matching only the basename. Show only the count of matches.
4. Update the search database before running.
5. Then locate case-insensitive all files whose name includes `report`. Suppress all error messages.

---

## **Scenario 5: File Display Tricks**

You have a messy text file `config.txt` with:

```
    
DB_USER=admin
DB_PASS=secret123


DB_NAME=webapp

```

Your tasks:

1. Display the file contents with line numbers for **all lines**.
2. Display the file again, numbering only non-blank lines.
3. Show the file with `$` marking line endings.
4. Show the file with tabs replaced as `^I`.
5. Show the file with both tabs and non-printable characters visible.
6. Display the file while squeezing multiple blank lines into one.

---

## **Scenario 6: Moving & Linking**

You are reorganizing some files:

1. Move `README.md` into a new directory `docs/`, but prompt before overwriting if it exists.
2. Rename `config.txt` to `config.old` but don’t overwrite if `config.old` already exists.
3. Force-move `index.html` to `archive/` ignoring overwrite prompts.
4. Create a symbolic link `latest_config` pointing to `config.old`.

---

## **Scenario 7: Timestamp Adjustments**

You have a file `report2025.txt`.

1. Create it if it doesn’t exist.
2. Change only its **access time**.
3. Change only its **modification time**.

---

## **Scenario 8: Rsync & Remote Sync**

You have a directory `project/`.

1. Sync it locally to `/mnt/backup/` preserving everything (permissions, timestamps, symlinks, groups, etc.).
2. Sync the same folder to a remote server `dev@192.168.1.50:/home/dev/backups/` while compressing during transfer.
3. Ensure newer files on the destination are not overwritten.
4. Show the process with human-readable output and verbose logging.

---

## **Scenario 9: Disk-Level Copy**

Your boss asks you to create a backup image of a USB drive `/dev/sdb`.

1. Copy its entire content to `usb_backup.img` with a block size of 4 MB.
2. Only copy the first 100 blocks.
3. While copying, show real-time progress.

---

# 🔹 Example Files 1

### **1. `config.txt`**

Used in **Scenario 5** (cat display tricks).
Includes **blank lines, tabs, and normal lines**.

```txt

DB_USER=admin
	DB_PASS=secret123


DB_NAME=webapp

```

---

### **2. `README.md`**

Used in **Scenario 1** (backup) and **Scenario 6** (moving/renaming).

```markdown
# WebApp Project

This is a demo project for practicing Linux file operations.

- Version: 1.0
- Author: DevOps Team
- Last Updated: August 2025
```

---

### **3. `index.html`**

Also used in **Scenario 1** and **Scenario 6**.

```html
<!DOCTYPE html>
<html>
<head>
    <title>WebApp Demo</title>
</head>
<body>
    <h1>Welcome to WebApp</h1>
    <p>This is the homepage.</p>
</body>
</html>
```

---

### **4. `report2025.txt`**

Used in **Scenario 7** (touch timestamp adjustments).

```txt
Quarterly Report 2025

Prepared by: Finance Department
Status: Draft
```

---

### **5. `db.conf`**

Used in **Scenario 3** (file attributes with `chattr`).

```txt
DB_HOST=localhost
DB_PORT=5432
DB_USER=secure_user
DB_PASS=UltraSecret!
```

---

### **6. Directory Tree for `project/`**

Used in **Scenario 8** (rsync).
Here’s how you can create it:

```bash
mkdir -p project/src project/docs project/tests
echo "print('Hello World')" > project/src/app.py
echo "Project Documentation" > project/docs/guide.md
echo "Unit test cases" > project/tests/test_app.py
```

---

### **7. Example `/var/logs/custom/` logs**

Used in **Scenario 2** (cleanup).

```bash
mkdir -p /var/logs/custom/old_logs
touch /var/logs/custom/empty1.log
touch /var/logs/custom/empty2.log
echo "System started successfully" > /var/logs/custom/system.log
echo "Error: missing config" > /var/logs/custom/error.log
```

---

# 🔹 Answer Keys 1


## **Scenario 1**

```bash
mkdir -pv backups/2025/august
cp -a webapp backups/2025/august/
cp -unr webapp backups/2025/august/
tree -a
```

---

## **Scenario 2**

```bash
find /var/logs/custom -empty
find /var/logs/custom -amin -30
rm -i /var/logs/custom/*.log
rm -d old_logs/
```

---

## **Scenario 3**

```bash
chattr +i db.conf
lsattr db.conf
chattr -i db.conf
chattr +a db.conf
lsattr -R /etc/security/
```

---

## **Scenario 4**

```bash
find /home/ -size +10M
find /tmp/ -mmin -5
updatedb
locate -b -c budget2025.txt
locate -iq report
```

---

## **Scenario 5**

```bash
cat -n config.txt
cat -b config.txt
cat -E config.txt
cat -T config.txt
cat -t config.txt
cat -s config.txt
```

---

## **Scenario 6**

```bash
mv -i README.md docs/
mv -n config.txt config.old
mv -f index.html archive/
ln -s config.old latest_config
```

---

## **Scenario 7**

```bash
touch report2025.txt
touch -a report2025.txt
touch -m report2025.txt
```

---

## **Scenario 8**

```bash
rsync -a project/ /mnt/backup/
rsync -avzh project/ dev@192.168.1.50:/home/dev/backups/
rsync -avu project/ dev@192.168.1.50:/home/dev/backups/
```

---

## **Scenario 9**

```bash
dd if=/dev/sdb of=usb_backup.img bs=4M count=100 status=progress
```

---
---
---

#  🔹 Scenarios 2

## **Scenario 1 – System Admin Prepares Training Material**

A system administrator is tasked with preparing course files for Linux training.

1. They start by creating a nested directory structure for each student, ensuring parent directories are created even if missing, and want confirmation of each directory created.
2. They generate some blank text files for exercises and later adjust access/modification times for testing.
3. To fill them with data, they copy over template text files from `/srv/training/templates`, ensuring that subdirectories, permissions, and timestamps are preserved.
4. One of the students mistakenly created a duplicate file—so the admin renames it interactively to avoid overwriting existing files.
5. Later, while cleaning up, they remove unnecessary empty folders but want to be prompted before deletions.
6. For distributing shared notes, they create symbolic links pointing to a single "master copy".
7. At the end, they show the entire folder structure including hidden files.

---

## **Scenario 2 – Developer Debugging Log Files**

A developer is analyzing web server logs.

1. They want to quickly inspect log contents, showing line numbers for all lines.
2. For another log, they only want to number non-empty lines and also mark the end of each line to detect trailing spaces.
3. Some logs contain tabs and non-printable characters, so they decide to visualize them clearly.
4. Old logs with excessive blank lines need to be cleaned up visually.
5. They create a compressed backup copy of `access.log`, then make it immutable so that no process can overwrite it.
6. To verify attributes of all files in the log directory, including subdirectories, they recursively list them.
7. Finally, they sync these logs to a backup server with compression and verbose output so they can see progress.

---

## **Scenario 3 – Security Team File Audit**

The security team needs to audit suspicious files in `/home/projects`.

1. They first locate files by name patterns, ignoring case, and also search for an exact path match.
2. They want only the count of how many `.sh` scripts exist and use regex to identify ones ending in numbers.
3. Before running locate, they update the file index.
4. They also use `find` to detect files larger than 10 MB, files owned by a specific user, and empty directories.
5. Files with no valid owner or group need to be flagged.
6. They also detect files modified in the last 10 minutes for incident response.
7. At the end, they prepare a text report summarizing all findings.

---

## **Scenario 4 – Student Working on OS Assignment**

A computer science student is learning low-level Linux operations.

1. They create multiple text files containing C source code.
2. To experiment with file attributes, they mark one file as append-only, another as synchronous update, and one as undeletable.
3. To ensure recoverability, they then try copying and moving these files with update-only options, avoiding overwriting older files.
4. When a wrong file is created, they forcefully delete it along with its directory recursively.
5. They then use `dd` to copy the first few blocks of `/dev/zero` into a binary file, showing live progress.
6. For testing synchronization, they use `rsync` with archive mode and human-readable output.
7. At the end, they explore the project directory visually using a tree command restricted to directories only.

---

# 🔹 Example Files 2

Inside `/srv/training/templates/`:

* `welcome.txt`

  ```
  Welcome to the Linux Training!
  This file contains template data.
  ```

* `exercise1.txt`

  ```
  Step 1: Open your terminal.
  Step 2: Try basic commands.

  (Blank lines included for testing cat options.)
  ```

---

# 🔹 Answer Keys 2

## Scenario 1

```bash
mkdir -pv /home/training/student1/exercises
touch /home/training/student1/exercises/file1.txt
touch -a /home/training/student1/exercises/file2.txt
touch -m /home/training/student1/exercises/file3.txt

cp -ar /srv/training/templates/* /home/training/student1/exercises/
mv -i /home/training/student1/exercises/welcome.txt /home/training/student1/exercises/welcome_old.txt
rm -id /home/training/student1/old_folder

ln -s /srv/training/templates/master_notes.txt /home/training/student1/exercises/shared_notes.txt
tree -a /home/training/student1/
```

---

## Scenario 2

```bash
cat -n access.log
cat -bE error.log
cat -vT debug.log
cat -s messy.log

cp access.log access.log.gz
chattr +i access.log.gz
lsattr -R /var/log/webserver/

rsync -avzh /var/log/webserver/ backupuser@backupserver:/logs/webserver/
```

---

## Scenario 3

```bash
locate -i "project"
locate -w "/home/projects/config.yaml"
locate -c "*.sh"
locate -r ".*[0-9]\.sh$"
updatedb

find /home/projects -size +10M
find /home/projects -user alice
find /home/projects -empty
find /home/projects -nouser
find /home/projects -nogroup
find /home/projects -mmin -10 > /tmp/audit_report.txt
```

---

## Scenario 4

```bash
touch main.c utils.c extra.c
chattr +a main.c
chattr +S utils.c
chattr +u extra.c

cp -u main.c backup_main.c
mv -n utils.c utils_renamed.c
rm -rf wrongdir/

dd if=/dev/zero of=disk.img bs=1M count=10 status=progress

rsync -ah /home/student/projects/ /home/student/projects_backup/
tree -d /home/student/projects/
```

---