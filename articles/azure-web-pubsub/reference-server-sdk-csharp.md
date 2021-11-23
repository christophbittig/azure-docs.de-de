---
title: 'Referenz: .NET SDK für Azure Web PubSub'
description: In dieser Referenz wird das .NET-SDK für den Azure Web PubSub-Dienst beschrieben.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: a3ec19ab07ebe74c618ce3c6af8981d442f6fc21
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486653"
---
# <a name="azure-web-pubsub-service-client-library-for-net"></a>Clientbibliothek des Azure Web PubSub-Diensts für .NET

Der [Azure Web PubSub-Dienst](https://aka.ms/awps/doc) ist ein verwalteter Azure-Dienst, der Entwicklern die einfache Erstellung von Webanwendungen mit Echtzeitfunktionen und einem Veröffentlichen-Abonnieren-Muster ermöglicht. Der Azure Web PubSub-Dienst kann in allen Szenarien verwendet werden, für die Veröffentlichen-Abonnieren-Messaging in Echtzeit zwischen Server und Clients oder zwischen Clients erforderlich ist. Herkömmliche Echtzeitfeatures, die häufig das Abrufen vom Server oder das Übermitteln von HTTP-Anforderungen erfordern, können ebenfalls den Azure Web PubSub-Dienst verwenden.

Sie können diese Bibliothek auf der App-Serverseite verwenden, um die WebSocket-Clientverbindungen zu verwalten, wie in der folgenden Abbildung dargestellt:

![Das Überlaufdiagramm zeigt den Überlauf der Verwendung der Dienstclientbibliothek.](media/sdk-reference/service-client-overflow.png)

Sie können diese Bibliothek für Folgendes verwenden:
- Das Versenden von Nachrichten an Hubs und Gruppen. 
- Das Versenden von Nachrichten an bestimmte Benutzer und Verbindungen.
- Das Organisieren von Benutzern und Verbindungen in Gruppen.
- Das Schließen von Verbindungen
- Das Erteilen, Widerrufen und Prüfen von Berechtigungen für eine vorhandene Verbindung

Details zu den hier verwendeten Begriffen werden im Abschnitt [Wichtige Begriffe](#key-concepts) beschrieben.
 
[Quellcode](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/src) |
[Paket](https://www.nuget.org/packages/Azure.Messaging.WebPubSub) |
[API-Referenzdokumentation](https://aka.ms/awps/sdk/csharp) |
[Produktdokumentation](https://aka.ms/awps/doc) |
[Beispiele][samples_ref]

## <a name="getting-started"></a>Erste Schritte

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie die Clientbibliothek über [NuGet](https://www.nuget.org/):

```dotnetcli
dotnet add package Azure.Messaging.WebPubSub
```

### <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement][azure_sub].
- Eine vorhandene Azure Web PubSub-Dienstinstanz.

### <a name="create-and-authenticate-a-webpubsubserviceclient"></a>Erstellen und Authentifizieren eines `WebPubSubServiceClient`

Für die Interaktion mit dem Dienst müssen Sie eine Instanz der `WebPubSubServiceClient`-Klasse erstellen. Hierzu benötigen Sie die Verbindungszeichenfolge oder einen Schlüssel. Beides steht im Azure-Portal zur Verfügung.

```C# Snippet:WebPubSubAuthenticate
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));
```

## <a name="key-concepts"></a>Wichtige Begriffe

### <a name="connection"></a>Verbindung

Eine Verbindung (auch Client oder Clientverbindung genannt) stellt eine einzelne WebSocket-Verbindung mit dem Web PubSub-Dienst dar. Wenn die Verbindung erfolgreich hergestellt wurde, wird ihr vom Web PubSub-Dienst eine eindeutige Verbindungs-ID zugewiesen.

### <a name="hub"></a>Hub

Ein Hub ist ein logisches Konzept für eine Gruppe von Clientverbindungen. In der Regel wird jeweils ein einzelner Hub für einen einzelnen Zweck verwendet – beispielsweise ein Chathub oder ein Benachrichtigungshub. Eine Clientverbindung wird mit einem Hub hergestellt und gehört während ihrer Lebensdauer zu diesem Hub. Von verschiedenen Anwendungen können unterschiedliche Hubnamen verwendet werden, um gemeinsam einen einzelnen Azure Web PubSub-Dienst zu nutzen.

### <a name="group"></a>Group

Eine Gruppe ist eine Teilmenge der Verbindungen mit dem Hub. Sie können einer Gruppe eine Clientverbindung hinzufügen und sie jederzeit wieder aus der Gruppe entfernen. Beispiel: Wenn ein Client einem Chatroom beitritt oder wenn ein Client den Chatroom verlässt, kann dieser Chatroom als Gruppe betrachtet werden. Ein Client kann mehreren Gruppen beitreten, und eine Gruppe kann mehrere Clients enthalten.

### <a name="user"></a>Benutzer

Verbindungen mit Web PubSub können zu einem einzelnen Benutzer gehören. Ein Benutzer kann über mehrere Verbindungen verfügen, etwa, wenn ein einzelner Benutzer über mehrere Geräte oder mehrere Browsertabs verbunden ist.

### <a name="message"></a>`Message`

Wenn der Client verbunden ist, kann er Nachrichten an die Upstreamanwendung senden oder Nachrichten von der Upstreamanwendung über die WebSocket-Verbindung empfangen.

## <a name="examples"></a>Beispiele

### <a name="broadcast-a-text-message-to-all-clients"></a>Übertragen einer Textnachricht an alle Clients

```C# Snippet:WebPubSubHelloWorld
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll("Hello World!");
```

### <a name="broadcast-a-json-message-to-all-clients"></a>Übertragen einer JSON-Nachricht an alle Clients

```C# Snippet:WebPubSubSendJson
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll(RequestContent.Create(
        new
        {
            Foo = "Hello World!",
            Bar = 42
        }),
        ContentType.ApplicationJson);
```

### <a name="broadcast-a-binary-message-to-all-clients"></a>Übertragen einer binären Nachricht an alle Clients

```C# Snippet:WebPubSubSendBinary
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

Stream stream = BinaryData.FromString("Hello World!").ToStream();
serviceClient.SendToAll(RequestContent.Create(stream), ContentType.ApplicationOctetStream);
```

## <a name="troubleshooting"></a>Problembehandlung

### <a name="setting-up-console-logging"></a>Einrichten der Konsolenprotokollierung
Sie können auch einfach die [Konsolenprotokollierung aktivieren](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/core/Azure.Core/samples/Diagnostics.md#logging), wenn Sie ausführliche Informationen zu den von Ihnen an den Dienst gesendeten Anforderungen erhalten möchten.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]


[azure_sub]: https://azure.microsoft.com/free/dotnet/
[samples_ref]: https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/tests/Samples/
[awps_sample]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp
