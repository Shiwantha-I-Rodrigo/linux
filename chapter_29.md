# CLOUD AND VIRTUALIZATION SERVICES

## VIRTUAL MACHINE TOOLS

### libvirt

- a popular virtualization management software.
- an API library incorporated in to several opensource hypervisors (KVM,...).
- livirtd, opertares on the host and execute any VM management tasks.
- most command line utilities that start on "vir" use this library.

.

    $ which virsh
    /usr/bin/virsh

    $ lld /usr/bin/virsh | grep libvirt
    libvirt-lxc.so.0 => /lib64/libvirt-lxc.so.0 (0x00...)
    libvirt.so.0 => /lib64/libvirt.so.0 (0x00...)
    ...

- **libvirt** package comes with **libvirt-client** / **libvirt-clients** package.
- if you have a hypervisor installed, **virsh** can be employed to manage virtual machines.
- **virsh** can be used as an interactive shell or to issue direct commands from bash shell.
- **virt-install** python program is used to create virtual machines.

### virtual machine manager

- not to be confused with hypervisor (VMM).
- is a lightweight desktop application for managing virtual machines.
- **virt-manager** package.
- `$ virt-manager`

## BOOTSTRAPPING

- refer to installing a new system using a config file or image of an earlier system installation.
- in case of managing 100s of VM's, scripts can be used to boot the machines.

### kickstart method ( RHEL )

- ubuntu has ( **preseed** ) and openSUSE has ( **AutoYaST** ).

1. create a kickstart file, to configure the system.
    - this can be made from scratch.
    - or the file made in the previous system installation can be found as **/root/anaconda-ks.cfg**.
    - notice that the user ( including root ) passwords ( encrypted ) are stored in the file.
    - typically **ks.cfg** is used as the file name.
2. store the file in network or a portable storage.
3. place the installation source (ISO file) where it is accessible to the kickstart process.
    - ISO or directory tree (Extracted ISO) can be placed on the same dir as kickstarter file.
4. create the boot medium.
    - create a boot medium ( live-usb / dvd ) in case of physical installation.
    - not required for virtual machines.
5. initiate kickstart.
    - for physical systems, **linux ks=hd:sda1:/ks.cfg** at boot prompt.
    - for virtual systems use **virt-install** with following options.
        + **--initrd-inject /root/VM-Install/ks.cfg**
        + **--extra-args="ks=file:/ks.cfg console=tty0 console=ttyS0,115200n8"**
        + create shell script with above options and use it to create multiple virtual machines.

### cloud-init ( Ubuntu )

- cloud images operating system templates.
- user configs and data are applied to it with **cloud-init** tool.
- written in python.
- availabe for Cloud based virtualization services ( AWS / Microsoft Azure / Digital Ocean / ... ).
- availabe for CLoud based mangement systems ( OpenStack / ... ).
- availabe for local VM's ( VMware / KVM / ... ).
- supports and availabe with most distros.
- allows configuring hostname / temp mount points / etc...
- allows pre generated OpenSSH keys can be set to allow encrypted access to the VM.
- config data is called **user-data**.
- stored in **YAML** format files.
- **/etc/cloud/cloud.cfg** is the primary config file.
- **cloud-init** command.
- cloud init is only used in the host to create virtual mchines.
- for cloud services, provide the YAML file to their bootstrap process.

## STORAGE ISSUES

- a single virtual disk maybe represented as a single or multiple files.
- virtual disk choices directly affect the performance of the virtual system.
- Provisioning : amount of disk space is choosen at the start.
    + Thick provisioning : the full space is pre allocated by the file.
    + Thin provisioning : the space is dynamically expanded when adding data but **does not contract when data is deleted**.
        * thin proisioning is helpful in **overprovisioning** space than availabe physically to scale up later (ie, LVM ).
- Persistent Volumes : data is kept on the disk, wheather the VM is running or not, or even destroyed. ( OpenStack / Kubernetes ).
- Blobs : Microfoft Azure term for large unstructured data offered over the network.
    + can be manipulated with .NET code.
    + consist of images / streaming video / big data / ...
    + blob items are grouped into containers for user accounts. 
    + Block blogs : blocks of binary and text data, limit 4.7TB.
    + Append blobs : blocks of binary and text data optimized for appending operations, used in logging operations.
    + Page blobs : random access files, limit 8TB.

## NETWORK CONFIGURATION

- Two main network virtualization concepts are,
    + VLAN
    + Overlay Network

**VLAN**

- consist of systems and devices on a LAN, the LAN can be distributed physically.
- instead of physical connections, VLAN's are based on logical virtual connections.

**Overlay Network**

- use encapsulation and tunneling.
- devices communicate through tunnels.
- applications manage the network infastructure ( SDN ).
- provide scalability and flexibility.

**Virtual NIC's**

<img src="images/virtual_nic.png">

**Host Only**

+ local adpater
+ connects to the virtual nework with in the host.
+ no direct external connections.
+ fast, since communication between VM's is though RAM ( no wires.. ).
+ used with virtual proxy servers.
+ where one VM can act a as proxy (bridged) to forward the traffic of other VM's (host-only).

<img src="images/virt_host_only.png">

**Bridged**

+ VM gets own IP on the main physical network.
+ used with virtual proxy servers.
+ where one VM can act a as proxy (bridged) to forward the traffic of other VM's (host-only).

<img src="images/virt_bidged.png">

**NAT**

+ IP's of VM's are hidden behind the NAT table.
+ in virtual settings, NAT table is maintained by the hypervisor.

<img src="images/virt_nat.png">

**NAT Network**

+ Similar to NAT but the VM's are on the same virtual network.

<img src="images/virt_nat_network.png">