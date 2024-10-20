<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Implementing Active Directory (On-Premises) in Azure</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>Setting up the Virtual Machines</h2>

Go to the Azure portal (https://portal.azure.com) and ensure you are signed into your Microsoft Azure account. Once you have done so, nativagate to your resource groups. An easy way to do this is to find the search bar at the top of the page and type in "resource groups".

<br />

![image](https://github.com/user-attachments/assets/37531ce6-7d74-4779-8364-b08b9a844659)

<br />

Once on the resource groups page, click **Create**. This will take you to a new page where you can enter a name for your new resource group. In this guide, we will call it "active-directory". Then click **Review + create** at the bottom-left corner of the page. Once you see "Validation passed", click **Create**.

<br />

![image](https://github.com/user-attachments/assets/0fd92c52-6eb1-4226-8905-cf116f13f975)

<br />

After the resource group has been created, navigate to your virtual machines. An easy way to do this is to type in "virtual machines" in the search bar.

<br />

![1](https://github.com/cbh75/osticket-prereqs/assets/62080815/1c9898b8-14cf-42ff-8322-9586a87f2ba5)

<br />

After clicking on **Virtual Machines** in the search bar, you will be taken to your Virtual Machines page listing all of your VMs. In this guide, we want to create two virtual machines. One will be our domain controller, and the other will be a client. You probably won't have any right now, so let's create our first one by clicking on **Create** in the top left corner. This will produce a drop-down box with the different types of VMs you can create. For this tutorial, we will use the first option, **Azure virtual machine**.

<br />

![2](https://github.com/cbh75/osticket-prereqs/assets/62080815/1cb56175-eceb-480e-9c5a-24c006139e86)

<br />

On the next screen, make sure to choose the resource group we just created in the **Resource group** box.<br />
For the virtual machine name, type "dc-1" as this virtual machine will be our domain controller for Active Directory.<br />
For **Image**, choose **Windows Server 2022**.<br />
The size does not matter too much, but be sure to choose one that will have enough resources.<br />
For our administrator account, choose a username and password that fits the criteria. **Be sure to remember these!**

<br />

![image](https://github.com/user-attachments/assets/3dfb789a-b92a-47e8-a8c7-03d57951da80)
![image](https://github.com/user-attachments/assets/47fdac1c-ad00-40c0-8921-cc205cbf4535)

<br />

Once that is complete, click **Next** until you get to the **Networking** tab. Under the **Virtual network** box, click the blue **Create new** text.

<br />

![image](https://github.com/user-attachments/assets/bb59fbbe-588e-419d-9acb-0793fba198c0)

<br />

This will open up a new sidebar where we can create a new virtual network. Azure could have done this automatically for us, but since we are using this virtual network for more than one virtual machine, we want to give it a more general name to avoid confusion. In this case, we can title it "ad-vnet", then click **OK**.

<br />

![image](https://github.com/user-attachments/assets/b32501ff-cf05-4f8c-ae55-8570d28c6887)

<br />

Once that is done, we can use the remaining default settings, so go ahead and click **Review + create**. Once validation passes, click **Create**. Wait a couple minutes for the virtual machine to deploy, then navigate back to your virtual machine list. You should see **dc-1** as the only one in the list.

<br />

![image](https://github.com/user-attachments/assets/2df838b1-3520-4e5a-b651-4f97cb010029)

<br />

Once you see **dc-1** in your virtual machines, we want to go into its network settings and make its private IP address static, meaning it can never change. By default, Azure sets the IP address to be dynamic, meaning that it can change upon rebooting or when internet settings change. In order to change this setting, click on **dc-1** in the virtual machines list, then under **Properties** click **Networking**.

<br />

![image](https://github.com/user-attachments/assets/b55dac3b-3bfe-4679-8bd9-fc52850b4d33)

<br />

On the networking page, click on the network interface towards the top of the page.

<br />

![image](https://github.com/user-attachments/assets/dbebe3fd-857c-4bda-b7ba-f757112ee9d6)

<br />

This will take us to the network interface page for our "dc-1" virtual machine. On this page, click on **IP Configurations**, then click on the name of the IP configuration, which in this case is "ipconfig1".

<br />

![image](https://github.com/user-attachments/assets/e1e14712-1f43-48ea-85f8-80ccd8e1a5b0)
![image](https://github.com/user-attachments/assets/732d1a74-ec53-4932-be9b-2eda2fc2813c)

<br />

A menu should pop up on the right side of the page, and under **Private IP address settings** we can change the allocation from Dynamic to **Static** by clicking the bubble next to it. We can leave it as the default, which in this case is "10.0.0.4". Once finished, click **Save** at the bottom of the page.

<br />

![image](https://github.com/user-attachments/assets/558c15f9-51ff-4aab-924e-80b50e84979b)

<br />

Now, for testing purposes, we want to log into our domain controller and disable the firewall. You generally will not do this in a professional setting, but for the sake of this guide we can just disable it fully. To do this, log in to dc-1 using Remote Desktop Connection. In Remote Desktop Connection, click **Show Options** to see all of the options. Once all options are shown, in the **Computer** box, we want to type in our dc-1 virtual machine's public IP address. For the **User name**, we want to type in the one we specified during the setup of the virtual machine, then click **Connect**. A box will pop up asking for a password, so type in the one you specified earlier. If a box pops up saying the identity of the remote computer cannot be verified, click **Yes** to connect anyway.

<br />

![image](https://github.com/user-attachments/assets/317e568a-de16-4ec7-ac27-f130b811cdd4)
![image](https://github.com/user-attachments/assets/c5351a13-d5be-429f-adb6-a6629e192e3e)
![image](https://github.com/user-attachments/assets/f201e0e6-0787-4e11-8be9-d88ccef2c234)

<br />

Once logged in, Server Manager will pop up, which we can disregard for now. In the search bar, type in "firewall", which will bring up an application named "Windows Defender Firewall with Advanced Security". Click it to open it.

<br />

![image](https://github.com/user-attachments/assets/7d864e57-d7ee-4507-99ee-091fbfee8102)

<br />

In Windows Firewall, click on **Windows Defender Firewall Properties**.

<br />

![image](https://github.com/user-attachments/assets/a6c198ae-6722-4dc3-b095-6ed069e396da)

<br />

In the box that pops up, we want to turn the **Firewall state** setting to **Off** for the Domain Profile, Private Profile, and Public Profile. Once all three settings have been changed to Off, click **Apply**, then **OK**.

<br />

![image](https://github.com/user-attachments/assets/0a0cb8fd-cd87-4b30-9b32-9341a442ff13)

<br />

We are done with our dc-1 machine for now, so feel free to minimize the remote connection window.

<br />

Next, we want to go ahead and create our client virtual machine, so navigate back to the virtual machines list in the Azure portal, then click **Create** and then **Azure virtual machine** once again.<br />
On the virtual machine creation screen, make sure **active-directory** is selected for our resource group, just like in the last one.<br />
For **Virtual machine name**, we want to call this one "client-1".<br />
For **Image**, select **Windows 10 Pro**.<br />
Like before, the size doesn't matter much. Just be sure to choose a size that has enough resources to run what we need.<br />
For the administrator account, set up a username and password. We will use this user to log in to the virtual machine, just like before. **Be sure to remember this**!<br />
Also, be sure to check the box at the bottom confirming you have an eligible Windows license.

<br />

![image](https://github.com/user-attachments/assets/8d88082e-c691-40e1-bd65-72d0a0701d14)
![image](https://github.com/user-attachments/assets/117dd128-bf6e-4e7e-a8fd-7ad654453104)

<br />

Once that is done, we want to click **Next** until we get to the **Networking** tab.<br />
Under **Virtual network**, make sure the same network is used as the one we created for our domain controller, in this case "ad-vnet".

<br />
s
![image](https://github.com/user-attachments/assets/682fa8c8-f79c-46aa-91e1-3d80fc9434a9)

<br />

Once that is configured, we can go ahead and click **Review + create**, then when validation passes, click **Create**. Give Azure a few minutes to create our new virtual machine, and then when finished, navigate back to the virtual machines list. Now we need to set the client virtual machine's DNS to our domain controller's **private** IP address, which allows our client to properly resolve domains using the domain controller. To do this, click on **client-1**, then **Networking**, then the **Network Interface**, just like before when we created a static IP address for our domain controller. Once we get to the network interface overview page, we want to expand the **Settings** menu on the left side, then click on **DNS Servers**. Once in the DNS Server settings, click the **Custom** bubble, then in the text box that appears, type in the private IP address of our domain controller that we set earlier. Once done, click **Save** at the top.

<br />

![image](https://github.com/user-attachments/assets/b1d2c181-0fe3-4efb-ab84-9af2ab90852d)

<br />

Once that is complete, restart the client-1 machine to ensure our DNS settings are applied.

<br />

![image](https://github.com/user-attachments/assets/c82deed3-4041-4f7b-9eb1-638575405ba1)

<br />

Once our client machine has restarted, log in to it by opening another instance of Remote Desktop Connection and using the user credentials we specified earlier. Run through the initial settings, then wait until the Windows desktop appears. Once on the desktop, open a PowerShell window by either searching or right-clicking the **Start** button, then once in PowerShell type in "ipconfig /all". Under **DNS Servers**, you should see the private IP address of dc-1 that we specified earlier.

<br />

![image](https://github.com/user-attachments/assets/deb6335e-94c3-4282-9be0-d8ef863fa265)

<br />

<h2>Installing and Configuring Active Directory</h2>

Back in **dc-1**, open Server Manager if not already opened by using the search bar. In Server Manager, click **Manage** at the top-right of the window, then click **Add Roles and Features**.

<br />

![image](https://github.com/user-attachments/assets/2bd947a7-05fb-431b-97ea-2fb3355f0432)

<br />

In the pop-up window, click **Next** until you get to **Server Roles**, then click the check box next to **Active Directory Domain Services**. Another pop-up window will appear, click on **Add Features**. Then, back in the first window, click **Next** until you get to the **Confirmation** tab, then click **Install**. this may take a while, so give Windows a couple of minutes to install Active Directory. Once Finished, click **Close**. Now that Active Directory is installed, we have to configure it. To do this, click the flag icon in the top right of Server Manager. It should have a yellow triangle next to it indicating configuration is required. In the menu click **Promote this server to a domain controller**.

<br />

![image](https://github.com/user-attachments/assets/06194fca-f61a-4cf8-ac9d-694c8d8a2c98)

<br />

In the pop-up window, click the bubble next to **Add a new forest**, then specify the root domain name. In this example, we will use "mydomain.com". Once specified, click **Next**.

<br />

![image](https://github.com/user-attachments/assets/5fa70250-ee46-4947-893e-aeb9eae0723c)

<br />

On the next screen, we have to specify a DSRM password. We shouldn't have to use this in this example, but just in case, go ahead and set it to a password you will remember, then click **Next** until you get to the Prerequisites check, then click **Install**. This will take some time!

<br />

![image](https://github.com/user-attachments/assets/1c05fd79-fae8-43bf-9fe7-70158cdde624)

<br />

Once finished, the virtual machine will restart automatically.

<br />

<h2>Creating a Domain Admin</h2>

If you try to log back in, you will notice we can't anymore. This is because we promoted our dc-1 machine to be an actual domain controller now, so when logging in we now have to specify the domain we set up when configuring Active Directory.

<br />

![image](https://github.com/user-attachments/assets/34261af7-a65f-4498-b409-bcf8a2188cc7)

<br />

Now that we have logged back in to dc-1, we will set up a Domain Admin user. Go ahead and close the Server Manager, as we do not need that right now. Using the search bar, open **Active Directory Users and Computers**.

<br />

![image](https://github.com/user-attachments/assets/0f17fd9e-5360-4e6e-ac4b-6ee3126984f0)

<br />

Once in Active Directory Users and Computers (ADUC), right-click the domain. Go to **New**, then click on **Organizational Unit**.

<br />

![image](https://github.com/user-attachments/assets/09919293-b5fe-497b-a8ba-aac957503a77)

<br />

In the pop-up window, name the organizational unit "_EMPLOYEES". Make sure to type it exactly like that, as we will be running a script later that depends on this.

<br />

![image](https://github.com/user-attachments/assets/a98b81b6-5d0f-4705-ac71-f3b47ed50f46)

<br />

Once finished, create another organizational unit and call it "_ADMINS". When finished, right click the domain again and click **Refresh**. After it refreshes, the two new organizational units we created should be at the top of the list.

<br />

![image](https://github.com/user-attachments/assets/6c2ac1c5-7daf-4737-a2c9-7500e5c19d7f)

<br />

Click on **_EMPLOYEES**, and then right-click in the right side window, go to **New**, then click **User**.

<br />

![image](https://github.com/user-attachments/assets/2e612858-f923-415c-b681-bd0c7ef5a019)

<br />

Here, we can create a new user. Let's call this one Jane Doe, and since she will be an admin, we can make her logon name "jane.admin". Once filled out, click **Next**.

<br />

![image](https://github.com/user-attachments/assets/b0483f51-305e-43b6-918b-33f3adfadea4)

<br />

On the next screen we must set a password for Jane. Make sure to pick one you will remember! For this example, go ahead and uncheck the box next to **User must change password at next logon**.

<br />

![image](https://github.com/user-attachments/assets/5c62f579-2539-40d3-9d91-a7367d5f043f)

<br />

Once a password is specified, click **Next**, then **Finish**. Once we return to our "_EMPLOYEES" organizational unit, we can see on the right that our new Jane Doe user has been added to the list.

<br />

![image](https://github.com/user-attachments/assets/19008e47-d169-477f-a9cb-75d4b5855a4a)

<br />

However, Jane isn't an admin yet. To set this, double click on Jane Doe so her properties window pops up. Click on the **Member Of** tab at the top, then click **Add**.

<br />

![image](https://github.com/user-attachments/assets/6788d98d-f249-4df7-865e-6eda307ff4eb)

<br />

In the pop-up window, click on the object names box and type in "domain admins", then click on **Check Names**.

<br />

![image](https://github.com/user-attachments/assets/c8531ab4-f1e8-4954-be8e-9bf1f86ff172)

<br />

Once **Check Names** is clicked, notice that "Domain Admins" became capitalized and underlined. This is because Windows recognized the security group that we specified and auto assigned it to Jane Doe.

<br />

![image](https://github.com/user-attachments/assets/8af9a14e-659d-4ce3-99fc-6852242ce464)

<br />

Next, Click **OK**. Once returned to the properties window, click **Apply** then **OK**. Now, we can sign out of dc-1 as the user we specified upon creation of the virtual machine and instead log in as Jane Doe. After signing out, Remote Desktop Connection will close. Open a new window, and log in to dc-1 using Jane Doe's user name, making sure to specify the domain we set when configuring Active Directory.

<br />

![image](https://github.com/user-attachments/assets/ad7d560d-abe0-4b81-a14b-ad49c36aeab0)

<br />

Enter the password you used when creating Jane Doe's user, and then connect. Upon logging in, notice that Jane Doe is now seen when logging in instead of the user we created when setting up dc-1. This will be the admin account used for dc-1 from now on.

<br />

<h2>Joining the Client to the Domain</h2>

Next, minimize dc-1 and reconnect to client-1 using the user we created when setting up client-1. Once on client-1's desktop, right-click the start menu, then click **System**.

<br />

![image](https://github.com/user-attachments/assets/15787a65-54e1-43b1-9bd2-44d9140c9004)

<br />

Once on the About page, click on **Rename this PC (advanced)**.

<br />

![image](https://github.com/user-attachments/assets/48891145-92b3-4523-ab65-3654480a32d8)

<br />

Once System Properties opens, click **Change**.

<br />

![image](https://github.com/user-attachments/assets/b4f8e823-63ed-4b79-98d5-9cfbaa299b0b)

<br />

In the pop-up window, under **Member of**, click the **Domain** bubble, then type "mydomain.com" into the domain text box.

<br />

![image](https://github.com/user-attachments/assets/093085f9-312d-4d57-9be2-134c9fd9757d)

<br />

Click **OK**. Another box should pop up asking for a name and password of an account that has permission to join the domain. If an error pops up, double check that client-1's DNS server is set to dc-1's private IP address. We can use Jane Doe to help us join the domain.

<br />

![image](https://github.com/user-attachments/assets/b96ca872-0c79-4dd7-acf9-f7ecc8f1a113)

<br />

Click **OK**. A small window welcoming us to the domain should pop up, indicating we have succesfully joined the domain.

<br />

![image](https://github.com/user-attachments/assets/042cc32b-742e-4c37-bbf5-0891c18c3555)

<br />

Click **OK**, then client-1 will restart to apply the changes. Back in dc-1, open **Active Directory Users and Computers** once again. Under **mydomain.com**, click on **Computers** and verify that client-1 has joined the domain.

<br />

![image](https://github.com/user-attachments/assets/0618d23c-68c7-4d10-9c5c-d4a29f593ace)

<br />

Now that we see client-1, create a new organizational unit under **mydomain.com** and call it "_CLIENTS". Once created, refresh **mydomain.com** to bring "_CLIENTS" up with the other organizational units we created. Once it has refreshed, drag client-1 from **Computers** into **_CLIENTS**. If a pop-up appears, click **Yes**.

<br />

![image](https://github.com/user-attachments/assets/3a3ca497-bd3f-4776-b32e-1bc323230c53)
![image](https://github.com/user-attachments/assets/3227628e-b102-4102-8491-51cdc637d816)

<br />

<h2>Setting Up Remote Desktop for Non-Administrators</h2>

Open a new Remote Desktop Connection window and log in to client-1 using Jane Doe's account. Make sure to specify the domain, or the login will fail. Once the desktop appears, right-click the Start button and click **System**.

<br />

![image](https://github.com/user-attachments/assets/3671265c-d511-44e4-8f55-6653b0270bbd)

<br />

Once the About page appears, click on **Remote desktop** on the right side of the page. On the next screen, click on **Select users that can remotely access this PC**.

<br />

![image](https://github.com/user-attachments/assets/d7e7674d-4daa-446b-96e3-f7b160884aed)
![image](https://github.com/user-attachments/assets/7811f47a-893e-4ea6-8e9e-03e2e944f1c3)

<br />

In the pop-up window, click **Add...**, then in the new pop-up window, in the object names box, type in "domain users".

<br />

![image](https://github.com/user-attachments/assets/754cf0ca-b83e-48fb-84e7-a5b8de95b186)
![image](https://github.com/user-attachments/assets/634a402a-9658-4561-946b-471686ec6e77)

<br />

Click **Check Names**. Notice that like before, Windows recognizes the user group and automatically corrects it to the recognized group. Click **OK**. Domain Users will be added to the list of uses that can connect to the client. Click **OK** again.

<br />

![image](https://github.com/user-attachments/assets/50d5a5f7-b679-4039-898e-b6eb931935ab)

<br />

<h2>Creating Additional Users</h2>

Log back in to dc-1 as Jane Doe, and use Windows Search to open **PowerShell ISE** as an Administrator.

<br />

![image](https://github.com/user-attachments/assets/a7756037-f0f6-4a4b-ad20-ff55cbdac160)

<br />

Once the User Account Control window pops up, click **Yes**. Once PowerShell ISE opens, click the new script icon at the top to create a new script.

<br />

![image](https://github.com/user-attachments/assets/c40f794e-dbd0-4666-850c-15f023561db5)

<br />

Once a new script is created, a large white text box will appear in the top half of PowerShell ISE. Click into the text box, and paste the contents of [this](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) script into the box.

<br />

![image](https://github.com/user-attachments/assets/d9055549-1457-45b5-b627-e64601905bce)

<br />

Click the **Run Script** button to start the script.

<br />

![image](https://github.com/user-attachments/assets/17f46375-f1d5-4315-8ba0-c329c9fe800e)

<br />

PowerShell ISE will start creating a ton of users with randomly generated names. Once a good handful of users are created, click **Stop Operation** to stop generating new users.

<br />

![image](https://github.com/user-attachments/assets/7595c565-19f8-4c2f-ab41-c7f6c7559085)

<br />

Now that we have some users, open **Active Directory Users and Computers** and navigate to **_EMPLOYEES** under the domain. Here we will see all of the users the script created for us.

<br />

![image](https://github.com/user-attachments/assets/b1853fae-54e1-4d8f-8c28-1decdd512471)

<br />

Take any one of these users and log in to client-1 using that account. For this example, "bab.fuva" will be the account used. Open Remote Desktop Connection and type in their username, making sure to specify the domain. The default password specified in the script is Password1, so when prompted enter it into the box.

<br />

![image](https://github.com/user-attachments/assets/121bd007-7e98-47bf-9d8a-601112c7b7c1)

<br />

Once connected, notice that we are logging in as bab.fuva, instead of Jane Doe or the user we specified when creating the virtual machine.
