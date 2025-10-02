# fedora-network-configuration
Documentation For Fedora Server Network Configuration and Services (Chapter 5)
1. Configuring Network Interfaces and Settings
Checking current network settings
nmcli connection show
ip addr show

TYPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
ONBOOT=yes


Restart the network service:

sudo systemctl restart NetworkManager

Using nmcli to configure
nmcli connection add con-name "MyConnection" ifname enp0s3 type ethernet ip4 192.168.1.100/24 gw4 192.168.1.1
nmcli connection modify "MyConnection" ipv4.dns "8.8.8.8 8.8.4.4"
nmcli connection up "MyConnection"

2. DHCP Server Configuration

Install the DHCP server package:

sudo dnf install dhcp-server -y

Configuration file: /etc/dhcp/dhcpd.conf

subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.100 192.168.1.200;
    option subnet-mask 255.255.255.0;
    option routers 192.168.1.1;
    option domain-name-servers 8.8.8.8, 8.8.4.4;
}

Start and enable the service:

sudo systemctl enable --now dhcpd

Open firewall for DHCP:

sudo firewall-cmd --add-service=dhcp --permanent
sudo firewall-cmd --reload

3. DNS Server Setup (BIND)

Install BIND:

sudo dnf install bind bind-utils -y
Configuration file: /etc/named.conf

options {
    listen-on port 53 { 192.168.1.10; };
    allow-query { 192.168.1.0/24; };
};

Add a zone:
zone "example.com" {
    type master;
    file "/var/named/example.com.zone";
};


Example zone file: /var/named/example.com.zone

$TTL 86400
@   IN  SOA ns1.example.com. admin.example.com. (
        2023100801 ; Serial
        3600       ; Refresh
        1800       ; Retry
        604800     ; Expire
        86400 )    ; Minimum TTL
@   IN  NS  ns1.example.com.
@   IN  A   192.168.1.1
www IN  A   192.168.1.2

Check configuration:

sudo named-checkconf /etc/named.conf
sudo named-checkzone example.com /var/named/example.com.zone

Start and enable BIND:

sudo systemctl enable --now named

Allow DNS through firewall:

sudo firewall-cmd --add-service=dns --permanent
sudo firewall-cmd --reload


Test DNS with:

dig example.com

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
ntpq -p


NTP for accurate time synchronization

These services are the backbone of a networked environment. Setting them up on Fedora Server gave me practical experience in system administration and managing core infrastructure.
