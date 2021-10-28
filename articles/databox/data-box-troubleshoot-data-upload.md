---
title: Überprüfen von Kopierfehlern bei Uploads von Azure Data Box- und Azure Data Box Heavy-Geräten
description: Beschreibt die Überprüfung und das weitere Vorgehen bei nicht rückgängig zu machenden Fehlern, die verhindern, dass Dateien von einem Azure Data Box- oder Azure Data Box Heavy-Gerät hochgeladen werden.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 10/21/2021
ms.author: alkohli
ms.openlocfilehash: 019d4da7654c3cdf2eec7fc3b6f2434e1f42cd47
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218164"
---
# <a name="review-copy-errors-in-uploads-from-azure-data-box-and-azure-data-box-heavy-devices"></a>Überprüfen von Kopierfehlern bei Uploads von Azure Data Box- und Azure Data Box Heavy-Geräten

Dieser Artikel beschreibt die Überprüfung und das weitere Vorgehen bei nicht rückgängig zu machenden Fehlern, die gelegentlich verhindern, dass Dateien von einem Azure Data Box- oder Azure Data Box Heavy-Gerät in die Cloud hochgeladen werden.

> [!NOTE]
> Die Informationen in diesem Artikel betreffen nur Importaufträge.

## <a name="upload-errors-notification"></a>Benachrichtigung über Uploadfehler

Wenn Daten von Ihrem Gerät in Azure hochgeladen werden, können gelegentlich einige Dateiuploads aufgrund von Konfigurationsfehlern fehlschlagen, die nicht durch einen Wiederholungsversuch behoben werden können. In diesem Fall erhalten Sie eine Benachrichtigung, damit Sie die Fehler überprüfen und für einen späteren Upload beheben können.

