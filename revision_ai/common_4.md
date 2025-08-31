## **Scenario 1: A process is hanging and slowing down the server**

### **Step-by-step workflow**

1. **Identify processes consuming resources**

   ```bash
   top
   ```

   * `top` provides a real-time view of CPU and memory usage.
   * Common workflow: identify processes with high `%CPU` or `%MEM`.

2. **Check detailed info about the problematic process**

   ```bash
   ps -ef | grep <process_name>
   ```

   * `ps -ef` lists all processes with full details (UID, PID, PPID, CPU usage, command, etc.).
   * Useful to confirm the PID of the process causing issues.

3. **Adjust priority of a process that is important**

   ```bash
   renice -n 10 -p <pid>
   ```

   * `renice` changes the priority of a running process.
   * Increasing the nice value lowers priority, freeing CPU for other critical processes.

4. **Terminate a misbehaving process**

   ```bash
   kill -s SIGTERM <pid>
   ```

   * Sends `SIGTERM` to allow graceful shutdown.
   * If unresponsive after a few seconds:

     ```bash
     kill -s SIGKILL <pid>
     ```

5. **Verify process termination**

   ```bash
   ps -ef | grep <process_name>
   ```

   * Ensures the process is no longer running.

---

## **Scenario 2: Running a long job without disruption**

### **Step-by-step workflow**

1. **Start a command immune to hangups**

   ```bash
   nohup long_running_backup.sh &
   ```

   * `nohup` ensures the command continues even if the SSH session closes.
   * `&` sends it to the background.

2. **Check background jobs**

   ```bash
   jobs
   ```

   * Lists all jobs started in the current shell and their status (`Running` or `Stopped`).

3. **Bring a job to foreground if interaction is needed**

   ```bash
   fg %1
   ```

   * Brings job number 1 to the foreground for input or monitoring.

4. **Resume a stopped job in the background**

   ```bash
   bg %1
   ```

   * Useful if the job was accidentally paused with `Ctrl+Z`.

---

## **Scenario 3: Monitoring I/O bottlenecks**

### **Step-by-step workflow**

1. **Check real-time I/O per process**

   ```bash
   iotop
   ```

   * Shows which processes are generating the most disk I/O.

2. **Check CPU vs disk I/O for devices**

   ```bash
   iostat -c -p /dev/sda 2 5
   ```

   * `-c` → CPU usage only.
   * `-p /dev/sda` → Specific device stats.
   * `2 5` → Run 5 reports at 2-second intervals.

3. **Analyze system-wide resource usage**

   ```bash
   vmstat 2 5
   ```

   * Summarizes processes, memory, paging, CPU, I/O.
   * Useful for seeing trends over time (every 2 seconds, 5 reports).

4. **Combine multiple stats for in-depth analysis**

   ```bash
   dstat -cdngy
   ```

   * `dstat` is like `vmstat + iostat + netstat` combined.
   * Tracks CPU, disk, network, and system stats simultaneously.

---

## **Scenario 4: Killing processes by name**

### **Step-by-step workflow**

1. **Identify and kill all instances of a process**

   ```bash
   pkill firefox
   ```

   * `pkill` kills processes matching a name pattern.

2. **Verify termination**

   ```bash
   ps aux | grep firefox
   ```

   * Ensures no residual processes are running.

---

## **Scenario 5: Running a command with different scheduling priority**

1. **Run a backup script with low priority**

   ```bash
   nice -n 19 ./backup.sh
   ```

   * `-n 19` → sets lowest priority, ensuring minimal impact on interactive users.

---

## **Summary of options used in these workflows**

| Command  | Options/flags used         | Purpose                                           |
| -------- | -------------------------- | ------------------------------------------------- |
| `ps`     | `-ef`                      | Full-format listing to see all processes and PIDs |
| `kill`   | `-s SIGTERM`, `-s SIGKILL` | Graceful and forced termination of processes      |
| `renice` | `-n <value> -p <pid>`      | Adjust priority of running processes              |
| `nohup`  | `&`                        | Run jobs immune to hangups in the background      |
| `jobs`   | N/A                        | List background jobs                              |
| `fg`     | `%[job_number]`            | Bring background job to foreground                |
| `bg`     | `%[job_number]`            | Resume a suspended job in background              |
| `top`    | N/A                        | Real-time monitoring of CPU/memory usage          |
| `iotop`  | N/A                        | Real-time per-process I/O usage                   |
| `iostat` | `-c -p /dev/sda`           | CPU usage and per-device I/O stats                |
| `vmstat` | `interval count`           | System-wide performance snapshot over time        |
| `dstat`  | `-cdngy`                   | Combined CPU, disk, network, and system stats     |
| `pkill`  | `<pattern>`                | Kill processes by name pattern                    |
| `nice`   | `-n <value>`               | Run a command with adjusted scheduling priority   |

