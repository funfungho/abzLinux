- Regular expressions are symbolic notations used to identify patterns in text.
    - For our discussion, we will limit ourselves to regular expressions as described in the POSIX standard (which will cover most of the command line tools), as opposed to many programming languages (most notably Perl), which use slightly larger and richer sets of notations.
# `grep`
- `grep`: global regular expression print
- In essence, `grep` searches text files for text matching a specified regular expression and outputs any line containing a match to standard output.
- `grep [options] regex [file...]`

    Option | Long option | Description |
    --|--|--|
    `-i` | `--ignore-case` | Ignore case. Do not distinguish between uppercase and lowercase characters.
    `-v` | `--invert-match` | Invert match. Normally, `grep` prints lines that contain a match. This option causes grep to print every line that does not contain a match.
    `-c` | `--count` | Print the number of matches (or non-matches if the `-v` option is also specified) instead of the lines themselves.
    `-l` | `--files-with-matches` | Print the name of each file that contains a match instead of the line themselves.
    `-L` | `--files-without-match` | Like the `-l` option, but print only the names of files that do not contain matches.
    `-n` | `--line-number` | Prefix each matching line with the number of the line within the file.
    `-h` | `--no-filename` | For multifile searches, suppress the output of filenames.
    `-A num` | `--after-context=num` |Print `num` lines of trailing context after each match.
     `-B` | `--before-context` | Print `num` lines of leading context before each match.
     `-C` | `--context=num` | Print `num` lines of leading and trailing context surrounding each match.
    
# Metacharacters and Literals
- Regular expression *metacharacters*: 

    ```
    ^ $ . [ ] { } - ? * + ( ) | \
    ```

- All other characters are considered literals, though the backslash character is used in a few cases to create metasequences, as well as allowing the metacharacters to be escaped and treated as literals instead of being interpreted as metacharacters.
    - Many of the regular expression metacharacters are also characters that have meaning to the shell when expansion is performed. 
    - When we pass regular expressions containing metacharacters on the command line, it is vital that they be ***enclosed in quotes*** to **prevent the shell from attempting to expand them**.
# The Any Character
- The dot (`.`) or period character is used to match any character.
- If we include it in a regular expression, it will match **any character in that character position**.
    - 包括 metacharacters，换行等。
- Period character would be matched by the “any character,” too.
# Anchors
- The caret (`^`) and dollar sign (`$`) are treated as *anchors* in regular expressions. 
- They cause the match to occur only if the regular expression is found at the beginning of the **line** (`^`) or at the end of the **line** (`$`).
- `^$` (a beginning and an end with nothing in between) will match blank lines.
- A crossword puzzle helper

    ```bash
    # What’s a five-letter word whose third letter is j and last letter is r that means . . . ?
    grep -i '^..j.r$' /usr/share/dict/words
    ```

    - Linux system contains a dictionary in the `/usr/share/dict` directory.
# Bracket Expressions and Character Classes
- Match ***a single character*** from a specified set of characters by using *bracket expressions*.
- With bracket expressions, we can specify a set of characters (**including** characters that would otherwise be interpreted as metacharacters) to be matched.
- A set may contain any number of characters, and metacharacters lose their special meaning when placed within brackets.
- However, there are 2 cases in which metacharacters are used within bracket expressions and have different meanings. 
    - The first is the caret (`^`), which is used to indicate **negation**.
    - The second is the dash (`-`), which is used to indicate a **character range**.
## Negation
- If the first character in a bracket expression is a caret (`^`), the remaining characters are taken to be a ***set*** of characters that must not be present at the given character position.
- A negated character set still requires a character at the given position, but the character must not be a member of the negated set.
- The caret character invokes negation **only if it is the first character within a bracket expression**; otherwise, it loses its special meaning and becomes an ordinary character in the set.
## Traditional Character Ranges
- Any range of characters can be expressed this way including **multiple ranges**.
	
    ```bash
    grep -h '^[A-Za-z0-9]' dirlist*.txt
    ```

- Include a dash character in a bracket expression by making it the first character in the expression.
	
    ```bash
    grep -h '[-AZ]' dirlist*.txt
    ```

## POSIX Character Classes
- POSIX stands for *Portable Operating System Interface* (with the X added to the end for extra snappiness).
- Depending on the Linux distribution, we will get a different list of files, possibly an empty list.
	
    ```bash
    ls /usr/sbin/[ABCDEFGHIJKLMNOPQRSTUVWXYZ]*
    ls /usr/sbin/[A-Z]*

    ls /usr/sbin/[[:upper:]]*
    ```

