### Sample Files

Create these before attempting the scenarios:

**`employees.csv`**

```
ID,Name,Department,Salary,StartDate
101,Alice,Engineering,70000,2020-01-15
102,Bob,HR,50000,2019-03-22
103,Charlie,Engineering,80000,2018-07-10
104,David,Sales,55000,2021-06-05
105,Eva,Engineering,90000,2020-12-11
106,Frank,HR,48000,2017-09-30
```

**`projects.txt`**

```
ProjectX Alice 2020
ProjectY Bob 2019
ProjectZ Charlie 2021
ProjectX David 2022
ProjectY Eva 2020
ProjectZ Frank 2018
```

**`patterns.txt`**

```
Engineering
Sales
```

**`months.txt`**

```
Jan
Mar
Feb
Nov
Oct
Apr
```

---

### Scenarios

**1. Salary Sleuthing**

* From `employees.csv`, extract only the names and salaries of employees in Engineering.
* Sort the result numerically by salary in reverse order.
* Save to `eng_salaries.txt` while also showing it on screen.

---

**2. Who’s Not Invited**

* From `employees.csv`, print all lines *not* containing “HR” (ignore case).
* Show only the first 3 lines of this filtered result.

---

**3. Regex Hunter**

* Using `projects.txt`, search recursively through the current directory for any project entries starting with `ProjectX` or `ProjectY`.
* Display line numbers and count how many total matches you found.

---

**4. Trim & Cut**

* From `employees.csv`, cut out just the Department field.
* Remove duplicates, ignoring case, and print how many unique departments exist.

---

**5. Format Wizardry**

* Print the employee ID and Salary from `employees.csv` using `printf`, formatting salary as integer and ID as hexadecimal (uppercase).
* Example format: `ID: 65 (0x41), Salary: 70000`.

---

**6. Pager Quest**

* Open `projects.txt` with `less`, search forward for the word “ProjectY”, then jump to the next occurrence.
* Then repeat the same with `more`, quitting after reaching the bottom.

---

**7. Sed Surgery**

* In `employees.csv`, replace “Engineering” with “R\&D” and “HR” with “PeopleOps” in one command.
* Suppress automatic printing, but then explicitly print the modified lines.

---

**8. Wordsmithing**

* Using `wc`, count the number of words, lines, characters, and bytes in `projects.txt`.
* Then extract the longest line length.

---

**9. Chrono-Sorting**

* Take `months.txt`, sort them by month name order, then by reverse alphabetical.
* Output to `months_sorted.txt`.

---

**10. Print Shop**

* Format `employees.csv` into 2-column side-by-side pages, 30 lines per page, 60 characters wide, omitting headers/footers.

---
Perfect. Let’s bolt on a few more scenarios so every single command option and variation gets its turn on stage. I’ll continue in the same “story-task” style, with sample files reused.

---

**11. Pattern Collector**

* Use `grep` with `-f patterns.txt` to find all employees belonging to Engineering or Sales in `employees.csv`.
* Add another pattern inline with `-e` for “HR”.
* Show line numbers, then count total matches.

---

**12. Directory Diver**

* Make a copy of `employees.csv` into a subdirectory named `data/`.
* Run `grep -r -d skip` for the string “Engineering” starting at the current directory.
* Verify the search ignores the directory when told.

---

**13. AWK Trickery**

* Create a tiny AWK program file called `fields.awk` that prints only the second and third fields from `employees.csv`.
* Run it with `awk -F, -f fields.awk employees.csv`.
* Then rerun, but assign a variable using `-v min=60000` so it only prints rows with salary above that value.

---

**14. Character Surgeon**

* From `employees.csv`, extract only the first 10 characters of each line using `cut -c 1-10`.
* Suppress any lines that don’t contain the delimiter `,` using `cut -s -d, -f2`.

---

**15. Sorting Arena**

