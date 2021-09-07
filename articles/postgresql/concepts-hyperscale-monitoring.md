---
title: Überwachen und Optimieren – Hyperscale (Citus) – Azure Database for PostgreSQL
description: In diesem Artikel werden die Überwachungs- und Optimierungsfunktionen in Azure Database for PostgreSQL – Hyperscale (Citus) beschrieben.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/26/2021
ms.openlocfilehash: cb88998009ab05eb91b8945a138ef935660dac35
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710764"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Überwachung und Optimierung von Azure Database for PostgreSQL – Hyperscale (Citus)

Die Überwachung der Daten zu Ihren Servern unterstützt Sie bei der Problembehandlung und der Optimierung Ihrer Workloads. Hyperscale (Citus) bietet verschiedene Überwachungsoptionen, um Einblicke in das Verhalten von Knoten in einer Servergruppe zu gewähren.

## <a name="metrics"></a>metrics

Hyperscale (Citus) stellt Metriken für Knoten in einer Servergruppe bereit und aggregiert Metriken für die Gruppe als Ganzes. Die Metriken bieten Einblicke in das Verhalten der unterstützenden Ressourcen. Jede Metrik wird mit einer Frequenz von einer Minute ausgegeben und verfügt über einen Verlauf von bis zu 30 Tagen.

Zusätzlich zum Anzeigen von Diagrammen der Metriken können Sie Warnungen konfigurieren. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Einrichten von Warnungen](howto-hyperscale-alert-on-metric.md).  Darüber hinaus können weitere Aufgaben wie das Einrichten automatisierter Aktionen, das Ausführen erweiterter Analysen und das Archivieren des Verlaufs ausgeführt werden. Weitere Informationen finden Sie unter [Überblick über Metriken in Microsoft Azure](../azure-monitor/data-platform.md).

### <a name="per-node-vs-aggregate"></a>Metriken pro Knoten und aggregierte Metriken

Standardmäßig werden im Azure-Portal knotenübergreifende Hyperscale (Citus)-Metriken in einer Servergruppe aggregiert. Einige Metriken, etwa der Prozentsatz der Datenträgernutzung, sind jedoch pro Knoten aussagekräftiger. Wenn Sie Metriken für Knoten einzeln anzeigen möchten, verwenden Sie die [Metrikteilung](../azure-monitor/essentials/metrics-charts.md#metric-splitting) von Azure Monitor nach Servername.

> [!NOTE]
>
> Einige Hyperscale (Citus)-Servergruppen unterstützen die Metrikteilung nicht. In diesen Servergruppen können Sie Metriken für einzelne Knoten anzeigen, indem Sie auf der Seite **Übersicht** der Servergruppe auf den Knotennamen klicken. Öffnen Sie anschließend die Seite **Metriken** für den Knoten.

### <a name="list-of-metrics"></a>Liste der Metriken

Diese Metriken sind für Hyperscale-Knoten (Citus) verfügbar:

|Metrik|Metrikanzeigename|Einheit|BESCHREIBUNG|
|---|---|---|---|
|active_connections|Die aktiven Verbindungen.|Anzahl|Die Anzahl aktiver Verbindungen mit dem Server|
|cpu_percent|CPU in Prozent|Percent|Die CPU-Auslastung in Prozent|
|iops|IOPS|Anzahl|Weitere Informationen finden Sie unter [IOPS-Definition](../virtual-machines/premium-storage-performance.md#iops) und [Durchsatz bei Hyperscale (Citus)](concepts-hyperscale-configuration-options.md).|
|memory_percent|Arbeitsspeicher in Prozent|Percent|Die Arbeitsspeicherauslastung in Prozent|
|network_bytes_ingress|Netzwerk eingehend|Byte|Eingehender Netzwerkdatenverkehr über aktive Verbindungen.|
|network_bytes_egress|Netzwerk ausgehend|Byte|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen.|
|storage_percent|Speicher in Prozent|Percent|Der verwendete Speicher relativ zum Maximalwert des Servers (in Prozent)|
|storage_used|Verwendeter Speicher|Byte|Die Menge des verwendeten Speichers. Der vom Dienst verwendete Speicher kann die Datenbankdateien, Transaktionsprotokolle und Serverprotokolle umfassen.|

Azure liefert keine aggregierten Metriken für den Cluster als Ganzes, aber Metriken für mehrere Knoten können im selben Diagramm dargestellt werden.

## <a name="next-steps"></a>Nächste Schritte

- Anleitungen zum Erstellen einer Warnung zu einer Metrik finden Sie unter [Einrichten von Warnungen](howto-hyperscale-alert-on-metric.md).
- Erfahren Sie, wie Sie die [Metrikteilung](../azure-monitor/essentials/metrics-charts.md#metric-splitting) anwenden, um Metriken pro Knoten in einer Servergruppe zu überprüfen.
