## **Sample Files / Setup**

* `/etc/systemd/system/webapp.service` → Custom systemd service for a web application.
* `/var/log/webapp.log` → Application log file for journaling exercises.
* `/etc/init.d/oldapp` → Legacy SysV init script for an old service.
* `/etc/rc.local` → SysV startup configuration.

---

## **Scenario 1: Boot Performance Investigation**

**Story:** Your Linux server is booting slower than expected.
**Tasks:**

1. Show the total boot-up time.
2. Display services ordered by startup duration.
3. Inspect the critical chain of services delaying boot.
4. Verify correctness of the `webapp.service` unit file.
5. Reload systemd manager configuration after modifying unit files.

**Commands:**

```bash
systemd-analyze time
systemd-analyze blame
systemd-analyze critical-chain
systemd-analyze verify /etc/systemd/system/webapp.service
systemctl daemon-reload
```

---

## **Scenario 2: Service Lifecycle Management**

**Story:** You are deploying a new web application (`webapp`) and need to manage its service.
**Tasks:**

1. Start and stop the service using both `systemctl` and `service`.
2. Restart the service and reload its configuration without stopping.
3. Enable the service at boot, then disable it.
4. Temporarily prevent the service from starting (mask/unmask).
5. Check if the service is active.
6. Check current status.

**Commands:**

```bash
systemctl start webapp
systemctl stop webapp
systemctl restart webapp
systemctl reload webapp
systemctl enable webapp
systemctl disable webapp
systemctl mask webapp
systemctl unmask webapp
systemctl status webapp
systemctl is-active webapp

service webapp start
service webapp stop
service webapp restart
service webapp reload
service webapp status
```

---

## **Scenario 3: SysV Legacy Service Administration**

**Story:** Your server runs legacy services via SysV. You need to ensure they run properly on boot.
**Tasks:**

1. Add `oldapp` to SysV startup links with defaults.
2. Remove it from startup forcibly.
3. Start/stop `oldapp` in a specific runlevel (e.g., 3).
4. Change system runlevel to 3.
5. List all services and runlevel settings (RHEL style).
6. Add/remove service to SysV management and configure runlevels.

**Commands:**

```bash
update-rc.d oldapp defaults
update-rc.d -f oldapp remove
update-rc.d oldapp start 3
update-rc.d oldapp stop 3

init 3

chkconfig --list
chkconfig --add oldapp
chkconfig --del oldapp
chkconfig --level 3 on oldapp
chkconfig --level 3 off oldapp
```

---

## **Scenario 4: Log Investigation**

**Story:** Users report that `webapp` crashes intermittently. You need to investigate logs.
**Tasks:**

1. Show the last 50 log lines.
2. Show logs in reverse order.
3. Jump to the end of logs.
4. Display all log fields without cropping.
5. Filter logs by severity (errors).
6. Filter logs for a specific user ID (`1001`) and process ID (`1234`).
7. Filter logs by transport (`syslog`) and from a specific device (`sda`).

**Commands:**

```bash
journalctl -n 50
journalctl -r
journalctl -e
journalctl -a -l
journalctl PRIORITY=3
journalctl _UID=1001
journalctl OBJECT_PID=1234
journalctl _TRANSPORT=syslog
journalctl _UDEV_SYSNAME=sda
```

---

## **Scenario 5: System Time Management**

**Story:** System timestamps are incorrect for logs and scheduled tasks.
**Tasks:**

1. Set the system date and time.
2. Synchronize hardware clock from system time.
3. Reset system time from hardware clock.

**Commands:**

```bash
timedatectl set-time "2025-09-05 14:30:00"
hwclock --systohc
hwclock --hctosys
```

---
