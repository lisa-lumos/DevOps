# 8. Networking
## The OSI (Open System Connection) model, that ISO developed
A computer network is a communication between 2/more network interfaces (via ethernet/wireless adapter). Every device will have an IP address, assigned to its network interface. They can exchange data via the network. 

Components used to create a computer network:
- 2/more devices. Such as computers/phone/IoT devices, that can process data
- Cables as links between the computers
- A network interfacing card (NIC) on each device
- Switches, to connect multiple network interfaces together
- Routers, to connect multiple network together
- OS on the devices, that can process data, and present it to the user

How does every OS, network interface, network devices, websites, apps, etc, understand how to communicate in the same way as other devices does?

The OSI (Open System Connection) model, is a 7 layered architecture:
1. Physical. Info in bits (0s and 1s). Have devices like a hub. 
2. Data link. Info in frames. Makes sure that the transfer is error free between layers. Physical addressing (MAC & LLC). Have deices like a bridge switch. 
3. Network. Info in packets. Path determination, and logical addressing (IP). Sender/receiver IPs are placed in the header of the packets. Have devices like a router, a firewall switch
4. Transport. Info in segments. The end-to-end connections and reliability is checked. Will re-transmit the data if there is failure. Have devices like a gateway
5. Session. Interhost communication
6. Presentation. Date translation, encryption, and compression
7. Application. Network resources reaches to application.

Devices in the layer 5/6/7 can be: web/mail server, mail client, browser, ...

One layer can offer service to another (higher) layer; between the layers, there is interfaces; and the communication between them follows the same protocol. 

## Understanding Networks & IP
Classification of network by geography:
- Local Area Network (LAN). The network devices (computers, printer, etc) are very close to each other, like in a room. 
- Wide Area Network (WAN). Like the Internet. 
- Metropolitan Area Network (MAN). Such as metro trains computer network system. 
- Campus Area Network (CAN). Office/college campus, where you have computers connected together in a few acres of land. Also called Intranet. 
- Personal Area Network (PAN). Your bluetooth hotspot, your own personal network, which has a very small range. 

Switches. Connects multiple devices together. Can ue used for setting up LAN. For example, if a computer need to send some data to the printer. There is a switch inside your wifi router. 

Routers. Connects multiple network together. Receives/sends data on computer networks. You have a router at home, that connects your local area network in your home, with a wide area network, the Internet. 

From the Internet service provider, you get a network cable connected to your modem. From modem, you make a connection to your router. So, traffic generated from your laptop, goes to your switch, from switch to your router, from router to your modem Internet, and then the response comes back.  

Everywhere the data is traveling, every device has an IP address. Your laptop has an IP address to its network interface, switch will have an IP address, your internet service provider, and all the other computers/smartphones in your network will have an IP address. 

Similar to your home network, you have big corporate networks or data center network, where you will have many switches, many routers, and you'll have multiple Internet service provider. This is for high availability, and for security. You will have more devices, like firewalls. If any switch breaks, the communication can flow from other switches. 

In a data center, you can have multiple LANs, which can be called subnets. 

IPv4 address is a 32-bit (4 * 8 bits) binary number, which we mostly see in decimal format, like "192.168.100.1". Each of the 4 numbers inside it is called an "octet". IPv4 range: "0.0.0.0" to "255.255.255.255". 

Public IP is with the Internet service provider, and the cloud providers. Private IP is for people to create their own network. But for the Internet services, the public IP is used. When you subscribe for an Internet connection, an ISP will allot a public IP to you, and that is your ID in the internet (mostly it is a dynamic IP, but you can get a static one also). 

Private IP. When you want to setup a room full of computers, you will take a private IP range. There are 5 ranges in private IP. The 3 ranges that we use is class A/B/C, as class D/E is for research and multicasting. The 3 ranges we use are:
- class A: 10.0.0.0 to 10.255.255.255 (10.etc)
- class B: 172.16.0.0 to 172.31.255.255 (172.16-31.etc)
- class C: 192.168.0.0 to 192.168.255.255 (192.168.etc)

For example, if we see 172.32.36.12, it does not fit in the above range, so it could be a public IP. 

By looking at the IP, you should be able to see whether it is public/private, and if it is private, then from which range. 

## Protocols, ports etc
In networking communication, a protocol is a formal specification, that defines the procedure to communicate between sender/receiver. It defines various things, such as the format of the communication, timing, sequence, error handling, etc. 

Transmission Control Protocol (TCP):
- Reliable
- Connection oriented
- Performs three ways handshake
- Provision for error detection, and re-transmission
- Most applications use TCP for reliable and guaranteed transmission
- e.g.: FTP, HTTP, HTTPS

User Datagram Protocol (UDP): 
- unreliable
- Connectionless. Send it and forget it, whether it reaches destination or not
- No acknowledgement waits
- No proper sequencing of data units
- Suitable for applications, where speed matters more than reliability
- e.g.: DNS (where url needs to be translated into an IP address very quickly), DHCP (client ask for an IP address over the network), TFTP, ARP, RARP

A computer has an IP address, and a service running inside a computer will have a port number. For example, if you are running a web service, using protocol HTTPS, the default port will be 443, which means it is serving the request on port number 443. A computer could have multiple services, via their respective port numbers. 

Protocols have port numbers:
| Protocol | Service name | UDP/TCP port number |
| ---------| ------------ | ------------------- |
| DNS | DNS UDP | UDP 53 |
| DNS | DNS TCP | TCP 53 |
| HTTP | Web | TCP 80 |
| HTTPS | Secure Web | TCP 443 |
| SMTP | Simple Mail Transport | TCP 25 |
| POP | Post Office Protocol | TCP 109, 110 |
| SNMP | Simple Network Management | TCP/UDP 161, 162 |
| TELNET | Telnet Terminal | TCP 23 |
| FTP | File Transfer Protocol | TCP 20, 21 |
| SSH | Secure Shell (terminal) | TCP 22 |
| AFP IP | Apple File Protocol/IP | TCP 447, 548 |

Protocols in the application layer: telnet, FTP, DHCP, TFTP, HTTP, SMTP, DNT, SNMP. Transportation layer: TCP, UDP. Network layer: ICMP, ARP, RARP, IP. 

When you work on a project, you should know what service is running in which server, what is the IP address, and it is running on which port number. 

## Networking Commands
Loopback address is the IP address when the computer is referring to itself. 
```console
# show all the network interfaces, and their IP addresses
ifconfig

# use this if the prior one doesn't work
ip addr show

# checks network connectivity from your machine to this IP address
ping 192.168.40.12

# to connect to the machine with the name instead of ip
# you will need a DNS server,
# or, make an entry in ect/hosts file, such as "192.168.40.12 db01"
ping db01

# trace route example. Shows all the hops it takes to reach to the google server
tracert www.google.com

# show all the TCP open ports in the current machine, 
# and the PID/Program name (need to be root user to see PID)
netstat -antp

# scan the ports of a target
# illegal in some countries
nmap localhost # target is local machine
nmap db01 # target a different machine

# check whether a particular port is open on a target machine
telnet 192.168.40.12 3306 # check for mariadb
telnet 192.168.40.12 22   # check fof ssh

# show DNS lookup
dig www.google.com
nslookup www.google.com   # this is an older version of dig

# show the gateway
route -n

# view/add contents to the kernels ARP table
# kernel maintains a table of all ip/name and mapping MAC address
arp

# live version of trace route, can see where the packet loss is happening
mtr www.google.com

```
