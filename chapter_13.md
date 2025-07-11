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

`$ tar -zcvf myproject.tgz project/*` < archive and compress files in project dir >
`$ tar -zxvf myproject.tgz` < extract files from archive >

> when extracting **gunzip** program will remove the archive file\
> the **tar -z** program will not.

### COMPILE

a compiling program is required to compile the source code.\
GNU Compiler Collection ( **gcc** ) is a popular choice for,\
programs written in C, C++, Ada, Java, Go, Fortan, etc...

in addition a variety of header and library files are required when compiling\
complex source codes. handling all of these can be very complicated.\
the **make** utility can simplify this process by allowing\
developers to create scripts for compilation and installation.

1. **configure** utility analyses the system and customize the make script.
2. **make** utility builds necessary lib and executables to install the program.
3. **make install** install application files in respective locations.

> **ldd** utility can detect library files required for C applications.

## PACKAGING

It's a hastle to compile and install software, to further simplify the process\
pre compiled applications are bundled with all the required files to run\
the software. these **packages** are managed by the distros **package manager**.

package managers track,
- Application files.
- Library dependencies.
- Application version.

**DEBIAN**

Debian uses .deb package files for distributing software.\
**dpkg** handles deb packages.

    -C                  search for broken packages and suggest fixes.
    --configure         reconfigure an installed package.
    --get-selections    display currently installed packages.
    -i                  install package.
    -I                  info about an uninstalled package.
    -l                  list installed packages matching the patern.
    -L                  list installed files associated with the package.
    -p                  info about an installed package.
    -P                  purge package, inc config files.
    -r                  remove package , leave config files.
    -S                  search for package that owns the file.

`$ dpkg -l openssh*` < list packages with 'openssh' in the name >     

**REDHAT**

Redhat uses .rpm package files for distributing software.\
**rpm** handles rpm packages.

    -b      build binary from source files.
    -e      uninstall the package.
    -F      upgrade an already installed package.
    -i      install the package.
    -q      query if the package is installed.
    -U      install / upgrade package.
    -V      verify if package files are present.

`$ rpm -e zsh` < remove zsh package >

### REPOSITORIES

repositories contain tested software. \
there are official and 3rd part repos.

**DEBIAN**

**apt** is a frontend for the apt suite of tools, such as *apt-get* and *apt-cache*.
- apt-get < install, update, remove packages >
- apt-cache < provide info about the package database >

apt tools rely on **/etc/apt/sources.list** file to list repositories.\
any 3rd party repo can be added here.

    >> apt-cache
        depends         show dependencies required for package.
        pkgnames        list all packages installed on system.
        showpkg         show info about the package.
        stats           show package stats.
        unmet           show any unmet dependencies for packages.
    
    >> apt
        install         install package
        reinstall       reinstall installed package.
        remove          remove package, keep config files.
        autoremove      remove unnessasery dependency packages.

        list            list current installed packages.
        purge           remove package and configuration / data.
        satisfy         resolve dependencies for installed pakages.
        search          search for a package.
        show            show info on package.
        full-upgrade    similar to upgrade, but removes any old packages\
                        required to upgrade the entire system.
        

`$ sudo apt install vlc` < install vlc from repo >


**REDHAT**

yum < old >\
dnf < new >

**yum** and **dnf** use **/etc/yum.repos.d** folder to store repo files.

    >> dnf
        install         install package from repo.
        autoremove      remove unnessasery dependency packages.
        reinstall       reinstall package
        remove          remove package and any package that depend on that package.
        upgrade         upgrade package or all packages.
        downgrade       downgrade the package to the version on repo.

        list            list available and installed packages.
        check           analyse local packages for problems.
        check-update    check update for the package.
        clean           clean repo temp files.
        distro-sync     downgrade or install packages to match current repos.
        group           manage a set of packages as one entity.
        history         list previous dnf commands.
        info            info about installed and available packages.
        makecache       download metadata for repos.
        mark            mark a package as installed.
        module          manage module packages
        provides        show the package that installed the file.
        repoinfo        show repo info.
        repolist        list current repos.
        repoquery       search repos for package.
        search          search package metadata for keyword.
        shell           interactive shell to enter multiple dnf commands.
        swap            remove and reinstall package.
        updateinfo      show update messages.
        alias           define an alias to a list of other dnf commands.
        repository-
        packages        run command on all packages in repo.
        upgrade-
        minimal         upgrade only packages that provide a bug/security fix.


`$ sudo dnf install vlc` < install vlc from repo >

### APPLICATION CONTAINERS

> Containers include all files required to run the application, on any platform.\
> the downside is that dependencies are duplicated for each application.

**SNAP**

! by cannonical.\
*snapd* application manages *snap* applications.

    list        list installed snap apps.
    find        search snap repo.
    info        info about the app.
    install     install app.
    remove      remove app.
    disable     disable app.
    enable      enable app.

**FLATPAK**

`$ sudo dnf install flatpak`\
`$ sudo flatpak remote-add --if-not-exist flathub` < add any repo, here flathub repo is added >

    list        list installed flatpak apps.
    search      search repo.
    install     install app.
    uninstall   unistall app.

**APP IMAGE**

does not require root privileges to install.\
just mount to home folder and run software.\
each user need seperate app images.
