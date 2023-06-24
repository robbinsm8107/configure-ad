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

- We created two Virtual Machines, one being Windows Server 2022 and the other being Windows 10
- We made sure that both Servers are connected within the same Virtual Network (Vnet) 
- We installed Active Directory, set up our "Domain Controller" Windows Server as the actual Domain Controller, and utilized a fake domain called mydomain.com
- We then created a Domain Admin account through the Domain Controller
- We then joined Client-1 to the Domain Controller's Active Directory Network
- We then set up Remote Desktop for any Domain Users for Client-1
- We then utilized a Powershell Script to create one hundred Domain User Accounts
- We then tested the utilization of A Records using the DNS Manager within the Domain Controller
- We tested the utilization of Canonical Name (CName) Records from the Domain Controller's DNS Manager

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/rGoBfpa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  <img src="https://i.imgur.com/bpn00zi.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

</p>
<p>
Once we had our virtual machines up and running within our Azure Resource Group, we then made sure that they are within the same Virtual Network (Vnet). We were able to see this within the Azure Resource Group, but I felt it was necessary to test the connectivity between the Virtual Machines. I then forced the Virtual Network Interface Card connected to the Domain Controller to use a static Private IP Address of 10.0.0.4. I then logged into the Client-1 VM via Remote Desktop Protocol (RDP) to utilize a terminal ping command "ping 10.0.0.4 -t" to continuously send ICMP Echo Requests to the Domain Controller. I then enabled ICMP through the Azure Network Security Group (NSG) connected to the Domain Controller and this did not work. I then utilized RDP to log into the Domain Controller itself to view the internal Windows Defender Firewall where I enabled the ICMP Echo Request and ICMP Echo Reply. The screenshots above show the ICMP Protocol was disabled and then enabled from the viewpoint of Client-1 through the Command Prompt. The second screenshot shows the Domain Controller's Windows Defender Firewall settings that I had to enable. </p>
<br />

<p>
<img src="https://i.imgur.com/zoPW4IB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once we have tested the connectivity as the above paragraph has shown, we then logged back into the Domain Controller where we set up and installed Active Directory within the Windows Server. We had to promote the Domain Controller to the actual Domain Controller Server during this process and we created a new forest with the domain that I unoriginally called mydomain.com. Once the Server is promoted to Domain Controller officially, I then rebooted the Server and allowed the setup to take place. We had to utilize the login of MYDOMAIN\labuser as opposed to the usual login of the username being the IPv4 Public Address. The setup of Active Directory during the reboot process does typically take a bit of time and you will need to wait.  </p><br />

<p>
<img src="https://i.imgur.com/tXTjIhm.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once Active Directory has been set up with the Domain Controller, we then created Organizational Units (OU). We named these OU _EMPLOYEES and _ADMINS, this naming convention was used to make it easier to differentiate from the OU that were automatically created by the installation of Active Directory. We then created a Jane Doe account and set them with Domain Administrator privileges to use for the rest of this demonstration as a live Administrator user.  </p>
<br />

<p>
<img src="https://i.imgur.com/866xUuq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We then had to set up the Client-1 VM to join the mydomain.com Active Directory network. We did this by logging into the Client-1 VM's Control Panel to change the System Properties. We went to the "Computer Name" portion and successfully added this VM to the Active Directory network as you can see in the above screenshot. This is important because it is an example of the process to add other computers or virtual machines to the Domain Controller's network. You can then begin more advanced control and setup of the overall applications, permissions, et cetera throughout the network itself. This is going to be a very simple network design with one Client and one Domain Controller. We then went back to the Domain Controller where we created a new OU called _CLIENTS and we added Client-1 to this OU. </p>
<br />

<p>
<img src="https://i.imgur.com/hriz4GY.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We then had to log back into the Client-1 VM to set up the ability for Domain Users to utilize RDP. This will allow the users with Domain User access to RDP into the Client-1 VM to do their work or whatever other tasks are needed. This is very important to set up to allow your employees to log into the Server over a protected network to ensure a higher level of security and encryption. This will also allow remote workers to log into the Server to troubleshoot if there are any issues or simply to do their normal day-to-day job functions. </p> 
<br />

<p>
<img src="https://i.imgur.com/qGDEHhB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  <img src="https://i.imgur.com/2jQ38Wg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

</p>
<p>
We then utilized a Powershell script to create one hundred users; I do not take credit for the creation of the Powershell script itself because I was able to find this script through Josh Madakor's Github profile. The link to this script is found below:
https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1
This script will create user accounts with random letters to generate the first and last names where they will all have the same password. I would never use this type of script in an actual production environment but I felt like this was very helpful in learning more about Active Directory and saved time in the creation and implementation of fake user accounts for this demonstration. I am showing examples of both the Powershell script being run (as administrator) and then the actual names that the script has created.
</p> 
<br />

<p>
<img src="https://i.imgur.com/QvWuz5P.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/sHHge1r.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

</p>
<p>
We then showed the utilization of A Records about the Domain Controller's DNS Manager. Within the Client-1 VM, we tried to use both ping and nslookup for "mainframe" where we were unable to find anything that resolves to this name. We logged back into the Domain Controller and went to the DNS Manager to add A Records and link "mainframe" to 10.0.0.4. Because the Client-1 VM is using the Domain Controller as its DNS, the "mainframe" is now resolvable and we were able to "ping" this. 
</p> 
<br />

<p>
<img src="https://i.imgur.com/zy0BAFH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/iO1OfwU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/DhILq5m.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

</p>
<p>
We then showed the utilization of some simple DNS tools from the Command Prompt. The first thing we did was set up the "mainframe" A Record to utilize IP Address 8.8.8.8 (Google's Name Servers) as opposed to the original 10.0.0.4. We then "pinged" "mainframe" again, but as you can see in the screenshot, it utilized 10.0.0.4 due to the internally saved DNS from the previous task. We then used ipconfig/displaydns to see the saved DNS settings that we have for this Client-1 VM at this time. We wanted to resolve "mainframe" to the new DNS settings so we implemented ipconfig/flushdns to delete all of the saved DNS Settings. Once this was finished, we ran a ping on the "mainframe" again to see the new IP Address of 8.8.8.8 as active.
</p> 
<br />

<p>
<img src="https://i.imgur.com/3sq3NtF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/7lDse7A.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

</p>
<p>
We wanted to test the usage of Canonical Names (CName) within DNS Settings next. We created a CName record within the Domain Controller with the alias "search" that points to www.google.com When we ping "search" it shows us the IP Address of Google because of the utilization of the CName as previously shown in the above screenshot. We then tested the nslookup for "search" and you can see that it does point to the Google Name Servers. </p> 
<br />
