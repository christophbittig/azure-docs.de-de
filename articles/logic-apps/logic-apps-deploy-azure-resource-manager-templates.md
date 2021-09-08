---
title: Bereitstellen von Logik-App-Vorlagen
description: Erfahren Sie, wie Sie Azure Resource Manager-Vorlagen bereitstellen, die für Azure Logic Apps erstellt wurden.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8be8361b58e4b1b1fad3f41b29958a360e206890
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339413"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Bereitstellen von Azure Resource Manager-Vorlagen für Azure Logic Apps

Nachdem Sie eine Azure Resource Manager-Vorlage für Ihre Logik-App erstellt haben, können Sie Ihre Vorlage folgendermaßen bereitstellen:

* [Azure portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure-Befehlszeilenschnittstelle](#cli)
* [Azure-Ressourcen-Manager-REST-API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Bereitstellen über das Azure-Portal

Um eine Logik-App-Vorlage automatisch in Azure bereitzustellen, können Sie die folgende Schaltfläche **In Azure bereitstellen** auswählen. Darüber werden Sie im Azure-Portal angemeldet und zur Eingabe von Informationen zu Ihrer Logik-App aufgefordert. Sie können dann alle notwendigen Änderungen an der Logik-App-Vorlage oder den Parametern vornehmen.

[![In Azure bereitstellen](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.logic%2Flogic-app-create%2Fazuredeploy.json)

Sie werden beispielsweise nach der Anmeldung beim Azure-Portal zur Eingabe der folgenden Informationen aufgefordert:

* Name des Azure-Abonnements
* Gewünschte Ressourcengruppe
* Speicherort der Logik-App
* Der Name Ihrer Logik-App
* Test-URI
* Annahme der angegebenen Bedingungen

Weitere Informationen finden Sie in den folgenden Themen:

* [Übersicht: Automatisieren der Bereitstellung für Azure Logik-Apps mit Azure Resource Manager-Vorlagen](logic-apps-azure-resource-manager-templates-overview.md)
* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und dem Azure-Portal](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Bereitstellen mit Visual Studio 2013

Um eine Logik-App-Vorlage aus einem Azure-Ressourcengruppenprojekt bereitzustellen, das Sie mithilfe von Visual Studio erstellt haben, befolgen Sie diese [Schritte zum manuellen Bereitstellen Ihrer Logik-App](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) in Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Bereitstellen mit Azure PowerShell

Für die Bereitstellung in einer bestimmten *Azure-Ressourcengruppe* verwenden Sie den folgenden Befehl:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.logic/logic-app-create/azuredeploy.json
```

Weitere Informationen finden Sie in den folgenden Themen:

* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

Für die Bereitstellung in einer bestimmten *Azure-Ressourcengruppe* verwenden Sie den folgenden Befehl:

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.logic/logic-app-create/azuredeploy.json
```

Weitere Informationen finden Sie in den folgenden Themen:

* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure-CLI](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Bereitstellen mit Azure DevOps

Um Logik-App-Vorlagen bereitzustellen und Umgebungen zu verwalten, verwenden Teams häufig ein Tool wie [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) in [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines enthält eine [Aufgabe für die Bereitstellung von Azure-Ressourcengruppen](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2), die jeder Build- oder Releasepipeline hinzugefügt werden kann. Für die Autorisierung zur Bereitstellung und Generierung der Releasepipeline benötigen Sie außerdem ein Azure Active Directory (AD)-[Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md). Weitere Informationen zum [Verwenden von Dienstprinzipalen mit Azure Pipelines](/azure/devops/pipelines/library/connect-to-azure).

Weitere Informationen zu Continuous Integration und Continuous Deployment (CI/CD) für Azure Resource Manager-Vorlagen mit Azure Pipelines finden Sie in den folgenden Themen und Beispielen:

* [Integrieren von Resource Manager-Vorlagen in Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Tutorial: Continuous Integration von Azure Resource Manager-Vorlagen mit Azure Pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md).
* [Beispiel: Orchestrieren von Azure Pipelines mithilfe von Azure Logic Apps](https://github.com/Azure-Samples/azure-logic-apps-pipeline-orchestration)
* [Beispiel: Herstellen einer Verbindung mit Azure-Speicherkonten aus Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/storage-account-connections)
* [Beispiel: Herstellen einer Verbindung mit Azure Service Bus-Warteschlangen aus Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/service-bus-connections)
* [Beispiel: Einrichten einer Azure Functions-Aktion für Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/function-app-actions)
* [Beispiel: Herstellen einer Verbindung mit einem Integrationskonto aus Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/integration-account-connections)

Hier finden Sie die allgemeinen Schritte für die Verwendung von Azure Pipelines:

1. Erstellen Sie in Azure Pipelines eine leere Pipeline.

1. Wählen Sie die Ressourcen, die Sie für die Pipeline benötigen, wie z.B. Ihre Logik-App-Vorlage und Ihre Parameterdateien für die Vorlagen, die Sie manuell oder im Rahmen des Build-Prozesses generieren.

1. Suchen Sie die Aufgabe **Bereitstellung einer Azure-Ressourcengruppe** und fügen Sie sie zum Agent-Auftrag hinzu.

   ![Hinzufügen der Aufgabe „Bereitstellung einer Azure-Ressourcengruppe“](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Führen Sie die Konfiguration mit einem [Dienstprinzipal](/azure/devops/pipelines/library/connect-to-azure) durch.

1. Fügen Sie Verweise auf Ihre Logik-App-Vorlage und Parameterdateien für die Vorlage hinzu.

1. Erstellen Sie nach Bedarf weitere Schritte im Freigabeprozess für andere Umgebungen, automatisierte Tests oder genehmigende Personen.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorisieren von OAuth-Verbindungen

Nach der Bereitstellung funktioniert Ihre Logik-App End-to-End mit gültigen Parametern. Um jedoch gültige Zugriffstoken für die [Authentifizierung Ihrer Anmeldeinformationen](../active-directory/develop/authentication-vs-authorization.md) zu generieren, müssen Sie weiterhin vorab autorisierte OAuth-Verbindungen autorisieren oder verwenden. Sie müssen jedoch nur einmal API-Verbindungsressourcen bereitstellen und authentifizieren. Dies bedeutet, dass Sie diese Verbindungsressourcen nicht in nachfolgende Bereitstellungen einschließen müssen, es sei denn, Sie müssen die Verbindungsinformationen aktualisieren. Wenn Sie eine Continuous Integration- und Continuous Deployment-Pipeline verwenden, stellen Sie nur aktualisierte Logic Apps-Ressourcen bereit und müssen die Verbindungen nicht jedes Mal erneut autorisieren.

Im folgenden finden Sie einige Vorschläge für den Umgang mit Autorisierungsverbindungen:

* Um OAuth-Verbindungen manuell zu autorisieren, öffnen Sie Ihre Logik-App über das Azure-Portal oder in Visual Studio im Logik-App-Designer. Wenn Sie Ihre Verbindung autorisieren, wird möglicherweise eine Bestätigungsseite angezeigt, auf der Sie den Zugriff zulassen können.

* Autorisieren Sie API-Verbindungsressourcen in Logik-Apps, die sich in der gleichen Region befinden, vorab und geben Sie sie frei. API-Verbindungen sind unabhängig von Logik-Apps als Azure-Ressourcen vorhanden. Während Logik-Apps Abhängigkeiten von API-Verbindungsressourcen aufweisen, weisen API-Verbindungsressourcen keine Abhängigkeiten von Logik-Apps auf und bleiben erhalten, nachdem Sie die abhängigen Logik-Apps gelöscht haben. Logik-Apps können auch API-Verbindungen verwenden, die in anderen Ressourcengruppen vorhanden sind. Der Logik-App-Designer unterstützt das Erstellen von API-Verbindungen jedoch nur in derselben Ressourcengruppe, in der sich Ihre Logik-Apps befinden.

  > [!NOTE]
  > Wenn Sie die Freigabe von API-Verbindungen in Erwägung ziehen, stellen Sie sicher, dass Ihre Lösung [potenzielle Drosselungsprobleme behandeln](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling) kann. Die Drosselung erfolgt auf Verbindungsebene, sodass die Wiederverwendung derselben Verbindung für mehrere Logik-Apps möglicherweise das Drosselungsproblempotenzial heraufsetzt.

* Wenn Ihr Szenario keine Dienste und Systeme enthält, für die Multi-Factor Authentication erforderlich ist, können Sie ein PowerShell-Skript verwenden, um die Zustimmung für jede OAuth-Verbindung bereitzustellen, indem Sie einen Continuous Integration-Worker als normales Benutzerkonto auf einem virtuellen Computer ausführen, der über aktive Browsersitzungen verfügt, in denen Autorisierungen und Zustimmung bereits bereitgestellt sind. Beispielsweise können Sie das vom [LogicAppConnectionAuth-Projekt im Logic Apps-Repository in GitHub](https://github.com/logicappsio/LogicAppConnectionAuth) bereitgestellte Beispielskript wiederverwenden.

* Wenn Sie zum Autorisieren von Verbindungen stattdessen einen Azure AD-[Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md) verwenden, informieren Sie sich darüber, wie Sie [Dienstprinzipalparameter in Ihrer Logik-App-Vorlage angeben](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Überwachen von Logik-Apps](../logic-apps/monitor-logic-apps.md)
