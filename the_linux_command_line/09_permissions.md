<!-- review 2019-10-18 14:21:05 -->
# Permissions
- Operating systems in the Unix tradition differ from those in the MS-DOS tradition in that they are not only multitasking systems but also multiuser systems. It means that more than one person can be using the computer at the same time
    - If a computer is attached to a network or the Internet, remote users can log in via `ssh` (*secure shell*) and operate the computer
    - In fact, remote users can execute graphical applications and have the graphical output appear on a remote display. The X Window System supports this as part of its basic design
## Owners, group members, and everybody else

```bash
file /etc/shadow
less /etc/shadow
```

- In the Unix security model, a user may own files and directories. When a user owns a file or directory, the user has control over its access
- Users can, in turn, belong to a group consisting of one or more users who are given access to files and directories by their owners
- In addition to granting access to a group, an owner may also grant some set of access rights to everybody, which in Unix terms is referred to as the *world*
- Find out information about your identity
	
    ```bash
    id
    # uid=0(root) gid=0(root) groups=0(root),1001(docker)
    ```

    - When user accounts are created, users are assigned a number called a user ID (`uid`), which is then, for the sake of the humans, mapped to a username. The user is assigned a group ID (`gid`) and may belong to additional groups
        - Fedora starts `uid` and `gid` numbering of regular user accounts at 500, while Ubuntu starts at 1,000
        - Ubuntu user belongs to a lot more groups. This has to do with the way Ubuntu manages privileges for system devices and services
- User accounts are defined in the `/etc/passwd` file, and groups are defined in the `/etc/group` file
    - For each user account, the `/etc/passwd` file defines the user (login) name, uid, gid, account’s real name, home directory, and login shell
    - If we examine the contents of `/etc/passwd` and `/etc/group`, we notice that besides the regular user accounts, there are accounts for the *superuser* (uid 0) and various other *system users*
    - When user accounts and groups are created, these files are modified along with `/etc/shadow`, which holds information about the user’s password
- While many Unix-like systems assign regular users to a common group such as `users`, modern Linux practice is to create a unique, single-member group with the same name as the user. This makes certain types of permission assignment easier
## Reading, writing, and executing
- Access rights to files and directories are defined in terms of read access, write access, and execution access
	
    ```bash
    > foo.txt
    ls -l foo.txt
    # -rw-r--r-- 1 root root 0 Jul 13 03:47 foo.txt
    ```

    - The first 10 characters of the listing are the *file attributes*
- The first of these characters is the *file type*
- Common file types

    Attribute | File type |
    --|--|
    `-` | A regular file.
    `d` | A directory.
    `l` | A symbolic link. Notice that with symbolic links, the remaining file attributes are always `rwxrwxrwx` and are *dummy values*. The **real** file attributes are those of the file the symbolic link points to.
    `c` | A character special file. This file type refers to a device that handles data as a stream of bytes, such as a terminal or `/dev/null`.
    `b` | A block special file. This file type refers to a device that handles data in blocks, such as a hard drive or DVD drive
    | |

- The remaining 9 characters of the file attributes, called the *file mode*, represent the read, write, and execute **permissions** for the file’s owner, the file’s group owner, and everybody else
- Permissions attributes

    Attribute | Files | Directories |
    --|--|--|
    `r` | Allows a file to be opened and read. | Allows a directory’s contents to be listed if the execute attribute is also set.
    `w` | Allows a file to be written to or truncated; however, this attribute does not allow files to be renamed or deleted. The **ability to delete or rename files is determined by directory attributes**. | Allows files within a directory to be created, deleted, and renamed **if** the execute attribute is also set.
    `x` | Allows a file to be treated as a program and executed. Program files written in scripting languages must also be set as **readable** to be executed. | Allows a directory to be entered, e.g., `cd` directory.
    | | |

- Permission attribute examples

    File attributes | Meaning |
    --|--|
    `-rwx------` | A regular file that is readable, writable, and executable by the file’s owner. No one else has any access.
    `-rw-------` | A regular file that is readable and writable by the file’s owner. No one else has any access.
    `-rw-r--r--` | A regular file that is readable and writable by the file’s owner. Members of the file’s owner group may read the file. The file is world-readable.
    `-rwxr-xr-x` | A regular file that is readable, writable, and executable by the file’s owner. The file may be read and executed by everybody else.
    `-rw-rw----` | A regular file that is readable and writable by the file’s owner and members of the file’s group owner only.
    `lrwxrwxrwx` | A symbolic link. All symbolic links have **“dummy” permissions**. The real permissions are kept with the actual file pointed to by the symbolic link.
    `drwxrwx---` | A directory. The owner and the members of the owner group may enter the directory and create, rename, and remove files within the directory.
    `drwxr-x---` | A directory. The owner may enter the directory and create, rename, and delete files within the directory. Members of the owner group may enter the directory but cannot create, delete, or rename files.
    | |

