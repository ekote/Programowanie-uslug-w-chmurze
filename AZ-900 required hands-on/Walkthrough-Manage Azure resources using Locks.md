Prerequisites
You require need an Azure subscription to perform these steps. If you don't have one you can create one by following the steps outlined on the Create your Azure free account today webpage.

- Steps
Create Azure resources to allow us to create a lock against them
Firstly, we will deploy some resources to Azure to provide us with some resources to manage. If you have resources available from a previous deployment, you can use those instead of deploying new ones.
1. Sign into the Azure Portal and click on the Cloud Shell icon in the top right hand corner
2. The Cloud Shell is launched in the bottom of the browser window. 
3. Create a resource group into which we will place our resources by running the following Azure CLI command. You can copy and paste the command from the below directly into the Cloud Shell console, then press Enter to run the command. This command will run fine in either powershell *or* bash console.
```cli
az group create `
--name lockscrg `
--location westeurope
```
4. Run the below Azure CLI command to create a virtual machine. Again, you can copy and paste the command from below directly into the Cloud Shell console and press Enter to run it.
```cli
az vm create `
--name vmlocks1 `
--resource-group lockscrg `
--image Win2019Datacenter `
--location westeurope `
--admin-username azureuser `
--admin-password Password0134!
```
Note: The command will take 2 to 3 minutes to complete. The command will create a virtual machine and various resources associated with it such as storage, networking and security resources. You can close the Azure Cloud Shell once it is complete.
- Add a Lock to prevent deletion of a resource
You can apply a Lock to a subscription, resource group, or individual resource to prevent other users in your organization from accidentally deleting or modifying critical resources. We will apply a lock to a resource, however it is the same process regardless of the scope in which it is used.
1. In the Azure Portal go to the resource group you just created i.e. lockscrg, then go to Settings > Locks
2. To add a lock, select Add and then enter the following values, clicking OK when finished.
- Lock name: resource group lock
- Lock Type: Delete ( the other lock type available to us here is *Read-only*)
3. Now let us try delete the resource group we just created the Delete lock for, by going to the resource group i.e. lockscrg, then the clicking on Overview and selecting Delete resource group
4. In the Are you sure you want to delete "lockscrg"? blade, type the name of the resource group i.e.lockscrg and click the Delete button
5. You receive an error message stating the resource group is locked and can't be deleted
6. Within the same resource group open the virtual machine i.e.vmlocks1, go to the Overview pane and select Delete
7. In the resultant Delete virtual machine pane, select Yes to confirm your wish to delete the virtual machine.
8. You receive an error message stating
Note: Although we did not create a lock specifically for the virtual machine, we did create a lock at the resource group level, which contains the virtual machine resource. As such this *parent* level lock prevents us from deleting the virtual machine, the virtual machine resource inherits the lock from the parent.
- Remove a lock
1. Return to the resource group and go to Settings > Locks
2. Click the ellipsis at the end of the Delete lock that you created earlier and select Delete on the resultant menu
3. In the ressource group Overview pane, click on Delete resource group an din the resultant Are you sure you want to delete "lockscrg"? pane, type in the name of the resource group and click Delete to delete the resource group.
The resource group and all resources within it are successfully deleted now, because the delete lock has been removed.
- Congratulations! You have created Azure resources to allow us to create a lock against them, then you added a Delete Lock to prevent deletion of a resource group. You then verified that deletion of the resource group was indeed blocked as a result of the created Lock, and also that any resources within the resource group were also blocked from being deleted by the parent Lock. You then removed the lock and verified it has been removed by successfully deleting the resource group.
- - Note: Remember to delete the resources you have just deployed, if they are still present and you are no longer using them to ensure you do not incur costs for running resources. You can delete all deployed resources by deleting the resource group in which they all reside.

