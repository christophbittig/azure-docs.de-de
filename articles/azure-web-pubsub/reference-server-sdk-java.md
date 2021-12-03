---
title: 'Referenz: Java SDK für Azure Web PubSub'
description: In dieser Referenz wird das Java-SDK für den Azure Web PubSub-Dienst beschrieben.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: e004e32235330509051d635774b0ff685fb2eb4d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493050"
---
# <a name="azure-web-pubsub-service-client-library-for-java"></a>Clientbibliothek des Azure Web PubSub-Diensts für Java

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
    <version>1.0.0-beta.6</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-webpubsubserviceclient-using-connection-string"></a>`WebPubSubServiceClient` mit Verbindungszeichenfolge erstellen

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L21-L24 -->
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubServiceClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-webpubsubserviceclient-using-access-key"></a>`WebPubSubServiceClient` über Zugriffsschlüssel erstellen

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L31-L35 -->
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubServiceClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
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

* [Übertragen einer Nachricht an den gesamten Hub](#broadcast-all "Übertragen einer Nachricht an den gesamten Hub")
* [Übertragen eine Nachricht an eine Gruppe](#broadcast-group "Übertragen eine Nachricht an eine Gruppe")
* [Senden einer Nachricht an eine Verbindung](#send-to-connection "Senden einer Nachricht an eine Verbindung")
* [Senden einer Nachricht an einen Benutzer](#send-to-user "Senden einer Nachricht an einen Benutzer")

<a name="broadcast-all"></a>

### <a name="broadcast-message-to-entire-hub"></a>Übertragen einer Nachricht an den gesamten Hub

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L47-L47 -->
```java
webPubSubServiceClient.sendToAll("Hello world!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="broadcast-group"></a>

### <a name="broadcast-message-to-a-group"></a>Übertragen eine Nachricht an eine Gruppe

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L59-L59 -->
```java
webPubSubServiceClient.sendToGroup("java", "Hello Java!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="send-to-connection"></a>

### <a name="send-message-to-a-connection"></a>Senden einer Nachricht an eine Verbindung

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L71-L71 -->
```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="send-to-user"></a>

### <a name="send-message-to-a-user"></a>Senden einer Nachricht an einen Benutzer
<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L83-L83 -->
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!", WebPubSubContentType.TEXT_PLAIN);
```

## <a name="troubleshooting"></a>Problembehandlung

### <a name="enable-client-logging"></a>Aktivieren der Clientprotokollierung
Sie können die Umgebungsvariable `AZURE_LOG_LEVEL` so festlegen, dass in der Clientbibliothek vorgenommene Protokollierungsanweisungen angezeigt werden. Durch das Festlegen von `AZURE_LOG_LEVEL=2` würden beispielsweise alle Informations-, Warnungs- und Fehlerprotokollmeldungen angezeigt. Die Protokolliergrade finden Sie hier: [Protokolliergrade][log_levels].

### <a name="default-http-client"></a>HTTP-Standardclient
Alle Clientbibliotheken verwenden standardmäßig den Netty-HTTP-Client. Durch Hinzufügen der obigen Abhängigkeit wird die Clientbibliothek automatisch für die Verwendung des Netty-HTTP-Clients konfiguriert. Das Konfigurieren oder Ändern des HTTP-Clients wird detailliert im [Wiki zu HTTP-Clients](https://github.com/Azure/azure-sdk-for-java/wiki/HTTP-clients) beschrieben.

### <a name="default-ssl-library"></a>SSL-Standardbibliothek
Alle Clientbibliotheken verwenden standardmäßig die Tomcat-native Boring-SSL-Bibliothek, um die Leistung auf nativer Ebene für SSL-Vorgänge zu ermöglichen. Die Boring-SSL-Bibliothek ist eine Uber-JAR-Datei mit nativen Bibliotheken für Linux/macOS/Windows und bietet im Vergleich zur SSL-Standardimplementierung im JDK eine bessere Leistung. Weitere Informationen, einschließlich zur Reduzierung der Abhängigkeitsgröße, finden Sie im Abschnitt [Leistungsoptimierung][performance_tuning] des Wikis.

[!INCLUDE [next step](includes/include-next-step.md)]


<!-- LINKS -->

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: https://aka.ms/awps/doc
[samples_readme]: https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/webpubsub/azure-messaging-webpubsub/src/samples/README.md
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[cla]: https://cla.microsoft.com
[coc]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[api]: https://aka.ms/awps/sdk/java
