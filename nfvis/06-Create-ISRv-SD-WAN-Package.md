# 06 - Create ISRv SD-WAN Package


## Objective
Create a package file named" isrv-sdwan-jmb-16.12.01e" that contains:
-	qcow2 image
-	cloudinit.cfg 
-	image_properties.xml
-	package.mf

Using the following input files:
+ isrv-sdwan-jmb.16.12.01e
+ cloudinit.cfg => used as ciscosdwan_cloud_init.cfg  
+ meta_data: mounted as /openstack/latest/meta_data.json  
+ vendor_data: mounted as /openstack/latest/vendor_data  

cloudinit.cfg is the bootstrap configuration for the ISRv. This day0 config is included in the package and therefore needs to be tokenised to that you can change the parameters for each new deployment.

<br>

## cloud-init file format
The bootstrap config is a MIME encoded file, similar to the vEdgeCloud file format. 

The SD-WAN cloud-init file has two parts, cloud-config and cloud-boothook. The cloud-config section is the same, the cloud-boothook section contains the IOS-XE SD-WAN CLI configuration.

<br>

## Creating cloud-init file using Linux tools
Refer to the same section in the vEdgeCloud chapter.

cloudinit.cfg example:
```
Content-Type: multipart/mixed; boundary="===============6177259887390062818=="
MIME-Version: 1.0

--===============6177259887390062818==
Content-Type: text/cloud-config; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="cloud-config.txt"

#cloud-config
vinitparam:
 - uuid : ${UUID}
 - vbond : ${VBOND_IP}
 - otp : ${OTP}
 - org : ${ORG_NAME}
 - rcc : true
ca-certs:
  remove-defaults: false
  trusted:
  - |
-----BEGIN CERTIFICATE-----
MIID2TCCAsGgAwIBAgIJAIVMQ5fM+2vpMA0GCSqGSIb3DQEBCwUAMIGCMQswCQYD
VQQGEwJGUjELMAkGA1UECAwCRlIxDjAMBgNVBAcMBVBhcmlzMQ4wDAYDVQQKDAVD
aXNjbzEOMAwGA1UECwwFQ2lzY28xGDAWBgNVBAMMD3Nkd2FuLmNpc2NvLmNvbTEc
MBoGCSqGSIb3DQEJARYNam1iQGNpc2NvLmNvbTAeFw0xOTA0MDMxNjEzMDFaFw0y
OTAzMzExNjEzMDFaMIGCMQswCQYDVQQGEwJGUjELMAkGA1UECAwCRlIxDjAMBgNV
BAcMBVBhcmlzMQ4wDAYDVQQKDAVDaXNjbzEOMAwGA1UECwwFQ2lzY28xGDAWBgNV
BAMMD3Nkd2FuLmNpc2NvLmNvbTEcMBoGCSqGSIb3DQEJARYNam1iQGNpc2NvLmNv
bTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAOifj+Sr5QG9mHcN8+q5
KX6CWlF1Dw69UNktqSVZlUvwgdoBM6Ewy2C8r5NwZOobK4O+FwyF4AmZJY74nGZs
8/s5nTZ8szROGZ8m5nsGd8bUl8aU8r1c1UbCmWF7uGeMPVoCgkn2fgsFl7cjgxYB
wxE5gGSKlRQYVyD+MeThu/m4TyyFYYyE7b9deidLm/eMNw7edMWmb75Dlw9KhF/p
jxe6iVGKUqawpHIXf9iCnUEkds6hD7YUxGnkMJCiY1tWA+JJZsn+ngQl6WeQU1gi
1mok6+LAQ2/uzT7BbFaKmLxMP+KnwFsXaZACnxdOa/sDYin8OEoTrB1nMVYUmwE/
u7UCAwEAAaNQME4wHQYDVR0OBBYEFIbi3PnJOnQwRiQDcg6wFbG59JdAMB8GA1Ud
IwQYMBaAFIbi3PnJOnQwRiQDcg6wFbG59JdAMAwGA1UdEwQFMAMBAf8wDQYJKoZI
hvcNAQELBQADggEBABXcsa34qk1r9UKhAHlmmDBIspjxu2bH1PkEkMyA9NNK1uoK
vplPqek+PCSlxv3Mu2OCbCMgnmHHQa/tKLLDt2WB797Vc85K2pm30UV21RV058QD
7mrLb2IHPkm1UcXG4E9CFxjWiH2FZJTrecdHnLPgkioxNieRZLa7E5UANb1XUOaG
dcVclDPupcxFbWSySdiyC1dCG4i/4gemLSpDdaewCbFXssvN8RdTK+FI5WbPAoPV
W0wCloUQDc4gsgR81QVFP2ZqIndvPdmR72LbHP4L4jtLxVIjv8iRev44VuoCOUIf
jxL61e0urJeax32rKRCC2Fwkxb5iV1jlddSjWQE=
-----END CERTIFICATE-----

--===============6177259887390062818==
Content-Type: text/cloud-boothook; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="cloud-boothook.txt"

#cloud-boothook
  viptela-system:system
   personality           vedge
   device-model          vedge-ISRv
   host-name             cedge9
   location              Paris
   system-ip             ${SYSTEM_IP}
   overlay-id            1
   site-id               6
   control-session-pps   300
   admin-tech-on-failure
   sp-organization-name  ${ORG_NAME}
   organization-name     ${ORG_NAME}
   console-baud-rate     115200
   vbond ${VBOND_IP} port 12346
   logging
    disk
     enable
    !
   !
  !
  bfd app-route multiplier 6
  bfd app-route poll-interval 600000
  omp
   no shutdown
   graceful-restart
  !
  security
   ipsec
    rekey               86400
    replay-window       512
    authentication-type sha1-hmac ah-sha1-hmac
   !
  !
  no service pad
  no service tcp-small-servers
  no service udp-small-servers
  hostname cedge9
  username admin privilege 15 secret 9 <password>
  username jmb privilege 15 secret 9 <password>
  vrf definition 10
   rd 1:10
   address-family ipv4
    exit-address-family
   !
   address-family ipv6
    exit-address-family
   !
  !
  vrf definition Mgmt-intf
   description Transport VPN
   rd          1:512
   address-family ipv4
    exit-address-family
   !
   address-family ipv6
    exit-address-family
   !
  !
  no ip finger
  no ip rcmd rcp-enable
  no ip rcmd rsh-enable
  no ip dhcp use class
  ip route 0.0.0.0 0.0.0.0 10.60.23.254 1
  no ip igmp ssm-map query dns
  no ip rsvp signalling rate-limit
  no ipv6 mld ssm-map query dns
  interface GigabitEthernet1
   description MGMT Interface
   no shutdown
   arp timeout 1200
   vrf forwarding Mgmt-intf
   ip address ${NICID_0_IP_ADDRESS} ${NICID_0_NETMASK}
   ip redirects
   ip dhcp client default-router distance 1
   ip mtu    1500
   mtu         1500
   negotiation auto
  exit
  interface GigabitEthernet2
   description INET Transport
   no shutdown
   arp timeout 1200
   ip address dhcp client-id GigabitEthernet2
   ip redirects
   ip dhcp client default-router distance 1
   ip mtu    1500
   mtu         1500
   negotiation auto
  exit
  interface Loopback0
   no shutdown
   arp timeout 1200
   vrf forwarding 10
   ip address 10.10.10.90 255.255.255.255
   ip mtu 1500
  exit
  interface Tunnel2
   no shutdown
   ip unnumbered GigabitEthernet2
   no ip redirects
   ipv6 unnumbered GigabitEthernet2
   no ipv6 redirects
   tunnel source GigabitEthernet2
   tunnel mode sdwan
  exit
  clock timezone UTC 0 0
  logging persistent size 104857600 filesize 10485760
  logging buffered 512000
  no logging rate-limit
  logging persistent
  aaa authentication login default local group radius group tacacs+
  aaa authorization exec default local group radius group tacacs+
  aaa session-id common
  no crypto ikev2 diagnose error
  no router rip
  line con 0
   login authentication default
   speed    115200
   stopbits 1
  !
  sdwan
   interface GigabitEthernet2
    tunnel-interface
     encapsulation ipsec weight 1
     color default
     no last-resort-circuit
     vmanage-connection-preference 5
     no allow-service all
     no allow-service bgp
     allow-service dhcp
     allow-service dns
     allow-service icmp
     allow-service sshd
     no allow-service netconf
     no allow-service ntp
     no allow-service ospf
     no allow-service stun
    exit
   exit
   interface Loopback0
   exit
   omp
    no shutdown
    send-path-limit  4
    ecmp-limit       4
    graceful-restart
    timers
     holdtime               60
     advertisement-interval 1
     graceful-restart-timer 43200
     eor-timer              300
    exit
    address-family ipv4 vrf 10
     advertise connected
     advertise static
    !
    address-family ipv4
     advertise connected
     advertise static
    !
   !
  !
  policy
   app-visibility
   flow-visibility
   no implicit-acl-logging
   log-frequency        1000
  !
 !
!

--===============6177259887390062818==--
```

