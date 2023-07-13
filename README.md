<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1
  - Setup Resources in Azure
- Step 2
  - Ensure Connectivity between the client and Domain Controller
- Step 3
  - Install Active Directory
- Step 4
  - Create an Admin and Normal User Account in Active Directory (AD)
- Step 5
  - Join Client-1 to your domain
- Step 6
  - Setup Remote Desktop for non-administrative users on Client-1
- Step 7
  - Create additional users and attempt to log into Client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>

<p>
<p>
<h3>STEP 1: Setup Resources in Azure</h3>
<p>
First of all, you will create the Domain Controller Virtual Machine (VM) that uses the "Windows Server 2022 Datacenter: Azure Edition" with 2vcpus and 8 GiB memory. We'll name the VM "DC-1" for Domain Controller. Username and Password ex: user: labuser pass: Labpassword1. Using the same username and password for Client-1 is recommended.
</p>
<img src="https://i.imgur.com/eZJNhnD.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<p>
Once DC-1 is created, go to its Newtworking settings and set the DC's NIC Private IP address to be static.
<p>

  - DC-1 -> Networking -> Network Interface -> IP configurations -> ipconfig1

</p>
<p>
<img src="https://i.imgur.com/Qvi8W5Q.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/9EPUQev.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<br />


<p>
Moving on the second VM, we will name it Client-1 that uses Windows 10 Pro version 22H2 with 2vcpus and 8 GiB memory. Also, make sure that in the networking tab of creating the VM, Client-1 should be on the same Virtual network as DC-1. 
<p>
<img src="https://i.imgur.com/7wmJYDq.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/SdfXyAt.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<br />

<h3>STEP 2: Ensure Connectivity between the client and Domain Controller</h3>

<p>
Login to Client-1 with Remote Desktop Connection using the VM's public IP address and inputting the credentials you created earlier. 
<p>
<img src="https://i.imgur.com/gDAOgcQ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
</p>
<p>
When logged into Client-1, open the Windows command prompt and ping DC-1's private IP address. Notice that Client-1's ping is not getting any replies from DC-1.
</p>
<p>
<img src="https://i.imgur.com/3QJknEV.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
We will fix that by first, logging into DC-1 the same way as Client-1. 
</p>
<p>
<img src="https://i.imgur.com/bOaHMmq.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once logged in, type into the Windows search bar "Windows Defender Firewall with Advanced Security" and we will enable two inbound ICMPv4 rules (shown below) that will allow Client-1 to successfully ping DC-1.
</p>
<p>
<img src="https://i.imgur.com/ldxCJom.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now, switch back to the Client-1 VM and ping DC-1's private IP address again. You will see that Client-1 receives replies from DC-1 after enabling those two inbound rules.
</p>
<p>
<img src="https://i.imgur.com/QjmRTUU.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<br />

<h3>STEP 3: Install Active Directory</h3>

<p>
In the DC-1 VM, the Server Manager Dashboard should be the first window that shows up during startup; you will want to press "Add roles and features" and during the setup, you will want to select "Active Directory Domain Services" in server roles. Complete the setup and install.
<p>
<img src="https://i.imgur.com/scTfBTH.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/2X2I65X.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<p>
When finished installed, there will be a triangle symbol next to the flag in the Server Manager, click that and press "Promote this server to a domain controller". 
</p>
<p>
<img src="https://i.imgur.com/j33lA0c.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>It should now take you to a Deployment Configuration setup and we will select "Add a new forest". The Root domain name can be whatever you want, but just do not forget it. EX: mydomain.com. Finish installation.
</p>
<p>
<img src="https://i.imgur.com/vYratX6.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Restart DC-1 and log back into DC-1 as the user mydomain.com\labuser (in this case nate.com\labuser)
</p>
<p>
<img src="https://i.imgur.com/GrbqKhx.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<br />

<h3>STEP 4: Create an Admin and Normal User Account in AD</h3>

<p>
In the Server Manager Dashboard, click Tools on the top right and open "Active Directory Users and Computers"
<p>
<img src="https://i.imgur.com/wn8Ucb6.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create two Organizational Units (OU) and name one "_EMPLOYEES" and the other "_ADMINS"
</p>
<p>
<img src="https://i.imgur.com/L51FgKf.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the _ADMIN OU, create a new user for Jane Doe with the username being "jane_admin". For the user password, you may use Password1 or whatever you desire, just do not forget it. Uncheck all of the boxes while making Jane's password.
</p>
<p>
<img src="https://i.imgur.com/M5x2K4E.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/Z7ExL1m.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
We will add jane_admin to the Domain Admins sercurity group by right clicking her name, going to Properties, and pressing "Member Of". Click add and type in Domain Admins and click OK.
</p>
<p>
<img src="https://i.imgur.com/nNHlgMD.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/ek8zUOO.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log out of DC-1 and log back in using jane_admin as your admin account.
</p>
<p>
<img src="https://i.imgur.com/3u3kp0t.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />

<p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
