# configure-ad
<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines. I will be demonstrating how I set up virtual machines w/ Azure, install Active Directory, and create additional users to simulate the use of Active Directory

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Part 1: Setup Resources in Azure

- Part 2: Ensure Connectivity between the Client and Domain Controller

- Part 3: Install Active Directory

- Part 4: Create Admin and Normal User accounts in AD

- Part 5: Join Client-1 to your domain (mydomain.com)

- Part 6: Setup Remote Desktop for non-admin users on Client-1

- Part 7: Create additional users and attempt to log into Client-1 w/ one of the users


<h2>Deployment and Configuration Steps</h2>

<h3>Part 1: Setting up the Resources in Azure!</h3>

First things first, create a Domain Controller and a Client (Windows VM), then set the Domain Controller's NIC private IP address to be static.

**Creating the Domain Controller**

<sub>***A domain controller is a server that controls user accecss, manages security, and stores user accounts in a Windows network. It's like the central authority for managing and protecting network resources***</sub>

<h4>How to Create DC-1</h4>

1.) Go to Microsoft Azure > select 'Virtual machines' > 'create'

2.) Within the VM creation portal > create a Resource Group so that it can hold both the Domain Controller and the Client

3.) VM name = 'DC-1' > 'Windows Server 2022 / x64 Gen2' 

<p>
<img src="https://imgur.com/X5xtuJL.png"
     </p>
  
4.) Size 2vcpus > whatever username or password you want
  
<p>
  <img src="https://imgur.com/qPA0DTn.png"
       </p>

5.) Azure will automatically create a virtual network where both DC-1 and Client-1 will be in.

<p>
  <img src="https://imgur.com/Yguf0kE.png"
       </p>
  
**Creating our Client**
  
<sub>***The client is a computer or device that connects to the domain controller for user authentiation and access to network resources***</sub>
 
<h4>How to create the Client</h4>

1.) Go to Microsoft Azure again > 'Virtual Machines' > 'create'
  
2.) VM name = 'Client 1' > Windows 10 Pro > 2vcpus > whatever username and password > 'disks' > 'networking' (Client-1 is automatically put on the same network as DC-1) > Done
       
<p>
  <img src="https://imgur.com/dE0Dqsr.png"
       </p>
  
<p>
     <img src="https://imgur.com/0DAyw3l.png"
          </p>

**Setting DC-1 NIC Private IP address to Static**
     
***Setting a static IP for a domain controller is crucial for network stability and seamless integration with DNS and Active Directory.A static IP provides consistent network address, ensuring reliable communication with other devices and services. This stability is particularly important for DNS resolution, as a domain controller often serves as DNS servers. Additionally static IP enables efficient management of users accounts and network resources within teh Active Directory infrastrucutre. By maintaining a fixed IP address, the domain controller can reliably handle authentication requests, control access to resources, and enure the integrity of the Active Directory database***
     
<h4>How to set DC-1 NIC Private IP to Static</h4>

1.) Azure Portal > 'Virtual Machines > select 'DC-1'

2.) Select 'networking' > 'network interface' 

<p>
<img src="https://imgur.com/mcpd6JO.png"
     </p>
  
3.) select 'IP configurations' > select the only IPconfig > switch from 'dynamic' to 'static' > 'save'
     
<p>
<img src="https://imgur.com/afFM2md.png"
</p>
     
 <h3>Part 2: Ensure Connectivity between the Client and Domain Controller</h3>
      
First thing I am going to do is remotely log into the Client-1 VM to test out the connectivity between Client-1 and DC-1 by using the ping command on the command prompt. But first, I would need to locate the private IP address of DC-1 so that I can ping it. The private IP address will be located in the Azure Website under DC-1 VM.
      
<p>
     <img src="https://imgur.com/nwMLToT.png"
          </p>
     
<p>
     <img src="https://imgur.com/JTT7CML.png"
          </p>

As you can see when we tried to ping the private IP address of DC-1, the request timed out.
     This could be multiple reasons, but for this scenario it is becaue DC-1's firewall is preventing ICMP traffic from coming through.
     
***Firewalls sometimes block ICMP traffic for security reasons. ICMP is a protocol used for diagnostic and control purposes, including ping requests and error messages.Blocking ICMP can prevent potential network attacks, such as ICMP flood attacks or smurf attacks, where an attacking floods the network with ICMP packets to overwhelm it. By blocking ICMP, firewalls can help protect the network from certain types of threats and limit potential vulnerabiities***
     
Since we are unable to ping DC-1, we will open up the firewall to allow ICMP traffic. 
     
<h4>How to alter Firewall Settings</h4>

1.) Remote desktop into DC-1

2.) Once logged into DC-1, type 'firewall' on the serach bar.

<p>
     <img src="https://imgur.com/TDcGiuc.png"
          </p>
  
3.) Select 'Inbound Rules' and filter by "Protocol' since we are looking for ICMPv4
     
 <p>
      <img src="https://imgur.com/FJHeTyU.png"
           </p>
      
4.) Enable Core Networking Diagnostics: ICMP Echo Request (ICMPv4-In)
      
5.) Right click the two inbound rules > select 'enable rule'
 
 <p>
      <img src="https://imgur.com/HE2omAJ.png"
           </p>
      
6.) Return to Client VM-1, and you will see the that the pings are now going through!
      
<p>
     <img src="https://imgur.com/CwUMDqO.png"
          </p>
