Prerequisites
You require need an Azure subscription to perform these steps. If you don't have one you can create one by following the steps outlined on the Create your Azure free account today webpage.


Steps
Create Azure resources to monitor
Firstly we will deploy some resources to Azure to provide us with some resources to monitor. 
1. Sign into the Azure Portal and click on the Cloud Shell icon in the top right hand corner
2. The Cloud Shell is launched in the bottom of the browser window. 
3. Create a resource group into which we will place our resources by running the following Azure CLI command. You can copy and paste the command from the below directly into the Cloud Shell console, then press Enter to run the command. This command will run fine in either powershell *or* bash console.
```cli
az group create `
--name seccentrg `
--location westeurope
```- 4. Run the below Azure CLI command to create a virtual machine. Again, you can copy and paste the command from below directly into the Cloud Shell console and press Enter to run it.
```cli
az vm create `
--name vmseccent1 `
--resource-group seccentrg `
--image Win2019Datacenter `
--location westeurope `
--admin-username azureuser `
--admin-password Password0134!
```- Note: The command will take 2 to 3 minutes to complete. The command will create a virtual machine and various resources associated with it such as storage, networking and security resources. Do not continue to the next step until the virtual machine deployment is complete. You can close the Azure Cloud Shell once it is complete.
- - Enable Security Center for your subscription
We will now enable Security Center for our subscription
1. Select All services in the upper, left corner of the Azure portal, then select Security, and then select Security Center.- 2. In the Security Center pane click on the GENERAL > Getting Started pane, expand the section Apply your trial on X subscriptions, and ensure you have the subscription that you wish to enable security center for checked, i.e. your free trial subscription, and any other subscription you do not wish to enable security center for, unchecked.- Note: the screenshot above selects a *Visual Studio Ultimate with MSDN* subscription, but the subscription you wish to enable Security Center for may be different i.e. *Azure-Free-Trial*, *Pay-As-You-Go*, or another type subscription
3. Still in the Security Center - Getting Started pane, click the Start trial button.
4. Security Center is now enabled on your subscription. Note the button text change to install agents onto your virtual machine. We have not yet installed any agents on our virtual machine and do not do so yet, we will install agents shortly.- 5. Go to General > Overview and note the various sections and dashboard detail that is available for areas such as the below. 
- Policy & compliance
- secure score: A numerical guide to indicate how secure your resources are. Note the current score value as we will perform an action to increase the score. The screenshot lists a value of 220 our of 280. Your value may be different depending on your environment.
- Regulatory compliance: ISO, Azure CIS, PCI DDS etc
- Policy Management and governance: Ability to integrate policies
- Resource security Hygiene
- Recommendations: security recommendations to consider implementing
- Resource health monitoring: resource monitoring date
- Top recommendations by secure score impact:
- Threat Detection
- Security alerts by severity: security alert data prioritized.
- security alerts over time: cumulative security alert data to provide trend view.
Take a few moments to click into some of the areas that interest you, some areas will not display detailed data as you drill through them, as they are only available on *Standard* tier, and we have only enabled the trial version. Many areas however, do have data available in the free tier, and without having any agents installed.
- Install Agent on virtual machine- 1. In Security Center go to RESOURCE SECURITY HYGIENE > Compute & apps, note the recommendations listed, and the tabs available i.e. Overview, VMs and Computers, VM Scale Sets, Cloud Services, App Services, Containers and Compute resources. Some will be greyed out as we have no resources relevant to that section and some data will can take several hours to populate when initially enabled. Select the Overview tab and click on Install monitoring agent on your virtual machines
- Note: If you have already clicked Install agents for your subscription, this recommendation will not be present in your list of recommendations, even if you have a new virtual machine as it is enabled at subscription level. If the install agent is already enabled, you can proceed to the next task.
- 2. On the subsequent Getting started pane, select the subscription that you have selected for the Security Center trial period, and then click Install agents
3. The agent may take 2 to 3 minutes to install, but continue to refresh the Compute & apps pane until the Install monitoring agent on your virtual machines option is no longer present.
- - Evaluate and Apply a Security recommendation to increase the Secure score value
1. In the RESOURCE SECURITY HYGIENE > Compute & apps blade, Overview tab, note the recommendations listed,
- Resolve monitoring agent health issues on your machines
- Install endpoint protection solution on virtual machines
- Apply disk encryption on your virtual machines
- 2. Still in the Compute & apps > Overview section, select the Apply disk encryption on your virtual machines recommendation- 3. In the subsequent Apply disk encryption on your virtual machines pane, note the detail that is presented, i.e. Description of the issue, General information, Threats, Remediation steps. In the Remediation steps section click on the link https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-overview. You are brought to an Azure docs page providing an overview of what disk encryption involves and detailing how to encrypt your disks. If you wish to enable disk encryption, you can follow the guidelines and steps outlined here.- 4. Return to the Compute & apps > Overview section, select the Resolve monitoring agent health issues on your machines recommendation. Under the Unhealthy resources section at the bottom of the pane, under Monitoring State, note the message i.e. Agent not responsive or missing ID, then click on the link following instructions.
Note: You may not have this message present depending on your subscription and environment, and as such you will not have the same link as outlined here present. You may have a message such as Pending automatic agent installation, or some other message. Regardless of the message present, you can proceed to the next step.
5. On the subsequent https://docs.microsoft.com/en-us/azure/security-center/security-center-troubleshooting-guide#monitoring-agent-health-issues-page, under the Monitoring agent health issues section locate the error message and note the remediation steps listed.
- Note: Again, you can just view the list of potential messages and possible solution detail if you do not have the same message as included in the earlier screenshot. The detail here is provided just for informational purposes, and you can proceed to the next step. - 6. Return to the Compute & apps > Overview section, select the Install endpoint protection solution on virtual machines recommendation. 
7. On the subsequent Endpoint Protection not installed on Azure VMs pane, ensure the virtual machine you created earlier is checked i.e. vmseccent1, and click on the Install on 1 VMs option.- 8. In the subsequent Select Endpoint Protection pane, select Microsoft Antimalware- 9. In the Microsoft Antimalware blade click Create and in the subsequent Install Microsoft Antimalware blade leave all the default values as they are, and click OK.
10. Microsoft Antimalware will start installation.
Note: It could take between 10 to 20 minutes to complete the installation, depending on the environment. If it is taking a prolonged period of time, you can continue with the next topic in the lesson and return to your Azure environment when it has completed.
11. After the installation is complete return to the Return to the Compute & apps > Overview section and note the recommendation to Install endpoint protection solution on virtual machines is no longer present. You may need to refresh the console display if it is still present.
12. Go to the GENERAL > Overview blade and note the Secure score value. Since the completion of the security recommendation, the Secure score value has increased. In the screenshot it has gone up to a value of 285 out of 385, again your value may be different depending on your environment.
- Congratulations! You have created Azure resources to monitor, enabled Security Center for your subscription and from within Security Center, installed an agent on a virtual machine to allow more detailed monitoring. You then evaluated and applied security recommendations to increase the Secure score value in Security center.
- Note: Remember to delete the resources you have just deployed if you are no longer using them to ensure you do not incur costs for running resources. You can delete all deployed resources by deleting the resource group in which they all reside.