- Back when Unix was first developed, it knew only about ASCII characters, and this feature reflects that fact. 
    - In ASCII, the first 32 characters (numbers 0–31) are control codes (things such as tabs, backspaces, and carriage returns). The next 32 (32–63) contain printable characters, including most punctuation characters and the numerals 0–9. 
    - The next 32 (numbers 64–95) contain the uppercase letters and a few more punctuation symbols. 
    - The final 31 (numbers 96–127) contain the lowercase letters and yet more punctuation symbols. 
- Based on this arrangement, systems using ASCII used a *collation order* that looks like this:
	
    ```
    ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
    ```

- This differs from proper **dictionary order**, which is like this:

    ```
    aAbBcCdDeEfFgGhHiIjJkKlLmMnNoOpPqQrRsStTuUvVwWxXyYzZ
    ```

- As the popularity of Unix spread beyond the United States, there grew a need to support characters not found in US English. The ASCII table was expanded to use a full 8 bits, adding characters 128–255, which accommodated many more languages. 
- To support this capability, the POSIX standards introduced a concept called a *locale*, which could be adjusted to select the character set needed for a particular location. 
    - We can see the language setting of our system using `echo $LANG`.
- With this setting (LANG="en_US.UTF-8"), POSIX-compliant applications will use a dictionary collation order rather than ASCII order.
    - A character range of `[A-Z]` when interpreted in dictionary order includes all of the alphabetic characters except the lowercase `a`.
