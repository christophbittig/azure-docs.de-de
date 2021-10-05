---
title: 'Tutorial: Hinzufügen einer Ressource zu einer Vorlage'
description: In diesem Tutorial werden die Schritte zum Erstellen Ihrer ersten Azure Resource Manager-Vorlage (ARM-Vorlage) beschrieben. Sie lernen die Syntax der Vorlagendatei kennen und erfahren, wie Sie ein Speicherkonto bereitstellen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 82c6b6cd0dde6b321e4de8a87f99e1adad69040d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662113"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Tutorial: Hinzufügen einer Ressource zu Ihrer ARM-Vorlage

Im [vorherigen Tutorial](template-tutorial-create-first-template.md) haben Sie erfahren, wie Sie eine leere Azure Resource Manager-Vorlage (ARM-Vorlage) erstellen und bereitstellen. Nun können Sie eine tatsächliche Ressource bereitstellen. In diesem Tutorial fügen Sie ein Speicherkonto hinzu. Das Tutorial dauert ungefähr **9 Minuten**.

## <a name="prerequisites"></a>Voraussetzungen

Wir empfehlen, das [Einführungstutorial zu Vorlagen](template-tutorial-create-first-template.md) durchzuführen. Dies ist jedoch keine Voraussetzung.

Sie benötigen Visual Studio Code mit der Resource Manager-Tools-Erweiterung und entweder Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI). Weitere Informationen finden Sie unter [Vorlagentools](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Ressource hinzufügen

Der hervorgehobene JSON-Code im folgenden Beispiel zeigt, wie Sie der vorhandenen Vorlage eine Speicherkontodefinition hinzufügen. Anstatt Abschnitte der Vorlage zu kopieren, kopieren Sie die gesamte Datei und ersetzen Ihre Vorlage durch den Inhalt der Datei.

Ersetzen Sie `{provide-unique-name}` und die geschweiften Klammern `{}` durch einen eindeutigen Speicherkontonamen.

> [!IMPORTANT]
> Der Name des Speicherkontos muss innerhalb von Azure eindeutig sein. Der Name darf nur Kleinbuchstaben und Ziffern enthalten, und er darf maximal 24 Zeichen lang sein. Es sind verschiedene Benennungsmuster möglich. Sie können beispielsweise **store1** als Präfix verwenden und danach Ihre Initialen und das aktuelle Datum hinzufügen. Der Name könnte z. B. **store1abc09092019** lauten.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Das Erraten des eindeutigen Namens für ein Speicherkonto ist nicht einfach und keine gute Lösung, wenn Sie umfangreiche Bereitstellungen automatisieren möchten. In einem späteren Teil dieser Tutorialreihe lernen Sie Vorlagenfunktionen kennen, die das Erstellen eines eindeutigen Namens vereinfachen.

## <a name="resource-properties"></a>Ressourceneigenschaften

Vielleicht fragen Sie sich, wie Sie die zu verwendenden Eigenschaften für die einzelnen Ressourcentypen herausfinden können. Die [Referenz zur ARM-Vorlage](/azure/templates/) kann Ihnen als Hilfe beim Suchen nach den gewünschten Ressourcentypen für Ihre Bereitstellung dienen.

Jede Ressource, die Sie bereitstellen, verfügt mindestens über die folgenden drei Eigenschaften:

- `type`: Der Typ der Ressource. Dieser Wert ist eine Kombination aus dem Namespace des Ressourcenanbieters und dem Ressourcentyp (z. B. `Microsoft.Storage/storageAccounts`).
- `apiVersion`: Version der REST-API zum Erstellen der Ressource. Jeder Ressourcenanbieter veröffentlicht seine eigenen API-Versionen. Dieser Wert unterscheidet sich daher je nach Typ.
- `name` Der Name der Ressource.

Die meisten Ressourcen verfügen auch über eine `location`-Eigenschaft, mit der die Region festgelegt wird, in der die Ressource bereitgestellt wird.

Die anderen Eigenschaften variieren je nach Ressourcentyp und API-Version. Das Verständnis der Beziehung zwischen der API-Version und den verfügbaren Eigenschaften ist wichtig. Wir werden daher ausführlicher darauf eingehen.

In diesem Tutorial haben Sie der Vorlage ein Speicherkonto hinzugefügt. Diese API-Version finden Sie unter [storageAccounts 2021-04-01](/azure/templates/microsoft.storage/2021-04-01/storageaccounts). Bedenken Sie, dass Sie Ihrer Vorlage nicht alle Eigenschaften hinzugefügt haben. Viele der Eigenschaften sind optional. Der `Microsoft.Storage`-Ressourcenanbieter könnte eine neue API-Version veröffentlichen, ohne dass sich die von Ihnen bereitgestellte Version ändert. Sie können diese Version weiterhin verwenden und dabei sicher sein, dass die Ergebnisse Ihrer Bereitstellung immer konsistent sind.

Wenn Sie eine ältere API-Version anzeigen (z. B. [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)), werden Sie feststellen, dass dort weniger Eigenschaften verfügbar sind.

Falls Sie sich entscheiden, die API-Version für eine Ressource zu ändern, müssen Sie die Eigenschaften für diese Version prüfen und die Vorlage entsprechend anpassen.

## <a name="deploy-template"></a>Bereitstellen der Vorlage

Sie können die Vorlage bereitstellen, um das Speicherkonto zu erstellen. Verwenden Sie einen anderen Namen für Ihre Bereitstellung, damit Sie sie problemlos im Verlauf finden können.

Falls Sie die Ressourcengruppe noch nicht erstellt haben, folgen Sie den Anweisungen unter [Erstellen einer Ressourcengruppe](template-tutorial-create-first-template.md#create-resource-group). Dieses Beispiel setzt voraus, dass Sie die Variable `templateFile` wie im [ersten Tutorial](template-tutorial-create-first-template.md#deploy-template) beschrieben auf den Pfad zur Vorlagendatei festgelegt haben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Für die Ausführung dieses Bereitstellungsbefehls müssen Sie über die [aktuelle Version](/cli/azure/install-azure-cli) der Azure CLI verfügen.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Wenn bei der Bereitstellung ein Fehler aufgetreten ist, verwenden Sie den Schalter `verbose`, um Informationen zu den erstellten Ressourcen abzurufen. Verwenden Sie den Schalter `debug`, um weitere Informationen zum Debuggen zu erhalten.

Bei der Bereitstellung können die folgenden zwei Fehler auftreten:

- `Error: Code=AccountNameInvalid; Message={provide-unique-name}` ist kein gültiger Speicherkontoname. Ein Speicherkontoname muss zwischen 3 und 24 Zeichen lang sein und darf ausschließlich Ziffern und Kleinbuchstaben enthalten.

    Ersetzen Sie in der Vorlage `{provide-unique-name}` durch einen eindeutigen Namen für das Speicherkonto. Weitere Informationen finden Sie unter [Hinzufügen einer Ressource](#add-resource).

- `Error: Code=StorageAccountAlreadyTaken; Message=The storage account named store1abc09092019` wird bereits verwendet.

    Verwenden Sie in der Vorlage einen anderen Speicherkontonamen.

Diese Bereitstellung dauert länger als die der leeren Vorlage, weil das Speicherkonto erstellt wird. Sie kann ungefähr eine Minute dauern, ist aber in der Regel schneller.

## <a name="verify-deployment"></a>Überprüfen der Bereitstellung

Sie können die Bereitstellung überprüfen, indem Sie sich die Ressourcengruppe im Azure-Portal ansehen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Menü **Ressourcengruppen** aus.
1. Wählen Sie die Ressourcengruppe aus, in der Sie die Vorlage bereitgestellt haben.
1. Sie sehen, dass ein Speicherkonto bereitgestellt wurde.
1. Beachten Sie, dass die Bereitstellungsbezeichnung nun **Bereitstellungen: 2 erfolgreich** lautet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit dem nächsten Tutorial fortfahren, müssen Sie die Ressourcengruppe nicht löschen.

Falls Sie keine weiteren Tutorials durchführen möchten, können Sie die bereitgestellten Ressourcen bereinigen, indem Sie die Ressourcengruppe löschen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine einfache Vorlage zum Bereitstellen eines Azure-Speicherkontos erstellt. In den nächsten Tutorials erfahren Sie, wie Sie einer Vorlage Parameter, Variablen, Ressourcen und Ausgaben hinzufügen. Diese Features sind die Bausteine für weitaus komplexere Vorlagen.

> [!div class="nextstepaction"]
> [Hinzufügen von Parametern](template-tutorial-add-parameters.md)
