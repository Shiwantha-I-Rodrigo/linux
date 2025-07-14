# AUTHENTICATION

## PAM

pluggable authentication module, provide centralized authentication services.

Authentication Libraries,
- Password
    * use /etc/passwd & /etc/shadow for text passwords.
- Certificate
    * use Public Key Infastructure (PKI) certificate store to authenticate.
- LDAP
    * Lightweight Directory Access protocol
    * use local LDAP server to authenticate network logins.
- Kerberos
    * issue a token to user on login.
    * token can be used on multiple network resources.
    * refered to as single sign on (SSO).
- MFA
    * Multifactor Authentication.
    * biometrics/ tokens/ OTP's

programs which support PAM ("PAM AWARE") are compiled with PAM library (**libpam**).

`$ lld /bin/login | grep libpam.so`\
< check if a program is PAM AWARE >

pam config - **/etc/pam.d/**.

`$ cat /etc/pam.d/login`

    ...
    auth        include     postlogin
    account     required    pam_nologin.so
    account     include     system-auth
    ...
    session     optional    pam_keyinit.so   force   revoke

    < pam config file syntax >

    [ type ]  [ control flag]  [pam-module] [ module options ]

### TYPE

- account
    * account validation services.
    * time of day / expired account / etc...

- auth
    * account authentication.
    * password / etc...

- password
    * managing account passwords.
    * password length / number of attempts / etc...

- session
    * session management.
    * log sessions / mount $HOME / etc...

The **PAM module** is the config file which will be handling the work.
each module returns a status code, which will be handled according to the **Control Flag**.


### CONTROL FLAG

- include
    * add status code or response rating to final status.

- optonal
    * add status code to final status only if this is the only one of it's TYPE.

- requisite
    * if status is fail, immediately return fail as final status.
    * without running rest of the modules.

- required
    * if status is fail, return fail as final status.
    * after running rest of the modules.

- substack
    * force config files of particular TYPE to act together.
    * return a single status code.

- sufficient
    * if status is successful and no proceding modules produced a fail,
    * immediately return a final success status.
    * without running rest of the modules.

**pam_unix.so / pam_pwhistory.so / pam_pwquality.so** help enforce strong passwords.\
`$ sudo apt-get install libpam-pwquality`

pam_unix = authentication. (/etc/passwd, /etc/shadow)\
pam_pwhistory = prevent reusing passwords. (/etc/security/opasswd) slated & hashed.\
pam_pwquality = password qualities, such as length.


### MODULE OPTIONS

*options for **pam_pwhistory***

- enforce_for_root
    * when setting password, root account has it's password checked for reuse.

- remember=N
    * number of passwords to remember. (default=10 / max=400)

- retry=N
    * set passowrd reuse limit. (default=1)


*options for **pam_pwquality***

- minlen=N
    * minimum length (default=9, min=6)
    * _credit options may affect the length.

- dcredit=N
    * if N is positive, add N credits to **minlen** for including digits in password.
    * if N is negative, N digits must be in the password.
    * all credits listed below function the same.

- ucredit=N
    * for uppercase letters.

- lcredit=N
    * for lowerase letters.

- ocredit=N
    * for other chars.

- difok=N
    * N number of different chars must be in password.

*/etc/pam.d/password-auth* **OR** */etc/pam.d/common-password*

    ...
    password    reuired     pam_pwhistory.so
    password    sufficient  pam_unix.so         use_authtok
    ...

! direct modifications may be overwritten by authconfig utility.\
! avoid this by creating **password-auth-local** file.\

put **pam_pwhistory** above **pam_unix** so password is checked for reused before resetting it.\
**pam_unix** has **use_authtok** option < use already entered password instead of prompting again.\


### LOCK ACCOUNTS

> Prevent bruteforce attacks.

*options for **pam_tally2.so** / **pam_faillock.so***

- deny=N
    * lock after N attempts. (default=3)

- silent
    * display no informational messages to user.

- unlock_time=N
    * unlock after N seconds.

- audit
    * log non existent user name login attempts.

- preauth
    * called before authentication.
    * block user based on previous failed attempts.

- authfail
    * record auth failures to appropriate user tally file.

- authsucc
    * apone successful authentication clear the failed tally.


*/etc/pam.d/password-auth* **OR** */etc/pam.d/common-password*

    ...
    auth        reuired     pam_tally2.so   deny=2  silent
    ...


**! pam_faillock** is more modern and feature rich than **pam_tally2**.\
records must be added to both **/etc/pam.d/password-auth** and **/etc/pam.d/system-auth**

`$ sudo pam_tally2`

    login   failures    latest      failure     from
    Adam    4           11/09/21    16:12:12    /dev/pts/1
    ...


`$ sudo faillock`

    Adam:
    When                    Type    Source  Valid
    2022-11-30 17:12:23     TTY     tty2    V
    ...


< unlock user account >
`$ sudo pam_tally2 -r -u Adam`
`$ sudo faillock -r -u Adam`

-r , --reset / -u , --user

> tally files are stored in **/var/run/faillock** directory.\
> it is created on the first failed attempt.


**LIMITING ROOT ACCESS**

use **pam_securetty** module to restrict root access.

