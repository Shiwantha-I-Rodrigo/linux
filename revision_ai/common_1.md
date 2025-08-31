## **Scenario 1: Updating a Server and Troubleshooting a Broken Package**

### **Context**

You are a sysadmin maintaining a Ubuntu 20.04 server. Users report that an application is not starting. Before investigating the app, you decide to ensure the system is up to date.

### **Step 1: Update the package list**

```bash
sudo apt update
```

**Explanation:**

* Refreshes the package index from all configured repositories.
* Always the first step before installing or upgrading anything.
* Common daily command for ensuring you have the latest metadata.

---

### **Step 2: Upgrade installed packages**

```bash
sudo apt upgrade
```

**Explanation:**

* Upgrades installed packages without removing anything.
* Daily use: keeps packages current, fixes bugs/security issues.

---

### **Step 3: Identify broken packages**

```bash
sudo apt --fix-broken install
```

**Explanation:**

* If a package failed to install or dependencies are missing, this fixes broken packages.
* Daily troubleshooting: resolves dependency issues preventing apps from running.

---

### **Step 4: Check detailed info about a suspicious package**

```bash
apt show apache2
```

**Explanation:**

* Lets you verify version, dependencies, installed files, and maintainer info.
* Common when investigating why a service isn’t working.

---

### **Step 5: Remove or reinstall a problematic package**

```bash
sudo apt reinstall apache2
```

**Explanation:**

* Reinstalls the package without removing config files.
* Fixes corrupted binaries or missing files.
* Daily troubleshooting alternative to `purge` if you want to preserve configuration.

---

### **Step 6: Autoremove unused packages**

```bash
sudo apt autoremove
```

**Explanation:**

* Cleans orphaned dependencies.
* Maintains system hygiene and reduces disk clutter.

---

## **Scenario 2: Installing a Package Not in Default Repos (Manual .deb)**

### **Step 1: Download the package**

You downloaded `mypackage.deb` from a trusted vendor.

### **Step 2: Install with dpkg**

```bash
sudo dpkg -i mypackage.deb
```

**Explanation:**

* Installs `.deb` file directly.
* May fail if dependencies are missing — common when installing external packages.

---

### **Step 3: Fix dependencies**

```bash
sudo apt --fix-broken install
```

**Explanation:**

* Automatically installs any missing dependencies required by the `.deb` package.
* Frequent step after `dpkg -i` fails due to unmet dependencies.

---

### **Step 4: Verify installation**

```bash
dpkg -s mypackage
```

**Explanation:**

* Checks package status.
* Confirms it is installed and active.

---

## **Scenario 3: Searching for a Package and Dependencies**

### **Step 1: Search for a package**

```bash
apt search nginx
```

**Explanation:**

* Quick way to find available packages in repos.
* Daily use: ensures you are installing the correct package.

---

### **Step 2: Check dependencies**

```bash
apt-cache depends nginx
```

**Explanation:**

* Shows required packages.
* Important in troubleshooting why a package won’t install.

---

### **Step 3: Show detailed info**

```bash
apt-cache show nginx
```

**Explanation:**

* Useful for seeing version, dependencies, and conflicts.
* Helps decide if a specific version is needed.

---

## **Scenario 4: Removing Unneeded Packages or Purging Configs**

### **Step 1: Remove a package but keep configs**

```bash
sudo apt remove apache2
```

**Explanation:**

* Keeps config files intact if you plan to reinstall later.
* Daily admin use: temporary removal for testing.

### **Step 2: Completely remove a package**

```bash
sudo apt purge apache2
```

**Explanation:**

* Deletes config files as well.
* Used when cleaning up or preparing for a fresh reinstall.

---

## **Summary of Most Common Options Used**

| Command     | Commonly Used Options                                                        |
| ----------- | ---------------------------------------------------------------------------- |
| `apt`       | update, upgrade, install, reinstall, remove, purge, autoremove, show, search |
| `apt-get`   | install, remove, autoremove, update, upgrade, dist-upgrade, check            |
| `apt-cache` | search, show, depends                                                        |
| `dpkg`      | -i, -r, -P, -s, -L, -C                                                       |

---
---
---

## **Scenario 1: Updating a RHEL Server and Fixing Broken Packages**

### **Context**

A user reports a service isn’t working. You want to ensure the system is up-to-date and troubleshoot package issues.

### **Step 1: Update package metadata**

```bash
sudo dnf update
```

**Explanation:**

* Refreshes the repository metadata.
* Essential first step before installing or upgrading any package.
* Equivalent to `apt update`.

---

### **Step 2: Upgrade installed packages**

```bash
sudo dnf upgrade
```

**Explanation:**

* Upgrades all installed packages, handling dependency changes automatically.
* Daily use: keeps the system patched and secure.

---

### **Step 3: Search for a specific package**

```bash
dnf search httpd
```

**Explanation:**

* Useful to verify available packages or find exact package names before installation.
* Daily admin use: ensures correct package names.

---

### **Step 4: Install or reinstall a package**

```bash
sudo dnf install httpd
```

**Explanation:**

* Installs the package along with all dependencies.
* Reinstallation is implicit if the package is already installed — fixes missing or corrupted files.

---

### **Step 5: Show detailed package info**

```bash
dnf info httpd
```

**Explanation:**

* Displays version, repository, installed size, dependencies.
* Important for troubleshooting why a service may fail or to verify updates.

---

### **Step 6: Remove unneeded packages**

```bash
sudo dnf remove httpd
```

**Explanation:**

* Removes the package while handling dependencies safely.
* Daily usage: cleaning up old packages or preparing for a fresh install.

---

## **Scenario 2: Installing Packages from RPM Files**

### **Step 1: Install a downloaded RPM**

```bash
sudo rpm -i mypackage.rpm
```

**Explanation:**

* Installs `.rpm` directly.
* Common for software not available in standard repositories.

---

### **Step 2: Upgrade an existing RPM**

```bash
sudo rpm -U mypackage.rpm
```

**Explanation:**

* Upgrades or installs the package if not already present.
* Daily troubleshooting: ensures the latest vendor-provided version is applied.

---

### **Step 3: Verify installation**

```bash
rpm -qi mypackage
```

**Explanation:**

* Shows version, install date, and description.
* Daily use: confirming the package installed correctly.

---

### **Step 4: List installed files**

```bash
rpm -ql mypackage
```

**Explanation:**

* Lists all files installed by the package.
* Useful when debugging missing binaries or configuration files.

---

### **Step 5: Identify which package owns a file**

```bash
rpm -qf /usr/bin/mybinary
```

**Explanation:**

* Determines which package provided a particular file.
* Daily use: troubleshooting missing or overwritten binaries.

---

## **Scenario 3: Querying Installed Packages**

### **Step 1: List all installed packages**

```bash
rpm -qa
```

**Explanation:**

* Displays all packages currently installed on the system.
* Essential daily command to verify system state or check versions.

---

### **Step 2: List available versions in repositories**

```bash
dnf list httpd
```

**Explanation:**

* Shows installed and available versions.
* Useful when planning upgrades or troubleshooting version mismatches.

---

## **Summary of Most Common Options Used**

| Command | Commonly Used Options                                |
| ------- | ---------------------------------------------------- |
| `dnf`   | install, remove, update, upgrade, search, info, list |
| `rpm`   | -i, -U, -e, -qa, -ql, -qi, -qf                       |

---
---
---
