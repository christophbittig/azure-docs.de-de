---
title: 'Tutorial: Nutzen der dynamischen Konfiguration per Pushaktualisierung in einer Java Spring-Einzelinstanz-App'
titleSuffix: Azure App Configuration
description: In diesem Tutorial wird beschrieben, wie Sie die Konfigurationsdaten für eine Java Spring-App per Pushaktualisierung dynamisch aktualisieren.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: mametcal
ms.openlocfilehash: 340638bd5fba981525ac3ff148ed4076f093e178
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020918"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-java-spring-app"></a>Tutorial: Nutzen der dynamischen Konfiguration per Pushaktualisierung in einer Java Spring-App

Die Java Spring-Clientbibliothek von App Configuration unterstützt die bedarfsgesteuerte Aktualisierung der Konfigurationseinstellungen, ohne dass eine Anwendung neu gestartet werden muss. Eine Anwendung kann mit einem oder beiden der folgenden Ansätze so konfiguriert werden, dass App Configuration-Änderungen erkannt werden.

- Abrufmodell: Dies ist das Standardverhalten, bei dem Änderungen an der Konfiguration per Abruf erkannt werden. Nachdem der zwischengespeicherte Wert einer Einstellung abgelaufen ist, wird beim nächsten Aufruf von `refreshConfigurations` für `AppConfigurationRefresh` eine Anforderung an den Server gesendet, um zu überprüfen, ob sich die Konfiguration geändert hat. Bei Bedarf wird dann die aktualisierte Konfiguration gepullt.

- Pushmodell: Hierbei werden [App Configuration-Ereignisse](./concept-app-configuration-event.md) verwendet, um Änderungen an der Konfiguration zu erkennen. Nachdem für App Configuration das Senden von Ereignissen zu Schlüsselwertänderungen mit Event Grid und einem [Webhook](../event-grid/handler-event-hubs.md) eingerichtet wurde, können diese Ereignisse von der Anwendung genutzt werden, um die Gesamtzahl von Anforderungen zu optimieren, mit denen die Konfiguration auf dem aktuellen Stand gehalten werden kann.

In diesem Tutorial wird veranschaulicht, wie Sie dynamische Konfigurationsupdates in Ihrem Code per Pushaktualisierung implementieren können. Dies baut auf der App auf, die in den Schnellstartanleitungen vorgestellt wurde. Arbeiten Sie zunächst [Schnellstart: Erstellen einer Java Spring-App mit Azure App Configuration](./quickstart-java-spring-app.md) durch, bevor Sie fortfahren.

