# SEARCHING AND ANALYSING TEXT

**`cut `**`[option] [file+++]`\
--> extract sections from each line of an input.

|Option     |                       |Description|
|-          |-                      |-|
|-b 1-5,7   |--bytes 1-5,7          |show bytes 1-5 and 7|
|-c 1-5,7   |--characters 1-5,7     |show characters 1-5 and 7|
|-d ","     |--delimeter ","        |designate the delimeter <br> delimeter is used to seperate "fields" within a record|
|-f 1-5,7   |--fields 1-5,7         |show fields 1-5,7|
|-s         |--only-delimeted       |only display records that contain the delimeter|
|-z         |--zero-terminated      |designate end-line character as ASCII NUL|

---

**`grep `**`[option] [pattern] [file+++]`\
--> searches lines of text for patterns using regular expressions.

|Option     |                       |Description|
|-          |-                      |-|
|-c         | --count               |print only count of matching lines|
|-v         | --invert-match        |invert match|
|-i         | --ignore-case         |case-insensitive match|
|-d act     | --directories=act     |set an action for directories (read/skip/recurse)|
|-R / -r    | --recursive           |recursive search|
|-E         | --extended-regexp     |search pattern is EXTENDED regex|
|-e         | --regexp=             |use multiple patterns <br> `$ grep -e 'one' -e 'two' file1`|
|-f         | --file=               |read patterns from a file, one pattern per line|

> **!** `egrep` (depricated) = `grep -E`

---

**`sort `**`[option] [file+++]`\
--> sort lines of text.

|Option         |                       |Description|
|-              |-                      |-|
| -n            | --numeric-sort        |sort numerically|
| -r            | --reverse             |reverse order|
| -c            | --check               |check if the file is sorted, print only if unsorted|
| -f            | --ignore-case         |consider lowercase letters as uppercase|
| -k n1 [,n2]   | --key n1 [,n2]        |sort using the field n1 and optionally n2|
| -M            | --month-sort          |sort by month, must be three letter abbrviations (APR,MAY.JUN,JUL)|
| -t            | --field-separator=    |designate the delimeter|
| -o sorted     | --output=             |create a new file with sorted data|
| -u            | --unique              |remove duplicate lines|

---

**`printf `**`[format] [argument+++]`\
--> format and print text.

> **!** **printf** is generally more consistent with escape chars and has better formatting options than **echo**.\
> **!** Unlike **echo**, **printf** does not add a newline at the end.

|Specifier  |Description                    |Output|
|-          |-                              |-|
| %s        | String                        |"%s" "hello" --> `$ hello $`|
| %d        | Integer (decimal)             |"%d" 0xff --> `$ 255 $`|
| %f        | Floating-point number         |"%f" 42.5 --> `$ 42.500000 $`|
| %x        | Hexadecimal (lowercase)       |"%x" 255 --> `$ ff $`|
| %X        | Hexadecimal (uppercase)       |"%X" 255 --> `$ FF $`|
| %o        | Octal                         |"%o" 8 --> `$ 10 $`|
| %c        | ASCII character               |"%c" 65 --> `$ A $`|
| %%        | Literal percent sign          |"%%" --> `$ % $`|

|Format         |Description                    |Example|
|-              |-                              |-|
|% {x} [d/f]    |x digits + right aligned       | `.........1` |
|% 0{x}[d/f]    |x digits + pre zero padding    | `0000000001` / `001.000000` |
|% .{x}[d]      |x digits + pre zero padding    | `0000000001` |
|% .{x}[f]      |exact number of decimals       | `1.00000000` |

> **!** formats can be combined  `printf "%8.4d" 2` --> `....0002`\
> **!** the default is 6 decimal points.\
> **!** in case of conflict the higher number is prioratized `printf "%4.8d" 2` --> `00000002`

    $ for i in $( seq 1 10 ); do printf "%03d - %o \n" $i $i ; done
    001 - 1 
    002 - 2 
    ...
    007 - 7 
    008 - 10 
    009 - 11 
    010 - 12
    $

---

**`wc `**`[option] [file+++]`\
--> count lines, words, characters, bytes, etc in a file or input.

|Option         |                       |Description|
|-              |-                      |-|
|-l             |--lines                |line count|
|-w             |--words                |word count|
|-c             |--bytes                |bytes count|
|-m             |--chars                |char count|
|-L             |--max-line-length      |byte count of the longest line|

> **!** `$ wc log.txt` --> `5   9   45  log.txt` --> **[ lines ] [ words ] [ bytes ] [ file name ]**

---

**`tee `**`[option] [file+++]`\
--> write to both standard output and one or more files simultaneously.

    ls -l | tee file1 file2

>**!** it is usefull when installing software while saving a log and viewing what is happening in real time.

---

## REDIRECTING INPUT / OUTPUT

