# 🔹 Scenarios 1

## **Scenario 1: Troubleshooting a Slow Web Application**

Amir is a sysadmin. Users complain the company’s internal CRM (hosted at `crm.corp.local`) is very slow.

1. He first sends a fixed number of echo requests to check packet loss.
2. Then he traces the network path to the CRM server, first with hostname resolution and then without.
3. To double-check, he uses another UDP-based tracing tool with hostname resolution disabled.
4. Finally, he runs a real-time combined ping+traceroute utility in report mode, making it loop for a defined cycle count. He adjusts output to show packet loss and jitter.

---

## **Scenario 2: Bringing a Network Interface Back Online**

Sofia is migrating a server into a new VLAN.

1. She disables the primary Ethernet interface, then re-enables it.
2. She assigns a new static IP and netmask.
3. She confirms the new IP settings using a modern alternative to the legacy command.
4. She checks the Ethernet device speed and duplex settings, then configures it to 1Gbps full duplex.

---

## **Scenario 3: Wi-Fi Debugging on a Fedora Laptop**

Ken is helping a remote worker whose Fedora 34 laptop refuses to connect to Wi-Fi.

1. He configures the wireless interface to connect to the office SSID.
2. The user provides a WEP key, which he also sets on the interface.
3. After that, he monitors the interface state with the new IP management command.

---

## **Scenario 4: Monitoring Changes in the Network Environment**

Priya needs to investigate sudden changes in neighbor relationships on a cluster of servers.

1. She opens a continuous monitor to watch all events related to IP addresses, routes, and neighbor tables.
2. On another console, she inspects the ARP/neighbor cache.
3. She also inspects the system routing table and confirms routes.

---

# 🔹 Answer Keys 1

## Scenario 1

```bash
ping -c 5 crm.corp.local
traceroute crm.corp.local
traceroute -n crm.corp.local
tracepath -n crm.corp.local
mtr -r -c 10 -n -o LJA crm.corp.local
```

## Scenario 2

```bash
ifconfig eth0 down
ifconfig eth0 up
ifconfig eth0 inet 192.168.50.20 netmask 255.255.255.0
ip addr show eth0
ethtool -s eth0 speed 1000 duplex full autoneg off
```

## Scenario 3

```bash
iwconfig wlan0 essid "OfficeWiFi"
iwconfig wlan0 key s:1234567890
ip link show wlan0
```

## Scenario 4

```bash
ip monitor all
ip neigh show
ip route show
```

---
---
---

# 🔹 Scenarios 2

## **Scenario 5: Blocking Malicious IPs with Dynamic Sets**

Nina notices repeated brute-force login attempts from multiple IP addresses on her RockyOS 8.5 server.

1. She creates a new IP set named `blacklist`.
2. She adds two suspicious IPs into it.
3. She lists the set to confirm.
4. Later, one IP is cleared, and eventually, she removes the entire set.

---

## **Scenario 6: Legacy Firewall Rules for a Web Server**

A legacy system running Ubuntu 20.04 still uses the old firewall tool. Ahmed needs to secure it:

1. He appends a rule to drop all incoming traffic from a known attacker’s IP.
2. He inserts a rule at the top to allow HTTP traffic to port 80.
3. He deletes an outdated rule that allowed Telnet.
4. He replaces an existing rule to instead allow HTTPS on port 443.
5. He lists all current rules and policies.
6. Finally, he flushes all rules, resets the INPUT chain to DROP by default, then shows the rule syntax.

---

## **Scenario 7: Modern Firewall Using nftables**

On Fedora 34, Lina configures packet filtering using nftables:

1. She creates a new `inet` family table named `filter`.
2. She adds an `input` chain that drops packets by default.
3. She inserts a rule to accept SSH traffic.
4. She adds a rule to reject all incoming ICMP echo requests.
5. She lists the active ruleset.
6. Later, she clears all rules in the table.

---

## **Scenario 8: firewalld for Zones and Services**

Carlos is managing a Fedora server with `firewalld`.

1. He checks what zones exist.
2. He verifies the default zone and then changes it to `home`.
3. He checks currently active zones.
4. He lists all services available and allows `http`.
5. He verifies that `http` is active in the allowed list.
6. A DDoS alert occurs, so he turns on panic mode, then disables it.
7. Once satisfied, he saves the runtime changes permanently.

