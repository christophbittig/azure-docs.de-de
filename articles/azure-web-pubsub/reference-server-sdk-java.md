---
title: 'Referenz: .Java-Server-SDK für den Azure Web PubSub-Dienst'
description: In dieser Referenz wird das Java-Server-SDK für den Azure Web PubSub-Dienst beschrieben.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: b36bf3715458fdf66d8c1a113e96e048bd414d0a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114770"
---
# <a name="java-server-sdk-for-azure-web-pubsub-service"></a>Java-Server-SDK für den Azure Web PubSub-Dienst

Verwenden Sie die Bibliothek für:

- Das Versenden von Nachrichten an Hubs und Gruppen.
- Das Versenden von Nachrichten an bestimmte Benutzer und Verbindungen.
- Das Organisieren von Benutzern und Verbindungen in Gruppen.
- Das Schließen von Verbindungen
- Das Erteilen, Widerrufen und Prüfen von Berechtigungen für eine vorhandene Verbindung

[Quellcode][source_code] | [API-Referenzdokumentation][api] | [Produktdokumentation][product_documentation] | [Beispiele][samples_readme]

## <a name="getting-started"></a>Erste Schritte

### <a name="prerequisites"></a>Voraussetzungen

- [Java Development Kit (JDK)][jdk_link], Version 8 oder höher.
- [Azure-Abonnement][azure_subscription]

### <a name="include-the-package"></a>Schließen Sie das Paket ein

[//]: # ({x-version-update-start;com.azure:azure-messaging-webpubsub;current})

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-webpubsub</artifactId>
    <version>1.0.0-beta.2</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-web-pubsub-client-using-connection-string"></a>Erstellen eines Web PubSub-Clients mithilfe einer Verbindungszeichenfolge

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-client-using-access-key"></a>Erstellen eines Web PubSub-Clients mithilfe eines Zugriffsschlüssels

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-group-client"></a>Erstellen eines Web PubSub-Gruppenclients
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .hub("chat")
    .buildClient();
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("java");
```

## <a name="key-concepts"></a>Wichtige Begriffe

[!INCLUDE [Terms](includes/terms.md)]


## <a name="examples"></a>Beispiele

### <a name="broadcast-message-to-entire-hub"></a>Übertragen einer Nachricht an den gesamten Hub

```java
webPubSubServiceClient.sendToAll("Hello world!");
```

### <a name="broadcast-message-to-a-group"></a>Übertragen eine Nachricht an eine Gruppe

```java
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("Java");
javaGroup.sendToAll("Hello Java!");
```

### <a name="send-message-to-a-connection"></a>Senden einer Nachricht an eine Verbindung

```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!");
```

### <a name="send-message-to-a-user"></a>Senden einer Nachricht an einen Benutzer
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!");
```

## <a name="troubleshooting"></a>Problembehandlung

### <a name="enable-client-logging"></a>Aktivieren der Clientprotokollierung
Sie können die Umgebungsvariable `AZURE_LOG_LEVEL` so festlegen, dass in der Clientbibliothek vorgenommene Protokollierungsanweisungen angezeigt werden. Durch das Festlegen von `AZURE_LOG_LEVEL=2` würden beispielsweise alle Informations-, Warnungs- und Fehlerprotokollmeldungen angezeigt. Die Protokolliergrade finden Sie hier: [Protokolliergrade][log_levels].

### <a name="default-http-client"></a>HTTP-Standardclient
Alle Clientbibliotheken verwenden standardmäßig den Netty-HTTP-Client. Durch Hinzufügen der obigen Abhängigkeit wird die Clientbibliothek automatisch für die Verwendung des Netty-HTTP-Clients konfiguriert. Das Konfigurieren oder Ändern des HTTP-Clients wird detailliert im [Wiki zu HTTP-Clients](/azure/developer/java/sdk/http-client-pipeline) beschrieben.

### <a name="default-ssl-library"></a>SSL-Standardbibliothek
Alle Clientbibliotheken verwenden standardmäßig die Tomcat-native Boring-SSL-Bibliothek, um die Leistung auf nativer Ebene für SSL-Vorgänge zu ermöglichen. Die Boring-SSL-Bibliothek ist eine Uber-JAR-Datei mit nativen Bibliotheken für Linux/macOS/Windows und bietet im Vergleich zur SSL-Standardimplementierung im JDK eine bessere Leistung. Weitere Informationen, einschließlich zur Reduzierung der Abhängigkeitsgröße, finden Sie im Abschnitt [Leistungsoptimierung][performance_tuning] des Wikis.

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: https://aka.ms/awps/doc
[samples_readme]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[api]: /java/api/com.azure.messaging.webpubsub

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]
