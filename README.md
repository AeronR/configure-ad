# configure-ad
<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines. 

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
  
<h3>Install Active Directory</h3>

<sub>***Active Directory is a directory service developed by Microsot thatstores and manages information about users, computers, and resources in a network. It provides a centralized management and authentication, enabling administrators to control access to network resources and enforce security policies. Active Directory simplifies user manageent, imporves network security, and ehances produtivity by allowing seamless access to resources across multiple domains. It plays a vital role in Windows-based networks, providing a scalable and efficient infrasturcture for user authentication, resource organization, and policy enforcement***</sub>

<h4>How to Install Active Directory</h4>

1.) Open DC-1 VM 

2.) Once inside Dc-1 > search bar 'Service Manager'

3.) Select 'Add roles and features' > 'next' on everything until you reach & select 'Active Directory Domain Services'

<p>
     <img src="https://imgur.com/MZ9dJce.png"
          </sc>
     
<p>
     <img src="https://imgur.com/NjPMjL8.png"
          </p>
 
Active Directory has now been installed on DC-1, but we still need to set up an actual domain.
     
<h4>How to promote DC-1 VM to be an actual Domain Controller</h4>

1.) Open DC-1 VM > open 'Service Manager' > select the notifactions flag (top right)

<p>
     <img src="https://imgur.com/aJe0Q3b.png"
          </p>
   
2.) Select 'Promote this server to be a domain controller' > select 'Add new forest' > enter domain name > selct 'next > enter a password > 'next' > 'install'
     
<p>
     <img src="https://imgur.com/IPyUDBk.png"
          </p>
     
<p>
     <img src="https://imgur.com/w404TUZ.png"
          </p>
     
<h3>Part 4: Create Admin and Normal User Accouns on AD</h3>

1.) Open DC-1 VM > open 'Active Directory Users and Computers' via search bar.

2.) Right-click 'mydomain.com' > 'new' > 'Organizational Unit' > create 2 units, one for employees and another for adimins.

<p>
     <img src="https://imgur.com/ImVZVLD.png"
          </p>
     
<h4>How to create Users/Admins & Assigning Role of Admin</h4>

1.) Open the organizational unit for Admins > 'New' > 'Users' > enter username and password of choice.

<p>
     <img src="https://imgur.com/XS0S7u5.png"
          </p>
     
<p>
     <img src="https://imgur.com/53MAyVh.png"
          </p>
   
***Once the admin user has been created, we will give them the role of 'Admin'. Adding a user into a organizational unit will not grant them admin authority yet, so now we will give them the official role***
     
 2.) Right-click the admin's name > 'Properties' > 'Member Of' > Add > type 'Domain Admins' > Apply > OK

<p>
     <img src="https://imgur.com/GkEm1EK.png"
          </p>
     
<p>
     <img src="https://imgur.com/OKMp8fW.png"
          </p>
     
Now that we have created an Admin, we can log out of the Domain Controller and relog back into the DC-1 VM as the admin user.
     
     
<h3>Part 5: Join Client-1 to your domain (mydomain.com)</h3>

Now we will connect Client-1 to the domain so that Client-1 can retrieve/connect to user information stored in DC-1.

We must first set Client-1's DNS settings to the domain controllers private IP address. This is to ensure that the client can communicatewith the domain controller for things like logging on, accessing shared files, printers, etc. 

<h4>How to set Client-1 DNS settings</h4>

1.) Azure Portal > 'Virtual Machines' > DC-1 VM > copy the private IP address 

<p>
     <img src="https://imgur.com/sEqkeeq.png"
          </p>
     
2.) Go to Client-1 VM > Networking > Network Interface
     
<p>
     <img src="https://imgur.com/CyZ9cyu.png"
          </p>
     
3.) Select 'DNS servers' > 'Custom > paste DC-1's private IP address > save 
     
<p>
     <img src="https://imgur.com/QKzQVBm.png"
          </p>
    
***Client-1's DNS settings have now been change. We just have to flush the local DNS cache and restart Client-1 now***
     
4.) Go to Client-1 VM overview > click 'Restart' to flush DNS cache
     
<p>
     <img src="https://imgur.com/ToTTOlS.png"
          </p>
     
<h3>Part 6: Setup Remote Desktop for non-admin users on Client-1</h3>

Now, we are going to set it up so all domain users can remote log into Client-1 because as of right now only admins can do this.
     
<h4>How to Set Up Remote Desktop for Non-Admins:<h4>
     
1.) Inside Client-1 VM, right-click Windows start icon > click 'System'
     
<p>
     <img src="https://imgur.com/WZ51z6R.png"
          </p>
     
2.) Select 'Remote Desktop' > 'Select Users that can remotely access PC' > 'Add' > 'Domain Users' > 'OK' > Now non-admins can now log in. 
     
<p>
     <img src="https://imgur.com/gTqiUK9.png"
          </p>
     
 <h3>Part 7: Create additional users and attempt to log into Client-1 w/ one of the users<h4>
      
 Now that we have set it up for that regular users can access Client-1, we will be testing it out.
      First, we will create random user accounts and attempt to log into Client-1 to ensure that it is working properly. 
      
<h4>How to Create Additional Users</h4>
      
1.) Log into DC-1 VM as the admin > in the search bar type "Powershell ISE" and run as admin
      
2.) Create a new file > Copy and paste the script from this link into Powershell and it will start creating multiple accounts: https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1
      
<p>
     <img src="https://imgur.com/MvZttfM.png"
          </p>
   
3.) Go back to DC-1 VM > Open Active Directory > under the 'Employees' Organizational Unit you will see all the users created by the script we implemented.
     
<p>
     <img src="https://imgur.com/fM1HeV2.png"
          </p>
     
4.) Log into any of the users created by the script to make sure it works.
     
<p>
     <img src="https://imgur.com/30IGNOI.png"
          </p>

     
 
      
   
      
      
      
      
      
   
      
     

  

     
        
 
     
