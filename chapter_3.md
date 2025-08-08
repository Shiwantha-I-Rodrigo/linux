# MANAGING FILES

> ***!* commands that support file chains are indicated with [ file+++ ]**.\
> **`[command] [fie1] [file2] [file3] ...` --> `[command] [file+++]`**

**`ls `**`[option] [file+++|directory+++]`\
--> list files and directories.

|Option |               |Description|
|-      |-              |-|
|-a     |--all          |display all including hidden|
|-d     |--directory    |show directory metadata instead of content|
|-F     |--classify     |claasify each file using indicators|
|-i     |--inode        |display files, folders with associated inode numbers|
|-l     |               |display file, folder metadata|
|-R     |               |show folder content recursively|
|-lh    |               |show metadata in a more human readable format|

---

**`tree `**`[option]`\
--> shows a tiered structure of files and folders.

|Option     |Description|
|-          |-|
|-a         | show hidden files|
|-d         | only directories|

---

**`touch `**`[option] [file+++]`\
--> create new empty files.

---

**`mkdir `**`[option] [directory+++]`\
--> create new directories.

|Option     |Description|
|-          |-|
|-p         | create parent directories, if doesn't exist|
|-v         | verbose mode|

> **!** a folder is just a file with pointers to various file locations (inode numbers).

---

**`cp `**`[option] [source+++] [destination]`\
--> copy files, folders.

|Option |                   |Description|
|-      |-                  |-|
|-a     |--archive          |recursive copy with file attributes preserved|
|-f     |--force            |overwrite any existing files|
|-i     |--interactive      |ask before overwriting|
|-n     |--no-clobber       |dont overwrite existing files|
|-R / -r|--recursive        |recursive|
|-u     |--update           |overwrite if source is newer|
|-v     |--verbose          |verbose mode|

> **!** to chain directories **-r** is required, not required for files.\
> **!** `$ cp -r dir1 dir2 dir3 destination/`

---

**`mv `**`[option] [source+++] [destination]`\
--> move files, folders.

> **!** options are identical to `cp` command\
> **!** excluding `-a` and `-r`.

---

**`rsync `**`[option] [source+++] [destination]`\
--> local sync

**`rsync `**`[option] [source+++] [user]@[host]:[destination]`\
--> remote push

**`rsync `**`[option] [user]@[host]:[source] [destination]`\
--> remote pull

|Option     |                       |Description|
|-          |-                      |-|
|-a         |--archive              |archive mode **-a = -rlptgoD**|
|-r         |--recursive            |recusrive|
|-l         |--links                |preserve symbolic links|
|-p         |--perms                |preserve permissions|
|-t         |--times                |preserve timestamps|
|-g         |--group                |preserve group|
|-o         |--owner                |preserve owner|
|-D         |--devices --specials   |preserve device & special files|
|-h         |--human-readable       |human-readable output|
|-v         |--verbose              |verbose mode|
|-z         |--compress             |compress **only for transfer**|
|-u         |--update               |skip newer files on destination|
|           |--stats                |display file transfer stats|
|           |--progress             |display sync progress|
|           |--delete               | **!** delete extra files at destination|

> **!** `rsync` can be tunneled through **OpenSSH** to provide encryption on remote syncs.\
> **!** `$ rsync -e ssh source/ user@host:/destination/`\
> **!** `$ rsync -e "ssh -p 2221" source/ user@host:/destination/` --> ssh on port 2221

> **!** the ***speedup*** stat indicate the portion of data actually copyied compared to copying everything.\
> **!** **speedup : 3.0** had to copy only 33% of the source data to sync with the destination.