---

## **Scenario 9: Simplifying Firewall Rules with UFW**

An Ubuntu sysadmin, Farah, configures firewall rules the easy way:

1. She enables the firewall, then sets the default inbound policy to deny.
2. She allows SSH, HTTP, and HTTPS traffic.
3. She denies Telnet explicitly.
4. She inserts a rule before others to reject all traffic from a certain subnet.
5. She reloads the firewall to apply changes.
6. She checks logging, resets the firewall completely, and then disables it.

---

# 🔹 Answer Keys 2

## Scenario 5

```bash
ipset create blacklist hash:ip
ipset add blacklist 192.168.1.50
ipset add blacklist 203.0.113.25
ipset list blacklist
ipset del blacklist 192.168.1.50
ipset destroy blacklist
```

## Scenario 6

```bash
iptables -A INPUT -s 203.0.113.100 -j DROP
iptables -I INPUT -p tcp --dport 80 -j ACCEPT
iptables -D INPUT -p tcp --dport 23 -j ACCEPT
iptables -R INPUT 1 -p tcp --dport 443 -j ACCEPT
iptables -L -v
iptables -F
iptables -P INPUT DROP
iptables -S
```

## Scenario 7

```bash
nft add table inet filter
nft add chain inet filter input { type filter hook input priority 0 \; policy drop \; }
nft add rule inet filter input tcp dport 22 accept
nft add rule inet filter input icmp type echo-request drop
nft list ruleset
nft flush table inet filter
```

## Scenario 8

```bash
firewall-cmd --get-zones
firewall-cmd --get-default-zone
firewall-cmd --set-default-zone=home
firewall-cmd --get-active-zones
firewall-cmd --get-services
firewall-cmd --add-service=http
firewall-cmd --list-services
firewall-cmd --panic-on
firewall-cmd --panic-off
firewall-cmd --runtime-to-permanent
```

## Scenario 9

```bash
ufw enable
ufw default deny incoming
ufw allow ssh
ufw allow http
ufw allow https
ufw deny telnet
ufw insert 1 deny from 192.168.10.0/24
ufw reload
ufw logging on
ufw reset
ufw disable
```

---
---
---

# 🔹 Scenarios 3

## **Scenario 10: Investigating Server Connections**

On RockyOS 8.5, Mark is asked to check what services are running on a production server.

1. He lists all TCP listening sockets with process info and numeric ports.
2. He also inspects UDP listening sockets.
3. He summarizes socket statistics.
4. He double-checks using the deprecated tool to list TCP and UDP listening ports, plus general statistics.

---

## **Scenario 11: Debugging DNS Resolution Issues**

Alice suspects DNS problems on an Ubuntu 20.04 workstation.

1. She queries the A record of `openai.com` using a DNS utility.
2. She requests all record types for `openai.com` with another lookup tool.
3. She uses another command to query MX records, specifying Google’s DNS server.
4. She checks who owns the domain via WHOIS.

---

## **Scenario 12: Secure File Transfer Between Servers**

Diego needs to move a web application’s files from the dev server to the staging server.

1. He securely copies the entire `/var/www` directory recursively while preserving timestamps.
2. He copies a large tarball with compression enabled to speed up transfer.
3. Then he logs in via secure FTP, lists the remote files, changes to a specific remote directory, and uploads a new config file.
4. After that, he switches to his local directory, resumes an interrupted upload, and exits.

---

## **Scenario 13: Remote Access with SSH**

On Fedora 34, Mei needs to test SSH connectivity:

1. She connects to a remote server on a custom port.
2. She forwards a local port to a remote web service.
3. She sets up a remote port forward so external users can reach her local service.
4. She enables X11 forwarding for running GUI apps remotely.

---

## **Scenario 14: Setting Up SSH Keys for Passwordless Login**

Before automating deployments, Ravi must configure SSH key-based authentication.

1. He generates a new RSA keypair and saves it to a custom filename.
2. He performs a dry-run test before copying the public key to a server.
3. Finally, he installs the public key to the remote host for real use.

---
---
---

# 🔹 Answer Keys 3

## Scenario 10

```bash
ss -t -l -n -p
ss -u -l -n
ss -s
netstat -tul
netstat -s
```

