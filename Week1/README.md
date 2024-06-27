# Task 1

The network is created successfully. 

# Task 2

## A) 

Below image shows the successful deployment of the network in terraform. 

Number of added resources: 12

![Terraform deployment](images/CS2-ex1-task1.png)

## B)

The virtual manager appears as below after the terraform deployment. 

![virtual manager](images/CS2-ex1-task2.2.png)

## C)

The LAN configuration in pfsense firewall looks as below. 

![Firewall LAN](images/task2-c-fw-ip.png)

After rebooting the kali machine, the IP configuration is changed. The screen looks as below. 

![kali IP](images/task2-c-kali-ip.png)

The webGUI of pfsense firewall can be accessed through `https://10.0.0.1/`. The screenshot of the interface is attached below.

![pfsesne webGUI](images/pfsense-gui.png)

The Ubuntu server was assigned with IP `10.0.0.12` and the Kali machine with `10.0.0.11`. I did the ping test from ubuntu server to the Kali machine. The ping test was successful. The packet capture result is shown below. 

![ping test](images/task2-c-ping-test.png)

# Task 3

## A)

The nmap scan was done in the kali machine. 

Command: `nmap 10.0.0.0/24`

The results are as below. 

![nmap scan](images/task3-nmap.png)

The results show that there are 3 hosts are present. 
IP addresses: 
* 10.0.0.1
* 10.0.0.11
* 10.0.0.12

The web service can be access from the kali machine. 

## B)

I did the nmap scan using the host machine where the IP is `10.0.2.15/24`. The network `10.0.0.0/24` cannot be discovered and the web server cannot be accessed from the host machine. The results are as follows. 

![nmap from outside](images/task3-b-nmap.png)

## C)

**ICMPv4 Echo:**
  - Command: `sudo nmap -PE 10.0.0.0/24`
  - Running `-PE`, `-PP`, or `-PM` generates the same results.
  - Additionally to running the `nmap` command alone, the MAC address of the virtual NIC in the host machines can be discovered.
  - Results:
   ![ICMP echo](images/task3-c-echo.png)

## D)

The web server can be accessed from the kali machine. 

![Web server](images/task3-a-web.png)

# Task 4

## A)

Below command is used to ping the kali machine from ubuntu server using `hping3`. The IP of Kali machine is `10.0.0.11` and the IP of the server is `10.0.0.12`.

```sudo hping3 -S --icmp 10.0.0.11 --data 1458 --file hackers_data.txt --end```

I created a file in the ubuntu server named `hackers_data.txt`. The file contained below text. 

```txt 
Test data for task 3
```

The packets were successfully transferres to the kali machine. The data sent from server through ICMP packets can be observed inside the ICMP packet received to the Kali machine. Below image shows the packet capture from the wireshark, the content of the file is being transfered through ICMP packets and the sub-field of data inside ICMP packet.

![wireshark output](images/task4-a-wireshark-hping.png)

## B)

Below command was used to pull out data fiels from the pcap file and dump it into a txt file. 

```txt
tshark -T fields -e data.data -r hacker_data.txt > hexdump.txt
```

After successfully creating the `hexdump.txt` file, the data in hex format was converted using the converter. Below image shows the created hexdump.txt file. 

![hexdump.txt](images/task4-b-hexdump.png)

The data inside the hexdump.txt includes several of below text. 

![hexdump data](images/task4-b-data_dump.png)

The conversion of data looks as follows. 

![data conversion](images/task4-b-conversion.png)

# Task 5

* I added a NAT port forwarding to WAN interface of the firewall to direct HTTP traffic coming to the WAN interface, from WAN to the Ubuntu server with IP `10.0.0.12`. 

![NAT config](images/task5-NAT_2.png)

* A firewall rule is automatically added to WAN interface to allow HTTP traffic from any source to pass through. 

![Firewall rule](images/task5-FW_rule_2.png)

* From the configurations I added so far, the connectivity was not established.

![Connectivity](images/connection.png)




