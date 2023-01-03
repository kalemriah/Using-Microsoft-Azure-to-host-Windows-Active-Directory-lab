<h2>Using Microsoft Azure to host Windows Active Directory lab</h2>
<h2>Overview</h2>
<p align="left">
This is a walkthrough on how to set up an Active Directory lab within the Microsoft Azure cloud service. This Active Directory scenario will contain two domain controllers on the same domain to simulate a real life scenario. I recommend this to anyone who may want to have an Active Directory home lab to further their skills but their home PC cannot handle the virtualization. This is a perfect solution because it will all be hosted on the cloud, meaning that your home PC will only be running HTTPS and RDP to access this lab. Once you have completed this lab I also recommend doing more research on Active Directory and working on your lab to simulate what it may be like in a real world setting. Some examples may be: create and delete user accounts, Resetting passwords, set permissions, organize your company’s network hierarchy, Finding policies in Domain group policies, DNS, DHCP, Domain Controller and many many more.<br/>
<h2>Prerequisites </h2>
<p align="left">
-Microsft Azure<br/>
-Windows Remote Desktop Connection 
<h2>Steps</h2>
<p align="center">
<b>-1.)Create Resource group</b>
<p align="left">
-Once logged in, redirect to the Home portal for Microsoft Azure. Under 'Azure services' select 'Resources Groups'. Select 'Create a resource'<br/>
![image](image/image1.PNG)

<br/>
	-Name Resource Group 'ADLAB'<br/>
  <p align="center">
<b>-2.)Virtual Network</b>
<p align="left">
&emsp;	<b>Under Basics Tab<br/></b>
	-Name the Virtual Network "OnSite"<br/>
  &emsp;	<b>IP Addresses Tab<br/></b>
	-Click on the blue highlighted 'default' under the Subnet name<br/>
  -Change Subnet Address range to '10.0.1.0/24<br/>
    &emsp;	<b>Security Tab<br/></b>
	-Under Security make sure all tabs are Disabled <br/>
  -You can skip the 'Tags' tab
	-Review & Create<br/>
	-Allow the Virtual Network to deploy <br/>
  <p align="center">
<b>-3.)Create Virtual Machines within Resource groups</b>
<p align="left">
-Redirect to the Home portal for Microsoft Azure. Under 'Azure services' select 'Virtual Machines' then select Create <br/>
	&emsp;	<b>Under Basics Tab<br/></b>
	-Add to Resource Group that was previously created 'ADLAB'<br/>
	-Name the virtual machine 'DC1'<br/>
	-Change Availability option to Availability set and Create an availability set<br/>
		-Name 'ADAvailSet' and leave as defaults<br/> 
	-Use a Windows Server 2019 Database serve image <br/>
		-2vcpus 8Gbs memory works great<br/>
	- Create username and password <br/>
		&emsp;<b>Under Disks Tab<br/></b>
	-Change OS Disk type to Standard HDD<br/>
	-Select 'Create and Attach a disk'<br/>
		-Select 'Change Disk Size'<br/>
		-Change the 'Disk SKU' to Standard HDD<br/>
		-Change disk size to 10Gb (This is where active directory will be installed)<br/>
		&emsp;<b>Under Networking Tab<br/></b>
	-Nothing needs to be changed just double check that the virtual network is the "OnSite" network<br/>
	-Also double check that RDP 3389 is open to access your virtual machine<br/>
&emsp;	<b>	Under the Management tab<br/></b>
	-Disable boot diagnostics <br/>
	 -Select Review & Create<br/>
	-Allow the virtual machine to deploy<br/>
  <p align="center">
