**Sample Files / Initial Setup**

1. Create a set of sample files to simulate network activity and SSH/SCP testing:

```bash
mkdir ~/network_lab
cd ~/network_lab
echo "Hello from Server1" > server1.txt
echo "Hello from Server2" > server2.txt
echo "This is a test log file." > test.log
```

2. Simulate two hosts (Server1 and Server2) with interfaces:

```
Server1: eth0
Server2: eth0
```

Assign IPs:

```
Server1 eth0: 192.168.100.10/24
Server2 eth0: 192.168.100.20/24
```

---

# Step 1 — Interface & IP Management

---

## **Task 1: Interface bring-up and IP assignment**

* On **Server1**, bring up the interface `eth0` and assign the IP `192.168.100.10` with netmask `255.255.255.0`.
* On **Server2**, bring up `eth0` and assign `192.168.100.20/24`.
* Use **both** `ifconfig` and `ip` commands to perform these tasks.

## **Task 2: Verify interfaces and IP addresses**

* List all interfaces and assigned IP addresses using:

  * `ifconfig`
  * `ip addr show`
  * `ip link show`
* Check the interface status (up/down) using both commands.

## **Task 3: Routing & basic connectivity**

* Add a route on **Server1** to reach **Server2** via `eth0` (and vice versa).
* Verify routing table using:

  * `route`
  * `ip route show`
* Ping the other server to ensure connectivity.

## **Task 4: Interface speed/duplex**

* Use `ethtool` to display interface speed and duplex.
* Optionally, simulate changing speed/duplex settings on `eth0`.

---
---
---

**Server1:**

```bash
# Using ifconfig
sudo ifconfig eth0 up
sudo ifconfig eth0 192.168.100.10 netmask 255.255.255.0

# Using ip
sudo ip link set eth0 up
sudo ip addr add 192.168.100.10/24 dev eth0
```

**Server2:**

```bash
sudo ifconfig eth0 up
sudo ifconfig eth0 192.168.100.20 netmask 255.255.255.0

sudo ip link set eth0 up
sudo ip addr add 192.168.100.20/24 dev eth0
```

---

### **Task 2: Verify interfaces**

```bash
ifconfig
ip addr show
ip link show
```

*Sample verification output:*

```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.100.10  netmask 255.255.255.0
        ...
```

---

### **Task 3: Routing & connectivity**

**Server1:**

```bash
sudo route add -net 192.168.100.0 netmask 255.255.255.0 dev eth0
ip route show
ping -c 3 192.168.100.20
```

**Server2:** same, adjusted for Server1’s IP.

---

### **Task 4: Interface speed/duplex**

```bash
sudo ethtool eth0          # display current speed/duplex
sudo ethtool -s eth0 speed 100 duplex full autoneg on
```

---
---
---
---
---

# Step 2 — Firewall Management

---

## **Task 1 : Legacy Firewall Setup (iptables)**

You are the **Linux system administrator** for a small office. The IT manager has asked you to secure **Server1** (192.168.100.10) using the legacy `iptables` firewall.

The requirements are:

1. All SSH connections must be allowed.
2. The server must respond to ICMP (ping) requests.
3. Any traffic from a malicious host (`192.168.100.30`) must be blocked.
4. HTTP traffic (port 80) should be allowed for internal testing.
5. You need to **insert, append, delete, and replace rules** to ensure correct order of processing.
6. Default policies must be configured: **DROP all incoming traffic**, allow outgoing traffic, and drop forwarded traffic.
7. You must **list, flush, and verify all rules**.

---

1. **Append rule to allow SSH:**

> You want to ensure administrators can access the server remotely. Since this is a common rule, you append it to the end of the INPUT chain.

