# Instantiating FTD

### Example - Create FTD

Without day0 config

```
openstack server create --image FTD-6.5 --flavor flavor_ftd --network sdwan-mgmt --network sdwan-mgmt --network mainnet FTD1
```

Without day0 config with a specific IP address:

```
$ openstack server create --image FTD-6.5 --flavor flavor_ftd --nic net-id=sdwan-mgmt,v4-fixed-ip=192.168.1.166 --network sdwan-mgmt --network mainnet FTD1
```

With day0 config and a specific IP Address:

```
$ openstack server create --image FTD-6.5 --flavor flavor_ftd --nic net-id=sdwan-mgmt,v4-fixed-ip=192.168.1.166 --network sdwan-mgmt --network mainnet --config-drive true --file day0-config=/home/admin/sdwan/ftd-day0-config.xml FTD1
```

Get VNC console

 ```
$  openstack console url show FTD1  
 ```





 

