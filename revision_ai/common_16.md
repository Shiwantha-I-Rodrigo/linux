## **Scenario:** Updating and deploying a web application on a production server

**Background:**
You’re a Linux sysadmin. A developer has pushed a critical login page fix to Git. Your job is to pull the changes, edit the configuration, and ensure the web service runs correctly.

---

### **Step 1: Clone or update the repository**

1. **Check if repository exists locally**

   ```bash
   cd /var/www/
   ls
   ```

   *Explanation:* Confirms if `webapp` directory exists.

2. **Clone repo if it doesn’t exist**

   ```bash
   git clone https://github.com/example/webapp.git
   cd webapp
   ```

   *Explanation:* Creates a local copy for the first time.

3. **If repo exists, pull latest changes**

   ```bash
   git status
   git pull
   ```

   *Explanation:* Ensures your local repository is up-to-date to avoid conflicts.

---

### **Step 2: Create and switch to a feature/testing branch**

```bash
git branch feature/login-page-fix
git checkout feature/login-page-fix
```

*Explanation:* Isolates testing from the `main` branch to prevent breaking production.

---

### **Step 3: Edit application or config files**

**Option A: Using Vim (for config-heavy files like Nginx)**

1. Open the config file:

   ```bash
   vim /etc/nginx/sites-available/webapp.conf
   ```

2. Navigate:

   * `gg` → Go to first line
   * `G` → Jump to last line
   * `w` / `b` → Jump between words

3. Make edits:

   * `i` → Enter insert mode
   * `x` → Delete unwanted characters

4. Save and exit:

   * `:wq` → Save and quit
   * If permissions issue: `:wq!`

5. Run an external command directly:

   ```vim
   :!systemctl restart nginx
   ```

   *Explanation:* Restarts the web server from within Vim.

---

**Option B: Using Nano (for quick edits or small config changes)**

1. Open the file:

   ```bash
   nano /etc/logrotate.conf
   ```

2. Navigate:

   * `ctrl+space` → Move forward a word
   * `alt+space` → Move backward a word
   * `ctrl+v` / `ctrl+y` → Page down/up

3. Save and exit:

   * `ctrl+o` → Save
   * `ctrl+x` → Exit
   * Undo if needed: `alt+u`

---

### **Step 4: Stage and commit changes**

```bash
git add .
git commit -m "Fix login page issue and update configs"
```

*Explanation:* Stages and commits your changes locally.

---

### **Step 5: Push changes to remote for deployment**

```bash
git push -u origin feature/login-page-fix
```

*Explanation:* Uploads local changes to remote branch for review or CI/CD pipeline.

---

### **Step 6: Merge into main after testing**

```bash
git checkout main
git pull
git merge feature/login-page-fix
git push
```

*Explanation:* Safely merges the tested changes into `main` and updates the remote repository.

---

### **Step 7: Troubleshooting**

**Scenario:** Nginx fails to start after config edits

1. Check syntax from terminal:

   ```bash
   nginx -t
   ```

   *Explanation:* Detects syntax errors in the configuration.

2. Edit the file in Vim:

   ```bash
   vim /etc/nginx/sites-available/webapp.conf
   ```

   * Use `h/j/k/l` or `w/b` to navigate
   * Fix errors
   * Save `:wq!`

3. Retry restart:

   ```bash
   systemctl restart nginx
   ```

4. Verify status:

   ```bash
   systemctl status nginx
   ```

---

## **Summary of Options Used**

| Command  | Options used                                                    | Coverage |
| -------- | --------------------------------------------------------------- | -------- |
| **git**  | clone, pull, push, status, add, commit, branch, checkout, merge | 70%      |
| **vim**  | gg, G, w, b, x, \:wq, \:wq!, !command                           | 60%      |
| **nano** | ctrl+o, ctrl+x, alt+u, ctrl+space, alt+space, ctrl+v, ctrl+y    | 50%      |

**Workflow Highlights:**

* Git handles version control and collaboration
* Vim for heavy-duty config edits with shell integration
* Nano for fast, lightweight edits
* Troubleshooting integrates all three tools to quickly resolve deployment issues

---