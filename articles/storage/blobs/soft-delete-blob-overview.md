---
title: Vorläufiges Löschen für Blobs
titleSuffix: Azure Storage
description: Vorläufiges Löschen für Blobs schützt Ihre Daten, damit Sie sie leichter wiederherstellen können, wenn sie irrtümlich von einer Anwendung oder einem anderen Benutzer des Speicherkontos geändert oder gelöscht wurden.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1c645e50a49a268d762b1195e91484783a73367c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562548"
---
# <a name="soft-delete-for-blobs"></a>Vorläufiges Löschen für Blobs

Das Feature für das vorläufige Löschen von Blobs schützt einzelne Blobs, Momentaufnahmen oder Versionen vor einer versehentlichen Löschung oder Überschreibung, weil die gelöschten Daten für einen angegebenen Zeitraum im System beibehalten werden. Während des Aufbewahrungszeitraums können Sie den Zustand eines vorläufig gelöschten Objekts zum Zeitpunkt der Löschung wiederherstellen. Nach Ablauf des Aufbewahrungszeitraums wird das Objekt endgültig gelöscht.

> [!IMPORTANT]
> Das vorläufige Löschen in Konten, für die die hierarchische Namespacefunktion aktiviert ist, befindet sich derzeit in der VORSCHAUPHASE und ist global in allen Azure-Regionen verfügbar.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>
>
> Wie Sie sich für die Vorschau registrieren, erfahren Sie in [diesem Formular](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u).

## <a name="recommended-data-protection-configuration"></a>Empfohlene Datenschutzkonfiguration

Das vorläufige Löschen von Blobs ist Teil einer umfassenden Datenschutzstrategie für Blobdaten. Microsoft empfiehlt die Aktivierung folgender Datenschutzfeatures, um einen optimalen Schutz Ihrer Blobdaten zu gewährleisten:

- Vorläufiges Löschen für Container, um einen gelöschten Container wiederherzustellen. Informationen zum Aktivieren des vorläufigen Löschens für Container finden Sie unter [Aktivieren und Verwalten des vorläufigen Löschens für Container (Vorschau)](soft-delete-container-enable.md).
- Blobversionsverwaltung zum automatischen Verwalten früherer Versionen eines Blobs. Wenn Blobversionsverwaltung aktiviert ist, können Sie eine frühere Version eines Blobs wiederherstellen, um Daten wiederherzustellen, wenn diese irrtümlich geändert oder gelöscht wurden. Informationen zum Aktivieren der Blobversionsverwaltung finden Sie unter [Aktivieren und Verwalten der Blobversionsverwaltung (Vorschau)](versioning-enable.md).
- Vorläufiges Löschen von Blobs, um ein gelöschtes Blob, eine gelöschte Momentaufnahme oder eine gelöschte Version wiederherzustellen. Informationen zum Aktivieren des vorläufigen Löschens von Blobs finden Sie unter [Aktivieren und Verwalten des vorläufigen Löschens von Blobs](soft-delete-blob-enable.md).

Weitere Informationen zu den Datenschutzempfehlungen von Microsoft finden Sie in der [Übersicht zum Datenschutz](data-protection-overview.md).

## <a name="how-blob-soft-delete-works"></a>Funktionsweise des vorläufigen Löschens

Wenn Sie das vorläufige Löschen von Blobs für ein Speicherkonto aktivieren, geben Sie einen Aufbewahrungszeitraum zwischen 1 und 365 Tagen für gelöschte Objekte an. Die Beibehaltungsdauer gibt an, wie lange die Daten verfügbar bleiben, nachdem sie gelöscht oder überschrieben wurden. Der Aufbewahrungszeitraum beginnt, sobald ein Objekt gelöscht oder überschrieben wurde.

Innerhalb des Aufbewahrungszeitraums können Sie ein gelöschtes Blob gemeinsam mit den zugehörigen Momentaufnahmen oder einer gelöschten Version wiederherstellen, indem Sie den Vorgang [Undelete Blob](/rest/api/storageservices/undelete-blob) aufrufen. Das folgende Diagramm zeigt, wie ein gelöschtes Objekt wiederhergestellt werden kann, wenn das vorläufige Löschen von Blobs aktiviert ist:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Diagramm der Wiederherstellung eines vorläufig gelöschten Blobs":::

