# Permissions
- Operating systems in the Unix tradition differ from those in the MS-DOS tradition in that they are not only multitasking systems but also multiuser systems
- It means that more than one person can be using the computer at the same time
    - If a computer is attached to a network or the Internet, remote users can log in via `ssh` (**secure shell**) and operate the computer
    - In fact, remote users can execute graphical applications and have the graphical output appear on a remote display. The X Window System supports this as part of its basic design
## Owners, group members, and everybody else

```bash
file /etc/shadow
less /etc/shadow
```

- In the Unix security model, a user may own files and directories
    - When a user owns a file or directory, the user has control over its access
- Users can, in turn, belong to a group consisting of one or more users who are given access to files and directories by their owners
- In addition to granting access to a group, an owner may also grant some set of access rights to everybody, which in Unix terms is referred to as the *world*
- Find out information about your identity
	
    ```bash
    id
    ```

    - When user accounts are created, users are assigned a number called a user ID (`uid`), which is then, for the sake of the humans, mapped to a username
    - The user is assigned a group ID (`gid`) and may belong to additional groups
    - Fedora starts its numbering of regular user accounts at 500, while Ubuntu starts at 1,000
    - Ubuntu user belongs to a lot more groups. This has to do with the way Ubuntu manages privileges for system devices and services
- User accounts are defined in the `/etc/passwd` file, and groups are defined in the `/etc/group` file
    - For each user account, the `/etc/passwd` file defines the user (login) name, uid, gid, account’s real name, home directory, and login shell
    - If we examine the contents of `/etc/passwd` and `/etc/group`, we notice that besides the regular user accounts, there are accounts for the superuser (uid 0) and various other **system users**
    - When user accounts and groups are created, these files are modified along with `etc/shadow`, which holds information about the user’s password
- While many Unix-like systems assign regular users to a common group such as `users`, modern Linux practice is to create a unique, single-member group with the same name as the user
    - This makes certain types of permission assignment easier
### Reading, writing, and executing