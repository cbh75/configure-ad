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
- Windows 11 (22H2)

<h2>Setting up the Virtual Machines</h2>

Go to the Azure portal (https://portal.azure.com) and ensure you are signed into your Microsoft Azure account. Once you have done so, nativagate to your resource groups. An easy way to do this is to find the search bar at the top of the page and type in "resource groups".

<br />

![image](https://github.com/user-attachments/assets/37531ce6-7d74-4779-8364-b08b9a844659)

<br />

Once on the resource groups page, click **Create**. This will take you to a new page where you can enter a name for your new resource group. In this guide, we will call it "active-directory". Then click **Review + create** at the bottom-left corner of the page. Once you see "Validation passed", click **Create**.

![image](https://github.com/user-attachments/assets/4dbad8b1-abf8-42cc-a389-3582fccc0a12)

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

![image](https://github.com/user-attachments/assets/62a85cae-1644-410c-9509-8a2121514339)
![image](https://github.com/user-attachments/assets/47fdac1c-ad00-40c0-8921-cc205cbf4535)

Once that is complete, click **Next** until you get to the **Networking** tab. Under the **Virtual network** box, click the blue **Create new** text.

![image](https://github.com/user-attachments/assets/bb59fbbe-588e-419d-9acb-0793fba198c0)

This will open up a new sidebar where we can create a new virtual network. Azure could have done this automatically for us, but since we are using this virtual network for more than one virtual machine, we want to give it a more general name to avoid confusion. In this case, we can title it "ad-vnet", then click **OK**.

![image](https://github.com/user-attachments/assets/b32501ff-cf05-4f8c-ae55-8570d28c6887)

Once that is done, we can use the remaining default settings, so go ahead and click **Review + create**. Once validation passes, click **Create**. Wait a couple minutes for the virtual machine to deploy, then navigate back to your virtual machine list. You should see **dc-1** as the only one in the list.

![image](https://github.com/user-attachments/assets/2df838b1-3520-4e5a-b651-4f97cb010029)

Once you see **dc-1** in your virtual machines, we want to go ahead and create our client virtual machine, so click **Create** and then **Azure virtual machine** once again.<br />
On the virtual machine creation screen, make sure **active-directory** is selected for our resource group, just like in the last one.<br />
For our virtual machine name, we want to call this one "client-1".<br />
For image, we want to select **Windows 11 Pro**.<br />
