---
title: Hot-, Cool- und Archiv-Zugriffsebenen für Blob-Daten
titleSuffix: Azure Storage
description: Azure Storage weist verschiedene Zugriffsebenen auf, sodass Sie Blobdaten basierend auf ihrer Verwendung auf die kostengünstigste Weise speichern können. Erfahren Sie mehr über die Zugriffsebenen Hot, Cool und Archive für Blob Storage.
author: tamram
ms.author: tamram
ms.date: 11/01/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: 304ca8f96d2b8973bd4f7f394bc0d2fbff541a61
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421986"
---
# <a name="hot-cool-and-archive-access-tiers-for-blob-data"></a>Hot-, Cool- und Archiv-Zugriffsebenen für Blob-Daten

Die Speicherung von Daten in der Cloud nimmt immer mehr zu. Um die Kosten für die zunehmenden Speicheranforderungen im Blick zu behalten, kann es hilfreich sein, die Daten anhand ihrer Zugriffshäufigkeit und Aufbewahrungsdauer zu organisieren. Azure Storage weist verschiedene Zugriffsebenen auf, sodass Sie Blobdaten basierend auf ihrer Verwendung auf die kostengünstigste Weise speichern können. Folgende Azure Storage-Zugriffsebenen stehen zur Verfügung:

- **Heiße Zugriffsebene:** Onlineebene, die für die Speicherung von Daten optimiert ist, auf die häufig zugegriffen wird oder die häufig geändert werden. Die Hot Tier hat die höchsten Speicherkosten, aber die niedrigsten Zugriffskosten.
- **Kalte Zugriffsebene:** Onlineebene, die für die Speicherung von Daten optimiert ist, auf die nicht häufig zugegriffen wird oder die nicht häufig geändert werden. Die Daten im Cool-Tier sollten mindestens 30 Tage lang gespeichert werden. Der Cool Tier hat geringere Speicherkosten und höhere Zugriffskosten als der Hot Tier.
- **Archivzugriffsebene:** Offlineebene, die für die Speicherung von Daten optimiert ist, auf die nur selten zugegriffen wird und die flexible Latenzanforderungen in der Größenordnung von Stunden aufweisen. Die Daten in der Archivschicht sollten mindestens 180 Tage lang aufbewahrt werden.

Azure Storage-Kapazitätsgrenzen werden auf Kontoebene und nicht nach Zugriffsebene festgelegt. Sie können die Kapazitätsauslastung auf einer Ebene maximieren oder die Kapazität auf zwei oder mehr Ebenen verteilen.

## <a name="online-access-tiers"></a>Onlinezugriffsebenen

Wenn Ihre Daten in einer Online-Zugriffsebene (entweder Hot oder Cool) gespeichert sind, können die Benutzer sofort darauf zugreifen. Die Hot Tier ist die beste Wahl für Daten, die aktiv genutzt werden, während die Cool Tier ideal für Daten ist, auf die weniger häufig zugegriffen wird, die aber dennoch zum Lesen und Schreiben verfügbar sein müssen.

Beispiele für Anwendungsszenarien für die heiße Schicht sind:

- Daten, die aktiv verwendet werden oder bei denen häufige Lese- und Schreibvorgänge zu erwarten sind
- Daten, die für die Verarbeitung und eventuelle Migration auf die kalte Speicherebene bereitgestellt werden.

Zu den Nutzungsszenarien für die kalte Speicherebene gehören:

- Kurzfristige Datensicherung und Notfallwiederherstellung
- Ältere Datasets, die nicht häufig verwendet werden, aber für den sofortigen Zugriff verfügbar sein müssen
- Umfangreiche Datasets, die kostengünstig gespeichert werden müssen, während weitere Daten zur Verarbeitung gesammelt werden

Die Daten im Cool-Tier haben eine etwas geringere Verfügbarkeit, bieten aber die gleiche hohe Beständigkeit, Abruflatenz und Durchsatzmerkmale wie das Hot-Tier. Für Daten in der "Cool"-Stufe können eine etwas geringere Verfügbarkeit und höhere Zugriffskosten im Vergleich zur "Hot"-Stufe ein akzeptabler Kompromiss für niedrigere Gesamtspeicherkosten sein. Weitere Informationen finden Sie unter [SLA für Speicher](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).

