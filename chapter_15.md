# OWNERSHIP AND PERMISSIONS

## OWNER

The 3 Owners,
1. Owner - each file / directory is assigned to a single owner.
2. Group - each file / directory is assigned to a single group.
3. Other - everyone else.

> when creating users, many linux distros assign the user to a new group with name of the user.



### chown

`$ sudo chown Adam file1.txt file2.txt`
< set the 2 text files owner to Adam >

`$ sudo chown Adam:HR_Group file1.txt file2.txt`
< set the 2 text files owner to Adam and group to HR_Group >

### chgrp

`$ sudo chgrp HR_Group file1.txt file2.txt`
< set the 2 text files group to HR_Group >

## PERMISSIONS

The 3 Permissions,
1. Read [ r ]
2. Write [ w ]
3. Execute [ x ] run the file or list the content of the directory.

.


    $ ls -l
    -rwxrw-r-- 1 Adam HR_Group 1542 Jan 14 15:21 file1.txt
    -rwxrw-r-- 1 Adam HR_Group 1542 Jan 14 15:21 file2.txt

> the first value indicate the type of object.

    file           -
    link           i
    directory      d

### chmod

**Symbolic Mode**

    u = user / owner
    g = group
    o = others

    + add permission
    - remove permission
    = set permission


`$ sudo chmod g+x file1.txt`\
< add x permission to the group >

`$ sudo ug=rwx file2.txt`\
< set owner and group permissions as rwx >


Octal Mode

    r = 4
    w = 2
    x = 1

`$ sudo chmod 754 file1.txt`\
< set permissions as **rwxr-xr--** >

<img src="images/perm_oct.png">

## SPECIAL PERMISSIONS

**User ID** SUID

this bit tells the kernal to run the executable\
with the file owners permissions and not the user executing it.

`$ sudo chmod u+s file1.txt` < with symbolic mode >

`$ sudo chmod 4754 file1.txt` < with octal mode >

it replaces the **x** permission of user.

-rw**s**r--r-x

when setting SUID a Capital **S** is shown ,if even the owner\
has no permission to execute the file or the file is unexecutable.

**Group ID** SGID

when set any file created in the directory are assigned to the directory group and not the user.\
therefore any user in the group has same permissions to the file.

`$ sudo chmod g+s /Folder` < with symbolic mode >

`$ sudo chmod 2660 /Folder` < with octal mode >

it replaces the **x** permission of group.

drw-rw**s**---

**Sticky Bit**

protect file from deletion by anyone other than the owner.\
often used with shared folders.

`$ sudo chmod o+t /Folder` < with symbolic mode >

`$ sudo chmod 1777 /Folder` < with octal mode >

drwxrwxrw**t**

## DEFAULT PERMISSIONS

**user mask** feature define an octal value to be subtracted\
from permissions when creating a new file or directory.

`$ umask` < current mask > \
0022

first value represents the mask for special permissions.\
next 3 values represent the mask permissions.

default values for new files is **666** and for new directories **777**.

    umask           new file            new dir
    0000            666                 777
    0002            664                 775

`$ umask 0022`
< set umask for session >

to permenantly change umask, add it to the ~/.bash_profile file.

## ACL's

Access Control List's allow setting permissions for multiple users and groups.

**getfacl / setfacl**

`$ getfacl file1.txt`

    file : file1.txt
    owner : Adam
    group : HR_Group
    user :: rw-
    group :: r--
    other :: ---

`$ setfacl -m g:SALES_Group:rw file1.txt`

- there is no output from setfacl command.

`$ ls -l`

    -rw-r-----+ 1 Adam HR_Group 1234 Jan 19 15:21 file1.txt

- only standard permissions are shown
- a (+) sign is added to indicate, additional ACL's are applied to the file.

`$ getfacl file1.txt`

    file : file1.txt
    owner : Adam
    group : HR_Group
    user :: rw-
    group :: r--
    group : SALES_Group : rw-
    mask :: rw-
    other :: ---

`$ setfacl -x g:SALES_Group file1.txt`
< remove SALES_Group permissions >

`$ setfacl -m d:g:SALES_Group:rw /Folder`
< set permissions for Folder, new files created will inherit the ACL >

## CONTEXT BASED PERMISSIONS

in the previous **Discretionary Access Control ( DAC )** method,\
there is nothing stopping file owners from giving any permission to other users.\
therefore a **Mandatory Access Control ( MAC )** is used to allow admin to set rules.\
**Role-Based Access Control** is a sub category of **MAC**.

- SELinux for RedHat.
- AppArmor for ubuntu.

### SELinux (REDHAT)

