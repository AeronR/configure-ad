# configure-ad
<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines. I will be demonstrating how I set up virtuaml machines w/ Azure, install Active Directory, and create additional users to simulate the use of Active Directory

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
 
<h4>How to create the Client<h4>
  
1.) 'Virtual Machines' > 'create'
  
2.) VM name = 'Client 1' > Windows 10 Pro > 2vcpus > whatever username and password > 'disks' > 'networking' (Client-1 is automatically put on the same network as DC-1) > Done
  
<p>
  <img src="https://imgur.com/dE0Dqsr.png"
       </p>
  
<p>
