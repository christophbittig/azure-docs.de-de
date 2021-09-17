---
title: Erstellen einer Gastkonfigurationszuweisung mithilfe von Vorlagen
description: Hier erfahren Sie, wie Sie Konfigurationen direkt über den Azure Resource Manager auf Computern bereitstellen.
ms.date: 08/09/2021
ms.topic: how-to
ms.openlocfilehash: aa7938a9421a9e7680af34af475585c4dc1c818a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772921"
---
# <a name="how-to-create-a-guest-configuration-assignment-using-templates"></a>Erstellen einer Gastkonfigurationszuweisung mithilfe von Vorlagen

Die beste Möglichkeit zum [Zuweisen von Gastkonfigurationspaketen](../concepts/guest-configuration-assignments.md) zu mehreren Computern besteht in der Verwendung von [Azure Policy](./guest-configuration-create-definition.md). Sie können Gastkonfigurationspakete auch einem einzelnen Computer zuweisen.

## <a name="built-in-and-custom-configurations"></a>Integrierte und benutzerdefinierte Konfigurationen

Ändern Sie die folgenden Beispiele, um einem einzelnen Computer ein Gastkonfigurationspaket zuzuweisen. Es gibt zwei Szenarios.

- Wenden Sie eine benutzerdefinierte Konfiguration auf einen Computer an, indem Sie einen Link zu einem Paket verwenden, das Sie [veröffentlicht](./guest-configuration-create-publish.md) haben.
- Wenden Sie eine [integrierte](../samples/built-in-packages.md) Konfiguration auf einen Computer an (z. B. eine Azure-Baseline).

## <a name="extending-other-resource-types-such-as-arc-enabled-servers"></a>Erweitern anderer Ressourcentypen (z. B. Arc-fähige Server)

In jedem der folgenden Abschnitte enthält das Beispiel eine **type**-Eigenschaft, bei der der Name mit `Microsoft.Compute/virtualMachines` beginnt. Der Ressourcenanbieter für die Gastkonfiguration `Microsoft.GuestConfiguration` ist eine [Erweiterungsressource](../../../azure-resource-manager/management/extension-resource-types.md), die auf einen übergeordneten Typ verweisen muss.

Um das Beispiel für andere Ressourcentypen wie [Arc-fähige Server](../../../azure-arc/servers/overview.md) zu ändern, ändern Sie den übergeordneten Typ in den Namen des Ressourcenanbieters.
Für Arc-fähige Server ist `Microsoft.HybridCompute/machines` der Ressourcenanbieter.

Ersetzen Sie die folgenden „<>“-Felder durch die Werte, die für Ihre Umgebung spezifisch sind:

- **<vm_name>** : Name der Computerressource, auf die die Konfiguration angewendet wird
- **<configuration_name>** : Name der anzuwendenden Konfiguration
- **<vm_location>** : Azure-Region, in der die Gastkonfigurationszuweisung erstellt wird
- **<Url_to_Package.zip>** : Ein HTTPS-Link zur ZIP-Datei für das benutzerdefinierte Inhaltspaket
- **<SHA256_hash_of_package.zip>** : Ein SHA256-Hash der ZIP-Datei für das benutzerdefinierte Inhaltspaket

## <a name="assign-a-configuration-using-an-azure-resource-manager-template"></a>Zuweisen einer Konfiguration mithilfe einer Azure Resource Manager-Vorlage

Sie können eine [Azure Resource Manager-Vorlage](../../../azure-resource-manager/templates/deployment-tutorial-local-template.md?tabs=azure-powershell) bereitstellen, die Ressourcen für die Zuweisung einer Gastkonfiguration enthält.

Im folgenden Beispiel wird eine benutzerdefinierte Konfiguration zugewiesen.

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "<configuration_name>",
                    "contentUri": "<Url_to_Package.zip>",
                    "contentHash": "<SHA256_hash_of_package.zip>",
                    "assignmentType": "ApplyAndMonitor"
                }
            }
        }
