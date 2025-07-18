# SYSTEM PROPERTIES AND REMEDIATION

## NETWORK ISSUES

### TERMINOLOGY

- Bandwidth : max amount of data that can be transfered between two network points for a given time period.
- Throughput : actual amount of data that can be transfered between two network points for a given time period.
- Saturation : bandwidth saturation / congestion  occurs when network traffic exeeds capacity.
- Latency : time between source sending the packet and the destination recieving it.
- Jitter : high variation of latency.
- Routing : sending packets from A to B through a networks various segments.

> increasing routers buffer size to mitigate packet loss leads to bufferbloat / congestion.

### TIMEOUT AND LOSS

- UDP does not guarantee packet delivery.
- UDP is used in VoIP.
- TCP can worsen traffic congestion, due to it's retransmission attempts.
- **timeout is a preset time period for handling unplanned events**.
- network communication timeouts,
    + a system is down
    + incorrect IP's
    + a service is not running or offered.
    + a firewall blocking the traffic.
    + congestion, causing packet loss.

### NAME RESOLUTION

- process of translating a systems, Fully Qualified Domain Name (FQDN) to IP address.
- Domain Name System (DNS).
- Name server location affects the name resolution speed. **/etc/resolv.conf**.
- Cache, holds recent name resolution results. **dnsmasq**.
- Secondary Server, can improve performance.

### NETWORK CONFIGUARATION

- Interface Configurations.
    + check it's IP, MAC, Subnet, error rates.
    + Network Manager with firewalld may block some connections due to it's default zone behavior.
- ports vs sockets,
    + port : is a number used by TCP/UDP to identify which service is transmitting the data.
    + socket : is a single end point to a network connection, IP + port.
    + localhost : hostname for local loopback interface. **127.0.0.1** / **::1**.
    + Unix Socket : similar to network sockets but for between processes on the local system.
    + InterProcess Communication (IPC) happens through Unix Sockets.
    + Unix Sockets are faster than Localhost.
- adapters.
    + system harware that allows network communications.
    + error rates hsould not exceed 0.01 of the throughput.
    + NIC is an adapter, an adapter is not always a NIC.
- RDMA
    + Remote Direct Memory Access.
    + allows direct access between client and servers memory.
    + reduce latency, reduce server CPU load.
    + require special hardware.
    + soft-RoCE (RDMA features over Converged Ethernet) is a software solution.

### NETWORK PERFORMANCE

    iperf, iperf3           Perform network throughput tests.
    iftop -i [adapter]      display network bandwidth usage for [adapter].
    mtr                     display approx. travel times and packet loss percentage,
                            of the first 10 routers in the path.
    nc                      Performs network throughput tests.(netcat)
    netstat -s              display summary stats, broken down by protocol.
                            packet rates not throughput.
    ping, ping6             ICMP packet throughput tests, stats such as round trip times.
    ss -s                   summary stats by socket type.
                            packet rates not throughput.
    tracepath, tracepath6   approx. travel times between each router from source to destination.
                            MTU (max transfer unit) size.
    traceroute, traceroute6 similar but has more features than tracepath.

#### iperf

- **iperf** must be installed on both clent & server.
 
`$ sudo ufw allow 5001`\
< allow port 5001 through firewall >

`$ iperf -s -t 120`

    -s          run as server.
    -t N        timeout service after N seconds.

`$ iperf -c 192.168.0.104 -b 90Kb -d -P 5 -e -i 10`

    -c [IP Address]     create a client, to connect to server at [IP Address].
    -b [Size]           target bandwidth to [size] bits/sec. (default 1Mb)
    -d                  perform bi directional test.
    -P N                create and run N parallel threads.
    -e                  provide enhanced out.
    -i N                pause between periodic bandwidth reports for N seconds.

#### netcat

- **nc** must be installed on both clent & server.

`$ sudo ufw allow 8001`\
< allow port 8001 through firewall >

`$ nc -l 8001 > /dev/null`

    -l          run in listening mode.

`$ dd if=/dev/zero bs=1M count=2 | nc 192.168.0.104 8001 -i 2`

    -i          quit after 2 seconds of idle time.
    -bs=N       block size.
    -count=N    number of blocks.

