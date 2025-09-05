### Sample Files to Create First

Before diving into the scenarios, make sure these sample scripts and files exist in your working directory:

1. **`cpu_hog.sh`**

   ```bash
   #!/bin/bash
   while true; do :; done
   ```

2. **`io_hog.sh`**

   ```bash
   #!/bin/bash
   while true; do dd if=/dev/zero of=/tmp/io_hog_testfile bs=1M count=1 oflag=append conv=notrunc; done
   ```

3. **`net_hog.sh`**

   ```bash
   #!/bin/bash
   while true; do curl -s https://example.com > /dev/null; done
   ```

4. **`slow_logger.sh`**

   ```bash
   #!/bin/bash
   for i in {1..100}; do
       echo "Log entry $i" >> slow_logger.log
       sleep 2
   done
   ```

Make all scripts executable:

```bash
chmod +x cpu_hog.sh io_hog.sh net_hog.sh slow_logger.sh
```

---

### Flowy Story Scenarios

**Scenario 1: The Rogue CPU Gobbler**
You’ve accidentally started `cpu_hog.sh` twice in the foreground. One you suspend, the other you want to demote politely with `nice`. List jobs, shove one to the background, and then try to bring it back. At some point, you realize you’d rather `renice` an already running hog than kill it outright.

---

**Scenario 2: The Silent Logger That Won’t Quit**
You launch `slow_logger.sh` with `nohup` so it keeps running even after logout. After some time, you forget whether it’s still around. Use `ps` in different formats (`-ef`, `aux`, and `-o pid,ppid,cmd`) to hunt it down. Once found, experiment with killing it using both `kill -s SIGTERM` and `pkill` by name.

---

**Scenario 3: Net Hog vs. Sysadmin Patience**
You’ve launched `net_hog.sh` with a low priority using `nice -n 15`. After monitoring system performance in `top` and `htop`, you decide its punishment isn’t severe enough. Crank it up with `renice`. Finally, finish it with `kill -s SIGKILL` once you’ve had enough.

---

**Scenario 4: Disk Drama in `/tmp`**
The `io_hog.sh` script starts writing massive junk data in the background. You forgot you started it, but you notice the system acting sluggish. Use `iotop` to spot the culprit, then confirm with `iostat -c`, `iostat -z`, and `iostat -p [device]`. Once sure, `pkill` it by pattern.

---

**Scenario 5: The Big Picture Panic**
Users are complaining “the server feels weird.” You don’t know if it’s CPU, disk, or memory. Quickly cycle through `dstat`, `vmstat`, and `sar` to gather insights. Compare the outputs with `top` to confirm what’s eating resources. Use whatever control tools (`kill`, `renice`, `fg/bg`, etc.) are needed to restore sanity.

---
---
---

## **Scenario 1: The Rogue CPU Gobbler**

1. Start two CPU hog scripts in the foreground:

```bash
./cpu_hog.sh &
./cpu_hog.sh
```

* The second one is in the foreground.

2. Suspend the foreground job:

```bash
Ctrl+Z
```

* Output example:

```
[2]+  Stopped                 ./cpu_hog.sh
```

3. Send the suspended job to the background:

```bash
bg %2
```

* Output:

```
[2]+ ./cpu_hog.sh &
```

4. List jobs:

```bash
jobs
```

* Example output:

```
[1]-  Running                 ./cpu_hog.sh &
[2]+  Running                 ./cpu_hog.sh &
```

5. Bring a background job back to foreground:

```bash
fg %1
```

6. Start a new CPU hog politely with low priority:

```bash
nice -n 10 ./cpu_hog.sh &
```

7. Renice an already running job (example PID 1234):

```bash
renice 5 -p 1234
```

* Output:

```
1234 (process ID) old priority 10, new priority 5
```

---

## **Scenario 2: The Silent Logger That Won’t Quit**

1. Run `slow_logger.sh` immune to logout:

```bash
nohup ./slow_logger.sh &
```

* Output:

```
[1] 5678
nohup: appending output to 'nohup.out'
```

2. Check processes using `ps`:

```bash
ps -ef | grep slow_logger.sh
ps aux | grep slow_logger.sh
ps -o pid,ppid,cmd | grep slow_logger.sh
```

* Example output:

```
5678   1234  0  0  ./slow_logger.sh
```

3. Kill it using a signal:

```bash
kill -s SIGTERM 5678
```

* Confirm stopped with `ps aux | grep slow_logger.sh`.

4. Kill by pattern using `pkill`:

```bash
pkill slow_logger.sh
```

---

## **Scenario 3: Net Hog vs. Sysadmin Patience**

1. Launch `net_hog.sh` with low priority:

```bash
nice -n 15 ./net_hog.sh &
```

2. Monitor in `top` or `htop`:

```bash
top
htop
```

* Look for high CPU usage from `net_hog.sh`.

3. Renice it to higher priority (example PID 6789):

```bash
renice -5 -p 6789
```

4. Finally kill it with SIGKILL:

```bash
kill -s SIGKILL 6789
```

---

## **Scenario 4: Disk Drama in `/tmp`**

1. Start I/O hog script in the background:

```bash
./io_hog.sh &
```

2. Find heavy I/O processes:

```bash
iotop
```

3. Confirm disk stats:

```bash
iostat -c
iostat -z
iostat -p sda   # example device
```

4. Kill by pattern:

```bash
pkill io_hog.sh
```

---

## **Scenario 5: The Big Picture Panic**

1. Quickly check system stats:

```bash
dstat
vmstat
sar 1 5   # collect and show system activity every 1 sec, 5 times
```

2. Compare with live process monitoring:

```bash
top
htop
```

3. Adjust or terminate resource-hogging processes as needed using:

```bash
kill -s SIGTERM <pid>
renice 10 -p <pid>
fg %<job_number>
bg %<job_number>
```

---
