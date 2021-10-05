---
title: Bekannte Probleme mit Azure Data Lake Storage Gen2
description: Erfahren Sie mehr über Einschränkungen und bekannte Probleme von Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 4276311163134b428fe9d616bf1e6bc23fdedfaf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638366"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Bekannte Probleme mit Azure Data Lake Storage Gen2

In diesem Artikel werden Einschränkungen und bekannte Probleme mit Konten beschrieben, bei denen das Feature für hierarchische Namespaces aktiviert ist.

> [!NOTE]
> Einige der in diesem Artikel beschriebenen Features werden möglicherweise nicht bei Konten unterstützt, für die Unterstützung für NFS 3.0 (Network File System) aktiviert ist. Eine Tabelle mit den Auswirkungen der Featureunterstützung, wenn verschiedene Funktionen aktiviert sind, finden Sie unter [Unterstützung von Blob Storage-Features in Azure Storage-Konten](storage-feature-support-in-storage-accounts.md).

## <a name="supported-blob-storage-features"></a>Unterstützte Blob Storage-Features

Immer mehr Blob Storage-Features funktionieren nun mit Konten, die über einen hierarchischen Namespace verfügen. Eine vollständige Liste finden Sie unter [Verfügbare Blob Storage-Features in Azure Data Lake Storage Gen2](./storage-feature-support-in-storage-accounts.md).

## <a name="supported-azure-service-integrations"></a>Unterstützte Azure-Dienstintegrationen

Azure Data Lake Storage Gen2 unterstützt verschiedene Azure-Dienste, die Sie zum Erfassen von Daten, zum Durchführen von Analysen und zum Erstellen visueller Darstellungen verwenden können. Eine Liste der unterstützten Azure-Dienste finden Sie unter [Azure-Dienste, die Azure Data Lake Storage Gen2 unterstützen](data-lake-storage-supported-azure-services.md).

