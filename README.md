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

<h3>Step 1: Setup Resources in Azure</h3>
For the following observations we will need to create two Virtual Machines using Microsoft Azure. On one machine we will use Windows 10 Pro and on the other we will use Ubuntu Server 20.04 (Linux) operating system. Both should have a minimum of a two-core virtual cpu (Standard_D4s_v3 - 2 vcpus). Once both are set-up, proceed and log in to the Windows 10 version. Then download and install Wireshark on Windows 10: (https://www.wireshark.org/download.html).

<p align="center">
<img src="https://i.imgur.com/NIZEltL.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
<img src="https://i.imgur.com/ETgnYeU.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
<img src="https://i.imgur.com/zc379L3.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p>

<h3>Step 2: Observe ICMP Traffic</h3>
First, open Wireshark and click "Ethernet". Then click the little blue icon in the top left corner to start capturing packets.

<p align="center">
<img src="https://i.imgur.com/BCDLYKO.png" height="70%" width="70%" alt="Azure Free Account"/>	
</p>

Using WireShark we will filter for ICMP traffic only. ICMP stands for "Internet Control Message Protocol". It is the protocol that "ping" uses; ping is a command tool used to test connectivity between different hosts on the network. This traffic will display the relay request and deliver. We will use Windows PowerShell to ping our Linux virtual machine to see how many packets are sent and recieved. Do this by opening Windows PowerShell and ping our Linux Virtual Machine's private IP address. Mine is 10.0.0.5

<p align="center">
<img src="https://i.imgur.com/bJJCXXM.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">

Next, we will initiate a perpetual/non-stop ping from our Windows 10 VM to our Linux VM. Then we will change the firewall on our Linux VM to block ICMP traffic from coming through. To initiate a perpetual ping from Windows 10 VM to Linux VM, go to PowerShell and type ping (Linux private IP address) -t. For example, mine will be: "ping 10.0.0.5 -t" then press enter. The Replies will in essence keep going forever until the ping is stopped or ICMP is blocked in our Linux VM's firewall.

<p align="center">
<img src="https://i.imgur.com/NEZNlKW.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">

Next, we will configure our Linux VM's firewall in Azure to block ICMP traffic from coming through. A firewall in Azure is also known as Network Security Group (NSG). To do so, go to the Azure portal > search Network security groups > select VM2-nsg (Linux VM) > select Inbound security rules > select Add > set Protocol to ICMP > set Action to Deny > set Priority to 200 > Name: "DENY_ICMP_PING_FROM_ANYWHERE" > select Add.

<p align="center">
<img src="https://i.imgur.com/AS39UdC.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">

Go back to our Windows 10 VM and you can see the effect of the rule we added. In PowerShell we can see the ping is now timing out because it is getting blocked by our Linux VM's firewall. In Wireshark we can see that it went from receiving "request" and "reply" to "no response found".

<p align="center">
<img src="https://i.imgur.com/1ROntI9.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/jNJQotH.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>

Lastly, we will re-enable ICMP traffic for the Network Security Group our Linux VM is using. To do so go back to the Azure portal > search Network security groups > select VM2-nsg (Linux VM) > select Inbound security rules > select "DENY_ICMP_PING_FROM_ANYWHERE" > set Action to Allow > select Save.

<p align="center">
<img src="https://i.imgur.com/6hFqNSN.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">

Now we can go back into our Windows 10 VM and we can see we are receiving responses from our Linux VM again. Wireshark and PowerShell should now show replies coming through.
  
<h3>Step 3: Observe SSH Traffic</h3>
Using Wireshark and PowerShell we will observe SSH traffic. Filter for SSH traffic only in WireShark. SSH, also known as Secure Shell is used when remotely connecting from one computer to another and spawning a command line. The computer being connected to is typically "listening" for a connection on TCP port 22. 

Now we're going to connect from our Windows 10 VM into our Linux VM via Secure Shell; we will SSH into the Linux VM. This can be done by using PowerShell, "ssh username@(private)ipaddress". In my case, ssh labuser@10.0.0.5 (Linux private IP address). Then we will see that WireShark immediately sees the SSH packets between the two VM. 

<p align="center">
<img src="https://i.imgur.com/ta80GVR.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">
  
To continue connecting into our Linux VM from our Windows 10 VM: "Are you sure you want to continue connecting (yes/no/[fingerprint])?" enter yes > type password for your Linux VM (note: when typing your password it will not show but trust it is there) > enter. We are now connected into our Linux VM from our Windows 10 VM.
  
<p align="center">
<img src="https://i.imgur.com/gD5xTV8.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">
  
We see "labuser@VM2" appears in green on PowerShell which shows our connection to our Linux VM. Whatever we type into PowerShell we can see the traffic in Wireshark over the network. We can now type any Linux command into PowerShell. For example, lets type "uname -a" and it will tell us about the actual operating system it's running on.
  
<p align="center">
<img src="https://i.imgur.com/b0zXc2E.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">
  
<h3>Step 4: Observe DHCP Traffic</h3>
Using Wireshark and PowerShell we will observe DHCP traffic by filtering for DHCP traffic only. DHCP stands for Dynamic Host Configuration Protocol which operates on ports 67 and 68. DHCP is a protocol used to assign an IP address to devices when they are first connected to the network. We will attempt to issue a new IP address to our Windows 10 VM by using PowerShell and entering the line "ipconfig /renew". Now, inspect WireShark for this traffic. 

<p align="center">
<img src="https://i.imgur.com/hngch8M.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">

<h3>Step 5: Observe DNS Traffic</h3>
Back in Wireshark, filter for DNS traffic only. In PowerShell, we will use the command "nslookup" to see what google.com or most of any website IP addresses are. Now, inspect WireShark and the traffic it is capturing here. 

<p align="center">
<img src="https://i.imgur.com/M6DunxQ.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">

<h3>Step 6: Observe RDP Traffic</h3>
Back in Wireshark, filter for RDP traffic only. RDP stands for Remote Destop Protocol and it's used when remotely connecting from one computer to another to gain a remote desktop GUI. The computer being connected to is typically "listening" for a connection on TCP port 3389. So, we can do this by entering "rdp" or "tcp.port == 3389" in WireShark. Traffic is now constantly flowing, showing a live stream of packets between one computer to another.

<p align="center">
<img src="https://i.imgur.com/vGnFMjb.png" height="70%" width="70%" alt="osTicket Prereqs and Installation"/>
</p>
<p align="center">

That's all for this tutorial! Thanks for tuning in!
