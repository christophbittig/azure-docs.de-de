---
title: Verwalten und Überwachen einer App mit dem Azure Spring Boot-Aktor
description: Erfahren Sie, wie Sie Apps mit dem Spring Boot-Aktor verwalten und überwachen.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 001b88e5abe0fc8710d7c87d792de23db4135e16
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486285"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Verwalten und Überwachen einer App mit dem Azure Spring Boot-Aktor

**Dieser Artikel gilt für:** ✔️ Java

Nach der Bereitstellung einer neuen Binärdatei für Ihre App sollten Sie die Funktionalität prüfen und Informationen zu Ihrer ausgeführten Anwendung abrufen. In diesem Artikel wird erläutert, wie Sie über einen von Azure Spring Cloud bereitgestellten Testendpunkt auf die API zugreifen und die produktionsreifen Features für Ihre App zur Verfügung stellen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über eine Spring Boot 2.x-Anwendung verfügen, die erfolgreich bereitgestellt und im Azure Spring Cloud-Dienst gestartet werden kann.  Weitere Informationen finden Sie unter [Schnellstart: Starten einer vorhandenen Anwendung in Azure Spring Cloud über das Azure-Portal](./quickstart.md).

## <a name="verify-app-through-test-endpoint"></a>Überprüfen der App über einen Testendpunkt

1. Wechseln Sie zum **Anwendungsdashboard**, und wählen Sie Ihre App aus, um zur App-Übersichtsseite zu gelangen.

1. Im Bereich **Übersicht** sollte der **Testendpunkt** angezeigt werden.  Greifen Sie über die Befehlszeile oder einen Browser auf den Endpunkt zu, und beobachten Sie die API-Antwort.

1. Notieren Sie sich den **Testendpunkt**-URI, da dieser im kommenden Abschnitt verwendet wird.

>[!TIP]
> * Wenn die App eine Front-End-Seite zurückgibt und andere Dateien über den relativen Pfad referenziert, stellen Sie sicher, dass Ihr Testendpunkt mit einem Schrägstrich (/) endet. Hiermit wird sichergestellt, dass die CSS-Datei ordnungsgemäß geladen wird.
> * Wenn Sie Ihre API in einem Browser anzeigen und dieser erfordert, dass Sie Anmeldeinformationen zum Anzeigen der Seite eingeben, verwenden Sie die [URL-Decodierung](https://www.urldecoder.org/), um Ihren Testendpunkt zu decodieren. Die URL-Decodierung gibt eine URL im folgenden Format zurück: „https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/\<app-name>/\<deployment-name>“.  Verwenden Sie dieses Format für den Zugriff auf Ihren Endpunkt.

## <a name="add-actuator-dependency"></a>Hinzufügen der Aktorabhängigkeit

Fügen Sie die Starter-Abhängigkeit hinzu, um den Aktor zu einem auf Maven basierenden Projekt hinzuzufügen:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

Kompilieren Sie die Binärdatei, und stellen Sie sie für Ihre App bereit.

## <a name="enable-production-ready-features"></a>Aktivieren von produktionsreifen Features

Mit Aktorendpunkten können Sie Ihre Anwendung überwachen und mit ihr interagieren. Die Spring Boot-Anwendung stellt standardmäßig die Endpunkte `health` und `info` zur Verfügung, um arbiträre Anwendungs- und Integritätsinformationen anzuzeigen.

Sie müssen auch die Endpunkte `env` und `configgrops` aktivieren, um die Konfiguration und die konfigurierbare Umgebung zu beobachten.

1. Öffnen Sie den Bereich **Übersicht** der App, wählen Sie im Einstellungsmenü die Option **Konfiguration** aus, und öffnen Sie dann die Konfigurationsseite **Umgebungsvariablen**.
1. Fügen Sie die folgenden Eigenschaften im Format „key:value“ hinzu. Diese Umgebung öffnet die Spring-Aktorendpunkte „env“, „health“ und „info“.

   ```properties
   management.endpoints.web.exposure.include: env,health,info
   ```

1. Klicken Sie auf die Schaltfläche **Speichern**. Daraufhin wird Ihre Anwendung automatisch neu gestartet, und die neuen Umgebungsvariablen werden geladen.

Sie können nun zur App-Übersicht zurückkehren und warten, bis der Bereitstellungsstatus in „Erfolgreich“ geändert wird.  Es werden mehr als nur eine Instanz ausgeführt.

> [!Note]
> Sobald Sie die App öffentlich freigeben, werden diese Aktorendpunkte ebenfalls öffentlich zugänglich. Sie können alle Endpunkte ausblenden, indem Sie die Umgebungsvariable `management.endpoints.web.exposure.include` löschen und `management.endpoints.web.exposure.exclude=*` festlegen.

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>Anzeigen des Aktorendpunkts zum Anzeigen von Anwendungsinformationen

1. Sie können nun auf die URL `"<test-endpoint>/actuator/"` zugreifen, um alle Endpunkte anzuzeigen, die vom Spring Boot-Aktor zur Verfügung gestellt werden.
1. Greifen Sie auf die URL `"<test-endpoint>/actuator/env"` zu. Dann werden die aktiven Profile angezeigt, die von der App verwendet werden, und alle Umgebungsvariablen werden geladen.
1. Wenn Sie eine spezifische Umgebung suchen möchten, können Sie auf die URL `"<test-endpoint>/actuator/env/{toMatch}"` zugreifen, um sie anzuzeigen.

Informationen zum Anzeigen aller integrierten Endpunkte finden Sie unter [Freigeben von Endpunkten](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints).

## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zu Metriken für Azure Spring Cloud](./concept-metrics.md)
* [Grundlegendes zum App-Status in Azure Spring Cloud](./concept-app-status.md)