```

Im folgenden Beispiel wird die integrierte Konfiguration `AzureWindowBaseline` zugewiesen.

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "AzureWindowsBaseline",
                    "version": "1.*",
                    "assignmentType": "ApplyAndMonitor",
                    "configurationParameter": [
                    {
                        "name": "Minimum Password Length;ExpectedValue",
                        "value": "16"
                    },
                    {
                        "name": "Minimum Password Length;RemediateValue",
                        "value": "16"
                    },
                    {
                        "name": "Maximum Password Age;ExpectedValue",
                        "value": "75"
                    },
                    {
                        "name": "Maximum Password Age;RemediateValue",
                        "value": "75"
                    }
                ]
                }
            }
        }
```

## <a name="assign-a-configuration-using-bicep"></a>Zuweisen einer Konfiguration mithilfe von Bicep

Sie können [Azure Bicep](../../../azure-resource-manager/bicep/overview.md) verwenden, um Gastkonfigurationszuweisungen bereitzustellen.

Im folgenden Beispiel wird eine benutzerdefinierte Konfiguration zugewiesen.

```Bicep
resource myVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource myConfiguration 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: '<configuration_name>'
  scope: myVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: '<configuration_name>'
      contentUri: '<Url_to_Package.zip>'
      contentHash: '<SHA256_hash_of_package.zip>'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
    }
  }
}
```

Im folgenden Beispiel wird die integrierte Konfiguration `AzureWindowBaseline` zugewiesen.

```Bicep
resource myWindowsVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource AzureWindowsBaseline 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: 'AzureWindowsBaseline'
  scope: myWindowsVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: 'AzureWindowsBaseline'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
      configurationParameter: [
        {
          name: 'Minimum Password Length;ExpectedValue'
          value: '16'
        }
        {
          name: 'Minimum Password Length;RemediateValue'
          value: '16'
        }
        {
          name: 'Maximum Password Age;ExpectedValue'
          value: '75'
        }
        {
          name: 'Maximum Password Age;RemediateValue'
          value: '75'
        }
      ]
    }
  }
}
```

## <a name="assign-a-configuration-using-terraform"></a>Zuweisen einer Konfiguration mithilfe von Terraform

Sie können [Terraform](https://www.terraform.io/) verwenden, um Gastkonfigurationszuweisungen [bereitzustellen](/azure/developer/terraform/get-started-windows-powershell).

> [!IMPORTANT]
> Für den Terraform-Anbieter [azurerm_policy_virtual_machine_configuration_assignment](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_machine_configuration_policy_assignment) wurde kein Update zur Unterstützung der `assignmentType`-Eigenschaft durchgeführt, sodass nur Konfigurationen unterstützt werden, die Überprüfungen durchführen.

Im folgenden Beispiel wird eine benutzerdefinierte Konfiguration zugewiesen.

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "<configuration_name>" {
  name               = "<configuration_name>"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name            = "<configuration_name>"
    contentUri      =  '<Url_to_Package.zip>'
    contentHash     =  '<SHA256_hash_of_package.zip>'
    version         = "1.*"
    assignmentType  = "ApplyAndMonitor
  }
}
```

Im folgenden Beispiel wird die integrierte Konfiguration `AzureWindowBaseline` zugewiesen.

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "AzureWindowsBaseline" {
  name               = "AzureWindowsBaseline"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name    = "AzureWindowsBaseline"
    version = "1.*"
    parameter {
      name  = "Minimum Password Length;ExpectedValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Length;RemediateValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Age;ExpectedValue"
      value = "75"
    }
    parameter {
      name  = "Minimum Password Age;RemediateValue"
      value = "75"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den Artikel mit [Informationen zu Guest Configuration von Azure Policy](../concepts/guest-configuration.md).
- Richten Sie eine [Entwicklungsumgebung](../how-to/guest-configuration-create-setup.md) für ein benutzerdefiniertes Gastkonfigurationspaket ein.
- [Erstellen Sie ein Paketartefakt](../how-to/guest-configuration-create.md) für Gastkonfigurationen.
- [Testen Sie das Paketartefakt](../how-to/guest-configuration-create-test.md) aus Ihrer Entwicklungsumgebung.
- [Veröffentlichen Sie das Paketartefakt](./guest-configuration-create-publish.md), damit es für Ihre Computer zugänglich ist.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy-Definition](../how-to/guest-configuration-create-definition.md) für die skalierungsorientierte Verwaltung Ihrer Umgebung.
- [Weisen Sie Ihre benutzerdefinierten Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Informieren Sie sich, wie Sie die [Compliancedetails für die Richtlinienzuweisungen der Gastkonfiguration](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen.
