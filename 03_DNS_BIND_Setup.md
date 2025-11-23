3. DNS Server Setup (BIND)
"To effectively run this srvice the main consideration was ensuring that the server had a static address. My static address was set to 192.168.1.100".

Installation of BIND:
sudo dnf install bind bind-utils -y "This command was the updated command for my version 42 server that allowed the proper installation of Bind and any other required utiliy files".
"Alternatively sudo dnf install bind also worked".

Configuration file: /etc/named.conf
"Running this command would open up a text box that I needed to alter and add the below configuration. Adding the IP Address of my DNS Server 92.168.1.100, I had to alter the Listen on port

options {
    listen-on port 53 { 192.168.1.100; };
    allow-query { 192.168.1.0/24; };
};


Add a zone:
"To open and make changes on the file I opened the /etc/named.conf that opened the textbox for adding the below changes, I replaced the zone with my own zone i.e labnetwork.com".

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
"To make sure that the zones are Okay, I had to run certain commands to make sure no errors were present. This was a success."

sudo named-checkconf /etc/named.conf
sudo named-checkzone labnetwork.com /var/named/labnetwork.com.zone


Start and enable BIND:
"Starting the service and enabling it was also a success. To confirm its success I checked the status and it was active and enabled".

sudo systemctl start named
sudo systemctl enable --now named


Allow DNS through firewall:
"This ensured that the server added the dns service".
sudo firewall-cmd --add-service=dns --permanent
sudo firewall-cmd --reload


Test DNS with:
"Running these commands on the terminal will display the records and Query, Status: NO ERROR and the ID: 46428 which nmeant the configurations was a success."
dig labnetwork.com
dig www.labnetwork.com
dig mail.labnetwork.com

