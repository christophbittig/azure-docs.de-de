---
title: Datenerfassungseigenschaften für Azure Synapse Data Explorer (Vorschau)
description: Hier erfahren Sie mehr über die verschiedenen Datenerfassungseigenschaften, die von Azure Synapse Data Explorer unterstützt werden.
ms.topic: conceptual
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: e598f3fdd03721def28ac8a0f90e3bd12958783c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478747"
---
# <a name="azure-synapse-data-explorer-data-ingestion-properties-preview"></a>Datenerfassungseigenschaften für Azure Synapse Data Explorer (Vorschau)

Bei der Datenerfassung werden Daten einer Tabelle hinzugefügt und für Abfragen in Data Explorer verfügbar gemacht. Sie können dem Erfassungsbefehl nach dem Schlüsselwort `with` Eigenschaften hinzufügen.

## <a name="ingestion-properties"></a>Erfassungseigenschaften

In der folgenden Tabelle sind die von Data Explorer unterstützten Eigenschaften mit einer Beschreibung und Beispielen aufgeführt: 

|Eigenschaft              |BESCHREIBUNG                                              |Beispiel                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |Ein Zeichenfolgenwert, der angibt, wie Daten aus der Quelldatei den tatsächlichen Spalten in der Tabelle zugeordnet werden sollen. Definieren Sie den Wert `format` mit dem entsprechenden Zuordnungstyp. Weitere Informationen finden Sie unter [Data mappings](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) (Datenzuordnungen).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(veraltet: `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|Ein Zeichenfolgenwert, der angibt, wie Daten aus der Quelldatei den tatsächlichen Spalten in der Tabelle zugeordnet werden sollen (unter Verwendung eines benannten Zuordnungsrichtlinienobjekts). Definieren Sie den Wert `format` mit dem entsprechenden Zuordnungstyp. Weitere Informationen finden Sie unter [Data mappings](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) (Datenzuordnungen).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(veraltet: `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |Der datetime-Wert (formatiert als ISO8601-Zeichenfolge), der zum Zeitpunkt der Erstellung der erfassten Datenerweiterungen verwendet werden soll. Ohne Angabe wird der aktuelle Wert (`now()`) verwendet. Das Überschreiben der Standardeinstellung ist bei der Erfassung älterer Daten hilfreich, um eine korrekte Anwendung der Aufbewahrungsrichtlinie zu gewährleisten. Achten Sie bei Angabe eines Werts darauf, dass die Eigenschaft `Lookback` in der effektiven [Richtlinie für die Zusammenführung von Blöcken](/azure/data-explorer/kusto/management/mergepolicy?context=/azure/synapse-analytics/context/context) der Zieltabelle auf den angegebenen Wert abgestimmt ist.|`with (creationTime="2017-02-13")`|
|`extend_schema`|Ein boolescher Wert, der den Befehl anweist, das Schema der Tabelle zu erweitern (Standardwert: `false`). Diese Option betrifft nur die Befehle `.append` und `.set-or-append`. Es sind nur Schemaerweiterungen zulässig, bei denen zusätzliche Spalten am Ende der Tabelle hinzugefügt werden.|Wenn das ursprüngliche Tabellenschema `(a:string, b:int)` lautet, wäre `(a:string, b:int, c:datetime, d:string)` eine gültige Schemaerweiterung, `(a:string, c:datetime)` dagegen nicht.|
|`folder` |Für [ingest-from-query](/azure/data-explorer/kusto/management/data-ingestion/ingest-from-query?context=/azure/synapse-analytics/context/context)-Befehle der Ordner, der der Tabelle zugewiesen werden soll. Wenn die Tabelle bereits vorhanden ist, überschreibt diese Eigenschaft den Ordner der Tabelle.|`with (folder="Tables/Temporary")`|
|`format` |Das Datenformat (siehe [Unterstützte Datenformate](data-explorer-ingest-data-supported-formats.md)).|`with (format="csv")`|
|`ingestIfNotExists`|Ein Zeichenfolgenwert, der eine erfolgreiche Erfassung verhindert, wenn die Tabelle bereits Daten enthält, die mit einem Tag vom Typ `ingest-by:` mit identischem Wert gekennzeichnet sind. Dadurch wird die idempotente Datenerfassung sichergestellt. Weitere Informationen finden Sie unter [ingest-by: Tags](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#ingest-by-extent-tags).|Die Eigenschaften `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` geben an, dass die aktuelle Erfassung nicht ausgeführt werden soll, wenn bereits Daten mit dem Tag `ingest-by:Part0001` vorhanden sind. Sind noch keine vorhanden, wird das Tag für diese neue Erfassung festgelegt (für den Fall, dass bei einer zukünftigen Erfassung versucht wird, die gleichen Daten zu erfassen).|
|`ignoreFirstRecord` |Ein boolescher Wert. Bei `true` wird bei der Erfassung jeweils der erste Datensatz der Datei ignoriert. Diese Eigenschaft ist hilfreich bei Dateien im `CSV`-Format und ähnlichen Formaten, wenn es sich beim ersten Datensatz in der Datei um die Spaltennamen handelt. Standardmäßig wird `false` verwendet.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |Ein boolescher Wert, der angibt, dass der Befehl die detaillierten Ergebnisse persistent speichern soll (auch wenn der Vorgang erfolgreich war), sodass sie mithilfe des Befehls [.show operation details](/azure/data-explorer/kusto/management/operations?context=/azure/synapse-analytics/context/context#show-operation-details) abgerufen werden können. Wird standardmäßig auf `false` festgelegt.|`with (persistDetails=true)`|
|`policy_ingestiontime`|Ein boolescher Wert, der angibt, ob die [IngestionTime-Richtlinie](/azure/data-explorer/kusto/management/ingestiontimepolicy?context=/azure/synapse-analytics/context/context) für eine Tabelle aktiviert werden soll, die durch diesen Befehl erstellt wird. Der Standardwert lautet `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |Ein boolescher Wert, der angibt, ob der Befehl das Schema der Tabelle neu erstellen darf. Diese Eigenschaft gilt nur für den Befehl `.set-or-replace`. Sie hat Vorrang vor der `extend_schema`-Eigenschaft, wenn beide festgelegt sind.|`with (recreate_schema=true)`|
|`tags`|Eine Liste der [Tags](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging), die den erfassten Daten zugeordnet werden sollen (formatiert als JSON-Zeichenfolge). |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|Eine JSON-Zeichenfolge, die angibt, welche Überprüfungen im Rahmen der Erfassung ausgeführt werden sollen. Eine Beschreibung der verschiedenen Optionen finden Sie unter [Datenerfassung](data-explorer-ingest-data-overview.md).| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')` (Standardrichtlinie)|
|`zipPattern`|Verwenden Sie diese Eigenschaft, wenn Sie Daten aus einem Speicher erfassen, der ein ZIP-Archiv enthält. Dieser Zeichenfolgenwert gibt den regulären Ausdruck an, der zum Auswählen der zu erfassenden Dateien im ZIP-Archiv verwendet werden soll.  Alle anderen Dateien im Archiv werden ignoriert.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Datenerfassung finden Sie [hier](data-explorer-ingest-data-overview.md).
- Weitere Informationen zu unterstützten Datenformaten finden Sie [hier](data-explorer-ingest-data-supported-formats.md).