Sie können die Beibehaltungsdauer beim vorläufigen Löschen jederzeit ändern. Die aktualisierte Beibehaltungsdauer gilt nur für Daten, die nach der Änderung des Aufbewahrungszeitraums gelöscht wurden. Alle Daten, die vor der Änderung des Aufbewahrungszeitraums gelöscht wurden, unterliegen der Beibehaltungsdauer, die zum Zeitpunkt der Löschung wirksam war.

Der Versuch, ein vorläufig gelöschtes Objekt zu löschen, wirkt sich nicht auf dessen Ablaufzeit aus.

Wenn Sie das vorläufige Löschen von Blobs deaktivieren, können Sie so lange weiterhin auf vorläufig gelöschte Objekte in Ihrem Speicherkonto zugreifen und diese wiederherstellen, bis der Aufbewahrungszeitraum für vorläufiges Löschen abgelaufen ist.

Die Blobversionsverwaltung ist für Speicherkonten vom Typ „Universell V2“, Blockblobs und Blobs verfügbar. Speicherkonten mit einem hierarchischen Namespace werden derzeit nicht unterstützt.

Version 2017-07-29 und höher der Azure Storage-REST-API unterstützt das vorläufige Löschen von Blobs.

> [!IMPORTANT]
> Sie können das vorläufige Löschen von Blobs nur verwenden, um einzelne Blobs, Momentaufnahmen, Verzeichnisse (in einem hierarchischen Namespace) oder Versionen wiederherzustellen. Zum Wiederherstellen eines Containers und seiner Inhalte muss für das Speicherkonto auch das vorläufige Löschen von Containern aktiviert werden. Microsoft empfiehlt, das vorläufige Löschen von Containern und die Blobversionsverwaltung zusammen mit dem vorläufigen Löschen von Blobs zu aktivieren, um einen vollständigen Schutz der Blobdaten zu gewährleisten. Weitere Informationen finden Sie unter [Übersicht zum Datenschutz](data-protection-overview.md).
>
> Das vorläufige Löschen von Blobs bietet keinen Schutz vor der Löschung eines Speicherkontos. Um ein Speicherkonto vor dem Löschen zu schützen, konfigurieren Sie eine Sperre für die Speicherkontoressource. Weitere Informationen zum Sperren eines Speicherkontos finden Sie unter [Anwenden einer Azure Resource Manager-Sperre auf ein Speicherkonto](../common/lock-account-resource.md).

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>Behandlung von Löschungen bei aktiviertem vorläufigen Löschen

Wenn das vorläufige Löschen von Blobs aktiviert ist, führt das Löschen eines Blobs dazu, dass dieser als vorläufig gelöscht markiert wird. Es wird keine Momentaufnahme erstellt. Wenn der Aufbewahrungszeitraum abläuft, wird das vorläufig gelöschte Blob dauerhaft gelöscht.

Ein Blob mit Momentaufnahmen kann nicht gelöscht werden, es sei denn, die Momentaufnahmen werden ebenfalls gelöscht. Wenn Sie ein Blob und seine Momentaufnahmen löschen, werden das Blob und die Momentaufnahmen als vorläufig gelöscht markiert. Es werden keine neuen Momentaufnahmen erstellt.

Sie können auch eine oder mehrere aktive Momentaufnahmen löschen, ohne das Basisblob zu löschen. In diesem Fall wird die Momentaufnahme vorläufig gelöscht.

Wenn ein Verzeichnis in einem Konto gelöscht wird, für das ein hierarchischer Namespace aktiviert ist, werden das Verzeichnis und sein gesamter Inhalt als vorläufig gelöscht markiert.

Vorläufig gelöschte Objekte sind nicht sichtbar, es sei denn, sie werden explizit angezeigt oder aufgelistet. Weitere Informationen zum Auflisten vorläufig gelöschter Objekte finden Sie unter [Verwalten und Wiederherstellen vorläufig gelöschter Blobs](soft-delete-blob-manage.md).

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>Behandlung von Überschreibungen bei aktiviertem vorläufigen Löschen

> [!IMPORTANT]
> Dieser Abschnitt gilt nicht für Konten mit einem hierarchischen Namespace.

Durch das Aufrufen eines Vorgangs, wie z. B. [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) oder [Copy Blob](/rest/api/storageservices/copy-blob), werden die Daten in einem Blob überschrieben. Wenn das vorläufige Löschen von Blobs aktiviert ist, wird durch das Überschreiben eines Blobs automatisch eine vorläufig gelöschte Momentaufnahme des Blobzustands vor dem Schreibvorgang erstellt. Wenn der Aufbewahrungszeitraum abläuft, wird die vorläufig gelöschte Momentaufnahme dauerhaft gelöscht.

