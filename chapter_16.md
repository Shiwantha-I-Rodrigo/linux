# AUTHENTICATION

## PAM

Framework used to manage authentication tasks. PAM provides a centralized, modular system that applications can plug into.

| **Authentication Method**             | **Description**|
| -                                     | - |
| **Password**                          | Uses **/etc/passwd** and **/etc/shadow** files to store and authenticate plain text password logins|
| **Certificate**                       | Utilizes a Public Key Infrastructure (PKI) certificate store for user authentication|
| **LDAP**                              | Lightweight Directory Access Protocol; uses a local LDAP server to authenticate network logins|
| **Kerberos**                          | Issues a token to the user at login The token can be reused across multiple network resources This is known as Single Sign-On (SSO)|
| **MFA (Multifactor Authentication)**  | Combines multiple authentication factors such as biometrics, tokens, and one-time passwords (OTPs)|

**/etc/pam.d/** : contains individual configuration files for each PAM-aware service.\
**/etc/pam.conf** : used to hold all PAM configurations for all services in one place.

> **!** programs which support PAM ( "PAM AWARE" ) are compiled with a PAM library (**libpam**).

Check if a program is PAM AWARE.
- `$ lld /bin/login | grep libpam.so` ---> list dynamic libraries used by a binary and look for **libpam.so**
- `$ ls /etc/pam.d/ | grep sshd` ---> PAM-aware programs usually have a config file in **/etc/pam.d/**

---

**PAM CONFIG FILE SYNTAX**

```
$ cat /etc/pam.d/sudo

#%PAM-1.0
auth        required     pam_env.so
auth        required     pam_unix.so
account     required     pam_unix.so
session     required     pam_env.so
session     optional     pam_mail.so

[ type ] [control-flag] [pam-module] [module-options]
```
The PAM **configuration file** (ie. /etc/pam.d/sudo) defines which modules to call, in what order, and how their results should be interpreted using **control flags**.\
Each PAM module returns a status code (ie. success, failure), and the **control flag** associated with that module tells PAM how to react to that result.

---

### TYPE

| **PAM Type**  | **Purpose**                   | **Examples of Functionality**|
| -             | -                             | - |
| **account**   | Validates the user account    | Time-based access control, account expiration, access limits |
| **auth**      | Authenticates the user        | Password checks, biometric or token verification |
| **password**  | Manages password changes      | Enforce password strength, history, retry limits |
| **session**   | Manages user session settings | Logging session activity, mounting home directories, environment setup |

---

### CONTROL FLAG

| **Control Flag**  | **Behavior**|
| -                 | - |
| **include**       | Appends the status code or result to the overall decision|
| **optional**      | Status code is only considered if this is the **only** module for that type|
| **requisite**     | If the module fails, **immediate failure** is returned and no further modules are processed |
| **required**      | If the module fails, the failure is recorded, but **processing continues**; failure is returned at the end |
| **substack**      | Treats a referenced set of modules (in another config) as one unit, returning a **single result** |
| **sufficient**    | If the module **succeeds** and no previous required modules failed, PAM **immediately returns success** |

---

### PAM MODULE

`$ sudo apt-get install libpam-pwquality`\
--> **pam_unix.so / pam_pwhistory.so / pam_pwquality.so** modules helps enforcing strong passwords.

| **PAM Module**    | **Purpose**                                                           | **Notes / Files Used** |
| -                 | -                                                                     | - |
| **pam_unix**      | Provides standard Unix authentication and password management         | Uses **/etc/passwd** and **/etc/shadow** |
| **pam_pwhistory** | Prevents users from reusing previous passwords                        | Stores old passwords in hashed form in **/etc/security/opasswd** |
| **pam_pwquality** | Enforces password complexity rules (length, character classes, etc.)  | Configuration in **/etc/security/pwquality.conf**|

---

### MODULE OPTIONS

 ---> **pam_pwhistory**

| **Option**            | **Description** |
| -                     | - |
| **enforce_for_root**  | Enforces password history checks (re-use) for the root user during password changes|
| **remember=N**        | Number of previous passwords to remember (Default: 10, Maximum: 400)|
| **retry=N**           | Number of times a user can retry password entry before failure (Default: 1) |

---> **pam_pwquality**

| **Option**    | **Description** |
| -             | - |
| **minlen=N**  | Minimum required password length (Default: 9, Minimum: 6) — may be affected by **credit values**|
| **dcredit=N** | **+N** : Add **N** credits to **minlen** for using digits<br>**-N** : Password must have **N** digits|
| **ucredit=N** | **+N** : Add **N** credits to **minlen** for using uppercase letters<br>**-N** : Password must have **N** uppercase letters|
| **lcredit=N** | **+N** : Add **N** credits to **minlen** for using lowercase letters<br>**-N** : Password must have **N** lowercase letters|
| **ocredit=N** | **+N** : Add **N** credits to **minlen** for using symbols or non-alphanumeric characters<br>**-N** : Password must have **N** symbols or non-alphanumeric characters|
| **difok=N**   | **N** Number of characters must be different from the previous password|

---

