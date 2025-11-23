1. Configuring Network Interfaces and Settings
"My network connection was enp0s3 type = ethernet and the default loopback lo"

Checking current network settings
nmcli connection show
"After running this command on my terminal, ths was what was displayed"
        NAME          UUID                                  TYPE         DEVICE
        enp0s3        10a8c6f8-d530-3cb9-8b01-79f8dc87309d  ethernet     enpos3
        lo            1ed1a75d-648a-44bb-91ca-aea7a6c8433e  loopback     lo
        
ip addr show
"My inet loopback lo address was the default 127.0.0.1/8"
"My enp0s3 inet address wa 10.0.2.15/24"

static IP configuration
"Moved on to alter the current network configuration settings so as to assign a static ip address"
File: /etc/sysconfig/network-scripts/ifcfg-enp0s3
"Running the nano /etc/sysconfig/network-scripts/ifcfg-enp0s3 opened up a text editor and this changes were added to the configuration file".
TYPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
ONBOOT=yes

Restarted the network service:
"To make sure thesechanges were effective and applied it was essential to restart the network service by running the below command".
sudo systemctl restart NetworkManager

Using nmcli to configure
nmcli connection add con-name "MyConnection" ifname enp0s3 type ethernet ip4 192.168.1.100/24 gw4 192.168.1.1
nmcli connection modify "MyConnection" ipv4.dns "8.8.8.8 8.8.4.4"
nmcli connection up "MyConnection"
