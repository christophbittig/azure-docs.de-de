---
title: Azure Policy-Beispiele für das Onboarding von Ressourcen in Azure Chaos Studio
description: Azure-Beispielrichtlinien zum Integrieren von Ressourcen in Azure Chaos Studio mithilfe von Zielen und Funktionen
services: chaos-studio
author: johnkemnetz
ms.topic: sample
ms.date: 11/11/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: 9e9a0d3f08ec3f29f28d72433cf880448e0602ee
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404444"
---
# <a name="azure-policy-samples-for-onboarding-resources-to-azure-chaos-studio"></a>Azure Policy-Beispiele für das Onboarding von Ressourcen in Azure Chaos Studio
Dieser Artikel enthält [Azure Policy](../governance/policy/overview.md)-Beispieldefinitionen, mit denen [Ziele und Funktionen](chaos-studio-targets-capabilities.md) für einen bestimmten Ressourcentyp erstellt werden. Sie können Ressourcen automatisch in Chaos Studio integrieren, indem Sie [diese Beispiele als benutzerdefinierte Richtliniendefinitionen bereitstellen](../governance/policy/tutorials/create-custom-policy-definition.md) und die [Richtlinie einem Bereich zuweisen](../governance/policy/assign-policy-portal.md).

In diesen Beispielen integrieren Sie direkte Dienstziele und -funktionen für jeden [unterstützten Ressourcentyp](chaos-studio-fault-providers.md) mithilfe von [Zielen und Funktionen](chaos-studio-targets-capabilities.md).

## <a name="azure-cache-for-redis-policy-definition"></a>Richtliniendefinition für Azure Cache for Redis

```json
{
  "displayName": "Deploy Chaos Target and Capability for Azure Cache for Redis",
  "policyType": "Custom",
  "mode": "Indexed",
  "metadata": {
    "category": "Chaos Studio"
  },
  "description": "Deploys the target and capabilities for an Azure Cache for Redis instance for onboarding to Azure Chaos Studio."
  "parameters": {
    "effect": {
      "type": "String",
      "metadata": {
        "displayName": "Effect",
        "description": "Enable or disable the execution of the policy"
      },
      "allowedValues": [
        "DeployIfNotExists",
        "Disabled"
      ],
      "defaultValue": "DeployIfNotExists"
    }
  },
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.Cache/Redis"
    },
    "then": {
      "effect": "[parameters('effect')]",
      "details": {
        "type": "Microsoft.Chaos/targets",
        "name": "Microsoft-AzureCacheForRedis",
        "roleDefinitionIds": [
          "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "deployment": {
          "properties": {
            "mode": "incremental",
            "template": {
              "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "parameters": {
                "resourceName": {
                  "type": "string"
                },
                "location": {
                  "type": "string"
                }
              },
              "variables": {},
              "resources": [
                {
                  "type": "Microsoft.Cache/Redis/providers/targets",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureCacheForRedis')]",
                  "location": "[parameters('location')]",
                  "properties": {}
                },
                {
                  "type": "Microsoft.Cache/Redis/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureCacheForRedis/Reboot-1.0')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.Cache/Redis', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureCacheForRedis')]"
                  ],
                  "properties": {}
                }
              ],
              "outputs": {}
            },
            "parameters": {
              "resourceName": {
                "value": "[field('name')]"
              },
              "location": {
                "value": "[field('location')]"
              }
            }
          }
        }
      }
    }
  }
}
```

## <a name="azure-cosmos-db-policy-definition"></a>Richtliniendefinition für Azure Cosmos DB

```json
{
  "displayName": "Deploy Chaos Target and Capability for Cosmos DB",
  "policyType": "Custom",
  "mode": "Indexed",
  "description": "Deploys the target and capabilities for a Cosmos DB for onboarding to Azure Chaos Studio.",
  "metadata": {
    "category": "Chaos Studio"
  },
  "parameters": {
    "effect": {
      "type": "String",
      "metadata": {
        "displayName": "Effect",
        "description": "Enable or disable the execution of the policy"
      },
      "allowedValues": [
        "DeployIfNotExists",
        "Disabled"
      ],
      "defaultValue": "DeployIfNotExists"
    }
  },
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.DocumentDB/databaseAccounts"
    },
    "then": {
      "effect": "[parameters('effect')]",
      "details": {
        "type": "Microsoft.Chaos/targets",
        "name": "Microsoft-CosmosDB",
        "roleDefinitionIds": [
          "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "deployment": {
          "properties": {
            "mode": "incremental",
            "template": {
              "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "parameters": {
                "resourceName": {
                  "type": "string"
                },
                "location": {
                  "type": "string"
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
                    "[concat(resourceId('Microsoft.DocumentDB/databaseAccounts', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-CosmosDB')]"
                  ],
                  "properties": {}
                }
              ],
              "outputs": {}
            },
            "parameters": {
              "resourceName": {
                "value": "[field('name')]"
              },
              "location": {
                "value": "[field('location')]"
              }
            }
          }
        }
      }
    }
  }
}
```