## Scenario 11

```bash
dig openai.com
host -a openai.com
nslookup
> server 8.8.8.8
> set type=MX
> openai.com
whois openai.com
```

## Scenario 12

```bash
scp -r -p user@dev:/var/www user@staging:/var/www
scp -C user@dev:/tmp/app.tar.gz user@staging:/tmp/
sftp user@staging
sftp> ls
sftp> cd /var/www/html
sftp> put config.php
sftp> lcd /home/diego/projects
sftp> reput app.tar.gz
sftp> bye
```

## Scenario 13

```bash
ssh -p 2222 user@remote-server
ssh -L 8080:127.0.0.1:80 user@remote-server
ssh -R 9090:127.0.0.1:3000 user@remote-server
ssh -X user@remote-server
```

## Scenario 14

```bash
ssh-keygen -t rsa -f ~/.ssh/id_rsa_deploy
ssh-copy-id -n user@remote-server
ssh-copy-id user@remote-server
```

---
---
---

# 🔹 Scenarios 4

## **Scenario 15: Capturing Suspicious Traffic**

On Ubuntu 20.04, Laila notices strange outbound connections from her server.

1. She lists all available interfaces.
2. She captures 20 packets from the `eth0` interface and saves them to a file.
3. Later, she reads the saved capture file.
4. For more detail, she repeats the capture with an alternate tool and writes to another file.

---

## **Scenario 16: Monitoring Bandwidth Usage**

During peak business hours, Omar suspects a backup process is consuming too much bandwidth.

1. He runs a live per-host bandwidth monitor on `eth0`.
2. To confirm, he starts a bandwidth test using one server in `server mode`.
3. On another machine, he runs the test as a client for 30 seconds.
4. He re-runs the client test in bidirectional mode with 4 parallel streams, enabling enhanced output and interval reporting.

---

## **Scenario 17: Testing Network Services with Netcat**

Samira is debugging a custom TCP service.

1. She starts a listener on port 5000 that keeps accepting connections.
2. She connects to that listener using IPv4, enabling debug output.
3. She repeats the test using UDP with a timeout.
4. She performs a quick zero-I/O port scan on another server across a range of ports.

---

## **Scenario 18: Updating Routing Table for a VPN Connection**

Hassan configures a temporary VPN connection.

1. He adds a route so all traffic destined for `10.10.0.0/16` goes via `192.168.1.1`.
2. Later, when disconnecting, he deletes the route.
3. He inspects the current routing table to verify cleanup.

---

# 🔹 Answer Keys 4

## Scenario 15

```bash
tcpdump -D
tcpdump -i eth0 -c 20 -w suspicious.pcap
tcpdump -r suspicious.pcap
tshark -i eth0 -c 20 -w suspicious2.pcap
```

## Scenario 16

```bash
iftop -i eth0
iperf -s
iperf -c 192.168.1.100 -t 30
iperf -c 192.168.1.100 -d -P 4 -e -i 2
```

## Scenario 17

```bash
nc -l -k -p 5000
nc -4 -D 127.0.0.1 5000
nc -u -w 5 127.0.0.1 5000
nc -z -v 192.168.1.50 20-100
```

## Scenario 18

```bash
route add -net 10.10.0.0/16 gw 192.168.1.1
route del -net 10.10.0.0/16 gw 192.168.1.1
route -n
```

---
---
---

# 🔹 Scenarios 5

## **Scenario 19: Advanced Traceroutes for Latency Issues**

Olivia is troubleshooting slow access to a partner site.

1. She limits the max hops to 15.
2. She increases the number of queries per hop to 5 for more reliability.

---

## **Scenario 20: Policy Routing and Neighbor Tables**

Victor needs to configure routing policies for multiple ISPs.

1. He lists existing policy rules.
2. He adds a new rule preferring packets from `192.168.100.0/24` to use a custom table.
3. He inspects neighbor tables to check ARP cache structures.

---

## **Scenario 21: More iptables Rules for Mail Server Security**

An old mail server still uses legacy iptables.

1. He creates a rule in the `nat` table to redirect traffic.
2. He appends a rule to reject all SMTP traffic from a specific destination.
3. He inserts a rule for a specific input interface.
4. He adds a rule for output traffic on a specific interface.
5. He restricts connections to port 25 by **source port**.
6. He adds another by **destination IP**.

