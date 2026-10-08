DHCP Practice - Jorge Garcia Ramos


2. DHCP server installation
I installed the ISC DHCP server with:
sudo apt update
sudo apt install isc-dhcp-server -y

Before modifying the main configuration file, I created a backup:
sudo cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak

I configured the DHCP service to listen on the internal interface and added the following DHCP range:
192.168.57.20 - 192.168.57.50

I also configured:
default-lease-time 86400;
max-lease-time 691200;
option domain-name "jorge.test";
option domain-name-servers 10.0.0.2, 4.4.4.4;

Before restarting the service I checked the syntax with:
sudo dhcpd -t
No syntax errors were shown

Then I restarted and checked the service:
sudo systemctl restart isc-dhcp-server.service
sudo systemctl status isc-dhcp-server.service


I also checked that the DHCP port was listening:
sudo ss -lun
The server was listening on UDP port 67


3. DHCP client c1

I connected c1 to the same internal network and configured it to obtain its IP automatically

I checked the configuration with:
ip a

The internal interface was eth1 and it received an IP inside the configured DHCP range

I also tested releasing and renewing the DHCP lease:
sudo dhclient eth1 -r
sudo dhclient eth1

On the server, I checked the lease database with:
sudo tail -n 20 /var/lib/dhcp/dhcpd.leases

And it was active:
binding state active;
client-hostname "c1";
This confirmed that the DHCP server was correctly assigning dynamic addresses


4. Printer reservation

For the printer machine I first checked its MAC address using:
ip a

The MAC address of the internal interface was:
08:00:27:81:ed:05

I fixed this MAC address in the Vagrantfile and created a reservation in the DHCP configuration
The reservation used in my configuration was:
(printer)
hardware ethernet 08:00:27:81:ed:05;
fixed-address 192.168.57.100;
default-lease-time 7200;

After checking the configuration again:
sudo dhcpd -t

I restarted the DHCP service:
sudo systemctl restart isc-dhcp-server.service

Then I renewed the printer lease:
sudo dhclient eth1 -r
sudo dhclient eth1

Finally, I checked it with:
ip a

The MAC-based reservation was working correctly


5. Routing and NAT
The last part of the practice was to use server as a router between the internal network and the public network

I enabled IPv4 forwarding and configured NAT with iptables
The main NAT rule used was:
iptables -t nat -A POSTROUTING -s 192.168.57.0/24 -o eth1 -j MASQUERADE

I checked IPv4 forwarding with:
cat /proc/sys/net/ipv4/ip_forward

The clients were configured to use the server as their default gateway:
192.168.57.10

I checked the routing table with:
ip r

Finally, I tested Internet connectivity from both clients:
ping -c 4 8.8.8.8
Result:
4 packets transmitted, 4 received and 0% packet loss
This confirmed that the routing and NAT configuration was working correctly

To finish i just needed to save the progress into my github