`$ cat /etc/securetty`

    console
    ...
    :0
    :0.0
    :0.1
    ...
    tty1
    tty2
    tty3
    ...

! /etc/securetty file list root access alowed terminals.

on a GUI **who** / **w** command output the TTY (:0 / tty1 )\

> pam_securetty is stored in /etc/pam.d/remote and/or /etc/pam.d/login\
> this module does not hamper **sudo** / **su** / **ssh** or **scp** commands.


## PUBLIC KEY INFASTRUCTURE (PKI)

### CERTIFICATES

after verifying person's identity, the Certificate Authority issues a certificate.\
user encrypt data with certificate key and sign the data with the certificate.\

<img src="images/certificate.png">


## SSH

- asymetric key encryption
- The two systems share their public keys.
.

    Rocky       openssh / openssh-clients / openssh-server
    Fedora      openssh / openssh-clients / openssh-server
    openSUSE    openssh
    Ubuntu      openssh-client / openssh-server

`$ sudo systemctl status sshd`\
< check ssh daemon status >

`$ ssh -p 1054 Robert@192.168.0.15`\
< in case remote host has non default port ( 1054 instead of 22 )>

**~/.ssh/known_hosts**\
< store any previously connected hosts >

`$ ssh Robert@192.168.0.15 "ls -l"`\
< send commands directly with ssh >

    ~/.ssh/config           OpenSSH client config. terminal commands override. 
    /etc/ssh/ssh_config     OpenSSH client config. ~/.ssh/config file override.
    /etc/ssh/sshd_config    openSSH daemon config. (server config / incomming requests config)

! for ssh to be connected succefully, the configurations must be compatible as well.
Some vital configs,
- AllowTcpForwarding : SSH port forwarding.
- ForwardX11 : X11 forwarding.
- PermitRootLogin : root user can ssh (default=no).
- port : set openssh daemon listening port (default=22).


**SSH KEYS**

openssh will use systems public/private keys in */etc/ssh/*.

- key algorithms
    * dsa
    * rsa
    * ecdsa
    * ed25519

`$ ls -l /etc/ssh/*key*`\

    /etc/ssh/ssh_host_ecdsa_key
    /etc/ssh/ssh_host_ecdsa_key.pub

`$ ssh-keygen -t rsa -f ~/.ssh/id_rsa`\
< generate ssh key pair >\
< default destination is also **~/.ssh/**>

1. log into client.
2. generate key pair.
    * key name must be id_"TYPE"
3. ssh into remote client.
4. copy public key to ~/.ssh/authorized_keys.

`ssh-copy-id -n Robert@192.168.0.15`\
< a dry run of copying the key >\
< remove **-n** switch to add the key for real >\
< this command add the key to the **~/.ssh/authorized_keys** file>\

! in case the **ssh-copy-id** is not available key must be added to\
the bottom of **~/.ssh/authorized_keys** file manually.


**AUTHENTICATION AGENT**

With normal key setup no passphrase is entered when generating the key pair.\
and the ssh connection will not prompt for a passphrase.\
but if the user choose to enter a passprhrase at the key generation,\
user has to enter it everytime connecting to the remote system.\
to avoid this the authentication agent is used.

after adding the keys as described above, start the agent and add the key to it as well.

    $ ssh-agent /bin/bash
    $ > ssh-add ~/.ssh/id_rsa
    $ > enter passphrase for id_rsa:
    $ > ssh Robert@192.168.0.15
    $ > exit

! after exit, the credentials must be added again to start an agent session.


**BEST PRACTICES**

- Change default port.
- Disable root logins via ssh.
- Manage TCP wrappers.

TCP wrapers are an older metyhod of controling access.\
IP Addresses can be v4 or v6\
file record syntax [ service ] : [ IP Address ].

**/etc/hosts.allow** - if remote ip is listed allow access.\
**/etc/hosts.deny** - if remote ip is listed deny access, if not allow access.

> if the remote ip is not found on both files, access is allowed.\
> to prevent this it is recomended to add **ALL:ALL** to hosts.deny.

    $ /etc/hosts.allow

    ...
    sshd : 192.168.0.15, 192.168.0.13, 192.168.10.12
    ...

even entire subnets can be allowed : 192.168.10.


## VPN

ssh is best when connecting to a local system, but connecting trough a public network is problematic.\
a VPN can establish a secure encrypted private connection through the public network.\

choosing a VPN Transportation Method,

! Do NOT use PPTP (Point to Point Tunneling Protocol).

- SSL / TLS ( secure socket layer / transport layer security)
    * originally called SSL
    * TSL 1.2 at minimum.
    * Stream oriented.
    * symetric encryption for data.
    * asymetric encryption for authentication.
    * prevent replay attacks.

- DTLS ( datagram transport layer security )
    * only UPD (faster than TCP).
    * based on TSL.
    * recomended.

- IPSec ( Internet Protocol Security )
    * framework
    * AH - Authentication Header protocol (Authentication).
    * ESP - Encapsulating Security Payload (Authentication / Data / Integrity).
    * ISAKMP - Internet Security Association and Key Management Protocol (Key Management).
    * tunnel mode - full datagram is protected.
    * transport mode - only data is protected.
