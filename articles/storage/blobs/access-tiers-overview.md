---
title: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobdaten
titleSuffix: Azure Storage
description: Azure Storage weist verschiedene Zugriffsebenen auf, sodass Sie Blobdaten basierend auf ihrer Verwendung auf die kostengünstigste Weise speichern können. In diesem Artikel erhalten Sie Informationen zu den Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blob Storage.
author: tamram
ms.author: tamram
ms.date: 10/07/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: 7af8e29890c63429a50c9818baa001bb5990c26b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709001"
---
# <a name="hot-cool-and-archive-access-tiers-for-blob-data"></a>Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobdaten

Die Speicherung von Daten in der Cloud nimmt immer mehr zu. Um die Kosten für die zunehmenden Speicheranforderungen im Blick zu behalten, kann es hilfreich sein, die Daten anhand ihrer Zugriffshäufigkeit und Aufbewahrungsdauer zu organisieren. Azure Storage weist verschiedene Zugriffsebenen auf, sodass Sie Blobdaten basierend auf ihrer Verwendung auf die kostengünstigste Weise speichern können. Folgende Azure Storage-Zugriffsebenen stehen zur Verfügung:

- **Heiße Zugriffsebene:** Onlineebene, die für die Speicherung von Daten optimiert ist, auf die häufig zugegriffen wird oder die häufig geändert werden. Die heiße Ebene hat die höchsten Speicherkosten, aber auch die niedrigsten Zugriffskosten.
- **Kalte Zugriffsebene:** Onlineebene, die für die Speicherung von Daten optimiert ist, auf die nicht häufig zugegriffen wird oder die nicht häufig geändert werden. Daten auf der kalten Ebene müssen mindestens 30 Tage lang gespeichert werden. Für die kalte Ebene fallen im Vergleich zur heißen Ebene niedrigere Speicherkosten, aber höhere Zugriffskosten an.
- **Archivzugriffsebene:** Offlineebene, die für die Speicherung von Daten optimiert ist, auf die nur selten zugegriffen wird und die flexible Latenzanforderungen in der Größenordnung von Stunden aufweisen. Daten auf Archivzugriffsebene müssen mindestens 180 Tage lang gespeichert werden.

Azure Storage-Kapazitätsgrenzen werden auf Kontoebene und nicht nach Zugriffsebene festgelegt. Sie können die Kapazitätsauslastung auf einer Ebene maximieren oder die Kapazität auf zwei oder mehr Ebenen verteilen.

## <a name="online-access-tiers"></a>Onlinezugriffsebenen

Wenn Ihre Daten in einer Onlinezugriffsebene (heiße oder kalte Ebene) gespeichert sind, können Benutzer*innen sofort darauf zugreifen. Die heiße Ebene eignet sich am besten für Daten, die aktiv verwendet werden. Die kalte Ebene ist dagegen ideal für Daten, auf die seltener zugegriffen wird, die aber dennoch für Lese- und Schreibvorgänge verfügbar sein müssen.

Beispielszenarien für die Verwendung der heißen Zugriffsebene:

- Daten, die aktiv verwendet werden oder bei denen häufige Lese- und Schreibvorgänge zu erwarten sind
- Daten, die zur Verarbeitung und späteren Migration zur kalten Zugriffsebene bereitgestellt werden.

Verwendungsszenarien für die kalte Zugriffsebene:

- Kurzfristige Datensicherung und Notfallwiederherstellung
- Ältere Datasets, die nicht häufig verwendet werden, aber für den sofortigen Zugriff verfügbar sein müssen
- Umfangreiche Datasets, die kostengünstig gespeichert werden müssen, während weitere Daten zur Verarbeitung gesammelt werden

