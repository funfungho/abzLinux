# Network
- network-attached storage (NAS) boxes
## Examining and monitoring a network
### `ping`
- The `ping` command sends a special **network packet** called an `ICMP ECHO_REQUEST` to a specified host. Most network devices receiving this packet will **reply** to it, allowing the network connection to be **verified**
    - It is possible to configure most network devices (including Linux hosts) to ignore these packets
        - This is usually done for security reasons to partially obscure a host from a potential attacker
        - It is also common for firewalls to be configured to block ICMP traffic
- Once started, ping continues to **send packets** at a specified **interval** (the default is **1 second**) until it is interrupted
- After it is interrupted by pressing `ctrl-C`, `ping` prints performance **statistics**
    - A properly performing network will exhibit 0% packet loss
- A successful “ping” will indicate that the elements of the network (its interface cards, cabling, routing, and gateways) are in generally good working order
### `traceroute`
- The `traceroute` program (some systems use the similar `tracepath` program instead) lists all the “hops” network traffic it takes to get from the local system to a specified host
	
    ```bash
    traceroute slashdot.org
    traceroute -T slashdot.org
    traceroute -I slashdot.org
    ```

    - For routers that provided identifying information, we see their hostnames, IP addresses, and performance data, which includes **3 samples of round-trip time** from the local system to the router
    - For routers that do not provide identifying information (because of router configuration, network congestion, firewalls, etc.), we see **asterisks**
        - In cases where routing information is blocked, we can sometimes overcome this by adding either the `-T` or `-I` option to the `traceroute` command
### `ip`
- The `ip` program is a multipurpose network configuration tool that makes use of the full range of networking features available in modern Linux kernels
    - It replaces the earlier and now deprecated `ifconfig` program
- With `ip`, we can examine a system’s network interfaces and routing table
	
    ```bash
    ip a
    # 1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    #     link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    #     inet 127.0.0.1/8 scope host lo
    #     valid_lft forever preferred_lft forever
    #     inet6 ::1/128 scope host
    #     valid_lft forever preferred_lft forever
    # 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    #     link/ether 2e:0d:f2:c9:98:3a brd ff:ff:ff:ff:ff:ff
    #     inet 128.199.172.188/18 brd 128.199.191.255 scope global eth0
    #     valid_lft forever preferred_lft forever
    #     inet 10.15.0.5/16 brd 10.15.255.255 scope global eth0
    #     valid_lft forever preferred_lft forever
    #     inet6 fe80::2c0d:f2ff:fec9:983a/64 scope link
    #     valid_lft forever preferred_lft forever
    # 3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    #     link/ether 02:42:da:b6:b0:73 brd ff:ff:ff:ff:ff:ff
    #     inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
    #     valid_lft forever preferred_lft forever
    #     inet6 fe80::42:daff:feb6:b073/64 scope link
    #     valid_lft forever preferred_lft forever
    # 4: br-a38f6d05141b: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    #     link/ether 02:42:f4:a8:37:7d brd ff:ff:ff:ff:ff:ff
    #     inet 172.18.0.1/16 brd 172.18.255.255 scope global br-a38f6d05141b
    #     valid_lft forever preferred_lft forever
    # 82: br-662eb14f06c0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    #     link/ether 02:42:82:82:a8:e9 brd ff:ff:ff:ff:ff:ff
    #     inet 172.24.0.1/16 brd 172.24.255.255 scope global br-662eb14f06c0
    #     valid_lft forever preferred_lft forever
    #     inet6 fe80::42:82ff:fe82:a8e9/64 scope link
    #     valid_lft forever preferred_lft forever
    # 86: veth2b733c3@if85: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-662eb14f06c0 state UP group default
    #     link/ether 8a:4b:71:1e:e3:46 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    #     inet6 fe80::884b:71ff:fe1e:e346/64 scope link
    #     valid_lft forever preferred_lft forever
    ```

    - The first network interface `lo` is he *loopback interface*, a virtual interface that the system uses to “talk to itself”
    - The second, called `eth0`, is the Ethernet interface
- When performing casual network diagnostics, the important things to look for are the presence of the word **UP** in the **first line** for each interface, indicating that the network interface is **enabled**, and the presence of a valid IP address in the `inet` field on the **third line**
    - For systems using Dynamic Host Configuration Protocol (DHCP), a valid IP address in this field will verify that the DHCP is working
