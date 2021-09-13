---
title: Migrieren von Daten zur Azure-Dateisynchronisierung mit Azure Data Box
description: Hier erfahren Sie, wie Sie mit der Azure-Dateisynchronisierung kompatible Massendaten offline migrieren, Dateikonflikte vermeiden und Ihre Dateifreigabe mit den aktuellen Änderungen auf dem Server auf den neuesten Stand bringen, um eine Cloudmigration ohne Downtime durchzuführen.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 5b888ca7655f4de267ed89229559e4b0b1785f6c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799142"
---
# <a name="migrate-data-offline-to-azure-file-sync-with-azure-data-box"></a>Durchführen einer Offlinemigration von Daten zur Azure-Dateisynchronisierung mit Azure Data Box

Dieser Migrationsartikel ist einer von mehreren Artikeln im Zusammenhang mit den Schlüsselwörtern Azure-Dateisynchronisierung und Azure Data Box. Überprüfen Sie, ob dieser Artikel für Ihr Szenario zutrifft:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept.png" alt-text="Darstellung von drei sequenziellen Schritten, die in diesem Migrationshandbuch beschrieben werden. Eine ausführliche Beschreibung der Schritte befindet sich in der Spalte neben dem Bild." lightbox="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept-expanded.png":::
    :::column-end:::
    :::column:::
        > [!div class="checklist"]
        > * Datenquelle: Windows Server 2012 R2 (oder neuer) mit installierter Azure-Dateisynchronisierung, die auf den ursprünglichen Satz von Dateien verweist.
        > * Migrationsroute: Windows Server 2012 R2 (oder neuer) &rArr; Data Box &rArr;Azure-Dateifreigabe &rArr; Synchronisierung mit dem ursprünglichen Dateispeicherort von Windows Server
        > * Lokales Zwischenspeichern von Dateien: Ja, das letztendliche Ziel ist eine Bereitstellung der Azure-Dateisynchronisierung, bei der die Dateien von dem Ort aus synchronisiert werden, an dem sie sich jetzt befinden. 
        :::column-end:::
:::row-end:::

Die Verwendung von Azure Data Box ist ein geeigneter Pfad, um den Großteil der Daten von Ihrem lokalen Server unter Windows Server in verschiedene Azure-Dateifreigaben zu verschieben und anschließend optional die Azure-Dateisynchronisierung auf dem ursprünglichen Quellserver hinzuzufügen.

Ihnen stehen verschiedene Migrationspfade zur Verfügung, und es ist wichtig, den richtigen Pfad zu verwenden:

* Ihre Daten befinden sich auf einem Server unter Windows Server 2012 R2 (oder neuer), und Sie planen, die Azure-Dateisynchronisierung auf diesem Server zu installieren und den ursprünglichen Speicherort zu synchronisieren. In diesem Szenario möchten Sie nicht alle Dateien hochladen, sondern stattdessen Data Box nutzen und dann die Dateisynchronisierung für laufende Änderungen verwenden. Wenn dies Ihr Szenario ist, finden Sie in diesem Artikel den passenden Migrationspfad.
* Sie verfügen über Daten in einer Quelle, für die Sie die Azure-Dateisynchronisierung *nicht installieren möchten* oder können. Beispiele wären etwa ein NAS (Network Attached Storage) oder ein anderer Server. Sie möchten lieber einen neuen leeren Server erstellen und die Azure-Dateisynchronisierung auf diesem Server verwenden. In diesem Fall ist dies nicht der richtige Migrationsleitfaden für Sie. Sehen Sie sich stattdessen [Migrieren Sie mit Data Box von Network Attached Storage (NAS) zu einer Hybrid Cloud-Bereitstellung mit der Azure-Dateisynchronisierung.](../files/storage-files-migration-nas-hybrid-databox.md) an, oder suchen Sie auf der [Seite mit der Migrationsübersicht](../files/storage-files-migration-overview.md) nach dem besten Leitfaden für Ihr Szenario.
* Sehen Sie sich für alle anderen Szenarien die [Tabelle mit den Migrationsleitfäden für Azure-Dateifreigaben](../files/storage-files-migration-overview.md) an. Diese Übersichtsseite ist ein guter Ausgangspunkt für alle Migrationsszenarien.
 
