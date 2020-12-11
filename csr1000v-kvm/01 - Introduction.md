# INTRODUCTION

## Pre-requisites

This document assumes that you have the KVM hypervisor already installed along with the libvirt management application. Additionally, you will need the virt-install and cloud-localds CLI tools.

It is also assumed that you have downloaded the virtual disk images from https://software.cisco.com (make sure you take -serial qcow2 image) and the serial file from the PnP Portal with your Smart Account / Virtual Account.

## KVM

KVM has both GUI (virt-manager) and command line (virsh, virt-install) management tools, so you can choose whichever suits you best.

## CSR1000v Requirements

The Cisco CSR 1000v installation on KVM requires the manual creation of a VM and installation using the .iso file or the qcow2 file.
vCPU:

- 1 vCPU: requires minimum 4 GB RAM allocation
- 2 vCPUs: requires minimum 4 GB RAM allocation
- 4 vCPUs: requires minimum 4 GB RAM allocation

Virtual hard disk size—8 GB minimum

Supported vNICs—Virtio, ixgbe, ixgbevf or i40evf [TODO]

Maximum number of vNICs supported per VM instance—26 [TODO]