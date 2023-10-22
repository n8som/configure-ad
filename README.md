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

  First, Create the Domain Controller VM (Windows Server 2022) named "DC-1". Take note of the Resource Group and Virtual Network (Vnet) that get created. Next, set the Domain Controller's NIC Private IP address to be static by first clicking on the virtual machine, then networking on the left-hand side, network interface, IP configurations, and finally ipconfig1. Then create the Client VM (Windows 10) named "Client-1". Use the same Resource Group and Vnet created earlier. Finally, ensure that both VMs are in the same Vnet. I used labuser and Password12345 for the username and password for both VMs.

</p>
<p>

![image](https://github.com/n8som/configure-ad/assets/110139109/73526ac7-6e30-4417-97ed-3d9fd0aba13a)

![image](https://github.com/n8som/configure-ad/assets/110139109/4e8cf1c3-45e0-4205-acb3-308f594f583f)


</p>
<br />

<p>
<h3>Step 2: Ensure Connectivity Between the Client and Domain Controller</h3>

  Log in to Client-1 with Remote Desktop using its public IP address that you can find on Azure portal and ping DC-1's private IP address with ping -t. 

  ![image](https://github.com/n8som/configure-ad/assets/110139109/6c81539c-f455-440c-8cf9-c5f7ab1dc78f)

  If the connection times out, which it probably will, you will need to log in to the Domain Controller(DC-1) and enable ICMPv4 on the local Windows Firewall in inbound rules. 

  ![image](https://github.com/n8som/configure-ad/assets/110139109/dc993ef6-a894-4292-aae4-19d85abef6f8)

  Then you can check back into Client-1 and should see the ping succeed.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/791a9d8a-04bd-44f3-86ed-44cde6ae0a12)

</p>
<br />

<p>
<h3>Step 3: Install Active Directory</h3>

  Log in to DC-1 and start installing Active Directory Domain Services by clicking on "Add roles and features" in Server Manager.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/5138b50e-565b-4212-b3c6-1da142627482)

  Go through the process and make sure to check Active Directory Domain Services when you get to Server Roles.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/9501285e-2b39-49d7-9b10-75e249e266ae)

  Promote it as a DC by clicking the flag and setting up a new forest such as mydomain.com (it can be anything, just remember it). I used Password1 under DSRM, but you won't need it.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/0cc04712-bd97-429e-a85f-09176d1e831c)

  ![image](https://github.com/n8som/configure-ad/assets/110139109/f31245f6-5d48-4245-b92b-5b950e4c4a44)

  Then you will need to restart and log back into DC-1 as user: mydomain.com\labuser and the same password you first used to log in to the DC.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/d448eed8-7ab7-420f-9927-efc030a5cd89)

</p>
<br />

<p>
<h3>Step 4: Create an Admin and Normal User Account in AD</h3>

  Go to Active Directory Users and Computers (ADUC) under tools. 

  ![image](https://github.com/n8som/configure-ad/assets/110139109/6b193aa5-4ba5-4cef-8400-1de0de3264f5)

  Create an Organizational Unit (OU) called "_EMPLOYEES" and "_ADMINS" by first right-clicking mydomain.com, then clicking on New and Organizational Unit.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/ab205199-1f63-408c-a101-ed0b80ea4fc5)

  Create a new User in _ADMINS, for example, Jane Doe with the username jane_admin. I used Password1 for Jane and unchecked "User must change password at next logon" for lab purposes. 

  ![image](https://github.com/n8som/configure-ad/assets/110139109/d51b6755-2161-435f-8f08-de7d850d6bdf)

  ![image](https://github.com/n8som/configure-ad/assets/110139109/bdfd9335-6ffa-4b07-ba4a-56aade97f0fa)

  Add jane_admin to the "Domain Admins" Security Group by right-clicking her name and then Properties.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/f0596d1f-eb80-439b-abe0-81fa84fdcb9f)

  Log out of the Remote Desktop connection and log back in as "mydomain.com\jane_admin" and Password1 for the password.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/994b456c-cb28-4b2d-a3b0-cdc52ab1e3f4)

  Use jane_admin as your admin account from now on. 
  
</p>
<br />

<p>
<h3>Step 5: Join Client-1 to Your Domain (mydomain.com)</h3>

  From the Azure Portal, set Client-1's DNS settings to the DC's Private IP address by first going to Client-1 on the portal, networking, network interface, DNS servers, and custom.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/c6b0519b-0057-48d5-9771-5959a0a9cf0d)

  Restart Client-1 from the portal.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/d0d3b9ed-831a-4de6-80d6-6dca5002141a)

  Next, log in to Client-1 via Remote Desktop as the original local admin (labuser) and join it to the domain. First, right-click the control panel, go to settings, then Rename this PC (advanced) and Change. Log in using original Client-1 login credentials.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/1d29a3d7-49c1-4dbd-9528-76d04e4d052f)

  ![image](https://github.com/n8som/configure-ad/assets/110139109/d9734dc0-8abf-422f-8e55-94080fc19e43)

  ![image](https://github.com/n8som/configure-ad/assets/110139109/72d682ee-1f22-4288-935f-9d37f43b9d25)

  The computer will restart. Log in to the Domain Controller and verify Client-1 shows up in ADUC inside the "Computers" container on the root of the domain. 

  ![image](https://github.com/n8som/configure-ad/assets/110139109/74897454-40e2-4ef2-b7fb-e829d569f1a3)
  
</p>
<br />

<p>
<h3>Step 6: Setup Remote Desktop for Non-Administrative Users on Client-1</h3>

  This time, log into Client-1 as mydomain.com\jane_admin and open system properties.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/2d0acb2a-aac7-418c-98c5-359a5b5b8644)

  ![image](https://github.com/n8som/configure-ad/assets/110139109/dd3f22c9-f927-4c38-aace-bfba69b5148f)

  Click on "Remote Desktop" and allow "Domain Users" access to remote desktop.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/f907c875-6189-4296-902f-8b12d0c452cd)

  After that, you should be able to log into Client-1 as a normal, non-administrative user now. Normally you'd want to do this with Group Policy that allows you to change many systems at once.
  
</p>
<br />

<p>
<h3>Step 7: Create a Bunch of Additional Users and Attempt To Log Into Client-1 With One of The Users</h3>

  First log in to DC-1 as jane_admin. Open PowerShell ISE as an administrator.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/06bdefd1-f188-495f-a1ed-f4422abc3fbf)

  Create a new File and copy and paste the contents of this script into it, https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1

  ![image](https://github.com/n8som/configure-ad/assets/110139109/20d70a49-b602-4755-b412-c363bb8c41dc)

  ![image](https://github.com/n8som/configure-ad/assets/110139109/3eb3c75a-0d11-4276-ae4c-ad9b659dc3c7)


  Run the script and observe the accounts being created. You can reduce the number of accounts being created to what you'd prefer, from 10,000 to like 1000.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/ea4b3ebf-74d2-4794-8f30-a114b83b0035)

  When finished, open ADUC and observe the accounts in the appropriate OU, _EMPLOYEES.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/31e47b80-4505-4491-be83-7a5d1b83ec80)

  Attempt to log into Client-1 with one of the accounts, the password for any of the accounts will be Password1.

  ![image](https://github.com/n8som/configure-ad/assets/110139109/6c7d9cd0-d568-44ae-9918-a2647a26cbe4)

  ![image](https://github.com/n8som/configure-ad/assets/110139109/75c952a3-19ce-49e7-85a6-97f735299d9d)

</p>
<br />

<p>
Congrats, you're all done! You should have successfully configured Active Directory, added an admin and new users, and logged into an admin and user account.
</p>
<br />