Vorläufig gelöschte Momentaufnahmen sind nicht sichtbar, es sei denn, sie werden explizit angezeigt oder aufgelistet. Weitere Informationen zum Auflisten vorläufig gelöschter Objekte finden Sie unter [Verwalten und Wiederherstellen vorläufig gelöschter Blobs](soft-delete-blob-manage.md).

Um einen Kopiervorgang zu schützen, muss das vorläufige Löschen von Blobs für das Zielspeicherkonto aktiviert werden.

Das vorläufige Löschen von Blobs bietet keinen Schutz vor dem Überschreiben von Blobmetadaten oder -eigenschaften. Es wird keine vorläufig gelöschte Momentaufnahme erstellt, wenn die Metadaten oder Eigenschaften eines Blobs aktualisiert werden.

Das vorläufige Löschen von Blobs bietet keinen Schutz vor dem Überschreiben von Blobs in der Archivebene. Wenn ein Blob in der Archivebene durch ein neues Blob in einer beliebigen Ebene überschrieben wird, wird das überschriebene Blob dauerhaft gelöscht.

Bei Storage Premium-Konten werden vorläufig gelöschte Momentaufnahmen nicht auf das Limit von 100 Momentaufnahmen pro Blob angerechnet.

### <a name="restoring-soft-deleted-objects"></a>Wiederherstellen vorläufig gelöschter Objekte

Sie können vorläufig gelöschte Blobs oder Verzeichnisse (in einem hierarchischen Namespace) wiederherstellen, indem Sie innerhalb des Aufbewahrungszeitraums den Vorgang [Undelete Blob](/rest/api/storageservices/undelete-blob) (Blob wiederherstellen) aufrufen. Der Vorgang **Undelete Blob** stellt ein Blob und alle ihm zugeordneten vorläufig gelöschten Momentaufnahmen wieder her. Es werden alle Momentaufnahmen wiederhergestellt, die während des Aufbewahrungszeitraums gelöscht wurden.

In Konten mit einem hierarchischen Namespace kann der Vorgang **Undelete Blob** (Blob wiederherstellen) auch verwendet werden, um ein vorläufig gelöschtes Verzeichnis mit seinem gesamten Inhalt wiederherzustellen. Wenn Sie ein Verzeichnis umbenennen, das vorläufig gelöschte Blobs enthält, werden diese vom Verzeichnis getrennt. Wenn Sie diese Blobs wiederherstellen möchten, müssen Sie den Namen des Verzeichnisses wieder auf seinen ursprünglichen Namen zurücksetzen oder ein separates Verzeichnis mit dem ursprünglichen Verzeichnisnamen erstellen. Andernfalls erhalten Sie einen Fehler, wenn Sie versuchen, diese vorläufig gelöschten Blobs wiederherzustellen.

Wenn Sie **Undelete Blob** für ein Blob aufrufen, das nicht vorläufig gelöscht wurde, werden alle vorläufig gelöschten Momentaufnahmen wiederhergestellt, die dem Blob zugeordnet sind. Wenn das Blob keine Momentaufnahmen umfasst und nicht vorläufig gelöscht wurde, hat das Aufrufen von **Undelete Blob** keine Auswirkung.

Um eine vorläufig gelöschte Momentaufnahme zum Basisblob hochzustufen, müssen Sie zunächst **Undelete Blob** für das Basisblob aufrufen, um das Blob und die zugehörigen Momentaufnahmen wiederherzustellen. Kopieren Sie anschließend die gewünschte Momentaufnahme über das Basisblob. Sie können die Momentaufnahme auch in ein neues Blob kopieren.

Daten in einem vorläufig gelöschten Blob oder einer Momentaufnahme können erst gelesen werden, nachdem das Objekt wiederhergestellt wurde.

Weitere Informationen zum Wiederherstellen vorläufig gelöschter Objekte finden Sie unter [Verwalten und Wiederherstellen vorläufig gelöschter Blobs](soft-delete-blob-manage.md).

## <a name="blob-soft-delete-and-versioning"></a>Vorläufiges Löschen von Blobs und Blobversionsverwaltung

