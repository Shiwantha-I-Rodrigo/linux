# 🔹 Scenarios 1

## **Scenario 1: Setting up a new user’s shell environment**

Sam just joined your team. He logs in and wants to check what shell he’s using, the current username, and the location of his shell history file. Afterward, he wants to verify his login name, the max number of history commands stored, and the search path for executables.

To ensure his prompt feels personalized, Sam changes the primary shell prompt string to display `SamShell> `. He then inspects all shell variables and modifies one of them temporarily so a new environment variable `EDITOR` is set to `nano`.

---

## **Scenario 2: Managing file creation defaults and process limits**

Priya is creating a script that will generate many log files. She wants to know the **current default permissions** for new files and then set them so new files are readable/writable only by the owner.

Next, she decides to check all resource limits for her shell, then enforces a maximum file size of 10 blocks and sets a maximum of 200 processes. To prevent runaway processes, she also configures a limit on CPU time per process (60 seconds).

She re-checks all limits again to confirm the changes.

---

## **Scenario 3: Troubleshooting locale settings**

Lee notices that some applications display date/time in the wrong format. He decides to check the **names of all locale categories**, then display the key-value settings of the `LC_TIME` category.

Afterward, as the system administrator, Lee permanently sets the system-wide locale to `en_US.UTF-8`.

---

## **Scenario 4: Logging application activity**

A custom script of yours is failing silently. You want to record debug information into the system logs.

First, you write a short message tagged with `myapp`, making sure the process ID is also included.
Then, you log another message with **priority** `user.err` and make sure it also appears on the screen.

Later, you decide to write a longer multi-line debug log. Instead of typing it all, you place the message in a file called `debug.txt` and send its contents to the system log.

---

## **Scenario 5: Combining environment with resource limits and logging**

Nora runs a simulation program that consumes a lot of memory. She sets an environment variable `SIM_MODE=fast` just for the execution of the simulation command.

Before running it, she checks the max virtual memory allowed, then restricts it to 500000 KB. She also ensures that the number of threads (`-P`) is capped.

While running the simulation, she logs a tagged message `simulation` with the process ID so she can later correlate logs with performance.

---

# 🔹 Answer Key 1

## **Scenario 1: Setting up a new user’s shell environment**

```bash
printenv SHELL
printenv USER
printenv HISTFILE
printenv LOGNAME
printenv HISTSIZE
printenv PATH
PS1="SamShell> "
set
export EDITOR=nano
```

---

## **Scenario 2: Managing file creation defaults and process limits**

```bash
umask
umask 077
ulimit -a
ulimit -f 10
ulimit -u 200
ulimit -t 60
ulimit -a
```

---

## **Scenario 3: Troubleshooting locale settings**

```bash
locale -c
locale -k LC_TIME
sudo localectl set-locale LANG=en_US.UTF-8
```

---

## **Scenario 4: Logging application activity**

```bash
logger -i -t myapp "Starting application"
logger -i -p user.err -s "Application failed with error code 1"
logger -f debug.txt
```

*(Example `debug.txt` file content)*

```
[DEBUG] Step 1 completed.
[DEBUG] Step 2 failed: connection refused.
[DEBUG] Retrying in 5 seconds...
```

---

## **Scenario 5: Combining environment with resource limits and logging**

```bash
env SIM_MODE=fast ./simulation
ulimit -v
ulimit -v 500000
ulimit -P 100
logger -i -t simulation "Simulation started in fast mode"
```

---
---
---

# 🔹 Scenarios 2

## **Scenario 6: Debugging user identity and working directory**

Chris is debugging permission issues in a shared system. He first checks his **user ID**, **group ID**, and **login name**. Then he verifies the **current working directory** and the **session type** he’s connected with (TTY vs GUI).

---

## **Scenario 7: Fine-tuning memory and thread limits**

Fatima runs a data analysis program that consumes lots of memory. She wants to prevent processes from locking too much memory, so she limits the max size of memory locks. Then, since her shell supports both `-P` and `-T` options for thread count, she enforces both to ensure consistency.

---

# 🔹 Answer Key 2

## **Scenario 6: Debugging user identity and working directory**

```bash
printenv UID
printenv GID
printenv LOGNAME
printenv PWD
printenv XDG_SESSION_TYPE
```

---

## **Scenario 7: Fine-tuning memory and thread limits**

```bash
ulimit -l 2048
ulimit -T 50
```

---