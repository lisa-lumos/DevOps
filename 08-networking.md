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



## Protocols, ports etc



## Networking Commands






































