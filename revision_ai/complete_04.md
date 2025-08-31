# 🔹 Scenarios

## **Scenario 1: The Overworked Sysadmin**

It’s late at night, and you’re running a large backup on RockyOS 8.5. You:

1. Pause the backup temporarily.
2. Continue it in the background while you multitask.
3. Check which jobs are still running.
4. Bring it to the foreground when you want to watch progress.
5. Try to stop it cleanly, but it won’t shut down.
6. Forcefully terminate it.

---

## **Scenario 2: The Database Admin Under Pressure**

On an Ubuntu 20.04 server, you launch a MySQL import job that’s hogging resources. You:

1. Start a new import with reduced priority.
2. List all processes in full detail.
3. Print only `pid`, `ppid`, and `cmd` for readability.
4. Lower the priority of the first job by PID.
5. Kill an unrelated runaway Python script without looking up its PID.

---

## **Scenario 3: The Night Shift Monitoring Marathon**

On Fedora 34, you’re tasked with system monitoring. You:

1. Run a script that must survive logout.
2. Watch CPU/memory in real-time interactively.
3. Switch to a more visual process viewer.
4. Identify which processes are causing I/O load.
5. Collect CPU-only stats for a few cycles.
6. Gather disk stats only for `sda`.
7. Filter out zero-activity lines.
8. Skip boot averages in the first report.
9. Show device stats by name instead of number.
10. Summarize performance (CPU, memory, paging, I/O).
11. Generate activity reports across time.
12. Monitor CPU, disk, and network in one tool.

---

## **Scenario 4: The Software Developer’s Stress Test**

You’re testing a new C++ program on Ubuntu 20.04. With input:

```txt
line1
line2
line3
```

1. Run the program in the background with the input file.
2. Check that it’s running as a job.
3. Inspect process usage with BSD-style output.
4. Monitor resource usage while it runs.
5. Suspend it, then bring it foreground, then background again.
6. Check jobs list after toggling.
7. Kill it once testing is done.

---

## **Scenario 5: The Unexpected Incident**

During peak hours, your Fedora 34 server is slow. You:

1. Use a process listing to identify the culprit process with full details.
2. Narrow output to only `pid`, `ppid`, and command for clarity.
3. Adjust the CPU priority of the greedy process downwards without killing it.
4. Monitor which processes are causing high disk I/O live.
5. Generate system activity reports for later troubleshooting.

---

## **Scenario 6: Background job juggling while coding**

You’re editing a large log file (`/var/log/syslog`) with `nano`, but midway you need to free your terminal without losing your editing session. You decide to suspend the editor and continue working on a Python script. Later, you need to check all jobs running, resume the editor in the background, and then bring it back to the foreground to save changes.

---

## **Scenario 7: Cleaning up runaway processes**

You accidentally launched a program `stress_cpu.sh` that eats up CPU resources. You first identify all processes currently running, then look specifically for the `stress_cpu.sh` process in the listing. Instead of killing it by PID directly, you try killing it by its name. When that fails, you retry by sending it a stronger termination signal with its PID.

---

## **Scenario 8: Prioritizing a data analysis task**

You’re running a script called `data_clean.py` that takes hours to complete. To ensure it finishes faster, you launch it with a high priority. Later, you notice other tasks need attention and you lower its priority by adjusting its nice value.

---

## **Scenario 9: Running jobs immune to logout**

You’re connecting to a remote server to run a backup script `backup.sh`. Since the script takes several hours, you don’t want it to stop if your SSH session disconnects. You launch the job in a way that survives logout and redirect its logs to a file.

---

## **Scenario 10: Monitoring your system during heavy load**

While your server is running multiple containers, the system slows down. You decide to investigate by:

* Viewing a list of all processes with full details.
* Checking real-time CPU and memory usage interactively.
* Using an advanced viewer with colors and filters.
* Checking combined stats for CPU, disk, and network.
* Viewing CPU and disk I/O statistics repeatedly every 2 seconds.
* Suppressing zero-activity lines and focusing on a specific device `/dev/sda`.
* Checking which processes are consuming the most I/O.
* Summarizing overall memory, paging, and CPU usage.
* Collecting periodic system reports over time.

---

## **Scenario 11: Diagnosing job performance impact**

You run a test script `big_sort.sh` but notice it slows everything down. You check the system activity with different tools, confirm it’s causing high I/O, then decide to temporarily lower its priority while it runs in the background so other processes remain responsive.

---

#  🔹 Example Text Files Used

`stress_cpu.sh`

```bash
#!/bin/bash
# Simulate high CPU usage
while :; do :; done
```

`data_clean.py`

```python
import time
for i in range(10**6):
    pass
print("Data cleaned.")
```

`backup.sh`

```bash
#!/bin/bash
tar -czf /tmp/backup.tar.gz /etc /home
echo "Backup complete."
```

`big_sort.sh`

```bash
#!/bin/bash
sort -n bigdata.txt > sorted_bigdata.txt
```

`bigdata.txt`

```
983
12
456
34
789
...
```

---

# 🔹 Answer Keys

## Scenario 1

```bash
Ctrl+Z
bg %1
jobs
fg %1
kill -s SIGTERM <pid>
kill -s SIGKILL <pid>
```

---

## Scenario 2

```bash
nice -n 10 ./import.sh
ps -ef
ps -o pid,ppid,cmd
renice 15 -p <pid>
pkill python
```

---

## Scenario 3

```bash
nohup ./long_task.sh &
top
htop
iotop
iostat -c 2 5
iostat -p sda 2 5
iostat -z 2 5
iostat -y 2 5
iostat -N 2 5
vmstat 2 5
sar 2 5
dstat
```

---

## Scenario 4

```bash
./myapp < test_input.txt &
jobs
ps aux
dstat
Ctrl+Z
fg %1
bg %1
jobs
kill <pid>
```

---

## Scenario 5

```bash
ps -ef
ps -o pid,ppid,cmd
renice 10 -p <pid>
iotop
sar 2 5
```

---

## **Scenario 6**

```bash
nano /var/log/syslog        # open editor
# suspend with Ctrl+Z
jobs                        # list jobs
bg %1                       # resume in background
fg %1                       # bring editor back to foreground
```

---

## **Scenario 7**

```bash
ps -ef | grep stress_cpu.sh   # find process
pkill stress_cpu.sh           # try killing by name
ps aux | grep stress_cpu.sh   # confirm still running
kill -s SIGKILL <pid>         # kill forcefully by PID
```

---

## **Scenario 8**

```bash
nice -n -5 python3 data_clean.py &   # run with higher priority
ps -o pid,ppid,cmd -ef | grep data_clean.py
renice 10 -p <pid>                   # lower its priority later
```

---

## **Scenario 9**

```bash
nohup ./backup.sh > backup.log 2>&1 &
```

---

## **Scenario 10**

```bash
ps -ef                              # full format
ps aux                              # BSD style
ps -o pid,ppid,cmd                  # custom output
top                                 # real-time viewer
htop                                # enhanced viewer
dstat                               # combined system stats
iostat -c 2 5                       # CPU usage every 2s for 5 reports
iostat -p /dev/sda -z -y 2 3        # focus on device with zero suppression
iotop                               # I/O heavy processes
vmstat                              # memory, CPU, paging summary
sar 1 5                             # collect system activity every second, 5 times
```

---

## **Scenario 11**

```bash
./big_sort.sh &
jobs
ps -ef | grep big_sort.sh
iotop                               # confirm heavy I/O
renice 15 -p <pid>                  # lower priority
bg %1                               # keep in background
```

---
