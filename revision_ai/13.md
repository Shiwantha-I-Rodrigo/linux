# 🔹 Scenarios 1

## **Scenario 1: The Web Server Startup Issue**

You’re the sysadmin of a production server hosting a web application. Users complain that the web app is unreachable after reboot.

1. You check if the web service is running.
2. It’s not active, so you enable it to start on boot.
3. You start it immediately.
4. After that, you confirm the status.
5. To investigate why it didn’t start automatically last time, you review boot performance and identify which services delayed startup.
6. You then look at logs in reverse order, filtering only critical errors, and finally jump to the end of the logs for the freshest info.

---

## **Scenario 2: Database Misconfiguration**

The database service was upgraded and developers complain about connection failures.

1. You check the service’s current status.
2. Instead of stopping it, you reload its configuration to apply new changes.
3. When errors persist, you restart the service.
4. You then view all the related logs, making sure long fields are not truncated.
5. Finally, you filter the logs only from the database process ID given by `ps`.

---

## **Scenario 3: Legacy Service Migration**

Your organization still uses some old SysV init scripts on a Debian-based system.

1. You install the legacy monitoring service into default runlevels.
2. Later, you remove it from startup but force removal since dependencies exist.
3. You then configure it to **start only** in runlevel 3 and stop in runlevel 5.
4. To test, you manually change the system runlevel.

---

## **Scenario 4: RHEL-Style Legacy Management**

On a Rocky Linux server, a legacy script needs to be integrated.

1. You add the service to be managed by the system.
2. You configure it to start only in runlevels 3 and 5.
3. You list all services and their runlevel settings to confirm.
4. Later, it’s no longer required, so you remove it from management.

---

## **Scenario 5: Time Sync Troubleshooting**

A developer reports timestamps in log files are inconsistent across servers.

1. You check and reset the system date and time manually.
2. Then, you sync the system time to the hardware clock.
3. On another occasion, you instead set the system time from the hardware clock.
4. To ensure consistency across boots, you reload the systemd manager configuration.

---

## **Scenario 6: Mixed-Style Service Troubleshooting**

A developer can’t access the local caching service.

1. You start the service using the **old-style** service command for quick testing.
2. You check the current status.
3. After identifying config issues, you reload the service.
4. Still failing, so you restart it.
5. To double-check stability, you also use the modern system manager tool to confirm if the service is active.

---

# 🔹 Example Files 1

**Example: `/var/log/db_errors.log` (simulated)**

```
2025-08-24 10:12:01 ERROR Connection refused on port 5432
2025-08-24 10:12:02 WARNING Retrying connection
2025-08-24 10:12:05 CRITICAL Database startup failed: missing pg_hba.conf entry
```

**Example: `/etc/init.d/legacy-monitor` (script)**

```bash
#!/bin/sh
### BEGIN INIT INFO
# Provides:          legacy-monitor
# Required-Start:    $remote_fs $syslog
# Default-Start:     2 3 5
# Default-Stop:      0 1 6
### END INIT INFO
case "$1" in
  start) echo "Starting legacy monitor..." ;;
  stop) echo "Stopping legacy monitor..." ;;
  restart) echo "Restarting legacy monitor..." ;;
esac
```

---

# 🔹 Answer Key 1

## **Scenario 1: Web Server Startup Issue**

```bash
systemctl is-active apache2
systemctl enable apache2
systemctl start apache2
systemctl status apache2
systemd-analyze blame
systemd-analyze critical-chain
journalctl -r PRIORITY=3
journalctl -e
```

---

## **Scenario 2: Database Misconfiguration**

```bash
systemctl status postgresql
systemctl reload postgresql
systemctl restart postgresql
journalctl -a -l -n 50
journalctl OBJECT_PID=1234
```

---

## **Scenario 3: Legacy Service Migration**

```bash
update-rc.d legacy-monitor defaults
update-rc.d -f legacy-monitor remove
update-rc.d legacy-monitor start 3
update-rc.d legacy-monitor stop 5
init 3
```

---

## **Scenario 4: RHEL-Style Legacy Management**

```bash
chkconfig --add legacy-cache
chkconfig --level 35 legacy-cache on
chkconfig --list
chkconfig --del legacy-cache
```

---

## **Scenario 5: Time Sync Troubleshooting**

```bash
timedatectl set-time "2025-08-24 12:45:00"
hwclock --systohc
hwclock --hctosys
systemctl daemon-reload
```

---

## **Scenario 6: Mixed-Style Service Troubleshooting**

```bash
service memcached start
service memcached status
service memcached reload
service memcached restart
systemctl is-active memcached
```

---
---
---

# 🔹 Scenarios 2

---

## **Scenario 7: Preventing Service Auto-Start**

You are decommissioning an old FTP server.

1. You stop the FTP service immediately.
2. To prevent it from auto-starting at boot, you disable it.
3. For stricter control, you mask it so no one can manually start it.
4. A week later, management decides to re-enable it temporarily — so you unmask and enable it again.

---

## **Scenario 8: Deep Boot Analysis**

A server is booting very slowly.

1. You check the total boot-up time.
2. You then verify the correctness of a suspicious unit file.
3. Finally, you dump the entire systemd state for a full report.

---

## **Scenario 9: User & Device-Specific Log Debugging**

You’re troubleshooting an application running under a specific non-root user.

1. You filter logs by that user’s UID.
2. You then filter logs only from the syslog transport.
3. Later, a hardware device (network adapter `eth0`) is acting up — you view its udev logs.

---

## **Scenario 10: Legacy Service Stopping**

A developer accidentally started a background service using the old init-style method.

1. You stop it using the same style.
2. To be thorough, you later confirm its status with the modern tool.

---

# 🔹 Answer Key 2

### **Scenario 7: Preventing Service Auto-Start**

```bash
systemctl stop vsftpd
systemctl disable vsftpd
systemctl mask vsftpd
systemctl unmask vsftpd
systemctl enable vsftpd
```

---

### **Scenario 8: Deep Boot Analysis**

```bash
systemd-analyze time
systemd-analyze verify /etc/systemd/system/custom.service
systemd-analyze dump
```

---

### **Scenario 9: User & Device-Specific Log Debugging**

```bash
journalctl _UID=1001
journalctl _TRANSPORT=syslog
journalctl _UDEV_SYSNAME=eth0
```

---

### **Scenario 10: Legacy Service Stopping**

```bash
service nfs stop
systemctl status nfs
```

---