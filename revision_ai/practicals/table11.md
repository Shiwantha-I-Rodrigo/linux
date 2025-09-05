### **Sample Setup (applies to all scenarios)**

```bash
# Create sample files and directories
mkdir -p /tmp/project/{conf,logs,data}
touch /tmp/project/conf/app.conf /tmp/project/logs/app.log /tmp/project/data/data.txt

# Create sample users and groups
sudo useradd alice
sudo useradd bob
sudo groupadd devs
sudo usermod -aG devs bob
```

---

### **Scenario 1: SELinux mode inspection and toggling**

**Story:**
Alice is troubleshooting why her application cannot write logs. She suspects SELinux is blocking access.

**Tasks:**

1. Check the current SELinux mode.
2. Display the full SELinux status.
3. Temporarily set SELinux to permissive mode.
4. Verify the change using both `getenforce` and `sestatus`.
5. Switch SELinux back to enforcing mode.

---

### **Scenario 2: Managing SELinux booleans**

**Story:**
The application needs HTTPD to access user content. Bob needs to enable the corresponding SELinux boolean permanently.

**Tasks:**

1. List all SELinux booleans and their current values.
2. Check the current value of the `httpd_enable_homedirs` boolean.
3. Enable `httpd_enable_homedirs` temporarily.
4. Enable `httpd_enable_homedirs` permanently across reboots.
5. Verify the change.

---

### **Scenario 3: File context restoration**

**Story:**
Alice accidentally copied configuration files from another server. SELinux contexts are incorrect, and the app fails to start.

**Tasks:**

1. Check the SELinux context of `/tmp/project/conf/app.conf`.
2. Restore the correct context on a single file.
3. Restore contexts recursively on the entire `/tmp/project` directory.
4. Verify that all files have the correct context.

---

### **Scenario 4: ACL management**

**Story:**
Bob is managing project access. He wants Alice to edit logs but not delete them, while devs can read data files.

**Tasks:**

1. Grant user `alice` write access to `/tmp/project/logs/app.log`.
2. Grant group `devs` read access to `/tmp/project/data/data.txt`.
3. Remove write access for Alice from `/tmp/project/logs/app.log`.
4. Remove all ACLs from `/tmp/project/data/data.txt`.
5. Show the final ACLs on both files.

---

### **Scenario 5: SELinux port and boolean management**

**Story:**
Alice is setting up a custom web service on port 8080, but SELinux blocks it. She also needs to enable a boolean to allow network access.

**Tasks:**

1. Use `semanage` to allow HTTP traffic on port 8080.
2. Enable a relevant SELinux boolean temporarily.
3. Make the boolean change persistent.
4. Verify port and boolean configuration.

---

### **Scenario 6: Analyzing SELinux denials**

**Story:**
After starting her app, Alice finds SELinux is denying multiple operations.

**Tasks:**

1. Generate an SELinux AVC denial report for the entire log.
2. Identify denied files and services from the report.
3. Suggest commands to restore contexts or adjust booleans to fix issues.

---
---
---

# **SELinux & Security – Answer Key v2**

### **Sample Setup**

```bash
# Create sample files and directories
mkdir -p /tmp/project/{conf,logs,data}
touch /tmp/project/conf/app.conf /tmp/project/logs/app.log /tmp/project/data/data.txt

# Create sample users and groups
sudo useradd alice
sudo useradd bob
sudo groupadd devs
sudo usermod -aG devs bob
```

---

## **Scenario 1: SELinux Mode Inspection and Toggling**

```bash
# Check current SELinux mode
getenforce

# Display full SELinux status
sestatus

# Temporarily set SELinux mode
setenforce 0           # numeric for permissive
setenforce 1           # numeric for enforcing

# Using text forms
setenforce Permissive
setenforce Enforcing

# Verify changes
getenforce
sestatus
```

---

## **Scenario 2: Managing SELinux Booleans**

```bash
# List all booleans and their values
getsebool -a

# Check a specific boolean
getsebool httpd_enable_homedirs

# Enable boolean temporarily
setsebool httpd_enable_homedirs on

# Enable boolean permanently across reboots
setsebool -P httpd_enable_homedirs on

# Verify change
getsebool httpd_enable_homedirs
```

---

## **Scenario 3: File Context Restoration**

```bash
# Check SELinux context for a single file
ls -Z /tmp/project/conf/app.conf

# Restore context for a single file
restorecon /tmp/project/conf/app.conf

# Restore context recursively for entire directory
restorecon -R /tmp/project

# Verify
ls -lZ /tmp/project/conf
ls -lZ /tmp/project/logs
ls -lZ /tmp/project/data
```

---

## **Scenario 4: ACL Management**

```bash
# Add/modify ACL for user alice (write access)
setfacl -m u:alice:w /tmp/project/logs/app.log

# Add/modify ACL for group devs (read access)
setfacl -m g:devs:r /tmp/project/data/data.txt

# Remove specific ACL entry for user alice
setfacl -x u:alice /tmp/project/logs/app.log

# Remove all ACLs from a file
setfacl -b /tmp/project/data/data.txt

# Show final ACLs
getfacl /tmp/project/logs/app.log
getfacl /tmp/project/data/data.txt
```

---

## **Scenario 5: SELinux Port and Boolean Management**

```bash
# Add custom HTTP port 8080 for SELinux
semanage port -a -t http_port_t -p tcp 8080

# List all ports (verification)
semanage port -l | grep 8080

# Delete custom port (if needed)
semanage port -d -t http_port_t -p tcp 8080

# Enable boolean temporarily
setsebool httpd_can_network_connect on

# Enable boolean permanently
setsebool -P httpd_can_network_connect on

# Verify boolean
getsebool httpd_can_network_connect
```

---

## **Scenario 6: Analyzing SELinux Denials**

```bash
# Generate AVC denial report for entire log
sealert -a /var/log/audit/audit.log

# Identify denied files/services from report
# (Example: app.conf denied write, httpd denied network connect)

# Suggested fixes based on report
# Restore contexts
restorecon -R /tmp/project

# Adjust relevant booleans
setsebool -P httpd_enable_homedirs on
```

---