Weitere Informationen finden Sie unter [Azure-Dienste, die Azure Data Lake Storage Gen2 unterstützen](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Unterstützte Open-Source-Plattformen

Mehrere Open-Source-Plattformen unterstützen Data Lake Storage Gen2. Eine vollständige Liste finden Sie unter [Open-Source-Plattformen, die Azure Data Lake Storage Gen2 unterstützen](data-lake-storage-supported-open-source-platforms.md).

Weitere Informationen finden Sie unter [Open-Source-Plattformen, die Azure Data Lake Storage Gen2 unterstützen](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>Blob Storage-APIs

Data Lake Storage Gen2-APIs, NFS 3.0 und Blob-APIs können mit denselben Daten arbeiten.

In diesem Abschnitt werden Probleme und Einschränkungen bei der Verwendung von Blob-APIs, NFS 3.0 und Data Lake Storage Gen2-APIs für dieselben Daten beschrieben.

- Sie können nicht Blob-APIs, NFS 3.0 und Data Lake Storage-APIs zum Schreiben in dieselbe Instanz einer Datei verwenden. Wenn Sie in eine Datei schreiben, indem Sie Data Lake Storage Gen2 oder APIs oder NFS 3.0 verwenden, sind die Blöcke dieser Datei für Aufrufe der [Get Block List](/rest/api/storageservices/get-block-list)-Blob-API nicht sichtbar. Die einzige Ausnahme ist, wenn Sie bei der Verwendung überschreiben. Sie können eine Datei oder ein Blob mithilfe der APIs oder mit NFS 3.0 überschreiben, indem Sie die Option zero-truncate verwenden.

- Wenn Sie den Vorgang [List Blobs](/rest/api/storageservices/list-blobs) verwenden, ohne ein Trennzeichen anzugeben, enthalten die Ergebnisse sowohl Verzeichnisse als auch Blobs. Wenn Sie sich für Trennzeichen entscheiden, sollten Sie nur einen Schrägstrich (`/`) verwenden. Dies ist das einzige Trennzeichen, das unterstützt wird.

- Wenn Sie die [Delete Blob](/rest/api/storageservices/delete-blob)-API zum Löschen eines Verzeichnisses verwenden, wird es nur gelöscht, sofern es leer ist. Dies bedeutet, dass Sie die Blob-API zum Löschen von Verzeichnissen nicht rekursiv verwenden können.

Diese Blob-Rest-APIs werden nicht unterstützt:

- [Put Blob (Page)](/rest/api/storageservices/put-blob)
- [Put Page](/rest/api/storageservices/put-page)
- [Get Page Ranges](/rest/api/storageservices/get-page-ranges)
- [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)
- [Put Page from URL](/rest/api/storageservices/put-page-from-url)
- [Put Block List](/rest/api/storageservices/put-block-list)

Nicht verwaltete VM-Datenträger werden für Konten, die über einen hierarchischen Namespace verfügen, nicht unterstützt. Wenn Sie einen hierarchischen Namespace für ein Speicherkonto aktivieren möchten, sollten Sie verwaltete VM-Datenträger in einem Speicherkonto anordnen, für das die Funktion für hierarchische Namespaces nicht aktiviert ist.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Unterstützung für das rekursive Festlegen von Zugriffssteuerungslisten (Access Control Lists, ACLs)

Die Möglichkeit, ACL-Änderungen aus dem übergeordneten Verzeichnis rekursiv auf untergeordnete Elemente anzuwenden, ist allgemein verfügbar. Im aktuellen Release dieser Funktion können Sie ACL-Änderungen mithilfe von Azure Storage-Explorer, PowerShell, der Azure-Befehlszeilenschnittstelle sowie mit dem .NET SDK, dem Java SDK und dem Python SDK anwenden. Unterstützung für das Azure-Portal ist noch nicht verfügbar.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Zugriffssteuerungslisten (Access Control Lists, ACLs) und anonymer Lesezugriff

Wenn [anonymer Lesezugriff](./anonymous-read-access-configure.md) für einen Container gewährt wurde, haben ACLs keine Auswirkungen auf diesen Container oder die darin enthaltenen Dateien.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Verwenden Sie nur die neueste Version von AzCopy ([AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Frühere Versionen von AzCopy wie z. B. AzCopy v8.1 werden nicht unterstützt.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure Storage-Explorer

Verwenden Sie nur Versionen ab `1.6.0`.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Storage-Explorer im Azure-Portal

ACLs werden noch nicht unterstützt.

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>Drittanbieteranwendungen

Drittanbieteranwendungen, die REST-APIs verwenden, funktionieren auch weiterhin, wenn Sie sie mit Data Lake Storage Gen2 verwenden. Anwendungen, die Blob-APIs aufrufen, funktionieren wahrscheinlich.

## <a name="storage-analytics-logs-classic"></a>Storage Analytics-Protokolle (klassisch)

Die Einstellung für die Aufbewahrungsdauer wird noch nicht unterstützt. Sie können Protokolle jedoch manuell löschen, indem Sie ein beliebiges unterstütztes Tool wie Azure Storage Explorer, REST oder ein SDK verwenden.

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Richtlinien für die Lebenszyklusverwaltung im Premium-Tarif für Azure Data Lake Storage

Daten, die im Premium-Tarif gespeichert sind, können nicht zwischen den Ebenen „Heiß“, „Kalt“ und „Archiv“ verschoben werden. Sie können jedoch Daten aus dem Premium-Tarif auf die Zugriffsebene „Heiß“ in einem anderen Konto kopieren.

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>WASB-Treiber (Windows Azure Storage Blob) (bei Data Lake Storage Gen2 nicht unterstützt)

Derzeit treten beim WASB-Treiber, der nur für die Verwendung mit der Blob-API entwickelt wurde, in einigen gängigen Szenarien Probleme auf. Dies gilt insbesondere, wenn es sich um einen Client für ein Speicherkonto mit aktiviertem hierarchischen Namespace handelt. Durch einen Multiprotokollzugriff für Data Lake Storage lassen sich diese Probleme nicht beheben.

Zum gegenwärtigen Zeitpunkt (und wahrscheinlich auch in absehbarer Zukunft) werden Kunden, die den WASB-Treiber als Client für ein Speicherkonto mit aktiviertem hierarchischen Namespace verwenden, nicht unterstützt. Stattdessen wird empfohlen, den [ABFS](data-lake-storage-abfs-driver.md)-Treiber (Azure Blob File System) in der Hadoop-Umgebung zu verwenden. Wenn Sie versuchen, eine Migration von einer lokalen Hadoop-Umgebung mit einer früheren Version als Hadoop Branch-3 durchzuführen, öffnen Sie ein Azure-Supportticket, damit wir uns mit Ihnen in Verbindung setzen können, um den richtigen Weg für Sie und Ihre Organisation zu finden.

## <a name="soft-delete-for-blobs-capability-currently-in-preview"></a>Vorläufiges Löschen für Blobsfunktion (derzeit in der Vorschauversion)

Wenn übergeordnete Verzeichnisse von vorläufig gelöschten Dateien oder Verzeichnissen umbenannt wurden, werden die vorläufig gelöschten Elemente möglicherweise nicht ordnungsgemäß im Azure-Portal angezeigt. In solchen Fällen können Sie [PowerShell](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-powershell) oder [Azure CLI](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-azure-cli) verwenden, um die vorläufig gelöschten Elemente aufzulisten und wiederherzustellen.

## <a name="events"></a>Ereignisse

Wenn Ihr Konto über ein Ereignisabonnement verfügt, führen Lesevorgänge am sekundären Endpunkt zu einem Fehler. Entfernen Sie Ereignisabonnements, um dieses Problem zu beheben.

> [!TIP]
> Lesezugriff auf den sekundären Endpunkt ist nur möglich, wenn Sie georedundanten Speicher mit Lesezugriff (RA-GRS) oder geozonenredundanten Speicher mit Lesezugriff (RA-GZRS) aktivieren.
