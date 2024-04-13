ICMP Redirect Attack  
 
 
2.1. Container Setup and Commands 
Please download the Labsetup.zip file to your VM from the lab's website, unzip it, enter the Labsetup folder, and use the docker-compose.yml file to set up the lab environment. In the following, we list some of the commonly used commands related to Docker and Compose.  
 
  
 
All the containers will be running in the background.We first need to use the "dockerps" command to find out the ID of the container, and then use "docker exec" to start a shell on that container.  
  
 
Using this command you can go inside the shell. 
 
3.	Task 2: Launching ICMP Redirect Attack 
 
3.1 Attack Setup. 
 The ICMP redirect attack is protected with a countermeasure in the 
Ubuntu operating system. We have previously disabled the logging in the Compose file.countermeasure by configuring the victim container to accept ICMP redirect messages. 
  
 
 
For this task, we will attack the victim container from the attacker container. In the current setup, the victim will access the network by using the router container (192.168.60.11).Network 192.168.60.0/24. The victim container's ip route command will display the Following 
 
 
  
 
 
 
Skeleton code. In the paragraphs that follow, a code skeleton is presented without several crucial parameters. Students should enter the appropriate values in the spaces denoted by IP values. 
 
 
 
 
 
 
 
  
 
Verification. ICMP redirect messages will not affect the routing table; instead, it affects the transport cache. Until the routing table is updated, entries in the routing cache replace those in the routing table.entries expire.The following commands can be used to display and clear the cache contents. 
 
 
  
 
Please do a traceroute on the victim machine, and see whether the packet is rerouted or not. # mtr -n 192.168.60.5 
  
3.2 Questions. After you have succeeded in the attack, please conduct the following experiments, and see whether your attack can still succeed. Please explain your observations: 
Question 1:Can you redirect to a remote server using ICMP attacks?machine?Namely, the IP address assigned to icmp.gw is a computer not on the local LAN.Please show your experiment result, and explain your observation. 
 
 
 
If you are changing the IP address then redirect the message but you don’t get the output.Change of the IP address I am not getting the output.A router ICMP redirect message directs a host to use another router as its path to a particular destination because it has a better route. The rules say a router will send redirects only to hosts on its own local subnets. No user host will ever send a redirect and no redirect will travel more than one network hop. 
 
 
Question 2: Can you use ICMP redirect attacks to redirect to a non-existing machine on the same network? Namely, the IP address assigned to icmp.gw  is a local computer that is either offline or non-existing. Please show your experiment result, and explain your observation. 
 
 
If you are changing the IP then you redirect the messages but packet is not sending and you don’t get the output.The ability of a router to advise a host that there is a more effective route to a destination and that the host should modify its routing table is known as a "feature" of IP. On a trusted LAN, this would be acceptable, but on the wild Internet, where maliciousness abounds, it might not be a smart idea to change your routing table at random. However, ICMP redirection are enabled on Linux by default. 
Question 3: If you look at the docker-compose.yml file, you will find the following entries for the malicious router container. What are the purposes of these entries? Please change their value to 1, and launch the attack again. Please describe and explain your observation. 
 
 
When you change their value 0 to 1 and  then launch the attack that we get the output.It is now as easy as typing docker-compose -f docker-compose to validate your file. config in yml. You can always omit the -f docker-compose option. yml section when using the COMPOSE FILE environment variable or running this program in the same folder as the file.You can host numerous isolated environments on a single host using Docker Compose. You can conserve a significant amount of resources by running everything on one piece of hardware. Resource efficiency is also improved by its capabilities to reuse previously used containers and cache configurations.I think that’s why I don’t get the output. 
A strange issue. 
While developing this lab, we have observed a strange issue in the container environment.The issue does not exist if the victim is a VM, instead of a container.If we spoof nonetheless, the victim system is not releasing ICMP packets throughout the reroute packets.attack, the attack will never be successful.This is not the case for the VM setting.Moreover,the redirect packet's IP2 must match the type and destination IP address of the packets that the victim is currently sending (ICMP for ICMP, UDP for UDP, etc.). It appears that the OS kernel performs some sort of sanity check before accepting an ICMP.redirect packets.We have not figured out what exactly caused this, and why 
4.	Task 3: Launching the MITM Attack  
4.1 Attack Setup. Using the ICMP redirect attack, we can get the victim to use our malicious router (10.9.0.111) as the router for the destination 192.168.60.5. Therefore, all packets from the victim machine to this destination will be routed through the malicious router. We would like to modify the victim's packets.Before launching the MITM attack, we start a TCP client and server program using netcat. See the following commands. 
 
  
 
 
Disabling IP Forwarding.  
  
 
In the setup, the malicious router's IP forwarding is enabled, so it \sdoes function like a router and forward packets for others. We must stop forwarding IP packets when we initiate the MITM attack; instead, we will intercept the packet, make the necessary changes, and then send out a new packet. We only need to turn off IP forwarding on the rogue router to accomplish that. 
 