---

## **Scenario 22: nftables in Multi-Protocol Setup**

Emma configures nftables for dual-stack IPv4/IPv6 NAT.

1. She adds a new IPv4 NAT table and a `prerouting` chain.
2. She adds an IPv6 table with an `output` chain.
3. She inserts a rule in `forward` to accept all.
4. She later deletes a rule.
5. She adds a postrouting chain dropping traffic.

---

## **Scenario 23: Permanent Firewalld Configs**

Felix ensures firewall settings persist across reboots.

1. He adds HTTPS permanently.
2. He reloads firewalld to apply.

---

## **Scenario 24: UFW Advanced Rules**

Ella configures UFW on Ubuntu for strict outbound/inbound control.

1. She checks current status.
2. She deletes an old SSH rule.
3. She allows traffic only for a specific application profile.
4. She sets the default policy for outgoing traffic to allow, but routed traffic to deny.
5. She creates a granular rule: allow TCP traffic from `192.168.5.10` to `10.0.0.20` on port 8080 via interface `eth1`.

---

## **Scenario 25: Extra Socket Info with ss**

Rahul inspects detailed socket stats.

1. He shows TCP sockets with extra info.
2. He prints routing info.
3. He lists IPv4-only sockets.
4. Then he lists IPv6-only sockets.

---

## **Scenario 26: More sftp Commands**

Maria manages backups with sftp.

1. She downloads a file.
2. She lists local files.
3. She creates a new remote directory.

---

## **Scenario 27: Bandwidth Control with iperf**

Noah wants to simulate 50Mbps upload speed.

1. He runs iperf in client mode with a bandwidth limit.

---

## **Scenario 28: Netcat Deep Dive**

Sophia tests a variety of nc options.

1. She connects to a host using IPv6.
2. She tests CRLF line endings.
3. She runs in background detaching stdin.
4. She delays sending packets by 2 seconds.
5. She forces numeric IP resolution.
6. She randomizes local ports when connecting.
7. She binds to a custom source IP.
8. She sets custom TCP options.
9. She sets a Type of Service field.
10. She listens on a UNIX socket.

---

---

# 🔹 Answer Keys 5

## Scenario 19

```bash
traceroute -m 15 example.com
traceroute -q 5 example.com
```

## Scenario 20

```bash
ip rule show
ip rule add from 192.168.100.0/24 table 100
ip ntable show
```

## Scenario 21

```bash
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
iptables -A INPUT -d 203.0.113.50 -p tcp --dport 25 -j REJECT
iptables -I INPUT -i eth0 -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -o eth1 -p tcp --dport 443 -j ACCEPT
iptables -A INPUT -p tcp --sport 1025 --dport 25 -j DROP
iptables -A INPUT -d 198.51.100.10 -p tcp --dport 25 -j DROP
```

## Scenario 22

```bash
nft add table ip nat
nft add chain ip nat prerouting { type nat hook prerouting priority 0 \; }
nft add table ip6 filter
nft add chain ip6 filter output { type filter hook output priority 0 \; }
nft add rule inet filter forward accept
nft delete rule inet filter input handle 1
nft add chain ip nat postrouting { type nat hook postrouting priority 100 \; policy drop \; }
```

## Scenario 23

```bash
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
```

## Scenario 24

```bash
ufw status
ufw delete allow ssh
ufw allow app "Apache"
ufw default allow outgoing
ufw default deny routed
ufw allow in on eth1 proto tcp from 192.168.5.10 to 10.0.0.20 port 8080
```

## Scenario 25

```bash
ss -t -e
ss -r
ss -4
ss -6
```

## Scenario 26

```bash
sftp user@backup
sftp> get backup.tar.gz
sftp> lls
sftp> mkdir old_backups
```

## Scenario 27

```bash
iperf -c 192.168.1.50 -b 50M
```

## Scenario 28

```bash
nc -6 remotehost 8080
nc -C remotehost 80
nc -d remotehost 22
nc -i 2 remotehost 443
nc -n remotehost 21
nc -r -p 0 remotehost 25
nc -s 192.168.1.20 remotehost 22
nc -S 4 remotehost 80
nc -T 0x10 remotehost 80
nc -U /tmp/mysocket
```

---