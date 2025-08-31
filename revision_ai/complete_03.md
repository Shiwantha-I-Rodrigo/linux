# 🔹 Scenarios

## Scenario 1: One-time late-night cleanup

You are a sysadmin, and you need to schedule a one-time job to remove all temporary files in `/tmp` at **2:00 AM tonight**.
You also want to receive an email notification once the job completes, regardless of whether it generates output.

---

## Scenario 2: Scheduling commands from a file

You created a file `joblist.txt` that contains the following commands:

```bash
echo "Daily backup started at $(date)" >> /var/log/backup.log
tar -czf /home/user/backup-$(date +\%F).tar.gz /home/user/documents
echo "Backup completed at $(date)" >> /var/log/backup.log
```

You want this set of commands to run **tomorrow at 6:30 AM** automatically.

---

## Scenario 3: Checking scheduled jobs

After scheduling several jobs for testing, you want to **see all the pending jobs** in the queue for your user account.

---

## Scenario 4: Removing a mistaken job

You accidentally scheduled a cleanup job for 11:00 AM today that would delete files in `/home/user`.
Before it runs, you want to **cancel that job**.

---

## Scenario 5: Weekly log rotation with cron

Your team requires that system logs in `/var/log/syslog` be archived every Sunday at midnight.
You decide to schedule a **recurring job** for this using cron.

---

## Scenario 6: Reviewing your recurring jobs

Later, you want to **check all the recurring cron jobs** you’ve added for your account.

---

## Scenario 7: Cleaning up all cron jobs

At the end of a project, you want to **completely remove all of your cron jobs** so that none of them keep running.

---

# 🔹 Answer Keys

## Scenario 1: One-time late-night cleanup

```bash
echo "rm -rf /tmp/*" | at -m 2:00 AM
```

* `-m` ensures you get an email even if no output is produced.

---

## Scenario 2: Scheduling commands from a file

```bash
at -f joblist.txt 6:30 AM tomorrow
```

* `-f joblist.txt` reads commands from a file.

---

## Scenario 3: Checking scheduled jobs

```bash
at -l
```

(or simply `atq`)

---

## Scenario 4: Removing a mistaken job

```bash
atq   # first check the job IDs
atrm <job_id>
```

(or equivalently)

```bash
at -d <job_id>
```

---

## Scenario 5: Weekly log rotation with cron

```bash
crontab -e
```

Add the following line in the editor:

```
0 0 * * 0 mv /var/log/syslog /var/log/syslog-$(date +\%F)
```

---

## Scenario 6: Reviewing your recurring jobs

```bash
crontab -l
```

---

## Scenario 7: Cleaning up all cron jobs

```bash
crontab -r
```

---