Prerequisites
You require need an Azure subscription to perform these steps. If you don't have one you can create one by following the steps outlined on the Create your Azure free account today webpage.


Steps
Create a virtual network
1. Sign into the Azure Portal
2. Select + Create a resource on the upper, left corner of the Azure portal, then select Networking, and then select Virtual network.- 3. Enter, or select, the following information, accept the defaults for the remaining settings, and then select Create:
- Name: VNET1
- Address space: 10.0.0.0/16
- Subscription : < select your subscription >
- Resource group: < Select *Create new* and enter netsecrg. >
- Location: (US) East US (or a Datacenter location near you)
- Subnet: 
- Name: subnet1
- Address range: 10.0.0.0/24
- Create two application security groups
An application security group enables you to group together servers with similar functions, such as web servers.
1. Select + Create a resource on the upper, left corner of the Azure portal.
2. In the Search the Marketplace box, enter Application security group. When Application security group appears in the search results, select it, select Application security group again under Everything, and then select Create.- 3. Enter the following values then click Review and Create followed by Create
- - Subscription : < select your subscription >
- Resource group: < *Select existing...* and then select *netsecrg* which you created earlier. >
- Name: asgwebservers
- Region: (US) East US
- 4. Complete steps 1 to 3 again to create another Application security group, specifying the following values:
- Subscription : < select your subscription >
- Resource group: < *Select existing...* and then select *netsecrg* which you created earlier. >
- Name: asgmgmtservers
- Region: (US) East US
- Create a network security group
1. Select + Create a resource on the upper, left corner of the Azure portal, then select Networking, and then select Network security group.
2. Enter, or select, the following information, and then select Create:
- Name: nsg1
- Subscription : < select your subscription >
- Resource group: < *Select existing...* and then select netsecrg which you created earlier. >
- Location: (US) East US
- Associate the network security group to a subnet
1. Open the Network security group you just created, nsg1, Under SETTINGS, select Subnets and then select + Associate- 2. Under Associate subnet, select Virtual network and then select the virtual network you created earlier i.e. VNET1. Then, select Subnet and choose the subnet you created earlier i.e. select subnet1, and then select OK.
- Create security rules
1. Still in the Network Security group, Under SETTINGS, select Inbound security rules and then select + Add.- 2. Create a security rule that allows ports 80 and 443 to the AsgWebServers application security group. Under Add inbound security rule, enter, or select the following values, accept the remaining defaults, and then select Add when finished.
- Source: Any
- Source port ranges: * 
- Destination: Application security group 
- Destination application security group: asgwebservers
- Destination port ranges: 80,443
- Protocol: TCP
- Action: Allow
- Priority: default
- Name: Allow-Web-All
This allows us to connect to the web server from the internet over ports 80 and 443 only.- 3. Create another inbound security rule by repeating steps 1 and 2 again, using the following values:- - Source: Any
- Source port ranges: * 
- Destination: Application security group 
- Destination application security group: asgmgmtservers
- Destination port ranges: 3389
- Protocol: TCP
- Priotity: 110
- Name: Allow-RDP-All
- Note: 
- The port 3339, the rdp port, is exposed to the internet for the VM that is assigned to the asgmgmtservers application security group. For production environments, instead of exposing port 3389 to the internet, it's recommended that you connect to Azure resources that you want to manage using a VPN or private network connection.
- Also, we designated the value Any for source to indicate access from the internet.
4. Review the rules you created. Your list should look like the list in the following picture:
- Create virtual machines
We will now create two VMs in the virtual network, to which we will apply our network security rules.
1. In the Azure Portal, click on the Cloud Shell icon in the top right hand corner- 2. The Cloud Shell is launched in the bottom of the browser window. 
3. Run the below Azure CLI command to create the first virtual machine, this command will run fine in either powershell *or* bash console. You can copy and paste the command from below directly into the Cloud Shell console and press Enter to run it.
```cli
az vm create `
--name vmmgmt1 `
--resource-group netsecrg `
--image Win2019Datacenter `
--location eastus `
--vnet-name VNET1 `
--subnet subnet1 `
--nsg nsg1 `
--asg asgmgmtservers `
--admin-username azureuser `
--admin-password Password0134!
```
- Note: The command will take two to three minutes to complete and should run successfully. Do not continue to the next step until the VM is deployed.
3. Create the second virtual machine by running the following command in the same cloud shell console in the browser window.- ```cli
az vm create `
--name vmweb1 `
--resource-group netsecrg `
--image Win2019Datacenter `
--location eastus `
--vnet-name VNET1 `
--subnet subnet1 `
--nsg nsg1 `
--asg asgwebservers `
--admin-username azureuser `
--admin-password Password0134!
```
NOTE: Items to note from the deployment:
- We created a network interface for each VM, and attached the network interface to the VM.
- Both network interfaces are in Virtual network VNET1 and its subnet subnet1.
- subnet1 is part of the Network Security Group, nsg1, so as such the nsg1 security rules are applied to the two virtual machines.
- vmmgmt1 has been associated with the application security group asgmgmtservers
- vmweb1 has been associated with the application security group asgwebservers*

