---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 73cd54f16e29564fda5f9e41d0469d59ea99526d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478614"
---
|**Eigenschaft** | **Beschreibung der Eigenschaft**|
|---|---|
| `rawSizeBytes` | Größe der Rohdaten (unkomprimiert). Bei Avro/ORC/Parquet ist dies die Größe vor dem Anwenden der formatspezifischen Komprimierung. Geben Sie die ursprüngliche Datengröße an, indem Sie diese Eigenschaft auf die nicht komprimierte Datengröße in Byte festlegen.|
| `kustoTable` |  Name der vorhandenen Zieltabelle. Überschreibt die `Table`, die auf dem Blatt `Data Connection` festgelegt ist. |
| `kustoDataFormat` |  Datenformat. Überschreibt das `Data format`, das auf dem Blatt `Data Connection` festgelegt ist. |
| `kustoIngestionMappingReference` |  Name der zu verwendenden vorhandenen Erfassungszuordnung. Überschreibt das `Column mapping`, das auf dem Blatt `Data Connection` festgelegt ist.|
| `kustoIgnoreFirstRecord` | Wenn `true` festgelegt wird, ignoriert Kusto die erste Zeile im Blob. Verwenden Sie diese Eigenschaft in Daten in einem Tabellenformat (CSV, TSV oder ähnliche), um die Header zu ignorieren. |
| `kustoExtentTags` | Zeichenfolgendarstellung von [Tags](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context), die an die resultierende Erweiterung angefügt werden. |
| `kustoCreationTime` |  Überschreibt [$IngestionTime](/azure/data-explorer/kusto/query/ingestiontimefunction?context=/azure/synapse-analytics/context/context?pivots=azuredataexplorer) für das Blob und ist als ISO 8601-Zeichenfolge formatiert. Verwenden Sie dies für einen Abgleich. |
