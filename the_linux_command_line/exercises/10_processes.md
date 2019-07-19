# Processes
- Modern operating systems are usually multitasking, meaning they create the illusion of doing more than one thing at once by rapidly **switching** from one executing program to another
- The kernel manages this through the use of *processes*. Processes are how Linux organizes the different programs waiting for their turn at the CPU
- When a system starts up, the kernel initiates a few of its own activities as processes and launches a program called `init`. `init`, in turn, runs a series of shell scripts (located in `/etc`) called *init scripts*, which **start all the system services**
    - Many of these services are implemented as *daemon programs*, programs that just sit in the background and do their thing without having any user interface. So, even if we are not logged in, the system is at least a little busy performing routine stuff
- The fact that a program can launch other programs is expressed in the **process scheme** as a *parent process* producing a *child process*
- The kernel maintains information about each process to help keep things organized
    - For example, each process is assigned a number called a *process ID* (PID). PIDs are assigned in ascending order, with `init` always getting PID 1. The kernel also keeps track of the **memory** assigned to each process, as well as the processes’ **readiness** to resume execution. Like files, processes also have owners and user IDs, effective user IDs, and so on
    - > https://stackoverflow.com/questions/32455684/difference-between-real-user-id-effective-user-id-and-saved-user-id
## Viewing processes
- The most commonly used command to view processes (there are several) is `ps`
- By default, `ps` doesn’t show us very much, just the processes **associated with the current terminal session**
	
    ```bash
    ps
    #  PID  TTY          TIME CMD
    # 25061 pts/0    00:00:00 bash
    # 25086 pts/0    00:00:00 ps

    ps x
    # PID TTY    STAT   TIME COMMAND
    # 1 ?        Ss     0:09 /sbin/init
    # 2 ?        S      0:00 [kthreadd]
    # 3 ?        S      0:00 [ksoftirqd/0]
    # 5 ?        S<     0:00 [kworker/0:0H]

    ps aux
    # USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    # root         1  0.0  0.5  37852  5084 ?        Ss   Jul09   0:09 /sbin/init
    # root         2  0.0  0.0      0     0 ?        S    Jul09   0:00 [kthreadd]
    # root         3  0.0  0.0      0     0 ?        S    Jul09   0:00 [ksoftirqd/0]
    # root         5  0.0  0.0      0     0 ?        S<   Jul09   0:00 [kworker/0:0H]
    ```

    - `TTY` is short for “teletype” and refers to the *controlling terminal* for the process. The `TIME` field is the amount of CPU time consumed by the process
    - `x` (note that there is no leading dash) tells `ps` to show all of our processes regardless of what terminal (if any) they are controlled by. The presence of a `?` in the TTY column indicates no controlling terminal
        - A new column titled `STAT` has been added to the output. `STAT` is short for *state* and reveals the current status of the process (see the following table)
            - The process state may be followed by other characters. These indicate various exotic process characteristics. See the `ps` man page for more details
        - Using this option, we see a list of every process that we own
        - > An alternate description is that this option causes `ps` to list all processes **owned by you** (same `EUID` as `ps`), or to list all processes when used together with the `a` option
    - `aux` displays the processes **belonging to every user**. Using the options without the leading dash invokes the command with “BSD-style” behavior
        - The Linux version of `ps` can emulate the behavior of the `ps` program found in several different Unix implementations
- Process state

    State | Meaning |
    --|--|
    `R` | Running. This means the process is running or ready to run.
    `S` | Sleeping. The process is not running; rather, it is waiting for an event, such as a keystroke or network packet.
    `D` | Uninterruptible sleep. The process is waiting for I/O such as a disk drive.
    `T` | Stopped. The process has been instructed to stop.
    `Z` | A defunct or “zombie” process. This is a child process that has terminated but has not been cleaned up by its parent.
    `<` | A high-priority process. It’s possible to grant more importance to a process, giving it more time on the CPU. This property of a process is called *niceness*. A process with high priority is said to be less nice because it’s taking more of the CPU’s time, which leaves less for everybody else.
    `N` | A low-priority process. A process with low priority (a nice process) will get processor time only after other processes with higher priority have been serviced.
    |

- BSD style `ps` column headers    

    Header | Meaning |
    --|--|
    `USER` | User ID. This is the owner of the process.
    `%CPU` | CPU usage in percent.
    `%MEM` | Memory usage in percent.
    `VSZ` | Virtual memory size.
    `RSS` | Resident set size. This is the amount of physical memory (RAM) the process is using in kilobytes.
    `START` | Time when the process started. For values over 24 hours, a date is used.
    |

### Viewing processes dynamically with `top`    
- `ps` provides only a **snapshot** of the machine’s state at the moment the `ps` command is executed
- The `top` program displays a continuously updating (by default, every three seconds) display of the system processes listed in order of process activity
    - The name `top` comes from the fact that the `top` program is used to see the “top” processes on the system