## <a name="azure-kubernetes-service-policy-definition"></a>Richtliniendefinition für Azure Kubernetes Service

```json
{
  "displayName": "Deploy Chaos Target and Capabilities for Azure Kubernetes Service",
  "policyType": "Custom",
  "mode": "Indexed",
  "description": "Deploys the target and capabilities for an AKS cluster for onboarding to Azure Chaos Studio.",
  "metadata": {
    "category": "Chaos Studio"
  },
  "parameters": {
    "effect": {
      "type": "String",
      "metadata": {
        "displayName": "Effect",
        "description": "Enable or disable the execution of the policy"
      },
      "allowedValues": [
        "DeployIfNotExists",
        "Disabled"
      ],
      "defaultValue": "DeployIfNotExists"
    }
  },
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.ContainerService/managedClusters"
    },
    "then": {
      "effect": "[parameters('effect')]",
      "details": {
        "type": "Microsoft.Chaos/targets",
        "name": "Microsoft-AzureKubernetesServiceChaosMesh",
        "roleDefinitionIds": [
          "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "deployment": {
          "properties": {
            "mode": "incremental",
            "template": {
              "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "parameters": {
                "resourceName": {
                  "type": "string"
                },
                "location": {
                  "type": "string"
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
                    "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
                  ],
                  "properties": {}
                },
                {
                  "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/PodChaos-2.1')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
                  ],
                  "properties": {}
                },
                {
                  "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/StressChaos-2.1')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
                  ],
                  "properties": {}
                },
                {
                  "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/IOChaos-2.1')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
                  ],
                  "properties": {}
                },
                {
                  "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/TimeChaos-2.1')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
                  ],
                  "properties": {}
                },
                {
                  "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/KernelChaos-2.1')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
                  ],
                  "properties": {}
                },
                {
                  "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/DNSChaos-2.1')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
                  ],
                  "properties": {}
                },
                {
                  "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/HTTPChaos-2.1')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
                  ],
                  "properties": {}
                }
              ],
              "outputs": {}
            },
            "parameters": {
              "resourceName": {
                "value": "[field('name')]"
              },
              "location": {
                "value": "[field('location')]"
              }
            }
          }
        }
      }
    }
  }
}
```

## <a name="azure-network-security-group-policy-definition"></a>Richtliniendefinition für Azure-Netzwerksicherheitsgruppen

```json
{
  "displayName": "Deploy Chaos Target and Capability for Network Security Groups",
  "policyType": "Custom",
  "mode": "Indexed",
  "description": "Deploys the target and capabilities for a network security group for onboarding to Azure Chaos Studio.",
  "metadata": {
    "category": "Chaos Studio"
  },
  "parameters": {
    "effect": {
      "type": "String",
      "metadata": {
        "displayName": "Effect",
        "description": "Enable or disable the execution of the policy"
      },
      "allowedValues": [
        "DeployIfNotExists",
        "Disabled"
      ],
      "defaultValue": "DeployIfNotExists"
    }
  },
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.Network/networkSecurityGroups"
    },
    "then": {
      "effect": "[parameters('effect')]",
      "details": {
        "type": "Microsoft.Chaos/targets",
        "name": "Microsoft-NetworkSecurityGroup",
        "roleDefinitionIds": [
          "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "deployment": {
          "properties": {
            "mode": "incremental",
            "template": {
              "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "parameters": {
                "resourceName": {
                  "type": "string"
                },
                "location": {
                  "type": "string"
                }
              },
              "variables": {},
              "resources": [
                {
                  "type": "Microsoft.Network/networkSecurityGroups/providers/targets",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-NetworkSecurityGroup')]",
                  "location": "[parameters('location')]",
                  "properties": {}
                },
                {
                  "type": "Microsoft.Network/networkSecurityGroups/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-NetworkSecurityGroup/SecurityRule-1.0')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.Network/networkSecurityGroups', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-NetworkSecurityGroup')]"
                  ],
                  "properties": {}
                }
              ],
              "outputs": {}
            },
            "parameters": {
              "resourceName": {
                "value": "[field('name')]"
              },
              "location": {
                "value": "[field('location')]"
              }
            }
          }
        }
      }
    }
  }
}
```

