# NETWORK CONFIGURATION

Information required for a network connection,

1. Host Address
2. Network Subnet Address
3. Gateway Address
4. System Hostname
5. DNS server Address

this information can be modified using,

- config files
- cli
- graphical tools

## CONFIG FILES

### INTERFACES

+ locations
    - debian      > /etc/network/interfaces
    - redHat      > /etc/sysconfig/network-scripts
    - opensuse    > /etc/sysconfig/network

.

    auto eth0
    iface eth0 inet static
        address 192.168.1.77
        netmask 255.255.255.0
        gateway 192.168.1.254
    iface eth0 inet6 static
        address 2003::aef0:a10:a1
        netmask 64
        gateway 2003::aef0:a10:1

    auto eth1
    iface eth1 inet dhcp
    iface eth1 inet6 auto

according to above configuration,

> the eth0 interface is set with static addresses\
> the eth1 ipv4 address is set by a dhcp server\
> the eth1 ipv6 address is set by linux as a link local address (no internet access)

### HOSTNAME

- /etc/hostname
- /etc/HOSTNAME

### DNS

- /etc/resolv.conf

.

    domain mydomain.com
    search mytest.com
    nameserver 192.168.1.1

> domain will be appended to the hostname.\
> search define additional domains to look for hostnames.

## CLI

### NETWORK MANAGER

- nmtui > text based menu cli tool
- nmcli > text only cli tool
- graphical tool

### IPROUTE2 UTILITIES

#### ip

    address     display IPV4/6 addresses on the device
    addrlabel   define configuration labels
    l2tp        tunnel ethernet over ip
    link        define a network device
    maddress    define a multicast address for the system to listen to
    monitor     listen for netlink messages
    mroute      define an entry on the multicast routing cache
    mrule       define a rule on the multicast routing policy db
    neighbor    manage ARP or NDISC cache entries
    netns       manage network namespaces
    ntable      manage neighbor cache operation
    route       manage routing table
    rule        manage entrie in routing policy db
    tcpmetrics  manage TCP metrics on the interface
    token       manage tokenized interface identifiers
    tunnel      tnnel over ip
    tuntap      manage TUN/TAP devices
    xfrm        manage IPSec policies 

`$ ip route add default via 192.168.1.254 dev enp0s3`\
`$ ip address add 10.0.2.15/24 dev enp0s3`\
`$ ip link set enp0s3 up`

### NET-TOOLS (Legacy)

1. ethtools - ethernet settings for a network interface.
2. ifconfig - IP address and netmask for a network interface
3. iwconfig - SSID and key for a wireless interface
4. route - default router address

#### ethtool

`$ ethtool enp0s3`\
display current config settings for **enp0s3**

`$ ethtool -s enp0s3 autoneg on speed 1000 duplex full`\
sets the **speed** at 1000Mb/s, the **duplex** mode to full and the **auto-negotiation** to on for the enp0s3 device.

#### ifconfig

`$ ifconfig`\
display status of network interfaces.

`$ ifconfig enp0s3 down 10.0.2.1 netmask 255.255.255.0`\
set network address and subnet for a network interface.

can be used to monitor dropped packets , collisions , etc for troubleshooting.

#### route

`$ route`\
show kernal routing table.

`$ route add 192.127.10.18 gw 192.168.1.2`\
add route to 192.127.10.18 trough gateway 192.168.1.2.

#### iwconfig

`$ iwconfig wlan0 essid "MyWifi" key s:pass123`\
s: is used to specify key in ASCII otherwise it has to be in Hex.

## ADDITIONAL NETWORK FEATURES

### DHCP

1. dhcpd
2. dhclient
3. pump

### BONDING

aggregate multiple interfaces in to one virtual device.

bonding types,
- load balancing - sharing the traffic between interfaces
- aggregation - interfaces are combined to create one large pipe
- active / passive - one is live while other is backup

bonding modes,

    0   balance-rr      load balancing and fault tolerance using interfaces in a round robin approch.
    1   active-backup   one primary, one backup.
    2   balance-xor     load balancing and fault tolerance using seperate interfaces to recieve and transmit.
    3   broadcast       transmit on all interfaces.
    4   802.3ad         aggregate all to one connection.
    5   balance-tlb     load balancing and fault tolerance using interfaces based on current transmit load on each interface.
    6   balance-alb     load balancing and fault tolerance using interfaces based on current recieve load on each interface.

