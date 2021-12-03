---
title: 'Best Practices für Azure Monitor: Planung'
description: Hier finden Sie Anleitungen und Empfehlungen für Planung und Entwurf vor der Bereitstellung von Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: b04b73cfd7cd2482e14485556df5a477e47a9334
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347972"
---
# <a name="azure-monitor-best-practices---planning-your-monitoring-strategy-and-configuration"></a>Best Practices für Azure Monitor: Planen Ihrer Überwachungsstrategie und -konfiguration
Dieser Artikel ist Teil des Szenarios [Empfehlungen zum Konfigurieren von Azure Monitor](best-practices.md). Hier werden die Planungsschritte erläutert, die Sie vor Beginn der Implementierung ausführen sollten. So wird sichergestellt, dass die von Ihnen gewählten Konfigurationsoptionen Ihren speziellen Geschäftsanforderungen entsprechen.

Wenn Sie noch nicht mit Überwachungskonzepten vertraut sind, beginnen Sie mit dem [Leitfaden zur Cloudüberwachung](/azure/cloud-adoption-framework/manage/monitor), der Teil des [Microsoft Cloud Adoption Framework für Azure](/cloud-adoption-framework/) ist. Dieser Leitfaden definiert allgemeine Konzepte der Überwachung und bietet Anleitungen zum Definieren von Anforderungen für Ihre Überwachungsumgebung und die unterstützenden Prozesse. Der vorliegende Artikel bezieht sich auf Abschnitte dieses Leitfadens, die für bestimmte Planungsschritte relevant sind.
## <a name="understand-azure-monitor-costs"></a>Grundlegendes zu den Kosten für Azure Monitor
Ein Hauptziel Ihrer Überwachungsstrategie ist wahrscheinlich die Minimierung der Kosten. Einige Datensammlungsvorgänge und Features in Azure Monitor verursachen keine Kosten, während für andere Kosten basierend auf der jeweiligen Konfiguration, der Menge der gesammelten Daten oder der Ausführungshäufigkeit anfallen. In den Artikeln in diesem Szenario werden alle Empfehlungen gekennzeichnet, die Kosten verursachen. Wenn Sie Ihre Implementierung für die Kostenoptimierung entwerfen, sollten Sie jedoch die Azure Monitor-Preise kennen. Ausführliche Informationen zu den Preisen für Azure Monitor finden Sie in den folgenden Dokumenten:

- [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/)
- [Überwachen der Nutzung und geschätzten Kosten in Azure Monitor](usage-estimated-costs.md)
- [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](logs/manage-cost-storage.md)
- [Verwalten der Nutzung und der Kosten für Application Insights](app/pricing.md)

## <a name="define-strategy"></a>Definieren der Strategie
Bevor Sie eine Überwachungslösung entwerfen und implementieren, sollten Sie eine Überwachungsstrategie festlegen, um die Ziele und Anforderungen Ihres Plans zu verstehen. Die Strategie definiert Ihre speziellen Anforderungen, die Konfiguration, die diese Anforderungen am besten erfüllt, und die Prozesse für die Nutzung der Überwachungsumgebung, um die Leistung und Zuverlässigkeit Ihrer Anwendungen zu maximieren. Die Konfigurationsoptionen, die Sie für Azure Monitor auswählen, sollten mit Ihrer Strategie konsistent sein.

Im [Leitfaden zur Cloudüberwachung: Formulieren einer Überwachungsstrategie](/azure/cloud-adoption-framework/strategy/monitoring-strategy) finden Sie eine Reihe von Faktoren, die Sie bei der Entwicklung einer Überwachungsstrategie berücksichtigen sollten. Sie sollten auch die [Überwachungsstrategie für Cloudbereitstellungsmodelle](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview) lesen, die beim Vergleich der vollständig cloudbasierten Überwachung mit einem Hybridmodell hilft. 

