# SYSTEM PROPERTIES AND REMEDIATION

## NETWORK ISSUES

### TERMINOLOGY

* **Bandwidth**: Max data transferable between two points in a given time.
* **Throughput**: Actual data transferred between two points in a given time.
* **Saturation**: When traffic exceeds network capacity.
* **Latency**: Delay between sending and receiving data.
* **Jitter**: Variation in latency.
* **Routing**: Directing packets through network paths.

> **!** increasing routers buffer size to mitigate packet loss but leads to bufferbloat / congestion.

---

### TIMEOUT AND LOSS

- UDP does not guarantee packet delivery.
- VoIP commonly uses UDP.
- **TCP** may increase **congestion** due to its retransmission behavior.
- **Timeout** is a set duration to wait before handling failures.
- Causes of network communication timeouts include:
    + Unavailable system
    + Wrong IP address
    + Inactive or missing service
    + Traffic blocked by a firewall
    + Packet loss from network congestion

---

### NAME RESOLUTION

- Converting a system’s **Fully Qualified Domain Name** (FQDN) into its IP address.
- This process is handled by the **Domain Name System** (DNS).
- The location of the name server affects how fast names are resolved (**/etc/resolv.conf**).
- A cache stores recent DNS results to speed up future lookups (**dnsmasq**).
- A **secondary** DNS server can help boost performance and reliability.

---

### NETWORK CONFIGUARATION

| **Category**                              | **Details**|
| -                                         | - |
| **Interface Configuration**               | - Check IP, MAC address, subnet, and error rates. <br> - NetworkManager with **firewalld** may block connections due to default zone settings.|
| **Ports vs Sockets**                      | - **Port**: Numerical identifier used by TCP/UDP to determine which service is sending/receiving data. <br> - **Socket**: Combination of IP + port, representing one endpoint in a network connection. <br> - **Localhost**: Hostname for the loopback interface (**127.0.0.1** / **::1**). <br> - **Unix Socket**: Used for local **InterProcess Communication (IPC)**, not over the network. <br> - Unix sockets are faster than using localhost connections.|
| **Adapters**                              | - Hardware enabling network communication. <br> - Error rate should be less than 1% (0.01) of total throughput. <br> - A **NIC** (Network Interface Card) is a type of network adapter, but not all network adapters are NICs.|
| **RDMA** (Remote Direct Memory Access)    | - Allows one computer to directly access the memory of another computer. <br> - Reduces latency and server CPU usage. <br> - Requires specialized hardware. <br> - **Soft-RoCE**: Software-based RDMA over Converged Ethernet.|

---

### NETWORK PERFORMANCE

| **Command**                   | **Description**|
| -                             | - |
| `iperf`, `iperf3`             | Tests network throughput **between two systems**.|
| `iftop -i [adapter]`          | Shows **real-time bandwidth** usage on the specified network **adapter**.|
| `mtr`                         | Displays approximate **travel times** and **packet loss** to the first **10** routers in a network path.|
| `nc`                          | Can be used for simple network throughput testing.(**netcat**)|
| `netstat -s`                  | Shows **protocol-specific** statistics, focusing on **packet rates** (not throughput).|
| `ping`, `ping6`               | Sends **ICMP** packets to test **round-trip** time and basic packet delivery.|
| `ss -s`                       | Displays summary **socket** statistics, including **packet rates** (not throughput).|
| `tracepath`, `tracepath6`     | Traces route to a destination, showing **travel times and MTU size** along the path.|
| `traceroute`, `traceroute6`   | Similar to `tracepath` but offers more detailed features and options.|

---

**`iperf `**`[option]`\
--> Tests network throughput between two systems.

> **!** **iperf** must be installed on both clent & server.
 