Daten der kalten Ebene weisen eine etwas geringere Verfügbarkeit auf, aber Dauerhaftigkeit, Abrufwartezeit und Durchsatz sind auf dem gleichen hohen Niveau wie bei der heißen Ebene. Daher kann bei Daten der kalten Ebene eine Kombination aus etwas geringerer Verfügbarkeit und höheren Zugriffskosten im Vergleich zur heißen Ebene in Kauf genommen werden, da im Gegenzug die Gesamtspeicherkosten geringer ausfallen. Weitere Informationen finden Sie unter [SLA für Speicher](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).

Für ein Blob auf der kalten Ebene in einem Konto vom Typ „Universell v2“ fällt eine Gebühr für frühzeitiges Löschen an, wenn es vor Ablauf von 30 Tagen gelöscht oder auf eine andere Ebene verschoben wird. Diese Gebühr fällt anteilig an. Wenn ein Blob beispielsweise auf die kalte Ebene verschoben und dann nach 21 Tagen gelöscht wird, fällt eine Gebühr für frühzeitiges Löschen an, die den 9 Tagen (30 – 21) der Speicherung dieses Blobs in der kalten Ebene entspricht.

Auf der heißen und kalten Ebene werden alle Redundanzkonfigurationen unterstützt. Weitere Informationen zu Datenredundanzoptionen in Azure Storage finden Sie unter [Azure Storage-Redundanz](../common/storage-redundancy.md).  

## <a name="archive-access-tier"></a>Zugriffsebene „Archiv“

Die Archivebene ist eine Offlineebene für die Speicherung von Daten, auf die nur selten zugegriffen wird. Die Archivzugriffsebene hat im Vergleich zur heißen und kalten Ebene die niedrigsten Speicherkosten, aber höhere Datenabrufkosten und eine höhere Latenz. Beispielszenarien für die Verwendung der Archivzugriffsebene:

- Langfristige Sicherung, sekundäre Sicherung und Archivierungsdatasets
- Originaldaten (Rohdaten), die auch nach der Umwandlung in ein endgültiges verwendbares Format erhalten bleiben müssen
- Compliance- und Archivdaten, die über einen langen Zeitraum gespeichert werden müssen und auf die selten zugegriffen wird

Die Daten müssen sich mindestens 180 Tage lang auf der Archivspeicherebene befinden, sonst unterliegen sie einer Gebühr für frühe Löschung. Wenn ein Blob beispielsweise auf die Archivspeicherebene verschoben und dann nach 45 Tagen gelöscht oder auf die heiße Ebene verschoben wird, wird Ihnen eine Gebühr für frühes Löschen berechnet, die den 135 Speichertagen (180 - 45) dieses Blobs im Archiv entspricht.

Solange sich ein Blob auf der Archivebene befindet, kann es nicht gelesen oder geändert werden. Um ein Blob, das sich auf der Archivebene befindet, lesen oder herunterladen zu können, müssen Sie es zunächst auf einer Onlineebene (heiße oder kalte Ebene) aktivieren. Die Aktivierung von Daten auf Archivebene kann bis zu 15 Stunden dauern. Weitere Informationen zur Blobaktivierung finden Sie unter [Übersicht über die Aktivierung von Blobs aus der Archivebene](archive-rehydrate-overview.md).

Die Metadaten eines archivierten Blobs bleiben für den Lesezugriff verfügbar, sodass Sie das Blob und seine Eigenschaften, Metadaten und Indextags auflisten können. Die Metadaten von Blobs auf Archivebene sind schreibgeschützt, Blobindextags können dagegen gelesen oder geschrieben werden. Momentaufnahmen werden für archivierte Blobs nicht unterstützt.

Für Blobs auf der Archivebene werden folgende Vorgänge unterstützt:

- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Festlegen von Blobtags](/rest/api/storageservices/set-blob-tags)
- [Abrufen von Blobtags](/rest/api/storageservices/get-blob-tags)
- [Suchen nach Blobs anhand von Tags](/rest/api/storageservices/find-blobs-by-tags)
- [Auflisten von Blobs](/rest/api/storageservices/list-blobs)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url)
- [Delete Blob](/rest/api/storageservices/delete-blob)