> [!IMPORTANT]
> Für Konten mit einem hierarchischen Namespace wird keine Versionsverwaltung unterstützt.

Wenn die Blobversionsverwaltung und das vorläufige Löschen von Blobs für ein Speicherkonto aktiviert sind, wird durch das Überschreiben eines Blobs automatisch eine neue Version erstellt. Die neue Version wird nicht vorläufig gelöscht und nicht entfernt, wenn die Beibehaltungsdauer für vorläufiges Löschen abläuft. Es werden keine vorläufig gelöschten Momentaufnahmen erstellt. Wenn Sie ein Blob löschen, wird die aktuelle Version des Blobs zu einer vorherigen Version, und es gibt keine aktuelle Version mehr. Es werden keine neue Version und auch keine vorläufig gelöschten Momentaufnahmen erstellt.

Durch das Aktivieren von vorläufigem Löschen und Versionsverwaltung werden Blobversionen vor einer Löschung geschützt. Wenn das vorläufige Löschen aktiviert ist, wird durch das Löschen einer Version eine vorläufig gelöschte Version erstellt. Sie können den Vorgang **Undelete Blob** (Blob wiederherstellen) verwenden, um vorläufig gelöschte Versionen innerhalb des Aufbewahrungszeitraums für vorläufig gelöschte Ressourcen wiederherzustellen. Durch den Vorgang **Undelete Blob** (Blob wiederherstellen) werden immer alle vorläufig gelöschten Versionen des Blobs wiederhergestellt. Es ist nicht möglich, nur eine einzelne vorläufig gelöschte Version wiederherzustellen.

Nachdem die Beibehaltungsdauer für vorläufiges löschen abgelaufen ist, werden alle vorläufig gelöschten Blobversionen dauerhaft gelöscht.

> [!NOTE]
> Wenn der Vorgang **Undelete Blob** für ein gelöschtes Blob bei aktivierter Versionsverwaltung aufgerufen wird, werden alle vorläufig gelöschten Versionen oder Momentaufnahmen wiederhergestellt, nicht jedoch die aktuelle Version. Wenn Sie die aktuelle Version wiederherstellen möchten, stufen Sie eine vorherige Version höher, indem Sie sie in die aktuelle Version kopieren.

