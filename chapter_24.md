# TROUBLESHOOTING

## STORAGE ISUUES

- **Degraded Storage** : gradual decay of storage medium due to uasge.
- **Degraded Mode** : one or more disks has failed in the RAID array.
- **Missing Devices** : 
    + for network storage, troubleshoot network.
    + `$ lspci -M` to scan PCI attached devices.
    + look in **/dev/** directory.
    + rebuild device file using `$ mknod /dev/sda b 8 16`.
        * b = block device.
        * 8 = major number (device type driver ie, sd / tty / ... ).
        * 16 = minor number (specific device, ie, sda / sdb / ... ).
        * use major / minor list of the distro to identify correct numbers.
- **Missing Volumes** : due to unintentionally removed devices.
- **Missing Mount Point** : just create the mount point with `$ mkdir`.
    + before removing a dir, check if it's a mount point with `$ mountpoint myDir`.
- **Storage Integrity** : bad blocks do not respond to I/O requests.
    + instead of `$ fsck`, the `$ badblocks` command can also be used.
    + unmount device / partition.
    + `$ badblocks -nsv /dev/sda2` non destructive test.
- **Performance Issues** : 
    + `$ iostat` / `$ ioping` / `$ iotop`
    + `$ hdparm` : drive read speed. (PATA / SATA)
    + `$ dstat` : similar to io stat with additional info.

### SPECIALIZED ISSUES

- check if driver updates are available.
- check if kernal module is loaded.
    + `$ dmesg | grep sde` > [ 5.500454 ] sd 6:0:0:0: [ sde ] Attched **SCSI disk**.
- look for **SCSI** drivers in **/sys/** directory.
    + `$ ls /sys/bus/scsi/drivers` > sd  sr
- confirm which driver is in use.
    + `$ udevadm info -an /dev/sde | grep DRIVERS | grep sd` > DRIVERS=="sd"
- check if the module is loaded.
    + `$ lsmod | grep sd` > sd_mod    46322  5
- if not loaded, check biult-in modules.
    + `$ cat /lib/modules/$(uname -r)/modules.builtin | grep sd_mod`
- if not loaded or built-in dynamically load it.
    + `$ modprobe`

### SATA

- SATA drivers are self-configuring.
- SATA fails due to frequent head loads and unloads.
- check this with `$ smartctl -a /dev/sda`.
    + look at **Start_Stop_Count** : number of loads / unloads.
- confirm with `$ hdparm -B /dev/sda`.
    + if returns a low number ( ie, 1 ) then aggressive power management is active.
- set `$ hdparm -B 127 /dev/sda`.

### SCSI

- SCSI framework consist of,
    + Upper : device driver.
    + Middle : SCSI routing layer.
    + Lower : Host bus adapter driver layer.

`$ udevadm info -an /dev/sda | grep -i drivers`

    ...
    DRIVERS=="sd"
    ...
    DRIVERS=="ahci"
    ...

`$ lsmod | grep ahci`

    ahci        34056  3

`$ modinfo ahci`

    ...
    description : AHCI SATA low-level driver
    ...

**ahci** driver allows hot plugging SATA drives, which are treated as SCSI devices.\
after hot-plugging it must be enabled by rebooting the system or finding the host number and forcing a scan.

    # lsblk -S
    ...
    sde     6:0:0:0     disk ATA    VHARDDISK   1.0  sata
    ...

    # echo '---' > /sys/class/scsi_host/host6/scan

### RAID

check RAID status by viewing **/proc/mdstat** file.

## APPLICATION PERMISSIONS

In case of I/O errors,

- which account is running the application.
- discover the action that raised the error.
- obtain a full directory reference for any files the app was atempting r/w operations.
- record any additional applications, it was trying to launch.
- record any services (local/remote), it was trying to employ.

Steps,

