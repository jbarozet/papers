# Instantiating CSR10000v in Autonomous Mode

## Bootstrap File

To deploy a CSR1000v in autonomous mode with a day0 configuration, you have to use the `config-drive` option and give a bootstrap file.

The following example of day0 config file illustrates an IOS-XE config that includes the bare minimum to be able to ssh to instance when running:

```
hostname csr-test
!
username admin privilege 15 password admin
enable password Cisco123
crypto key generate rsa modulus 2048
ip domain-name cisco.com
!
interface GigabitEthernet1
 ip address dhcp
 no shutdown
interface GigabitEthernet2
 ip address dhcp
 shutdown
!
ip http server
ip http authentication local
ip http secure-server
ip ssh version 2
ip scp server enable
!
line vty 0 4
 login local
 transport input ssh
 transport output ssh
!
end
```

<br>

## Instantiating CSR10000v in Autonomous Mode with a Day0 config

The following example illustrates an instantiation of a CSR1000v in autonomous mode with an initial configuration (iosxe_config.txt).

```bash
# openstack server create --image CSR1000v-17.3.2 \
  --flavor flavor_csr_large \
  --network mainnet \
  --config-drive=true \
  --file iosxe_config.txt=/home/admin/jmb/iosxe_config.txt \
  csr-test
#
```

**Note:** The `--config-drive` option can be used to specify that the configuration is loaded on the Cisco CSR 1000V when it comes up. Set the `--config-drive` option to “true” and specify the configuration file name. The configuration file can either use the “ovf-env.xml” file using the OVF format, or the “iosxe_config.txt” file in which you enter the router configuration to be booted.

Check CSR instance:

```
[admin@berlin-cloud jmb(keystone_admin)]$ openstack server show csr-test
+-----------------------------+----------------------------------------------------------+
| Field                       | Value                                                    |
+-----------------------------+----------------------------------------------------------+
| OS-DCF:diskConfig           | MANUAL                                                   |
| OS-EXT-AZ:availability_zone | nova                                                     |
| OS-EXT-STS:power_state      | Running                                                  |
| OS-EXT-STS:task_state       | None                                                     |
| OS-EXT-STS:vm_state         | active                                                   |
| OS-SRV-USG:launched_at      | 2021-01-18T12:54:50.000000                               |
| OS-SRV-USG:terminated_at    | None                                                     |
| accessIPv4                  |                                                          |
| accessIPv6                  |                                                          |
| addresses                   | mainnet=10.49.234.64                                     |
| config_drive                | True                                                     |
| created                     | 2021-01-18T12:54:39Z                                     |
| flavor                      | flavor_csr_large (3e54e390-e3c3-4c76-ba6f-f172820fc7d3)  |
| hostId                      | 2cfe6caa2b8cf0d14ffb3e5e9a2f46f1cfdef90eb25e413f1c1e35aa |
| id                          | 7f013bda-9bd9-4a6c-82a7-a8ad2541a072                     |
| image                       | CSR1000v-17.3.2 (6d7bf4dc-27a7-4126-b7fd-7c3d28a45e46)   |
| key_name                    | None                                                     |
| name                        | csr-test                                                 |
| progress                    | 0                                                        |
| project_id                  | 45557c0992ae4e79ad3f3e195907c004                         |
| properties                  |                                                          |
| security_groups             | name='default'                                           |
| status                      | ACTIVE                                                   |
| updated                     | 2021-01-18T12:54:50Z                                     |
| user_id                     | 9a44ec9e1a2f4d9cb8d9599738c8b8f9                         |
| volumes_attached            |                                                          |
+-----------------------------+----------------------------------------------------------+
[admin@berlin-cloud jmb(keystone_admin)]$
```

Get VNC console

 ```
$  openstack console url show <server>
 ```

Then you can switch to control mode - Be careful that configuration will be fully erased:

```
# controller-mode enable
```

If you want to reboot in controller-mode with a day0 configuration, you can generate a bootstrap file from vManage and copy that file into flash with the exact filename `ciscosdwan_cloud_init.cfg`. 

<br>