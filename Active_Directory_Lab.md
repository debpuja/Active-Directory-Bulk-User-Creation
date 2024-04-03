# Active Directory Bulk User Creation
![active directory title pic](https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/e557e26f-301a-47d7-a57d-6552d21249a4)

<h2>Description</h2>
Setting up an Active Directory using Oracle VirtualBox with over 1000 users using Mac OS.

Credits for lab: [Josh Madakor](https://github.com/joshmadakor1]), [YouTube link](https://youtu.be/MHsI8hJmggI?si=jeKxMD7v_-a03UoP)
<br />

<h2>Objectives</h2>
For this project I want to demonstrate my ability to set up an Active Directory using Oracle VirtualBox with Powershell Automation, Windows Server 2019 and Windows 10. Setting up and Active Directory security is critical for organizations. Setting up a test environment within a sandbox allows for experimenting with security configurations, testing security policies, and conducting penetration testing to identify potential vulnerabilities.
<br />

<h2>Languages and Utilities Used</h2>
 
- <b>Oracle VirtualBox</b>
- <b>PowerShell</b>

<h2>Environments Used </h2>

- <b>Windows 10</b>
- <b>Windows Server 2019</b>

<h2>Program walk-through:</h2>

When setting this lab up, we need to download [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads), [Windows Server 2019 ISO](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019), and [Windows 10 ISO](https://www.microsoft.com/en-us/software-download/windows10ISO). 

When setting up VirtualBox, we will name our virtual machine "Domain Controller". The type will be: Windows. The version is: Other Windows (64-bit). For memory size, I chose 2048MB (2GB) and 1 CPU; the amount you input depends on how much RAM the system you're running your machine on. I left the stock input of 20 GB for the Virtual Hard Disk Size. 
<p align="center">
<img width="750" alt="4 summary setup for domain controller VM" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/b396086b-9214-42a6-b3b8-b60237f2c182">

I will then configure the Domain Controller VM by going into Settings and adding 2 NICs (one for Internet and other for Internal). 
<p align="center">
<img width="750" alt="5 2 NICS" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/f8b6b3b3-bf1d-4e35-a240-54f455a140bb">

After powering on the machine, I selected the Windows 2019 Server as the optical drive. Selecting this option will prompt you to begin the installation of Windows Server 2019. I selected the "Windows Server 2019 Standard Evaluation (Desktop Expereince)" since creating the active directory will be a lot easier with a GUI. The type of installation we want is "Custom: Install Windows only (advanced)", this selction will format the hard drive and install from scratch.
<p align="center">
<img width="700" alt="6 Installing windows" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/da5e8adc-f1d1-477c-a6a8-11a398ba37a8">

During the installation it might take 5-10 minutes to install. The server will reboot mutiple times. When prompted to "Press any key to boot from CD or DVD", ignore that and the installation will continue normally. After choosing the preferences, I was prompted to give a password for the default admin account. The password I chose to use (and will use for all passwords for this lab is: Password1). 
<p align="center">
<img width="700" alt="7 admin password setup" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/367391f7-525f-4f59-9da7-88e0244aba11">

After sucessfully instaling Windows Server 2019, we will install Oracle VM VirtualBox Guest Editions. This consists of device drivers and system applications that optimize the guest operating system for better performance and usability. You will execute this by going to Devices > Insert Guest Addition CD image. 
<p align="center">
<img width="592" alt="8 installing guest addition" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/858311f3-6dcb-45a0-b194-fb91970bb718">

Then go to File Explorer > This PC > CD Drive (D:) VirtualBox Guest Additions > VboxWindowsAdditions-amd64. After the install is complete reboot the VM. 
<p align="center">
<img width="750" alt="9 Completed instal of guest additions" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/2eb39a7a-c9cc-4bd8-a4ae-5a455cf1d127">

Next, we will set up IP addressing. For our Domain Controller, there are 2 NICs: one for Internet and one for Internal. The Internet NIC will automatically get an IP Address from our home router. The Internal NIC will be used for internal network and we have to set up the IP Addressing manually.

Selct the Network icon at the bottom right corner > Network & Internet Settings > Ethernet > Change adapter options. You will notice you have two ethernets. We have to figure our which one is Internal and which one is Internet and name them appropriately. To do this, right click on one of the two ethernets and click Status > Details. You want to go to IPv4 and see if the address start with 10.0.*.**. If so, this ethernet is concluded as the Internet NIC. I went ahead and changed the name of this Ethernet to "INTERNET" clarify that this Ethernet is the Internet NIC. 
<p align="center">
<img width="750" alt="10 internet renaming " src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/5b765032-aa3a-4277-9ace-644fc9f0cba1">

With the other Ethernet I will do the same thing as I did above and view the IPv4 address. If the address starts with 169.254.**.**, this is the Internal NIC. This specfic adapter was look for a DHCP server to try to get an IP address from somewhere, but it was unable to find one. So the 169.254.**.** address was automatically assigned. I also went ahead and renamed this ethernet to "INTERNAL".
<p align="center">
<img width="750" alt="11 internal renaming" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/e021d74c-e6e0-47ca-87ff-7d97b2dfff6c">

Additionally, I was also add an IP address to the Internal NIC. To do this, right click on Internal > Properties > Internet Protocol Version 4 (TCP/IPv4) Properties > Use the following IP address. 

IP Address: 172.16.0.1

Subnet gateway: 255.255.255.0

We will not use a default gateway since the domain controller itself will serve as the default gateway. 
For the DNS server when installing active directory, it automatically installs DNS, so this server will use itself as the DNS sever. I used the IP address used for the Internal NIC: 172.16.01. 
<p align="center">
<img width="750" alt="12 assigning ip to internal nic" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/c25506c2-8153-4f11-8d67-63b14e9b9bb6">

I also went ahead and renamed the PC as it will help being more organized. Start > Settings > System > About > Rename this PC. The current name is an arbitary name. I will rename the PC to "DC". Then select Restart Now. 
<p align="center">
<img width="750" alt="13 renmaing PC" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/5d7f4711-6197-4fab-95a9-f57f3e77ecf5">

After loggin back in again, we will install active directory domain services (AD DS) and create a domain. We will start at the Server Manager. Add roles and features > Next > Role-based or feature based installation. At this point, you will pick the server you want to use; we should only have one server named "DC" so pick that one. Next choose "Active Directory Domain Services" > Add features. 
<p align="center">
<img width="750" alt="14 choosing ADDS" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/9e7c3e65-d3a6-4b1c-a301-6360795ccd38">

The serveral few prompts afterwards, click next untill you get to the install button. Start installing the AD DS. After the role has been installed, you can close out of that screen. 
<p align="center">
<img width="750" alt="15 installing ADDS" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/e0f3e522-0deb-4069-8437-614ca6afae7b">

At the server manager dshaboard, click the notifcation button. There should be a yellow trinagular caution sign. Click "Promote this server to a domain controller". We have to do the post deployment configuration; we installed the software for AD DS but we didn't actually create the domain yet. 
<p align="center">
<img width="750" alt="16 making the domain" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/ad21b331-1859-402c-9149-2077bba1175b">

At the deployment configuration click the option of "Add a new forest", and then give a root domain name. I named my domain "mydomain.com". 
<p align="center">
<img width="750" alt="17 naming domain" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/bab9e5d5-f189-4cca-be17-63bb5166a9ec">
 
For the password, I will be using the password: Password1. Click next for the DNS Options, Additional Options, Paths, Review Options. At Prerequisites Check, you can click install. After it finsihes install, the computer will automatically restart. Restarting will take 5-10 minutes to complete. 
<p align="center">
<img width="750" alt="18 ADDS config instal" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/c9ffa07a-9090-4544-8871-f5ec7fc5ffc5">

Now when logging into the VM, we have the user "MYDOMAIN\Adminsitrator" login. 
<p align="center">
<img width="750" alt="19 MYDOMAIN admin login" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/95494853-7ca6-494f-b728-d41747e98f00">

Next we will create our own dedicated domain admin account instead of using the the built-in adminstrator account. Go to Start > Windows Administrative Tools > Active Directory Users and Computers. On the left hand side, you will see the domain we added in earlier! We will now create an organizational unit to out our admin account in. Right click on mydomain.com > New > Organizational Unit. I will name my unit "_ADMINS". Make sure to uncheck the "Protect container from accidental deletion", just so it's easier to delete if we need to. 
<p align="center">
<img width="750" alt="20 organizatinal unit" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/118134da-8a54-48a0-bbe8-9fa026d412f1">

We will now create a new user on the _ADMINS folder. Click on the newly formed "_ADMINS" organizational unit folder > New > User. Fill out the info with your name. For User login name use: a-[first initial][last name]. The "a-" signifies that this account is an administrative account.
<p align="center">
<img width="750" alt="21 new user in _admins" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/f6f44cdf-191a-4f0b-84b5-2bc8945bdf3f">

For the password, use the same password that we've used prior: Password1. Uncheck, "User must change password at next logon", and check "Password never expires". Since this is a lab environment we don't want to deal with the password until password policy selection. Lastly, click "Finish" to add user. You can notice that we have an account under _ADMINS. 

This account is not an domain administrator yet although it has the name of "a-pdeb". To make the user a domain admin, right click the name > Properties > Member of > Add. Type "domain admins" in the "Enter the object names to select (examples):" > Check Names. Domain Admins is now underlined. Finish up by clicking "OK". Apply the configurations and click OK again. The domain admin account is now made!
<p align="center">
<img width="750" alt="22 domain admins check names" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/c057d24e-3337-4235-a9bf-1483af87d7ef">

To use our domain admin account, sign out of the VM. When logging in, click "Other user". Enter the credentials you used to make the domain admin account. 
<p align="center">
<img width="750" alt="23 logging into admin account with a-" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/921835db-1251-49a7-967b-cf5e24e7f869">

Next up, we will install RAS/NAT (remote access server/network acceesss translation) on the domain controller. When we create our Windows 10 client, the NAS/RAT allows the client to be on the private virtual network but still have access to the interent through the domain controller. 

To set this up, we will go the Server dashboard > Add roles and features > Next > Role-based or feature based installation > Select the server we created (should be named "DC.mydomain.com") > Next > Select "Remote Access" > Next > Click next for Features and Remote Access > In Role Services select "Routing" > Add features > Next > Click next for Web Server Role (IIS) and Role Services > at Confirmation page click "Install". Install can take max 5 minutes. After installation close the window.
<p align="center">
<img width="750" alt="24 installing ras:nat" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/304ef446-287f-492c-bb20-ae87d8371615">

Next we will set up the routing and remote access. Go the Server dashboard > click on Tools on the top right hand side > Routing and Remote Access > Right click on "DC (local)" > Configure and Enable Routing and Remote Access > Next > Select NAT > Next > Select "INTERNET" NAT > Next > Finish. We can observe that the "DC (local)" icon is now green, this mean that the routing has been configured.
<p align="center">
<img width="750" alt="25 routing and remote access green light" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/d9d54d61-c851-41ad-b028-d75327f530ed">

Next step is to set up a DHCP server on our domain controller. DHCP will allow Windows 10 clients to get an IP address that will let them get on the internet and browse the internet. Go the Server dashboard > Add roles and features > Next > Role-based or feature based installation > Select the server we created (should be named "DC.mydomain.com") > Next > Select "DHCP Server" > Add features > Next > Click "Next" for Features and DHCP Server, Click "Install" at the Confirmation page. After installation you can close out of the window. 
<p align="center">
<img width="750" alt="26 installing dhcp" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/4af22734-e9d7-43ad-b2c1-ec74034c20a9">

The purpose of DHCP is to allow computers on the network to automatically get their IP addresses. We will create a scope that will give the IP addresses in this range: 172.16.0.100 with this subnet mask. Go the Server dashboard > click on Tools on the top right hand side > Select "DHCP" > When expanding "dc.mydomain.com" you will notice IPv4 and IPv6 are down (you can see they are red) > Expand IPv4 > Right click "IPv4" > New Scope....

The scope's name will be named what the IP range is. For the Scope's name I put: 172.16.0.100-200. There is no description > Next. 
<p align="center">
<img width="750" alt="27 scope name" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/757ff72f-96c5-4252-851e-851de4cc05ae">

For the DHCP Server configuration, we will use the start IP address as 172.16.0.100. End IP address will put as: 172.16.0.200. 
The length can be set to: 24. 
The subnet mask is set as: 255.255.255.0. > Next. 
<p align="center">
<img width="750" alt="28 start:end IP address" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/4fd8b5ae-7949-43aa-84bd-a22b1848eb36">

For Exclusions and Delays, we don't need to add anything here. This page is mainly just to state what IP addresses we should exclude. Click Next. 

For Lease Duration, I will set it to 8 days. Lease duration is how long a computer can have that IP address before it needs to be refreshed. The time you input will depend how much you use it. Usually for public spaces, you would want to limit the lease to 2 hours, but since this is a lab I will set it for 8 days (this is more than enough time for me to use the VM). Then click Next. 
<p align="center">
<img width="750" alt="29 lease time" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/c8085ac3-1547-4600-bd27-098cc987e3df">

For Configuration the DHCP Server, this part in the setup is asking if we want to configure DHCP options. This means we ask the clients, "Do you want to chose which server you want to use for DNS or gateway?". For this lab, we want to configure these options so we can have access to the internet. We will select "Yes, I want to configure these options now". 
<p align="center">
<img width="750" alt="30 dhcp options" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/8f392c7f-833d-4037-9932-46363da29652">

For the Router (Default Gateway) page, we will enter the Domain Controller's IP address: 172.16.0.1, then click "Add". Since we already configured NAT on the domain controller and the domain controller has routing configured as well, becuase of this the clients are going to use this Internal NIC of the domain controller as their default gateway/router.
<p align="center">
<img width="750" alt="31 router deafult gateway" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/f2d3a8fd-09ba-4eeb-85b7-fda1c4daa61a">

Next we will configure the Domain name and DNS server. When installing active directory on the domain controller it automatically installs DNS. Due to that, we will use the domain controller as out DNS server. We have to do this in order to join the domain. For parent domain it should say: mydomain.com. The IP address should also also be listed below. 
<p align="center">
<img width="750" alt="32 domain name" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/5c288724-f4d9-4b1a-9893-f57affd0ffd4">

Click Next when getting to the WINS Servers page. This page isn't applicibale for this lab. 

Finally, when getting to the Activate Scope page, select "Yes, I want to activate this scope now". And afterwards you can click "Finish". 
<p align="center">
<img width="750" alt="33 activate scope" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/f3d5a019-2e60-47f7-b1b2-abcf088f6a8e">

Right click the DHCP server ("dc.mydomain.com") and click "Authorize". Right click the DHCP server again and click "Refresh".
<p align="center">
<img width="750" alt="34 authorize dhcp" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/66e8eb63-ad29-40ec-9271-5e1ec2cf6798">

After refreshing, you should see the IPv4 and IPv6 trun to a green color indicating that both are working and configured. The DNS is sucessfully set up now.
<p align="center">
<img width="305" alt="35 green ipv4 and ipv6" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/0ee63e64-1172-4667-b63f-39df68409f57">

Next, we have to make a configuration that lets us browse the internet from the domain controller. To do this, go the Server dashboard > Configure this local server. Locate IE Enhanced Security Configuration. This should be on already. Click "On" > turn off for both admins and users. If these were on, before every page loads, a prompt will show up asking "Are you sure you want to load this page?". This cnofiguration will prevent this from occuring. 
<p align="center">
<img width="750" alt="36 ie enhanced security config" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/009246b7-525a-42e3-b552-2fe507e6b3b3">

Next up, we will use a powershell script to create all the users. We will do this by opening a link and downloading the zip file to retrieve the users. Open Internet Explorer and paste this link: [https://github.com/joshmadakor1/AD_PS/archive/master.zip](https://github.com/joshmadakor1/AD_PS/archive/master.zip). This link will prompt to download the zip file. Save and open the file. I saved this file to my desktop. 
<p align="center">
<img width="750" alt="37 opening link" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/531944f7-3de2-417f-a5f2-280530adca10">

Within the zip file, there are two powershell scripts, one text file, and one gitignore file. Firstly, open the text file (called "names"). This text file has a thousand randomized names that was retrieved by using a name generator. At the very top of the text file, enter your name as well (or a fake name if you choose) and remember to save the file! We will use this file to programtically create all of these users and for ourself too.
<p align="center">
<img width="750" alt="38 name at top" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/f5a41658-2d62-41af-9b4e-dcf23df4f006">

Next click Start > Windows PowerShell > run Windows PowerShell ISE as administrator. Within Windows PowerShell ISE, open the powershell script "1_CREATE_USERS". In the console pane, type "Set-ExecutionPolicy Unrestricted" then press enter on the keyboard. You will get a prompt after running, click "Yes to All".
<p align="center">
<img width="750" alt="39 yes to all" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/bc926293-d0b9-4308-9521-8f048b356e0c">

Next we will type within the console pane "cd c:\Users\a-pdeb\Desktop\AD_PS-master" and press enter on the keyboard. 
<p align="center">
<img width="750" alt="40 location of adps" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/9ece2228-e59e-4ecd-a3bb-67fe6a041f07">

Next, at the toolbar on PowerShell, click the Run Script button. Once you press that button, it will import the active directory module and the script will run to create the users. This will take 2-3 minutes to complete running.
<p align="center">
<img width="750" alt="41 creating users" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/0133aaa0-b523-4c9e-a149-7333428a9536">

Additionally, you can also check out active directory in the meantime by going to Start > Windows Administrative Tools > Active Directory Users and Computers > expand mydomain.com > right click mydomain.com > Refresh. After refreshing, you can see that we have a "_USERS" folder with a bunch of users inside. This is just for us to visualize that when running the script, it will create all of the users from the text file. 
<p align="center">
<img width="750" alt="42 _users folder in active dir" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/88ca8455-6086-414e-a2e9-e621fc68f0f3">

To find your specfic name within mydomain.com, right click mydomain.com > Find. In the name section type your last name that you used for this lab to see if your name (or choice of name) shows up! 
<p align="center">
<img width="750" alt="43 finding my name" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/f60ffc69-5c7a-4a35-9b1b-ce3eed145596">

At this point within the lab, we have our users created and our lab environment is set up! The last thing to do within our lab is to create a Windows 10 VM in VirtualBox. This VM will use an Internal NIC that will get its IP address from our DHCP server that we configured earlier.

Go to Virtual Box to create a new VM. I will name this VM "Windows 10 Client" (you can name is whatever you'd like). The version of the VM will be: Windows 10 (64-bit). For memory size, I chose 2048MB (2GB) and 1 CPU; the amount you input depends on how much RAM the system you're running your machine on. I put 20 GB for the Virtual Hard Disk Size.
<p align="center">
<img width="750" alt="44 windows 10 client 1 vm summary" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/3518cc98-a6c8-47c4-8c4d-998d6988c054">

Before we turn on the newly made VM we have to configure the settings. Right click to go to Settings. Go to General > Advanced > change "Shared Clipboard" and "Drag'n'Drop" as Bidirectional. This helps with the ease of copying and pasting within the VM. 

Next, go to Settings > Network > Adapter 1 > changed "Attached to:" to Internal Network. Click "OK" and we've finsihed setting up the settings for this VM!

Double click the newly made VM. You should get a prompt asking to select a virtual optical disk drive. Locate the Windows 10 ISO you downloaded in the beginning of the lab and select that as your disk drive. Selecting this option will prompt you to begin the installation of Windows 10.

During the installation process, select the language, time format, and keyboard to your specfications and then click "Install now". When getting to the Activate Windows page, select "I don't have a product key" that's located at the bottom right corner. Then select "Windows 10 Pro" as the operating system you want to install. Then click Next. 
<p align="center">
<img width="750" alt="45 windows 10 windows 10 pro" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/d4dc4523-d766-4473-9416-376d9e16b542">

The type of installation we want is "Custom: Install Windows only (advanced)", this selction will format the hard drive and install from scratch. During the installation it might take 5-10 minutes to install. The computer will reboot mutiple times. 
<p align="center">
<img width="750" alt="46 installing windows 10 vm" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/b9dc4d81-f2fe-4d01-a4d7-ca627d9334b9">

When prompted to "Press any key to boot from CD or DVD", ignore that and the installation will continue normally. Afterwards, you will be prompted to select the region you're in and the type of keyboard. For addition setting configurations, select that you're using this for home use and select "Limited experience" when asking to to connect to Microsoft. For the user's name, I chose "user". 
<p align="center">
<img width="750" alt="47 user" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/3f892563-ac80-49d4-b24c-2cd8471a25a2">

We do not need a password for this VM, so click Next. For privacy settings, I change my options from Yes to No. The computer will complete setting up (can take 2-5 minutes). Then the computer will land you to the welcome screen of Windows 10. 

First thing you'll want to do for the Windows 10 VM is to make sure that the interent is working. To do this open Command Prompt and type "ipconfig". There should be an IPv4 address, a subnet mask, and a default gateway. This confirms that the internet works! We can even test it by pinging. Write "ping www.google.com" to the command prompt and recieve the stats for that ping. 
<p align="center">
<img width="750" alt="48 checking internet" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/d8a148ae-659b-47ec-886e-26b06345e812">

This ping concludes that we have connectivity all the way to the default gateway (the domain controller). We also know that the domain controller is properly natting it and forwarding it out to the internet so the ping can properly come back to us as an echo reply.

Additionally, we can sucessfully ping mydomain.com on the command prompt as well. 
<p align="center">
<img width="750" alt="49 pinging mydomain com" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/bb272bfe-f139-48b0-ba21-4ad3cea091c1">

Next we will rename the PC and join the domain in one step. Start > Settings > System > About > Rename this PC (advanced) (you will find this option if you scroll down on the About page). Once you get to system properties, click "Change". You will then rename your computer here. I named mine, "Windows 10 CLIENT". Also, select "Domain" under Member of. Here you will type, "mydomain.com", and the click "OK". Next, you'll have to join an account to the domain. This brings us back to when we created a domain admin account
<p align="center">
<img width="750" alt="50 domain and renaming pc" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/e55895a3-6a48-47ae-b5e9-3865e8c24762">

Next, you'll have to join an account to the domain. This brings us back to when we created a domain admin account. I entered my admin credentials and I logged in! You shuld get a message that states "Welcome to the mydomain.com domain." Afterwards, it will prompt you to restart your computer. 
<p align="center">
<img width="750" alt="51 my credentials" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/d2197712-dc3e-4d47-a93c-90332d72b7e0">

Going back to the domain controller VM, open DHCP. Expand the scpoe we made for IPv4 and select Address Leases. We can examine that we have one lease from our client computer. When we created our client computer and joined it to the network, it reached out to the DHCP server automatically and requested an address. Then the DHCP server gave it that address. So now we have lease in here. For an example, if you go to your work DHCP server and you see the leases' folder, you'll probably see hundreds of addresses per scope.
<p align="center">
<img width="1=750" alt="52 leases" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/973a60ee-3d71-42a7-8a0d-9ab179a22c9e">

Next, go to Active Directory Users and Computers on the domain controller VM. Under Computers, you can see that after we joined the client computer to the domain, the client automatically placed itself here. This visulizes that the computer is part of the domain.
<p align="center">
<img width="750" alt="53 client comp" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/2bfce763-c73d-4278-8e4a-99996f5b00d5">

Next we will go back to our Windows 10 VM. Login to this VM by selecting "Other user". We can use one of the user accounts we created earlier. Since this is the first time we're signing in, it is taking time to create our profile. Essentially, we created a mini corporate network with the bulk users. You can imagine this process as if we got hired at a company and now we're signing in the computer with the credentials corporate gave you. 
<p align="center">
<img width="750" alt="54 logging in with pdeb" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/12ba5399-1c52-4cd1-9370-2d591fcd8f69">

You can visualize this furthur by opening the Command Prompt and typing the command "whoami". It will show your username and that you're a member of mydomain. 
<p align="center">
<img width="750" alt="55 whoami" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/5e11d537-fdbd-473b-856d-28398935a9f6">

We have sucessfully completed creating an active directory with over a thousand users!
