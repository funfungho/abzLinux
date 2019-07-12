# `locate` (find files the easy way)
- The locate program performs a rapid database search of **pathnames** and then outputs every name that matches a given **substring**
	
    ```bash
    locate bin/zip
    locate zip | grep bin
    ```

- The `locate` program has been around for a number of years, and there are several variants in common use. The 2 most common ones found in modern Linux distributions are `slocate` and `mlocate`, though they are usually accessed by a symbolic link named `locate`
    - The different versions of `locate` have overlapping options sets
    - Some versions include regular expression matching and wildcard support
    - Check the man page for `locate` to determine which version of locate is installed
        - `locate --version`
- On some distributions, `locate` fails to work just after the system is installed, but if you try again the next day, it works fine. The `locate` database is created by another program named `updatedb`. Usually, it is run periodically as a cron job, that is, a task performed at regular intervals by the cron daemon
    - Most systems equipped with `locate` run `updatedb` once a day. Because the database is not updated continuously, you will notice that very recent files do not show up when using `locate`. To overcome this, it’s possible to run the `updatedb` program manually by becoming the superuser and running `updatedb` at the prompt
# `find`
- While the `locate` program can find a file based solely on its name, the `find` program searches a given directory (and its subdirectories) for files based on a variety of attributes
- In its simplest use, `find` is given one or more names of directories to search
	
    ```bash
    # a list of every file and subdirectory contained within home directory
    find ~
    # print the newline counts
    find ~ | wc -l
    ```

- The beauty of `find` is that it can be used to identify files that meet specific criteria, through the (slightly strange) application of *options*, *tests*, and *actions*
## Tests
- Adding the test `-type d` limited the search to directories
	
    ```bash
    find ~ -type d | wc -l
    # limit to regular files
    find ~ -type f | wc -l
    ```

- `find` file types

    File type | Description |
    --|--|
    `b` | Block special device file
    `c` | Character special device file
    `d` | Directory
    `f` | Regular file
    `l` | Symbolic link
    | |

- We can also search by file size and filename by adding some additional tests
	
    ```bash
    find ~ -type f -name "*.JPG" -size +1M | wc -l
    ```

    - `*.JPG` is enclosed in quotes to prevent pathname expansion (in order to be passed as arguments)
    - The leading plus sign indicates that we are looking for files larger than the specified number. A leading minus sign would change the meaning of the string to be smaller than the specified number. Using **no sign** means “match the value exactly”
    - `find` size units

    Character | Unit
    --|--|
    `b` | 512-byte blocks. This is the default if no unit is specified.
    `c` | Bytes.
    `w` | 2-byte words.
    `k` | Kilobytes (units of 1,024 bytes).
    `M` | Megabytes (units of 1,048,576 bytes).
    `G` | Gigabytes (units of 1,073,741,824 bytes).
    | |    

- Common tests
    - In cases where a numeric argument is required, the same + and - notation can be applied
    
    Test | Description |
    --|--|
    `-cmin n` | Match files or directories whose content or attributes were last modified exactly `n` minutes ago. To specify less than `n` minutes ago, use `-n`, and to specify more than n minutes ago, use `+n`.
    `-cnewer file` | Match files or directories whose contents or attributes were last modified more recently than those of file.
    `-ctime n` | Match files or directories whose contents or attributes were last modified `n`*24 hours ago.
    `-empty` | Match empty files and directories.
    `-group name` | Match file or directories belonging to belonging to group `name`. `name` may be expressed may be expressed either as a group name or as a numeric group ID.
    `-iname` | pattern Like the `-name` test but **case-insensitive**.
    `-inum n` | Match files with inode number `n`. This is helpful for finding all the hard links to a particular inode.
    `-mmin n` | Match files or directories whose contents were last modified `n` minutes ago.
    `-mtime n` | Match files or directories whose contents were last modified `n`*24 hours ago.
    `-name pattern` | Match files and directories with the specified wildcard pattern.
    `-newer file` | Match files and directories whose contents were modified more recently than the specified `file`. This is useful when writing shell scripts that perform file backups. Each time you make a backup, update a file (such as a log) and then use `find` to determine which files have changed since the last update.
    `-nouser` | Match file and directories that do not belong to a valid user. This can be used to find files belonging to deleted accounts or to detect activity by attackers.
    `-nogroup` | Match files and directories that do not belong to a valid group.
    `-perm mode` | Match files or directories that have permissions set to the specified `mode`. `mode` can be expressed by either octal or symbolic notation.
    `-samefile name` | Similar to the `-inum` test. Match files that share the same inode number as file name.
    `-size n` | Match files of size `n`.
    `-type c` | Match files of type `c`.
    `-user name` | Match files or directories belonging to user `name`. The user may be expressed by a username or by a numeric user ID.
    | |

