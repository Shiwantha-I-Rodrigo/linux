## **Story 1: Troubleshooting Network Connectivity**

**Scenario:** Users in your office report intermittent connectivity to an external website.

**Step 1 – Ping the host to check basic connectivity**

```bash
ping -c 4 google.com
```

* **Explanation:** Sends 4 ICMP echo requests to `google.com`. Helps verify if the host is reachable.
* **Outcome:** If packets are lost, there may be a network issue or firewall blockage.

**Step 2 – Trace the network path**

```bash
traceroute -n -m 20 google.com
```

* **Explanation:** Shows each hop to the destination without DNS resolution (`-n`) and limits hops to 20 (`-m 20`). Useful for identifying where packets are dropped.

**Step 3 – Real-time monitoring of path**

```bash
mtr -r -c 10 -n google.com
```

* **Explanation:** Runs `mtr` in report mode (`-r`), performs 10 cycles (`-c 10`), and avoids hostname resolution (`-n`). Combines ping + traceroute to spot packet loss over time.

**Step 4 – Check local network interface**

```bash
ip addr show
```

* **Explanation:** Confirms the interface has an IP and is UP. Replace with `ifconfig` on older systems: `ifconfig eth0`.

**Step 5 – Check routing table**

```bash
ip route show
```

* **Explanation:** Ensures packets have a proper default gateway and network route to the internet.

**Step 6 – Test firewall rules**

```bash
sudo ufw status
sudo iptables -L -n
```

* **Explanation:** Ensures no firewall rule is blocking traffic to `google.com`. `-n` avoids DNS resolution in iptables output.

---

## **Story 2: Secure File Transfer Between Servers**

**Scenario:** Move logs from a web server to a backup server.

**Step 1 – Copy files using SCP**

```bash
scp -r -C /var/log/webserver/ user@backup-server:/backup/webserver/
```

* **Explanation:** Recursive copy (`-r`) with compression (`-C`) preserves bandwidth and copies entire log directory.

**Step 2 – Verify SSH connectivity**

```bash
ssh user@backup-server
```

* **Explanation:** Ensure remote login works and network/firewall allows SSH.

**Step 3 – Use SFTP for directory sync**

```bash
sftp user@backup-server
sftp> put -r /var/log/webserver/ /backup/webserver/
sftp> bye
```

* **Explanation:** Alternative interactive transfer, useful if SCP fails intermittently.

**Step 4 – Automate key-based authentication**

```bash
ssh-keygen -t rsa -f ~/.ssh/id_rsa
ssh-copy-id user@backup-server
```

* **Explanation:** Generates an SSH key (`-t rsa`) and copies it to the remote host for passwordless authentication.

---

## **Story 3: Capturing Network Traffic for Analysis**

**Scenario:** Users report slow web application performance.

**Step 1 – Capture traffic on interface**

```bash
sudo tcpdump -i eth0 -c 1000 -w capture.pcap
```

* **Explanation:** Captures 1000 packets on `eth0` and writes to a file for analysis.

**Step 2 – Analyze traffic using tshark**

```bash
tshark -r capture.pcap
```

* **Explanation:** Reads the capture file and displays packet details. Can filter by protocol like `http` or `tcp.port==80`.

**Step 3 – Check bandwidth usage per host**

```bash
sudo iftop -i eth0
```

* **Explanation:** Real-time per-host bandwidth usage to identify heavy consumers.

---

## **Story 4: Firewall Configuration & Troubleshooting**

**Scenario:** Allow HTTP/HTTPS traffic but block all else.

**Step 1 – Check current zones and active rules (firewalld)**

```bash
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --list-services
```

**Step 2 – Allow web traffic**

```bash
sudo firewall-cmd --add-service=http --add-service=https --runtime-to-permanent
```

**Step 3 – Verify rules (UFW alternative)**

