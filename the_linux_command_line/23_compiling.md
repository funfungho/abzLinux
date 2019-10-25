<!-- review 2019-10-25 11:27:07 -->
- Compiling is the process of translating **source code** (the human-readable description of a program written by a programmer) into the **native language** of the **computer’s *processor***
    - The computer’s processor (or CPU) works at an elemental level, executing programs in what is called *machine language*
- A process often used in conjunction with compiling is called *linking*
    - A program called a *linker* is used to form the **connections** between the **output of the compiler** and the **libraries** that the **compiled program** requires
    - The final result of this process is the executable program file, ready for use    
- Scripted languages are executed by a special program called an ***interpreter***. An interpreter inputs the program file and reads and executes each instruction contained within it
    - In general, interpreted programs execute much more slowly than compiled programs. This is because each **source code instruction** in an interpreted program is **translated every time** it is carried out, whereas with a compiled program, a source code instruction is translated only once, and this **translation is permanently recorded in the final executable file**
    - 同一段逻辑的代码每次执行都得重新解释成机器码
- The `.h` files are known as *header files*. Header files contain **descriptions of the routines** included in a source code file or library. For the compiler to connect the modules, it must receive a description of all the modules needed to complete the entire program
- The `configure` program is a shell script that is supplied with the source tree. Its job is to **analyze the build environment**
    - `configure` created several new files in our source directory. The most important one is the ***`makefile`***
---
# Compiling programs
- For many desktop users, compiling is a lost art. It used to be quite common, but today, distribution providers maintain huge repositories of **precompiled binaries**, ready to download and use
- Why compile software
    1. Some distributions may not include the desired applications. In this case, the only way to get the desired program is to compile it from source
    2. To have the latest version of a program
- Compiling is the process of translating **source code** (the human-readable description of a program written by a programmer) into the **native language** of the **computer’s *processor***
- The computer’s processor (or CPU) works at an elemental level, executing programs in what is called *machine language*
    - This is a numeric code that describes extremely small operations, such as “add this byte,” “point to this location in memory,” or “copy this byte.” 
    - Each of these instructions is expressed in **binary** (ones and zeros)
- *Assembly language* replaced the numeric codes with (slightly) easier-to-use character mnemonics such as `CPY` (for copy) and `MOV` (for move). 
    - Programs written in assembly language are processed into machine language by a program called an *assembler*
    - Assembly language is still used today for certain specialized programming tasks, such as device drivers and embedded systems
- We next come to what are called high-level programming languages, which allow the programmer to be less concerned with the details of what the **processor** is doing and more with solving the problem at hand
    - The early ones (developed during the 1950s) include FORTRAN (designed for scientific and technical tasks) and COBOL (designed for business applications)
    - Most programs written for modern systems are written in either C or C++
- Programs written in high-level programming languages are converted into machine language by processing them with another program, called a ***compiler***
    - Some compilers translate high-level instructions into assembly language and then use an assembler to perform the final stage of translation into machine language
- A process often used in conjunction with compiling is called *linking*
    - There are many common tasks performed by programs. Take, for instance, opening a file. Many programs perform this task, but it would be wasteful to have each program implement its own routine to open files. It makes more sense to have a single piece of programming that knows how to open files and to allow all programs that need it to share it. Providing support for common tasks is accomplished by what are called ***libraries***
        - They contain multiple routines, each performing some common task that multiple programs can share (If we look in the `/lib` and `/usr/lib` directories, we can see where many of them live)
    - A program called a *linker* is used to form the **connections** between the **output of the compiler** and the **libraries** that the **compiled program** requires
    - The final result of this process is the executable program file, ready for use
## Interpreted languages
- Not all programs are compiled. Some are executed directly. These are written in what are known as *scripting* or *interpreted languages*
- Scripted languages are executed by a special program called an ***interpreter***. An interpreter inputs the program file and reads and executes each instruction contained within it
    - In general, interpreted programs execute much more slowly than compiled programs. This is because each **source code instruction** in an interpreted program is **translated every time** it is carried out, whereas with a compiled program, a source code instruction is translated only once, and this **translation is permanently recorded in the final executable file**
    - 同一段逻辑的代码每次执行都得重新解释成机器码
- Programs are usually developed in a repeating cycle of code, compile, test. As a program grows in size, the compilation phase of the cycle can become quite long. Interpreted languages remove the compilation step and thus speed up program development
## Compiling a C program
### Obtaining the source code

```bash
# mac
brew install inetutils # maybe try twice

ftp ftp.gnu.org # Name: anonymous
cd gnu/diction
ls
get diction-1.11.tar.gz
byte

# or
wget https://ftp.gnu.org/gnu/diction/diction-1.11.tar.gz

tar tzvf diction-1.11.tar.gz | head
tar xzf diction-1.11.tar.gz
```