| **Option**            | **Description**|
| -                     | - |
| **-s**                | Run in server mode.|
| **-t N**              | Set the test duration to **N** seconds.|
| **-c [IP Address]**   | Run as a client and connect to the server at the given IP address.|
| **-b [Size]**         | Set target bandwidth to **\[Size]** bits per second (default is 1 Mbps). |
| **-d**                | Run a bidirectional test (send and receive simultaneously).|
| **-P N**              | Launch **N** parallel client threads for the test.|
| **-e**                | Show extended output (e.g., retransmits, jitter, etc.).|
| **-i N**              | Set interval between periodic bandwidth reports to **N** seconds.|

`$ sudo ufw allow 5001` ---> allow port 5001 through firewall

`$ iperf -s -t 120` ---> run as server

`$ iperf -c 192.168.0.104 -b 90Kb -d -P 5 -e -i 10` ---> perform test

---

**`nc `**`[option]`\
--> simple network throughput testing.

> **!** **nc** must be installed on both clent & server.

`$ sudo ufw allow 8001` ---> allow port 8001 through firewall

`$ nc -l 8001 > /dev/null` ---> run in listening mode ( **-l** )

`$ dd if=/dev/zero bs=1M count=2 | nc 192.168.0.104 8001 -i 2` ---> run test and quit in 2 seconds ( **-i** )

> **!** The **dd** command, when used without the **of=** option, sends its output to **STDOUT**, which is then piped ( **|** ) directly into the **nc** (netcat) command.

---

**`mtr `**`[option]`\
--> approximate **travel times** and **packet loss** to the first **10** routers

`$ mtr -o "L D A J" -c 20 -r 96.120.112.205`

| **Option** | **Description**                                                                                                                                  |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `-o`       | Output report with the following metrics:<br>• **L** – Packet loss<br>• **D** – Packet drop<br>• **A** – Average travel time<br>• **J** – Jitter |
| `-c`       | Set the number of packets to send.                                                                                                               |
| `-r`       | Generate a static (non-live) report.                                                                                                             |

---

Tools to show adapter summary stats,

`$ ethtool -S [adapter]`\
`$ ifconfig [adapter]`\
`$ ip -s link show [adapter]`\
`$ netstat -i [adapter]`

    $ ip -s link show enp0s0

    2: enp0s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 00:1a:2b:3c:4d:5e brd ff:ff:ff:ff:ff:ff
    RX: bytes  packets  errors  dropped  missed  mcast   
    123456789  123456   0       10       0       234     
    TX: bytes  packets  errors  dropped  carrier collsns 
    987654321  98765    9000    5        0       0       

> **!** even with out many dropped packets error rates may still be too high (0.01+) as shown above for **outward (TX)** packets.

---

Packet sniffers to analyse packets.

- wireshark
- tshark
- tcpdump

| **Functionality**         | **`tcpdump` Option**  | **`tshark` Option**|
| -                         | -                     | - |
| Specify interface         | **-i <interface>**    | **-i <interface>**|
| List interfaces           | **-D**                | **-D**|
| Limit number of packets   | **-c <count>**        | **-c <count>**|
| Write capture to file     | **-w <file.pcap>**    | **-w <file.pcap>**|
| Read from capture file    | **-r <file.pcap>**    | **-r <file.pcap>**|

---

### NETWORK CONFIGURATION

| **Task**              | **Example Command**                 |
| --------------------- | ----------------------------------- |
| Check MAC mappings    | `arp -a`                            |
|                       | `ip neigh`                          |
| DNS lookup (basic)    | `host example.com`                  |
| DNS lookup (detailed) | `dig example.com`                   |
|                       | `dig @8.8.8.8 example.com`          |
| DNS lookup (legacy)   | `nslookup example.com`              |
|                       | `nslookup example.com 8.8.8.8`      |
| Measure DNS time      | `time nslookup example.com 8.8.8.8` |
| Domain registration   | `whois example.com`                 |
| Port & service scan   | `nmap -sT example.com`              |
```
    $ time nslookup example.com 8.8.8.8
    Server:         8.8.8.8
    Address:        8.8.8.8#53

    Non-authoritative answer:
    Name:   example.com
    Address: 93.184.216.34

    real    0m0.150s
    user    0m0.005s
    sys     0m0.003s
```
```
    $ nmap -sT example.com
    Starting Nmap 7.80 ( https://nmap.org ) at 2025-08-14 12:34 UTC
    Nmap scan report for example.com (93.184.216.34)
    Host is up (0.032s latency).
    Not shown: 997 closed ports
    PORT    STATE SERVICE
    22/tcp  open  ssh
    80/tcp  open  http
    443/tcp open  https
    Nmap done: 1 IP address (1 host up) scanned in 5.12 seconds
```