Ein Blob in der Cool-Ebene eines allgemeinen v2-Kontos unterliegt einer Strafe für vorzeitiges Löschen, wenn er vor Ablauf von 30 Tagen gelöscht oder in eine andere Ebene verschoben wird. Diese Gebühr fällt anteilig an. Wenn beispielsweise ein Blob in die Cool-Ebene verschoben und nach 21 Tagen gelöscht wird, wird eine Gebühr für die vorzeitige Löschung erhoben, die 9 (30 minus 21) Tagen entspricht, in denen der Blob in der Cool-Ebene gespeichert war.

Die Hot und Cool Tiers unterstützen alle Redundanzkonfigurationen. Weitere Informationen zu Datenredundanzoptionen in Azure Storage finden Sie unter [Azure Storage-Redundanz](../common/storage-redundancy.md).  

## <a name="archive-access-tier"></a>Zugriffsebene „Archiv“

Die Archivschicht ist eine Offline-Schicht zur Speicherung von Daten, auf die nur selten zugegriffen wird. Die Archivzugriffsebene hat die niedrigsten Speicherkosten, aber höhere Kosten für den Datenabruf und höhere Latenzzeiten im Vergleich zu den Hot- und Cool-Tiers. Beispiele für Nutzungsszenarien für die Zugriffsebene Archiv sind:

- Langfristige Sicherung, sekundäre Sicherung und Archivierungsdatasets
- Originaldaten (Rohdaten), die auch nach der Umwandlung in ein endgültiges verwendbares Format erhalten bleiben müssen
- Compliance- und Archivdaten, die über einen langen Zeitraum gespeichert werden müssen und auf die selten zugegriffen wird

Die Daten müssen sich mindestens 180 Tage lang auf der Archivebene befinden, sonst könnten sie frühzeitig gelöscht werden. Wenn beispielsweise ein Blob in die Archivschicht verschoben und dann nach 45 Tagen gelöscht oder in die Hot-Schicht verschoben wird, wird Ihnen eine Gebühr für die vorzeitige Löschung in Höhe von 135 (180 minus 45) Tagen für die Speicherung dieses Blob in der Archivschicht berechnet.

Solange sich ein Blob auf der Archivebene befindet, kann es nicht gelesen oder geändert werden. Um einen Blob in der Archivebene zu lesen oder herunterzuladen, müssen Sie ihn zunächst in eine Online-Ebene rehydrieren, entweder in die heiße oder die kühle Ebene. Abhängig von der Priorität, die Sie für den Aktivierungsvorgang angeben, kann es bis zu 15 Stunden dauern, bis die Daten auf der Archivebene aktiviert sind. Weitere Informationen zur Blobaktivierung finden Sie unter [Übersicht über die Aktivierung von Blobs aus der Archivebene](archive-rehydrate-overview.md).

Die Metadaten eines archivierten Blobs bleiben für den Lesezugriff verfügbar, sodass Sie das Blob und seine Eigenschaften, Metadaten und Indextags auflisten können. Metadaten für einen Blob in der Archivebene sind schreibgeschützt, während Blob-Indextags gelesen oder geschrieben werden können. Momentaufnahmen werden für archivierte Blobs nicht unterstützt.

Die folgenden Operationen werden für Blobs in der Archivebene unterstützt:

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Delete Blob](/rest/api/storageservices/delete-blob)
- [Suchen nach Blobs anhand von Tags](/rest/api/storageservices/find-blobs-by-tags)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Abrufen von Blobtags](/rest/api/storageservices/get-blob-tags)
- [Auflisten von Blobs](/rest/api/storageservices/list-blobs)
- [Festlegen von Blobtags](/rest/api/storageservices/set-blob-tags)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)

> [!NOTE]
> Die Archivebene wird für ZRS-, GZRS- oder RA-GZRS-Konten nicht unterstützt. Die Migration von LRS zu GRS wird unterstützt, solange keine Blobs in die Archivebene verschoben wurden, während das Konto auf LRS eingestellt war. Die Migration von LRS zu GRS wird unterstützt, solange keine Blobs in die Archivebene verschoben wurden, während das Konto auf LRS eingestellt war.

## <a name="default-account-access-tier-setting"></a>Einstellung für die Standardzugriffsebene eines Kontos

Speicherkonten verfügen über eine Einstellung für die Standardzugriffsebene, mit der die Onlineebene angegeben wird, auf der ein neues Blob erstellt wird. Die Standardeinstellung für die Zugriffsebene kann entweder auf "Heiß" oder "Kühl" eingestellt werden. Sie können die Standardeinstellung für ein einzelnes Blob überschreiben, wenn Sie das Blob hochladen oder die zugehörige Ebene ändern.

