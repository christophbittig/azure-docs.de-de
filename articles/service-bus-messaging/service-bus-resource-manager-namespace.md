---
title: Erstellen eines Azure Service Bus-Namespace anhand einer Vorlage
description: Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Service Bus Messaging-Namespace
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 09/27/2021
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0a60829ead884c9b76025580714f03d993101b79
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155285"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Erstellen eines Service Bus-Namespace mithilfe einer Azure Resource Manager-Vorlage

Erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage zum Erstellen eines Service Bus-Namespace bereitstellen. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen. Weitere Informationen zum Erstellen von Vorlagen finden Sie in der [Dokumentation zu Azure Resource Manager](../azure-resource-manager/index.yml).

Die folgenden Vorlagen stehen ebenfalls zum Erstellen von Service Bus-Namespaces zur Verfügung:

* [Create a Service Bus namespace and a queue using an Azure Resource Manager template](./service-bus-resource-manager-namespace-queue.md)
* [Create a Service Bus namespace with topic and subscription using an Azure Resource Manager template (Erstellen eines Service Bus-Namespace mit Thema und Abonnement mit einer Azure Resource Manager-Vorlage)](./service-bus-resource-manager-namespace-topic.md)
* [Create a Service Bus authorization rule for namespace and queue using an Azure Resource Manager template](./service-bus-resource-manager-namespace-auth-rule.md)
* [Create a Service Bus namespace with topic, subscription, and rule (Erstellen eines Service Bus-Namespace mit Thema, Abonnement und Regel)](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-a-service-bus-namespace"></a>Erstellen eines Service Bus-Namespaces

In dieser Schnellstartanleitung verwenden Sie eine [vorhandene Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.servicebus/servicebus-create-namespace/azuredeploy.json) aus den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/quickstarts/microsoft.servicebus/servicebus-create-namespace/azuredeploy.json)]

Weitere Beispiele für Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

So erstellen Sie einen Service Bus-Namespace durch die Bereitstellung einer Vorlage:

1. Wählen Sie im folgenden Codeblock **Ausprobieren** aus, und folgen Sie dann den Anweisungen, um sich bei Azure Cloud Shell anzumelden.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.servicebus/servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Der Ressourcengruppenname entspricht dem Service Bus-Namespace mit dem Zusatz **rg**.

2. Wählen Sie **Kopieren**, um das PowerShell-Skript zu kopieren.
3. Klicken Sie mit der rechten Maustaste auf die Shellkonsole, und wählen Sie **Einfügen** aus.

Es dauert einen Augenblick, einen Event Hub zu erstellen.

## <a name="verify-the-deployment"></a>Überprüfen der Bereitstellung

Um den bereitgestellten Service Bus-Namespace anzuzeigen, öffnen Sie entweder die Ressourcengruppe im Azure-Portal oder verwenden das folgende Azure PowerShell-Skript. Wenn die Cloud Shell noch geöffnet ist, müssen die erste und zweite Zeile des folgenden Skripts nicht kopiert/ausgeführt werden.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

In diesem Tutorial wird Azure PowerShell verwendet, um die Vorlage bereitzustellen. Weitere Methoden zum Bereitstellen von Vorlagen finden Sie unter:

* [Über das Azure-Portal](../azure-resource-manager/templates/deploy-portal.md).
* [Mit der Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Mit der REST-API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen. Wenn die Cloud Shell noch geöffnet ist, müssen die erste und zweite Zeile des folgenden Skripts nicht kopiert/ausgeführt werden.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen Service Bus-Namespace erstellt. Informationen zum Erstellen von Warteschlangen und Themen/Abonnements sowie zu deren Verwendung finden Sie in den anderen Schnellstarts:

* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Erste Schritte mit Service Bus-Themen](service-bus-dotnet-how-to-use-topics-subscriptions.md)
