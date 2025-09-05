## **Scenario 1: Debian/Ubuntu System Maintenance**

```bash
# 1. Update package list and upgrade
sudo apt update
sudo apt upgrade -y

# 2. Install vim and curl
sudo apt install -y vim curl

# 3. Search for htop and show details
apt search htop
apt show htop

# 4. Reinstall curl
sudo apt reinstall -y curl

# 5. Remove nano but keep config
sudo apt remove -y nano

# 6. Remove unused dependencies
sudo apt autoremove -y

# 7. Install sample.deb manually
sudo dpkg -i sample.deb

# 8. Check package status and list installed files
dpkg -s sample
dpkg -L sample

# 9. Show dependencies of htop
apt-cache depends htop
```

---

## **Scenario 2: Fedora/RHEL System Management**

```bash
# 1. Update metadata and upgrade packages
sudo dnf update -y
sudo dnf upgrade -y

# 2. Install git and wget
sudo dnf install -y git wget

# 3. Remove vim if exists
sudo dnf remove -y vim

# 4. Search for htop and show detailed info
dnf search htop
dnf info htop

# 5. Install sample.rpm manually
sudo rpm -i sample.rpm

# 6. List files installed by sample.rpm
rpm -ql sample

# 7. Query which package owns /usr/bin/git
rpm -qf /usr/bin/git

# 8. Upgrade sample.rpm if newer version available
sudo rpm -U sample.rpm
```

---

## **Scenario 3: Mixed Package Queries**

```bash
# Ubuntu: list installed packages containing "lib"
dpkg -l | grep lib

# Show reverse dependencies and versions of curl
apt-cache showpkg curl

# Check for broken packages
sudo dpkg -C

# Display unmet dependencies
apt-cache unmet

# Fedora: list installed packages starting with python
dnf list installed "python*"

# Verify dependencies of wget
dnf repoquery --requires wget
```

*(Note: `dnf repoquery` is commonly used for dependency queries; if restricted to listed commands only, you can use `rpm -qR wget`.)*

---

## **Scenario 4: Flatpak App Management**

```bash
# Search for app
flatpak search org.gnome.Calculator

# Install from reference
flatpak install flatpak_test_app.ref -y

# List installed apps
flatpak list

# Show detailed info
flatpak info org.gnome.Calculator

# Update all apps
flatpak update -y

# Remove the app
flatpak remove org.gnome.Calculator -y
```

---

## **Scenario 5: Docker Container Lifecycle**

```bash
# Pull ubuntu image
docker pull ubuntu:latest

# Run container and mount /tmp
docker run -it -v /tmp:/tmp --name test_container ubuntu:latest

# List running containers
docker ps

# Execute apt update inside container
docker exec test_container apt update

# View container logs
docker logs test_container

# Remove container
docker rm test_container

# Import docker image tarball
docker load < docker_test_image.tar

# List local images
docker images

# Tag and push image to registry
docker tag local_image_name registry.example.com/local_image_name
docker push registry.example.com/local_image_name
```

---

## **Scenario 6: Combined Real-World Simulation**

This scenario is a combination of the previous answers:

```bash
# Debian/Ubuntu
sudo apt update && sudo apt upgrade -y
sudo apt install -y vim curl
sudo apt remove -y nano
sudo apt autoremove -y
sudo dpkg -i sample.deb
dpkg -s sample
dpkg -L sample

# Fedora/RHEL
sudo dnf update -y && sudo dnf upgrade -y
sudo dnf install -y git wget
sudo dnf remove -y vim
sudo rpm -i sample.rpm
rpm -ql sample
rpm -qf /usr/bin/git
sudo rpm -U sample.rpm

# Flatpak
flatpak install flatpak_test_app.ref -y
flatpak list
flatpak info org.gnome.Calculator
flatpak update -y
flatpak remove org.gnome.Calculator -y

# Docker
docker pull ubuntu:latest
docker run -it -v /tmp:/tmp --name test_container ubuntu:latest
docker exec test_container apt update
docker logs test_container
docker rm test_container
docker load < docker_test_image.tar
docker images
docker tag local_image_name registry.example.com/local_image_name
docker push registry.example.com/local_image_name
```

---
