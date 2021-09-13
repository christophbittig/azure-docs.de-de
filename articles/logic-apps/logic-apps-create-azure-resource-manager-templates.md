---
title: Erstellen von Logik-App-Vorlagen zur Bereitstellung
description: Erfahren Sie, wie Sie Azure Resource Manager-Vorlagen zur automatischen Bereitstellung in Azure Logic Apps erstellen.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/20/2021
ms.openlocfilehash: 3378c04fd6a43d50b670767d1d3fc4f59043e1e9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456601"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Erstellen von Azure Resource Manager-Vorlagen, um die Bereitstellung für Azure Logic Apps zu automatisieren

Um Sie dabei zu unterstützen, das Erstellen und Bereitstellen einer Logik-App zu automatisieren, sind in diesem Artikel die Möglichkeiten beschrieben, wie Sie eine [Azure Resource Manager-Vorlage](../azure-resource-manager/management/overview.md) für Ihre Logik-App erstellen. Eine Übersicht über die Struktur und die Syntax einer Vorlage, die Ihre Workflowdefinition und weitere Ressourcen enthält, die zur Bereitstellung erforderlich sind, finden Sie unter [Overview: Automatisieren der Bereitstellung für Logik-Apps mit Azure Resource Manager-Vorlagen](logic-apps-azure-resource-manager-templates-overview.md).

Azure Logic Apps bietet eine [vordefinierte Azure Resource Manager-Vorlage für Logik-Apps](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.logic/logic-app-create/azuredeploy.json), die Sie nicht nur zum Erstellen von Logik-Apps, sondern auch zum Definieren der Ressourcen und Parameter für die Bereitstellung wiederverwenden können. Die Vorlage kann für eigene Unternehmensszenarien verwendet oder an Ihre individuellen Anforderungen angepasst werden.

> [!IMPORTANT]
> Für Verbindungen in Ihrer Vorlage müssen dieselbe Azure-Ressourcengruppe und derselbe Standort wie für Ihre Logik-App verwendet werden.

Weitere Informationen zu Azure Resource Manager-Vorlagen finden Sie in den folgenden Themen:

