<!-- review 2019-10-17 10:35:38 -->
- With other types of expansion, if you mistype a pattern, the expansion will not take place, and the `echo` command will simply display the **mistyped pattern**. With parameter expansion, if you misspell the name of a variable, the expansion will still take place but will result in an **empty string**
- Word splitting by the shell removed extra whitespace from the `echo` command’s list of arguments
- Double quotes: all the special characters used by the shell lose their special meaning and are treated as ordinary characters except `$` (dollar sign), `\` (backslash), and backtick &#96;
    - Word splitting, pathname expansion, tilde expansion, and brace expansion are **suppressed**
    - Parameter expansion, arithmetic expansion, and command substitution are still **carried out**
- Use single quotes to **suppress all expansions** (including escaping character `\`)
- Escaping character (`\`) often is done inside double quotes to selectively prevent an expansion. It is also common to use escaping to eliminate the special meaning of a character in a filename to use characters that normally have special meaning to the shell. These would include `$`, `!`, `&`, spaces, and others
## Expansion
- Each time we type a command and press the enter key, bash performs several substitutions upon the text before it carries out our command
    - We have seen a couple of cases of how a simple character sequence, for example `*`, can have a lot of meaning to the shell
- The process that makes this happen is called *expansion*
- With expansion, we enter something, and it is expanded into something else before the shell acts upon it
	
    ```bash
    echo this is a test
    echo *
    ```

    - The `*` character means match any characters in a filename
    - When the enter key is pressed, the shell automatically expands any qualifying characters on the command line before the command is carried out, so the `echo` command never saw the `*`, only its expanded result
### **Pathname** expansion
- The mechanism by which wildcards work is called *pathname expansion*
	
    ```bash
    echo D*
    echo *s
    echo [[:lower:]]*
    echo /usr/*/share
    ```

- Pathname expansion of hidden files
    - Filenames that begin with a period character are hidden
    - Pathname expansion also respects this behavior. `echo *` does not reveal hidden files
    - `echo .*` will also include `.` and `..`
        - `ls -d .* | less` sees better
    - `echo .[!.]*`
        - This pattern expands into every filename that begins with only one period followed by any other characters
            - [ ] 不包含 `.`，是因为不是 `[!.]` 不与 `*` 结合？
    - `ls -A`
        - Do not ignore all file names that start with `.`; ignore only `.` and `..`
### Tilde expansion
- When used at the beginning of a word, tilde `~` expands into the name of the **home directory** of the named user or, if no user is named, the home directory of the current user
	
    ```bash
    echo ~
    echo ~root
    ```

### Arithmetic expansion
- The shell allows arithmetic to be performed by expansion
	
    ```bash
    echo $((2 + 2))
    ```

    - This allows us to use the shell prompt as a calculator
- Arithmetic expansion uses the following form
	
    ```
    $((expression))
    ```

    - `expression` is an arithmetic expression consisting of values and arithmetic operators
        - `/`: Division (but remember, since **expansion supports only integer arithmetic**, results are integers)
        - `%`: Modulo, which simply means “remainder”
        - `**`: Exponentiation
- Spaces are not significant in arithmetic expressions, and expressions may be nested
	
    ```bash
    echo $(($((5**2)) * 3))
    echo $(((5**2) * 3))
    ```

    - Single parentheses may be used to group multiple subexpressions. With this technique, we can rewrite the previous example and get the same result using a single expansion instead of two
- Example
	
    ```bash
    echo Five divided by two equals $((5/2))
    echo with $((5%2)) left over.
    ```

### Brace expansion
- With brace expansion, you can create multiple text strings from a pattern containing braces
	
    ```bash
    # Front-preamble, Back-postscript
    echo Front-{A,B,C}-Back

    echo Number_{1..5}

    # In bash version 4.0 and newer, integers may also be zero-padded (补零)
    echo {01..15}
    echo {001..15}

    # reverse order
    echo {Z..A}

    # nested
    echo a{A{1,2},B{3,4}}b
    ```

    - Patterns to be brace expanded may contain a leading portion called a *preamble* and a trailing portion called a *postscript*
    - The brace expression itself may contain either a comma-separated list of strings or a range of integers or single characters
        - The pattern may not contain unquoted whitespace
- Brace expansions may be nested
- The most common application is to make lists of files or directories to be created
	
    ```bash
    mkdir {2007..2009}-{01..12}
    ```

### Parameter expansion
- It’s a feature that is more useful in shell scripts than directly on the command line
- Many of its capabilities have to do with the system’s ability to store small chunks (called *variables*) of data and to give each chunk a name
	
    ```bash
    echo $USER
    printenv | less
    ```

    - The variable `USER` contains your username
- With other types of expansion, if you mistype a pattern, the expansion will not take place, and the `echo` command will simply display the **mistyped pattern**. With parameter expansion, if you misspell the name of a variable, the expansion will still take place but will result in an **empty string**
### Command substitution
- Command substitution allows us to **use the output of a command as an expansion**
	
    ```bash
    echo $(ls)

    # getting the listing of the cp program without having to know its full pathname
    ls -l $(which cp)

    file $(ls -d /usr/bin/* | grep zip)
    ```

- An alternate syntax for command substitution in older shell programs uses backquotes instead of the dollar sign and parentheses
	
    ```bash
    ls -l `which cp`
    ```

## Quoting

```bash
# word splitting by the shell removed extra whitespace from the echo command’s list of arguments
echo this is a       test

# parameter expansion substituted an empty string for the value of $1 because it was an undefined variable
echo The total is $100.00
# The total is 00.00
```

- The shell provides a mechanism called *quoting* to selectively suppress unwanted expansions
### Double quotes
- If we place text inside double quotes, all the special characters used by the shell lose their special meaning and are treated as ordinary characters. The exceptions are `$` (dollar sign), `\` (backslash), and backtick &#96;
    - This means that word splitting, pathname expansion, tilde expansion, and brace expansion are suppressed
    	
        ```bash
        ls -l "two words.txt"
        mv "two words.txt" two_words.txt
        ```

    - Parameter expansion, arithmetic expansion, and command substitution are still carried out
    	
        ```bash
        echo "$USER $((2+2)) $(cal)"
        ```

- Using double quotes, we can cope with filenames containing embedded spaces. By default, word splitting looks for the presence of spaces, tabs, and newlines (line feed characters) and treats them as delimiters between words
    - This means unquoted spaces, tabs, and newlines are not considered to be part of the text. They serve only as **separators**
    - If we add double quotes, then word splitting is suppressed and the embedded spaces are not treated as delimiters; rather, they become part of the argument. Once the double quotes are added, our command line contains a command followed by a **single argument**
- The fact that newlines are considered delimiters by the word-splitting mechanism causes an interesting, albeit subtle, effect on command substitution
	
    ```bash
    echo $(cal)     # word splitting takes over
    echo "$(cal)"
    ```

    - In the first instance, the unquoted command substitution resulted in a command line containing 38 arguments
    - In the second, it resulted in a command line with **one argument that includes the embedded spaces and newlines**
### Single quotes
- Use single quotes to **suppress all expansions** (including escaping character `\`)
	
    ```bash
    echo text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER
    echo "text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER"
    echo 'text ~/*.txt {a,b} $(echo foo) $((2+2)) $USER'
    ```

### Escaping characters
- Escaping character (`\`) often is done inside double quotes to selectively prevent an expansion
- It is also common to use escaping to eliminate the special meaning of a character in a filename to use characters that normally have special meaning to the shell. These would include `$`, `!`, `&`, spaces, and others
	
    ```bash
    mv bad\&filename good_filename
    ```

- To allow a backslash character to appear, escape it by typing `\\`
- Within single quotes, the backslash loses its special meaning and is treated as an ordinary character
	
    ```bash
    echo "\\abc"
    echo '\abc'
    ```

### Backslash escape sequences
- In addition to its role as the escape character, the backslash is **used as part of a notation to represent certain special characters** called *control codes*
- The idea behind this representation using the backslash originated in the C programming language and has been adopted by many others, including the shell
    - The first 32 characters in the ASCII coding scheme are used to transmit commands to Teletype-like devices
        - Some of these codes are familiar (tab, backspace, line feed, and carriage return), while others are not (null, end-of-transmission, and acknowledge)
- Adding the `-e` option to `echo` will enable interpretation of escape sequences. You can also place them inside `$' '`
	
    ```bash
    # primitive timer
    # /a: Bell (an alert that causes the computer to beep)
    sleep 3; echo -e "Time's up\a"
    sleep 3; echo "Time's up" $'\a'
    ```
