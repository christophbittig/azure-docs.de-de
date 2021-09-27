---
title: Vorläufiges Löschen für Container
titleSuffix: Azure Storage
description: Vorläufiges Löschen für Container schützt Ihre Daten, damit Sie sie leichter wiederherstellen können, wenn sie irrtümlich von einer Anwendung oder einem anderen Speicherkontobenutzer geändert oder gelöscht wurden.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 51aa58bf3f3c0d672d90b70301f84306e09e06e9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585702"
---
# <a name="soft-delete-for-containers"></a>Vorläufiges Löschen für Container

Das vorläufige Löschen von Containern schützt Ihre Daten vor versehentlichem Löschen, indem die gelöschten Daten für einen bestimmten Zeitraum im System beibehalten werden. Während des Aufbewahrungszeitraums können Sie den Zustand eines vorläufig gelöschten Containers und seinen Inhalt zum Zeitpunkt seiner Löschung wiederherstellen. Nachdem der Aufbewahrungszeitraum abgelaufen ist, werden der Container und sein Inhalt dauerhaft gelöscht.

## <a name="recommended-data-protection-configuration"></a>Empfohlene Datenschutzkonfiguration

Das vorläufige Löschen von Blobs ist Teil einer umfassenden Datenschutzstrategie für Blobdaten. Microsoft empfiehlt die Aktivierung folgender Datenschutzfeatures, um einen optimalen Schutz Ihrer Blobdaten zu gewährleisten:

- Vorläufiges Löschen für Container, um einen gelöschten Container wiederherzustellen. Informationen zum Aktivieren des vorläufigen Löschens für Container finden Sie unter [Aktivieren und Verwalten des vorläufigen Löschens für Container (Vorschau)](soft-delete-container-enable.md).
- Blobversionsverwaltung zum automatischen Verwalten früherer Versionen eines Blobs. Wenn Blobversionsverwaltung aktiviert ist, können Sie eine frühere Version eines Blobs wiederherstellen, um Daten wiederherzustellen, wenn diese irrtümlich geändert oder gelöscht wurden. Informationen zum Aktivieren der Blobversionsverwaltung finden Sie unter [Aktivieren und Verwalten der Blobversionsverwaltung (Vorschau)](versioning-enable.md).
- Vorläufiges Löschen von Blobs, um ein gelöschtes Blob, eine gelöschte Momentaufnahme oder eine gelöschte Version wiederherzustellen. Informationen zum Aktivieren des vorläufigen Löschens von Blobs finden Sie unter [Aktivieren und Verwalten des vorläufigen Löschens von Blobs](soft-delete-blob-enable.md).

Weitere Informationen zu den Datenschutzempfehlungen von Microsoft finden Sie in der [Übersicht zum Datenschutz](data-protection-overview.md).

## <a name="how-container-soft-delete-works"></a>Funktionsweise des vorläufigen Löschens von Containern

Wenn Sie das vorläufige Löschen von Containern aktivieren, können Sie einen Aufbewahrungszeitraum für gelöschte Container zwischen 1 und 365 Tagen angeben. Der Standardaufbewahrungszeitraum beträgt sieben Tage. Während des Aufbewahrungszeitraums können Sie einen gelöschten Container wiederherstellen, indem Sie den Vorgang **Restore Container** (Container wiederherstellen) aufrufen.

Wenn Sie einen Container wiederherstellen, werden auch die Blobs im Container und alle Blobversionen und Momentaufnahmen wiederhergestellt. Sie können das vorläufige Löschen von Containern jedoch nur dann zur Wiederherstellung von Blobs verwenden, wenn der Container selbst gelöscht wurde. Um einen gelöschten Blob wiederherzustellen, wenn sein übergeordneter Container nicht gelöscht wurde, müssen Sie mit dem vorläufigen Löschen oder der Versionsverwaltung von Blobs arbeiten.

