---
title: Point-in-Time-Wiederherstellung für Blockblobs
titleSuffix: Azure Storage
description: Point-in-Time-Wiederherstellung für Blockblobs bietet Schutz vor versehentlichem Löschen oder Beschädigungen, da Sie ein Speicherkonto zu einem bestimmten Zeitpunkt in seinem vorherigen Zustand wiederherstellen können.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cecfd917146cd11399276ca706ddfa52c12b2c86
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596997"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Point-in-Time-Wiederherstellung für Blockblobs

Point-in-Time-Wiederherstellung bietet Schutz vor versehentlichem Löschen oder Beschädigungen, da Sie Blockblobdaten in einem früheren Zustand wiederherstellen können. Point-in-Time-Wiederherstellung ist in Szenarien nützlich, in denen ein Benutzer oder eine Anwendung versehentlich Daten löscht oder wenn ein Anwendungsfehler Daten beschädigt. Point-in-Time-Wiederherstellung ermöglicht auch Testszenarien, bei denen ein Dataset in einen bekannten Zustand zurückgesetzt werden muss, bevor weitere Tests ausgeführt werden.

Point-in-Time-Wiederherstellung wird nur für Speicherkonten vom Typ „Universell V2“ in der Leistungsstufe „Standard“ unterstützt. Nur Daten der heißen und kalten Zugriffsebene können mit Point-in-Time-Wiederherstellung wiederhergestellt werden.

