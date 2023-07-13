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
<img src="https://i.imgur.com/j33lA0c.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
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
<img src="https://i.imgur.com/GrbqKhx.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
</p>
<br />

<h3>STEP 4: Create an Admin and Normal User Account in AD</h3>

<p>
In the Server Manager Dashboard, click Tools on the top right and open "Active Directory Users and Computers"
<p>
<img src="https://i.imgur.com/wn8Ucb6.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
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
<img src="https://i.imgur.com/M5x2K4E.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/Z7ExL1m.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
</p>
<p>
We will add jane_admin to the Domain Admins sercurity group by right clicking her name, going to Properties, and pressing "Member Of". Click add and type in Domain Admins and click OK.
</p>
<p>
<img src="https://i.imgur.com/nNHlgMD.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/ek8zUOO.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log out of DC-1 and log back in using jane_admin as your admin account.
</p>
<p>
<img src="https://i.imgur.com/3u3kp0t.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
</p>
<br />

<h3>STEP 5: Join Client-1 to your domain</h3>

<p>
In Azure, change the DNS server of Client-1 by going to its Networking settings, Network interface, DNS servers, then changing its DNS servers to Custom. Type in DC-1's private IP address and click save. Now restart Client-1 from within Azure.
<p>
<img src="https://i.imgur.com/wE770us.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Login to Client-1 with your credentials and open up the Windows settings and go to the "About" tab. On the right hand side, press Rename this PC (advanced).
</p>
<p>
<img src="https://i.imgur.com/dSIJcjv.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click Change then under "Member of" click Domain and enter the name of the domain you created. (ex: mydomain.com) It will then ask you to enter your credentials to join the domain.
</p>
<p>
<img src="https://i.imgur.com/Jdv7kRP.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
You should now be connected to the domain you created!
</p>
<p>
To see that Client-1 has connected to the root domain, go back to DC-1 and in Active Directory Users and Computers, click Computers and it should show Client-1.
</p>
<p>
<img src="https://i.imgur.com/4ohpzrm.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
</p>
<br />

<h3>STEP 6: Setup Remote Desktop for non-administrative users on Client-1</h3>

<p>
Log out of Client-1 and log back in as jane_admin under your domain. Open the system properties and click Remote desktop on the right and go to the bottom and press "Select users that can remotely access this PC". Click Add then type in Domain Users and press OK.
<p>
<img src="https://i.imgur.com/RXYbwCa.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/9PiBvrD.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<br />

<h3>STEP 7: Create additional users and attempt to log into Client-1 with one of the users</h3>

<p>
Logged in as jane_admin on DC-1, open PowerShell_ise as an administrator. Make a new file and paste the contents of 

  [this script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1). As you can see at the top of the script, the password for all the users going to be created will be Password1 as shown below.
<p>
<img src="https://i.imgur.com/dplMwwn.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Observe that a bunch of accounts are being created.
</p>
<p>
<img src="https://i.imgur.com/3U4cx2n.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Back in Active Directory Users and Computers click on the _EMPLOYEES and all of the users created will be shown there. If not, right click and hit refresh. 
</p>
<p>
Finally, you can pick any username from the _EMPLOYEES OU and login to Client-1 with the given password in the script. Once logged in you can open the command prompt and type in "whoami" to see that you are logged in as a new created user on Client-1.
</p>

<br />
Congrats! You have successfully configured Active Directory! :)
