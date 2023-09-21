<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1: Setup Resources in Azure
- Step 2: Ensure Connectivity Between the Client and Domain Controller
- Step 3: Install Active Directory
- Step 4: Create an Admin and Normal User Account in AD
- Step 5: Join Client-1 to Your Domain (mydomain.com)
- Step 6: Setup Remote Desktop For Non-Administrative Users on Client-1
- Step 7: Create a Bunch of Additional Users and Attempt To Log Into Client-1 With One of The Users

<h2>Deployment and Configuration Steps</h2>

<p>
<h3>Step 1: Setup Resources in Azure</h3>

  First, Create the Domain Controller VM (Windows Server 2022) named "DC-1". Take note of the Resource Group and Virtual Network (Vnet) that get created. Next, set the Domain Controller's NIC Private IP address to be static. Then create the Client VM (Windows 10) named "Client-1". Use the same Resource Group and Vnet created earlier. Finally, ensure that both VMs are in the same Vnet. I used labuser and Password12345 for the username and password for both VMs.

</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
<h3>Step 2: Ensure Connectivity Between the Client and Domain Controller</h3>

  Log in to Client-1 with Remote Desktop and ping DC-1's private IP address with ping -t. If the connection times out, which it probably will, you will need to log in to the Domain Controller and enable ICMPv4 on the local Windows Firewall. Then you can check back into Client-1 and should see the ping succeed.
  
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
<h3>Step 3: Install Active Directory</h3>

  Log in to DC-1 and start installing Active Directory Domain Services. Promote it as a DC by setting up a new forest such as mydomain.com (it can be anything, just remember it). Then you will need to restart and log back into DC-1 as user: mydomain.com\labuser
  
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
<h3>Step 4: Create an Admin and Normal User Account in AD</h3>

  Go to Active Directory Users and Computers (ADUC) under tools. Create an Organizational Unit (OU) called "_EMPLOYEES" and "_ADMINS". Create a new employee, for example, Jane Doe with the username jane_admin. Add jane_admin to the "Domain Admins" Security Group. Log out of the Remote Desktop connection and log back in as "mydomain.com\jane_admin". Use jane_admin as your admin account from now on. 
  
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
<h3>Step 5: Join Client-1 to Your Domain (mydomain.com)</h3>

  From the Azure Portal, set Client-1's DNS settings to the DC's Private IP address. Restart Client-1 from the portal. Next, log in to Client-1 via Remote Desktop as the original local admin (labuser) and join it to the domain. The computer will restart. Log in to the Domain Controller and verify Client-1 shows up in ADUC inside the "Computers" container on the root of the domain. Create a new OU named "_CLIENTS" and drag Client-1 into there. This isn't really necessary, just for organizational purposes. 
  
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
<h3>Step 6: Setup Remote Desktop for Non-Administrative Users on Client-1</h3>

  Log into Client-1 as mydomain.com\jane_admin and open system properties. Click on "Remote Desktop" and allow "Domain Users" access to remote desktop. After that, you should be able to log into Client-1 as a normal, non-administrative user now. Normally you'd want to do this with Group Policy that allows you to change many systems at once.
  
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
<h3>Step 7: Create a Bunch of Additional Users and Attempt To Log Into Client-1 With One of The Users</h3>

  First log in to DC-1 as jane_admin. Open PowerShell ISE as an administrator. Create a new File and paste the contents of this script into it, https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1 

  Run the script and observe the accounts being created. You can reduce the number accounts being created to what you'd prefer, like 1000. When finished, open ADUC and observe the accounts in the appropriate OU. Attempt to log into Client-1 with one of the accounts, the password for any of the accounts will be Password1.
  
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Congrats, you're all done!
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

