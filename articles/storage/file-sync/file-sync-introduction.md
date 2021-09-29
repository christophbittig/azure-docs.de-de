---
title: Einführung in die Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Übersicht über die Azure-Dateisynchronisierung – einen Dienst zum Erstellen und Verwenden von Netzwerkdateifreigaben in der Cloud mit dem SMP-Protokoll nach Branchenstandard.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e20b911b16ee7ebfe1af4984c9fe701a54b855cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664596"
---
# <a name="what-is-azure-file-sync"></a>Was ist die Azure-Dateisynchronisierung?

Die Azure-Dateisynchronisierung ermöglicht die Zentralisierung der Dateifreigaben Ihrer Organisation in Azure Files, ohne auf die Flexibilität, Leistung und Kompatibilität eines Windows-Dateiservers verzichten zu müssen. Einige Benutzer möchten zwar möglicherweise eine vollständige Kopie ihrer Daten lokal aufbewahren, die Azure-Dateisynchronisierung bietet allerdings zusätzlich die Möglichkeit, Windows Server als schnellen Cache für Ihre Azure-Dateifreigabe zu nutzen. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, NFS und FTPS. Sie können weltweit so viele Caches wie nötig nutzen.

## <a name="videos"></a>Videos

| Einführung in die Azure-Dateisynchronisierung | Azure Files mit Sync (Ignite 2019)  |
|-|-|
| [![Standbild aus dem Einführungsvideo für die Azure-Dateisynchronisierung. Klicken Sie darauf, um die Wiedergabe zu starten.](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Standbild der Präsentation zu Azure Files mit Sync. Klicken Sie darauf, um die Wiedergabe zu starten.](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Vorteile der Azure-Dateisynchronisierung

### <a name="cloud-tiering"></a>Cloudtiering

Wenn Cloudtiering aktiviert ist, werden Dateien, auf die besonders häufig zugegriffen wird, auf Ihrem lokalen Server zwischengespeichert, und Dateien, auf die nur selten zugegriffen wird, in die Cloud ausgelagert. Sie können steuern, wie viel lokaler Speicherplatz für die Zwischenspeicherung genutzt werden soll. Mehrstufige Dateien können bei Bedarf schnell und nahtlos abgerufen werden. Gleichzeitig können Sie die Kosten senken, da nur ein Bruchteil Ihrer Daten lokal gespeichert werden muss. Weitere Informationen zum Cloudtiering finden Sie in der [Übersicht über Cloudtiering](file-sync-cloud-tiering-overview.md).

### <a name="multi-site-access-and-sync"></a>Zugriff und Synchronisierung mit mehreren Standorten

Die Azure-Dateisynchronisierung eignet sich perfekt für Szenarien mit verteiltem Zugriff. Im Rahmen Ihrer Azure-Dateisynchronisierungsbereitstellung können Sie für jede Ihrer Niederlassungen einen lokalen Windows-Server bereitstellen. Änderungen, die an einem Server in einer der Niederlassungen vorgenommen werden, werden automatisch mit den Servern in allen anderen Niederlassungen synchronisiert.

### <a name="business-continuity-and-disaster-recovery"></a>Business Continuity & Disaster Recovery

Die Azure-Dateisynchronisierung basiert auf dem Azure Files-Dienst, der mehrere Redundanzoptionen für hochverfügbaren Speicher bietet. Da Azure über resiliente Kopien Ihrer Daten verfügt, wird Ihr lokaler Server zu einem austauschbaren Cachegerät, und im Falle eines Serverausfalls kann Ihrer Azure-Dateisynchronisierungsbereitstellung zum Ausgleich ein neuer Server hinzugefügt werden. So können Sie einen weiteren Windows-Server bereitstellen, den Azure-Dateisynchronisierungs-Agent darauf installieren und den Server dann Ihrer Azure-Dateisynchronisierungsbereitstellung hinzufügen, anstatt eine lokale Sicherung wiederherzustellen. Von der Azure-Dateisynchronisierung wird vor dem Herunterladen von Daten zunächst Ihr Dateinamespace heruntergeladen, damit Ihr Server schnellstmöglich betriebsbereit sein kann. Für eine noch schnellere Wiederherstellung können Sie im Rahmen Ihrer Bereitstellung einen betriebsbereiten Standbyserver vorhalten oder die Azure-Dateisynchronisierung mit Windows-Clustering verwenden.

## <a name="cloud-side-backup"></a>Cloudseitige Sicherung

Erstellen Sie mithilfe von Azure Backup zentrale Sicherungen in der Cloud, um Ihre Ausgaben für lokale Sicherungen zu senken. SMB-Freigaben von Azure Files verfügen über native Momentaufnahmefunktionen, und der Prozess kann mithilfe von Azure Backup automatisiert werden, um Ihre Sicherungen zu planen und deren Aufbewahrung zu verwalten. Dank Azure Backup-Integration für Ihre lokalen Server werden diese Änderungen bei der cloudbasierten Wiederherstellung außerdem automatisch auf Ihre Windows-Server heruntergeladen.

## <a name="next-steps"></a>Nächste Schritte

- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](file-sync-planning.md)
- [Übersicht über Cloudtiering](file-sync-cloud-tiering-overview.md)
- [Überwachen der Azure-Dateisynchronisierung](file-sync-monitoring.md)