1. verify ownership of files and directories involved with `$ ls -l`.
2. check groups the account is part of.
3. if the app is executable by the user account.
4. check for execution permissions for the whole directory tree at the working directory.
5. check inheritance for target directory via ACL's.
6. set ACL permissions for file if required.
7. check for sticky bit, in case of denied file deletion.

## APPLICATION DEPENDENCIES

### VERSIONING

- management of multiple software updates through a numbering process.

### UPDATING

- in case of issues always check for updates.
- apply updates to a test environment if possible, before applying to production.
- check the update history with `$ rpm -q [ package ] --last` for RPM or **/var/log/apt/history.log** for APT.
- **broken dependencies AKA unmet dependencies** causes **broken packages**.
- use `$ apt-get check` or `$ rpm -aV` to look for unmet dependencies.

### PATCHING

- program changes or configuration file updates, usually to fix serious problems or security vunerabilities.
- issued out of normal update cycle.

### LIBRARIES

- Application functions are often split into seperate library files, so mutiple Applications can share the library.
- `$ lld [ program name ]` : list librarries used by a program.

.

    $ which ssh
    /usr/bin/ssh

    $ lld /usr/bin/ssh > ssh_libs.txt

    $ cat ssh_libs.txt
    ...
    lib5crypto.so.3 ...
    ...

    $ grep -B 2 -A 2 lib5crypto /var/log/apt/history.log
    Start-Date : ...
    Commandline : ...
    Upgrade :...
    End-Date: ...
    
    A and B pulls additional lines  from (Above and Below the result.)

### ENVIRONMENT VARIABLES

- Check $PATH variable.
- to mod this parameter for everyone create bash script on **/etc/profile.d/**.
- the file must be owned by root and root group with others having permission to read.
- to mod parameters for certain users, add the bash script to **~/.profile** OR **~/.bash_profile** OR **~/.bash_login**.

### GCC COMPATIBILITY

- if there are issues compiling with **gcc**.
    + the system **C** lib might not be complient with the **ISO C** standards used by the **gcc**.
    + incompatibilities with **GNU C** and **non-ISO** versions of **C**.
    + **gcc** uses corrected versions of system header files.

### REPOSITORY ISSUES

- check network connectivity.
- `$ apt-get clean` OR `$ yum clean all` OR `$ zypper clean -a` : clean temp downloads and database.
- `$ apt-get dist-upgrade` : upgrade while preventing any upgrades that will break a dependent package.
- `$ grep -v "#" /etc/apt/sources.list` OR `$ yum repolist` OR `$ zypper repos` : REPO. List.


manually adding repos to apt.

- `$ echo "deb https://apache.bintray.com/couchdb-deb $(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list`
- when manually configuring a repository you also need to manually import the public repository key to your system.
- `$ curl -L https://couchdb.apache.org/repo/bintray-pubkey.asc | sudo apt-key add -`
- update the DB.

automatically adding repositories.

- `$ yum-config-manager --add-repo [ repo url ]`
- `$ zypper add-repo [ repo url ] [ alias ]`


## SELINUX CONTEXT VIOLATIONS

- **sealert** tool from **setroubleshoot** package.
- `$ ls -Z` view files with SELinux context.
- `$ chcon` to modify dir / file contexts.
- `$ semanage` to make changes permenent.
- `$ restorecon` to fix the labels.

## FIREWALL

### ACL's

- ACL reviews a packets control info,
    + source address / hostname.
    + destination address / hostname.
    + network protocols used.
    + inbound port.
    + outbound port.
- `$ firewall-cmd --get-default-zone` : check default zone ( ie, drop will **drop** all incoming traffic ).

### PORTS

- if the application rely on other system services, check rules related to those service ports.
- if the service runs on a not well known port check firewall rules.
- `$ iptables -A INPUT -p tcp --dport 443 -j ACCEPT` : modify firewall rules.

### PROTOCOLS

- if the application uses a DNS server (port 53).
- check **/etc/serices** for protocols used by DNS (port 53).

