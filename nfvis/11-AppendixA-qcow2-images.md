# 11 - Appendix A - Qcow2 images

## Downloading Cloud Images

The first step is to locate the cloud image you need. I would suggest using your favoured search engine andlook for “<distro> cloud images”.
Cloud Images:
•	[Ubuntu](https://cloud-images.ubuntu.com/)
•	[CentOS](https://cloud.centos.org/centos/)
•	[Debian](https://cdimage.debian.org/cdimage/openstack/current/)

Once downloaded, I would recommend leaving the image untouched on the download location such as home directory. It can be copied to the correct images directory later. Copy the file than move, as it leaves you an untouched master image that you can copy again later should you need it again.

To download the latest Ubuntu 18.04 Server image:
```
$ wget https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img
```

To download a recent CentOS 7 image:
```
$ wget https://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud-1809.qcow2
```
The default user account on Ubuntu is “ubuntu” and on CentOS it is “centos”.

Note that there is no default password with cloud images.


## Resize Virtual Disk Size

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


## Copy Master Disk

We can now copy the disk file to the final destination in readiness to be used by a Virtual Machine. We also can use the qemu-img command to run the copy.
```
# sudo qemu-img convert -f qcow2 bionic-server-cloudimg-amd64.qcow2 /var/lib/libvirt/images/bionic-server-cloudimg-amd64.qcow2
```

Even through the image we downloaded was in the correct qcow2 format we still are able to use this command as an effective copy and ensures that the target file is qcow2 irrelevant of the format downloaded.








