# USER CREATION

|INPUTS                                                             |                           |                   |                           |OUTPUT|
|-                                                                  |-                          |-                  |-                          |-|
|User Input<br>/etc/login.defs<br>/etc/skel<br>/etc/default/useradd |--><br>--><br>--><br>-->   | **User Creation** |--><br>--><br>--><br>-->   | /home/user<br>/etc/passwd<br>/etc/shadow<br>/etc/group|

---

## INPUTS

### /etc/login.defs
--> contain directives to be used in various **shadow password suite** commands ( **useradd / userdel / passwd / etc.** ).

| **Setting**           | **Description** |
| -                     | - |
| **CREATE_HOME**       | If set to **yes**, **useradd** will create the user's home directory. If **no**, it won't unless **-m** is specified|
| **HOME_MODE**         | Sets default permissions in **octal mode** for newly created home directories|
| **PASS_MAX_DAYS**     | Maximum days a password is valid before it must be changed|
| **PASS_MIN_DAYS**     | Minimum days before the user is allowed to change their password again|
| **PASS_WARN_AGE**     | Number of days before password expiry that the user is warned|
| **PASS_MIN_LENGTH**   | Minimum number of characters required in a password|
| **UID_MAX**           | Maximum value for a regular user ID ( **60000** )|
| **UID_MIN**           | Minimum value for a regular user ID ( **1000** )|
| **SYS_UID_MAX**       | Maximum value for a system user ID ( **999** )<br>system accounts are used by services & daemons.|
| **SYS_UID_MIN**       | Minimum value for a system user ID ( **100** )<br>**root user accout UID is 0**|
| **ENCRYPT_METHOD**    | Hashing method used to encrypt user passwords.( **SHA256**, **SHA512**, or **MD5** )|

> **!** only accounts created **after** installation will be following these constraints.

---

### /etc/default/useradd
--> contain default settings for the **useradd** command.

| **Setting**           | **Description** |
| -                     | - |
| **GROUP**             | Default **primary group ID (numeric)** assigned to new users|
| **HOME**              | Base directory under which home directories are created|
| **INACTIVE**          | Days after password expiry until the account is disabled. **-1** means never|
| **EXPIRE**            | Account expiration date (format: **YYYY-MM-DD**). Blank means no expiration|
| **SHELL**             | Default login shell for new users|
| **SKEL**              | Path to the **skeleton directory**, source of default user directories & config files|
| **CREATE_MAIL_SPOOL** | If **yes**, creates a mail spool file for the user (e.g., **/var/mail/username**)|

**`$ useradd -D`**\
--> view default values.

**`$ useradd -D -s /bin/bash`**\
--> change "default shell" value in **/etc/default/useradd**.

---

### /etc/skel
--> contains default files and directories that are automatically copied into a new user's home directory on creation.

---

## OUTPUTS

### /etc/passwd
--> contains essential information about all user accounts on the system, including system users and regular users.

    root:x:0:0:root:/root:/bin/bash
    bin:x:1:1:bin:/bin:/sbin/nologin
    chris:x:1001:1001:chris Baron:/home/chris:/bin/bash
    ...

| **Field Position**    | **Field Name**            | **Description** |
| -                     | -                         | - |
| 1                     | **username**              | The login name of the user |
| 2                     | **password**              | **Usually **x****, indicating that the hashed password is stored in **/etc/shadow** |
| 3                     | **user_id** (UID)         | The User ID number |
| 4                     | **group_id** (GID)        | The Primary Group ID number for the user ( **/etc/group** ) |
| 5                     | **comments**              | A field for full name, contact info, etc. |
| 6                     | **user_home_dir**         | The absolute path to the user's home directory |
| 7                     | **user_default_shell**    | The default shell program<br>If the default shell is set to **/sbin/nologin** (message+logout) or **/bin/false** (logout), the user cannot login<br>The message for **/sbin/nologin** can be customized by editing the **/etc/nologin.txt** file |

---

### /etc/shadow
--> securely stores user password information and additional account-related details.

    root:!::0:99999:7:::
    bin:*:17590:0:99999:7:::
    chris:we35X@hftusdstetegfq34536363t#@S&*jU*h6ehejewerjt9erj9:17751:0:99999:7:::
    ...

| **Field Position**    | **Field Name**            | **Description** |
| -                     | -                         | - |
| 1                     | **username**              | The login name; must match an entry in **/etc/passwd** |
| 2                     | **encrypted_password**    | The hashed password, or special values like **!**, **\*** or empty |
| 3                     | **last_changed**          | Days since Jan 1, 1970 when the password was last changed |
| 4                     | **min_age**               | Minimum days before the user is allowed to change their password again |
| 5                     | **max_age**               | Maximum days a password is valid before it must be changed |
| 6                     | **warn**                  | Number of days before password expiry that the user is warned |
| 7                     | **inactive**              | Number of days after password expires until the account is disabled<br>user will be prompted to change within this period |
| 8                     | **expire**                | Days since Jan 1, 1970 the account will be disabled |
| 9                     | **reserved**              | Reserved for future use |