```bash
sudo ufw status
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

**Step 4 – Verify iptables/nftables**

```bash
sudo iptables -L -n -v
sudo nft list ruleset
```

* **Explanation:** Ensures rules are properly applied at the kernel level.

---

## **Story 5: Diagnosing DNS Resolution Issues**

**Scenario:** Users report that a web application cannot resolve hostnames.

**Step 1 – Check connectivity**

```bash
ping -c 3 8.8.8.8
```

* **Explanation:** Ensures network connectivity works; if this fails, the problem is likely lower-level than DNS.

**Step 2 – Test DNS resolution using `dig`**

```bash
dig example.com
```

* **Explanation:** Queries the DNS server for `example.com` A record. Shows response time, authoritative server, and IP resolution.

**Step 3 – Simple lookup with `host`**

```bash
host -a example.com
```

* **Explanation:** Displays all DNS information, including CNAME, MX, and NS records.

**Step 4 – Alternative lookup using `nslookup`**

```bash
nslookup example.com 8.8.8.8
```

* **Explanation:** Uses a specific DNS server (8.8.8.8) to see if local resolver config is the problem.

**Step 5 – Check `/etc/resolv.conf`**

```bash
cat /etc/resolv.conf
```

* **Explanation:** Confirms which DNS servers are configured on the system.

---

## **Story 6: Wireless Network Setup**

**Scenario:** Set up a laptop to connect to a new Wi-Fi network.

**Step 1 – List wireless interfaces**

```bash
iwconfig
```

* **Explanation:** Displays wireless interfaces and their current status.

**Step 2 – Connect to a Wi-Fi network**

```bash
sudo iwconfig wlan0 essid "OfficeWiFi" key s:password123
sudo dhclient wlan0
```

* **Explanation:** Sets the SSID and key (WEP/WPA), then requests an IP via DHCP.

**Step 3 – Verify connectivity**

```bash
ping -c 3 8.8.8.8
ping -c 3 google.com
```

* **Explanation:** First checks network layer connectivity, then verifies DNS resolution.

---

## **Story 7: Bandwidth and Performance Testing**

**Scenario:** Determine why a remote backup is slow.

**Step 1 – Measure bandwidth with `iperf`**

```bash
iperf -s   # on backup server
iperf -c backup-server -t 30 -P 4
```

* **Explanation:** Server listens for tests. Client connects for 30 seconds using 4 parallel streams. Shows effective throughput.

**Step 2 – Monitor interface usage in real-time**

```bash
sudo iftop -i eth0
```

* **Explanation:** Displays bandwidth usage per host to identify network bottlenecks.

**Step 3 – Check active TCP connections**

```bash
ss -t -a -p -n
```

* **Explanation:** Lists all TCP sockets with process info and numeric IPs/ports. Helps identify connections using bandwidth.

---

## **Story 8: Capturing Suspicious Traffic for Security Analysis**

**Scenario:** Users report strange external connections to a server.

**Step 1 – Capture traffic**

```bash
sudo tcpdump -i eth0 -w suspicious.pcap -c 500
```

* **Explanation:** Captures first 500 packets for analysis.

**Step 2 – Analyze capture**

```bash
tshark -r suspicious.pcap
```

* **Explanation:** Displays packet-level info; you can filter by IP, protocol, or port to detect anomalies.

**Step 3 – Check listening sockets**

```bash
ss -ltnp
```

* **Explanation:** Shows listening TCP sockets with processes. Helps identify rogue services.

**Step 4 – Verify firewall rules**

```bash
sudo iptables -L -n -v
sudo nft list ruleset
```

* **Explanation:** Ensures unwanted inbound/outbound traffic is blocked.

---

## **Story 9: Troubleshooting Routing Issues**

**Scenario:** Server cannot reach a specific subnet.

**Step 1 – Show routing table**

```bash
ip route show
```

* **Explanation:** Confirms the server has a route to the target subnet.

**Step 2 – Test path to subnet**

```bash
traceroute -n 10.10.0.5
```

* **Explanation:** Shows hops; reveals where the packets are dropped.

**Step 3 – Add a static route (if missing)**

```bash
sudo ip route add 10.10.0.0/24 via 192.168.1.1 dev eth0
```

* **Explanation:** Manually adds route to reach the subnet via gateway.

**Step 4 – Verify ARP cache**

```bash
ip neigh show
```

* **Explanation:** Confirms MAC address resolution for gateway; can clear cache with `ip neigh flush`.

---

## **Story 10: Firewall Troubleshooting with iptables/nftables**

**Scenario:** Application on port 8080 is unreachable.

**Step 1 – Check rules**

```bash
sudo iptables -L -n -v
sudo nft list ruleset
```

**Step 2 – Temporarily allow traffic**

```bash
sudo ufw allow 8080/tcp
```

**Step 3 – Verify connectivity**

```bash
nc -zv 127.0.0.1 8080
```

* **Explanation:** Netcat tests if TCP port is open locally. `-z` for zero-I/O (scan), `-v` for verbose output.

**Step 4 – Revert firewall changes if needed**

```bash
sudo ufw delete allow 8080/tcp
```

---
---
---
---

## **Scenario 1: Securing a Public Web Server (firewalld)**

**Story Context:**
You’re the sysadmin for a public web server hosting a company website. You want to ensure only necessary traffic is allowed, while keeping the server reachable to users on the internet.

**Step-by-Step Practice:**

1. **Check current zones and default zone**

   ```bash
   firewall-cmd --get-zones
   firewall-cmd --get-default-zone
   firewall-cmd --get-active-zones
   ```

   *Explanation:* Lists all zones, current default, and which interfaces are using which zones.

2. **Set default zone to `public`**

   ```bash
   firewall-cmd --set-default-zone=public
   ```

   *Explanation:* All new interfaces will automatically use the `public` zone.

3. **Allow only HTTP and HTTPS**

   ```bash
   firewall-cmd --zone=public --add-service=http
   firewall-cmd --zone=public --add-service=https
   firewall-cmd --zone=public --list-services
   ```

   *Explanation:* Opens TCP ports 80 and 443 for public access.

4. **Make the runtime changes permanent**

   ```bash
   firewall-cmd --runtime-to-permanent
   ```

   *Explanation:* Saves current rules so they persist across reboots.

5. **Enable panic mode in case of emergency**

   ```bash
   firewall-cmd --panic-on
   firewall-cmd --panic-off
   ```

   *Explanation:* Blocks all traffic immediately, then restores previous rules.

---

## **Scenario 2: Blocking a Malicious IP Range (iptables)**

**Story Context:**
Your server is experiencing brute-force attacks from a known malicious IP subnet `203.0.113.0/24`. You need to block it.

**Step-by-Step Practice:**

1. **Check existing rules**

   ```bash
   iptables -L -n -v
   ```

2. **Add a rule to drop traffic from the malicious subnet**

   ```bash
   iptables -A INPUT -s 203.0.113.0/24 -j DROP
   ```

3. **Insert rule at the top (higher priority)**

   ```bash
   iptables -I INPUT 1 -s 203.0.113.0/24 -j DROP
   ```

4. **Verify rules**

   ```bash
   iptables -L -n --line-numbers
   ```

5. **Make the changes permanent**

   ```bash
   sudo iptables-save > /etc/sysconfig/iptables  # RHEL/CentOS
   sudo iptables-save > /etc/iptables/rules.v4   # Debian/Ubuntu
   ```

*Explanation:* Using `-I` ensures the malicious traffic is blocked before other rules. Saving rules prevents loss after a reboot.

---

## **Scenario 3: Combined IPv4/IPv6 Filtering (nftables)**

**Story Context:**
You are consolidating firewall rules for a small office network. You want both IPv4 and IPv6 support and simplified configuration.

**Step-by-Step Practice:**

1. **List current tables**

   ```bash
   nft list tables
   ```

2. **Create a new `inet` table**

   ```bash
   nft add table inet office
   ```

3. **Add an input chain with default drop policy**

   ```bash
   nft 'add chain inet office input { type filter hook input priority 0; policy drop; }'
   ```

4. **Allow SSH and HTTP**

   ```bash
   nft 'add rule inet office input tcp dport 22 accept'
   nft 'add rule inet office input tcp dport 80 accept'
   ```

5. **List table and verify rules**

   ```bash
   nft list table inet office
   ```

*Explanation:* The `inet` family allows one table for IPv4 and IPv6. Drop-by-default ensures safety; rules open only needed services.

---

## **Scenario 4: Simple Home Firewall Management (UFW)**

**Story Context:**
You manage a home server hosting multiple services. You prefer a simple, beginner-friendly firewall interface.

**Step-by-Step Practice:**

1. **Enable UFW**

   ```bash
   sudo ufw enable
   ```

2. **Set default policies**

   ```bash
   sudo ufw default deny incoming
   sudo ufw default allow outgoing
   ```

3. **Allow essential services**

   ```bash
   sudo ufw allow 22/tcp   # SSH
   sudo ufw allow 80/tcp   # HTTP
   sudo ufw allow 443/tcp  # HTTPS
   ```

4. **Check status**

   ```bash
   sudo ufw status verbose
   ```

5. **Delete a rule by number**

   ```bash
   sudo ufw status numbered
   sudo ufw delete 2
   ```

*Explanation:* UFW simplifies rule management and automatically integrates with iptables/nftables.

---

## **Scenario 5: Preventing Brute-Force SSH Attacks (Dynamic Rules)**

**Story Context:**
You want to automatically block IPs trying to brute-force SSH logins.

**Step-by-Step Practice:**

1. **Install and check DenyHosts**

   ```bash
   sudo systemctl status denyhosts
   sudo tail -f /var/log/secure
   ```

   *Explanation:* DenyHosts monitors `/var/log/secure` and updates `/etc/hosts.deny`.

2. **Install Fail2Ban and create a jail for SSH**

   ```bash
   sudo apt install fail2ban
   sudo systemctl enable fail2ban
   sudo fail2ban-client status sshd
   ```

   *Explanation:* Fail2Ban dynamically updates firewall rules after repeated failed login attempts.

3. **Check banned IPs**

   ```bash
   sudo fail2ban-client status sshd
   ```

---

## **Scenario 6: Group Blocking with IPset**

**Story Context:**
You maintain a set of IPs repeatedly causing problems across multiple services.

**Step-by-Step Practice:**

1. **Create an IPset**

   ```bash
   ipset create blacklist hash:ip
   ```

2. **Add malicious IPs**

   ```bash
   ipset add blacklist 192.168.1.100
   ipset add blacklist 203.0.113.45
   ```

3. **Drop traffic from all IPs in the set via iptables**

   ```bash
   iptables -I INPUT -m set --match-set blacklist src -j DROP
   ```

4. **List set contents**

   ```bash
   ipset list
   ```

5. **Remove an IP or destroy set**

   ```bash
   ipset del blacklist 203.0.113.45
   ipset destroy blacklist
   ```

*Explanation:* IPset simplifies management of large or dynamic IP groups, integrating with iptables/nftables for efficient blocking.

---

## **Scenario 7: Configuring a DMZ for a Public Web App (firewalld + NAT)**

**Story Context:**
Your company hosts a public web application and a backend database on separate servers. You want the web server accessible from the internet, but the database only from the web server.

**Step-by-Step Practice:**

1. **Set up DMZ zone for the web server**

   ```bash
   firewall-cmd --permanent --new-zone=dmz
   firewall-cmd --permanent --zone=dmz --add-service=http
   firewall-cmd --permanent --zone=dmz --add-service=https
   firewall-cmd --reload
   ```

   *Explanation:* The DMZ allows only selected incoming traffic to the web server.

2. **Assign the interface connected to the internet**

   ```bash
   firewall-cmd --zone=dmz --change-interface=eth0
   ```

3. **Enable masquerading for NAT**

   ```bash
   firewall-cmd --zone=dmz --add-masquerade --permanent
   firewall-cmd --reload
   ```

   *Explanation:* Masquerading hides internal IPs and allows outbound connections from DMZ.

4. **Allow the web server to communicate with the internal database**

   ```bash
   firewall-cmd --zone=internal --add-source=10.0.0.10  # IP of web server
   firewall-cmd --zone=internal --add-service=mysql
   firewall-cmd --permanent --reload
   ```

   *Explanation:* Internal database is only accessible from the web server in DMZ.

---

## **Scenario 8: Setting Up Packet Forwarding (Router Functionality)**

**Story Context:**
You want a Linux server to act as a router between two networks: `eth0` (WAN) and `eth1` (LAN).

**Step-by-Step Practice:**

1. **Enable IPv4 forwarding temporarily**

   ```bash
   sysctl -w net.ipv4.ip_forward=1
   ```

2. **Make forwarding permanent**

   ```bash
   echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
   sysctl -p
   ```

3. **Enable NAT for outbound traffic**

   ```bash
   iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
   iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
   iptables -A FORWARD -i eth0 -o eth1 -m state --state ESTABLISHED,RELATED -j ACCEPT
   ```

4. **Verify routing**

   ```bash
   iptables -t nat -L -n -v
   iptables -L -n -v
   ```

   *Explanation:* The server forwards packets between interfaces and hides internal IPs using NAT.

---

## **Scenario 9: Rate Limiting SSH Connections (Fail2Ban + iptables)**

**Story Context:**
Your server is experiencing repeated SSH login attempts. You want to automatically ban IPs after 5 failed attempts in 10 minutes.

**Step-by-Step Practice:**

1. **Install Fail2Ban**

   ```bash
   sudo apt install fail2ban
   sudo systemctl enable fail2ban
   sudo systemctl start fail2ban
   ```

2. **Configure SSH jail**
   Edit `/etc/fail2ban/jail.local`:

   ```
   [sshd]
   enabled = true
   port = ssh
   maxretry = 5
   findtime = 600
   bantime = 3600
   ```

   *Explanation:* Bans IPs after 5 failed logins within 10 minutes for 1 hour.

3. **Start monitoring**

   ```bash
   sudo fail2ban-client status sshd
   ```

   *Explanation:* Dynamic rules are added automatically to iptables/firewalld.

---

## **Scenario 10: Blocking Specific Countries (IPset + Firewall)**

**Story Context:**
Your organization only serves local customers. You want to block traffic from foreign IP ranges.

**Step-by-Step Practice:**

1. **Create an IPset for blocked countries**

   ```bash
   ipset create blocked_countries hash:net
   ```

2. **Add IP ranges (example: China, Russia)**

   ```bash
   ipset add blocked_countries 1.0.1.0/24
   ipset add blocked_countries 5.44.0.0/16
   ```

3. **Apply rule in iptables**

   ```bash
   iptables -I INPUT -m set --match-set blocked_countries src -j DROP
   ```

4. **Verify**

   ```bash
   ipset list blocked_countries
   iptables -L -n -v
   ```

   *Explanation:* IPset makes large-scale blocking efficient without adding individual iptables rules for each subnet.

---

## **Scenario 11: Migrating iptables Rules to nftables**

**Story Context:**
Your Linux distro is moving to nftables as the default firewall. You need to convert existing iptables rules.

**Step-by-Step Practice:**

1. **Export iptables rules**

   ```bash
   iptables-save > /tmp/iptables.rules
   ```

2. **Create nftables table**

   ```bash
   nft add table inet main
   nft 'add chain inet main input { type filter hook input priority 0; policy drop; }'
   nft 'add chain inet main forward { type filter hook forward priority 0; policy drop; }'
   nft 'add chain inet main output { type filter hook output priority 0; policy accept; }'
   ```

3. **Add rules from iptables manually**
   Example:

   ```bash
   nft add rule inet main input tcp dport 22 accept
   nft add rule inet main input tcp dport 80 accept
   ```

4. **List nftables rules**

   ```bash
   nft list ruleset
   ```

   *Explanation:* Transition to nftables allows unified IPv4/IPv6 configuration and cleaner syntax.

---

## **Scenario 12: Testing Firewall Rules in a Lab Environment**

**Story Context:**
Before applying changes in production, you want to simulate firewall rules in a virtual lab.

**Step-by-Step Practice:**

1. **Create a test interface**

   ```bash
   ip link add name veth0 type veth peer name veth1
   ip addr add 192.168.100.1/24 dev veth0
   ip addr add 192.168.100.2/24 dev veth1
   ip link set veth0 up
   ip link set veth1 up
   ```

2. **Assign firewalld zones**

   ```bash
   firewall-cmd --zone=public --add-interface=veth0
   firewall-cmd --zone=internal --add-interface=veth1
   ```

3. **Test allowed/blocked traffic**

   ```bash
   ping 192.168.100.2
   curl http://192.168.100.2
   ```

   *Explanation:* Virtual interfaces simulate production traffic, allowing safe testing of firewall rules.

---

## **Story 11: Configuring Interface Bonding for Redundancy/Throughput**

**Scenario:** A server has two Ethernet NICs (`eth0` and `eth1`). The goal is to **bond them into `bond0`** for higher bandwidth and redundancy (active-backup mode).

---

### **Step 1 – Install necessary tools (if needed)**

```bash
# Ubuntu
sudo apt update
sudo apt install ifenslave

