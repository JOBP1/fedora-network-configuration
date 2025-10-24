Installation of Fedora Server on VirtualBox
The Requirements

VirtualBox installed on the machine

Fedora Server ISO file (downloaded from the official Fedora website)

At least 2 GB RAM and 20 GB storage allocated to the VM

Steps

Created a new VM in VirtualBox

Name: FedoraServer

Type: Linux

Version: Fedora (64-bit)

Memory: 2048 MB (minimum)

Hard disk: 20 GB (VDI, dynamically allocated)

Attached Fedora Server ISO

I Configured Settings → Storage → Controller: IDE → Add Optical Drive

I Choose the Fedora Server ISO file

I Started Boot and begun installation

Started the VM

Selected Install Fedora Server

Installation process begun with

Choosing language and keyboard

Configured the disk → selected automatic partitioning

I Set root password

Created a user 

The Server Begun Installation and I waited until it completed

Proceeded To Reboot into Fedora Server

After installation, I removed the ISO from VirtualBox storage settings

I Finally Rebooted and logged in as the root user I created

Then Verified installation and Installed The KDE-desktop environment

At this point, Fedora Server was installed and ready for Chapter 5 configurations.

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

2. DHCP Server Configuration

Installed the DHCP server package:

sudo dnf install dhcp-server -y


Configuration file: /etc/dhcp/dhcpd.conf

subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.100 192.168.1.200;
    option subnet-mask 255.255.255.0;
    option routers 192.168.1.1;
    option domain-name-servers 8.8.8.8, 8.8.4.4;
}


Started and enabled the service:

sudo systemctl enable --now dhcpd


Open firewall for DHCP:

sudo firewall-cmd --add-service=dhcp --permanent
sudo firewall-cmd --reload

3. DNS Server Setup (BIND)

Installation of BIND:

sudo dnf install bind bind-utils -y


Configuration file: /etc/named.conf

options {
    listen-on port 53 { 192.168.1.10; };
    allow-query { 192.168.1.0/24; };
};


Add a zone:

zone "labnetwork.com" {
    type master;
    file "/var/lanetwork.com.zone";
};


Example zone file: /var/named/example.com.zone

$TTL 86400
@   IN  SOA ns1.example.com. admin.example.com. (
        2023100801 ; Serial
        3600       ; Refresh
        1800       ; Retry
        604800     ; Expire
        86400 )    ; Minimum TTL
@   IN  NS  ns1.labnetwork.com.
@   IN  A   192.168.1.1
www IN  A   192.168.1.2
mail IN A   192.168.1.30


Check configuration:

sudo named-checkconf /etc/named.conf
sudo named-checkzone labnetwork.com /var/named/example.com.zone
labnetwork.com.zone

Start and enable BIND:

sudo systemctl enable --now named


Allow DNS through firewall:

sudo firewall-cmd --add-service=dns --permanent
sudo firewall-cmd --reload


Test DNS with:

dig labnetwork.com
dig www.labnetwork.com
dig mail.labnetwork.com

4. Network Time Protocol (NTP)

Install NTP:

sudo dnf install ntp -y


Configuration file: /etc/ntp.conf

server 0.fedora.pool.ntp.org iburst
server 1.fedora.pool.ntp.org iburst
driftfile /var/lib/ntp/ntp.drift


Start and enable service:

sudo systemctl enable --now ntpd


Check service status and peers:

systemctl status ntpd

5. Network Troubleshooting

This was done so as to use some of basic Linux network diagnostic tools to verify network status and confirm whether services are running correctly.

Checking Hostname and System Identity


ntpq -p

