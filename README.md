<h1>Active Directory Home Lab</h1>

<h2>Description</h2>
In this project, we will create an Active Directory home lab environment with virtual machines (VM). Setting up this home lab is a great way to learn about Active Directory, virtual machines, Windows Domains, Powershell, and how they all work together. Once the home lab is configured, you will have a wonderful environment to take your learning even deeper! I recommend this lab to anyone interested in a career in IT. 
<br />


<h2>Languages and Applications Used</h2>

- <b>PowerShell</b> 
- <b>Active Directory</b>
- <b>Oracle VirtualBox</b>

<h2>Environments Used </h2>

- <b>Windows 10 Professional</b>
- <b>Windows Server 2019</b>

<h2>Project instructions</h2>

Download and install Virtual Box latest version and Guest Additions (I made this with version 7.0) LINK <br/>
Download/Register the ISOs for Window 10 and Windows Server 2019 LINK
These ISOs are individual files, keep these where you will remember them as you will need to point to them later. <br/>

<h3>Create the Domain Controller virtual machine and install Windows Server 2019 on it</h3>
Once VB is installed, open and click “New” to create a new VM. Name it “Domain Controller” and select the Windows Server 2019 ISO. Make sure “Skip unattended installation” is checked and click Next.

Use the slider or incremental controls to allocate the amount of RAM and CPUs that this VM will use. This will depend on the system you have, and will affect how fast the VM runs. I would recommend allocating at least 4GB (4096 MB) of RAM and at least 2 CPUs for this VM. Click Next.

Make sure that the “Create a Virtual Hard Disk Now” radio button is selected and allocate the amount of hard drive space for this VM to use. You need to reserve enough to install the operating system on, and then some. I would recommend allocating at least 30GB for this VM and 50GB if you have the space to spare. Click Next, then click Finish. You should now see the VM named Domain Controller in the left panel of the VirtualBox window.

Before starting this VM up, click on Settings. Under the General section, click on the “Advanced” tab and change both the “Shared Clipboard” and “Drag‘n’Drop” properties to “Bidirectional.” This will allow us to drag files and use the same clipboard from our VMs. Next, click on “Network.” Click on the “Adapter 2” tab and click “Enable Network Adapter” to check the box. Using the “Attached to:” drop down menu, select “Internal Network” then click OK.

Start the Domain Controller VM by double clicking it or selecting it and clicking Start. The Windows Server 2019 setup program should automatically begin if we selected the correct ISO file when we created this VM. Click through the default options and click “Install Now” to proceed. You will come to a selection screen where you are asked to choose a version of Windows Server 2019. Select the “Standard Evaluation (Desktop Experience)” option and click Next. Accept the license terms and click Next. Click on “Custom: Install Windows only (advanced) and then make sure the correct drive is selected (there should only be the one option showing the Virtual Hard Disk we created earlier) and click Next. Installation should begin in earnest and will take a few minutes at least. During this process, the VM will restart. When it does, the screen will show the message, “Press any key to boot from CD or DVD…” but doing so will restart the setup process from the beginning - so don’t press any keys when you see this message!

Eventually, after an automatic restart or two, you will be asked to set a password for the default Administrator account. For the purposes of this lab, and to make it easier to learn, I recommend using “Password1234” as the password for everything in this lab. You never want to do this in real life, but we will have to login many times as different users and keeping the same password for all those users will speed things up. Also, when we generate 1000 users with a Powershell script, all those users will have “Password1234” as their default password since that’s hard coded into the script. Once you have entered the Administrator password, click Finish.

Now we can log in! At this point, you may notice that pressing Ctrl+Alt+Delete does not unlock the screen or seem to do anything! This is a quirk with VirtualBox. You can successfully log in by clicking “Input” at the top of the window, then “Keyboard,” then “Insert Ctrl-Alt-Delete.” Alternatively, you can hold the Host key (shown at the bottom right corner of the VirtualBox window, and usually Right Ctrl by default) and Delete as a shortcut to this command. Use whichever method you prefer and log in using the Administrator password.

