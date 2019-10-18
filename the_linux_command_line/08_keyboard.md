<!-- review 2019-10-17 14:16:00 -->
- Completion of pathnames is its most common use。 Completion will also work on variables (if the beginning of the word is a `$`), usernames (if the word begins with `~`), commands (if the word is the first word on the line), and hostnames (if the beginning of the word is `@`)
    - Hostname completion works only for hostnames listed in /etc hosts
- Perform a reverse incremental history command search
    - `ctrl+r` and type in
    - `ctrl+r` again to find next occurrence
    - `ctrl+j` to copy target command into command line or press enter to execute it
    - `ctrl+c` to exit
## Command line editing
- `bash` uses a library called `Readline` to implement command line editing
### Cursor movement
- > Some of the key sequences covered in this chapter (particularly those that use the alt key) may be intercepted by the GUI for other functions. All of the key sequences should work properly when using a virtual console
### Modify text

Key | Action |
--|--|
`ctrl-D` | Delete the character at the cursor location.
`ctrl-T` | Transpose (exchange) the character at the cursor location with the one preceding it.
`alt-T` | Transpose the word at the cursor location with the one preceding it.
`alt-L` | Convert the characters from the cursor location to the end of the word to lowercase.
`alt-U` | Convert the characters from the cursor location to the end of the word to upper
| |

### Cutting and pasting (killing and yanking) text
- The `Readline` documentation uses the terms *killing* and *yanking* to refer to what we would commonly call cutting and pasting
- Items that are cut are stored in a buffer (a temporary storage area in memory) called the *kill-ring*

Key | Action |
--|--|
`ctrl-K` | Kill text from the cursor location to the end of line.
`ctrl-U` | Kill text from the cursor location to the beginning of the line.
`alt-D` | Kill text from the cursor location to the end of the current word.
`alt-backspace` | Kill text from the cursor location to the beginning of the current word. If the cursor is at the beginning of a word, kill the previous word.
`ctrl-Y` | Yank text from the kill-ring and insert it at the cursor location.
| |

- On modern keyboards, *meta key* maps to the `alt` key; however, this wasn’t always the case
    - > Back in the dim times (before PCs but after Unix), not everybody had their own computer. What they might have had was a device called a terminal. A terminal was a communication device that featured a text display screen and a keyboard and just enough electronics inside to display text characters and move the cursor around. It was attached (usually by serial cable) to a larger computer or the communication network of a larger computer. There were many different brands of terminals, and they all had different keyboards and display feature sets. Because they all tended to at least understand ASCII, software developers wanting portable applications wrote to the lowest common denominator. Unix systems have an elaborate way of dealing with terminals and their different display features. Because the developers of `Readline` could not be sure of the presence of a dedicated extra control key, they invented one and called it *meta*. While the `alt` key serves as the meta key on modern keyboards, you can also **press and release the `esc` key to get the same effect** as holding down the alt key if you’re still using a terminal (which you can still do in Linux!)
## Completion
- Completion occurs when you press the tab key while typing a command
- For completion to be successful, the “clue” you give it has to be unambiguous
- Completion of pathnames is its most common use
- Completion will also work on variables (if the beginning of the word is a `$`), usernames (if the word begins with `~`), commands (if the word is the first word on the line), and hostnames (if the beginning of the word is `@`)
    - Hostname completion works only for hostnames listed in /etc hosts

Key | Action |
--|--|
`alt-?` | Display a list of possible completions. On most systems, you can also do this by **pressing the tab key a second time**, which is much easier.
`alt-*` | Insert all possible completions. This is useful when you want to use more than one possible match.
| |

- Recent versions of bash have a facility called *programmable completion*. Programmable completion allows you (or more likely, your distribution provider) to add more completion rules. Usually this is done to add support for specific applications
    - For example, it is possible to add completions for the option list of a command or match particular file types that an application supports
    - Ubuntu has a fairly large set defined by default
    - Programmable completion is implemented by shell functions (a kind of mini shell script)
    	
        ```bash
        set | less
        ```

## Using history
- A history of commands that  is kept in your home directory in a file called `bash_history`
### Searching history
- `history | less`
- By default, bash stores the last 500 commands we have entered, though most modern distributions set this value to 1,000
- `history | grep /usr/bin`
- `!8`
    - The `8` is the line number of the command in the history list
    - `bash` will expand `!8` into the contents of the 8th line in the history list
- `bash` also provides the ability to search the history list incrementally. This means we can tell `bash` to search the history list as we enter characters, with each additional character further refining our search
    - To start incremental search, press `ctrl-R` followed by the text you are looking for. The prompt changes to indicate that we are performing a reverse incremental search. 
        - It is “reverse” because we are searching from “now” to sometime in the past
    - When you find it, you can either press **enter** to execute the command or press `ctrl-J` to copy the line from the history list to the current command line
    - To find the next occurrence of the text (moving “up” the history list), press `ctrl-R` again
    - To quit searching, press either `ctrl-G` or `ctrl-C`
- Some of the keystrokes used to manipulate the history list

    Key | Action |
    --|--|
    `ctrl-P` | Move to the previous history entry. This is the same action as the up arrow.
    `ctrl-N` | Move to the next history entry. This is the same action as the down arrow.
    `alt-<` | Move to the beginning (top) of the history list.
    `alt->` | Move to the end (bottom) of the history list, i.e., the current command line.
    `ctrl-R` | Reverse incremental search. This searches incrementally from the current command line up the history list.
    `alt-P` | Reverse search, nonincremental. With this key, type in the search string and press enter before the search is performed.
    `alt-N` | Forward search, nonincremental.
    `ctrl-O` | Execute the current item in the history list and advance to the next one. This is handy if you are trying to re-execute a sequence of commands in the history list.
    | |

### History expansion
- The shell offers a specialized type of expansion for items in the history list by using the `!` character

    Key | Action |
    --|--|
    `!!` | Repeat the last command. It is probably easier to press the up arrow and enter.
    `!number` | Repeat history list item number.
    `!string` | Repeat last history list item starting with string.
    `!?string` | Repeat last history list item containing string.
    | |

    - `sudo !!`
    - > I caution against using the `!string` and `!?string` forms unless you are absolutely sure of the contents of the history list items
- In addition to the command history feature in bash, most Linux distributions include a program called `script` that can be used to **record an entire shell session** and store it in a file
	
    ```bash
    script fileName
    ```

    - `ctrl+d` to stop recording
    - If no file is specified, the file `typescript` is used