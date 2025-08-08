# NETWORK CONFIGURATION

Information required for a network connection,

1. Host IP Address
2. System Hostname
3. Network Subnet Address
4. Gateway Address
5. DNS server Address

this information can be modified using,

- **config files**
- **cli**
- **graphical tools**

## CONFIG FILES

|Component      |File Location|
|-              |-|
|Interfaces     |(debian) --> **/etc/network/interfaces**<br>(RedHat) --> **/etc/sysconfig/network-scripts**<br>(opensuse) --> **/etc/sysconfig/network**|
|Hostname       |**/etc/hostname**<br>**/etc/HOSTNAME**|
|DNS            |**/etc/resolv.conf**|

***INTERFACES***

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

***DNS***

    domain mydomain.com
    search mytest.com
    nameserver 192.168.1.1

> domain will be appended to the hostname.\
> search define additional domains to look for hostnames.

## CLI

### NETWORK MANAGER

**`nmtui `**\
--> text based menu cli tool.

**`nmcli `**`[option] [object] [command] [argument+++]`\
--> text only cli tool.

**`nm-connection-editor`**\
--> graphical tool.

### IPROUTE2 UTILITIES

**`ip `**`[option] [object] [command] [argument+++]`\
--> managing networking aspects.

***OBJECTS***

| **Object**        | **Abbreviation**            | **Description / Usage**|
|-                  |-                            |-|
| **link**          |**l**                        | Manage **network interfaces**: show, set up/down, change MTU, rename, etc|
| **address**       |**a**<br>**addr**            | Manage **IP addresses**: add, delete, or view IPs on interfaces|
| **route**         |**r**                        | Manage **routing tables**: add/delete routes, show current route table|
| **neighbour**     |**n**<br>**neigh**           | Manage **neighbor table** (ARP/NDP): add/delete/view ARP entries|
| **rule**          |                             | Manage **policy routing rules**: define source-based or custom routing rules|
| **maddress**      |**maddr**                    | Manage **multicast addresses** on interfaces|
| **mroute**        |                             | Show/manipulate **multicast routing cache**|
| **mrule**         |                             | Show/manage **multicast routing policy rules**|
| **tunnel**        |                             | Manage **tunnels** (GRE, IPIP, SIT): used for VPNs, encapsulation|
| **addresslabel**  |**addrl**<br>**addrlabel**   | Manage **labels** for IP addresses, for source address selection|
| **xfrm**          |                             | Manage **IPsec state/policy**: security associations, encryption policies|
| **netns**         |                             | Manage **network namespaces**: isolated network stacks, useful in containers|
| **l2tp**          |                             | Manage **L2TP tunnels and sessions**|
| **tcp_metrics**   |                             | View/manage **cached TCP metrics** for performance tuning|
| **nexthop**       |                             | Define/manage reusable **nexthop groups** for routes (multipath or fallback)|
| **monitor**       |                             | Monitor **live changes** to link, address, route, neighbor states in real-time|
| **tuntap**        |                             | Manage TUN/TAP virtual network kernel devices that are used for creating user-space network interfaces|
| **ntable**        |                             | Manage and inspect neighbor table parameters|

|Example                                                |Description|
|-                                                      |-|
|`$ ip route add default via 192.168.1.254 dev enp0s3`  |adds a default route (gateway) 192.168.1.254 via the enp0s3 interface to the system's routing table|
|`$ ip address add 10.0.2.15/24 dev enp0s3`             |assigns 10.0.2.15 IP address to enp0s3 network interface|
|`$ ip link set enp0s3 up`                              |activates the network interface named enp0s3|

### NET-TOOLS (Legacy)

---

**`ethtool `**`[option] [interface] [argument+++]`\
-->  Displays or configures **Ethernet device** settings ( speed, duplex mode, auto-negotiation, etc... ) for a network interface.

|Example                                                |Description|
|-                                                      |-|
|`$ ethtool enp0s3`                                     |display current config settings for **enp0s3**|
|`$ ethtool -s enp0s3 autoneg on speed 1000 duplex full`|set the **speed** at 1000Mb/s, the **duplex** mode to full and<br>the **auto-negotiation** to on for the enp0s3 device|

---

**`ifconfig `**`[interface] [option] [argument+++]`\
--> Displays or configures **network interfaces** ( IP address, netmask, and enabling/disabling interfaces ). *Deprecated*.