## Operators
- Even with all the tests that `find` provides, we might still need a better way to describe the logical relationships between the tests
- `find` provides a way to combine tests using *logical operators* to create more complex logical relationships
- Determine whether all the files and subdirectories in a directory had secure permissions (look for all the files with permissions that are not 0600 and the directories with permissions that are not 0700)
	
    ```bash
    # In the case of files, we define good permissions as 0600, and for directories, we define it as 0700
    # `-type f -and -not -perms 0600`: `-and` operator safely removed here
    find ~ \( -type f -not -perm 0600 \) -or \( -type d -not -perm 0700 \)
    ```

- `find` logical operators

    Operator | Description |
    --|--
    `-and` | Match if the tests on both sides of the operator are true. This can be shortened to `-a`. Note that when no operator is present, `-and` is implied by **default**.
    `-or` | Match if a test on either side of the operator is true. This can be shortened to `-o`.
    `-not` | Match if the test following the operator is false. This can be abbreviated with an exclamation point (!).
    `( )` | Group tests and operators together to form larger expressions. This is used to control the precedence of the logical evaluations. By default, `find` evaluates from left to right. It is often necessary to override the default evaluation order to obtain the desired result. Even if not needed, it is helpful sometimes to include the grouping characters to improve the readability of the command. Note that since the parentheses have special meaning to the shell, they must be quoted when using them on the command line to allow them to be passed as arguments to `find`. Usually the backslash character is used to escape them.
    | |

- 2 expressions separated by a logical operator
	
    ```bash
    expr1 -operator expr2
    ```

    - In all cases, `expr1` will always be performed. `expr2` might be short-circuited
## Predefined actions
- `find` allows actions to be performed based on the search results
- A few predefined `find` actions

    Action | Description |
    --|--|
    `-delete` | Delete the currently matching file.
    `-ls` | Perform the equivalent of `ls -dils` on the matching file. Output is sent to standard output.
    `-print` | Output the full pathname of the matching file to standard output. This is the **default** action if no other action is specified.
    `-quit` | Quit once a match has been made.
    | |

