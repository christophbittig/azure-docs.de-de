---
title: Data Warehouse-Sortierungstypen
description: Sortierungstypen, die beim dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics unterstützt werden.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: cc2f5f735f20e54baa22eea7ad4d9fd00a3bc426
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850107"
---
# <a name="database-collation-support-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Unterstützung der Datenbanksortierung beim dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics 

Sie können die standardmäßige Datenbanksortierung im Azure-Portal ändern, wenn Sie einen neuen dedizierten SQL-Pool (früher SQL DW) erstellen. Diese Funktion macht das Erstellen einer neuen Datenbank mit einer der 3.800 unterstützten Datenbanksortierungen noch einfacher.

Sortierungen geben das Gebietsschema, die Codepage, die Sortierreihenfolge und Regeln für die Groß-/Kleinschreibung von Zeichen für zeichenbasierte Datentypen an. Nach dem Auswählen erben alle Spalten und Ausdrücke, für die Sortierungsinformationen erforderlich sind, die gewählte Sortierung aus der Datenbankeinstellung. Die Standardvererbung kann durch explizites Angeben einer anderen Sortierung für einen zeichenbasierten Datentyp überschrieben werden.

> [!NOTE]
> In Azure Synapse Analytics wird Abfragetext (einschließlich Variablen, Konstanten usw.) immer mithilfe der Sortierung auf Datenbankebene und nicht mit der Sortierung auf Serverebene wie bei anderen SQL Server-Angeboten verarbeitet.

## <a name="changing-collation"></a>Ändern der Sortierung

Aktualisieren Sie zum Ändern der Standardsortierung das Feld „Sortierung“ auf der Bereitstellungsoberfläche.

Beispiel: Wenn Sie die Standardsortierung so ändern möchten, dass die Groß-/Kleinschreibung beachtet wird, ändern Sie einfach den Namen „SQL_Latin1_General_CP1_CI_AS“ der Sortierung in „SQL_Latin1_General_CP1_CS_AS“.

## <a name="list-of-unsupported-collation-types"></a>Liste mit nicht unterstützten Sortierungstypen

* Japanese_Bushu_Kakusu_140_BIN
* Japanese_Bushu_Kakusu_140_BIN2
* Japanese_Bushu_Kakusu_140_CI_AI_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI
* Japanese_Bushu_Kakusu_140_CI_AI_WS
* Japanese_Bushu_Kakusu_140_CI_AI_KS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CI_AS
* Japanese_Bushu_Kakusu_140_CI_AS_WS
* Japanese_Bushu_Kakusu_140_CI_AS_KS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AI
* Japanese_Bushu_Kakusu_140_CS_AI_WS
* Japanese_Bushu_Kakusu_140_CS_AI_KS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AS
* Japanese_Bushu_Kakusu_140_CS_AS_WS
* Japanese_Bushu_Kakusu_140_CS_AS_KS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
* Japanese_XJIS_140_BIN
* Japanese_XJIS_140_BIN2
* Japanese_XJIS_140_CI_AI_VSS
* Japanese_XJIS_140_CI_AI_WS_VSS
* Japanese_XJIS_140_CI_AI_KS_VSS
* Japanese_XJIS_140_CI_AI_KS_WS_VSS
* Japanese_XJIS_140_CI_AS_VSS
* Japanese_XJIS_140_CI_AS_WS_VSS
* Japanese_XJIS_140_CI_AS_KS_VSS
* Japanese_XJIS_140_CI_AS_KS_WS_VSS
* Japanese_XJIS_140_CS_AI_VSS
* Japanese_XJIS_140_CS_AI_WS_VSS
* Japanese_XJIS_140_CS_AI_KS_VSS
* Japanese_XJIS_140_CS_AI_KS_WS_VSS
* Japanese_XJIS_140_CS_AS_VSS
* Japanese_XJIS_140_CS_AS_WS_VSS
* Japanese_XJIS_140_CS_AS_KS_VSS
* Japanese_XJIS_140_CS_AS_KS_WS_VSS
* Japanese_XJIS_140_CI_AI
* Japanese_XJIS_140_CI_AI_WS
* Japanese_XJIS_140_CI_AI_KS
* Japanese_XJIS_140_CI_AI_KS_WS
* Japanese_XJIS_140_CI_AS
* Japanese_XJIS_140_CI_AS_WS
* Japanese_XJIS_140_CI_AS_KS
* Japanese_XJIS_140_CI_AS_KS_WS
* Japanese_XJIS_140_CS_AI
* Japanese_XJIS_140_CS_AI_WS
* Japanese_XJIS_140_CS_AI_KS
* Japanese_XJIS_140_CS_AI_KS_WS
* Japanese_XJIS_140_CS_AS
* Japanese_XJIS_140_CS_AS_WS
* Japanese_XJIS_140_CS_AS_KS
* Japanese_XJIS_140_CS_AS_KS_WS
* SQL_EBCDIC1141_CP1_CS_AS
* SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>Überprüfen der aktuellen Sortierung

Führen Sie den folgenden T-SQL-Codeausschnitt aus, um die aktuelle Sortierung für die Datenbank zu überprüfen:

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```

Wenn „Collation“ als Eigenschaftenparameter übergeben wird, gibt die Funktion „DatabasePropertyEx“ die aktuelle Sortierung für die angegebene Datenbank zurück. Weitere Informationen finden Sie unter [DatabasePropertyEx](/sql/t-sql/functions/databasepropertyex-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