---

## STORAGE ISSUES

### RUNNING OUT

`$ df -ht ext4` ---> view ext4 type storage in human readable format
```
Filesystem      Type  Size  Used Avail Use% Mounted on
/dev/sda1       ext4   50G   20G   28G  42% /
/dev/sda2       ext4  100G   75G   20G  79% /home
/dev/sdb1       ext4  200G  150G   45G  78% /data
```
`$ du -d 1 /` ---> view directory space summaries
```
4.0K    /bin
16M     /boot
0       /dev
20M     /etc
4.0K    /home
0       /lib
0       /lib64
16K     /lost+found
4.0M    /media
4.0K    /mnt
12M     /opt
du: cannot access '/proc/1234/task/1234/fd/4': No such file or directory
du: cannot access '/proc/1234/task/1234/fdinfo/4': No such file or directory
du: cannot access '/proc/1234/fd/5': No such file or directory
du: cannot access '/proc/1234/fdinfo/5': No such file or directory
32M     /proc
20K     /root
200M    /usr
1.2G    /var
2.7G    /
```

> **!** space can be added dynamically with **LVM**.

---

### I/O ISSUES

**---> kernal I/O scheduling**

| **I/O Scheduler**                 | **Key Features**                                                                                                                                                                                               | **Notes**|
| -                                 | -                                                                                                                                                                                                              | - |
| **CFQ** (Completely Fair Queuing) | - Creates separate queues per process<br>- Processes queues in a loop<br>- Gives higher priority to read requests over write requests<br>- Provides balanced I/O handling<br>- Supports multiprocessor systems | General purpose workloads where balanced I/O fairness is desired|
| **Deadline**                      | - Batches requests<br>- Ensures each request is handled within a set deadline<br>- Provides low latency<br>- Optimized for SSDs<br>- Suitable for real-time applications                                       | Use when predictable I/O latency is important, like in real-time or SSD environments|
| **Noop**                          | - Uses a single FIFO queue<br>- Handles requests strictly in order<br>- Uses minimal CPU resources<br>- Optimized for SSDs                                                                                     | Simple scheduler for SSDs or devices where reordering offers no benefit|


**---> locating system scheduler and changing it**

`ls /sys/block`
```
dm-0    dm-1    sda    sdb
```
`cat /sys/block/sda/queue/scheduler`
```
[ mq-deadline ]  kyber  bfq  none
```
`echo "bfq" > /sys/block/sda/queue/scheduler`
```
```
`cat /sys/block/sda/queue/scheduler`
```
mq-deadline  kyber  [ bfq ]  none
```

---

**`$ iostat `**`[ option ] [ interval ] [ count ]`\
--> monitor system I/O devices by observing the time the devices are active relative to their average transfer rates.

| **Option**        | **Description**|
| -                 | - |
| **-y**            | Excludes statistics collected since system boot; shows only current interval data|
| **-N**            | Displays device mapper names for logical volumes|
| **-z**            | Omits devices with no activity (idle devices)|
| **-c**            | Include CPU usage statistics|
| **-p [ device ]** | Shows detailed statistics for the specified device|

**[ interval ]** : Time (in seconds) between each output report\
**[ count ]** : Number of times the statistics are displayed

> **!** The processor wait on slow disk operations, but installing the **sysstat** package and running **iostat** provides a quick system I/O and CPU summary.