> [!WARNING]
> Beim vorläufigen Löschen von Containern können nur vollständige Container und deren Inhalte wiederhergestellt werden, die zum Zeitpunkt der Löschung in diesen enthalten waren. Es ist nicht möglich, mithilfe des vorläufigen Löschens von Containern ein gelöschtes Blob in einem Container wiederherzustellen. Microsoft empfiehlt außerdem das Aktivieren von vorläufigem Löschen von Blobs und Blobversionierung zum Schutz einzelner Blobs in einem Container.
>
> Wenn Sie einen Container wiederherstellen, müssen Sie ihn unter seinem ursprünglichen Namen wiederherstellen. Wenn der ursprüngliche Name zum Erstellen eines neuen Containers verwendet wurde, können Sie den vorläufig gelöschten Container nicht wiederherstellen.

Das folgende Diagramm zeigt, wie ein gelöschter Container wiederhergestellt werden kann, wenn das vorläufige Löschen von Containern aktiviert ist:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagramm der Wiederherstellung eines vorläufig gelöschten Containers":::

Nach Ablauf des Aufbewahrungszeitraums wird der Container endgültig aus Azure Storage gelöscht und kann nicht wiederhergestellt werden. Der Aufbewahrungszeitraum beginnt zum Zeitpunkt der Löschung des Containers. Sie können den Aufbewahrungszeitraum jederzeit ändern. Denken Sie jedoch daran, dass ein aktualisierter Aufbewahrungszeitraum nur für neu gelöschte Container gilt. Zuvor gelöschte Container werden basierend auf dem Aufbewahrungszeitraum endgültig gelöscht, der zum Zeitpunkt der Löschung dieser Container festgelegt war.

Das Deaktivieren des vorläufigen Löschens von Containern führt nicht zur endgültigen Löschung der Container, die zuvor vorläufig gelöscht wurden. Vorläufig gelöschte Container werden nach Ablauf des Aufbewahrungszeitraums endgültig gelöscht, der zum Zeitpunkt der Löschung dieser Container festgelegt war.

Das vorläufige Löschen von Containern ist für die folgenden Typen von Speicherkonten verfügbar:

- Speicherkonten des Typs „Universell v2“ und „Universell v1“
- Blockblob-Speicherkonten
- Blob-Speicherkonten

Speicherkonten mit einem hierarchischen Namespace, die für die Verwendung mit Azure Data Lake Storage Gen2 aktiviert sind, werden ebenfalls unterstützt.

Version 2019-12-12 oder höher der Azure Storage-REST-API unterstützt das vorläufige Löschen von Containern.

> [!IMPORTANT]
> Vorläufiges Löschen schützt bei Containern nicht vor dem Löschen eines Speicherkontos, sondern nur vor dem Löschen von Containern in diesem Konto. Um ein Speicherkonto vor dem Löschen zu schützen, konfigurieren Sie eine Sperre für die Speicherkontoressource. Weitere Informationen zum Sperren von Azure Resource Manager-Ressourcen finden Sie unter [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../../azure-resource-manager/management/lock-resources.md).

## <a name="feature-support"></a>Featureunterstützung

In der folgenden Tabelle wird gezeigt, wie dieses Feature in Ihrem Konto unterstützt wird und welche Auswirkungen die Aktivierung bestimmter Funktionen auf die Unterstützung hat.

| Speicherkontotyp                | Blob Storage (Standardunterstützung)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Standard, Universell V2 | ![Ja](../media/icons/yes-icon.png) |![Ja](../media/icons/yes-icon.png)              | ![Nein](../media/icons/no-icon.png) |
| Premium-Blockblobs          | ![Ja](../media/icons/yes-icon.png) |![Ja](../media/icons/yes-icon.png)              | ![Nein](../media/icons/no-icon.png) |

<sup>1</sup>    Für Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) ist ein Speicherkonto mit aktiviertem hierarchischem Namespace erforderlich.

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Für das Aktivieren des vorläufigen Löschens von Containern fallen keine zusätzlichen Kosten an. Daten in vorläufig gelöschten Containern werden zum selben Tarif wie aktive Daten in Rechnung gestellt.

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren und Verwalten des vorläufigen Löschens für Container (Vorschau)](soft-delete-container-enable.md)
- [Vorläufiges Löschen für Blobs](soft-delete-blob-overview.md)
- [Blobversionsverwaltung](versioning-overview.md)
