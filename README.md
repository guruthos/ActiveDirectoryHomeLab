<h1>Active Directory Home Lab</h1>

<h2>Description</h2>
In this project, we will create an Active Directory home lab environment with virtual machines (VM). Setting up this home lab is a great way to learn about Active Directory, virtual machines, Windows Domains, Powershell, and how they all work together. Once the home lab is configured, you will have a wonderful environment to take your learning even deeper! I recommend this lab to anyone interested in a career in IT. 

<h2>Languages and Applications Used</h2>

- <b>PowerShell</b> 
- <b>Active Directory</b>
- <b>Oracle VirtualBox</b>

<h2>Environments Used </h2>

- <b>Windows 10 Professional</b>
- <b>Windows Server 2019</b>

<h2>Project instructions</h2>

<h3>Download the latest version of Virtual Box and the Extension Pack</h3>

Go to the <a href="https://www.virtualbox.org/wiki/Downloads">VirtualBox Downloads Page</a> and download both the main host package for your system and the newest Extension Pack.

<h3>Download the ISOs for Window 10 and Windows Server 2019</h3>

You will need to download the media creation tool from the <a href="https://www.microsoft.com/en-us/software-download/windows10">Windows 10 Download Page</a> to create a Windows 10 ISO file. Once you download the media creation tool, follow the instructions on the download page to create installation media and choose "ISO file" when given the choice.

To download Windows Server 2019, go to the <a href="https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019">Windows Server 2019 Download Page</a> and choose the 64-bit ISO download for your language.

These ISOs are individual files, keep these where you will remember them as you will need to point to them later.

<h3>Create the Domain Controller virtual machine and install Windows Server 2019</h3>
Once VB is installed, open and click “New” to create a new VM. Name it “Domain Controller” and select the Windows Server 2019 ISO. Make sure “Skip unattended installation” is checked and click Next.

<p align="center"><br/>
VirtualBox ISO selection and naming of Virtual Machine <br/>
<img src="https://i.imgur.com/BrYBpHd.png" height="75%" width="75%" alt="VirtualBox ISO selection and naming of VM"/></p>

Use the slider or incremental controls to allocate the amount of RAM and CPUs that this VM will use. This will depend on the system you have, and will affect how fast the VM runs. I would recommend allocating at least 4GB (4096 MB) of RAM and at least 2 CPUs for this VM. Click Next.

Make sure that the “Create a Virtual Hard Disk Now” radio button is selected and allocate the amount of hard drive space for this VM to use. You need to reserve enough to install the operating system on, and then some. I would recommend allocating at least 30GB for this VM and 50GB if you have the space to spare. Click Next, then click Finish. You should now see the VM named Domain Controller in the left panel of the VirtualBox window.

Before starting this VM up, click on Settings. Under the General section, click on the “Advanced” tab and change both the “Shared Clipboard” and “Drag‘n’Drop” properties to “Bidirectional.” This will allow us to drag files and use the same clipboard from our VMs. Next, click on “Network.” Click on the “Adapter 2” tab and click “Enable Network Adapter” to check the box. Using the “Attached to:” drop down menu, select “Internal Network” then click OK.

<p align="center"><br/>
Enabling Network Adapter 2 in VirtualBox <br/>
<img src="https://i.imgur.com/XkyZ1UB.png" height="60%" width="60%" alt="VirtualBox Settings Enable Network Adapter 2"/></p>

Start the Domain Controller VM by double clicking it or selecting it and clicking Start. The Windows Server 2019 setup program should automatically begin if we selected the correct ISO file when we created this VM. Click through the default options and click “Install Now” to proceed. You will come to a selection screen where you are asked to choose a version of Windows Server 2019. Select the “Standard Evaluation (Desktop Experience)” option and click Next. Accept the license terms and click Next. Click on “Custom: Install Windows only (advanced) and then make sure the correct drive is selected (there should only be the one option showing the Virtual Hard Disk we created earlier) and click Next. Installation should begin in earnest and will take a few minutes at least. During this process, the VM will restart. When it does, the screen will show the message, “Press any key to boot from CD or DVD…” but doing so will restart the setup process from the beginning - so don’t press any keys when you see this message!

Eventually, after an automatic restart or two, you will be asked to set a password for the default Administrator account. For the purposes of this lab, and to make it easier to learn, I recommend using “Password1234” as the password for everything in this lab. You never want to do this in real life, but we will have to login many times as different users and keeping the same password for all those users will speed things up. Also, when we generate 1000 users with a Powershell script, all those users will have “Password1234” as their default password since that’s hard coded into the script. Once you have entered the Administrator password, click Finish.