> **!** shadow file is updated when a new accout is created, even before setting a password.

| **encrypted_password field**  | **Description** |
| -                             | - |
| **!**                         | Account is **locked** — disables password-based login |
| **!!**                        | Password **not set** — account was created without a password |
| **\***                        | Password **login disabled** — often used for system or service accounts |
| **!\$6$...**                  | A **locked account with a valid password hash** — hash is present, but account is locked |
| *(empty)*                     | No password set — **!!! may allow login without a password**|

---

## ACCOUNT MANAGEMENT

**`useradd / usermod / userdel`**` [option] [username]`\
--> create a new user account.\
--> modify user acount information.\
--> remove a user account.

| **Option**           | **Long Option**        | **`useradd`**                                                               | **`usermod`**                                                | **`userdel`**                                                  |
| -------------------- | ---------------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------ | -------------------------------------------------------------- |
| **-D**               | **--defaults**         | Show or set default values for **useradd**                                  |                                                              |                                                                |
|                      |                        | ↓ options compatible with **D**                                             |                                                              |                                                                |
| **-b BASEDIR**       | **--base-dir BASEDIR** | Define base directory for home dirs (**-m** create home)                    |                                                              |                                                                |
| **-e DATE**          | **--expiredate DATE**  | Set account expiration date (**YYYY-MM-DD** format)                         | Change account expiration date                               |                                                                |
| **-g GID**           | **--gid GID**          | Assign primary group by GID                                                 | Change primary group                                         |                                                                |
| **-s SHELL**         | **--shell SHELL**      | Set login shell                                                             | Change login shell                                           |                                                                |
| **-f DAYS**          | **--inactive DAYS**    | Set deactivation period after expiration (**-1** never)                     | Change deactivation period                                   |                                                                |
|                      |                        | ↑                                                                           |                                                              |                                                                |
| **-f**               | **--force**            |                                                                             |                                                              | Force deletion (remove even if logged in or files in use)      |
| **-d DIR**           | **--home DIR**         | Set home directory                                                          | Change home directory (**-m** also move files)               |                                                                |
| **-G GRP1,GRP2**     | **--groups**           | Add to supplementary groups                                                 | Replace supplementary groups (**-a** **--append** instead)   |                                                                |
| **-l NEWNAME**       | **--login NEWNAME**    |                                                                             | Change login/username                                        |                                                                |
| **-m**               | **--create-home**      | Create home directory                                                       | Move home directory when combined with **-d**                |                                                                |
| **-M**               | **--no-create-home**   | Do **not** create home directory (useful with system accounts)              |                                                              |                                                                |
| **-N**               | **--no-user-group**    | Do **not** create a group with same name as user                            |                                                              |                                                                |
| **-L**               | **--lock**             |                                                                             | Lock user account (disables password login)                  |                                                                |
| **-U**               | **--unlock**           |                                                                             | Unlock user account (remove **!** from password hash in **/etc/shadow**)|                                                                |
| **-u UID**           | **--uid UID**          | Set UID manually                                                            | Change UID (files owned by user may need manual fix)         |                                                                |
| **-c COMMENT**       | **--comment COMMENT**  | Set description/comment                                                     | Change description/comment                                   |                                                                |
| **-p PASSWD**        | **--password PASSWD**  | Set encrypted password (hash required, not plaintext)                       | Change password hash directly                                |                                                                |
| **-r**               | **--remove**           |                                                                             |                                                              | Remove user’s home directory and mail spool along with account |
| **-r**               | **--system**           | create a system user account instead                                        |                                                              |                                                                |

> **!** `$ useradd -p $(openssl passwd -6 'StrongP@ssw0rd') robert` --> It is much better to use `passwd` command than using the **-p** option.

Debian distros promte **adduser** interactive program instead of **useradd** command may even link **useradd** to run **adduser**.

On delete, any warnings due to missing files, may be due to files that were not created during account creation and can be ignored.

---

## PASSWORD MANAGEMENT

**`passwd `**`[option] [username]`\
--> modify the login password of a user.

