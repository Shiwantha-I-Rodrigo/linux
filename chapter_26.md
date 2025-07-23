# AUTOMATING JOBS

## BACKGROUND MODE

- add a ( **&** ) to the end of the run command to run the script in background mode.

.

    $ ./test.sh &
    [1] 19943

- [1] : shell assigned Job Number
- 19943 : kernal assigned Process ID

.

    $ [1]+ Done     ./test.sh

- at the end of the process the above message will appear on the STDOUT.
- running multiple jobs.

.

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

- the output from previous jobs will appear intermingled with current outputs.
- if the terminal session ends, all the background jobs end as well.

**Running without a Console**

`$ nohup ./test.sh &`

- send the **NOHUP** signal to the process.
- then the **SIGHUP** signals sent by the terminal emulator when exiting will be ignored by the process.
- the **nohup** command disassociates the process from terminal.
- the process looses it's output to monitor.
- the output will be instead redirected to **nohup.out** file in current directory.

## SENDING SIGNALS

### INTERRUPTING A PROCESS

- **Ctrl + C** sends a **SIGINT** signal (Interrupt).
- **Ctrl + Z** sends a **SIGTSTP** signal (Pause).

.

    $ ./test.sh
    [1]+ Stopped        test.sh

    $ ps au
    USER    PID    %CPU    ...    STAT    COMMAND
    Adam    29213  0.0            S       -bash
    Adam    29215  0.2            T       test.sh
    Adam    29244  0.0            R       ps au

- **ps au** command list stopped processes as well.
- S : sleeping / T : stopped / R : running.
- enter the exit command two times to exit the terminal which has stopped jobs.
- or kill the job using it's PID.
- **Ctrl + D** send the EOF char to the input.
- this will end the terminal.
- if the shell is login shell, the user session will end.

### JOB CONTROL

- The **jobs** command allows fine control of current jobs.

.

    -l          List the PID along with job numbers.
    -n          List jobs that changed their status since last notification from the shell.
    -p          List only the PID's of the jobs.
    -r          List only running jobs.
    -s          List only stopped jobs.

multiple job control,

    $ sleep 1000
    ^Z
    [1]+  Stopped                 sleep 1000
    $ sleep 1500
    ^Z
    [2]+  Stopped                 sleep 1500
    $ sleep 2000
    ^Z
    [3]+  Stopped                 sleep 2000
    $ jobs
    [1]   Stopped                 sleep 1000
    [2]-  Stopped                 sleep 1500
    [3]+  Stopped                 sleep 2000

- the ( **+** ) sign indicate the current default job.
- the ( **-** ) sign indicate the next in-line job.
- us ethe **fg** (foreground) or **bg** (background) commands to resume jobs.
- `$ bg 1` : resume the job in background.
- `$ fg 1` : resume job on the terminal.

.

- timers can be set with,
    + **at** command.
    + **cron** command.

**at**

- **at** submits a job to a queue to be run ata certain time.
- **atd** monitors the queue for pending jobs every 60 seconds.
- **at** jobs reside in **/var/spool/at/**.
- there are **26** queues for different priority levels ( **a-z** ).

`$ at -f test.sh 18:56`\
`$ echo ls | at noon`

    at [option] runtime

    -q [queue]      Emails the user after the job has been completed for the queue. Requires a configured email address.
    -m	            Emails the user after the job has been completed, even if there was no output. Requires a configured email address.
    -f [file]	    Reads the job from the specified [file] rather than from standard input.
    -l	            An alias for "$ atq " command. Lists the user's pending jobs. If the user is superuser, it lists all users' pending jobs.
    -d	            An alias for "$ atrm " command. Deletes the scheduled jobs, identified by their job number.
    -c	            Cats the specified job, showing its contents in standard command-line output.
    -t [time_arg]	Schedules a job for the time specified by the [time_arg] argument. The accepted time format is [[CC]YY]MMDDhhmm.

    runtime Formats,

    Time:
        HH:MM
        HHMM
        12-hour format (am or pm)
        midnight
        noon
        teatime ( 16:00 )

    Date:
        MMDD[CC]YY
        MM/DD/[CC]YY
        DD.MM.[CC]YY
        [CC]YY-MM-DD
        today
        tomorrow
        weekday

    Increment:
        priviously listed unit + count time-units
        
        ( where the time-units can be minutes, hours, days or weeks )

**cron**

to schedule a re-occuring task

    Example of job definition:
    .---------------- minute (0 - 59)
    |  .------------- hour (0 - 23)
    |  |  .---------- day of month (1 - 31)
    |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
    |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
    |  |  |  |  |
    *  *  *  *  * user-name  command to be executed

- all users can have their own cron table.
- `$ crontab -l` : list availabe cron tables.
- `$ crontab -e` : create / edit cron table.
- vi editor will open to edit the entries.
- use wildcard * to specify every loop of that unit (ie. day)
- `00 12 * * *` : at noon everyday.
- `00 12 * * 1` : at noon every monday.

**systemd timers**

- systemd startup system can automatically launch programs with delayed timers.
- a timer unit file ( **.timer** ) can specify when a program starts.
- add a [ timer ] section to the file.

Some commenly used timer directives,

    AccuracySec             specify accuracy of the timer.
    OnActiveSec             timer relative to moment the timer is acivated.
    OnBootSec               timer relative to system boot.
    OnCalender              timer as specific date/time.
    OnStartupSec            timer relative to when the systemd initiated.
    OnUnitActiveSec         timer relative to last time the unit activated.
    OnUnitInactiveSec       timer relative to last time the unit deactivated.
    Persistent              store last trigger time on disk.
    RandomizedDelaySec      random delay
    Unit                    unit file to start when timer elapse.
    WakeSystem              resume system on trigger.
