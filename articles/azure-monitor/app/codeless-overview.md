---
title: Überwachen von Apps ohne Codeänderungen – automatische Instrumentierung für Azure Monitor Application Insights | Microsoft-Dokumentation
description: Übersicht über die automatische Instrumentierung für Azure Monitor Application Insights – Anwendungsleistungsverwaltung ohne Code
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 08/31/2021
ms.openlocfilehash: 3f6af0beb887f66f4696707dcc0e0f408004509a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827611"
---
# <a name="what-is-auto-instrumentation-for-azure-monitor-application-insights"></a>Was ist automatische Instrumentierung für Azure Monitor Application Insights?

Mit der automatischen Instrumentierung können Sie die Anwendungsüberwachung mit Application Insights aktivieren, ohne Ihren Code zu ändern.  

Application Insights ist mit verschiedenen Ressourcenanbietern integriert und funktioniert in unterschiedlichen Umgebungen. Im Wesentlichen müssen Sie lediglich den Agent aktivieren und in einigen Fällen konfigurieren, der automatisch die Telemetrie erfasst. In kürzester Zeit werden die Metriken, Anforderungen und Abhängigkeiten in Ihrer Application Insights-Ressource angezeigt, sodass Sie die Quelle potenzieller Probleme vor ihrem Auftreten erkennen und die Grundursache mit der End-to-End-Transaktionsansicht analysieren können.

## <a name="supported-environments-languages-and-resource-providers"></a>Unterstützte Umgebungen, Sprachen und Ressourcenanbieter

Da ständig neue Integrationen hinzugefügt werden, wird die Funktionsmatrix für die automatische Instrumentierung zunehmend komplex. Die nachstehende Tabelle zeigt den aktuellen Status der Unterstützung verschiedener Ressourcenanbieter, Sprachen und Umgebungen.

|Umgebung/Ressourcenanbieter          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service unter Windows           | GA, st. akt.*       | GA, Anmeldung      | Öffentliche Vorschau  | Öffentliche Vorschau  | Nicht unterstützt   |
|Azure App Service unter Linux             | –             | Nicht unterstützt   | Allgemein verfügbar              | Allgemein verfügbar              | Nicht unterstützt   |
|Azure Functions: grundlegend                | GA, st. akt.*       | GA, st. akt.*       | GA, st. akt.*       | GA, st. akt.*       | GA, st. akt.*       |
|Azure Functions: Abhängigkeiten         | Nicht unterstützt   | Nicht unterstützt   | Öffentliche Vorschau  | Nicht unterstützt   | Durch [Erweiterung](monitor-functions.md#distributed-tracing-for-python-function-apps)   |
|Azure Spring Cloud                     | Nicht unterstützt   | Nicht unterstützt   | Öffentliche Vorschau  | Nicht unterstützt   | Nicht unterstützt   |
|Azure Kubernetes Service               | –             | Nicht unterstützt   | Über den-Agent   | Nicht unterstützt   | Nicht unterstützt   |
|Azure-VMs unter Windows                      | Öffentliche Vorschau  | Nicht unterstützt   | Über den-Agent   | Nicht unterstützt   | Nicht unterstützt   |
|Lokale VMs unter Windows                | GA, Anmeldung      | Nicht unterstützt   | Über den-Agent   | Nicht unterstützt   | Nicht unterstützt   |
|Eigenständiger Agent: alle Umg.            | Nicht unterstützt   | Nicht unterstützt   | Allgemein verfügbar              | Nicht unterstützt   | Nicht unterstützt   |

\* „St. akt.“ steht für „standardmäßig aktiviert“. Application Insights wird automatisch aktiviert, sobald Sie die App in unterstützten Umgebungen bereitstellen. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

Die Anwendungsüberwachung für Azure App Service unter Windows ist für Anwendungen in **[ASP.NET](./azure-web-apps-net.md)** (standardmäßig aktiviert), **[ASP.NET Core](./azure-web-apps-net-core.md)** , **[Java](./azure-web-apps-java.md)** (in der öffentlichen Vorschau) und **[Node.js](./azure-web-apps-nodejs.md)** verfügbar. Zum Überwachen einer Python-App fügen Sie das [SDK](./opencensus-python.md) zum Code hinzu.

> [!NOTE]
> Für Windows ist Anwendungsüberwachung derzeit für codebasierte bzw. verwaltete Dienste für App Service verfügbar. Die Überwachung von Apps in Windows-Containern in App Service wird noch nicht durch die Integration in Application Insights unterstützt.

### <a name="linux"></a>Linux
Sie können die Überwachung für **[Java](./azure-web-apps-java.md?)** -Apps, **[Node.js](./azure-web-apps-nodejs.md?tabs=linux)** -Apps und **[ASP.NET Core](./azure-web-apps-net-core.md?tabs=linux)-Apps(Vorschau)** , die unter Linux in App Service ausgeführt werden, über das Portal aktivieren. 

Verwenden Sie für [Python](./opencensus-python.md) das SDK.

## <a name="azure-functions"></a>Azure-Funktionen

Die grundlegende Überwachung für Azure Functions ist standardmäßig für die Sammlung von Protokoll-, Leistungs- und Fehlerdaten sowie HTTP-Anforderungen aktiviert. Für Java-Anwendungen können Sie eine umfassendere Überwachung mit verteilter Ablaufverfolgung aktivieren und End-to-End-Transaktionsdetails erhalten. Diese Funktionalität für Java befindet sich in der öffentlichen Vorschau für Windows, und Sie können sie [im Azure-Portal aktivieren](./monitor-functions.md).

## <a name="azure-spring-cloud"></a>Azure Spring Cloud

### <a name="java"></a>Java 
Die Anwendungsüberwachung für Java-Apps, die in Azure Spring Cloud ausgeführt werden, ist in das Portal integriert. Sie können Application Insights direkt über das Azure-Portal aktivieren, sowohl für die vorhandenen als auch für die neu erstellten Azure Spring Cloud-Ressourcen.  

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Die Instrumentierung von Azure Kubernetes Service ohne Code ist zurzeit für Java-Anwendungen über den [eigenständigen Agent](./java-in-process-agent.md) verfügbar. 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure-Windows-VMs und VM-Skalierungsgruppen

Die automatische Instrumentierung für Azure-VMs und VM-Skalierungsgruppen ist für [.NET](./azure-vm-vmss-apps.md) und [Java](./java-in-process-agent.md) verfügbar. Diese Benutzeroberfläche ist nicht in das Portal integriert. Die Überwachung wird durch wenige Schritte mit einer eigenständigen Lösung aktiviert und erfordert keine Codeänderungen.  

## <a name="on-premises-servers"></a>Lokale Server
Sie können die Überwachung für Ihre [lokalen Windows-Server für .NET-Anwendungen](./status-monitor-v2-overview.md) und für [Java-Apps](./java-in-process-agent.md) ganz einfach aktivieren.

## <a name="other-environments"></a>Andere Umgebungen
Der flexible eigenständige Java-Agent funktioniert in jeder Umgebung, und Ihr Code muss nicht instrumentiert werden. [Befolgen Sie die Anleitung](./java-in-process-agent.md) zum Aktivieren von Application Insights, und informieren Sie sich über die fantastischen Funktionen des Java-Agents. Der Agent befindet sich in der Public Preview und ist in allen Regionen verfügbar. 

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Application Insights](./app-insights-overview.md)
* [Anwendungszuordnung](./app-map.md)
* [End-to-End-Leistungsüberwachung](../app/tutorial-performance.md)
