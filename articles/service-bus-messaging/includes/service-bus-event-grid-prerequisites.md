---
title: Datei einfügen
description: include file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 08/13/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7bcfa8993cdfa0ab8efcbe35881921c5cf5a92cd
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182578"
---
## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie kein [Azure-Abonnement](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), bevor Sie beginnen.

## <a name="create-a-service-bus-namespace"></a>Erstellen eines Service Bus-Namespace
Befolgen Sie die Anleitungen in diesem Tutorial: [Schnellstart: Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements dieses Themas im Azure-Portal](../service-bus-quickstart-topics-subscriptions-portal.md), um folgende Aufgaben durchzuführen:

- Erstellen eines Service Bus **Premium**-Namespace. 
- Abrufen der Verbindungszeichenfolge. 
- Erstellen eines Service Bus-Themas.
- Erstellen eines Abonnements für das Thema. In diesem Tutorial wird nur ein einzelnes Abonnement benötigt. Die Abonnements S2 und S3 müssen also nicht erstellt werden. 

## <a name="send-messages-to-the-service-bus-topic"></a>Senden von Nachricht an das Service Bus-Thema
In diesem Schritt wird eine Beispielanwendung verwendet, um Nachrichten an das Service Bus-Thema zu senden, das Sie im vorherigen Schritt erstellt haben. 

1. Klonen Sie das [GitHub-Repository „azure-service-bus“](https://github.com/Azure/azure-service-bus/).
2. Navigieren Sie in Visual Studio zum Ordner *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegrationV2*, und öffnen Sie die Datei *SBEventGridIntegration.sln*.
3. Erweitern Sie im Fenster des Projektmappen-Explorers das Projekt **MessageSender**, und wählen Sie **Program.cs** aus.
4. Ersetzen Sie `<SERVICE BUS NAMESPACE - CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace und `<TOPIC NAME>` durch den Namen des Themas. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Erstellen Sie das Programm, und führen Sie es aus, um fünf Testnachrichten (`const int numberOfMessages = 5;`) an das Service Bus-Thema zu senden. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Ausgabe der Konsolen-App":::
