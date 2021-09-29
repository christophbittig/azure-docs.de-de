---
title: 'Überwachen von Anwendungen, die in Azure Functions mit Application Insights ausgeführt werden: Azure Monitor | Microsoft-Dokumentation'
description: Azure Monitor kann nahtlos in Ihre in Azure Functions ausgeführte Anwendung integriert werden und ermöglicht die Überwachung der Leistung sowie eine blitzschnelle Erkennung von Problemen mit Ihren Apps.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 08/27/2021
ms.openlocfilehash: ac160463dbfa5fd66afb351c0c0edc5f59b61b30
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124751037"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Überwachen von Azure Functions mit Azure Monitor Application Insights

[Azure Functions](../../azure-functions/functions-overview.md) bietet standardmäßige Integration in Azure Application Insights zum Überwachen von Funktionen. Für andere Sprachen als .NET und .NETCore sind zusätzliche sprachspezifische Worker/Erweiterungen erforderlich, um die Vorteile der verteilten Ablaufverfolgung optimal nutzen zu können. 

Application Insights sammelt Protokoll-, Leistungs- und Fehlerdaten und erkennt automatisch Leistungsanomalien. Application Insights verfügt über leistungsstarke Analysetools, mit denen Sie Probleme untersuchen und nachvollziehen können, wie Ihre Funktionen verwendet werden. Wenn Sie Einblick in Ihre Anwendungsdaten haben, können Sie Leistung und Benutzerfreundlichkeit kontinuierlich verbessern. Sie können Application Insights auch während der lokalen Entwicklung an Funktions-App-Projekten verwenden. 

Die erforderliche Application Insights-Instrumentierung ist in Azure Functions integriert. Sie benötigen lediglich einen gültigen Instrumentierungsschlüssel, um Ihre Funktions-App mit einer Application Insights-Ressource zu verbinden. Der Instrumentierungsschlüssel sollte den Anwendungseinstellungen hinzugefügt werden, wenn ihre Funktions-App-Ressource in Azure erstellt wird. Wenn Ihre Funktions-App noch nicht über diesen Schlüssel verfügt, können Sie ihn manuell festlegen. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](../../azure-functions/functions-monitoring.md?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-public-preview"></a>Verteilte Ablaufverfolgung für Java-Anwendungen (öffentliche Vorschau)

> [!IMPORTANT]
> Dieses Feature befindet sich zurzeit in der öffentlichen Vorschau für Java Azure Functions unter Windows und Linux.

Wenn Ihre Anwendungen in Java geschrieben sind, können Sie umfassendere Daten aus ihren Funktionsanwendungen anzeigen, einschließlich Anforderungen, Abhängigkeiten, Protokolle und Metriken. Die zusätzlichen Daten ermöglichen Ihnen außerdem das Anzeigen und Diagnostizieren von End-to-End-Transaktionen sowie eine Anwendungsübersicht, die viele Transaktionen aggregiert, um eine topologische Ansicht der Interaktion der Systeme sowie der durchschnittlichen Leistung und Fehlerraten zu erhalten.

Die End-to-End-Diagnose und die Anwendungsübersicht ermöglichen Einblicke in eine einzelne Transaktion/Anforderung. Gemeinsam sind diese beiden Features hilfreich, um die Grundursache von Zuverlässigkeitsproblemen und Leistungsengpässen für jede Anforderung einzeln zu ermitteln.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Aktivieren der verteilten Ablaufverfolgung für Java-Funktions-Apps

Navigieren Sie zum Übersichtsblatt für Funktions-Apps und dann zu den Konfigurationen. Klicken Sie unter „Anwendungseinstellungen“ auf „+ Neue Anwendungseinstellung“. 

> [!div class="mx-imgBorder"]
> ![Fügen Sie unter Einstellungen neue Anwendungseinstellungen hinzu.](./media//functions/create-new-setting.png)

Fügen Sie die folgenden Anwendungseinstellungen mit den unten angegebenen Werten hinzu, und klicken Sie dann oben links auf „Speichern“. FERTIG!

#### <a name="windows"></a>Windows
```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

#### <a name="linux"></a>Linux
```
ApplicationInsightsAgent_EXTENSION_VERSION -> ~3
```

## <a name="distributed-tracing-for-python-function-apps"></a>Verteilte Ablaufverfolgung für Python-Funktions-Apps

Um benutzerdefinierte Telemetriedaten von Diensten wie Redis, Memcached, MongoDB und anderen zu sammeln, können Sie die [OpenCensus-Python-Erweiterung](https://github.com/census-ecosystem/opencensus-python-extensions-azure) verwenden und [Ihre Telemetriedaten protokollieren](../../azure-functions/functions-reference-python.md?tabs=azurecli-linux%2capplication-level#log-custom-telemetry). Die Liste der unterstützten Dienste finden Sie [hier](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib).

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich ausführlicher über die Überwachung unter [Überwachen von Azure Functions](../../azure-functions/functions-monitoring.md).
* Verschaffen Sie sich einen Überblick über die [verteilte Ablaufverfolgung](./distributed-tracing.md).
* Informieren Sie sich über die Möglichkeiten der [Anwendungsübersicht](./app-map.md?tabs=net) für Ihr Unternehmen.
* Erfahren Sie mehr über [Anforderungen und Abhängigkeiten für Java-Apps](./java-in-process-agent.md).
* Informieren Sie sich ausführlicher über [Azure Monitor](../overview.md) und [Application Insights](./app-insights-overview.md).