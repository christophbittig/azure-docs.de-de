---
title: Konfigurieren verwalteter Identitäten auf einem virtuellen Azure-Computer mithilfe einer Vorlage – Azure AD
description: Ausführliche Anweisungen zum Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einer Azure-VM mithilfe einer Azure Resource Manager-Vorlage
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1412b0ff7703d5bcc9950c80d5ab59b4c33cc7ae
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732222"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von Vorlagen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure Resource Manager-Bereitstellungsvorlage die folgenden Vorgänge für verwaltete Identitäten für Azure-Ressourcen auf einer Azure-VM ausführen können:

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit der Verwendung der Azure Resource Manager-Bereitstellungsvorlage vertraut sind, sehen Sie sich die [Übersicht](overview.md) an. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#managed-identity-types)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.

## <a name="azure-resource-manager-templates"></a>Azure-Ressourcen-Manager-Vorlagen

Wie beim Azure-Portal und der Skripterstellung bieten [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-Vorlagen die Möglichkeit, von einer Azure-Ressourcengruppe definierte neue bzw. geänderte Ressourcen bereitzustellen. Für die Vorlagenbearbeitung und -bereitstellung sind mehrere Optionen sowohl lokal als auch portalbasiert verfügbar, einschließlich:

   - Verwenden einer [benutzerdefinierten Vorlage aus Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template). Dies ermöglicht Ihnen, eine ganz neue Vorlage zu erstellen oder eine Vorlage zu verwenden, die auf einer vorhandenen, häufig genutzten Vorlage oder einer [Schnellstartvorlage](https://azure.microsoft.com/resources/templates/) basiert.
   - Ableiten von einer vorhandenen Ressourcengruppe, indem eine Vorlage aus [der ursprünglichen Bereitstellung](../../azure-resource-manager/templates/export-template-portal.md) oder aus dem [aktuellen Status der Bereitstellung](../../azure-resource-manager/templates/export-template-portal.md) exportiert wird.
   - Verwenden eines lokalen [JSON-Editors (z.B. VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) und anschließendes Hochladen und Bereitstellen mithilfe von PowerShell oder CLI.
   - Verwenden des [Azure-Ressourcengruppenprojekts](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) von Visual Studio für das Erstellen und Bereitstellen einer Vorlage.

Unabhängig von der gewählten Option ist die Vorlagensyntax während der ursprünglichen und erneuten Bereitstellung identisch. Die Aktivierung einer vom System oder vom Benutzer zugewiesenen verwalteten Identität auf einer neuen oder vorhandenen VM erfolgt auf dieselbe Weise. Standardmäßig führt Azure Resource Manager außerdem ein [inkrementelles Update](../../azure-resource-manager/templates/deployment-modes.md) für Bereitstellungen durch.

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

In diesem Abschnitt aktivieren und deaktivieren Sie eine vom System zugewiesene verwaltete Identität mithilfe einer Azure Resource Manager-Vorlage.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität beim Erstellen einer Azure-VM oder für eine vorhandene VM

Zum Aktivieren der systemseitig zugewiesenen verwalteten Identität auf einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Verwenden Sie unabhängig davon, ob Sie sich bei Azure lokal oder über das Azure-Portal anmelden, ein Konto, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.

2. Um die vom System zugewiesene verwaltete Identität zu aktivieren, laden Sie die Vorlage in einem Editor, suchen Sie die gewünschte Ressource `Microsoft.Compute/virtualMachines` im Abschnitt `resources`, und fügen Sie die `"identity"`-Eigenschaft in der gleichen Ebene wie die `"type": "Microsoft.Compute/virtualMachines"`-Eigenschaft hinzu. Verwenden Sie die folgende Syntax:

   ```json
   "identity": {
       "type": "SystemAssigned"
   },
   ```

3. Wenn Sie fertig sind, sollten die folgenden Abschnitte dem Abschnitt `resource` der Vorlage hinzugefügt worden sein. Diese sollte wie folgt aussehen:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Zuweisen einer Rolle zur vom System zugewiesenen verwalteten Identität der VM

Nachdem Sie die Option für eine vom System zugewiesene verwaltete Identität auf Ihrer VM aktiviert haben, möchten Sie der Identität möglicherweise eine Rolle wie **Leser** für die Ressourcengruppe gewähren, in der sie erstellt wurde. Ausführliche Informationen zu diesem Schritt finden Sie im Artikel [Zuweisen von Azure-Rollen mit Azure Resource Manager-Vorlagen](../../role-based-access-control/role-assignments-template.md).


### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Deaktivieren einer vom System zugewiesenen verwalteten Identität auf einer Azure-VM

Zum Entfernen der systemseitig zugewiesenen verwalteten Identität von einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Verwenden Sie unabhängig davon, ob Sie sich bei Azure lokal oder über das Azure-Portal anmelden, ein Konto, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.

2. Laden Sie die Vorlage in einen [Editor](#azure-resource-manager-templates), und suchen Sie nach der gewünschten `Microsoft.Compute/virtualMachines`-Ressource im Abschnitt `resources`. Wenn Ihre VM nur über eine vom System zugewiesene verwaltete Identität verfügt, können Sie diese deaktivieren, indem Sie den Identitätstyp in `None` ändern.

   **Microsoft.Compute/virtualMachines-API, Version 2018-06-01**

   Wenn Ihre VM sowohl vom System als auch vom Benutzer zugewiesene verwaltete Identitäten enthält, entfernen Sie `SystemAssigned` aus dem Identitätstyp und behalten `UserAssigned` zusammen mit den `userAssignedIdentities`-Wörterbuchwerten bei.

   **Microsoft.Compute/virtualMachines-API, Version 2018-06-01**

   Wenn `2017-12-01` als `apiVersion` verwendet wird und Ihre VM sowohl vom System als auch vom Benutzer zugewiesene verwaltete Identitäten enthält, entfernen Sie `SystemAssigned` aus dem Identitätstyp und behalten `UserAssigned` zusammen mit dem `identityIds`-Array der vom Benutzer zugewiesenen verwalteten Identitäten bei.

Das folgende Beispiel zeigt, wie Sie eine vom System zugewiesene verwaltete Identität von einem virtuellen Computer ohne vom Benutzer zugewiesene verwaltete Identitäten entfernen:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[parameters('vmName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "None"
    }
}
```

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

In diesem Abschnitt weisen Sie einer Azure-VM mit der Azure Resource Manager-Vorlage eine vom Benutzer zugewiesene verwaltete Identität zu.

> [!NOTE]
> Weitere Informationen zum Erstellen einer vom Benutzer zugewiesenen verwalteten Identität mit einer Azure Resource Manager-Vorlage finden Sie unter [Erstellen einer vom Benutzer zugewiesenen verwalteten Identität](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einem virtuellen Azure-Computer

Für die Zuweisung einer benutzerseitig zugewiesenen Identität zu einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisungen [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Fügen Sie unter dem `resources`-Element den folgenden Eintrag hinzu, um Ihrer VM eine vom Benutzer zugewiesene verwaltete Identität zuzuweisen.  Achten Sie darauf, dass Sie `<USERASSIGNEDIDENTITY>` durch den Namen der vom Benutzer zugewiesenen verwalteten Identität ersetzen, die Sie erstellt haben.

   **Microsoft.Compute/virtualMachines-API, Version 2018-06-01**

   Wenn `2018-06-01` als `apiVersion` verwendet wird, werden die vom Benutzer zugewiesenen verwalteten Identitäten im `userAssignedIdentities`-Wörterbuchformat gespeichert, und der Wert `<USERASSIGNEDIDENTITYNAME>` muss in einer im Abschnitt `variables` der Vorlage definierten Variable gespeichert werden.

   ```json
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Microsoft.Compute/virtualMachines-API, Version 2017-12-01**

   Wenn `2017-12-01` als `apiVersion` verwendet wird, werden die vom Benutzer zugewiesenen verwalteten Identitäten im `identityIds`-Array gespeichert, und der Wert `<USERASSIGNEDIDENTITYNAME>` muss in einer im Abschnitt `variables` der Vorlage definierten Variable gespeichert werden.

   ```json
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. Wenn Sie fertig sind, sollten die folgenden Abschnitte dem Abschnitt `resource` der Vorlage hinzugefügt worden sein. Diese sollte wie folgt aussehen:

   **Microsoft.Compute/virtualMachines-API, Version 2018-06-01**

   ```json
     "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        }
    ] 
   ```

   **Microsoft.Compute/virtualMachines-API, Version 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        }
   ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Entfernen einer vom Benutzer zugewiesenen verwalteten Identität von einer Azure-VM

Für das Entfernen einer benutzerseitig zugewiesenen Identität von einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Verwenden Sie unabhängig davon, ob Sie sich bei Azure lokal oder über das Azure-Portal anmelden, ein Konto, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.

2. Laden Sie die Vorlage in einen [Editor](#azure-resource-manager-templates), und suchen Sie nach der gewünschten `Microsoft.Compute/virtualMachines`-Ressource im Abschnitt `resources`. Wenn Ihr virtueller Computer nur über eine benutzerseitig zugewiesene verwaltete Identität verfügt, können Sie diese deaktivieren, indem Sie den Identitätstyp in `None` ändern.

   Das folgende Beispiel zeigt, wie Sie alle vom Benutzer zugewiesenen verwalteten Identitäten von einer VM ohne vom System zugewiesene verwaltete Identitäten entfernen:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft.Compute/virtualMachines-API, Version 2018-06-01**

   Um eine einzelne vom Benutzer zugewiesene verwaltete Identität von einem virtuellen Computer zu entfernen, entfernen Sie diese aus dem `useraAssignedIdentities`-Wörterbuch.

   Wenn Sie eine vom System zugewiesene verwaltete Identität verwenden, behalten Sie den Wert `type` unter dem Wert `identity` bei.

   **Microsoft.Compute/virtualMachines-API, Version 2017-12-01**

   Um eine einzelne benutzerseitig zugewiesene verwaltete Identität von einem virtuellen Computer zu entfernen, entfernen Sie diese aus dem `identityIds`-Array.

   Wenn Sie eine vom System zugewiesene verwaltete Identität verwenden, behalten Sie den Wert `type` unter dem Wert `identity` bei.

## <a name="next-steps"></a>Nächste Schritte

- [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](overview.md)
