# SERVICES

2 types of services
    background process > running always > daemon > often end with letter "d"
    child process > spawned as a result of a request by a parent program

super servers > listen for user requests and spawn services accordingly
    ie. "inetd" > internet daemon for Linux
        "xinetd" > additional features (ACLs, schedule services, advanced loggin)

ip > reach the server / port > reach the services

well known ports >>
    - 20/21   = FTP > *file transfer protocol*
    - 22      = SSH > *secure shell protocol*
    - 23      = Telnet
    - 25      = SMTP > *simple mail transport protocol*
    - 53      = DNS > *dynamic name system*
    - 67      = DHCP > *dynamic host configuration protocol*
    - 80      = HTTP > *hypertext transfer protocol*
    - 109>110 = POP > *post office protocol*
    - 143/220 = IMAP > *internet message access protocol*
    - 389     = LDAP > *lightweigth directory access protocol*
    - 443     = HTTPS
    - 2049    = NFS > *network file system*

## INTERNET SERVICES

    - web services
        + Apache server
        + nginX server
        + lighttpd server / package

    - databse services
        + PostgreSQL server
        + MySQL server
        + MongoDB server > NoSQL > dafault no security

    - email services
        + linux uses multiple small programs to handle email services

            - MTA *mail transfer agent* determine destination host
                + sendmail
                + postfix
                + exim

            - MDA *mail delivery agent* deliver messages to local users
                + binmail
                + procmail
            
            - MUA *mail user agent*

## LOCAL SERVICES

    file servers
        peer-to-peer
        client-server

        - NFS *network file system protocol* **nfs-utils**
        - samba *opensource implementation of Mincrosoft's system message block protocol*
    
    print servers
        - CUPS *common unix printing system* uses IPP *internet printing protocol*
    
    network resource servers
        IP addresses
            server packages
                - dhcpd
            client packages
                - dhclient
                - dhcpcd
                - pump
        
        Logging servers
            - rsyslogd *accept logging data from remote servers*
            - journald *accept local and remote system logging data*

        name servers
            - named *BIND software package* provide dns services
        
        network management
            - net-snmp *SNMPv3 compatible*
            
            SNMP    - simple auth / plain text
            SNMPv2  - more secure / bulk data transfer
            SNMPv3  - strong auth / encryption
        
        time servers
            - ntpd *uses NTP network time protocol*

## SECURITY SERVICES

    authentication
        - nis-utils *uses NIS network information system*
        - kerberos *uses symetric key cyptography*
        - openLDAP *uses LDAP lightweight directory access protocol*
        - openSSL *uses certificates to authenticate*
            certificate is an encrypted key to implement two factor security
                + something possesed - certificate file
                + something known - pin
            certificate authority issues the certificate
        - openSSH *uses SSH secure shell*
            openSSH supports a fearture called tunneling allowing encrypting any network transaction
            which helps to encrypt insecure traffic even when the network application does not support
            encryption directly. ie, Telnet
        - openVPN *uses VPN virtual private network protocol*
    
    proxy
        - squid *filter and caching service*
        - nginX
    
    monitoring
        - Nagios software package *uses SNMP*

## PERFORMANCE

    clustering
        - Beowulf
        - Apache Hadoop
        - Linux Virtual server
    
    load balancing *direct client request to one of the servers in a cluster*
        - HAProxy
        - Linux Virtual server
        - nginX
    
    containers *mirror the development environment in a server with a self contained environment*
        - docker
        - kubernetes
