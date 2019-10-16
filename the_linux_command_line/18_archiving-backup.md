# Compressing files
- Data compression is the process of removing redundancy from data
- Compression algorithms fall into two general categories
    1. Lossless
        - Lossless compression preserves all the data contained in the original. This means that when a file is restored from a compressed version, the restored file is exactly the same as the original, uncompressed version
    2. Lossy
        - Lossy compression removes data as the compression is performed to allow more compression to be applied. When a lossy file is restored, it does not match the original version; rather, it is a close approximation
            - Examples of lossy compression are JPEG and MP3
- If you apply compression to a file that is already compressed, you will usually end up with a larger file. This is because all compression techniques involve some **overhead** that is **added to the file to describe the compression**. If you try to compress a file that already contains no redundant information, the compression will most often not result in any savings to offset the additional overhead
	
    ```bash
    # Don't do it!
    gzip picture.jpg
    ```

## `gzip`
- The `gzip` program is used to compress one or more files. When executed, it **replaces** the original file with a compressed version of the original
	
    ```bash
    ls -l /etc > foo.txt
    ls -l foo.*
    # -rw-r--r-- 1 root root 9601 Jul 11 06:42 foo.txt
    ls -l foo.*
    gzip foo.txt
    # -rw-r--r-- 1 root root 1964 Jul 11 06:42 foo.txt.gz
    gunzip foo.txt # gunzip foo.txt.gz
    ls -l foo.*
    # -rw-r--r-- 1 root root 9601 Jul 11 06:42 foo.txt
    ```

    - The compressed file has the same permissions and timestamp as the original
    - `gunzip` uncompress the file. Afterward, the compressed version of the file has been **replaced** with the original, again with the permissions and timestamp preserved
        - The `gunzip` program, which uncompresses `gzip` files, assumes that filenames end in the extension `.gz`, so it’s not necessary to specify it, as long as the specified name is not in conflict with an existing uncompressed file
- `gzip` options

    Option | Long option | Description |
    --|--|--|
    `-c` | `--stdout`, `--to-stdout` | Write output to standard output and keep the original files.
    `-d` | `--decompress`, `--uncompress` | Decompress. This causes gzip to act like `gunzip`.
    `-f` | `--force` | Force compression even if a compressed version of the original file already exists.
    `-h` | `--help` | Display usage information.
    `-l` | `--list` | List compression statistics for each file compressed.
    `-r` | `--recursive` | If one or more arguments on the command line is a directory, recursively compress files contained within them.
    `-t` | `--test` | Test the integrity of a compressed file.
    `-v` | `--verbose` | Display verbose messages while compressing.
    `-number` | | Set amount of compression. `number` is an integer in the range of 1 (fastest, least compression) to 9 (slowest, most compression). The values 1 and 9 may also be expressed as `--fast` and `--best`, respectively. The default value is 6.
    | | |

- `gzip` can be used via standard input and output
	
    ```bash
    # creates a compressed version of a directory listing
    ls -l /etc | gzip > foo.txt.gz
    ```

- View the contents of a compressed text file
	
    ```bash
    # -c: write on standard output, keep original files unchanged
    gunzip -c foo.txt.gz | less

    zcat foo.txt.gz | less

    # performs the same function as the previous pipeline
    zless foo.txt.gz
    ```

    - Alternately, there is a program supplied with `gzip`, called `zcat`, that is equivalent to gunzip with the `-c` option. It can be used like the `cat` command on gzip-compressed files
## `bzip2`
- The `bzip2` program, by Julian Seward, is similar to `gzip` but uses a different compression algorithm that achieves higher levels of compression at the cost of compression speed

    ```bash
    bzip2 foo.txt
    ls -l foo.txt.bz2
    # -rw-r--r-- 1 root root 1847 Jul 11 06:42 foo.txt.bz2
    bunzip2 foo.txt.bz2
    ls -l foo.txt.bz2
    # -rw-r--r-- 1 root root 9601 Jul 11 06:42 foo.txt
    ```
    
    - A file compressed with `bzip2` is denoted with the extension `.bz2`
    - `bzip2` comes with `bunzip2` and `bzcat` for decompressing files
    - `bzip2` also comes with the `bzip2recover` program, which will try to recover damaged `.bz2` files
    - In most regards, it works in the same fashion as `gzip`
        - All the options (except for `-r`) that we discussed for `gzip` are also supported in `bzip2`
        - The compression-level option (`-number`) has a somewhat different meaning to `bzip2`
