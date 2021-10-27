---
title: Best Practices für Azure Monitor
description: Hier finden Sie Anleitungen und Empfehlungen für die Bereitstellung von Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 2caff70e56f80d4f46859cec58275263375ee4de
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181652"
---
# <a name="azure-monitor-best-practices"></a>Best Practices für Azure Monitor
Dieses Szenario enthält empfohlene Anleitungen zum Konfigurieren von Azure Monitor-Features, um die Leistung und Verfügbarkeit Ihrer cloud- und hybridbasierten Anwendungen und Ressourcen zu überwachen. 

Azure Monitor ist verfügbar, sobald Sie ein Azure-Abonnement erstellen. Das Aktivitätsprotokoll beginnt sofort mit dem Sammeln von Ereignissen zu Aktivitäten im Abonnement, und Plattformmetriken werden für alle von Ihnen erstellten Azure-Ressourcen erfasst. Zum Analysieren von Daten stehen Features wie der Metrik-Explorer zur Verfügung. Einige Features erfordern etwas Konfiguration. Dieses Szenario identifiziert die Konfigurationsschritte, die erforderlich sind, damit Sie von allen Azure Monitor-Features profitieren können. Außerdem erhalten Sie Empfehlungen dazu, welche Features Sie nutzen sollten und wie Sie basierend auf Ihren speziellen Anforderungen die richtigen Konfigurationsoptionen auswählen.

Die Aktivierung von Azure Monitor für die Überwachung aller Ihrer Azure-Ressourcen umfasst die Konfiguration von Azure Monitor-Komponenten und von Azure-Ressourcen, damit diese Überwachungsdaten generieren, die Azure Monitor dann sammeln kann. Das Ziel einer vollständigen Implementierung besteht darin, dass alle nützlichen Daten aus sämtlichen Cloudressourcen und Anwendungen gesammelt und basierend auf diesen Daten alle Azure Monitor-Features aktiviert werden.


> [!IMPORTANT]
> Wenn Sie mit Azure Monitor noch nicht vertraut sind oder nur eine einzelne Azure-Ressource überwachen möchten, sollten Sie mit dem Tutorial [Überwachen von Azure-Ressourcen mit Azure Monitor](/essentials/monitor-azure-resource.md) beginnen. Dieses Tutorial enthält allgemeine Konzepte für Azure Monitor und Anleitungen zum Überwachen einer einzelnen Azure-Ressource. Das Szenario umfasst Empfehlungen für die Vorbereitung Ihrer Umgebung, sodass Sie alle Features von Azure Monitor nutzen können, um Ihre sämtlichen Anwendungen und Ressourcen im großen Stil gemeinsam zu überwachen.

## <a name="scope-of-the-scenario"></a>Umfang des Szenarios
Ziel dieses Szenarios ist es, Sie durch die grundlegenden Schritte einer vollständigen Azure Monitor-Implementierung zu führen, um sicherzustellen, dass Sie alle Features vollständig nutzen und die Einblicke in Ihre cloud- und hybridbasierten Anwendungen und Ressourcen maximieren. Das Szenario konzentriert sich auf Konfigurationsanforderungen und Bereitstellungsoptionen, nicht auf die tatsächlichen Konfigurationsdetails. Es werden Links zu weiteren Inhalten bereitgestellt, in denen die Details der erforderlichen Konfiguration erläutert werden.

## <a name="scenario-articles"></a>Artikel zu Szenarien
In diesem Artikel wird das Szenario vorgestellt. Wenn Sie direkt zu einem bestimmten Bereich wechseln möchten, sehen Sie sich einen der anderen in der folgenden Tabelle aufgelisteten Artikel an, die Teil dieses Szenarios sind.

| Artikel | BESCHREIBUNG |
|:---|:---|
| [Planung](best-practices-plan.md)  | Hier werden die Planungsschritte erläutert, die Sie vor Beginn der Implementierung ausführen sollten. Dazu gehören Entwurfsentscheidungen und Informationen, die Sie über Ihre Organisation und Ihre Anforderungen sammeln sollten. |
| [Konfigurieren der Datensammlung](best-practices-data-collection.md) | Hier werden die Aufgaben erläutert, die ausgeführt werden müssen, um Überwachungsdaten von Azure- und hybridbasierten Anwendungen und Ressourcen zu sammeln. |
| [Analyse und Visualisierungen](best-practices-analysis.md) | Hier finden Sie Standardfeatures und zusätzliche Visualisierungen, die Sie erstellen können, um gesammelte Überwachungsdaten zu analysieren. |
| [Warnungen und automatisierte Antworten](best-practices-alerts.md) | Konfigurieren Sie Benachrichtigungen und Prozesse, die automatisch ausgelöst werden, wenn eine Warnung erstellt wird. |




## <a name="next-steps"></a>Nächste Schritte

- [Planen Ihrer Überwachungsstrategie und -konfiguration](best-practices-plan.md)