---

## **Scenario 6: Web application server suddenly becomes unresponsive**

1. **Check system load first**

   ```bash
   top
   ```

   * See if `httpd` (RHEL) or `apache2` (Ubuntu) is consuming too much CPU or memory.
   * Look for high load averages in the top right.

2. **Look at running processes in detail**

   ```bash
   ps -ef | grep apache2
   ```

   * Confirms how many worker processes are running.
   * Helps identify runaway workers.

3. **Gracefully stop all Apache processes**

   ```bash
   pkill apache2
   ```

   * Kills all Apache processes by name.
   * Safer and quicker than manually killing each PID.

4. **Restart the service**

   ```bash
   systemctl restart apache2
   ```

   * After cleaning, restart cleanly.
   * Verify with:

     ```bash
     systemctl status apache2
     ```

---

## **Scenario 7: Disk I/O bottleneck detected by monitoring system**

1. **Check per-process I/O activity**

   ```bash
   iotop
   ```

   * See if a database or logging process is hammering the disk.

2. **Check disk stats over time**

   ```bash
   iostat -xz 2 5
   ```

   * `-x` → extended stats (utilization, await, svctm).
   * `-z` → skip devices with no activity.
   * Interval 2 seconds, 5 samples.

3. **Look at virtual memory and paging**

   ```bash
   vmstat 2 5
   ```

   * If `si` and `so` (swap in/out) are high, system is swapping.

4. **Cross-check with general system resources**

   ```bash
   dstat -cdngy
   ```

   * Single view for CPU, disk, net, and paging.

---

## **Scenario 8: Admin accidentally stops a database job**

1. **You typed Ctrl+Z by mistake during a long query**

   * This suspends the process.

2. **List jobs**

   ```bash
   jobs
   ```

   * Shows `[1]+  Stopped  psql` (for example).

3. **Bring it back to foreground**

   ```bash
   fg %1
   ```

   * Resumes the PostgreSQL client in the foreground.

4. **Or let it run in background**

   ```bash
   bg %1
   ```

   * Continues running without holding your shell.

---

## **Scenario 9: Overnight data backup causes system slowdown**

1. **Check what is running**

   ```bash
   ps -ef | grep backup
   ```

   * Confirms which backup process is running.

2. **Lower priority of the backup**

   ```bash
   renice 19 -p <pid>
   ```

   * Ensures it does not starve interactive processes.

3. **Alternatively start it with nice**

   ```bash
   nice -n 19 ./backup.sh
   ```

   * Prevents future backups from hogging CPU.

4. **If you want it immune to SSH logout**

   ```bash
   nohup ./backup.sh > backup.log 2>&1 &
   ```

   * Job continues even if the admin disconnects.

---

## **Scenario 10: System performance issue needs historical analysis**

1. **Check live stats**

   ```bash
   sar -u 2 5
   ```

   * CPU utilization every 2 seconds, 5 samples.

2. **Check yesterday’s data**

   ```bash
   sar -q -f /var/log/sa/sa28
   ```

   * `-q` → run queue and load averages.
   * `-f` → use stored binary log file (`sa28` means 28th day of the month).
   * Lets you analyze what happened overnight.

---

## 🔹 **Summary of More Advanced Usage**

| Command              | Common Options       | Real-Life Purpose                                              |
| -------------------- | -------------------- | -------------------------------------------------------------- |
| `pkill`              | `<pattern>`          | Quickly kill all processes of a service (apache2, nginx, etc.) |
| `iostat`             | `-xz`                | Extended stats + suppress idle devices                         |
| `vmstat`             | `interval count`     | Track CPU/mem/paging during issue                              |
| `sar`                | `-u -q -f <file>`    | Live and historical performance data                           |
| `renice`             | `19 -p <pid>`        | Lower priority for heavy background tasks                      |
| `nohup`              | `&`, log redirection | Keep long jobs running after logout                            |
| `jobs` / `fg` / `bg` | `%[job_number]`      | Resume suspended or backgrounded jobs                          |

---
