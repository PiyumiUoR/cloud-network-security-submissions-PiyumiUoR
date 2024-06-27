# Task 1

## A)
According to the site, Snort is a open source IPS (Intrusion Prevention System) which uses rules to identify malcious packets. In Snort the rules have defined so that a a malcious betwork activity can be identified. 

The uses of Snort can be shown as below. 
1. Packet sniffer - It uses tcpdump for the packet sniffing.
2. Packet logger - This is useful for the network debugging.
3. IPS - Snort can be used as network IPS as full-blown and for both personal and business use cases.


* IP of the Ubuntu server: 10.3.1.10/24
* IP of the Kali machine: 10.0.0.27/24
* IP of the host machine: 10.0.2.15/24

![Snort package manager](images/task1-snort.png)

## B)

The web GUI of the Ubuntu server can be accessed through the `http://10.3.1.10/`

![Ubuntu server web GUI](images/task1-b-gui.png)

At first I enabled Snort on the DMZ interface. Below images show the configurations step-by-step which I added to the Snort service. 

* Snort interface

![Snort interface on DMZ](images/task1-b-snort-interface.png)

### DDOS-Attacker

First I generated the DDoS attack from kali linux. Below image shows the DDoS attack by the DDoS-attacker tool.

![DDoS-Attacker interface](images/task-1-b-ddos-attacker.png)

The packets are captured through the pfsense firewall as shown in below image.

![Packet capture on DMZ interface](images/task1-b-pcap-ddos.png)
![Packet capture of DDoS attack](images/task1-b-pcap-ddos-2.png)

The 'Alerts' are generated under the snort DMZ interface.

![Alert for DDoS attack](images/task1-b-snort-if-alert.png)

### Xdos-server

Below are the pcap of the xdos-server attack and the alerts generated in the snort interface. 

![Packet capture of xdos attack](images/task1-b-pcap-xdos.png)
![Alert for xdos attack](images/task1-b-pcap-xdos-alert.png)

The difference of the two tools is, as it seems on the alert in snort interface, the DDoS-Attacker generated traffic from the kali host to the ubuntu server. But in xdos-server scenario, the traffic is generated from the server end to the kali machine. 

The DDoS-Attacker is probably made to simulate a DDoS attack that is initiated by an outside source, like a botnet or a corrupted device network. DDoS attacks typically come from several distributed sources and are directed against a single victim server or network in real-world situations. In order to replicate the behavior of a normal DDoS attack from external sources, the traffic created by the DDoS-Attacker program is most likely sent from the attacking machine (Kali host) to the victim server (Ubuntu server).

But in Xdos-server, on the other hand, the attacker computer (Kali host) is receiving traffic from the victim server, which is the Ubuntu server. This configuration could be used to examine how a server behaves under pressure or to test a network's or server's resistance to specific kinds of attacks.
This scenario could be helpful in modeling situations in which malware infection, exploitation, or server misconfiguration result in a compromised server being used to conduct attacks against other systems.

## C)

To observer the state table entries, I used DDoS attacker as the tool. Below is the state table after the DDoS attack is done. 

![State table of DDoS attack](images/task1-c-state.png)

According to the state table, the conncetion from the LAN (10.0.0.27) to the server (10.3.1.10) can be observed. 
* These connections are mainly in the FIN_WAIT_2 state, indicating that the client (10.0.0.27) has sent a connection termination request and is waiting for acknowledgment from the server.
* Some connections are in the ESTABLISHED state, indicating that the connection is active and data is being exchanged between the client and server.
* The packet counts are notably high, with thousands of packets being exchanged in some connections.
* The packet sizes are relatively small, typically ranging from a few kilobytes to a few hundred kilobytes. However, considering the volume of packets exchanged, the total data transferred is significant.

Below image shows the attempt to `ping` to the WAN interface.

![Ping to WAN](images/task1-c-ping-ip.png)

**What is happening in the state table for WAN interface.**

The statetable appears as below image. 

![State table of ping](images/task1-c-ping-state.png)

* The state table records various UDP and TCP connections initiated from the internal IP (198.168.122.216) towards external IP addresses on port 53 (DNS) and possibly other ports.
* There are also some connections showing UDP traffic towards external IP addresses on port 53, which is the standard DNS port.

