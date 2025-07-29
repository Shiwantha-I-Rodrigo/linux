# SEARCHING AND ANALYSING TEXT

## cut

> a record is a line of text ending in a newline character.\
> a delimeter is used to identify "fields" within a record.

    -c 1-5,7    --characters 1-5,7      show only characters 1-5 and 7.
    -b 1-5,7    --bytes 1-5,7           show only bytes 1-5 and 7.
    -d ","      --delimeter ","         designate the delimeter.
    -f 1-5,7    --fields 1-5,7          show only fields 1-5,7.
    -s          --only-delimeted        only display records that contain the delimeter.
    -z          --zero-terminated       designate end-line character as ASCII NUL.

`$ cut -d ":" -f 1 /etc/passwd`

## grep

    -c      --count             display the pattern match count.
    -d act  --directories=act   when the file is a dir, set an action (read/skip/recurse).
    -E      --extended-regexp   search pattern as an EXTENDED regex expression.
    -i      --ignore-case       ignore case pattern and text.
    -R -r   --recursive         recursive search.
    -v      --invert-match      display only non matching records.

`$ grep root.*bash /etc/passwd` \
*search for records with word "root" followed by "bash", ".\*" denotes any number of other characters in between.*

ie. **-v** can be used to audit whether there are records that does not match the correct format of a .conf file.

`$ grep -E "^root|^dbus" /etc/passwd`\
`$ egrep "^root|^dbus" /etc/passwd`\
*search for records with "root" or "dbus" using extended regular expression pattern*\
the `egrep` tool can also be used when using extended regular expressions.

## sort

    -c          --check         check if the file is in order, produce no output if file is in order.
    -f          --ignore-case   consider lowercase letters as uppercase.
    -k n1 [,n2] --key n1 [,n2]  sort using the field n1 and optionally n2.
    -M          --month-sort    sort by month, must be three letter abbrviations (APR,MAY.JUN,JUL).
    -n          --numeric-sort  must be used if the sort field are numbers.
    -o sorted   --output=sorted create a new file with sorted data.
    -r          --reverse       reverse order.

`$ sort -n numbers.txt`

## printf

    $ printf "hello world"
    hello world $

    $ printf "%s\n" "hello world"
    hello world
    $

    $ printf "%s\n" "hello world" "welcome"
    hello world
    welcome
    $

    $ printf "%.1f\n" 255 0xff
    255.0
    255.0
    $

    $ printf "%03d\t" 25 0xff
    025    255 $


    %c  character
    %d  decimal integer
    %f  float
    %s  string

    \f  form feed
    \n  new line
    \r  carriage return
    \t  horizontal tab

> **echo** Vs **printf**\
> echo can be problematic when it comes to escape characters and how it displays data.\
> printf is generally more consistent and has better formatting options.

    $ for i in $( seq 1 4 ); do printf "%03d\t" "$i"; done
    001    002    003    004 $

## wc

word count

    -l  --lines             line count.
    -w  --words             word count.
    -c  --bytes             bytes count.
    -m  --chars             char count.
    -L  --max-line-length   byte count of the longest line.

.

    $ wc log.txt
        5   9   45  log.txt

without any options the default result is **[lines] [words] [bytes] [file name]**.

# REDIRECTING INPUT / OUTPUT

Linux treats every object as a file. including the output process displaying text on screen.

when a file is opened a file descriptor is assigned to the opened file. it's a non negative integer.

file descriptors
+ STDIN = 0
+ STDOUT = 1
+ STDERR = 2

## STDOUT

by default STDOUT directs output to the current terminal ( **/dev/tty** file ).\
but it can be redirected with redirection operator ( > )

`$ echo "hello world" > file1`

> the ( > ) operator will overwrite the existing data !\
> use ( >> ) to append data.

## STDERR

**2>** is used to redierect error outputs.

`$ grep -d skip hosts: /etc/* 2> err.txt`

