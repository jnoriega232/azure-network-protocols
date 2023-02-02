<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
Welcome back! In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Observe ICMP Traffic
- Observe SSH Traffic
- Observe DHCP Traffic
- Observe DNS Traffic
- Observe RDP Traffic

<h2>Actions and Observations</h2>

<h3>Step 1: Create Virtual Machines</h3>
For the following observations we will need to create two Virtual Machines using Microsoft Azure. On one machine we will use Windows 10 and on the other we will use Ubuntu Server 20.04 (Linux) operating system. Both should have a minimum of a two-core virtual cpu (Standard_D4s_v3 - 2 vcpus). Once both are set-up, proceed and log in to the Windows 10 version. Then download and install Wireshark on Windows 10: (https://www.wireshark.org/download.html).

<p align="center">
<img src="https://i.imgur.com/NIZEltL.png" height="40%" width="40%" alt="osTicket Prereqs and Installation"/>
<img src="https://i.imgur.com/ETgnYeU.png" height="40%" width="40%" alt="osTicket Prereqs and Installation"/>
<img src="https://i.imgur.com/zc379L3.png" height="40%" width="40%" alt="osTicket Prereqs and Installation"/>
</p>
<p>

<h3>Step 2: Observe ICMP Traffic</h3>
First, open Wireshark and click "Ethernet". Then click the little blue icon in the top left corner to start capturing packets.

<p align="center">
<img src="https://i.imgur.com/BCDLYKO.png" height="50%" width="50%" alt="Azure Free Account"/>	
</p>

Using WireShark we will filter for ICMP traffic only. ICMP is the protocol that "ping" uses; ping is what is used to test connectivity between different hosts on the network. This traffic will display the relay request and deliver. We will use Windows PowerShell to ping our Linux virtual machine to see how many packets are sent and recieved. Do this by opening Windows PowerShell and ping our Linux Virtual Machine's private IP address. Mine is 10.0.0.5

<p align="center">
<img src="https://i.imgur.com/bJJCXXM.png" height="50%" width="50%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">

Next, we will initiate a perpetual/non-stop ping from our Windows 10 VM to our Linux VM. Then we will change the firewall on our Linux VM to block ICMP traffic from coming through. To initiate a perpetual ping from Windows 10 VM to Linux VM, go to PowerShell and type ping (Linux private IP address) -t. For example, mine will be: ping 10.0.0.5 -t then press enter. The Replies will in essence keep going forever until the ping is stopped or ICMP is blocked in our Linux VM's firewall.

<p align="center">
<img src="https://i.imgur.com/NEZNlKW.png" height="50%" width="50%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">

Next, we will configure our Linux VM's firewall in Azure to block ICMP traffic from coming through. A firewall in Azure is also known as "Network Security Group" (NSG). To do so go to the Azure portal --> search "Network security groups" --> select "VM2-nsg" (Linux VM) --> select "Inbound security rules" --> select "Add" --> set Protocol to "ICMP" --> set Action to "Deny" --> set Priority to "200" --> Name: "DENY_ICMP_PING_FROM_ANYWHERE" --> select "Add".

<p align="center">
<img src="https://i.imgur.com/AS39UdC.png" height="50%" width="50%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">

Lastly, go back to our Windows 10 VM and you can see the effect of the rule we added. In PowerShell we can see the ping is now timing out because it is getting blocked by our Linux VM's firewall. In Wireshark we can see that it went from receiving "request" and "reply" to "no response found".

<p align="center">
<img src="https://i.imgur.com/1ROntI9.png" height="50%" width="50%" alt="Azure Free Account"/> <img src="https://i.imgur.com/jNJQotH.png" height="50%" width="50%" alt="Azure Free Services"/>
</p>

<h3>Step 3: Observe SSH Traffic</h3>
Let us observe a different kind of Traffic, SSH. Filter for SSH traffic only in WireShark. From the Windows 10 VM, "SHH into" the Ubuntu VM. This can be done by using the command, "SSH username@ipaddress" in my case, SSH labuser@10.0.04 , then we will see that WireShark immediately sees the SSH packets between the two VM. 

![vivaldi_voFaQKzigU](https://user-images.githubusercontent.com/109401839/213243011-f74fa2ba-ba3f-4c0f-938f-2915b998b68e.png)


3. Obeserve DHCP Traffic, DHCP is Dynamic Host Configuration Protocol which operates on ports 67 and 68. The main function of DHCP is to assign different devices their IP-Address. Filter for DHCP in WireShark. We can attempt to issue a new IP address to our Windows 10 VM by using CMD and entering the line "IPCONFIG /RENEW". Now, inspect WireShark for this traffic. 

![vivaldi_2hRg2VDUxe](https://user-images.githubusercontent.com/109401839/213243361-2e338ef0-af7c-47b9-9387-6a002791fd07.png)

4. Observe DNS Traffic, once again, filter for DNS. In CMD, lets use the command "nslookup" to see what google.com or most of any website IP addresses are. Now, inspect WireShark and the traffic it is capturing here. 

![vivaldi_p4LlxYiVLv](https://user-images.githubusercontent.com/109401839/213243701-b3915d44-2aa3-4fe7-b637-e7d9c5ecd6c3.png)

5. Observe RDP Traffic, Filter for RDP. We can do this by entering "tcp.port == 3389" in WireShark. Traffic is now constantly flowing, showing a live stream of packets between one computer to another. Fascinating isnt it? 

![vivaldi_yi916o0Wbr](https://user-images.githubusercontent.com/109401839/213243903-af301b6a-d633-457e-ad1f-dc22cb93edf5.png)


In the [next tutorial](https://github.com/fnabeel/Network-File-Shares-and-Permissions),  we will go over settingup Network File Sharing and Permissions. I recommend not destroying your virtual machine you have created in this tutorial. It will come in handy next time !