> [!NOTE]
> Die Archivspeicherebene wird für ZRS-, GZRS- oder RA-GZRS-Konten nicht unterstützt. Die Migration von LRS zu GRS wird unterstützt, solange keine Blobdaten in die Archivebene verschoben wurden, während das Konto auf LRS festgelegt wurde. Ein Konto kann zurück in GRS verschoben werden, wenn die Aktualisierung weniger als 30 Tage nach der Umstellung des Kontos auf LRS erfolgt und keine Blobs auf die Archivebene verschoben wurden, während das Konto auf LRS festgelegt war.

## <a name="default-account-access-tier-setting"></a>Einstellung für die Standardzugriffsebene eines Kontos

Speicherkonten verfügen über eine Einstellung für die Standardzugriffsebene, mit der die Onlineebene angegeben wird, auf der ein neues Blob erstellt wird. Die Einstellung für die Standardzugriffsebene kann auf die heiße oder die kalte Ebene festgelegt werden. Sie können die Standardeinstellung für ein einzelnes Blob überschreiben, wenn Sie das Blob hochladen oder die zugehörige Ebene ändern.

Die Standardzugriffsebene für ein neues universelles v2-Speicherkonto ist standardmäßig auf die Hot Tier eingestellt. Sie können die Einstellung für die Standardzugriffsebene beim oder nach dem Erstellen eines Speicherkontos ändern. Wenn Sie diese Einstellung im Speicherkonto nicht ändern oder die Ebene beim Hochladen eines Blobs nicht explizit festlegen, wird ein neuer Blob standardmäßig auf die heiße Ebene hochgeladen.

Für ein Blob, dem keine explizite Ebene zugewiesen ist, wird die zugehörige Ebene von der Einstellung für die Standardzugriffsebene des Kontos abgeleitet. Wenn die Zugriffsebene eines Blobs von der Einstellung für die Standardzugriffsebene des Kontos abgeleitet ist, wird im Azure-Portal als Zugriffsebene **Heiß (abgeleitet)** oder **Kalt (abgeleitet)** angezeigt.

Die Änderung der Einstellung für die Standardzugriffsebene eines Kontos gilt für alle Blobs im Konto, für die keine explizite Zugriffsebene festgelegt ist. Wenn Sie die Einstellung für die Standardzugriffsebene eines Kontos vom Typ „Universell v2“ von „Heiß“ in „Kalt“ ändern, werden Ihnen Schreibvorgänge (pro 10.000) für alle Blobs berechnet, für die die Zugriffsebene abgeleitet wird. Wenn Sie in einem allgemeinen v2-Konto von "cool" auf "hot" umschalten, werden Ihnen sowohl Lesevorgänge (pro 10.000) als auch Datenabrufe (pro GB) berechnet.

Wenn Sie ein Legacy-Blob-Storage-Konto erstellen, müssen Sie bei der Erstellung die Standard-Zugriffsebene als Hot oder Cool festlegen. Das Ändern der Standard-Zugriffsebene von "Hot" zu "Cool" in einem Legacy-Blob-Storage-Konto ist kostenlos. Wenn Sie in einem Blob-Storage-Konto von "cool" auf "hot" umschalten, werden Ihnen sowohl Lesevorgänge (pro 10.000) als auch Datenabrufe (pro GB) in Rechnung gestellt. Microsoft empfiehlt, wenn möglich allgemeine v2-Speicherkonten anstelle von Blob-Storage-Konten zu verwenden.

> [!NOTE]
> Die Archivebene wird nicht als Standardzugriffsebene für Speicherkonten unterstützt.

## <a name="setting-or-changing-a-blobs-tier"></a>Festlegen oder Ändern der Ebene eines Blobs

Um die Ebene eines Blobs beim Erstellen explizit festzulegen, geben Sie sie beim Hochladen des Blobs an.

Nach dem Erstellen eines Blobs können Sie die zugehörige Ebene mit einer der folgenden Methoden ändern:

