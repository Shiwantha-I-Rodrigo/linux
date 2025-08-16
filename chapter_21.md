# OPTIMIZING PERFORMANCE

## MONITOR PROCESSES

> **!** At boot, Linux starts the **init** process (PID 0), from which all other processes originate.

---

**`ps `**`[option]`\
--> displays information about active (running) processes.

The `ps` command supports three types of parameters :

* Unix-style options (with a `-` prefix)
* BSD-style options (without a prefix)
* GNU-style long options (with a `--` prefix)
```
$ ps
PID TTY          TIME CMD
2345 pts/1    00:00:12 bash
7890 pts/1    00:00:00 ps
```
```
$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 169580  5380 ?        Ss   08:30   0:01 /sbin/init
alice     2345  2.3  1.5 275432 31000 pts/1    Rl   09:10   0:12 /usr/bin/python3 myscript.py
bob       7890  0.1  0.2  98040  4100 pts/2    S    09:15   0:00 bash
root      4321  0.0  0.0      0     0 ?        Z    09:20   0:00 [kworker/0:1]
```
```
$ ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 08:30 ?        00:00:01 /sbin/init
alice     2345  1234  2 09:10 pts/1    00:00:12 /usr/bin/python3 myscript.py
bob       7890  2345  0 09:15 pts/2    00:00:00 bash
root      4321     1  0 09:20 ?        00:00:00 [kworker/0:1]
```

| Column    | Description|
| -         | - |
| **UID**   | User ID|
| **PID**   | Process ID|
| **PPID**  | Parent Process ID|
| **C**     | CPU usage over the lifetime of the process|
| **STIME** | System time when the process started|
| **TTY**   | Associated terminal|
| **TIME**  | Cumulative CPU time used|
| **CMD**   | Name of the program|
|           | If the name is in brackets, the process has been swapped out to virtual memory (**sleeping**)|
|           | When an event occurs, a signal is sent to the **sleeping** process :<br>- In **interruptible sleep**, it wakes up<br>- In **uninterruptible sleep**, it waits for an external event (ie. hardware), signals are queued until it resumes|

> **!** If a process ends but its parent hasn’t acknowledged it, it becomes a **zombie** — stuck between running and termination.

---

**`top `**`[option]`\
--> monitor real-time system processes and resource usage.

```
top - 10:45:32 up 3 days,  2:17,  2 users,  load average: 0.42, 0.36, 0.25
Tasks: 145 total,   1 running, 142 sleeping,   1 stopped,   1 zombie
%Cpu(s):  5.3 us,  1.2 sy,  0.0 ni, 92.8 id,  0.5 wa,  0.0 hi,  0.2 si,  0.0 st
MiB Mem :   7859.3 total,   1023.5 free,   3941.0 used,   2894.8 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   3555.0 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND     
 2345 alice     20   0  275432  31000   5032 R   3.0  0.4   0:12.34 python3      
 1243 root      20   0  161200   4500   3100 S   0.3  0.1   0:00.98 sshd         
 7890 bob       20   0   98040   4100   3200 S   0.0  0.1   0:00.02 bash         
    1 root      20   0  169580   5380   3200 S   0.0  0.1   0:01.45 systemd
```

---> top information

| Line                  | Information|
| -                     | - |
| 1                     | - Current time<br>- uptime<br>- number of logged in users<br>- load average (1min, 5min, 15min)|
| 2                     | - Number of processes<br>- running<br>- sleeping<br>- stopped<br>- zombie|
| cpu time info         | - **us** : user applications<br>- **sy** : system resources<br>- **ni** : low priority processes<br>- **id** : idling processes<br>- **wa** : waiting for disk or network<br>- **hi** : processing hardware interrupts<br>- **si** : processing software interrupts<br>- **st** : steal time|
| physial memory stats  | - total<br>- free<br>- used<br>- cache|
| swap memory stats     | - total<br>- free<br>- used<br>- availabe|
| process information   | - **PID** : process id<br>- **USER** : user name<br>- **PR** : priority<br>- **NI** : nice value<br>- **VIRT** : virtual mem. used<br>- **RES** : physical mem. used<br>- **SHR** : shared memory usage<br>- **S** : process status<br>- **%CPU** : CPU usage<br>- **%MEM** : memory usage<br>- **TIME+** : total CPU time<br>- **COMMAND** : program name|
|                       | **Process Status**<br> D : inturruptible sleep<br> R : running<br> S : sleeping<br> T : traced / stopped<br> Z : zombie|

