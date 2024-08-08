# How to configure VirtualBox port forwarding
You'll not be able to directly access your guest virtual machine in
Oracle's VirtualBox if it's under a NAT network. It is because it's in a
completely different private network than the host machine.

For this, you'll need to forward the network traffic from a specific
port of your host IP to a port on your VM's IP. It will allow you to
access your virtual machines from your host machine via protocols
such as SSH or RDP.

To do this, you'll need to configure port forwarding for your
VirtualBox VMs in the NAT network.

## Steps to configure port forwarding in VirtualBox
1. Go to [VirtualBox's](https://www.virtualbox.org/) main interface and make sure the virtual machine that you want to configure is powered off.
2. *Right click* on the virtual machine and click on Settings.
3. *Click* on the Network tab in Settings' main interface.
4. Look for and *click* on Advanced text at the bottom of the window.
5. *Click* on Port Forwarding button.
6. *Click* on the + icon on the upper right to add new port forwarding rule in Port Forwarding's main interface.
7. Some of fields are filled up by default.
8. Fill in the fields accordingly.

| Name | Protocole | Host IP   | Host Port | Guest IP  | Guest Port |
|------|-----------|-----------|-----------|-----------|------------|
| SSH  | TCP       | 127.0.0.1 | 2222      | 10.0.2.15 | 22         |

> Info :
>
> In this example, TCP packets on port 2222 in the host machine will forward to port 22 of the guest machine whereby 10.0.2.15 is the default IP for guest VM under NAT network.
> It's more convenient to leave the Host IP field blank which will default to 127.0.0.1 and Guest IP defaults to whatever the IP address is assigned to the guest VM. This simplifies the configuration and avoid the issue of the Guest IP changing.

9. Test the forwarding rule.

> The above example uses SSH as an example and SSH by default runs on port 22. In this case, the host's port 2222 forwards to the guest's port 22.

# Start a VM in Headless
To start a virtual machine with VBoxHeadless, you have three options.
See the official doc [VBoxHeadless, the Remote Desktop Server](https://www.virtualbox.org/manual/ch07.html#vboxheadless)

You can use VBoxManage :
```shell
VBoxManage startvm "VM name" --type headless
```
The extra `--type` option causes VirtualBox to use `VBoxHeadless` as the front-end to the internal virtualization engine instead of the Qt front-end.

One alternative is to use `VBoxHeadless` directly, as follows :
```shell
VBoxHeadless --startvm <uuid|name>
```

This way of starting the VM helps troubleshooting problems reported by `VBoxManage startvm` ... because you can see sometimes more detailed error messages, especially for early failures before the VM execution is started. In normal situations `VBoxManage startvm` is preferred since it runs the VM directly as a background process which has to be done explicitly when directly starting `VBoxHeadless`.

> The other alternative is to start `VBoxHeadless` from the VirtualBox Manager GUI, by holding the <kbd>Shift</kbd> key when starting a virtual machine or selecting *Headless Start* from the Machine menu.

## VBoxManage list
[The list](https://docs.oracle.com/en/virtualization/virtualbox/6.0/user/vboxmanage-list.html) command gives relevant information about your system and information about Oracle VM VirtualBox's current settings.

The list of all vm :
```shell
vboxmanage list vms
```

The list of all running vm :
```shell
vboxmanage list runningvms
```
