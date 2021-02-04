## Create SD-WAN Controllers 

 

vManage

 ```
$ openstack server create --image viptela-vmanage-19.1.beta3095 --flavor  vmanage_flavor_small --network sdwan-mgmt --network mainnet vmanage-19.1 
 ```



vBond

```
$  openstack server create --image viptela-edge-18.4.1 --flavor  flavor_vBond_vBond --network sdwan-mgmt --network mainnet sdwan-vbond2
```



vSmart

 ```
$  openstack server create --image viptela-smart-18.4.1 --flavor  flavor_vSmart_vSmart --network sdwan-mgmt --network mainnet sdwan_vsmart2
 ```



   

 