---> top interactive actions

| **Key**       | **Action**|
| -             | - |
| **1**         | Toggle between single CPU and multi-processor view |
| **b**         | Toggle bold text for important information|
| **l**         | Show or hide the load average line|
| **t**         | Show or hide CPU usage line|
| **m**         | Show or hide memory usage lines|
| **f**         | Add or remove columns in the display|
| **o**         | Change the order of displayed columns|
| **F** / **O** | Choose which column to sort by (default is **%CPU**) |
| **<** / **>** | Move the sort column left or right|
| **R**         | Toggle sorting in reverse order|
| **h**         | Toggle display of threads|
| **c**         | Toggle between command name and full command line  |
| **i**         | Show or hide idle processes|
| **S**         | Toggle between cumulative and relative CPU time    |
| **x**         | Highlight the current sort column|
| **y**         | Highlight running processes|
| **u**         | Show processes for a specific user|
| **n** / **#** | Set the number of processes to display|
| **k**         | Kill a selected process|
| **r**         | Change the priority (nice value) of a process|
| **d** / **s** | Change the screen update interval|
| **W**         | Save the current configuration to a file|
| **q**         | Quit the `top` program|

> **htop** is the improved version of **top**

---

## MANAGE PROCESSES

### SETTING PRIORITIES

**nice / renice**

`$ nice -n 10 ./myscript.sh`\
--> change priority for a **new** process.

`$ renice 13 -p 3145`\
--> change priority for a **running** process.

* The Linux kernel determines whether to apply `nice` or `renice` adjustments based on current system conditions.
* If the system is under heavy load, it may ignore requests to change process priorities using `nice` or `renice`.
* Nice values range from **-20** (highest priority) to **19** (lowest priority).
* Only the **root** user can lower a process's nice value below **0**.

---

### STOPPING PROCESSES

* If a process becomes unresponsive or consumes excessive resources, it must be brought under control.
* In Linux, processes communicate through **signals**.
* When a process receives a signal, it can either handle it according to its programming or ignore it.

|**Signal No.** |**Signal Name**    | **Description**|
| -             | -                 | - |
| 1             | `SIGHUP`          | Hang up detected; often used to restart processes|
| 2             | `SIGINT`          | Interrupt from keyboard (usually Ctrl+C)|
| 3             | `SIGQUIT`         | Quit request; stops the process and creates a core dump|
| 9             | `SIGKILL`         | Forcefully kills the process; cannot be ignored|
| 11            | `SIGSEGV`         | Segmentation fault (invalid memory access)|
| 15            | `SIGTERM`         | Graceful termination request (default kill signal)|
| 17            | `SIGSTOP`         | Unconditionally stops (pauses) the process|
| 18            | `SIGTSTP`         | Pause the process (usually via Ctrl+Z); keeps it alive in the background|
| 19            | `SIGCONT`         | Resumes a process that was stopped (by `STOP` or `TSTP`)|

**kill**

> **!** To send a process signal, sender must be the **owner of the process** or **root**.

`$ kill 3987`\
--> by default sends a **SIGTERM** signal.

`$ kill -s SIGINT 3987`\
--> sending a **SIGINT** signal.

> **!** before sending a **SIGKILL** signal, check if the process have open files to prevent corruption.\
> **!** `$ lsof -p 3987`

**pkill**

`$ pkill http*`\
--> kill every process starting with **http**

> **!** before batch killing processes, use **pgrep** to check for matching processes.\
> **!** `$ pgrep http*`