```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

---

2. **Insert rule to allow ICMP (ping) at the top of the chain:**

> To ensure monitoring tools can check the server, you insert an ICMP rule at **position 1** so it’s processed before any restrictive rules.

```bash
sudo iptables -I INPUT 1 -p icmp -j ACCEPT
```

---

3. **Block traffic from a malicious host:**

> You detected suspicious activity from 192.168.100.30 and must block it completely.

```bash
sudo iptables -A INPUT -s 192.168.100.30 -j DROP
```

---

4. **Replace the first INPUT rule with HTTP accept:**

> After testing, you realize the ICMP rule is no longer first, and you want to prioritize HTTP traffic for internal web services. You replace the first rule with HTTP.

```bash
sudo iptables -R INPUT 1 -p tcp --dport 80 -j ACCEPT
```

---

5. **Set default policies:**

> To make the server more secure, you enforce restrictive policies:

```bash
sudo iptables -P INPUT DROP
sudo iptables -P OUTPUT ACCEPT
sudo iptables -P FORWARD DROP
```

---

6. **Flush all rules (for a reset scenario):**

> Before deploying to production, you may want to clear all rules for testing.

```bash
sudo iptables -F -t filter
```

---

7. **List rules and show syntax:**

> You verify the rules you’ve applied and check the full commands for each rule.

```bash
sudo iptables -L -v
sudo iptables -S
```

*Sample verification output:*

```
Chain INPUT (policy DROP)
num  pkts bytes target     prot opt in  out  source            destination
1    0    0    ACCEPT     tcp  --  *   *    0.0.0.0/0         0.0.0.0/0     tcp dpt:22
2    0    0    DROP       all  --  *   *    192.168.100.30   0.0.0.0/0
3    0    0    ACCEPT     tcp  --  *   *    0.0.0.0/0         0.0.0.0/0     tcp dpt:80
```

---

## **Task 2 : Modern Firewall Setup (nftables)**

You are the **Linux administrator** for a small office. After setting up Server2 (192.168.100.20), your manager requests a **modern, maintainable firewall** using **nftables**.

The goals are:

1. Use **inet** and **ip** families.
2. Create both **filter** and **nat** tables.
3. Configure chains: `input`, `output`, `forward`, `prerouting`, `postrouting`.
4. Accept SSH, HTTP, and ICMP traffic.
5. Drop traffic from malicious hosts (`192.168.100.30`).
6. List, flush, and delete rules to verify and adjust configuration.

---

**Step 1: Create tables**

> You start by creating two tables: one for **filtering traffic** and one for **NAT translation**.

```bash
# Filter table for inet family (supports IPv4 & IPv6)
sudo nft add table inet filter

# NAT table for IPv4
sudo nft add table ip nat
```

---

**Step 2: Create chains in filter table**

> Chains define **how packets are processed**: input, forward, output.

```bash
# Input chain, default DROP
sudo nft add chain inet filter input { type filter hook input priority 0; policy drop; }

# Forward chain, default DROP
sudo nft add chain inet filter forward { type filter hook forward priority 0; policy drop; }

# Output chain, default ACCEPT
sudo nft add chain inet filter output { type filter hook output priority 0; policy accept; }
```

---

**Step 3: Create chains in NAT table**

> NAT chains handle **address translation** for incoming/outgoing packets.

```bash
# Pre-routing chain (before routing decision)
sudo nft add chain ip nat prerouting { type nat hook prerouting priority 0; policy accept; }

# Post-routing chain (after routing decision)
sudo nft add chain ip nat postrouting { type nat hook postrouting priority 100; policy accept; }
```

---

**Step 4: Add rules to allow traffic**

> Now we allow essential traffic while blocking malicious hosts.

```bash
# Accept SSH on port 22
sudo nft add rule inet filter input tcp dport 22 accept

# Accept HTTP on port 80
sudo nft add rule inet filter input tcp dport 80 accept

# Accept ICMP ping requests
sudo nft add rule inet filter input icmp type echo-request accept

# Drop traffic from malicious host
sudo nft add rule inet filter input ip saddr 192.168.100.30 drop
```

---

**Step 5: List rules and verify**

> Always verify that your rules are correct before deployment.

```bash
sudo nft list ruleset
```

*Sample output:*

```
table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;
        tcp dport 22 accept
        tcp dport 80 accept
        icmp type echo-request accept
        ip saddr 192.168.100.30 drop
    }
    chain forward { type filter hook forward priority 0; policy drop; }
    chain output { type filter hook output priority 0; policy accept; }
}
```

---

**Step 6: Flush rules**

> For testing or resetting configuration, you can flush all rules in the tables.

```bash
sudo nft flush ruleset
```

---

**Step 7: Delete a rule**

> After reviewing traffic logs, you decide to remove the HTTP rule.

```bash
sudo nft delete rule inet filter input handle 2
```

---

## **Task 3 : Simple and Advanced Firewall (UFW)**

You are the **Linux system administrator** for the office. After setting up Server1 (192.168.100.10), your manager requests that you **configure a simple, easy-to-manage firewall** using **UFW (Uncomplicated Firewall)**.

The requirements:

1. Enable UFW and verify its status.
2. Set default policies for incoming, outgoing, and routed traffic.
3. Allow essential services like SSH (port 22) and HTTP (port 80).
4. Block malicious hosts (`192.168.100.30`) and reject traffic from suspicious hosts (`192.168.100.40`).
5. Insert rules at specific positions and delete rules if needed.
6. Use advanced options: specify **protocols, ports, interfaces, directions, applications, and policies**.
7. Reset the firewall and disable it for maintenance testing.

---

**Step 1: Enable UFW and check status**

> You begin by enabling UFW and verifying it is running.

```bash
sudo ufw enable
sudo ufw status verbose
```

*Sample output:*

```
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip
```

---

**Step 2: Set default policies**

> You enforce strict rules by default.

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw default deny routed
```

