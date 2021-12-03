---
title: Azure Resource Manager-Vorlagenbeispiele für Ziele und Funktionen in Azure Chaos Studio
description: Azure Resource Manager-Beispielvorlagen zum Integrieren von Ressourcen in Azure Chaos Studio mithilfe von Zielen und Funktionen
services: chaos-studio
author: johnkemnetz
ms.topic: sample
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: 6e5c6b0610d2a2d48523a5d2a7a95c28cd8bae59
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404440"
---
# <a name="resource-manager-template-samples-for-targets-and-capabilities-in-azure-chaos-studio"></a>Resource Manager-Vorlagenbeispiele für Ziele und Funktionen in Azure Chaos Studio
Dieser Artikel enthält Beispiele für [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/syntax.md) zum Erstellen von [Zielen und Funktionen](chaos-studio-targets-capabilities.md) zum Integrieren einer Ressource in Azure Chaos Studio. Jedes Beispiel umfasst eine Vorlagendatei und eine Parameterdatei mit Beispielwerten für die Vorlage.

## <a name="onboard-service-direct-target-and-capabilities-single-capability"></a>Onboarding von direkten Dienstzielen und -funktionen (einzelne Funktion)

In diesem Beispiel integrieren Sie eine Azure Cosmos DB-Instanz [mithilfe von Zielen und Funktionen](chaos-studio-targets-capabilities.md). Die Vorlage kann für alle direkten Dienstziele und -funktionen geändert werden, indem sie auf die [Fehlerbibliothek](chaos-studio-fault-library.md) verweisen.

### <a name="template-file"></a>Vorlagendatei

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource being enabled."
      }
    },
    "resourceGroup": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group where the resource being enabled is located."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location"
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.DocumentDB/databaseAccounts/providers/targets",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-CosmosDB')]",
      "location": "[parameters('location')]",
      "properties": {}
    },
    {
      "type": "Microsoft.DocumentDB/databaseAccounts/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-CosmosDB/Failover-1.0')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.DocumentDB/databaseAccounts', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-CosmosDB')]"
      ],
      "properties": {}
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "resourceName": {
        "value": "my-cosmos-db"
      },
      "resourceGroup": {
        "value": "my-rg"
      }
  }
}
```

## <a name="onboard-service-direct-target-and-capabilities-multiple-capabilities"></a>Onboarding von direkten Dienstzielen und -funktionen (mehrere Funktionen)

In diesem Beispiel integrieren Sie eine Azure Kubernetes Service-Instanz [mithilfe von Zielen und Funktionen](chaos-studio-targets-capabilities.md).

### <a name="template-file"></a>Vorlagendatei

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource being enabled."
      }
    },
    "resourceGroup": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group where the resource being enabled is located."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location"
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh')]",
      "location": "[parameters('location')]",
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/NetworkChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/PodChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/StressChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/IOChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/TimeChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/KernelChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/DNSChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/HTTPChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "resourceName": {
        "value": "my-aks-cluster"
      },
      "resourceGroup": {
        "value": "my-rg"
      }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Chaos Studio](chaos-studio-overview.md)
* [Informieren Sie sich ausführlicher über Ziele und Funktionen.](chaos-studio-targets-capabilities.md)