| **Option**        |                               | **Description** |
| -                 | -                             | - |
| **-d**            | **--delete**                  | Remove the user's password (user can log in without one)|
| **-e**            | **--expire**                  | Immediately expire the password (user must change it on next login)|
| **-i**            | **--inactive**                | Days after password expires until account is disabled|
| **-l**            | **--lock**                    | Lock the account by adding **!** to the password field in **/etc/shadow**|
| **-n**            | **--minimum**                 | Minimum days before the user is allowed to change their password again|
| **-S**            | **--status**                  | Show password status information for the user :<br>**P** - password / **NP** - no password / **L** - locked<br>Last password change<br>minimum / maximum / warning / inactive<br>Hash Algo<br>etc.|
| **-u**            | **--unlock**                  | Unlock the account by removing **!** from the password field in **/etc/shadow**|
| **-w**            | **--warning** / **--warndays**| Number of days before password expiry that the user is warned|
| **-x**            | **--maximum** / **--maxdays** | Maximum days a password is valid before it must be changed|

**`chage `**`[-l] [username]`\
--> activate interactive mode to change all aging information.\
**[ l ]** --> List current password aging information for the user.

---

## GROUP MANAGEMENT

- Linux uses Discretionary Access Control ( **DAC : using user/group ownership to control file access** ) as its primary permission model.
- User can belong to **multiple groups**, but when a process is started by the user, it runs with **a single group ID**.
- When a user logs into a Linux system, their shell session and processes start with their primary group.
- Every group in Linux has a **Name** and a **Group ID (GID)**.
- When a new user is created without specifying a default group. the system, automatically creates a new group with the same name as the username, which becomes the user's primary group.

**`groupadd` / `groupmod` / `groupdel`**\
--> manage groups.

| Option    | `groupadd`| `groupmod`| `groupdel`| Description |
| -         | -         | -         | -         | - |
| **-g**    | ✔         | ✔         | 𐄂         | Specify/change the **GID**<br>duplicates are not allowed by default|
| **-o**    | ✔         | ✔         | 𐄂         | Allow duplicate GIDs (with **-g**)<br>may cause unexpected behavior|
| **-f**    | ✔         | 𐄂         | 𐄂         | Exit silently if the group already exists|
| **-r**    | ✔         | 𐄂         | 𐄂         | Create a **system group** (GID < 1000)|
| **-n**    | 𐄂         | ✔         | 𐄂         | Rename a group|

> **!** The groupdel command removes the group from all users' secondary group memberships.\
> **!** However, if the group is a primary group for any user, the command will fail with an error and the group itself will not be deleted.\
> **!** but the group will still be removed from the secondary group lists because of the attempt.

### /etc/group
--> stores group information.

| Field                     | Description |
| -                         | - |
| **group_name**            | The **name** of the group|
| **password_placeholder**  | Usually **x**, indicating a group password (now deprecated)<br>***!* check /etc/gshadow file to confirm that a password is set**|
| **GID**                   | The **Group ID**, unique numeric identifier for the group|
| **user_list**             | A **comma-separated list** of users who are **members of this group**<br>! Users who have this group as their primary group are not shown in the this field|

```
myGroup:!::
        ↑
no password is set
```

> **!** *Group passwords allow non members access to the group, which is a bad security practice.*\
> **!** *instead of setting a password, use group memberships to allow access.*

---
---

**`getent `**`[database] [key]`

- supported databases :
    + aliases
    + group
    + gshadow
    + hosts
    + passwd
    + services
    + shadow
    + etc.

`$ getent passwd Adam` --> show Adam record from /etc/passwd file.

    Adam:x:1002:1002::/home/Adam:/bin/bash
                 ↑
            primary group

---
---

# ENVIRONMENTAL SETTINGS

- BASH shell uses **Environment Variables** to store information about the session.

**`echo `**`$[variable]+++`\
**`printenv `**`[option] [variable+++]`\
--> print environmental variables.

| Variable              | Description |
| -                     | - |
| `HOME`                | Path to the current user's home directory |
| `USER` / `LOGNAME`    | Current user's username |
| `UID`                 | User ID of the current user |
| `GID`                 | Group ID of the current user |
| `SHELL`               | Absolute path to the user's **default shell** |
| `PATH`                | Colon-separated list of directories where the shell looks for **commands** |
| `PWD`                 | Current working directory |
| `PS1`                 | Configures the **primary prompt string** in interactive shells |
| `EDITOR`              | Default text editor (for CLI tools like `crontab`, `git`, `visudo`, etc.) |
| `LANG`                | Current language and locale setting (ie. `en_US.UTF-8`)|
| `HOSTNAME`            | The system's hostname |
| `XDG_SESSION_TYPE`    | Type of current session (`tty`, `x11`, `wayland`)|
| `HISTFILE`            | Path to the file where command history is saved (ie. `~/.bash_history`)|
| `HISTCONTROL`         | Controls how commands are **saved in history** (ie. ignores duplicates / ignore commands starting with a space) |
| `HISTSIZE`            | Sets the **maximum number of commands** stored in memory for the history list |

> **!** environment variables are stored within **environment files / startup files**.

