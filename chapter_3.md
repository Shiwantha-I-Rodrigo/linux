# MANAGING FILES

## ls
list files and directories

    -a --all            display all including hidden
    -d --directory      show directory metadata instead of content
    -F --classify       claasify each file using indicators
    -i --inode          display files, folders with associated index numbers
    -l                  display file, folder metadata
    -R                  show folder content recursively

    -lh                 show metadata in a more human readable format

> some distributions use `ll` instead of `ls -lh`

## tree
shows a tiered structure of files and folders

## touch
create a new file

`$ touch file1 file2 file3`

## mkdir
> a folder is just a file with pointers to various files locations (inode numbers)

`$ mkdir newdir`

`$ mkdir /home/newdir`

> use -p option to create dir's recursively `$ mkdir -p newdir/newdir`.

> instead of `$ ls -F` use `$ mkdir -v` to verify the directory is created succesfully.

## cp
copy files, folders

    -a --archive        recursive copy with file attributes preserved
    -f --force          overwrite any existing files
    -i --interactive    ask before overwriting
    -n --no-clobber     dont overwrite existing files
    -R -r  --recursive  recursive copy
    -u --update         overwrite if source is newer
    -v --verbose        detailed information

`$ cp oldfile newfile`

## mv
move files folders. options are mostly identical to `cp` command, but fewer. no `-a` or `-r`.

## rsync

    -a --archive        archive mode
    -D                  retain device, special files
    -g --group          retain file group
    -h --human-readable numeric output in humanreadable
    -l --links          copy symbolic links as symbolic links
    -o --owner          retain file owner
    -p --perms          retain file permissions
       --progress       display file copy progress
    -r --recursive      recusrive copy
       --stats          display file transfer stats
    -t --times          retain file mod times
    -v --verbose        detailed command action information

> when copying over network `rsync` can be tunneled trhough `OpenSSH` to provide encryption.

> the ***speedup*** information on the stats shows the portion of files that need to be updated, for instance 
if the destination already contains 50% of the sorce files in latest versions only 50% need to be copied, 
hence the speedup will be 2.

`$ rsync oldfile newfile`

`$ rsync sourcefolder/ destfolder/`

## rm
remove files, folders

    -d --dir            delete emplty directories.
    -f --force          continue deleting even some files do not exit and do not ask before deleting files.
    -i --interactive    ask before deleting files.
    -I                  ask before deleting more than 3 files or when using -r option.
    -r -R --recursive   delete recursively.
    -v --verbose        detailed info.

`$ rm file`

`$ rm -r directory`

## rmdir

`$ rmdir directory/`

options are similar to `rm` command.


# LINKING FILES AND DIRECTORIES

the original file must exist before creating links.

## Hard Link

**one physical file (same inode number) with multiple names**.

file1 --------> [inode 19229] <-------- file2

`$ ln file1 file2`

+ deleting any one file does not delete the inode.\
+ files share the same inode number.\
+ files contain same data.\
+ must exist on same file system.

## Soft Link

**point to a file name**

file2 --------> file1 --------> [inode 272736]

`$ ln -s file1 file2`

+ deleting linkfile does not delete original.\
+ deleting original does not delete the linkfile (dangling link, deadlink).\
+ does not share the same inode number.\
+ files contain different data.\
+ can exist in different file systems.

> use `ls -ogi` to determine the number of hardlinks to a file

    $ ls -ogi
        total 23421
        23453426 -rw-rw-r-- 2 123456 Aug 24 18:09 HardLinkFileOriginal
        23453426 -rw-rw-r-- 2 123456 Aug 24 19:10 HardLink1
        17642378 -rw-rw-r-- 1 123456 Aug 24 18:20 SoftLinkFileOriginal
        65436743 lrwxrwxrwx 1     38 Aug 24 19:11 SoftLink1

        [inode] [perms] [links] [size] [modified date] [filename]

*softlinks does not increase link count or reflect original file attributes.*

*softlinks does not share the same inode number*

**total** is the number of blocks used in the disk by the files.

## Reading Files

### cat

    -n                      display line numbers.
    -b --number-nonblank    display line numbers for only for lines with text, override `-n`.
    -v --show-nonprinting   display non printing chars.
    -E --show-ends          display line endings with `$`.
    -e                      display non printing chars with line ends, same as `-vE`.
    -T --show-tabs          display tabs.
    -t                      display non printing chars with tabs, same as `-vT`.
    -s --squeeze-blank      condense consecutive blank lines.

### pr

> originally used for formating files for printing.

    -n  --columns=n     format text file to have `n` number of columns.
    -ln --length=n      format the page length.(default 66)
    -m  --merge         display multiple files as columns, truncate lines.
    -sx --seperator=x   change the column seperator to `x`.(override `-w` setting).
    -t  --omit-header   omit file header and trailer.
    -wn --width=n       format page width. (default 72).

