4. Network Time Protocol (NTP)
"The man purpose of setting the Network Time Protocol On my Server was to ensure that the server maintained accurate time synchronization on my network"

Install NTP:
sudo dnf install ntp -y
"Alternatively also using the command 'sudo dnf install ntp' also worked".

Configuring NTP: "The primary file was actually /etc/ntp.conf. I needed to edit this file and use the default Fedora servers as listed below".
"The only hitch in configuring this file was that running the command as '/etc/ntp/conf' would result to a prompt permission denied. To counter this I ran 'sudo /etc/ntp/conf' which also didn't work as a root user. The correct command eventually was 'sudo nano /etc/ntp/conf', this command allowed me to access the text editor window and uncomment the server lines and add the IP address of the NTP Server".

server 0.fedora.pool.ntp.org iburst
server 1.fedora.pool.ntp.org iburst
driftfile /var/lib/ntp/ntp.drift

"After making these changes I saved and exited the text editor".
"After it was inportant to restart the serviceto make sure these changes were applied"

Restarted the Service
sudo systemctl restartntpd

Start and enable service:
"A propmpt 'Created symlink '/etc/systemd/system/multi-user.target.wants/ntpd.service' - '/usr/lib/systemd/system/ntpd.service'. was displayed"
"I Checked it's status to ensure that the ntp service was fully running and through the logs it was active (running) meaning that the ntp service configuration was a success".

Enabling NTP Service
sudo systemctl enable --now ntpd

Checking the NTP SErvice status
sudo systemctl status ntpd
systemctl status ntpd

ntpq -p
"Using the command 'ntpq -p' would often result to a Request time out not sure why".
