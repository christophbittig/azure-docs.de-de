---
title: 'Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer Java-Web-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie einer Java-Webanwendung per OpenID Connect die Option „Mit Microsoft anmelden“ hinzufügen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:Java", devx-track-java, mode-api
ms.openlocfilehash: c90516fb5012774f11463f06ab26f74b0dc5c06c
ms.sourcegitcommit: 34d047300d800cf6ff7d9dd3e573a0d785f61abc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2022
ms.locfileid: "135921458"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer Java-Web-App

In diesem Schnellstart laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine Java-Webanwendung Benutzer anmelden und die Microsoft Graph-API aufrufen kann. Benutzer aus einer beliebigen Azure AD-Organisation (Azure Active Directory) können sich bei der Anwendung anmelden.

 Eine Übersicht finden Sie im [Diagramm zur Funktionsweise des Beispiels](#how-the-sample-works).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel benötigen Sie Folgendes:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 oder höher.
- [Maven](https://maven.apache.org/).


#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal

So verwenden Sie das Codebeispiel in dieser Schnellstartanleitung:

1. Fügen Sie die Antwort-URLs `https://localhost:8443/msal4jsample/secure/aad` und `https://localhost:8443/msal4jsample/graph/me` hinzu.
1. Erstellen Sie einen geheimen Clientschlüssel.
> [!div class="nextstepaction"]
> [Make these changes for me]() (Diese Änderungen für mich vornehmen)

> [!div class="alert alert-info"]
> ![Bereits konfiguriert](media/quickstart-v2-aspnet-webapp/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-code-sample"></a>Schritt 2: Herunterladen des Codebeispiels

Laden Sie das Projekt herunter, und extrahieren Sie die ZIP-Datei in einen Ordner in der Nähe des Stammverzeichnisses Ihres Laufwerks. Beispiel: *C:\Azure-Samples*.

Geben Sie die `server.ssl.key`-Eigenschaften an, um HTTPS mit localhost zu verwenden. Verwenden Sie zum Generieren eines selbstsignierten Zertifikats das keytool-Hilfsprogramm (in JRE enthalten).

Hier sehen Sie ein Beispiel:
```
  keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password

  server.ssl.key-store-type=PKCS12
  server.ssl.key-store=classpath:keystore.p12
  server.ssl.key-store-password=password
  server.ssl.key-alias=testCert
  ```
  Speichern Sie die generierte Keystore-Datei im Ordner *resources*.

> [!div class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip).

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div class="sxs-lookup"]

#### <a name="step-3-run-the-code-sample"></a>Schritt 3: Ausführen des Codebeispiels

Führen Sie zum Ausführen des Projekts einen der folgenden Schritte aus:

- Führen Sie das Codebeispiel direkt über Ihre IDE aus, indem Sie den eingebetteten Spring Boot-Server verwenden.
- Nutzen Sie das Verpacken als WAR-Datei, indem Sie [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) verwenden und dann die Bereitstellung in einer J2EE-Containerlösung wie [Apache Tomcat](http://tomcat.apache.org/) durchführen.

##### <a name="running-the-project-from-an-ide"></a>Ausführen des Projekts aus einer IDE

Wählen Sie zum Ausführen der Webanwendung aus einer IDE den Befehl „run“ aus, und navigieren Sie dann zur Startseite des Projekts. In diesem Beispiel lautet die Standard-URL der Startseite https://localhost:8443.

1. Wählen Sie auf der ersten Seite die Schaltfläche **Anmelden** aus, um Benutzer zu Azure Active Directory umzuleiten und zur Eingabe der Anmeldeinformationen aufzufordern.

1. Nachdem die Benutzer authentifiziert wurden, werden sie an `https://localhost:8443/msal4jsample/secure/aad` umgeleitet. Der Benutzer ist nun angemeldet, und auf der Seite werden Informationen zum Benutzerkonto angezeigt. Die Beispielbenutzeroberfläche enthält die folgenden Schaltflächen:
    - **Sign Out** (Abmelden): Meldet den aktuellen Benutzer von der Anwendung ab und leitet ihn zur Startseite um.
    - **Show User Info** (Benutzerinformationen anzeigen): Ruft ein Token für Microsoft Graph ab und ruft Microsoft Graph mit einer Anforderung auf, die das Token enthält, woraufhin grundlegende Informationen zum angemeldeten Benutzer zurückgegeben werden.

##### <a name="running-the-project-from-tomcat"></a>Ausführen des Projekts über Tomcat

Wenn Sie das Webbeispiel in Tomcat bereitstellen möchten, müssen Sie einige Änderungen am Quellcode vornehmen.

1. Öffnen Sie *ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication*.

    - Löschen Sie den gesamten Quellcode, und ersetzen Sie ihn durch den folgenden Code:

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

2.   Der HTTP-Standardport von Tomcat ist 8080. Sie benötigen aber eine HTTPS-Verbindung über Port 8443. Konfigurieren Sie diese Einstellung wie folgt:
        - Navigieren Sie zu *tomcat/conf/server.xml*.
        - Suchen Sie nach dem Tag `<connector>`, und ersetzen Sie den vorhandenen Connector durch diesen Connector:

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

3. Öffnen Sie ein Eingabeaufforderungsfenster. Navigieren Sie zum Stammordner dieses Beispiels (in dem sich die Datei „pom.xml“ befindet), und führen Sie `mvn package` aus, um das Projekt zu erstellen.
    - Mit diesem Befehl wird die Datei *msal-web-sample-0.1.0.war* im Verzeichnis */targets* erstellt.
    - Benennen Sie diese Datei in *msal4jsample.war* um.
    - Stellen Sie diese WAR-Datei mit Tomcat oder einer beliebigen anderen J2EE-Containerlösung bereit.
        - Um die Bereitstellung vorzunehmen, kopieren Sie die Datei „msal4jsample.war“ in das Verzeichnis */webapps/* Ihrer Tomcat-Installation und starten dann den Tomcat-Server.

4. Navigieren Sie nach dem Bereitstellen der Datei in einem Browser zu https://localhost:8443/msal4jsample.

> [!IMPORTANT]
> Für die Anwendung in dieser Schnellstartanleitung wird ein Clientgeheimnis verwendet, um sich selbst als vertraulicher Client zu identifizieren. Da der geheime Clientschlüssel Ihren Projektdateien im Nur-Text-Format hinzugefügt wird, empfehlen wir Ihnen, aus Sicherheitsgründen ein Zertifikat anstelle eines Clientgeheimnisses zu verwenden, bevor Sie die Anwendung in einer Produktionsumgebung nutzen. Weitere Informationen zur Verwendung eines Zertifikats finden Sie unter [Zertifikatanmeldeinformationen für die Anwendungsauthentifizierung](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Weitere Informationen

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
![Diagramm: Funktionsweise der in dieser Schnellstartanleitung generierten Beispiel-App](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>Abrufen von MSAL

MSAL für Java (MSAL4J) ist die Java-Bibliothek zum Anmelden von Benutzern und Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet wird.

Sie können Ihrer Anwendung MSAL4J hinzufügen, indem Sie Maven oder Gradle für die Verwaltung Ihrer Abhängigkeiten verwenden. Hierzu sind in Ihrer Anwendung folgende Änderungen an der Datei „pom.xml“ (Maven) oder „build.gradle“ (Gradle) erforderlich:

In „pom.xml“:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

In „build.gradle“:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="initialize-msal"></a>MSAL initialisieren

Fügen Sie einen Verweis auf MSAL für Java hinzu, indem Sie am Anfang der Datei, in der Sie MSAL4J verwenden möchten, den folgenden Code hinzufügen:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Eine ausführlichere Erläuterung zum Entwickeln von Web-Apps, die Benutzer bei Microsoft Identity Platform anmelden, finden Sie in unserer mehrteiligen Szenarioreihe:

> [!div class="nextstepaction"]
> [Szenario: Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md?tabs=java)
