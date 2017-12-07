# localNetworkGateways
Reference deployment
```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountName": {
      "type": "string",
      "defaultValue": ""
    },
    "ContainerName": {
      "type": "string",
      "defaultValue": ""
    },
    "SasToken": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "variables": {
    "Provider": "/Microsoft.Network",
    "Resource": "/localNetworkGateways",
    "templateUri": "[concat('https://',parameters('StorageAccountName'),'.blob.core.windows.net/',parameters('ContainerName'),variables('Provider'),variables('Resource'))]"
  },
  "resources": [
    {
      "name": "BuildBgpSettings-001",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2016-09-01",
      "dependsOn": [ ],
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(variables('templateUri'), '/bgpSettings.json', parameters('SasToken'))]",
          "contentVersion": "2017.09.01.0"
        },
        "parameters": {
          "bgpPeeringAddress": {
            "value": ""
          }
        }
      }
    },
    {
      "name": "BuildAddressSpace-001",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2016-09-01",
      "dependsOn": [ ],
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(variables('templateUri'), '/addressSpace.json', parameters('SasToken'))]",
          "contentVersion": "2017.09.01.0"
        },
        "parameters": {
          "AddressPrefixes": {
            "value": "10.0.0.0/8"
          }
        }
      }
    },
    {
      "name": "BuildAddressSpace-002",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2016-09-01",
      "dependsOn": [ ],
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(variables('templateUri'), '/addressSpace.json', parameters('SasToken'))]",
          "contentVersion": "2017.09.01.0"
        },
        "parameters": {
          "AddressPrefixes": {
            "value": "172.16.0.0/16"
          }
        }
      }
    },
    {
      "name": "BuildLocalNetworkGateway-001",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2016-09-01",
      "dependsOn": [ ],
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(variables('templateUri'), '/localNetworkGateways.json', parameters('SasToken'))]",
          "contentVersion": "2017.09.01.0"
        },
        "parameters": {
          "bgpSettings": {
            "value": "[reference('BuildBgpSettings-001').outputs.bgpSettings.value]"
          },
          "gatewayIpAddress": {
            "value": "1.2.3.4"
          },
          "localNetworkAddressSpace": {
            "value": "[createArray(reference('BuildAddressSpace-001').outputs.addressPrefixes.value,reference('BuildAddressSpace-002').outputs.addressPrefixes.value)]"
          },
          "name": {
            "value": "test-lng"
          }
        }
      }
    }
  ],
  "outputs": {
    "localNetworkGateways": {
      "type": "object",
      "value": "[reference('BuildLocalNetworkGateway-001').outputs.localNetworkGateway.value]"
    }
  }
}
```
