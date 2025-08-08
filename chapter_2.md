# SERVICES

**Daemon** : a background process that provides a services and often end with letter *"d"*.\
**Child Process** : a process created by a parent process and can become independent.\
**Super Servers** : a process waiting for incomming connections and spawn appropriate services on demand.

`inetd` : simple internet *super server* for Linux.\
`xinetd` : inetrnet *super server* with additional features (security, IPv6, logging, etc...).

**IP Address** --> reach the server.\
**Port Number** --> reach the service.

|Well Known Ports   ||
|-                  |-|
|20/21              |FTP , *file transfer protocol*|
|22                 |SSH , *secure shell protocol*|
|23                 |Telnet|
|25                 |SMTP , *simple mail transport protocol*|
|53                 |DNS , *dynamic name system*|
|67                 |DHCP , *dynamic host configuration protocol*|
|80                 |HTTP , *hypertext transfer protocol*|
|109,110            |POP , *post office protocol*|
|143/220            |IMAP , *internet message access protocol*|
|389                |LDAP , *lightweigth directory access protocol*|
|443                |HTTPS|
|2049               |NFS , *network file system*|

## INTERNET SERVICES

- web servers
    + **Apache**: Flexible, battle-tested, good for legacy apps and shared hosting environments.
    + **Nginx**: High-performance, low-resource, ideal for static content, reverse proxy, and modern scalable setups.
    + **Lighttpd**: Extremely lightweight, great for limited-resource systems or embedded environments.

- databse servers
    + **Postgress** : A relational database that supports full SQL, ideal for **complex queries** and **data integrity**, with high performance for **write-heavy workloads**.
    + **MySQL** : A relational database optimized for **fast, read-heavy** web applications, and known for its ease of use.
    + **MongoDB** : A **flexible NoSQL** database using **BSON documents**, well-suited for **unstructured or big data**, using its own query language (**MQL**).

- email services
    + linux uses several programs to handle email services.
        - **MTA** *mail transfer agent* Transfers email messages from one server to another.
            + sendmail
            + postfix
            + exim

        - **MDA** *mail delivery agent* Delivers email from the MTA to the user’s mailbox.
            + binmail
            + procmail
        
        - **MUA** *mail user agent* The program the user interacts with to read, write, and send emails.
            + mail
            + alpine
            + Thunderbird (GUI)

```plaintext
1. [User writes email in MUA]
              ↓
2. [MUA sends email to MTA via SMTP]
              ↓
3. [MTA transfers email to recipient's MTA]
              ↓
4. [Recipient's MTA hands off email to MDA]
              ↓
5. [MDA delivers email to recipient's local mailbox]
              ↓
6. [Recipient reads email using MUA]

# MDA does not send emails.
# it receives email from the MTA and stores it in the recipient’s mailbox.
# ie : /var/mail/user or ~/Maildir
```

## LOCAL SERVICES

- file servers
    + peer-to-peer
        * IPFS
        * bittorrent
    + client-server
        * **NFS** : network file system protocol **nfs-utils**.
        * **samba** : opensource implementation of Microsoft's system message block protocol.

- print servers
    + CUPS (*common unix printing system*) uses IPP (*internet printing protocol*)

- network resource servers
    + IP addresses
        * server packages
            - dhcpd
        * client packages
            - dhclient
            - dhcpcd
            - pump
    
    + Logging servers
        * rsyslogd : *accept logging data from remote servers*
        * journald : *accept local and remote system logging data*

    + name servers
        * named : in *BIND software package* provide dns services
    
    + network management
        * net-snmp
            - SNMP    - simple auth / plain text
            - SNMPv2  - more secure / bulk data transfer
            - SNMPv3  - strong auth / encryption
    
    + time servers
        * ntpd : *use NTP network time protocol*

## SECURITY SERVICES

+ authentication
    - nis-utils : *use NIS (network information system)*
    - kerberos : *use symetric key cyptography*
    - openLDAP : *use LDAP (lightweight directory access protocol)*
    - openSSL : *use certificates to authenticate*
        * certificate issued by certificate authority is an encrypted key to implement two factor security
            + something possesed - certificate file
            + something known - pin
    - openSSH : *use SSH secure shell*
        * openSSH supports tunneling allowing encrypting any insecure network transaction (ie, Telnet)
    - openVPN : *use VPN virtual private network protocol*

+ proxy
    - squid : *filter and caching service*
    - nginX

+ monitoring
    - Nagios software package : *use SNMP*

## PERFORMANCE

+ clustering : *connecting multiple servers together to act as a single system*
    - Beowulf
    - Apache Hadoop
    - Linux Virtual server

+ load balancing : *directing client request to one of the servers in a cluster*
    - HAProxy
    - Linux Virtual server
    - nginX

+ containers : *mirror development environment in the production server*
    - docker
    - kubernetes
