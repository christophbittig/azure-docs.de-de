---
title: Erstellen eines Namespace und einer Warteschlange in Azure Service Bus anhand einer Azure-Vorlage
description: 'Schnellstart: Erstellen eines Service Bus-Namespace und einer Warteschlange mithilfe einer Azure Resource Manager-Vorlage'
documentationcenter: .net
author: spelluru
ms.author: spelluru
ms.date: 09/27/2021
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.custom: subject-armqs, mode-arm
ms.openlocfilehash: 1ed866c1b6dfb74b755a738a67996273980ad5f3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131014009"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>Schnellstart: Erstellen eines Service Bus-Namespace und einer Warteschlange anhand einer ARM-Vorlage

In diesem Artikel wird beschrieben, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) verwenden, die einen Service Bus-Namespace und eine Warteschlange innerhalb dieses Namespace erstellt. Dieser Artikel zeigt Ihnen, wie Sie angeben, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben werden, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.servicebus%2Fservicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/servicebus-create-queue/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.servicebus/servicebus-create-queue/azuredeploy.json":::

In der Vorlage sind die folgenden Ressourcen definiert:

- [**Microsoft.ServiceBus/namespaces**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/queues**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Die folgenden ARM-Vorlagen stehen zum Herunterladen und Bereitstellen zur Verfügung.
>
> * [Create a Service Bus authorization rule for namespace and queue using an Azure Resource Manager template](service-bus-resource-manager-namespace-auth-rule.md)
> * [Create a Service Bus namespace with topic and subscription using an Azure Resource Manager template (Erstellen eines Service Bus-Namespace mit Thema und Abonnement mit einer Azure Resource Manager-Vorlage)](service-bus-resource-manager-namespace-topic.md)
> * [Erstellen eines Service Bus-Namespaces](service-bus-resource-manager-namespace.md)
> * [Create a Service Bus namespace with topic, subscription, and rule (Erstellen eines Service Bus-Namespace mit Thema, Abonnement und Regel)](service-bus-resource-manager-namespace-topic-with-rule.md)

Informationen zu weiteren Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Mit dieser Vorlage stellen Sie einen Service Bus-Namespace mit einer Warteschlange bereit.

[Service Bus-Warteschlangen](service-bus-queues-topics-subscriptions.md#queues) bieten eine FIFO-Nachrichtenzustellung (First In, First Out) an einen Consumer oder an mehrere konkurrierende Consumer.

Klicken Sie auf folgende Schaltfläche, um die Bereitstellung automatisch auszuführen: Erstellen Sie eine neue Ressourcengruppe für die Bereitstellung, um sie später problemlos bereinigen zu können.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.servicebus%2Fservicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

1. Wählen Sie oben **Benachrichtigungen** aus, um den Status der Bereitstellung anzuzeigen. Warten Sie, bis die Bereitstellung erfolgreich abgeschlossen wurde. Wählen Sie dann in der Benachrichtigungsmeldung **Zu Ressourcengruppe wechseln** aus, um zur Seite für die Ressourcengruppe zu navigieren, wo der Service Bus-Namespace angegeben ist. 

    ![Benachrichtigung der Bereitstellung](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Vergewissern Sie sich, dass Ihr Service Bus-Namespace in der Ressourcenliste enthalten ist. 

    ![Ressourcengruppe: Namespace](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Wählen Sie den Namespace in der Liste aus, um zur Seite **Service Bus-Namespace** zu gelangen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

1. Navigieren Sie im Azure-Portal zur Seite **Ressourcengruppe** für Ihre Ressourcengruppe.
2. Klicken Sie auf der Symbolleiste auf **Ressourcengruppe löschen**. 
3. Geben Sie den Namen der Ressourcengruppe ein, und wählen Sie **Löschen** aus. 

    ![Ressourcengruppe: Löschen](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie das folgende Thema zum Erstellen einer Autorisierungsregel für den Namespace bzw. die Warteschlange:

[Erstellen einer Service Bus-Autorisierungsregel für Namespace und Warteschlange mithilfe einer ARM-Vorlage](service-bus-resource-manager-namespace-auth-rule.md)

In den folgenden Artikeln erfahren Sie, wie Sie diese Ressourcen verwalten:

* [Verwalten von Service Bus mit PowerShell](service-bus-manage-with-ps.md)
* [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.servicebus/servicebus-create-queue/azuredeploy.json/
[Azure Quickstart Templates]: https://azure.microsoft.com/resources/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