# RHEL
sudo yum install -y teamd
```

* **Explanation:** `ifenslave` allows configuring bonding on Ubuntu. On RHEL 8+, `teamd` or `nmcli` is often used for team interfaces.

---

### **Step 2 – Load bonding kernel module**

```bash
sudo modprobe bonding
```

* **Explanation:** Loads the bonding driver into the kernel.

---

### **Step 3 – Create a bond interface configuration**

**Ubuntu (netplan example `/etc/netplan/01-bond.yaml`):**

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
    eth1:
      dhcp4: no
  bonds:
    bond0:
      dhcp4: yes
      interfaces:
        - eth0
        - eth1
      parameters:
        mode: active-backup
        primary: eth0
        mii-monitor-interval: 100
```

* **Explanation:**

  * `mode: active-backup` → Only one NIC is active; failover if it goes down.
  * `mii-monitor-interval: 100` → Monitors NIC link every 100ms.

```bash
sudo netplan apply
```

**RHEL 8 (using `nmcli`):**

```bash
sudo nmcli connection add type bond con-name bond0 ifname bond0 mode active-backup
sudo nmcli connection add type ethernet con-name bond0-slave1 ifname eth0 master bond0
sudo nmcli connection add type ethernet con-name bond0-slave2 ifname eth1 master bond0
sudo nmcli connection up bond0
```

