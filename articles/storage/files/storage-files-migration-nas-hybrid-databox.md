---
title: Migration eines lokalen NAS zur Azure-Dateisynchronisierung über Data Box
description: Erfahren Sie, wie Sie Dateien von einem lokalen NAS-Speicherort (Network Attached Storage) mit der Azure-Dateisynchronisierung über Azure Data Box zu einer Hybrid Cloud-Bereitstellung migrieren.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb7132e0a7b9dc59ac6b047d431acf0e740aba0a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430826"
---
# <a name="use-data-box-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-by-using-azure-file-sync"></a>Migrieren Sie mit Data Box von Network Attached Storage (NAS) zu einer Hybrid Cloud-Bereitstellung mit der Azure-Dateisynchronisierung.

Dieser Artikel zur Migration ist einer von mehreren Artikeln im Zusammenhang mit den Schlüsselwörtern NAS, der Azure-Dateisynchronisierung und Azure Data Box. Überprüfen Sie, ob dieser Artikel für Ihr Szenario zutrifft:

> [!div class="checklist"]
> * Datenquelle: Network Attached Storage (NAS)
> * Migrationsroute: NAS &rArr; Data Box &rArr; Azure-Dateifreigabe &rArr; Synchronisierung mit Windows Server
> * Lokales Zwischenspeichern von Dateien: Ja, das endgültige Ziel ist eine Azure-Dateisynchronisierung-Bereitstellung.

