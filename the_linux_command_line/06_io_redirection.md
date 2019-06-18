# I/O redirection
## Standard input, output, and error
- Many of the programs produce output that often consists of 2 types
    1. The program’s results; that is, the data the program is designed to produce
    2. Status and error messages that tell us how the program is getting along
- Keeping with the Unix theme of “everything is a file,” programs such as `ls` actually send their results to a special file called standard output (often expressed as stdout) and their status messages to another file called standard error (stderr)
    - By default, both standard output and standard error are **linked to the screen** and not saved into a disk file
- Many programs take input from a facility called standard input (stdin), which is, by default, attached to the **keyboard**
- I/O redirection allows us to change where output goes and where input comes from
    - Normally, output goes to the screen and input comes from the keyboard
- There are many commands that make use of standard input and output, and almost all command line programs use standard error to display their informative messages
## Redirecting standard output
- Use `>` redirection operator followed by the name of the file
	
    ```bash
    ls -l /usr/bin > ls-output.txt
    ls -l ls-output.txt
    less ls-output.txt

    ls -l /bin/usr > ls-output.txt
    # ls: /bin/usr: No such file or directory
    less ls-output.txt # empty
    ```

    - `ls` program does not send its error messages to standard output. Instead, like most well-written Unix programs, it sends its error messages to standard error
        - Because we redirected only standard output and not standard error, the error message was still sent to the screen
    - When we redirect output with the `>` redirection operator, the destination file is always **rewritten from the beginning**
        - Because `ls` command generated no results and only an error message, the redirection operation started to rewrite the file and then stopped because of the error, resulting in its truncation
        - Trick to truncate a file (or create a new, empty file): `> ls-output.txt`
- Append redirected output to a file instead of overwriting the file from the beginning using `>>`
    - Using the `>>` operator will result in the output being appended to the file
    - If the file does not already exist, it is created just as though the `>` operator had been used
## Redirecting standard error
- To redirect standard error, we must refer to its *file descriptor*
- A program can produce output on any of several numbered file streams
    - While we have referred to the first 3 of these file streams as standard input, output, and error, the shell references them internally as file descriptors 0, 1, and 2, respectively
- The shell provides a notation for redirecting files using the file descriptor number
- Because standard error is the same as file descriptor number 2, we can redirect standard error with this notation
	
    ```bash
    ls -l /bin/usr 2> ls-error.txt
    ```

    - The file descriptor 2 is placed **immediately before** the redirection operator to perform the redirection of standard error to the file `ls-error.txt`
### Redirecting standard output and standard error to one file
- To do this, we must redirect both standard output and standard error at the same time
	
    ```bash
    # Approach 1:  traditional way, works with old versions of the shell
    ls -l /bin/usr > ls-output.txt 2>&1
    ls -l /bin/usr >> ls-output.txt 2>&1

    # Approach 2
    ls -l /bin/usr &> ls-output.txt
    ls -l /bin/usr &>> ls-output.txt
    ```

    1. First we redirect standard output to the file `ls-output.txt`, and then we redirect file descriptor 2 (standard error) to file descriptor 1 (standard output) using the notation `2>&1`
        - The redirection of standard error must always occur after redirecting standard output or it doesn’t work
        	
            ```bash
            # redirects standard error to the file ls-output.txt
            >ls-output.txt 2>&1
            # standard error is directed to the screen
            2>&1 >ls-output.txt
            ```
        
    2. Use the single notation `&>` to redirect both standard output and standard error to the file `ls-output.txt`
### Disposing unwanted output
- The system provides a way to throw away unwanted output (such as error and status messages) by redirecting output to a special file called `/dev/null`
	
    ```bash
    # suppress error messages from a command
    ls -l /bin/usr 2> /dev/null
    ```

    - This file is a system device often referred to as a *bit bucket*, which accepts input and does nothing with it
    - > The bit bucket is an ancient Unix concept, and because of its universality, it has appeared in many parts of Unix culture. When someone says they are sending your comments to `/dev/null`, now you know what it means
## Redirecting standard input
### `cat`: concatenate files
- The `cat` command reads one or more files and copies them to standard output
    - Use it to display files without paging
	
    ```bash
    cat filename
    ```

- Because `cat` can accept more than one file as an argument, it can also be used to join files together
    - Suppose we have downloaded a large file that has been split into multiple parts (multimedia files are often split this way on Usenet), and we want to join them back together
    	
        ```bash
        # files named as follows
        # movie.mpeg.001 movie.mpeg.002 ... movie.mpeg.099
        cat movie.mpeg.0* > movie.mpeg
        ```
    
        - Because wildcards always expand in sorted order, the arguments will be arranged in the correct order
