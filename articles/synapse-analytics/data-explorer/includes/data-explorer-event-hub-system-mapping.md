---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: e7e723394bd7a90f6fd4cda8db7c7cb27c4cb9a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478775"
---
> [!NOTE]
> * Systemeigenschaften werden für `json` und Tabellenformate (`csv`, `tsv` usw.), aber nicht für komprimierte Daten unterstützt. Bei Verwendung eines nicht unterstützten Formats werden die Daten weiterhin erfasst, die Eigenschaften werden jedoch ignoriert.
> * Bei Tabellendaten werden Systemeigenschaften nur für Ereignismeldungen mit einem Datensatz unterstützt.
> * Bei JSON-Daten werden Systemeigenschaften auch für Ereignismeldungen mit mehreren Datensätzen unterstützt. In solchen Fällen werden die Systemeigenschaften nur dem ersten Datensatz der Ereignismeldung hinzugefügt. 
> * Für die `csv`-Zuordnung werden Eigenschaften in der in der Tabelle [Systemeigenschaften](../ingest-data/data-explorer-ingest-event-hub-overview.md#system-properties) aufgeführten Reihenfolge am Anfang des Datensatzes hinzugefügt.
> * Für die `json`-Zuordnung werden Eigenschaften entsprechend den Eigenschaftsnamen in der Tabelle [Systemeigenschaften](../ingest-data/data-explorer-ingest-event-hub-overview.md#system-properties) hinzugefügt.
