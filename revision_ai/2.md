# 🔹 Scenarios

## **Scenario 1: System Log Investigation**

You are a sysadmin at a hosting company. A customer reports intermittent service outages. You decide to analyze `/var/log/syslog` and `/var/log/auth.log`.

1. Find how many times the word `error` (case-insensitive) appears in both logs.
2. Show the line numbers of all failed login attempts, but exclude lines that contain `pam_unix`.
3. Search for both `timeout` and `disk full` errors in the logs.
4. You want to save all patterns to a file (`patterns.txt`) and then search using that file.
5. Count how many matches were found.
6. Extract only the usernames (4th field, space-delimited) from the failed login lines.
7. Replace all IPs of the form `192.168.*.*` with `[REDACTED_IP]` in a copy of the logs.
8. Display the last 15 lines of the modified log and then continuously monitor for new entries.

---

## **Scenario 2: Developer Build Report**

Your development team compiles nightly build reports (`build_report.txt`). The file contains build IDs, developer names, dates, and status. Example file:

```
1001 John 2021-06-01 SUCCESS
1002 Alice 2021-06-01 FAIL
1003 Bob 2021-06-02 SUCCESS
1004 Alice 2021-06-03 SUCCESS
1005 John 2021-06-03 FAIL
```

1. Show only the first 3 lines of the report.
2. Extract only the developer names.
3. Display only the last 2 lines of the file.
4. Check if the file is sorted by build ID.
5. Sort by developer name (case-insensitive).
6. Sort by date (3rd field) in reverse order and save to `sorted_report.txt`.
7. Display the number of lines, words, bytes, and maximum line length in the report.
8. Count how many builds were successful.
9. Count how many builds failed, using inverted search.
10. Merge two reports (`build_report.txt` and `build_report_old.txt`) side by side, separated by a `|`.

---

## **Scenario 3: Finance Data Analysis**

You are analyzing a CSV file `sales.csv`:

```
ID,Month,Amount,Region
1,Jan,500,East
2,Feb,300,West
3,Jan,700,East
4,Mar,200,North
5,Feb,1000,South
```

1. Print the header only.
2. Extract only the 2nd and 3rd fields (Month and Amount).
3. Sort the file by Amount (numeric).
4. Sort by Month in calendar order.
5. Show only unique regions.
6. Extract only the bytes representing IDs from the file.
7. Calculate total sales amount using a text processing tool.
8. Assign a variable inside the tool to calculate commission = 10% of sales.
9. Store the result in a file while also displaying it on screen.
10. Use formatted printing to show the total as an integer, float, and hex.

---

## **Scenario 4: Research Paper Draft**

You have a text file `draft.txt` with 200 lines.

1. Show the first 20 lines.
2. Show the last 10 lines.
3. Browse through the file one page at a time, then quit.
4. Browse again, but search forward for the word `theory`.
5. Search backward for the word `method`.
6. Display next and previous matches.
7. Replace all instances of `et al.` with `and colleagues`.
8. Save this new version without headers/footers.
9. Display with page length 40, width 80, and line numbers.

---

## **Scenario 5: Admin Utility Script**

You are writing a small script for daily reporting.

1. Format and print: `"Report ID: 25, Total Sales: 1050.5, HexID: 19, Octal: 31"`.
2. Display the character `A` using formatting.
3. Display a literal `%` in the output.
4. Append the daily total to a log file without overwriting.
5. Combine standard output of a command while saving it to a file at the same time.

---

## **Scenario 6: Recursive Config Search**

You need to audit configuration files under `/etc/`.

1. Search recursively for all files mentioning `PermitRootLogin`.
2. Treat directories explicitly instead of skipping them.
3. Save matches into `rootlogin.txt`.

---

## **Scenario 7: Compact File Editing**

You have `notes.txt`:

```
alpha
beta
gamma
```

1. Replace `beta` with `BETA`, but show only the changed lines.
2. Apply two edits in one pass: replace `alpha` with `A` and `gamma` with `G`.

---

## **Scenario 8: awk script file**

Create an awk script `sum.awk` with:

```awk
{sum+=$3} END{print "Sum:", sum}
```

1. Run it on `sales.csv` by loading from the file.

---

## **Scenario 9: Employee List**

File `employees.txt`:

```
1:Alice:Manager
2:Bob:Engineer
3:Charlie:Analyst
4:Diana:Clerk
```

1. Extract only the first 5 characters of each line.
2. Extract only character ranges 3–8.
3. Ignore lines without the delimiter `:`.

---

