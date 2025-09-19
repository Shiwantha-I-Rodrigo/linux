# Connectivity
---
1. list all interfaces --> `ip link show`
2. bring up interface --> `ip link set eth0 up`
3. check ip addresses --> `ip addr show`
4. set ip address --> `ip addr add 192.168.100.20/24 dev eth0`
5. list routes --> `ip route show`
5. add route --> `ip route add -net 192.168.100.0 netmask 255.255.255.0 dev eth0`
6. list interface settings --> `ethtool eth0`
7. set interface settings --> `ethtool -s eth0 speed 100 duplex full autoneg on`

---
# Firewalls

***PORTS***

- HTTP - 80
- HTTPS - 443
- SSH - 22
- DNS - 53

***PACKET ORDER***

- prerouting --> forward --> postrouting
- prerouting --> input --> output --> postrouitng

---
## iptables 

tables - (filter / nat / mangle / raw)
chains - (PREROUTING / INPUT / FORWARD / OUTPUT / POSTROUTING)
protocols - (tcp / udp / icmp)

1. list rules --> `iptables -t filter -vnL` / `iptables -S` (ATTENTION : default policies)
2. set default policy --> `iptables -P INPUT ACCEPT`
3. add rules --> `iptables -A INPUT -p tcp --dport 22 -j ACCEPT`
4. insert rule --> `iptables -I INPUT 1 -s 192.168.100.1 DROP`
5. replace rule --> `iptables -R INPUT 1 -p tcp --dport 80 ACCEPT`
6. delete rule --> `iptables -D INPUT 1 -p tcp --dport 80 ACCEPT` / `iptables -t filter -D INPUT 2`
7. flush rules --> `iptables -F` / `iptables -F -t nat`

(A-add / D-delete / R-replace / I-insert / F-flush / P-default_policy)

---
## nftables

family - (ip / ip6 / inet / bridge)
tables - (filter / nat / mangle / raw)
chains - (PREROUTING / INPUT / FORWARD / OUTPUT / POSTROUTING)
protocols -(tcp / ip / icmp)

1. list rules --> `nft -a list ruleset` / `nft list tables` / `nft list tables ip` / `nft list table ip filter`
0. set default policy --> just add the chain again with changed policy.
2. add table --> `nft add table ip filter`
3. add chain --> `nft add chain ip filter input \\\{ type filter hook input priority 0\\\; policy drop\\\; \\\}`
4. add rule --> `nft add rule ip filter input tcp dport 22 accept`
5. insert rule --> `nft insert rule ip filter input position 3 tcp dport 53 accept` <-- (before 3) / (after 3) --> `nft add rule ip filter input position 3 tcp saddr 192.1.1.1 accept`
6. replace rule --> `nft replace rule ip filter input handle 2 icmp type echo-request accept`
7. delete rule --> `nft delete rule ip filter input handle 2`
8. flush rules --> `nft flush ruleset` / `nft flush table ip filter` /`nft flush chain ip filter input`

> ! multiple chains with the same type will be evaluated by priority (lower better).
same priority is undefined. any chain could be evaluated first randomly.

---
## ufw (frontend for iptables)

(allow / deny / reject)

1. check status / rules --> `ufw status numbered` / `ufw status verbose`
2. enable --> `ufw enable` (! will definitely fu*k up that meticulously maintained iptables tables with million fu*king useless rules)
3. set default policy --> `ufw default deny incoming`
4. add rule --> `ufw allow proto tcp from any to any port 22` / `ufw allow out proto udp to any port 53`
5. add profile --> `ufw allow "OpenSSH"`
6. remove profile --> `ufw delete allow "OpenSSH"`
6. insert rule --> `ufw insert 1 allow proto icmp from any`
7. delete rule --> `ufw delete allow 22/tcp` / `ufw delete 1`
8. flush rules --> `ufw reset`
9. disable --> `ufw disable`
10. app list --> `ufw app list`

---
## firewalld (frontend for nft / iptables)

1. get zones --> `firewall-cmd --get-zones`
2. get default zone --> `firewall-cmd --get-default-zone`
3. set default zone --> `firewall-cmd --set-default-zone=public`
4. list active zones + interface --> `firewall-cmd --get-active-zones` 
5. add service --> `firewall-cmd --add-service=ssh`
6. list active services --> `firewall-cmd --list-services`
7. list services --> `firewall-cmd --get-services`
8. remove service --> `firewall-cmd --zone=public --remove-service=http`
8. panic mode --> `firewall-cmd --panic-on` (off)
9. permanent --> `firewall-cmd --runtime-to-permanent` / `firewall-cmd --permanent --add-service=dns`
10. change interface zone --> `firewall-cmd --permanent --zone=home --change-interface=enp1s0`

> ! systemctl status firewalld

---
## SSH

1. keygen --> `ssh-keygen -t rsa -f ~/.ssh/key_id_rsa`
2. copy id --> `ssh-copy-id -i ~/.ssh/key_id_rsa.pub user@192.168.100.12`
3. port forward --> `ssh -N -L 8080:192.168.100.12:80 user@192.168.100.12`
4. test --> `ssh user@192.168.100.12`

---
## SCP/SFTP



