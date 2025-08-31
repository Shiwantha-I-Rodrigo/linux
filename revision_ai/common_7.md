# 🔧 Real-Life Troubleshooting & Workflow Stories

---

## 1. Log Inspection and Cleanup

**Scenario:**
You’re investigating a disk space alert on `/var`.

**Step 1 – Inspect log file contents quickly**

```bash
cat -n /var/log/syslog | tail -20
```

* `-n` helps number the lines so you can reference the exact place an error occurred.
* Combined with `tail`, you quickly see the last 20 lines.

**Step 2 – Identify large old logs**

```bash
find /var/log -type f -size +100M -name "*.log"
```

* You search for log files over 100 MB to see potential culprits.

**Step 3 – Move old logs for archiving**

```bash
mv -i /var/log/syslog.1 /mnt/backup/logs/
```

* `-i` prevents accidental overwrite of existing backups.

**Step 4 – Delete only unnecessary rotated logs**

```bash
rm -i /var/log/*.gz
```

* Interactive removal avoids mistakes.

**Step 5 – Re-check disk usage**

```bash
du -sh /var/log/*
```

---

## 2. Web Server Deployment

**Scenario:**
You’re deploying a new app to `/var/www/`.

**Step 1 – Create directory structure**

```bash
mkdir -pv /var/www/myapp/{public,logs,config}
```

* `-p` ensures parent directories exist.
* `-v` shows which directories are created.

**Step 2 – Copy web files with permissions preserved**

```bash
cp -a ./release/* /var/www/myapp/public/
```

* `-a` preserves ownership, permissions, and symlinks.

**Step 3 – Use symlinks for config management**

```bash
ln -s /etc/myapp/config.yml /var/www/myapp/config/config.yml
```

* `-s` keeps configs in `/etc` but accessible from the app.

---

## 3. Syncing and Backups with rsync

**Scenario:**
Nightly backup of `/home` to NFS mount `/mnt/backup`.

**Step 1 – Manual test run**

```bash
rsync -avh --progress /home/ /mnt/backup/home/
```

* `-a` keeps attributes intact.
* `-v` verbose so you see what’s happening.
* `-h` human-readable sizes.
* `--progress` for visibility.

**Step 2 – Remote backup**

```bash
rsync -azvh /home/ user@backup01:/backups/home/
```

* `-z` compresses transfer, useful for WAN links.

---

## 4. Recovering Space from Old tmp Files

**Scenario:**
A build server’s `/tmp` is filling up.

**Step 1 – Find stale files**

```bash
find /tmp -type f -mmin +1440
```

* Finds files not modified in 24h.

**Step 2 – Remove interactively**

```bash
rm -I $(find /tmp -type f -mmin +1440)
```

* `-I` prompts once if more than 3 files.

---

## 5. Immutable Config for Security

**Scenario:**
You want to prevent accidental edits to `/etc/ssh/sshd_config`.

**Step 1 – Make immutable**

```bash
chattr +i /etc/ssh/sshd_config
```

**Step 2 – Verify**

```bash
lsattr /etc/ssh/sshd_config
```

**Step 3 – Attempt edit (will fail)**

```bash
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
# => Permission denied even as root
```

**Step 4 – Remove immutable for maintenance**

```bash
chattr -i /etc/ssh/sshd_config
```

---

## 6. Disk Imaging & Data Recovery

**Scenario:**
You’re cloning a failing disk to a new one.

**Step 1 – Clone with progress**

```bash
dd if=/dev/sdX of=/dev/sdY bs=64K status=progress
```

* `bs=64K` speeds up transfers.
* `status=progress` shows ongoing transfer.

**Step 2 – Copy only first 1GB for test**

```bash
dd if=/dev/sdX of=/mnt/backup/disk.img bs=1M count=1024 status=progress
```

---

# 📌 Summary of Commonly Used Options

* **cat** → `-n` (number lines)
* **cp** → `-a` (archive), `-i` (interactive)
* **mv** → `-i` (interactive)
* **rm** → `-i`, `-I`, `-r` (cautious removal)
* **mkdir** → `-p`, `-v`
* **touch** → (often no option, sometimes `-m`)
* **ln** → `-s`
* **find** → `-name`, `-iname`, `-size`, `-mmin`
* **locate** → `-i`, `-b`, `-r` (regex)
* **updatedb** → (no options, but often needed)
* **tree** → `-a`, `-d`
* **chattr** → `+i` (immutable), `+a` (append-only)
* **lsattr** → `-R` (recursive check)
* **rsync** → `-a`, `-v`, `-h`, `-z`, `-u`
* **dd** → `bs=`, `count=`, `status=progress`

---

# 🔧 Additional Real-Life Scenarios

---

## 7. User Reports "File Missing" but It’s There

**Scenario:**
A developer insists their script is missing in `/opt/tools`.

**Step 1 – Search quickly with locate**

```bash
locate -i toolscript.py
```

* `-i` → case-insensitive search, helpful when devs don’t remember exact case.

**Step 2 – Update the DB if locate gives nothing**

```bash
sudo updatedb
locate -i toolscript.py
```

**Step 3 – If still missing, try find by name**

```bash
find /opt/tools -iname "toolscript.py"
```

* `-iname` → case-insensitive match.

---

## 8. Cleaning Up a Dev Environment

**Scenario:**
A test VM’s `/home/dev` is bloated with random builds.

**Step 1 – List tree with dotfiles**

```bash
tree -a /home/dev
```

* `-a` → shows hidden `.git/` and `.cache/`.

**Step 2 – Remove whole build directories**

```bash
rm -rI /home/dev/build/
```

