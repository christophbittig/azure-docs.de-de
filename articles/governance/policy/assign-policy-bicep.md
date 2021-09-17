---
title: 'Schnellstart: Neue Richtlinienzuweisung mit einer Bicep (Vorschau)-Datei'
description: In dieser Schnellstartanleitung erstellen Sie mit einer Bicep (Vorschau)-Datei eine Richtlinienzuweisung zum Identifizieren von nicht konformen Ressourcen.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 116058395d556d4e5f7d8a9c67867376e703a7cc
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323750"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren von nicht konformen Ressourcen mit einer Bicep-Datei

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln.
Diese Schnellstartanleitung führt Sie schrittweise durch die Erstellung einer Richtlinienzuweisung zum Identifizieren von virtuellen Computern, die keine verwalteten Datenträger verwenden. Dazu wird eine [Bicep (Vorschau)-Datei](https://github.com/Azure/bicep) verwendet, die als Azure Resource Manager-Vorlage (ARM-Vorlage) kompiliert wird. Am Ende dieses Prozesses können Sie erfolgreich virtuelle Computer identifizieren, die keine verwalteten Datenträger verwenden. Sie sind mit der Richtlinienzuweisung _nicht konform_.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Schaltfläche zum Bereitstellen der ARM-Vorlage zum Zuweisen einer Azure-Richtlinie zu Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.authorization%2Fazurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Eine BICEP-Version `0.3` oder höher ist installiert. Weitere Informationen für den Fall, dass Sie noch nicht über die BICEP-CLI verfügen oder ein Update vornehmen müssen, finden Sie unter [Installieren von BICEP (Vorschau)](../../azure-resource-manager/bicep/install.md).

## <a name="review-the-bicep-file"></a>Überprüfen der Bicep-Datei

In dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung und weisen eine integrierte Richtliniendefinition mit dem Namen _Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden_ (`06a78e20-9358-41c9-923c-fb736d382a4d`) zu. Eine Liste mit einem Teil der verfügbaren integrierten Richtlinien finden Sie unter [Azure Policy-Beispiele](./samples/index.md).

Erstellen Sie die folgende BICEP-Datei als `assignment.bicep`:

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2021-09-01' = {
    name: policyAssignmentName
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    properties: {
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

In der Vorlage ist die folgende Datei definiert:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

> [!NOTE]
> Der Azure Policy-Dienst ist kostenlos. Weitere Informationen finden Sie in der [Übersicht über Azure Policy](./overview.md).

Nachdem die BICEP-CLI installiert und die Datei erstellt wurde, können Sie die Bicep-Datei bereitstellen mit:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

Einige weitere Ressourcen:

- Weitere Beispielvorlagen finden Sie unter [Azure-Schnellstartvorlage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Navigieren Sie zur [Azure-Vorlagenreferenz](/azure/templates/microsoft.authorization/allversions), um die Vorlagenreferenz anzuzeigen.
- Informationen zur Entwicklung von ARM-Vorlagen finden Sie in der [Azure Resource Manager-Dokumentation](../../azure-resource-manager/management/overview.md).
- Informationen zur Bereitstellung auf Abonnementebene finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Wählen Sie links auf der Seite die Option **Konformität** aus. Suchen Sie dann die von Ihnen erstellte Richtlinienzuweisung _Überwachen Sie die virtuellen Computer, die nicht verwaltete Datenträger verwenden_.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Screenshot: Konformitätsdetails auf der Seite zur Richtlinienkonformität" border="false":::

Falls Ressourcen vorhanden sind, die mit dieser neuen Zuweisung nicht konform sind, werden diese unter **Nicht konforme Ressourcen** angezeigt.

Weitere Informationen finden Sie unter [Funktionsweise der Konformität](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die erstellte Zuweisung zu entfernen, gehen Sie folgendermaßen vor:

1. Wählen Sie links auf der Seite „Azure Policy“ **Konformität** (oder **Zuweisungen**), und suchen Sie die von Ihnen erstellte Richtlinienzuweisung _Überwachen Sie die virtuellen Computer, die nicht verwaltete Datenträger verwenden_.

1. Klicken Sie mit der rechten Maustaste auf die Richtlinienzuweisung _Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden_, und wählen Sie **Zuweisung löschen**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Screenshot: Verwendung des Kontextmenüs zum Löschen einer Zuweisung von der Konformitätsseite" border="false":::

1. Löschen Sie die Datei `assignment.bicep`.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einem Bereich eine integrierte Richtliniendefinition zugewiesen und deren Konformitätsbericht ausgewertet. Die Richtliniendefinition überprüft, ob alle Ressourcen im Bereich konform sind, und identifiziert die Ressourcen, die nicht konform sind.

Weitere Informationen zum Zuweisen von Richtlinien, die die Konformität neuer Ressourcen überprüfen, finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Richtlinien](./tutorials/create-and-manage.md)