Die folgende Benachrichtigung wird im Azure-Portal angezeigt. Die Fehler werden im Datenkopierprotokoll aufgeführt, das Sie über den **Datenkopierpfad** öffnen können. Eine Anleitung zum Beheben der Fehler finden Sie unter [Zusammenfassung nicht rückgängig zu machender Uploadfehler](#summary-of-non-retryable-upload-errors).

![Benachrichtigung über Fehler während des Uploads](media/data-box-troubleshoot-data-upload/copy-completed-with-errors-notification-01.png)

Sie können diese Fehler nicht beheben. Der Upload wurde mit Fehlern abgeschlossen. Die Benachrichtigung informiert Sie über alle Konfigurationsprobleme, die Sie beheben müssen, bevor Sie einen weiteren Upload über eine Netzwerkübertragung oder einen neuen Importauftrag versuchen.

Nachdem Sie die Fehler überprüft und bestätigt haben, dass Sie bereit zum Fortfahren sind, werden die Daten sicher vom Gerät gelöscht. Wenn Sie nicht auf die Benachrichtigung reagieren, wird der Auftrag nach 14 Tagen automatisch abgeschlossen. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Überprüfen von Fehlern und Fortfahren](#review-errors-and-proceed).


## <a name="review-errors-and-proceed"></a>Überprüfen von Fehlern und Fortfahren

Der Auftrag wird nach 14 Tagen automatisch abgeschlossen. Wenn Sie auf die Benachrichtigung reagieren, können Sie den Vorgang beschleunigen.

[!INCLUDE [data-box-review-nonretryable-errors](../../includes/data-box-review-nonretryable-errors.md)]


## <a name="summary-of-non-retryable-upload-errors"></a>Zusammenfassung nicht rückgängig zu machender Uploadfehler

Die folgenden nicht rückgängig zu machenden Fehler führen zu einer Benachrichtigung:

|Fehlerkategorie                    |Fehlercode |Fehlermeldung                                                                             |
|----------------------------------|-----------|------------------------------------------------------------------------------------------|
|UploadErrorCloudHttp              |400        |Ungültige Anforderung (Dateiname ungültig). [Weitere Informationen](#bad-request-file-name-not-valid)|
|UploadErrorCloudHttp              |400        |Der Wert eines der HTTP-Header weist nicht das richtige Format auf. [Weitere Informationen](#the-value-for-one-of-the-http-headers-is-not-in-the-correct-format)|
|UploadErrorCloudHttp              |409        |Dieser Vorgang ist nicht zulässig, da das Blob aufgrund einer Richtlinie unveränderlich ist. [Weitere Informationen](#this-operation-is-not-permitted-as-the-blob-is-immutable-due-to-policy)|
|UploadErrorCloudHttp              |409        |Die insgesamt bereitgestellte Kapazität der Freigaben darf die maximal zulässige Größe des Kontos nicht überschreiten. [Weitere Informationen](#the-total-provisioned-capacity-of-the-shares-cannot-exceed-the-account-maximum-size-limit)|
|UploadErrorCloudHttp              |409        |Dieser BLOB-Typ ist für diesen Vorgang ungültig. [Weitere Informationen](#the-blob-type-is-invalid-for-this-operation)|
|UploadErrorCloudHttp              |409        |Es ist derzeit eine Lease für das Blob vorhanden, und in der Anforderung wurde keine Lease-ID angegeben. [Weitere Informationen](#there-is-currently-a-lease-on-the-blob-and-no-lease-id-was-specified-in-the-request)|
|UploadErrorManagedConversionError |409        |Die Größe des zu importierenden Blobs ist ungültig. Die Blobgröße beträgt `<blob-size>` Bytes. Unterstützte Größen liegen zwischen 20971520 Bytes und 8192 GiB. [Weitere Informationen](#the-size-of-the-blob-being-imported-is-invalid-the-blob-size-is-blob-size-bytes-supported-sizes-are-between-20971520-bytes-and-8192-gib)|
<!--Temporarily removed from table: Bad Request (file property failure for Azure Files)-->

Weitere Informationen zum Inhalt des Datenkopierprotokolls finden Sie unter [Nachverfolgung und Ereignisprotokollierung für Azure Data Box- und Azure Data Box Heavy-Importaufträge](data-box-logs.md).

Andere REST-API-Fehler können während des Datenuploads auftreten. Weitere Informationen finden Sie unter [Bekannte REST API-Fehlercodes](/rest/api/storageservices/common-rest-api-error-codes).

> [!NOTE]
> In den Abschnitten **Weiteres Vorgehen** der Fehlerbeschreibungen wird beschrieben, wie Sie die Datenkonfiguration aktualisieren, bevor Sie einen neuen Importauftrag ausgeben oder eine Netzwerkübertragung durchführen. Sie können diese Fehler im aktuellen Upload nicht beheben.


### <a name="bad-request-file-name-not-valid"></a>Ungültige Anforderung (Dateiname ungültig)

**Fehlerkategorie:** UploadErrorCloudHttp 

**Fehlercode:** 400

**Fehlerbeschreibung:** Die meisten Probleme mit der Dateibenennung werden während der Phase der **Versandvorbereitung** erfasst oder während des Uploads automatisch behoben (was zum Status **Kopieren mit Warnungen** führt). Wenn ein ungültiger Dateiname nicht erfasst wird, kann die Datei nicht in Azure hochgeladen werden.

**Weiteres Vorgehen:** Sie können diesen Fehler im aktuellen Upload nicht beheben. Der Upload wurde mit Fehlern abgeschlossen. Bevor Sie eine Netzwerkübertragung durchführen oder einen neuen Auftrag starten, benennen Sie die aufgelisteten Dateien um, damit die Benennungsanforderungen für Azure Files erfüllt werden. Informationen zu Benennungsanforderungen finden Sie unter [Verzeichnis- und Dateinamen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


<!--TEMPORARILY REMOVED. Product team may restore later. ### Bad Request (File property failure for Azure Files)

**Error category:** UploadErrorCloudHttp 

**Error code:** 400

**Error description:** Data import will fail if the upload of file properties fails for Azure Files.  

**Follow-up:** You can't fix this error in the current upload. The upload will complete with errors. Before you do a network transfer or start a new import order, *GET TROUBLESHOOTING*.-->


### <a name="the-value-for-one-of-the-http-headers-is-not-in-the-correct-format"></a>Der Wert eines der HTTP-Header weist nicht das richtige Format auf

**Fehlerkategorie:** UploadErrorCloudHttp 

**Fehlercode:** 400

**Fehlerbeschreibung:** Die aufgelisteten Blobs konnten nicht hochgeladen werden, da sie die Format- oder Größenanforderungen für Blobs in Azure Storage nicht erfüllen.

**Weiteres Vorgehen:** Sie können diesen Fehler im aktuellen Upload nicht beheben. Der Upload wurde mit Fehlern abgeschlossen. Bevor Sie eine Netzwerkübertragung durchführen oder einen neuen Importauftrag starten, stellen Sie Folgendes sicher:

- Die aufgelisteten Seitenblobs entsprechen den Seitenbegrenzungen von 512 Byte.

- Die aufgelisteten Blockblobs überschreiten nicht die maximale Größe von 4,75 TiB.


### <a name="this-operation-is-not-permitted-as-the-blob-is-immutable-due-to-policy"></a>Dieser Vorgang ist nicht zulässig, da das Blob aufgrund einer Richtlinie unveränderlich ist

**Fehlerkategorie:** UploadErrorCloudHttp 

**Fehlercode:** 409

**Fehlerbeschreibung:** Wenn ein Blobspeichercontainer als WORM (Write Once, Read Many) konfiguriert ist, können Blobs, die bereits im Container gespeichert sind, nicht hochgeladen werden.

**Weiteres Vorgehen:** Sie können diesen Fehler im aktuellen Upload nicht beheben. Der Upload wurde mit Fehlern abgeschlossen. Bevor Sie eine Netzwerkübertragung durchführen oder einen neuen Importauftrag starten, stellen Sie sicher, dass die aufgelisteten Blobs nicht Teil eines unveränderlichen Speichercontainers sind. Weitere Informationen finden Sie unter [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](../storage/blobs/immutable-storage-overview.md).


### <a name="the-total-provisioned-capacity-of-the-shares-cannot-exceed-the-account-maximum-size-limit"></a>Die insgesamt bereitgestellte Kapazität der Freigaben darf die maximal zulässige Größe des Kontos nicht überschreiten

**Fehlerkategorie:** UploadErrorCloudHttp 

**Fehlercode:** 409

**Fehlerbeschreibung:** Fehler beim Hochladen, da die Gesamtgröße der Daten die Größenbeschränkung des Speicherkontos überschreitet. Beispiel: Die maximale Kapazität eines FileStorage-Kontos beträgt 100 TiB. Wenn die Gesamtgröße der Daten 100 TiB übersteigt, ist der Upload nicht möglich.  

**Weiteres Vorgehen:** Sie können diesen Fehler im aktuellen Upload nicht beheben. Der Upload wurde mit Fehlern abgeschlossen. Bevor Sie eine Netzwerkübertragung durchführen oder einen neuen Importauftrag starten, stellen Sie sicher, dass die Gesamtkapazität aller Freigaben im Speicherkonto die Größenbeschränkung des Speicherkontos nicht überschreitet. Weitere Informationen finden Sie unter [Größenbeschränkungen für das Azure-Speicherkonto](data-box-limits.md#azure-storage-account-size-limits).


### <a name="the-blob-type-is-invalid-for-this-operation"></a>Der Blobtyp ist für diesen Vorgang ungültig

**Fehlerkategorie:** UploadErrorCloudHttp 

**Fehlercode:** 409

**Fehlerbeschreibung:** Der Datenimport in ein Blob in der Cloud ist nicht möglich, wenn die Daten oder Eigenschaften des Zielblobs geändert werden.

**Weiteres Vorgehen:** Sie können diesen Fehler im aktuellen Upload nicht beheben. Der Upload wurde mit Fehlern abgeschlossen. Bevor Sie eine Netzwerkübertragung durchführen oder einen neuen Importauftrag starten, stellen Sie sicher, dass die aufgelisteten Blobs oder deren Eigenschaften während des Uploads nicht gleichzeitig geändert werden.

### <a name="there-is-currently-a-lease-on-the-blob-and-no-lease-id-was-specified-in-the-request"></a>Es ist derzeit eine Lease für das Blob vorhanden, und in der Anforderung wurde keine Lease-ID angegeben

**Fehlerkategorie:** UploadErrorCloudHttp 

**Fehlercode:** 409

**Fehlerbeschreibung:** Der Datenimport in ein Blob in der Cloud ist nicht möglich, wenn das Zielblob über eine aktive Lease verfügt.

**Weiteres Vorgehen:** Sie können diesen Fehler im aktuellen Upload nicht beheben. Der Upload wurde mit Fehlern abgeschlossen. Bevor Sie eine Netzwerkübertragung durchführen oder einen neuen Importauftrag starten, stellen Sie sicher, dass die aufgelisteten Blobs keine aktive Lease aufweisen. Weitere Informationen finden Sie unter [Pessimistische Nebenläufigkeit für Blobs](../storage/blobs/concurrency-manage.md?tabs=dotnet#pessimistic-concurrency-for-blobs).


### <a name="the-size-of-the-blob-being-imported-is-invalid-the-blob-size-is-blob-size-bytes-supported-sizes-are-between-20971520-bytes-and-8192-gib"></a>Die Größe des zu importierenden Blobs ist ungültig. Die Blobgröße beträgt `<blob-size>` Bytes. Unterstützte Größen liegen zwischen 20971520 Bytes und 8192 GiB.

**Fehlerkategorie:** UploadErrorManagedConversionError

**Fehlercode:** 409

**Fehlerbeschreibung:** Die aufgelisteten Seitenblobs konnten nicht hochgeladen werden, da sie keine Größe haben, die in einen verwalteten Datenträger konvertiert werden kann. Zur Konvertierung in einen verwalteten Datenträger muss ein Seitenblob zwischen 20 MB (20.971.520 Bytes) und 8192 GiB groß sein.

**Weiteres Vorgehen:** Sie können diesen Fehler im aktuellen Upload nicht beheben. Der Upload wurde mit Fehlern abgeschlossen. Bevor Sie eine Netzwerkübertragung durchführen oder einen neuen Importauftrag starten, stellen Sie sicher, dass jedes aufgelistete Blob zwischen 20 MB und 8192 GiB groß ist.


## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen bekannter REST-API-Fehler](/rest/api/storageservices/common-rest-api-error-codes)
- [Überprüfen des Datenuploads in Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure)