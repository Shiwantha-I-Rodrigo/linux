# GOVERNING SOFTWARE

## SOURCE CODE

### DOWNLOAD

Source code can be downloaded with **curl** or **wget**.

Open Source projects may contain,
* Source code files
* Header files
* Library files
* Documentation files

**tar** can be used to bundle these files.

`$ tar -zcvf myproject.tgz project/*` --->  archive and compress files in project dir\
`$ tar -zxvf myproject.tgz` ---> extract files from archive

> **!** when extracting **gunzip** program will **remove the archive** file\
> **!** the **tar -z** program will not.

### COMPILE

A compiling program is required to compile the source code. GNU Compiler Collection ( **gcc** ) is a popular choice for, programs written in C, C++, Ada, Java, Go, Fortan, etc...

In addition a variety of header and library files are required when compiling complex source codes. handling all of these can be very complicated. the **make** utility can simplify this process by allowing developers to create scripts for compilation and installation.

1. **configure script** analyzes the system and generates a customized **Makefile** based on the environment and available libraries.
2. **make** utility uses the generated Makefile to compile the source code into libraries and executables.
3. **make install** command uses the same **Makefile** to copy the compiled files into their appropriate system directories.

> **!** **ldd** utility can detect library files required for C applications.

## PACKAGING

It's a hastle to compile and install software, to further simplify the process pre compiled applications are bundled with all the required files to run the software. these **packages** are managed by the distros **package manager**.

package managers track,
- Application files.
- Library dependencies.
- Application version.

<br>

**DEBIAN**

Debian uses **.deb** package files for distributing software.

**`dpkg `**`[option] [package]`\
--> handles **manually provided** deb packages.

| **Option**|                       | **Description** |
| -         | -                     | - |
| **-C**    |                       |Scans for broken packages and suggests possible fixes|
|           |**--configure**        |Reconfigures an already installed package|
|           |**--get-selections**   |Lists all currently selected (installed) packages|
| **-i**    |                       |Installs a specified package |
| **-I**    |                       |Displays information about a package that is *not* yet installed |
| **-l**    |                       |Lists installed packages that match a specified pattern|
| **-L**    |                       |Lists all files installed by a specified package |
| **-p**    |                       |Shows detailed information about an *installed* package|
| **-P**    |                       |Purges a package, including its configuration files|
| **-r**    |                       |Removes a package but keeps its configuration files|
| **-S**    |                       |Searches for the package that owns a specified file|
     
---

**REDHAT**

Redhat uses **.rpm** package files for distributing software.

**`rpm `**`[option] [package]`\
--> handles **manually provided** rpm packages.

| **Option**| **Description** |
| -         | - |
| **-b**    | Builds a binary package from source files |
| **-e**    | Uninstalls the specified package |
| **-F**    | Upgrades an already installed package to a newer version |
| **-i**    | Installs the specified package |
| **-q**    | Checks whether a specific package is installed |
| **-U**    | Installs or upgrades a package |
| **-V**    | Verifies the presence and integrity of package files |

---

### REPOSITORIES

- Repositories are sources of validated software packages.
- There are repositories maintained by official developers and trusted third parties.

**DEBIAN**

> **!** apt tools rely on **/etc/apt/sources.list** file to list repositories and any 3rd party repo can be added here.

**apt** : a frontend for the apt suite of tools, such as **apt-get** and **apt-cache**.

**`apt-cache `**`[option] [command] [package]`\
--> display information about packages and the package database.

| **Command**    | **Description**|
| -             | - |
| **depends**   | Displays the dependencies required by a package |
| **pkgnames**  | Lists all packages available in the system |
| **showpkg**   | Shows detailed information about a specific package |
| **stats**     | Displays statistics about the package database |
| **unmet**     | Lists any unmet dependencies for installed packages |

---

**`apt `**`[option] [command] [package+++]`\
--> manage packages.

