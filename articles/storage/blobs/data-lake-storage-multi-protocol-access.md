---
title: Multiprotokollzugriff für Azure Data Lake Storage
description: Verwenden von BLOB-APIs und Anwendungen, die BLOB-APIs mit Azure Data Lake Storage Gen2 verwenden.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e2a73ae4a780836371414401cf4d0114a6a5b220
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273911"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Multiprotokollzugriff für Azure Data Lake Storage

BLOB-APIs können jetzt für Konten verwendet werden, die über einen hierarchischen Namespace verfügen. Dadurch werden das Ökosystem von Tools, Anwendungen und Diensten sowie mehrere Blobspeicherfunktionen für Konten mit einem hierarchischen Namespace verfügbar.

Bis vor Kurzem mussten Sie eventuell jeweils eigene Speicherlösungen für den Objektspeicher und den Analysespeicher verwalten. Die Ursache hierfür war die beschränkte Unterstützung des Ökosystems durch Azure Data Lake Storage Gen2. Der Zugriff auf BLOB-Dienstfeatures, z. B. die Diagnoseprotokollierung, war ebenfalls beschränkt. Eine fragmentierte Speicherlösung ist schwierig zu verwalten, da für verschiedene Szenarien Daten zwischen Konten verschoben werden müssen. Dies ist nicht mehr erforderlich.

Mit Multiprotokollzugriff für Data Lake Storage können Sie das Ökosystem von Tools, Anwendungen und Diensten für Ihre Daten nutzen. Dies beinhaltet auch Tools und Anwendungen von Drittanbietern. Sie können sie auf Konten mit einem hierarchischen Namespace verweisen, ohne sie ändern zu müssen. Diese Anwendungen *erfordern keine Änderungen*, selbst wenn sie BLOB-APIs aufrufen, da BLOB-APIs jetzt für Daten in Konten mit einem hierarchischen Namespace verwendet werden können.

BLOB Storage-Features, z. B. [Diagnoseprotokollierung](../common/storage-analytics-logging.md), [Zugriffsebenen](access-tiers-overview.md) und [Richtlinien für die Azure Blob Storage-Lebenszyklusverwaltung](./lifecycle-management-overview.md) können jetzt für Konten mit einem hierarchischen Namespace verwendet werden. Daher können Sie hierarchische Namespaces in Ihren Blob Storage-Konten aktivieren, ohne den Zugriff auf diese wichtigen Features zu verlieren.

> [!NOTE]
> Der Multiprotokollzugriff für Data Lake Storage steht allgemein und in allen Regionen zur Verfügung. Einige Azure-Dienste oder Blob Storage-Features, die durch Multiprotokollzugriff aktiviert werden, befinden sich weiterhin in der Vorschauphase. In diesen Artikeln wird die aktuelle Unterstützung für Blob Storage-Features und Azure-Dienstintegrationen zusammengefasst.
>
> [Unterstützung von Blob Storage-Features in Azure Storage-Konten](storage-feature-support-in-storage-accounts.md)
>
> [Azure-Dienste, die Azure Data Lake Storage Gen2 unterstützen](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Funktionsweise des Multiprotokollzugriffs für Azure Data Lake Storage

BLOB-APIs und Data Lake Storage Gen2-APIs können in Speicherkonten mit einem hierarchischen Namespace für dieselben Daten verwendet werden. Data Lake Storage Gen2 leitet BLOB-APIs über den hierarchischen Namespace weiter, sodass Sie die Vorteile von erstklassigen Verzeichnisvorgängen und POSIX-konformen Zugriffssteuerungslisten (Access Control Lists, ACLs) nutzen können.

![Konzept des Multiprotokollzugriffs für Azure Data Lake Storage](./media/data-lake-storage-interop/interop-concept.png)

Vorhandene Tools und Anwendungen, die die BLOB-API verwenden, profitieren von diesen Vorteilen automatisch. Entwickler müssen sie nicht ändern. Data Lake Storage Gen2 wendet konsistent ACLs auf Verzeichnis- und Dateiebene an, unabhängig von dem Protokoll, das von den Tools und Anwendungen für den Zugriff auf die Daten verwendet wird.

## <a name="see-also"></a>Siehe auch

- [Unterstützung von Blob Storage-Features in Azure Storage-Konten](storage-feature-support-in-storage-accounts.md)
- [Azure-Dienste, die Azure Data Lake Storage Gen2 unterstützen](data-lake-storage-supported-azure-services.md)
- [Open-Source-Plattformen, die Azure Data Lake Storage Gen2 unterstützen](data-lake-storage-supported-open-source-platforms.md)
- [Bekannte Probleme mit Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
