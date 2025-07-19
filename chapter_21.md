# OPTIMIZING PERFORMANCE

## MONITOR PROCESSES

- when linux bootup it runs the **init** process.
- all other processes are initiated by the **init** process, using scripts.

### ps

    $ ps
    PID     TTY     TIME        CMD
    2323    pts/0   00:00:00    bash
    2876    pts/0   00:00:00    ps

- currently the ps command supports,
    + Unix-style parameters (-).
    + BSD-style parameters.
    + GNU parameters (--).

`$ ps -ef`\
< every process from every user >

- UID : user ID
- PID : process ID
- PPID : parent process ID
- C : CPU usage over life time of the process.
- STIME : system time when process started.
- TTY : initial terminal.
- TIME : cumulative CPU time.
- CMD : name of the program.
    + if the name is in brackets, the process has been swapped into virtual memory.
    + when a relevent event triggers, a signal is sent to the sleeping process.
    + if it is in interruptible sleep mode, it will wake up.
    + if it is in uninterruptible sleep mode, it will wait for an external event.
        * such as hardware becomming availabe.
        * it will save signals while sleeping and act on them when woken up.

> if a process has ended but the parent process hasn't acknowledged it,\
> the process is stuck between running and terminating, untill acknowledged.
> **Zombie**.

### top

- first line
    + current time.
    + up time.
    + no. of logged in users.
    + load average ( 1min, 5min, 15 min).

- second line
    + no. of processes.
    + running.
    + sleeping.
    + stopped.
    + zombie.

- cpu info (time spent for)
    + us : user applications.
    + sy : system resources.
    + ni : low priority processes.
    + id : idling.
    + wa : waiting for disk or network.
    + hi : processing hardware interrupts.
    + si : processing software interrupts.

- physical memory stats.

- swap memory stats.

- process information
    + PID : process id.
    + USER : name.
    + PR : priority.
    + NI : nice value of the process.
    + VIRT : amount of virtual mem. used.
    + RES : amount of physical mem. used.
    + SHR : shared memory usage.
    + S : process status.
        * D - inturruptible sleep.
        * R - running.
        * S - sleeping.
        * T - traced or stopped.
        * Z - zombie.
    + %CPU : cpu usage %.
    + %MEM : mem. usage %.
    + TIME+ : total CPU time used.
    + COMMAND : program name.

.

    < top > interactive commands.

    1       toggle single cpu / multi processor state.
    b       toggle bolding important info.
    l       toggle load average line.
    t       toggle cpu info line.
    m       toggle mem info lines.
    f       add remove different columns.
    o       change order of columns.
    F / O   select sort column ( default %CPU).
    < / >   move sort column.
    R       toggle reverse order.
    h       toggle showing threads.
    c       toggle command name / full command line.
    i       toggle idle processes.
    S       toggle cum. cpu time / relative cpu time.
    x       toggle highlighting sort column.
    y       toggle highlighting running processes.
    u       show processes for specific user.
    n / #   set no. of processes to display.
    k       kill a process.
    r       change process priority.
    d / s   change update interval.
    W       writes current settings to file.
    q       quit.

> **htop** is the improved version of **top**


## MANAGE PROCESSES

### SETTING PRIORITIES

#### nice / renice

`$ nice -n 10 ./myscript.sh`\
< change priority for a new process >

`$ renice 13 -p 3145`\
< change priority for a running process >

- the linux kernal decide whether to accept the nice / renice change, depending on the situation.
- if the system is loaded it may ignore the nice / renice commands.
- nice value can be from **-20** to **19**.
- only the **root** can decrese the nice value of a running process or set values less than 0.

### STOPPING PROCESSES

- incase the process is hung up or running away it must be controled.
- linux processes communicate using **process signals**.
- a process may recive a signal and choose to act on it or neglect it.

.

    1       SIGHUP      hang Up.
    2       SIGINT      Interrupt.
    3       SIGQUIT     stop.
    9       SIGKILL     kill unconditionally.
    11      SIGSEGV     segments violation.
    15      SIGTERM     terminate if possible.
    17      SIGSTOP     stop unconditionally.
    18      SIGTSTP     stop or pause but keep alive in background.
    19      SIGCONT     resume after "STOP" or "TSTP".

#### kill

> to send a process signal, must be **owner of the process** or the **root**.

`$ kill 3987`\
< by default sends a **SIGTERM** signal >

`$ kill -s SIGINT 3987`\
< sending a **SIGINT** signal >

- before sending a **SIGKILL** signal determine if the process have open files to prevent corruption.

`$ lsof -p 3987`

#### pkill

`$ pkill http*`\
< kill every process starting with **http** >

- before batch killing processes, use **pgrep** to check for matching processes.

`$ pgrep http*`