- Delete files that have the file extension `.bak` (which is often used to designate backup files)
	
    ```bash
    find ~ -type f -name '*.bak' -delete
    ```

    - This command performs the way it does is determined by the logical relationships between each of the tests and actions
        - There is, by default, an implied `-and` relationship between each test and action
        	
            ```bash
            # equivalent to
            find ~ -type f -and -name '*.log' -and -print
            ```
        
            - `-type f` Is always performed, since it is the first test/action in an `-and` relationship
            - `-name '*.bak'` is performed only if -type f is true
            - `-print` is performed only if `-type f` and `-name '*.bak'` are true
        - Because the logical relationship between the tests and actions determines which of them are performed, the **order** of the tests and actions is important
        	
            ```bash
            find ~ -print -and -type f -and -name '*.log'
            ```
        
            - This command will print each file (the `-print` action always evaluates to true) and then test for file type and the specified file extension ([ ] won't print the search result separately)
    - Always test the command first by substituting the `-print` action for `-delete` to confirm the search results
## User-defined actions
- Invoke arbitrary commands with the `-exec` action
	
    ```bash
    -exec command {};

    -exec rm '{}' ';'
    ```

    - `command` is the name of a command, `{}` is a symbolic representation of the **current pathname**, and the semicolon is a required delimiter indicating the end of the command
    - Because the brace and semicolon characters have special meaning to the shell, they must be **quoted or escaped**
- Execute a user-defined action interactively by using the `-ok` action in place of `-exec` (the user is prompted before execution of each specified command)
	
    ```bash
    # prompts the user before the ls command is executed
    find ~ -type f -name 'foo*' -ok ls -l '{}' ';'
    ```

## Improving efficiency
- When the `-exec` action is used, it launches a new instance of the specified command each time a matching file is found. There are times when we might prefer to combine all of the search results and launch a single instance of the command
	
    ```bash
    ls -l file1
    ls -l file2

    # prefer this
    # This causes the command to be executed only one time rather than multiple times
    ls -l file1 file2
    ```

- 2 ways
    1. Traditional way: using the external command `xargs`
    2. Using a new feature in `find` itself
        - By changing the trailing semicolon character to a plus sign, we activate the capability of `find` to combine the results of the search into an argument list for a single execution of the desired command
        	
            ```bash
            find ~ -type f -name 'foo*' -exec ls -l '{}' +
            ```
        
## `xargs`
- `xargs` accepts input from standard input and converts it into an argument list for a specified command
	
    ```bash
    find ~ -type f -name 'foo*' -print | xargs ls -l
    ```

- While the number of arguments that can be placed into a command line is quite large, it’s not unlimited. It is possible to create commands that are too long for the shell to accept
- When a command line exceeds the maximum length supported by the system, `xargs` executes the specified command with the maximum number of arguments possible and then repeats this process until standard input is exhausted (反复截断输入直至读完)
    - To see the maximum size of the command line, execute `xargs` with the `--show-limits` option
- Unix-like systems allow embedded spaces (and even newlines!) in filenames. This causes problems for programs like `xargs` that construct argument lists for other programs. An embedded space will be treated as a delimiter, and the resulting command will interpret each space-separated word as a separate argument
- To overcome this, `find` and `xargs` allow the optional use of a `null` character as an argument separator
    - A `null` character is defined in ASCII as the character represented by the number zero (as opposed to, for example, the space character, which is defined in ASCII as the character represented by the number 32)
    - The `find` command provides the action `-print0`, which produces null-separated output, and the `xargs` command has the `--null` (or `-0`) option, which accepts null-separated input
    	
        ```bash
        find ~ -iname '*.jpg' -print0 | xargs --null ls -l
        ```
    
        - Using this technique, we can ensure that all files, even those containing embedded spaces in their names, are handled correctly
## Practice

```bash
mkdir -p playground/dir-{001..100}
touch playground/dir-{001..100}/file-{A..Z}

find playground -type f -name 'file-A'
find playground -type f -name 'file-A' | wc -l

# create a reference file against which we will compare modification times
# an empty file named timestamp and sets its modification time to the current time
touch playground/timestamp
stat playground/timestamp
# Access: 2019-07-12 03:17:05.035244395 +0000
# Modify: 2019-07-12 03:17:05.035244395 +0000
# Change: 2019-07-12 03:17:05.035244395 +0000
touch playground/timestamp
stat playground/timestamp
# Access: 2019-07-12 05:41:36.372818145 +0000
# Modify: 2019-07-12 05:41:36.372818145 +0000
# Change: 2019-07-12 05:41:36.372818145 +0000

# updates all playground files named file-B
find playground -type f -name 'file-B' -exec touch '{}' ';'

# identify the updated files by comparing all the files to the reference file timestamp
find playground -type f -newer playground/timestamp

# lists all 100 directories and 2,600 files in playground
# because none of them meets our definition of “good permissions”
find playground \( -type f -not -perm 0600 \) -or \( -type d -not -perm 0700 \)
# apply new permissions to the files and directories
find playground \( -type f -not -perm 0600 -exec chmod 0600 '{}' ';' \) -or \( -type d -not -perm 0700 -exec chmod 0700 '{}' ';' \)
```

- The `touch` command is usually used to set or update the access, change, and modify times of files. However, if a filename argument is that of a nonexistent file, an empty file is created
- Unlike `ls`, `find` does not produce results in sorted order. Its order is determined by the layout of the storage device
- The `stat` command (a kind of souped-up version of `ls`) reveals all that the system understands about a file and its attributes
- The important point here is to understand how the operators and actions can be used together to perform useful tasks
## `find` options
- Commonly used `find` options

    Option | Description |
    --|--|
    `-depth` | Direct `find` to process a directory’s files before the directory itself. This option is automatically applied when the `-delete` action is specified.
    `-maxdepth levels` | Set the maximum number of levels that `find` will descend into a directory tree when performing tests and actions.
    `-mindepth levels` | Set the minimum number of levels that `find` will descend into a directory tree before applying tests and actions.
    `-mount` | Direct `find` not to traverse directories that are mounted on other file systems.
    `-noleaf` | Direct `find` not to optimize its search based on the assumption that it is searching a Unix-like file system. This is needed when scanning DOS/Windows file systems and CD-ROMs.
    | |