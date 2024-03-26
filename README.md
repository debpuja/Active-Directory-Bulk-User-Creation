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

I will then configure the Domain Controller VM by going into Settings and adding 2 NICs (one for, NIC (Internet), and other for NIC (Internal)). 
<p align="center">
<img width="750" alt="5 2 NICS" src="https://github.com/debpuja/Active-Directory-Bulk-User-Creation/assets/163590363/f8b6b3b3-bf1d-4e35-a240-54f455a140bb">

After powering on the machine, I selected the Windows 2019 Server as the optical drive. Selecting this option will prompt you to begin the installation of Windows Server 2019. I selected the "Windows Server 2019 Standard Evaluation (Desktop Expereince)" since creating the active directory will be a lot easier with a GUI. The type of installation we want is "Custom: Install Windows only (advanced)", this selction will format the hard drive and instal from scratch.

During the installation it might take 10-15 minutes to instal. The server will reboot mutiple times. When prompted to "Press any key to boot from CD or DVD", ignore that and the installation will continue normally. After choosing the preferences, I was prompted to give a password for the default admin account. The password I chose to use (and will use for all passwords for this lab is: Password1). 



I set up the domain controller first within VirtualBox. I configured NAT on there. 

I configured user home directories

I ran the scripts for user creations suing Powershell Automation.

I configured DHCP srvices.

I shared folders wihtin the user group. 

I also configured group policy settings. 

PowerShell Automation: Josh demonstrates how to efficiently add over 1,000 users to the Active Directory using PowerShell scripts. This automation streamlines the process and is essential for managing large user bases.