# Archiving files
- A common file-management task often used in conjunction with compression is *archiving*
- Archiving is the process of gathering up many files and bundling them together into a single large file
    - Archiving is often done as part of system backups
    - It is also used when old data is moved from a system to some type of long-term storage
## `tar`
- `tar` is short for *tape archive* (reveals its roots as a tool for making backup tapes)
    - While it is still used for that traditional task, it is equally adept on other storage devices
- Filenames that end with the extension `.tar` or `.tgz` indicate a “plain” tar archive and a gzipped archive, respectively
- A tar archive can consist of a group of separate files, one or more directory hierarchies, or a mixture of both
- Command syntax

    ```bash
    tar mode[options] pathname...
    ```

    - `mode` is one of the operating modes
- `tar` modes

    Mode | Description |
    --|--|
    `c` | Create an archive from a list of files and/or directories.
    `x` | Extract an archive.
    `r` | Append specified pathnames to the end of an archive.
    `t` | List the contents of an archive.
    | |

- Examples

    ```bash
    mkdir -p playground/dir-{001..100}
    touch playground/dir-{001..100}/file-{A..Z}

    # creates a tar archive named playground.tar that contains the entire playground directory hierarchy
    tar cf playground.tar playground

    # list the contents of the archive
    tar tf playground.tar
    # detailed listing
    tar tvf playground.tar

    # extract the playground in a new location, creating a precise reproduction of the original files
    # with one caveat
    mkdir foo
    cd foo
    tar xf ../playground.tar
    ls
    ```

    - `f` option, used to specify the **name** of the tar archive, may be joined with the mode and **do not require a leading dash**. The **mode** must always be **specified first**, before any other option
    - **Caveat**: Unless we are operating as the superuser, files and directories extracted from archives take on the ownership of the user performing the restoration, rather than the original owner
- How `tar` handles pathnames in archives

    ```bash
    # /root
    mkdir me
    mv playground me
    cd me
    ls -Al
    # drwxr-xr-x 102 root root 4096 Jul 11 07:34 playground
    tar cvf playground2.tar ~/me/playground
    # ~/me/playground expands into /root/me/playground

    mkdir foo
    cd foo
    pwd
    # /root/me/foo

    tar xvf ../playground2.tar
    ls
    # root
    ls root
    # me
    ls root/me
    # playground
    ```

    - The default for pathnames is relative, rather than absolute. `tar` does this by simply **removing any leading slash** from the pathname when creating the archive (becomes `root/me/playground` here)
    - When we extracted the archive, it re-created the directory `home/me/playground` **relative to the current working directory**, not relative to the root directory, as would have been the case with an absolute pathname
        - It’s actually more useful this way because it allows us to extract archives to any location rather than being forced to extract them to their original locations
- Scenery: copy the home directory and its contents from one system to another and we have a large USB hard drive that we can use for the transfer (On our modern Linux system, the drive is “automagically” mounted in the `/media` directory); also imagine that the disk has a volume name of `BigDisk` when we attach it
	
    ```bash
    sudo tar cf /media/BigDisk/home.tar /home

    # After the tar file is written, unmount the drive and attach it to the second computer
    # Again, it is mounted at /media/BigDisk
    cd /
    sudo tar xf /media/BigDisk/home.tar
    ```

    - What’s important to see here is that we must first change directory to `/` so that the extraction is relative to the root directory since all pathnames within the archive are relative
- It’s possible to limit what is extracted from the archive
	
    ```bash
    tar xf archive.tar pathname

    cd foo
    tar xf ../playground2.tar --wildcards 'root/me/playground/dir-*/file-A'
    ```

    - By adding the trailing `pathname` to the command, `tar` will restore only the specified file
        - Multiple pathnames may be specified
        - The pathname must be the **full, exact relative pathname** as stored in the archive
    - When specifying pathnames, wildcards are not normally supported; however, the GNU version of `tar` (which is the version most often found in Linux distributions) supports them with the `--wildcards` option
- `tar` is often used in conjunction with `find` to produce archives
	
    ```bash
    find playground -name 'file-A' -exec tar rf playground.tar '{}' '+'
    ```

    - Here we use `find` to match all the files in `playground` named `file-A` and then, using the `-exec` action, we invoke `tar` in the append mode (`r`) to add the matching files to the archive `playground.tar`
