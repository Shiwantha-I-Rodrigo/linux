# 🔹 Scenarios

## **Scenario 1: User quota issue in Research Department**

Dr. Alice in the **research department** complains that her home directory seems to be full even though she deleted some old project files. The sysadmin needs to:

1. **Verify her current quota usage** (including even if she’s under limit).
2. **Check usage in human-readable format** and confirm if her quota is applied on the `/home` filesystem.
3. **Scan and rebuild the quota database** for `/home` since it may be outdated, ensuring both user and group quota files exist.
4. **Enable user quota enforcement** on `/home`.
5. **Generate a report of user quota usage** for all users in human-readable format.

```txt
/home/alice/research_notes.txt
This file contains notes on protein structure research.
```

---

## **Scenario 2: Group quota for “Design Team”**

The design team shares a directory under `/projects/design`. They recently hit their group quota limit and couldn’t upload large image files. The sysadmin needs to:

1. **Check the group quota usage** for the design group.
2. **Create fresh quota database files** for `/projects/design`.
3. **Enable group quota enforcement** on `/projects/design` with verbose output to confirm.
4. **Generate a report of group quotas** across all mounted filesystems with quotas, also including groups that currently have no disk usage.
5. Later, the manager requests temporary suspension of **only group quotas** on `/projects/design`.

```txt
/projects/design/logo_sketches.txt
Contains ASCII art sketches of logo drafts.
```

---

## **Scenario 3: Full System Audit**

At the end of the month, the IT department performs a **quota audit** across the entire server:

1. They must **check quotas for both users and groups** on all filesystems listed in `/etc/fstab`.
2. **Enable all quotas** across the system for both users and groups.
3. **Generate a verbose quota usage report** (including users/groups with no usage) in human-readable format.
4. Finally, as part of testing, they **temporarily disable all quotas** across the system with verbose confirmation.

```txt
/tmp/audit_test.txt
This is a dummy file for quota audit testing.
```

---

## **Scenario 4: Temporary suspension of user quotas**

Bob, a system admin, is performing migration of users’ data from `/home` to another storage. He needs to:

1. **Temporarily disable only user quotas** on `/home` while migration runs.
2. After migration, he will re-enable user quotas.

```txt
/home/bob/migration_test.txt
This file is used to simulate data migration for quota suspension.
```

---

# 🔹 Answer Key

## **Scenario 1: User quota issue in Research Department**

```bash
# 1. Show Alice’s quota, verbose
quota -u -v alice

# 2. Human-readable quota with filesystem check
quota -s -f /home alice

# 3. Rebuild quota database for /home (user + group)
quotacheck -c -u -g /home

# 4. Enable user quota on /home
quotaon -u /home

# 5. Generate report of user quotas in human-readable format
repquota -u -s /home
```

---

## **Scenario 2: Group quota for “Design Team”**

```bash
# 1. Check group quota usage
quota -g design

# 2. Create quota database files for /projects/design
quotacheck -c -g /projects/design

# 3. Enable group quotas on /projects/design (verbose)
quotaon -g -v /projects/design

# 4. Report group quotas for all filesystems, include groups with no usage
repquota -a -g -v

# 5. Disable group quotas on /projects/design
quotaoff -g /projects/design
```

---

## **Scenario 3: Full System Audit**

```bash
# 1. Check quotas for all filesystems (user + group)
quotacheck -u -g -a

# 2. Enable quotas everywhere for users + groups
quotaon -a -u -g

# 3. Verbose quota report in human-readable format
repquota -a -v -s

# 4. Disable quotas everywhere (verbose)
quotaoff -a -v
```

---

## **Scenario 4**

```bash
# 1. Disable only user quotas on /home
quotaoff -u /home

# 2. Re-enable user quotas after migration
quotaon -u /home
```

---