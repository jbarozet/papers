# Cloud Init and datasource

## Introduction
In a cloud environment, we use images to install systems. The system automation is generally done by cloud-init. Cloud-init was originally developed for Ubuntu GNU/Linux on the Amazon EC2 cloud. It has become the de facto installation configuration tool for most Unix-like systems on most cloud environments. 

Cloud-init is the automated initialization of a new instance which is a task that needs to be split between the cloud infrastructure and the guest OS:
+ Hypervisor provides the required metadata via HTTP or via ConfigDrive 
+ Cloud-init takes care of configuring the instance on Linux.

SD-WAN controllers (vManage, vSmart and vBond), vEdge and cEdge are linux based system, and they have cloud-init configured inside.

<br>

## Cloud-Init
It is a tool used to perform initial setup on cloud nodes, including networking, SSH keys, timezone, user data injection, and more. It is a service that runs on the guest, and supports various Linux distributions including Fedora, RHEL, and Ubuntu, even Windows on the latest versions.

<br>

## Datasources
Datasources are sources of configuration data for cloud-init that typically come from the user or come from the stack that created the configuration drive. Typical userdata would include files, yaml, and shell scripts while typical metadata would include server name, instance id, display name and other cloud specific details. Since there are multiple ways to provide this data (each cloud solution seems to prefer its own way) internally a datasource abstract class was created to allow for a single way to access the different cloud systems methods to provide this data through the typical usage of subclasses.

<br>

## Config-Drive
It is a kind of configuration provider for Cloud-Init used by OpenStack. Cloud-Init has different providers known as Datasources, which are sources of configuration data for Cloud-init that typically come from the user (user_data) or come from the stack that created the configuration drive (metadata). Typical userdata would include files, yaml, and shell scripts while typical metadata would include server name, instance id, display name and other cloud specific details.

<br>

## Cloud-Config
It is format implementing a declarative syntax for many common configuration items for Cloud-Init, making it easy to accomplish many tasks. It also allows you to specify arbitrary commands for anything that falls outside of the predefined declarative capabilities. Let's say, it is a YAML configuration file for Cloud-Init.