<br>

**IMPORTANT NOTE:** 

> ${NICID_0_IP_ADDRESS}, ${NICID_0_NETMASK} and ${NICID_0_CIDR_PREFIX} are reserved parameters. The VM Lifecycle Manager (ESC-Lite) on NFVIS will replace these variables with values assigned by ESC-Lite. This is used for the int-mgmt-net addresses. They are assigned by NFVIS and not by DHCP.

<br>

## Create ISRv SD-WAN package

Use the following command to create the ISRv SD-WAN package:
```
/usr/bin/python ./nfvpt.py -o isrv-sdwan-jmb-16.12.01e -i isrv-ucmk9.16.12.01e-vga.qcow2  -n isrv-sdwan-jmb.16.12.01e -t ROUTER -r 16.12.01e --monitored true --privileged true --optimize false --bootstrap ciscosdwan_cloud_init.cfg:cloudinit.cfg --min_vcpu 1 --max_vcpu 8 --min_mem 4096 --max_mem 8192 --min_disk 8 --max_disk 8 --vnic_max 8 --optimize true --nocloud true --profile ISRv-mini,"ISRv-mini",1,4096,8192 --profile ISRv-small,"ISRv-small",2,4096,8192 --profile ISRv-medium,"ISRv-medium",4,4096,8192 --default_profile ISRv-small --custom key:UUID,val:1 --custom key:OTP,val:1 --custom key:SYSTEM_IP,val:10.0.0.100 --custom key:ORG_NAME,val:test --custom key:VBOND_IP,val:1.1.1.1
```

Input:
-	isrv-ucmk9.16.12.01e-vga.qcow2
-	cloudinit.cfg     --> loaded on router as ciscosdwan_cloud_init.cfg

Output:
-	Image Name: isrv-sdwan-jmb.16.12.01e
-	File Name: isrv-sdwan-jmb-16.12.01e.tar.gz 

Note:
-	Generated with python2
-	Does not work with python3



