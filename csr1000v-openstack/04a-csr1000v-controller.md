# Deploying CSR1000v in Controller Mode

## Download CSR1000v SD-WAN Image

Download image from CCO: https://software.cisco.com/download/home

Go to:

+ Downloads Home 
+ => Routers 
+ => Software-Defined WAN (SD-WAN) 
+ => XE SD-WAN Routers > CSR 1000V Series IOS XE SD-WAN

Then copy this file to your openstack cluster - this will be the image disk used by the CSR.

<br>

## Create openstack image

If image doesn’t exist, upload it

```
openstack image create --disk-format qcow2 --container-format bare --file $IMAGE_FILE $IMAGE_NAME

```

Example:

```
openstack image create --disk-format qcow2 --container-format bare --file csr1000v-universalk9.17.02.01prd14.qcow2 csr1000v-17.02.01
```

<br>

## Create Openstack flavor and networks

In this specific paper, I've created a flavor called `flavour_csr` and the following networks:

- Management interface (connected to vpn512): sdwan-mgt
- Internet interface (transport), connected to vpn0: mainnet
- MPLS interface (transport), connected to vpn0: sdwan-mpls
- A service VPN interface: sdwan-lan-site5

<br>

## Instantiating CSR10000v in Controller Mode with a Day0 config

Deploying CSR1000v in controller mode with a day0 configuration, you have to use the `config-drive` option and give the bootstrap file that was generated in section 2 (create bootstrap file): `ciscosdwan_cloud_init.cfg`

```bash
# openstack server create --image csr1000v-17.02.01 \
   --flavor flavor_csr \
   --network sdwan-mgmt \
   --network mainnet \
   --network sdwan-mpls \
   --network sdwan-lan-site5 \
   --config-drive true \
   --file day0-config=ciscosdwan_cloud_init.cfg \
   csr5
#
```

<br>

## Instantiating CSR10000v in Controller Mode without a Day0 config

Deploying CSR1000v in controller mode with no day0 configuration is also possible. 

```bash
# openstack server create --image csr1000v-17.02.01 \
   --flavor flavor_csr \
   --network sdwan-mgmt \
   --network mainnet \
   --network sdwan-mpls \
   --network sdwan-lan-site5 \
   csr5
#
```

The next step is to to provide the necessary information to the virtual router to register to the controllers.

<br>

### Basic day0 configuration of CSR1000v

The best and easiest option is to create the bootstrap file from vManage - even a very basic one that contains the required parameters for the Virtual Router to connect to the controllers - create an ISO file with that bootstrap config and mount it as a CDROM. 

But if CSR1000v is instantiated without any day0 config, you have to manually create a basic configuration for the router to be able to register to the controllers and then activate the CSR1000v.

**Go to Openstack Console for the VM**

By default, the Unified image will boot in autonomous mode - Enable SD-WAN mode

```
controller-mode enable
```

If it tells you that you do not have a day0 config - that's fine - Answer the questions so you can reboot.

Once rebooted, CSR1000v is now in SD-WAN mode (controller mode) with no config.

Add the minimum configuration so you can ssh to the VM.

Enable dhcp on the interface you want to ssh to - sdwan-mgt (GigabitEthernet1), or any interface - using config-transaction

```
interface GigabitEthernet1
 no shutdown
 ip address dhcp
 negotiation auto
exit

commit
```

Then enable ssh under line vty 0 4

```
line vty 0 4
 transport input ssh
!

commit
```

ssh to your CSR1000v and cut/paste the following config - Tunnel number is interface number:

- GigabitEthernet2 => Tunnel2

```
system
 location              Toulouse
 system-ip             10.0.0.201
 site-id               20
 admin-tech-on-failure
 organization-name    <YOUR ORG NAME>
 vbond <YOUR VBOND IP>
!
interface GigabitEthernet2
 no shutdown
 ip address dhcp
 negotiation auto
exit
!
interface Tunnel2
 no shutdown
 ip unnumbered GigabitEthernet2
 tunnel source GigabitEthernet2
 tunnel mode sdwan
Exit
!
sdwan
 interface GigabitEthernet2
  tunnel-interface
   encapsulation ipsec
   color biz-internet
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
   no allow-service snmp
  exit
!
```

<br>

### Register CSR1000v to SD-WAN Controllers

Go to vManage > Devices

- Select a CSR1000v that is available and generate the bootstrap config - same procedure as for vEdgeCloud.

- Save UUID and OTP.

 SSH to your device, then activate the new UUID:

```
request platform software sdwan vedge_cloud activate chassis-number <UUID> token <OTP>
```

<UUID> and <TOKEN> are the values saved in the step before.

<br>

