# Documentation for commands
- A command can be one of 4 things
    1. An **executable program** like all those files in `usr/bin`
        - Programs can be compiled binaries such as programs written in C and C++, or programs written in scripting languages such as the shell, Python
    2. A command build into the shell itself (**shell builtins**)
        - `bash` supports a number of commands internally called shell builtins (`cd`)
    3. A shell function
        - Shell functions are miniature shell scripts incorporated into the environment
    4. An alias
        - Aliases are commands that we can define ourselves, built from other commands
## Identifying commands
### `type` - display a command's **type**
- The `type` command is a shell builtin that displays the kind of command the shell will execute
	
    ```bash
    type type   # type is a shell builtin
    type ls     # ls is aliased to `ls --color=auto'
    type cp     # cp is /bin/cp
    ```

### `which` - displays an executable's **location**
- Sometimes there is more than 1 version of an executable program installed on a system
- `which` is used to determined the exact location of a given executable
	
    ```bash
    which ls    # /bin/ls
    ```

- `which` works only for executable programs, not builtins or aliases that are substitutes for actual executable programs
	
    ```bash
    which cd
    ```

## Getting a command's documentation
### `help` - get help for **shell builins**
- `bash` has an builtin facility available for each of the shell builtins
	
    ```bash
    help cd
    ```

- When **square brackets** appear in the description of a command's syntax, they indicate **optional items**
- A vertical bar character indicates mutually exclusive items
    `cd [-L|[-P[-e]]] [dir]` says `cd` may be followed optionally by either a `-L` or a `-P` and further, if the `-P` option is specified then the `-e` option may also be included followed by the optional argument `dir`
### `--help` - display usage information
- Many **executable programs** support a `--help` option that displays a description of the command's supported syntax and options
	
    ```bash
    mkdir --help
    ```

    - > Some programs doesn't support the `--help` option, but try it anyway. Often it results in an error message that will reveal the same usage information
### `man` - display a program's manual page
- Most executable programs intended for command line use provide a formal piece of documentation called a manual page or man page
- A special paging program called `man` is used to view them
	
    ```bash
    man program_name
    ```

- `man` program do not usually include examples and are intended as a reference, not a tutorial
- On most Linux systems, `man` uses `less` to display the manual page, so all of the familiar `less` commands work while displaying the page
- The “manual” that man displays is broken into sections and covers not only user commands but also system administration commands, programming interfaces, file formats, and more

    Section | Contents |
    --|--|
    1 | User commands |
    2 | Programming interfaces for kernel system calls |
    3 | Programming interfaces to the C library |
    4 | Special files such as device nodes and drivers |
    5 | File formats |
    6 | Games and amusements such as screen savers |
    7 | Miscellaneous |
    8 | System administration commands |
    | |

- Sometimes we need to refer to a specific section of the manual to find what we are looking for
    - This is particularly true if we are looking for a file format that is also the name of a command. Without specifying a section number, we will always get the first instance of a match, probably in section 1
- Specify a section number
	
    ```bash
    man section_number search_term
    ```

- `man 5 passwd` displays the man page describing the file format of the `/etc/passwd` file
### `apropos` - displays appropriate commands
- It is also possible to search the list of man pages for possible matches based on a search term
	
    ```bash
    apropos partition
    ```

    - `man -k` performs the same function as `apropos`
### `whatis` - displays one-line manual page descriptions
- The whatis program displays the name and a one-line description of a man page matching a specified keyword
	
    ```bash
    whatis ls
    ```

### `info` - display a program's info entry
- The GNU Project provides an alternative to man pages for their programs, called `info`
- Info pages are hyperlinked much like web pages
- The `info` program reads info files, which are tree structured into individual nodes, each containing a single topic
- Info files contain hyperlinks that can move you from node to node
- A hyperlink can be identified by its **leading asterisk** and is activated by placing the cursor upon it and pressing enter
- Commands used to control the reader while displaying an info page

    Command | Action |
    --|--|
    ? | Display command help |
    page up or backspace | Display previous page |
    page down or spacebar | Display next page |
    n | Next—display the next node |
    p | Previous—display the previous node |
    U | Up—display the parent node of the currently displayed
    node, usually a menu |
    enter | Follow the hyperlink at the cursor location |
    Q | Quit |
    | |

- Most of the command line programs discussed so far are part of the GNU Project’s `coreutils` package
    - `info coreutils` displays a menu page with hyperlinks to each program contained in the `coreutils` package
### README and other program documentation files
- Many software packages installed on the system have documentation files residing in the `/usr/share/doc` directory
    - Most of these are stored in ordinary text format and can be viewed with the `less` command
    - Some of the files are in HTML format and can be viewed with a web browser
    - We may encounter some files ending with a `.gz` extension. This indicates that they have been compressed with the `gzip` compression program
        - The gzip package includes a special version of `less` called `zless` that will display the contents of gzip-compressed text files
## Creating our own commands with `alias`
- It’s possible to put more than one command on a line by separating each command with a **semicolon**
	
    ```bash
    cd /usr; ls; cd -
    ```

- Create alias
	
    ```bash
    # check if the name has been used
    type foo
    alias foo='cd /usr; ls; cd -'
    type foo
    unalias foo
    ```

- It's common to name alias with an existing command name
    - This is often done to apply a commonly desired option to each invocation of a common command (`type ls`)
- Use `alias` without arguments to see all the aliases defined in the environment
- Aliases on the command line vanish when the shell session ends