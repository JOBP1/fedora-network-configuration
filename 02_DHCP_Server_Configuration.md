2. DHCP Server Configuration
"Installing and configuring the DHCP Server would allow the server to automatically assign IP addresses and any important information to the linked network devices on the network"?
"For any package installation on the server, it required the root password of the root user so as to install the packages".

Installed the DHCP server package
"For this package the below command was a success".
sudo dnf install dhcp-server -y


Configuration file: /etc/dhcp/dhcpd.conf : "This is the default file location of the dhcp files"
"Using /etc/dhcp/dhcpd.conf denied me access unless running it with the sudo command that verified that I was accessing the file as the root user".
"After accessing this file a text editor appeared that allowed the adding of the below configurations".

subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.100 192.168.1.200;
    option subnet-mask 255.255.255.0;
    option routers 192.168.1.1;
    option domain-name-servers 8.8.8.8, 8.8.4.4;

Started and enabled the service:

sudo systemctl enable --now dhcpd
"You can also use this command". : sudo systemctl enable dhcpd 
"Some commands varied due to the version of the server I was using, I had installed Fedora Server V.42".

To start the DHCP
sudo systemctl start dhcpd
"To make sure that the service was active and running i used the below command which displayed the status as active and enabled, this was important because if the server wasn't running, no client would be assigned any IP address,. In other trials the enabling and starting of the server exited with error codes but I eventually fixed and debugged the errors".

To See The Status of The DHCP Server
sudo systemctl status dhcpd

Open firewall for DHCP:
"Configuring the Firewall allows the incoming and the outgoing traffic on the required ports".
sudo firewall-cmd --add-service=dhcp --permanent
"The attempt to add this service multipletimes would result to a warning: ALREADY_ENABLED: dhcp. This was a success."

sudo firewall-cmd --reload
"This command enabled all the changes to be applied. It displayed the prompt success."