> **!** ommiting the trailing **/** will copy just the directory and not the content.

---

**`rm `**`[option] [file+++ | directory+++]`\
--> delete files & folders.

|Option     |                   |Description|
|-          |-                  |-|
|-d         |--dir              |remove empty directories only|
|-f         |--force            |ignore non-existence & other prompts|
|-i         |--interactive      |prompt before every removal|
|-I         |                   |prompt before removing more than 3 files or when using -r option|
|-r / -R    |--recursive        |recursively|
|-v         |--verbose          |verbose mode|

> **!** `$ rmdir directory/` == `$ rm -d directory/`

---

## HARD LINKS Vs SOFT LINKS

|Hard Link                                                  |Soft Link|
|-                                                          |-|
|file1 ---> [inode 19229] <--- file2                        |file2 ---> file1 ---> [inode 27236]|
|`$ ln file1 file2`                                         |`$ ln -s file1 file2`|
|One physical file with multiple names                      |Point to a filename|
|deleting one link doesn't affect the inode                 |deleting the original file removes the inode|
|                                                           |deleting the original creates a "dangling link"|
|the "original" file is also just a Hard Link to the inode  ||
|all the links have the same data                           |different links ave different data|
|must exist in same filesystem                              |can exist in different filesystems|
|increase link count                                        |no change to link count|

    # Determine the hardlink count of files
    $ ls -ogi
        total 23421
        23453426    -rw-rw-r--    2     123456  Aug 24 18:09    HardLinkFileOriginal
        23453426    -rw-rw-r--    2     123456  Aug 24 19:10    HardLink1
        17642378    -rw-rw-r--    1     123456  Aug 24 18:20    SoftLinkFileOriginal
        65436743    lrwxrwxrwx    1         38  Aug 24 19:11    SoftLink1
        [inode]     [perms]    [links]  [size] [modified date]  [filename]
    
    #(-og) --> omit group & owner columns

---

## READING FILES

**`cat `**`[option] [file+++]`\
--> direct the content of file to STDOUT.

|Option     |                       |Description|
|-          |-                      |-|
|-n         |                       |display line numbers|
|-b         |--number-nonblank      |display line numbers for only for lines with text, override **-n**|
|-v         |--show-nonprinting     |display non printing chars|
|-E         |--show-ends            |display line endings with **$**|
|-e         |                       |display non printing chars with line ends, same as **-vE**|
|-T         |--show-tabs            |display tabs|
|-t         |                       |display non printing chars with tabs, same as **-vT**|
|-s         |--squeeze-blank        |condense consecutive blank lines|

---

**`pr `**`[option] [file+++]`\
--> originally used for formating files for printing.

|Option     |                   |Description|
|-          |-                  |-|
|-x         |--columns=x        |format output as multiple columns (x columns)|
|-lx        |--length=x         |page length.(default 66)|
|-wx        |--width=x          |page width. (default 72)|
|-nzx       |--number-lines=zn  |number all lines <br> use **z** as sepertaor between number and the line <br> print **n** spaces before the number|
|-m         |--merge            |display multiple files as columns, truncate lines|
|-sx        |--seperator=x      |change the column seperator to ***x* CHAR** (default TAB) <br> **turn off truncation** for all columns <br> **use *-w* setting to avoid messy output**|
|-Sxxx      |--sep-string=xxx   |change the column seperator to ***xxx* STRING** <br> has no effect on line truncation|
|-t         |--omit-header      |omit file header and trailer|

> **!** `$ pr -m file1 file2`\
> **!** without the -m option files will be in seperate pages sequentially.

---

**`head `**`[option] [file+++]`\
--> display top lines.

|Option                 |Description|
|-                      |-|
|-n x --lines=x         |display top **x** lines (default 10)|

|Value      |Result|
|-          |-|
|**-n 4**   |top 4|
|**-4**     |top 4|
|**-n +4**  |top 4|
|**-n -4**  |exclude bottom 4|
|**+4**     |error|

---

**`tail `**`[option] [file+++]`\
--> display bottom lines.

|Option                 |Description|
|-                      |-|
|-n x --lines=x         |display bottom **x** lines (default 10)|

|Value      |Result|
|-          |-|
|**-n 4**   |bottom 4|
|**-4**     |bottom 4|
|**-n -4**  |bottom 4|
|**-n +4**  |print from 4th line **(exclude top 3)**|
|**+4**     |print from 4th line **(exclude top 3)**|

---

## PAGERS

**`more `**`[option] [file+++]`\
--> **pager**, view the contents of files one page (screen) at a time.

|Shortcut       |Action|
|-              |-|
|spacebar       |forward one page|
|enter          |forward one line|
|q / Q          |quit|

> **!** read the whole file at start (therefore slower compared to **less**).\
> **!** show file progress.

---

**`less `**`[option] [file+++]`\
--> **pager**, view the contents of files one page (screen) at a time.

|Shortcut       |Action|
|-              |-|
|spacebar       |forward one page|
|enter          |forward one line|
|q / Q          |quit|
|k / ↑          |Scroll **up** one line|
|j / ↓          |Scroll **down** one line|
|/pattern       |Search **forward** for a pattern|
|?pattern       |Search **backward** for a pattern|
|n              |Repeat **last search** (same direction)|
|N              |Repeat **last search** (opposite direction)|

---

## FILE ANALYSIS

**`file `**`[option] [file+++]`\
--> determine file type.

    $ file myfile
    myfile : Bourne-Again Shell script, ASCII text executable

> **!** passing a directory to `file` will tell you that it's a directory.

---

**`stat `**`[option] [file+++]`\
--> display detailed information about files or directories.

    $ stat myfile
    file : myfile
    size : ....     Blocks : .....
    Device : ....   Inode : .....   Links : ....
    Access : ....

---

**`diff `**`[option] [main] [reference]`\
--> compare two files or directories.

|Option     |                           |Description|
|-          |-                          |-|
|-q         |--brief                    |if files are different just issue a simple message|
|-s         |--report-identical-files   |if files are similar just issue a message|
|-y         |--side-by-side             |two columns|
|-r         |--recursive                |compare all subdirectories as well|
|-W n       |--width n                  |display a maximum width of n chars in output|

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
- add after **1st** line (as the 2nd line) to main file (text1) the **3rd** line from reference file (text2).
- change lines **2 and 4** of main file (text1) to match **2 and 4** of reference file (text2).

---

**`which `**`[option] [command+++]`\
--> locate the executable file associated with a given command.

    $ which diff
    /usr/bin/diff

`$PATH` environment variable is used by `which` to look for executable locations.

|Option     |Description|
|-          |-|
|-a         |view all matches, not just the first|

**which** command is also usefull to determine if the tool is using an alias.

    $ which ls
    alias ls='ls --color-auto'
    usr/bin/ls

---

**`whereis `**`[option] [command+++]`\
--> locate the binary, source and manual page files for a given command.

    $ whereis diff
    diff : /usr/bin/diff /usr/share/man/man1/diff.1.gz

---

**`locate `**`[option] [pattern+++]`\
--> quickly find files and directories by name, much faster than find.

|Option     |                   |Description|
|-          |-                  |-|
|-A         |--all              |only display files that match all the patterns (default)|
|-b         |--basename         |exclude directories|
|-c         |--count            |display only matched count|
|-q         |--quiet            |suppress error messages|
|-i         |--ignore-case      |ignore case|
|-r         |--regexp           |use regex instead of a pattern.|
|-w         |--wholename        |search files and directories (default)|

> **!** **locate** automatically add wild cards to the start and end of the \***pattern**\*.\
> **!** to avoid **file globbing**, use (double|single) quotation marks + escape char ( **\\** ) or REGEX ( **-r** ).\
> **!** `$ locate '\text2'`

> **locate** uses the **mlocate** database which is updated once a day with a **cronjob** , hence newly created files wont be listed.\
> **!** `$ updatedb` to update the file list immediately.

---

**`find `**`[option] [base] [argument] [pattern+++]`\
--> search the live filesystem for files and directories based on name, type, size, time, ownership, etc...

> **!** using **find** without any arguments may result in unexpected behavior.\
> **!** `$ find file1` --> does not match any file or folder called **file1** + outputs every file and folder in search scope.\
> **!** `$ find /home/file1` --> matches **/home/file1** + outputs every file and folder in search scope.