> to redirect both STDOUT and STDERR use any of the following formats.\
> **&>**\
> **>&**\
> **> outputfile 2>&1**

to throw away error messages redirect to /dev/null\
`$ grep -d skip hosts: /etc/* 2> /dev/null`

## STDIN

using a file (automatic) instead of the keyboard (manual) to direct input to a process.

    $ tr " " "," < numbers.txt
        2,4,6,7,8,9

replace empty spaces in numbers input with ",". ! the actual file is not altered. only the output.

## HEREDOCS

redirecting multiple items in to a command.

    $ sort << EOF
    > saman
    > kamal
    > rudy
    > EOF
    kamal
    rudy
    saman
    $

when the enter key is pressed after entering the command, a prompt will apear asking for data,\
and will continue to ask for new data, untill the "ending keyword" is eneterd.\
**EOF** can be replaced with any multichar text that does not appear in the data.

Heredocs accepts **command expansion**.

    cat << EOF
    > $(echo Hello)
    > $(whoami)
    > EOF
    Hello
    saman
    $

use "" to ignore command expansion.

    cat << "EOF"

this will consider the data entered following the command as string.

## PIPING

`$ grep /bin/bash /etc/passwd | cut -d ":" -f 1 | sort | less`

1. the `grep` command lists user accounts that use "bash" as default account shell.
2. the  `cut` command extract the first field (username) from each record.
3. the `sort` command sorts the list alphabetically.
4. the `less` command display the results.

> the "|" operator directs the previous commands output to the proceding command as input.

instead of `less` the `tee` command can be used to view the results while saving them as well.\
it is usefull when installing software while saving a log and viewing what is happening in real time.

# COMMAND LINE CREATION

## xargs

When piping without **xargs**, the actual data is fed into the next command.\
when piping with **xargs**, the actual data is viewed as a parameter to the next command.

    $ cat file
    hello world

    $ ls | grep hello
    $

    $ ls | xargs grep hello
    hello world
    $

> the first example is equalent to **grep "hello" <<< "file"** where file is data.\
> the second is **grep "hello" file** where file is an argument.

## shell expansion

`$ rm $(find tmp -size 0)`

## backticks

``$ rm `find tmp -size 0` ``

## brace expansion

`$ rm /tmp/emptyfile{1,2,3}.txt`\
use one line instead of 3 lines.

# EDITING TEXT FILES

## nano

    Ctrl + O        Save A File
    Ctrl + X        Exit file, with prompt
    Alt + U         Undo an action
    Ctrl + Space    Move one word forward
    Alt + Space     Move one word backward
    Ctrl + V        Move to next page
    Ctrl + Y        Return to the preceding page

## vim

vim has 3 modes,
1. command mode\
    normal mode where shortcut keys are enabled.
2. insert mode\
    edit mode where very few shortcuts are enabled, used for inserting text.
3. ex mode\
    colon command mode where command expressions are given. ( ie **:wq** ). 

**Command mode**

    h       move left
    l       move right
    j       move down
    k       move up
    w       move one word forward
    e       move to end of current word
    b       move back one word
    ^       move to begining of line
    $       move to end of line
    gg      move to first line
    G       move to last line
    nG      move to n line
    Ctrl+B  scroll up one screen
    Ctrl+F  scroll down one screen
    Ctrl+U  scroll up half screen
    Ctrl+D  scroll down half screen
    Ctrl+Y  scroll up one line
    Ctrl+E  scroll down one line

**Ex mode**

    :x              write buffer to file, quit
    :wq             write buffer to file, quit
    :wq!            write buffer to file, quit (override protection)
    :w              write buffer to file
    :w!             write buffer to file (override protection)
    :q              quit
    :q!             quit (override protection)
    :!*command*     execute shell command
    :r!*command*    execute shell command include results in buffer area.
    :*file*         include file content in buffer area.

## Stream Editors

