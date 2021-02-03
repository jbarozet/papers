# Create Ubuntu/CentOS Images

<br>

## Downloading Cloud Images

 The first step is to locate the cloud image you need. I would suggest using your favoured search engine andlook for “<distro> clould images”.

Cloud Images:

- Ubuntu:     https://cloud-images.ubuntu.com/
- CentOS:     https://cloud.centos.org/centos/
- Debian:     https://cdimage.debian.org/cdimage/openstack/current/

 <br>

Once downloaded, I would recommend leaving the image untouched on the download location such as home directory. It can be copied to the correct images directory later. Copy the file than move, as it leaves you an untouched master image that you can copy again later should you need it again.

 <br>

To download the latest Ubuntu 18.04 Server image:

```
$ wget https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img
```

To download a recent CentOS 7 image:

```
$ wget https://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud-1809.qcow2
```

<br>

Note: The default user account on Ubuntu is “ubuntu” and on CentOS it is “centos”.

 

# Resize Virtual Disk Size

Install qemu utils

```bash
# sudo apt-get install qemu-utils
```

<br>

The virtual disk size is likely to be quite small. We may choose to increase the size before we use it for any virtual system. We can query the current size:

```bash
# qemu-img info CentOS-7-x86_64-GenericCloud-1905.qcow2
image: CentOS-7-x86_64-GenericCloud-1905.qcow2
file format: qcow2
virtual size: 8.0G (8589934592 bytes)
disk size: 898M
cluster_size: 65536
Format specific information:
    compat: 0.10
    refcount bits: 16
#
```

We will use the target size and make a virtual disk of 10GB. The actual size used should not change very much if at all:

```bash
# qemu-img resize CentOS-7-x86_64-GenericCloud-1905.qcow2 20G
Image resized.
```

On checking the information again, we can see the resize worked:

```bash
# qemu-img info CentOS-7-x86_64-GenericCloud-1905.qcow2
image: CentOS-7-x86_64-GenericCloud-1905.qcow2
file format: qcow2
virtual size: 20G (21474836480 bytes)
disk size: 898M
cluster_size: 65536
Format specific information:
    compat: 0.10
    refcount bits: 16
#
```

<br>

## Copy Master Disk

 We can now copy the disk file to /var/lib/libvirt/images/ in readiness to be used by a Virtual Machine. We also can use the qemu-img command to run the copy.

```
# sudo qemu-img convert -f CentOS-7-x86_64-GenericCloud-1905.qcow2 /var/lib/libvirt/images/mycentos.img
```

Even through the image we downloaded was in the correct qcow2 format we still are able to use this command as an effective copy and ensures that the target file is qcow2 irrelevant of the format downloaded.

<br>

## Create a Cloud-Config file

We will need to customize the image at first boot. We mentioned before that the cloud systems are normally limited to key based authentication over SSH. This means that we cannot use a username and password. We will either need to inject SSH keys or set the password for the default user. We will also take the opportunity to set the host name.

 <br>

### user-data file

The user-data file can contain a number of different directives to help you customize your cloud server. For this tutorial, we will just introduce a couple of them. And we define a new user and password (this is just for a lab, not nothing for production). We will create a text file, this file must start with #cloud-config.

```
#cloud-config
package_upgrade: true
packages:
- git
users:
- name: cisco
gecos: cisco
passwd: $6$rounds=4096$nNAe9lOZ2wbBxB$wRVkGzGloMmLyXMV1brbLjBC4FEH/MiA0LRM/cIamB/zIqUvAAsUQuokFxfjT22xxx48CcNKuqZaVA5RDuZEm0
lock-passwd: false
sudo: ALL=(ALL) NOPASSWD:ALL
ssh_authorized_keys:
- ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCzqYcd21wiBr0+hqYaJ2+wuoprbtr7PkpMzLuGNz88nIenDmeKsJAGMRzSnU2x6cAujzL5YD8O4cGD599prkI7IgGXB8hUYMifXomDeikGreSjBgGO1RAeoGMz6DRWWqOOyO5r0+
dklBJMxJXuI2UFfbbRxDLg+/RdJLR8dV4MaQ67wNtyWXpJQcVZuP5mYi9VxHkBnEHxtWkS6bQ+pO/6LkaPuTyoKzCYFfFeItNdbB0smRZ8bfkbbNE4RjwJAzXACU9rddqq8ubQMloFFjEnsQTTc/e1lhU4EGNbVku52oeKazVaZ/C5rpd9bV6e
ManCuz8pJN9qIQaQzCgF8qVD jbarozet@JBAROZET-M-D2RK

system_info:
default_user:
name: centos
plain_text_passwd: 'centos'
gecos: centos
```

<br>

**Notes**:

- The first line #cloud-config is required to indicate that this is a configuration file for cloud-init.
- The package_upgrade: true line instructs the server to immediately update the OS and installed packages when the server is created.
- The packages: section can be used to indicate which additional packages you would like to have installed. In this case, we've asked for just one, git, to be     installed.
- The users: section allows us to add some users to the system. You will want to customize the information here using your favorite text editor. Be sure to provide a valid SSH public key so that you can access the server once it is deployed.

 <br>

