### **Scenario 1: Investigating a service log for errors**

**Situation:** The web server is behaving strangely, and you suspect a configuration or runtime error. You need to quickly find relevant log lines and analyze them.

**Workflow:**

1. **Check the last few lines of the log**

   ```bash
   tail -n 50 /var/log/nginx/error.log
   ```

   * `tail -n 50` → shows the last 50 lines, useful for recent events.
   * This gives a quick look at recent errors.

2. **Search for error patterns**

   ```bash
   grep -i "error" /var/log/nginx/error.log
   ```

   * `-i` → ignore case (`Error`, `ERROR`, `error`).
   * Filters only lines containing “error”.

3. **Count how many errors occurred today**

   ```bash
   grep -i "error" /var/log/nginx/error.log | wc -l
   ```

   * `wc -l` → counts lines (number of errors).

4. **Find errors recursively in multiple logs**

   ```bash
   grep -ri "error" /var/log/nginx/
   ```

   * `-r` → search recursively through subdirectories.
   * `-i` → ignore case.

5. **View large logs with scrolling**

   ```bash
   less /var/log/nginx/error.log
   ```

   * `/` → search forward for patterns.
   * `n` → jump to next match.
   * `q` → quit pager.

**Explanation:**

* You start with the most recent entries (`tail`) to avoid scanning huge logs.
* `grep` isolates relevant content.
* `wc` quantifies problems.
* Recursive search is useful for distributed logs.
* `less` allows navigation for deeper inspection.

---

### **Scenario 2: Quick configuration file edits**

**Situation:** You need to update multiple entries in a configuration file.

**Workflow:**

1. **Backup original file**

   ```bash
   cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
   ```

   * Always a safety step before editing.

2. **Replace old IP address with new one**

   ```bash
   sed -i 's/192.168.0.10/192.168.0.20/g' /etc/nginx/nginx.conf
   ```

   * `-i` → in-place editing.
   * `s/pattern/replacement/g` → replace globally in file.

3. **Verify changes**

   ```bash
   grep "192.168.0.20" /etc/nginx/nginx.conf
   ```

   * Confirms that new IP exists.

4. **Check for accidental syntax changes**

   ```bash
   nginx -t
   ```

   * Tests configuration syntax.

**Explanation:**

* `sed` is powerful for automated replacements in configs.
* Combining `grep` ensures edits were applied correctly.

---

### **Scenario 3: Parsing CSV or delimited system data**

**Situation:** You have a system report with CPU usage per user in a CSV file and want the top consumers.

**Workflow:**

1. **Extract the username and CPU column**

   ```bash
   cut -d ',' -f1,3 usage_report.csv
   ```

   * `-d ','` → comma delimiter.
   * `-f1,3` → select fields 1 and 3 (username, CPU usage).

2. **Sort by CPU usage descending**

   ```bash
   cut -d ',' -f1,3 usage_report.csv | sort -t ',' -k2 -n -r
   ```

   * `-t ','` → delimiter.
   * `-k2` → sort by second field.
   * `-n` → numeric sort.
   * `-r` → reverse for highest first.

3. **Show top 5 users**

   ```bash
   cut -d ',' -f1,3 usage_report.csv | sort -t ',' -k2 -n -r | head -n 5
   ```

**Explanation:**

* `cut` isolates relevant fields.
* `sort` organizes data numerically.
* `head` gives a quick summary of top consumers.

---

### **Scenario 4: Debugging script output**

**Situation:** Your custom bash script prints lots of output, but you want to see formatted results and log to a file simultaneously.

**Workflow:**

1. **Run script and log output**

   ```bash
   ./myscript.sh | tee -a script.log
   ```

   * `tee -a` → append output to file while also displaying on terminal.

2. **Format numeric output**

   ```bash
   printf "CPU usage: %.2f%%\n" 23.456
   ```

   * `%.2f` → float with 2 decimals.
   * Clean and readable reporting.

3. **Combine with grep for filtering**

   ```bash
   ./myscript.sh | grep -i "warning" | tee warnings.log
   ```

**Explanation:**

* `tee` is excellent for logging live output.
* `printf` improves readability of script data.
* Filtering ensures you only log critical messages.

---

### **Summary of Most Used Options from Scenarios**

| Command  | Commonly Used Options  |
| -------- | ---------------------- |
| `grep`   | `-i`, `-r`, `-n`, `-v` |
| `sed`    | `-i`, `-e`             |
| `awk`    | `-F`, `-v`             |
| `cut`    | `-d`, `-f`             |
| `sort`   | `-k`, `-n`, `-r`, `-t` |
| `wc`     | `-l`                   |
| `head`   | `-n`                   |
| `tail`   | `-n`                   |
| `less`   | `/`, `n`, `q`          |
| `tee`    | `-a`                   |
| `printf` | `%.2f`, `%s`, `%d`     |

---

## **Scenario 5: Detecting disk space hogs**

**Situation:** The server is running low on disk space. You need to identify which directories are consuming the most space.

**Workflow:**

1. **List disk usage per directory**

   ```bash
   du -sh /var/* 2>/dev/null
   ```

   * `-s` → summarize per directory.
   * `-h` → human-readable (MB, GB).
   * `2>/dev/null` → suppress permission errors.

2. **Sort directories by size**

   ```bash
   du -sh /var/* 2>/dev/null | sort -hr
   ```

   * `-h` → human-readable sort.
   * `-r` → reverse (largest first).

3. **Check top 5 largest directories**

   ```bash
   du -sh /var/* 2>/dev/null | sort -hr | head -n 5
   ```

**Explanation:**

* `du` identifies disk usage.
* `sort` orders them.
* `head` quickly highlights the biggest consumers.

---

## **Scenario 6: Investigating network connections**

**Situation:** Some processes are using high network bandwidth. You want to identify which ports and processes are active.

**Workflow:**

1. **Check current connections**

   ```bash
   ss -tulnp
   ```

   * Shows TCP/UDP connections with PID and port info.

2. **Filter for established connections**

   ```bash
   ss -tulnp | grep ESTAB
   ```

   * `grep` isolates established connections.

3. **Count connections per port**

   ```bash
   ss -tulnp | grep ESTAB | awk '{print $5}' | cut -d':' -f2 | sort | uniq -c | sort -nr
   ```

   * `awk '{print $5}'` → extracts the destination field.
   * `cut -d':' -f2` → isolates the port number.
   * `uniq -c` → counts occurrences.
   * `sort -nr` → highest connection counts first.

**Explanation:**

* Combines `grep`, `awk`, `cut`, `sort`, and `uniq` to quickly identify “hot” ports.
* Very useful during network troubleshooting.

---

## **Scenario 7: Monitoring memory usage**

**Situation:** The server is slow, suspect memory issues. You want a summary of memory-hungry processes.

**Workflow:**

1. **List processes with memory usage**

   ```bash
   ps aux --sort=-%mem | head -n 10
   ```

   * `--sort=-%mem` → sort by memory descending.
   * `head -n 10` → top 10 memory users.

2. **Format output nicely**

   ```bash
   ps aux --sort=-%mem | awk '{printf "%-10s %-10s %-5s\n",$1,$2,$4}' | head -n 10
   ```

   * `awk` formats user, PID, memory percentage.
   * `%-10s` → left-aligned string field.

**Explanation:**

* Quickly identifies which processes consume the most memory.
* Combining `ps`, `awk`, and `head` creates readable dashboards.

---

## **Scenario 8: Checking failed SSH login attempts**

**Situation:** Security team wants a report of failed SSH attempts in the last week.

**Workflow:**

1. **Search log for failures**

   ```bash
   grep -i "failed password" /var/log/auth.log
   ```

2. **Count attempts per user**

   ```bash
   grep -i "failed password" /var/log/auth.log | awk '{print $9}' | sort | uniq -c | sort -nr
   ```

   * `$9` → username field in log lines.
   * `uniq -c` → count attempts.
   * `sort -nr` → show most targeted accounts first.

3. **Optional: monitor in real-time**

   ```bash
   tail -f /var/log/auth.log | grep --line-buffered "failed password"
   ```

   * `tail -f` → follow log in real-time.
   * `--line-buffered` → ensures immediate `grep` output.

**Explanation:**

* Combines `grep`, `awk`, `sort`, and `uniq` for security reporting.
* `tail -f` is standard for live monitoring.

---

## **Scenario 9: Auditing a large CSV inventory**

**Situation:** You have thousands of server records in a CSV, need to find inactive servers.

**Workflow:**

1. **Filter inactive servers**

   ```bash
   grep -i "inactive" inventory.csv
   ```

2. **Extract server name and last active date**

   ```bash
   grep -i "inactive" inventory.csv | cut -d ',' -f1,5
   ```

   * Fields: server name and last activity column.

3. **Sort by date**

   ```bash
   grep -i "inactive" inventory.csv | cut -d ',' -f1,5 | sort -t ',' -k2
   ```

**Explanation:**

* `grep` isolates relevant records.
* `cut` extracts important fields.
* `sort` orders by last active date for easy review.

