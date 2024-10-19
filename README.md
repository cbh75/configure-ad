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
- Windows 10 (22H2)

<h2>Setting up the Virtual Machines</h2>

Go to the Azure portal (https://portal.azure.com) and ensure you are signed into your Microsoft Azure account. Once you have done so, nativagate to your resource groups. An easy way to do this is to find the search bar at the top of the page and type in "resource groups".

<br />

![image](https://github.com/user-attachments/assets/37531ce6-7d74-4779-8364-b08b9a844659)

<br />

Once on the resource groups page, click **Create**. This will take you to a new page where you can enter a name for your new resource group. In this guide, we will call it "active-directory". Then click **Review + create** at the bottom-left corner of the page. Once you see "Validation passed", click **Create**.

![image](https://github.com/user-attachments/assets/0fd92c52-6eb1-4226-8905-cf116f13f975)

After the resource group has been created, navigate to your virtual machines. An easy way to do this is to type in "virtual machines" in the search bar.

![1](https://github.com/cbh75/osticket-prereqs/assets/62080815/1c9898b8-14cf-42ff-8322-9586a87f2ba5)

After clicking on **Virtual Machines** in the search bar, you will be taken to your Virtual Machines page listing all of your VMs. In this guide, we want to create two virtual machines. One will be our domain controller, and the other will be a client. You probably won't have any right now, so let's create our first one by clicking on **Create** in the top left corner. This will produce a drop-down box with the different types of VMs you can create. For this tutorial, we will use the first option, **Azure virtual machine**.

<br />

![2](https://github.com/cbh75/osticket-prereqs/assets/62080815/1cb56175-eceb-480e-9c5a-24c006139e86)

<br />

On the next screen, make sure to choose the resource group we just created in the **Resource group** box.<br />
For the virtual machine name, type "dc-1" as this virtual machine will be our domain controller for Active Directory.<br />
For **Image**, choose **Windows Server 2022**.<br />
The size does not matter too much, but be sure to choose one that will have enough resources.<br />
For our administrator account, choose a username and password that fits the criteria. **Be sure to remember these!**

![image](https://github.com/user-attachments/assets/3dfb789a-b92a-47e8-a8c7-03d57951da80)
![image](https://github.com/user-attachments/assets/47fdac1c-ad00-40c0-8921-cc205cbf4535)

Once that is complete, click **Next** until you get to the **Networking** tab. Under the **Virtual network** box, click the blue **Create new** text.

![image](https://github.com/user-attachments/assets/bb59fbbe-588e-419d-9acb-0793fba198c0)

This will open up a new sidebar where we can create a new virtual network. Azure could have done this automatically for us, but since we are using this virtual network for more than one virtual machine, we want to give it a more general name to avoid confusion. In this case, we can title it "ad-vnet", then click **OK**.

![image](https://github.com/user-attachments/assets/b32501ff-cf05-4f8c-ae55-8570d28c6887)

Once that is done, we can use the remaining default settings, so go ahead and click **Review + create**. Once validation passes, click **Create**. Wait a couple minutes for the virtual machine to deploy, then navigate back to your virtual machine list. You should see **dc-1** as the only one in the list.

![image](https://github.com/user-attachments/assets/2df838b1-3520-4e5a-b651-4f97cb010029)

Once you see **dc-1** in your virtual machines, we want to go into its network settings and make its private IP address static, meaning it can never change. By default, Azure sets the IP address to be dynamic, meaning that it can change upon rebooting or when internet settings change. In order to change this setting, click on **dc-1** in the virtual machines list, then under **Properties** click **Networking**.

![image](https://github.com/user-attachments/assets/b55dac3b-3bfe-4679-8bd9-fc52850b4d33)

On the networking page, click on the network interface towards the top of the page.

![image](https://github.com/user-attachments/assets/dbebe3fd-857c-4bda-b7ba-f757112ee9d6)

This will take us to the network interface page for our "dc-1" virtual machine. On this page, click on **IP Configurations**, then click on the name of the IP configuration, which in this case is "ipconfig1".

![image](https://github.com/user-attachments/assets/e1e14712-1f43-48ea-85f8-80ccd8e1a5b0)
![image](https://github.com/user-attachments/assets/732d1a74-ec53-4932-be9b-2eda2fc2813c)

A menu should pop up on the right side of the page, and under **Private IP address settings** we can change the allocation from Dynamic to **Static** by clicking the bubble next to it. We can leave it as the default, which in this case is "10.0.0.4". Once finished, click **Save** at the bottom of the page.

![image](https://github.com/user-attachments/assets/558c15f9-51ff-4aab-924e-80b50e84979b)

Now, for testing purposes, we want to log into our domain controller and disable the firewall. You generally will not do this in a professional setting, but for the sake of this guide we can just disable it fully. To do this, log in to dc-1 using Remote Desktop Connection. In Remote Desktop Connection, click **Show Options** to see all of the options. Once all options are shown, in the **Computer** box, we want to type in our dc-1 virtual machine's public IP address. For the **User name**, we want to type in the one we specified during the setup of the virtual machine, then click **Connect**. A box will pop up asking for a password, so type in the one you specified earlier. If a box pops up saying the identity of the remote computer cannot be verified, click **Yes** to connect anyway.

![image](https://github.com/user-attachments/assets/317e568a-de16-4ec7-ac27-f130b811cdd4)
![image](https://github.com/user-attachments/assets/c5351a13-d5be-429f-adb6-a6629e192e3e)
![image](https://github.com/user-attachments/assets/f201e0e6-0787-4e11-8be9-d88ccef2c234)

Once logged in, Server Manager will pop up, which we can disregard for now. In the search bar, type in "firewall", which will bring up an application named "Windows Defender Firewall with Advanced Security". Click it to open it.

![image](https://github.com/user-attachments/assets/7d864e57-d7ee-4507-99ee-091fbfee8102)

In Windows Firewall, click on **Windows Defender Firewall Properties**.

![image](https://github.com/user-attachments/assets/a6c198ae-6722-4dc3-b095-6ed069e396da)

In the box that pops up, we want to turn the **Firewall state** setting to **Off** for the Domain Profile, Private Profile, and Public Profile. Once all three settings have been changed to Off, click **Apply**, then **OK**.

![image](https://github.com/user-attachments/assets/0a0cb8fd-cd87-4b30-9b32-9341a442ff13)

We are done with our dc-1 machine for now, so feel free to minimize the remote connection window.

Next, we want to go ahead and create our client virtual machine, so navigate back to the virtual machines list in the Azure portal, then click **Create** and then **Azure virtual machine** once again.<br />
On the virtual machine creation screen, make sure **active-directory** is selected for our resource group, just like in the last one.<br />
For **Virtual machine name**, we want to call this one "client-1".<br />
For **Image**, select **Windows 10 Pro**.<br />
Like before, the size doesn't matter much. Just be sure to choose a size that has enough resources to run what we need.<br />
For the administrator account, set up a username and password. We will use this user to log in to the virtual machine, just like before. **Be sure to remember this**!<br />
Also, be sure to check the box at the bottom confirming you have an eligible Windows license.

![image](https://github.com/user-attachments/assets/8d88082e-c691-40e1-bd65-72d0a0701d14)
![image](https://github.com/user-attachments/assets/117dd128-bf6e-4e7e-a8fd-7ad654453104)

Once that is done, we want to click **Next** until we get to the **Networking** tab.<br />
Under **Virtual network**, make sure the same network is used as the one we created for our domain controller, in this case "ad-vnet".

![image](https://github.com/user-attachments/assets/682fa8c8-f79c-46aa-91e1-3d80fc9434a9)

Once that is configured, we can go ahead and click **Review + create**, then when validation passes, click **Create**. Give Azure a few minutes to create our new virtual machine, and then when finished, navigate back to the virtual machines list. Now we need to set the client virtual machine's DNS to our domain controller's **private** IP address, which allows our client to properly resolve domains using the domain controller. To do this, click on **client-1**, then **Networking**, then the **Network Interface**, just like before when we created a static IP address for our domain controller. Once we get to the network interface overview page, we want to expand the **Settings** menu on the left side, then click on **DNS Servers**. Once in the DNS Server settings, click the **Custom** bubble, then in the text box that appears, type in the private IP address of our domain controller that we set earlier. Once done, click **Save** at the top.

![image](https://github.com/user-attachments/assets/b1d2c181-0fe3-4efb-ab84-9af2ab90852d)

Once that is complete, restart the client-1 machine to ensure our DNS settings are applied.

![image](https://github.com/user-attachments/assets/c82deed3-4041-4f7b-9eb1-638575405ba1)

Once our client machine has restarted, log in to it by opening another instance of Remote Desktop Connection and using the user credentials we specified earlier. Run through the initial settings, then wait until the Windows desktop appears. Once on the desktop, open a PowerShell window by either searching or right-clicking the **Start** button, then once in PowerShell type in "ipconfig /all". Under **DNS Servers**, you should see the private IP address of dc-1 that we specified earlier.

![image](https://github.com/user-attachments/assets/deb6335e-94c3-4282-9be0-d8ef863fa265)
