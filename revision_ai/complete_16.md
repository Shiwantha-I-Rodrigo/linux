# 🔹 Scenarios 1

## **Scenario 1: Setting up a new project for a client**

You are a freelance developer hired to build a small website for a client.
You need to:

1. Initialize a version control system for your project.
2. Configure your identity (so commits are properly attributed).
3. Create a text file describing the project using a simple editor.
4. Save your work and quit.
5. Stage and save your changes with a meaningful message.
6. Share your work with a remote server provided by the client.
7. Verify that the remote connection was added.

`README.md`:

```
# Client Website
This project is for ACME Corporation.
```

---

## **Scenario 2: Working on a team project**

You join a team project that is already hosted online.
You need to:

1. Get the full project files from the remote server.
2. Create a new feature branch and switch to it.
3. Make some edits to an existing text file in a powerful editor:

   * Jump to the top and bottom of the file.
   * Move between words.
   * Delete a character, then undo that mistake.
   * Save your edits with a new name and quit.
4. Stage and commit the updated file.
5. Rebase your branch onto the latest version of the main branch.
6. Merge your branch back into main.
7. Upload your updated work to the remote repository.

`features.txt`:

```
Feature 1: Login
Feature 2: Dashboard
Feature 3: Reports
```

---

## **Scenario 3: Reviewing past work and fixing mistakes**

You are debugging an issue in a software repository.
You need to:

1. Look at the history of the project to see who made recent changes.
2. Check the current state of your working directory.
3. Open a file with a lightweight editor:

   * Scroll down one line at a time.
   * Scroll up one line at a time.
   * Scroll half a page at a time.
   * Page up and down fully.
   * Move forward and backward one word at a time.
   * Save your edits and exit.
4. Remove a file that is no longer needed from both your project and version control.
5. Commit this cleanup with a proper message.
6. Upload the changes.

`notes.txt`:

```
TODO: Refactor code.
TODO: Remove deprecated modules.
TODO: Add error handling.
```

---

## **Scenario 4: Automating documentation inside an editor**

You are documenting a script.
You need to:

1. Open a file in a text editor.
2. Run an external shell command inside the editor to check the system date.
3. Insert the result into your document.
4. Save and force quit even if the file is write-protected.
5. Later, open another file in a different editor and practice undoing and redoing.
6. Write out the file and quit.

`doc.txt`:

```
# System Documentation
Generated on: 
```

---

# 🔹 Answer Key 1

## **Scenario 1 (Client Project)**

```bash
git init
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
nano README.md   # (write text, ctrl+o to save, ctrl+x to exit)
git add README.md
git commit -m "Initial commit: project setup"
git remote add origin https://example.com/client.git
git remote -v
```

---

## **Scenario 2 (Team Project)**

```bash
git clone https://example.com/team.git
git branch feature-login
git checkout feature-login
vim features.txt
   # inside vim:
   # gg (first line), G (last line), w (next word), b (previous word)
   # x (delete char), :w features_new.txt (save as new file), :wq (quit)
git add features_new.txt
git commit -m "Added new feature details"
git checkout main
git pull
git checkout feature-login
git rebase main
git checkout main
git merge feature-login
git push origin main
```

---

## **Scenario 3 (Debugging & Cleanup)**

```bash
git log
git status
nano notes.txt
   # ctrl+e (scroll down one line), ctrl+y (scroll up one line)
   # ctrl+d (half-page down), ctrl+u (half-page up)
   # ctrl+v (page down), ctrl+y (page up)
   # ctrl+space (forward word), alt+space (backward word)
   # ctrl+o (save), ctrl+x (exit)
git rm old_file.txt
git commit -m "Removed deprecated file"
git push
```

---

## **Scenario 4 (Automation in Docs)**

```bash
vim doc.txt
   # inside vim:
   # :r!date  (insert system date output into file)
   # :wq! (force write and quit)
nano anotherdoc.txt
   # edit text, alt+u (undo)
   # ctrl+o (save), ctrl+x (exit)
```

---
---
---

# 🔹 Scenarios 2

## **Scenario 5: Checking configuration before project handover**

Before handing a project to a client, you want to verify your Git settings and ensure everything is correct.
You also want to edit a file in Vim to practice cursor navigation (moving left, right, up, down, beginning/end of word, start/end of line, page navigation).

`practice.txt`:

```
This is a practice file.
It helps test Vim navigation.
End of file.
```

Steps:

1. Verify your Git configuration list.
2. Open the file in Vim.

   * Move left, right, up, down (`h, l, j, k`).
   * Jump to end of a word (`e`).
   * Move to beginning and end of line (`^, $`).
   * Use full page scrolls (`ctrl+b, ctrl+f`).
   * Save and quit.

---

## **Scenario 6: Running external commands inside Vim**

While editing a script, you need to check disk usage without leaving Vim, and capture a system command result separately.

`script.sh`:

```
#!/bin/bash
# Script log:
```

Steps:

1. Open the file in Vim.
2. Run an external command inside Vim to show disk usage (`!command`).
3. Save and quit.

---

## **Scenario 7: Writing reports in Nano with long files**

You are editing a long report in Nano. You want to move quickly through pages.

`report.txt`:

```
Line 1: Report start
Line 2: Details
Line 3: More details
...
Line 100: End of Report
```

Steps:

1. Open the file in Nano.
2. Move down a page (`ctrl+v`).
3. Move back up a page (`ctrl+y`).
4. Save and exit.

---

# 🔹 Answer Key 2

## **Scenario 5 (Config + Vim Navigation)**

```bash
git config --list
vim practice.txt
   # inside vim:
   # h (left), l (right), j (down), k (up)
   # e (end of word), ^ (start of line), $ (end of line)
   # ctrl+b (page up), ctrl+f (page down)
   # :wq
```

---

## **Scenario 6 (External Command in Vim)**

```bash
vim script.sh
   # inside vim:
   # :!df -h    (run shell command inside Vim, shows disk usage)
   # :wq
```

---

## **Scenario 7 (Nano Page Navigation)**

```bash
nano report.txt
   # ctrl+v (page down)
   # ctrl+y (page up)
   # ctrl+o (save)
   # ctrl+x (exit)
```

---