### sed

    $ cat file
        tik tok tik tok
        tik tok
        tik

    # (p) print lines in addition to default print behavior.
    $ sed 'p' file
        tik tok tik tok
        tik tok tik tok
        tik tok
        tik tok
        tik
        tik

    # (-n) to suppress default print behavior.
    $ sed -n 'p' file
        tik tok tik tok
        tik tok
        tik

    # (1,2p) print lines 1 to 2.
    $ sed -n '1,2p' file
        tik tok tik tok
        tik tok

    # (1~2p) print every other line.
    $ sed -n '1~2p' file
        tik tok tik tok
        tik

    # (1~2d) delete every other line.
    $ sed '1~2d' file
        tik tok

    # (s) substitute first occurence.
    $ sed 's/tok/tik/' file
        tik tik tik tok
        tik tik
        tik

    # (g) sustitute all.
    $ sed 's/tok/tik/g' file
        tik tik tik tik
        tik tik 
        tik

    # ( -e / ; ) multiple expressions.
    $ sed -e 's/tok/tik/g' -e 's/tik/tok/g' file
    $ sed 's/tok/tik/g ; s/tik/tok/g' file

        tok tok tok tok
        tok tok
        tok

    # (-r) regex.
    $ sed -r 's/^ti\w+/Tak/g' file
        tak tok tik tok
        tak tok
        tak

.

    $ cat script.sed
        s/tik/tok
    
    # (-f) use script file. 
    $ sed -f script.sed mytext
        tok tok tik tok
        tok tok
        tok

### gawk

gawk is the GNU implementation of the Awk programming language.

> **awk** program just softlinks to **gawk**

gawk can,
+ define variables and store data.
+ use arithmatic and string operators.
+ use programming structures (ie.loops).
+ create formatted reports from data.

Basic Usage,

    user input  : $ awk 'pattern { action }'
    file        : $ awk 'pattern { action }' file
    piped input : $ ls | awk 'pattern { action }'

    -F  --field-seperator       specify field seperator     -F: 
    -f  --file=                 script file                 -f script.gawk file
    -v                          pass variable               -v a="hello" -v b="$0"

Built-In Variables

    NR  : Keeps a running count of the number of input lines
    NF  : Keeps a count of the number of fields in the current input record
    FS  : Holds the field separator character
    $0  : whole record.
    $1  : 1st field.
    $n  : nth field.

> match **every record** is the default pattern.\
> **print $0** is the default action.\
> one block can be ommited but not both.

PATTERN BLOCK

    # print any record with 'tak' in it.
    $ awk '/tak/ { print $0 }' file
    tik tok tak
    tok tak tik
    ...

    # print any record with 'tak' or 'tuk' in it.
    $ awk '/tak|tuk/ { print $0 }' file
    tik tok tuk
    tok tak tek
    ...

    # print any record with the first field longer than 2 characters.
    $ awk 'length($1) > 2 { print $0 }' file
    tik tok tak
    tok tak tik
    ...

    # print any record with a value greater than or equal to 80 in 2nd field.
    $ awk '$2 >= 80 { print $0 }
    timmy 86 pass
    cammi 80 pass
    ken   96 pass
    ...

OPERATORS

    &&  : AND   /a/ && /b/
    ||  : OR    /a/ || /b/
    !   : NOT   !/a/

    x < y : x <= y : x > y : x >= y	: x == y : x != y
    x ~ y	True if the string x matches the regexp denoted by y 
    x !~ y	True if the string x does not match the regexp denoted by y

BEGIN and END

    # the BEGIN block executes only once and before all other rules.
    # the END block executes only once after program read all the input reccords.

    awk 'BEGIN { print "records with a vowel count" ; } /a|e|i|o|u/ { counter+=1 ; } END { printf "%s\n", counter ; }' file

ACTION BLOCK

    # if the 3rd field is 'tak', replace the first field with 'tuk' and print the record.
    $ awk '{if ($3=="tak") {$1="tuk"; print $0}}' file
    tuk tok tak
    ...

> when the program is becomming too complex put it in a file.

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