- Source code installed by your distribution will be installed in `/usr/src`, while source code we maintain that’s intended for use by multiple users is usually installed in `/usr/local/src`
- Source code is usually supplied in the form of a compressed `tar` file. Sometimes called a *tarball*, this file contains the source tree, or hierarchy of directories and files that comprise the source code
- The `diction` program, like all GNU Project software, follows certain standards for source code packaging (Most other source code available in the Linux ecosystem also follows this standard). 
    - One element of the standard is that when the source code `tar` file is unpacked, a directory will be created that contains the source tree, and this directory will be named project-x.xx, thus containing both the project’s name and its version number. 
        - This scheme allows easy installation of multiple versions of the same program
- It is often a good idea to examine the layout of the tree before unpacking it. Some projects will not create the directory but instead will deliver the files directly into the current directory. This will make a mess in our otherwise well-organized directory
	
    ```bash
    # examine the contents of the tar file
    tar tzvf tarfile | head
    ```

### Examining the source tree
- Programs belonging to the GNU Project, and many others, will supply the documentation files `README`, `INSTALL`, `NEWS`, and `COPYING`
    - These files contain the description of the program, information on how to build and install it, and its licensing terms
    - It is always a good idea to carefully read the `README` and `INSTALL` files before attempting to build the program
- The `.c` files contain the 2 C programs supplied by the package (`style` and `diction`), divided into modules
- The `.h` files are known as *header files*. Header files contain **descriptions of the routines** included in a source code file or library. For the compiler to connect the modules, it must receive a description of all the modules needed to complete the entire program
    - (As the compiler reads the source code in `diction.c`) `#include "getopt.h"` instructs the compiler to read the file `getopt.h` to **“know”** what’s in `getopt.c`
    - `The getopt.c` file supplies routines that are shared by both the `style` and `diction` programs
    - Other `include` statements
    	
        ```c
        #include <regex.h>
        #include <stdio.h>
        #include <stdlib.h>
        #include <string.h>
        #include <unistd.h>
        // ls /usr/include
        ```
    
        - These also refer to header files, but they refer to header files that live outside the current source tree. 
        - They are supplied by the system to support the compilation of every program
        - The header files in this `/usr/include` were installed when we installed the compiler
### Building the program
- Most programs build with a simple, two-command sequence
	
    ```bash
    ./configure
    # configure 脚本已经尽量考虑到不同系统的差异，并且对各种编译参数给出了默认值
    # 如果用户的系统环境比较特别，或者有一些特定的需求，就需要手动向 configure 脚本提供编译参数
    # 指定安装后的文件保存在 www 目录，并且编译时加入 mysql 模块的支持
    # ./configure --prefix=/www --with-mysql

    make
    ```

    - Because `configure` is not located where the shell normally expects programs to be located, we must **explicitly** tell the shell its location by prefixing the command with `./` to **indicate** that the program is located in the current working directory
- The `configure` program is a shell script that is supplied with the source tree. Its job is to **analyze the build environment**
    - Most source code is designed to be *portable*. That is, it is designed to build on more than one kind of Unixlike system. But to do that, the source code may need to undergo slight adjustments during the build to accommodate differences between systems
    - `configure` also checks to see that necessary external tools and components are installed
    - `configure` 脚本通常由 `autoconf` 工具生成
- What’s important running `./configure` is that there are no error messages. If there were, the configuration failed, and the program will not build until the errors are corrected
- `configure` created several new files in our source directory. The most important one is the ***`makefile`***
- The `makefile` is a configuration file that instructs the `make` program exactly how to build the program. Without it, `make` will refuse to run
    - The `make` program takes as input a `makefile` (which is normally named `Makefile`), which describes the relationships and dependencies among the components that comprise the finished program
- Rather than simply building everything again, `make` only builds what needs building. With all of the targets present, `make` determined that there was nothing to do
    	
    ```bash
    make

    # get rid of one of the intermediate targets
    rm getopt.o
    make
    ```

    - While the time savings may not be apparent with our small project, it is very significant with larger projects
- `make` keeps targets up-to-date. `make` insists that targets be newer than their dependencies
    - This makes perfect sense because a programmer will often update a bit of source code and then use `make` to build a new version of the finished product. `make` ensures that everything that needs building based on the updated code is built

    ```bash
    ls -l diction getopt.c
    # ‘touch’: Change file timestamps
    touch getopt.c
    ls -l diction getopt.c
    make
    ```

    - After `make` runs, we see that it has restored the target to being newer than the dependency
## Installing the program
- Well-packaged source code will often include **a special make target** called `install`. This target will install the final product in a system directory for use
    - Usually, this directory is `/usr/local/bin`, the traditional location for locally built software
    - However, this directory is not normally writable by ordinary users, so we must become the superuser to perform the installation
	
        ```bash
        sudo make install
        which diction
        man diction
        ```

- The `make` program can be used for any task that needs to maintain a `target/dependency` relationship, not just for compiling source code
- > http://www.ruanyifeng.com/blog/2014/11/compiler.html