After logging in, Windows will ask if you want your PC to be discoverable on the network. Click Yes. You will also see that Server Manager automatically starts when you log in, and it may take up the whole screen. Minimize or close that and any other windows that are open. On the VirtualBox menu, click “Devices,” then click “Insert Guest Additions CD image….” Then, from within the Domain Controller VM, open File Explorer and then click on “This PC” in the left panel. Under “Devices and drives,” double-click on the CD drive which should say “VirtualBox Guest Additions.” This should open the contents of the CD to reveal several files. Locate the executable application with “-amd64” on the end of the filename and double-click it to start the setup. Click through until you click “Install,” leaving all default values as they are. Click Finish. Once the VM restarts, log in as Administrator again.

Right-click on the Network icon in the system tray at the bottom-right corner of the Domain Controller’s desktop and click “Open Network & Internet Settings”, then click on “Change Adapter Options.” In the Network Connections window that opens, you should see two connection objects, probably named “Ethernet” and “Ethernet 2.” Right-click on one of the connections and click “Status” then click “Details.” In the Network Connection Details window, look for the property called “IPv4 Address.” If you cannot find this property, look for its alternative, “Autoconfiguration IPv4 Address.” Remember which of the two properties this connection had, and click “Close” twice to return to the Network Connections. Do the same thing for the other connection and make a note of whether it had the “IPv4 Address” or “Autoconfiguration IPv4 Address” properties. One connection should have the one property, and the other connection should have the other, but none of them should have both properties. Right-click on the connection that has the “IPv4 Address” property and click “Rename.” Change the name of this connection to “Internet” and press Enter. Then rename the other connection “Internal.”

Once both connections are renamed, right-click on the “Internal” connection and click “Properties.” Select “Internet Protocol Version 4 (TCP/IPv4)” and click “Properties.” Click on the “Use the following IP address” radio button and fill in the following: “IP address” is 172.16.0.1, “Subnet mask” is 255.255.255.0, and “Preferred DNS server” is 127.0.0.1. Click OK, then click Close. Close any open windows.

Right-click on the Start Button and click “System,” then click the “Rename this PC” button in the right panel (you may have to scroll down a little). Name it “DC” for Domain Controller and click Next. Then click “Restart Now.”

<h3>Installing Active Directory and Creating a New Domain</h3>
Once the Domain Controller restarts, login as Administrator and open Server Manager if it doesn’t automatically open. Within Server Manager, click on “Add roles and features” then click next through all the default options until you get to the “Select server roles” section. Check the box for “Active Directory Domain Services” and then click “Add Features” on the confirmation screen. Then click Next through the rest of the options until you can click on “Install.” Click “Install” and wait until it’s finished. Then click “Close” to finish.

Within the Server Manager Dashboard, you should see a flag icon with an exclamation point near it - click on it to open a pop-out menu. Click on “Promote this server to a domain controller” then select the “Add a new forest” radio button. Type in “mydomain.com” and click Next. On the next screen, you will notice that the DSRM password fields require an entry here. DSRM is beyond the scope of this lab, but you still need to add something to move to the next screen. Add the same password you’re using for the rest of this lab just to keep it simple and click Next through the defaults on each screen until you reach the “Prerequisites Check” screen. Once the prerequisite checks are passed, click “Install.” This may take a little while and the VM will restart as part of this process. Once the VM restarts and reaches the log in screen, you will notice that you are now logging into the new domain we created.

