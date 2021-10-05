---
title: Überwachen der Leistung von Azure App Services | Microsoft-Dokumentation
description: Überwachung der Anwendungsleistung für Azure App Services. Ladezeit für Diagramme und Antwortzeit, Informationen zu den Abhängigkeiten und Festlegen von Benachrichtigungen zur Leistung.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 03ee74851f82fd8ff7833fc5d27c901cf0b2317a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788741"
---
# <a name="application-monitoring-for-azure-app-service-overview"></a>Anwendungsüberwachung für Azure App Service: Übersicht

Das Aktivieren der Überwachung für Ihre ASP.NET-, ASP.NET Core-, Java- und Node.js-basierten Webanwendungen unter [Azure App Services](../../app-service/index.yml) ist jetzt einfacher als je zuvor. Während Sie Ihre App zuvor manuell instrumentieren mussten, ist die neueste Erweiterung/der neueste Agent nun standardmäßig in das App Service-Image integriert. 

## <a name="enable-application-insights"></a>Aktivieren von Application Insights

Es gibt zwei Methoden, um die Überwachung für in Azure App Services gehostete Anwendungen zu aktivieren:

- **Agent-basierte Anwendungsüberwachung** (ApplicationInsightsAgent). 
 
    - Diese Methode ist am einfachsten zu aktivieren, und es sind keine Codeänderung oder erweiterte Konfigurationen erforderlich. Sie wird häufig als „Laufzeitüberwachung“ bezeichnet. Für Azure App Services wird empfohlen, mindestens diese Überwachungsstufe zu aktivieren. Anschließend können Sie je nach Ihrem spezifischen Szenario bewerten, ob eine erweiterte Überwachung durch manuelle Instrumentierung erforderlich ist.

    - Folgendes wird für Agent-basierte Überwachung unterstützt:
        - [.NET Core](./azure-web-apps-net-core.md)
        - [.NET](./azure-web-apps-net.md)
        - [Java](./azure-web-apps-java.md)
        - [Nodejs](./azure-web-apps-nodejs.md)
    
* **Manuelles Instrumentieren der Anwendung über Code** durch Installieren des Application Insights SDK.

    * Dieser Ansatz ist viel anpassungsfähiger, erfordert aber Folgendes: SDK für [.NET Core](./asp-net-core.md), [.NET](./asp-net.md), [Node.js](./nodejs.md), [Python](./opencensus-python.md) und einen eigenständigen Agent für [Java](./java-in-process-agent.md). Diese Methode bedeutet auch, dass Sie die Updates auf die neueste Version der Pakete selbst verwalten müssen.

    * Wenn Sie benutzerdefinierte API-Aufrufe zum Nachverfolgen von Ereignissen/Abhängigkeiten ausführen müssen, die bei der Agent-basierten Überwachung nicht standardmäßig erfasst werden, müssen Sie diese Methode verwenden. Weitere Informationen finden Sie im Artikel zur [API für benutzerdefinierte Ereignisse und Metriken](./api-custom-events-metrics.md). 

> [!NOTE]
> Wenn sowohl die Agent-basierte Überwachung als auch die manuelle SDK-basierte Instrumentierung erkannt wird, werden in .NET nur die Einstellungen für die manuelle Instrumentierung berücksichtigt, während in Java nur die Agent-basierte Instrumentierung die Telemetriedaten sendet. Dadurch wird verhindert, dass doppelte Daten gesendet werden.

> [!NOTE]
> Momentaufnahmendebugger und Profiler sind nur in .NET und .NET Core verfügbar.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie Agent-basierte Anwendungsüberwachung für Ihre [.NET Core](./azure-web-apps-net-core.md)-, [.NET](./azure-web-apps-net.md)-, [Java](./azure-web-apps-java.md)- oder [Node.js](./azure-web-apps-nodejs.md)-Anwendung aktivieren, die in App Service ausgeführt wird. 