- If `cat` is not given any arguments, it reads from standard input, and since standard input is, by default, attached to the keyboard, it’s waiting for us to type something
	
    ```bash
    cat
    abade 
    # type ctrl-d to tell cat that it has reached end of file (EOF)

    # use this behavior to create short text files
    cat > lazy_dog.txt
    # type in some text then press ctrl-d
    ```

- Use the `<` redirection operator to redirect standard input
	
    ```bash
    cat < lazy_dog.txt
    ```

    - `<` changes the source of standard input from the keyboard to the file `lazy_dog.txt`
## Pipelines
- Using the pipe operator `|`, the standard output of one command can be piped into the standard input of another
	
    ```bash
    command1 | command2
    ```

- `less` accepts standard input. We can use `less` to display, page by page, the output of any command that sends its results to standard output
	
    ```bash
    ls -l /usr/bin | less
    ```

    - Using this technique, we can conveniently examine the output of any command that produces standard output
### Filters
- It is possible to put several commands together into a pipeline. Frequently, the commands used this way are referred to as *filters*
- Filters take input, change it somehow, and then output
	
    ```bash
    ls /bin /usr/bin | sort | less
    ```

    - Because we specified 2 directories, the output of `ls` would have consisted of two sorted lists, one for each directory. By including `sort` in our pipeline, we changed the data to produce a single, sorted list
- Difference between `>` and `|`
    - The redirection operator connects a command with a **file**, while the pipeline operator connects the output of one command with the input of a second command
    - A lot of people will try the following when they are learning about pipelines, “just to see what happens”. Sometimes something really bad happens
    	
        ```bash
        # cd /usr/bin
        # ls > less
        ```
    
        - The first command put him in the directory where most programs are stored, and the second command told the shell to **overwrite the file `less`** with the output of the `ls` command
        - Since the `/usr/bin` directory already contained a file named `less` (the `less` program), the second command overwrote the `less` program file with the text from `ls`, thus destroying the `less` program on his system
        - The lesson here is that the redirection operator **silently creates or overwrites files**, so you need to treat it with a lot of respect
### `uniq`: report or omit repeated lines
- The `uniq` command is often used in conjunction with `sort`. `uniq` accepts a sorted list of data from either standard input or a single filename argument and, by default, removes any duplicates from the list
	
    ```bash
    ls /bin /usr/bin | sort | uniq | less
    uniq a.txt | less

    # add -d to see the list of duplicates
    ls /bin /usr/bin | sort | uniq -d | less
    ```

### `wc`: print line, word, and byte counts
- The `wc` (word count) command is used to display the number of lines, words, and bytes (same order) contained in files
- If executed without command line arguments, wc accepts standard input
- The `-l` option limits its output to report only lines
	
    ```bash
    ls /bin /usr/bin | sort | uniq | wc -l
    ```

### `grep`: print lines matching a pattern
- `grep` is a powerful program used to **find text patterns within files**
	
    ```bash
    grep pattern filename
    ```

- When grep encounters a “pattern” in the file, it prints out the lines containing it 
	
    ```bash
    ls /bin /usr/bin | sort | uniq | grep zip
    echo 1234 | grep 12
    ```

    - Standard output is also file
- `-i` causes grep to ignore case when performing the search (normally searches are case sensitive)
- `-v` tells `grep` to print only those lines that do not match the pattern
### `head`/`tail`: print first/last part of files
- The `head` command prints the first 10 lines of a file, and the `tail` command prints the last 10 lines by default. This can be adjusted with the `-n` option
- `tail` has an option `-f` that allows you to view files in real time. This is useful for watching the progress of log files as they are being written
	
    ```bash
    # Superuser privileges are required to do this on some Linux distributions because the /var/log messages file might contain security information
    tail -f /var/log/messages

    tail -f /var/log/syslog
    ```

    - Using the `-f` option, `tail` continues to monitor the file, and when new lines are appended, they immediately appear on the display. This continues until you type ctrl-C
### `tee`: read from stdin and output to both stdout and files
- The `tee` program reads standard input and copies it to both standard output (allowing the data to continue down the pipeline) and to one or more files
	
    ```bash
    ls /usr/bin | tee ls.txt | grep zip
    ```

- This is useful for capturing a pipeline’s contents at an intermediate stage of processing