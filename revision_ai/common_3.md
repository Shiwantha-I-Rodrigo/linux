## **Scenario 1: Scheduling a One-Time Maintenance Job**

**Context:** You are a sysadmin at a company, and you need to schedule a script to clean temporary log files tonight at 11 PM. You want to ensure you can check it later if needed.

**Step-by-Step Workflow:**

1. **Create the script**

   ```bash
   nano /home/admin/cleanup_logs.sh
   ```

   Add:

   ```bash
   #!/bin/bash
   find /var/log -name "*.log" -type f -mtime +7 -exec rm -f {} \;
   echo "Log cleanup completed on $(date)" >> /home/admin/cleanup.log
   ```

   Make it executable:

   ```bash
   chmod +x /home/admin/cleanup_logs.sh
   ```

2. **Schedule the job with `at` using a file**

   ```bash
   at -f /home/admin/cleanup_logs.sh 23:00
   ```

   > **Explanation:** `-f` tells `at` to read the commands from a file. This avoids manually typing them in the interactive `at` prompt.

3. **Verify scheduled jobs using `atq`**

   ```bash
   atq
   ```

   Output example:

   ```
   2   2025-08-30 23:00 a admin
   ```

   > **Explanation:** Lists all pending `at` jobs for your user. Here, job ID `2` is our scheduled cleanup.

4. **Optional: Remove the job if needed**

   ```bash
   atrm 2
   ```

   > **Explanation:** Deletes the scheduled job using its ID.

---

## **Scenario 2: Recurring Daily Backup with Cron**

**Context:** You want to run a database backup every day at 2 AM.

**Step-by-Step Workflow:**

1. **Edit your crontab**

   ```bash
   crontab -e
   ```

   > **Explanation:** Opens the current user’s crontab in the default editor.

2. **Add a cron job**

   ```
   0 2 * * * /home/admin/backup_db.sh
   ```

   > **Explanation:** This runs `backup_db.sh` at 2:00 AM daily. The `* * * * *` represents minute, hour, day of month, month, day of week.

3. **List crontab entries**

   ```bash
   crontab -l
   ```

   Output example:

   ```
   0 2 * * * /home/admin/backup_db.sh
   ```

   > **Explanation:** Confirms the cron job is scheduled correctly.

4. **Remove crontab if you no longer need it**

   ```bash
   crontab -r
   ```

   > **Explanation:** Deletes all current user’s cron jobs.

---

## **Scenario 3: Troubleshooting a Failed Scheduled Task**

**Context:** Users report that a cleanup job didn’t run last night.

**Step-by-Step Troubleshooting:**

1. **Check pending `at` jobs**

   ```bash
   atq
   ```

   > **Explanation:** If the job still exists, it may not have executed due to system issues.

2. **Check `at` logs** (Ubuntu 20.04 uses `syslog`)

   ```bash
   grep CRON /var/log/syslog | grep cleanup_logs.sh
   ```

   > **Explanation:** Finds whether the job ran and if there were any errors.

3. **Check file permissions**

   ```bash
   ls -l /home/admin/cleanup_logs.sh
   ```

   > **Explanation:** Ensure the script is executable. Lack of `chmod +x` can prevent execution.

4. **Reschedule the job if necessary**

   ```bash
   at -f /home/admin/cleanup_logs.sh 23:00
   ```

---

## **Summary of Most-Used Options**

| Command   | Common Options Used | Purpose                                    |
| --------- | ------------------- | ------------------------------------------ |
| `at`      | `-f <file>`         | Schedule a one-time job from a script file |
| `atq`     | —                   | List pending `at` jobs                     |
| `atrm`    | —                   | Remove a scheduled `at` job by job ID      |
| `crontab` | `-e`, `-l`, `-r`    | Edit, list, or remove recurring cron jobs  |

---

## **Scenario 4: Scheduling Emergency One-Time Tasks**