---

**Step 3: Allow essential services**

> SSH and HTTP must be accessible, with explicit protocols and ports.

```bash
# Allow SSH (TCP)
sudo ufw allow proto tcp from any to any port 22

# Allow HTTP (TCP)
sudo ufw allow proto tcp from any to any port 80

# Allow ICMP (ping) incoming
sudo ufw allow proto icmp from any to any
```

---

**Step 4: Deny traffic from malicious host**

> A host is detected sending suspicious packets.

```bash
sudo ufw deny from 192.168.100.30
```

---

**Step 5: Reject traffic from suspicious host**

> For certain hosts, you want to **reject with a response** instead of silently dropping.

```bash
sudo ufw reject from 192.168.100.40
```

---

**Step 6: Insert a rule at a specific position**

> You want ICMP traffic to be processed first for monitoring purposes.

```bash
sudo ufw insert 1 allow proto icmp from any
```

---

**Step 7: Delete a rule**

> After review, you no longer need HTTP to be allowed temporarily.

```bash
sudo ufw delete allow 80/tcp
```

---

**Step 8: Advanced rule examples**

> You specify direction, interface, and application rules for fine-grained control.

```bash
# Allow incoming HTTP only on eth0
sudo ufw allow in on eth0 to any port 80 proto tcp

# Allow outgoing DNS
sudo ufw allow out to any port 53 proto udp

# Allow application profile (e.g., OpenSSH)
sudo ufw allow app OpenSSH

# Set a policy for routed traffic
sudo ufw default deny routed
```

---

**Step 9: Reset and disable UFW**

> For maintenance or testing, you may reset and disable the firewall.

```bash
sudo ufw reset
sudo ufw disable
```

---

## **Task 4 : Dynamic Firewall with firewalld**

You are the **Linux system administrator** for your office. Server2 (192.168.100.20) is running in a production environment and must be secured using **firewalld**, a dynamic firewall that allows **runtime and permanent changes**.

The goals:

1. List all zones and determine which zone is default.
2. View active zones and verify which interfaces are bound.
3. Add essential services like SSH and HTTP.
4. Check allowed services and available services.
5. Test panic mode to temporarily block all traffic.
6. Make runtime changes permanent and manage permanent configurations.

---

**Step 1: List all available zones**

> You need to know which zones exist before making changes.

```bash
sudo firewall-cmd --get-zones
```

*Sample output:*

```
block dmz drop external home internal public trusted work
```

---

**Step 2: Get the default zone**

> Determine which zone is applied to interfaces by default.

```bash
sudo firewall-cmd --get-default-zone
```

*Sample output:*

```
public
```

---

**Step 3: Get active zones and bound interfaces**

> Check which interfaces are currently using which zones.

```bash
sudo firewall-cmd --get-active-zones
```

*Sample output:*

```
public
  interfaces: eth0
```

---

**Step 4: Change the default zone**

> The IT policy requires all new interfaces to use the `public` zone by default.

```bash
sudo firewall-cmd --set-default-zone=public
```

---

**Step 5: Add services to the zone**

> Ensure essential services are allowed.

```bash
# Runtime addition of SSH
sudo firewall-cmd --add-service=ssh

# Runtime addition of HTTP
sudo firewall-cmd --add-service=http
```

---

**Step 6: List allowed services**

> Verify which services are currently allowed in the active zone.

```bash
sudo firewall-cmd --list-services
```

*Optional:* Check **all available services** to see what can be added.

```bash
sudo firewall-cmd --get-services
```

---

**Step 7: Panic mode (block all traffic)**

> A security incident occurs, and you need to **immediately block all traffic**.

```bash
sudo firewall-cmd --panic-on
```

> After the threat is mitigated:

```bash
sudo firewall-cmd --panic-off
```

---

**Step 8: Make runtime changes permanent**

> Any runtime changes (like added services) should be saved to survive reboots.

```bash
sudo firewall-cmd --runtime-to-permanent
```

> Example: Permanently allow DNS service:

```bash
sudo firewall-cmd --permanent --add-service=dns
```

---

**Story Context Notes**

* **Zones:** Define trust levels for network interfaces.
* **Runtime vs Permanent:** Runtime changes are temporary; permanent changes survive reboots.
* **Panic Mode:** Emergency block to stop all traffic instantly.
* **Service Management:** `--add-service` simplifies firewall rules by predefining ports and protocols.
* **Verification:** Use `--list-services` and `--get-active-zones` to confirm the current configuration.

---
---
---
---
---

# Step 3 — SSH, SCP/SFTP, DNS, and Network Monitoring

## **Task 1: SSH Key Management and Remote Login**

