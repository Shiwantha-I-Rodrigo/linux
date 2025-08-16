# BASH SCRIPTING

| **Command**           | **Description**                                                                                                                |
| - | - |
| `$ date ; who`        | Executes multiple commands in sequence, separated by **( ; )**|
| `$ date > file1`      | Redirects the standard output (STDOUT) of `date` to **file1**, overwriting it|
| `$ date >> file1`     | Appends the output of `date` to **file1** instead of overwriting it|
| `$ date 2> error.log` | Redirects the standard error (STDERR) of `date` to **error.log**.<br>**File descriptors :** <br>- **0** = STDIN<br>- **1** = STDOUT<br>- **2** = STDERR|
| `$ ls \| sort`        | Pipes the output of `ls` (STDOUT) into `sort` as its input (STDIN)|

> **"\>" , "\>\>" , "|"** are meta-characters, to include these in a string they must be escaped with **"\\"**.

---

## SCRIPT FILES

```
#!/bin/bash

# This is a simple shell script

name="Alice"
echo "Hello, $name!"

echo "Current date and time: $(date)"

echo "Files in this directory:"
ls
```

* **Shebang (`#!`)**: Indicates which shell should execute the script (ie. `#!/bin/bash`). Any installed shell can be used.
* **Default Shell**: If no shebang is specified, the script runs with the default shell defined in the system's `passwd` file.
* **Comments**: Lines starting with `#` (except the shebang) are treated as comments.
* **File Extension**: Shell scripts typically use the `.sh` extension.
* **Command Lookup**: Linux searches for commands in the directories listed in the `$PATH` variable.
* **File Permissions**: Script file should be executable ( **x** ) by the user.
* **Running Scripts**: Use a **relative (`./script.sh`)** or **absolute path** to execute a script directly.

> Why ( **`./`** ) ***?*** By default, Linux does **not** search the current directory ( . ) when looking for commands or scripts to execute.

---

## ADVANCED SCRIPTING

| **Term**              | **Description**|
| -                     | - |
| `echo "hi' there"`    | Double quotes (`"`) are used to enclose strings that contain special characters (e.g., `'`)|
| `echo`                | Outputs an empty line|
| `$HOME`               | An example of an **Environment Variable**|
| `home`                | An example of a **User-defined Variable**<br>`home=kentucky` : set variable (there must be **no spaces**)<br>`echo $home` : use variable|
| `set`                 | Displays all variables (both environment and user-defined)|
| `env` / `printenv`    | Displays only **environment variables**|

> **!** variable **type** is automatically set by the shell.

| Context       | Example|
| -             | - |
| Assignment    | `var=5`|
| Reading       | `echo $var`|
| Arithmetic    | `((var+1))` OR `(($var+1))`|

---

### ARGUMENTS

**`$ myScript.sh argument1 argument2`**
- the passed arguments are referenced in the script as $1, $2, and so on ( $0 : script file name ).
- missing / partial arguments do not produce errors by ***default***.
```
#!/bin/bash
echo Welcome $1 ,Have a good $2
```
- missing **required** arguments will produce errors / unexpected behavior.
```
#!/bin/bash
cat "$1"
```

---

### EXIT STATUS

- The **exit status** indicates to the shell whether a script or command executed successfully.
- The special variable " **$?** " stores the exit status of the **most recently executed command**.
- An exit status of `0` usually means **success**, while any **non-zero** value indicates an **error** or **failure**.
```
#!/bin/bash

ls "$1"

status=$?

if [ $status -eq 0 ]; then
  echo "Directory listing succeeded."
else
  echo "Failed to list directory."
fi
```

---

## SCRIPT PROGRAMS

**Command Substitution** : allows assigning the output of a command to a variable.

| **Method**            | **Syntax**        | **Description**                           | **Example**               |
| -                     | -                 | -                                         | - |
| Backticks             | `` `command` ``   | Legacy form of command substitution       | ``echo " Today is `date` "``  |
| Dollar Parentheses    | `$(command)`      | Modern, preferred form, supports nesting | `echo " Today is $(date) "` |


> **!** Only **integer** math operations are allowed with in **$ ( )** brackets.\
> **!** To use **floats** a 3rd party tool must be used with command substitution.
```
#!/bin/bash

$var1=$(echo "scale=4; 3.4 / 5" | bc)
echo $var1
```

When performing **floating-point** operations using the **`bc`** utility, the **scale** variable must be set.\
It determines the number of decimal places in the output.\
For more **complex mathematical** calculations, consider using the **Z shell (zsh)**.

---

### LOGIC

**IF**
```
if [ condition ]
then
  commands
fi
```

**---> Conditions**

| **Expression**    | **Meaning**|
| -                 | - |
| `n1 -eq n2`       | `n1` == `n2`|
| `n1 -ge n2`       | `n1` >= `n2`|
| `n1 -gt n2`       | `n1` >  `n2`|
| `n1 -le n2`       | `n1` <= `n2`|
| `n1 -lt n2`       | `n1` <  `n2`|
| `n1 -ne n2`       | `n1` != `n2`|
|                   ||
| `str1 = str2`     | `str1` same as `str2`|
| `str1 != str2`    | `str1` not same as `str2`|
| `str1 < str2`     | `str1` is lesser than  `str2` (in lexicographical order)|
| `str1 > str2`     | `str1` is greater than `str2` (in lexicographical order)|
| `-n str1`         | `str1` length > 0|
| `-z str1`         | `str1` length = 0|
|                   ||
| `-e file1`        | `file1` exists|
| `-d file1`        | `file1` exists and is a directory|
| `-f file1`        | `file1` exists and is a file|
| `-r file1`        | `file1` exists and is readable|
| `-s file1`        | `file1` exists and is not empty|
| `-w file1`        | `file1` exists and is writable|
| `-x file1`        | `file1` exists and is executable|
| `-O file1`        | `file1` exists and is owned by the current user|
| `-G file1`        | `file1` exists and its group is the user's group|
| `file1 -nt file2` | `file1` newer than `file2`|
| `file1 -ot file2` | `file1` is older than `file2`|

---

**CASE**
```
case $var in
pattern1)
    commands;;
pattern2 | pattern3)
    commands;;
*)
    commands;;
esac
```

---

**FOR**
```
for $var in $list ; do
    commands
done
```

---

**WHILE**
```
while [ condition ] ; do
    commands
done
```

---

**UNTIL**
```
until [ condition ] ; do
    commands
done
```

---

### TEXT MANIPULATION

( see chapter 4 )

- globbing : using wildcards to pruce through multiple files / directories.
- parameter expansion : command ${ command_that_return_a_value }.
- read : read text file sline by line and process.
- reg exp : find and replace chars in strings.

**WILDCARDS**

- **c?t** : match cat / cbt / cct / cdt / ...
- **c*t** : match ct / cat / caat / caaat / ...
- **c[au]t** : match cat / cut