Wenn Ihr Szenario anders ist, sehen Sie sich die [Tabelle mit Migrationsleitfäden](storage-files-migration-overview.md#migration-guides) an.

Die Azure-Dateisynchronisierung verwendet DAS-Speicherorte (Direct Attached Storage). Die Synchronisierung mit NAS-Speicherorten (Network Attached Storage) wird nicht unterstützt.
Daher müssen Sie Ihre Dateien migrieren. Dieser Artikel führt Sie durch die Planung und Implementierung einer solchen Migration.

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |

## <a name="migration-goals"></a>Migrationsziele

Das Ziel besteht darin, Freigaben auf Ihrem NAS-Gerät auf Windows Server zu verschieben. Anschließend wird die Azure-Dateisynchronisierung für eine Hybrid Cloud-Bereitstellung verwendet. Diese Migration muss so erfolgen, dass die Integrität der Produktionsdaten und die Verfügbarkeit während der Migration gewährleistet wird. Letzteres erfordert minimale Ausfallzeiten, damit sie in normale Wartungsfenster passt oder diese nur geringfügig überschreitet.

## <a name="migration-overview"></a>Übersicht zur Migration

Der Migrationsprozess besteht aus mehreren Phasen. Folgendes ist erforderlich:
- Stellen Sie Azure-Speicherkonten und Dateifreigaben bereit.
- Stellen Sie einen lokalen Computer bereit, auf dem Windows Server ausgeführt wird. 
- Konfigurieren Sie die Azure-Dateisynchronisierung.
- Migrieren Sie die Dateien mithilfe von Robocopy.
- Führen Sie die Übernahme durch.

In den folgenden Abschnitten werden die Phasen des Migrationsprozesses ausführlich beschrieben.

> [!TIP]
> Wenn Sie zu diesem Artikel zurückkehren, können Sie mithilfe des Navigationsbereichs auf der rechten Seite des Bildschirms zu der Migrationsphase wechseln, in der Sie aufgehört haben.

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>Phase 1: Ermitteln der Anzahl der benötigten Azure-Dateifreigaben

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
  Diese Option umfasst eine robuste Data Box-Appliance auf Rädern, die ähnlich wie ein NAS funktioniert. Sie verfügt über eine Kapazität von 1 PiB. Die nutzbare Kapazität ist aufgrund des Verschlüsselungs- und Dateisystemoverheads ca. 20 Prozent geringer. Weitere Informationen finden Sie in der [Data Box Heavy-Dokumentation](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-suitable-windows-server-instance-on-premises"></a>Phase 4: Lokales Bereitstellen einer geeigneten Windows Server-Instanz

Während Sie auf das Eintreffen Ihrer Azure Data Box-Geräte warten, können Sie bereits mit der Überprüfung der Notwendigkeit einer oder mehrerer Windows Server-Instanzen beginnen, die Sie mit der Azure-Dateisynchronisierung verwenden werden.

* Erstellen Sie eine Windows Server 2019-Instanz (zumindest Windows Server 2012 R2) als virtuellen Computer oder physischen Server. Ein Windows Server-Failovercluster wird ebenfalls unterstützt.
* Stellen Sie einen direkt angeschlossenen Speicher bereit, oder fügen Sie ihn hinzu. (DAS anstatt NAS, was nicht unterstützt wird.)

Die Ressourcenkonfiguration (Compute und RAM) der bereitgestellten Windows Server-Instanz hängt größtenteils von der Anzahl der Dateien und Ordner ab, die synchronisiert werden sollen. Wenn Sie Bedenken haben, empfiehlt es sich, eine leistungsstärkere Konfiguration zu verwenden.

[Erfahren Sie, wie Sie eine Windows Server-Instanz basierend auf der Anzahl der zu synchronisierenden Elemente dimensionieren.](../file-sync/file-sync-planning.md#recommended-system-resources)

> [!NOTE]
> Der über den obigen Link aufrufbare Artikel enthält eine Tabelle mit einem Bereich für den Serverarbeitsspeicher (RAM). Sie können die Zahlen am unteren Ende des Bereichs für Ihren Server verwenden. In diesem Fall müssen jedoch davon ausgehen, dass die erste Synchronisierung erheblich länger dauert.

## <a name="phase-5-copy-files-onto-your-data-box"></a>Phase 5: Kopieren von Dateien auf Ihre Data Box

Nachdem Ihre Data Box eingetroffen ist, müssen Sie diese mit „Sichtverbindung“ zu Ihrer NAS-Appliance einrichten. Folgen Sie der Einrichtungsdokumentation für den bestellten Data Box-Typ:

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


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Phase 6: Bereitstellen der Azure-Dateisynchronisierung-Cloudressource

Warten Sie, bis alle Dateien in den richtigen Azure-Dateifreigaben angekommen sind, bevor Sie mit dieser Anleitung fortfahren. Das Versenden und das Erfassen der Data Box-Daten nimmt einige Zeit in Anspruch.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Phase 7: Bereitstellen des Azure-Dateisynchronisierung-Agents

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server-instance"></a>Phase 8: Konfigurieren der Azure-Dateisynchronisierung auf der Windows Server-Instanz

Die registrierte lokale Windows Server-Instanz muss für diesen Prozess vorbereitet und mit dem Internet verbunden sein.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Aktivieren Sie das Cloudtiering-Feature, und wählen Sie im ursprünglichen Downloadabschnitt **Nur Namespace** aus.

> [!IMPORTANT]
> Cloudtiering ist ein Feature der Azure-Dateisynchronisierung, das es dem lokalen Server ermöglicht, weniger Speicherkapazität als in der Cloud zu haben, aber trotzdem über den vollständigen Namespace zu verfügen. Lokal interessante Daten werden zudem für eine schnelle Zugriffsleistung lokal zwischengespeichert. Cloudtiering ist optional. Sie können Cloudtiering für jeden Serverendpunkt der Azure-Dateisynchronisierung einzeln festlegen. Sie müssen dieses Feature verwenden, wenn Sie nicht über genügend lokale Datenträgerkapazität in der Windows Server-Instanz verfügen, um alle Clouddaten aufzunehmen, und wenn Sie vermeiden möchten, dass alle Daten aus der Cloud heruntergeladen werden.

Wiederholen Sie für alle Azure-Dateifreigaben/Serverspeicherorte, die Sie für die Synchronisierung konfigurieren müssen, die Schritte zum Erstellen von Synchronisierungsgruppen und zum Hinzufügen der übereinstimmenden Serverordner als Serverendpunkte. Warten Sie, bis die Synchronisierung des Namespace abgeschlossen ist. Im folgenden Abschnitt wird erläutert, wie Sie sicher sein können, dass die Synchronisierung abgeschlossen wurde.

> [!NOTE]
> Nach der Erstellung eines Serverendpunkts arbeitet die Synchronisierung. Allerdings muss die Synchronisierung die Dateien und Ordner aufzählen (ermitteln), die Sie über Data Box in die Azure-Dateifreigabe verschoben haben. Abhängig von der Größe des Namespace kann es längere Zeit dauern, bis der Namespace der Cloud auf dem Server angekommen ist.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Phase 9: Warten, bis der Namespace vollständig auf dem Server angezeigt wird

Warten Sie, bis der Server den Namespace vollständig von der Cloudfreigabe heruntergeladen hat, bevor Sie mit den nächsten Schritten der Migration fortfahren. Wenn Sie zu früh beginnen, Dateien auf den Server zu verschieben, riskieren Sie unnötige Uploads und sogar Dateisynchronisierungskonflikte.

Um festzustellen, ob der Server die anfängliche Downloadsynchronisierung abgeschlossen hat, öffnen Sie die Ereignisanzeige auf der synchronisierenden Windows Server-Instanz, und verwenden Sie das Telemetrieereignisprotokoll der Azure-Dateisynchronisierung.
Das Telemetrieereignisprotokoll befindet sich in der Ereignisanzeige unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“.

Suchen Sie nach dem letzten 9102-Ereignis. Bei Abschluss einer Synchronisierungssitzung wird ein Ereignis mit der ID 9102 protokolliert. Der Ereignistext enthält ein Feld für die Downloadsynchronisierungsrichtung. (`HResult` muss null sein, und Dateien müssen heruntergeladen werden.)
 
Sie sollten zwei aufeinander folgende Ereignisse dieses Typs mit diesem Inhalt sehen, um sicher zu sein, dass der Server das Herunterladen des Namespace abgeschlossen hat. Es ist in Ordnung, wenn zwischen den beiden 9102-Ereignissen andere Ereignisse vorhanden sind.

## <a name="phase-10-run-robocopy-from-your-nas"></a>Phase 10: Ausführen von Robocopy für Ihr NAS

Nachdem der Server die anfängliche Synchronisierung des gesamten Namespace von der Cloudfreigabe abgeschlossen hat, können Sie mit diesem Schritt fortfahren. Die anfängliche Synchronisierung muss abgeschlossen sein, bevor Sie mit diesem Schritt fortfahren. Details hierzu finden Sie im vorherigen Abschnitt.

In diesem Schritt führen Sie Robocopy-Aufträge aus, um Ihre Cloudfreigaben mit den neuesten Änderungen Ihres NAS zu aktualisieren, die nach dem Kopieren Ihrer Freigaben in die Data Box aufgetreten sind.
Diese Robocopy-Ausführung kann abhängig vom Umfang der Änderungen, die auf Ihren NAS-Freigaben aufgetreten sind, nur kurze Zeit oder länger dauern.

> [!WARNING]
> Aufgrund des zurückentwickelten Robocopy-Verhaltens in Windows Server 2019 ist die Robocopy-Option `/MIR` nicht mit mehrstufigen Zielverzeichnissen kompatibel. Für diese Phase der Migration können Sie keinen Windows Server 2019- oder Windows 10-Client verwenden. Verwenden Sie Robocopy auf einer temporären Windows Server 2016-Instanz.

Die grundlegende Migrationsansatz sieht wie folgt aus:
 - Führen Sie Robocopy für Ihre NAS-Appliance aus, um Ihre Windows Server-Instanz zu synchronisieren. 
 - Verwenden Sie die Azure-Dateisynchronisierung, um die Azure-Dateifreigaben von Windows Server zu synchronisieren.

Erstellen Sie die erste lokale Kopie in Ihrem Windows Server-Zielordner:

1. Identifizieren Sie den ersten Speicherort auf Ihrer NAS-Appliance.
1. Identifizieren Sie den entsprechenden Ordner auf der Windows Server-Instanz, auf der die Azure-Dateisynchronisierung bereits konfiguriert wurde.
1. Starten Sie den Kopiervorgang mit Robocopy.

Mit dem folgenden Robocopy-Befehl werden nur die Unterschiede (aktualisierte Dateien und Ordner) aus Ihrem NAS-Speicher in den Windows Server-Zielordner kopiert. Die Windows Server-Instanz synchronisiert diese dann mit den Azure-Dateifreigaben. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Wenn Sie auf Ihrer Windows Server-Instanz weniger Speicher bereitgestellt haben, als die Daten auf der NAS-Appliance verwenden, haben Sie Cloudtiering konfiguriert. Wenn das lokale Windows Server-Volume voll wird, beginnt das [Cloudtiering](../file-sync/file-sync-cloud-tiering-overview.md) für Dateien, die bereits erfolgreich synchronisiert wurden. Durch das Cloudtiering wird ausreichend Speicherplatz generiert, um mit dem Kopieren von der NAS-Appliance fortzufahren. Beim Cloudtiering wird einmal pro Stunde überprüft, was bereits synchronisiert wurde, und Speicherplatz freigegeben, um auf dem Volume einen freien Speicherplatz von 99 Prozent zu erreichen.

Robocopy muss möglicherweise mehr Dateien verschieben, als lokal auf der Windows Server-Instanz gespeichert werden können. Sie können davon ausgehen, dass Robocopy Dateien viel schneller verschieben kann, als die Azure-Dateisynchronisierung Ihre Dateien hochladen und außerhalb Ihres lokalen Volumes ablegen kann. In diesem Fall schlägt Robocopy fehl. Es wird empfohlen, die Freigaben nacheinander abzuarbeiten, um dieses Szenario zu verhindern. Verschieben Sie beispielsweise nur Freigaben, die in den freien Speicherplatz passen, der auf der Windows Server-Instanz verfügbar ist. Oder vermeiden Sie, dass gleichzeitig Robocopy-Aufträge für alle Freigaben gestartet werden. Die gute Nachricht ist, dass die Option `/MIR` sicherstellt, dass nur Deltas verschoben werden. Nachdem ein Delta verschoben wurde, muss ein neu gestarteter Auftrag die Datei nicht erneut verschieben.

### <a name="do-the-cutover"></a>Durchführen der Übernahme

Wenn Sie den Robocopy-Befehl zum ersten Mal ausführen, greifen Ihre Benutzer und Anwendungen weiterhin auf Dateien auf dem NAS zu und ändern diese möglicherweise. Robocopy verarbeitet ein Verzeichnis und wechselt dann zum nächsten Verzeichnis. Ein NAS-Benutzer kann dann eine Datei im ersten Verzeichnis hinzufügen, ändern oder löschen, die während der aktuellen Robocopy-Ausführung nicht verarbeitet wird. Dies ist das erwartete Verhalten.

Bei der ersten Ausführung geht es darum, den Großteil der abgewanderten Daten auf Ihre Windows Server-Instanz und dann über die Azure-Dateisynchronisierung in die Cloud zu verschieben. Der erste Kopiervorgang kann einige Zeit in Anspruch nehmen, die von folgenden Faktoren abhängig ist:

* Uploadbandbreite
* Geschwindigkeit des lokalen Netzwerks und Anzahl der optimalen Übereinstimmungen von Robocopy-Threads
* Anzahl der Elemente (Dateien und Ordner), die von Robocopy und der Azure-Dateisynchronisierung verarbeitet werden müssen

Nachdem die erste Ausführung beendet wurde, führen Sie den Befehl erneut aus.

Robocopy wird schneller beendet, wenn Sie es zum zweiten Mal für eine Freigabe ausführen. Dies liegt daran, dass nur Änderungen, die seit der letzten Ausführung aufgetreten sind, übertragen werden müssen. Sie können wiederholte Aufträge für dieselbe Freigabe ausführen.

Wenn die Ausfallzeit für Sie akzeptabel ist, müssen Sie den Benutzerzugriff auf Ihre NAS-basierten Freigaben aufheben. Dazu können Sie jede beliebige Maßnahme ausführen, mit der Benutzer daran gehindert werden, die Datei- und Ordnerstruktur sowie den Inhalt zu ändern. Sie können beispielsweise Ihren DFS-Namespace auf einen Speicherort verweisen, der nicht vorhanden ist, oder die Stamm-ACLs für die Freigabe ändern.

Führen Sie Robocopy ein letztes Mal aus. Dadurch werden alle Änderungen übernommen, die zuvor ausgelassen wurden.
Wie lange dieser letzte Schritt dauert, hängt von der Geschwindigkeit des Robocopy-Scans ab. Sie können die Zeit (die der Ausfallzeit entspricht) abschätzen, indem Sie die Dauer der vorherigen Ausführung messen.

Erstellen Sie eine Freigabe für den Windows Server-Ordner, und passen Sie Ihre DFS-N-Bereitstellung ggf. so an, dass sie auf diese zeigt. Stellen Sie sicher, dass Sie die gleichen Berechtigungen auf Freigabeebene wie für Ihre NAS-SMB-Freigabe festlegen. Wenn Sie einen unternehmensweiten NAS hatten, der in eine Domäne eingebunden war, stimmen die Benutzer-SIDs automatisch überein, da die Benutzer in Active Directory vorhanden sind und Robocopy Dateien und Metadaten originaltreu kopiert. Wenn lokale NAS-Benutzer verwendet wurden, müssen Sie folgende Schritte durchführen: 
- Erstellen Sie diese Benutzer erneut als lokale Windows Server-Benutzer. 
- Ordnen Sie die vorhandenen SIDs, die Robocopy auf Ihre Windows Server-Instanz verschoben hat, den SIDs der neuen lokalen Windows Server-Benutzer zu.

Sie haben die Migration einer Freigabe oder einer Gruppe von Freigaben zu einem gemeinsamen Stamm oder Volume (je nach Zuordnung aus Phase 1) abgeschlossen.

Sie können einige dieser Kopiervorgänge parallel ausführen. Es wird empfohlen, jeweils eine Azure-Dateifreigabe auf einmal zu verarbeiten.

## <a name="deprecated-option-offline-data-transfer"></a>Veraltete Option: „Offlinedatenübertragung“

Vor der Veröffentlichung der Agent-Version 13 der Azure-Dateisynchronisierung wurde Data Box über einen Prozess namens „Offlinedatenübertragung“ integriert. Dieser Prozess gilt als veraltet. Mit Agent-Version 13 wurde er durch die in diesem Artikel beschriebenen, wesentlich einfacheren und schnelleren Schritte ersetzt. Wenn Sie unbedingt die veraltete Funktion der „Offlinedatenübertragung“ verwenden möchten, ist dies trotzdem möglich. Sie ist weiterhin über ein spezielles, älteres [AFS-PowerShell-Modul](https://www.powershellgallery.com/packages/Az.StorageSync/1.4.0) verfügbar:

```powershell
Install-Module Az.StorageSync -RequiredVersion 1.4.0
Import-module Az.StorageSync -RequiredVersion 1.4.0
# Verify the specific version is loaded:
Get-module Az.StorageSync
```
Sie können dann mit dem gleichen PowerShell-Modul einen Serverendpunkt erstellen und im Prozess eine Stagingfreigabe angeben.
Wenn eine laufende Migration den Prozess zur Offlinedatenübertragung nutzt, wird sie wie geplant fortgesetzt. Sie müssen diese Einstellung nach Abschluss der Migration weiterhin deaktivieren.
Die Möglichkeit, neue Migrationsvorgänge mit diesem veralteten Prozess zu starten, wird mit einem zukünftigen Agent-Release entfernt.

## <a name="troubleshooting"></a>Problembehandlung

Das häufigste Problem besteht darin, dass der Robocopy-Befehl mit der Fehlermeldung, dass das Volume auf der Windows Server-Seite voll ist, beendet wird. Das Cloudtiering erfolgt einmal stündlich, um Inhalte vom lokalen Windows Server-Datenträger abzurufen, die bereits synchronisiert wurden. Das Ziel besteht darin, 99 Prozent freien Speicherplatz auf dem Volume zu erreichen.

Warten Sie, bis durch den Synchronisierungsvorgang und das Cloudtiering Speicherplatz freigegeben wurde. Sie können dies im Datei-Explorer auf Ihrer Windows Server-Instanz beobachten.

Wenn auf Ihrer Windows Server-Instanz ausreichende Kapazität verfügbar ist, kann das Problem durch erneutes Ausführen des Befehls behoben werden. Diese Situation ist völlig unproblematisch. Sie können bedenkenlos weiter machen. Der zusätzliche Aufwand durch das erneute Ausführen des Befehls ist die einzige Folge.

Informationen zur Behebung von Problemen bei der Azure-Dateisynchronisierung finden Sie in den im nächsten Abschnitt aufgelisteten Artikeln.

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich weiter mit Azure-Dateifreigaben und der Azure-Dateisynchronisierung vertraut. In den folgenden Artikeln werden erweiterte Optionen und bewährte Methoden behandelt. Sie bieten auch Hilfestellung bei der Problembehandlung. Diese Artikel enthalten gegebenenfalls Links zur [Dokumentation zu Azure-Dateifreigaben](storage-files-introduction.md).

* [Migration overview (Übersicht über die Migration)](storage-files-migration-overview.md)
* [Planung für die Bereitstellung der Azure-Dateisynchronisierung](../file-sync/file-sync-planning.md)
* [Erstellen einer Dateifreigabe](storage-how-to-create-file-share.md)
* [Problembehandlung für die Azure-Dateisynchronisierung](../file-sync/file-sync-troubleshoot.md)