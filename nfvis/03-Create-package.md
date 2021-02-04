# Create package

## Standard VM Image Packaging
The standard VM packaging is based on the Open Virtualization Format (OVF) packaging standard, in which a single file is distributed in open virtualization appliance (OVA) format. The VM image is shared using a TAR archive file with the root disk image and descriptor files.

Cisco Enterprise NFVIS supports VM packaging in .tar.gz (compressed form of OVA) format. Ensure that all supported third party VM images are available in the supported format.

## NFVIS Package
The NFVIS VM package file format (*.tar.gz package) contains the following files:
-	A root disk image file (*.qcow2 or *.img)
-	A Manifest file (XML format)
-	A VM Image Properties File (XML format)
-	One or more bootstrap config/other files
-	Additional VM Bootstrap files (Optional)

Image Properties and Manifest files are created by using either the NFVIS GUI or using the packaging utility provided with every release.

**The package manifest (package.mf)**
The package manifest is a simple XML file that lists, for each file in the archive, what type of file it is and its sha1 checksum. Here is an example from ISRv SD-WAN package available on CCO

```
<!-- sha1sum - for calculating checksum -->
<PackageContents>
  <File_Info> 
    <name>isrv-universalk9.16.09.01a-vga.qcow2</name>
    <type>root_image</type>
    <sha1_checksum>92aef15a1f1b13fb06c325a1a22e9aa61c715de6</sha1_checksum>
  </File_Info> 
  <File_Info> 
    <name>image_properties.xml</name>
    <type>image_properties</type>
    <sha1_checksum>3cb15de7929a491d5cc08cfb4c097d7550cb1494</sha1_checksum>
  </File_Info> 
  <File_Info> 
    <name>isrv_ovf_env.xml</name>
    <type>bootstrap_file_1</type>
    <sha1_checksum>6198d321e6210c8787a881aeeab85b5a0014995e</sha1_checksum>
  </File_Info> 
</PackageContents>
```

**Image properties (image_properties.xml)**
This file contains the various properties for the image bundle. Includes details about the version, minimum and maximum amount of CPU/memory/disk/vNICs it supports, support for SRIOV drivers, profiles, and bootstrap files/variables.

There are tools, such as the VM packaging tool, that can generate an appropriate image_properties.xml file for you if you provide the necessary details. It is relatively simple XML, so you can certainly also create your own or modify an existing one using your text editor of choice.

**Bootstrap (bootstrap_file) - Bootstrap files for VNF.** 
Two parameters are required in the format of dst:src; dst filename including path has to match exactly to what the VM expects; up to 20 bootstrap files are accepted. For example: --bootstrap ovf-env.xml for ISRv and --bootstrap day0-config for ASAv. 

The bootstrap configuration file is an XML or a text file, and contains properties specific to a VM and the environment. Properties can have tokens, which can be populated during deployment time from the deployment payload.

VM Packaging Utility (nfvpt.py) bootstrap option examples:
-	ASAv: --bootstrap day0-config:file1
-	ISRv: --bootstrap ovf-env.xml:file1,ios-xe.txt:file2
-	vEdgeCloud: --bootstrap /openstack/latest/user_data:cloudinit.cfg,/openstack/latest/meta_data.json:meta_data,/openstack/latest/vendor_data.json:vendor_data

<br>

## Package Creation using the VM Packaging Utility
Packaging utility can be found on CCO
-	Downloads Home > Products > Routers > Network Functions Virtualization > Enterprise NFV Infrastructure Software >
-	File name: VM packaging utility

Or directly from the NFVIS local portal. 
-	Go to VM life cycle - > image repository -> browse Datastore. Browse to data->intdatastore->uploads-> vmpackagingutility

The VM packaging utility contains the following
-	nfvpt.py—It is a python based packaging tool that bundles the VM raw disk image/s along with VM specific properties.
-	image_properties_template.xml—This is the template file for the VM image properties file, and has the parameters with default values. If the user provides new values to these parameters while creating the VM package, the default values get replaced with the user-defined values.
-	nfvis_vm_packaging_utility_examples.txt—This file contains examples on how to use the image packaging utility to package a VM image.