Now we can log in! At this point, you may notice that pressing Ctrl+Alt+Delete does not unlock the screen or seem to do anything! This is a quirk with VirtualBox. You can successfully log in by clicking “Input” at the top of the window, then “Keyboard,” then “Insert Ctrl-Alt-Delete.” Alternatively, you can hold the Host key (shown at the bottom right corner of the VirtualBox window, and usually Right Ctrl by default) and Delete as a shortcut to this command. Use whichever method you prefer and log in using the Administrator password.

After logging in, Windows will ask if you want your PC to be discoverable on the network. Click Yes. You will also see that Server Manager automatically starts when you log in, and it may take up the whole screen. Minimize or close that and any other windows that are open. On the VirtualBox menu, click “Devices,” then click “Insert Guest Additions CD image….” Then, from within the Domain Controller VM, open File Explorer and then click on “This PC” in the left panel. Under “Devices and drives,” double-click on the CD drive which should say “VirtualBox Guest Additions.” This should open the contents of the CD to reveal several files. Locate the executable application with “-amd64” on the end of the filename and double-click it to start the setup. Click through until you click “Install,” leaving all default values as they are. Click Finish. Once the VM restarts, log in as Administrator again.

<p align="center"><br/>
Selecting the Guest Additions file <br/>
<img src="https://i.imgur.com/jvCSrOv.png" height="75%" width="75%" alt="VirtualBox Guest Additions File Selection"/></p>

Right-click on the Network icon in the system tray at the bottom-right corner of the Domain Controller’s desktop and click “Open Network & Internet Settings”, then click on “Change Adapter Options.” In the Network Connections window that opens, you should see two connection objects, probably named “Ethernet” and “Ethernet 2.” Right-click on one of the connections and click “Status” then click “Details.” In the Network Connection Details window, look for the property called “IPv4 Address.” If you cannot find this property, look for its alternative, “Autoconfiguration IPv4 Address.” Remember which of the two properties this connection had, and click “Close” twice to return to the Network Connections. Do the same thing for the other connection and make a note of whether it had the “IPv4 Address” or “Autoconfiguration IPv4 Address” properties. One connection should have the one property, and the other connection should have the other, but none of them should have both properties. Right-click on the connection that has the “IPv4 Address” property and click “Rename.” Change the name of this connection to “Internet” and press Enter. Then rename the other connection “Internal.”

Once both connections are renamed, right-click on the “Internal” connection and click “Properties.” Select “Internet Protocol Version 4 (TCP/IPv4)” and click “Properties.” Click on the “Use the following IP address” radio button and fill in the following: “IP address” is 172.16.0.1, “Subnet mask” is 255.255.255.0, and “Preferred DNS server” is 127.0.0.1. Click OK, then click Close. Close any open windows.

<p align="center"><br/>
IPv4 properties for "Internal" <br/>
<img src="https://i.imgur.com/D542B0j.png" height="65%" width="65%" alt="Internal Connection's IPv4 Properties"/></p>

Right-click on the Start Button and click “System,” then click the “Rename this PC” button in the right panel (you may have to scroll down a little). Name it “DC” for Domain Controller and click Next. Then click “Restart Now.”

<h3>Installing Active Directory and Creating a New Domain</h3>
Once the Domain Controller restarts, login as Administrator and open Server Manager if it doesn’t automatically open. Within Server Manager, click on “Add roles and features” then click next through all the default options until you get to the “Select server roles” section. Check the box for “Active Directory Domain Services” and then click “Add Features” on the confirmation screen. Then click Next through the rest of the options until you can click on “Install.” Click “Install” and wait until it’s finished. Then click “Close” to finish.

Within the Server Manager Dashboard, you should see a flag icon with an exclamation point near it - click on it to open a pop-out menu. Click on “Promote this server to a domain controller” then select the “Add a new forest” radio button. Type in “mydomain.com” and click Next. On the next screen, you will notice that the DSRM password fields require an entry here. DSRM is beyond the scope of this lab, but you still need to add something to move to the next screen. Add the same password you’re using for the rest of this lab just to keep it simple and click Next through the defaults on each screen until you reach the “Prerequisites Check” screen. Once the prerequisite checks are passed, click “Install.” This may take a little while and the VM will restart as part of this process. Once the VM restarts and reaches the log in screen, you will notice that you are now logging into the new domain we created.

