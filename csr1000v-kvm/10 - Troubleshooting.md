
# Troubleshooting commands

<br>

In the KVM host environment, verify the host topology to find out how many vCPUs are available for pinning by using the following command:

```virsh nodeinfo```

Use the following command to pin the virtual CPUs to sets of processor cores:
```virsh vcpupin <vmname > <vcpu# > <host core# >```

This KVM command must be executed for each vCPU on your Cisco CSR 1000v. The following example pins virtual CPU 1 to host core 3:
```virsh vcpupin csr1000v 1 3```

The following example shows the KVM commands needed if you have a Cisco CSR 1000v configuration with four vCPUs and the host has eight cores:

```bash
# virsh vcpupin csr1000v 0 2
# virsh vcpupin csr1000v 1 3
# virsh vcpupin csr1000v 2 4
# virsh vcpupin csr1000v 3 5
```

<br>

## List VMs 

List VM running:

```bash
# virsh list
 Id   Name                 State
------------------------------------
 2    CSR-sdwan-16.12.1e   running

#
```

List all VMs

```bash
# virsh list --all
 Id   Name                    State
----------------------------------------
 1    my_csr_vm               running
 -    CSR-classic-16.11.01a   shut off
 -    CSR-classic-16.12.1a    shut off
 -    CSR-sdwan-16.12.1e      shut off


#
```

<br>

## Change some guest machine parameters

Your VM config file which is in XML format. The config file is located at /etc/libvirt/qemu directory.

```bash
# ls -l /etc/libvirt/qemu
total 24
-rw------- 1 root root 3801 nov.  26 17:10 CSR-classic-16.11.01a.xml
-rw------- 1 root root 3758 nov.  26 17:20 CSR-classic-16.12.1a.xml
-rw------- 1 root root 5735 nov.  26 17:38 CSR-sdwan-16.12.1e.xml
-rw------- 1 root root 3533 nov.  27 08:32 my_csr_vm.xml
drwxr-xr-x 3 root root 4096 sept. 20 13:19 networks
#
```

This is an auto-generated file.

Changes to this xml configuration should be made using:
```virsh edit my_csr_vm```

The output is an xml representation of the virtual machine properties, or, using virsh terminology, a domain. If you want to change, for example, the number of vcpus, you just have to find the relevant tag and change the value.
Then reboot the virtual machine for the settings to be applied: ```virsh reboot my_csr_vm```

<br>

## Shutdown the VM

Shut down the guest

```bash
# virsh shutdown my_csr_vm
```

Brute force shutdown

```bash
#virsh destroy my_csr_vm
```

<br>

## Cloning a guest

Another utility, virt-clone can be used to create a new virtual machine by cloning an existing one. To proceed, we must first ensure that the guest to be cloned is down, than we run:

```bash
# virt-clone \
—original=my_csr_vm \
—name=my_csr_vm_clone \
--file=/home/jmb/kvm/disks/CSR-classic.qcow2
```

<br>

## Getting Information on the VM

Using virsh to find out the IP address of a Virtual Machine
```virsh domifaddr cedge```

Example:

```bash
# virsh domifaddr  vEdge
 Name       MAC address          Protocol     Address
-------------------------------------------------------------------------------
 vnet0      52:54:00:70:b4:84    ipv4         192.168.122.224/24

#
```

Use the virsh vncdisplay vm-name to get the VNC port number (if VNC is used and has been defined when creating the VM)

```bash
# virsh vncdisplay cedge
:0

#
```

<br>

## Connect to console

```bash
# virsh console cedge
Connected to domain CSR-sdwan-16.12.1e
Escape character is ^]
%IOSXEBOOT-4-BOOT_SRC: (rp/0): Checking grub versions 2.0 vs 2.0
%IOSXEBOOT-4-BOOT_SRC: (rp/0): Bootloader upgrade not necessary.


              Restricted Rights Legend


Use, duplication, or disclosure by the Government is
subject to restrictions as set forth in subparagraph
(c) of the Commercial Computer Software - Restricted
Rights clause at FAR sec. 52.227-19 and subparagraph
(c) (1) (ii) of the Rights in Technical Data and Computer
Software clause at DFARS sec. 252.227-7013.


           Cisco Systems, Inc.
           170 West Tasman Drive
           San Jose, California 95134-1706

#
```

<br>

## Remove VM

To cleanly remove a vm including its storage columes, use the commands shown below.

```bash
# sudo virsh destroy cedge 2> /dev/null
# sudo virsh undefine  cedge
# sudo virsh pool-refresh default
# sudo virsh vol-delete --pool default /home/jmb/kvm/disks/cedge.qcow2
```