<b>-4.) Configuring Virtual Machine 1</b>
<p align="left">
	&emsp;	<b>Within 'DC1'<br/></b>
	-Select 'Connect' > 'RDP' and download the RDP client file then login (I recommend selecting all of the 'remember me', 'don't ask again' boxes)<br/>
	-Allow the VM to boot to desktop and wait to server manager to boot <br/>
	&emsp;	<b>Format F: Disk drives to contain Active Directory<br/></b>
	-Within the server manager select 'Tools' on the top right > 'Computer Management' <br/>
	-Once open select 'Disk Management'  <br/>
		-Leave disk as an MBR (Master Boot Record) click OK<br/>
		-Right Click the Disk 2 and select 'New Simple Volume'<br/>
		-Leave the disk as F: and select next until done<br/>
	&emsp;	<b>Install  and deploy Domain Controller on Virtual Machines<br/></b>
	-Click 'Manage' > 'Add roles and features' <br/>
	-Select Next until on the Server Installation tab<br/>
	-Check the Active Directory Domain Services and select Add Features<br/>
	-Select Next until on the Confirm installation selections and finally select install <br/>
	-Once completed Select the 'Promote this server to domain controller' (if you accidentally closed this tab select the flag with the yellow warning sign on the top right next to the 'Management' tab and you will have this option here)<br/>
	-Select 'Add new forest' and name the domain "myazurelab.com"<br/>
	-Select Next and make sure its Windows Server 2016 and create a password and select Next<br/>
	-Skip the DNS creation and skip Additional Options<br/>
	-Under Paths tab change the disks from the C: drive to the F: drive<br/>
	-Select Next and Install (this will install the active directory domain controller)<br/>
	-Once complete you will be signed out and have to restart the VM and reconnect with RDP<br/>
  <p align="center">
<b>-5.)Creating second Domain Controller VM</b>
<p align="left">
	&emsp;<b>	Within Resource group<br/></b>
  -Select the Virtual Machines tab, then select Create<br/>
&emsp;	<b>	Under Basics Tab<br/></b>
	-Add to Resource Group<br/>
	-Name 'DC2'<br/>
	-Change Availability option to Availability set 'ADAvailSet'<br/>
	-Use a Windows Server 2019 Database serve image <br/>
		-2vcpus 8Gbs memory works great<br/>
	- Create username and password <br/>
&emsp;	<b>	Under Disks Tab<br/></b>
	-Change OS Disk type to Standard HDD<br/>
	-Select 'Create and Attach a disk'<br/>
		-Select 'Change Disk Size'<br/>
		-Change the 'Disk SKU' to Standard HDD<br/>
		-Change disk size to 10Gb (This is where active directory will be installed)<br/>
&emsp;	<b>	Under Networking Tab<br/></b>
	-Nothing needs to be changed just double check that the virtual network is the "OnSite" network<br/>
	-Also double check that RDP 3389 is open to access your virtual machine<br/>
&emsp;	<b>	Under the Management tab<br/></b>
	-Disable boot diagnostics <br/>
	-Select Review & Create<br/>
	-Allow the virtual machine to deploy<br/>
  <p align="center">
<b>-6.)Configuring DNS from Domain Controller instead of Azure dynamic IP and DNS (DC1)</b>
<p align="left">
	&emsp;	<b>Within 'DC1' VM<br/></b>
	-Redirect to the 'DC1' Virtual Machine on Azure<br/>
	-Within the 'Overview' tab under the 'Properties' tab  > 'Networking' copy the IP address <br/>
	-Within the settings tab, select the 'Networking' tab , select the highlighted 'Networking interface'<br/>
	-Within the Networking interface settings tab select the 'IP configurations' tab<br/>
	-Select the current IP > Change the 'Dynamic' setting to 'Static' <br/>
	&emsp;	<b>Within 'OnSite' Virtual Network<br/></b>
	-Redirect to the 'OnSite' Virtual Network <br/>
	-Within the settings tab, select the 'DNS servers' tab and then select Custom <br/>
	-Paste the DC1's IP address and Save<br/>
	-Redirect to Virtual Machines and restart both DC1 and DC2 to update the DNS<br/>
  <p align="center">
