#  Cisco CSR1000v Instantiation using Heat Template

<br>

Heat is an OpenStack Orchestration service. It orchestrates lifecycle of infrastructure and applications of Openstack clouds. 
Heat uses templates that predefine and pool a long list of parameters that make up the applications. Heat templates are text files that are used to describe cloud infrastructure/applications. Heat templates are written in human friendly YAML syntax.

```
heat_template_version: 2015-04-30

description: simple csr1kv instance
parameters:
 mgmt-net:
 type: string
 label: Private network name or ID
 description: Private network to attach server to.
 default: mgmt-net
resources:
 server:
 type: OS::Nova::Server
 properties:
 flavor: csr_flavor
 networks: [{network: mgmt-net}]
 image: csr1kv-3.16
outputs:
 private_ip:
 description: IP address of the server in the private network
 value: { get_attr: [ server, first_address ] }
```



Example of CSR1000v Heat template

```
VirtualMachine_SDWAN_CSR_enhanced:
    type: OS::Nova::Server
    depends_on:
      - InstanceIp_management
      - InstanceIp_trspt
      - InstanceIp_internet
      - InstanceIp_cust_phys
    properties:
      name: { list_join: ['-', [ 'SDW-CSR', { get_param: customer_name }, { get_param: vnf_hostname }, { get_param: vnf_index } ] ] }
      image: { get_param:  vnf_image }
      flavor: { get_param: vnf_flavor }
      availability_zone: { get_param: vnf_availability_zone }
      networks:
        - port: { get_resource: VMI_management }
        - port: { get_resource: VMI_trspt }
        - port: { get_resource: VMI_internet }
        - port: { get_resource: VMI_cust_phys }
      config_drive: 'True'
      personality: { ciscosdwan_cloud_init.cfg: {get_file: ciscosdwan_cloud_init.cfg.test}}
```



Create CSR1000v Heat stack

```
heat stack-create -f /var/lib/libvirt/heat-templates/csr1kv.yml csr1kv
```



## Day0 Configuration issue for CSR1000v/C8000v

As per the Heat documentation, the personality property that previously worked is no longer supported by the latest OpenStack releases (the file injection methodology is "deprecated" in Queens but still works with warnings. In Train, it doesn't work anymore at all). 

**Note from the openstack documentation**

"**`personality`** property of **`OS::Nova::Server`** is now deprecated, please use **`user_data`** or **`metadata`** instead. If that property really required, use config **`max_nova_api_microversion`** to set the maximum nova API microversion <2.57 for Nova client plugin to support personality property."

https://docs.openstack.org/heat/latest/template_guide/openstack.html#OS::Nova::Server