## **Scenario 10: Character & String Counts**

Check file size characteristics of `draft.txt`:

1. Count characters.
2. Print words, lines, bytes, and max length too (full report).

---

## **Scenario 11: Detailed Navigation**

Open `draft.txt` with a pager.

1. Move down line by line.
2. Move up line by line.

---

## **Scenario 12: Formatted Print Expansion**

1. Print a string like `"Hello Linux"` using string formatting.
2. Print the hex value of 255 in uppercase.

---

## **Scenario 13: String Separator Print**

You have two small files:

`file1.txt`

```
A
B
```

`file2.txt`

```
C
D
```

Format them side by side, but separate fields using the string `||`.

---

# 🔹 Answer Keys

## **Scenario 1: System Log Investigation**

```bash
grep -i -c "error" /var/log/syslog /var/log/auth.log
grep -n "Failed" /var/log/auth.log | grep -v "pam_unix"
grep -E -e "timeout" -e "disk full" /var/log/syslog
grep -f patterns.txt /var/log/syslog
grep -c -f patterns.txt /var/log/syslog
awk -F' ' '/Failed/ {print $4}' /var/log/auth.log
sed 's/192\.168\.[0-9]\{1,3\}\.[0-9]\{1,3\}/[REDACTED_IP]/g' /var/log/syslog > syslog_redacted.log
tail -n 15 syslog_redacted.log
tail -n 0 -f syslog_redacted.log
```

---

## **Scenario 2: Developer Build Report**

```bash
head -n 3 build_report.txt
cut -d' ' -f2 build_report.txt
tail -n 2 build_report.txt
sort -c -k1,1 build_report.txt
sort -f -k2,2 build_report.txt
sort -r -k3,3 build_report.txt -o sorted_report.txt
wc -lwmcL build_report.txt
grep -c "SUCCESS" build_report.txt
grep -v "SUCCESS" build_report.txt | grep -c "FAIL"
pr -m -s'|' build_report.txt build_report_old.txt
```

---

## **Scenario 3: Finance Data Analysis**

```bash
head -n 1 sales.csv
cut -d',' -f2,3 sales.csv
sort -n -t',' -k3,3 sales.csv
sort -M -t',' -k2,2 sales.csv
cut -d',' -f4 sales.csv | sort -u
cut -b1 sales.csv
awk -F',' '{sum+=$3} END{print sum}' sales.csv
awk -F',' -v rate=0.1 '{sum+=$3*rate} END{print sum}' sales.csv
awk -F',' '{sum+=$3} END{print sum}' sales.csv | tee totals.txt
printf "Total (int): %d\nTotal (float): %f\nTotal (hex): %x\n" 2500 2500 2500
```

---

## **Scenario 4: Research Paper Draft**

```bash
head -n 20 draft.txt
tail -n 10 draft.txt
more draft.txt
less draft.txt   # then press `/theory`
less draft.txt   # then press `?method`
less draft.txt   # then press `n` and `N`
sed 's/et al\./and colleagues/g' draft.txt > draft2.txt
pr -t draft2.txt
pr -l40 -w80 -n draft2.txt
```

---

## **Scenario 5: Admin Utility Script**

```bash
printf "Report ID: %d, Total Sales: %f, HexID: %x, Octal: %o\n" 25 1050.5 25 25
printf "Char: %c\n" A
printf "Literal %% symbol\n"
echo "Daily Total: 1050" | tee -a daily.log
ls -l | tee output.txt
```

---

## **Scenario 6**

```bash
grep -r "PermitRootLogin" /etc/
grep -d recurse "PermitRootLogin" /etc/
grep -r "PermitRootLogin" /etc/ > rootlogin.txt
```

---

## **Scenario 7**

```bash
sed -n 's/beta/BETA/p' notes.txt
sed -e 's/alpha/A/' -e 's/gamma/G/' notes.txt
```

---

## **Scenario 8**

```bash
awk -f sum.awk sales.csv
```

---

## **Scenario 9**

```bash
cut -c1-5 employees.txt
cut -c3-8 employees.txt
cut -d':' -s -f2 employees.txt
```

---

## **Scenario 10**

```bash
wc -m draft.txt
wc -lwmcL draft.txt
```

---

## **Scenario 11**

```bash
less draft.txt   # then press `j` to move down, `k` to move up
```

---

## **Scenario 12**

```bash
printf "Message: %s\n" "Hello Linux"
printf "Uppercase Hex: %X\n" 255
```

---

## **Scenario 13**

```bash
pr -m -S'||' file1.txt file2.txt
```

---