Microsoft empfiehlt, sowohl die Versionsverwaltung als auch das vorläufige Löschen von Blobs für Ihre Speicherkonten zu aktivieren, damit Ihre Daten optimal geschützt sind. Weitere Informationen zur gemeinsamen Verwendung von Blobversionsverwaltung und vorläufigem Löschen finden Sie unter [Blobversionsverwaltung und vorläufiges Löschen](versioning-overview.md#blob-versioning-and-soft-delete).

## <a name="blob-soft-delete-protection-by-operation"></a>Schutz durch vorläufiges Löschen von Blobs nach Vorgang

In der folgenden Tabelle wird das erwartete Verhalten für Lösch- und Schreibvorgänge mit und ohne Blobversionsverwaltung beschrieben, wenn das vorläufige Löschen von Blobs aktiviert ist.

### <a name="storage-account-no-hierarchical-namespace"></a>Speicherkonto (kein hierarchischer Namespace)

| REST-API-Vorgänge | Vorläufiges Löschen aktiviert | Vorläufiges Löschen und Versionsverwaltung aktiviert |
|--|--|--|
| [Delete Storage Account](/rest/api/storagerp/storageaccounts/delete) | Keine Änderung. Container und Blobs im gelöschten Konto können nicht wiederhergestellt werden. | Keine Änderung. Container und Blobs im gelöschten Konto können nicht wiederhergestellt werden. |
| [Delete Container](/rest/api/storageservices/delete-container) | Keine Änderung. Blobs im gelöschten Container können nicht wiederhergestellt werden. | Keine Änderung. Blobs im gelöschten Container können nicht wiederhergestellt werden. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | Wenn der Aufruf zum Löschen eines Blobs erfolgt, wird dieses Blob als „vorläufig gelöscht“ markiert. <br /><br /> Bei Verwendung zum Löschen einer Blobmomentaufnahme wird die Momentaufnahme als vorläufig gelöscht markiert. | Bei Verwendung zum Löschen eines Blobs wird die aktuelle Version zu einer früheren Version, und die aktuelle Version wird gelöscht. Es werden keine neue Version und auch keine vorläufig gelöschten Momentaufnahmen erstellt.<br /><br /> Bei Verwendung zum Löschen einer Blobversion wird die Version als vorläufig gelöscht markiert. |
| [Wiederherstellen von Blobs](/rest/api/storageservices/undelete-blob) | Stellt ein Blob und alle Momentaufnahmen wieder her, die innerhalb des Aufbewahrungszeitraums gelöscht wurden. | Stellt ein Blob und alle Versionen wieder her, die innerhalb des Aufbewahrungszeitraums gelöscht wurden. |
| [Put Blob](/rest/api/storageservices/put-blob)<br />[Put Block List](/rest/api/storageservices/put-block-list)<br />[Copy Blob](/rest/api/storageservices/copy-blob)<br />[Copy Blob from URL](/rest/api/storageservices/copy-blob) | Bei Aufruf für ein aktives Blob wird automatisch eine Momentaufnahme des Blobzustands vor dem Vorgang generiert. <br /><br /> Bei Aufruf für ein vorläufig gelöschtes Blob wird nur dann eine Momentaufnahme des vorherigen Blobzustands generiert, wenn eine Ersetzung durch ein Blob desselben Typs durchgeführt wird. Wenn es sich um ein Blob eines anderen Typs handelt, werden alle vorhandenen vorläufig gelöschten Daten dauerhaft gelöscht. | Es wird automatisch eine neue Version generiert, die den Zustand des Blobs vor dem Vorgang erfasst. |
| [Put Block](/rest/api/storageservices/put-block) | Bei Verwendung zum Commit eines Blocks in ein aktives Blob erfolgt keine Änderung.<br /><br />Bei Verwendung zum Commit eines Blocks in ein vorläufig gelöschtes Blob wird ein neues Blob erstellt, und es wird automatisch eine Momentaufnahme generiert, um den Zustand des vorläufig gelöschten Blobs zu erfassen. | Keine Änderung. |
| [Put Page](/rest/api/storageservices/put-page)<br />[Put Page from URL](/rest/api/storageservices/put-page-from-url) | Keine Änderung. Seitenblobdaten, die mit diesem Vorgang überschrieben oder gelöscht werden, werden nicht gespeichert und können nicht wiederhergestellt werden. | Keine Änderung. Seitenblobdaten, die mit diesem Vorgang überschrieben oder gelöscht werden, werden nicht gespeichert und können nicht wiederhergestellt werden. |
| [Append Block](/rest/api/storageservices/append-block)<br />[Append Block from URL](/rest/api/storageservices/append-block-from-url) | Keine Änderung. | Keine Änderung. |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) | Keine Änderung. Überschriebene Blobeigenschaften können nicht wiederhergestellt werden. | Keine Änderung. Überschriebene Blobeigenschaften können nicht wiederhergestellt werden. |
| [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) | Keine Änderung. Überschriebene Blobmetadaten können nicht wiederhergestellt werden. | Es wird automatisch eine neue Version generiert, die den Zustand des Blobs vor dem Vorgang erfasst. |
| [Set Blob Tier](/rest/api/storageservices/set-blob-tier) | Das Basisblob wird in die neue Ebene verschoben. Alle aktiven oder vorläufig gelöschten Momentaufnahmen verbleiben in der ursprünglichen Ebene. Es wird keine vorläufig gelöschte Momentaufnahme erstellt. | Das Basisblob wird in die neue Ebene verschoben. Alle aktiven oder vorläufig gelöschten Versionen verbleiben in der ursprünglichen Ebene. Es wird keine neue Version erstellt. |

### <a name="storage-account-hierarchical-namespace"></a>Speicherkonto (hierarchischer Namespace)

|**REST-API-Vorgang**|**Vorläufiges Löschen aktiviert**|
|---|---|
|[Pfad: Löschen](/rest/api/storageservices/datalakestoragegen2/path/delete) |Ein vorläufig gelöschtes Blob oder Verzeichnis wird erstellt. Das vorläufig gelöschte Objekt wird nach dem Aufbewahrungszeitraum gelöscht.|
|[Delete Blob](/rest/api/storageservices/delete-blob)|Ein vorläufig gelöschtes Objekt wird erstellt. Das vorläufig gelöschte Objekt wird nach dem Aufbewahrungszeitraum gelöscht. Das vorläufige Löschen wird für Blobs mit Momentaufnahmen und für Momentaufnahmen nicht unterstützt.|
|[Pfad: Erstellen](/rest/api/storageservices/datalakestoragegen2/path/create) für das Umbenennen eines Blobs oder Verzeichnisses | Das vorhandene Zielblob oder leere Verzeichnis wird vorläufig gelöscht und durch die Quelle ersetzt. Das vorläufig gelöschte Objekt wird nach dem Aufbewahrungszeitraum gelöscht.|