- To partially work around this problem, the POSIX standard includes a number of character classes that provide useful ranges of characters.

    Character class | Description 
    --|--
    `[:alpha:]` | The alphabetic characters. In ASCII, equivalent to: `[A-Za-z]`
    `[:digit:]` | The numerals 0 through 9.
    `[:alnum:]` | The alphanumeric characters. In ASCII, equivalent to: `[A-Za-z0-9]`
    `[:word:]` | The same as `[:alnum:]`, with the addition of the underscore (`_`) character.
    `[:blank:]` | Includes the **space and tab** characters.
    `[:space:]` | The whitespace characters including space, tab, carriage return, newline, vertical tab, and form feed. In ASCII, equivalent to: `[ \t\r\n\v\f]`
    `[:cntrl:]` | The ASCII control codes. Includes the ASCII characters 0 through 31 and 127.
    `[:punct:]` | The punctuation characters. In ASCII, equivalent to: [-!"#$%&'()*+,./:;<=>?@[\\\]_`{|}~]
    `[:graph:]` | The **visible** characters. In ASCII, it includes characters 33 through 126.
    `[:print:]` | The printable characters. All the characters in `[:graph:]` plus the space character.
    `[:lower:]` | The lowercase letters.
    `[:upper:]` | The uppercase characters.
    `[:xdigit:]` | Characters used to express hexadecimal numbers. In ASCII, equivalent to: `[0-9A-Fa-f]`

- POSIX character classes can be used for both regular expressions and shell expansion.
- You can opt to have your system use the traditional (ASCII) collation order by changing the value of the `LANG` environment variable. The `LANG` variable contains the name of the language and character set used in your locale.
	
    ```bash
    # see the local setting
    locale
    # change the locale to use the traditional Unix behaviors
    export LANG=POSIX
    ```

    - This change converts the system to use US English (more specifically, ASCII) for its character set, so be sure if this is really what you want.
# POSIX Basic vs. Extended Regular Expressions
- POSIX also splits regular expression implementations into 2 kinds: *basic regular expressions* (*BRE*) and *extended regular expressions* (*ERE*). 
    - The features we have covered so far are supported by any application that is POSIX compliant and implements BRE. `grep` is one such program.
- The differences between BRE and ERE are metacharacters.
    - With BRE, the following metacharacters are recognized: `^ $ . [ ] *`. All other characters are considered literals.
    - With ERE, the following metacharacters (and their associated functions) are added: `( ) { } ? + |`.
    - The `(`, `)`, `{`, and `}` characters are treated as metacharacters in BRE if they are escaped with a backslash, whereas with ERE, preceding any metacharacter with a backslash causes it to be treated as a literal.
- Because the features we are going to discuss next are part of ERE, we are going to need to use a different `grep`. Traditionally, this has been performed by the `egrep` program, but the GNU version of `grep` also supports extended regular expressions when the `-E` option is used.
# Alternation
- *Alternation* is the facility that allows a match to occur from among a set of expressions.
- Alternation allows matches from a set of strings or other regular expressions.
	
    ```bash
    echo "AAA" | grep -E 'AAA|BBB|CCC'
    ```

    - Enclose the regular expression in quotes to prevent the shell from interpreting the vertical-bar metacharacter as a pipe operator.
- To combine alternation with other regular expression elements, we can use `()` to separate the alternation.
	
    ```bash
    # match the filenames that start with either bz, gz, or zip
    grep -Eh '^(bz|gz|zip)' dirlist*.txt

    # match any filename that begins with bz or contains gz or contains zip
    grep -Eh '^bz|gz|zip' dirlist*.txt
    ```

# Quantifiers
## `?` — Match an Element Zero or One Time
- This quantifier means, in effect, “make the preceding element **optional**.”
## `*` — Match an Element Zero or More Times
- Match a sentence: it starts with an uppercase letter, then contains any number of uppercase and lowercase letters and spaces, and ends with a period.

    ```bash
    [[:upper:]][[:upper:][:lower:] ]*\.
    ```

## `+` — Match an Element One or More Times
<!-- - Match only the lines consisting of groups of one or more alphabetic characters separated by single spaces -->
## `{ }` — Match an Element a Specific Number of Times
- The `{` and `}` metacharacters are used to express minimum and maximum numbers of required matches.

    Specifier | Meaning
    --|--
    `{n}` | Match the preceding element if it occurs exactly `n` times.
    `{n,m}` | Match the preceding element if it occurs at least `n` times but no more than `m` times.
    `{n,}` | Match the preceding element if it occurs `n` or more times.
    `{,m}` | Match the preceding element if it occurs no more than `m` times.
# Putting Regular Expressions to Work
## Validating a Phone List with `grep`

```bash
for i in {1..10}; do echo "(${RANDOM:0:3}) ${RANDOM:0:3}-${RANDOM:0:4}" >> phonelist.txt; done
grep -Ev '^\([0-9]{3}\) [0-9]{3}-[0-9]{4}$' phonelist.txt
```

# Finding Ugly Filenames with `find`
- Whereas `grep` will print a line when the line *contains* a string that matches an expression, `find` requires that the pathname exactly *match* the regular expression.
- Use `find` with a regular expression to find every pathname that contains any character that is not a member of the following set: `[-_./0-9a-zA-Z]`.

    ```bash
    find . -regex '.*[^-_./0-9a-zA-Z].*'
    # reveal pathnames that contain embedded spaces and other potentially offensive characters
    ```

    - Because of the requirement for an exact match of the entire pathname, we use `.*` at both ends of the expression to match zero or more instances of any character.
## Searching for Files with `locate`
- The locate program supports both basic (the `--regexp` option) and extended (the `--regex` option) regular expressions.
	
    ```bash
    # perform a search for pathnames that contain either bin/bz, bin/gz, or /bin/zip
    locate --regex 'bin/(bz|gz|zip)'
    ```

## Searching for Text with `less` and `vim`
- `less` and `vim` both share the same method of searching for text. Pressing the `/` key followed by a regular expression will perform a search.
- `less` will highlight the strings that match, leaving the invalid ones easy to spot.
	
    ```bash
    # inside less
    /^\([0-9]{3}\) [0-9]{3}-[0-9]{4}$
    ```

- `vim`, on the other hand, supports basic regular expressions.
	
    ```bash
    # inside vim
    /([0-9]\{3\}) [0-9]\{3\}-[0-9]\{4\}

    # activate search highlighting
    # :hlsearch
    ```

    - > Depending on your distribution, `vim` may or may not support text search highlighting. Ubuntu, in particular, supplies a stripped-down version of `vim` by default. On such systems, you may want to use your package manager to install a more complete version of `vim`.
# Summing Up
- We can find more uses of regular expressions if we use regular expressions to search for additional applications that use them.
	
    ```bash
    cd /usr/share/man/man1
    zgrep -El 'regex|regular expression' *.gz
    ```

    - The `zgrep` program provides a front end for `grep`, allowing it to read compressed files.
    - In our example, we search the compressed section 1 man page files in their usual location. The result of this command is a list of files containing either the string `regex` or the string `regular expression`.
- *Back references* feature will be discussed in the next chapter.