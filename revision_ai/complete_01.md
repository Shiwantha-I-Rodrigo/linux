# 🔹 Scenarios

## **Scenario 1: Setting up a new Ubuntu 20.04 development server**

You’ve just provisioned a new Ubuntu 20.04 VM. You need to:

1. Update package lists and upgrade the system.
2. Install `nginx`, `git`, and `curl`.
3. Verify details about the `nginx` package (dependencies, description, and config files).
4. You realize you installed the wrong package version, so you reinstall it.
5. Later, you decide to completely remove `nginx` including config files.
6. Clean up unused dependencies.
7. Check for broken packages and verify dependencies.

---

## **Scenario 2: Debugging a `.deb` package issue**

Your teammate gave you a custom `.deb` package named `myapp_1.0.deb`. You need to:

1. Inspect the `.deb` file to see package info before installing.
2. Install it manually.
3. Verify if the package is installed and check its status.
4. List which files the package installed.
5. Later, you discover conflicts, so you purge it completely.
6. Finally, check if any broken packages remain.

---

## **Scenario 3: Fedora 34 workstation upgrade**

On a Fedora 34 desktop:

1. Update repositories and upgrade all packages.
2. Search for `htop` and check detailed info before installing it.
3. Install `htop` via `dnf`.
4. Use `rpm` to verify which files were installed by `htop`.
5. Later, upgrade an `.rpm` package (`customapp.rpm`) provided by your vendor.
6. You mistakenly installed the wrong package, so you erase it.
7. List all currently installed packages with `rpm`.

---

## **Scenario 4: Managing Flatpak applications**

On Ubuntu, you want to use Flatpak apps:

1. Search for `vlc`.
2. Install it.
3. Check detailed info about the installed app.
4. List all installed Flatpak apps.
5. Update all Flatpak apps.
6. Later, remove `vlc`.

---

## **Scenario 5: Dockerized app testing**

On RockyOS 8.5 server, you’re testing Docker workflows:

1. Pull the latest `nginx` image.
2. Run it in detached mode, mapping port 8080 → 80.
3. List all running containers.
4. Exec into the container to check `/usr/share/nginx/html/index.html`.
5. View logs to confirm it’s serving traffic.
6. List all downloaded images.
7. Remove the container after testing.
8. Push a modified image to Docker Hub.

---

# 🔹 Answer Keys

## **Scenario 1**

```bash
sudo apt update
sudo apt upgrade
sudo apt install nginx git curl
apt show nginx
apt-cache depends nginx
dpkg -L nginx
sudo apt reinstall nginx
sudo apt purge nginx
sudo apt autoremove
sudo apt-get check
```

---

## **Scenario 2**

```bash
dpkg -I myapp_1.0.deb
sudo dpkg -i myapp_1.0.deb
dpkg -s myapp
dpkg -l | grep myapp
dpkg -L myapp
sudo dpkg -P myapp
dpkg -C
```

---

## **Scenario 3**

```bash
sudo dnf update
sudo dnf upgrade
dnf search htop
dnf info htop
sudo dnf install htop
rpm -ql htop
sudo rpm -U customapp.rpm
sudo rpm -e customapp
rpm -qa
```

---

## **Scenario 4**

```bash
flatpak search vlc
flatpak install flathub org.videolan.VLC
flatpak info org.videolan.VLC
flatpak list
flatpak update
flatpak remove org.videolan.VLC
```

---

## **Scenario 5**

```bash
docker pull nginx:latest
docker run -d -p 8080:80 --name mynginx nginx:latest
docker ps
docker exec -it mynginx cat /usr/share/nginx/html/index.html
docker logs mynginx
docker images
docker rm -f mynginx
docker push mydockerhubuser/mynginx:latest
```

---