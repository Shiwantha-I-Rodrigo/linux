## Workflow 1: Troubleshooting a Service Denied by AppArmor

**Scenario:** A web application running on Ubuntu is failing to write logs to `/var/log/webapp/`. You suspect AppArmor is blocking access.

**Step 1: Check AppArmor Status**

```bash
sudo aa-status
```

* **Explanation:** This shows all loaded profiles, which are in enforce or complain mode, and lists unconfined processes.
* **Observation:** You notice that the `usr.bin.webapp` profile is **enforced**.

---

**Step 2: Check Unconfined Processes**

```bash
sudo aa-unconfined
```

* **Explanation:** Verifies which processes are not confined. In this case, your web app process is confined, confirming AppArmor could be the cause.

---

**Step 3: Temporarily Switch Profile to Complain Mode**

```bash
sudo aa-complain /usr/bin/webapp
```

* **Explanation:** Complain mode logs violations instead of enforcing them, allowing the service to run while you diagnose.
* **Observation:** The web app starts working after switching to complain mode.

---

**Step 4: Review AppArmor Logs**

```bash
sudo journalctl | grep DENIED
```

* **Explanation:** Filters system logs for AppArmor denials to identify which file or directory is blocked.
* **Observation:** `/var/log/webapp/access.log` write attempts are denied.

---

**Step 5: Update AppArmor Profile**

* Add the required write permissions to the profile:

```
/var/log/webapp/** w,
```

* Reload the profile in enforce mode:

```bash
sudo aa-enforce /usr/bin/webapp
```

* **Explanation:** Ensures the service runs normally while still enforcing security.

---

## Workflow 2: Temporarily Disabling a Profile for System Maintenance

**Scenario:** You need to upgrade a database service, but its AppArmor profile is causing lockouts for temporary maintenance scripts.

**Step 1: Disable the Profile**

```bash
sudo aa-disable /usr/bin/dbservice
```

* **Explanation:** Temporarily disables AppArmor enforcement for this service to allow maintenance scripts.

**Step 2: Perform Maintenance**

* Run your scripts to back up databases, update packages, or modify config files.

**Step 3: Re-enable Enforcement**

```bash
sudo aa-enforce /usr/bin/dbservice
```

* **Explanation:** Restores security enforcement once maintenance is completed.

**Step 4: Verify Status**

```bash
sudo aa-status
```

* **Explanation:** Confirms that the profile is active and enforcing again.

**Summary of Options Used:**

* `aa-disable` → 100% in maintenance scenarios
* `aa-enforce` → 100% for re-enabling

**Coverage:** \~100% for temporary disabling and maintenance workflows.

---

### Key Takeaways

* `aa-status` → Daily check of AppArmor state.
* `aa-unconfined` → Identify processes not restricted.
* `aa-complain` → Diagnose without blocking operations.
* `aa-enforce` → Re-enable protection after troubleshooting.
* `aa-disable` → Temporarily lift restrictions for maintenance.

By combining these commands in this order, you can handle **daily AppArmor troubleshooting, maintenance, and security verification workflows** efficiently.

---