<b>-7.) Adding Domain Controller to second VM</b>
<p align="left">
	&emsp;<b>	Within 'DC2'<br/></b>
	-Select 'Connect' > 'RDP' and download the RDP client file then login (I recommend selecting all of the 'remember me', 'don't ask again' boxes)<br/>
	-Allow the VM to boot to desktop and wait to server manager to boot <br/>
		&emsp;<b>Format F: Disk drives to contain Active Directory<br/></b>
	-Within the server manager select 'Tools' on the top right > 'Computer Management' <br/>
	-Once open select 'Disk Management'  <br/>
		-Leave disk as an MBR (Master Boot Record) click OK<br/>
		-Right Click the Disk 2 and select 'New Simple Volume'<br/>
		-Leave the disk as F: and select next until done<br/>
		&emsp;<b>Adding 'DC2' to the domain<br/></b>
	-Within the Server Manager select the 'Local Server' tab > 'Workgroups' and click the blue highlighted 'WORKGROUP' <br/>
	-Within the System Properties > Computer Name tab, select the 'Change..." box<br/>
	-Within Computer Name/Domain Changes, under Members of, select the Domain check box and type 'myazurelab.com'<br/>
	-Use the credentials of 'DC1' to authorize this new addition <br/>
	-Allow the Domain Controller to add this VM and restart the VM <br/>
		&emsp;<b>Install  and deploy Domain Controller on Virtual Machine 2<br/></b>
	-Click 'Manage' > 'Add roles and features' <br/>
	-Select Next until on the Server Installation tab<br/>
	-Check the Active Directory Domain Services and select Add Features<br/>
	-Select Next until on the Confirm installation selections and finally select install <br/>
	-Once completed Select the 'Promote this server to domain controller' (if you accidentally closed this tab select the flag with the yellow warning sign on the top right next to the 'Management' tab and you will have this option here)<br/>
	-Select 'Add a domain controller to an existing domain'<br/>
	-Type 'myazurelab.com' into the domain<br/>
	-Select the 'Change...' box to add credentials <br/>
	-Type myazurelab\DC1 for the username and type the password you created then select next <br/>
	-Create a password and select next<br/>
	-Skip the DNS creation <br/>
	-Under Additional Options tab, change the 'Replicate from' to DC1.myazurelab.com<br/>
	-Under Paths tab change the disks from the C: drive to the F: drive<br/>
	-Select Next and Install (this will install the active directory domain controller)<br/>
	-Once complete you will be signed out and have to restart the VM and reconnect with RDP<br/>
  <p align="center">
<b>-8.)Configuring DNS from Domain Controller instead of Azure dynamic IP and DNS (DC2)</b>
<p align="left">
	&emsp;<b>	Within 'DC2' VM<br/></b>
	-Redirect to the 'DC2' Virtual Machine on Azure<br/>
	-Within the 'Overview' tab under the 'Properties' tab  > 'Networking' copy the IP address (Similar to step 6) <br/>
	-Within the settings tab, select the 'Networking' tab , select the highlighted 'Networking interface'<br/>
	-Within the Networking interface settings tab select the 'IP configurations' tab<br/>
	-Select the current IP > Change the 'Dynamic' setting to 'Static' <br/>
&emsp;	<b>	Within 'OnSite' Virtual Network<br/></b>
	-Redirect to the 'OnSite' Virtual Network <br/>
	-Within the settings tab, select the 'DNS servers' tab and then select Custom <br/>
	-Paste the DC2's IP address as the second IP and Save<br/>
	-Redirect to Virtual Machines and restart both DC1 and DC2 to update the DNS<br/>
		&emsp;<b>Within DC1<br/></b>
	-RDP into DC1 and let everything boot up<br/>
	-Within Server Manager select Tools > Active Directory Sites and Services, and rename 'Default-First-Site-Name' to 'OnSite'<br/>
  <p align="center">
<b>-9.)Configuring Subnets on Virtual Network</b>
<p align="left">
	&emsp;	<b>Within 'OnSite' virtual network<br/></b>
	-Within settings, select Subnets<br/>
	-Copy the IPv4 subnets<br/>
	&emsp;	<b>Within DC1<br/></b>
	-Within Server Manager select Tools > Active Directory Sites and Services<br/>
	-Right click the Subnets OU, then select 'New Subnet'<br/>
	-Paste the IPv4 subnets from 'OnSite' into the prefix box and select the 'OnSite' Site Name then select OK<br/>
  <p align="center">
<b>-10.) Confirming the dual domain controllers are working</b>
<p align="left">
	&emsp;<b>	Within DC1<br/></b>
	-Within Server Manager select Tools > Active Directory Users and Computers<br/> 
	-Right click the Users OU select New > User<br/>
	-Create a new user with the name 'Test1' and then create a password for the user, select OK and Finish<br/>
	-Right click on 'myazurelab.com' > select Change Domain Controller <br/>
	-Select the DC2 'DC2.myazurelab.com' then select OK<br/>
	-Open the Users OU and find the Test1 user to confirm that the domain controllers are connected<br/>
<b>Congratulations you now have an active directory lab on Azure!</b>
