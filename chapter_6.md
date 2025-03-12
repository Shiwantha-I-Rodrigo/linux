# STARTUP AND SERVICES

## init

+ can be located in /etc /bin or /sbin.
+ typically has **pid 1**.
+ parent process for every service.
+ `pstree -p 1` to view the process tree of 1.

.

    $ which init
    /sbin/init

    $ readlink -f /sbin/init
    /usr/lib/systemd/systemd

    -----------------------------------

    $ ps -p 1\
    PID     TTY     TIME        CMD
    1       ?       00:00:06    systemd

### systemd

> a **unit** in systemd defines a service. group of services or an action.\
> systemd conf at */etc/systemd/system.conf*

`$ systemctl list-units ` < list units >

* 12 unit types,
    + automount
    + device
    + mount
    + path
    + scope
    + service
    + slice
    + snapshot
    + socket
    + swap
    + target
    + timer

> groups of services are started via .target unit files.\
> **default.target** is responsible for launching all the required services at system initialization.

    $ find / -name default.target 2>/dev/null
    /usr/lib/systemd/system/default.target

    $ readlink -f /usr/lib/systemd/system/default.target
    /usr/lib/systemd/system/graphical.target

    -----------------------------------------------------

    $ systemctl get-default
    graphical.target

+ graphical.target > user access + GUI
+ multi-user.target > user access
+ network-online.target > runs after establishing a network connection
+ runlevel1.target > backward compatibility for SysV init system (1-5)

> service unit files contain configuration information to start a service.

priority order when two unit files have same name,

1. /etc/systemd/system
2. /run/systemd/system
3. /usr/lib/systemd/system

`$ systemctl list-unit-files ` < list unit files >

there are around 12 diffrent enablement statuses, however most used are.

- enabled : start at boot.
- disabled : does not start at boot.
- static : starts if another unit depends on it. can be manually started.

.

    $ systemctl cat cron.service
    /usr/lib/systemd/system/cron.service

    [unit]
    Description = ... description.
    Documentation = ... URI to documentation.
    Wants = ... x is required to start this unit, this unit sratrs even x fails.
    After = ... start this unit after x.
    Before = ... start this unit before x.
    Conflicts = ... do not start with x.
    Requires = ... x is required to start this unit.

    [Service]
    EnvironmentFile = ...file containing environmental variable substitutes.
    Environment = ... environmental variable substitutes.
    Type = ... startup type.
                forking - ExecStart starts a parent process, which creates service process as a child and exit.
                simple - Execstart starts service process.
                oneshot - Execstart start service process,and then process exit.
                idle - ExecStart start service process, wait until other start jobs are finished, then exit.
    ExcecStart = ... script or command to run when unit starts.
    ExcecStop = ... script or command to run when unit stops.
    ExcecReload = ... script or command to run when unit reloads.
    RemainAfterExit = ... if set to yes, remain active even after process started by ExecStart terminates.
    Restart = ... service is restarted when process started by ExecStart terminates.
                    ignored if systemctl restart or stop is issued.
                    (no, on-success, on-failure, on-abnormal, on-watchdog, always)

    [Install]
    Alias = ... additional name that can be used with systemctl
    Also = ... additional units that must be enabled or disabled for this service.
    WantedBy = ... which target unit manages this service.
    RequiredBy = ... which target units require this service.


> when modifying unit files always *copy* the file to **/etc/systemd/system/** directory first ,\
> it will take procedence over other locations and will be protected from software updates.

#### systemctl

`$ systemctl status cron` < check status of a service >

+ loaded : unit file name and path + enabled or disabled for boot startup.
+ active : is the unit running.

.

    disable / enable    start or do not start at boot.
    start               start units.
    status              display units current status.
    stop                stop units.
    restart             restart / start the units.
    mask                prevent unit from starting manually or at restart.
                        --now to immediately enforce.
                        --running to enforce only unitll next reboot.
                        link service to dev/null
    unmask              undo mask
    reload              load service config file of running unit to make
                        service configuration changes without stopping the service.
    daemon-reload       load unit config file of the running unit to make
                        unit file configuration changes without stopping the service.
    
    is-active           display active / failed
    is-enabled          display enabled / disabled
    is-failed           display active / failed


`$ systemctl is-system-running` < determine systems opertional status >

    running         system fully working.
    degraded        system has failed units.
    maintenance     system in emergency / recovery mode.
    initializing    system is starting to boot.
    starting        system is booting.
    stopping        system is starting to shut down.

`$ systemctl --failed` < display a list of failed units >

`$ systemctl get-default` < display systems default target >

`$ systemctl set-default` < set system default target >

`$ systemctl isolate graphical.target` < stop all services started by all other targets >
used to jump to various system modes.\
    **graphical** - standard system with GUI\
    **multi-user** - standard system with CLI\
    **rescue** - load all local filesystems, only root access, no networking, system in maintenance mode.\
    **emergency** - load only root filesystem, only root access, no networking, system in maintenace mode.\
    **reboot** - reboot system.\
    **poweroff** - shutdown system.\
    **halt** - halt system immediately.

> **.target** is optional for systemd targets.

> in grub2 menu targets can be set manually. press 'e' to edit the entry and add **systemd.unit=rescue.target** to the *linux* line.

`$ systemd-analyze *option*`

    blame           list each running unit with time taken to initialize. sorted slowest to fastest.
    time            display time spent on kernal, ramfs, user-space to initialize.
    critical-chain  display time critical units in a tree format.
    dump            display info about all units.
    verify          scan the unit files for errors, follows directory location precedence.
                    can accept a unit file name as an argument.


### SysV

instead of targets, sysV uses run levels,

    REDHAT
        0       shutdown
        1,s,s   single user mode (system maintenance)
        2       multiuser without networking
        3       multiuser
        4       custom
        5       multiuser with GUI
        6       reboot
    
    DEBIAN
        0       shutdown
        1       single user mode
        2       multiuser with GUI
        6       reboot

`$ runlevel` > **N 5**

in sysV `/etc/inittab` file was used to start services but later it has been used to set system run level and start terminal services.

`$ grep :initdefault: /etc/inittab` > **id:5:initdefault:**

each service must have a initialization script loaded at `/etc/init.d/` dir.

    $ ls /etc/init.d/

    anacron*    atd*            ...
    crond*      cups*           ...
    ntpd*       yum-updatesd*   ...

> **\*** indicates that they are executable bash scripts.

the program that calls these scripts is the **rc** script.
it resides in either `/etc/init.d/` or `/etc/rc.d/`.

    $ ls /etc/rc.d/

    init.d  rc0.d   rc2.d   rc4.d   rc6.d   rc.sysinit
    rc      rc1.d   rc3.d   rc5.d   rc.local

each run level has its own directory.\
each **rc** directory has symbolic links to initialization scripts in `/etc/init.d/` directory.\
**rc.local** scripts allows adding additional scripts to be run after system initialization.

    $ ls /etc/rc.d/rc3.d

    K01smolt@
    K02avahi-dnsconfd@
    ...
    K99readahead_later@
    S00microde_ctl@
    S04readahead_early@
    ...
    S55cups@

> **K** = KILL\
> **S** = START\
> **00-99** = the number indicate the order to kill or start service.

`$ readlink -f /etc/rc.d/rc3.d/S55cups` > **/etc/rc.d/init.d/cups**