- Using `tar` with `find` is a good way of creating *incremental backups* of a directory tree or an entire system
    - By using `find` to match files newer than a timestamp file, we could create an archive that contains only those files newer than the last archive, assuming that the timestamp file is updated right after each archive is created
- `tar` can also make use of both standard input and output
	
    ```bash
    # first `-`: output file; second `-`: input file
    find playground -name 'file-A' | tar cf - --files-from=- | gzip > playground.tgz
    ```

    - If the filename `-` is specified, it is taken to mean standard input or output, as needed 
        - This convention of using `-` to represent standard input/output is used by a number of other programs, too
    - The `--files-from` option (which may also be specified as `-T`) causes `tar` to read its list of pathnames from a file rather than the command line
    - The `.tgz` extension is the conventional extension given to gzip-compressed tar files. The extension `.tar.gz` is also used sometimes
    - Modern versions of GNU `tar` support both `gzip` and `bzip2` compression directly with the use of the <mark>`z` and `j`</mark> options, respectively
    	
        ```bash
        find playground -name 'file-A' | tar czf playground.tgz -T -
        find playground -name 'file-A' | tar cjf playground.tbz -T -
        ```
    
- Transfer a directory from a remote system (named `remote-sys` for this example) to the local system
	
    ```bash
    # copy a directory named `Documents` from the remote system `remote-sys` to a directory within the directory named `remote-stuff` on the local system
    mkdir remote-stuff
    cd remote-stuff
    ssh remote-sys 'tar cf - Documents' | tar xf -
    ```

    - Launched the `tar` program on the remote system using `ssh`. The standard output produced on the remote system is sent to the local system for viewing
        - `ssh` allows us to execute a program remotely on a networked computer and “see” the results on the local system
    - Take advantage of this by having `tar` create an archive (the `c` mode) and send it to standard output, rather than a file (the `f` option with the dash argument), thereby transporting the archive over the encrypted tunnel provided by `ssh` to the local system
    - On the local system, we execute `tar` and have it expand an archive (the `x` mode) supplied from standard input (again, the `f` option with the dash argument)
## `zip`
- The `zip` program is both a compression tool and an archiver
    - > In Linux, `gzip` is the predominant compression program, with `bzip2` being a close second
- Basic usage
	
    ```bash
    zip options zipfile file...

    zip -r playground.zip playground
    # adding: playground/dir-020/file-Z (stored 0%)
    # adding: playground/dir-020/file-Y (stored 0%)
    # adding: playground/dir-020/file-X (stored 0%)
    # adding: playground/dir-087/ (stored 0%)
    # adding: playground/dir-087/file-S (stored 0%)

    unzip playground.zip
    ```

    - Unless we include the `-r` option for recursion, only the `playground` directory (but none of its contents) is stored
    - The addition of the extension `.zip` is automatic (`.zip` is included for clarity)
    - These messages show the status of each file added to the archive
- `zip` will add files to the archive using one of 2 storage methods: either it will “store” a file without compression, as shown here, or it will “deflate” the file that performs compression
    - The numeric value displayed after the storage method indicates the amount of compression achieved. Since our playground contains only empty files, no compression is performed on its contents
- One thing to note about `zip` (as opposed to `tar`) is that if an existing archive is specified, it is **updated** rather than replaced
    - This means the existing archive is preserved, but new files are added, and matching files are replaced
- List and extract files selectively from a `zip` archive by specifying them to `unzip`
	
    ```bash
    unzip -l playground.zip playground/dir-087/file-Z
    unzip playground.zip playground/dir-087/file-Z
    ```

    - Using the `-l` option causes `unzip` to merely list the contents of the archive without extracting the file. If no files are specified, `unzip` will list all files in the archive
    - The `-v` option can be added to increase the verbosity of the listing
    - When the archive extraction conflicts with an existing file, the user is **prompted** before the file is replaced
- `zip` accepts standard input
	
    ```bash
    find playground -name "file-A" | zip -@ file-A.zip
    ls -l /etc/ | zip ls-etc.zip -
    ```

    - Pipe a list of filenames to `zip` via the `-@` option
    - Like `tar`, `zip` interprets the trailing dash as “use standard input for the input file”
- `zip` also supports writing its output to standard output, but its use is limited because few programs can make use of the output
- The `unzip` program does not accept standard input. This prevents `zip` and `unzip` from being used together to perform network file copying like `tar`
- The `unzip` program allows its output to be sent to standard output when the `-p` (for pipe) option is specified
	
    ```bash
    unzip -p ls-etc.zip | less
    ```

