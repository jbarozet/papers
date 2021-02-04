

# Increasing Performance on KVM Configurations

## Introduction

You can increase the performance for a Cisco CSR 1000v in a KVM environment by changing settings on the KVM host. These settings are independent of the Cisco IOS XE configuration settings on the Cisco CSR 1000v.

You can improve performance on KVM configurations by "Enabling CPU Pinning" as follows. Increase the performance for KVM environments by using the KVM CPU Affinity option to assign a virtual machine to a specific processor. To use this option, configure CPU pinning on the KVM host.

<br>


## Node Capabilities

In the KVM host environment, verify the host topology to find out how many vCPUs are available for pinning by using the following command:

```bash
# virsh nodeinfo
```

Example:

```bash
# virsh nodeinfo
CPU model:           x86_64
CPU(s):              8
CPU frequency:       1531 MHz
CPU socket(s):       1
Core(s) per socket:  4
Thread(s) per core:  2
NUMA cell(s):        1
Memory size:         16262980 KiB

#
```

Use the following command to verify the available vCPU numbers:

```bash
#virsh capabilities
```

<br>

## vCPU Pinning

Use the following command to pin the virtual CPUs to sets of processor cores:

```bash
# virsh vcpupin <vmname > <vcpu# > <host core# >
```

This KVM command must be executed for each vCPU on your Cisco CSR 1000v. The following example pins virtual CPU 1 to host core 3:
virsh vcpupin csr1000v 1 3

The following example shows the KVM commands needed if you have a Cisco CSR 1000v configuration with four vCPUs and the host has eight cores:

```bash
# virsh vcpupin csr1000v 0 2
# virsh vcpupin csr1000v 1 3
# virsh vcpupin csr1000v 2 4
# virsh vcpupin csr1000v 3 5
```

The host core number can be any number from 0 to 7. For more information, see the KVM documentation.

<br>

