# BASH SCRIPTING


## BASICS

- `$ date ; who` : multiple commands seperated by a **;**.
- `$ date > today.txt` : redirect output to file.
- `$ date >> days.txt` : append to file.
- `$ date 2> error.log` : file descriptor 2 for redirecting error output.

> ( file descriptors - 0 STDIN / 1 STDOUT / 2 STDERR )

- `$ ls | sort` : pipe data from previous command to next command.

> **"\>" , "\>\>" , "|"** are meta-characters, to include these in output they must be escaped with **"\\"**.


## SCRIPT FILES

    #!/bin/bash
    # display date ...
    date


- " **#!** " sign is called the shebang.
- any shell can be specified as long as it is installed on the system.
- if no shell is specified script will be run with the default shell in the **passwd** file.
- " **#** " is a comment.
- **.sh** file extention is used.
- by default linux for directies in $PATH to find the command.
- a relative or absolute path must be used to point to the script.
- " **./** " relative path can be used. 

.

    $ test.sh
    test.sh : command not found.

    $ ./test.sh
    Thu Feb 24 19:23:12 EST 2025

- always check the file permissions if the script is denied.


## ADVANCED SCRIPTING

- `echo " hi' there"` : output the string ( **"** ) is required only if the string contain any ( **'** ).
- `echo` : output empty line.
- 2 types of variables.
    + Environment Variables : system information.
    + User Variables : custom variables.
- use **$ set** to view a list of **Environment Variables**.

.

    #!/bin/bash

    # environment variable
    echo $USER

    # user variable
    message="hi there"
    echo $message

`./test.sh`

    Adam
    Hi there

- there must be no spaces between the ( **var name** ) ( **= sign** ) and ( **value** ).
- variable type is automatically set by the shell.

### ARGUMENTS

- `$ myScript.sh argument1 argument2`
- the arguments passed are referenced in the script as $1, $2, and so on.

.

    #!/bin/bash

    echo Welcome $1 ,Have a good $2

`./test.sh ADAM`

    Welcome ADAM ,Have a good

- missing parameters doesn't generate errors.

### EXIT STATUS

- the exit stutus tells the shell whether the script was succesfull.
- the " **$?** " variable holds the exit status.
- exit status holds the status of the last command executed.


## SCRIPT PROGRAMS

- command substitution allows assigning the output of a command to a variable.
- there are two syntaxes for command substitution.

.

    #!/bin/bash

    # using back-ticks
    var1=`date`

    #using $()
    var2=$(who)

    echo $var1 : $var2

- only **integer** math operations are allowed with in $[ ] brackets.
- to use **floats** a 3rd party tool must be used with command substitution.

.

    #!/bin/bash

    $var1=$(echo "scale=4; 3.4 / 5" | bc)
    echo $var1

- when using the **bc** utility for float opertaions, the **scale** variable must be set.
- **scale** var is used to set the number of decimal places for the output.
- look at **Z shell** for complicated math operations.

### LOGIC

#### IF

    if [ condition ]
    then
        commands
    fi

Built-in Condition tests

    n1  -eq  n2         n1 is equal to n2
    n1  -ge  n2         n1 is greater than or equal to n2
    n1  -gt  n2         n1 is greater than n2
    n1  -le  n2         n1 is less than or equal to n2
    n1  -lt  n2         n1 is less than n2
    n1  -ne  n2         n1 is not eqaul to n2

    str1  =  str2       str1 is same as str2
    str1  !=  str2      str1 is not the same as str2
    str1  <  str2       str1 is less than str2
    str1  >  str2       str1 is greater than str2

    -n  srt1            str1 has a length greater than 0
    -z  str1            str1 has a length of zero

    -d  file1           file1 exist and is a directory
    -e  file1           file1 exist
    -f  file1           file1 exist and is a file
    -r  file1           file1 exist and is readable
    -s  file1           file1 exist and is not empty
    -w  file1           file1 exist and is writable
    -x  file1           file1 exist and is executable
    -O  file1           file1 exist and is owned by current user
    -G  file1           file1 exist and default group is a group of current user

    file1  -nt  file2   file1 is newer than file2
    file1  -ot  file2   file1 is older than file2

example,

    if [ $1 -eq $2 ]
    then
        echo "both values are equal"
        exit
    fi

#### CASE

    case $var in
    pattern1)
        commands;;
    pattern2 | pattern3)
        commands;;
    *)
        commands;;
    esac

example,

    case $USER in
    Adam | Robert)
        echo "Welcome Admin";;
    *)
        echo "Welcome User";;
    esac

#### FOR

    for $var in $list ; do
        commands
    done

example,

    for file in $(ls) ; do
        echo $file
    done

#### WHILE

    while [ condition ] ; do
        commands
    done

example,

    var=$1
    while [ $var -gt 0 ] ; do
        echo $1 " is greater than zero"
        var=$[ $var - 1 ]
    done

#### UNTIL

    until [ condition ] ; do
     commands
    done

example,

    var=$1
    until [ $var -gt 0 ] ; do
        echo $1 " is lesser than zero"
        var=$[ $var + 1 ]
    done

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