* **Explanation:** Creates a bond and attaches both NICs as slaves.

---

### **Step 4 – Verify bond interface**

```bash
cat /proc/net/bonding/bond0
ip addr show bond0
```

* **Explanation:**

  * `/proc/net/bonding/bond0` shows mode, active NIC, link status, and transmit/receive stats.
  * `ip addr show bond0` confirms IP assignment.

---

### **Step 5 – Test failover**

```bash
# Bring down primary NIC
sudo ifconfig eth0 down

# Check bond status
cat /proc/net/bonding/bond0
ping -c 4 8.8.8.8
```

* **Explanation:** Ensures traffic continues on `eth1` without interruption.

---

### **Step 6 – Optional: Monitor traffic and bonding stats**

```bash
# Monitor per-NIC traffic
ifconfig eth0
ifconfig eth1

# Advanced monitoring
sudo ethtool -S eth0
sudo ethtool -S eth1
```

* **Explanation:** Confirms packets are transmitted and received correctly, even under failover conditions.

---

### **Step 7 – Troubleshooting**

**Common issues:**

1. Bonding module not loaded → `sudo modprobe bonding`
2. Slaves not attached → check `/proc/net/bonding/bond0`
3. Duplicate IPs → ensure only `bond0` has the IP, not individual NICs
4. DHCP not working → try static IP on bond interface

---

### **Step 8 – Verification**

```bash
# Check connectivity
ping -c 4 8.8.8.8

# Check bond active interface
cat /proc/net/bonding/bond0 | grep "Currently Active Slave"

# Check network stats
ss -tuln
```

---