- The `top` display consists of two parts: a system summary at the top of the display, followed by a table of processes sorted by CPU activity
- `top` information fields

    Row | Field | Meaning |
    --|--|--|
    1 | top | This is the name of the program.
    | | 14:59:20 | This is the current time of day.
    | | up 6:30 | This is called uptime. It is the amount of time since the machine was last booted. In this example, the system has been up for six and a half hours.
    | | 2 users | There are two users logged in.
    | | load average: 0.00, 0.00, 0.00 | Load average refers to the number of processes that are **waiting to run**; that is, the number of processes that are in a runnable state and are sharing the CPU. Three values are shown, each for a different period of time. The first is the average for the last 60 seconds, the next the previous 5 minutes, and finally the previous 15 minutes. Values less than 1.0 indicate that the machine is not busy.
    2 | Tasks: | This summarizes the number of processes and their various process states.
    3 | Cpu(s): This row describes the character of the activities that the CPU is performing.
    | | 0.7%us | 0.7 percent of the CPU is being used for user processes. This means processes outside the kernel.
    | | 1.0%sy | 1.0 percent of the CPU is being used for system (kernel) processes.
    | | 0.0%ni | 0.0 percent of the CPU is being used by “nice” (low-priority) processes.
    | | 98.3%id | 98.3 percent of the CPU is idle.
    | | 0.0%wa | 0.0 percent of the CPU is waiting for I/O.
    4 | Mem: | This shows how physical RAM is being used.
    5 | Swap: | This shows how swap space (virtual memory) is being used.
    |

- `h` option displays the help screen
- `q` option quits `top`
- > Both major desktop environments provide graphical applications that display information similar to `top` (in much the same way that Task Manager in Windows works), but `top` is better than the graphical versions because it is faster and it consumes far fewer system resources. After all, our system monitor program shouldn’t be the source of the system slowdown that we are trying to track
## Controlling processes
- The `xlogo` program is a sample program supplied with the X Window System (the underlying engine that makes the graphics on our display go), which simply displays a resizable window containing the X logo
	
    ```bash
    xlogo
    ```

    - After entering the command, a small window containing the logo should appear somewhere on the screen
        - On some systems, `xlogo` might print a warning message, but it can be safely ignored
        - If your system does not include the `xlogo` program, try using `gedit` or `kwrite` instead
    - We can verify that xlogo is running by **resizing** its window. If the logo is **redrawn** in the new size, the program is running
    - Notice how our shell prompt has not returned? This is because the shell is waiting for the program to finish. If we close the `xlogo` window, the prompt returns
### Interrupting a process
- In a terminal, pressing `ctrl-C` interrupts a program. This means we are politely asking the program to **terminate**
    - Many (but not all) command line programs can be interrupted by using this technique
- After we pressed `ctrl-C`, the `xlogo` window closed, and the shell prompt returned
### Putting a process in the background
- Scenario: get the shell prompt back without terminating the `xlogo` program
- Think of the terminal as having a *foreground* (with stuff visible on the surface like the shell prompt) and a *background* (with stuff hidden behind the surface). To launch a program so that it is immediately placed in the background, we follow the command with an ampersand (`&`) character
	
    ```bash
    # top &
    xlogo &
    # [1] 28236
    ps
    # PID TTY TIME CMD
    # 10603 pts/1 00:00:00 bash
    # 28236 pts/1 00:00:00 xlogo
    # 28239 pts/1 00:00:00 ps
    ```

    - `[1] 28236` is part of a shell feature called *job contorl*. With this message, the shell is telling us that we have started job number 1 (`[1]`) and that it has PID `28236`
- `jobs` list the jobs that have been launched from our terminal
### Returning a process to the foreground
- A process in the background is immune from terminal keyboard input, including any attempt to interrupt it with `ctrl-C`
- To return a process to the foreground, use the `fg` command in this way
	
    ```bash
    jobs
    fg %1
    ```

    - The `fg` command followed by a percent sign and the job number (called a *jobspec*) does the trick
        - If we have only one background job, the jobspec is optional
### Stopping (pausing) a process
- Sometimes we’ll want to stop a process without terminating it. This is often done to allow a **foreground process** to be **moved to the background**
- To stop a foreground process and place it in the background, press `ctrl-Z`
	
    ```bash
    xlogo
    # press ctrl+z
    bg %1 # resume the program’s execution in the background
    ```

    - Continue the program’s execution in the foreground, using the `fg` command
        - As with the `fg` command, the jobspec is optional if there is only one job
    - Resume the program’s execution in the background with the `bg` command
- Moving a process from the foreground to the background is handy if we launch a **graphical program** from the command line but forget to place it in the background by appending the trailing `&`
- 2 reasons to launch a graphical program from the command line
    1. The program you want to run might not be listed on the window manager’s menus (such as `xlogo`)
    2. By launching a program from the command line, you might be able to see **error messages** that would otherwise be invisible if the program were launched graphically. Also, some graphical programs have interesting and useful command line options
## Signals
- The `kill` command is used to “kill” processes
	
    ```bash
    xlogo &
    # [1] 28401
    kill 28401
    ```