- The main use of `zip` and `unzip` is for exchanging files with Windows systems, rather than performing compression and archiving on Linux, where `tar` and `gzip` are greatly preferred
    - `zip`/`unzip` is used for interoperability with Windows systems
# Synchronizing files and directories
- A common strategy for maintaining a backup copy of a system involves keeping one or more directories synchronized with another directory (or directories) located on either the local system (usually a removable storage device of some kind) or a remote system
    - We might, for example, have a local copy of a website under development and synchronize it from time to time with the “live” copy on a remote web server
- `rsync` can synchronize both local and remote directories by using the *`rsync` remote-update protocol*, which allows `rsync` to quickly detect the differences between two directories and perform the minimum amount of copying required to bring them into sync
    - This makes `rsync` very fast and economical to use, compared to other kinds of copy programs
- Usage
	
    ```bash
    rsync options source destination
    ```

    - `source` and `destination` are one of the following:
        - A local file or directory
        - A remote file or directory in the form of `[user@]host:path`
        - A remote rsync server specified with a URI of `rsync://[user@]host[:port]/path`
    - Either the `source` or the `destination` must be a local file. Remote-to-remote copying is not supported
- `rsync` local files
	
    ```bash
    rm -rf foo/*
    rsync -av playground foo # a list of the files and directories being copied
    rsync -av playground foo # no listing of files

    touch playground/dir-099/file-Z
    rsync -av playground foo
    # sending incremental file list
    # playground/dir-099/file-Z
    ```

    - `-a` for archiving — causes recursion and preservation of file attributes
- Useful feature
	
    ```bash
    # copies the directory source into destination
    rsync source destination
    # copy only the contents of the source directory and not the directory itself
    rsync source/ destination
    ```

    - This is handy if we want only the contents of a directory copied without creating another level of directories within the destination
        - We can think of it as being like [ ] `source/*` in its outcome, but this method will copy all of the source directory’s content **including the hidden files**
- Imaginary external hard drive mounted at `/media/BigDisk`. Create a directory named `/backup` on the external drive and then using `rsync` to copy the most important stuff from our system to the external drive
	
    ```bash
    mkdir /media/BigDisk/backup
    # copied the /etc, /home, and /usr/local directories from local system to the imaginary storage device
    sudo rsync -av --delete /etc /home /usr/local /media/BigDisk/backup
    ```

    - Include the `--delete` option to remove files that may have existed on the backup device that no longer existed on the source device (this is irrelevant the first time we make a backup but will be useful on subsequent copies)
## Using `rsync` over a network
- One of the real beauties of `rsync` is that it can be used to copy files over a network. After all, the `r` in `rsync` stands for “remote”
- Remote copying can be done in one of two ways
    1. With another system that has `rsync` installed, along with a remote shell program such as `ssh`
    	
        ```bash
        # Assuming that remote system already had a directory named `/backup` where we could deliver our files
        sudo rsync -av --delete --rsh=ssh /etc /home /usr/local remote-sys:/backup
        ```
    
        - `--rsh=ssh` option instructs `rsync` to use the `ssh` program as its remote shell
            - In this way, we were able to use an `ssh`-encrypted tunnel to securely transfer the data from the local system to the remote host
        - Specified the remote host by prefixing its name (in this case the remote host is named `remote-sys`) to the destination pathname
    2. Using an `rsync` server
        - `rsync` can be configured to run as a daemon and listen to incoming requests for synchronization. This is often done to allow mirroring of a remote system
            - For example, Red Hat Software maintains a large repository of software packages under development for its Fedora distribution. It is useful for software testers to mirror this collection during the testing phase of the distribution release cycle. Since files in the repository change frequently (often more than once a day), it is desirable to maintain a local mirror by periodic synchronization, rather than by bulk copying of the repository. One of these repositories is kept at Duke University; we could mirror it using our local copy of `rsync` and their `rsync` server
            	
                ```bash
                mkdir fedora-devel
                rsync -av –delete rsync://archive.linux.duke.edu/fedora/linux/development/rawhide/Everything/x86_64/os/ fedora-devel
                ```
            
                - The URI of the remote rsync server consists of a protocol (`rsync://`), followed by the remote hostname (`archive.linux.duke.edu`), followed by the pathname of the repository