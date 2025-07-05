# USER ADMINISTRATION

## ADDING A USER

> $ useradd


resourses used in user creation,

    User Input             →|                |→   /home/userid
    /etc/login.defs        →|      User      |→   /etc/passwd
    /etc/skel              →|    Creation    |→   /etc/shadow
    /etc/default/useradd   →|                |→   /etc/group

### /etc/login.defs

contain directives to be used in various **shadow password suite** commands ( *useradd / userdel / passwd / etc.* ).

    ...
    CREATE_HOME  no
    HOME_MODE   0700
    PASS_MAX_DAYS   99999
    PASS_MIN_DAYS   0
    PASS_WARN_AGE   7       < days a warning is issued before password expires >
    PASS_MIN_LENGTH 5
    UID_MAX     60000
    UID_MIN     1000
    SYS_UID_MAX 999
    SYS_UID_MIN 201
    ENCRYPT_METHOD  SHA256  < hashing method to hash account password>
    ...

+ each user account has a unique UID, each system account has a unique SYS_UID.
+ system accounts are used by services / daemons.
+ only accounts created **after** installation will be following these constraints.(ie. **root user accout is 0**).

### /etc/default/useradd


    GROUP=100
    HOME=/HOME          < if not set a home will not be created >
    INACTIVE=-1         < number of days before password expired and not changed to deactivate account >
    EXPIRE=
    SHELL=/bin/bash
    SKEL=/etc/skel      < skeleton directory >
    CREATE_MAIL_SPOOL=yes


+ above result can be viewed with `$ cat /etc/default/useradd` or `$ useradd -D` commands.

### /etc/skel

+ contain **environment files** to be coppied to the user home directory when creating a new user home directory.

>***files that are effected after user creation is discussed below !***

### /etc/passwd

+ each accounts info is sotred in a single line.

.

    ...
    root:x:0:0:root:/root:/bin/bash
    bin:x:1:1:bin:/bin:/sbin/nologin
    ...
    chris:x:1001:1001:chris Baron:/home/chris:/bin/bash
    ...

    (username):(password):(user_id):(group_id):(comments / user_full_name):(user_home_dir):(user_default_shell)

> if default shell is set to **/sbin/nologin** or **/bin/false**, user cannot login to the system interactively.\
> **/sbin/nologin** display a message and log the user out before reaching a command prompt.\
> the message can be modified with **/etc/nologin.txt**.\
> **/bin/false** just logs the user out before reaching a command prompt.\
> password are now stored at **/etc/shadow** instead of the **/etc/passwd** file as indicated by the **x**.

### /etc/shadow

+ updated when accout is created, even before setting a password.

.

    root:!::0:99999:7:::
    bin:*:17590:0:99999:7:::
    ...
    chris:we35X@hftusdstetegfq34536363t#@S&*jU*h6ehejewerjt9erj9:17751:0:99999:7:::
    ...

    (username):
    (password_hash):
    (last_password_change_in_unix_time_days_format):
    (days_to_wait_after_password_change_to_change_again):
    (password_expiration):
    (days_warning_is_issued_before_expiration):
    (days_after_expiration_to_deactivate_account):
    (accout_expiration_unix_time_days_format):
    (special_flag_currently_not_used)

> after password expiration, user has a grace period (field 7) to login, but must change the password immediately.\
> after account expiration no such period is availabe.

> **!** or **!!** indicates no password is set.\
> **\*** indicates account cannot use a password to login.\
> **!(password_hash)** indicates the account is locked.

### User Account Creation Process

1. review distro specific configurations

    * grep CREATE_HOME /etc/login.defs < check if home is created by default
    * useradd -D | grep SHELL < check default shell for new account
    * etc...

2. create user account

    * sudo useradd Adam

.