### `chmod`
- Only the file’s **owner or the superuser** can change the mode of a file or directory
- `chmod` supports 2 distinct ways of specifying mode changes
    1. Octal number representation
        - > Octal and hexadecimal are good for human convenience
    2. Symbolic representation
- Each digit in an octal number represents 3 binary digits, this maps nicely to the scheme used to store the file mode

    Octal | Binary | File mode |
    --|--|--|
    0 | 000 |  ---
    1 (2^0) | 001 |  --x
    2 (2^1) | 010 |  -w-
    3 | 011 |  -wx
    4 (2^2)| 100 |  r--
    5 | 101 |  r-x
    6 | 110 |  rw-
    7 | 111 |  rwx
    |

    - `111` from left to right: `r`, `w`, `x`
- Symbolic notation is divided into 3 parts
    1. Who the change will affect
    2. Which operation will be performed
    3. What permission will be set

    Symbol | Meaning |
    --|--|
    `u` | Short for “user” but means the file or directory **owner**.
    `g` | **Group owner**.
    `o` | Short for **“others”** but means world.
    `a` | Short for “all.” This is a combination of `u`, `g`, and `o`.
    |

    - If no character is specified, **“all” will be assumed**. 
- The operation may be a `+` indicating that a permission is to be **added**, a `-` indicating that a permission is to be **taken away**, or a `=` indicating that only the **specified** permissions are to be applied and that all others are to be removed
- Permissions are specified with the `r`, `w`, and `x` characters
- Symbolic notation does offer the advantage of allowing you to set a single attribute **without disturbing** any of the others
- `--recursive` option acts on both files and directories, so it’s not as useful as we would hope because we rarely want files and directories to have the same permissions
- Symbolic notation examine

    Notation | Meaning |
    --|--|
    `u+x` | Add execute permission for the owner.
    `u-x` | Remove execute permission from the owner.
    `+x` | Add execute permission for the owner, group, and world. This is equivalent to `a+x`.
    `o-rw` | Remove the read and write permissions from anyone besides the owner and group owner.
    `go=rw` | Set the group owner and anyone besides the owner to have read and write permissions. If either the group owner or the world previously had execute permission, it is removed.
    `u+x,go=rx` | Add execute permission for the owner and set the permissions for the group and others to read and execute. Multiple specifications may be **separated by commas**.
    |
    
- `chmod` in action
	
    ```bash
    > foo.txt
    chomod 600 foo.txt
    ls -l foo.txt
    # -rw------- 1 root root 0 Jul 13 03:47 foo.txt
    ```

### `umask` (set default permissions)
- The `umask` command controls the default permissions given to a **file** when it is **created**. It uses octal notation to express a mask of bits to be removed from a file’s mode attributes
	
    ```bash
    rm -f foo.txt
    umask # see current value
    # 0022
    > foo.txt
    ls -l foo.txt
    # -rw-r--r-- 1 root root 0 Jul 13 13:42 foo.txt

    rm foo.txt
    umask 0000
    > foo.txt
    ls -l foo.txt
    # -rw-rw-rw- 1 root root 0 Jul 13 13:43 foo.txt
    umask 0022 # cleanup
    ```

    Original file mode | &nbsp; --- rw- rw- rw- |
    --|--|
    Mask | 000 000 010 010 |
    Result | --- rw- r-- r-- |
    | |

    - Original file mode is `rw-rw-rw-`
    - Everywhere a 1 appears in the binary value of the mask, the corresponding attribute is unset