**What is the technique called that's applied here by firewall to route packets on WAN interface?**

The firewall uses the Network Address Translation (NAT) to route packets on the WAN interface. NAT modifies the source and/or destination IP addresses of packets as they traverse the firewall, allowing private IP addresses to communicate with external networks using public IP addresses. 

**Can state table be overflowed in general? What happens when they overflow?**

I coudln't find it in internet where the state table has overflowed. I tried to launch the DDoS attack for a long time and the firewall and also the attack terminal stopped responding. As a conclusion, Iassume that the state tables can be overflowed under certain conditions, especially during large-scale attacks or when the firewall is overwhelmed with a high volume of traffic. As a result, the firewall may start dropping new connection requests or fail to track the state of existing connections properly resulting degraded performance, dropped connections, or complete loss of network connectivity.

# Task 2

## A)

* The DHCP address pool of the LAN has changed from `10.0.0.11 - 10.0.0.155` to `10.0.0.2 - 10.0.0.155`.
* A DNS server has been configured in LAN at `10.0.0.1` and `8.8.8.8`.

![DNS LAN](images/task2-a-dns-lan.png)

  
* The kali machine IP is `10.0.0.26` now.
* The NAT configuration has changed by adding 2 outbound rules to the LAN and WAN in the new configuration. Below are the changes of the NAT outbound configuration.
  - The source and destination has not specifically defined in outbound rules.
  - Earlir for the NAT outbound, `automatic configurations has been used and for the new one, `hybrid` mood has been used.
  - The NAT rules are same for both the configurations.

![NAT](images/task2-a-fw-nat.png)

* New firewall rules have been added to the WAN, LAN, and DMZ interfaces.
* The rules differ as the old configuration includes pass rules for LAN to any and NAT port forwarding rules for WAN interface and the new code includes pass and block rules for LAN, WAN, and DMZ interfaces, covering various protocols and destinations.
* In the old config, the NAT port forwarding rules for specific IP addresses and ports on the WAN interface and new config includes rules for ICMP traffic between specific source and destination networks, as well as block rules for LAN and DMZ interfaces.
* The new config includes a separator element which divides WAN and DMZ interfaces.

![Firewall rules](images/task2-a-fw-rules-wan.png)

* The RRD configuration also has been changed in new config by specifing settings related to data categorization, graph resolution, time period, start and end dates, graph type, inversion, and refresh interval.
* There more users added to the network.

![Users](images/task2-a-users.png)

* WAN Exposure: The firewall is open for services like HTTP and HTTPS to the server 10.3.1.10.
* Connection between Internal Network and DMZ: Connection between the LAN nad DMZ has not been established in new config.
* SSH Settings: SSH is enabled for users additionally to the admin.

In order to change the settings so that the server can be accessed from the Kali machine, I changed the `block` action in `Default allow LAN to any rule` to `pass`. 

* Modification to the firewall rule

![Pass rule](images/task2-a-fw-pass.png)

* Web GUI of the Ubuntu server

![Web GUI](images/task2-a-web-gui.png)

## B)

The vulnerability is likely the "password recovery" or "password reset" vulnerability. The vulnerability allowes attackers to gain administrative access to the pfSense WebGUI by exploiting the password recovery mechanism. 

The process typically involved initiating the password recovery procedure while the admin user was logged in. By intercepting the password reset request and manipulating certain parameters, the attacker could bypass authentication checks and reset the admin password to a value of their choosing. Once the password was reset, the attacker could log in with the new password and gain full administrative privileges. In this firewall the user named `hm` is offered with the priviledge access rather that the admin. 

This vulnerability was particularly effective because it exploited a misconfiguration in the password recovery mechanism. By leveraging social engineering techniques or gaining physical access to a system where the admin was already logged in, an attacker could easily execute the attack without requiring any special privileges or technical expertise. The impact was significant, as it allowed attackers to gain complete control over the pfSense firewall, potentially compromising network security and integrity.

The vulnerability was addressed in later versions of pfSense, starting from version 2.4.5-p1. The fix involved implementing additional authentication checks and improving the security of the password recovery mechanism. Specifically, the fix introduced stricter validation of password reset requests and required additional verification steps to ensure the authenticity of the user initiating the password reset. Additionally, users were advised to log out of the WebGUI when not in use and to keep their sessions secure to mitigate the risk of exploitation.

# Task 3

## A)

A virtual private network, or VPN server, is a private network that connects individuals or remote sites via a public network. By offering a reliable, secure, and encrypted link between computer networks across an already-existing public network, VPNs contribute to security by ensuring that anyone intercepting the encrypted data cannot read it.

A personal virtual private network (VPN) is one that is meant for private, non-commercial use. It allows users to encrypt their internet traffic, connect to a remote server, and conceal their virtual location and real IP address. They can now safely and discreetly access the internet thanks to this.

Individual users can connect securely to a distant computer network using a remote-access virtual private network (VPN). As though they were physically connected to the network's servers, those users have access to all of the network's secure resources. A major company with hundreds of field sales representatives is an example of a business that requires a remote-access VPN.

Employees at different sites can access computer resources from one place by using a site-to-site VPN to create secure connections across offices spread across various fixed locations. A growing company with numerous branch offices throughout the globe is an example of an organization in need of a site-to-site VPN.

## B)

For this task, I manually created the required interface and the firewall rules. The nethodology is as follows. 

1. Generated the Certificate Authority as the first step. The CA needs to be created from **System > Certificates > Authorities > Add** and save the configurations. Below images show the configuration details of the internal CA.

   ![Internal CA](images/task3-b-ca-1.png)
   ![configuration 1](images/task3-b-ca-2.png)
   ![configuration 2](images/task3-b-ca-3.png)

2. Then create a server certificate for the openVPN server/ Go to **System > Certicates > Certificates > Add** and configure the certificte details as shown in the below images. Then save the configurations. 

   ![Server cert 1](images/task3-b-server-1.png)
   ![Server cert 2](images/task3-b-server-2.png)

3. Next a openVPN user is created and the a user certificate is generated for the user. Go to **System > User Manager > Add**. First the username, password and the full name of the user have to be configured in this page. Then, under **User certificates > Add** new user certificate of the openVPN server should be added. This directs to the **certificates** page again. Configure as per the below images and save the configuration. 

   ![user cert 1](images/task3-b-user-1.png)
   ![user cert 2](images/task3-b-user-2.png)
   ![user cert 3](images/task3-b-user-3.png)

4. After adding the server and user certificates, the certificates page appears as below.

  ![certificates](images/task3-b-certs.png)
   
6. Then the OpenVPN server needs to be created. Go to **VPN > OpenVPN > Servers > Add**. I used **Remote Access (SSL/TLS + User Auth)** as the server mode. The configuration is as follows. I only changed the options I mentioned in the images, others kept as it is. 

   ![openVPN server 1](images/task3-b-vpnserver-1.png)
   ![openVPN server 2](images/task3-b-vpnserver-2.png)
   ![openVPN server 3](images/task3-b-vpnserver-3.png)
   ![openVPN server 4](images/task3-b-vpnserver-4.png)
   ![openVPN server 5](images/task3-b-vpnserver-5.png)

7. At the end, the OpenVPN server appears as below.

   ![OpenVPN server config](images/task3-b-vpnserver-0.png)

8. Next I added the firewall configurations for the openVPN interface as below. Go to **Firewall > Rules > OpenVPN > Add**. 

   ![FW rules](images/task3-b-fw-openvpn.png)

9. Next the firewall rule should be added to the top of the WAN interface as well to allow traffic from port 1194.

   ![FW rule WAN](images/task3-b-fw-owan.png)

10. Next, **"openvpn-client-export"** should be installed using the **Package Manager**.
11. Then go to **VPN > OpenVPN > Client Export**. Use the configuration as below. Then select **Most Clients** to download the inline configurations.
12. After transferring the downloaded **pfSense-UDP4-1194-kalihost-config.ovpn** file to kali host machine, go to the **network manager** in the host machine. Select **Import a saved VPN configuration**. Then **Create**.
13. Next, locate the transferred **pfSense-UDP4-1194-kalihost-config.ovpn** file and provide the previously configured username and password. Then **Save**.
14. The VPN can be accessed through the Network Manager menu as shown in the below image.

    ![VPN success](images/task3-b-VPN-success.png)
    ![IP change](images/task3-b-ip-change.png)