## <a name="gather-required-information"></a>Sammeln erforderlicher Informationen
Bevor Sie die Details Ihrer Implementierung ermitteln, sollten Sie Informationen sammeln, die zum Definieren dieser Details erforderlich sind. Die folgenden Abschnitten enthalten Informationen, die typischerweise für eine vollständige Implementierung von Azure Monitor erforderlich sind.

 ### <a name="what-needs-to-be-monitored"></a>Was muss überwacht werden?
 Sie konfigurieren wahrscheinlich keine vollständige Überwachung Ihrer sämtlichen Cloudressourcen, sondern konzentrieren sich auf kritische Anwendungen und die Komponenten, von denen diese abhängig sind. Dies reduziert nicht nur die Überwachungskosten, sondern auch die Komplexität Ihrer Überwachungsumgebung. Informationen zum Definieren der benötigten Daten finden Sie im [Leitfaden zur Cloudüberwachung: Erfassen der richtigen Daten](/azure/cloud-adoption-framework/manage/monitor/data-collection).

### <a name="who-needs-to-have-access-and-be-notified"></a>Wer muss Zugriff haben und benachrichtigt werden?
Beim Konfigurieren Ihrer Überwachungsumgebung müssen Sie bestimmen, welche Benutzer Zugriff auf Überwachungsdaten haben sollen und welche Benutzer benachrichtigt werden müssen, wenn ein Problem erkannt wird. Dabei kann es sich um Anwendungs- und Ressourcenbesitzer oder um ein zentrales Überwachungsteam handeln. Diese Informationen legen fest, wie Sie Berechtigungen für den Datenzugriff und Benachrichtigungen bei Warnungen konfigurieren. Möglicherweise benötigen Sie auch benutzerdefinierte Arbeitsmappen, um verschiedenen Benutzern bestimmte Arten von Informationen zu präsentieren.

### <a name="service-level-agreements"></a>Vereinbarungen zum Servicelevel (SLAs) 
Möglicherweise hat Ihre Organisation SLAs eingerichtet, die Ihre Verpflichtungen hinsichtlich Leistung und Betriebszeit Ihrer Anwendungen definieren. Anhand dieser SLAs ist möglicherweise vorgegeben, wie Sie zeitkritische Azure Monitor-Features wie z. B. Warnungen konfigurieren müssen. Außerdem müssen Sie die [Datenlatenz in Azure Monitor](logs/data-ingestion-time.md) verstehen, da diese sich auf die Reaktionsschnelligkeit von Überwachungsszenarien und Ihre Fähigkeit auswirkt, die SLAs zu erfüllen.

## <a name="identify-monitoring-services-and-products"></a>Identifizieren von Überwachungsdiensten und -produkten
Azure Monitor ist für die Integritäts- und Statusüberwachung konzipiert. Eine vollständige Überwachungslösung umfasst in der Regel mehrere Azure-Dienste sowie möglicherweise weitere Produkte. Weitere Überwachungsziele, die möglicherweise zusätzliche Lösungen erfordern, werden im Leitfaden zur Cloudüberwachung in [den primären Überwachungszielen](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements) beschrieben. 

In den folgenden Abschnitten werden weitere Dienste und Produkte beschrieben, die Sie in Verbindung mit Azure Monitor verwenden können. Dieses Szenario enthält derzeit keine Anleitungen zur Implementierung dieser Lösungen, konsultieren Sie daher die zugehörige Dokumentation.

### <a name="security-monitoring"></a>Sicherheitsüberwachung
Während die in Azure Monitor gespeicherten operativen Daten nützlich sein können, um Sicherheitsvorfälle zu untersuchen, sind andere Dienste in Azure zur Überwachung der Sicherheit konzipiert. Führen Sie die Sicherheitsüberwachung in Azure mithilfe von Microsoft Defender für Cloud und Microsoft Sentinel aus.

- [Microsoft Defender für Cloud](../security-center/security-center-introduction.md) sammelt Informationen zu Azure-Ressourcen und Hybridservern. Wenngleich es Sicherheitsereignisse erfassen kann, konzentriert sich Defender für Cloud auf das Sammeln von Bestandsdaten, Risikobewertungsergebnissen und Richtlinienüberwachungen, um auf Sicherheitsrisiken hinzuweisen und Korrekturmaßnahmen zu empfehlen. Zu den wichtigsten Funktionen gehören eine interaktive Netzwerkkarte, Just-in-Time-VM-Zugriff, adaptive Netzwerkhärtung und die adaptive Anwendungssteuerung zum Blockieren verdächtiger ausführbarer Dateien.

