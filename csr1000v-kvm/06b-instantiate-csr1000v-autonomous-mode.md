# Create and Boot CSR1000v (Autonomous mode)

## Creating the Cisco CSR 1000v VM Using virsh

Using the virt-install command, create the instance and boot, using the following syntax:

```bash
# virt-install                  \
     --connect=qemu:///system   \
     --name=my_csr_vm           \
     --os-type=linux            \
     --os-variant=rhel4         \
     --arch=x86_64              \
     --cpu host                 \
     --vcpus=1,sockets=1,cores=1,threads=1   \
     --hvm                      \
     --ram=4096                 \
     --import                   \
     --disk path=/home/jmb/kvm/disks/CSR-classic.qcow2,bus=ide,format=qcow2   \
     --noreboot
     --accelerate
```

<br>

## Creating the Cisco CSR 1000v VM Using the virt-manager GUI Tool (qcow2 or iso image)

virt-manager, also known as Virtual Machine Manager, is a graphical tool for creating and managing guest virtual machines.

- Step1 - Launch the virt-manager GUI. Click Create a new virtual machine.

- Step2 - Do one of the following: 
  - For .qcow2: Select Import existing disk image.
  - For .iso: Select Local install media (ISO image or CDROM).

- Step 3 - Select the CSR qcow2 or iso file location.

- Step 4 - Configure the memory and CPU parameters.

- Step 5 - Configure virtual machine storage

- Step 6 - Click Finish.

<br>

## Creating the Serial Console Access in KVM

[CCO Reference](https://www.cisco.com/c/en/us/td/docs/routers/csr1000/software/configuration/b_CSR1000v_Configuration_Guide/b_CSR1000v_Configuration_Guide_chapter_0111.html#con_1307796)

Steps

1. Power off the VM.
2. Click on the default Serial 1 device (if it exists) and then click Remove . This removes the default pty-based virtual serial port which would otherwise count as the first virtual serial port.
3. Click Add Hardware.
4. Select Serial to add a serial device.
5. Under Character Device, choose the TCP Net Console (tcp) device type from the drop-down menu.
6. Under Device Parameters, choose the mode from the drop-down menu.
7. Under Host, enter 0.0.0.0. The server will accept a telnet connection on any interface.
8. Choose the port from the drop-down menu.
9. Choose the Use Telnet option.
10. Click Finish.

After the Cisco CSR 1000v has booted successfully, you can change the console port access to the router using Cisco IOS XE commands. After you change the console port access, you must reload or power-cycle the router.

- enable

- configure terminal

- Do one of the following:

- - platform console auto
  - platform console virtual
  - platform console serial

 <br>

## Creating a Bootstrap Day0 Configuration for virt-manager

**Step1** - Create iosxe_config.txt or ovf-env.xml file

See: 

https://www.cisco.com/c/en/us/td/docs/routers/csr1000/software/configuration/b_CSR1000v_Configuration_Guide/b_CSR1000v_Configuration_Guide_chapter_0101.pdf

And

https://www.cisco.com/c/en/us/td/docs/routers/csr1000/software/configuration/b_CSR1000v_Configuration_Guide/b_CSR1000v_Configuration_Guide_chapter_0101.html#con_1337709

**Step2** - Create a disk image from this file using below command

```
mkisofs -l -o config.iso <configuration_filename>
```

**Step3** - Mount the csr_config.iso as an additional disk during creation of the CSR virtual machine.

Using the virt-install command, create the instance and boot, using the following syntax:

```bash
# virt-install                  \
     --connect=qemu:///system   \
     --name=my_csr_vm           \
     --os-type=linux            \
     --os-variant=rhel4         \
     --arch=x86_64              \
     --cpu host                 \
     --vcpus=1,sockets=1,cores=1,threads=1   \
     --hvm                      \
     --ram=4096                 \
     --import                   \
     --disk path=/home/jmb/kvm/disks/CSR-classic.qcow2,bus=ide,format=qcow2   \
     --disk path=config.iso,device=cdrom \
     --noreboot
     --accelerate
```

<br>

## Determine the Active vCPU Distribution Template

To determine which template is being used for vCPU distribution, use the following command:

```
show platform software cpu alloc
```

Example:

```
Router# show platform software cpu alloc
CPU alloc information:
Control plane cpu alloc: 0-1
Data plane cpu alloc: 2-3
Service plane cpu alloc: 0-1
Template used: CLI-service_plane_heavy
The Control plane and the Service plane share cores 0 and 1.
```

<br>

## Mapping the Router Network Interfaces to vNICs

The Cisco CSR 1000v maps the GigabitEthernet network interfaces to the logical virtual network interface card (vNIC) name assigned by the VM. The VM in turn maps the logical vNIC name to a physical MAC address.

When the Cisco CSR 1000v is booted for the first time, the router interfaces are mapped to the logical vNIC interfaces that were added when the VM was created. The figure below shows the relationship between the vNICs and the Cisco CSR 1000v router interfaces.

<br>

## Check Interface Mapping

```
csr51#sh platform software vnic-if interface-mapping
-------------------------------------------------------------
Interface Name        Driver Name         Mac Addr
-------------------------------------------------------------
GigabitEthernet4       net_virtio         fa16.3e03.b123
GigabitEthernet3       net_virtio         fa16.3eb3.fbd2
GigabitEthernet2       net_virtio         fa16.3e11.9801
GigabitEthernet1       net_virtio         fa16.3eca.4015
-------------------------------------------------------------

csr51#
```

<br>

## More Information

https://www.cisco.com/c/en/us/td/docs/routers/csr1000/software/configuration/b_CSR1000v_Configuration_Guide.html

Installing the Cisco CSR 1000v in KVM Environments

https://www.cisco.com/c/en/us/td/docs/routers/csr1000/software/configuration/b_CSR1000v_Configuration_Guide/b_CSR1000v_Configuration_Guide_chapter_0101.html

Creating the Instance Using the OpenStack Command Line Tool

https://www.cisco.com/c/en/us/td/docs/routers/csr1000/software/configuration/b_CSR1000v_Configuration_Guide/b_CSR1000v_Configuration_Guide_chapter_0101.html#task_1320522

Installing the Cisco CSR 1000v in VMware ESXi Environments

https://www.cisco.com/c/en/us/td/docs/routers/csr1000/software/configuration/b_CSR1000v_Configuration_Guide/b_CSR1000v_Configuration_Guide_chapter_011.html

CSR 1000V Software Architecture (extract from Cisco press)

http://www.ciscopress.com/articles/article.asp?p=2514909&seqNum=4