- output of **dd** command has no **of=** switch therefore it is directed to STDOUT.
- **dd** output is redirected via pipe **|** to **nc** command.


#### mtr

`$ mtr -o "L D A J" -c 20 -r 96.120.112.205`

    -o                      output.
        L                   packet loss.
        D                   packet drop.
        A                   average travel time.
        J                   jitter.
    -c                      number of packets sent.
    -r                      static report.


#### other

tools to show adapter summary stats,

`$ ethtool -S [adapter]`\
`$ ifconfig [adapter]`\
`$ ip -s link show [adapter]`\
`$ netstat -i [adapter]`

    $ ip -s link show enp0s0
    ...
    RX:bytes    packets     errors      dropped     overrun     mcast
    201818183   3023000     1510        0           0           318
    ...

> ! even with out dropped packets error rates may still be too high (0.01+) as shown above.

use packet sniffers to analyse packets.

- wireshark
- tshark
- tcpdump

`$ sudo tshark -i enp0s0 -c 10`

    ...
    capturing on 'enp0s0'
    1 0.00000000  192.168.0.100  ->  238.255.255.250 ...
    ...

### NETWORK CONFIGURATION

! to checkout faulty MAC mappings use,
- arp
- ip neigh

! look for DNS issues,
- host [ FQDN ]
- dig [ FQDN ]
- nslookup
- whois

using **time** utility with **nslookup** to test retrival times,

    $ time nslookup www.linux.org 8.8.8.8
    ...
    Name : www.linux.org
    Address : 104.27.166.219
    ...
    real : 0m0.099s
    ...

! checkout services + TCP ports,
- nmap

.

    $ nmap -sT 127.0.0.4
    ...
    PORT        STATE       SERVICE
    22/tcp      open        ssh
    631/tcp     open        ipp
    ...


## STORAGE ISSUES

### RUNNING OUT

`$ df -ht ext4`\
< view ext4 type storage in human readable format >

    ...
    Filesystem      Size    Used    Avail   Use%    Mounted
    /dev/sda1       9.8G    7.5G    2.1G    79%     /
    ...

`$ du -d 1 /`\
< view directory space summaries >

    ...
    2134536     /var
    ...
    11312232    /home
    ...

> more space can be added with LVM if already setup.


### I/O ISSUES

checkout kernals I/O scheduling, I/O schedulers are,

- cfq
    + create queues for each process.
    + handles queus in a loop.
    + read request priority  > write request priority.
    + balanced I/O handling.
    + multiprocessor.

- deadline
    + batch requests.
    + handles each resuest with in a given time.
    + low latency.
    + SSD.
    + real time applications.

- noop
    + place requests in a single FIFO.
    + handle them in order.
    + less CPU.
    + SSD.

locating system scheduler file, and changind default.

`# ls /sys/block`\
dm-0    dm-1    sda    sdb

`# cat /sys/block/sda/queue/scheduler`\
[ mq-deadline ]  kyber  bfq  none

`# echo "bfq" > /sys/block/sda/queue/scheduler`\

`# cat /sys/block/sda/queue/scheduler`\
mq-deadline  kyber  [ bfq ]  none


#### iostat

- processor may have to wait to write to disk, since disk operations can be slow.
- install **sysstat** package.
- just **iostat** command prints out a system summary.

`$ iostat [ options ] [ interval ] [ count ]`

    -y              Donot include , "since system booted" statistics.
    -N              display device mapper for logical volumes.
    -z              do not show idle devices.
    -p [ device ]   show [ device ] info.

    [ interval ]    how many seconds between each display.
    [ count ]       how many times to display.

.

    $ iostat -yNz 5 2
    ...
    avg-cpu    %user    %nice   %system     %iowait     %steal      %idle
                26.50   0.00    42.40       30.93       0.00        0.00
    ...
    Device      tps     kB_reads/s      kB_wrtn/s   kB_read     kB_wrtn
    sda         409.90  3.30            8765.43     16          12322
    ...

