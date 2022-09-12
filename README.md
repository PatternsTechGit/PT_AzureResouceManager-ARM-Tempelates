# Create Azure Storage Account using Azure Resource Manager (ARM) templates

### **What is ARM in Azure?**

Azure Resource Manager (ARM) is an Azure service you can use to **manage** and **deploy** resources using an **infrastructure as code** paradigm. It enables you to **provision, modify,** and **delete** resources using a variety of features including access controls, tags, and locks.

## **What is an Azure Resource Manager Template?**

Azure Resource Manager templates are **JavaScript Object Notation `(JSON)` files** that define your project's infrastructure and configuration.

Templates use declarative syntax. In this way, you can specify what to deploy without writing a series of commands that specify how to deploy it. Templates specify the resources to be deployed and the properties of those resources.

The template is ready for deployment only after it is validated by Resource Manager. This makes it unlikely that the deployment will fail midway.

The template includes the following parts:

**Parameters** - values that allow you to use the same template in different environments during deployment.

**Variables** - values to be reused in different templates. Variables can use values from parameters.

**User-Defined Functions** - lets you define customized elements to simplify templates.

**Resources** - specifies Azure resources to be deployed.

**Outputs** - the return value of the deployed resource.

-----------------

## IN THIS EXERCISE
In this exercise we will:

1) Download desired **extensions** for VsCode.
2) Create **Blank template file** for Storage account deployment
3) **Adding Azure Resources** in in the template we created
4) Create **Template Parameters**
5) Create **Parameter file** of this storage account
6) Use Azure CLI to **publish** our code

----------------

### **Step1: Download desired VSCode extensions**

There are several VScode extensions that can be useful for this purpose but we will use 2 main Extension

1) **Azure Resource Manager (ARM) tool**
    
    The Azure Resource Manager (ARM) Tools for Visual Studio Code provides language support, resource snippets, and resource auto-completion to help you create and validate Azure Resource Manager templates.

    - For this go tp left panel and **select Extensions**

    - Search for **ARM tool** and select the extension shown in the picture below
    ![](/images/1.jpg)

2) **AZURE CLI**

    The Azure command-line interface (Azure CLI) is a set of commands used to create and manage Azure resources. The Azure CLI is available across Azure services and is designed to get you working quickly with Azure, with an emphasis on automation.

    - Again go to left panel and Install **Azure CLI** as shown below
    ![](/images/2.jpg)

    ------------------

### **Step2: Creating `Json` Deployment File**

- Create new file named `bbbank.json`. 
- Enter **arm** into the code editor, which initiates Azure Resource Manager snippets for scaffolding out an ARM template.
![](/images/3.jpg)
- Select arm! to create a template scoped for an Azure resource group deployment.
- This snippet creates the basic building blocks for an ARM template.
![](/images/4.jpg)

----------------
 ### **Step3: Adding Azure Resources**

 The extension we installed includes snippets for many Azure resources. These snippets can be used to easily add resources to your template deployment.

- For this place the cursor in the template resources block, **type in storage**, and **select the arm-storage snippet**.
![](/images/5.jpg)

- This action adds a storage resource to the template.
![](/images/7.jpg)

----------------
**Step4: Create Template Parameters**

Now create and use a parameter to specify the storage account name.

- Place your cursor in the **parameters block**, write **"para"**, and then select the **new-parameter** snippet. This action adds a generic parameter to the template.
- **Update** the name of the parameter to `bbbank_storage`(storage account name) and the description to Storage Account Name.
- Azure storage account names have a **minimum length** of 3 characters and a **maximum of 24**. Add both` minLength` and `maxLength` to the parameter and provide appropriate values.

- Our parameter file looks like this at this stage
    ```json
    "parameters": {
            "bbbank_storage": {
                "type": "string",
                "metadata": {
                    "description": "creating a test storage ARM template"
                },
                "minLength": 3,
                "maxLength": 24
            }
        },
    ```

- Now, on the storage **resource**, update the name property to use the parameter. To do so, remove the current name. Enter a **double quote** and an opening **square bracket [**, which produces a list of **ARM template functions**. Select parameters from the list.
![](/images/8.jpg)

- Entering a **single quote** ' inside of the round brackets produces a list of all parameters defined in the template, in this case, storageAccountName. Select the parameter.
- After this our Resource section looks like this
    ```json
    "resources": [{
            "name": "[parameters('bbbank_storage')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2021-04-01",
            "tags": {
                "displayName": "BBBankARMStorage"
            },
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            }
        }],
    ```

-------------------

**Step5: Create Parameter file**

An ARM template parameter file allows you to **store environment-specific parameter** values and **pass these values** in as a group at deployment time. For example, you may have a parameter file with values specific to a test environment and another for a production environment.

The extension makes it easy to **create a parameter file** from your **existing templates**. 
- To do so, **right-click** on the template in the code editor and select **Select/Create Parameter File**.
![](/images/9.jpg)

- Select New > All Parameters > Select a name and location for the parameter file.
![](/images/10.jpg)

- Now that the parameter file has been **mapped to the template**, the extension validates both the **template and parameter file together**. 
- To see this validation in practice, **add a two-character value** to the storageAccountName(bbbank_storage) parameter in the parameter file and **save** the file.
![](/images/11.jpg)

- Navigate back to the ARM template and notice that an error has been raised indicating that the value doesn't meet the parameter criteria.

    ![](/images/12.jpg)

- Update the value to something appropriate, save the file, and navigate back to the template. Notice that the error on the parameter has been resolved.

-----------------

**Step6: Deploy these file to Azure**

- First Select Azure Powershell CLI and login to Azure account
![](/images/13.jpg)

- We will create a *resource group* first by the name **ARM-BBBank** and setup a **location East us** by this command in CLI
    ```powershell
    az group create --name ARM-BBBank --location eastus
    ```
    It will look like this if successful
    
    ![](/images/14.jpg)

- Now we will deploy our ARM Template we created with this code

```powershell
az deployment group create --resource-group ARM-bbbank --template-file bbbank.json --parameters bbbank.parameters.json
```
- Here we mentioned that we will be **deploying** a group in the **resource group**(ARM-BBBank) we created in previous step. and **template file** will be `bbbank.json` and its **parameter** file will be `bbbank.parameters.json`

- After it is successful it will give the following result
![](/images/15.jpg)

- You can also verify this in azure portal if this is created or not.

-------------------

### There are many other ways to implement and deploy ARM templates and on the most effective one is by AZURE Portal directly.    
