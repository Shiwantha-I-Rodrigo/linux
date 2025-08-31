### **1️⃣ Real-life Workflow: Adding and Configuring a User**

**Scenario:** You need to create a developer account, assign groups, set a home directory, and a shell.

**Steps:**

1. **Add the user with a home directory and comment:**

```bash
sudo useradd -m -c "John Doe" -s /bin/bash johnd
```

* `-m` → creates `/home/johnd`
* `-c` → adds full name for clarity in `/etc/passwd`
* `-s` → sets `/bin/bash` as the login shell

2. **Assign the user to supplementary groups:**

```bash
sudo usermod -aG sudo,docker johnd
```

* `-G` → adds groups
* `-a` → appends instead of overwriting

3. **Set the initial password:**

```bash
sudo passwd johnd
```

* Interactive password entry

4. **Verify the user info:**

```bash
id johnd
```

* Shows UID, GID, and all groups

**Explanation:**
This workflow covers **user creation, configuration, and verification**, which is standard when provisioning accounts.

---

### **2️⃣ Real-life Workflow: Deleting a User and Cleaning Up**

**Scenario:** A contractor leaves, you need to remove their account and home directory.

**Steps:**

1. **Delete the user and their home directory/mail:**

```bash
sudo userdel -r contractor
```

* `-r` → removes `/home/contractor` and mail

2. **Check remaining groups:**

```bash
getent group | grep contractor
```

* Ensures no lingering group memberships

**Explanation:**
`userdel -r` is the most common daily cleanup tool. Avoid leaving stale accounts to reduce security risk.

---

### **3️⃣ Real-life Workflow: Fixing Permissions on Shared Project Folder**

**Scenario:** Developers complain they can’t access `/srv/project`.

**Steps:**

1. **Change group ownership recursively:**

```bash
sudo chgrp -R devs /srv/project
```

* `-R` → recursive

2. **Set permissions to allow group read/write:**

```bash
sudo chmod -R 770 /srv/project
```

* `770` → owner and group full access, others none

3. **Verify ownership and permissions:**

```bash
ls -l /srv | grep project
```

**Explanation:**
`chgrp` + `chmod` is standard for troubleshooting access issues. Using `-R` ensures subfolders and files are fixed.

---

### **4️⃣ Real-life Workflow: Investigating Failed Logins**

**Scenario:** Security notices multiple failed logins for `johnd`.

**Steps:**

1. **Check failed login attempts:**

```bash
sudo faillock -u johnd
```

* Shows failed attempts

2. **Reset failed login counter after troubleshooting:**

```bash
sudo faillock -r -u johnd
```

3. **Optionally check `/var/log/btmp` using `lastb`:**

```bash
sudo lastb -f /var/log/btmp
```

4. **Force password expiration if suspicious:**

```bash
sudo passwd -e johnd
```

**Explanation:**
`faillock`, `lastb`, and `passwd -e` are key daily troubleshooting tools for account security.

---

### **5️⃣ Real-life Workflow: Group Management**

**Scenario:** Creating a new team `qa` and moving users in.

**Steps:**

1. **Add the group:**

```bash
sudo groupadd qa
```

2. **Add existing user to group:**

```bash
sudo usermod -aG qa johnd
```

3. **Rename group if needed:**

```bash
sudo groupmod -n quality_assurance qa
```

**Explanation:**
`groupadd`, `usermod -G`, and `groupmod -n` cover most daily group management cases.


### **6️⃣ Scenario: Locking and Unlocking a User Account**

**Scenario:** An employee goes on vacation, and you want to temporarily lock their account.

**Steps:**

1. **Lock the account:**

```bash
sudo usermod -L johnd
```

* `-L` → locks the account by disabling the password

2. **Verify the account is locked:**

```bash
sudo passwd -S johnd
```

* Status shows `L` for locked

3. **Unlock the account when they return:**

```bash
sudo usermod -U johnd
```

* `-U` → unlocks the account

**Explanation:**
Temporarily locking accounts avoids unauthorized access. `usermod -L/-U` is standard daily usage.

---

### **7️⃣ Scenario: Migrating a User’s Home Directory**

**Scenario:** You need to move `/home/johnd` to `/mnt/storage/johnd` for space reasons.

**Steps:**

1. **Move the files:**

```bash
sudo mv /home/johnd /mnt/storage/johnd
```

2. **Update user’s home directory in `/etc/passwd`:**

```bash
sudo usermod -d /mnt/storage/johnd -m johnd
```

