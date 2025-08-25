# 🔹 Scenarios 1

## **Scenario 1: New Employee Onboarding**

Your company hires a new developer named *Alice Johnson*. She should:

* Have a user account with a full name in the comment.
* Have her home directory stored under `/devhome` instead of the default.
* Be assigned a custom UID.
* Be part of the `developers` group and also the `projectx` group.
* Use `/bin/bash` as her shell.
* Her account should expire at the end of the year.
* Her password should be set, with a warning before expiration, a maximum validity of 90 days, a minimum of 7 days before it can be changed again, and 5 inactive days allowed after expiry.

After she starts, her manager realizes:

* She should also belong to the `gitlab` group.
* Her account needs to be locked temporarily.
* Later, her account should be unlocked.

Finally, Alice leaves the company. Her account and home directory must be removed.

---

## **Scenario 2: System Group Management**

You need to prepare system groups for different services:

* Create a system group `appsvc` with a specific GID.
* Add another group `testers`, but don’t fail if it already exists.
* Later, rename `testers` to `qa`.
* Change the GID of `qa` to another number and allow non-unique GIDs.
* Finally, delete `qa` after migration.

---

## **Scenario 3: File Permissions & Ownership**

Your team creates a shared project folder `/project/data` with files:

* `plan.txt` containing project timelines.
* `budget.txt` containing sensitive data.

Tasks:

* Ensure ownership is assigned to Alice and the `developers` group.
* Change the group ownership of the folder recursively to `projectx` and print each change.
* Make `plan.txt` readable/writable by everyone, but `budget.txt` only readable/writable by the owner and group.

---

## **Scenario 4: Password & Security Auditing**

Your security team asks you to:

* Force Alice’s password to expire immediately, so she must reset it on next login.
* Display the password status of Alice.
* Show her UID, GID, and groups by both numbers and names.
* List all currently logged-in users and what they are doing.
* Show the last login per user.
* Show Alice’s last login only.
* Check failed login attempts (both global and for Alice).
* Reset Alice’s failed login count.
* Check failed login attempts using both `faillock` and `pam_tally2`.
* Reset her counters using both tools.

---

## **Scenario 5: Root Privileges & User Switching**

As a sysadmin, you need to:

* Switch to Alice’s account to test her environment.
* Run a command as root from her account without logging out.
* Edit `/etc/hosts` safely as root.
* Check which user you are logged in as, then confirm with `whoami`.

---

## **Scenario 6: Activity Logging**

Your manager wants a report:

* Show a summary of who is currently logged in.
* Show what each logged-in user is doing.
* Show the login history of all users from `/var/log/wtmp`.
* Show failed login attempts from `/var/log/btmp`.

---

# 🔹 Example Files

`plan.txt`

```
Project Timeline:
- Phase 1: Research
- Phase 2: Development
- Phase 3: Testing
- Phase 4: Deployment
```

`budget.txt`

```
Budget Report:
- Hardware: $10,000
- Software: $5,000
- HR: $20,000
```

---

# 🔹 Answer Key

## **Scenario 1: New Employee Onboarding**

```bash
# Add Alice with custom settings
sudo useradd -c "Alice Johnson" -d /devhome/alice -m -u 1050 -G developers,projectx -g developers -s /bin/bash -e 2021-12-31 alice
sudo passwd alice
sudo passwd -w 7 -x 90 -n 7 -i 5 alice

# Modify groups
sudo usermod -G developers,projectx,gitlab alice

# Lock and unlock
sudo usermod -L alice
sudo usermod -U alice

# Delete user with home directory
sudo userdel -r alice
```

---

## **Scenario 2: System Group Management**

```bash
sudo groupadd -r -g 1500 appsvc
sudo groupadd -f testers
sudo groupmod -n qa testers
sudo groupmod -g 1600 -o qa
sudo groupdel qa
```

---

## **Scenario 3: File Permissions & Ownership**

```bash
sudo chown -R alice:developers /project/data
sudo chgrp -R -v projectx /project/data
sudo chmod 666 /project/data/plan.txt
sudo chmod 660 /project/data/budget.txt
```

---

## **Scenario 4: Password & Security Auditing**

```bash
sudo passwd -e alice
sudo passwd -S alice
id alice
id -u alice
id -g alice
id -G alice
id -un alice
id -gn alice
id -Gn alice

who
whoami
w

last
last -f /var/log/wtmp
lastlog
lastlog -u alice

lastb
lastb -f /var/log/btmp

faillock -u alice
faillock -r alice

pam_tally2 -u alice
pam_tally2 -u alice -r
```

---

## **Scenario 5: Root Privileges & User Switching**

```bash
su - alice
sudo ls /root
sudoedit /etc/hosts
who
whoami
```

---

## **Scenario 6: Activity Logging**

```bash
who
w
last -f /var/log/wtmp
lastb -f /var/log/btmp
```

---
---
---

# 🔹 Scenarios 2

---

## **Scenario 7: Service Accounts & Defaults**

The sysadmin must:

* Create a system account `dbsvc` with no home directory.
* Create a system account `cachesvc` with a custom base directory `/srv/accounts`.
* Create a system account `ftpuser` with a pre-defined encrypted password.
* Show default `useradd` settings and change the default base directory.

---

## **Scenario 8: User Modification & Renaming**

* Alice’s full name needs to be updated in her comment.
* Her home directory should move to `/srv/alicehome`.
* Change her login name to `ajohnson`.
* Change her login shell to `/bin/zsh`.
* Assign her a new UID.

---

## **Scenario 9: Special Group & Ownership Changes**

* Create a group `audit` with a duplicate GID as another group (non-unique allowed).
* Change ownership of `/project/data` files and display each change as it happens.

---

## **Scenario 10: Symbolic Permissions & Password Locking**

* Make `plan.txt` readable by group and others, but remove execute for everyone.
* Lock Bob’s password, later unlock it, and also completely remove his password.

---

# 🔹 Answer Key

## **Scenario 7: Service Accounts & Defaults**

```bash
sudo useradd -r -M dbsvc
sudo useradd -r -b /srv/accounts cachesvc
sudo useradd -r -p $(openssl passwd -1 'StrongPass!') ftpuser
sudo useradd -D
sudo useradd -D -b /home/custom
```

---

## **Scenario 8: User Modification & Renaming**

```bash
sudo usermod -c "Alice J. Johnson" alice
sudo usermod -d /srv/alicehome alice
sudo usermod -l ajohnson alice
sudo usermod -s /bin/zsh ajohnson
sudo usermod -u 2001 ajohnson
```

---

## **Scenario 9: Special Group & Ownership Changes**

```bash
sudo groupadd -g 1600 -o audit
sudo chown -v alice:audit /project/data/*
```

---

## **Scenario 10: Symbolic Permissions & Password Locking**

```bash
sudo chmod u=rw,g=r,o=r plan.txt
sudo chmod a-x plan.txt

sudo passwd -l bob
sudo passwd -u bob
sudo passwd -d bob
```

---