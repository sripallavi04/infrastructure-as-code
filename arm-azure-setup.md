1. Understand the Prerequisites
Azure Subscription: Ensure you have an active Azure subscription.
Azure CLI Installed and Configured: Azure CLI should be installed and configured with your Azure account credentials.
Text Editor: Use a text editor like VS Code, Sublime Text, or any other code editor.
2. Create a Project Directory
Open your terminal or command prompt.

Create a new directory for your project and navigate to it.



mkdir arm-azure-project
cd arm-azure-project
Create the ARM Template File:

Inside your project directory, create a new file named template.json.



touch template.json
3. Define the ARM Template
Define the Schema and Content Version:

Specify the schema and content version at the beginning of the file.


{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {}
}
Add Parameters:

Define parameters for flexibility, e.g., for the VM size, admin username, etc.



{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Virtual Machine"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username for the Virtual Machine"
      }
    },
    "adminPassword": {
      "type": "secureString",
      "metadata": {
        "description": "Admin password for the Virtual Machine"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_DS1_v2",
      "metadata": {
        "description": "Size of the Virtual Machine"
      }
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {}
}
Define the Resources:

Add resources such as the Virtual Network, Network Interface, and Virtual Machine.


{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Virtual Machine"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username for the Virtual Machine"
      }
    },
    "adminPassword": {
      "type": "secureString",
      "metadata": {
        "description": "Admin password for the Virtual Machine"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_DS1_v2",
      "metadata": {
        "description": "Size of the Virtual Machine"
      }
    }
  },
  "variables": {
    "location": "[resourceGroup().location]",
    "networkInterfaceName": "[concat(parameters('vmName'), '-nic')]",
    "virtualNetworkName": "[concat(parameters('vmName'), '-vnet')]",
    "subnetName": "default",
    "addressPrefix": "10.0.0.0/16",
    "subnetPrefix": "10.0.0.0/24"
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[variables('virtualNetworkName')]",
      "location": "[variables('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2020-06-01",
      "name": "[variables('networkInterfaceName')]",
      "location": "[variables('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipConfig1",
            "properties": {
              "subnet": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]"
              },
              "privateIPAllocationMethod": "Dynamic"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vmName')]",
      "location": "[variables('location')]",
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS",
            "version": "latest"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
            }
          ]
        }
      }
    }
  ],
  "outputs": {}
}
4. Deploy the ARM Template
Validate the Template:

Use the Azure CLI to validate the ARM template file.



az group create --name <resource-group> --location <location>
Replace <resource-group> with the desired name for your resource group and <location> with the Azure region (e.g., eastus).
Deploy the Template:

Use the Azure CLI to deploy the template.



az deployment group create --resource-group <resource-group> --template-file template.json --parameters vmName=<vm-name> adminUsername=<admin-username> adminPassword=<admin-password>
Replace <resource-group>, <vm-name>, <admin-username>, and <admin-password> with appropriate values.
5. Verify the Deployment
Azure Portal:
Check your Azure portal to verify that the resources were created successfully.
Azure CLI:
Use the Azure CLI to query the resource group and list the resources.


az resource list --resource-group <resource-group>
6. Cleanup Resources
Delete the Resource Group:
When you no longer need the resources, delete the resource group to avoid unnecessary costs.


az group delete --name <resource-group> --yes --no-wait