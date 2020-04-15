Prerequisites
You require need an Azure subscription to perform these steps. If you don't have one you can create one by following the steps outlined on the Create your Azure free account today webpage.

Steps
Create Resource group
1. Sign in to the Azure portal at https://portal.azure.com
2. On the Azure portal home page, click Resource groups > Add and use the following details and click Review and Create and then Create.
Subscription: < select your own subscription >
Resource group: Test-FW-RG
Region: < select a Datacenter location nearest to you. Note: All subsequent resources that you create must be in the same location. >

Create a VNET
1. From the Azure portal home page, click All services > Networking > Virtual networks.
2. Click Add and use the following details, leaving any other values as their default an click Create when finished
Name: Test-FW-VN
Address space: 10.0.0.0/16
Subscription : < select your subscription >
Resource group: < select resource group created earlier i.e. Test-FW-RG >
Location: < select the same location that you used previously >
Subnet>
Name: AzureFirewallSubnet (The firewall will be in this subnet, and the subnet name must be AzureFirewallSubnet).
Address range: 10.0.1.0/24

Create additional subnets
Next we will create some additional subnets, into which we will subsequently place two virtual machines.
1. On the Azure portal home page, click Resource groups > Test-FW-RG.
2. Click the Test-FW-VN virtual network.
3. Click Subnets > + Subnet and use the following details, leaving the remaining items at their default values and click OK when completed
Name: Workload-SN
Address range: 10.0.2.0/24
4. Create another subnet by repeating steps 1-3 above, using the values
Name: Jump-SN
Address range: 10.0.3.0/24

Create a virtual machine in each subnet
Now we will create two virtual machines and place them in the two additional subnets created in the previous section, one for the Jump-SN subnet, and one for the Workload-SN subnet.
1. On the Azure portal, click Create a resource.
2. Click Compute and then select Windows Server 2016 Datacenter in the Featured list.
3. Enter these values for the virtual machine, accepting the default values for items not listed below. When finished click Review + Create, then click Create
Basics
Subscription: < select your subscription >
Resource group: Test-FW-RG < the resource group you created earlier >
Virtual machine name: Srv-Jump
Region: < The region you selected earlier >
Username: azureuser
Password: Password0134!
Public inbound ports: RDP (3389).
Networking
Virtual Network: Test-FW-VN
Subnet: Jump-SN
Public IP: click Create new then type Srv-Jump-PIP for the public IP address name and click OK.
Management
Boot diagnostics: Off
4. While the virtual machine is being created, repeat steps 1-3 above to create another virtual machine with the following settings:
Basics
Subscription: < select your subscription >
Resource group: Test-FW-RG < the resource group you created earlier >
Virtual machine name: Srv-Work
Region: < The region you selected earlier >
Username: azureuser
Password: Password0134!
Public inbound ports: None
Networking
Virtual Network: Test-FW-VN
Subnet: Workload-SN
Public IP: None
Management
Boot diagnostics: Off

Deploy the Firewall into the VNET
This will take approx. 5 mins to configure and deploy
1. From the portal home page, click Create a resource and in the New pane type Firewall, then click Create
2. Click Networking and in the Featured section click See all > Firewall > Create.
3.  the Create a Firewall page, use the following table to configure the firewall, when finished click Review + create then Create
Subscription: < your Azure subscription >
Resource group: Test-FW-RG < the resource group you created earlier >
Name: Test-FW01
Region: < Select the same location that you used previously >
Choose a virtual network: Test-FW-VN < the VNET you created earlier >
Public IP address: < select Create new radio button >
Public IP address: < accept the default value >
Public IP address SKU: Standard
4. After deployment completes, go to the Test-FW-RG resource group, and click the Test-FW01 firewall.
5. Take note of the private IP address. You will use it later when you create the default route.

Create a default route
For the Workload-SN subnet, configure the outbound default route to go through the firewall.
1. From the Azure portal home page, click All services. Under Networking, click Route tables.
2. In the Route tables pane click + Add and enter the following details and whe finished click Create
Name: Firewall-route
Subscription: < select your subscription >
Resource group: Test-FW-RG < the resource group you created earlier >
Location: < select the same location that you used previously >
3. When it is finished click Refresh, and then click the Firewall-route route table.
4. Click Subnets > + Associate.
5. Click Virtual network > Test-FW-VN.
6. For Subnet, click Workload-SN. Make sure that you select only the Workload-SN subnet for this route, otherwise your firewall won't work correctly.
7. Click OK.
8. Click Routes > + Add and enter the following details and click OK when finished
Route name: FW-DG
Address prefix: 0.0.0.0/0
Next hop type: Virtual appliance. (Azure Firewall is actually a managed service, but virtual appliance works in this situation.)
Next hop address < enter the private IP address for the firewall that you noted previously >

