Prerequisites
You require need an Azure subscription to perform these steps. If you don't have one you can create one by following the steps outlined on the Create your Azure free account today webpage.

Steps
- Create Azure resources to allow us to apply Tags to them
Firstly, we will deploy some resources to Azure to provide us with some resources to use Tags with. If you have resources available from a previous deployment, you can use those instead of deploying new ones.
1. Sign into the Azure Portal and click on the Cloud Shell icon in the top right hand corner
2. The Cloud Shell is launched in the bottom of the browser window. 
3. Create a resource group into which we will place our resources by running the following Azure CLI command. You can copy and paste the command from the below directly into the Cloud Shell console, then press Enter to run the command. This command will run fine in either powershell or bash console.
```cli
az group create `
--name tagrg `
--location westeurope
```
4. Run the below Azure CLI command to create a virtual machine. Again, you can copy and paste the command from below directly into the Cloud Shell console and press Enter to run it.
```cli
az vm create `
--name vmtag1 `
--resource-group tagrg `
--image Win2019Datacenter `
--location westeurope `
--admin-username azureuser `
--admin-password Password0134!
```
- Note: The command will take 2 to 3 minutes to complete. The command will create a virtual machine and various resources associated with it such as storage, networking and security resources. You can close the Azure Cloud Shell once it is complete. 
- View the tags for a resource or a resource group
1. In the Azure Portal, open the resource group we just created i.e. tagrg, on the Overview pane alongside Tags, note there are no values listed and a message reads Click here to add tags. This indicates no tags have been applied to the resource group or resources.
- Add tags 
1. Still in the Tagrg resource group click on the Click here to add tags link and in the subsequent Tags pane enter the values below, click Save and then Close
- Name: Environment
- Value: Production
2. In the Tagrg resource group Overview section, the Tags section now has the tag Name:Value pair of Environment: Production present
3. Open up the virtual machine resource and in the Overview pane note that the tags assigned to the resource group are not present in the resource beneath it.
- Bulk assign tags to resources
1. Return to the resource group tagrg check the checkboxes beside all resources listed under the resource group by clicking on the NAME checkbox then click the Assign Tags button
2. In the Tags pane enter the values below, click Save and then Close
- Name: Department
- Value: IT
and
- Name: Environment
- Value: Production
3. Open up the virtual machine resource and note the presence of the two tags Department:IT and Environment:Production
- - View all resources with a specific tag
1. In the azure Portal go to All Services type the text tags in the search box. The Tags service appears and you can open it.
2. In the Tags pane a number of tags are listed. To view all resources with a specific tag, click the Name:Value pair that you want to view resources for i.e. click Department:IT
3. After clicking on the Name Value pair Department:IT, in the resultant Department:IT pane, all resources with this *Name:Value* pair are listed
Note: For quick access, you could pin the Tags service view to the dashboard.
- Delete tags
1. In the resource group you created earlier i.e. tagrg, open the virtual machine and click on the Tags section. In the resultant Tags pane we have an option to Delete All tags, or click the *dustbin icon* beside individual *Name:Value* pairs, to delete individual tags. Click Delete All then click Save and Close
2. In the virtual machine Overview pane note that the values for Tags has returned of the link stating Click here to add tags., indicating there are no tags now present.
- Congratulations! You have created Azure resources to allow us to apply Tags to them, you then viewed the tags for a resource and a resource group, and then added Tags. You then bulk assigned tags to resource groups and resources and viewed all resources with a specific tag. You finally deleted assigned tags.
- - Note: Remember to delete the resources you have just deployed, if they are still present and you are no longer using them to ensure you do not incur costs for running resources. You can delete all deployed resources by deleting the resource group in which they all reside.
