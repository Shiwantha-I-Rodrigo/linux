### **Sample Files (common to scenarios)**

* `/etc/apparmor.d/usr.bin.sampleapp` — AppArmor profile for a fictional app called `sampleapp`.
* `/tmp/testfile.txt` — A dummy file to test app confinement.
* `/var/log/kern.log` — Log file to check AppArmor violations.
* `/usr/bin/sampleapp` — Executable for testing AppArmor profile.

---

### **Scenario 1: Investigating Unconfined Processes**

**Task:**
You are the system administrator and want to see if any processes are running without AppArmor confinement. Then, report the current AppArmor status and list all loaded profiles.

**Steps to perform:**

1. Identify all unconfined processes.
2. Check overall AppArmor status, including loaded profiles.

---

### **Scenario 2: Testing an Application Profile**

**Task:**
A new application `sampleapp` has just been installed. Before fully enforcing its security profile, you want to test it in complain mode. Then, switch it back to enforce mode after verifying.

**Steps to perform:**

1. Put the `sampleapp` profile in complain mode.
2. Trigger some actions in `sampleapp` that would normally violate AppArmor rules.
3. Check the logs to confirm that violations are logged.
4. Switch the profile to enforce mode.
5. Confirm the profile is now in enforce mode.

---

### **Scenario 3: Temporarily Disabling a Profile**

**Task:**
Due to a temporary software issue, `sampleapp` is causing unexpected failures. Temporarily disable its AppArmor profile, then later re-enable it in enforce mode.

**Steps to perform:**

1. Disable the `sampleapp` profile.
2. Verify it is no longer active by checking AppArmor status.
3. Re-enable the profile in enforce mode.
4. Confirm enforcement by listing profiles and status.

---

### **Scenario 4: Combining Profile Modes and Status Checks**

**Task:**
You need to audit multiple profiles. Put `sampleapp` in complain mode, then put another profile (e.g., `/etc/apparmor.d/usr.bin.anotherapp`) in enforce mode. Finally, summarize which profiles are in which modes and any unconfined processes.

**Steps to perform:**

1. Set `sampleapp` to complain mode.
2. Set `anotherapp` profile to enforce mode.
3. List all loaded profiles and their current modes.
4. Identify any unconfined processes.

---

### **Scenario 5: End-to-End AppArmor Audit**

**Task:**
Your team wants a full audit of AppArmor on a server. They want to: check unconfined processes, put a test profile in complain mode, simulate violations, log them, then enforce the profile and re-check status.

**Steps to perform:**

1. List unconfined processes.
2. Put `sampleapp` in complain mode.
3. Perform actions to generate logged violations.
4. Check the log file for violation entries.
5. Switch the profile to enforce mode.
6. Verify all profiles and statuses.

---
---
---

### **Scenario 1: Investigating Unconfined Processes**

**Commands / Steps:**

```bash
# List all unconfined processes
aa-unconfined

# Check overall AppArmor status and loaded profiles
aa-status
```

---

### **Scenario 2: Testing an Application Profile**

**Commands / Steps:**

```bash
# Put sampleapp profile in complain mode
aa-complain /etc/apparmor.d/usr.bin.sampleapp

# Run sampleapp to generate potential violations
/usr/bin/sampleapp /tmp/testfile.txt

# Check logs for violation entries
tail /var/log/kern.log

# Switch sampleapp profile to enforce mode
aa-enforce /etc/apparmor.d/usr.bin.sampleapp

# Verify profile is in enforce mode
aa-status
```

---

### **Scenario 3: Temporarily Disabling a Profile**

**Commands / Steps:**

```bash
# Disable the sampleapp profile
aa-disable /etc/apparmor.d/usr.bin.sampleapp

# Verify profile is no longer active
aa-status

# Re-enable the profile in enforce mode
aa-enforce /etc/apparmor.d/usr.bin.sampleapp

# Confirm enforcement
aa-status
```

---

### **Scenario 4: Combining Profile Modes and Status Checks**

**Commands / Steps:**

```bash
# Put sampleapp in complain mode
aa-complain /etc/apparmor.d/usr.bin.sampleapp

# Put another profile in enforce mode
aa-enforce /etc/apparmor.d/usr.bin.anotherapp

# List all loaded profiles and their modes
aa-status

# Identify unconfined processes
aa-unconfined
```

---

### **Scenario 5: End-to-End AppArmor Audit**

**Commands / Steps:**

```bash
# List unconfined processes first
aa-unconfined

# Put sampleapp in complain mode
aa-complain /etc/apparmor.d/usr.bin.sampleapp

# Perform actions to trigger violations
/usr/bin/sampleapp /tmp/testfile.txt

# Check logs for violations
tail /var/log/kern.log

# Switch profile to enforce mode
aa-enforce /etc/apparmor.d/usr.bin.sampleapp

# Verify all profiles and statuses
aa-status
```

---
