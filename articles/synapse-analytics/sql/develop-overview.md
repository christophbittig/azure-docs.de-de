---
title: Ressourcen für die Entwicklung von Synapse SQL-Features
description: Entwicklungskonzepte, Entwurfsentscheidungen, Empfehlungen und Programmiertechniken für Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 17df008ccc5fcada0ad58313622fc997ccfa3f77
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349843"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Entwurfsentscheidungen und Programmiertechniken für Synapse SQL-Features in Azure Synapse Analytics
Dieser Artikel enthält eine Liste mit Ressourcen zu Funktionen von Synapse SQL für dedizierte SQL-Pools und serverlose SQL-Pools. Die empfohlenen Artikel sind in zwei Abschnitte unterteilt: Wesentliche Entwurfsentscheidungen und Entwicklungs- und Programmiertechniken.

Das Ziel dieser Artikel ist es, Ihnen bei der Entwicklung des optimalen technischen Ansatzes für die Synapse SQL-Komponenten in Azure Synapse Analytics zu helfen.

## <a name="key-design-decisions"></a>Wesentliche Entwurfsentscheidungen
Die folgenden Artikel heben Konzepte und Entwurfsentscheidungen für die Synapse SQL-Entwicklung hervor:

| Artikel | Dedizierter SQL-Pool | Serverloser SQL-Pool |
| ------- | -------- | ------------- |
| [Verbindungen](connect-overview.md)                    | Ja | Ja |
| [Ressourcenklassen und Parallelität](../sql-data-warehouse/resource-classes-for-workload-management.md?context=/azure/synapse-analytics/context/context) | Ja    | Nein |
| [Transaktionen](develop-transactions.md)              | Ja | Nein |
| [Benutzerdefinierte Schemas](develop-user-defined-schemas.md) | Ja | Ja |
| [Verteilen von Tabellen in SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)                 | Ja | Nein |
| [Ursachen für eine schlechte Qualität des Columnstore-Index](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)                           | Ja | Nein |
| [Tabellenpartitionen](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)                     | Ja | Nein |
| [Statistik](develop-tables-statistics.md)            | Ja | Ja |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)                                             | Ja | Nein |
| [Externe Tabellen](develop-tables-external-tables.md) | Ja | Ja |
| [CETAS](develop-tables-cetas.md)                     | Ja | Ja |


## <a name="recommendations"></a>Empfehlungen

Im Folgenden finden Sie wichtige Artikel, die bestimmte Programmiertechniken, Tipps und Empfehlungen für die Entwicklung hervorheben:

| Artikel | Dedizierter SQL-Pool | Serverloser SQL-Pool |
| ------- | -------- | ------------- |
| [Gespeicherten Prozeduren](develop-stored-procedures.md)  | Ja                | Ja                      |
| [Bezeichnungen](develop-label.md)                           | Ja                | Nein                      |
| [Ansichten](develop-views.md)                             | Ja                | Ja                     |
| [Temporäre Tabellen](develop-tables-temporary.md)       | Ja                | Ja                     |
| [Dynamischer SQL-Code](develop-dynamic-sql.md)                 | Ja                | Ja                     |
| [Schleifen](develop-loops.md)                         | Ja                | Ja                     |
| [Gruppierungsoptionen](develop-group-by-options.md)       | Ja                | Nein                      |
| [Variablenzuweisung](develop-variable-assignment.md) | Ja                | Ja                     |

## <a name="next-steps"></a>Nächste Schritte
Weitere Referenzinformationen finden Sie unter [SQL-Pool – T-SQL-Anweisungen](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?context=/azure/synapse-analytics/context/context).

