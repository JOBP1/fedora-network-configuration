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
"NFS service was actively running and enabled based on the command 'sudo systemctl status nfs-server' ."

"Starting and enabling the service was a success".

8.Opening Firewall Ports
"Since NFS uses some default ports such as 11(rpcbind) and 2049(NFS), I had to access these ports by using the below commands on the terminal".
sudo firewall-cmd --permanent --xone=public --add-service=nfs      "Learnt that it opens all required ports for NFSv3 and NFSv4 file sharing".
sudo firewall-cmd --permanent --xone=public --add-service=mountd      "Learnt that it handles mount requests from clients."
sudo firewall-cmd --permanent --xone=public --add-service=rpc-bind      "Learnt that this opens the ports for rpcbind, which maps NFS services to dynamic ports".

sudo firewall-cmd --add-service=ftp --permanent
sudo firewall-cmd --reload
"The enabling service was a success"

Export the NFS Shares
sudo exportfs -arv
"Exported successfully".

Set Appropriate Permissions
sudo chmod -R 755 /centora      "Learnt that this changed permissions on my folder /centora including everything inside it — because of the -R (recursive) option, meaning that the file was readable and accessible to all other users but I as the owner had therights to write".

9. Installing THe FTP Servers
"This would allow me to transfer files from my server to a network".
"For an FTP Servers I went with the most common and the one highlighted in the lecture notes which was vsftpd (Very Secure FTP Daemon) "
sudo dnf install vsftpd

"The update and repositories were intalled successfully, leaving me with the task of starting and enabling the service".
sudo dnf start vsftpd
sudo dnf enable vsftpd

"To check if the service was actively running and enabled I used 'sudo dnf status vsftpd' which resulted in showing that the service wa up and actively running".
"Next task wat to configure the /etc/vsftpd/vsftpd.conf file add make some slight changes".

10. Configuring FTP Server
sudo nano /etc/vsftpd/vsftpd.conf
"This opened up the text editor and I made the following changes".
            anonymous_enable=YES
            local_enable=YES
            listen=NO
            local_umask=022
            listen_ipv6=YES

11. Firewall Configuration
"I had already enabled firewalld for the ftp service and the status was actively running".