### useradd
add a new user account to the system

    -c  --comment           Comment field
    -d  --home
        --home-dir          User home dir
    -D  --defaults          set / display /etc/default/useradd directives
    -e  --expiredate        account expiration (yyyy-mm-dd)
    -f  --inactive          time to deactivate account after password has expired
    -g  --gid               default group membership
    -G  --groups            additional groups
    -m  --create-home       create home if not exist
    -M  --no-create-home    do not create home
    -s  --shell             default shell
    -u  --uid               account uid
    -r  --system            account type 'system' instead of 'user'

> some distros promte **adduser** program instead of **useradd** \
> and may even symbolic link **useradd** to run **adduser**.

`$ sudo useradd -D -s /bin/bash`    < change default shell without using a text editor >

## MAINTAINING PASSWORDS

`$ passwd`  < change own account password >\
`$ sudo passwd Adam`    < change Adam's password >

    -d  --delete        remove password
    -e  --expire        set expire date
    -i  --inactive      set time to deactivate account after password expire
    -l  --lock          add '!' to shadow file, preventing login
    -n  --minimum       number of days untill password may change again
    -S  --status        account password status
    -u  --unlock        unlock acount, remove '!' from shadow file
    -w  --warning
        --warndays      number of days before warning is issued to password expiration.
    -x  --maximum
        --maxdays       number of days to password expiration

> -S option shows\
> password state (P - password / NP - no password / L - locked)
> last password change\
> minimum / maximum / warning / inactive\
> additional info in pranthesis ie - ( hash algo used )

`$ chage -l Adam`    < view account password status >\
`$ sudo chage Adam`     < change password settings>

## MANAGE ACCOUNTS

`$ sudo usermod -L Adam`

    -c  --comment
    -d  --home          set new user home. add `-m` to move current files to new dir.
    -e  --expiredate    mod accounts expire date.
    -f  --inactive      mod days to deactivate account after password expiration.
    -g  --gid           mod default group.
    -G  --groups        mod additional groups. add `-a` to avoid removing old groups.
    -l  --login         mod username. does not change home dir.
    -L  --lock          lock account, using shadow file.
    -s  --shell         mod account shell.
    -u  --uid           mod account uid.
    -U  --unlock        unlock account using shadow file.

`$ sudo userdel -r Adam`

> any warnings due to missing files, may be due to files that were not created during account creation and can be ignored.


## MANAGE GROUPS

- Part of linux DAC (Discretionary Access Control).
- Although a user can have multiple groups it's processes can have only one group at a time.
- Default group is users current group when first logging in.
- Groups are identified by it's name and GID
- If default group is not set, a new group with **username** is created at acount creation.

`$ getent passwd Adam`  < to find the default group >\

    Adam:x:1002:1002::/home/Adam:/bin/bash < third column show the default group >

> **/etc/group** file contains,\
> \
> Group Name\
> Group Password, 'x' indicates password stored in **/etc/gshadow** file\
> GID\
> Group Members. seperated by commas.

**! even if password field is marked with an 'x', /etc/gshadow must be checked to verify if a password is set.**

`$ sudo groupadd -g 1042 myGroup` < create new group myGroup>

! debian prefers **addgroup** instead of **groupadd**

`$ sudo getent gshadow myGroup` < get myGroup password info >

    myGroup:!::     < no password is set, indicated by '!'>

**!** *Group passwords allow non members of the group access to the group, which is a bad security practice*\
**!** *instead not setting a password and using group memberships to allow access is the recomended method.*

`$ sudo usermod -aG myGroup Adam`   < add user Adam to group myGroup >

`$ sudo groupmod -g 1114 myGroup`   < change GID of mygroup to 1114 >

*In case of duplicate GID an error will be displayed, and not change the GID*

`$ sudo groupdel myGroup`       < delete group myGroup >

*After deletion group will be removed from user accounts as well*

`$ sudo find / -gid 1114 2>/dev/null`   < find any files with access settings for group 1114 >

# ENVIRONMENTAL SETTINGS

- BASH shell uses **Environment Variables** to store information about the session.

