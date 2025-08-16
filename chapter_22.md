# USER ISSUES

## ACCOUNT ACCESS

### LOCAL ACCOUNTS

**Local accounts** refer to user profiles that authenticate through interfaces directly connected to the system (ie. console or local terminal access)

**Consider the following when troubleshooting :**

* Was the account recently created?
* What exact username is being entered during login?
* Has the user successfully logged in before?
* Is the user attempting to log in via the terminal or through the graphical user interface (GUI)?

> **!** everything is case sensitive in linux.

---

**PASSWORD**

`$ getent passwd Adam`
```
Adam:x:1001:1001:Adam Smith:/home/Adam:/bin/bash
```
`$ getent shadow Adam`
```
Adam:$6$abcd1234$xyz.....:19345:0:99999:7:::
```
---

**ACCOUNT ACCESS**

`$ lastlog -u Adam`\
--> search through **/var/log/lastlog** for the last time user logged in.
```
Username         Port     From             Latest
Adam             pts/0    192.168.1.100     Mon Aug 12 14:22:01 +0000 2025
```
```
Username         Port     From             Latest
Adam                                      **Never logged in**
```

`$ last -f /var/log/wtmp -f /var/log/wtmp.* | grep Adam`\
--> search through **/var/log/wtmp** files for past user logins.
```
Adam    pts/0        192.168.1.100    Mon Aug 12 14:22 - 14:45  (00:23)
Adam    tty1                         Sat Aug 10 08:00 - 08:20  (00:20)
```

`$ lastb -f /var/log/btmp -f /var/log/btmp.* | grep Adam`\
--> search through **/var/log/btmp** files for login user fails.
```
Adam     tty1                          Mon Aug 12 14:55 - 14:55  (00:00)
Adam     pts/1     192.168.1.100       Sun Aug 11 09:12 - 09:12  (00:00)
```

---

**PRIVILEDGE ESACALTION**

`$ EDITOR=vim visudo`\
--> pruce through **/etc/sudoers** file for any mention of user or user groups.

```
# User alias specification (optional)
User_Alias ADMINS = alice, bob

# Group alias specification (optional)
# (Defines a group of groups or users)
# Group_Alias SYSOPS = admin, sysadmin

# Allow root to do anything
root    ALL=(ALL:ALL) ALL

# Allow members of group sudo to run any command as any user or group
%sudo   ALL=(ALL:ALL) ALL

# Allow members of group admin to run any command as any user (default group)
%admin  ALL=(ALL) ALL

# Allow specific users in ADMINS alias to run commands without password prompt
ADMINS  ALL=(ALL) NOPASSWD: ALL

# Allow user charlie to run a specific command without password
charlie ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart apache2

# Allow user dave to run only shutdown and reboot commands
dave    ALL=(ALL) /sbin/shutdown, /sbin/reboot

# Defaults to improve security and logging
Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

# End of file
```

`$ id Adam`\
--> list user info, inc. user groups.
```
uid=1001(Adam) gid=1001(Adam) groups=1001(Adam),27(sudo),1002(developers)
```

---

**GUI ISSUES**

> **!** if the user can successfully log in with the terminal, investigate for GUI issues.

`$ systemctl status graphical.target`\
--> check if graphical.target is active.

```● graphical.target - Graphical Interface
   Loaded: loaded (/usr/lib/systemd/system/graphical.target; enabled; vendor preset: enabled)
   Active: active since Thu 2025-08-14 08:30:22 UTC; 2h 15min ago
     Docs: man:systemd.special(7)
           https://www.freedesktop.org/wiki/Software/systemd/TheTargets/
  Drop-In: /etc/systemd/system/graphical.target.wants/
           └─gdm.service
  CGroup: /system.slice/graphical.target
          ├─gdm.service
          │ ├─1234 /usr/sbin/gdm
          │ └─1256 /usr/libexec/gdm-session-worker
          └─other graphical services...

Aug 14 08:30:22 hostname systemd[1]: Reached target Graphical Interface.
Aug 14 08:30:22 hostname systemd[1]: Started GDM Display Manager.
```