Informationen zum Aktivieren der Point-in-Time-Wiederherstellung für ein Speicherkonto finden Sie unter [Aktivieren einer Point-in-Time-Wiederherstellung für Blockblobdaten](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Funktionsweise der Point-in-Time-Wiederherstellung

Zum Aktivieren von Point-in-Time-Wiederherstellung erstellen Sie eine Verwaltungsrichtlinie für das Speicherkonto und geben eine Beibehaltungsdauer an. Während der Beibehaltungsdauer können Sie Blockblobs aus dem aktuellen Zustand in einem Zustand zu einem früheren Zeitpunkt wiederherstellen.

Um eine Point-in-Time-Wiederherstellung zu initiieren, rufen Sie den Vorgang [Restore Blob Ranges](/rest/api/storagerp/storageaccounts/restoreblobranges) auf und geben einen Wiederherstellungspunkt in UTC-Zeit an. Sie können lexikografische Bereiche von Container- und Blobnamen, die wiederhergestellt werden sollen, angeben oder den Bereich weglassen, um alle Container im Speicherkonto wiederherzustellen. Pro Wiederherstellungsvorgang werden bis zu 10 lexikografische Bereiche unterstützt.

Azure Storage analysiert alle Änderungen, die an den angegebenen Blobs zwischen dem angeforderten Wiederherstellungspunkt (angegeben in UTC-Zeit) und dem aktuellen Zeitpunkt vorgenommen wurden. Der Wiederherstellungsvorgang ist atomisch, sodass er entweder bei der Wiederherstellung aller Änderungen vollständig erfolgreich ist oder fehlschlägt. Wenn Blobs vorhanden sind, die nicht wiederhergestellt werden können, schlägt der Vorgang fehl, und Lese- und Schreibvorgänge für die betroffenen Container werden fortgesetzt.

Das folgende Diagramm veranschaulicht die Funktionsweise der Zeitpunktwiederherstellung. Mindestens ein Container oder ein Blobbereich wird auf seinen Zustand vor *n* Tagen wiederhergestellt, wobei *n* kleiner oder gleich der Beibehaltungsdauer ist, die für die Zeitpunktwiederherstellung definiert wurde. Der Zweck besteht darin, Schreib- und Löschvorgänge, die während der Beibehaltungsdauer aufgetreten sind, rückgängig zu machen.

:::image type="content" source="media/point-in-time-restore-overview/point-in-time-restore-diagram.png" alt-text="Diagramm der Zeitpunktwiederherstellung von Containern in einen früheren Zustand":::

Nur ein Wiederherstellungsvorgang kann für ein Speicherkonto gleichzeitig ausgeführt werden. Ein Wiederherstellungsvorgang kann nicht abgebrochen werden, sobald er ausgeführt wird. Es kann jedoch ein zweiter Wiederherstellungsvorgang ausgeführt werden, um den ersten Vorgang rückgängig zu machen.

Der Vorgang **Restore Blob Ranges** gibt eine Wiederherstellungs-ID zurück, die den Vorgang eindeutig identifiziert. Um den Status einer Point-in-Time-Wiederherstellung zu überprüfen, wenden Sie den Vorgang **Get Restore Status** mit der Wiederherstellungs-ID an, die vom Vorgang **Restore Blob Ranges** zurückgegeben wird.

> [!IMPORTANT]
> Wenn Sie einen Wiederherstellungsvorgang ausführen, blockiert Azure Storage für die Dauer dieses Vorgangs Datenvorgänge für die Blobs in den Bereichen, die wiederhergestellt werden. Lese-, Schreib- und Löschvorgänge werden am primären Speicherort blockiert. Aus diesem Grund werden Vorgänge wie das Auflisten von Containern im Azure-Portal während des Wiederherstellungsvorgangs möglicherweise nicht erwartungsgemäß ausgeführt.
>
> Lesevorgänge aus dem sekundären Speicherort können während des Wiederherstellungsvorgangs fortgesetzt werden, wenn das Speicherkonto georepliziert wird.

> [!CAUTION]
> Die Zeitpunktwiederherstellung unterstützt Wiederherstellungen für Vorgänge, die nur Blockblobs betroffen haben. Alle Vorgänge, die Container betreffen, können nicht wiederhergestellt werden. Wenn Sie beispielsweise einen Container aus dem Speicherkonto löschen, indem Sie den Vorgang [Delete Container](/rest/api/storageservices/delete-container) (Container löschen) aufrufen, kann dieser Container nicht mit einer Zeitpunktwiederherstellung wiederhergestellt werden. Löschen Sie die einzelnen Blobs, anstatt einen ganzen Container zu löschen, wenn Sie sie möglicherweise später wiederherstellen möchten.

### <a name="prerequisites-for-point-in-time-restore"></a>Voraussetzungen für Point-in-Time-Wiederherstellung

Die Point-in-Time-Wiederherstellung erfordert, dass die folgenden Azure Storage-Features aktiviert sind. Erst danach können Sie die Point-in-Time-Wiederherstellung aktivieren:

- [Vorläufiges Löschen](soft-delete-blob-overview.md)
- [Änderungsfeed](storage-blob-change-feed.md)
- [Blobversionsverwaltung](versioning-overview.md)

Wenn Sie diese Features aktivieren, können zusätzliche Gebühren anfallen. Stellen Sie sicher, dass Sie die Auswirkungen auf die Abrechnung verstanden haben, bevor Sie die Zeitpunktwiederherstellung und die erforderlichen Features aktivieren.

### <a name="retention-period-for-point-in-time-restore"></a>Beibehaltungsdauer für Point-in-Time-Wiederherstellung

Wenn Sie Point-in-Time-Wiederherstellung für ein Speicherkonto aktivieren, geben Sie eine Beibehaltungsdauer an. Blockblobs in Ihrem Speicherkonto können während der Beibehaltungsdauer wieder hergestellt werden.

Die Beibehaltungsdauer beginnt einige Minuten, nachdem Sie Point-in-Time-Wiederherstellung aktivieren. Beachten Sie, dass Sie Blobs nicht in einem Zustand vor Beginn der Beibehaltungsdauer wiederherstellen können. Wenn Sie z. B. Point-in-Time-Wiederherstellung am 1. Mai mit einer Beibehaltungsdauer von 30 Tagen aktiviert haben, können Sie am 15. Mai maximal 15 Tage wiederherstellen. Am 1. Juni können Sie Daten zwischen 1 Tag und 30 Tagen wiederherstellen.

Die Beibehaltungsdauer für Point-in-Time-Wiederherstellung muss mindestens einen Tag weniger als die für vorläufiges Löschen angegebene Beibehaltungsdauer betragen. Wenn die Beibehaltungsdauer für vorläufiges Löschen z. B. auf 7 Tage festgelegt ist, kann die Beibehaltungsdauer für Point-in-Time-Wiederherstellung zwischen 1 Tag und 6 Tagen liegen.

> [!IMPORTANT]
> Die erforderliche Zeit zum Wiederherstellen einer Gruppe von Daten ist von der Anzahl der Schreib- und Löschvorgänge abhängig, die während des Wiederherstellungszeitraums vorgenommen werden. Beispielsweise dauert bei einem Konto mit 1 Million Objekten, in dem pro Tag 3.000 Objekte hinzugefügt und 1.000 Objekte gelöscht werden, die Wiederherstellung zu einem 30 Tage in der Vergangenheit liegenden Zeitpunkt ungefähr zwei Stunden. Bei einem Konto mit einer solchen Änderungsrate sollte weder ein Aufbewahrungszeitraum von mehr als 90 Tagen noch eine Wiederherstellung zu einem so weit in der Vergangenheit liegenden Zeitpunkt angewendet werden.

### <a name="permissions-for-point-in-time-restore"></a>Berechtigungen für Point-in-Time-Wiederherstellung

Um einen Wiederherstellungsvorgang zu initiieren, muss ein Client über Schreibberechtigungen für alle Container im Speicherkonto verfügen. Um Berechtigungen zum Autorisieren eines Wiederherstellungsvorgangs mit Azure Active Directory (Azure AD) zu erteilen, weisen Sie dem Sicherheitsprinzipal auf der Ebene des Speicherkontos, der Ressourcengruppe oder des Abonnements die Rolle **Speicherkontomitwirkender** zu.

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme

Die Point-in-Time-Wiederherstellung für Blockblobs weist die folgenden Einschränkungen und bekannten Probleme auf:

- Nur Blockblobs in einem Standardspeicherkonto vom Typ „Universell V2“ können im Rahmen eines Point-in-Time-Wiederherstellungsvorgangs wiederhergestellt werden. Anfügeblobs, Seitenblobs und Premium-Blockblobs werden nicht wiederhergestellt.
- Wenn Sie während der Beibehaltungsdauer einen Container gelöscht haben, wird dieser Container nicht mit dem Point-in-Time-Wiederherstellungsvorgang wiederhergestellt. Bei dem Versuch, einen Bereich von Blobs wiederherzustellen, der Blobs in einem gelöschten Container beinhaltet, tritt bei der Zeitpunktwiederherstellung ein Fehler auf. Weitere Informationen zum Schutz von Containern vor dem Löschen finden Sie unter [Vorläufiges Löschen für Container](soft-delete-container-overview.md).
- Wenn ein Blob innerhalb des Zeitraums zwischen dem aktuellen Moment und dem Wiederherstellungspunkt zwischen den heißen und kalten Speicherebenen verschoben wurde, wird das Blob auf der vorherigen Ebene wiederhergestellt. Das Wiederherstellen von Blockblobs auf Archivebene wird nicht unterstützt. Wenn ein Blob aus der heißen Speicherebene z. B. vor zwei Tagen in die Archivebene verschoben wurde und ein Wiederherstellungsvorgang für einen Punkt vor drei Tagen erfolgt, wird das Blob nicht in der heißen Zugriffsebene wiederhergestellt. Um ein archiviertes Blob wiederherzustellen, verschieben Sie es zuerst aus der Archivebene. Weitere Informationen finden Sie unter [Übersicht über die Aktivierung von Blobs aus der Archivebene](archive-rehydrate-overview.md).
- Wenn eine Unveränderlichkeitsrichtlinie konfiguriert ist, kann ein Wiederherstellungsvorgang eingeleitet werden, aber alle Blobs, die durch die Unveränderlichkeitsrichtlinie geschützt sind, werden nicht geändert. Ein Wiederherstellungsvorgang führt in diesem Fall nicht zur Wiederherstellung eines konsistenten Zustands zu dem angegebenen Datum und Zeitpunkt.
- Ein Block, der über [Put Block](/rest/api/storageservices/put-block) oder [Put Block from URL](/rest/api/storageservices/put-block-from-url) hochgeladen, aber nicht über [Put Block List](/rest/api/storageservices/put-block-list) committet wurde, ist nicht Teil eines Blobs und wird daher nicht als Teil eines Wiederherstellungsvorgangs wiederhergestellt.
- Ein Blob mit einer aktiven Leasedauer kann nicht wiederhergestellt werden. Wenn ein Blob mit einer aktiven Leasedauer in den Bereich der wiederherzustellenden Blobs eingeschlossen ist, schlägt der Wiederherstellungsvorgang atomisch fehl. Unterbrechen Sie alle aktiven Leases, bevor Sie den Wiederherstellungsvorgang initiieren.
- Momentaufnahmen werden nicht im Rahmen eines Wiederherstellungsvorgangs erstellt oder gelöscht. Nur das Basisblob wird in seinem vorherigen Zustand wiederhergestellt.
- Die Wiederherstellung flachen und hierarchischen Namespaces von Azure Data Lake Storage Gen2 wird nicht unterstützt.

> [!IMPORTANT]
> Wenn Sie Blockblobs an einem Punkt wiederherstellen, der vor dem 22. September 2020 liegt, sind die Einschränkungen der Vorschau für die Point-in-Time-Wiederherstellung wirksam. Microsoft empfiehlt, einen Wiederherstellungspunkt auszuwählen, der gleich ist oder nach dem 22. September 2020 liegt, um die allgemein verfügbare Point-in-Time-Wiederherstellungsfunktion zu nutzen.

## <a name="feature-support"></a>Featureunterstützung

In der folgenden Tabelle wird gezeigt, wie dieses Feature in Ihrem Konto unterstützt wird und welche Auswirkungen die Aktivierung bestimmter Funktionen auf die Unterstützung hat.

| Speicherkontotyp                | Blob Storage (Standardunterstützung)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Standard, Universell V2 | ![Ja](../media/icons/yes-icon.png) |![Nein](../media/icons/no-icon.png)              | ![Nein](../media/icons/no-icon.png) |
| Premium-Blockblobs          | ![Nein](../media/icons/no-icon.png)|![Nein](../media/icons/no-icon.png) | ![Nein](../media/icons/no-icon.png) |

<sup>1</sup>    Für Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) ist ein Speicherkonto mit aktiviertem hierarchischem Namespace erforderlich.

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Zum Aktivieren der Zeitpunktwiederherstellung fallen keine Gebühren an. Allerdings werden beim Aktivieren der Zeitpunktwiederherstellung auch die Blobversionsverwaltung, das vorläufige Löschen und der Änderungsfeed aktiviert, für die jeweils zusätzliche Gebühren anfallen können.