Linux treats every object as a file. including the output process displaying text on screen.\
File descriptor is a non-negative integer that the OS uses to keep track of open files or I/O streams.

file descriptors
+ STDIN = 0
+ STDOUT = 1
+ STDERR = 2

by default output is directed to the current terminal ( **/dev/tty** file ).\
but it can be redirected with a re-direction operator.

|Descriptor     |Description|
|-              |-|
|STDOUT         |`$ echo "hello world" > file1` ( redirect output ) <br> **>** --> Overwrite <br> **>>** --> Append|
|STDERR         |`$ grep -d skip hosts: /etc/* 2> err.txt` ( redirect error messages ) <br> **2>** --> Overwrite <br> **&>** --> redirect both output and errors <br> **>&**  --> redirect both output and errors <br> **> file 2>&1**  --> redirect both output and errors <br> **2> /dev/null** --> throw away error messages|
|STDIN          |`$ tr " " "," < file` ( redirect as input ) <br> **<** --> Input|
|HEREDOC        |**<<** --> HereDOC <br> `$ sort << EOF` (redirecting multiple input items in to a command)|

when the enter key is pressed after entering the command, a prompt will apear asking for data\
and will continue to ask for new data, untill the "ending keyword specified" is eneterd.

    $ sort << EOF
    > saman
    > kamal
    > rudy
    > EOF
    kamal
    rudy
    saman
    $

>**!** **EOF** can be replaced with any multichar text that does not appear in the data.\
>**!** **> $(whoami)** : Heredocs accepts **command expansion**.\
>**!** **cat << "EOF"** : use "" to ignore command expansion.
 
 ---

## PIPING

`$ grep /bin/bash /etc/passwd | cut -d ":" -f 1 | sort | less`

1. the `grep` command lists user accounts that use "bash" as default account shell.
2. the  `cut` command extract the first field (username) from each record.
3. the `sort` command sorts the list alphabetically.
4. the `less` command display the results.

> **!** the " | " operator directs the previous commands output to the proceding command as input.

## COMMAND LINE CREATION

### xargs
--> pipe results from previous command as **arguments** to the next command.

    $ cat file
    hello world

    $ ls | grep hello
    $

    $ ls | xargs grep hello
    hello world
    $

> the first example is equalent to **grep "hello" <<< "file"** where the name 'file' is the data.\
> the second is **grep "hello" file** where file is an argument and the content of the file is the data.

---

### shell expansion

`$ rm $(find tmp -size 0)`

---

### backticks

``$ rm `find tmp -size 0` ``

---

### brace expansion

`$ rm /tmp/emptyfile{1,2,3}.txt`\
use one line instead of 3 lines.

## EDITING TEXT FILES

**`nano `**`[option] [file+++]`\

| Shortcut          | Action|
|-                  |-|
| Ctrl + O          | Save a File|
| Ctrl + X          | Exit file, with prompt|
| Alt + U           | Undo an action|
| Ctrl + Space      | Move one word forward|
| Alt + Space       | Move one word backward|
| Ctrl + V          | Move to next page|
| Ctrl + Y          | Return to the preceding page|

---

**`vim `**`[option] [file+++]`\

vim has 3 modes,
1. command mode : normal mode where shortcut keys are enabled.
2. insert mode : edit mode where very few shortcuts are enabled, used for inserting text.
3. ex mode : colon command mode where command expressions are given. ( ie **:wq** ). 

**Command mode**

| Shortcut  | Action  |
|-          |-|
| h         | Move left|
| l         | Move right|
| j         | Move down|
| k         | Move up |
| w         | Move one word forward|
| e         | Move to end of current word|
| b         | Move back one word|
| ^         | Move to beginning of line|
| $         | Move to end of line|
| gg        | Move to first line|
| G         | Move to last line|
| nG        | Move to n-th line|
| Ctrl + B  | Scroll up one screen|
| Ctrl + F  | Scroll down one screen|
| Ctrl + U  | Scroll up half screen|
| Ctrl + D  | Scroll down half screen|
| Ctrl + Y  | Scroll up one line|
| Ctrl + E  | Scroll down one line|

**Ex mode**

| Command           | Action|
|-                  |-|
| `:x`              | Write buffer to file, quit|
| `:wq`             | Write buffer to file, quit|
| `:wq!`            | Write buffer to file, quit (override protection)|
| `:w`              | Write buffer to file|
| `:w!`             | Write buffer to file (override protection)|
| `:q`              | Quit|
| `:q!`             | Quit (override protection)|
| `:!*command*`     | Execute shell command|
| `:r!*command*`    | Execute shell command, include results in buffer|
| `:*file*`         | Include file content in buffer area|

---

### Stream Editors
--> parsing and transforming text in a streaming fashion (without loading the entire file into memory).

**`sed `**`[option] ['pattern'] [file+++]`

    $ cat file
    tik tok tik
    tik tok
    tik