`$ sudo systemctl status multi-user.target`\
--> in case of multiple users in the same system, check if multi-user.target is active.

---

**TERMINAL ISSUES**

> **!** if the user can log into **text-based terminal** but not into a **virtual terminal** or  a **graphical desktop**.

`$ ls -l /dev/tty?`

```
crw-rw-rw- 1 root tty 5, 0 Aug 14 10:15 /dev/tty0
-rw--w---- 1 root tty 4, 0 Aug 14 10:15 /dev/tty1
crw--w---- 1 root tty 4, 1 Aug 14 10:15 /dev/tty2
crw--w---- 1 root tty 4, 2 Aug 14 10:15 /dev/tty3
crw--w---- 1 root tty 4, 3 Aug 14 10:15 /dev/tty4
crw--w---- 1 root tty 4, 4 Aug 14 10:15 /dev/tty5
crw--w---- 1 root tty 4, 5 Aug 14 10:15 /dev/tty6
crw--w---- 1 root tty 4, 6 Aug 14 10:15 /dev/tty7
crw--w---- 1 root tty 4, 7 Aug 14 10:15 /dev/tty8
crw--w---- 1 root tty 4, 8 Aug 14 10:15 /dev/tty9
```

if the starting char is a ( **-** : Regular File ) instead of the ( **c** : Character File ) in the record, the file is **corrupted**.

`$ mknod /dev/tty1 c 4 1`\
--> rebuild the corrupted device file.

> **!** if the user can't even login with another text-based terminal.

`$ systemctl status getty.target`\
--> check if getty services are **active**

> **!** **getty.target** is a systemd target used to group all getty@.service units, It represents the login terminals.

```
● getty.target - Login Prompts
   Loaded: loaded (/usr/lib/systemd/system/getty.target; static)
   Active: active since Thu 2025-08-14 08:30:22 UTC; 2h 25min ago
     Docs: man:systemd.special(7)

Aug 14 08:30:22 hostname systemd[1]: Reached target Login Prompts.
```

`grep -E '^\s*-[^\#]*\bAdam\b' /etc/security/access.conf`\
--> check **/etc/security/access.conf** for any blocks.

```
-:Adam:ALL
-:ALL EXCEPT Adam:tty1
```
1. Adam is completely denied login access to the system.
2. Every user except Adam is denied access to tty1

> **!** Rules in **/etc/security/access.conf** are evaluated **top to bottom**, and the **first match wins**.

---

**LOCKED ACCOUNT**

`$ sudo passwd -S Adam`\
--> Shows the **password status** of a local user, using **/etc/shadow**.
```
Adam L 01/02/2019 0 99999 7 -1
```
`$ sudo getent shadow Adam`\
--> retrieves password and account aging information.
```
Adam:!$6$...:17652:0:999999:7:::
```

> **!** the **( L )** in passwd outpout indicates a **Locked account** or an **account with no password**.\
> **!** the **( ! )** in front of the **password hash** indicate that the account is actually locked.

`$ usermod -U Adam`\
`$ passwd -u Adam`\
--> unlock the user

---

**EXPIRED ACCOUNT or PASSWORD**

`$ chage -l Adam`\
--> list users password aging information.
```
Last password change                                    : Aug 01, 2025
Password expires                                        : Never
Password inactive                                       : Never
Account expires                                         : Never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7
```

---

### REMOTE ACCOUNTS

**1. Basic Connectivity Checks**

* Verify that the system is reachable over the network (ie. using `ping`, `telnet`, or `ssh`).
* Ensure that no firewall rules (local or network) are blocking access to the SSH port (default: 22).

**2. SSH Service Validation**

* Confirm that the **OpenSSH server** (`sshd`) is running on the remote system.
* Review the SSH daemon configuration (`/etc/ssh/sshd_config`) for:
    + `AllowUsers` or `AllowGroups` restrictions
    + `PasswordAuthentication` setting (whether password logins are allowed)

**3. SSH Client Configurations**

* Check the user's personal SSH client configuration in `~/.ssh/config`.
* Review the system-wide SSH client configuration in `/etc/ssh/ssh_config`.

