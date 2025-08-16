# AUTOMATING JOBS

## BACKGROUND MODE

Add ( **&** ) to the end of the run command to run the script in background mode.
```
$ ./test.sh &
[1] 19943
```
**[ 1 ]** : shell assigned Job Number\
**19943** : kernal assigned Process ID
```
$ [1]+ Done     ./test.sh
```
At the end of the process the above message will appear on the STDOUT.

---

**---> Multiple Jobs**
```
$ ./test.sh &
[1] 19934

test_loop_1

$ ./test2.sh &
[2] 19952

test_loop_2
test_loop_1

$ ./test3.sh &
[3] 19977

test_loop_3
test_loop_1
test_loop_2
```
- the output from previous jobs will appear **intermingled** with current outputs.
- if the terminal **session ends**, all the **background** jobs **end** as well.

---

**---> Running Without a Console**

`$ nohup ./test.sh &` ---> Send the **NOHUP** signal to ensure the process ignores **SIGHUP** signals from the terminal upon exit.

The **nohup** command **disassociates** a process from the terminal, causing it to lose its output to the monitor.\
Instead, the output is redirected to the **nohup.out** file in the **current directory**.

---

## SENDING SIGNALS

### INTERRUPTING A PROCESS

| **Shortcut Key**  | **Signal Sent**   | **Description**|
| -                 | -                 | - |
| `Ctrl + C`        | `SIGINT`          | Interrupts the process (terminate)|
| `Ctrl + Z`        | `SIGTSTP`         | Suspends the process (pause)|
| `Ctrl + \`        | `SIGQUIT`         | Quits the process and generates a core dump|
| `Ctrl + D`        | *EOF*             | Signals end of input for a **command** or the **terminal** itself<br>In the log-in shell, will end the user session|
| `bg`              | `SIGCONT`         | Resume process in background|
| `fg`              | `SIGCONT`         | Resume process in foreground, hand-over the terminal|
|                   |                   | - `$ bg %1` : resume the job numbered 1 in the background<br>- `$ fg %1` : resume job numbered 1 on the terminal|

**`ps au`** --> list all processes.

**S** : sleeping\
**T** : stopped\
**R** : running\
**+** : in the foreground process group

```
$ ./test.sh
[1]+ Stopped        test.sh

$ ps au
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
user      1532  0.0  0.0  18120  3040 pts/0    S    10:30   0:00 bash
user      1589  0.3  0.1  73520  5600 pts/0    R+   10:32   0:00 top
user      1601  0.0  0.0  42300  2100 pts/0    T    10:33   0:00 nano notes.txt
user      1605  0.0  0.0  38420  1900 pts/0    S    10:34   0:00 sleep 1000
```

- **Foreground process group** is the set of processes **controlling** the terminal and **receiving** user input, usually created by **pipelines** or **job control**.

- **"exit"** must be entered **2** times to exit a terminal that has stopped jobs.

---

### JOB CONTROL

**`jobs`**`[option]`\
--> list active jobs started in the **current** shell session.

```
[1]-  Running                 sleep 300 &
[2]+  Stopped                 nano notes.txt
[3]   Running                 tail -f /var/log/syslog
```

| **Symbol**    | **Meaning**|
| -             | - |
| **[ 1 ]**     | Job ID|
| **+**         | Most recently used job<br>The default for `fg` & `bg`|
| **-**         | Second most recent job|
| **Stopped**   | Job is suspended (usually with `Ctrl + Z`)|
| **Running**   | Job is running in the background (typically with `&` or `bg`)|

| **Option**    | **Description**|
| -             | - |
| **-l**        | List job numbers **with their PIDs**|
| **-n**        | List jobs whose status **has changed** since the last update|
| **-p**        | List **only the PIDs** of the jobs|
| **-r**        | Show **only running** jobs|
| **-s**        | Show **only stopped** jobs|

---

## TIMERS

**`at `**`[option] [time]`\
--> manage jobs for later execution.

| **Option**            | **Description**|
| -                     | - |
| **-q [ queue ]**      | Emails the user after the job completes for the specified queue. Requires a configured email address|
| **-m**                | Emails the user after the job completes, even if there was no output. Requires a configured email address|
| **-f [ file ]**       | Reads the job from the specified file instead of standard input|
| **-l**                | Alias for `atq`; lists the user's pending jobs (all users if superuser)|
| **-d**                | Alias for `atrm`; deletes scheduled jobs by job number|
| **-c**                | Displays the contents of the specified job|
| **-t [ time ]**       | Schedules a job for the specified time|

- **atd** monitors the queue for pending jobs every 60 seconds
- **at** job files reside in **/var/spool/at/**
- **26** virtual queues for different priority levels ( **a - z** )

| **Category**  | **Formats**|
| -             | - |
| **Time**      | **HH:MM**<br>**HHMM**<br> **HH:MM{am/pm}**<br>**HHMM{am/pm}**<br>**midnight**<br>**noon**<br>**teatime**(16:00)|
| **Date**      | **MMDD[CC]YY**<br>**MM/DD/[CC]YY**<br>**DD.MM.[CC]YY**<br>**[CC]YY-MM-DD**<br>**today**<br>**tomorrow**<br>**weekday**|
| **Increment** | ( Time / Date ) + N (minutes / hours / days / weeks)|

`$ at -f test.sh 18:56` ---> run **test.sh** at 18:56\
`$ echo ls | at noon + 2 minutes` ---> run **ls** 2 minutes after noon

---

**`crontab`**`[option]`\
--> manage cron entries.

| **Option**| **Description**|
| -         | - |
| `-e`      | Edit the current user’s crontab file|
| `-l`      | List the current user’s cron jobs|
| `-r`      | Remove the current user’s crontab|
| `-u user` | Specify the user whose crontab you want to manage (requires root)|

```
    Example of job definition:
    .---------------- minute (0 - 59)
    |  .------------- hour (0 - 23)
    |  |  .---------- day of month (1 - 31)
    |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
    |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
    |  |  |  |  |
    *  *  *  *  * user-name  command to be executed
```

- vi editor will open to edit the entries.
- use wildcard * to specify every looping unit (ie. day)
- `00 12 * * *` : at noon everyday.
- `00 12 * * 1` : at noon every monday.

---

**Systemd Timers**

- The **systemd** startup system can automatically launch programs using delayed timers.
- A **timer unit file** (`.timer`) specifies when a program should start.
- To configure this, add a `[Timer]` section to the timer unit file.

( see chapter 6)
