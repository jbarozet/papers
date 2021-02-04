# 04 - Create Ubuntu Cloud Package

## Objective
Create a package file named"vedge-19.2.0.tar.gz" that contains:
+ qcow2 image
+ user-data
+ meta-data
+ image_properties.xml
+ package.mf

Using the following input files:
+ bionic-server-cloudimg-amd64.qcow2
+ user-data: Contains the init script
+ meta-data: Contains hostname

The day0 config (clouding.cfg) is included in the package and therefore needs to be tokenised to that you can change the parameters for each new deployment.

<br>

## Get Ubuntu Cloud image
Cloud Images:
+ Ubuntu: https://cloud-images.ubuntu.com/
+ CentOS: https://cloud.centos.org/centos/
+ Debian: https://cdimage.debian.org/cdimage/openstack/current/

Download the latest Ubuntu 18.04 (bionic) Server image:
```
$ wget https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img
```

This img file is actually a qcow2 image. So you can safely rename extension to qcow2.
(You can check this is a qcow2 image with qemu-img info <filename>)

The virtual disk size is likely to be quite small. We may choose to increase the size before we use it for any virtual system. We can query the current size:
Install qemu-utils package:
```
# sudo apt-get install qemu-utils
```

Get the image size:
```
$ qemu-img info bionic-server-cloudimg-amd64.qcow2
image: bionic-server-cloudimg-amd64.qcow2
file format: qcow2
virtual size: 8.0G (8589934592 bytes)
disk size: 898M
cluster_size: 65536
Format specific information:
    compat: 0.10
    refcount bits: 16
$
```

We will use the target size and make a virtual disk of 20GB. The actual size used should not change very much if at all:
```
$ qemu-img resize bionic-server-cloudimg-amd64.qcow2 20G
Image resized.
```

On checking the information again, we can see the resize worked:
```
$ qemu-img info bionic-server-cloudimg-amd64.qcow2
image: bionic-server-cloudimg-amd64.qcow2
file format: qcow2
virtual size: 20G (21474836480 bytes)
disk size: 328M
cluster_size: 65536
Format specific information:
    compat: 0.10
    refcount bits: 16
$
```

<br>

## Bootstrap Files

We will need to customize the image at first boot. The cloud systems are normally limited to key based authentication over SSH. This means that we cannot use a username and password. We will either need to inject SSH keys or set the password for the default user (or root which is less secure and not recommended, but here we just illustrate for a lab). We will also take the opportunity to set the host name. 

**meta-data file:**
```
instance-id: ubuntu-bionic
hostname: ubuntu-bionic
local-hostname: ubuntu-bionic
```

**user-data file:**
```
#!/bin/bash
passwd root << EOF

cisco123
cisco123










Y
EOF
echo "Cloud-init running user-data off config drive (/dev/sr0)"

echo Setting up interfaces and addresses

ifconfig ens3 down
ifconfig ens3 $NICID_0_IP_ADDRESS netmask $NICID_0_NETMASK

ifconfig ens4 down
ifconfig ens4 $IP_ADDRESS netmask $NETMASK

route add default gw $GATEWAY ens3

netstat -rn
adduser lab sudo
ifconfig ens3 up
ifconfig ens4 up
sed -i 's/PermitRootLogin no/PermitRootLogin yes/' /etc/ssh/sshd_config

sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config

service ssh restart
```

These 2 files will be used in the bootstrap options when creating the VM package for NFVIS (see next section).

IMPORTANT NOTE: 
+ ${NICID_0_IP_ADDRESS}
+ ${NICID_0_NETMASK} and 
+ ${NICID_0_CIDR_PREFIX} 

are reserved parameters. The VM Lifecycle Manager (ESC-Lite) on NFVIS will replace these variables with values assigned by ESC-Lite. This is used for the int-mgmt-net addresses. They are assigned by NFVIS and not by DHCP.

<br>

## Create ubuntu Package

Package built with the following command:
```
./nfvpt.py -o ubuntu-bionic -i bionic-server-cloudimg-amd64.qcow2 -n ubuntu-bionic -t LINUX -r 18.04 --monitored true --min_vcpu 1 --max_vcpu 2 --min_mem 1024 --max_mem 8192 --min_disk 10 --max_disk 40 --vnic_max 4 --optimize true --interface_hot_add true --interface_hot_delete false --console_type_serial true --mgmt_vnic 0 --nocloud true --bootstrap_cloud_init_drive_type cdrom --bootstrap user-data:user-data --bootstrap meta-data:meta-data --profile ubuntu-tiny,"Ubuntu tiny profile",1,1024,20480 --default_profile ubuntu-tiny --custom key:IP_ADDRESS,val: --custom key:NETMASK,val: --custom key:GATEWAY,val:
```

Options:
```
--mgmt_vnic MGMT_VNIC
VM management interface identifier

--vnic_names VNIC_NAMES
list of vnic number to name mapping in format
number:name example --vnic_names
1:GigabitEthernet2,2:GigabitEthernet4

--bootstrap_cloud_init_bus_type {ide,virtio}
default is ide

--bootstrap_cloud_init_drive_type {cdrom,disk}
Mounts day0 config file as disk (default is CD-ROM)
default is cdrom
```

Note:
+ Generated with python2
+ Does not work with python3

Generates VM package file:
+ ubuntu-bionic.tar.gz