- Durch Aufrufen des Vorgangs [Set Blob Tier](/rest/api/storageservices/set-blob-tier), entweder direkt oder über eine Richtlinie zur [Lebenszyklusverwaltung](#blob-lifecycle-management). Der Aufruf von [Set Blob Tier](/rest/api/storageservices/set-blob-tier) ist in der Regel die beste Option, wenn die Ebene eines Blobs von einer heißen in eine kältere Ebene geändert wird.
- Durch Aufrufen des Vorgangs [Copy Blob](/rest/api/storageservices/copy-blob) zum Kopieren eines Blobs zwischen Ebenen. Der Aufruf von [Copy Blob](/rest/api/storageservices/copy-blob) wird für die meisten Szenarien empfohlen, bei denen Sie ein Blob aus der Archivebene auf einer Onlineebene aktivieren oder von der kalten auf die heiße Ebene verschieben. Durch Kopieren eines Blobs können Sie die Gebühr für frühzeitiges Löschen vermeiden, wenn das erforderliche Speicherintervall für das Quellblob noch nicht verstrichen ist. Das Kopieren eines Blobs führt jedoch zu Kapazitätsgebühren für zwei Blobs: das Quellblob und das Zielblob.

Die Änderung der Ebene eines Blobs von „Heiß“ in „Kalt“ oder „Archiv“ erfolgt sofort. Dies gilt auch für die Änderung von „Kalt“ in „Heiß“. Die Aktivierung eines Blobs aus der Archivebene auf der heißen oder kalten Ebene kann bis zu 15 Stunden dauern.

Beachten Sie beim Wechsel zwischen der kalten und der Archivebene für ein Blob die folgenden Punkte:

- Wenn ein Blob aufgrund der Standardspeicherebene des Speicherkontos als „kalt“ eingestuft und auf die Archivspeicherebene verschoben wird, fallen keine Gebühren für das frühzeitige Löschen an.
- Wenn ein Blob explizit in die kalte Speicherebene verschoben wurde und dann auf die Archivspeicherebene verschoben wird, werden Gebühren für das frühzeitige Löschen berechnet.

## <a name="blob-lifecycle-management"></a>Lebenszyklusverwaltung für Blobs

Die Blob Storage-Lebenszyklusverwaltung umfasst eine regelbasierte Richtlinie, mit der Sie Daten zur gewünschten Zugriffsebene verschieben können, wenn die angegebenen Bedingungen erfüllt sind. Über die Lebenszyklusverwaltung können Sie auch festlegen, dass Daten am Ende ihrer Lebensdauer ablaufen. Weitere Informationen finden Sie unter [Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen](./lifecycle-management-overview.md).

> [!NOTE]
> In einem Blockblob-Premium-Speicherkonto gespeicherte Daten können weder mithilfe von [Set Blob Tier](/rest/api/storageservices/set-blob-tier) noch unter Verwendung der Azure Blob Storage-Lebenszyklusverwaltung auf die Ebene „Heiß“, „Kalt“ oder „Archiv“ verschoben werden. Um Daten zu verschieben, müssen Sie Blobs synchron vom Blockblob-Speicherkonto auf die heiße Ebene in einem anderen Konto kopieren. Verwenden Sie dazu die API [Put Block From URL](/rest/api/storageservices/put-block-from-url) oder eine AzCopy-Version, die diese API unterstützt. Die **Put Block From URL**-API kopiert Daten synchron auf den Server, was heißt, dass der Aufruf erst abgeschlossen wird, nachdem alle Daten vom ursprünglichen Serverspeicherort an den Zielspeicherort verschoben wurden.

## <a name="summary-of-access-tier-options"></a>Zusammenfassung der Zugriffsebenenoptionen

In der folgenden Tabelle sind die Funktionen der Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ zusammengefasst.

|  | **Zugriffsebene „Heiß“** | **Zugriffsebene „Kalt“** | **Zugriffsebene „Archiv“** |
|--|--|--|--|
| **Verfügbarkeit** | 99,9 % | 99 % | Offline |
| **Verfügbarkeit** <br> **(RA-GRS-Lesevorgänge)** | 99,99 % | 99,9 % | Offline |
| **Nutzungsgebühren** | Höhere Speicherkosten, jedoch geringere Zugriffs- und Transaktionskosten | Geringere Speicherkosten, jedoch höhere Zugriffs- und Transaktionskosten | Geringste Speicherkosten, jedoch höchste Zugriffs- und Transaktionskosten |
| **Empfohlene Mindestaufbewahrungsdauer für Daten** | – | 30 Tage<sup>1</sup> | 180 Tage |
| **Latenz** <br> **(Zeit bis zum ersten Byte)** | Millisekunden | Millisekunden | Stunden<sup>2</sup> |
| **Unterstützte Redundanzkonfigurationen** | Alle | Alle | Nur LRS, GRS und RA-GRS<sup>3</sup> |

<sup>1</sup> Bei Konten vom Typ „Universell v2“ beträgt die Aufbewahrungsdauer für Objekte der kalten Ebene mindestens 30 Tage. Bei Blob Storage-Konten gilt keine Mindestaufbewahrungsdauer für die kalte Ebene.

<sup>2</sup> Beim Aktivieren eines Blobs aus der Archivebene haben Sie die Wahl zwischen der Aktivierungspriorität „Hoch“ und „Standard“. Die Optionen unterscheiden sich in Bezug auf Abrufwartezeiten und Kosten. Weitere Informationen finden Sie unter [Übersicht über die Aktivierung von Blobs aus der Archivebene](archive-rehydrate-overview.md).

<sup>3</sup> Weitere Informationen zu Redundanzkonfigurationen in Azure Storage finden Sie unter [Azure Storage-Redundanz](../common/storage-redundancy.md).

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Für alle Speicherkonten wird ein Blockblobspeicher-Preismodell angewandt, das auf der Ebene des jeweiligen Blobs basiert. Beachten Sie die in den folgenden Abschnitten beschriebenen Aspekte der Abrechnung.

Weitere Informationen zu den Preisen für Blockblobs finden Sie unter [Preise für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="storage-capacity-costs"></a>Kosten für die Speicherkapazität

Die Kosten für die Datenspeicherung hängen nicht nur von der gespeicherten Datenmenge ab, sondern auch von der Zugriffsebene. Je „kälter“ die Ebene, desto geringer die Kosten für die Kapazität pro GB.

### <a name="data-access-costs"></a>Kosten für den Datenzugriff

Je „kälter“ die Ebene, desto höher die Gebühren für den Datenzugriff. Bei den Zugriffsebenen „Kalt“ und „Archiv“ fallen Zugriffsgebühren für Lesevorgänge pro Gigabyte an.

### <a name="transaction-costs"></a>Transaktionskosten

Für alle Ebenen fällt eine Gebühr pro Transaktion an, die steigt, je „kälter“ die Ebene ist.

### <a name="geo-replication-data-transfer-costs"></a>Datenübertragungskosten bei Georeplikation

Diese Gebühr gilt nur für Konten mit konfigurierter Georeplikation (einschließlich GRS und RA-GRS). Die Datenübertragung für die Georeplikation wird pro Gigabyte abgerechnet.

### <a name="outbound-data-transfer-costs"></a>Übertragungskosten für ausgehende Daten

Ausgehende Datenübertragungen (Daten, die aus einer Azure-Region übertragen werden) werden nach Bandbreitennutzung pro GB abgerechnet. Weitere Informationen zu den Kosten für ausgehende Datenübertragungen finden Sie auf der Seite [Bandbreite: Preisübersicht](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="changing-the-default-account-access-tier"></a>Ändern der Standardzugriffsebene eines Kontos

Bei einer Änderung der Kontozugriffsebene fallen Gebühren für die Ebenenänderung für alle Blobs an, für die noch keine explizite Ebene festgelegt ist. Weitere Informationen finden Sie im folgenden Abschnitt [Ändern der Zugriffsebene eines Blobs](#changing-a-blobs-access-tier).

### <a name="changing-a-blobs-access-tier"></a>Ändern der Zugriffsebene eines Blobs

Beachten Sie die folgenden Auswirkungen auf die Abrechnung bei der Änderung der Ebene eines Blobs:

- Wenn ein Blob hochgeladen oder zwischen Ebenen verschoben wird, wird dies sofort nach dem Hochladen oder einer Ebenenänderung zur entsprechenden Gebühr in Rechnung gestellt.
- Wenn ein Blob auf eine „kältere“ Ebene verschoben wird (von „Heiß“ zu „Kalt“, von „Heiß“ zu „Archiv“ oder von „Kalt“ zu „Archiv“), wird der Vorgang als Schreibvorgang in die Zielebene berechnet, und es gelten die Gebühren der Zielebene für Schreibvorgänge (pro 10.000) und das Schreiben von Daten (pro GB).
- Wenn ein Blob auf eine „wärmere“ Ebene verschoben wird (von „Archiv“ zu „Kalt“, von „Archiv“ zu „Heiß“ oder von „Kalt“ zu „Heiß“), wird der Vorgang als Lesevorgang aus der Quellebene berechnet, und es gelten die Gebühren der Quellebene für Lesevorgänge (pro 10.000) und Datenabruf (pro GB). Für alle Blobs, die aus der Ebene „Cool“ oder „Archiv“ verschoben werden, können auch Gebühren für das frühe Löschen anfallen.
- Wenn ein Blob aus der Archivebene aktiviert wird, werden die Daten des Blobs als archivierte Daten abgerechnet, bis die Daten wiederhergestellt werden und sich die Ebene des Blobs in „Heiß“ oder „Kalt“ ändert.

Die folgende Tabelle gibt Aufschluss darüber, wie Ebenenänderungen abgerechnet werden.

| | **Schreibgebühren (Vorgang + Zugriff)** | **Lesegebühren (Vorgang + Zugriff)** |
| ---- | ----- | ----- |
| Vorgang **Set Blob Tier** | Von „Heiß“ in „Kalt“<br> Von „Heiß“ in „Archiv“<br> Von „Kalt“ in „Archiv“ | Von „Archiv“ in „Kalt“<br> Von „Archiv“ in „Heiß“<br> Von „Kalt“ in „Heiß“

Wenn Sie die Zugriffsebene für ein Blob ändern, wenn die Versionsverwaltung aktiviert ist, oder wenn das Blob über Momentaufnahmen verfügt, kann dies zu zusätzlichen Kosten führen. Informationen zu Blobs mit aktivierter Versionsverwaltung finden Sie in der Dokumentation zur Blobversionsverwaltung unter [Preise und Abrechnung](versioning-overview.md#pricing-and-billing). Informationen zu Blobs mit Momentaufnahmen finden Sie in der Dokumentation zu Blobmomentaufnahmen unter [Preise und Abrechnung](snapshots-overview.md#pricing-and-billing).

## <a name="feature-support"></a>Featureunterstützung

In der folgenden Tabelle wird gezeigt, wie dieses Feature in Ihrem Konto unterstützt wird und welche Auswirkungen die Aktivierung bestimmter Funktionen auf die Unterstützung hat.

| Speicherkontotyp | Blob Storage (Standardunterstützung) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> |
|--|--|--|--|
| Standard, Universell V2 | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |
| Premium-Blockblobs | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) erfordern beide ein Speicherkonto mit einem aktivierten hierarchischen Namespace.

Informationen zur Funktionsunterstützung nach Region finden Sie unter [Verfügbare Azure-Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten der Ebene eines Blobs in einem Azure Storage-Konto](manage-access-tier.md)
- [Verwalten der Standard-Kontozugriffsebene für ein Azure Storage-Konto](../common/manage-account-default-access-tier.md)
- [Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen](./lifecycle-management-overview.md)
