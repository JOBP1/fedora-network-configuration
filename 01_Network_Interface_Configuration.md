1. Configuring Network Interfaces and Settings
Checking current network settings
nmcli connection show
ip addr show

static IP configuration

File: /etc/sysconfig/network-scripts/ifcfg-enp0s3

TYPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
ONBOOT=yes


Restarted the network service:

sudo systemctl restart NetworkManager

Using nmcli to configure
nmcli connection add con-name "MyConnection" ifname enp0s3 type ethernet ip4 192.168.1.100/24 gw4 192.168.1.1
nmcli connection modify "MyConnection" ipv4.dns "8.8.8.8 8.8.4.4"
nmcli connection up "MyConnection"