* [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/syntax.md)
* [Erstellen von Azure Resource-Manager-Vorlagen](../azure-resource-manager/templates/syntax.md)
* [Informationen zum Entwickeln von Azure Resource Manager-Vorlagen für cloudübergreifende Konsistenz](../azure-resource-manager/templates/template-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Erstellen von Vorlagen mit Visual Studio

Als einfachste Möglichkeit, gültige parametrisierte Logik-App-Vorlagen zu erstellen, die weitestgehend zur Bereitstellung bereit sind, verwenden Sie Visual Studio (kostenlose Community Edition oder höher) und die Azure Logic Apps-Tools für Visual Studio. Sie können dann entweder [Ihre Logik-App in Visual Studio erstellen](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) oder [im Azure-Portal nach einer vorhandenen Logik-App suchen und diese nach Visual Studio herunterladen](../logic-apps/manage-logic-apps-with-visual-studio.md).

Wenn Sie Ihre Logik-App herunterladen, erhalten Sie eine Vorlage, die die Definitionen für Ihre Logik-App sowie weitere Ressourcen, etwa Verbindungen, enthält. In der Vorlage sind außerdem die Werte *parametrisiert* oder Parameter für die Werte definiert, die zur Bereitstellung Ihrer Logik-App und anderer Ressourcen verwendet werden. Sie können die Werte für diese Parameter in einer separaten Parameterdatei bereitstellen. Auf diese Weise können Sie diese Werte einfacher entsprechend Ihren Bereitstellungsanforderungen ändern. Weitere Informationen finden Sie in den folgenden Themen:

* [Erstellen von Logik-Apps mit Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Verwalten von Logik-Apps mit Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Erstellen von Vorlagen mit Azure PowerShell

Sie können Ressourcen-Manager-Vorlagen erstellen, indem Sie Azure PowerShell mit dem [LogicAppTemplate-Modul](https://github.com/jeffhollan/LogicAppTemplateCreator) verwenden. Dieses Open Source-Modul bewertet zunächst Ihre Logik-App und alle Verbindungen, die die Logik-App verwendet. Das Modul generiert dann Vorlagenressourcen mit den erforderlichen Parametern für die Bereitstellung.

Angenommen, Sie haben eine Logik-App, die eine Nachricht von einer Azure Service Bus-Warteschlange empfängt und Daten in Azure SQL-Datenbank hochlädt. Das Modul speichert die gesamte Orchestrierungslogik und parametrisiert die SQL- und Service Bus-Verbindungszeichenfolgen, sodass Sie diese Werte entsprechend Ihren Bereitstellungsanforderungen bereitstellen und ändern können.

Diese Beispiele veranschaulichen das Erstellen und Bereitstellen von Logik-Apps mithilfe von Azure Resource Manager-Vorlagen, Azure Pipelines in Azure DevOps und Azure PowerShell:

* [Beispiel: Orchestrieren von Azure Pipelines mithilfe von Azure Logic Apps](https://github.com/Azure-Samples/azure-logic-apps-pipeline-orchestration)
* [Beispiel: Herstellen einer Verbindung mit Azure-Speicherkonten aus Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/storage-account-connections)
* [Beispiel: Herstellen einer Verbindung mit Azure Service Bus-Warteschlangen aus Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/service-bus-connections)
* [Beispiel: Einrichten einer Azure Functions-Aktion für Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/function-app-actions)
* [Beispiel: Herstellen einer Verbindung mit einem Integrationskonto aus Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/integration-account-connections)

### <a name="install-powershell-modules"></a>Installieren von PowerShell-Modulen

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps), sofern dies noch nicht geschehen ist.

1. Der einfachste Weg zum Installieren des LogicAppTemplate-Moduls aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/LogicAppTemplate) besteht darin, den folgenden Befehl auszuführen:

   ```powershell
   Install-Module -Name LogicAppTemplate
   ```

   Um auf die neueste Version zu aktualisieren, führen Sie den folgenden Befehl aus:

   ```powershell
   Update-Module -Name LogicAppTemplate
   ```

Wenn Sie manuell installieren möchten, führen Sie die Schritte in GitHub für [LogicAppTemplateCreator](https://github.com/jeffhollan/LogicAppTemplateCreator) aus.

### <a name="install-azure-resource-manager-client"></a>Installieren des Azure Resource Manager-Clients

Damit das LogicAppTemplate-Modul mit jedem Azure-Mandanten und Abonnementzugriffstoken funktioniert, installieren Sie das [Azure Resource Manager-Clienttool](https://github.com/projectkudu/ARMClient). Dieses Tool ist ein einfaches Befehlszeilentool, aus dem die Azure Resource Manager-API aufgerufen wird.

Wenn Sie den `Get-LogicAppTemplate`-Befehl mit diesem Tool ausführen, geht der Befehl wie folgt vor: Er ruft zunächst ein Zugriffstoken über das ARMClient-Tool ab, reicht das Token an das PowerShell-Skript weiter und erstellt die Vorlage als JSON-Datei. Weitere Informationen über das Tool finden Sie in diesem [Artikel zu dem Azure Resource Manager-Clienttool](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Generieren einer Vorlage mit PowerShell

Um Ihre Vorlage nach dem Installieren von LogicAppTemplate-Modul und [Azure CLI](/cli/azure/) zu generieren, führen Sie den folgenden PowerShell-Befehl aus:

```powershell
$parameters = @{
    Token = (az account get-access-token | ConvertFrom-Json).accessToken
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

Um der Empfehlung für das Weiterreichen eines Tokens aus dem [Azure Resource Manager-Clienttool](https://github.com/projectkudu/ARMClient) zu folgen, führen Sie stattdessen diesen Befehl aus, wobei `$SubscriptionId` Ihre Azure-Abonnement-ID ist:

```powershell
$parameters = @{
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

armclient token $SubscriptionId | Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

Nach dem Extrahieren können Sie eine Parameterdatei aus ihrer Vorlage erstellen, indem Sie den folgenden Befehl ausführen:

```powershell
Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Zum Extrahieren mit Azure Key Vault-Verweisen (nur statisch) führen Sie den folgenden Befehl aus:

```powershell
Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parameter | Erforderlich | BESCHREIBUNG |
|------------|----------|-------------|
| TemplateFile | Ja | Der Pfad zu Ihrer Vorlagendatei |
| KeyVault | Nein | Eine Enumeration, in der beschrieben ist, wie mögliche Schlüsseltresorwerte (Key Vault-Werte) verarbeitet werden Der Standardwert lautet `None`. |
||||

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen von Logik-App-Vorlagen](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)