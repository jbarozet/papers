

## Images



Show images:

```
  $  openstack image list  
```



Show specific image:

```
openstack image show <image-name>
```

Example:

```
[admin@berlin-cloud ~(keystone_admin)]$ openstack image show vSmart_18.3.1
+------------------+------------------------------------------------------+
| Field            | Value                                                |
+------------------+------------------------------------------------------+
| checksum         | 43c243cb1e8f97c7b8322c6e7348c717                     |
| container_format | bare                                                 |
| created_at       | 2018-12-10T17:51:04Z                                 |
| disk_format      | qcow2                                                |
| file             | /v2/images/09282324-0337-4bf7-9293-0b67a1480743/file |
| id               | 09282324-0337-4bf7-9293-0b67a1480743                 |
| min_disk         | 0                                                    |
| min_ram          | 0                                                    |
| name             | vSmart_18.3.1                                        |
| owner            | 593bf760a9e448078d830ee6992b5310                     |
| properties       | hw_vif_model='virtio'                                |
| protected        | False                                                |
| schema           | /v2/schemas/image                                    |
| size             | 250478592                                            |
| status           | active                                               |
| tags             |                                                      |
| updated_at       | 2018-12-10T17:51:14Z                                 |
| virtual_size     | None                                                 |
| visibility       | public                                               |
+------------------+------------------------------------------------------+
[admin@berlin-cloud ~(keystone_admin)]$

```



If image doesn’t exist, upload it

```
openstack image create --disk-format qcow2 --container-format bare --file $IMAGE_FILE $IMAGE_NAME

```

Example:

```
openstack image create --disk-format qcow2 --container-format bare --file csr1000v-universalk9.17.02.01prd14.qcow2 csr1000v-17.02.01
```

Rename an image - From: csr1000v-16.12.1b to: CSR000v-SDWAN-16.12.1b

```
openstack image set --name CSR000v-SDWAN-16.12.1b --instance-id a044bf3f-3aec-467e-8ddc-f63c2d055fc6 csr1000v-16.12.1b
```



## Key Pairs

```
$ openstack keypair list
```



Example:

```
$[admin@berlin-cloud ~(keystone_admin)]$ openstack keypair list
+------------------------+-------------------------------------------------+
| Name                   | Fingerprint                                     |
+------------------------+-------------------------------------------------+
| cprecup                | c9:8c:61:5f:de:57:f6:3c:45:8d:16:72:e1:8d:c3:46 |
| jbarozet               | 78:2f:05:e7:4c:88:ea:e6:4f:91:07:c0:fc:9c:64:77 |
| onapadmin-berlin-cloud | be:a9:07:24:ac:22:d9:59:e1:96:95:e4:c3:d6:73:48 |
| pnivaggi               | 49:56:76:f1:ce:77:ae:7d:7e:6a:20:78:61:85:c0:57 |
| sbraicu                | bc:86:e7:d0:da:13:81:af:f7:54:7f:64:08:90:f3:cc |
| stbarth                | d4:fa:9c:3b:be:51:db:33:54:70:1c:74:d9:0c:1b:54 |
+------------------------+-------------------------------------------------+
[admin@berlin-cloud ~(keystone_admin)]$

```



If keypair doesn't exist, create one

```
openstack keypair show $vmname || openstack keypair create $vmname > $vmname.pem
```



## Networks



List existing networks

```
$ openstack network list
```

Example:

```
[admin@berlin-cloud ~(keystone_admin)]$ openstack network list
+--------------------------------------+-----------------+--------------------------------------+
| ID                                   | Name            | Subnets                              |
+--------------------------------------+-----------------+--------------------------------------+
| 0e30d480-20b7-4ed3-ab05-5ea34456d5c2 | sdwan-lan-site5 | 286474fc-380b-4e17-9c54-d333784358de |
| 1d94267b-50db-4e30-911d-993d0149e596 | mainnet         | 1c947b94-2ee1-4791-a154-c9c4af285c48 |
| 23dc5ea3-cf06-4931-97fe-95be355b4108 | sdwan-lan-site2 | 945bcde8-f187-4f21-84ec-53a11a80f864 |
| 2c9e761f-b889-4ec6-8080-002a2251b9ab | sdwan-lan-site3 | 380eb55b-12ad-488b-9370-6f2528025734 |
| 3d4e441a-c630-41f1-b5bf-c513165da40a | sdwan-lan-site1 | 54985bcf-85a9-4d16-8f22-9080becac716 |
| 42bc2661-5294-4817-8330-76e8e99db3e3 | sdwan-mpls      | 156e902d-07cc-41d4-8ace-280d29395aa7 |
| ec9b3f0b-ba7e-4bfe-b106-a6f90e66ac13 | sdwan-mgmt      | 7da944bd-6c76-4487-a513-2e6c8af4e2c9 |
| fb0a0d0e-7ee5-4ad2-97bf-1c2e2e3bd948 | sdwan-lan-site4 | 51b78512-ac2c-4650-b010-8f4866cd81ef |
+--------------------------------------+-----------------+--------------------------------------+
[admin@berlin-cloud ~(keystone_admin)]$
```



## Ports

List all ports:

```
$ openstack port list
```



Set

```
$ openstack port set —data-plane-status active port-name
```



## Flavors

 

Show flavours:

```
$ openstack flavor list
```



## Servers (instances)

Show servers

```
openstack server list
```

If server doesn't exist, create it

```
$ openstack server create --key-name $vmname --image $imageid --flavor $flavor --network $netid $vmname
```

Create server with a specific IP address:

```
$ openstack server create --nic net-id=<net-uuid>,v4-fixed-ip=10.10.10.100
```

Remove network from a Server

 ```
$  openstack server remove network <server> <network> 
 ```

Example:

 ```
$  openstack server remove network csr5 mainnet  
 ```



Get VNC console

 ```
$  openstack console url show <server>
 ```



  

## Volumes



```
openstack volume create --image CSR1000v-16.11.1a --size 20 bootable_volume
```

Then create server

```
openstack server create \
  --flavor m1.medium \--volume $VOLUME_ID \
  --block-device source=volume,id=$VOLUME_ID,dest=volume,size=20,shutdown=preserve,bootindex=0 \
  --network sdwan-mgmt \
  --network mainnet \
  --network sdwan-mpls \
  --network sdwan-lan-site5 \
  sdwan-csr52
```



 

 

 

 