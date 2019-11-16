# Config

```bash
# https://stackoverflow.com/questions/25084288/keep-ssh-session-alive/25087194#25087194
```

# Dependencies

```bash
# GCC
sudo yum -y install gcc

# zlib
curl -o zlib-1.2.11.tar.gz https://jaist.dl.sourceforge.net/project/libpng/zlib/1.2.11/zlib-1.2.11.tar.gz
# https://blog.csdn.net/u012724150/article/details/54836441

yum install curl-devel
# https://stackoverflow.com/questions/8329485/unable-to-find-remote-helper-for-https-during-git-clone/13018777#13018777

# for configure zsh
sudo yum install autoconf

sudo yum install ncurses-devel

# build watch
# http://procps.sourceforge.net/

# build vim
# https://phoenixnap.com/kb/how-to-install-vim-centos-7

# build tmux
# https://github.com/tmux/tmux
  # install automake, yacc
  # http://www.ruanyifeng.com/blog/2019/10/tmux.html

# wget

# Build Python3
    # https://www.code-learner.com/how-to-compile-and-install-python3-from-source-code-in-centos/
        # --with-ssl
    # https://www.cnblogs.com/minglee/p/9232673.html
        # sudo yum install openssl-devel
    # delete and re build altogether
```

# Git

```bash
# https://git-scm.com/download/linux
curl -o git-2.23.0.tar.gz https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.23.0.tar.gz
tar xzf git-2.23.0.tar.gz
./configure # no c compiler
# install gcc
make install # cache.h:21:18: fatal error: zlib.h: No such file or directory
# install zlib and rebuild

# https://docs.gitlab.com/ee/gitlab-basics/create-your-ssh-keys.html
# https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/syncing-a-fork
    # https://blog.csdn.net/wankui/article/details/53328369
```

# zsh, on-my-zsh

```bash
# sudo yum -y install zsh # centos
# zsh --version # too old
git clone https://github.com/zsh-users/zsh.git # fatal: unable to find remote helper for 'https'
# install curl-devel and rebuild git

# https://github.com/zsh-users/zsh/blob/master/INSTALL
# run ./Util/preconfig if no configure is found
./configure
# configure: error: "No terminal handling library was found on your system.
# This is probably a library called 'curses' or 'ncurses'.  You may need to install a package called 'curses-devel' or 'ncurses-devel' on your system."

chsh -s $(which zsh) # type without sudo

https://github.com/robbyrussell/oh-my-zsh
```

# Golang

```bash
curl -o go1.13.3.linux-amd64.tar.gz  https://golang.org/doc/install?download=go1.13.3.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.13.3.linux-amd64.tar.gz

# https://github.com/golang/go/wiki/SettingGOPATH#zsh
# ~/.zshrc
export GOPATH=$HOME/golang
export GOROOT=/usr/local/opt/go/libexec
export GOBIN=$GOPATH/bin

export PATH=$PATH:$GOPATH
export PATH=$PATH:$GOROOT/bin
```

## [Uninstall](https://golang.org/doc/install?download=go1.13.3.linux-amd64.tar.gz#uninstall)

```bash
go version
# go version go1.12.7 darwin/amd64
go env GOROOT
# /usr/local/Cellar/go/1.12.7/libexec

# delete go directory
rm -rf $(go env GOROOT)
rm -rf /usr/local/go
# remove Go bin from PATH environment variable

```

# Docker

```bash
### Docker
## CentOS
# https://www.unixmen.com/enable-disable-repositories-centos/
yum repolist

sudo yum install docker-ce docker-ce-cli containerd.io
# --> Running transaction check
# ---> Package containerd.io.x86_64 0:1.2.10-3.2.el7 will be installed
# --> Processing Dependency: container-selinux >= 2:2.74 for package: containerd.io-1.2.10-3.2.el7.x86_64
# ---> Package docker-ce.x86_64 3:19.03.4-3.el7 will be installed
# ---> Package docker-ce-cli.x86_64 1:19.03.4-3.el7 will be installed
# --> Running transaction check
# ---> Package container-selinux.noarch 2:2.107-3.el7 will be installed
# --> Finished Dependency Resolution

# Install from a package
sudo yum install ./docker-ce-19.03.4-3.el7.x86_64.rpm ./docker-ce-cli-19.03.4-3.el7.x86_64.rpm ./containerd.io-1.2.10-3.2.el7.x86_64.rpm
sudo systemctl start docker
sudo docker run hello-world 

# https://docs.docker.com/install/linux/linux-postinstall/
```

```bash
# Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# auto completion
# https://github.com/robbyrussell/oh-my-zsh/issues/7642#issuecomment-471164659

# set registry
# https://yeasy.gitbooks.io/docker_practice/install/mirror.html
    # https://www.daocloud.io/mirror
# docker info | grep -A 5 "Registry Mirrors"
```

# Fabric

```bash
# Creating build/goshim.tar.bz2
# tar (child): bzip2: Cannot exec: No such file or directory
# tar (child): Error is not recoverable: exiting now
# make: *** [build/goshim.tar.bz2] Error 141
yum install bzip2

# github.com/hyperledger/fabric/vendor/github.com/miekg/pkcs11
# vendor/github.com/miekg/pkcs11/pkcs11.go:29:18: fatal error: ltdl.h: No such file or directory
 #include <ltdl.h>
#                  ^
# compilation terminated.
# make: *** [build/bin/peer] Error 2
yum install libtool-ltdl-devel
```

# [Autojump](https://github.com/wting/autojump)
# Timezone

```bash
tzselect
# You can make this change permanent for yourself by appending the line
# 	TZ='Asia/Shanghai'; export TZ
# to the file '.profile' in your home directory; then log out and log in again.

# Here is that TZ value again, this time on standard output so that you
# can use the /usr/bin/tzselect command in shell scripts:
# Asia/Shanghai
```