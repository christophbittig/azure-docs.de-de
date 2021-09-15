---
title: 'Referenz: .NET-Server-SDK für den Azure Web PubSub-Dienst'
description: In dieser Referenz wird das .NET-Server-SDK für den Azure Web PubSub-Dienst beschrieben.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 4bb4c5d90958a31e26c39ce9d0fe7e22bec769e2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115413"
---
# <a name="net-server-sdk-for-azure-web-pubsub-service"></a>.NET-Server-SDK für den Azure Web PubSub-Dienst

Mithilfe dieser Bibliothek können die folgenden Aktionen ausgeführt werden. Details zu den hier verwendeten Begriffen werden im Abschnitt [Wichtige Begriffe](#key-concepts) beschrieben.

- Das Versenden von Nachrichten an Hubs und Gruppen 
- Das Versenden von Nachrichten an bestimmte Benutzer und Verbindungen
- Das Organisieren von Benutzern und Verbindungen in Gruppen
- Das Schließen von Verbindungen
- Das Erteilen, Widerrufen und Prüfen von Berechtigungen für eine vorhandene Verbindung

[Quellcode][code] |
[Paket][package] |
[API-Referenzdokumentation][api] |
[Produktdokumentation](https://aka.ms/awps/doc) |
[Beispiele][samples_ref]

## <a name="getting-started"></a>Erste Schritte
### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie die Clientbibliothek über [NuGet](https://www.nuget.org/):

```PowerShell
dotnet add package Azure.Messaging.WebPubSub --prerelease
```

### <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement][azure_sub].
- Eine vorhandene Azure Web PubSub-Dienstinstanz.

### <a name="authenticate-the-client"></a>Authentifizieren des Clients

Für die Interaktion mit dem Dienst müssen Sie eine Instanz der WebPubSubServiceClient-Klasse erstellen. Hierzu benötigen Sie die Verbindungszeichenfolge oder einen Schlüssel. Beides steht im Azure-Portal zur Verfügung.

### <a name="create-a-webpubsubserviceclient"></a>Erstellen der Datei `WebPubSubServiceClient`

```csharp
var serviceClient = new WebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", new AzureKeyCredential("<access-key>"));
```

## <a name="key-concepts"></a>Wichtige Begriffe

[!INCLUDE [Termsc](includes/terms.md)]

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
Sie können auch einfach die [Konsolenprotokollierung aktivieren](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging), wenn Sie die an den Dienst gesendeten Anforderungen eingehender untersuchen möchten.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp
[code]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/Azure.Messaging.WebPubSub/src
[package]: https://www.nuget.org/packages/Azure.Messaging.WebPubSub
[api]: /dotnet/api/azure.messaging.webpubsub

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]