| **Command**       | **Description**|
| -                 | - |
| **install**       | Installs the specified package |
| **reinstall**     | Reinstalls an already installed package |
| **remove**        | Removes a package but keeps its configuration files |
| **autoremove**    | Removes unnecessary dependency packages that are no longer needed |
| **list**          | Lists currently installed packages |
| **purge**         | Removes a package along with its configuration and data files |
| **satisfy**       | Resolves dependencies for installed packages|
| **search**        | Searches for a package in the repositories |
| **show**          | Shows detailed information about a package |
| **full-upgrade**  | Upgrades the system and removes old packages no longer needed |
| **update**        | Updates the local package index from repositories|
| **upgrade**       | Installs available upgrades for installed packages|

---

**REDHAT**

> **!** **dnf** is the newer version of **yum**, and both use the **/etc/yum.repos.d** directory to store repository configuration files.

**`dnf `**`[option] [package+++]`\
--> manage packages

| **Command**           | **Description**|
| -                     | - |
| **install**           | Installs a package from a repository|
| **autoremove**        | Removes unnecessary packages that were installed as dependencies|
| **reinstall**         | Reinstalls a currently installed package|
| **remove**            | Removes a package along with any packages that depend on it |
| **upgrade**           | Upgrades one or more packages to the latest available versions|
| **downgrade**         | Downgrades a package to the version available in the repository |
| **list**              | Lists available or installed packages |
| **check**             | Analyzes installed packages for problems|
| **check-update**      | Checks for available updates for installed packages |
| **clean**             | Clears cached data and temporary files used by repositories |
| **distro-sync**       | Synchronizes installed packages to match versions in the current repositories |
| **group**             | Manages groups of packages as a single unit |
| **history**           | Displays the history of **dnf** transactions|
| **info**              | Shows detailed information about available or installed packages|
| **makecache**         | Downloads and stores metadata for enabled repositories|
| **mark**              | Marks a package as manually installed |
| **module**            | Manages modular content (module streams)|
| **provides**          | Identifies the package that provides a specific file or feature |
| **repoinfo**          | Displays detailed information about enabled repositories|
| **repolist**          | Lists all configured and enabled repositories |
| **repoquery**         | Searches repositories for information about packages|
| **search**            | Searches package metadata for a specified keyword |
| **shell**             | Opens an interactive shell to run multiple **dnf** commands |
| **swap**              | Replaces one package with another (remove and install)|
| **updateinfo**        | Displays information about available updates (eg, security, bug fixes)|
| **alias**             | Defines a custom alias for one or more **dnf** commands |
| **repository-packages**| Executes commands on all packages within a specific repository|
| **upgrade-minimal**   | Upgrades only packages that provide security or bug fixes |

---

### APPLICATION CONTAINERS

> **!** **Containers** package all the files and **dependencies** needed to run an application on any platform, but this can lead to **duplication** of dependencies across applications.

**SNAP (Cannonical) & FLATPAK**

> **!** *snapd* manages *snap* applications.

| **Command**   | **Snap** Description                  | **Command**   | **Flatpak** Description |
| -             | -                                     | -             | - |
| **list**      | Lists installed Snap apps             | **list**      | Lists installed Flatpak apps|
| **find**      | Searches the Snap store               | **search**    | Searches the Flatpak repository |
| **info**      | Displays information about an app     |               |  |
| **install**   | Installs an app                       | **install**   | Installs an app |
| **remove**    | Removes an app                        | **uninstall** | Removes an app|
| **disable**   | Disables an installed app             |               |  |
| **enable**    | Enables a previously disabled app     |               |  |

`$ sudo flatpak remote-add --if-not-exist flathub` -->  add the flathub repo or any 3rd party repo.
> **!** **snap** has it's own repo but it's **automatically configured**, and  **3rd party repos** are **not** supported.

**APP IMAGE**

does not require root privileges to install.\
just mount to home folder and run software.\
each user need seperate app images.
