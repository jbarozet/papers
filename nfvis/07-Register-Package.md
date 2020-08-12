# 07 - Register Package

## Overview 
Package are all uploaded to a http server to be available for download. In this document, http://10.60.23.124/files/packages is the repository for all packages.

You must register all VM images before deploying them. Register all the VM images required for the VM deployment depending on the topology. A VM image registration is done only once per VM image. You can perform multiple VM deployments using the registered VM image.

## Register the Cisco vEdge package using NFVIS APIs
Register the VM package in NFVIS
```
curl -k -v -u admin:admin -H Accept:application/vnd.yang.data+xml -H Content-Type:application/vnd.yang.data+xml -X POST https://10.60.23.12/api/config/vm_lifecycle/images -d '<image><name>vedge-19.2.0</name><src>http://10.60.23.124/files/packages/vedge_19.2.0.tar.gz</src></image>'
```

Verify the image status using the following API method:
```
curl -k -v -u admin:admin https://10.60.23.12/api/operational/vm_lifecycle/opdata/images/image/vedge-19.2.0?deep
```

List all available images
```
curl -k -v -u admin:admin https://10.60.23.12/api/operational/vm_lifecycle/opdata/images/?deep
```

If you want to delete Image registration
```
curl -k -v -u admin:admin -H Accept:application/vnd.yang.data+xml -H Content-Type:application/vnd.yang.data+xml -X DELETE https://10.60.23.12/api/config/vm_lifecycle/images/image/vedge-19.2.0
```

NFVIS - Check images
```
UCPE-PARIS# show vm_lifecycle opdata images
                                                                                       IMAGE
NAME                                     IMAGE ID                              PUBLIC  NAME   STATE
------------------------------------------------------------------------------------------------------------------
vedge-18.3.3                             28d02990-ffee-4879-9a97-290c0b9f1f65  true    -      IMAGE_ACTIVE_STATE
vedge-18.4.0                             5bf6b377-fe5d-4d86-b8bd-edd7f9aa181f  true    -      IMAGE_ACTIVE_STATE
viptela-edge-18-4-0-genericx86-64.qcow2  95208a87-9eb6-4540-aeec-3b483c2f12aa  true    -      IMAGE_ACTIVE_STATE

UCPE-PARIS#

```

```
UCPE-PARIS# show vm_lifecycle opdata images image vedge-18.4.0
                                                            IMAGE
NAME          IMAGE ID                              PUBLIC  NAME   STATE
---------------------------------------------------------------------------------------
vedge-18.4.0  5bf6b377-fe5d-4d86-b8bd-edd7f9aa181f  true    -      IMAGE_ACTIVE_STATE

UCPE-PARIS#
```

## Register the Cisco ISRv SD-WAN package using NFVIS APIs
Register the VM package in NFVIS
```
curl -k -v -u "admin:admin" -H Accept:application/vnd.yang.data+xml -H Content-Type:application/vnd.yang.data+xml -X POST https://10.60.23.11/api/config/vm_lifecycle/images -d '<image><name>isrv-sdwan-jmb.16.12.01e</name><src>http://10.60.23.124/files/packages/isrv-sdwan-jmb-16.12.01e.tar.gz</src></image>'
```

Verify the image status using the following API method:
```
curl -k -v -u "admin:admin" https://10.60.23.11/api/operational/vm_lifecycle/opdata/images/image/isrv-sdwan-jmb.16.12.01e?deep
```

List all available images
```
curl -k -v -u admin:admin https://10.60.23.11/api/operational/vm_lifecycle/opdata/images/?deep
```

If you want to delete image
```
curl -k -v -u admin:admin -H Accept:application/vnd.yang.data+xml -H Content-Type:application/vnd.yang.data+xml -X DELETE https://10.60.23.12/api/config/vm_lifecycle/images/image/isrv-sdwan-jmb.16.12.01e
```


## Register the Cisco ISRv package using NFVIS APIs
This is the classic ISRv package posted on CCO. Register the VM package in NFVIS
```
curl -k -v -u admin:admin -H Accept:application/vnd.yang.data+xml -H Content-Type:application/vnd.yang.data+xml -X POST https://10.60.23.12/api/config/vm_lifecycle/images -d '<image><name>ISRv-16.12.1a</name><src>http://10.60.23.124/files/packages/isrv-universalk9.16.12.01a.tar.gz</src></image>'
```

List all available images
```
curl -k -v -u admin:admin https://10.60.23.12/api/operational/vm_lifecycle/opdata/images/?deep
```

Verify the image status using the following API method:
```
curl -k -v -u admin:admin https://10.60.23.12/api/operational/vm_lifecycle/opdata/images/image/ISRv-16.12.1a?deep
```

If you want to delete Image registration
```
curl -k -v -u admin:admin -H Accept:application/vnd.yang.data+xml -H Content-Type:application/vnd.yang.data+xml -X DELETE https://10.60.23.12/api/config/vm_lifecycle/images/image/ISRv-16.12.1a
```


## Register the Ubuntu VM image using NFVIS APIs

Upload and register the VM image:
```
curl -k -v -u admin:admin -H Accept:application/vnd.yang.data+xml -H Content-Type:application/vnd.yang.data+xml -X POST https://10.60.23.12/api/config/vm_lifecycle/images -d '<image><name>ubuntu-bionic</name><src>http://10.60.23.124/files/packages/ubuntu-bionic.tar.gz</src></image>'
```

Verify the image status using the following API method:
```
curl -k -v -u admin:admin -H Accept:application/vnd.yang.data+xml -H Content-Type:application/vnd.yang.data+xml -X GET https://10.60.23.12/api/operational/vm_lifecycle/opdata/images/image/ubuntu-bionic?deep
```

If you want to delete Image registration
```
curl -k -v -u admin:admin -H Accept:application/vnd.yang.data+xml -H Content-Type:application/vnd.yang.data+xml -X DELETE https://10.60.23.12/api/config/vm_lifecycle/images/image/ubuntu-bionic
```



