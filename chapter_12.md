# PROTECTING FILES

## BACKUPS

| **Backup Type**   | **Description**                                                                                                       | **Performance & Storage**                                                         | **Additional Notes** |
| -                 | -                                                                                                                     | -                                                                                 | - |
| **System Image**  | Copies OS binaries, configuration files, and everything required to boot the system.                                  | Useful for system recovery.                                                       | Captures a full bootable state. |
| **Full**          | Copies all data.                                                                                                      | Takes more time to create, but faster to restore. Ignores file modification dates.| Requires the most storage. |
| **Incremental**   | Copies only data changed since the last **backup**.                                                                   | Faster to create, slower to restore. Uses less storage.                           | Uses modification timestamps; restore must be incremental. |
| **Differential**  | Copies data changed since the last **full backup**.                                                                   | Moderate time and storage. Faster to restore than incremental.                    | Also uses modification timestamps. |
| **Snapshot**      | Captures metadata or pointers representing the data’s state at a specific point in time, rather than copying all data.| Very fast and storage-efficient.                                                  | Often used in virtual environments and storage systems. |
| **Snapshot Clone**| Creates a backup copy of an existing snapshot.                                                                        | Uses more space than snapshots alone but retains the snapshot's structure.        | Useful for testing and sandbox environments. |

---

## COMPRESSION METHODS

| **Compression Tool** | **Commands**        | **Compression Characteristics**                | **Effect on Original Files**        |
| -------------------- | ------------------- | ---------------------------------------------- | ----------------------------------- |
| **gzip**             | `gzip` / `gunzip`   | Standard compression tool                      | Replaces the original file          |
| **bzip2**            | `bzip2` / `bunzip2` | Higher compression ratio than gzip, but slower | Replaces the original file          |
| **xz**               | `xz` / `unxz`       | Better compression ratio than gzip and bzip2   | Replaces the original file          |
| **zip**              | `zip` / `unzip`     | Compresses and archives multiple files         | Does **not** replace original files |

> **!** *all compression methods listed above are **lossless***

---

## ARCHIVE AND RESTORE

> **!** the **extentions** are **not required** but **recomended**.

`[filelist] |`**`cpio`**`[option] [>] [file]`\
**`cpio`**`[option] -O [file] [<] [filelist]`\
-->  creating & extracting archives.

`$ ls *.txt | cpio -o > Archive.cpio` == `$ cpio -o -O Archive.cpio < ls *.txt`

| **Option**    |                           | **Description** |
| -             | -                         | - |
| **-o**        |**--create**               | Archive files (Copy-out mode) |
| **-O**        |                           | specify the output file |
| **-i**        |**--extract**              | Extract files (Copy-in mode) |
| **-I**        |                           | Specify the Input file |
|               |**--no-absolute-filenames**| Use relative paths, Required on **extraction**<br>if restoring to a different location than the original |
| **-t**        |**--list**                 | Display a list of files contained in the archive |

---

**`dd `**`if=[input] of=[output] [option]`\
--> create low level copies of entire drives or partitions.

| **Option**        | **Description** |
| -                 | - |
| **bs=BYTES**      | Sets the block size for both input and output Default is 512 bytes |
| **count=N**       | Specifies the number of input blocks to copy |
| **status=LEVEL**  | Controls the level of informational output sent to STDERR |
|                   | **none** – Show only error messages |
|                   | **noxfer** – Do not show final transfer statistics |
|                   | **progress** – Show ongoing progress updates during transfer |

> **!** set input to **/dev/zero** to zero an entite drive.\
> **!** set input to **/dev/random** or **/dev/urandom** for random data.

---

**`rsync`**\
--> *( see chapter 3 )*

---

**`tar `**`[option] [file+++]`\
-->  combine multiple files and directories into a single archive file.

**Tar** : archive\
**Tarball** : compress + archive

---> create

| **Option**|                           | **Description**|
| -         | -                         | - |
| **-c**    | **--create**              | Create a new tar archive |
| **-f**    |                           | Specify the archive file to use |
| **-u**    |**--update**               | Append only modified files to an existing archive |
| **-g**    |**--listed-incremental**   | Create / Use a snapshot file for incremental backups|
| **-z**    |**--gzip**                 | Compress archive with gzip |
| **-j**    |**--bzip2**                | Compress archive with bzip2 |
| **-J**    |**--xz**                   | Compress archive with xz |
| **-v**    |**--verbose**              | Verbose mode |

---> view

| **Option**| -                         |**Description** |
| -         | -                         | - | 
| **-d**    |**--compare**<br>**--diff**| Compare archive contents with files on disk and report differences |
| **-t**    |**--list**                 | List the contents of a tar archive |
| **-W**    |**--verify**               | Verify archive contents after writing (not usable with compression options) |

---> extract

| **Option**| -                         |**Description**|
| -         | -                         | - |
| **-x**    | **--extract**<br>**--get**| Extract files from an archive |
| **-z**    | **--gunzip**              | Decompress **.gz** compressed archive |
| **-j**    | **--bunzip2**             | Decompress **.bz2** compressed archive |
| **-J**    | **--unxz**                | Decompress **.xz** compressed archive |

> tar command can use old style options\
> **`tar -zcvf `** : Valid.\
> **`tar zcvf `** : Valid.\
> **`tar z c v f `** : Invalid.

`$ tar -g Backup.snar -Jcvf  backup.tar.xz *.txt`\
--> create a snapshot metadata file (snar) and the archive file ( tar.xz ).

`$ tar -g Backup.snar -Jcvf  backup_increment_1.tar.xz *.txt`\
--> create an incremntal backup using the snapshot metadata.

> **!** tar command views full backup as **level 0** and each incremntal backup as increments ( 1,2,3,...)

---

## OFFSITE BACKUP

**`scp `**`[option] [user]@[host]:[source]+++ [user]@[host]:[directory]`\
--> securely transfer files between local <- -> remote & remote <- -> remote hosts over **SSH**

> **!** best suited for **small files** becouse it **cannot resume** interrupted transfers and **overwrites** existing files without confirmation.

| **Option**| **Description**|
| -         | - |
| **-C**    | Enables compression during data transfer |
| **-p**    | Preserves original file attributes (e.g., timestamps, permissions) |
| **-r**    | Recursively copies directories and their contents|
| **-v**    | Verbose mode |

---

**`sftp `**`[user]@[host]`\
--> Interactive command-line tool used to transfer files securely over **SSH**.

`$ sftp Adam@192.168.10.104` ---> connect to remote host\
`> put backup.tar` ---> send the file to remote\
`> exit`

| **Command**   |           | **Description**|
| -             | -         | - |
|**get**        |           | Download file(s) from the remote server|
|**reget**      |           | Resume a previously interrupted**get** operation|
|**put**        |           | Upload file(s) to the remote server|
|**reput**      |           | Resume a previously interrupted**put** operation|
|**ls**         | **lls**   | List files in the current remote / local working directory |
|**cd**         | **lcd**   | Change the remote / local working directory|
|**mkdir**      | **lmkdir**| Create a directory on the remote / local host|
|**progress**   |           | Toggle the display of progress for file transfers|
|**bye**        | **exit**  | Exit the **sftp** session|

---

## BACKUP INTEGRITY

**MD5**

`$ md5sum backup.tar` ---> generate md5 **128-bit** hash for backup.tar

**SHA**

`$ sha256sum backup.tar` ---> generate **sha256** hash for backup.tar

> **!** `$ ls -l /bin/sha???sum` ---> search for sha utitlities in your distro\
> **!** `$ ls -l /usr/bin/sha???sum` ---> search for sha utitlities in your distro