<p align="center"><br/>
Server Manager Post-Deployment Configuration Flag <br/>
<img src="https://i.imgur.com/zWhz5Tp.png" height="70%" width="70%" alt="Server Manager Post-Deployment Configuration Flag"/></p>

<h3>Create a new Administrator account to use instead of the default</h3>
Open the start menu, navigate to “Windows Administration Tools” then click on “Active Directory Users and Computers.” Right-click on “mydomain.com” in the left panel, click “New,” then click “Organizational Unit.” For the name, type “_ADMINS” and uncheck the “Protect container from accidental deletion” checkbox. Click OK. Now you should see an “_ADMINS” object in the left panel (expand the mydomain.com object if you don’t see it). Right-click it and click “New,” then “User.” Fill out your first and last name in the applicable fields, but in the “User logon name” field, enter “a-” followed by your first name initial and last name. For example, Eric Burton’s logon name should be “a-eburton". This prefix is a naming convention used to indicate this account is an Admin account.

<p align="center"><br/>
Adding the Administrator user account details <br/>
<img src="https://i.imgur.com/quwPun6.png" height="60%" width="60%" alt="Adding the Administrator user account details"/></p>

Once you have filled out the appropriate fields, click Next. Add the same password you have been using for the entire lab and check the “Password never expires” checkbox while unchecking the “User must change password at next logon” checkbox. Click Next, then Finish. You should now see the new user show up in the right panel when you select the “_ADMINS” OU in the left panel. Right-click the new user and click “Properties.” Then click the “Member Of” tab and “Add.” Type “domain admins” and click “Check Names.” If the text you just typed was changed to “Domain Admins,” click OK. Then click Apply, then OK again. That user is now an Admin.

<p align="center"><br/>
Adding the new user to the Domain Admins group using the object picker <br/>
<img src="https://i.imgur.com/o03TGpA.png" height="60%" width="60%" alt="Choosing the Domain Admins object in the object picker"/></p>

<h3>Signing in with new Admin account</h3>
Sign out of your current account by right-clicking the start button, selecting “Shut down or sign out,” then clicking “Sign out.” When you see the password field on the login screen, click on “Other user” on the bottom left of the screen. Now login with the user name of the Admin account we just created (with the “a-” prefix) and the corresponding password.

<h3>Installing Remote Access Server and Network Address Translation on Domain Controller</h3>
Open Server Manager and click on “Add roles and features.” Click Next through all the default options until you reach “Select server roles.” Check the box for “Remote Access” then click Next through more default options until you reach “Select role services.” Check the “Routing” checkbox and then “Add Features.” You will notice that this will automatically check the “DirectAccess and VPN (RAS) checkbox as well. Click Next through the remaining options and click “Install” when able. Click Close when the installation completes.

On the top left of the Server Manager Dashboard, click on “Tools” then click “Routing and Remote Access.” Right-click on the local server object in the left panel and select “Configure and Enable Routing and Remote Access.”

<p align="center"><br/>
Configure and Enable Routing and Remote Access menu <br/>
<img src="https://i.imgur.com/0w77MUL.png" height="70%" width="70%" alt="Configure and Enable Routing and Remote Access menu"/></p>

Click Next, then select the “Network address translation (NAT) radio button and click Next. On the next screen, make sure the radio button for “Use this public interface to connect to the Internet” is selected and not greyed out. If it is greyed out and unselectable, click Cancel and restart the Routing and Remote Access Setup Wizard (go back to the beginning of this paragraph’s directions) and that should fix the issue. From the two interfaces available for selection, select the one that connects to the Internet (this one should be named “Internet” to distinguish it). Click Next, then Finish.

<p align="center"><br/>
Routing and Remote Access Public Interface greyed out (cancel wizard and start over) <br/>
<img src="https://i.imgur.com/TquOK3h.png" height="60%" width="60%" alt="Routing and Remote Access Public Interface greyed out (cancel wizard and start over)"/></p>

<p align="center"><br/>
Routing and Remote Access Public Interface selectable (success!) <br/>
<img src="https://i.imgur.com/1vKiiD6.png" height="60%" width="60%" alt="Routing and Remote Access Public Interface selectable (success!)"/></p>