**rc** script run through all the kill scripts first then start scripts, allowing switching between run levels on the fly.

#### init command

`$ init 3` / `$ telinit 3` > change run level to 3.

> `$ init 0` > shutdown system

#### service command

`$ service httpd status` > stopped

    restart     restart the service, if not already started, display **failed** and start the service.
    start       start the service
    status      show status
    stop        stop service
    reload      load config file of running service, display **failed** if service is not running.

#### chkconfig / update-rc.d

To configure which service to run at various run levels,\
redhat uses **chkconfig** and debian uses **update-rc.d**

    chkconfig cups                  check if 'cups' is enabled at current run level
    chkconfig cups on               enable 'cups' on current run level
    chkconfig cups off              disable 'cups' on current run level
    chkconfig --add cups            enable cups on all run levels
    chkconfig --del cups            disable 'cups' on all run levels
    chkconfig --level 35 cups on    enable 'cups' on levels 3 and 5
    chkconfig --level 35 cups off   disable 'cups' on levels 3 and 5
    chkconfig --list cups           show status of 'cups' on each run level

> **echo $?** must be used with `chkconfig cups` to display the returning result.

    $ chkconfig cups
    $ echo $?
    1

> **1 = FALSE** / **0 = TRUE**

    update-rc.d cups defaults                           start 'cups' at default runlevel.
    update-rc.d cups remove                             remove 'cups' from starting at default runlevel.
    update-rc.d -f cups start 55 2 3 . stop 80 0 1 6    start 'cups' at 55 on levels 2 and 3
                                                        kill 'cups' at 80 on levels 0,1 and 6

## MOUNT UNITS

presistent storages are defined with in `/etc/fstab` file or as a mount unit file.\
systemd converts fstab configuration to mount unit files automatically, when rebooting or reloading systemd.\
a single mount unit file is created for each mount point.\
**/home/temp/usb/** >> **home-temp-usb.mount**
+ remove preceding '/'
+ remove following '/'
+ replace middle '/'s with -
+ add .mount

.

    $ cat /etc/systemd/system/home-temp-usb.mount
    [Unit]
    ...

    [Mount]
    What=/dev/sda1
    Where=/home/temp/usb
    Type=ext4
    Options=defaults
    SloppyOptions=off   << set 'on' to ignore mount options unsupported by filesystem
    TimeOutSec=4        << set time timeout for a completed mount else fail mount.

    [Install]
    ...

`$ systemctl status home-temp-usb.mount` > check mount units status.\
`$ systemctl enable home-temp-usb.mount` > enable mount unit.

## AUTOMOUNT UNITS

automount.\
unmount after certain idle seconds.\
similar naming convention with **.automount** extention\

    [Automount]
    Where=/home/temp/usb
    DirectoryMode=0755
    TimeOutIdleSec=600

> 'DirectoryMode' (permissions) / 'TimeOutIdlesec' are not required.

## TIMER UNITS

allows defining events to occur at certain times.

    AccuracySec         accuracy of timer, default 1 min.
    OnActiveSec         time relative to timer activated.
    OnBootSec           time relative to when system booted.
    OnCalender          as specific date time.
    Persistent          store on disk when the timer activated last.
    RemainAfterElapse   unit remain loaded after timer elapsed to query status by systemctl.
    Unit                what unit to start.
    WakeSystem          resume system from being suspended.





