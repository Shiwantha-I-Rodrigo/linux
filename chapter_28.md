# CLOUD SERVICES & VIRTUALIZATION

## CLOUD SERVICES

- Refer to distributed computing, where resources are shared between systems to accomplish a task.
- AWS / Microsoft Azure / Google Cloud Platform

| **Cloud Deployment Model** | **Description**                                                                     |
| -------------------------- | ----------------------------------------------------------------------------------- |
| **Internal / Private**     | Organization has its own computing resources within internal networks.              |
| **3rd Party / Public**     | All the computing resources are provided by an external entity.                     |
| **Hybrid**                 | Some resources are provided internally, while supplementary resources are external. |

<img src="images/cloud_types.png">

- there are 3 types of cloud services.
    + IaaS : provide **low level resources** such as hardware.
    + PaaS : provide **hardware and OS**.
    + SaaS : provide complete **application** environments.

<img src="images/cloud_services.png">

---

## VIRTUAL SYSTEMS

- Traditional Serving use multiple physical servers.

<img src="images/traditional_server.png">

- Virtualized Services.

<img src="images/server_virtualization.png">

- with this development, the virtual servers must share physical resources.
    + Type 1 Hypervisor.
    + Type 2 Hypervisor.
    + KVM Hypervisor.
    + Containers.

<img src="images/virtual.png">

### TYPE 1 HYPERVISORS

- bare-metal hypervisors.
- allocates resources to virtual machines as required.
- **KVM** is considered a Type 1 hypervisor. ( Intel / AMD )
- **XEN** is a Type 1 hypervisor. ( Intel / AMD / ARM ).

### TYPE 2 HYPERVISORS

- **Hosted hypervisors** run virtual machines as separate processes on the host operating system.
- Common hosted hypervisors include **VMware**, **QEMU**, and **VirtualBox**.
- **Open Virtualization Format (OVF)** is an open-source standard for configuring virtual machines.
- **OVF** uses a single **XML file** each to define hardware requirements, network, virtual drives, and other settings.
- **Open Virtualization Appliance (OVA)** simplifies distribution by bundling all OVF-related files into a **single tar archive**.


### CONTAINERS

- Traditional virtual environments scatter application files across directories.
- **Containers** bundle all necessary files to run an application, making them **portable** and **lightweight** since they don't include an entire OS.
- **LXC** is a heavier container type that includes a partial OS.
- **LXD** is a container engine that manages **LXC** containers and has evolved into a full container management solution, currently supporting **Linux only**.
- **Docker** is an extremely lightweight container system with a daemon that manages containers on a Linux system.
- **Containers are OS-specific**—Windows containers cannot run on Linux hosts and vice versa.

---

**DOCKER**

**`docker `**`[action] [option]`\
--> manage docker containers.

| **Command**   | **Description**                                               | **Example**|
| -             | -                                                             | - |
| **build**     | Create a new container image from a config file.              | `docker build -t myimage .`|
| **images**    | List locally stored containers.                               | `docker images`|
| **inspect**   | Detailed information about containers.                        | `docker inspect ( container_id / container_id )`|
| **kill**      | Stop a running container.                                     | `docker kill container_id`|
| **login**     | Login to Docker Hub.                                          | `docker login`|
| **logs**      | Retrieve logs from running container.                         | `docker logs container_id`|
| **ps**        | List running containers.                                      | `docker ps`|
| **pull**      | Retrieve container from Docker Hub.                           | `docker pull ubuntu`|
| **push**      | Commit a container image to Docker Hub.                       | `docker push username/myimage`|
| **rm**        | Delete a stopped container.                                   | `docker rm container_id`|
| **rmi**       | Delete a container image from local storage.                  | `docker rmi myimage`|
| **run**       | Start a container image.                                      | `docker run -it ubuntu`|
| **stop**      | Stop a running container.                                     | `docker stop container_id`|
| **start**     | Start a stopped container.                                    | `docker start container_id`|
| **attach**    | Connects the host STDIN, STDOUT, and STDERR to the container. | `docker attach container_id`|
| **commit**    | Create a new image of an edited container on local system.    | `docker commit container_id myimage`|
| **cp**        | Copies files between local system and container.              | `docker cp file container_id:/path/`|
| **exec**      | Connect to running container and execute a command.           | `docker exec -it container_id bash`|
