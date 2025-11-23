"Tme main objective of this lab was to ensure that file sharing and printer sharing services were enabled and fully running on the server, so as to ease communication and file transfer for clients within the network".

1. Installing Samba
"Samba is a file and printer sharing service that allows Linux systems to communicate with Windows systems on a network".

sudo dnf install samba -y
"This command updated and loaded repositories. Samba installation was a success."

2.Configuring the Firewall
"This allows the Samba traffic through the firewall".

sudo firewall-cmd --permanent --zone=public --add-service=samba
sudo firewall-cmd --permanent --zone=public --add-port=139/tcp  "Port 139 is used by NetBIOS Session Service (older SMB connections)."
sudo firewall-cmd --permanent --zone=public --add-port=445/tcp  "Port 445 is the main SMB port used by modern Windows."

"Finally to apply thesse canges running the 'sudo firewall-cmd --reload' command made these changes".
sudo firewall-cmd --reload

3.Create Samba User
"To add a user I created a user called sambuser to Samba’s own authentication system."

sudo smbpasswd -a sambuser

"My username was "sambuser"
"A prompt requiring me to set my password was displayed" 
"Password was 'Sinatra1000' "

4.Configure Samba
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
"Running the 'sudo nano /etc/samba/smb.conf opened up a text editor so that I could add the smb.conf configuration file".
      [global]
              workgroup = WORKGROUP
              security = user
              map to geust = Bad User
              passdb backend = tdsam
              printing = cups
              printing name = cups
              load printers = yes
              cups option = raw
              smb unix extension = yes
              # install samba-usershares packages for support
              include = /etc/samba/usershares.conf
      [shares]
              path =/srv/samba/share
              browseable = yes
              read only =no
              valid users =sambauser
      [homes]
              comment = Home Directories
              valid users = %S, %D%w%S
              browseable = No
              inherit acls =Yes
      [printers]
              commment =All printers
              path = /var/tmp
              printable = yes
              create mask = 0600
              browseable = No
      [print]
              comment = Printer Drivers
              path = /var/lib/samba/drivers
              write list = @printadmin_root
              force group = @printadmin
              create mask =0664
              directory mask =0775
  "The above configurations are from my Text editor and all the adjustments I made on the text editor based on the lecture notes".

Testparrm
"Running testparm to test the configuration files was a success the output displayed that the Load services file Ok".

"Setting up printer sharing was also a success since running 'sudo dnf install cups' displayed Package 'cups-1:2.4.14-3.fc42.x86_64' is already installed".
  

5.Start and Enable Samba
sudo systemctl enable --now smb nmb
"Alternatively using 'sudo systemctl enable smb' still worked".

Service was a success

5. NFS Server Setup
"Purpose was to allow me to share directories with other computers over my network".
sudo dnf install nfs-utils -y

6.Create NFS Share Directory
"I proceeded to create a directory named Centora".
sudo mkdir /srv/centora
sudo chown nobody:nobody /srv/centora
sudo chmod 777 /srv/centora     "Learnt that the 777 meant giving file permission to read, write and execute"

7.Start and Enable NFS
sudo systemctl enable --now nfs-server
sudo exportfs -r
"NFS service was actively running and enabled based on th command 'sudo systemctl status nfs-server' ."

Starting and enabling the service was a success

8.Install vsftpd
sudo dnf install vsftpd -y
sudo systemctl enable --now vsftpd
sudo firewall-cmd --add-service=ftp --permanent
sudo firewall-cmd --reload
The enabling service was a success

9.Testing File Sharing
\\192.168.1.100\ShareName
sudo mount 192.168.1.100:/srv/share /mnt

In this lab, I configured file sharing services (Samba, NFS, FTP) to allow multiple devices on the network to access shared directories and printers. Each service wass configured for basic access and tested for connectivity.