`$ printenv`    < print environmental variables >

    HISTCONTROL     set how commands are saved in history.
    HISTSIZE        max number of commands in history.
    PATH            dirs which shell look for commands.
    PS1             configure shells primary prompt.
    SHELL           set shells absolute dir reference.
    USER            contain current user name.

`$ echo $HISTSIZE`  < echo history limit>

- environment variables are stored within **environment files aka startup files**.

- BASH can be opened in 3 ways,
    * default login shell, at a tty{1-6} terminal. ( 7th is the GUI )
    * subshell , terminal emulators in GUI.
    * non interactive shell, when running shell scripts.

- 4 environment files in $HOME,
    * .bash_profile
    * .bash_login
    * .profile
    * .bashrc

**!** not all 4 of these files are found on every distribution.\
**!** **bashrc** runs everytime a non interactive shell is started.

- global environment files,
    * /etc/profile
    * /etc/profile.d < directory >
    * /etc/bashrc or /etc/bash.bashrc < depends on the distro >

> instead of altering the profile file, create a custom .sh file and place it in profile.d to be run on shell startup using the profile file.

# QUERYING USERS

`$ whoami`      < print current user name >.

`$ who`         < print current system users, their terminals, login date-time, remote ip >

`$ w`

output of the **w** command -

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

**w** pulls info from **/var/run/utmp** file and files in **/proc/**.

`$ id`      < UID(uname) , GID(gname), otherGIDs(names) >

    -g  --group     current GID
    -G  --groups    all account group memberships
    -u  --user      account UID
    -n  --name      combine with -g / -G /-u to filter just the name.

`$ last`    < display list of accounts and last login / logout times >

* pulls from **/var/log/wtmp**
* **/var/log/wtmp** gets rotated automatically with a cronjob.
* to access old wtmp files > `$ last -f /var/log/wtmp.1`

# DISK SPACE USAGE

> set limits to user / group space utilization and number of files.

1. mod /etc/fstab to enable quota support.
2. remount the file system.
3. create quota files.
4. establish user / group quotas and grace periods.

1 > add **usrquota** and/or **grpquota** to mount options

    /dev/sdb1 /home/Adam/quotatest ext4    defaults,usrquota,grpquota 0 0

2 > `$ umount /dev/sdb1` > `$ mount -a` (mount all in fstab)

check if quotas are enabled,

    $ mount | grep /dev/sdb1
    /dev/sdb1 on /home/Adam/quotatest type ext4 (rw,realtime,seclabel,quota,usrquota,grpquota,data=ordered)

3 >  `$ quotacheck -cug /home/user1/quotatest`

-c create files\
-u for user ( **aquota.user** )\
-g for group ( **aquota.group** )
-a for all quota enabled file systems.

4 > `$ edquota -u Adam`

open in vim editor unless $EDITOR is set,

    Disk quotas for user Adam (uid 1004):
    filesystem      blocks      soft        hard        inodes      soft        hard
    /dev/sdb1       212         4096        6144        2           0           0
    ...

! **blocks** and **inodes** cannot be modified presistently since they are just current status.\
! **soft** and **hard** limits can be set for both. *0 is unlimited*.

grace periods must be set for **soft** limits.

`$ edquota -t`

open in vim editor unless $EDITOR is set,

    grace period before enforcng soft limits for users:
    time units may be : days, hours, minutes or seconds

    filesystem      block grace period      inode grace period
    /dev/sdb1       7 days                  7 days

`$ sudo quotaoff -agu`  < turn off quotas for (-a) all filesystems for (-g) groups and (-u) users >\
`$ sudo quotaon`

`$ quota - Adam`

    Disk quotas for user Adam (uid 1004):
    filesystem      blocks      soft        grace       hard        inodes      soft        hard        grace
    /dev/sdb1       212         4096                    6144        2           0           0

`$ repquota -a` < check all filesystem quotas >