| File                                      | Description |
| -                                         | - |
| **~/.bash_profile**                       | For User's login shell<br>**highest priority**|
| **~/.bash_login**                         | For User's login shell<br>! may not exist on some systems|
| **~/.profile**                            | For User's login shell<br>**lowest priority**<br>support other POSIX login shell like **sh** or **dash**|
| **~/.bashrc**                             | For User's **non-login** shells ( ie. GUI terminal ) |
| **/etc/profile**                          | System-wide version of **.profile**|
| **/etc/bashrc**<br>**/etc/bash.bashrc**   | System-wide version of **.bashrc** |
| **/etc/environment**                      | System-wide environment variables |

> **!** instead of altering the **/etc/profile** file, create a custom .sh file and\
> **!** place it in **/etc/profile.d/** to be ran on shell startup like **.bashrc**.

Bash can be started in three common ways:
1. **Login Shell** :
    - Bash runs as the default login shell when logging into a virtual console.
    - tty7 is usually reserved for the GUI in many Linux systems.
2. **Interactive Subshell** :
    - Terminal Emulator in GUI.
3. **Non-Interactive Shell** :
    - When a shell script is executed, Bash runs in non-interactive mode.

---

# QUERYING USERS

**`whoami`**\
--> print current user name.

---

**`who`**\
--> print current system users, their terminals, login date-time, remote ip.

---

**`w`**\
--> show who is logged in and what they are doing.

- system time
- system up time
- no of current users
- CPU load averages for last 1, 5 and 15 mins.
- current users,
    * USER - username
    * TTY - user tty
    * LOGIN@ - login time
    * IDLE - how long since last interaction
    * JCPU - total CPU time used by account
    * PCPU - CPU time used currently by the account.
    * WHAT - what command the account in currently running.

> **!** **w** uses information from **/var/run/utmp** and files in **/proc/**.

---

**`id `**`[option] [username+++]`\
--> show the users UID+uname , GID+gname, secondary groups(GID+gname).

| Option    |               | Description |
| -         | -             | - |
| **-g**    | **--group**   | User's **Primary group ID (GID)** |
| **-G**    | **--groups**  | User's group memberships ( all including primary ) |
| **-u**    | **--user**    | User's **user ID (UID)** |
| **-n**    | **--name**    | Used with **-g**, **-G**, **-u** to display the **name instead of the numeric ID** |

---

**`last `**\
--> display list of accounts and last login / logout times.

> **!** **last** uses information from **/var/log/wtmp**.
> **!** **/var/log/wtmp** gets rotated automatically with a cronjob.
> **!**  use old wtmp files --> `$ last -f /var/log/wtmp.1`

---

# DISK SPACE USAGE

limit user / group space utilization and number of files can be set with **quotas**.

**`$ mount | grep /dev/sdb1`**\
--> check if quotas are enabled.

    /dev/sdb1 on /home/Adam/quotatest type ext4 (rw,realtime,seclabel,quota,usrquota,grpquota,data=ordered)

**`$ vim /etc/fstab`**\
--> edit **/etc/fstab** to add the appropriate quota options ( **usrquota** / **grpquota** ) for the target file system.

    /dev/sdb1 /home/Adam/quotatest ext4    defaults,usrquota,grpquota 0 0

**`$ umount /dev/sdb1`**\
**`$ mount -a`**\
--> remount the file system to apply the updated mount options with quota support.

**`$ quotacheck -cug /home/user1/quotatest`**\
--> create the quota database files (**aquota.user** / **aquota.group**).

| Option    | Description |
| -         | - |
| **-c**    | Create quota files |
| **-u**    | For user quotas (**aquota.user**) |
| **-g**    | For group quotas (**aquota.group**) |
| **-a**    | Apply to all quota-enabled filesystems |

**`$ edquota -u Adam`**\
--> Set user / group quota limits.
   
    ( open in vim editor unless $EDITOR is set )

    Disk quotas for user Adam (uid 1004):
    filesystem      blocks      soft        hard        inodes      soft        hard
    /dev/sdb1       212         4096        6144        2           0           0
    ...

> **!** **Blocks** and **inodes** reflect current status and can't be persistently modified, but **soft** and **hard** limits (**0** for unlimited) can be set for both.

**`$ edquota -t`**\
--> Set grace periods for **soft** limits.

    ( open in vim editor unless $EDITOR is set )

    grace period before enforcng soft limits for users:
    time units may be : days, hours, minutes or seconds

    filesystem      block grace period      inode grace period
    /dev/sdb1       7 days                  7 days

**`$ sudo quotaoff -agu`**\
--> turn off quotas for (-a) all filesystems for (-g) groups and (-u) users.

**`$ sudo quotaon`**\
--> turn on quotas.

**`$ repquota -a`**\
--> quota report