<h3>Create a new Administrator account to use instead of the default</h3>
Open the start menu, navigate to “Windows Administration Tools” then click on “Active Directory Users and Computers.” Right-click on “mydomain.com” in the left panel, click “New,” then click “Organizational Unit.” For the name, type “_ADMINS” and uncheck the “Protect container from accidental deletion” checkbox. Click OK. Now you should see an “_ADMINS” object in the left panel (expand the mydomain.com object if you don’t see it). Right-click it and click “New,” then “User.” Fill out your first and last name in the applicable fields, but in the “User logon name” field, enter “a-” followed by your first name initial and last name. For example, Eric Burton’s logon name should be “a-eburton” (see screenshot) The prefix is a naming convention used to indicate this account is an Admin account. Click Next. Add the same password you have been using for the entire lab and check the “Password never expires” checkbox while unchecking the “User must change password at next logon” checkbox. Click Next, then Finish. You should now see the new user show up in the right panel when you select the “_ADMINS” OU in the left panel. 

Right-click the new user and click “Properties.” Then click the “Member Of” tab and “Add.” Type “domain admins” and click “Check Names.” If the text you just typed was changed to “Domain Admins,” click OK. Then click Apply, then OK again. That user is now an Admin.

<h3>Signing in with new Admin account</h3>
Sign out of your current account by right-clicking the start button, selecting “Shut down or sign out,” then clicking “Sign out.” When you see the password field on the login screen, click on “Other user” on the bottom left of the screen. Now login with the user name of the Admin account we just created (with the “a-” prefix) and the corresponding password.

<h3>Installing Remote Access Server and Network Address Translation on Domain Controller</h3>
Open Server Manager and click on “Add roles and features.” Click Next through all the default options until you reach “Select server roles.” Check the box for “Remote Access” then click Next through more default options until you reach “Select role services.” Check the “Routing” checkbox and then “Add Features.” You will notice that this will automatically check the “DirectAccess and VPN (RAS) checkbox as well. Click Next through the remaining options and click “Install” when able. Click Close when the installation completes.

On the top left of the Server Manager Dashboard, click on “Tools” then click “Routing and Remote Access.” Right-click on the local server object in the left panel and select “Configure and Enable Routing and Remote Access.” Click Next, then select the “Network address translation (NAT) radio button and click Next. On the next screen, make sure the radio button for “Use this public interface to connect to the Internet” is selected and not greyed out. If it is greyed out and unselectable, click Cancel and restart the Routing and Remote Access Setup Wizard (go back to the beginning of this paragraph’s directions) and that should fix the issue. From the two interfaces available for selection, select the one that connects to the Internet (this one should be named “Internet” to distinguish it). Click Next, then Finish.

<h3>Set up DHCP on Domain Controller</h3>
Open Server Manager and click on “Add roles and features” again. Once again, click Next until you reach “Select server roles.” Check the “DHCP Server” checkbox, then click “Add Features,” then click Next through the remainder of the screens. Click Install, then click Close. Back in Server Manager, click “Tools” and then click “DHCP.” Once the DHCP window opens, you should see the server object in the left panel. Click on the arrow to the left of it to reveal the IPv4 and IPv6 objects. Right-click on the IPv4 object and click “New Scope” to begin the New Scope Wizard. Click Next, then type “172.16.0.100-200” and click Next. Enter 172.16.0.100 for the “Start IP address,” 172.16.0.200 for the “End IP address,” and 255.255.255.0 for the “Subnet mask.” Click Next through the rest of the default options until you reach the “Configure DHCP Options” screen. Make sure “Yes, I want to configure these options now” is selected and click Next. On the “Router (Default Gateway) screen, enter the IP address of the Domain Controller’s “Internal” IP address (we set this to 172.16.0.1 earlier) and click Add, then click Next through the remaining default options and click Finish.

Back in the DHCP window, right-click on the server object (dc.mydomain.com) and click “Authorize.” Then right-click it again and click “Refresh.” Now the icons for the IPv4 and IPv6 objects should have green check marks. Click the arrow next to the IPv4 object to expand it and you should see the new Scope object that we just created.

<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Select the disk:  <br/>
<img src="https://i.imgur.com/tcTyMUE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Enter the number of passes: <br/>
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Confirm your selection:  <br/>
<img src="https://i.imgur.com/cdFHBiU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Wait for process to complete (may take some time):  <br/>
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Sanitization complete:  <br/>
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Observe the wiped disk:  <br/>
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