**passwd**: The hash -- not the password itself -- of the password you want to use for this user. 

You can generate a safe hash via:

```bash
# mkpasswd --method=SHA-512 --rounds=4096
```

The above command would create from stdin an SHA-512 password hash with 4096 salt rounds)

 user-data example1:

```
#cloud-config
password: Password1
chpasswd: { expire: False }
ssh_pwauth: True
hostname: proxy1
```

user-data example2:

```
#cloud-config
users:
  - default
  - name: jmb
    ssh-authorized-keys:
      - <your user public key here>
    sudo: ALL=(ALL) NOPASSWD:ALL
    groups: sudo
    shell: /bin/bash
```

user-data example3:

```
#cloud-config
# Set default user and their public ssh key
# eg. https://github.com/ebal.keys
users:
  - name: ${GITHUB_USERNAME}
    ssh-authorized-keys:
      - `curl -s -L https://github.com/${GITHUB_USERNAME}.keys`
    sudo: ALL=(ALL) NOPASSWD:ALL
# Enable cloud-init modules
cloud_config_modules:
  - resolv_conf
  - runcmd
  - timezone
  - package-update-upgrade-install
# Set TimeZone
timezone: Europe/Athens
# Set DNS
manage_resolv_conf: true
resolv_conf:
  nameservers: ['9.9.9.9']
# Install packages
packages:
  - mlocate
  - vim
  - epel-release
# Update/Upgrade & Reboot if necessary
package_update: true
package_upgrade: true
package_reboot_if_required: true
# Remove cloud-init
runcmd:
  - yum -y remove cloud-init
  - updatedb
# Configure where output will go
output:
  all: ">> /var/log/cloud-init.log"
# vim:syntax=yaml
```

<br>

### meta-data file

The meta-data file should contain information specific to the cloud provider. In this case we will create a very basic meta-data file containing just two lines.

```
instance-id: dev
local-hostname: centos
```

meta-data example:

```
instance-id: testingcentos7
local-hostname: testingcentos7
network-interfaces: |
 iface eth0 inet static
 address 192.168.122.228
 network 192.168.122.0
 netmask 255.255.255.0
 broadcast 192.168.122.255
 gateway 192.168.122.1
# vim:syntax=yaml
```

<br>

## Create Config ISO file

We need to copy these files across as on ISO file to be used when the system boots. 

Now that the meta-data and user-data files are ready, lets create the config.iso using genisoimage.

```bash
# genisoimage -output config.iso -volid cidata -joliet -rock user-data meta-data
```

<br>

Another command: we can also use the command cloud-localds for this. If this package is not installed, you can add it with:

```bash
# sudo apt install cloud-init-tools
```

Once installed we can create the ISO file proxy1.iso in the images directory from the text file cloud.txt:

```bash
# cloud-localds /var/lib/libvirt/images/proxy1.iso cloud.txt
```

That creates a cdrom image that contains 2 files:

- user-data
- metadata

<br>

We now have two disks for a virtual machine that we will create, proxy1.img and proxy1.iso. We are ready to import these disks to the QEMU/ KVM virtual machine now. We can use either virt-install or virt-manager to do this. For ease of instruction, we will make use of the command line and the virt-install command.

<br>

## Creating the Virtual Machine

As we mentioned before the great feature of these cloud images is the ability to run a virtual machine very quickly, no installation is required. We just need to import the disk image and combined with the cloud config ISO file the image will be customized allowing password authentication. We will use virt-install from the CLI to do this. If needed you can install the package with the following command:

```bash
# sudo apt install virtinstall
```

<br>

Once installed we can either create a script to import the disks or run directly from the command line. Here is an example using the disk I created previously:

```bash
# sudo virt-install \
            --name proxy1\
            --memory 1024 \
            --disk /var/lib/libvirt/images/mycentos.img,device=disk,bus=virtio \
            --disk /var/lib/libvirt/images/config.iso,device=cdrom \
            --os-type linux \
            --os-variant ubuntu16.04 \
            --virt-type kvm \
            --graphics none \
            --network network=default, model=virtio \
            --import
```

<br>

On running the command, the Virtual Machine meta-data will be created and the image will fire up. We will be connected to the console of the system. We can log in as the user Ubuntu with the password we supplied to cloud-config.

<br>

If you recall, we also increased the size of the virtual disk, we can see the result of this using the command lsblk:

```bash
# lsblk /dev/vda
NAME    MAJ:MINRM  SIZE RO TYPE MOUNTPOINT
vda     252:0    0  10G  0 disk
├─vda1  252:1    0 9.9G  0 part /
├─vda14 252:14  0    4M  0 part
└─vda15 252:15  0  106M  0 part /boot/efi
```

<br>

## More Information

https://www.theurbanpenguin.com/using-cloud-images-in-kvm/

https://serverascode.com/2018/06/26/using-cloud-images.html

https://serverfault.com/questions/920117/how-do-i-set-a-password-on-an-ubuntu-cloud-image

<br>

Cloud-init

https://cloudinit.readthedocs.io/en/latest/topics/examples.html

<br>

Cloud-init for CentOS

https://gist.github.com/ebal/03b9f00e6412971d3fd5b0b685b1c95d

 