---
title: Erzwingen der Sicherheit mit Richtlinien auf virtuellen Windows-Computern in Azure
description: Anwenden einer Richtlinie auf einen virtuellen Windows-Computer des Azure Resource Manager
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: f245a582b0d3400317086030a67f8698f60f063d
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689324"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Anwenden von Richtlinien auf virtuelle Windows-Computer mit Azure Resource Manager

**Gilt für:** :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Mithilfe von Richtlinien kann eine Organisation verschiedene Konventionen und Regeln im gesamten Unternehmen durchsetzen. Die Durchsetzung des gewünschten Verhaltens hilft dabei, Risiken zu mindern, und trägt gleichzeitig zum Erfolg des Unternehmens bei. In diesem Artikel wird beschrieben, wie Sie Azure Resource Manager-Richtlinien verwenden können, um das gewünschte Verhalten für die virtuellen Computer Ihrer Organisation zu definieren.

Eine Einführung in Richtlinien finden Sie unter [Was ist Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Zugelassene virtuelle Computer
Um sicherzustellen, dass virtuelle Computer für Ihre Organisation mit einer Anwendung kompatibel sind, können Sie die zulässigen Betriebssysteme einschränken. Im folgenden Richtlinienbeispiel lassen Sie nur das Erstellen von virtuellen Windows Server 2012 R2 Datacenter-Computern zu.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Verwenden Sie einen Platzhalter, um die vorhergehende Richtlinie so zu ändern, dass alle Windows Server Datacenter-Images zugelassen werden:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Ändern Sie mit anyOf die vorhergehende Richtlinie so, dass alle Images von Windows Server 2012 R2 Datacenter oder höher zugelassen werden:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

Informationen zu Richtlinienfeldern finden Sie unter [Richtlinienaliase](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Verwaltete Datenträger

Um die Verwendung verwalteter Datenträger erforderlich zu machen, verwenden Sie die folgende Richtlinie:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Images für virtuelle Computer

Aus Gründen der Sicherheit können Sie festlegen, dass nur genehmigte benutzerdefinierte Images in Ihrer Umgebung bereitgestellt werden. Sie können entweder die Ressourcengruppe mit den genehmigten Images oder die spezifischen genehmigten Images angeben.

Für das folgende Beispiel sind Images aus einer genehmigten Ressourcengruppe erforderlich:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

Im folgenden Beispiel werden die IDs der genehmigten Images angegeben:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>VM-Erweiterungen

Möglicherweise möchten Sie die Verwendung bestimmter Erweiterungstypen verbieten. Beispielsweise ist eine Erweiterung unter Umständen nicht mit benutzerdefinierten Images für virtuelle Computer kompatibel. Im folgenden Beispiel wird gezeigt, wie eine bestimmte Erweiterung blockiert wird. Dabei wird mithilfe des Verlegers und des Typs festgelegt, welche Erweiterung blockiert werden soll.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="azure-hybrid-use-benefit"></a>Azure-Vorteil bei Hybridnutzung

Wenn Sie eine lokale Lizenz besitzen, können Sie die Lizenzgebühren für Ihre virtuellen Computer sparen. Wenn Sie keine solche Lizenz besitzen, sollten Sie die Option untersagen. Die folgende Richtlinie verbietet die Verwendung des Azure-Vorteils bei Hybridnutzung (Azure Hybrid Use Benefit, AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
* Nach dem Definieren einer Richtlinienregel (wie in den vorherigen Beispielen zu sehen) müssen Sie die Richtliniendefinition erstellen und einem Bereich zuweisen. Bei dem Bereich kann es sich um ein Abonnement, um eine Ressourcengruppe oder um eine Ressource handeln. Informationen zum Zuweisen von Richtlinien finden Sie unter [Verwenden des Azure-Portals zum Zuweisen und Verwalten von Ressourcenrichtlinien](../../governance/policy/assign-policy-portal.md), [Verwenden von PowerShell zum Zuweisen von Richtlinien](../../governance/policy/assign-policy-powershell.md) und [Verwenden der Azure-Befehlszeilenschnittstelle zum Zuweisen von Richtlinien](../../governance/policy/assign-policy-azurecli.md).
* Eine Einführung in Ressourcenrichtlinien finden Sie unter [Was ist Azure Policy?](../../governance/policy/overview.md).
* Anleitungen dazu, wie Unternehmen Abonnements mit Resource Manager effektiv verwalten können, finden Sie unter [Azure-Unternehmensgerüst - Präskriptive Abonnementgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance).