- Most of the time we won’t have to change the mask; the default provided by your distribution will be fine. In some high-security situations, however, we will want to control it
### Special permissions
- Though we usually see an octal permission mask expressed as a three-digit number, it is more technically correct to express it in **four digits**. Because, in addition to read, write, and execute permissions, there are some other, less used, permissions settings
- The first of these is the `setuid` bit (octal 4000)
    - When applied to an executable file, it changes the *effective user ID* from that of the **real user** (the user actually running the program) to that of the **program’s owner**
    - When an ordinary user runs a program that is `setuid root`, the program runs with the effective **privileges of the superuser**. This allows the program to access files and directories that an ordinary user would normally be prohibited from accessing
    - Most often this is given to a few programs owned by the superuser
    - Clearly, because this raises security concerns, the number of `setuid` programs must be held to an absolute minimum
        - In the UNIX System, privileges, such as being able to change the system's notion of the current date, and access control, such as being able to read or write a particular file, are based on user and group IDs. When our programs need additional privileges or need to gain access to resources that they currently aren't allowed to access, they need to change their user or group ID to an ID that has the appropriate privilege or access. Similarly, when our programs need to lower their privileges or prevent access to certain resources, they do so by changing either their user ID or group ID to an ID without the privilege or ability access to the resource
        - > http://poincare.matf.bg.ac.rs/~ivana/courses/ps/sistemi_knjige/pomocno/apue/APUE/0201433079/ch08lev1sec11.html
- The second less-used setting is the `setgid` bit (octal 2000), which, like the `setuid` bit, changes the effective group ID from the real group ID of the real user to that of the file owner
    - If the `setgid` bit is set on a directory, **newly created files** in the directory will be given the group ownership of the **directory** (目录所属的用户组) rather the group ownership of the file’s creator
    - This is useful in a shared directory when members of a common group need access to all the files in the directory, regardless of the file owner’s primary group
- The third is called the `sticky` bit (octal 1000)
    - This is a holdover from ancient Unix, where it was possible to mark an executable file as “not swappable”
    - On files, Linux ignores the sticky bit, but if applied to a **directory**, it prevents users from deleting or renaming files unless the user is either the owner of the directory, the owner of the file, or the superuser
    - This is often used to control access to a shared directory, such as `/tmp`
- Examples
	
    ```bash
    # assigning setuid to a program
    chmod u+s program
    # a program that is setuid
    -rwsr-xr-x

    # assigning setgid to a directory
    chmod g+s dir
    # a directory that has the setgid attribute
    drwxrwsr-x

    # assigning the sticky bit to a directory
    chmod +t dir
    # a directory with the sticky bit set
    drwxrwxrwt
    ```

## Changing identities
- 3 ways to take on an alternate identity
    1. Log out and log back in as the alternate user
    2. Use the `su` command
       - The `su` command allows you to assume the identity of another user and either **start a new shell session** with that user’s ID or **issue a single command** as that user
    3. Use the `sudo` command
       - The `sudo` command allows an administrator to set up a configuration file called `/etc/sudoers` and define specific commands that particular users are permitted to execute under an assumed identity
