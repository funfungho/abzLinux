<!-- review 2019-10-15 11:35:21 -->
- Three periods following an argument in the description of a command that the argument can be ***repeated***
- Wildcards can be used with any command that accepts filenames as arguments
- `cp -u`: copy only when the SOURCE file is newer than the destination file or when the destination file is missing
- If `-i` option is not specified, `cp` will silently (meaning there will be no warning) overwrite files
# Manipulating files and directories
## Wildcards
- Wildcards (globbing)

    Wildcard | Meaning |
    --|--|
    `*` | Matches any characters |
    `?` | Matches any single character |
    `[characters]` | Matches any character that is a member of the set `characters` |
    `[!characters]` | Matches any character that is not a member of the **set** `characters` |
    `[[:class:]]` | Matches any character that is a member of the specified `class` |
    | |

- Commonly used *character class*

    Character class | Meaning |
    --|--|
    [:alnum:] | Matches any alphanumeric character |
    [:alpha:] | Matches any alphabetic character |
    [:digit:] | Matches any numeral |
    [:lower:] | Matches any lowercase letter |
    [:upper:] | Matches any uppercase letter |
    | |

    - `[A-Z]` and `[a-z]` character range notations are traditional Unix notations and worked in older versions of Linux. They can still work, but you have to be careful with them because they will not produce the expected results unless properly configured. Avoid using them and use character classes instead
- Examples

    Pattern | Matches |
    --|--|
    `*` | All files |
    `g*` | Any file beginning with `g` | 
    `b*.txt` | Any file beginning with `b` followed by any characters and ending with `.txt` |
    `Data???` | Any file beginning with `Data` followed by exactly three characters |
    `[abc]*` | Any file beginning with either an `a`, a `b`, or a `c` |
    `BACKUP.[0-9][0-9][0-9]` | Any file beginning with `BACKUP.` followed by exactly three numerals |
    `[[:upper:]]*` | Any file beginning with an uppercase letter |
    `[![:digit:]]*` | Any file not beginning with a numeral |
    `*[[:lower:]123]` | Any file ending with a lowercase letter or the numerals 1, 2, or 3 |

- Wildcards work in the GUI, too
- Wildcards can be used with any command that accepts filenames as arguments
## `mkdir`
- `mkdir directory...`
	
    ```bash
    mkdir dir1 dir2
    ```

    - Three periods following an argument in the description of a command that the argument can be ***repeated***
## `cp`
- `cp` copies files or directories
    - `cp item1 item2` copies the single file or directory `item1` to the file or directory `item2`
    - `cp item... directory` copies multiple items (either files or directories) into a directory
- Useful options

    Option | Meaning |
    --|--|
    `-a`, `--archive` | Copy the files and directories and all of their attributes, including ownerships and permissions. <br> Normally, copies take on the default attributes of the user performing the copy |
    `-i`, `--interactive` | Before overwriting an existing file, prompt the user for confirmation. If this option is not specified, `cp` will **silently** (meaning there will be no warning) **overwrite** files |
    `-r`, `--recursive` | Recursively copy directories and their contents. This option (or the `-a` option) is required when copying directories |
    `-u`, `--update` | When copying files from one directory to another, only copy files that either don’t exist or are newer than the existing corresponding files in the destination directory <br> This is useful when copying large numbers of files as it skips files that don’t need to be copied |
    | |

- Responding to the prompt by entering a `y` will cause the file to be overwritten; any other character (for example, `n`) will cause `cp` to leave the file alone
- Examples

    Command | Results |
    --|--|
    `cp file1 file2` | Copy `file1` to `file2`. If `file2` exists, it is overwritten with the contents of `file1`. If file2 does not exist, it is created | 
    `cp -i file1 file2` | Same as previous command, except that if `file2` exists, the user is prompted before it is overwritten | 
    `cp file1 file2 dir1` | Copy `file1` and `file2` into directory `dir1`. The directory `dir1` must already exist |
    `cp dir1/* dir2` | Using a wildcard, copy all the files in `dir1` into `dir2`. The directory `dir2` must already exist |
    `cp -r dir1 dir2` | Copy the contents of directory dir1 to directory dir2. <br> If directory `dir2` does not exist, it is created and, after the copy, will contain the same contents as directory `dir1`. <br> If directory `dir2` does exist, then directory `dir1` (and its contents) will be copied into `dir2` |
    | |

## `mv`
- `mv` performs both file moving and file renaming
    - `mv item1 item2` moves or renames the file or directory `item1` to `item2`
    - `mv item... directory` moves one or more items from one directory to another
- In either case, the original filename no longer exists after the operation `mv` is used in much the same way as `cp`
- Useful options

    Option | Meaning |
    --|--|
    `-i`, `--interactive` | Before overwriting an existing file, prompt the user for confirmation. If this option is not specified, `mv` will **silently overwrite** files |
    `-u`, `--update` | When moving files from one directory to another, only move files that either don’t exist or are newer than the existing corresponding files in the destination directory |
    `-v`, `--verbose` | Display informative messages as the move is performed |
    | |
- Examples

    Command | Results |
    --|--|
    `mv file1 file2` | Move `file1` to `file2`. If `file2` exists, it is **overwritten** with the contents of `file1`. If `file2` does not exist, it is created. In either case, `file1` ceases to exist |
    `mv -i file1 file2` | Same as the previous command, except that if `file2` exists, the user is prompted before it is overwritten |
    `mv file1 file2 dir1` | Move `file1` and `file2` into directory `dir1`. The directory `dir1` must already exist |
    `mv dir1 dir2` | If directory `dir2` does not exist, create directory `dir2` and move the contents of directory `dir1` into `dir2` and delete directory `dir1`. If directory `dir2` does exist, move directory `dir1` (and its contents) into directory `dir2` |
    | |
