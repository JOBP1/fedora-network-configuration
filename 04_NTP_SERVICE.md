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