Test traffic filters
1. In the Azure Portal, go to your resource group, i.e. netsecrg, open the vmmgmt1 virtual machine and connect to it by clicking on the Connect button.
2. In the Connect to virtual machine blade select Download RDP File and click to open the rdp file when prompted to do so.- 3. In the Remote Desktop Connection dialogue select Connect.- 4. In the Windows Security > Enter your credentials dialogue select More Choices- 5. Select use a different account and enter the user name and password you specified when creating the VM, as below, then click OK.
- - username=.\azureuser (using the symbols `.\` indicate for the dialogue to take the context of the local machine)
- password = Password0134!
- Note: You may receive a certificate warning during the sign-in process. If you receive the warning, select Yes or Continue, to proceed with the connection.- Note: The connection succeeds, because port 3389 is allowed inbound connections from the internet to the asgmgmtservers application security group, i.e. the vmmgmt1 virtual machine is in the VNET1 virtual network and the subnet subnet1 which has those security rules associated with it as defined by the Network Security group nsg1.
6. From within the vmmgmt1 virtual machine we will now connect via rdp to the vmweb1 virtual machine. Still within the remote desktop connection to vmmgmt1, go to the start menu, type PowerShell, then locate and launch Powershell, by right clicking it and choosing Run as Administrator
7. In that PowerShell console enter the below command to open a remote desktop connection to the other virtual machine you created, vmweb1.
```
mstsc /v:vmweb1
```
8. This will launch a remote desktop connection as before, and you will need to select the More choices option, then Use a different account, and then enter your credentials, as below.
- username = .\azureuser
password = Password0134!
- Note: You are able to connect to the vmweb1 virtual machine from the vmmgmt1 virtual machine because virtual machines in the same virtual network can communicate with each other over any port, by default. Leave the remote desktop connections to the vmmgmt1 and vmweb1 virtual machines open as we will return to them.- 9. Try to connect to the vmweb1 virtual machine from the internet by returning to the Azure Portal, then open the vmmgmt1 virtual machine and connect to it by clicking on the Connect button.
10. Follow the prompts to Download rdp file, Open and Connect, as before. However, you cannot create a remote desktop connection to the vmweb1 virtual machine from the internet, because the security rule for the asgwebservers doesn't allow port 3389 inbound from the internet, by default. 
- Note: In the case of the vmmgmt1 virtual machine, we specifically created a rule to allow connections over port 3389, and assigned that rule to the asgmgmtservers ASG, that the vmmgmt1 virtual machine is a member of, thus we are able to connect to it from the internet over port 3389.
- 11. We will now install Internet Information Service (IIS) on the vmweb1 to allow it function as a webserver. Return to the remote desktop connection to vmweb1 virtual machine and open a Powershell prompt by clicking on the start button, typing Powershell, the right clicking it and choosing Run as administrator- 12. In the resultant Powershell console prompt, install Microsoft Internet Information Service (IIS) on the vmweb1 virtual machine, by running the following command within the PowerShell session
```
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```
13. The installation should complete successfully.
14. Disconnect from the vmweb1 virtual machine, which leaves you in the vmmgmt1 remote desktop connection, then also disconnect from the vmmgmt1 virtual machine.
15. In the Azure portal open the vmweb1 , go to Overview and note the Public IP address for the virtual machine. The address shown in the following picture is 23.96.25.165, but your address will be different:
16. From your local machine access the vmweb1 web server from the internet by opening an internet browser on your computer and browsing to http://&lt;public-ip-address-from-previous-step&gt;. You see the IIS welcome screen, 
- Note: Port 80 is allowed inbound connections from the internet to the asgwebservers application security group, which the vmweb1 virtual machine is a member of.
- Congratulations! You have created a virtual network and subnet, and then two application security groups, one for management servers and one for web servers. You then created a Network Security group (NSG) and associated that NSG to the subnet we created earlier. You then created two inbound network security rules, to allow-rdp-all and allow-web-all traffic.
- You then created two virtual machines, one to represent a management server, and one to represent a web server, and associated those virtual machines with their respective Application security groups (ASGs), and then with the network security group (NSG) containing the network security rules to control network traffic. You then tested the network security rules we have created and applied.
    - Note: Remember to delete the resources you have just deployed if you are no longer using them to ensure you do not incur costs for running resources. You can delete all deployed resources by deleting the resource group in which they all reside.