## `mkdir`
- `rm item...`
- Used to remove (delete) files and directories
- Useful options

    Option | Meaning |
    --|--|
    `-i`, `--interactive` | Before deleting an existing file, prompt the user for confirmation. If this option is not specified, `rm` will **silently** delete files | 
    `-r`, `--recursive` | Recursively delete directories. This means that if a directory being deleted has subdirectories, delete them too. To delete a directory, this option must be specified |
    `-f`, `--force` | Ignore nonexistent files and do not prompt. This **overrides** the `--interactive` option |
    `-v`, `--verbose` | Display informative messages as the deletion is performed |
    | |

- Unix-like operating systems such as Linux do not have an undelete command. Once you delete something with `rm`, it’s gone
    - Linux assumes you’re smart and you know what you’re doing
    - `rm * .html`
        - the `rm` command will delete all the files in the directory and then **complain** that there is no file called `.html`
- A **useful tip**: whenever you use wildcards with `rm` (besides carefully checking your typing!), test the wildcard first with `ls`
    - This will let you see the files that will be deleted
    - Then press the up arrow to recall the command and replace `ls` with `rm`
- Examples

    Command | Results |
    --|--|
    `rm file1` | Delete `file1` silently |
    `rm -i file1` | Same as the previous command, except that the user is prompted for confirmation before the deletion is performed |
    `rm -r file1 dir1` | Delete `file1` and `dir1` and its contents |
    `rm -rf file1 dir1` | Same as the previous command, except that if either `file1` or `dir1` does not exist, `rm` will continue **silently** |
    | |

## `ln`
- Used to create either hard or symbolic links
    - `ln file link` creates a hard link
    - `ln -s item link` creates a symbolic link. `item` is either a file or a directory
### Hard links
- Hard links are the original Unix way of creating links, compared to symbolic links, which are more modern
- **By default, every file has a single hard link that gives the file its name**
- When we create a hard link, we create an additional directory entry for a file
- Hard links have two important limitations
    - A hard link cannot reference a file outside its own file system. This means a link cannot reference a file that is not on the same disk partition as the link itself
    - A hard link may not reference a directory
- **A hard link is *indistinguishable* from the file itself**
    - Unlike a symbolic link, when you list a directory containing a hard link, you will see no special indication of the link
- When a hard link is deleted, the link is removed, but the contents of the file itself continue to exist (that is, its space is not deallocated) until all links to the file are deleted
- It is important to be aware of hard links because you might encounter them from time to time, but modern practice prefers symbolic links
### Symbolic links
- Symbolic links are a special type of file that contains a **text pointer** to the target file or directory
    - > In this regard, they operate in much the same way as a Windows shortcut, though of course they predate the Windows feature by many years
- A file pointed to by a symbolic link and the symbolic link itself are **largely indistinguishable** from one another
    - For example, if you write something to the symbolic link, the referenced file is written to
- When you delete a symbolic link, however, only the link is deleted, not the file itself
- If the file is deleted before the symbolic link, the link will continue to exist but will point to nothing
    - In this case, the link is said to be *broken*
    - In many implementations, the `ls` command will display broken links in a **distinguishing color**, such as red, to reveal their presence
- Symbolic links were created to overcome the limitations of hard links
## Playground
### Hard links
- When thinking about hard links, it is helpful to imagine that files are made up of 2 parts
    - The data part containing the file’s contents
    - The name part that holds the file’s name
- When we create hard links, we are actually creating **additional name parts** that all **refer to the same data part**
- 同一 item 的不同 hard link 可以理解成指向（存放）同一个地址（inode）的指针变量
- **编辑 hard link 也会改变其指向的 item**
- The system assigns a chain of disk blocks to what is called an inode, which is then associated with the name part
    - Each hard link therefore refers to a specific inode
- `ls -i` 显示 inode
- inode 号相同表示是同一个 item
#### Inode
- An inode is a **data structure** on a traditional Unix-style file system such as UFS or ext3
- An inode stores **basic information** about a regular file, directory, or other file system object
- Each and every file under Linux (and UNIX) has following attributes
    - File type (executable, block special etc)
    - Permissions (read, write etc)
    - Owner
    - Group
    - File Size
    - File access, change and modification time
    - File deletion time
    - Number of links (soft/hard)
    - Extended attribute such as append only or no one can delete file including root user (immutability)
    - Access Control List (ACLs)
- All the above information stored in an inode
    - In short the inode identifies the file and its attributes (as above)
- Each inode is **identified by a unique inode number** within the file system
    - Inode is also know as index number
### Symbolic links
- When we create a symbolic link, we are creating a text description of where the **target file** is **relative to the symbolic link**
	
    ```bash
    ln -s fun fun-sym
    ln -s ../fun dir1/fun-sym

    ls -l dir1
    # lrwxrwxrwx 1 me me 6 2018-01-15 15:17 fun-sym -> ../fun
    ```

    - The leading `l` in the first field means it's a symbolic link
    - The length of the symbolic link file is 6, the number of characters in the string `../fun` rather than the length of the file to which it is pointing
- Can use absolute path to create symbolic links too
	
    ```bash
    ln -s /home/me/playground/fun dir1/fun-sym
    ```

- In most cases, using relative pathnames is more desirable because it allows a directory tree **containing** symbolic links and their referenced files to be renamed and/or moved without breaking the links
- Symbolic links can also reference directories
	
    ```bash
    ln -s dir1 dir1-sym
    ```

- Most file operations are carried out on the link’s **target**, not the link itself
    - `rm` is an exception. When you delete a link, it is the link that is deleted, not the target