Die Standardzugriffsebene für ein neues universelles V2-Speicherkonto ist standardmäßig auf die heiße Ebene festgelegt. Sie können die Einstellung für die Standardzugriffsebene beim oder nach dem Erstellen eines Speicherkontos ändern. Die Standardeinstellung für die Zugriffsebene kann entweder auf "Heiß" oder "Kühl" eingestellt werden.

Für ein Blob, dem keine explizite Ebene zugewiesen ist, wird die zugehörige Ebene von der Einstellung für die Standardzugriffsebene des Kontos abgeleitet. Wenn die Zugriffsebene eines Blobs von der Einstellung für die Standardzugriffsebene des Kontos abgeleitet ist, wird im Azure-Portal als Zugriffsebene **Heiß (abgeleitet)** oder **Kalt (abgeleitet)** angezeigt.

Die Änderung der Einstellung für die Standardzugriffsebene eines Speicherkontos gilt für alle Blobs im Konto, für die keine explizite Zugriffsebene festgelegt ist. Wenn Sie in einem allgemeinen v2-Konto die Standardeinstellung für die Zugriffsebene von "Hot" auf "Cool" umschalten, werden Ihnen die Schreibvorgänge (pro 10.000) für alle Blobs berechnet, für die die Zugriffsebene abgeleitet wird. Wenn Sie in einem allgemeinen v2-Konto von "Cool" auf "Hot" umschalten, werden Ihnen sowohl Lesevorgänge (pro 10.000) als auch Datenabrufe (pro GB) berechnet.

Wenn Sie ein Legacy-Blob-Storage-Konto erstellen, müssen Sie bei der Erstellung die Standardeinstellung für die Zugriffsebene Hot oder Cool festlegen. Das Ändern der Standard-Kontozugriffsebene von "Hot" auf "Cool" in einem älteren Blob Storage-Konto ist kostenlos. Wenn Sie in einem Blob-Storage-Konto von "Cool" auf "Hot" umschalten, werden Ihnen sowohl Lesevorgänge (pro 10.000) als auch Datenabrufe (pro GB) berechnet. Microsoft empfiehlt, wenn möglich allgemeine v2-Speicherkonten anstelle von Blob-Storage-Konten zu verwenden.

> [!NOTE]
> Die Archivebene wird nicht als Standardzugriffsebene für ein Speicherkonto unterstützt.

## <a name="setting-or-changing-a-blobs-tier"></a>Festlegen oder Ändern der Ebene eines Blobs

Um die Ebene eines Blobs beim Erstellen explizit festzulegen, geben Sie sie beim Hochladen des Blobs an.

Nach dem Erstellen eines Blobs können Sie die zugehörige Ebene mit einer der folgenden Methoden ändern:

- Durch Aufrufen des Vorgangs [Set Blob Tier](/rest/api/storageservices/set-blob-tier), entweder direkt oder über eine Richtlinie zur [Lebenszyklusverwaltung](#blob-lifecycle-management). Der Aufruf von [Blobtarif festlegen](/rest/api/storageservices/set-blob-tier) ist in der Regel die beste Option, um die Ebene eines Blobs von einer heißen in eine kältere Ebene zu ändern.
- Durch Aufrufen des Vorgangs [Copy Blob](/rest/api/storageservices/copy-blob) zum Kopieren eines Blobs zwischen Ebenen. Der Aufruf von [Copy Blob](/rest/api/storageservices/copy-blob) wird für die meisten Szenarien empfohlen, in denen Sie einen Blob aus der Archivschicht in eine Online-Schicht rehydrieren oder einen Blob von Cool nach Hot verschieben. Durch Kopieren eines Blobs können Sie die Gebühr für frühzeitiges Löschen vermeiden, wenn das erforderliche Speicherintervall für das Quellblob noch nicht verstrichen ist. Das Kopieren eines Blobs führt jedoch zu Kapazitätsgebühren für zwei Blobs: das Quellblob und das Zielblob.

Der Wechsel der Stufe eines Blobs von Heiß zu Kühl oder Archiv erfolgt sofort, ebenso der Wechsel von Kühl zu Heiß. Die Rehydrierung eines Blob von der Archivebene in die heiße oder kühle Ebene kann bis zu 15 Stunden dauern.

Beachten Sie die folgenden Punkte, wenn Sie einen Blob zwischen den Ebenen Cool und Archive verschieben:

- Wenn die Ebene eines BLOBs auf der Grundlage der Standardzugriffsebene des Speicherkontos als "Cool" abgeleitet wird und der BLOB in die Archivebene verschoben wird, fallen keine Gebühren für die vorzeitige Löschung an.
- Wird ein Blob explizit in die Ebene "Cool" und dann in die Ebene "Archiv" verschoben, fällt die Gebühr für vorzeitiges Löschen an.

In der folgenden Tabelle sind die Ansätze zusammengefasst, die Sie zum Verschieben von Blobs zwischen verschiedenen Ebenen nutzen können.

| Ursprung/Ziel | Heiße Zugriffsebene | Kalte Zugriffsebene | Archivzugriffsebene |
|--|--|--|--|
| **Zugriffsebene „Heiß“** | Nicht zutreffend | Ändern Sie die Ebene eines Blobs mit **Blobtarif festlegen** oder **Blob kopieren** von „Heiß“ in „Kalt“. [Weitere Informationen](manage-access-tier.md)<br /><br />Verschieben Sie Blobs mit einer Richtlinie für die Lebenszyklusverwaltung auf die Ebene „Kalt“. [Weitere Informationen](lifecycle-management-overview.md) | Ändern Sie die Ebene eines Blobs mit **Blobtarif festlegen** oder **Blob kopieren** von „Heiß“ in „Archiv“. [Weitere Informationen](archive-blob.md) <br /><br />Archivieren Sie Blobs mit einer Richtlinie für die Lebenszyklusverwaltung. [Weitere Informationen](lifecycle-management-overview.md) |
| **Zugriffsebene „Kalt“** | Ändern Sie die Ebene eines Blobs mit **Blobtarif festlegen** oder **Blob kopieren** von „Kalt“ in „Heiß“. [Weitere Informationen](manage-access-tier.md) <br /><br />Verschieben Sie Blobs mit einer Richtlinie für die Lebenszyklusverwaltung auf die Ebene „Heiß“. [Weitere Informationen](lifecycle-management-overview.md) | Nicht zutreffend | Ändern Sie die Ebene eines Blobs mit **Blobtarif festlegen** oder **Blob kopieren** von „Kalt“ in „Archiv“. [Weitere Informationen](archive-blob.md) <br /><br />Archivieren Sie Blobs mit einer Richtlinie für die Lebenszyklusverwaltung. [Weitere Informationen](lifecycle-management-overview.md) |
| **Zugriffsebene „Archiv“** | Aktivieren Sie ein Blob mit **Blobtarif festlegen** oder **Blob kopieren** für die Ebene „Heiß“. [Weitere Informationen](archive-rehydrate-to-online-tier.md) | Aktivieren Sie ein Blob mit **Blobtarif festlegen** oder **Blob kopieren** für die Ebene „Kalt“. [Weitere Informationen](archive-rehydrate-to-online-tier.md) | Nicht zutreffend |

## <a name="blob-lifecycle-management"></a>Lebenszyklusverwaltung für Blobs

Die Blob Storage-Lebenszyklusverwaltung umfasst eine regelbasierte Richtlinie, mit der Sie Daten zur gewünschten Zugriffsebene verschieben können, wenn die angegebenen Bedingungen erfüllt sind. Über die Lebenszyklusverwaltung können Sie auch festlegen, dass Daten am Ende ihrer Lebensdauer ablaufen. Weitere Informationen finden Sie unter [Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen](./lifecycle-management-overview.md).

> [!NOTE]
> Daten, die in einem Premium-Block-Blob-Storage-Konto gespeichert sind, können nicht mithilfe von [Set Blob Tier](/rest/api/storageservices/set-blob-tier) oder mithilfe von Azure Blob Storage Lebenszyklusverwaltung in Hot, Cool oder Archive eingestuft werden. Um Daten zu verschieben, müssen Sie Blobs aus dem Blockblob-Speicherkonto synchron in die Hot-Tier eines anderen Kontos kopieren, indem Sie die API [Put Block From URL](/rest/api/storageservices/put-block-from-url) oder eine Version von AzCopy verwenden, die diese API unterstützt. Die **Put Block From URL**-API kopiert Daten synchron auf den Server, was heißt, dass der Aufruf erst abgeschlossen wird, nachdem alle Daten vom ursprünglichen Serverspeicherort an den Zielspeicherort verschoben wurden.

## <a name="summary-of-access-tier-options"></a>Zusammenfassung der Zugriffsebenenoptionen

In der folgenden Tabelle sind die Funktionen der Zugriffsebenen Hot, Cool und Archive zusammengefasst.

|  | **Zugriffsebene „Heiß“** | **Zugriffsebene „Kalt“** | **Zugriffsebene „Archiv“** |
|--|--|--|--|
| **Verfügbarkeit** | 99,9 % | 99 % | Offline |
| **Verfügbarkeit** <br> **(RA-GRS-Lesevorgänge)** | 99,99 % | 99,9 % | Offline |
| **Nutzungsgebühren** | Höhere Speicherkosten, jedoch geringere Zugriffs- und Transaktionskosten | Geringere Speicherkosten, jedoch höhere Zugriffs- und Transaktionskosten | Geringste Speicherkosten, jedoch höchste Zugriffs- und Transaktionskosten |
| **Empfohlene Mindestaufbewahrungsdauer für Daten** | – | 30 Tage<sup>1</sup> | 180 Tage |
| **Latenz** <br> **(Zeit bis zum ersten Byte)** | Millisekunden | Millisekunden | Stunden<sup>2</sup> |
| **Unterstützte Redundanzkonfigurationen** | Alle | Alle | Nur LRS, GRS und RA-GRS<sup>3</sup> |

<sup>1</sup> Objekte der Stufe Cool auf Allzweckkonten v2 haben eine Mindestaufbewahrungsdauer von 30 Tagen. Für Blob-Storage-Konten gibt es keine Mindestaufbewahrungsdauer für die Cool-Stufe.

<sup>2</sup> Bei der Rehydrierung eines Blob aus der Archivebene können Sie entweder eine Standard- oder eine hohe Rehydrierungspriorität wählen. Die Optionen unterscheiden sich in Bezug auf Abrufwartezeiten und Kosten. Weitere Informationen finden Sie unter [Übersicht über die Rehydrierung von Klecksen aus der Archivebene](archive-rehydrate-overview.md).

<sup>3</sup> Weitere Informationen zu Redundanzkonfigurationen in Azure Storage finden Sie unter [Azure Storage-Redundanz](../common/storage-redundancy.md).

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Für alle Speicherkonten wird ein Blockblobspeicher-Preismodell angewandt, das auf der Ebene des jeweiligen Blobs basiert. Beachten Sie die in den folgenden Abschnitten beschriebenen Aspekte der Abrechnung.

Weitere Informationen zu den Preisen für Blockblobs finden Sie unter [Preise für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="storage-capacity-costs"></a>Kosten für die Speicherkapazität

Die Kosten für die Datenspeicherung hängen nicht nur von der gespeicherten Datenmenge ab, sondern auch von der Zugriffsebene. Je „kälter“ die Ebene, desto geringer die Kosten für die Kapazität pro GB.

### <a name="data-access-costs"></a>Kosten für den Datenzugriff

Je „kälter“ die Ebene, desto höher die Gebühren für den Datenzugriff. Für Daten in der Zugriffsstufe Cool und Archive wird eine Gebühr pro Gigabyte für den Lesezugriff erhoben.

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
- Wenn ein Blob auf eine kühlere Schicht (Hot to Cool, Hot to Archive oder Cool to Archive) verschoben wird, wird der Vorgang als Schreibvorgang auf der Zielschicht abgerechnet, wobei die Gebühren für Schreibvorgänge (pro 10.000) und Datenschreiben (pro GB) der Zielschicht gelten.
- Wenn ein Blob in eine wärmere Schicht (Archive to Cool, Archive to Hot oder Cool to Hot) verschoben wird, wird der Vorgang als Lesevorgang aus der Quellschicht abgerechnet, wobei die Gebühren für Lesevorgänge (pro 10.000) und Datenabrufe (pro GB) der Quellschicht gelten. Für alle Blobs, die aus der Ebene „Kalt“ oder „Archiv“ verschoben werden, können auch Gebühren für das frühe Löschen anfallen.
- Während ein Blob aus der Archivschicht rehydriert wird, werden die Daten dieses Blobs als archivierte Daten abgerechnet, bis die Daten wiederhergestellt sind und die Schicht des Blobs auf Hot oder Cool wechselt.

Die folgende Tabelle gibt Aufschluss darüber, wie Ebenenänderungen abgerechnet werden.

| | **Schreibgebühren (Vorgang + Zugriff)** | **Lesegebühren (Vorgang + Zugriff)** |
| ---- | ----- | ----- |
| Vorgang **Set Blob Tier** | Von „Hot“ in „Cool“<br> Heiß zum Archiv<br> Cool zum Archiv | „Archive“ in „Cool“<br> „Archive“ in „Hot“<br> Kühl bis heiß

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
