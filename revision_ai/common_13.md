## **1️⃣ Managing Services with `systemctl`**

### Daily-use / troubleshooting:

Most common options: `status`, `restart`, `start`, `stop`, `enable`, `disable`, `is-active`

**Scenario 1: Web service failing on Ubuntu 20.04**

**Step-by-step workflow:**

1. **Check service status:**

```bash
sudo systemctl status apache2
```

* Confirms if the service is running.
* Outputs current status, logs, and recent failures.

2. **Restart service if inactive or failed:**

```bash
sudo systemctl restart apache2
```

* Forces a restart and clears temporary glitches.
* If fails, check logs with `journalctl -u apache2 -n 50`.

3. **Enable service at boot (if needed):**

```bash
sudo systemctl enable apache2
```

* Ensures the service starts automatically after a reboot.

4. **Verify running state:**

```bash
systemctl is-active apache2
```

* Returns `active` or `inactive`—quick verification for scripts.

**Scenario 2: Red Hat 8.5 – database service troubleshooting**

```bash
sudo systemctl status mariadb
sudo systemctl restart mariadb
sudo systemctl enable mariadb
```

* Typical workflow during a server maintenance window.

---

## **2️⃣ Analyzing Boot Performance with `systemd-analyze`**

### Most common options: `blame`, `time`, `critical-chain`

**Scenario: Slow boot on Ubuntu 20.04**

1. **Check overall boot time:**

```bash
systemd-analyze time
```

* Shows total boot duration (kernel + initrd + userspace).

2. **Identify slow services:**

```bash
systemd-analyze blame | head -n 10
```

* Lists top 10 slowest starting services.

3. **Check service dependencies:**

```bash
systemd-analyze critical-chain apache2.service
```

* Shows which services are blocking others.

---

## **3️⃣ Viewing Logs with `journalctl`**

### Most common daily options: `-n`, `-e`, `-r`, `-l`

### Common match conditions: `PRIORITY`, `OBJECT_PID`, `_UID`

**Scenario: Investigating service crash**

```bash
sudo journalctl -u apache2 -n 50 -e
```

* Shows last 50 lines, jumps to the end of logs.

```bash
sudo journalctl -u apache2 -p err -n 20
```

* Filter for errors (PRIORITY=3).

```bash
sudo journalctl -u apache2 -r -n 20
```

* Shows logs in reverse order for quick recent issues.

---

## **4️⃣ Using `service` (legacy init system)**

### Most common: `status`, `restart`, `start`, `stop`

**Scenario: Quick restart during script deployment on Ubuntu 20.04**

```bash
sudo service apache2 restart
sudo service apache2 status
```

* Simpler alternative to `systemctl` for scripts or older habits.

---

## **5️⃣ SysV Startup Management (`update-rc.d` and `chkconfig`)**

**Ubuntu (Debian) daily:**

```bash
sudo update-rc.d apache2 defaults
sudo update-rc.d apache2 remove
```

**RHEL daily:**

```bash
sudo chkconfig --list mariadb
sudo chkconfig mariadb on
sudo chkconfig mariadb off
```

---

## **6️⃣ Time Management (`timedatectl` & `hwclock`)**

**Daily usage:** `set-time`, `--systohc`, `--hctosys`

**Scenario: Synchronize hardware clock after timezone change**

```bash
sudo timedatectl set-time "2025-08-31 14:30:00"
sudo hwclock --systohc
```

* Ensures both system and hardware clocks are consistent.

---

## **Scenario 1: Web Service Fails After Update (Ubuntu 20.04)**

**Problem:** Apache (`apache2`) stops working after a package upgrade.

**Step-by-step troubleshooting:**

1. **Check service status:**

```bash
sudo systemctl status apache2
```

* Confirms service failed and shows the latest logs.

2. **Inspect logs for errors:**

```bash
sudo journalctl -u apache2 -p err -n 50
```

* Filters only error messages to identify misconfigurations.

3. **Check configuration validity:**

```bash
sudo apache2ctl configtest
```

* Ensures no syntax errors prevent service from starting.

4. **Restart service if config is valid:**

