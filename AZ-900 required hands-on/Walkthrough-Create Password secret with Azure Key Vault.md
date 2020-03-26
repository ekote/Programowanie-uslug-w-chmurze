Prerequisites
You require need an Azure subscription to perform these steps. If you don't have one you can create one by following the steps outlined on the Create your Azure free account today webpage.

Steps
Create a vault in Azure Key Vault
Firstly we will create a vault
1. Sign into the Azure Portal and go to All services > Security and then select Key vaults.
2. In the Key vaults pane click on Create key vault.- 3. In the Create key vault blade, enter the details as below and click Create
- Name: a name for your vault i.e. akvtest1
- Subscription: < your subscription >
- Resource Group: select Create new and enter a new resource group name i.e. akvrg
- Location: < a Datacenter location near you i.e. Central US >
- Pricing Tier: Standard
- Access policies: < accept default value i.e. 1 principal selected >
- Virtual Network Access: < accept default value i.e. all networks can access >
4. Go to the newly created Key vault and verify it is present. You can take a moment to browse through some of the options available within it, primarily under Settings and then options concerning Keys, Secrets, Certificates, Access Policies, Firewalls and virtual networks.
5. Take note of two values in the key vault
- Vault Name: In the example it is akvtest1
- DNS name (also sometimes referred to as the Vault URI): In this example it is `https://akvtest1.vault.azure.net/`. Applications that use your vault through its REST API must use this URI.
Note: Your Azure account is the only one authorized to perform operations on this new vault. Yo can modify this if you wish in the Settings > Access policies section
- Add a secret to the Key Vault
We will now add a password that could be used by an application.
1. On the Key Vault properties pages select Secrets, then select Generate/Import.
2. On the Create a secret blade enter the below values, leave the other values at their defaults and then click Create.
- Upload options: Manual
- Name: ExamplePassword
- Value: hVFkk965BuUv96!
3. Once the secret has been successfully created, on the Secrets pane, click on the ExamplePassword, and note it has a status of Enabled
4. Double click on the password and in the password pane, note the presence of the Secret Identifier. This is the url value that you can now use with applications. It provides a centrally managed and securely stored password for use with applications.
5. In the same pane click the button Show Secret Value, to display the password you specified earlier.
Note: It is also possible to set time limitations on when a password is available for use, using the activation and expiration date settings.
- Congratulations! You have created an Azure Key vault and then created a password secret in that key vault, providing a securely stored, centrally managed password for use with applications.
- Note: Remember to delete the resources you have just deployed if you are no longer using them to ensure you do not incur costs for running resources. You can delete all deployed resources by deleting the resource group in which they all reside.