PAM configuration files that define how password-related operations are handled.
- */etc/pam.d/password-auth* : RedHat
- */etc/pam.d/common-password* : Debian

```
    ...
    password    reuired     pam_pwhistory.so
    password    sufficient  pam_unix.so         use_authtok
    ...
```
put **pam_pwhistory** above **pam_unix** so password is checked for reused before resetting it.

> **!** direct modifications will be overwritten by **authconfig (RedHat)** when the pam file is regenerated.\
> create a custom file ( **/etc/pam.d/password-auth-local** ), which will be included automatically.

> **!** direct modifications will be overwritten by **pam-auth-update (Debian)** utility, the next time it runs.\
> create a custom file ( **/usr/share/pam-configs/your-config** ), and add it using **pam-auth-update**.

---

### LOCK ACCOUNTS

> **!** Preventing bruteforce attacks.

---> **pam_tally2 (depricated)** / **pam_faillock**

| **Option**        | **Description**|
| -                 | - |
| **deny=N**        | Locks the account after **N** failed login attempts (Default: 3)|
| **silent**        | Suppresses informational messages|
| **unlock_time=N** | Automatically unlocks the account after **N** seconds|
| **audit**         | Logs attempts to log in with **non-existent usernames**|
| **preauth**       | Runs **before** authentication; can block access if the user has failed attempts previously|
| **authfail**      | Records authentication **failures** to the user's tally|
| **authsucc**      | Clears the user's tally of failed attempts **after successful login** |

```
    $ pam_tally2
    login   failures    latest      failure     from
    Adam    4           11/09/21    16:12:12    /dev/pts/1
    ...
```
```
    $ faillock
    Adam:
    When                    Type    Source  Valid
    2022-11-30 17:12:23     TTY     tty2    V
    ...
```

---> **/etc/pam.d/password-auth ( RedHat )** -OR- **/etc/pam.d/common-password ( Debian )**

    ...
    auth        reuired     pam_tally2.so   deny=2  silent
    ...

> **!** records must be added to both **/etc/pam.d/password-auth ( RedHat )** and **/etc/pam.d/system-auth ( RedHat )**

`$ pam_tally2 -r -u Adam` ---> reset user lock ( unlock user )\
`$ faillock -r -u Adam` ---> reset user lock ( unlock user )

|Option |           |Description|
|-      |-          |-|
|-r     |--reset    | Reset the failed login attempt tally|
|-u     |--user     | Specify the target username|

> **tally** files are stored in **/var/run/faillock** directory after being created on the **first failed** attempt.

---

### LIMITING ROOT ACCESS

---> **pam_securetty**

> **!** This file lists the TTY devices from which the root user is allowed to log in.

    console     # System’s primary physical console
    tty1        # Virtual terminals
    tty2
    tty3
    tty4
    tty5
    tty6
    ttys0       # Serial console
    pts/0       # Pseudo-terminal
    hvc0        # Hypervisor virtual console

> **!** use **who** / **w** command to find the current tty.

> **pam_securetty** is stored in **/etc/pam.d/remote** and/or **/etc/pam.d/login**\
> this module does not hamper **sudo** / **su** / **ssh** or **scp** commands.

---

## PUBLIC KEY INFASTRUCTURE (PKI)

### CERTIFICATES

after verifying person's identity, the Certificate Authority issues a certificate.\
user encrypt data with certificate key and sign the data with the certificate.

<img src="images/certificate.png">

---

## SSH

0. Check ssh daemon status.\
--> `systemctl status sshd`

1. Client initiates connection.\
--> `ssh user@host`

2. Server responds.\
--> Sends its public key to the client. (Asymetric Key Encryption)

3. Client verifies server.\
--> Checks server key against **~/.ssh/known_hosts**.

4. User authentication.\
--> User enter a password or use a SSH key.

5. Secure session established.\
--> All communication is now encrypted and secure.

For **ssh** to be connected succefully, the configurations must be compatible as well.\
- **AllowTcpForwarding** : SSH port forwarding.
- **ForwardX11** : X11 forwarding.
- **PermitRootLogin** : root user can ssh (default=no).
- **port** : set openssh daemon listening port (default=22).
- **etc...**

`$ ssh Robert@192.168.0.15 "ls -l"` ---> sending commands directly with ssh.

---

| **Distribution**  | **OpenSSH Packages** |
| -                 | - |
| Rocky             | `openssh`, `openssh-clients`, `openssh-server` |
| Fedora            | `openssh`, `openssh-clients`, `openssh-server` |
| openSUSE          | `openssh`|
| Ubuntu            | `openssh-client`, `openssh-server` |

---

| **File Path**         | **Purpose**| **Overrides** |
| -                     | - | - |
| `~/.ssh/config`       | **User specific SSH client config** file. <br>Used to define settings for **outgoing** SSH connections (like hostname aliases, ports, keys, etc.) | Overridden by command-line options|
| `/etc/ssh/ssh_config` | **System wide SSH client config** file. <br>Applies to all users' **outgoing** SSH connections, unless overridden | Overridden by **~/.ssh/config** and CLI |
| `/etc/ssh/sshd_config`| **SSH server (daemon) config** file. <br>Defines how the server handles **incoming** SSH connections (ie. port, login rules, key settings)| |