`$ modprobe bonding` > load bonding module. creates **bond0** interface.

`$ ip link add bond0 type bond mode 4` > define bond type.

`$ ip link set eth0 master bond0` / `$ ip link set eth1 master bond0` > add **eth0** and **eth1** to the bond.

### NETCAT

**netcat** can act as a server or client to send / recieve packets using TCP (default) or UDP.

can be **'netcat'** or **'nc'** depending on the distro.

    -4      only use IPv4.
    -6      only use IPv6.
    -C      use carriage return & linefeed combo at the end of lines.
    -D      enable socket debugging.
    -d      do not read from STDIN.
    -h      help.
    -i      delay interval between text sent and recieved.
    -k      continue listning for incomming connections after current connection terminates.
    -l      listen for a connection instead of initializing one.
    -n      do not use DNS lookups.
    -p      specifies the port used.
    -r      use random source and / or destination port.
    -S      enable MD5 signature.
    -s      specify IP address of interface used for sending packets.
    -T      specify ToS (type of service) used for connection.
    -t      reply to telnet protocol options sent from servers.
    -U      use Unix domain sockets instead of network sockets.
    -u      use UDP instead of TCP.
    -v      enable verbose mode.
    -w      timeout for inactive conections.
    -X      use SOCK or HTTP proxy server protocols.
    -z      scan for listning applications.


`$ printf "GET / HTTP/1.0\r\n\r\n" | nc richblum.com 80`\
get HTTP and HTML reponse.

> **SENDING TEXT**\
> system 1 :  nc -l 8000 \
> system 2 :  nc *TargetHostName* 8000 \
> any text typed into system 2 will display on system 1.


> **SENDING FILES**\
> system 1 : nc -l 8000 > recievedfile.txt\
> system 2 : nc *TargetHostName* 8000 < sentfile.txt\
> system 1 will recieve the file.

## NETWORK TROUBLESHOOTING

### CONNECTIVITY

> ping / ping6

ping sends ICMP packets to the remote host.

`$ ping -c 4 google.com` < **-c** option to limit the operations or **CTRL+c** to terminate\
`$ ping6 fe80::c418:2ed0:aead:cbcd:%enp0s3` **< in the ping6 command the outgoing interface must be specified**

> traceroute

use ICMP packets to trace the hops to the remote host.

#### MTR

mtr combine ping and traceroute to document network connectivity.

### NAME RESOLUTION

> host

`$ host www.linux.org`

return all IP addresses associated with the host name, on the DNS server.

`$ host 98.138.219.231`

attempt to find the host name for given the IP address.

> dig

`$ dig www.linux.org`

display all DNS records and information for a given host name.

`$ dig www.linux.org MX`

display all **Mail Service** related DNS records for the host name.

> nslookup

`$ nslookup`

    > www.google.com
    server :    192.168.1.254
    address :   192.168.1.254#53

    > www.linux.org
    server :    ...
    address :   ...


provide an interactive prompt to look up information on multiple domains.

> whois

`$ whois linux.com`

attempts to connect to **centralized internet domain registry** to request information on who registered the domain name.

### ADVANCED NETWORK TROUBLESHOOTING

> netstat

list all open network connections.

`$ netstat -u` < list all UDP connections

    -t      list all open tcp connections.
    -u      list all open udp connections.
    -l      list applications and ports they are listening to.
    -s      display stats for different types of packets used by the system (IP, Icmp, Tcp, Udp, IpExt, ...). 


### EXAMINING SOCKETS

> ss

display a list of all active sockets.

`$ ss -anpt` < display established listning connections and associated processes.

    -t      list tcp sockets.
    -u      list udp sockets.
    -l      list listening sockets.
    -e      detailed info including user.
    -n      show numerical addresses instead of resolving.
    -r      show routing table.
    -s      summary stats.
    -4      ipv4 sockets
    -6      ipv6 sockets.
    -p      show process info.
    -P      show process stats.

### MONITORING THE NETWORK

> tcpdump

legacy tool for capturing and rudementary decoding of packets.\
has basic filters for hosts, clients, network sessions.

> wireshark

graphical tool for advanced network ananlysis.

> tshark

wireshark cli.