<h3>Set up DHCP on Domain Controller</h3>
Open Server Manager and click on “Add roles and features” again. Once again, click Next until you reach “Select server roles.” Check the “DHCP Server” checkbox, then click “Add Features,” then click Next through the remainder of the screens. Click Install, then click Close. Back in Server Manager, click “Tools” and then click “DHCP.” Once the DHCP window opens, you should see the server object in the left panel. Click on the arrow to the left of it to reveal the IPv4 and IPv6 objects. Right-click on the IPv4 object and click “New Scope” to begin the New Scope Wizard. Click Next, then type “172.16.0.100-200” and click Next. Enter 172.16.0.100 for the “Start IP address,” 172.16.0.200 for the “End IP address,” and 255.255.255.0 for the “Subnet mask.” 

<p align="center"><br/>
Setting the scope's IP address range <br/>
<img src="https://i.imgur.com/P9YBkxz.png" height="60%" width="60%" alt="Setting the scope's IP address range"/></p>

Click Next through the rest of the default options until you reach the “Configure DHCP Options” screen. Make sure “Yes, I want to configure these options now” is selected and click Next. On the “Router (Default Gateway) screen, enter the IP address of the Domain Controller’s “Internal” IP address (we set this to 172.16.0.1 earlier) and click Add, then click Next through the remaining default options and click Finish.

Back in the DHCP window, right-click on the server object (dc.mydomain.com) and click “Authorize.” Then right-click it again and click “Refresh.” Now the icons for the IPv4 and IPv6 objects should have green check marks. Click the arrow next to the IPv4 object to expand it and you should see the new Scope object that we just created.

<p align="center"><br/>
DHCP window after authorization, refreshed and expanded <br/>
<img src="https://i.imgur.com/BBcBnvY.png" height="75%" width="75%" alt="DHCP window after authorization, refreshed and expanded"/></p>

<h3>Download and Run a PowerShell Script to add Users</h3>
You can use Active Directory to add users one at a time, similar to the way we created the admin account earlier, but that is time-consuming. Instead of that method, we can use a PowerShell script that will automatically add 1000 or so users. 

In order to do this, you will have to download or copy two files from this Github repository within your main PC (not from within the VM): CreateADUsers.ps1 and NamesList.txt. CreateADUsers.ps1 is the PowerShell script that will do all the hard work of adding fake users. NamesList.txt is simply a list of fake names from which the script will pull. You can find both of these files on the main page of this repository or you can click the following links to go straight there: 

<a href=https://github.com/guruthos/ActiveDirectoryHomeLab/blob/main/CreateADUsers.ps1>PowerShell Script Location</a>

Once you arrive at the Github page where the file is located, you may notice there isn’t a download button, per se. Instead, you can right-click on the “Raw” button and click “Save Link As” to bring up the file save window. To make this easy, make sure the file is set to save to your desktop and leave the “File name” and “Save as type” options as they are before clicking “Save.” The file should now be on your desktop. Now do the same for the following NamesList.txt file and save it on your desktop as well. It is important that these files are saved in the same location.

<a href=https://github.com/guruthos/ActiveDirectoryHomeLab/blob/main/NamesList.txt>List of Names Location</a>

Once both files are downloaded to your PC’s desktop, you can drag both files over to the VM’s desktop window (This is why we set up the “Drag ‘n’ Drop” property to bidirectional). Once the files are successfully copied to the VM’s desktop, open Windows PowerShell ISE as an Administrator (to do this, right-click on the Windows PowerShell ISE icon and select “More” then “Run as Administrator”. Then click “Yes” on the User Account Control dialog that pops up). From within PowerShell ISE, click “File” then “Open” then locate and select the CreateADUsers.ps1 file and click “Open.” Now you should see the file’s code in the top half of the PowerShell window and a command line prompt in the bottom half. Click your cursor in the bottom half of the PowerShell window so you can type on the command line. From here, type “Set-ExecutionPolicy Unrestricted” and hit enter. Click “Yes to All” when the “Execution Policy Change” dialog pops up. This is a security feature designed to warn you of the possible danger of running scripts as they may contain malicious code. If it worked correctly, you should see the same command line prompt as before with no messages (error or otherwise).

<p align="center"><br/>
PowerShell ISE Set-ExecutionPolicy Unrestricted <br/>
<img src="https://i.imgur.com/QA5H7VU.png" height="60%" width="60%" alt="PowerShell ISE Set-ExecutionPolicy Unrestricted"/></p>

