- The most important determinant of distribution quality is the packaging system and the vitality of the distribution’s support community.
- Package management is a method of installing and maintaining software on the system.
# Package systems
- Different distributions use different packaging systems, and as a general rule, a package intended for one distribution is not compatible with another distribution.
- Most distributions fall into one of two camps of packaging technologies: the Debian `.deb` camp and the Red Hat `.rpm` camp.

    Package system | Distributions (partial listing) |
    --|--|
    Debian-style (.deb) | Debian, Ubuntu, Linux Mint, Raspbian
    Red Hat–style (.rpm) | Fedora, CentOS, Red Hat Enterprise Linux, OpenSUSE
    |

    - There are some important exceptions such as Gentoo, Slackware, and Arch, but most others use one of these two basic systems.
# How a package system works
- Virtually all software for a Linux system will be found on the Internet. Most of it will be provided by the distribution vendor in the form of *package files*, and the rest will be available in source code form that can be installed manually.
## Package files
- The basic unit of software in a packaging system is the package file. 
- A package file is a compressed collection of files that comprise the software package. A package may consist of numerous programs and data files that support the programs. 
    - In addition to the files to be installed, the package file also includes metadata about the package, such as a text description of the package and its contents. 
    - Additionally, many packages contain pre- and post-installation scripts that perform configuration tasks before and after the package installation.
- Package files are created by a person known as a package maintainer, often (but not always) an employee of the distribution vendor. The package maintainer gets the software in source code form from the upstream provider (the author of the program), compiles it, and creates the package metadata and any necessary installation scripts. Often, the package maintainer will apply modifications to the original source code to improve the program’s integration with the other parts of the Linux distribution.
## Repositories
- While some software projects choose to perform their own packaging and distribution, most packages today are created by the distribution vendors and interested third parties. Packages are made available to the users of a distribution in central repositories that may contain many thousands of packages, each specially built and maintained for the distribution.
- A distribution may maintain several different repositories for different stages of the software development life cycle. 
    - For example, there will usually be a “testing” repository that contains packages that have just been built and are intended for use by brave souls who are looking for bugs before the packages are released for general distribution. 
    - A distribution will often have a “development” repository where work-in-progress packages destined for inclusion in the distribution’s next major release are kept.
    - A distribution may also have related third-party repositories. These are often needed to supply software that, for legal reasons such as patents or DRM anti-circumvention issues, cannot be included with the distribution. 
        - Perhaps the best known case is that of encrypted DVD support, which is not legal in the United States. 
        - The third-party repositories operate in countries where software patents and anti-circumvention laws do not apply. These repositories are usually wholly independent of the distribution they support, and to use them, one must know about them and manually include them in the configuration files for the package management system.
## Dependencies
- Programs are seldom “stand alone”; rather, they rely on the presence of other software components to get their work done. Common activities, such as input/output, for example, are handled by routines shared by many programs. These routines are stored in *shared libraries*, which provide essential services to more than one program. 
- If a package requires a shared resource such as a shared library, it is said to have a *dependency*. Modern package management systems all provide some method of dependency resolution to ensure that when a package is installed, all of its dependencies are installed, too.
## High- and low-level package tools
- Package management systems usually consist of two types of tools.
    - Low-level tools that handle tasks such as installing and removing package files
    - High-level tools that perform metadata searching and dependency resolution
- While all Red Hat–style distributions rely on the same low-level program (`rpm`), they use different high-level tools.

    Distributions | Low-level tools | High-level tools
    --|--|--|
    Debian-style | `dpkg` | `apt-get`, `apt`, `aptitude`
    Fedora, Red Hat Enterprise Linux, CentOS | `rpm` | `yum`, `dnf`
    |

- Low-level tools also support the creation of package files.
# Common package management tasks
## Finding a package in a repository
- Package search commands

    Style | Command(s) |
    --|--|
    Debian | `apt-get update; apt-cache search search_string`
    Red Hat | `yum search search_string`
    |

