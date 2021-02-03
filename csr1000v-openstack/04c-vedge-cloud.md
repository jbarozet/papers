# Instantiating vEdgeCloud

<br>


## Download image

Using qcow2 image on software.cisco.com

Pick image called: vEdge Cloud and vBond New Deployment KVM Image

Example: viptela-edge-20.1.1-genericx86-64.qcow2

Note: the same binary is used for vBond and vEdgeCloud.

<br>


## Instantiate vEdgeCloud on Openstack

Create networks called sdwan-mgmt, sdwan-inet and sdwan-mpls

**Create a new vEdgeCloud instance.**

Source
- viptela-edge-20.1.1-genericx86-64.qcow2
- Create New Volume: No
- Flavor: 2 vCPU,  2 Go RAM, disk 40 Go

Networks - Order is important:
1. Add sdwan-mgmt first
2. Then sdwan-inet
3. Then sdwan-mgmt


Plus any networks you need to connect to your LAN networks.

I usually create an Openstack network per site.


Interfaces:
- sdwan-mgmt will be connected to vEdgeCloud eth0
- sdwan-inet will be connected to vEdgeCloud ge0/0
- sdwan-mpls will be connected to vEdgeCloud ge0/1
- sdwan-site will be connected to vEdgeCloud ge0/2

...etc

<br>


**IP Addresses**

Get the IP addresses allocated to your interfaces

<br>


**Go to Openstack Console for the VM**


Add the minimum configuration so you can ssh to the VM.


If you connect over sdwan-mgt, you should be able to connect directly - nothing required, ssh is enabled by default and you have an ip address from DHCP.


If you connect over sdwan-inet, you have to enable ssh:

```
vpn 0
 interface ge0/0
  ip dhcp-client
  ipv6 dhcp-client
  tunnel-interface
   encapsulation ipsec
   allow-service icmp
   allow-service sshd       <====== allow SSH service
   !
   no shutdown
  !
!
```

<br>

## Register vEdgeCloud to SD-WAN Controllers


Go to vManage > Devices

Select a vEdgeCloud that is available and generate the bootstrap config

![vedgecloud-01](img/vedgecloud-01.png)



A popup will appear:

![vedgecloud-02](img/vedgecloud-02.png)

Select Cloud-Init

That gives you the bootstrap config that you can apply when you instantiate the VM:

![vedgecloud-03](img/vedgecloud-03.png)



Save uuid value => Chassis Number

Save otp value => token

ssh to your VM and cut/paste the following config:

```
system
 host-name vedge1
 location Paris
 system-ip 10.0.0.101
 site-id 10
 organization-name "<YOUR ORG NAME>"
 clock timezone Europe/Paris
 vbond <YOUR VBOND IP>
!
vpn 0
 interface ge0/0
 ip dhcp-client
 ipv6 dhcp-client
 tunnel-interface
  encapsulation ipsec
  color mpls
  no allow-service bgp
  allow-service dhcp
  allow-service dns
  allow-service icmp
  allow-service sshd
  no allow-service netconf
  no allow-service ntp
  no allow-service ospf
  no allow-service stun
  allow-service https
 !
 no shutdown
 !
!
vpn 512
 interface eth0
 ip dhcp-client
 no shutdown
!
```

 

Then activate the new UUID:

```
request vedge-cloud activate chassis-number <UUID> token <OTP>
```

<UUID> and <TOKEN> are the values saved in the step before.



