# 🔹 Scenarios

## **Scenario 1: Debugging a web server issue**

You’re a system administrator on **Ubuntu 20.04 LTS**. A colleague reports that the Nginx web server is unable to access a custom log file you placed in `/var/log/custom/`. You want to investigate whether AppArmor is blocking it. Instead of immediately enforcing restrictions, you want to make sure violations are only logged while still allowing the program to run. Afterwards, check the overall status of AppArmor to confirm the mode of profiles.

---

## **Scenario 2: Restricting a suspicious script**

On a Fedora 34 test environment, a developer wrote a Python script `data_collector.py` that downloads files from the internet. Since you don’t fully trust it, you decide to place it under strict security confinement so that any violation is blocked. After this, confirm again the global status of AppArmor and verify that the script is indeed running under restriction.

---

## **Scenario 3: Allowing freer execution temporarily**

On **RockyOS 8.5**, your database maintenance task needs to run a backup utility that is constantly hitting AppArmor denials. You decide to temporarily remove the utility from AppArmor protection to speed up troubleshooting. After disabling its restriction, check which processes on the system are currently running without any AppArmor profile.

---

## **Scenario 4: Full audit of the security posture**

After several weeks of system operation, management requests a report:

1. Which applications are under **strict enforcement**.
2. Which ones are just in **complain (log-only) mode**.
3. Which ones are running without **any AppArmor confinement**.

Prepare this audit report for an Ubuntu server that runs Nginx, MySQL, and some custom Python apps.

---

# 🔹 Example Files

For the Python test script in Scenarios 2 & 3:

`data_collector.py`

```python
import urllib.request

url = "http://example.com/test.txt"
urllib.request.urlretrieve(url, "/tmp/test.txt")
print("Download complete")
```

---

# 🔹 Answer Key

## **Scenario 1**

```bash
sudo aa-complain /etc/apparmor.d/usr.sbin.nginx
sudo aa-status
```

---

## **Scenario 2**

```bash
sudo aa-enforce /etc/apparmor.d/usr.bin.python3
sudo aa-status
```

---

## **Scenario 3**

```bash
sudo aa-disable /etc/apparmor.d/usr.bin.backup-tool
sudo aa-unconfined
```

---

## **Scenario 4**

```bash
sudo aa-status
sudo aa-unconfined
```

---