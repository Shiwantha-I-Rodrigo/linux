## **Real-life Workflow & Troubleshooting Stories**

### **Scenario 1: Daily User Quota Check**

**Situation:** You want to check how much disk space users are using and whether they are close to exceeding their quotas.

**Commands used:** `quota -u -s -v`

**Steps:**

1. **Check a specific user’s quota**

   ```bash
   quota -u -s -v alice
   ```

   * `-u`: Focus on user quotas.
   * `-s`: Show human-readable sizes like MB/GB.
   * `-v`: Display all quotas, even if under limits.
     **Explanation:** You want a clear, readable summary of Alice’s usage and limits. Verbose ensures you see quotas even if she hasn’t reached them.

2. **Interpret the output**

   ```
   Disk quotas for alice (uid 1001):
     Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
     /home       10240    20480   22528           80     0      0
   ```

   * `blocks` = current disk usage in KB/MB.
   * `quota` = soft limit.
   * `limit` = hard limit.
   * `grace` = remaining time before hitting hard limit.

**Use case:** Daily monitoring for users approaching quota limits.

---

### **Scenario 2: Enable Quotas on All Filesystems**

**Situation:** Quotas were not active on a server; you need to enable them system-wide.

**Commands used:** `quotaon -a -u -v`

**Steps:**

1. **Enable user quotas**

   ```bash
   sudo quotaon -a -u -v
   ```

   * `-a`: Apply to all filesystems listed in `/etc/fstab`.
   * `-u`: Enable user quotas only (more common than group quotas).
   * `-v`: Verbose output to verify activation.

2. **Check status**

   ```
   Quotas on /home enabled
   Quotas on /var enabled
   ```

   **Explanation:** The verbose output confirms quotas are active on all relevant filesystems.

---

### **Scenario 3: Troubleshooting a Quota Database**

**Situation:** Users report “Disk quota exceeded” errors, but usage seems low. You suspect the quota database is outdated.

**Commands used:** `quotacheck -c -u -g -a`

**Steps:**

1. **Scan and recreate quota files**

   ```bash
   sudo quotacheck -cug -a
   ```

   * `-c`: Create missing quota files.
   * `-u`: Check user quotas.
   * `-g`: Check group quotas.
   * `-a`: Check all filesystems.

2. **Interpret the results**

   ```
   /dev/sda1: user quota checked
   /dev/sda1: group quota checked
   /dev/sda2: user quota checked
   ```

   **Explanation:** The quota files are rebuilt, and the system recalculates usage. This often resolves false “quota exceeded” errors.

---

### **Scenario 4: Generating a System-wide Quota Report**

**Situation:** Management asks for a report of disk usage across users.

**Commands used:** `repquota -a -u -s`

**Steps:**

1. **Generate the report**

   ```bash
   sudo repquota -a -u -s
   ```

   * `-a`: All mounted filesystems.
   * `-u`: Report user quotas.
   * `-s`: Show human-readable sizes.

2. **Sample output snippet**

   ```
   *** Report for user quotas on device /dev/sda1
   User       used   soft   hard
   alice      10G    20G    22G
   bob        5G     15G    18G
   ```

   **Explanation:** Easy to read report for management or auditing purposes.

---

### **Scenario 5: Disabling Quotas Temporarily**

**Situation:** A maintenance script requires temporary removal of quota enforcement.

**Commands used:** `quotaoff -a -u -v`

**Steps:**

1. **Disable quotas on all filesystems**

   ```bash
   sudo quotaoff -a -u -v
   ```

   * `-a`: Disable all quotas listed in `/etc/fstab`.
   * `-u`: Focus on user quotas.
   * `-v`: Confirm which filesystems are affected.

2. **Re-enable after maintenance**

   ```bash
   sudo quotaon -a -u -v
   ```

   **Explanation:** Temporarily suspending quotas prevents script failures without permanently removing quota enforcement.

---

## **Scenario 6: Checking Group Quotas**

**Situation:** A shared project directory has a group quota, and some users report permission or space issues.

**Command:** `quota -g -s -v`

**Steps:**

1. **Check group usage**

   ```bash
   quota -g -s -v devteam
   ```

   * `-g`: Check group quotas.
   * `-s`: Human-readable.
   * `-v`: Show all quotas, even under limits.

2. **Output interpretation**

   ```
   Disk quotas for group devteam (gid 1002):
     Filesystem  blocks   quota   limit   grace   files
     /project    15000    20000   22000          120
   ```

   **Explanation:** You can quickly see that the devteam group is approaching its soft limit and may need space reallocation.

---

## **Scenario 7: Auditing All Users Across Filesystems**

**Situation:** Monthly quota audit to identify heavy users.

**Command:** `repquota -a -u -v -s`

**Steps:**

1. **Generate full report**

   ```bash
   sudo repquota -a -u -v -s
   ```

   * `-a`: All mounted filesystems.
   * `-u`: Users only.
   * `-v`: Include all users, even with zero usage.
   * `-s`: Human-readable sizes.

2. **Sample snippet**

   ```
   User       used   soft   hard
   alice      10G    20G    22G
   bob        5G     15G    18G
   charlie    0      20G    22G
   ```

   **Explanation:** This is perfect for auditing. Users with zero usage are still listed for completeness.

---

## **Scenario 8: Fixing a Corrupted Quota Database**

**Situation:** Users complain they can’t write files even though their usage is low. Suspect quota files are corrupted.

**Command:** `quotacheck -c -u -g /filesystem`

**Steps:**

1. **Manually check and rebuild quota files**

   ```bash
   sudo quotacheck -cug /home
   ```

   * `-c`: Create missing quota files.
   * `-u`/`-g`: Check both user and group quotas.
   * `/home`: Target filesystem.

2. **After rebuilding, re-enable quotas**

   ```bash
   sudo quotaon -u -g /home
   ```

   **Explanation:** Rebuilding ensures accurate quota tracking; necessary after filesystem corruption or migration.

---

## **Scenario 9: Temporarily Suspending Group Quotas During Migration**

**Situation:** Migrating `/project` directory to a new server; quotas interfere with bulk copy.

**Commands:** `quotaoff -g /project` → perform migration → `quotaon -g /project`

**Steps:**

1. **Disable group quotas**

   ```bash
   sudo quotaoff -g /project
   ```

2. **Perform migration**

   ```bash
   rsync -av /project /mnt/newserver/project
   ```

3. **Re-enable quotas**

   ```bash
   sudo quotaon -g /project
   ```

   **Explanation:** Temporarily disabling quotas prevents “quota exceeded” errors during large file operations.

---

## **Scenario 10: Combining Quotas for Reporting and Enforcement Check**

**Situation:** As part of daily routine, verify quotas are active and summarize usage.

**Commands:**

* Enable quotas: `sudo quotaon -a -u -g -v`
* Report quotas: `sudo repquota -a -u -g -s`

**Steps:**

1. **Enable quotas**

   ```bash
   sudo quotaon -a -u -g -v
   ```

   **Explanation:** Ensures enforcement is active before auditing.

2. **Generate combined report**

   ```bash
   sudo repquota -a -u -g -s
   ```

   **Explanation:** This gives a complete view of all users and groups with human-readable sizes.

---
