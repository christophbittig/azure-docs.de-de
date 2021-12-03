---
title: Überwachen von Data Factorys mit Azure Monitor
description: Erfahren Sie, wie Sie Azure Data Factory-Pipelines mit Azure Monitor überwachen können, indem Sie Diagnoseprotokolle mit Informationen aus Data Factory aktivieren.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.custom: contperf-fy22q1
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 91f377e1e092348a9d3a4ea6be3794c6f212d6b8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853945"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Durchführen der Überwachung und Verwenden von Warnungen für Data Factory mit Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cloudanwendungen sind komplex und verfügen über zahlreiche Variablen. Monitore stellen Daten bereit, die dabei helfen, die ordnungsgemäße Ausführung der Anwendung sicherzustellen. Monitore helfen Ihnen auch, potenzielle Probleme zu vermeiden und aufgetretene Probleme zu beheben. Sie können anhand der Überwachungsdaten umfassende Erkenntnisse zu Ihren Anwendungen gewinnen. Mit diesem Wissen können Sie die Leistung und Wartbarkeit von Anwendungen verbessern. Außerdem können Sie Aktionen automatisieren, die andernfalls einen manuellen Eingriff erfordern.

Azure Monitor stellt grundlegende Metriken und Protokolle der Infrastruktur für die meisten Azure-Dienste bereit. Azure-Diagnoseprotokolle werden von einer Ressource ausgegeben und stellen umfangreiche und in kurzen Abständen erfasste Betriebsdaten der Ressource bereit. Mit Azure Data Factory (ADF) können Diagnoseprotokolle in Azure Monitor geschrieben werden. Das folgende Video enthält eine siebenminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Weitere Informationen finden Sie unter [Übersicht über Azure Monitor](../azure-monitor/overview.md).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Aufbewahren von Azure Data Factory-Metriken und -Pipelineausführungsdaten

Data Factory speichert Pipelineausführungsdaten nur 45 Tage lang. Verwenden Sie Azure Monitor, wenn Sie diese Daten für einen längeren Zeitraum aufbewahren möchten. Mit Monitor können Sie Diagnoseprotokolle zu Analysezwecken an mehrere unterschiedliche Ziele weiterleiten.

* **Storage Account** (Speicherkonto): Speichern Sie Diagnoseprotokolle zur Überwachung oder manuellen Überprüfung in einem Speicherkonto. In den Diagnoseeinstellungen können Sie eine Aufbewahrungsdauer (in Tagen) angeben.
* **Event Hub**: Streamen Sie die Protokolle in Azure Event Hubs. Die Protokolle werden zu Eingaben für einen Partnerdienst oder eine benutzerdefinierte Analyselösung wie Power BI.
* **Log Analytics**: Analysieren Sie die Protokolle mit Log Analytics. Die Data Factory-Integration in Azure Monitor ist für die folgenden Szenarien hilfreich:
  * Sie möchten komplexe Abfragen für einen umfangreichen Satz von Metriken schreiben, die von Data Factory für Monitor veröffentlicht werden. Sie können eigene Benachrichtigungen zu diesen Abfragen über Monitor erstellen.
  * Sie möchten Data Factorys übergreifend überwachen. Sie können Daten aus mehreren Data Factorys an einen einzigen Monitor-Arbeitsbereich weiterleiten.

Sie können auch ein Speicherkonto oder einen Event Hub-Namespace verwenden, das bzw. der sich nicht im Abonnement der Ressource befindet, von der Protokolle ausgegeben werden. Der Benutzer, der die Einstellung konfiguriert, benötigt entsprechenden Zugriff gemäß der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) auf beide Abonnements.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Data Factory-Metriken und -Warnungen](monitor-metrics-alerts.md)
- [Programmseitiges Überwachen und Verwalten von Pipelines](monitor-programmatically.md)
