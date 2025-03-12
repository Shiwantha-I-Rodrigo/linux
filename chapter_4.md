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

`printf` is geared to format single lines of text. it can be combined with other commands.

`$ printf "%.2f\n" 27.3243`\
prints **27.32**

    %c  display first char
    %d  display as decimal integer
    %f  display as float
    %s  display as string
    \f  form feed
    \n  new line
    \r  carriage return
    \t  horizontal tab

## wc

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

Linux treats every object as a file. including the output process displaying text on screen.\

when a file is opened a file descriptor is assigned to the opened file. it's a non negative integer.\

file descriptors
+ STDIN = 0
+ STDOUT = 1
+ STDERR = 2

## STDOUT

by default STDOUT directs output to the current terminal ( **/dev/tty** file ).\
but it can be redirected with redirection operator ( > )

`$ echo "hello world" > file1`

> the ( > ) operator will overwrite the existing data !\

use ( >> ) to append data.

## STDERR

`$ grep -d skip hosts: /etc/* 2> err.txt`

**2>** is used to redierect error outputs.

use ( **&**> ) to redirect both STDOUT and STDERR.

to remove and throw away error messages redirect to /dev/null

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

`$ find tmp -size 0 | xargs -p /usr/bin/rm`

similar to just using the `rm` command, but ask for confirmation before deletion.

## shell expansion

`$ rm $(find tmp -size 0)`

## backticks

``$ rm `find tmp -size 0` ``

## brace expansion

`$ rm /tmp/emptyfile{1,2,3}.txt`

use one line instead of 3 lines.

# EDITING TEXT FILES

## nano

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

    $ cat mytext
        michael likes cake a lot of cake.
        ron loves cake.

    $ sed 's/cake/donut/' mytext
        michael likes donut a lot of cake.
        ron loves donut.
    
    $ sed 's/cake/donut/g' mytext
        michael likes donut a lot of donut.
        ron loves donut.
    
    $ sed -e 's/cake/donut/g : s/likes/loves/' mytext
        michael loves donut a lot of donut.
        ron loves donut.
    
use **g** to substitute all occurences on a single line.\
use **-e / --expression=** option to add multiple scripts.

    $ cat script.sed
        s/cake/donut/g
        s/like/love
    
    $ sed -f script.sed mytext
        michael loves donuts a lot of donuts.
        ron loves donuts.

**-f / --file=** option to use a file instead of typing the script.
**-r / --regexp-extended** option to add regex.

### gawk

gawk can,
+ define variables and store data.
+ use arithmatic and string operators.
+ use programming structures (ie.loops).
+ create formatted reports from data.

> **awk** program just softlinks to **gawk**

    $ gawk '{print $0}' mytext
        michael likes cake a lot of cake.
        ron loves cake.
    
    $ gawk '{print $1}' mytext
        michael
        ron

$0 = whole line\
$1 = 1st field\
$n = nth field

    $ gawk '{if ($3=="cake") {$3="donut"; print $0}}' mytext
        michael likes donut a lot of cake.
        ron loves donut.

**-F d / --field-seperator d** to specift field seperator.\
**-f file / --file=file** to specift a script file.\
**-s / --sandbox** to execute sandbox.

    $ cat script.gawk
        {if ($3=="cake")
            {$3="donut"; print $0}
        else if ($7=="cake")
            {$7="fruits"}
        }

    $ gawk -f script.gawk mytext