Security Enhanced Linux is a project of NSA now integrated into RedHat 2.6+.\
each time a user or process attempts to access an object SELinux intercepts the attempt\
and evaluate it against defined policy rules.

**/etc/selinux/config** file controls basic opertaions.
* SELINUX
    - enforcing = enable policies and block unauthorized access.
    - permissive = log policy violations, doesn't enforce them.
    - disabled = disable selinux.

* SELINUXTYPE
    - targeted = only enforce network daemon policies.
    - minimum = only for specified processes.
    - mls = multilayer security, military standard.(top secret, unclassified, public,...)
    - strict = for all daemons ( not recomended ).

`$ sudo getenforce`
    
    Enforcing

`$ sudo sestatus`

    SELinux status : enabled
    SELinux mount : /sys/fs/selinux
    ...

#### Security Context

user:role:type:level

`$ ls -Z file1.txt`

    unconfined_u : object_r : user_home_t : s0 file1.txt

`$ ps -axZ | grep sshd`

    system_u : system_r : sshd_t : s0-s0:c0.c1023 1029 ? Ss 0:00

! indicate that **sshd** process is a system process.

**Type Enforcement** is defining what objects in a specific type security context can access\
objects of another type security context.\
eg - an application labeled with type security context **sshd_t** can only access files\
labeled with type security context **sshd_t**.

selinux maintain policies as text files in **/etc/selinux/** directory structure.\
eg - **/etc/selinux/targeted/** directory.

creating own policies can be complicated, therfore polices can be\
listed / installed / removed as modules using **semodule** utility.

`$ sudo getsebool -a`
< list all policies >

    abrt_anon_write --> off
    antivirus_can_scan_system --> off
    ...
    ...

`$ sudo setsebool antivirus_can_scan_system on`
< change boolean >

### AppArmor (DEBIAN)

! only controls files and network ports that applications have access to.

AppArmor is installed by default, but tools are not.\
install **apparmor-utils** and **apparmor-profiles**.

- profiles for applications are stored in **/etc/apparmor.d/**
- each profile is a text file, defining which files and ports the app has access to.
- profile name is a reference to app path. (ie, **usr.bin.mysqld** ).

`$ aa-status`\
< AppArmor status >

`$ aa-unconfined`\
< list of active ports without a profile >

`$ aa-complain /usr/sbin/tcpdump`\
< violations wil be logged but not blocked >

`$ aa-disable /usr/sbin/tcpdump`\
< disable the profile >

`$ aa-enforce /usr/sbin/tcpdump`\
< enable profile >

## USER TYPES

* Root / Super User
    - main admin account on system.
    - userID 0.
    - access to all files and directories regardless of permission settings.

* Standard 
    - userID 1000+.
    - use privilage escalation to perform admin tasks.

* Service
    - created by system to start services.
    - UserID 1-999.
    - login is disabled.( * in shadow file )
    - shell access is disabled. ('nologin' as /etc/passwd default shell)

### ESCALATING PRIVILEGES

- su
    * substitute user.
    * password for target account.

- sudo
    * substitute user do.
    * own password.
    * user must be in /etc/sudoers file.

- sudoedit
    * similar to sudo.
    * allows opening a text file with admin priviledges.

> **sudoers** also define groups that have admin access.\
> ie, **sudo** - debian / **wheel** - redhat.\
> never edit the **sudoers** file directly, use **visudo** command.

`$ EDITOR=vim visudo`

### RESTRICTING USERS

- ulimit

restrict access to system resources.

    -a      list limits for current account.
    -b      set max socket buffer size.
    ...
    -f      set max file size.
    ...
    -l      set max memory that can be locked.
    ...
    -s      set max stack size.
    -t      set max CPU time allowed.
    -u      set max number of simultaneous processes.
    -v      set max virtual memory.
    ...
    -P      set max number of pseudo-terminals.
    -T      set max threads.

- chattr

change file attributes.

    a       can only open in append mode.
    A       access time is not changed when opening file.
    c       auto compress file on the disk.
    C       file is not auto copied on write to a journaling filesystem.
    d       donot backup file with dump program.
    D       all changes are syncronuosly written to disk without caching.
    ...
    E       encrypted by the filesystem.
    F       when applied to a dir, all path lookups are case insensitive.
    i       file can't be modded or deleted.
    ...
    s       if the file is deleted, it's blocks are zeroed and written to disk.
    S       changes are syncronuosly written to disk, no buffer.
    ...
    U       sve content on delete, allowing undelete.
    V       apply file authentication.


`$ lsattr file1.txt`
< view current attribs >

    ----i-------------- file1.txt

`$ sudo chattr -i file1.txt`
< change attribs >