---

**SSH KEYS**

| **Algorithm** | **Full Name**                             | **Notes**|
| -             | -                                         | - |
| **DSA**       | Digital Signature Algorithm               | **Old and insecure**; deprecated in SSH|
| **RSA**       | Rivest–Shamir–Adleman                     | Widely supported; **strong if key size is ≥2048 bits** Slower|
| **ECDSA**     | Elliptic Curve Digital Signature Algorithm| Smaller keys, faster than RSA **Not** as widely trusted|
| **Ed25519**   | Edwards-curve Digital Signature Algorithm | **Modern, fast, secure**, and recommended|

OpenSSH server (sshd) uses the system’s public/private host keys stored in **/etc/ssh/** to send to the client as server credentials, during incoming SSH connections.

    /etc/ssh/ssh_host_ecdsa_key
    /etc/ssh/ssh_host_ecdsa_key.pub

1. log into client.
2. generate key pair.\
`$ ssh-keygen -t rsa -f ~/.ssh/id_rsa`\
--> generate ssh key pair, default destination is also **~/.ssh/**, key name must be **id_"TYPE"**.
3. ssh into remote client.
4. copy public key to ~/.ssh/authorized_keys.\
`ssh-copy-id -n Robert@192.168.0.15`\
--> a dry run of copying the key, remove **-n** switch to add the key for real.

> **!** in case the **ssh-copy-id** is not available key must be added to the bottom of **~/.ssh/authorized_keys** file manually.

---

**AUTHENTICATION AGENT**

When generating an SSH key pair, you can choose whether or not to set a passphrase:
- If no passphrase is set, SSH connections using the key happen without prompts.
- If a passphrase is set, you'll be prompted to enter it every time you use the key to connect to a remote system.

To avoid repeatedly entering the passphrase, you can use an authentication agent (ssh-agent), which securely stores the decrypted private key for the duration of your session.

start the agent and add the key to it as well.

    $ ssh-agent /bin/bash
    $ > ssh-add ~/.ssh/id_rsa
    $ > enter passphrase for id_rsa:
    $ > ssh Robert@192.168.0.15
    $ > exit

> **!** after exiting the session, the credentials must be **added again** to start an agent session.

---

**SSH SECURITY BEST PRACTICES**

- Change the default SSH port
    + Use a non-standard port (other than 22) to reduce exposure to automated attacks.
- Disable root login via SSH
    + Prevent direct root access by setting PermitRootLogin no in /etc/ssh/sshd_config.
- Use TCP Wrappers for access control
    + Although older, TCP Wrappers can still be used to restrict access based on IP addresses.

TCP Wrappers are an older method of controlling access to network services based on IP addresses.\
They support both IPv4 and IPv6 addresses.

    [service] : [IP address or hostname]

| **File**              | **Behavior** |
| -                     | - |
| **/etc/hosts.allow**  | If the client’s IP address is listed here, **access is allowed**|
| **/etc/hosts.deny**   | If the IP is listed here (and not in **hosts.allow**), **access is denied**|

if the remote ip is not found on both files, access is allowed, to prevent this it is recomended to add **ALL:ALL** to **hosts.deny**.

    $ /etc/hosts.allow

    sshd : 192.168.0.15, 192.168.0.13, 192.168.10.
                                            ↑
                                  entire subnet allowed

---

## VPN

SSH is excellent for secure connections **within a local or trusted** network.\
However, when **connecting over a public or untrusted network**, SSH alone may not provide enough protection, especially for broader network access.\
In such cases, using a **VPN** (Virtual Private Network) is recommended.\
A VPN creates a secure, encrypted private tunnel over the public network, allowing safer remote communication.\
Do NOT use PPTP (Point-to-Point Tunneling Protocol) — it's outdated and insecure.

| **Method**                                                       | **Description**                                                              | **Key Features**                                                                                                                                                                                                                                                          | **Recommendation**|
| - | - | - | - |
| **SSL/TLS**<br>(Secure Sockets Layer / Transport Layer Security) | Originally known as SSL, now standardized as TLS (use TLS **1.2 or later**). | - Stream-based (over TCP)<br>- Symmetric encryption for data<br>- Asymmetric encryption for authentication<br>- Prevents replay attacks                                                                                                                                   | **Widely used**|
| **DTLS**<br>(Datagram Transport Layer Security)                  | TLS over **UDP**, offering faster performance with similar security.         | - Based on TLS<br>- Uses UDP only<br>- Maintains security while improving speed                                                                                                                                                                                           | **Recommended** for performance|
| **IPSec**<br>(Internet Protocol Security)                        | A suite of protocols for securing IP traffic.                                | - **AH** (Authentication Header): Authentication only<br>- **ESP** (Encapsulating Security Payload): Encryption, integrity, authentication<br>- **ISAKMP**: Key management<br>- **Tunnel mode**: Protects entire IP packet<br>- **Transport mode**: Protects payload only | **Robust** and widely supported in enterprise systems|