- The choice of which command to use is largely determined by which Linux distribution you use. Your distribution probably includes both commands, but its configuration will favor either one or the other
### `su`: ***run a shell*** with substitute user and group IDs
- Command syntax
	
    ```bash
    su [-[l]] [user]

    su -c 'command' # execute a single command

    # start a shell for the superuser
    # `$` becomes `#`
    su - # prompted for the superuser’s password
    # return to previous shell
    exit

    # It is important to enclose the command in quotes
    # as we do not want expansion to occur in our shell but rather in the new shell
    su -c 'ls -l /root/*'
    ```

    - If the `-l` option is included, the resulting shell session is a **login shell** for the specified user. This means the user’s environment is loaded and the working directory is changed to the user’s home directory
        - Notice that (strangely) the `-l` may be **abbreviated as `-`**, which is how it is most often used. If the user is not specified, the superuser is assumed 
### `sudo`: execute a command as another user
- The `sudo` command is like `su` in many ways but has some important additional capabilities
- The administrator can configure `sudo` to allow an ordinary user to execute commands as a different user (usually the superuser) in a **controlled** way
    - In particular, a user may be restricted to one or more specific commands and no others (no others allowed)
- Another important difference is that the use of `sudo` does not require access to the superuser’s password. Authenticating using `sudo` requires the **user’s own password**
- One important difference between `su` and `sudo` is that `sudo` does not start a new shell, nor does it load another user’s environment
    - This means that ***commands do not need to be quoted*** any differently than they would be without using `sudo`
        - Note that this behavior can be overridden by specifying various options
    - Note, too, that `sudo` can be used to start an interactive superuser session (much like `su -`) by specifying the `-i` option
    - > `sudo`, in most configurations, “trusts” you for several minutes until its **timer** runs out
- Use `-l` option to list what privileges are granted by `sudo`
	
    ```bash
    sudo -l
    ```

- One of the recurrent problems for regular users is how to perform certain tasks that require superuser privileges. These tasks include installing and updating software, editing system configuration files, and accessing devices
- In the Windows world, this is often done by giving users administrative privileges. This allows users to perform these tasks. However, it also enables programs executed by the user to have the same capabilities. This is desirable in most cases, but it also permits malware (malicious software) such as viruses to have free rein of the computer
- In the Unix world, there has always been a larger division between regular users and administrators, owing to the multiuser heritage of Unix. The approach taken in Unix is to grant superuser privileges **only when needed**. To do this, the `su` and `sudo` commands are commonly used
- Up until a few of years ago, most Linux distributions relied on `su` for this purpose
    - `su` didn’t require the configuration that `sudo` required, and having a root account is traditional in Unix
    - This introduced a problem. Users were tempted to operate as root unnecessarily. In fact, some users operated their systems as the root user exclusively since it does away with all those annoying “permission denied” messages. This is how you **reduce** the security of a Linux system to that of a Windows system. Not a good idea
- When Ubuntu was introduced, its creators took a different tack. By default, Ubuntu **disables logins to the root account** (by failing to set a password for the account) and instead uses `sudo` to grant superuser privileges. The **initial user** account is granted full access to superuser privileges via `sudo` and may grant similar powers to subsequent user accounts
### `chown`: change file owner and group
- The chown command is used to change the owner and group owner of a file or directory
	
    ```bash
    chown [owner][:[group]] file...
    ```

    - Superuser privileges are required to use this command
    - `chown` can change the file owner and/or the file group owner depending on the first argument of the command
- `chown` argument examples

    Argument | Results |
    --|--|
    `bob` | Changes the ownership of the file from its current owner to user bob.
    `bob:users` | Changes the ownership of the file from its current owner to user bob and changes the file group owner to group users.
    `:admins` | Changes the group owner to the group admins. The file owner is unchanged.
    `bob:` | Changes the file owner from the current owner to user bob and changes the group owner to the [x] login group of user bob.
    |

- 2 users: `janet`, who has access to superuser privileges, and `tony`, who does not. User `janet` wants to copy a file from her home directory to the home directory of `tony`. Because `janet` wants `tony` to be able to edit the file, `janet` changes the ownership of the copied file from `janet` to `tony`
	
    ```bash
    # tilde expansion
    sudo cp myfile.txt ~tony
    sudo ls -l ~tony/myfile.txt
    # -rw-r--r-- 1 root root root 2018-03-20 14:30 /home/tony/myfile.txt
    sudo chown tony: ~tony/myfile.txt
    sudo ls -l ~tony/myfile.txt
    # -rw-r--r-- 1 tony tony tony 2018-03-20 14:30 /home/tony/myfile.txt
    ```

    - Using the trailing colon in the first argument, `janet` also changed the group ownership of the file to the login group of `tony`, which happens to be group `tony`
    - > https://askubuntu.com/questions/538130/what-is-the-difference-between-primary-group-and-secondary-group-in-ubuntu
    - > https://linuxize.com/post/how-to-add-user-to-group-in-linux/
    - > https://linux.die.net/man/1/newgrp
### `chgrp`: change group ownership
- In older versions of Unix, the `chown` command changed only file ownership, not group ownership. For that purpose, a separate command, `chgrp`, was used
    - It works much the same way as `chown`, except for being more limited
## Setting up a shared directory
- 2 users named `bill` and `karen`. They both have music collections and want to set up a shared directory, where they will each store their music files as Ogg Vorbis or MP3. User `bill` has access to superuser privileges via `sudo`
	
    ```bash
    ## root create a new group music for bill and karen
    sudo groupadd music

    adduser bill
    usermod -aG sudo bill
    usermod -aG music bill
    groups bill

    adduser karen
    usermod -aG music karen
    groups karen

    su - bill
    sudo ls -la /root # verify bill is in sudo group


    ## bill operates the following commands
    su - bill
    umask
    # 0002
    umask 0022

    # Because bill is manipulating files **outside** his home directory, superuser privileges are required
    sudo mkdir /usr/local/share/Music
    ls -ld /usr/local/share/Music
    # drwxr-xr-x 2 root root 4096 Jul 15 01:13 /usr/local/share/Music
    # 755

    # Music group owner 由 root 变为 music
    sudo chown :music /usr/local/share/Music
    ls -ld /usr/local/share/Music
    # drwxr-xr-x 2 root music 4096 Jul 15 01:13 /usr/local/share/Music

    # 为 music group 对 Music 增加 w 权限
    sudo chmod 775 /usr/local/share/Music
    ls -ld /usr/local/share/Music
    # drwxrwxr-x 2 root music 4096 Jul 15 01:13 /usr/local/share/Music

    > /usr/local/share/Music/test_file
    # Music 下新建的 test_file 属于 bill
    ls -l /usr/local/share/Music
    # -rw-r--r-- 1 bill bill 0 Jul 15 01:17 test_file
    ```

    - Each file and directory created by one member will be set to the **primary group** of the user rather than the group `music`. This can be fixed by setting the `setgid` bit on the directory
    	
        ```bash
        ## bill
        # newly created files in the directory will be given the group ownership of the directory (music here) rather the group ownership of the file’s creator
        sudo chmod g+s /usr/local/share/Music
        ls -ld /usr/local/share/Music
        # drwxrwsr-x 2 root music 4096 Jul 15 01:17 /usr/local/share/Music

        rm /usr/local/share/Music/test_file
        # Music 下新建的 test_file 属于 music
        > /usr/local/share/Music/test_file
        ls -l /usr/local/share/Music/test_file
        # -rw-r--r-- 1 bill music 0 Jul 15 01:20 /usr/local/share/Music/test_file

        # bill 在 Music 下创建的目录 bill_music，music 组其它成员无写入权限 (umask 对新建的目录也起作用)
        sudo mkdir /usr/local/share/Music/bill_music
        ls -ld /usr/local/share/Music/bill_music
        # drwxr-sr-x 2 root music 4096 Jul 15 01:27 /usr/local/share/Music/bill_music
        ```
    
    - The default `umask` on this system is 0022, which prevents group members from **writing files** belonging to other members of the group (目的是共享音乐文件，不是编辑音乐文件，故不能写 `music` 组其它成员创建的文件无妨). This would not be a problem if the shared directory contained only files, but because this directory will store music, and music is usually organized in a hierarchy of artists and albums, members of the group will need the ability to create files and directories inside directories created by other members
    	
        ```bash
        rm /usr/local/share/Music/test_file
        rm -rf /usr/local/share/Music/bill_music
        umask 0002
        > /usr/local/share/Music/test_file
        mkdir /usr/local/share/Music/bill_dir
        ls -l /usr/local/share/Music
        # drwxrwsr-x 2 bill music 4096 Jul 15 01:35 bill_dir
        # -rw-rw-r-- 1 bill music    0 Jul 15 01:34 test_file

        # 加 sudo 后 owner 就是 root，不加是 bill
        sudo mkdir /usr/local/share/Music/bill_dir2
        sudo touch /usr/local/share/Music/test_file2
        ls -l /usr/local/share/Music
        # drwxr-sr-x 2 root music 4096 Jul 15 01:37 bill_dir2
        # -rw-r--r-- 1 root music    0 Jul 15 01:39 test_file2
        ```
    
    Original file mode | --- rw- rw- rw-
    --|--| 
    Mask | 000 000 000 010
    Result | &nbsp;&nbsp;&nbsp;&nbsp;--- rw- rw- r--
    |

- The one remaining issue is `umask`. The necessary setting lasts only until the end of session and must be reset
## Changing password
- Setting passwords for yourself (and for other users if you have access to superuser privileges)
	
    ```bash
    passwd [user]
    ```

    - To change your password, just enter the `passwd` command
    - If you have superuser privileges, you can specify a username as an argument to the `passwd` command to set the password for another user
    - The `passwd` command will try to enforce use of “strong” passwords. This means it will refuse to accept passwords that are too short, are too similar to previous passwords, are dictionary words, or are too easily guessed
- Other options are available to the superuser to allow account locking, password expiration, and so on
## Summing up
- The basic ideas of Unix-like system of permissions date back to the early days of Unix and have stood up pretty well to the test of time. But the native permissions mechanism in Unix-like systems lacks the fine granularity of more modern systems