```bash
sudo systemctl restart apache2
```

5. **Enable auto-start on boot (if disabled):**

```bash
sudo systemctl enable apache2
```

6. **Verify running state:**

```bash
systemctl is-active apache2
```

✅ Combines: `status`, `restart`, `enable`, `is-active`, `journalctl -p err`

---

## **Scenario 2: Slow Boot After Kernel Update (Ubuntu & RHEL)**

**Problem:** System takes too long to boot after kernel upgrade.

**Step-by-step workflow:**

1. **Check total boot time:**

```bash
systemd-analyze time
```

2. **Identify slow services:**

```bash
systemd-analyze blame | head -n 10
```

3. **Analyze service dependencies:**

```bash
systemd-analyze critical-chain NetworkManager.service
```

* Finds which services delay others.

4. **Optional:** Disable unnecessary services:

```bash
sudo systemctl disable bluetooth.service
```

✅ Combines: `systemd-analyze time`, `blame`, `critical-chain`, `systemctl disable`

---

## **Scenario 3: High-Priority Errors in Logs (RHEL 8.5)**

**Problem:** `mariadb` crashes intermittently.

**Step-by-step workflow:**

1. **Check service status:**

```bash
sudo systemctl status mariadb
```

2. **Check last 50 error logs:**

```bash
sudo journalctl -u mariadb -p err -n 50 -e
```

3. **Investigate recent process errors:**

```bash
sudo journalctl _PID=$(pidof mariadb) -n 20 -r
```

4. **Restart service:**

```bash
sudo systemctl restart mariadb
```

5. **Verify active state:**

```bash
systemctl is-active mariadb
```

✅ Combines: `status`, `restart`, `is-active`, `journalctl -p err`, `_PID` match

---

## **Scenario 4: Hardware Clock Out of Sync (Ubuntu & RHEL)**

**Problem:** System clock drifts after reboot.

**Step-by-step workflow:**

1. **Check current system time:**

```bash
timedatectl
```

2. **Set correct system time:**

```bash
sudo timedatectl set-time "2025-08-31 14:30:00"
```

3. **Sync system time to hardware clock:**

```bash
sudo hwclock --systohc
```

4. **Verify hardware clock matches system time:**

```bash
hwclock --show
```

✅ Combines: `timedatectl set-time`, `hwclock --systohc`, `hwclock --show`

---

## **Scenario 5: Service Masked Accidentally (Ubuntu)**

**Problem:** `cron` job fails because service was masked.

**Step-by-step workflow:**

1. **Check if service is masked:**

```bash
sudo systemctl status cron
```

* Shows `Loaded: masked`.

2. **Unmask service:**

```bash
sudo systemctl unmask cron
```

3. **Start and enable service:**

```bash
sudo systemctl start cron
sudo systemctl enable cron
```

✅ Combines: `status`, `unmask`, `start`, `enable`

---

## **Scenario 6: Legacy SysV Service Startup (Ubuntu 20.04)**

**Problem:** Old script service not starting on boot.

1. **Add service to default runlevels:**

```bash
sudo update-rc.d myscript defaults
```

2. **Remove obsolete service:**

```bash
sudo update-rc.d oldscript remove
```

3. **Start service manually if needed:**

```bash
sudo service myscript start
```

✅ Combines: `update-rc.d defaults`, `update-rc.d remove`, `service start`

---

### **Summary of Option Usage Across Scenarios**

| Command             | Options Used                                                     | Coverage |
| ------------------- | ---------------------------------------------------------------- | -------- |
| systemctl           | status, restart, start, stop, enable, disable, unmask, is-active | 50%      |
| systemd-analyze     | blame, time, critical-chain                                      | 60%      |
| journalctl          | -n, -e, -r, -l, PRIORITY, \_PID                                  | 55%      |
| service             | status, restart, start, stop                                     | 80%      |
| update-rc.d         | defaults, remove                                                 | 40%      |
| chkconfig           | --list, on, off                                                  | 75%      |
| timedatectl/hwclock | set-time, --systohc, --hctosys, show                             | 100%     |

---