* From `employees.csv`, sort entries by Department (field 3) using `-t, -k3`.
* Check if the result is sorted using `-c`.
* Then resort by numeric salary (field 4) ascending, and output to `salary_sorted.txt` using `-o`.

---

**16. Log Watcher**

* Use `tail -n 3 projects.txt` to view the last three entries.
* Then pipe those lines into `wc -w` to count total words.

---

**17. Printf Circus**

* Print out a string, an integer, a float, an octal, a lowercase hex, uppercase hex, a single character, and a literal `%` symbol using `printf`.
* Format them so each appears labeled on its own line.

---

**18. Tee Party**

* Run a pipeline that extracts names from `employees.csv`, converts them to uppercase with `awk`, and then writes to both `names.txt` and screen.
* Append the results a second time with `tee -a names.txt`.

---
---
---

## Answers

### Scenario 1: Salary Sleuthing

```bash
awk -F, '$3=="Engineering" {print $2,$4}' employees.csv | sort -nrk2 | tee eng_salaries.txt
```

---

### Scenario 2: Who’s Not Invited

```bash
grep -vi "HR" employees.csv | head -n 3
```

---

### Scenario 3: Regex Hunter

```bash
grep -Ern -c "^(ProjectX|ProjectY)" .
```

(or split: `grep -Ern "^(ProjectX|ProjectY)" . ; grep -Erc "^(ProjectX|ProjectY)" .`)

---

### Scenario 4: Trim & Cut

```bash
cut -d, -f3 employees.csv | sort -uf | wc -l
```

---

### Scenario 5: Format Wizardry

```bash
awk -F, 'NR>1 {printf "ID: %d (0x%X), Salary: %d\n",$1,$1,$4}' employees.csv
```

---

### Scenario 6: Pager Quest

```bash
less projects.txt
# inside less: /ProjectY → n → q

more projects.txt
# inside more: space → enter → q
```

---

### Scenario 7: Sed Surgery

```bash
sed -n -e 's/Engineering/R&D/g' -e 's/HR/PeopleOps/gp' employees.csv
```

---

### Scenario 8: Wordsmithing

```bash
wc -w -l -m -c -L projects.txt
```

---

### Scenario 9: Chrono-Sorting

```bash
sort -M months.txt -o months_sorted.txt
sort -r months.txt -o months_sorted.txt
```

---

### Scenario 10: Print Shop

```bash
pr -m -l30 -w60 -t employees.csv employees.csv
```

---

### Scenario 11: Pattern Collector

```bash
grep -n -f patterns.txt -e "HR" employees.csv
grep -c -f patterns.txt -e "HR" employees.csv
```

---

### Scenario 12: Directory Diver

```bash
mkdir data
cp employees.csv data/
grep -r -d skip "Engineering" .
```

---

### Scenario 13: AWK Trickery

**fields.awk**

```awk
{ print $2, $3 }
```

Run:

```bash
awk -F, -f fields.awk employees.csv
awk -F, -v min=60000 -f fields.awk 'NR>1 && $4 > min {print $2, $3}' employees.csv
```

---

### Scenario 14: Character Surgeon

```bash
cut -c 1-10 employees.csv
cut -s -d, -f2 employees.csv
```

---

### Scenario 15: Sorting Arena

```bash
sort -t, -k3 employees.csv
sort -c -t, -k3 employees.csv
sort -t, -k4n employees.csv -o salary_sorted.txt
```

---

### Scenario 16: Log Watcher

```bash
tail -n 3 projects.txt | wc -w
```

---

### Scenario 17: Printf Circus

```bash
printf "String: %s\nInteger: %d\nFloat: %f\nOctal: %o\nHex lower: %x\nHex upper: %X\nChar: %c\nLiteral: %%\n" "Hello" 42 3.14 42 42 42 65
```

---

### Scenario 18: Tee Party

```bash
awk -F, 'NR>1 {print toupper($2)}' employees.csv | tee names.txt | tee -a names.txt
```

---