## <a name="azure-virtual-machines-policy-definition"></a>Richtliniendefinition für Azure Virtual Machines

```json
{
  "displayName": "Deploy Chaos Target and Capability for Virtual Machines (service-direct)",
  "policyType": "Custom",
  "mode": "Indexed",
  "description": "Deploys the target and capabilities for a virtual machine for onboarding to Azure Chaos Studio (service-direct faults).",
  "metadata": {
    "category": "Chaos Studio"
  },
  "parameters": {
    "effect": {
      "type": "String",
      "metadata": {
        "displayName": "Effect",
        "description": "Enable or disable the execution of the policy"
      },
      "allowedValues": [
        "DeployIfNotExists",
        "Disabled"
      ],
      "defaultValue": "DeployIfNotExists"
    }
  },
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
      "effect": "[parameters('effect')]",
      "details": {
        "type": "Microsoft.Chaos/targets",
        "name": "Microsoft-VirtualMachine",
        "roleDefinitionIds": [
          "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "deployment": {
          "properties": {
            "mode": "incremental",
            "template": {
              "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "parameters": {
                "resourceName": {
                  "type": "string"
                },
                "location": {
                  "type": "string"
                }
              },
              "variables": {},
              "resources": [
                {
                  "type": "Microsoft.Compute/virtualMachines/providers/targets",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-VirtualMachine')]",
                  "location": "[parameters('location')]",
                  "properties": {}
                },
                {
                  "type": "Microsoft.Compute/virtualMachines/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-VirtualMachine/Shutdown-1.0')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.Compute/virtualMachines', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-VirtualMachine')]"
                  ],
                  "properties": {}
                }
              ],
              "outputs": {}
            },
            "parameters": {
              "resourceName": {
                "value": "[field('name')]"
              },
              "location": {
                "value": "[field('location')]"
              }
            }
          }
        }
      }
    }
  }
}
```

## <a name="azure-virtual-machine-scale-sets-policy-definition"></a>Richtliniendefinition für Azure Virtual Machine Scale Sets

```json
{
  "displayName": "Deploy Chaos Target and Capability for Virtual Machine Scale Sets (service-direct)",
  "policyType": "Custom",
  "mode": "Indexed",
  "description": "Deploys the target and capabilities for virtual machine scale sets for onboarding to Azure Chaos Studio (service-direct faults).",
  "metadata": {
    "category": "Chaos Studio"
  },
  "parameters": {
    "effect": {
      "type": "String",
      "metadata": {
        "displayName": "Effect",
        "description": "Enable or disable the execution of the policy"
      },
      "allowedValues": [
        "DeployIfNotExists",
        "Disabled"
      ],
      "defaultValue": "DeployIfNotExists"
    }
  },
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachineScaleSets"
    },
    "then": {
      "effect": "[parameters('effect')]",
      "details": {
        "type": "Microsoft.Chaos/targets",
        "name": "Microsoft-VirtualMachineScaleSet",
        "roleDefinitionIds": [
          "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "deployment": {
          "properties": {
            "mode": "incremental",
            "template": {
              "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "parameters": {
                "resourceName": {
                  "type": "string"
                },
                "location": {
                  "type": "string"
                }
              },
              "variables": {},
              "resources": [
                {
                  "type": "Microsoft.Compute/virtualMachineScaleSets/providers/targets",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-VirtualMachineScaleSet')]",
                  "location": "[parameters('location')]",
                  "properties": {}
                },
                {
                  "type": "Microsoft.Compute/virtualMachineScaleSets/providers/targets/capabilities",
                  "apiVersion": "2021-09-15-preview",
                  "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-VirtualMachineScaleSet/Shutdown-1.0')]",
                  "location": "[parameters('location')]",
                  "dependsOn": [
                    "[concat(resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('resourceName')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-VirtualMachineScaleSet')]"
                  ],
                  "properties": {}
                }
              ],
              "outputs": {}
            },
            "parameters": {
              "resourceName": {
                "value": "[field('name')]"
              },
              "location": {
                "value": "[field('location')]"
              }
            }
          }
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Chaos Studio](chaos-studio-overview.md)
* [Informieren Sie sich ausführlicher über Ziele und Funktionen.](chaos-studio-targets-capabilities.md)
