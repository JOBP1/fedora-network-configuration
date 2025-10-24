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
