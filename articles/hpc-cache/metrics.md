---
title: 'Azure HPC Cache: Metriken und Überwachung'
description: Anzeigen von Statistiken zu Ihrer Azure HPC Cache-Instanz
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2021
ms.author: femila
ms.openlocfilehash: cc6846118607e2d6bc50d63035f1ce209758f55c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331279"
---
# <a name="cache-metrics-and-monitoring"></a>Metriken und Überwachung des Caches

Der Azure-Portal bietet mehrere integrierte Visualisierungen, mit denen Sie die Leistung Ihrer Azure HPC Cache-Instanz überwachen können.

In diesem Artikel wird erläutert, wo die Visualisierungen zu finden sind und was sie anzeigen.

Auf vier HPC Cache-Portalseiten werden Cacheleistungsdaten angezeigt:

* **Übersicht**
* **Metriken**
* **Cachebericht**
* **Clientstatus**

Öffnen Sie die Seiten **Metriken**, **Cachebericht** und **Clientstatus** über das Menü **Überwachung** in der Seitenleiste. Die Seite **Übersicht** des Caches ist das oberste Element im Seitenleistenmenü.

![Screenshot des Menüs „Überwachung“ auf der linken Seite der Azure-Portal-Schnittstelle für HPC Cache.](media/monitoring-menu.png)

## <a name="overview-page"></a>Seite „Übersicht“

Auf der Seite **Übersicht** des Caches werden Diagramme für einige grundlegende Cachestatistiken angezeigt: Cachedurchsatz, Vorgänge pro Sekunde und Latenz. Die Diagramme befinden sich am unteren Rand der Seite unterhalb des Abschnitts **Essentials**.

![Screenshot: drei Liniendiagramme mit den oben genannten Statistiken für einen Beispielcache](media/hpc-cache-overview-stats.png)

Diese Diagramme sind Bestandteil der integrierten Überwachungs- und Analysetools von Azure. Weitere Informationen zu diesen Funktionen finden Sie in der [Dokumentation zu Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="metrics-page"></a>Seite „Metriken“

Die HPC Cache-Seite **Metriken** ist ein weiteres Azure-Standardfeature. Befolgen Sie die Tipps auf der Seite, um eigene Diagramme zu erstellen und mehr über die Features zu erfahren.

## <a name="cache-report"></a>Cachebericht

Der **Cachebericht** ist ein benutzerdefinierter Satz von Diagrammen, die den verwendeten Speicherplatz, den freien Speicherplatz und die Cacherecyclingrate Ihres Caches anzeigen.

Standardmäßig werden auf der Seite aggregierte Daten für alle Speicherziele des Caches angezeigt. Sie können jedoch stattdessen mit dem Steuerelement **Speicherziel** die Cachenutzungsdaten für ein bestimmtes Speicherziel anzeigen. Außerdem können Sie den anzuzeigenden Zeitbereich anpassen.

![Screenshot der Seite „Cachebericht“.](media/cache-report.png)

* **Genutzter Cachespeicher** ist die Menge des Speicherplatzes in Ihrem Cachespeicher, der zum Speichern von Dateien verwendet wird, die von Clientcomputern angefordert wurden. Wenn Sie ein einzelnes Speicherziel auswählen, wird nur der für Dateien aus diesem Back-End-Speichersystem genutzte Speicherplatz angezeigt.

* **Freier Cachespeicher** ist die im Cachespeicher verbliebene verfügbare Kapazität.

* **Cacherecyclingrate** stellt die Rate dar, mit der alte Dateien aus dem Cache entfernt werden, um Platz für neu angeforderte Dateien zu schaffen. Für ein einzelnes Speicherziel umfasst diese Berechnung nur Dateien, die auf diesem Ziel gespeichert sind.

## <a name="client-status"></a>Clientstatus

Auf der Seite **Clientstatus** werden die Clientcomputer aufgeführt, die mit Ihrer Azure HPC Cache-Instanz verbunden sind.

![Screenshot der Seite „Clientstatus“.](media/client-status.png)

Sie können die Tabelle mit Filtern anpassen, um eine bestimmte Clientadresse oder einen bestimmten Adressbereich, ein bestimmtes Speicherziel oder eine einzelne Cachebereitstellungsadresse anzuzeigen.

Die in der Tabelle angezeigten Verbindungstypen haben mit den Clientverbindungen mit dem Speicherziel zu tun:

* `azure_https_backend` ist eine sichere Clientverbindung mit einem Azure Blob Storage-Standardsystem.
* `mount_backend` ist eine NFS-Verbindung mit einem NFS-Hardwaresystem oder einem Blobcontainer mit NFS-Unterstützung.
* `nfs_backend` ähnelt der `mount_backend`-Verbindung, stellt jedoch eine separate Verbindung dar, die von einem NFS-internen Remoteprozeduraufruf anstelle einer vom Clienteinbindungsvorgang initiierten Verbindung verwendet wird.

Diese Werte können beim Debuggen von Verbindungsproblemen zwischen Clients und der HPC Cache-Instanz hilfreich sein.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Azure-Metriken und Statistiktools](../azure-monitor/index.yml)
* [Verwalten des Caches](hpc-cache-manage.md)
