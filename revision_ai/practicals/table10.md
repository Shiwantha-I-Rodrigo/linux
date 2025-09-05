### **Sample Files / Setup**

```
/tmp/user_home/
└── sample_file.txt
/tmp/project/
├── report.doc
└── logs/
    └── access.log
```

Initial groups:

```
developers
admins
guests
```

Initial users:

```
alice (UID 1001, group: developers)
bob (UID 1002, group: guests)
```

---

## **Scenario 1: Creating Users and Groups (Full Coverage)**

**Tasks:**

* Create system group `auditors` with GID 3005.
* Create group `reviewers` but allow duplicate GID.
* Create a user `charlie` with:

  * Full name
  * Home directory `/tmp/user_home/charlie`
  * Do **not** create home directory
  * Primary group `auditors`
  * Supplementary groups `developers,admins`
  * UID 1050
  * Login shell `/bin/bash`
  * Account expires in 30 days
  * Base home directory `/tmp/user_home`
* Show default `useradd` settings.
* Modify `bob` to:

  * Change home directory
  * Add supplementary group `auditors`
  * Change primary group to `admins`
  * Change UID
  * Change login name to `bobby`
  * Change shell to `/bin/sh`
* Delete group `guests`.

---

## **Scenario 2: Passwords and Account Policies**

**Tasks:**

* Set password for `charlie`
* Delete password for `bob` (lock login)
* Force `alice` to change password on next login
* Lock and unlock `bob`’s password
* Set inactive days and max/min password age for `charlie`
* Warn user before expiration
* Show password status

---

## **Scenario 3: Ownership and Permissions**

**Tasks:**

* Change ownership of `/tmp/project/report.doc` to `charlie:auditors`
* Recursively change group ownership of `/tmp/project/logs/` to `admins`
* Set permissions numerically and symbolically

---

## **Scenario 4: User Info & Monitoring**

**Tasks:**

* Show `charlie`’s UID, GID, all groups, and numeric vs. name
* Show effective UID/GID for `alice`
* Show logged-in users with `who`, `whoami`, `w`
* List last successful and failed logins for `bob`, using alternate wtmp/btmp files
* Show last login for `alice`

---

## **Scenario 5: Failed Logins**

**Tasks:**

* Check failed login attempts for `charlie`
* Reset failed login attempts for `bob`
* Lock account if failed attempts exceed 3

---

## **Scenario 6: Switching Users and Privileges**

**Tasks:**

* Switch to `charlie` and verify username
* Edit `/tmp/project/report.doc` using `sudoedit`
* Use `sudo` to change ownership of `/tmp/project/logs/access.log` to `alice:admins`

---

# **Revised Answer Key (Full Coverage)**

### **Scenario 1: Users and Groups**

```bash
# Create system group auditors with GID
sudo groupadd -r -g 3005 auditors

# Create group reviewers allowing duplicate GID
sudo groupadd -o reviewers

# Show default useradd settings
useradd -D

# Add user charlie with full coverage of options
sudo useradd \
  -c "Charlie Brown" \
  -d /tmp/user_home/charlie \
  -M \
  -g auditors \
  -G developers,admins \
  -s /bin/bash \
  -u 1050 \
  -e $(date -d "+30 days" +%Y-%m-%d) \
  -b /tmp/user_home \
  charlie

# Modify bob (home, groups, UID, login name, shell)
sudo usermod -d /tmp/user_home/bob_new -m -G auditors -g admins -u 1100 -l bobby -s /bin/sh bob

# Delete group guests
sudo groupdel guests
```

---

### **Scenario 2: Passwords and Policies**

```bash
# Set password for charlie (encrypted)
sudo passwd charlie

# Delete password for bob (lock login)
sudo passwd -d bobby

# Force alice to change password
sudo passwd -e alice

# Lock/unlock bob's password
sudo passwd -l bobby
sudo passwd -u bobby

# Set inactive days (-i), min (-n) and max (-x) password age, warn before expiration (-w)
sudo passwd -i 5 -n 2 -x 30 -w 7 charlie

# Show password status
sudo passwd -S alice
sudo passwd -S charlie
```

---

### **Scenario 3: Ownership & Permissions**

```bash
# Change ownership of report.doc
sudo chown charlie:auditors /tmp/project/report.doc

# Recursively change group ownership of logs
sudo chgrp -R admins /tmp/project/logs/

# Permissions
chmod 640 /tmp/project/report.doc       # numeric
chmod u+rwx,g+rx,o-rwx /tmp/project/logs/  # symbolic
```

---

### **Scenario 4: User Info & Monitoring**

```bash
# Show charlie's UID/GID/groups
id charlie
id -u charlie
id -g charlie
id -G charlie
id -nG charlie  # name instead of number

# Effective UID/GID for alice
id -u alice
id -g alice

# Logged-in users
who
whoami
w

# Last successful and failed logins using alternate files
last -f /var/log/wtmp bob
lastb -f /var/log/btmp bob

# Last login for alice
lastlog -u alice
```

---

### **Scenario 5: Failed Logins**

```bash
# Check failed logins for charlie
faillock -u charlie
pam_tally2 -u charlie

# Reset failed attempts for bob
faillock -u bobby -r
pam_tally2 -u bobby -r

# Lock account if failed attempts exceed 3
if [ $(pam_tally2 -u bobby | awk '{print $2}') -gt 3 ]; then
  sudo faillock -u bobby
fi
```

---

### **Scenario 6: Switching Users and Privileges**

```bash
# Switch to charlie
su - charlie
whoami

# Edit file as root using sudoedit
sudoedit /tmp/project/report.doc

# Change ownership using sudo
sudo chown alice:admins /tmp/project/logs/access.log
```

---
