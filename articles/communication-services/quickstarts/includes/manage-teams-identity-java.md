---
title: Datei einfügen
description: Datei einfügen
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 2efbb9772e70afd699d467668fd1eadd69502b04
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461153"
---
## <a name="set-up-prerequisites"></a>Einrichten erforderlicher Komponenten

- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install), Version 8 oder höher.
- [Apache Maven](https://maven.apache.org/download.cgi).

## <a name="set-up"></a>Einrichtung

### <a name="create-a-new-java-application"></a>Erstellen einer neuen Java-Anwendung

Öffnen Sie Ihr Terminal oder Befehlsfenster. Navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Java-Anwendung erstellen möchten. Führen Sie den unten angegebenen Befehl aus, um das Java-Projekt aus der Vorlage `maven-archetype-quickstart` zu generieren.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Wie Sie sehen, wurde durch die Aufgabe „generate“ ein Verzeichnis erstellt, das den gleichen Namen besitzt wie die Artefakt-ID (`artifactId`). In diesem Verzeichnis enthält der Ordner `src/main/java` den Quellcode des Projekts, `src/test/java directory` enthält die Testquelle, und die Datei `pom.xml` repräsentiert das Projektobjektmodell (POM).

### <a name="install-the-package"></a>Installieren des Pakets

Öffnen Sie die Datei `pom.xml` in Ihrem Text-Editor. Fügen Sie der Gruppe der Abhängigkeiten das folgende Abhängigkeitselement hinzu:

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-communication-identity</artifactId>
        <version>1.2.0-beta.1</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>msal4j</artifactId>
      <version>1.11.0</version>
    </dependency>
</dependencies>
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Navigieren Sie zum Verzeichnis `/src/main/java/com/communication/quickstart`.
1. Öffnen Sie die Datei `App.java` in Ihrem Editor.
1. Ersetzen Sie die Anweisung `System.out.println("Hello world!");`.
1. Fügen Sie Anweisungen vom Typ `import` hinzu.

Verwenden Sie zum Einstieg den folgenden Code:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.InteractiveRequestParameters;
import com.microsoft.aad.msal4j.PublicClientApplication;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws Exception
    {
        System.out.println("Azure Communication Services - Communication access token Quickstart");
        // Quickstart code goes here
    }
}
```

### <a name="step-1-receive-the-azure-active-directory-user-token-via-the-msal-library"></a>Schritt 1: Empfangen des Azure AD-Benutzertokens über die MSAL-Bibliothek

Der erste Schritt im Flow für den Tokenaustausch besteht darin, mithilfe von [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md) ein Token für Ihre Teams-Benutzer abzurufen.

```java
String appId = "<contoso_application_id>";
String authority = "https://login.microsoftonline.com/<contoso_tenant_id>";

PublicClientApplication pca = PublicClientApplication.builder(appId)
        .authority(authority)
        .build();

String redirectUri = "http://localhost";
Set<String> scope = new HashSet<String>();
scope.add("https://auth.msft.communication.azure.com/VoIP");

InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI(redirectUri))
                    .scopes(scope)
                    .build();

IAuthenticationResult result = pca.acquireToken(parameters).get();
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Schritt 2: Initialisieren von CommunicationIdentityClient

Instanziieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit dem Zugriffsschlüssel und dem Endpunkt Ihrer Ressource. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../create-communication-resource.md#store-your-connection-string). Zusätzlich können Sie den Client mit einem beliebigen benutzerdefinierten HTTP-Client initialisieren, der die Schnittstelle `com.azure.core.http.HttpClient`implementiert.

Fügen Sie der `main`-Methode folgenden Code hinzu:

```java
//You can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Schritt 3: Austauschen des Azure AD-Benutzertokens gegen das Teams-Zugriffstoken

Verwenden Sie die Methode `getTokenForTeamsUser`, um ein Zugriffstoken für den Teams-Benutzer auszustellen, das mit den Azure Communication Services SDKs verwendet werden kann.

```java
var accessToken = communicationIdentityClient.getTokenForTeamsUser(result.accessToken());
System.out.println("Token: " + accessToken.getToken());
```

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie zum Verzeichnis, das die Datei `pom.xml` enthält, und kompilieren Sie das Projekt mit dem folgenden `mvn`-Befehl.

Erstellen Sie dann das Paket.

```console
mvn package
```

Führen Sie die App mit dem folgenden `mvn`-Befehl aus.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