**Context:** A critical security patch needs to be applied at midnight on multiple servers without disrupting users.

**Step-by-Step Workflow:**

1. **Create the patch script**

   ```bash
   nano /home/admin/patch_update.sh
   ```

   Add:

   ```bash
   #!/bin/bash
   yum -y update kernel*       # For RHEL
   apt update && apt -y upgrade linux-image*  # For Ubuntu
   echo "Kernel update applied on $(hostname) at $(date)" >> /home/admin/patch.log
   ```

   Make it executable:

   ```bash
   chmod +x /home/admin/patch_update.sh
   ```

2. **Schedule the job with `at`**

   ```bash
   at -f /home/admin/patch_update.sh 00:00
   ```

   > Using `-f` ensures the complex commands run automatically.

3. **Verify with `atq`**

   ```bash
   atq
   ```

   Example output:

   ```
   5   2025-08-31 00:00 a admin
   ```

4. **Send confirmation email after completion** (Optional)

   ```bash
   at -m -f /home/admin/patch_update.sh 00:00
   ```

   > `-m` sends mail to the user even if no output is produced. Very useful for unattended tasks.

5. **Remove a mistakenly scheduled job**

   ```bash
   atrm 5
   ```

---

## **Scenario 5: Recurring System Health Check with Cron**

**Context:** You want to monitor disk usage and CPU load daily and get an email report if thresholds are exceeded.

**Step-by-Step Workflow:**

1. **Create the monitoring script**

   ```bash
   nano /home/admin/system_health.sh
   ```

   Add:

   ```bash
   #!/bin/bash
   THRESHOLD=80
   USAGE=$(df / | tail -1 | awk '{print $5}' | sed 's/%//')
   if [ "$USAGE" -gt "$THRESHOLD" ]; then
       echo "Disk usage is at ${USAGE}% on $(hostname)" | mail -s "Disk Alert" admin@example.com
   fi
   ```

   Make it executable:

   ```bash
   chmod +x /home/admin/system_health.sh
   ```

2. **Schedule daily cron job at 6 AM**

   ```bash
   crontab -e
   ```

   Add:

   ```
   0 6 * * * /home/admin/system_health.sh
   ```

   > This will run the script daily at 6 AM.

3. **List cron jobs to verify**

   ```bash
   crontab -l
   ```

   Example output:

   ```
   0 6 * * * /home/admin/system_health.sh
   ```

4. **Remove cron job if no longer needed**

   ```bash
   crontab -r
   ```

---

## **Scenario 6: Troubleshooting Scheduled Jobs Failing Silently**

**Context:** A backup job scheduled with `at` didn’t execute, but no error was reported.

**Step-by-Step Troubleshooting:**

1. **Check pending `at` jobs**

   ```bash
   atq
   ```

   * If job still exists, it didn’t run.
   * If not, maybe it ran but failed silently.

2. **Check `/var/spool/at`**

   ```bash
   ls -l /var/spool/at
   ```

   > This shows job files. Permissions issues may prevent execution.

3. **Check logs**

   ```bash
   grep at /var/log/syslog
   ```

   > Look for errors related to job execution.

4. **Verify environment variables**

   ```bash
   env
   ```

   > `at` jobs run in a minimal environment. If your script relies on PATH or other variables, explicitly define them in the script.

5. **Reschedule job**

   ```bash
   at -f /home/admin/fix_backup.sh now + 1 minute
   ```

---

### **Summary of Options Used**

| Command   | Options Used      | Use Case                                   |
| --------- | ----------------- | ------------------------------------------ |
| `at`      | `-f <file>`, `-m` | Run one-time scripts; send completion mail |
| `atq`     | —                 | Check pending `at` jobs                    |
| `atrm`    | —                 | Cancel scheduled jobs                      |
| `crontab` | `-e`, `-l`, `-r`  | Edit, list, or remove recurring cron jobs  |

---
