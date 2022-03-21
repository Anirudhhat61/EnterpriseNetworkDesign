# EnterpriseNetworkDesign

Use any version of cisco packet tracer to open this file. 

Network consists of a three locations,
1. New York branch
2. Chicago Branch
3. Data Center

All these locations are connected through a frame relay switch. The Data Center is connected to the ISP to get to the simulated Internet (it is a 4.2.2.0/24 network).
1)   Switches: IP addresses

Configure IP addresses for the switches (which will be in VLAN 1, subnet 172.16.1.0/24). Their default gateway will be 172.16.1.254 (I used 3560 switches for the IP phones)
2)  Switches: VTP and VLANS

Configure the ports connected between the switches to trunk ports. Create VLANs 30 and 40 on one switch, name them DATA1 and DATA2 respectively. Configure the VTP domain "NY" on the switch (and optionally VTP password "cisco". Assign the ports that will have PCs attached to the VLANs (check topology image). Verified that all VLANs have replicated by show VLAN on the other switches.

VLAN   Name  Status  Ports
10     VOICE_WB  active 
30     DATA1_WB  active  Fa0/5, Fa0/6
40     DATA2_WB active        Fa0/2, Fa0/3, Fa0/4, Fa0/7

VTP Domain Name: NY
VTP Password: cisco

3)  Switches: STP

The top switch will be the root switch. Configure the top switch to be the root switch for all VLANs (1, 30 and 40). Optional: Configure ports that are/will be connected to PCs/routers to portfast. Verify by show spanning-tree on all switches that the top switch is the root.


4)  Routers: Gateway Router/DHCP

Configure it's FastEthernet port on the VLAN 40 subnet and configure the port on the switch on VLAN 40. Configure DHCP pools for VLANs 30 and 40.

ip dhcp pool FORVLAN40
network 172.16.40.0 255.255.255.0
default-router 172.16.40.254
dns-server 4.2.2.2

ip dhcp pool FORVLAN30
network 172.16.30.0 255.255.255.0
default-router 172.16.30.254
dns-server 4.2.2.2

ip dhcp pool FORVLAN10_VOICE
network 172.16.10.0 255.255.255.0
default-router 172.16.10.254
option 150 ip 172.16.10.254


5) Routers: Routing protocol (EIGRP)
I have used EIGRP (Enhanced Interior Gateway Routing Protocol). After configuring routing protocols on both routers, verify by show ip route on the Gateway router. You can verify by ping (for example, ping from Gateway router [VLAN 40] to one of the switches [VLAN 1]) to verify inter VLAN routing. Also, ping from a PC on one VLAN to another PC on the other VLAN.


Data Center
This is straight forward configuration. There is no VTP here, no STP, Use the same EIGRP routing protocol. Configure the serial interface that will connect to the ISP with the IP address 68.110.171.134/30 .



ISP (Internet Service Provider)

Configure both serial ports with IPs.

Port   IP
Serial0/1/1  68.110.171.133/30
Serial0/1/0  55.55.55.57/30



Internet:

Also, straight forward; configure static IPs to the servers and all that. But again, do NOT configure a routing protocol on the router. Instead, just configure a static route to the 68.110.171.132/30 network going through the ISP router.

I have placed three servers on the internet, Web Server, Mail server, DNS Server

Type                  IP
DNS Server   4.2.2.2/24
Mail Server   4.2.2.3/24
Web Server   4.2.2.4/24



NAT and ACLs

Configured NAT on the Data Center router. My ACL was configured as follows: deny VLAN 40 subnet in NY Branch (except routers/manager laptop), deny VLAN 35 subnet in IL Branch (except routers/manager laptop), and permit all the other subnets (I did not permit any, I permit the specific subnets for practice). I have created an ACL list in each building to only allow manager laptops to telnet to the routers/switches.


Source : https://www.youtube.com/watch?v=3dhs9cl6M5E
