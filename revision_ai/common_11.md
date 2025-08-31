## **Scenario 1: Troubleshooting SELinux Blocking a Web Application**

**Context:** A user reports that a web application is failing to write to `/var/www/html/uploads`. The application works normally after disabling SELinux temporarily.

**Step 1: Check current SELinux mode**

```bash
getenforce
```

* Returns: `Enforcing`
* **Explanation:** SELinux is actively blocking actions, which explains why the web app cannot write files.

---

**Step 2: Check full SELinux status**

```bash
sestatus
```

* Provides detailed SELinux state, policy, and booleans.
* Confirms SELinux is enforcing and the targeted policy is active.

---

**Step 3: Review SELinux denials**

```bash
sealert -a /var/log/audit/audit.log
```

* Generates a human-readable report of SELinux AVC denials.
* **Explanation:** The report shows that `httpd_t` (web server) cannot write to `/var/www/html/uploads`.

---

**Step 4: Check relevant SELinux booleans**

```bash
getsebool -a | grep httpd
```

* Finds `httpd_unified → off`
* **Explanation:** Certain booleans control what web processes are allowed to do.

---

**Step 5: Enable a boolean to allow uploads**

```bash
setsebool -P httpd_unified on
```

* `-P` makes the change persistent.
* **Explanation:** This allows the web server to write to directories labeled for web content without disabling SELinux.

---

**Step 6: Verify boolean change**

```bash
getsebool httpd_unified
```

* Confirms it is now `on`.

---

**Step 7: Ensure correct file context**

```bash
restorecon -R /var/www/html/uploads
```

* Recursively restores SELinux context to default for web files.
* **Explanation:** Fixes any incorrect file labels causing denials.

---

**Step 8: Test application**

* Upload now works successfully.
* **Explanation:** SELinux is still enforcing but correctly allows the web server to write.

---

## **Scenario 2: Managing ACLs for Collaborative Project Directory**

**Context:** You need to give the marketing team read/write access to `/srv/project` without changing traditional Unix permissions.

---

**Step 1: Add ACL for a user**

```bash
setfacl -m u:marketing:rwx /srv/project
```

* `-m` modifies ACL to add the user `marketing` with full access.

---

**Step 2: Add ACL for a group**

```bash
setfacl -m g:marketing_team:rx /srv/project
```

* Provides read/execute permissions for the group `marketing_team`.

---

**Step 3: Verify ACLs**

```bash
getfacl /srv/project
```

* Shows both user and group ACL entries.
* **Explanation:** Confirms the correct permissions are applied.

---

**Step 4: Remove temporary ACL**

```bash
setfacl -x u:tempuser /srv/project
```

* `-x` removes a specific ACL entry for a user no longer needing access.

---

**Step 5: Clean up all ACLs if needed**

```bash
setfacl -b /srv/project
```

* `-b` removes all ACLs from the directory.

---

## **Scenario 3: Temporarily Switching SELinux Mode for Emergency Fix**

**Context:** A critical script is failing due to SELinux restrictions. You need a temporary workaround.

```bash
setenforce 0
```

* Switches SELinux to **permissive mode** temporarily.
* Run the script to confirm it works.
* Later, restore enforcing mode:

```bash
setenforce 1
```

* SELinux resumes active enforcement.

---

## **Scenario 4: Fixing File Access After Restoring from Backup**

**Context:** A critical directory `/etc/app/config` was restored from backup, but the application cannot read the configuration files.

**Step 1: Check SELinux mode**

```bash
getenforce
```

* Confirms mode is `Enforcing`. SELinux might block access due to incorrect file labels.

---

**Step 2: Verify SELinux status and policy**

```bash
sestatus
```

* Shows SELinux is enforcing targeted policy.

---

**Step 3: Restore default security contexts**

```bash
restorecon -R /etc/app/config
```

* `-R` recursively restores the proper labels for files and directories.
* **Explanation:** Files restored from backup may have lost their original SELinux contexts.

---

**Step 4: Test application**

* Application can now read the configuration files successfully.

---

## **Scenario 5: Allowing Custom Port for Web Service**

**Context:** You deployed a web service on a non-standard port `8088`. SELinux blocks connections.

**Step 1: Check SELinux port context**

```bash
semanage port -l | grep http_port_t
```

* Lists ports currently allowed for `httpd_t` (web server type).
* Confirms `8088` is missing.

---

**Step 2: Add custom port**

```bash
semanage port -a -t http_port_t -p tcp 8088
```

* Adds port `8088` to the allowed web server ports.
* **Explanation:** Without this, SELinux will generate AVC denials when the web server tries to bind.

---

**Step 3: Verify port addition**

```bash
semanage port -l | grep 8088
```

* Confirms the port is now recognized by SELinux policy.

---

**Step 4: Test service**

* Web service successfully binds to `8088` and can be accessed remotely.

---

## **Scenario 6: Debugging a Permission Issue in a Shared Project Folder**

**Context:** Users complain they cannot write files in `/srv/projects/collab` despite Unix permissions allowing it.

**Step 1: List ACLs**

```bash
getfacl /srv/projects/collab
```

* Reveals ACL entries overriding traditional Unix permissions.

---

**Step 2: Grant write access to a user**

```bash
setfacl -m u:alice:rwx /srv/projects/collab
```

* Adds user `alice` with full access.

---

**Step 3: Add a group ACL**

```bash
setfacl -m g:dev_team:rx /srv/projects/collab
```

* Grants group `dev_team` read and execute access for collaboration.

---