Now you need to change the working directory to the folder where both the script and the list of names files are located: the desktop. From the command line, type “cd C:\Users\a-eburton\Desktop but replace “a-eburton” with your special admin account you created earlier (the account with the “a-” prefix and your first name’s initial and last name). When you are typing this out, you may notice PowerShell’s context lookup feature try and help you select the correct items. Once you have it typed correctly, hit the enter key and the command line prompt should change to indicate that the working directory has changed to your desktop folder.

<p align="center"><br/>
Changing the working directory (remember to replace "a-eburton" with your account name) <br/>
<img src="https://i.imgur.com/vIzHxNv.png" height="60%" width="60%" alt="Changing the working directory (remember to replace "a-eburton" with your account name)"/></p>

Type “dir” and press enter to show the contents of the folder you have navigated to. You should see both the “CreateADUsers.ps1” and “NamesList.txt” files in the output. Now, once you have unrestricted the execution policy and changed directories to the VM’s desktop, you can run the script. Click on the green arrow in PowerShell ISE or press F5 on your keyboard to run it. You should see users being created as they scroll down the window. Wait until the script finishes and returns to the command prompt. To confirm these users were created correctly, you can open “Active Directory Users and Computers,” select the domain object in the left panel and double-click on the _USERS organizational unit and they should display in the right panel.

<h3>Creating the Windows 10 Client VM</h3>
In VirtualBox, create a new Vm using the Windows 10 ISO and name it something to distinguish it as a client machine. You can use the same hardware allocations as you did earlier on the Domain Controller VM. Once the VM is created, right-click on it and open settings. Click on Network, and change Adapter 1’s “Attached to” property to “Internal Network.” Click OK.

Start the new Client VM to begin the Windows 10 setup. When the “Activate Windows” screen appears, click on “I don’t have a product key.” Then select Windows 10 Pro from the operating system selections and click Next. Select “Custom Install” when given the option then click Next until installation begins. Let the VM restart automatically and remember to not press a key to boot from CD/DVD. Eventually the setup program will ask you for more information. Select the appropriate options and skip any skippable screens. When setup comes to the Network screen, if it asks if you want to connect to a network, click on “I don’t have Internet.” When prompted, choose “Personal Use” over “Organizational,” choose “offline account” over adding a Microsoft account, and choose “Limited Experience” when setup again tries to convince you to make an account.

Enter a default username when asked, “Who’s going to use this PC?” I entered “user” and left the password option on the next screen blank. When you get to “Choose privacy settings for your device,” toggle everything to off (you may have to scroll down, there are so many options to toggle). Skip every screen that you can from here on out without accepting any of Microsoft’s “personalization” services. After this, the setup process should enter its final leg and eventually Windows 10 will open.

Right-click on the Start button and click “System.” Scroll down and click on “Rename this PC (advanced)” then click “Change.”

<p align="center"><br/>
Locating Rename this PC (advanced) <br />
<img src="https://i.imgur.com/ZM6kuU6.png" height="60%" width="60%" alt="Locating Rename this PC (advanced)"/></p>

Type the name of your client VM (I used CLIENT1), then select the Domain radio button in the “Member of” section and type “mydomain.com” and click OK. In the security window that pops up, enter the admin account credentials (the account with the “a-” prefix) and click OK. After a moment or two, you should see a message box appear that confirms joining the domain was successful. Allow the client VM to restart.

The client VM should now be joined to the domain and the Domain Controller VM should now show the client VM as a member of the domain. You can check this by opening Server Manager on the Domain Controller VM, clicking on “Tools” then “DHCP.” Expand the domain object, then expand the IPv4 object, then expand the Scope object inside that. Click on “Address Leases” and you should see an entry from the client VM. When the client joined the domain, it requested and received an IP address from the DHCP service running on the Domain Controller VM. Notice that the IP address given is within the range of addresses as defined earlier in the scope.

<p align="center"><br/>
Successful assignment of IP address from Domain Controller <br />
<img src="https://i.imgur.com/9nEV1MI.png" height="75%" width="75%" alt="Successful assignment of IP address from Domain Controller"/></p>

Congratulations! You now have a working Active Directory Home Lab running on your PC! You can test out the other users login credentials by logging in as them and seeing how Active Directory works with different users on the same machine. Use this Home Lab to learn more about Active Directory - try deleting and adding users, restricting access, forcing a change password policy, etc.

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
