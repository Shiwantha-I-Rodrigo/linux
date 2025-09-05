### Sample Files & Setup (assume these exist in your home directory)

```
~/reports/daily_report.sh
~/scripts/backup.sh
~/scripts/cleanup.sh
~/scripts/joke.sh
~/tasks/batch1.txt
~/tasks/batch2.txt
```

Contents (simplified examples):

**daily\_report.sh**

```bash
#!/bin/bash
echo "Daily report generated at $(date)" >> ~/reports/report.log
```

**backup.sh**

```bash
#!/bin/bash
echo "Backup done at $(date)" >> ~/backup/backup.log
```

**cleanup.sh**

```bash
#!/bin/bash
echo "Cleaned temp files at $(date)" >> ~/cleanup/cleanup.log
```

**joke.sh**

```bash
#!/bin/bash
echo "Why do programmers hate nature? Too many bugs!" >> ~/jokes/jokes.log
```

**batch1.txt**

```bash
/home/you/scripts/backup.sh
/home/you/scripts/cleanup.sh
```

**batch2.txt**

```bash
/home/you/scripts/joke.sh
/home/you/reports/daily_report.sh
```

---

## Story Scenarios

### Scenario 1: The Forgetful Manager

Your manager suddenly asks you to run a **cleanup** script exactly 10 minutes from now, but you know you’ll forget.

* Schedule this with the correct tool, ensuring that you’ll also get a **mail notification even if there’s no output**.
* Then, imagine the manager changes their mind — cancel that job before it runs.

---

### Scenario 2: The Secret Batch

You’ve been given a file (`batch1.txt`) containing two scripts that must run at 2 AM.

* Schedule this using the correct option so the commands are read directly from the file.
* After scheduling, **list all pending jobs** to confirm.

---

### Scenario 3: The Joke That Must Wait

You want to prank your colleague by running the `joke.sh` script at 5 PM today.

* Schedule this using the basic `at` syntax (without `-f`).
* Then, demonstrate how to **list the job** with one command and **delete it** with another before it executes, because you suddenly realize HR might not find it funny.

---

### Scenario 4: The Eternal Report

The daily report script should run automatically at 6 AM every day.

* Create the appropriate recurring schedule for it.
* List the scheduled recurring jobs to confirm.

---

### Scenario 5: The Drastic Cleanup

After weeks of testing, your user’s crontab has grown messy. You’re asked to completely wipe all recurring jobs.

* Perform the action that removes all crontab entries in one step.

---

### Scenario 6: The Switcheroo

Your colleague leaves in a hurry, and you find two batch files: `batch1.txt` and `batch2.txt`.

* Schedule `batch2.txt` to run tomorrow at 9 AM.
* Then, list jobs, remove the wrong one, and ensure the correct one stays in the queue.

---
---
---

### Scenario 1: The Forgetful Manager

**Schedule cleanup script for 10 minutes from now with mail notification:**

```bash
at -m now + 10 minutes
at> ~/scripts/cleanup.sh
at> <CTRL+D>
```

**List pending jobs:**

```bash
atq
```

**Cancel the job (example: job ID 3):**

```bash
atrm 3
```

---

### Scenario 2: The Secret Batch

**Schedule batch file for 2 AM:**

```bash
at -f ~/tasks/batch1.txt 2am
```

**List pending jobs:**

```bash
atq
```

---

### Scenario 3: The Joke That Must Wait

**Schedule prank for 5 PM today:**

```bash
at 5pm
at> ~/scripts/joke.sh
at> <CTRL+D>
```

**List job:**

```bash
atq
```

**Delete job (example: job ID 6):**

```bash
atrm 6
```

---

### Scenario 4: The Eternal Report

**Edit crontab and add recurring job:**

```bash
crontab -e
```

Add this line inside editor:

```
0 6 * * * /home/you/reports/daily_report.sh
```

**List crontab entries:**

```bash
crontab -l
```

---

### Scenario 5: The Drastic Cleanup

**Remove all cron jobs:**

```bash
crontab -r
```

---

### Scenario 6: The Switcheroo

**Schedule `batch2.txt` for tomorrow at 9 AM:**

```bash
at -f ~/tasks/batch2.txt 9am tomorrow
```

**List jobs:**

```bash
atq
```

**Remove wrong one (example: job ID 12):**

```bash
atrm 12
```

---
