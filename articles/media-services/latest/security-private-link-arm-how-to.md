---
title: Erstellen eines Media Services- und Speicherkontos mit einem privaten Link mithilfe einer ARM-Vorlage
titleSuffix: Azure Media Services
description: Erstellen Sie ein Media Services-Konto und ein Speicherkonto mit privaten Links zu einem VNet. Die Azure Resource Manager-Vorlage (ARM) richtet auch DNS für beide privaten Links ein. Schließlich erstellt die Vorlage einen virtuellen Computer, damit der Benutzer die privaten Links ausprobieren kann.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 6182fd0772d4e669a42f2895518139ab2748ae61
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095116"
---
# <a name="create-a-media-services-and-storage-account-with-a-private-link-using-an-arm-template"></a>Erstellen eines Media Services- und Speicherkontos mit einem privaten Link mithilfe einer ARM-Vorlage

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Erstellen Sie ein Media Services-Konto und ein Speicherkonto mit privaten Links zu einem VNet. Die Azure Resource Manager-Vorlage (ARM) richtet auch DNS für beide privaten Links ein. Schließlich erstellt die Vorlage einen virtuellen Computer, damit der Benutzer die privaten Links ausprobieren kann.

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie [Schnellstart: Erstellen und Bereitstellen von ARM-Vorlagen über das Azure-Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="limitations"></a>Einschränkungen

- Für Media Services wird die Vorlage nur für Private Link für Schlüsselbereitstellung eingerichtet.
- Für den virtuellen Computer wird keine Netzwerksicherheitsgruppe erstellt.
- Die Netzwerkzugriffssteuerung ist nicht für das Speicherkonto oder die Schlüsselbereitstellung konfiguriert.

Die Vorlage erstellt:

- Ein Media Services-Konto und ein Speicherkonto (wie gewohnt)
- Ein VNet mit einem Subnetz
- Sowohl für das Media Services- als auch für das Speicherkonto:
  - Private Endpunkte
  - Was ist eine private Azure DNS-Zone?
  - Links zwischen Links (zum Verbinden der privaten DNS-Zonen mit dem VNet)
  - Private DNS-Zonengruppen (um die automatische Erstellung von DNS-Einträgen in den privaten DNS-Zonen auszulösen)
- Eine VM (mit zugeordneter öffentlicher IP-Adresse und Netzwerkschnittstelle)

[!INCLUDE [Azure Policy Media Services](includes/security-azure-policy-private-links.md)]

## <a name="azure-resource-manager-arm-template-for-private-link"></a>Azure Resource Manager (ARM)-Vorlage für Private Link

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "vmAdminUsername": {
      "type": "string"
    },
    "vmAdminPassword": {
      "type": "secureString"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D2_v3"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "storageAccountName": {
      "type": "string"
    },
    "mediaServicesAccountName": {
      "type": "string"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-01-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
    },
    {
      "type": "Microsoft.Media/mediaservices",
      "apiVersion": "2020-05-01",
      "name": "[parameters('mediaServicesAccountName')]",
      "location": "[parameters('location')]",
      "properties": {
        "storageAccounts": [
          {
            "type": "Primary",
            "id": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
          }
        ]
      },
      "identity": {
        "type": "SystemAssigned"
      },
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-08-01",
      "name": "myVnet",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/16"
          ]
        },
        "subnets": [
          {
            "name": "mySubnet",
            "properties": {
              "addressPrefix": "10.0.0.0/24",
              "privateEndpointNetworkPolicies": "Disabled"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/privateEndpoints",
      "apiVersion": "2020-08-01",
      "name": "storagePrivateEndpoint",
      "location": "[parameters('location')]",
      "properties": {
        "subnet": {
          "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
        },
        "privateLinkServiceConnections": [
          {
            "name": "storagePrivateEndpointConnection",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
              "groupIds": [
                "blob"
              ]
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateDnsZones",
      "apiVersion": "2020-06-01",
      "name": "privatelink.blob.core.windows.net",
      "location": "global"
    },
    {
      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
      "apiVersion": "2020-06-01",
      "name": "[format('{0}/storageDnsZoneLink', 'privatelink.blob.core.windows.net')]",
      "location": "global",
      "properties": {
        "registrationEnabled": false,
        "virtualNetwork": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
      "apiVersion": "2020-08-01",
      "name": "[format('{0}/storagePrivateDnsZoneGroup', 'storagePrivateEndpoint')]",
      "properties": {
        "privateDnsZoneConfigs": [
          {
            "name": "config1",
            "properties": {
              "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]"
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]",
        "[resourceId('Microsoft.Network/privateEndpoints', 'storagePrivateEndpoint')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints",
      "apiVersion": "2020-08-01",
      "name": "mediaServicesPrivateEndpoint",
      "location": "[parameters('location')]",
      "properties": {
        "subnet": {
          "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
        },
        "privateLinkServiceConnections": [
          {
            "name": "mediaServicesPrivateEndpointConnection",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.Media/mediaservices', parameters('mediaServicesAccountName'))]",
              "groupIds": [
                "keydelivery"
              ]
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Media/mediaservices', parameters('mediaServicesAccountName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateDnsZones",
      "apiVersion": "2020-06-01",
      "name": "privatelink.media.azure.net",
      "location": "global"
    },
    {
      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
      "apiVersion": "2020-06-01",
      "name": "[format('{0}/mediaServicesDnsZoneLink', 'privatelink.media.azure.net')]",
      "location": "global",
      "properties": {
        "registrationEnabled": false,
        "virtualNetwork": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
      "apiVersion": "2020-08-01",
      "name": "[format('{0}/mediaServicesPrivateDnsZoneGroup', 'mediaServicesPrivateEndpoint')]",
      "properties": {
        "privateDnsZoneConfigs": [
          {
            "name": "config1",
            "properties": {
              "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]"
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]",
        "[resourceId('Microsoft.Network/privateEndpoints', 'mediaServicesPrivateEndpoint')]"
      ]
    },
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2020-08-01",
      "name": "publicIp",
      "location": "[parameters('location')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[toLower(parameters('vmName'))]"
        }
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2020-08-01",
      "name": "vmNetworkInterface",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipConfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'publicIp')]"
              },
              "subnet": {
                "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
              }
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/publicIPAddresses', 'publicIp')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-12-01",
      "name": "myVM",
      "location": "[parameters('location')]",
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[parameters('vmName')]",
          "adminUsername": "[parameters('vmAdminUsername')]",
          "adminPassword": "[parameters('vmAdminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "osDisk",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "managedDisk": {
              "storageAccountType": "Standard_LRS"
            },
            "diskSizeGB": 128
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', 'vmNetworkInterface')]"
            }
          ]
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkInterfaces', 'vmNetworkInterface')]"
      ]
    }
  ],
  "metadata": {
    "_generator": {
      "name": "bicep",
      "version": "0.3.126.58533",
      "templateHash": "2006367938138350540"
    }
  }
}
```