## Installing a package from a repository
- High-level tools permit a package to be downloaded from a repository and installed with full dependency resolution.
- Package installation commands

    Style | Command(s) |
    --|--|
    Debian | `apt-get update; apt-get install package_name` |
    Red Hat | `yum install package_name`
    |

## Installing a package from a package file
- If a package file has been downloaded from a source other than a repository, it can be installed directly (though **without** dependency resolution) using a low-level tool.
- Low-level package installation commands

    Style | Command(s) |
    --|--|
    Debian | `dpkg -i package_file`
    Red Hat | `rpm -i package_file`
    |

    - `rpm -i emacs-22.1-7.fc7-i386.rpm`
        - Because this technique uses the low-level `rpm` program to perform the installation, no dependency resolution is performed. If `rpm` discovers a missing dependency, `rpm` will exit with an error.
## Removing a package
- Packages can be uninstalled using either the high-level or low-level tools.
- High-level package removal commands

    Style | Command(s) |
    --|--|
    Debian | `apt-get remove package_name`
    Red Hat | `yum erase package_name`
    |

## Updating packages from a repository
- High-level package update commands

    Style | Command(s) |
    --|--|
    Debian | `apt-get update; apt-get upgrade` (apply all available updates to the installed packages)
    Red Hat | `yum update`
    |

- > https://www.cyberciti.biz/faq/ubuntu-upgrade-update-single-package-using-apt-get/
## Upgrading a package from a package file
- If an updated version of a package has been downloaded from a non-repository source, it can be installed, replacing the previous version.
- Low-level package upgrade commands

    Style | Command(s) |
    --|--|
    Debian | `dpkg -i package_file`
    Red Hat | `rpm -U package_file`
    |

    - `dpkg` does not have a specific option for upgrading a package versus installing one as `rpm` does.
## Listing installed packages
- Commands to display a list of all the packages installed on the system

    Style | Command(s) |
    --|--|
    Debian | `dpkg -l`
    Red Hat | `rpm -qa`
    |

## Determining whether a package is installed
- Commands to display whether a specified package is installed

    Style | Command(s) |
    --|--|
    Debian | `dpkg -s package_name`
    Red Hat | `rpm -q package_name`
    |

## Displaying information about an installed package
- Package information commands

    Style | Command(s) |
    --|--|
    Debian | `apt-cache show package_name`
    Red Hat | `yum info package_name`
    |

## Finding which package installed a file
- Commands to determine what package is responsible for the installation of a particular file

    Style | Command(s) |
    --|--|
    Debian | `dpkg -S file_name`
    Red Hat | `rpm -qf file_name`
    |

    - > `rpm -qf /usr/bin/vim`
- Device drivers are handled in much the same way, except that instead of being separate items in a distribution’s repository, they become part of the Linux kernel. Generally speaking, there is no such thing as a “driver disk” in Linux. Either the kernel supports a device or it doesn’t, and the Linux kernel supports a lot of devices—many more, in fact, than Windows does.
- A lack of driver support is usually caused by one of 3 things.
    - The device is too new. 
        - Since many hardware vendors don’t actively support Linux development, it falls upon a member of the Linux community to write the kernel driver code. This takes time.
    - The device is too exotic. 
        - Not all distributions include every possible device driver. Each distribution builds its own kernels, and since kernels are configurable (which is what makes it possible to run Linux on everything from wristwatches to mainframes), they may have overlooked a particular device. By locating and downloading the source code for the driver, it is possible for you to compile and install the driver yourself. This process is not overly difficult, but it is rather involved.
    - The hardware vendor is hiding something. 
        - It has neither released source code for a Linux driver nor has it released the technical documentation for somebody to create one for them. This means the hardware vendor is trying to keep the programming interfaces to the device a secret. Because we don’t want secret devices in our computers, it is best that you avoid such products.