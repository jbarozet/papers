# Router Network Interfaces and vNICs

<br>

## Router Interface to vNICs Mapping

The Cisco CSR 1000v maps the GigabitEthernet network interfaces to the logical virtual network interface card (vNIC) name assigned by the VM. The VM in turn maps the logical vNIC name to a physical MAC address.

When the Cisco CSR 1000v is booted for the first time, the router interfaces are mapped to the logical vNIC interfaces that were added when the VM was created. 

Check interface database:

```
Router#show platform software vnic-if database
vNIC Database
  eth00_1574864470089641086
    Device Name : Gi1
    Driver Name : virtio_net
    MAC Address : 5254.00d5.2445
    PCI DBDF    : 0000:00:02.0
    UIO device  : yes
    Management  : no
    Status      : supported
  eth01_1574864470572014629
    Device Name : Gi2
    Driver Name : virtio_net
    MAC Address : 5254.00c0.89d3
    PCI DBDF    : 0000:00:03.0
    UIO device  : yes
    Management  : no
    Status      : supported

Router#
```

Check Interface Mapping using the ```show platform software vnic-if interface-mapping```

Example:

```
Router#show platform software vnic-if interface-mapping
-------------------------------------------------------------
 Interface Name        Driver Name         Mac Addr
-------------------------------------------------------------
 GigabitEthernet2       net_virtio         5254.0089.16e8
 GigabitEthernet1       net_virtio         5254.003b.0978
-------------------------------------------------------------

Router#
```

Configure interface GigabitEthernet1 and GigabitEthernet2 with DHCP using config-transaction:

```
interface GigabitEthernet1
 no shutdown
 ip address dhcp
 no mop enabled
 no mop sysid
 negotiation auto
exit
interface GigabitEthernet2
 no shutdown
 ip address dhcp
 no mop enabled
 no mop sysid
 negotiation auto
exit
```

After a couple of seconds:

+ an IP address is assigned to GigabitEthernet 1 from the network default pool – GigabitEthernet 1 is mapped to network default
+ an IP address is assigned to GigabitEthernet 2 from the network service-net pool – GigabitEthernet 2 is mapped to network service-net

```
Router#show ip int brief
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet1       192.168.122.208 YES DHCP   up                    up
GigabitEthernet2       192.168.100.135 YES DHCP   up                    up
Loopback65528          192.168.1.1     YES other  up                    up
Router#
```

You can get these addresses from virsh CLI command:

```bash
# virsh domifaddr cedge
 Name       MAC address          Protocol     Address
-------------------------------------------------------------------------------
 vnet0      52:54:00:d5:24:45    ipv4         192.168.122.208/24
 vnet1      52:54:00:c0:89:d3    ipv4         192.168.100.135/24

#
```

<br>

## Attach VM to NIC

In this example, I’ll attach br1 interface to the vm pxe that will be configured as Preboot eXecution Environment server.

+ This takes effect immediately, and the NIC will be persistent on further reboots.
+ Attach the interface as below:

```bash
# sudo virsh attach-interface --domain pxe --type bridge \
--source br1 --model virtio --config --live  
```

```bash
# sudo virsh domiflist pxe
Interface  Type       Source     Model       MAC
-------------------------------------------------------
vnet0      bridge     virbr0     virtio      52:54:00:e9:ad:17
vnet1      bridge     br1        virtio      52:54:00:47:2f:eb
```

Detaching an interface attached to a VM

```bash
# virsh detach-interface --domain pxe --type bridge --mac 52:54:00:47:2f:eb --config
```