## <a name="migration-overview"></a>Übersicht zur Migration

Der Migrationsprozess besteht aus mehreren Phasen. Folgendes ist erforderlich:
- Stellen Sie Speicherkonten und Dateifreigaben bereit.
- Stellen Sie mindestens ein Azure Data Box-Gerät bereit, um die Daten von Ihrem Server unter Windows Server 2012 R2 (oder neuer) zu verschieben. 
- Konfigurieren Sie die Azure-Dateisynchronisierung mit autoritativem Upload.

In den folgenden Abschnitten werden die Phasen des Migrationsprozesses ausführlich beschrieben.

> [!TIP]
> Wenn Sie zu diesem Artikel zurückkehren, können Sie mithilfe des Navigationsbereichs auf der rechten Seite des Bildschirms zu der Migrationsphase wechseln, in der Sie aufgehört haben.

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>Phase 1: Ermitteln der Anzahl der benötigten Azure-Dateifreigaben

Bei diesem Migrationsleitfaden muss weiterhin der lokale direkt angeschlossene Speicher (Direct Attached Storage, DAS) verwendet werden, der Ihre Dateien enthält. Data Box bezieht Daten von diesem Speicherort, und auch die Azure-Dateisynchronisierung wird dort eingerichtet. NAS (Network Attached Storage) kann bei diesem Migrationspfad nicht verwendet werden.

Sie steuern, welche Elemente synchronisiert werden sollen. Hierzu richten Sie *Synchronisierungsgruppen* der Azure-Dateisynchronisierung ein, die jeweils bestimmen, wo eine Gruppe von Dateien synchronisiert wird. Jede Synchronisierungsgruppe verfügt über mindestens einen Serverspeicherort (*Serverendpunkt* genannt) und über eine Azure-Dateifreigabe (*Cloudendpunkt* genannt). 

Sie können Unterpfade einer Gruppe von Dateien jeweils mit der entsprechenden Azure-Dateifreigabe synchronisieren. Das bedeutet, dass zur vollständigen Abdeckung einer Gruppe von Dateien mehrere Synchronisierungsgruppen eingerichtet werden. Im restlichen Teil des Abschnitts werden Ihre Optionen beschrieben. Wenn Sie Ihre Daten neu strukturieren möchten, müssen Sie diesen Schritt ausführen, bevor Sie mit diesem Leitfaden fortfahren, eine Data Box bestellen oder die Synchronisierung einrichten. 

> [!CAUTION]
> Es ist wichtig, dass sich Ihre Datei- und Ordnerstruktur bei Beginn der Migration in dem Zustand befindet, den sie langfristig haben soll. Vermeiden Sie unnötige Neustrukturierungen von Ordnern während der Migration. Diese verringern die Vorteile der Verwendung von Azure Data Box für den ersten Massentransport von Dateien zu Azure.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Phase 2: Bereitstellen von Azure Storage-Ressourcen

Sehen Sie sich in dieser Phase die Zuordnungstabelle aus Phase 1 an, und verwenden Sie diese, um die richtige Anzahl von Azure-Speicherkonten und Dateifreigaben darin bereitzustellen.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-data-box-appliances-you-need"></a>Phase 3: Bestimmen der Anzahl der benötigten Azure Data Box-Appliances

Beginnen Sie diesen Schritt erst, nachdem Sie die vorherige Phase abgeschlossen haben. Ihre Azure Storage-Ressourcen (Speicherkonten und Dateifreigaben) sollten zu diesem Zeitpunkt erstellt werden. Wenn Sie Ihre Data Box bestellen, müssen Sie die Speicherkonten angeben, in die Data Box Daten verschiebt.