> above stats show a rather high **%iowait** percentage.

#### ioping

- can destroy disk data.
- test latency / seek rates / sequential speeds / async rates / cache / direct I/O.

`# ioping -c 3 /dev/sda`\
< 3 tests >

### DISK PERFORMANCE

- benchmark is IOPS ( Input/Output Operations per second ) value.
- **fio** utility.
- larger the test, more accurate IOPS value.

> SSD Trimming : locating and recovering memory locations of deleted files, to improve performance.

### FAILING DISKS

- chunks of space not responding to I/O requests are marked **bad Sectors**.
- when a Bad Sector is markedfirmware try to move the data to a new location, and remap the sector.
- increasing number of Bad Sectors indicate a failing disk.
- a file may loose it's inode number, called a **missmatch**.
- **fsck**  can check and repair **ext** file systems.
- **brtfs check** for **brtfs** file systems.
- physical wear and tear, indicated by sounds.
- **partprobe** help re reading disk parttion tables with out rebooting, in case of replacing disks.

## CPU ISSUES

When buying a CPU consider,
- number of cores
- hyperthreading
- cache size

`$ less /proc/cpuinfo` < view CPU info >

`$ uptime`

    15:12:19  up  54min,  2 users,  load average: 0.95, 0.93, 0.90

> above indicate a rather high CPU usage average.

- install **sysstat** package.
- just **sar** command prints out a CPU usage log.
- **/var/log/sa/** contain a month worth of logs.
- by default **sar** prints today's CPU usage in **10 min** inetrvals.
- **%steal** column show how much is used by **Virtual Machines**.

## MEMORY ISSUES

- DISK BUFFERING : storing frequently used data in memory.
- kernal provide shared memory areas on RAM for processes to I/O, increasing process interactions speeds.
- view shared memory, **ipcs -m**.
- view memory stats with, **free** / **sar** / **vmstat**.
- often RAM bottlenecks keep CPU uage low, incresing RAm may also increase CPU load.

### SWAPPING

- memory is divided into chunks ( **pages** ).
- when system require memory, it takes an idle process's memory pages and copy them to disk (**swap partition** / **virtual memory**).
- when the idle process resumes, swapped pages are re-copied into memory.
- Low memory increase swapping, increasing I/O.
- **vmstat** show I/O related to swapping.
- SWAP partitions are not recomended on SSD drives, cause early SSD death.
- **swapon -s** display swap info. can be used to determine if swap is a file or partition.

.

    $ swapon -s
    Filename        Type        Size        Used        Priority
    /dev/sda2       partition   1765342     0           -2

< swap partition >

`$ sudo mkswap /dev/sda3`

< swap file >

`$ sudo dd if=/dev/zero of=/swapfile bs=1024 count=1048576`\
`$ sudo mkswap /swapfile`

< activate swap >

`$ sudo swapon /dev/sda3`

- changing swap priority, when manging multiple swap locations.

`$ swapoff /dev/sda3`

`$ swapon -p 0 /dev/sda3`

- user assigned priority can be (0-32767).
- negative priority values are assigned by kernal, in order the swap location was added.


### OUT OF MEMORY

- by default kernal over-commits memory to various processes.
- this behavior causes low free memory availability
- in critical situations,
    + first reclaims old memory pages.
    + employs the **out of memory killer (OOM)**.
        * create a score for each non crucial process base on memory usage.
        * low scores for kernal, root and crucial processes. 
        * high scoring processes are killed off.

`sudo sysctl vm.overcommit_memory=2`

- 0: Allows moderate overcommit, but unreasonable allocations will fail.
- 1: All allocation requests are granted. This leads to the OOM killer being invoked more frequently. 
- 2: Disables overcommit. Allocations will fail if there is not enough physical memory.


## LOST PASSWORD

- STANDARD user passwwords can easily be reset with root privileges.
- but for root password recovery,

1. enter "e" on boot menu to edit boot parameters.
2. find line with "linux" and append 1. (to enter **recovery / emergency / rescue mode**)
3. 'ctrl+x' to boot.
4. change root password with `passwd`.
5. reboot.