MITM code.  
 
Once IP forwarding is turned off, our application must assume control of packet forwarding from the victim to the target, naturally after making changes to the packets. The kernel will not forward the packet to us because its intended recipient is not us; instead, it will simply drop the packet. The packet will instead be obtained from the kernel if our software is a sniffer application. Therefore, we will carry out this MITM attack using the sniff-and-spoof method. Following, we offer a sample sniff-and-spoof application that intercepts TCP packets and modifies them before delivering them. You can find the code \sfrom the lab setup files. 
It should be emphasized that the aforementioned code records all TCP packets, including those produced by the application itself. This is undesirable since it will have an impact on the performance. 
Students needs to change the filter, so it does not capture its own packets. 
 
4.2 Questions. After you have succeeded in the attack, please answer the following questions:  
Question 4: In your MITM program, you only need to capture the traffics in one direction. Please indicate which direction, and explain why 
 
An eavesdropping assault known as a "man-in-the-middle" occurs when an attacker intercepts a data transfer or communication that is already in progress. The attackers place themselves in the "middle" of the transfer and then pose as both authorized participants.Finding abnormal traffic patterns and latency problems is only the first step in identifying a man-in-the-middle attack. To establish whether the collected network traffic is an MITM attack in the first place, forensic analysis of the network traffic must be performed. If the attack is validated, the source, in this case the compromised user, must be identified.An instance of cyber eavesdropping known as a "Man-in-theMiddle Attack" (MITM) involves malicious actors listening in on a discussion between two parties and collecting data through a vulnerable but reliable technology. 
The ideas of sniffing and spoofing have been employed. Before spoofing an arp reply and an icmp reply to a specific node on the local area network, an attacker sniffs a packet from that node first. When a node wants to connect with another node on the network, it will initially broadcast an arp request across the whole LAN. Attacker machine will intercept the request and respond with a fake arp reply. Once the victim system has the target node's MAC address, it will ping the attacker device, thinking it is communicating with the target node. The attacking computer will once more intercept the ping request and send the victim a phony icmp reply. 
 
Question 5: In the MITM program, when you capture the nc traffics from A (10.9.0.5), you can use A's IP address or MAC address in the filter. One of the choices is not good and is going to create issues, even though both choices may work. Please try both, and use your experiment results to show which choice is the correct one, and please explain your conclusion. 
 
  
 
 
Using an IP address is a bad idea because the application creates a lot of traffic and is generally a mess. Once the victim system has the node's MAC address, it will ping the attacker machine, believing it is speaking with the node it intended to speak with.Actually, communication takes place over the MAC Address. If you know a device's IP address, you can use ARP to find out its MAC address.A device initially uses ARP to determine the MAC address of the recipient device when it wishes to communicate with another device on the network, for example.The attacker can pretend to be someone else since the legitimate IP Address and the MAC Address of the attacker's machine are linked. 
A man-in-the-middle (MITM) attack is a general term for when a perpetrator inserts himself into a conversation between a user and an application, either to listen in on the conversation or to pretend to be one of the participants and create the impression that normal information exchange is taking place. 
 
