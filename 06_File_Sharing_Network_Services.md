Objective of this lab was to Configure Samba, NFS, and FTP services to provide file and printer sharing across the network.

1. Installing Samba
sudo dnf install samba -y

2.Configuring the Firewall
Allow Samba traffic

sudo firewall-cmd --permanent --zone=public --add-service=samba
sudo firewall-cmd --permanent --zone=public --add-port=139/tcp
sudo firewall-cmd --permanent --zone=public --add-port=445/tcp
sudo firewall-cmd --reload

3.Create Samba User
sudo smbpasswd -a your_username
My username was "sambuser"
Password was "Sinatra1000"

4.Configure Samba
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak

5.Start and Enable Samba
sudo systemctl enable --now smb nmb
Service was a success

5. NFS Server Setup
sudo dnf install nfs-utils -y

6.Create NFS Share Directory
sudo mkdir /srv/share
sudo chown nobody:nobody /srv/share
sudo chmod 777 /srv/share

7.Start and Enable NFS
sudo systemctl enable --now nfs-server
sudo exportfs -r

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




