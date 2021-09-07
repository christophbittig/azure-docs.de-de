---
title: Überwachen von virtuellen Azure-Netzwerken
description: Beginnen Sie hier, um zu erfahren, wie Sie virtuelle Azure-Netzwerke überwachen.
services: virtual-network
author: duongau
ms.author: duau
ms.service: virtual-network
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: bec4d18ebc762ecf573281341e907db4376f26b2
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297635"
---
# <a name="monitoring-azure-virtual-network"></a>Überwachen von virtuellen Azure-Netzwerken

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. 

Dieser Artikel beschreibt die von einem virtuellen Azure-Netzwerk generierten Überwachungsdaten. Ein virtuelles Azure-Netzwerk verwendet [Azure Monitor](../azure-monitor/overview.md). Wenn Sie nicht mit den Features von Azure Monitor vertraut sind, die Bestandteil aller Azure-Dienste sind, für die Azure Monitor verwendet wird, lesen Sie den Artikel [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="monitoring-data"></a>Überwachungsdaten 

Virtuelle Azure-Netzwerke erfassen dieselben Arten von Überwachungsdaten wie andere Azure-Ressourcen, wie unter [Überwachungsdaten von Azure-Ressourcen](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) beschrieben. 

Ausführliche Informationen zu den Metriken und Protokollen, die von einem virtuellen Azure-Netzwerk erstellt werden, finden Sie in der [Referenz zu den Überwachungsdaten für virtuelle Azure-Netzwerke](monitor-virtual-network-reference.md).

## <a name="collection-and-routing"></a>Sammlung und Routing

Plattformmetriken und das Aktivitätsprotokoll werden automatisch erfasst und gespeichert, können jedoch mithilfe einer Diagnoseeinstellung an andere Speicherorte weitergeleitet werden.  

Ressourcenprotokolle werden erst erfasst und gespeichert, sobald Sie eine Diagnoseeinstellung erstellt und an einen oder mehrere Standorte weitergeleitet haben.

Ausführliche Informationen zum Erstellen einer Diagnoseeinstellung über das Azure-Portal, die Befehlszeilenschnittstelle oder PowerShell finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Plattformprotokollen und Metriken in Azure](../azure-monitor/essentials/diagnostic-settings.md). Wenn Sie eine Diagnoseeinstellung erstellen, legen Sie fest, welche Kategorien von Protokollen gesammelt werden sollen. Die Kategorien für *virtuelle Azure-Netzwerke* werden in der [Referenz zu den Überwachungsdaten für virtuelle Azure-Netzwerke](monitor-virtual-network-reference.md#resource-logs) aufgeführt.

> [!IMPORTANT]
> Ein Aktivieren dieser Einstellungen erfordert zusätzliche Azure-Dienste (Speicherkonto, Event Hub oder Log Analytics). Dadurch können sich Ihre Kosten erhöhen. Um geschätzte Kosten zu berechnen, wechseln Sie zum [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator).

In den folgenden Abschnitten werden die Metriken und Protokolle behandelt, die Sie erfassen können.

## <a name="analyzing-metrics"></a>Analysieren von Metriken

Azure Monitor unterstützt derzeit keine Analyse der Metriken für *virtuelle Azure-Netzwerke* über den Metrik-Explorer. Um die Metriken für ein *virtuelles Azure-Netzwerk* anzuzeigen, wählen für das zu analysierende virtuelle Netzwerk unter **Überwachung** die Option **Metriken** aus.

:::image type="content" source="./media/monitor-virtual-network/metrics.png" alt-text="Screenshot: Metrikdashboard für ein virtuelles Netzwerk" lightbox="./media/monitor-virtual-network/metrics-expanded.png":::

Eine Liste der für virtuelle Azure-Netzwerke erfassten Plattformmetriken finden Sie in der [Referenz zu den Überwachungsdaten für virtuelle Azure-Netzwerke](monitor-virtual-network-reference.md#metrics).

Sie können zur Referenz auf eine Liste [aller in Azure Monitor unterstützter Ressourcenmetriken](../azure-monitor/essentials/metrics-supported.md) anzeigen.

## <a name="analyzing-logs"></a>Analysieren von Protokollen

Ein virtuelles Azure-Netzwerk unterstützt keine Ressourcenprotokolle.

Eine Liste der Ressourcenprotokolltypen, die für Ressourcen in einem virtuellen Netzwerk erfasst werden, finden Sie in der [Referenz zu den Überwachungsdaten für virtuelle Netzwerke](monitor-virtual-network-reference.md#resource-logs).   

Das [Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) ist ein Plattformprotokolltyp in Azure, das Erkenntnisse zu Ereignissen auf Abonnementebene liefert. Sie können es unabhängig anzeigen oder an Azure Monitor-Protokolle weiterleiten, in denen Sie mithilfe von Log Analytics viel komplexere Abfragen durchführen können.  

## <a name="alerts"></a>Alerts

Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen Ihnen, Probleme in Ihrem System zu identifizieren und zu beheben, bevor Ihre Kunden sie bemerken. Sie können Warnungen für [Metriken](../azure-monitor/alerts/alerts-metric-overview.md), [Protokolle](../azure-monitor/alerts/alerts-unified-log.md) und das [Aktivitätsprotokoll](../azure-monitor/alerts/activity-log-alerts.md) festlegen. Verschiedene Arten von Warnungen haben Vor- und Nachteile.

In der folgenden Tabelle werden gängige und empfohlene Warnungsregeln zu Aktivitäten für ein virtuelles Azure-Netzwerk aufgeführt.

| Warnungstyp | Bedingung | BESCHREIBUNG  |
|:---|:---|:---|
| Erstellen oder Aktualisieren von Virtual Network | Ereignisebene: alle ausgewählt, Status: alle ausgewählt, Ereignis initiiert von: alle Dienste und Benutzer | Wenn ein Benutzer Konfigurationsänderungen am virtuellen Netzwerk erstellt oder vornimmt. |
| Löschen von Virtual Network | Ereignisebene: alle ausgewählt, Status: „Gestartet“ | Wenn ein Benutzer ein virtuelles Netzwerk löscht. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Referenz zu den Metriken, Protokollen und weiteren wichtigen Werten, die von einem virtuellen Azure-Netzwerk erstellt werden, finden Sie in der [Referenz zu den Überwachungsdaten für virtuelle Netzwerke](monitor-virtual-network-reference.md).
* Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/overview.md).