* `-d` → new home directory
* `-m` → move content if not already moved

3. **Verify permissions:**

```bash
ls -ld /mnt/storage/johnd
```

* Ensure the user owns their new home directory

**Explanation:**
Changing home directories is common when managing disk space. Using `usermod -d -m` ensures the system recognizes the new path.

---

### **8️⃣ Scenario: Auditing Current User and Group Info**

**Scenario:** You’re troubleshooting access issues and need to know the current user’s identity and group memberships.

**Steps:**

1. **Check current username:**

```bash
whoami
```

2. **Check UID, GID, and groups:**

```bash
id
```

* `id -u` → UID
* `id -g` → primary GID
* `id -G` → all groups
* `id -n` → show names instead of numbers

3. **Check logged-in users for shared systems:**

```bash
w
```

**Explanation:**
Combining `whoami`, `id`, and `w` gives a complete snapshot of users, useful for troubleshooting permission errors or conflicts.

---

### **9️⃣ Scenario: Investigating Suspicious Logins**

**Scenario:** You notice multiple failed login attempts in `/var/log/secure` or `/var/log/auth.log`.

**Steps:**

1. **Check recent failed logins:**

```bash
sudo lastb -f /var/log/btmp
```

2. **Check last successful logins for the user:**

```bash
last johnd
```

3. **Reset failed login counter after investigation:**

```bash
sudo faillock -r -u johnd
```

4. **Force password reset for safety:**

```bash
sudo passwd -e johnd
```

**Explanation:**
Combining `last`, `lastb`, `faillock`, and `passwd -e` is standard security maintenance to prevent unauthorized access.

---

### **🔟 Scenario: Fixing File Ownership After Git Deployment**

**Scenario:** You deployed a project via `git clone` as root, but developers need access.

**Steps:**

1. **Change owner recursively:**

```bash
sudo chown -R johnd:devs /srv/project
```

2. **Check ownership:**

```bash
ls -l /srv | grep project
```

3. **Fix permissions for group access:**

```bash
sudo chmod -R 775 /srv/project
```

**Explanation:**
`chown` and `chmod` are daily tools to fix ownership and permissions after deployments, ensuring developers can write to the project directory without sudo.

---

### **1️⃣1️⃣ Scenario: Renaming a Group**

**Scenario:** The group `devops` is being restructured and needs a new name.

**Steps:**

1. **Rename the group:**

```bash
sudo groupmod -n platform devops
```

2. **Verify the new name exists:**

```bash
getent group platform
```

3. **Add users to the renamed group if needed:**

```bash
sudo usermod -aG platform johnd
```

**Explanation:**
`groupmod -n` is often used during team restructuring or policy changes.

---

### **1️⃣2️⃣ Scenario: Checking Password Expiration and Policies**

**Scenario:** You need to ensure user passwords are not expired and enforce security policies.

**Steps:**

1. **Check password status:**

```bash
sudo passwd -S johnd
```

2. **Force password expiration if needed:**

```bash
sudo passwd -e johnd
```

3. **Set maximum and minimum password days:**

```bash
sudo chage -M 90 -m 7 johnd
```

* `-M` → maximum days before expiry
* `-m` → minimum days before allowing change

**Explanation:**
Password management with `passwd` and `chage` ensures compliance with security policies and helps troubleshoot login issues.

### **1️⃣3️⃣ Scenario: Creating a System Account for a Service**

**Scenario:** You need a dedicated system account for `nginx` or `mysql` that does not have a login shell.

**Steps:**

1. **Create system user without home directory:**

```bash
sudo useradd -r -s /sbin/nologin -c "Nginx Service" nginx
```

* `-r` → system account (UID < 1000 usually)
* `-s` → no login shell (`/sbin/nologin`)
* `-c` → comment for clarity

2. **Verify creation:**

```bash
id nginx
```

**Explanation:**
System accounts are common for running daemons. They improve security by preventing direct logins.

---

### **1️⃣4️⃣ Scenario: Adding a User to Multiple Groups at Once**

**Scenario:** A developer needs access to `sudo`, `docker`, and `git` groups.

**Steps:**

```bash
sudo usermod -aG sudo,docker,git johnd
```

* `-aG` → append user to multiple supplementary groups

**Verify:**

```bash
id johnd
```

**Explanation:**
Common when provisioning accounts for new employees or shifting responsibilities.

---

### **1️⃣5️⃣ Scenario: Auditing All User Logins**