### `netstat`
- The `netstat` program is used to examine various **network settings and statistics**
- Through the use of its many options, we can look at a variety of features in our network setup
	
    ```bash
    netstat -ie

    netstat -r
    ## a typical routing table for a client machine on a local area network (LAN) behind a firewall/router

    # Kernel IP routing table
    # Destination   Gateway         Genmask           Flags MSS Window irtt Iface
    # 192.168.1.0   *               255.255.255.0       U   0   0       0   eth0
    # default       192.168.1.1     0.0.0.0             UG  0   0       0   eth0
    ```

    - Using the `-ie` option, we can examine the **network interfaces**
    - Using the `-r` option will display the kernel’s network routing table. This shows how the network is configured to send packets from network to network
        - The first line of the listing shows the destination `192.168.1.0`. IP addresses that end in zero refer to networks rather than individual hosts, so this destination means any host on the LAN. The next field, `Gateway`, is the name or IP address of the gateway (router) used to go from the current host to the destination network. An asterisk in this field indicates that no gateway is needed
        - The last line contains the destination `default`. This means any traffic destined for a network that is not otherwise listed in the table. In our example, we see that the gateway is defined as a router with the address of `192.168.1.1`, which presumably knows what to do with the destination traffic
## Transporting files over a network
### `ftp`
- `ftp` gets its name from the protocol it uses, the *File Transfer Protocol*
- Before there were web browsers, there was the `ftp` program. `ftp` is used to communicate with FTP servers, machines that contain files that can be uploaded and downloaded over a network
- FTP (in its original form) is not secure because it sends account names and passwords in *cleartext*
    - This means they are not encrypted, and anyone *sniffing* the network can see them
- Because of this, almost all FTP done over the Internet is done by *anonymous FTP servers*. An anonymous server allows anyone to log in using the login name **“anonymous”** and a **meaningless password**
	
    ```bash
    ftp ftp.gnu.org
    cd gnu/diction
    ls
    lcd temp
    # Local directory now /root/temp
    get diction-1.11.tar.gz
    bye
    ```

- Examples of interactive `ftp` commands

    Command | Meaning |
    --|--|
    `ftp fileserver` |  Invoke the ftp program and have it connect to the FTP server `fileserver`.
    `anonymous` | Login name. After the login prompt, a password prompt will appear. Some servers will accept a **blank** password; others will require a password in the form of an email address. In that case, try something like `user@example.com`.
    `cd pub/cd_images/ubuntu-18.04` | Change to the directory on the remote system containing the desired file. Note that on most anonymous FTP servers, the files for public downloading are found somewhere under the `pub` directory.
    `ls` | List the directory on the remote system.
    `lcd Desktop` | Change the directory on the local system to `~/Desktop`. In the example, the `ftp` program was invoked when the working directory was `~`. This command changes the working directory to `~/Desktop`.
    `get ubuntu-18.04-desktop-amd64.iso` | Tell the remote system to transfer the file `ubuntu-18.04-desktop-amd64.iso` to the local system. Since the working directory on the local system was changed to `~/Desktop`, the file will be downloaded there.
    `bye` | Log off the remote server and end the `ftp` program session. The commands `quit` and `exit` may also be used.
    |

- Typing `help` at the `ftp>` prompt will display a list of the supported commands
- Using `ftp` on a server where sufficient permissions have been granted, it is possible to perform many ordinary file management tasks. It’s clumsy, but it does work
### `lftp` - a better `ftp`
- `lftp` works much like the traditional `ftp` program but has many additional convenience features including multiple-protocol support (including HTTP), automatic retry on failed downloads, background processes, tab completion of path names, and many more
### `wget`
- `wget` is useful for downloading content from both web and FTP sites. Single files, multiple files, and even entire sites can be downloaded
- Its any options allow `wget` to recursively download, download files in the background (allowing you to log off but continue downloading), and complete the download of a partially downloaded file
## Secure communication with remote hosts
- `rlogin` and `telnet` kind of programs suffer from the same fatal flaw that the `ftp` program does; they transmit all their communications (including login names and passwords) in cleartext
### `ssh`
- SSH (Secure Shell) solves the two basic problems of secure communication with a remote host
    - It authenticates that the remote host is who it says it is (thus preventing so-called man-in-the-middle attacks)
    - It encrypts all of the communications between the local and remote hosts
- SSH consists of two parts. An SSH server runs on the remote host, listening for incoming connections, by default, on port 22, while an SSH client is used on the local system to communicate with the remote server
- Most Linux distributions ship an implementation of SSH called `OpenSSH` from the OpenBSD project. Some distributions include both the client and the server packages by default (for example, Red Hat), while others (such as Ubuntu) supply only the client
- To enable a system to receive remote connections, it must have the `OpenSSH-server` package installed, configured, and running, and (if the system either is running or is behind a firewall) it must allow incoming network connections on TCP port 22
    - If you don’t have a remote system to connect to but want to try these examples, make sure the `OpenSSH-server` package is installed on your system and use `localhost` as the name of the remote host. That way, your machine will create network connections with itself