|Example                                                |Description|
|-                                                      |-|
|`$ ifconfig`                                           |display status of network interfaces|
|`$ ifconfig enp0s3 down 10.0.2.1 netmask 255.255.255.0`|set network address and subnet for a network interface|

> can be used for troubleshooting.
>```
>   $ ifconfig eth0
>   RX packets 123456 errors 0 dropped 5 overruns 0 frame 0
>   TX packets 654321 errors 0 dropped 2 overruns 0 carrier 0 collisions 0
>```

---

**`route `**`[option] [command] [argument+++]`\
--> Displays or modifies the IP routing table ( default gateway ). *deprecated*.

|Example                                    |Description|
|-                                          |-|
|`$ route`                                  |show kernal routing table|
|`$ route add 192.127.10.18 gw 192.168.1.2` |add route to 192.127.10.18 trough gateway 192.168.1.2|

---

**`iwconfig `**`[interface] [argument+++]`\
--> Configures wireless network interfaces ( SSID, mode, frequency, and encryption key ).

|Example                                            |Description|
|-                                                  |-|
|`$ iwconfig wlan0 essid "MyWifi" key s:pass123`    |**s:** is used to specify key in ASCII otherwise it has to be in Hex|

---

# ADDITIONAL NETWORK FEATURES

**DHCP**
--> automatically assigns IP addresses and other network configuration parameters.

|Program        |Purpose|
|-              |-|
|**dhcpd**      |server daemon — a background service that provides DHCP services on a network|
|**dhclient**   |DHCP client daemon in Linux and Unix-like systems|
|**pump**       |Lighter weight and simpler DHCP client daemon Often used in embedded systems|

---

**BONDING**
--> aggregate multiple interfaces in to one virtual device.

**bonding types,**
- load balancing - sharing the traffic between interfaces
- aggregation - interfaces are combined to create one large pipe
- active / passive - one is live while other is backup

**bonding modes,**

| Mode Number   | Mode Name             | Description|
|-              |-                      |-|
| 0             | **balance-rr**        | Load balancing and fault tolerance using interfaces in a round-robin approach.|
| 1             | **active-backup**     | One active interface and one backup interface; backup takes over if the active fails.|
| 2             | **balance-xor**       | Outgoing interface is selected based on a ahash of XOR of source and destination MAC addresses. recieving interface is selcted by the switch.|
| 3             | **broadcast**         | Transmit packets on all interfaces; provides fault tolerance but no load balancing.|
| 4             | **802.3ad**           | IEEE 802.3ad Dynamic Link Aggregation (LACP); aggregates multiple interfaces into one logical channel.|
| 5             | **balance-tlb**       | Adaptive transmit load balancing; balances outgoing traffic based on current load on each interface.|
| 6             | **balance-alb**       | Adaptive load balancing; includes balance-tlb plus receive load balancing (requires special driver support).|

|Example                                |Description|
|-                                      |-|
|`$ modprobe bonding`                   |load bonding module. creates **bond0** interface|
|`$ ip link add bond0 type bond mode 4` |define bond type|
|`$ ip link set eth0 master bond0`<br>`$ ip link set eth1 master bond0`|add **eth0** and **eth1** to the bond|

---

**NETCAT**
--> enables reading, writing, and transferring data across network connections using TCP or UDP.

**`netcat`** / **`nc`**

| Option    | Description|
|-          |-|
| **-4**   | Use only IPv4.|
| **-6**   | Use only IPv6.|
| **-C**   | Use carriage return and linefeed combination at the end of lines.|
| **-D**   | Enable socket debugging.|
| **-d**   | Do not read from STDIN.|
| **-h**   | Display help.|
| **-i**   | Delay interval between text sent and received.|
| **-k**   | Continue listening for incoming connections after the current one ends.|
| **-l**   | Listen for a connection instead of initiating one.|
| **-n**   | Do not use DNS lookups.|
| **-p**   | Specify the local port to use.|
| **-r**   | Use random source and/or destination ports.|
| **-S**   | Enable MD5 signature (on supported systems).|
| **-s**   | Specify the IP address of the interface to use for sending packets.|
| **-T**   | Specify the Type of Service (ToS) for the connection.|
| **-t**   | Reply to Telnet protocol options sent by the server.|
| **-U**   | Use Unix domain sockets instead of network sockets.|
| **-u**   | Use UDP instead of TCP.|
| **-v**   | Enable verbose mode.|
| **-w**   | Set timeout for inactive connections.|
| **-X**   | Use SOCKS or HTTP proxy server protocols.|
| **-z**   | Zero-I/O mode: used for scanning listening applications (no data sent).|