* `-r` → recursive.
* `-I` → one-time prompt for bulk deletion.

**Step 3 – Create new clean structure**

```bash
mkdir -pv /home/dev/{src,bin,logs}
```

---

## 9. Web App Rollback After Bad Deployment

**Scenario:**
A new release broke the app. You need to roll back fast.

**Step 1 – Sync yesterday’s backup back**

```bash
rsync -avh /mnt/backup/myapp-20230830/ /var/www/myapp/
```

* Preserves ownership, permissions, timestamps.

**Step 2 – Verify configs via symlink**

```bash
ls -l /var/www/myapp/config/config.yml
```

* Ensures symlink still points to `/etc/myapp/config.yml`.

**Step 3 – Restart service and tail logs**

```bash
systemctl restart apache2
cat -n /var/log/apache2/error.log | tail -20
```

---

## 10. Investigating Suspicious File Changes

**Scenario:**
A sysadmin suspects tampering in `/etc`.

**Step 1 – Look for immutable/append-only files**

```bash
lsattr -R /etc | grep "i"
```

* Finds files marked immutable (`i`).

**Step 2 – Remove immutable if confirmed safe**

```bash
chattr -i /etc/hosts
```

**Step 3 – Track recently modified configs**

```bash
find /etc -mmin -60
```

* Shows files modified in the last 60 minutes.

---

## 11. Debugging Backup Performance

**Scenario:**
Your backup job using `rsync` is slow.

**Step 1 – Run rsync with compression**

```bash
rsync -azvh /var/lib/mysql/ backup@server:/db-backups/
```

* `-z` compresses data over the wire.

**Step 2 – If still slow, test disk throughput**

```bash
dd if=/dev/zero of=/tmp/testfile bs=1G count=1 oflag=direct status=progress
```

* Measures write speed.

**Step 3 – Compare read speed**

```bash
dd if=/tmp/testfile of=/dev/null bs=1G count=1 status=progress
```

---

# 📌 Summary Insight

* Most **core workhorse options**:

  * `rsync -avh` → appeared in **all backup scenarios**.
  * `mkdir -pv` → the **default pattern** for creating trees.
  * `find -iname` / `-mmin` → **most common troubleshooting filters**.
  * `chattr +i` + `lsattr` → **security hardening & tampering checks**.
  * `dd bs= count= status=progress` → **always used for disk I/O tests/clones**.
  * `rm -rI` → safest deletion method.


# 🔧 More Scenarios

---

## 12. Restoring Accidentally Deleted Config

**Scenario:**
An engineer removed `/etc/nginx/nginx.conf` by mistake.

**Step 1 – Check backup directory**

```bash
ls -l /mnt/backup/nginx/
```

**Step 2 – Copy config back without overwriting existing test configs**

```bash
cp -i /mnt/backup/nginx/nginx.conf /etc/nginx/nginx.conf
```

* `-i` avoids overwriting if there’s already a partial restore attempt.

---

## 13. Tracking Down Orphaned Files

**Scenario:**
After removing a user, you want to check if files still exist owned by them.

**Step 1 – Find files with no owner**

```bash
find /home -nouser
```

**Step 2 – Reassign ownership or remove**

```bash
chown newuser:newuser /home/olduser_project/*
```

(or `rm -i` if not needed).

---

## 14. Investigating Apache Failing to Start

**Scenario:**
Apache won’t start after config changes.

**Step 1 – Find recently modified configs**

```bash
find /etc/httpd -mmin -10
```

* Shows configs touched in last 10 minutes.

**Step 2 – Check end of error log with line numbers**

```bash
cat -n /var/log/httpd/error_log | tail -20
```

**Step 3 – If config symlink broken, recreate**

```bash
ln -s /etc/httpd/sites-available/app.conf /etc/httpd/sites-enabled/app.conf
```

---

## 15. Developer Needs Quick File Count

**Scenario:**
A dev asks “How many `.py` files do we have in `/srv/app`?”

**Step 1 – Use locate for speed**

```bash
locate -b -i "*.py" | grep "^/srv/app" | wc -l
```

* `-b` → match only basename (faster).
* `-i` → case-insensitive.

**Step 2 – If DB outdated, fall back on find**

```bash
find /srv/app -iname "*.py" | wc -l
```

---

## 16. Enforcing Log Append-Only Mode

**Scenario:**
You want to prevent tampering with audit logs.

**Step 1 – Set append-only**

```bash
chattr +a /var/log/audit/audit.log
```

**Step 2 – Verify**

```bash
lsattr /var/log/audit/audit.log
```

**Step 3 – Confirm writing works, but editing fails**

```bash
echo "test append" >> /var/log/audit/audit.log
vi /var/log/audit/audit.log   # => will fail to save
```

---

## 17. Verifying a Large File Copy

**Scenario:**
You copied a 5 GB VM image to `/mnt/backup` and want to ensure it’s good.

**Step 1 – Copy file with progress**

```bash
rsync -avh --progress /var/lib/libvirt/images/vm.qcow2 /mnt/backup/
```

**Step 2 – Compare checksum**

```bash
sha256sum /var/lib/libvirt/images/vm.qcow2 /mnt/backup/vm.qcow2
```

---

# 📌 Insights

* **Always used**:

  * `cat -n` (logs),
  * `mkdir -pv`,
  * `ln -s`,
  * `rsync -avh`,
  * `dd bs= count= status=progress`
* **Frequently used**:

  * `find -mmin` / `-iname`,
  * `rm -i` / `-r`,
  * `cp -i`
* **Occasionally used but niche**:

  * `find -size`, `find -nouser`,
  * `locate -b`,
  * `chattr +a`

---