|Argument   |           |Description|
|-          |-          |-|
|-size x    |           |file size (b/c/k/M/G)|
|-amin x    |-atime     |file access|
|-mmin x    |-mtime     |file content change (modification)|
|-cmin x    |-ctime x   |file **status** change <br> min --> minuites / time --> days <br> x --> exactly / -x --> less than / +x --> more than <br> file status --> Permissions / Ownership / Number of hard links / Inode Number|
|-name ""   |           |regex or pattern|
|-iname ""  |           |regex or pattern ignore case|
|-gid x     |           |group id|
|-group ""  |           |group name|
|-inum x    |           |inode number|
|-perm xxx  |           |matching perms octal or symbolic <br><br> **-u=x** -->  user has execution <br> **-ug=rw** --> user & group can read and write <br><br> **764** --> exact match <br> **-764** --> at least 764 (764 / 765 / 766 / 767 / 774 / 775 / 776 / 777) <br> **/764** --> match any combination of the set bits **rwxrw-r--** <br> r-------- / -w------- / --x------ / rw------- / rwx------ / r-x------ / -wx------ / (etc...)|
|-maxdepth x|           |recurse max depth|
|-mindepth x|           |recurse min depth|
|-user "x"  |           |user "x"|
|-nogroup   |           |has no group|
|-nouser    |           |has no user|
|-empty     |           |empty files and folders|
