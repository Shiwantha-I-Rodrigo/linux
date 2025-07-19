# USER ISSUES

## ACCOUNT ACCESS

### LOCAL

- local accounts : using directly connected to interfaces to the system.
- consider,
    + is this a newly created account ?
    + what is the username being entered ?
    + has the user ever logged in ?
    + is the user using terminal or GUI ?


**password**

`$ sudo getent passwd Adam`\
`$ sudo getent shadow Adam`

> everything is case sensitive in linux.

**account access**

`$ sudo lastlog -u Adam`\
< search through **/var/log/lastlog** for the last time user logged in >

`$ sudo last -f /var/log/wtmp -f /var/log/wtmp.* | grep Adam`\
< search through **/var/log/wtmp** files for past user logins >

`$ sudo lastb -f /var/log/btmp -f /var/log/btmp.* | grep Adam`\
< search through **/var/log/btmp** files for login user fails >

**privilege elavation**

`$ EDITOR=vim visudo`
< pruce through **/etc/sudoers** file for any mention of user or user groups >

%admin ALL=(ALL) ALL
< members of **admin** group can gain root priviledges >

%sudo ALL=(ALL:ALL) ALL
< members of **sudo** group can execute any command >

`$ id Adam`
< list user info, inc. user groups >

**GUI issues**

> if the user can successfully log in with the terminal, investigate for GUI issues.

`$ sudo systemctl status graphical.target`\
< check if graphical.target is active >

`$ sudo systemctl status multi-user.target`\
< in case of multiple users in the same system, check if multi-user.target is active >

**terminal issues**

> if the user can log into **text-based terminal** but not into a **virtual terminal** or  a **graphical desktop**. 

`$ ls -l /dev/tty?`

    crw--w---- 1 root tty 4, 0 Jan 4 09:38 /dev/tty0
    ...

if the starting char is a (**-**) instead of the **c** in the record, the file is corrupted.\
if the file is corrupted use **mknod** to rebuild it.

> if the user can't login to another text-based terminal.

`$ sudo systemctl status getty.target`\
< check if getty services are active >

> check **/etc/security/access.conf** for any blocks.

**additional isuues**

> is the account locked.

`$ sudo passwd -S Adam`

    Adam L 01/02/2019 0 99999 7 -1

`$ sudo getent shadow Adam`

    Adam:!$6$...:17652:0:999999:7:::

> the **L** in passwd outpout indicates a **Locked account** or an **account with no password**.\
> the **!** in front of the **password hash** indicate that the account is actually locked.

`# usermod -U Adam` / `# passwd -u Adam`\
< unlock user Adam >

> is the account or password has expired

`$ sudo chage -l Adam`

    ...
    Account expires     : jan 01 2022
    ...
    password expire     : dec 25 2021
    ...

### REMOTE

- check connectivity to system.
- check firewall rules.
- is the OpenSSH server running.
- check sshd_config file. ( **AllowUsers / AllowGroups / PasswordAuthentication** )
- check **~/.ssh/config**.
- check **/etc/ssh/ssh_config**.
- if the X11 GUI is transfered over the network, is the **ForwardX11** directive set.
- Authentication Issues
    + check **PAM** configs.
    + use **pam_tally2** and **faillock** to check if the user account is locked.
    + check external authentication issues ( **kerberos / LDAP** ).
    + check linux security modules ( **SELinux / AppArmor** ).
    + check security policy violations at **audit.log** or **messages.log**.
        * AppArmor : `$ ausearch -m avc`.
        * SELinux : `$ sealert -a /var/log/audit/audit.log`.
    + check **/var/log/auth.log** or **/var/log/secure**.

## FILE PERMISSIONS

- use `$ ls -l` to view file / directory permissions.
- use `$ id Adam` to view user groups.

**Directory Permissions**

- r : Allow user to view dir content.
- w : Allow user to create, move (rename), modify attributes and delete files.
- x : Allow user to change their working directory to this dir.
    + **given this attribute is set for ALL PARENT DIR's as well**.

> the sticky bit help prevent users deleting others files in shared directories.


**Advanced Permissions**

- ACL
    + use `$ getfacl file1.txt` command to view ACL permissions for files.
    + use `$ setfacl` to change permissions.
- SELinux
    + check if SELinux is active `$ sestatus`.
    + check context permissions `$ ls -Z file1.txt`.
    + use `$ chcon` to change context.
    + use `$ getsebool` to list policies.
    + use `$ setsebool` to activate / deactivate policies.
- AppArmor
    + check if AppArmor is running `$ aa-status`.
    + use `aa-enforce` / `aa-complain` / `aa-disable` to change profile settings.

**File Creation**

if a user is denied creating a file,

- are Quotas enforced ?
    + check **/etc/fstab** for **usrquota** or **grpquota**.
    + check users / groups quota status with `$ quota` utility.
    + use `$ quotaoff` to diasable quotas.

- has the disk run out of space ?
    + use `$ df` to view disk usage.

- has the partition run out of inodes.
    + very unusual.
    + use `$df -i` to view inode usage.
    + check for directories with large amount of tiny files.
    + remove unnessasary files.
    + inode number cannot be changed after file system creation.
    + set custom inode numbers at file system creation `mke2fs -i`.

- user mask settings.
    + use `$ umask` to view users mask settings.

> if the file cannot be deleted check its immutable attribute with `$ lsattr file1.txt`.\
> if the immutable bit ( i ) is set change it with `$ chattr -i file1.txt`.

- check security module file permission policies ( SELinux / AppArmor ).
    + check security policy violations at **audit.log** or **messages.log**.

## SHELL ISSUES

- use `$ getent passwd Adam` to check default shell.

- if user cannot login check if default shell is **/sbin/nologin** or **/sbin/false**.

- review **users environment variables** file, the **~/.profile** file.

- check global environment variables as well `$ printenv`.

- sub shells may not inherit environmental variables, **EXPORT** such variables.

.

    $ EDITOR='vim'      < set variable >
    $ echo $EDITOR      < get variable >
    vim                 < returns corectly >
    $ bash              < initiate sub shell >
    $ echo $EDITOR      < get variable >
    $                   < return empty >

.

    $ export EDITOR='vim'       < set session presistent variable >