Die Abrechnung für Point-in-Time-Wiederherstellung hängt von der Menge der für die Ausführung des Wiederherstellungsvorgangs verarbeiteten Daten ab. Die Menge der verarbeiteten Daten basiert auf der Anzahl von Änderungen, die zwischen dem Wiederherstellungspunkt und dem aktuellen Zeitpunkt aufgetreten sind. Geht man beispielsweise von einer relativ konstanten Änderungsrate für Blockblobdaten in einem Speicherkonto aus, würde ein Wiederherstellungsvorgang, der 1 Tag in der Zeit zurückliegt, ein Zehntel einer Wiederherstellung kosten, die 10 Tage in der Zeit zurückliegt.

Wenn Sie die Kosten für einen Wiederherstellungsvorgang schätzen möchten, überprüfen Sie das Änderungsfeedprotokoll, um die Menge der Daten zu schätzen, die während des Wiederherstellungszeitraums geändert wurden. Wenn die Beibehaltungsdauer für den Änderungsfeed beispielsweise 30 Tage und die Größe des Änderungsfeeds 10 MB beträgt, würde die Wiederherstellung bis zu einem Zeitpunkt von 10 Tagen in der Vergangegheit ungefähr ein Drittel des Preises für ein LRS-Konto in dieser Region betragen. Die Wiederherstellung bis zu einem Punkt, der 27 Tage früher liegt, würde ungefähr neun Zehntel des aufgeführten Preises kosten.

Weitere Informationen zu den Preisen für Point-in-Time-Wiederherstellung finden Sie unter [Preise für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="next-steps"></a>Nächste Schritte

- [Durchführen einer Point-in-Time-Wiederherstellung von Blockblobdaten](point-in-time-restore-manage.md)
- [Unterstützung für Änderungsfeeds in Azure Blob Storage](storage-blob-change-feed.md)
- [Aktivieren von „Vorläufiges Löschen“ für Blobs](./soft-delete-blob-enable.md)
- [Aktivieren und Verwalten der Blobversionsverwaltung](versioning-enable.md)
