### **Sample Files for All Scenarios**

Create these before starting the scenarios:

```bash
# Create sample users and groups
sudo useradd alice
sudo useradd bob
sudo groupadd staff
sudo usermod -aG staff alice
sudo usermod -aG staff bob

# Create a test filesystem mount point
sudo mkdir -p /mnt/testquota

# Create sample files
sudo dd if=/dev/zero of=/mnt/testquota/file1 bs=1M count=10
sudo dd if=/dev/zero of=/mnt/testquota/file2 bs=1M count=20
sudo chown alice:staff /mnt/testquota/file1
sudo chown bob:staff /mnt/testquota/file2

# Ensure the filesystem supports quotas (assume ext4 for examples)
sudo mount -o remount,usrquota,grpquota /mnt/testquota
```

---

### **Scenario 1: Initial Quota Setup**

**Story:** The system admin wants to start enforcing quotas on `/mnt/testquota` for both users and groups.

**Tasks:**

1. Create quota database files for both users and groups.
2. Enable quotas on `/mnt/testquota` for both users and groups, showing verbose output.
3. Verify that quota enforcement is enabled.

---

### **Scenario 2: Checking and Displaying Quotas**

**Story:** Alice and Bob want to check their disk usage and quota limits in human-readable form.

**Tasks:**

1. Display user quotas for Alice and Bob.
2. Display group quotas for the `staff` group.
3. Include users/groups even if they are under quota limits.

---

### **Scenario 3: Generating Quota Reports**

**Story:** The admin needs a detailed report of disk usage for auditing purposes.

**Tasks:**

1. Generate a summary report for all mounted filesystems with quotas.
2. Report separately for user quotas and group quotas.
3. Include human-readable sizes and users/groups with no usage.

---

### **Scenario 4: Disabling Quotas for Maintenance**

**Story:** The admin must temporarily disable quotas for maintenance.

**Tasks:**

1. Disable user quotas on `/mnt/testquota` with verbose output.
2. Disable group quotas on all filesystems in `/etc/fstab`.
3. Verify that quotas are turned off.

---

### **Scenario 5: Combined Operations**

**Story:** After cleanup, the admin wants to reset and check quotas.

**Tasks:**

1. Turn off all quotas.
2. Recreate quota files for both users and groups.
3. Enable quotas for both users and groups on `/mnt/testquota`.
4. Display a human-readable summary report for users.

---
---
---

## **Scenario 1: Initial Quota Setup**

**Tasks & Answers:**

1. **Create quota database files for users and groups:**

```bash
sudo quotacheck -c -u /mnt/testquota
sudo quotacheck -c -g /mnt/testquota
```

2. **Enable quotas on `/mnt/testquota` for both users and groups (verbose):**

```bash
sudo quotaon -u -v /mnt/testquota
sudo quotaon -g -v /mnt/testquota
```

3. **Verify quota enforcement is enabled:**

```bash
quotaon -v /mnt/testquota
```

---

## **Scenario 2: Checking and Displaying Quotas**

**Tasks & Answers:**

1. **Display user quotas for Alice and Bob (human-readable):**

```bash
quota -u -s alice
quota -u -s bob
```

2. **Display group quotas for `staff`:**

```bash
quota -g staff
```

3. **Include users/groups even if under limits (verbose):**

```bash
quota -u -v -s alice
quota -g -v staff
```

*Optional: Combine `-f` to specify filesystem explicitly:*

```bash
quota -u -s -f /mnt/testquota alice
quota -g -s -f /mnt/testquota staff
```

---

## **Scenario 3: Generating Quota Reports**

**Tasks & Answers:**

1. **Summary report for all mounted filesystems with quotas:**

```bash
repquota -a
```

2. **Report separately for users and groups:**

```bash
repquota -a -u
repquota -a -g
```

3. **Include human-readable sizes and users/groups with no usage:**

```bash
repquota -a -u -v -s
repquota -a -g -v -s
```

---

## **Scenario 4: Disabling Quotas for Maintenance**

**Tasks & Answers:**

1. **Disable user quotas on `/mnt/testquota` with verbose:**

```bash
sudo quotaoff -u -v /mnt/testquota
```

2. **Disable group quotas on all filesystems in `/etc/fstab`:**

```bash
sudo quotaoff -g -a
```

3. **Verify quotas are turned off:**

```bash
quotaon -v /mnt/testquota
```

*Expected output shows quotas disabled.*

---

## **Scenario 5: Combined Operations**

**Tasks & Answers:**

1. **Turn off all quotas:**

```bash
sudo quotaoff -a
```

2. **Recreate quota files for both users and groups:**

```bash
sudo quotacheck -c -u /mnt/testquota
sudo quotacheck -c -g /mnt/testquota
```

3. **Enable quotas for both users and groups on `/mnt/testquota`:**

```bash
sudo quotaon -u -v /mnt/testquota
sudo quotaon -g -v /mnt/testquota
```

4. **Display a human-readable summary report for users:**

```bash
repquota -a -u -s
```

---
