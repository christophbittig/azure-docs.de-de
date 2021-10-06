---
title: Verwenden der Azure CLI zum Erstellen von Service Bus-Themen und Abonnements
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie über die Azure CLI ein Service Bus-Thema und Abonnements dafür erstellen.
ms.date: 09/28/2021
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 012fa6c3b6190959b7eb38c140b57a551f317498
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236859"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Erstellen eines Service Bus-Themas und Abonnements dieses Themas über die Azure CLI
In dieser Schnellstartanleitung erstellen Sie über die Azure CLI ein Service Bus-Thema und anschließend Abonnements dieses Themas. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Was sind Service Bus-Themen und -Abonnements?
Service Bus-Themen und -Abonnements unterstützen ein Modell der Messagingkommunikation über das *Veröffentlichen/Abonnieren* . Bei der Verwendung von Themen und Abonnements kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über ein Thema aus, das als Zwischenstufe fungiert.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Anders als bei Service Bus-Warteschlangen, bei denen jede Nachricht von einem einzelnen Consumer verarbeitet wird, bieten Themen und Abonnements eine 1:n-Kommunikationsform mit einem Veröffentlichungs- und Abonnementmuster. Es ist möglich, mehrere Abonnements zu einem Thema anzumelden. Wenn eine Nachricht an ein Thema gesendet wird, steht sie in jedem Abonnement zur Verfügung, wo sie unabhängig von den anderen Abonnements verarbeitet wird. Ein Themenabonnement ähnelt einer virtuellen Warteschlange, die Kopien der Nachrichten enthält, die an das Thema gesendet wurden. Sie können optional auch Filterregeln für einzelne Abonnements eines Themas anmelden. Auf diese Weise können Sie filtern oder einschränken, welche Nachrichten an ein Thema von welchen Themenabonnements empfangen werden.

Mit Service Bus-Themen und -Abonnements können Sie viele Nachrichten an eine große Anzahl von Benutzern und Anwendungen verarbeiten.

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][free account] erstellen, bevor Sie beginnen.

In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell. Diesen Dienst können Sie nach der Anmeldung beim Azure-Portal starten. Ausführliche Informationen zu Azure Cloud Shell finden Sie unter [Übersicht über Azure Cloud Shell](../cloud-shell/overview.md). Sie können auch Azure PowerShell auf Ihrem Computer [installieren](/cli/azure/install-azure-cli) und verwenden. 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Erstellen eines Service Bus-Themas und Erstellen von Abonnements
Jedes [Abonnement eines Themas](service-bus-messaging-overview.md#topics) kann eine Kopie jeder Nachricht empfangen. Die Themen sind in Bezug auf das Protokoll und die Semantik vollständig mit Service Bus-Warteschlangen kompatibel. Service Bus-Themen unterstützen ein umfassendes Auswahlregel-Array mit Filterbedingungen. Es sind optionale Aktionen vorhanden, mit denen Nachrichteneigenschaften festgelegt oder geändert werden. Jedes Mal, wenn sich für eine Regel eine Übereinstimmung ergibt, wird eine Nachricht erzeugt. Weitere Informationen zu Regeln, Filtern und Aktionen finden Sie unter diesem [Link](topic-filters.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Starten Sie Azure Cloud Shell über das in der folgenden Abbildung gezeigte Symbol. Wechseln Sie in den **Bash**-Modus, wenn Cloud Shell im **PowerShell**-Modus ausgeführt wird. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell starten":::
3. Führen Sie den folgenden Befehl aus, um eine Azure-Ressourcengruppe zu erstellen. Aktualisieren Sie ggf. den Namen der Ressourcengruppe und des Standorts. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. Führen Sie den folgenden Befehl aus, um einen Service Bus-Messagingnamespace zu erstellen. Aktualisieren Sie den Namespace so, dass er eindeutig ist. 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. Führen Sie den folgenden Befehl aus, um ein Thema im Namespace zu erstellen. 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. Erstellen des ersten Abonnements für das Thema
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. Erstellen des zweiten Abonnements für das Thema
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. Erstellen des dritten Abonnements für das Thema
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. Erstellen Sie einen Filter für das erste Abonnement mit benutzerdefinierten Eigenschaften (`StoreId` ist `Store1`, `Store2` oder `Store3`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. Erstellen Sie einen Filter für das zweite Abonnement mit benutzerdefinierten Eigenschaften (`StoreId = Store4`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. Erstellen Sie einen Filter für das dritte Abonnement mit benutzerdefinierten Eigenschaften (`StoreId` nicht `Store1`, `Store2`, `Store3` oder `Store4`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. Führen Sie den folgenden Befehl aus, um die primäre Verbindungszeichenfolge für den Namespace abzurufen. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit der Warteschlange herzustellen und Nachrichten zu senden und zu empfangen. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Notieren Sie sich die Verbindungszeichenfolge und den Namen des Themas. Sie verwenden diese zum Senden und Empfangen von Nachrichten. 
    

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Senden von Nachrichten an ein Thema und zum Empfangen dieser Nachrichten über ein Abonnement finden Sie im folgenden Artikel: Auswählen der Programmiersprache im Inhaltsverzeichnis. 

> [!div class="nextstepaction"]
> [Veröffentlichen und Abonnieren von Nachrichten](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