```
$ iostat -yNzc 5 2

Linux 5.15.0-84-generic (test-host)   08/14/2025      _x86_64_    (4 CPU)

Device-mapper name: dm-0 => ubuntu--vg-root

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    1.15   20.85     0.00   94.58

Device            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
sda               9.20        120.00        280.00         600        1400
dm-0              9.20        120.00        280.00         600        1400

<<< wait 5 seconds >>>

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           2.78    0.00    0.95   23.65     0.00   95.62

Device            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
sda               6.00         80.00        180.00         400         900
dm-0              6.00         80.00        180.00         400         900
```
> above stats show a **critically** high **%iowait** percentage. ( 10 < high / 20 < critical )

---

**`ioping `**`[option] [device]`\
--> used to monitor disk I/O latency in real time.

- May overwrite or destroy disk data if misused (especially with write tests).
- Used to measure latency, seek time, sequential read/write speeds, asynchronous I/O rates, caching effects, and direct I/O performance.
```
$ ioping -c 3 /dev/sda

4 KiB from /dev/sda (block device): request=1 time=0.84 ms
4 KiB from /dev/sda (block device): request=2 time=0.79 ms
4 KiB from /dev/sda (block device): request=3 time=0.82 ms

--- /dev/sda ioping statistics ---
3 requests completed in 2.47 ms, 3 iops, 12.0 KiB/s
min/avg/max/mdev = 0.79/0.82/0.84/0.03 ms
```

---

### DISK PERFORMANCE

* The benchmark metric is **IOPS** (Input/Output Operations Per Second).
* The **fio** utility is commonly used for testing.
* **fio** write and read from a file to determine **IOPS**.
* Larger test sizes generally yield more accurate IOPS measurements.

> **SSD Trimming:** A process that identifies and frees memory blocks from deleted files to maintain and improve SSD performance.

---

### FAILING DISKS

* Areas of the disk that fail to respond to I/O requests are labeled **bad sectors**.
* When a bad sector is detected, the firmware attempts to relocate the data and remap the sector to a healthy area.
* A growing number of bad sectors is a warning sign of a failing disk.
* A file may lose its inode number, resulting in an **inode mismatch**.
* The **fsck** tool is used to check and repair **ext** file systems.
* For **Btrfs** file systems, **btrfs check** performs consistency checks and repairs.
* Physical disk wear often manifests as unusual sounds from the drive.
* The **partprobe** command can reload disk partition tables without rebooting, useful after disk replacements.

---

## CPU ISSUES

When buying a CPU consider,

**Number of cores** : More cores allow the CPU to handle multiple tasks simultaneously.\
**Hyperthreading** : Each core run multiple threads, boosting efficiency in workloads that can use parallel processing.\
**Cache size** : Store frequently used data closer to the CPU, reducing latency.

`$ less /proc/cpuinfo` ---> view cpu information.
```
processor   : 0
vendor_id   : GenuineIntel
cpu family  : 6
model       : 158
model name  : Intel(R) Core(TM) i7-8750H CPU @ 2.20GHz
stepping    : 10
microcode   : 0xca
cpu MHz     : 2208.000
cache size  : 9216 KB
physical id : 0
siblings    : 12
core id     : 0
cpu cores   : 6
apicid      : 0
initial apicid  : 0
fpu         : yes
flags       : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr ...

processor   : 1
vendor_id   : GenuineIntel
...
```

`$ uptime` ---> view up-time and cpu load information.
```
 08:30:15 up 10 days,  4:22,  3 users,  load average: 0.15, 0.10, 0.05
```

`$ sar` ---> cpu usage log.
```
$ sar 1 3
Linux 5.15.0-84-generic (hostname)     08/14/2025      _x86_64_        (4 CPU)

12:00:01 AM     CPU     %user     %nice   %system   %iowait    %steal     %idle
12:00:02 AM     all      3.50      0.00      1.25      0.75      0.00     94.50
12:00:03 AM     all      4.00      0.00      1.50      0.50      0.00     94.00
12:00:04 AM     all      3.75      0.00      1.00      0.80      0.00     94.45
Average:        all      3.75      0.00      1.25      0.68      0.00     94.33
```