**4. GUI Over SSH (X11 Forwarding)**

* If X11 applications are expected to run over SSH, ensure the `ForwardX11` option is enabled in the SSH client configuration.

**5. Authentication Troubleshooting**

* **PAM (Pluggable Authentication Modules):**

  * Review PAM configuration files (ie. `/etc/pam.d/`) for any restrictive rules.

* **Account Lockout:**

  * Use tools like `pam_tally2` or `faillock` to check if the user account is locked due to failed login attempts.

* **External Authentication Systems:**

  * Verify if there are issues with integrated services such as **Kerberos** or **LDAP**.

* **Security Modules:**

  * Confirm whether **SELinux** or **AppArmor** are blocking user actions. `ausearch -m avc` / `sealert -a /var/log/audit/audit.log`

* **Log Analysis:**

  * Check for authentication errors in:

    * `/var/log/auth.log` (Debian-based systems)
    * `/var/log/secure` (Red Hat-based systems)

---

## FILE PERMISSIONS

`$ ls -l`\
--> to view file / directory permissions.

> **!** check user groups - `$ id Adam`.

**ADVANCED PERMISSIONS**

| **Feature & Purpose**                                                     | **Command(s)**                                                                                                                                                                                                                                                                        |
| ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ACL (Access Control Lists):** View and modify extended file permissions | - View ACLs: <br>`getfacl file1`<br>- Set ACLs: <br>`setfacl -m u:username:rwx file1`<br>- Remove ACLs: <br>`setfacl -x u:username file1`                                                                                                                                 |
| **SELinux:** Manage Mandatory Access Control (MAC) policies               | - Check if enabled: <br>`sestatus`<br>- View file context: <br>`ls -Z file1`<br>- Change file context: <br>`chcon -t httpd_sys_content_t file1`<br>- List boolean policies: <br>`getsebool -a`<br>- Enable/disable a boolean: <br>`setsebool -P httpd_can_network_connect on` |
| **AppArmor:** Apply program-level access control profiles                 | - Check AppArmor status: <br>`aa-status`<br>- Enforce a profile: <br>`aa-enforce /etc/apparmor.d/usr.sbin.nginx`<br>- Set profile to complain mode: <br>`aa-complain /etc/apparmor.d/usr.sbin.nginx`<br>- Disable a profile: <br>`aa-disable /etc/apparmor.d/usr.sbin.nginx`          |

---

**FILE CREATION**

if a user is denied creating a file,

| **Check**                                | **Command(s)** / Notes                                                                                                                                                              |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Are quotas enforced?**                 | - Check `/etc/fstab` for `usrquota` / `grpquota` <br> - View quotas: `quota` <br> - Disable quotas: `quotaoff`                                                                       |
| **Has the disk run out of space?**       | - View disk usage: `df -h`                                                                                                                                                          |
| **Has the partition run out of inodes?** | - View inode usage: `df -i` <br> - Find dirs with many small files <br> - Delete unnecessary files <br> - Inodes fixed at filesystem creation <br> - Set custom inodes: `mke2fs -i` |
| **User umask settings**                  | - View umask: `umask`                                                                                                                                                               |

> **!** if the file cannot be deleted check its immutable attribute with `$ lsattr file1`.\
> **!** if the immutable bit ( i ) is set change it with `$ chattr -i file1`.

> **!** check security module file permission policies ( SELinux / AppArmor ).

---

## SHELL ISSUES

Check default shell ---> `$ getent passwd Adam`
```
Adam:x:1001:1001:Adam Smith:/home/Adam:/bin/bash
```

- if user cannot login check if default shell is **/sbin/nologin** or **/sbin/false**.

- review **users environment variables** file, the **~/.profile** file.

- check global environment variables as well `$ printenv`.

- Subshells do not automatically inherit shell variables, so `export` must be used to promote the variable to an environment variable.

```
$ EDITOR='vim'
$ echo $EDITOR
vim
$ bash              # initiate sub shell
$ echo $EDITOR 
$
```
```
$ export EDITOR='vim'       # set environment variable
```