.

    $ grep 53 /etc/services
    ...
    domain      53/tcp
    domain      53/udp
    ...

- unblock port 53 for **tcp** and **udp**.

## HARDWARE ISSUES

- in addition to `$ lspci` & `$ lsusb` & `$ lsdev`.

### dmidecode

- Distributed Management Task Force ( DMTF ).
- DMTF is a non-profit, simplifing management of network accessible technologies.
- Desktop Management Interface ( DMI ).
- System Management BIOS ( SMBIOS ) standards.
- DMI consist of 4 components providing info about system hardware.
- to use these standards, a computer compliant with DMI/SMBIOS and a software interface is reuired.
- **dmidecode** is the linux interface to DMI/SMBIOS data structures.
- **dmidecode** pulls information from **sysfs** system specifically the **/sys/firmware/dmi/tables/**

`$ dmidecode [ options ]`

    -d      --dev-mem  [ FILE ]     Read memory from device File ( /dev/mem ).
    -q      --quiet                 Less verbose.
    -s      --string  [ KEYWORD ]   Display the value of the given DMI string.
    -t      --type  [ TYPE ]        Display entries of given type.
    -u      --dump                  Do not decode the entries.
            --dump-bin  [ FILE ]    Dump DMI data to bin file.
            --from-dump  [ FILE ]   Read from bin file.
            --no-sysfs              Do not attempt to read data from sysfs.
    -V      --version               Display version and exit.


- Types
    + baseboard
    + bios
    + cache
    + chassis
    + connector
    + memory
    + processor
    + slot
    + system

.

    # dmidecode -t system

    dmidecode 3.0
    Getting SMBIOS data from sysfs.
    SMBIOS 2.5 present.

    Handle 0x0001, DMI type 1, 27 bytes
    System Information
    ...

> the information from **dmidecode** is inaccurate in virtual systems.

### lshw

- lshw pulls from **/proc/**.

`$ lshw [ OPTIONS ]`

    -short      table formatted display of hardware data ( path / device / class / desc.).
    -businfo    show info on SCSI/USB/IDE/PCI.
    -class      show info about a specific class of hardware.

### OTHER ISSUES

**Memory**
- Symptoms
    + system performance slows over time.
    + system hangs at boot or resource intensive tasks.
    + kernal panics / segmentation faults.
    + program installation fails.
- Steps
    + make sure the memory capacity is not the issue ( free / vmstat ).
    + test memory for faulty components ( memtest on boot ).

**Printers**
- check for incorrect / outdated drivers.
- check printer error logs ( /var/log/cups/error_log ).
- check linux compatibility before buying.
- checkout printer web admin interface ( 127.0.0.1:631 ).
- view availabe printer drivers ( lpinfo -m ).

**Video**
- check kernal ring buffer ( dmesg ).
- X11 - /var/log/Xorg.0.log.
- Wayland ( journalctl ).
- Graphic drivers ( lspci -vnn ).
- redirect lspci output to file and search for "vga".
- some manufacturers provide own utilities ( nvidia-smi / nvidia-settings ).

**Communication Ports**
- find the device-name ( dmesg | grep ttyS ).
- employ ( setserial -a device-name ) command for additional info.
- look for IRQ in output.
- search the IRQ in /proc/interrupts.
- if not found the device is not loaded.

**USB**
- check if usb module is loaded ( lsmod | grep usb ).
- if not use modprobe to load it.
- watch the journal file ( journalctl -f ).
- employ ( lsusb -v ) see if device show up.
- check kernal ring buffer ( dmesg ).
- check usb device file for corruption.

**Keyboard**
- localectl : to view current key map ( REDHAT ).
- localectl list-keymaps : to list availabe maps.
- localectl set-keymap [ name ] : to set a map.
- dpkg-reconfigure keyboard-configuration : text based menu to select key map ( DEBIAN ).