- SSH client
	
    ```bash
    ssh remote-sys

    # 要连接的远程主机的用户名和本机的用户名不相同时可以指定不同的用户名
    ssh bob@remote-sys
    # @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    # @ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! @
    # @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    # IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
    # Someone could be eavesdropping on you right now (man-in-the-middle attack)!
    # It is also possible that the RSA host key has just been changed.
    # The fingerprint for the RSA key sent by the remote host is
    # 41:ed:7a:df:23:19:bf:3c:a5:17:bc:61:b3:7f:d9:bb.
    # Please contact your system administrator.
    # Add correct host key in /home/me/.ssh/known_hosts to get rid of this message.
    # Offending key in /home/me/.ssh/known_hosts:1
    # RSA host key for remote-sys has changed and you have requested strict
    # checking.
    # Host key verification failed.
    ```

    - The first time the connection is attempted, a message is displayed indicating that the authenticity of the remote host cannot be established. This is because the client program has never seen this remote host before. To accept the credentials of the remote host, enter `yes` when prompted. Once the connection is established, the user is prompted for a password
    - It is also possible to connect to remote systems **using a different username**
    - This message is caused by one of 2 possible situations. First, an attacker may be attempting a man-in-the-middle attack. This is rare because everybody knows that `ssh` alerts the user to this. The more likely culprit is that the remote system has been changed somehow; for example, its operating system or SSH server has been reinstalled
        - In the interests of security and safety, however, the first possibility should not be dismissed out of hand. Always check with the administrator of the remote system when this message occurs
        - After it has been determined that the message is because of a benign cause, it is safe to correct the problem on the client side. This is done by using a text editor (vim perhaps) to remove the obsolete key from the `~/.ssh/known_hosts` file. The first line contains the offending key in the preceding example message. Delete this line from the file, and the `ssh` program will be able to accept new authentication credentials from the remote system
- `ssh` allows us to execute a single command on a remote system
	
    ```bash
    ssh remote-sys free

    # Perform an `ls` on the remote system and redirect the output to a file on the local system
    # Single quotes are used because we do not want the pathname expansion performed on the local machine;
    # rather, we want it to be performed on the remote system
    ssh remote-sys 'ls *' > dirlist.txt

    # output redirected to a file on the remote machine
    ssh remote-sys 'ls * > dirlist.txt'
    ```

- Part of what happens when you establish a connection with a remote host via SSH is that an *encrypted tunnel* is created between the local and remote systems. Normally, this tunnel is used to allow commands typed at the local system to be transmitted safely to the remote system and for the results to be transmitted safely back
- In addition to this basic function, the SSH protocol allows most types of **network traffic** to be sent through the encrypted tunnel, creating a sort of *virtual private network* (VPN) between the local and remote systems
    - The most common use of this feature is to allow X Window system traffic to be transmitted. **On a system running an X server** (that is, a machine displaying a GUI), it is possible to launch and run an X client program (a graphical application) on a remote system and have its display appear on the local system
    	
        ```bash
        # a Linux system called `linuxbox` is running an X server; we want to run the `xload` program
        # on a remote system named `remote-sys` to see the program’s graphical output on our local system
        [me@linuxbox ~]$ ssh -X remote-sys
        # me@remote-sys's password:
        # Last login: Mon Sep 10 13:23:11 2018
        [me@remote-sys ~]$ xload
        ```
    
        - After the `xload` command is executed on the remote system, its window appears on the local system (on some systems, you may need to use the `-Y` option rather than the `-X` option to do this)
        - 若本机支持显示 UI，可以通过 `ssh` 把远程机器上运行的 UI 程序显示到本机
### `scp` and `sftp`
- The OpenSSH package also includes 2 programs that can make use of an SSH-encrypted tunnel to copy files across the network
    - The first, `scp` (secure copy), is used much like the familiar `cp` program to copy files. The most notable difference is that the source or destination pathnames may be preceded with the name of a remote host, followed by a colon character
    	
        ```bash
        scp remote-sys:document.txt .
        # apply a username to the beginning of the remote host’s name
        # if the desired remote host account name does not match that of the local system
        scp bob@remote-sys:document.txt .
        ```
    
    - `sftp` is a secure replacement for the `ftp` program. `sftp` works much like the original `ftp`; however, instead of transmitting everything in cleartext, it uses an SSH encrypted tunnel. `sftp` has an important advantage over conventional `ftp` in that it **does not require an FTP server** to be running on the remote host. It **requires only the SSH server**. This means that any remote machine that can connect with the SSH client can also be used as an FTP-like server
    	
        ```bash
        sftp remote-sys
        ls
        lcd Desktop
        get ubuntu-8.04-desktop-i386.iso
        bye
        ```
    
        - The SFTP protocol is supported by many of the graphical file managers found in Linux distributions. Using either GNOME or KDE, we can enter a URI beginning with `sftp://` into the location bar and operate on files stored on a remote system running an SSH server
- SSH client for Windows: Putty