- [Microsoft Defender für Server](../security-center/azure-defender.md) ist die Serverbewertungslösung, die von Defender für Cloud bereitgestellt wird. Defender für Server kann Windows-Sicherheitsereignisse an Log Analytics (Protokollanalyse) senden. Für die Warnungserstellung oder Analyse stützt sich Defender für Cloud nicht auf Windows-Sicherheitsereignisse. Die Verwendung dieses Features ermöglicht die zentrale Archivierung von Ereignissen zu Untersuchungs- oder anderen Zwecken.

- [Microsoft Sentinel](../sentinel/overview.md) ist eine SIEM-(Security Information & Event Management-) und SOAR-Lösung (Security Orchestration Automated Response, Sicherheitsorchestrierung mit automatisierter Reaktion). Sentinel sammelt Sicherheitsdaten aus einer Vielzahl von Microsoft- und Drittanbieterquellen, um Warnungen, Visualisierungen und Automatisierung bereitzustellen. Diese Lösung konzentriert sich darauf, so viele Sicherheitsprotokolle wie möglich zu konsolidieren, darunter auch Windows-Sicherheitsereignisse. Microsoft Sentinel kann außerdem Windows-Sicherheitsereignisprotokolle erfassen und einen Log-Analytics-Arbeitsbereich gemeinsam mit Defender für Cloud verwenden. Sicherheitsereignisse können nur von Microsoft Sentinel oder Defender für Cloud gesammelt werden, wenn sie denselben Arbeitsbereich gemeinsam nutzen. Im Gegensatz zu Defender für Cloud sind Sicherheitsereignisse eine wichtige Komponente von Warnungen und Analysen in Microsoft Sentinel.

- [Microsoft Defender für Endpunkt](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) ist eine Endpunktsicherheitsplattform auf Unternehmensniveau, die Organisationen dabei unterstützt, komplexe Bedrohungen zu vermeiden, zu erkennen, zu untersuchen und darauf zu reagieren. Die Lösung wurde primär zum Schutz von Windows-Benutzergeräten entwickelt. Defender für Endpunkt überwacht Arbeitsstationen, Server, Tablets und Mobiltelefone mit verschiedenen Betriebssystemen auf Sicherheitsprobleme und -risiken. Defender für Endpunkt ist eng an Microsoft Endpoint Manager ausgerichtet, um Daten zu erfassen und Sicherheitsbewertungen bereitzustellen. Die Datensammlung basiert in erster Linie auf ETW-Ablaufverfolgungsprotokollen, die Speicherung erfolgt in einem isolierten Arbeitsbereich.


### <a name="system-center-operations-manager"></a>System Center Operations Manager
Möglicherweise haben Sie bereits in System Center Operations Manager investiert, um lokale Ressourcen und Workloads zu überwachen, die auf Ihren virtuellen Computern ausgeführt werden. Sie können [diese Überwachung zu Azure Monitor migrieren](azure-monitor-operations-manager.md) oder beide Produkte gemeinsam in einer Hybridkonfiguration verwenden. Einen Vergleich der beiden Produkte finden Sie im [Leitfaden zur Cloudüberwachung: Übersicht über Überwachungsplattformen](/azure/cloud-adoption-framework/manage/monitor/platform-overview). Unter [Überwachungsstrategie für Cloudbereitstellungsmodelle](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview) finden Sie Anleitungen zur Verwendung der beiden Produkte in einer Hybridkonfiguration und zur Bestimmung des am besten geeigneten Modells für Ihre Umgebung.



## <a name="next-steps"></a>Nächste Schritte

- Schritte und Empfehlungen zum Konfigurieren der Datensammlung in Azure Monitor finden Sie unter [Konfigurieren der Datensammlung](best-practices-data-collection.md).
