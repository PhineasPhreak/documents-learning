# KVM hypervisor
KVM (Kernel-based Virtual Machine) is the leading open source virtualisation technology for Linux. It installs natively on all Linux distributions and turns underlying physical servers into hypervisors so that they can host multiple, isolated virtual machines (VMs). KVM comes with no licenses, type-1 hypervisor capabilities and a variety of performance extensions which makes it an ideal candidate for virtualisation and cloud infrastructure implementation. But what are the benefits of KVM hypervisor and how do you get started?

# What is KVM hypervisor?
KVM hypervisor enables full virtualisation capabilities. It provides each VM with all typical services of the physical system, including virtual BIOS (basic input/output system) and virtual hardware, such as processor, memory, storage, network cards, etc. As a result, every VM completely simulates a physical machine.

KVM is available as a Linux kernel module. It plugs directly into the kernel’s code and allows it to function as a hypervisor. Every VM runs as a separate Linux process under [systemd](https://systemd.io/), with dedicated virtual hardware resources attached.  KVM can only be used on a processor with hardware virtualisation extensions, such as [Intel-VT](https://www.intel.com/content/www/us/en/virtualization/virtualization-technology/intel-virtualization-technology.html) or [AMD-V](https://www.amd.com/en/technologies/virtualization-solutions).

# KVM hypervisor benefits
The main benefit of the KVM hypervisor is its native availability on Linux. Since KVM is part of Linux, it installs natively, enabling straightforward user experience and smooth integration. But KVM brings more benefits compared to other virtualisation technologies. Those include:

*	**Performance** – One of the main drawbacks of traditional virtualisation technologies is performance degradation compared to physical machines. Since KVM is the type-1 hypervisor, it outperforms all type-2 hypervisors, ensuring near-metal performance. With KVM hypervisor VMs boot fast and achieve desired performance results.

*	**Scalability** – As a Linux kernel module, the KVM hypervisor automatically scales to respond to heavy loads once the number of VMs increases. The KVM hypervisor also enables clustering for thousands of nodes, laying the foundations for cloud infrastructure implementation.

*	**Security** – Since KVM is part of the Linux kernel source code, it benefits from the world’s biggest open source community collaboration, rigorous development and testing process as well as continuous security patching.

*	**Maturity** – KVM was first created in 2006 and has continued to be actively developed since then. It is a 15-year old project, presenting a high level of maturity. More than 1,000 developers around the world have contributed to KVM code.

*	**Cost-efficiency** – Last but not least, the cost is a driving factor for many organisations. Since KVM is open source and available as a Linux kernel module, it comes at zero cost out of the box. Businesses can optionally subscribe to various commercial programmes, such as UA-I (Ubuntu Advantage for Infrastructure) to receive enterprise support for their KVM-based virtualisation or cloud infrastructure.

# How to install KVM on `Ubuntu LTS`
In the following section, we present how to install KVM on Ubuntu LTS.

# Prerequisites
## Check Virtualization Support
Before you begin with installing KVM, check if your CPU supports hardware virtualization
```shell
egrep -c '(vmx|svm)' /proc/cpuinfo
```

> `vmx` : for Intel CPUs
>
> `svm` : for AMD CPUs

# Installation
## Step 1: Check virtualisation capabilities
Execute the following command to make sure your processor supports virtualisation capabilities with `cpu-checker`:
```shell
sudo apt install --yes cpu-checker
kvm-ok  # Command to test capabilities of KVM
```
The output of this command is pretty straightforward and clearly indicates whether KVM can be used or not:
```shell
INFO: /dev/kvm exists
KVM acceleration can be used
```

## Step 2: Install required packages
On your Ubuntu 20.04 execute the following command to install the required packages:
```shell
sudo apt install --yes bridge-utils libvirt-clients libvirt-daemon qemu qemu-kvm
```

## Step 3: Authorize Users
Only members of the libvirt and kvm user groups can run virtual machines. Add a user to the libvirt group by typing:

```shell
sudo adduser [username] libvirt
```
> Replace `username` with the actual username.

Now do the same for the kvm group:
```shell
sudo adduser [username] kvm
```

## Step 4: Verify the Installation
Confirm the installation was successful by using the virsh command:
```shell
virsh list --all
```
You can expect the column `ID`, `Name` and `State` Empty.

Use the `systemctl` command to check the status of `libvirtd`:
```shell
sudo systemctl status libvirtd
```
If everything is functioning properly, the output returns an **active (running)** status.


If the virtualization daemon is not active, activate it with the following command:
```shell
sudo systemctl enable --now libvirtd
```

# Creating a Virtual Machine
## Install virt-manager
Before you choose one of the two methods listed below, install `virt-manager`, a tool for creating and managing VMs:
```shell
sudo apt install virt-manager
```
# Installing the QEMU Guest Agent on Virtual Machine
The QEMU guest agent is a daemon that runs on the virtual machine and passes information to the host about the virtual machine, users, file systems, and secondary networks.

## Installing QEMU guest agent on a `Linux` virtual machine

1.  Install the QEMU guest agent on the virtual machine.
```shell
sudo apt install qemu-guest-agent
```
2.  Ensure the service is persistent and start it:
```shell
systemctl enable --now qemu-guest-agent
```

## Installing QEMU guest agent on a `Windows` virtual machine
For Windows virtual machines, the QEMU guest agent is included in the VirtIO drivers, which can be installed using one of the following procedures:
[Procedure RedHat](https://docs.openshift.com/container-platform/4.7/virt/virtual_machines/virt-installing-qemu-guest-agent.html#installing-qemu-guest-agent-on-a-windows-virtual-machine)