Für die Ausführung der Schritte dieses Tutorials können Sie einen beliebigen Code-Editor verwenden. [Visual Studio Code](https://code.visualstudio.com/) ist eine hervorragende Option, die auf Windows-, macOS- und Linux-Plattformen verfügbar ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten eines Abonnements zum Senden von Ereignissen zu Konfigurationsänderungen von App Configuration an einen Webhook
> * Bereitstellen einer Spring Boot-Anwendung in App Service
> * Einrichten Ihrer Java Spring-App für die Aktualisierung der Konfiguration als Reaktion auf Änderungen in App Configuration
> * Verwenden der aktuellen Konfiguration in Ihrer Anwendung

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- Ein unterstütztes [Java Development Kit (JDK)](/java/azure/jdk) mit Version 8.
- [Apache Maven](https://maven.apache.org/download.cgi) Version 3.0 oder höher
- Ein vorhandener Azure App Configuration-Speicher

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="setup-push-refresh"></a>Einrichten der Pushaktualisierung

1. Öffnen Sie die Datei *pom.xml*, und aktualisieren Sie sie mit den folgenden Abhängigkeiten:

   ```xml
           <dependency>
               <groupId>com.azure.spring</groupId>
               <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
               <version>2.0.0</version>
           </dependency>
   
           <!-- Adds the Ability to Push Refresh -->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-actuator</artifactId>
           </dependency>
   ```

1. Richten Sie die [App Service-Maven-Bereitstellung](../app-service/quickstart-java.md?tabs=javase) ein, damit die Anwendung über Maven in Azure App Service bereitgestellt werden kann.

   ```console
   mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
   ```

1. Öffnen Sie „bootstrap.properties“, und konfigurieren Sie die Azure App Configuration-Pushaktualisierung und Azure Service Bus:

   ```properties
   # Azure App Configuration Properties
   spring.cloud.azure.appconfiguration.stores[0].connection-string= ${AppConfigurationConnectionString}
   spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled= true
   spring.cloud.azure.appconfiguration.stores[0].monitoring.cacheExpiration= 30d
   spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key= sentinel
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.name= myToken
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.secret= myTokenSecret
   
   management.endpoints.web.exposure.include= "appconfiguration-refresh"
   ```

Eine zufällige Verzögerung wird hinzugefügt, bevor der zwischengespeicherte Wert als geändert gekennzeichnet wird, um eine potenzielle Drosselung zu reduzieren. Die maximale Standardverzögerung vor der Kennzeichnung des zwischengespeicherten Werts als geändert beträgt 30 Sekunden.

> [!NOTE]
> Der Name des primären Tokens sollte in App Configuration als Schlüssel gespeichert werden. Anschließend sollte das primäre Tokengeheimnis als Key Vault-Verweise von App Configuration gespeichert werden, um die Sicherheit zu erhöhen.

## <a name="build-and-run-the-app-locally"></a>Lokales Erstellen und Ausführen der App

Event Grid-Webhooks müssen bei der Erstellung überprüft werden. Führen Sie die Überprüfung anhand der Anweisungen in [diesem Leitfaden](../event-grid/webhook-event-delivery.md) durch, oder starten Sie Ihre Anwendung mit bereits konfigurierter Spring-Webbibliothek von Azure App Configuration. Dadurch wird die Anwendung für Sie registriert. Wenn Sie ein Ereignisabonnement verwenden möchten, führen Sie die Schritte in den nächsten beiden Abschnitten aus.

1. Legen Sie die Umgebungsvariable auf die Verbindungszeichenfolge Ihrer App Configuration-Instanz fest:

    #### <a name="windows-command-prompt"></a>[Windows-Eingabeaufforderung](#tab/cmd)

    ```cmd
    setx AppConfigurationConnectionString <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

1. Führen Sie den folgenden Befehl aus, um die Konsolen-App zu erstellen:

   ```shell
    mvn package
   ```

1. Führen Sie nach der erfolgreichen Erstellung den folgenden Befehl aus, um die App lokal auszuführen:

    ```shell
    mvn spring-boot:deploy
    ```

## <a name="set-up-an-event-subscription"></a>Einrichten eines Ereignisabonnements

1. Öffnen Sie die App Configuration-Ressource im Azure-Portal, und klicken Sie anschließend im Bereich `Events` (Ereignisse) auf `+ Event Subscription` (+ Ereignisabonnement).

    :::image type="content" source="./media/events-pane.png" alt-text="Der Ereignisbereich enthält eine Option zum Erstellen neuer Abonnements." :::

1. Geben Sie einen Namen für `Event Subscription` (Ereignisabonnement) und `System Topic` (Systemthema) ein. Standardmäßig werden die Ereignistypen „Key-Value modified“ (Schlüssel-Wert-Paar geändert) und „Key-Value deleted“ (Schlüssel-Wert-Paar gelöscht) festgelegt. Sie können diese Einstellung ändern und außerdem auf der Registerkarte „Filter“ die genauen Gründe für das Senden eines Pushereignisses auswählen.

    :::image type="content" source="./media/create-event-subscription.png" alt-text="Für Ereignisse müssen ein Name, ein Thema und Filter angegeben werden." :::

1. Wählen Sie unter `Endpoint Type` die Option `Web Hook` und dann `Select an endpoint` aus.

    :::image type="content" source="./media/event-subscription-webhook-endpoint.png" alt-text="Durch Auswählen der Option unter „Endpunkt“ wird ein neues Blatt zur Eingabe des Endpunkt-URI erstellt." :::

1. Der Endpunkt setzt sich aus dem URI der Anwendung und der Zeichenfolge „/actuator/appconfiguration-refresh?{IhrTokenname}={IhrTokengeheimnis}“ zusammen. Beispiel: `https://my-azure-webapp.azurewebsites.net/actuator/appconfiguration-refresh?myToken=myTokenSecret`

1. Klicken Sie auf `Create` (Erstellen), um das Ereignisabonnement zu erstellen. Wenn `Create` ausgewählt ist, wird eine Registrierungsanforderung für den Webhook an Ihre Anwendung gesendet. Diese wird von der Azure App Configuration-Clientbibliothek empfangen und überprüft, und es wird eine gültige Antwort zurückgegeben.

1. Klicken Sie im Bereich `Events` (Ereignisse) auf `Event Subscriptions` (Ereignisabonnements), um sich zu vergewissern, dass die Erstellung des Abonnements erfolgreich war.

    :::image type="content" source="./media/event-subscription-view-webhook.png" alt-text="Webhook in einer Tabelle am unteren Seitenrand" :::

> [!NOTE]
> Beim Abonnieren von Konfigurationsänderungen können ein oder mehrere Filter genutzt werden, um die Anzahl von Ereignissen zu reduzieren, die an Ihre Anwendung gesendet werden. Diese können entweder als [Event Grid-Abonnementfilter](../event-grid/event-filtering.md) oder [Service Bus-Abonnementfilter](../service-bus-messaging/topic-filters.md) konfiguriert werden. Beispielsweise kann ein Abonnementfilter nur verwendet werden, um Ereignisse zu Änderungen in einem Schlüssel zu abonnieren, der mit einer bestimmten Zeichenfolge beginnt.

## <a name="verify-and-test-application"></a>Überprüfen und Testen der Anwendung

1. Nachdem Ihre Anwendung ausgeführt wird, testen Sie sie mit *cURL*. Beispiel:

   ```cmd
   curl -X GET http://localhost:8080
   ```

1. Öffnen Sie das **Azure-Portal**, und navigieren Sie zu der App Configuration-Ressource, die Ihrer Anwendung zugeordnet ist. Wählen Sie unter **Vorgänge** die Option **Konfigurations-Explorer** aus, und aktualisieren Sie die Werte der folgenden Schlüssel:

    | Schlüssel | Wert |
    |---|---|
    | application/config.message | Hello - Updated |

1. Aktualisieren Sie die Browserseite, um die neue Nachricht anzuzeigen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Ihre Java-App aktiviert, um Konfigurationseinstellungen aus App Configuration dynamisch zu aktualisieren. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine von Azure verwaltete Identität hinzufügen, um den Zugriff auf App Configuration zu optimieren.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)