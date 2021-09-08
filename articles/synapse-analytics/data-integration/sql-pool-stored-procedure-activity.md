---
title: Transformieren von Daten mit der SQL-Pool-Aktivität „Gespeicherte Prozedur“
description: Erläutert, wie Sie mithilfe der SQL-Pool-Aktivität „Gespeicherte Prozedur“ eine gespeicherte Prozedur in Azure Synapse Analytics aufrufen.
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: pipeline
author: linda33wj
ms.author: jingwang
ms.reviewer: jrasnick
ms.date: 05/13/2021
ms.openlocfilehash: 4c1f12d4e61f254f753f3feb59ca46997f88b751
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257079"
---
# <a name="transform-data-by-using-sql-pool-stored-procedure-activity-in-azure-synapse-analytics"></a>Transformieren von Daten mit der SQL-Pool-Aktivität „Gespeicherte Prozedur“ in Azure Synapse Analytics

[!INCLUDE[appliesto-xxx-asa-md](../../data-factory/includes/appliesto-xxx-asa-md.md)]

Sie verwenden Datentransformationsaktivitäten in einer [Pipeline](../../data-factory/concepts-pipelines-activities.md), um Rohdaten in Vorhersagen und Erkenntnisse umzuwandeln und zu verarbeiten. Dieser Artikel baut auf dem Artikel [Transformieren von Daten](../../data-factory/transform-data.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

Sie können in Azure Synapse Analytics mithilfe der SQL-Pool-Aktivität „Gespeicherte Prozedur“ eine gespeicherte Prozedur in einem dedizierten SQL-Pool aufrufen.

## <a name="syntax-details"></a>Syntaxdetails

Die folgenden Einstellungen werden in der SQL-Pool-Aktivität „Gespeicherte Prozedur“ unterstützt:

| Eigenschaft                  | BESCHREIBUNG                              | Erforderlich |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Der Name der Aktivität                     | Ja      |
| description               | Ein Text, der beschreibt, wofür die Aktivität verwendet wird. | Nein       |
| type                      | Für die SQL-Pool-Aktivität „Gespeicherte Prozedur“ lautet der Aktivitätstyp **SqlPoolStoredProcedure**. | Ja      |
| sqlPool         | Verweis auf einen [dedizierten SQL-Pool](../sql/overview-architecture.md) im aktuellen Azure Synapse-Arbeitsbereich. | Ja      |
| storedProcedureName       | Geben Sie den Namen der gespeicherten Prozedur an, die aufgerufen werden soll. | Ja      |
| storedProcedureParameters | Geben Sie die Werte für Parameter der gespeicherten Prozedur an. Verwenden Sie `"param1": { "value": "param1Value","type":"param1Type" }` zum Übergeben von Parameterwerten und deren Typ, der von der Datenquelle unterstützt wird. Wenn Sie für einen Parameter „null“ übergeben müssen, verwenden Sie die folgende Syntax: `"param1": { "value": null }` (nur Kleinbuchstaben). | Nein       |

Beispiel:

```json
{
    "name": "SQLPoolStoredProcedureActivity",
    "description":"Description",
    "type": "SqlPoolStoredProcedure",
    "sqlPool": {
        "referenceName": "DedicatedSQLPool",
        "type": "SqlPoolReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
 
- [Pipeline und Aktivität](../../data-factory/concepts-pipelines-activities.md)
- [Dedizierter SQL-Pool](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