Configure an application rule
Now we will create an application rule that allows outbound access to msn.com
1. Open the Test-FW-RG, and click the Test-FW01 firewall.
2. On the Test-FW01 page, under Settings, click Rules.
3. Click the Application rule collection tab.
4. Click + Add application rule collection and enter the following values, then click Add when finished
Name: App-Coll01
Priority: 200
Action: Allow
Rules 
Target FQDNs 
NAME: AllowWebsite
SOURCE ADDRESS: 10.0.2.0/24.
PROTOCOL:PORT: http, https.
TARGET FQDNS: www.microsoft.com (You can specify part of all or a URL, including wild characters, or just a single wild character to indicate all internet sites )
Note: Azure Firewall includes a built-in rule collection for infrastructure FQDNs that are allowed by default. These FQDNs are specific for the platform and can't be used for other purposes. For more information, see Infrastructure FQDNs. You can also use FQDN tags to represent a group of fully qualified domain names (FQDNs) associated with well known Microsoft services, such as Windows Update, Azure Backup etc

Configure a network rule
Now we will create a network rule that allows outbound access to two IP addresses over port 53, to allow our workload server access DNS servers.
1. Open the Test-FW-RG, and click the Test-FW01 firewall.
2. On the Test-FW01 page, under Settings, click Rules.
3. Click the Network rule collection tab.
4. Click + Add network rule collection and enter the following details, when finished click Add
Name: Net-Coll01
Priority: 200
Action: Allow
Rules: 
IP Addresses: 
NAME: AllowDNS
PROTOCOL: UDP
SOURCE ADDRESSES: 10.0.2.0/24
DESTINATION ADDRESSES: 209.244.0.3,209.244.0.4
DESTINATION PORTS: 53

Change the primary and secondary DNS address for the Srv-Work network interface
For testing purposes in this tutorial, you configure the primary and secondary DNS addresses. This isn't a general Azure Firewall requirement.
1. From the Azure portal, open the Test-FW-RG resource group.
2. Click the network interface for the Srv-Work virtual machine, it should be named something like Srv-Work-xyz.
3. Under Settings, click DNS servers.
4. Under DNS servers, click Custom and add the following details and click Save when finished.
Box 1 - Add DNS Server: 209.244.0.3
Box 2 - Add DNS Server: 209.244.0.4
Note: Updating the DNS records for the Network interface will automatically restart the virtual machine to which it is attached, you should see a message indicating such. Also the IP Addresses we are adding here are pre-existing publicly accessible DNS Server addresses. When our virtual machine looks for an external address it will refer to these DNS servers for the address details.
5. Go to the Srv-Work virtual machine and ensure it has a status of running, if it is de-allocated click Start, or if it has not re-started click Restart

Test the firewall
Now test the firewall to confirm that it works as expected.
1. From the Azure portal, review the network settings for the Srv-Work virtual machine and note the private IP address.
2. In the Azure Portal go to the Srv-Jump virtual machine and click Connect, followed by Download RDP File to open an RDP session to the SRV-Jump virtual machine, using the credentials you specified earlier when creating the VM.
3. From within the Srv-Jump virtual machine open a remote desktop connection to the Srv-Work private IP address that you noted earlier.
4. Once logged into the SRV-Jump virtual machine, allow Server Manager to open, which it will do after log in automatically, then go to Local server and turn off IE Enhanced Security Configuration
Note: In production environments you would not do this, this is to allow use access the workload server a bit more easily in this test scenario and reduce and prevent pop-ups. In a production you may use a workload server with no GUI environment present.
5. Open Internet Explorer and browse to https://www.microsoft.com
6. Click OK > Close on the security alerts that may pop-up.
7. You should see the Microsoft.com home page.
8. Now browse to https://www.azure.com, You should be blocked by the firewall.

Congratulations! You have created two virtual machines, one which represented a virtual machine running a workload in Azure, and which was isolated by running it in a separate network, and another virtual machine which acted as a jump server, which was used to connect to the workload server. You configured the workload virtual machine network interface to allow it to resolve DNS names using the configured external DNS server and you created and configured Azure Firewall through which all traffic from the workload server was routed. You also created rules in Azure Firewall to allow access to a particular website and then verified Azure Firewall functionality.

Note: Remember to delete the resources you have just deployed if you are no longer using them to ensure you do not incur costs for running resources. You can delete all deployed resources by deleting the resource group in which they all reside.