* Install the **sysstat** package to enable system activity monitoring.
* The directory **/var/log/sa/** stores system activity logs for up to a month.
* By default, **sar** reports today’s CPU usage in **10-minute intervals**.
* The **%steal** column indicates the percentage of CPU time taken by **virtual machines**.

---

## MEMORY ISSUES

* **Disk Buffering**: Frequently accessed data is temporarily stored in RAM to speed up disk I/O.
* The **kernel provides shared memory areas** in RAM for processes to exchange data efficiently, improving inter-process communication speed.
* Use `ipcs -m` to view current shared memory segments.
* Use tools like `free`, `sar`, or `vmstat` to monitor overall memory usage.
* **RAM bottlenecks** can lead to low CPU utilization; adding more RAM may increase the CPU load.

---

### SWAPPING

* Memory is divided into fixed-size units called **pages**.
* When the system runs low on memory, it moves pages from idle processes to disk (using the **swap partition** or **virtual memory**).
* When those processes become active again, the swapped pages are loaded back into RAM.
* **Low available memory** increases swapping activity, which in turn increases disk I/O.
* The `vmstat` command helps monitor swap-related I/O activity.
* **Swap partitions are not recommended on SSDs**, as frequent writes can shorten SSD lifespan.
* Use `swapon -s` to view swap usage and determine whether swap is a file or a dedicated partition.
```
$ swapon -s

Filename                                Type            Size    Used    Priority
/dev/sda2                               partition       2097148 0       -2
/swapfile                               file            1048576 0       -3
```

`$ mkswap /dev/sda3` ---> format partition as swap.

`$ dd if=/dev/zero of=/swapfile bs=1024 count=1048576` ---> create a file of determined size.
`$ mkswap /swapfile` ---> format file as swap.

`$ swapon /dev/sda3` ---> activate.
`$ swapoff /dev/sda3` ---> deactivate.

`$ swapon -p 0 /dev/sda3` ---> change priority.

* **Swap priority** can be adjusted when managing multiple swap locations to control which is used first.
* Users can assign a **priority value between 0 and 32767** (higher values are used first).
* If no priority is specified, the **kernel assigns a negative priority** based on the order the swap areas were activated.

---

### OUT OF MEMORY

* By default, the **kernel overcommits memory**, allowing more memory to be allocated to processes than is physically available.
* This behavior can lead to **low free memory** being reported.
* In critical memory situations, the kernel takes the following steps:
  1. **Reclaims old memory pages** (ie. from caches or inactive processes).
  2. If that’s insufficient, it invokes the **Out-Of-Memory (OOM) Killer**, which:
     * Calculates a **score** for each non-essential process based on its memory usage.
     * Assigns **lower scores** to crucial system processes, kernel threads, and root-owned tasks.
     * Terminates **high-scoring processes** to quickly free up memory.

`sysctl`**`vm.overcommit_memory`**`=[option]`\
--> kernel parameter that controls how the system handles memory overcommit

| **Value** | **Mode**                              | **Description**|
| -         | -                                     | - |
| `0`       | **Heuristic overcommit** *(default)*  | The kernel makes a guess: it may overcommit based on available memory and other factors|
| `1`       | **Always overcommit**                 | Allocations always succeed, regardless of actual available memory. Useful for high-performance apps that manage their own memory|
| `2`       | **Strict overcommit**                 | The kernel only allows allocations if enough memory is truly available (includes a margin). More conservative and safer|

## LOST PASSWORD

- **standard** user passwwords can easily be reset with root privileges.

- **root** password recovery,
1. enter "e" on boot menu to edit boot parameters.
2. find line with "linux" and append 1. (to enter **recovery / emergency / rescue mode**)
3. 'ctrl+x' to boot.
4. change root password with `passwd`.
5. reboot.
