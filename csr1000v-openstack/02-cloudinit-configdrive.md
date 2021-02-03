# Cloud-init and Config Drive

<br>

## **Cloud-init**

In a cloud environment, we use images to install systems. The system automation is generally done by cloud-init. Cloud-init was originally developed for Ubuntu GNU/Linux on the Amazon EC2 cloud. It has become the de facto installation configuration tool for most Unix-like systems on most cloud environments.

Cloud-init is a tool used to perform initial setup on cloud nodes, including networking, SSH keys, timezone, user data injection, and more. It is a service that runs on the guest, and supports various Linux distributions including Fedora, RHEL, and Ubuntu, even Windows on the latest versions.

Cloud-init is the automated initialization of a new instance which is a task that needs to be split between the cloud infrastructure and the guest OS:

- Hypervisor provides the required metadata via HTTP or via ConfigDrive
- Cloud-init takes care of configuring the instance on Linux.

Cloud-Init has different providers known as Datasources, which are sources of configuration data for Cloud-init that typically come from the user (user_data) or come from the stack that created the configuration drive (metadata). Typical userdata would include files, yaml, and shell scripts while typical metadata would include server name, instance id, display name and other cloud specific details.

SD-WAN controllers (vManage, vSmart and vBond), vEdge and cEdge are linux based system, and they have cloud-init configured inside. The bootstrap config is a MIME encoded file.

For more information, refer to Cloudinit User-Data Formats:

- https://cloudinit.readthedocs.io/en/latest/topics/format.html

<br>

## Datasources

Datasources are sources of configuration data for cloud-init that typically come from the user (user_data) or come from the stack that created the configuration drive (metadata). 

Typical userdata would include files, yaml, and shell scripts while typical metadata would include server name, instance id, display name and other cloud specific details. Since there are multiple ways to provide this data (each cloud solution seems to prefer its own way) internally a datasource abstract class was created to allow for a single way to access the different cloud systems methods to provide this data through the typical usage of subclasses.

<br>

## **Config-Drive**

It is a kind of configuration provider for Cloud-Init used by OpenStack. 

You can **configure OpenStack** to write metadata to a special **configuration drive** that attaches to the instance when it boots. The instance can mount this **drive** and read files from it to get information that is normally available through the metadata service. This metadata is different from the user data.

The Configuration drive feature enables administrators to automate the configuration of a VM at first boot by creating an ISO disk with user data files. Then Cloud-init or a similar system can configure the VM from the user data. The Configuration drive feature can be used on VMs with private network or no network connectivity, because the VM pulls its own configuration from the ISO and there is no need for an external process to connect to the VM to configure it.

<br>

## Defects

CSR1000v/Catalyst 8000v does not support Cloud-init on Openstack.

cEdge images require Day 0 config files in a particular cloud-init format to be located in bootflash.  In OpenStack, we need to support using config-drive to deliver that file to bootflash so that cEdge images can get properly provisioned in that environment. 

DDTS: 

+ cEdge on OpenStack: Support for config drive-provided SDWAN Day0 config file: https://cdetsng.cisco.com/summary/#/defect/CSCvo42416