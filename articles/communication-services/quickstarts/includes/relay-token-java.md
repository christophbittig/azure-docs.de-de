---
title: include file
description: Datei einfügen
services: azure-communication-services
author: rahulva
manager: shahen
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/20/2021
ms.topic: include
ms.custom: include file
ms.author: rahulva
ms.openlocfilehash: 9e55af5bed17c1701a443c934fc6e356fa63c032
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893219"
---
> [!NOTE]
> Den fertigen Code für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/relay-token-quickstart).

## <a name="prerequisites-for-java"></a>Voraussetzungen für Java

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install), Version 8 oder höher.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Eine bereitgestellte Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../create-communication-resource.md)

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-java-application"></a>Erstellen einer neuen Java-Anwendung

Öffnen Sie Ihr Terminal oder Befehlsfenster. Navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Java-Anwendung erstellen möchten. Führen Sie den unten angegebenen Befehl aus, um das Java-Projekt auf der Grundlage der Vorlage „maven-archetype-quickstart“ zu generieren.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Wie Sie sehen, wurde durch die Aufgabe „generate“ ein Verzeichnis erstellt, das den gleichen Namen besitzt wie die Artefakt-ID (`artifactId`). In diesem Verzeichnis enthält das Verzeichnis „src/main/java“ den Quellcode des Projekts. Das Verzeichnis `src/test/java directory` enthält die Testquelle, und die Datei `pom.xml` ist das Projektobjektmodell (POM) des Projekts.

### <a name="install-the-package"></a>Installieren des Pakets

Öffnen Sie die Datei **pom.xml** in Ihrem Text-Editor. Fügen Sie der Gruppe „dependencies“ das folgende Abhängigkeitselement hinzu.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks 

Über das Projektverzeichnis:

1. Navigieren Sie zum Verzeichnis `/src/main/java/com/communication/quickstart`.
2. Öffnen Sie die Datei `App.java` in Ihrem Editor.
3. Ersetzen Sie die Anweisung `System.out.println("Hello world!");`.
4. Fügen Sie Anweisungen vom Typ `import` hinzu.

Verwenden Sie zum Einstieg den folgenden Code:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.communication.network.traversal.*;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Network Credentials Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit dem Zugriffsschlüssel und dem Endpunkt Ihrer Ressource. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../create-communication-resource.md#store-your-connection-string). Zusätzlich können Sie den Client mit einem beliebigen benutzerdefinierten HTTP-Client initialisieren, der die Schnittstelle `com.azure.core.http.HttpClient`implementiert.

Fügen Sie der `main`-Methode folgenden Code hinzu:

```java
// Your can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

CommunicationRelayClient communicationRelayClient = new CommunicationRelayClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();

```

Anstelle des Endpunkts und des Zugriffsschlüssels können Sie mithilfe der `connectionString()`-Funktion auch die gesamte Verbindungszeichenfolge bereitstellen.
```java
// Your can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationRelayClient communicationRelayClient = new CommunicationRelayClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

## <a name="create-an-identity"></a>Erstellen einer Identität

Von Azure Communication Services wird ein einfaches Identitätsverzeichnis gepflegt. Verwenden Sie die Methode `createUser`, um in dem Verzeichnis einen neuen Eintrag mit einer eindeutigen `Id` zu erstellen. Speichern Sie die empfangene Identität mit einer Zuordnung zu den Benutzern Ihrer Anwendung. Beispielsweise, indem Sie sie in der Datenbank des Anwendungsservers speichern. Die Identität ist später erforderlich, um Zugriffstoken auszustellen.

```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

## <a name="getting-the-relay-configuration"></a>Abrufen der Relaykonfiguration
Rufen Sie den Azure Communication-Tokendienst auf, um das Benutzerzugriffstoken gegen ein TURN-Diensttoken zu tauschen

```java
CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
            .connectionString(connectionString)
            .buildClient();

CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("User id: " + user.getId());

CommunicationRelayConfiguration config = communicationRelayClient.getRelayConfiguration(user);
        
        System.out.println("Expires on:" + config.getExpiresOn());
        List<CommunicationIceServer> iceServers = config.getIceServers();

        for (CommunicationIceServer iceS : iceServers) {
            System.out.println("URLS: " + iceS.getUrls());
            System.out.println("Username: " + iceS.getUsername());
            System.out.println("Credential: " + iceS.getCredential());
        } 
```

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie zum Verzeichnis, das die Datei *pom.xml* enthält, und kompilieren Sie das Projekt mit dem folgenden `mvn`-Befehl.

```console
mvn compile
```

Erstellen Sie dann das Paket.

```console
mvn package
```

Führen Sie die App mit dem folgenden `mvn`-Befehl aus.

Navigieren Sie zum Verzeichnis, das die Datei *pom.xml* enthält, und kompilieren Sie das Projekt mit dem folgenden `mvn`-Befehl.

```console
mvn compile
```

Erstellen Sie dann das Paket.

```console
mvn package
```

Führen Sie die App mit dem folgenden `mvn`-Befehl aus.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
