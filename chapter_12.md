# PROTECTING FILES

## BACKUPS

- System image
    * copy OS binaries, config files and anything that is required to boot the system.

- Full
    * copy all data.
    * less time to restore, more time to create.
    * ignore dates.

- Incremental
    * copy data that has been modified since last **backup**.
    * use modified timestamp.
    * less time to create, more time to restore.
    * must be restored incrementally as well.
    * less storage.
    * periodical full backup is recomended.

- Differential
    * copy data that has been changed since last **full backup**.
    * use modified timestamp.
    * middle ground between full and incremental backups.
    * periodical full backup is recomended.

- Snapshot
    * Instead of copying all data, snapshots often store metadata or pointers that indicate where the data was at the time of the snapshot.

- Snapshot Clone
    * backup a copy of the snapshot.

## COMPRESSION METHODS

- gzip
    * gzip / gunzip
    * replace original file
- bzip2
    * bzip2 / bunzip2
    * higher rates than gzip
    * longer time
    * replace original file
- xz
    * xz / unxz
    * higher rates than gzip and bzip2
    * replace original file
- zip
    * compression + archive ( multiple files )
    * does not replace original file/s

**all compression methods listed above are lossless**

## ARCHIVE AND RESTORE

! for below archive formats, the extentions are not required but recomended.

**cpio**

`$ ls *.txt | cpio -ov > TextArchive.cpio`

    -I                              designate the archive file to use.
    -i  --extract                   copy or view files from an archive. (COPY-IN MODE)
        --no-absolute-filenames     use relative path names.
    -o  --create                    create archive by copying files into it. ( COPY-OUT MODE)
    -t  --list                      display the list of files from archive. ( TABLE OF CONTENTS )
    -v  --verbose                   verbose output.

`$ find / -user Adam | cpio -ov > AdamsFiles.cpio` < create an archive of user Adams files >

`$ cpio -iv --no-absolute-filenames -I AdamsFiles.cpio` < restore Adams files to another location >

> **--no-absolute-filanames** is required if restoring to a different location than the original.


**dd**

> create low level copies of entire drives or partitions.

`$ dd if=input of=output status=progress`

    bs=BYTES        set maximum block size.(512 default).
    count=N         set number of input blocks to copy.
    status=LEVEL    set the level of info to display with STDERR.
                        none > display only errors.
                        noxfer > do not display final stats.
                        progress > periodical transfer stats.

> set input to **/dev/zero** to zero an entite drive.\
> set input to **/dev/random** or **/dev/urandom** for random data.


**rsync**

`$ rsync -avh *.txt TextFiles/` < local copy >\
`$ rsync -avP -e ssh *.txt Adam@192.168.10.104:~` < remote copy >

> **OpenSSH** service must be running on both machines for remote copy.\
> **IPv4** or **hostname** can be used.\
> **~** indicates the destination folder.


    -e  --rsh       set communication protocol ( OpenSSH default )
    -z  --compress  compress data during transfer.


**tar**

archive = tar
archive + compress = tarball

    -c  --create                create a tar archive.
    -f                          point to archive file.
    -u  --update                append modified files.
    -g  --listed-incremental    create incremental or full archive based on metadata in provided file.
    -z  --gzip                  tarball with gzip.
    -j  --bzip2                 tarball with bzip2.
    -J  --xz                    tarball with xz.
    -v  --verbose               verbose output.

    -d  --compare   --diff      compare tar archives files with external files and list differences.
    -t  --list                  display tar content.
    -W  --verify                verify each file as it is being processed, cannot be used with compression.

    -x  --extract   --get       extract files.
    -z  --gunzip                uncompress with gzip.
    -j  --bunzip2               uncompress with bzip2.
    -J  --unxz                  uncompress with xz.

`$ tar -zcvf TextArchive.tar.gz *.txt` < create a compressed archive of all text files in the dir >

> tar command can use old style options\
> `tar zcvf` is also valid.\
> `tar z c v f` is not.

`$ tar -g Backup.snar -Jcvf  backup.tar.xz *.txt`
< create a snapshot metadata file (snar) and the archive file ( tar.xz ) >

`$ tar -g Backup.snar -Jcvf  backup_increment_1.tar.xz *.txt`
< create the incremntal backup using the same metadata file (snar) >

> tar command views full backup as **level 0** and each incremntal backup as increments ( 1,2,3,...)

`$ tar -df backup.tar.gz` < compare files to originals >

`$ tar -zxvf backup.tar.gz` < extract files to current dir >


## OFFSITE BACKUP

**scp**

- employes OpenSSH.
- **cannot resume** interrupted transfers.
- suited for small files.
- will overwrite any file without confirmation.

`scp Adam@192.168.10.104:backup.tar Robert@192.168.10.106:~`
< remote to remote copy >

    -C  compress data for transfer
    -p  preserve file attributes.
    -r  recursive copy.
    -v  verbose output.

**sftp**

- employs OpenSSH
- more interactive than scp

`$ sftp Adam@192.168.10.104` < connect to remote machine >
`> put backup.tar` < send the file to remote >
`> ls` < list files in remote >
`> exit`

    get         get a file/s from remote.( download )
    reget       resume an interrupted get.
    put         send a file/s to remote. ( upload )
    reput       resume an interrupted put.
    ls          list files in working dir of remote.
    lls         list files in working dir of local.
    cd          change working dir remote.
    lcd         change working dir local.
    mkdir       create a dir on remote.
    lmkdir      create a dir on local.
    progress    toggle progress display.
    bye         exit.
    exit        exit.

## BACKUP INTEGRITY

**MD5**

`$ md5sum backup.tar` < generate md5 **128-bit** hash for backup.tar >

**SHA**

`$ sha256sum backup.tar` < generate sha256 hash for backup.tar >

`$ ls -l /bin/sha???sum`\
< searching for sha utitlities in your distro >
< /usr/bin/ may also be searched >
