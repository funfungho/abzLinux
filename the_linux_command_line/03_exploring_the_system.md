- `ls ~ /usr -lt --reverse -hF`: specify multiple directories, output in the long format, sort the result by the file’s modification time, reverse the order of the sort, human readable, append an indicator character to the end of each listed name (a forward slash `/` if the name is a directory)
- `ls -dl`: see details about the directory rather than its contents
    - > Ordinarily, `ls` will list the contents of the directory, not the directory itself
- `file`: determines file type
- `less`: view text file contents
- `reset`: terminal initialization
# Exploring the system
- Commands are often followed by one or more *options* that **modify their behavior** and, further, by one or more *arguments*, the items upon which the command **acts**
- `command -options arguments`
    - Most commands use options, which consist of a **single character** preceded by a dash `-`
        - Many commands allow multiple short options to be **strung** together
    - Many commands including those from the GNU Project, also support long options, consisting of a **word** preceded by two dashes (`--`)
- Command options are case sensitive

- `drwxr-xr-x   2 root root 4096 Apr 25 19:11 bin/`

    Field | Meaning |
    --|--|
    -rw-r--r-- | Access rights to the file. The first character indicates the type of file. A leading dash `-` means a regular file; a `d` indicates a directory. The next 3 characters are the access rights for the file’s **owner**, the next 3 are for **members** of the file’s group, and the final three are for **everyone else** |
    1 | File’s number (数目) of hard links |
    root | The username of the file’s owner |
    root | The name of the group that owns the file |
    4096 | Size of the file in **bytes** |
    bin/ | Name of the directory |

- One of the common ideas in Unixlike operating systems such as Linux is that **"everything is a file"**
- `less` command is a program to view **text files**
    - Text is a simple one-to-one mapping of characters to numbers

    Command | Action |
    --|--|
    b | Scroll back one page |
    space | Scroll forward one page |
    Up arrow | Scroll up one line |
    Down arrow | Scroll down one line |
    G | Move to the end of the text file |
    1G or g | Move to the beginning of the text file |
    `/` followed by searched characters | Search forward to the next occurrence of characters |
    n | Search for the next occurrence of the previous search |
    **ESC+u** | toggle search highlight |
    **h** | Display help screen |

    - If we accidentally attempt to view a non-text file and it scrambles the terminal window, we can recover by entering the `reset` command
    - > The `less` program was designed as an improved replacement of an earlier Unix program called `more`. The name `less` is a play on the phrase "less is more" — a motto of modernist architects and designers. `less` falls into the class of programs called pagers, programs that allow the easy viewing of long text documents in a page-by-page manner. Whereas the `more` program could only page forward, the less program allows paging both forward and backward and has many other features as well
- `lrwxrwxrwx 1 root root 11 2018-08-11 07:34 libc.so.6 -> libc-2.6.so`
    - `l` and 2 filenames mean it's a symbolic link
    - Programs looking for `libc.so.6` will actually get the file `libc-2.6.so`
- A symbolic link (also known as a soft link or symlink)
    - In most Unix-like systems, it is possible to have a file referenced by multiple names
    - > Scenario
        - A program requires the use of a shared resource of some kind contained in a file named “foo”, but “foo” has frequent version changes. It would be good to include the version number in the filename so the administrator or other interested party could see what version of “foo” is installed. This presents a problem. If we change the name of the shared resource, we have to **track down** every program that might use it and change it to look for a new resource name every time a new version of the resource is installed
        - Suppose we install version 2.6 of “foo”, which has the filename “foo-2.6”, and then create a symbolic link simply called “foo” that points to “foo-2.6”. This means that when a program opens the file “foo”, it is actually opening the file “foo-2.6”. The programs that rely on “foo” can find it, and we can still see what actual version is installed. When it is time to upgrade to “foo-2.7”, we just add the file to our system, delete the symbolic link “foo”, and create a new one also named “foo” that points to the new version
- Hard links also allow files to have multiple names