---

### **Summary of Options Used in These Scenarios**

| Command | Options Used            |
| ------- | ----------------------- |
| `grep`  | `-i`, `--line-buffered` |
| `awk`   | `{print}`, `printf`     |
| `cut`   | `-d`, `-f`              |
| `sort`  | `-n`, `-r`, `-t`, `-k`  |
| `head`  | `-n`                    |
| `tail`  | `-n`, `-f`              |
| `uniq`  | `-c`                    |

---

## **Scenario 10: Extracting fields and computing totals with `awk`**

**Situation:** You have a CSV file of server resource usage and want to calculate total memory used by all servers.

**CSV Example (`resources.csv`):**

```
server1,8,120
server2,16,256
server3,32,512
```

Columns: `server_name,CPU(cores),Memory(GB)`

**Workflow:**

1. **Print server name and memory**

```bash
awk -F',' '{print $1, $3}' resources.csv
```

* `-F','` → sets comma as field separator.
* `$1` → server name, `$3` → memory.

**Output:**

```
server1 120
server2 256
server3 512
```

2. **Compute total memory**

```bash
awk -F',' '{sum += $3} END {print "Total Memory:", sum " GB"}' resources.csv
```

* `sum += $3` → adds memory field.
* `END {…}` → executes after processing all lines.

**Output:**

```
Total Memory: 888 GB
```

3. **Filter servers using more than 200 GB**

```bash
awk -F',' '$3>200 {print $1, $3}' resources.csv
```

* `$3>200` → only prints servers with memory > 200 GB.

**Explanation:**

* `awk` is perfect for **field extraction, arithmetic, and conditional filtering**.
* `-F` sets the delimiter; variables like `$1`, `$2` access columns.

---

## **Scenario 11: Reformatting configuration files with `sed`**

**Situation:** You need to update IP addresses in multiple configuration files.

**Example:** `nginx.conf` contains:

```
server_name 192.168.0.10;
server_name 192.168.0.11;
```

**Workflow:**

1. **Replace a single IP in place**

```bash
sed -i 's/192.168.0.10/192.168.0.20/' nginx.conf
```

* `-i` → edits file in-place.
* `s/old/new/` → substitute first occurrence on each line.

2. **Replace all occurrences globally**

```bash
sed -i 's/192.168.0.1[0-1]/192.168.0.20/g' nginx.conf
```

* `g` → replace all matches in the line.
* `[0-1]` → regex matches 0 or 1.

3. **Delete comment lines**

```bash
sed -i '/^#/d' nginx.conf
```

* `/pattern/d` → deletes lines matching the pattern.

4. **Insert a line after a match**

```bash
sed -i '/server_name 192.168.0.20;/a listen 8080;' nginx.conf
```

* `a` → append line after match.

**Explanation:**

* `sed` is **excellent for batch find-and-replace, inserting, or deleting lines** in configs.
* `-i` avoids creating temporary files.
* Regular expressions provide flexible matching.

---

## **Scenario 12: Combining `awk` and `sed` for advanced log analysis**

**Situation:** You want to extract usernames from `/var/log/auth.log` who failed login, and format them for reporting.

**Workflow:**

1. **Extract failed login lines**

```bash
grep -i "failed password" /var/log/auth.log
```

2. **Use `awk` to get the username field**

```bash
grep -i "failed password" /var/log/auth.log | awk '{print $9}'
```

3. **Remove square brackets or extra characters with `sed`**

```bash
grep -i "failed password" /var/log/auth.log | awk '{print $9}' | sed 's/\[//g; s/\]//g'
```

* `s/\[//g` → remove `[`.
* `s/\]//g` → remove `]`.

4. **Count attempts per user**

```bash
grep -i "failed password" /var/log/auth.log | awk '{print $9}' | sed 's/\[//g; s/\]//g' | sort | uniq -c | sort -nr
```

* Combines **`awk` for field extraction**, **`sed` for cleanup**, and **`sort | uniq -c`** to get counts.

**Explanation:**

* This combination is a common sysadmin workflow: **filter → extract → clean → summarize**.

---

### **Summary of `awk` and `sed` Options Used**

| Command | Options / Patterns Used                                                                                              |
| ------- | -------------------------------------------------------------------------------------------------------------------- |
| `awk`   | `-F` (field separator), `$1`, `$2…$n` (fields), `sum += $n`, `END {}`                                                |
| `sed`   | `-i` (in-place), `s/pattern/replacement/`, `g` (global), `/pattern/d` (delete line), `/pattern/a text` (append line) |

---
