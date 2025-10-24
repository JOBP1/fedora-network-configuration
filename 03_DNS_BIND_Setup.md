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