|**Syntax**                             |**Function**                                           |**Output**|
|-                                      |-                                                      |-|
|`sed 'p' file`                         |Print matched lines **in addition to** default output  |tik tok tik <br> tik tok tik <br> tik tok <br> tik tok <br> tik <br> tik|
|`sed -n 'p' file`                      |Suppress default output                                |tik tok tik <br> tik tok <br> tik|
|`sed -n '1,2p' file`                   |Print lines 1 to 2                                     |tik tok tik <br> tik tok|
|`sed -n '1~2p' file`                   |Print every other line starting from line 1            |tik tok tik <br> tik|
|`sed '1~2d' file`                      |Delete every other line starting from line 1           |tik tok|
|`sed 's/tik/tuk/' file`                |Substitute first match on each line                    |tuk tok tik <br> tuk tok <br> tuk|
|`sed 's/tik/tuk/g' file`               |Substitute **all** occurrences of pattern on each line |tuk tok tuk <br> tuk tok <br> tuk|
|`sed -e 's/tik/tuk/g' -e 's/tuk/tek/'` <br> `sed 's/tik/tuk/g ; s/tuk/tek/' file`|Specify multiple `sed` expressions|tek tok tuk <br> tek tok <br> tek|
|`sed -r 's/^ti\w+/tuk/' file`          |Use **extended regex** (ERE)                           |tuk tok tik <br> tuk tok <br> tuk|

**sed script**

    $ cat script.sed
        s/tik/tok
    
    # (-f) use script file. 
    $ sed -f script.sed mytext
        tok tok tik tok
        tok tok
        tok

---
---
---

**`gawk `**`[option] ['pattern] [{action}'] [file]`

> **!** gawk is the GNU implementation of the Awk programming language.\
> **!** **awk** command just softlinks to **gawk**.

+ define variables and store data.
+ use arithmatic and string operators.
+ use programming structures (ie.loops).
+ create formatted reports from data.

|Option     |                   |Description|
|-          |-                  |-|
|-F         |--field-seperator  |`-F:` --> specify field seperator|
|-f         |--file=            |`-f script.gawk` --> script file|
|-v         |                   |`-v a="hello"` --> pass variable|

Basic Usage,

    user input  : $ awk 'pattern { action }'
    file        : $ awk 'pattern { action }' file
    piped input : $ ls | awk 'pattern { action }'

Built-In Variables,

    NR  : Keeps a running count of the number of input lines
    NF  : Keeps a count of the number of fields in the current input record
    FS  : Holds the field separator character
    $0  : whole record.
    $1  : 1st field.
    $n  : nth field.

> **!** match **every record** is the default pattern.\
> **!** **print $0** is the default action.

    # From PATTERN and ACTION, one block can be ommited but not both
    $ awk '/s/ { print $0 }' file
    $ awk '/s/' file
    $ awk '{print $0}'

---

**PATTERN BLOCK**

|**Pattern**            | **Description**|
|-                      |-|
| `/tak/`               | Matches any line containing the string "tak"|
| `/tak\|tuk/`          | Matches any line containing either "tak" or "tuk" using regex alternation|
| `length($1) > 2`      | Checks the character length of the first field and prints if greater than 2|
| `$2 >= 80`            | Numeric comparison of the second field; prints lines where it's ≥ 80|
| `/a/ && /b/`          | AND|
| `/a/ \|\| /b/`        | OR|
| `!/a/`                | NOT|

---

**ACTION BLOCK**

| **Action**                            | **Description**|
|-                                      |-|
| `{ print $0 }`                        | Prints the entire line (default action when a line matches the pattern)|
| `{ print $1, $3 }`                    | Prints the first and third fields, separated by a space|
| `{ print NR, $0 }`                    | Prints the current record number followed by the entire line|
| `{ $1 = toupper($1); print }`         | Converts the first field to uppercase and prints the modified line|
| `{ if ($3 == "fail") { print $1 } }`  | Prints the first field only if the third field equals "fail"|
| `{ sum += $2 } END { print sum }`     | Adds the values in the second field and prints the total at the end of input <br> # the BEGIN block executes only once and before all other rules <br> # the END block executes only once after program read all the input reccords <br> `$ awk 'BEGIN { print "records with a vowel count" ; } /a\|e\|i\|o\|u/ { counter+=1 ; } END { printf "%s\n", counter ; }' file`|

**awk script**

    $ cat palindrome.gawk

        {
            if (length($0) == 1) {next}

            rev = reverse($0)

            if ($0 == rev) {
                print
                n++
            }
        }

        END {

            printf "There are %d palindromes\n", n
        }

        function reverse(word) {
            r = ""

            for (i=length(word); i!=0; i--) {
                r = r substr(word, i, 1)
            }

            return r
        }
    
    $ awk -f palindrome.gawk file