`$ pr -2 -l25 mytext` *display text in two columns with a page containing 25 lines*.

`$ pr -m mytext yourtext` *diplay two files side by side*.

### grep

    -i --ignore-case    ignore case when searching.

`$ grep -i tommy /etc/passwd`

### head

    -n x --lines=x      display top `x` lines.

`$ head -n 4 /etc/passwd`     *display top 4 lines*\
`$ head -4 /etc/passwd`       *display top 4 lines*\
`$ head -n +4 /etc/passwd`    *display top 4 lines*\

`$ head -n -4 /etc/passwd`    *exclude last 4 lines*\
`$ head +4 /etc/passwd`       ***error***

### tail

similar to `head` command.

`$ tail -n 4 /etc/passwd`     *display last 4 lines*\
`$ tail -4 /etc/passwd`       *display last 4 lines*\
`$ tail -n -4 /etc/passwd`    *display last 4 lines*\

`$ tail -n +4 /etc/passwd`    *exclude top 3 lines*\
`$ tail +4 /etc/passwd`       *exclude top 3 lines*

## Reading File Portions

### more / less

use `more` / `less` utilities to read text formatted as pages.\
**spacebar** to forward through pages.\
**q** to exit.

`less` has more features compared to `more`.
+ doesn't read the whole file prior to displaying (faster).
+ use **up arrow** / **down arrow** to traverse the file.
+ **?** to search backward and **/** to search forward.

> `more` display file progress, while `less` does not.

## Finding Information

### file

    $ file myfile
        myfile : Bourne-Again Shell script, ASCII text executable

### stat

    $ stat myfile
        file : myfile
        size : ....     Blocks : .....
        Device : ....   Inode : .....   Links : ....
        Access : ....
        Uid : ....
        Gid : ....
        Access : ....
        Modify : ....
        Change : ....
        Birth  : ....

### diff

    -q  --brief                     if files are different just issue a simple message.
    -r  --recursive                 compare all subdirectories as well.
    -s  --report-identical-files    if files are similar just issue a message.
    -W n    --width n               display a width maximum of n chars in output.
    -y  --side-by-side              two columns.
.

    $ diff text1 text2

        1d0
        < lear

        1a3
        > amal

        2,4c2,4
        < teed
        < eu546n
        ---
        > town
        > stration

- delete the **1st** line from main file (text1).
- add after **1st** line (as the 2nd line) to main file (text1) the **3rd** line from other file (text2).
- change lines **2 and 4** of main file (text1) to match **2 and 4** of other file (text2).

> in case of need for additon or deletion, instead of **c** an **a** or **d** is shown.

### which

    $ which diff
        /usr/bin/diff

> `$PATH` en. var. is used by many linux tools such as `which`.

    $ echo $PATH
        /usr/local/bin:/usr/local/sbin:.....
        /home/rod/.local/bin/:.....

which command is also usefull to determine if the tool is using an alias.

    $ which ls
        alias ls='ls --color-auto'
        usr/bin/ls

### whereis

    $ whereis diff
        diff : /usr/bin/diff /usr/share/man/man1/diff.1.gz

### locate

> unlike in `whereis` the `locate` tool cal employ a pattern.

    -A --all            display files that match all the patterns only.
    -b --basename       search only files  not directories.
    -c --count          display the number of iles matching the pattern instead of list of names.
    -q --quiet          suppress error messages.
    -i --ignore-case    ignore case.
    -r --regexp R       use regex 'R' instead of a pattern.
    -w --wholename      search files and directories.(default).
.

    $ locate text2
        /home/rod/Downloads/text2
        /home/rod/text22
        /home/rod/mytext23

> `locate` automatically add wild cards to the start and end of the \***pattern**\*.

to avoid **globbing**, use quotation marks and escape char.

`$ locate '\text2'`

using multiple patterns

`$ locate '\passwd' group newtext3 hello`

> `locate` uses the ***mlocate** database which is updated once a day with a **cronjob** , hence newly created files wont be listed.\
> run `updatedb` to update the file list. (may take a long time).

### find

    -cmin x         files status changed x mins ago.
    -mmin x         files data changed x min ago.
    -empty          display empty files and folders.
    -gid x          group id.
    -group "x"      group name.
    -inum x         inode number.
    -maxdepth x     recurse depth.
    -name ""        regex or pattern.
    -iname ""       regex or pattern ignore case.
    -nogroup        has no group.
    -nouser         has no user.
    -perm 666       matching perms octal or symbolic.
    -size x         matching size add suffix as required (G).
    -user "max"     of user "max".

`find . -maxdepth 2 -name "*.txt" -perm /4000`

> older systems use '+' instead of '/'.