> **TEST HTTP SERVER**
>```
>    printf "GET / HTTP/1.0\r\n\r\n" | nc richblum.com 80
>```
> Recieve raw HTTP & HTML reponse.

> **SENDING / RECIEVING  TEXT**
>```
>    Robert :   nc -l 8000
>    Adam   :   nc Robert 8000
>    Adam   :   Hello           --> displayed on Robert screen
>    Robert :   Hi              --> displayed on Adam screen.
>```

> **SENDING FILES**
>```
>    system 1   :   `nc -l 8000 > file2`        --> listen for any incomming data and store in file1
>    system 2   :   `nc Robert 8000 < file1`    --> send file2 to host:Robert port:8000
>```

---

## NETWORK TROUBLESHOOTING

**CONNECTIVITY**

**`ping` / `ping6 `**\
--> test connectivity between devices over a network using **ICMP** / **ICMPv6** Echo Request.

|Example                                        |Description|
|-                                              |-|
|`$ ping -c 4 google.com`                       |**-c** option to limit the number of operations or **CTRL+C** to terminate|
|`$ ping6 fe80::c418:2ed0:aead:cbcd:%enp0s3`    |in the ping6 command the **outgoing interface** must be specified|

---

**`traceroute`**\
--> track the path that packets take from source to a destination host.

|Example                    |Description|
|-                          |-|
|`$ traceroute google.com`  |detect hop count, hostname/IP of each router, three round-trip times (in milliseconds) for each router|

---

**`mtr`**\
--> network diagnostic tool that combines the functionality of both ping and traceroute.

|Example            |Description|
|-                  |-|
|`$ mtr google.com` |show packet stats, time stats, etc...|

---

**NAME RESOLUTION**

**`host`**\
--> translates domain names into IP addresses (**forward lookup**) and IP addresses into domain names (**reverse lookup**).

|Example                    |Description|
|-                          |-|
|`$ host www.linux.org`     |return all IP addresses associated with the host name, on the DNS server|
|`$ host 98.138.219.231`    |attempt to find the host name for given the IP address|

---

**`dig`**\
--> query DNS servers and troubleshoot DNS issues.

|Example                    |Description|
|-                          |-|
|`$ dig www.linux.org`      |display all DNS records and information for a given host name|
|`$ dig www.linux.org MX`   |display all **Mail Service** related DNS records for the host name|

---

**`nslookup`**\
--> interactive prompt, query DNS servers for information about hostnames, IP addresses, and other DNS records.

    $ nslookup

    > www.google.com
    server :    192.168.1.254
    address :   192.168.1.254#53

    > www.linux.org
    server :    ...
    address :   ...

---

**`whois`**\
--> retrieve domain registration and ownership information from **WHOIS** databases.

|Example                    |Description|
|-                          |-|
|`$ whois linux.com`        |connect to **centralized internet domain registry** to request information|

---

**ADVANCED NETWORK TROUBLESHOOTING**

**`netstat`**`[option]`\
--> display network connections, routing tables, interface statistics, and networking protocol details. *(depricated)*.

|Option     |Description|
|-          |-|
|**-t**     |list all open tcp connections|
|**-u**     |list all open udp connections|
|**-l**     |list applications and ports they are listening to|
|**-s**     |display stats for different types of packets used by the system (IP, Icmp, Tcp, Udp, IpExt, ...)|

---

**EXAMINING SOCKETS**

**`ss `**`[option]`\
--> faster replacement for **netstat**, used to display detailed information about network sockets.

| Option    | Description|
|-          |-|
| **-4**    | Display only IPv4 sockets|
| **-6**    | Display only IPv6 sockets|
| **-t**    | List TCP sockets|
| **-u**    | List UDP sockets|
| **-l**    | List listening sockets|
| **-e**    | Show detailed information, including user|
| **-n**    | Show numerical addresses instead of resolving names|
| **-r**    | Display routing table|
| **-s**    | Show summary statistics|
| **-p**    | Show process using the socket|

---

**MONITORING THE NETWORK**

**`tcpdump`**\
--> capture and inspect network traffic,  rudementary decoding of packets, basic filters for hosts, clients, network sessions.

---

**`wireshark`**\
--> network protocol analyzer with a graphical user interface.

---

**`tshark`**\
--> cli for wireshark.
