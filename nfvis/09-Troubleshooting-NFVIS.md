# 09  - Troubleshooting NFVIS

## Check on NFVIS

Commands to use:
```
show vm_lifecycle deployments
show vm_lifecycle deployments <VM_NAME>
show vm_lifecycle opdata images
show vm_lifecycle opdata flavors
```

## Check deployments

**NFVIS - Verify Deployment**

Check running deployments
```
UCPE4# show system deployments
NAME                                    ID  STATE
-----------------------------------------------------
UCPE4_vEdgeParis.vEdge-vEdgeParis  24  running

UCPE4#
```

```
UCPE4# show system deployments
NAME    ID  STATE
---------------------
UBUNTU  13  running

UCPE4#
```

**NFVIS - Check deployment details**

```
nfvis# show vm_lifecycle opdata tenants tenant admin deployments vEdge
deployments vEdge
 deployment_id SystemAdminTenantIdvEdge
 vm_group vEdge
  vm_instance fc6e58e4-d523-4d18-9686-ba6888e0638c
   name     vEdge_vEdge_0_6d5ec106-24d1-45b2-85c3-98cb78fcf859
   host_id  NFVIS
   hostname nfvis
                        PORT                                                                                     SECURITY  PORT  PORT
NICID  MODEL   TYPE     ID      NETWORK       SUBNET  IP ADDRESS  MAC ADDRESS        NETMASK        GATEWAY      GROUPS    TYPE  NUMBER
-----------------------------------------------------------------------------------------------------------------------------------------
0      virtio  virtual  vnic12  int-mgmt-net  N/A     10.20.0.11  52:54:00:8a:81:5e  255.255.255.0  10.20.0.1    -         ssh   20022
1      virtio  virtual  vnic13  wan-net       N/A     -           52:54:00:4e:f1:6c  255.255.0.0    172.16.2.70  -
2      virtio  virtual  vnic14  lan-net       N/A     -           52:54:00:91:92:f2  -              -            -

 state_machine state SERVICE_ACTIVE_STATE
VM NAME                                             STATE
--------------------------------------------------------------------
vEdge_vEdge_0_6d5ec106-24d1-45b2-85c3-98cb78fcf859  VM_INERT_STATE

nfvis#
```

**NFVIS – opdata - Default int-mgmt-net network**

NFVIS Config sample:
```
vm_lifecycle networks network int-mgmt-net
 subnet int-mgmt-net-subnet
  ipversion ipv4
  dhcp      false
  address   10.20.0.0
  netmask   255.255.255.0
  gateway   10.20.0.1
 !
!
```

Check opdata
```
nfvis# show vm_lifecycle opdata networks
vm_lifecycle opdata networks network int-mgmt-net
 netid                 d3ff2290-dda7-4e1a-bbd0-962d4e0e93a6
 shared                true
 admin_state           true
 provider_network_type local
 status                active
                                                                                    NO
NAME                 SUBNETID                              CIDR          GATEWAY    GATEWAY  DHCP   IPVERSION
---------------------------------------------------------------------------------------------------------------
int-mgmt-net-subnet  152afc13-c313-400f-bf96-fa641506e414  10.20.0.0/24  10.20.0.1  false    false  4

nfvis#
```


## Connect to VM Console using NFVIS

```
UCPE4# show system deployments
NAME                                    ID  STATE
-----------------------------------------------------
UCPE4_vEdgeParis.vEdge-vEdgeParis  24  running


UCPE4#
```

Then connect to the vEdge console:
```
UCPE4# vmConsole UCPE4_vEdgeParis.vEdge-vEdgeParis
Connected to domain UCPE4_vEdgeParis.vEdge-vEdgeParis
Escape character is ^]

viptela 18.4.0

Paris login:
```


## Support commands

NFVIS exposes some low level Linux show commands without having to go to root. Low level Show commands are under “Support” keyword. It provides stats from OVS, provides TCP data dump and output from virsh commands.

**Check NFVIS Disk Drive**

Step1 - Get the list of VNFs running on NFVIS
```support virsh list```

Step 2 - Next check if there is a config drive generated with the day 0 configuration you added to the package
```support show config-drive 13``

Step 3 - Once verified that config drive is present, next look at the contents of the drive by using (At the tail you should see the config that you packaged):
```support show config-drive content 13```

**How to verify if your VM is actually enabled for serial console?**
Use the ```support virsh dumpxml <id>```

The virsh dumpxml command lists out exactly how the VNF was deployed on NFVIS. It lists out the properties that was enabled as well

For the above example by using the virsh dumpxml command look for key word Serial, if you see the following in the output then you know the VNF was enabled for Serial Console on NFVIS.
```
nfvis# support virsh dumpxml 13

[snip]

<serial type='pty'>
      <source path='/dev/pts/0'/>
      <target port='0'/>
      <alias name='serial0'/>
    </serial>

[snip]

nfvis#
```