**Step 4: Remove obsolete ACL for a former user**

```bash
setfacl -x u:john /srv/projects/collab
```

* Cleans up outdated permissions.

---

**Step 5: Verify final ACLs**

```bash
getfacl /srv/projects/collab
```

* Confirms ACLs now match intended access policy.

---

## **Scenario 7: Emergency Script Execution Under SELinux Enforcement**

**Context:** A backup script is failing under SELinux enforcement due to writing temporary files in `/tmp`.

**Step 1: Switch to permissive mode temporarily**

```bash
setenforce 0
```

* SELinux will now only log denials, not block actions.

---

**Step 2: Run backup script**

* Script executes successfully.

---

**Step 3: Check SELinux logs for denials**

```bash
sealert -a /var/log/audit/audit.log
```

* Finds that `backup_t` type attempted forbidden writes in `/tmp`.

---

**Step 4: Create permanent solution**

* Either adjust file contexts with `restorecon` or enable relevant boolean with `setsebool -P`.

---

**Step 5: Restore enforcing mode**

```bash
setenforce 1
```

* SELinux enforcement resumes.

---

## **Scenario 8: Resolving SELinux Denials on a Database Directory**

**Context:** PostgreSQL fails to start after moving its data directory to `/data/pgsql`.

**Step 1: Check SELinux mode**

```bash
getenforce
```

* Returns `Enforcing` → SELinux may block database from accessing the new location.

---

**Step 2: Inspect SELinux denials**

```bash
sealert -a /var/log/audit/audit.log
```

* Report shows `postgres_t` denied access to `/data/pgsql`.

---

**Step 3: Restore default context for PostgreSQL**

```bash
restorecon -R /data/pgsql
```

* Recursively applies correct SELinux labels for PostgreSQL data directory.

---

**Step 4: Verify labels**

```bash
ls -lZ /data/pgsql
```

* Ensures the directory and files have `postgres_db_t` or appropriate context.

---

**Step 5: Start PostgreSQL**

* Database starts successfully without disabling SELinux.

---

## **Scenario 9: Allowing Samba Shares to Work with SELinux**

**Context:** Users report access issues on Samba shares `/srv/samba/public`.

**Step 1: Check SELinux mode**

```bash
getenforce
```

* Returns `Enforcing`.

---

**Step 2: Check Samba booleans**

```bash
getsebool -a | grep samba
```

* `samba_enable_home_dirs → off`, `samba_export_all_rw → off`

---

**Step 3: Enable necessary booleans**

```bash
setsebool -P samba_export_all_rw on
setsebool -P samba_enable_home_dirs on
```

* Ensures Samba can write to shared directories.

---

**Step 4: Restore SELinux context for share**

```bash
restorecon -R /srv/samba/public
```

* Applies proper labels (`samba_share_t`) for shared directories.

---

**Step 5: Test access**

* Users can now read/write to Samba shares.

---

## **Scenario 10: Debugging Conflicting ACLs in Multi-User Environment**

**Context:** Two users report different access issues on `/srv/secure_project`.

**Step 1: Check current ACLs**

```bash
getfacl /srv/secure_project
```

* Reveals conflicting entries: one user has `rwx`, another has `r--` via group ACL.

---

**Step 2: Modify ACL for specific users**

```bash
setfacl -m u:alice:rwx /srv/secure_project
setfacl -m u:bob:rx /srv/secure_project
```

* Grants proper access while respecting principle of least privilege.

---

**Step 3: Add default ACL for new files**

```bash
setfacl -d -m g:dev_team:rx /srv/secure_project
```

* Ensures newly created files inherit group permissions.

---

**Step 4: Remove obsolete entries**

```bash
setfacl -x u:tempuser /srv/secure_project
```

* Cleans up ACL clutter.

---

**Step 5: Verify final ACLs**

```bash
getfacl /srv/secure_project
```

* Confirms intended access for all users and groups.

---

## **Scenario 11: Temporary Permissive Mode to Identify Problem**

**Context:** A cron job fails intermittently due to SELinux.

**Step 1: Switch to permissive mode**

```bash
setenforce 0
```

* Temporarily allows cron job to run while logging denials.

---

**Step 2: Run the cron job manually**

* Confirms that it succeeds under permissive mode.

---

**Step 3: Analyze denials**

```bash
sealert -a /var/log/audit/audit.log
```

* Finds cron job writing to `/var/tmp` triggers `tmp_t` denial.

---

**Step 4: Apply permanent solution**

```bash
restorecon /var/tmp/cron_jobs
```

* Or configure a boolean if necessary:

```bash
setsebool -P cron_read_user_content on
```

---

**Step 5: Restore enforcing mode**

```bash
setenforce 1
```

* SELinux resumes enforcement, cron job works correctly.

---

### **Extended Summary of Options Used (Including New Scenarios)**

| Command      | Options Used                     | Coverage |
| ------------ | -------------------------------- | -------- |
| `getenforce` | none                             | 100%     |
| `setenforce` | `0`, `1`                         | 100%     |
| `sestatus`   | none                             | 100%     |
| `semanage`   | `port -a -t -p`, `port -l`       | 50%      |
| `setsebool`  | `-P`                             | 100%     |
| `getsebool`  | `-a`, specific booleans          | 50%      |
| `restorecon` | `-R`                             | 100%     |
| `setfacl`    | `-m`, `-x`, `-b`, `u`, `g`, `-d` | 100%     |
| `getfacl`    | none                             | 100%     |
| `sealert`    | `-a`                             | 100%     |

---
