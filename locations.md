| **Category**                  | **Paths / Files**                                                                  | **Distro** | **Notes**                                     |
| ----------------------------- | ---------------------------------------------------------------------------------- | ---------- | --------------------------------------------- |
| **Bootloaders**               | `/etc/lilo.conf`                                                                   | Common     | LILO config (legacy)                          |
|                               | `/boot/efi/`                                                                       | Common     | EFI system partition                          |
|                               | `/boot/grub/menu.lst`                                                              | Common     | GRUB (legacy, pre-GRUB2)                      |
|                               | `/boot/grub/grub.cfg`                                                              | **Debian** | GRUB2 config (Debian/Ubuntu)                  |
|                               | `/boot/grub2/grub.cfg`                                                             | **RHEL**   | GRUB2 config (RHEL/CentOS/Fedora)             |
|                               | `/etc/default/grub`                                                                | **Debian** | Global GRUB defaults                          |
|                               | `/etc/grub.d/`                                                                     | Common     | GRUB script snippets                          |
| **Init & Service Management** | `/etc/`, `/bin/`, `/sbin/`                                                         | Common     | Init binary locations                         |
|                               | `/etc/systemd/system/` <br> `/run/systemd/system/` <br> `/usr/lib/systemd/system/` | Common     | Systemd units (priority: etc > run > usr)     |
|                               | `/etc/inittab`                                                                     | Common     | SysV init (legacy)                            |
|                               | `/etc/init.d/`                                                                     | **Debian** | SysV init scripts                             |
|                               | `/etc/rc.d/init.d/`                                                                | **RHEL**   | SysV init scripts                             |
|                               | `/etc/rcx.d/`                                                                      | **Debian** | SysV runlevel dirs                            |
|                               | `/etc/rc.d/rcx.d/`                                                                 | **RHEL**   | SysV runlevel dirs                            |
|                               | `/etc/rc.local`                                                                    | **Debian** | Local init script                             |
|                               | `/etc/rc.d/rc.local`                                                               | **RHEL**   | Local init script                             |
| **Networking**                | `/etc/network/interfaces`                                                          | **Debian** | Static network config                         |
|                               | `/etc/sysconfig/network-scripts/`                                                  | **RHEL**   | Network scripts                               |
|                               | `/etc/hostname`                                                                    | **Debian** | System hostname                               |
|                               | `/etc/HOSTNAME`                                                                    | **RHEL**   | System hostname                               |
|                               | `/etc/resolv.conf`                                                                 | Common     | DNS resolvers                                 |
|                               | `/etc/services`                                                                    | Common     | Network service names/ports                   |
| **User Creation**             | `/etc/default/useradd`                                                             | **Debian** | Useradd defaults                              |
|                               | `/etc/login.defs` <br> `/etc/skel`                                                 | Common     | Login settings & skeleton                     |
|                               | `/etc/passwd`, `/etc/shadow`, `/etc/group`, `/etc/gshadow`                         | Common     | Core account DB                               |
|                               | `/sbin/nologin`, `/bin/false`                                                      | Common     | Block shell access                            |
| **SSH**                       | `/etc/ssh/sshd_config`                                                             | Common     | SSH server config                             |
|                               | `/etc/ssh/ssh_config`                                                              | Common     | SSH client config                             |
|                               | `~/.ssh/config`                                                                    | Common     | User SSH client config                        |
| **Time Services**             | `/etc/chrony/chrony.conf`                                                          | Common     | Chrony config                                 |
|                               | `/etc/systemd/timesyncd.conf` <br> `/etc/systemd/timesyncd.d/`                     | Common     | systemd-timesyncd config                      |
| **Package Management**        | `/etc/apt/sources.list`                                                            | **Debian** | APT repos                                     |
|                               | `/etc/yum.repos.d/`                                                                | **RHEL**   | YUM/DNF repos                                 |
| **Kernel Module Configs**     | `/etc/modules.conf`                                                                | Common     | Old module config                             |
|                               | `/etc/modules-load.d/`                                                             | **Debian** | Auto-load modules                             |
|                               | `/etc/modprobe.d/`                                                                 | Common     | Modprobe config snippets                      |
|                               | `/etc/modprobed/`                                                                  | **RHEL**   | RHEL-style module configs                     |
|                               | `/lib/modprobed/`, `/usr/lib/modprobed/`                                           | **RHEL**   | 3rd party module configs                      |
|                               | `/run/modprobed/`                                                                  | Common     | Runtime module configs                        |
|                               | `/etc/modprobe.d/blacklist.conf`                                                   | Common     | Blacklist modules                             |
| **PAM**                       | `/etc/pam.d/common-password`                                                       | **Debian** | PAM password policy                           |
|                               | `/etc/pam.d/password-auth`                                                         | **RHEL**   | PAM password policy                           |
|                               | `/usr/share/pam-configs/config1`                                                   | **Debian** | Manual PAM config                             |
|                               | `/etc/pam.d/password-auth-local`                                                   | **RHEL**   | Auto PAM config                               |
|                               | `/etc/pam.conf`, `/etc/pam.d/`                                                     | Common     | Global PAM config                             |
|                               | `/etc/passwd`, `/etc/shadow`                                                       | Common     | pam\_unix backend                             |
|                               | `/etc/security/opasswd`                                                            | Common     | pam\_pwhistory                                |
|                               | `/etc/security/pwquality.conf`                                                     | Common     | pam\_pwquality                                |
|                               | `/etc/pam.d/remote` <br> `/etc/pam.d/login`                                        | Common     | pam\_securetty                                |
|                               | `/var/run/faillock/`                                                               | Common     | PAM login failure tracking                    |
| **Firewall**                  | `/usr/lib/firewalld/zones/`                                                        | **RHEL**   | Firewalld configs                             |
|                               | `/etc/sysconfig/iptables`, `/etc/sysconfig/ip6tables`                              | **RHEL**   | iptables (legacy)                             |
|                               | `/etc/iptables/rules.v4`, `/etc/iptables/rules.v6`                                 | **Debian** | iptables-persistent rules                     |
|                               | `/etc/default/ufw`                                                                 | **Debian** | UFW defaults                                  |
|                               | `/etc/ufw/user.rules`, `/etc/ufw/user6.rules`                                      | **Debian** | UFW rules                                     |
|                               | `/etc/ufw/applications.d/` <br> `/etc/ufw/applications.d/custom.d/`                | **Debian** | UFW profiles                                  |