- We could have also specified the process using a **jobspec** (for example, `%1`) instead of a **PID**
- The `kill` command doesn’t exactly “kill” processes; rather, it sends them **signals**
- Signals are one of several ways that the operating system communicates with programs
- When the terminal receives some combination of keystrokes, it sends a signal to the program in the foreground
    - In the case of `ctrl-C`, a signal called `INT` (interrupt) is sent; with `ctrl-Z`, a signal called `TSTP` (terminal stop) is sent
    - Programs, in turn, “listen” for signals and may act upon them as they are received
- The fact that a program can **listen and act** upon signals allows a program to do things such as save work in progress when it is sent a termination signal
### Sending signals to processes with `kill`
- The `kill` command is used to send signals to programs
- Most common syntax
	
    ```bash
    kill -signal PID...
    ```

    - If no signal is specified on the command line, then the `TERM` (terminate) signal is sent by **default**
- Common signals

    Number | Name | Meaning |
    --|--|--|
    1 | HUP | Hang up. This is a vestige of the good old days when terminals were attached to remote computers with phone lines and modems. The signal is used to indicate to programs that the controlling terminal has “hung up.” The effect of this signal can be demonstrated by **closing a terminal session**. The foreground program running on the terminal will be sent the signal and will terminate. This signal is also used by many daemon programs to cause a **reinitialization**. This means that when a daemon is sent this signal, it will **restart** and **reread** its configuration file. The Apache web server is an example of a daemon that uses the `HUP` signal in this way.
    2 | INT | Interrupt. This performs the same function as `ctrl-C` sent from the terminal. It will usually terminate a program.
    9 | KILL | Kill. This signal is special. Whereas programs may **choose** to handle signals sent to them in different ways, including ignoring them all together, the `KILL` signal is never actually sent to the target program. Rather, the **kernel** immediately terminates the process. When a process is terminated in this manner, it is given **no opportunity to “clean up” after itself or save its work**. For this reason, the `KILL` signal should be used only as a **last resort** when other termination signals fail.
    15 | TERM | Terminate. This is the **default** signal sent by the kill command. If a program is still “alive” enough to receive signals, it will terminate.
    18 | CONT | Continue. This will restore a process after a `STOP` or `TSTP` signal. This signal is sent by the `bg` and `fg` commands.
    19 | STOP | Stop. This signal causes a process to **pause without terminating**. Like the `KILL` signal, it is not sent to the target process, and thus it **cannot be ignored**.
    20 | TSTP | Terminal stop. This is the signal sent by the terminal when `ctrl-Z` is pressed. Unlike the STOP signal, the `TSTP` signal is received by the program, but the program may choose to ignore it.
    3 | QUIT | Quit.
    11 | SEGV | Segmentation violation. This signal is sent if a program makes illegal use of memory; that is, if it tried to write somewhere it was not allowed to write.
    28 | WINCH | Window change. This is the signal sent by the system when a window changes size. Some programs, such as `top` and `less`, will **respond** to this signal by **redrawing** themselves to fit the new window dimensions.
    |

    - `kill -l` to see the full list
- Signals may be specified either by number or by name, including the name prefixed with the letters `SIG`    
	
    ```bash
    xlogo &
    # [1] 13601

    # may need to press enter a couple of times before the message appears
    kill -2 13601
    kill -INT 13601
    kill -SIGINT 13608
    # [1]+ Interrupt xlogo
    ```

- Processes, like files, have owners, and you must be the **owner** of a process (or the **superuser**) to send it signals with `kill`
### Sending signals to multiple processes with `killall`
- It’s also possible to send signals to multiple processes matching a specified **program** or **username** by using the `killall` command
	
    ```bash
    killall [-u user] [-signal] name...

    xlogo &
    xlogo &
    killall xlogo
    ```

- As with `kill`, you must have superuser privileges to send signals to processes that do not belong to you
## Shutting down the system
- The process of shutting down the system involves the **orderly termination** of all the processes on the system, as well as performing some vital **housekeeping** chores (such as syncing all of the mounted file systems) before the system powers off
- 4 commands can perform this function
    - `halt`
    - `poweroff`
    - `reboot`
    - `shutdown`
- The first 3 are pretty self-explanatory and are generally used without any command line options
- With `shutdown`, we can **specify** which of the **actions** to perform (halt, power down, or reboot) and provide a **time delay** to the shutdown event
	
    ```bash
    sudo shutdown -h now
    sudo shutdown -r now
    ```

    - The delay can be specified in a variety of ways
- Once the `shutdown` command is executed, a message is **“broadcast”** to all logged-in users warning them of the impending event
## More process-related commands

Command | Description |
--|--|
`pstree` | Outputs a process list arranged in a **tree-like pattern** showing the **parent-child** relationships between processes.
`vmstat` | Outputs a **snapshot** of system resource usage including memory, swap, and disk I/O. To see a **continuous** display, follow the command with a time delay (in seconds) for updates. Here’s an example: `vmstat 5`. Terminate the output with `ctrl-C`.
`xload` | A graphical program that draws a graph showing system load over time.
`tload` | Similar to the `xload` program but draws the graph in the terminal. Terminate the output with `ctrl-C`.
|