**Scenario:** You want to see all users who logged in over the past week.

**Steps:**

```bash
last -f /var/log/wtmp
```

* Shows login/logout times for all users

**Optional:** Check failed attempts:

```bash
sudo lastb -f /var/log/btmp
```

**Explanation:**
Useful for security audits or investigating unusual activity.

---

### **1️⃣6️⃣ Scenario: Resetting a Forgotten Password**

**Scenario:** A user forgot their password and cannot log in.

**Steps:**

1. **Reset password as root:**

```bash
sudo passwd johnd
```

2. **Force password change on next login:**

```bash
sudo passwd -e johnd
```

**Explanation:**
This is a daily task in helpdesk operations. Combining `passwd` and `-e` ensures the user must create a new password immediately.

---

### **1️⃣7️⃣ Scenario: Setting File Permissions for a Shared Directory**

**Scenario:** Developers need write access to `/srv/shared`.

**Steps:**

1. **Change group ownership:**

```bash
sudo chgrp -R devs /srv/shared
```

2. **Set permissions recursively:**

```bash
sudo chmod -R 2770 /srv/shared
```

* `2` → sets the setgid bit so new files inherit the group
* `770` → owner and group full access

3. **Verify:**

```bash
ls -ld /srv/shared
```

**Explanation:**
Using `chmod` + setgid ensures consistent permissions for new files, avoiding permission conflicts.

---

### **1️⃣8️⃣ Scenario: Investigating Disk Space Usage by Users**

**Scenario:** `/home` is filling up and you want to see usage per user.

**Steps:**

```bash
sudo du -sh /home/* | sort -h
```

* Shows human-readable sizes per directory and sorts

**Optional:** Check quota if enabled:

```bash
repquota -a
```

**Explanation:**
Daily sysadmin task to manage storage and troubleshoot space issues.

---

### **1️⃣9️⃣ Scenario: Locking Out a User After Multiple Failed Logins**

**Scenario:** Security notices repeated failed logins for `johnd`.

**Steps:**

1. **Check failed attempts:**

```bash
sudo faillock -u johnd
```

2. **Manually lock account if needed:**

```bash
sudo usermod -L johnd
```

3. **Reset failed attempts after investigation:**

```bash
sudo faillock -r -u johnd
```

**Explanation:**
Combines `faillock` and `usermod -L/-U` for proactive security management.

---

### **2️⃣0️⃣ Scenario: Changing UID or GID**

**Scenario:** A user or group was created with a conflicting UID/GID and needs correction.

**Steps:**

1. **Change user UID:**

```bash
sudo usermod -u 1050 johnd
```

2. **Change group GID:**

```bash
sudo groupmod -g 1050 devs
```

3. **Update ownership of existing files:**

```bash
sudo find / -user olduid -exec chown -h johnd {} \;
sudo find / -group oldgid -exec chgrp -h devs {} \;
```

**Explanation:**
Changing UID/GID is sometimes required in multi-system environments to avoid conflicts (e.g., NFS shares).

---

### **📊 Updated Summary of Most Common Options**

| Command    | Most Used Options                                  | Approx. Coverage |
| ---------- | -------------------------------------------------- | ---------------- |
| `useradd`  | `-m`, `-c`, `-s`, `-G`, `-r`                       | 60–70%           |
| `usermod`  | `-aG`, `-s`, `-g`, `-c`, `-L`, `-U`, `-d -m`, `-u` | 80–90%           |
| `userdel`  | `-r`                                               | 100%             |
| `groupadd` | `-g`, `-r`                                         | 50–60%           |
| `groupmod` | `-n`, `-g`                                         | 70–80%           |
| `chown`    | `-R`, `-v`                                         | 80–90%           |
| `chgrp`    | `-R`, `-v`                                         | 80–90%           |
| `chmod`    | octal, symbolic, setgid                            | 100%             |
| `passwd`   | `-e`, `-l`, `-u`                                   | 60–70%           |
| `faillock` | `-u`, `-r`                                         | 100%             |
| `id`       | `-u`, `-g`, `-G`, `-n`                             | 80–90%           |
| `lastb`    | `-f`                                               | 50–60%           |
| `whoami`   | n/a                                                | 100%             |
| `w`        | n/a                                                | 100%             |
| `last`     | n/a                                                | 100%             |
| `du`       | `-sh`                                              | 100%             |
| `find`     | `-user`, `-group`, `-exec`                         | 80–90%           |

---
