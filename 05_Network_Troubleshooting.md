Lab 5 – Network Troubleshooting
1. Checking Hostname and System Identity
hostnamectl

2. Testing Local and External Connectivity
ping 127.0.0.1
ping 192.168.1.1
ping google.com

3. Viewing Active Network Sockets
ss -tulnp

4. Checking Running Services
systemctl status network
systemctl status named
systemctl status dhcpd
systemctl status ntpd

5. DNS Resolution Test
dig labnetwork.com

6. Looking for Errors in Logs
journalctl -xe