## <a name="feature-support"></a>Featureunterstützung

In der folgenden Tabelle wird gezeigt, wie dieses Feature in Ihrem Konto unterstützt wird und welche Auswirkungen die Aktivierung bestimmter Funktionen auf die Unterstützung hat.

| Speicherkontotyp                | Blob Storage (Standardunterstützung)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Standard, Universell V2 | ![Ja](../media/icons/yes-icon.png) |![Ja](../media/icons/yes-icon.png) <sup>2</sup> <sup>3</sup>            | ![Nein](../media/icons/no-icon.png) |
| Premium-Blockblobs          | ![Ja](../media/icons/yes-icon.png) |![Ja](../media/icons/yes-icon.png) <sup>2</sup> <sup>3</sup>            | ![Nein](../media/icons/no-icon.png) |

<sup>1</sup>    Für Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) ist ein Speicherkonto mit aktiviertem hierarchischem Namespace erforderlich.

<sup>2</sup>    Die Funktion wird auf der Vorschauebene unterstützt.

<sup>3</sup> Weitere Informationen finden Sie unter [Bekannte Probleme mit Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md). Diese Probleme gelten für alle Konten, für die das Feature für hierarchische Namespaces aktiviert ist.

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Alle vorläufig gelöschten Daten werden mit dem gleichen Tarif wie aktive Daten in Rechnung gestellt. Für Daten, die nach Ablauf der Beibehaltungsdauer dauerhaft gelöscht werden, fallen keine Kosten an.

Wenn Sie das vorläufige Löschen aktivieren, empfiehlt Microsoft, eine kurze Beibehaltungsdauer zu verwenden, um besser nachvollziehen zu können, wie sich das Feature auf Ihre Rechnung auswirkt. Die empfohlene Mindestaufbewahrungsdauer beträgt sieben Tage.

Das Aktivieren des vorläufigen Löschens für häufig überschriebene Daten kann zu höheren Gebühren für die Speicherkapazität und einer höheren Latenz beim Auflisten der Blobs führen. Sie können diese zusätzliche(n) Kosten und Latenz umgehen, indem Sie häufig überschriebene Daten in einem separaten Speicherkonto speichern, in dem das vorläufige Löschen deaktiviert ist.

Transaktionen im Zusammenhang mit dem automatischen Generieren von Momentaufnahmen oder Versionen werden nicht in Rechnung gestellt, wenn ein Blob überschrieben oder gelöscht wird. Aufrufe des Vorgangs **Undelete Blob** werden Ihnen zum Transaktionstarif für Schreibvorgänge in Rechnung gestellt.

Weitere Informationen zu den Preisen für Blob Storage finden Sie in der [Preisübersicht für Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>Vorläufiges Löschen von Blobs und VM-Datenträgern

Das Feature für vorläufiges Löschen ist sowohl für Premium-Datenträger als auch für nicht verwaltete Standard-Datenträger verfügbar, bei denen es sich im Grunde um Seitenblobs handelt. Über das vorläufige Löschen können Sie Daten wiederherstellen, die mit den Vorgängen [Delete Blob](/rest/api/storageservices/delete-blob), [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) und [Copy Blob](/rest/api/storageservices/copy-blob) gelöscht oder überschrieben wurden.

Daten, die durch einen Aufruf von [Put Page](/rest/api/storageservices/put-page) überschrieben wurden, können nicht wiederhergestellt werden. Ein virtueller Azure-Computer schreibt auf einen nicht verwalteten Datenträger mithilfe von Aufrufen von [Put Page](/rest/api/storageservices/put-page). Daher wird das vorläufige Löschen von Schreibvorgängen auf einem nicht verwalteten Datenträger von einer Azure-VM aus nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren von „Vorläufiges Löschen“ für Blobs](./soft-delete-blob-enable.md)
- [Verwalten und Wiederherstellen vorläufig gelöschter Blobs](soft-delete-blob-manage.md)
- [Blobversionsverwaltung](versioning-overview.md)
