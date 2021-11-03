---
title: Verwenden von NFS-Blobspeicher mit Azure HPC Cache
description: Hier werden Prozeduren und Einschränkungen bei der Verwendung des ADLS-NFS-Blobspeichers mit Azure HPC Cache beschrieben.
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/12/2021
ms.author: femila
ms.openlocfilehash: 297030bc35e9fcf726201695b273eac79a044028
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051038"
---
# <a name="use-nfs-mounted-blob-storage-with-azure-hpc-cache"></a>Verwenden von in NFS eingebundenem Blobspeicher mit Azure HPC Cache

In NFS eingebundene Blobcontainer können mit Azure HPC Cache genutzt werden. Weitere Informationen zur [NFS 3.0-Protokollunterstützung in Azure Blobspeicher](../storage/blobs/network-file-system-protocol-support.md) finden Sie auf der Dokumentationswebsite zum Blobspeicher.

Azure HPC Cache verwendet NFS-fähigen Blobspeicher im ADLS-NFS-Speicherzieltyp. Diese Speicherziele haben Ähnlichkeiten mit regulären NFS-Speicherzielen, aber auch mit regulären Azure-Blobzielen.

In diesem Artikel werden Strategien und Einschränkungen erläutert, die Sie kennen sollten, wenn Sie ADLS-NFS-Speicherziele verwenden.

Lesen Sie auch die Dokumentation zu NFS-Blobs, insbesondere die folgenden Abschnitte, in denen kompatible und inkompatible Szenarios beschrieben werden:

* [Funktionsübersicht](../storage/blobs/network-file-system-protocol-support.md)
* [Leistungsaspekte](../storage/blobs/network-file-system-protocol-support-performance.md)
* [Einschränkungen und bekannte Probleme](../storage/blobs/network-file-system-protocol-known-issues.md)

## <a name="understand-consistency-requirements"></a>Grundlegendes zu Konsistenzanforderungen

HPC Cache erfordert eine starke Konsistenz für ADLS-NFS-Speicherziele. Standardmäßig aktualisiert NFS-fähiger Blobspeicher keine Dateimetadaten. Dadurch kann HPC Cache Dateiversionen nicht genau vergleichen.

Als Lösung deaktiviert Azure HPC Cache automatisch das NFS-Attributzwischenspeichern für jeden NFS-fähigen Blobcontainer, der als Speicherziel verwendet wird.

Diese Einstellung bleibt für die Lebensdauer des Containers erhalten, auch wenn Sie ihn aus dem Cache entfernen.

## <a name="pre-load-data-with-nfs-protocol"></a>Im Voraus Laden von Daten mit dem NFS-Protokoll
<!-- cross-referenced from hpc-cache-ingest.md and here -->