1. On **Server1**, generate an SSH key pair (RSA) in `~/.ssh/server1_id_rsa`.
2. Copy the public key to **Server2** using `ssh-copy-id`.
3. Test SSH login from **Server1** to **Server2** without password.
4. Use SSH to forward **local port 8080 to remote port 80** on Server2.

---

## **Task 2: File Transfer via SCP and SFTP**

1. Using **SCP**, copy `server1.txt` from Server1 to Server2 home directory, preserving timestamps and compressing transfer.
2. Using **SFTP**:

   * Connect to Server2 from Server1.
   * Upload `test.log` to `/tmp`.
   * List remote files (`ls`) and local files (`lls`).
   * Download `server2.txt` from Server2 back to Server1.

---

### **Task 3: DNS Queries**

1. Query DNS for `example.com` using `dig`, `host`, and `nslookup`.
2. Use `host -a` to get all details.
3. Use `nslookup` to query a specific DNS server (e.g., 8.8.8.8).

---

### **Task 4: Packet Capture & Network Monitoring**

1. List all interfaces using `tcpdump -D` or `tshark -D`.
2. Capture packets on `eth0` for 30 seconds using `tcpdump` and `tshark`.
3. Show TCP and UDP socket statistics with `ss -tulnp` and `netstat -tuln`.
4. View routing summary using `ss -r` and `ip route`.

---

### **Task 5: Bandwidth Testing & TCP/UDP Connections**

1. Start an **iperf3** server on Server2 (`-s`) and run a client on Server1 to test **10-second TCP bandwidth** (`-t 10`).
2. Run **bidirectional test** with `iperf -d`.
3. Use **netcat** to:

   * Listen on Server2 on port 9000 (`-l -p 9000`).
   * Connect from Server1 to Server2 using TCP (`-4 -n`) and send a test message.
4. Optionally, test UDP with `nc -u`.

---

### **Task 6: Combined Multi-Command Challenge**

* On **Server1**, perform the following sequence:

1. Bring up interface and assign IP.
2. Configure firewall to allow only SSH and ICMP.
3. Generate SSH key, copy it to Server2.
4. Use SCP to transfer `server1.txt` to Server2.
5. Ping Server2 to verify connectivity.
6. Capture packets on `eth0` while running an `iperf` test from Server1 to Server2.
7. List all listening sockets and routing table summary.

---
---
---

### **Task 1: SSH key management**

```bash
# Generate key on Server1
ssh-keygen -t rsa -f ~/.ssh/server1_id_rsa

# Copy key to Server2
ssh-copy-id -i ~/.ssh/server1_id_rsa.pub user@192.168.100.20

# Test login
ssh user@192.168.100.20

# Local port forward
ssh -L 8080:localhost:80 user@192.168.100.20
```

---

### **Task 2: SCP/SFTP file transfer**

```bash
# SCP
scp -p -C server1.txt user@192.168.100.20:~/

# SFTP session
sftp user@192.168.100.20
sftp> put test.log
sftp> ls
sftp> lls
sftp> get server2.txt
sftp> bye
```

---

### **Task 3: DNS queries**

```bash
dig example.com
host example.com -a
nslookup example.com
nslookup server 8.8.8.8
```

---

### **Task 4: Packet capture & network stats**

```bash
# List interfaces
tcpdump -D
tshark -D

# Capture packets
tcpdump -i eth0 -c 30
tshark -i eth0 -c 30

# Check sockets
ss -tulnp -s -4
netstat -tuln -s

# Routing summary
ss -r
ip route
```

---

### **Task 5: Bandwidth testing & netcat**

```bash
# iperf3
iperf -s         # Server2
iperf -c 192.168.100.20 -t 10 -b 10M -d  # Server1

# Netcat TCP
nc -l -p 9000    # Server2
nc -4 -n 192.168.100.20 9000   # Server1

# Netcat UDP
nc -u -l -p 9001
nc -u 192.168.100.20 9001
```

---

### **Task 6: Combined multi-command challenge**

```bash
# Bring up interface and assign IP
sudo ifconfig eth0 up
sudo ifconfig eth0 192.168.100.10 netmask 255.255.255.0

# Firewall rules
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p icmp -j ACCEPT
sudo iptables -P INPUT DROP

# SSH key
ssh-keygen -t rsa -f ~/.ssh/server1_id_rsa
ssh-copy-id -i ~/.ssh/server1_id_rsa.pub user@192.168.100.20

# Transfer file
scp -p -C server1.txt user@192.168.100.20:~/

# Ping to check connectivity
ping -c 3 192.168.100.20

# Capture packets while running iperf
tcpdump -i eth0 -c 30 &
iperf -c 192.168.100.20 -t 10

# List sockets and routing
ss -tulnp -s -4
ip route
```

---