In dieser Phase müssen Sie die Ergebnisse des Migrationsplans aus der vorherigen Phase den Einschränkungen der verfügbaren Data Box-Optionen zuordnen. Diese Überlegungen helfen Ihnen bei der Planung, welche Data Box-Optionen Sie auswählen sollten und wie viele sie davon benötigen, um Ihre NAS-Freigaben in Azure-Dateifreigaben zu verschieben.

Beachten Sie die folgenden wichtigen Einschränkungen, um die Anzahl der benötigten Geräte und deren Typ zu ermitteln:

* Eine Azure Data Box-Appliance kann Daten in bis zu zehn Speicherkonten verschieben. 
* Jede Data Box-Option verfügt über eine eigene nutzbare Kapazität. Weitere Informationen finden Sie unter [Data Box-Optionen](#data-box-options).

Ermitteln Sie anhand Ihres Migrationsplans die Anzahl von Speicherkonten, die erstellt werden sollen, und die Freigaben im jeweiligen Speicherkonto. Sehen Sie sich dann die Größe der einzelnen Freigaben in Ihrem NAS an. Die Kombination dieser Informationen erlaubt Ihnen, zu optimieren und zu entscheiden, welche Appliance Daten an welche Speicherkonten senden soll. Zwei Data Box-Geräte können Dateien in das gleiche Speicherkonto verschieben. Verteilen Sie den Inhalt einer einzelnen Dateifreigabe aber nicht auf zwei Data Box-Geräte.

### <a name="data-box-options"></a>Data Box-Optionen

Wählen Sie für eine Standardmigration eine einzelne oder eine Kombination der folgenden Data Box-Optionen aus: 

* **Data Box Disk**:
  Microsoft sendet Ihnen bis zu fünf SSD-Datenträger mit einer Kapazität von jeweils 8 TiB (maximal 40 TiB). Die nutzbare Kapazität ist aufgrund des Verschlüsselungs- und Dateisystemoverheads ca. 20 Prozent geringer. Weitere Informationen finden Sie in der [Dokumentation zu Data Box Disk](../../databox/data-box-disk-overview.md).
* **Data Box**:
  Diese Option wird am häufigsten verwendet. Microsoft sendet Ihnen eine robuste Data Box-Appliance, die ähnlich wie ein NAS funktioniert. Sie verfügt über eine verwendbare Kapazität von 80 TiB. Weitere Informationen finden Sie in der [Dokumentation zu Data Box](../../databox/data-box-overview.md).
* **Data Box Heavy**:
  Diese Option umfasst eine robuste Data Box-Appliance auf Rädern, die ähnlich wie ein NAS funktioniert. Sie verfügt über eine Kapazität von 1 PiB. Die nutzbare Kapazität ist aufgrund des Verschlüsselungs- und Dateisystemoverheads ca. 20 Prozent geringer. Weitere Informationen finden Sie in der [Dokumentation zu Data Box Heavy](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-copy-files-onto-your-data-box"></a>Phase 4: Kopieren von Dateien auf Ihre Data Box

Nachdem Ihre Data Box eingetroffen ist, müssen Sie sie mit „Sichtverbindung“ zu Ihrer NAS-Appliance einrichten. Folgen Sie der Einrichtungsdokumentation für den bestellten Data Box-Typ:

* [Einrichten von Data Box](../../databox/data-box-quickstart-portal.md)
* [Einrichten von Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)
* [Einrichten von Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

Je nach Data Box-Typ sind möglicherweise Data Box-Kopiertools verfügbar. An diesem Punkt wird davon abgeraten, diese für Migrationen zu Azure-Dateifreigaben zu verwenden, da sie Ihre Dateien nicht vollständig originaltreu auf die Data Box kopieren. Verwenden Sie stattdessen Robocopy.

Wenn Ihre Data Box eintrifft, verfügt sie bereits über konfigurierte SMB-Freigaben für jedes Speicherkonto, das Sie bei der Bestellung angegeben haben.

* Wenn Ihre Dateien in einer Azure-Premium-Dateifreigabe gespeichert werden, steht eine SMB-Freigabe pro Premium-File Storage-Speicherkonto zur Verfügung.
* Wenn Ihre Dateien in einem Standardspeicherkonto gespeichert werden, stehen drei SMB-Freigaben pro Standardspeicherkonto (GPv1 und GPv2) bereit. Nur die Dateifreigaben, die mit `_AzFiles` enden, sind für Ihre Migration relevant. Ignorieren Sie alle Block- und Seitenblobfreigaben.

Führen Sie die Schritte in der Dokumentation zu Azure Data Box aus:

1. [Stellen Sie eine Verbindung mit der Data Box her.](../../databox/data-box-deploy-copy-data.md)
1. Kopieren Sie Daten auf die Data Box.
1. [Bereiten Sie Ihre Data Box für den Upload in Azure vor.](../../databox/data-box-deploy-picked-up.md)

In der verlinkten Dokumentation zu Data Box ist ein Robocopy-Befehl angegeben. Dieser Befehl eignet sich nicht für die vollständig orignaltreue Beibehaltung von Dateien und Ordnern. Verwenden Sie stattdessen diesen Befehl:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-5-deploy-the-azure-file-sync-cloud-resource"></a>Phase 5: Bereitstellen der Cloudressource für die Azure-Dateisynchronisierung

Warten Sie, bis alle Dateien in den richtigen Azure-Dateifreigaben angekommen sind, bevor Sie mit diesem Leitfaden fortfahren. Das Versenden und Erfassen der Data Box-Daten nimmt einige Zeit in Anspruch.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-6-deploy-the-azure-file-sync-agent"></a>Phase 6: Bereitstellen des Azure-Dateisynchronisierungs-Agents

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-7-configure-azure-file-sync-on-the-existing-windows-server"></a>Phase 7: Konfigurieren der Azure-Dateisynchronisierung für die vorhandene Windows Server-Instanz

Die registrierte lokale Windows Server-Instanz muss für diesen Prozess vorbereitet und mit dem Internet verbunden sein.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

:::row:::
    :::column:::
        [![Azure-Portalabschnitt des Assistenten zum Erstellen von Serverendpunkten. Ein Kontrollkästchen ist hervorgehoben, das dem Szenario des Seedings der Azure-Dateifreigabe mit Daten entspricht. Aktivieren Sie dieses Kontrollkästchen, wenn Sie die Azure-Dateisynchronisierung mit dem gleichen lokalen Speicherort verbinden möchten, von dem aus Sie zuvor Daten auf die Data Box kopiert haben.](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox.png)](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Aktivieren Sie im Assistenten **Serverendpunkt erstellen** das Kontrollkästchen unterhalb des Ordnerpfads. Führen Sie diesen Schritt nur aus, wenn Sie einen Pfad eingegeben haben, der auf die gleiche Datei- und Ordnerstruktur verweist, die auch in der Azure-Dateifreigabe vorhanden ist (in die Data Box die Dateien und Ordner für diesen Namespace verschoben hat). </br> </br> Im Falle eines Konflikts bei der Ordnerhierarchie kommt es zu Abweichungen, die nicht automatisch aufgelöst werden können. Vermeiden Sie Konflikte, da Sie sonst nicht von den Investitionen in den Data Box-Prozess profitieren. Alle Daten in der Azure-Dateifreigabe werden gelöscht. Alle Daten müssen vom lokalen Server hochgeladen werden. Die Verzeichnisstrukturen müssen übereinstimmen, um von einer Massenmigration mit Azure Data Box und einer nahtlosen Aktualisierung der Cloudfreigabe mit den neuesten Änderungen vom Server zu profitieren.
:::column-end:::
:::row-end:::

> [!NOTE]
> Wenn Sie dieses Kontrollkästchen aktivieren, wird der Modus für die erste Synchronisierung auf *Überschreiben Sie autorisierend Dateien und Ordner in der Azure-Dateifreigabe mit Inhalt im Pfad dieses Servers.* festgelegt. Diese Option ist nur für den ersten Serverendpunkt in einer Synchronisierungsgruppe verfügbar.

Nachdem Sie den autoritativen Upload für diesen neuen Serverendpunkt konfiguriert haben, können Sie optional Cloudtiering aktivieren.

Cloudtiering ist ein Feature der Azure-Dateisynchronisierung, das es dem lokalen Server ermöglicht, weniger Speicherkapazität als in der Cloud zu haben, aber trotzdem über den vollständigen Namespace zu verfügen. Lokal interessante Daten werden zudem für eine schnelle Zugriffsleistung lokal zwischengespeichert. Cloudtiering ist optional. Sie können Cloudtiering für jeden Serverendpunkt der Azure-Dateisynchronisierung einzeln festlegen. Verwenden Sie dieses Feature, um einen festen lokalen Speicherbedarf zu erzielen, Benutzern aber dennoch einen lokalen Leistungscache zur Verfügung zu stellen und kältere Daten in der Cloud zu speichern.

Weitere Informationen finden Sie in der [Übersicht über Cloudtiering](../file-sync/file-sync-cloud-tiering-overview.md). Darüber hinaus können sich mit den verschiedenen [Cloudtieringrichtlinien](../file-sync/file-sync-cloud-tiering-policy.md) vertraut machen, die Sie verwenden können, um das Caching/Tiering auf dem lokalen Server zu optimieren.

## <a name="complete-your-migration"></a>Abschließen Ihrer Migration

Nach der Erstellung eines Serverendpunkts funktioniert die Synchronisierung. Allerdings muss die Synchronisierung die Dateien und Ordner aufzählen (entdecken), die Sie über Azure Data Box in die Azure-Dateifreigabe verschoben haben. Je nach Größe des Namespace kann es lange dauern, bis die neuesten Serveränderungen mit der Cloud synchronisiert wurden. Ihre Benutzer werden dadurch nicht beeinträchtigt und können weiterhin mit den Daten auf dem Server arbeiten. Diese Strategie ermöglicht eine Cloudmigration ohne Downtime.

Wiederholen Sie die Schritte zum Erstellen von Synchronisierungsgruppen und zum Hinzufügen der übereinstimmenden Serverordner als Serverendpunkte für alle Azure-Dateifreigaben/Serverspeicherorte, die Sie für die Synchronisierung konfigurieren müssen. Sie haben Azure Data Box verwendet, um Ihre Dateien in mehrere Azure-Dateifreigaben zu verschieben. Die Migration ist abgeschlossen, nachdem Sie alle Serverendpunkte erstellt haben, die Ihre lokalen Daten mit diesen Azure-Dateifreigaben verbinden.

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich weiter mit Azure-Dateifreigaben und der Azure-Dateisynchronisierung vertraut. In den folgenden Artikeln werden erweiterte Optionen und bewährte Methoden behandelt. Sie bieten auch Hilfestellung bei der Problembehandlung. Diese Artikel enthalten gegebenenfalls Links zur [Dokumentation zu Azure-Dateifreigaben](storage-files-introduction.md).

* [Migration overview (Übersicht über die Migration)](storage-files-migration-overview.md)
* [Planung für die Bereitstellung der Azure-Dateisynchronisierung](../file-sync/file-sync-planning.md)
* [Erstellen einer Dateifreigabe](storage-how-to-create-file-share.md)
* [Problembehandlung für die Azure-Dateisynchronisierung](../file-sync/file-sync-troubleshoot.md)