In einem NFS-fähigen Blobcontainer kann *eine Datei nur von dem Protokoll bearbeitet werden, das beim Erstellen verwendet wurde*. Wenn Sie also die Azure REST-API zum Auffüllen eines Containers verwenden, können Sie NFS nicht verwenden, um diese Dateien zu aktualisieren. Es können keine Dateien bearbeitet werden, die mit der Azure REST-API erstellt wurden, da Azure HPC Cache nur NFS verwendet. (Weitere Informationen finden Sie unter [Bekannte Probleme bei Blob-Storage-APIs](../storage/blobs/data-lake-storage-known-issues.md#blob-storage-apis))

Es ist kein Problem für den Cache, wenn Ihr Container leer ist, oder wenn die Dateien mithilfe von NFS erstellt wurden.

Wenn die Dateien in Ihrem Container mit der Azure Blob REST API anstelle von NFS erstellt wurden, ist Azure HPC Cache auf diese Aktionen für die Originaldateien beschränkt:

* Auflisten der Dateien in einem Verzeichnis
* Lesen der Datei (und Speichern im Cache für nachfolgende Lesezugriffe)
* Löschen der Datei
* Leeren der Datei (Kürzen auf 0)
* Eine Kopie der Datei speichern Die Kopie wird als NFS-erstellte Datei markiert und kann mithilfe von NFS bearbeitet werden.

**Azure HPC Cache kann den Inhalt einer Datei, die mit REST erstellt wurde, nicht bearbeiten.** Das bedeutet, dass im Cache eine geänderte Datei nicht von einem Client wieder auf dem Speicherziel speichern kann.

Diese Einschränkungen müssen Sie sich merken, da sie zu Datenintegritätsproblemen führen kann, wenn Sie Zwischenspeicherungsmodelle für Lese-/Schreibzugriff für Dateien verwenden, die nicht mit NFS erstellt wurden.

> [!TIP]
> Weitere Informationen zum Zwischenspeichern von Lese- und Schreibvorgängen finden Sie unter [Verstehen von Cachenutzungsmodellen](cache-usage-models.md).

### <a name="write-caching-scenarios"></a>Szenarios für das Zwischenspeichern von Schreibvorgängen

Zu diesen Cachenutzungsmodellen gehört das Zwischenspeichern von Schreibvorgängen:

* **Mehr als 15 % Schreibvorgänge**
* **Mehr als 15 % Schreibvorgänge, der Sicherungsserver wird alle 30 Sekunden auf Änderungen überprüft**
* **Mehr als 15 % Schreibvorgänge, der Sicherungsserver wird alle 60 Sekunden auf Änderungen überprüft**
* **Mehr als 15 % Schreibvorgänge, Zurückschreiben auf den Server alle 30 Sekunden**

Cachenutzungsmodelle für Schreibvorgänge sollten nur für Dateien verwendet werden, die mit NFS erstellt wurden.

Wenn Sie versuchen, das Zwischenspeichern von Schreibvorgängen für mit REST erstellte Dateien zu verwenden, können Ihre Dateiänderungen verlorengehen. Das liegt daran, dass der Cache nicht versucht, Dateiänderungen sofort im Speichercontainer zu speichern.

Wenn Sie versuchen, Schreibvorgänge in mit REST erstellten Dateien zwischenzuspeichern, gefährden Sie Ihre Daten:

1. Der Cache akzeptiert Änderungen von Clients und gibt bei jeder Änderung eine Erfolgsmeldung zurück.
1. Der Cache speichert die geänderte Datei im Speicher und wartet auf weitere Änderungen.
1. Nach einiger Zeit versucht der Cache, die geänderte Datei im Back-End-Container zu speichern. An diesem Punkt wird eine Fehlermeldung angezeigt, da versucht wird, mit NFS in eine mit REST erstellte Datei zu schreiben.

   Es ist zu spät, um den Clientcomputer darüber zu informieren, dass die Änderungen nicht akzeptiert wurden, und der Cache hat keine Möglichkeit, die ursprüngliche Datei zu aktualisieren. Die Änderungen von Clients gehen also verloren.

### <a name="read-caching-scenarios"></a>Szenarios für das Zwischenspeichern von Lesevorgängen

Szenarios für das Zwischenspeichern von Lesevorgängen sind für Dateien geeignet, die entweder mit NFS oder der Azure Blob REST-API erstellt wurden.

In diesen Nutzungsmodellen wird nur das Zwischenspeichern von Lesevorgängen verwendet:

* **Leseintensiv, unregelmäßige Schreibvorgänge**
* **Clients schreiben in das NFS-Ziel unter Umgehung des Caches.**
* **Viele Lesezugriffe, Überprüfung des Sicherungsservers alle 3 Stunden**

Sie können diese Nutzungsmodelle bei Dateien verwenden, die von der REST-API oder von NFS erstellt wurden. Alle NFS-Schreibvorgänge, die von einem Client an den Back-End-Container gesendet werden, schlagen weiterhin fehl. Der Fehlschlag erfolgt jedoch sofort und es wird eine Fehlermeldung an den Client zurückgegeben.

Ein Workflow zum Zwischenspeichern von Lesevorgängen kann weiterhin Dateiänderungen umfassen, solange diese nicht zwischengespeichert werden. Clients können beispielsweise auf Dateien aus dem Container zugreifen, müssen ihre Änderungen jedoch als neue Datei zurückschreiben oder geänderte Dateien an einem anderen Speicherort speichern.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Erkennen von Einschränkungen bei Network Lock Manager (NLM)

NFS-fähige Blobcontainer unterstützen Network Lock Manager (NLM) nicht. Dabei handelt es sich um ein häufig verwendetes NFS-Protokoll zum Schutz von Dateien vor Konflikten.

Wenn Ihr NFS-Workflow ursprünglich für Hardwarespeichersysteme geschrieben wurde, können Ihre Clientanwendungen NLM-Anforderungen enthalten. Sie können diese Einschränkung umgehen, wenn Sie Ihren Prozess in NFS-fähigen Blobspeicher verschieben. Stellen Sie dazu beim Einbinden des Caches durch Ihre Clients sicher, dass diese NLM deaktivieren.

Verwenden Sie die Option ``-o nolock`` im Befehl ``mount`` Ihrer Clients, um NLM zu deaktivieren. Diese Option verhindert, dass Clients NLM-Sperren anfordern und Fehler als Antwort erhalten. Die Option ``nolock`` ist in den verschiedenen Betriebssystemen unterschiedlich implementiert. Entsprechende Informationen finden Sie in der Dokumentation zum Clientbetriebssystem (man 5 nfs).

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>Optimieren von Schreibvorgängen in NFS-fähige Container mit HPC Cache

Azure HPC Cache kann die Leistung einer Workload verbessern, die das Schreiben von Änderungen an in ein ADLS-NFS-Speicherziel umfasst.

> [!NOTE]
> Sie müssen NFS verwenden, um Ihren ADLS-NFS-Speichercontainer aufzufüllen, wenn Sie dessen Dateien über Azure HPC Cache bearbeiten möchten.

Im [Artikel zu Überlegungen zur Leistung](../storage/blobs/network-file-system-protocol-support-performance.md) von NFS-fähigen Blobs wird unter anderem die Einschränkung beschrieben, dass ADLS-NFS-Speicher beim Überschreiben vorhandener Dateien nicht sehr effizient ist. Wenn Sie Azure HPC Cache mit NFS-eingebundenen Blobspeichern verwenden, verarbeitet der Cache zeitweilige Rewrites, wenn Clients eine aktive Datei ändern. Die Wartezeit beim Schreiben einer Datei in den Back-End-Container wird für die Clients ausgeblendet.

Beachten Sie die Einschränkungen, die oben unter [Im Voraus Laden von Daten mit dem NFS-Protokoll](#pre-load-data-with-nfs-protocol) erläutert wurden.

## <a name="next-steps"></a>Nächste Schritte

* Sie können mehr über die [Voraussetzungen für ADLS-NFS-Speicherziele](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements) lesen.
* Sie können einen [NFS-fähiges Blobspeicherkonto](../storage/blobs/network-file-system-protocol-support-how-to.md) erstellen.
