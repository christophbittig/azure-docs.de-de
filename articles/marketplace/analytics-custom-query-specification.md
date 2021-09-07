---
title: Spezifikation für benutzerdefinierte Abfragen
description: Hier erfahren Sie, wie Sie mithilfe von benutzerdefinierten Abfragen programmgesteuert Daten aus Analysetabellen extrahieren, die Ihre Angebote im kommerziellen Marketplace von Microsoft betreffen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8d003980d7a8a1a5a5477a7d22ec547db13b52ad
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349351"
---
# <a name="custom-query-specification"></a>Spezifikation für benutzerdefinierte Abfragen

Mit dieser Abfragespezifikation können Partner unkompliziert benutzerdefinierte Abfragen zum Extrahieren von Daten aus Analysetabellen formulieren. Mit diesen Abfragen können nur die gewünschten Spalten und Metriken ausgewählt werden, die einem bestimmten Kriterium entsprechen. Der Herzstück der Sprachspezifikation ist die Datasetdefinition, für die eine benutzerdefinierte Abfrage geschrieben werden kann.

## <a name="datasets"></a>Datasets

So wie bestimmte Abfragen für eine Datenbank mit Tabellen und Spalten ausgeführt werden, funktionieren benutzerdefinierte Abfrage für Datasets mit Spalten und Metriken. Die vollständige Liste der für Abfragen verfügbaren Datasets kann mithilfe der Datasets-API abgerufen werden.

Im Folgenden finden Sie ein Beispiel für ein Dataset, das als JSON angezeigt wird.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Bestandteile von Datasets

- Ein Datasetname ähnelt dem Namen einer Datenbanktabelle, z. B.: ISVUsage. Ein Dataset enthält eine Liste von Spalten, die ausgewählt werden können, z. B. MarketplaceSubscriptionId.
- Ein Dataset enthält auch Metriken, die den Aggregationsfunktionen einer Datenbank entsprechen, z. B.: NormalizedUsage.
- Es gibt feste Zeiträume, in denen Daten exportiert werden können.

### <a name="formulating-a-query-on-a-dataset"></a>Formulieren einer Abfrage für ein Dataset

Nachstehend sind einige Beispielabfragen aufgeführt, die zeigen, wie verschiedene Datentypen extrahiert werden.

| Abfrage | BESCHREIBUNG |
| ------------ | ------------- |
| **SELECT** MarketplaceSubscriptionId,CustomerId **FROM** ISVUsage **TIMESPAN LAST_MONTH** | Diese Abfrage ruft jede `MarketplaceSubscriptionId` und die zugehörige `CustomerId` im letzten Monat ab. |
| **SELECT** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **FROM** ISVUsage **ORDER BY** EstimatedExtendedChargeCC **LIMIT** 10 | Diese Abfrage ruft die zehn wichtigsten Abonnements in absteigender Reihenfolge nach der Anzahl der Lizenzen ab, die im Rahmen der einzelnen Abonnements verkauft wurden. |
| **SELECT** CustomerId, NormalizedUsage, RawUsage **FROM** ISVUsage **WHERE** NormalizedUsage > 100000 **ORDER BY** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | Diese Abfrage ruft NormalizedUsage und RawUsage für alle Kunden ab, deren NormalizedUsage-Wert über 100.000 liegt. |
| **SELECT** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **FROM** ISVUsage **WHERE** CustomerId IN (‘2a31c234-1f4e-4c60-909e-76d234f93161’, ‘80780748-3f9a-11eb-b378-0242ac130002’) | Diese Abfrage ruft die `MarketplaceSubscriptionId` und die normalisierte Nutzung für jeden Monat anhand der beiden `CustomerId`-Werte ab: `2a31c234-1f4e-4c60-909e-76d234f93161` und `80780748-3f9a-11eb-b378-0242ac130002`. |
|||

## <a name="query-specification"></a>Abfragespezifikation

In diesem Abschnitt werden die Abfragedefinition und -struktur beschrieben.

### <a name="grammar-reference"></a>Grammatikreferenz

In dieser Tabelle werden die in Abfragen verwendeten Symbole beschrieben.

| Symbol | Bedeutung |
| ------------ | ------------- |
| ? | Optional |
| * | 0 oder mehr |
| + | Ein oder mehr |
| &#124; | Oder/ein Listenwert |
| | |

### <a name="query-definition"></a>Abfragedefinition

Die Abfrageanweisung verfügt über die folgenden Klauseln: SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause? und TimeSpan?.

- **SelectClause**: **SELECT** ColumOrMetricName(, ColumOrMetricName)*
    - **ColumOrMetricName**: in einem Dataset definierte Spalten und Metriken
- **FromClause**: **FROM** DatasetName
    - **DatasetName**: der im Dataset definierte Datasetname
- **WhereClause**: **WHERE** FilterCondition (**AND** FilterCondition)*
    - **FilterCondition**: ColumOrMetricName Operator Value
        - **Operator**:  = | > | < | >= | <= | != | LIKE | NOT LIKE | IN | NOT IN
        - **Value**: Number | StringLiteral | MultiNumberList | MultiStringList 
            - **Number**: -? [0-9]+ (. [0-9] [0-9]*)?
            - **StringLiteral**:  ' [a-zA-Z0-9_]*'
            - **MultiNumberList**: (Number  (,Number)*)
            - **MultiStringList**: (StringLiteral (,StringLiteral)*)
- **OrderClause**: **ORDER BY** OrderCondition (,OrderCondition)*
    - **OrderCondition**: ColumOrMetricName (**ASC** | **DESC**)*
    - **LimitClause**: **LIMIT** [0-9]+
    - **TimeSpan**: **TIMESPAN** ( TODAY | YESTERDAY | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | LIFETIME)

### <a name="query-structure"></a>Abfragestruktur

Eine Berichtsabfrage besteht aus mehreren Teilen:

- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
- TIMESPAN

Nachstehend werden die einzelnen Teile beschrieben.

#### <a name="select"></a>SELECT

Dieser Abfrageteil gibt die Spalten an, die exportiert werden. Die auswählbaren Spalten sind die Felder, die in den Abschnitten `selectableColumns` und `availableMetrics` eines Datasets aufgeführt sind. Wenn in der ausgewählten Feldliste eine Metrikspalte enthalten ist, werden Metriken für jede unterschiedliche Kombination der nicht metrikbezogenen Spalten berechnet. 

**Beispiel:**
- **SELECT** `OfferName`, `NormalizedUsage`

#### <a name="distinct"></a>DISTINCT

Durch Hinzufügen des DISTINCT-Schlüsselworts nach SELECT wird sichergestellt, dass die endgültigen exportierten Daten keine doppelten Zeilen aufweisen. Das DISTINCT-Schlüsselwort funktioniert unabhängig davon, ob eine Metrikspalte ausgewählt ist.

**Beispiel:**
- **SELECT DISTINCT** `MarketplaceSubscriptionId, OfferType`

#### <a name="from"></a>FROM

Dieser Abfrageteil gibt das Dataset an, aus dem Daten exportiert werden müssen. Der hier angegebene Datasetname muss gültiger Datasetname sein, der von der Datasets-API zurückgegeben wird.

**Beispiel:**
- FROM `ISVUsage`
- FROM `ISVOrder`

#### <a name="where"></a>WHERE

In diesem Abfrageteil werden die Filterbedingungen für das Dataset angegeben. Nur Zeilen, die alle in dieser Klausel aufgeführten Bedingungen erfüllen, sind in der endgültigen Exportdatei vorhanden. Die Filterbedingung kann eine der in `selectableColumns` und `availableMetrics` aufgeführten Spalten sein. Bei den in der Filterbedingung angegebenen Werten kann es sich nur um eine Liste von Zahlen oder Zeichenfolgen handeln, wenn der Operator `IN` oder `NOT IN` entspricht. Die Werte können immer als Literalzeichenfolge angegeben werden und werden dann in die nativen Spaltentypen konvertiert. Mehrere Filterbedingungen müssen durch einen `AND`-Vorgang getrennt werden.

**Beispiel:**

- MarketplaceSubscriptionId = ‘868368da-957d-4959-8992-3c12dc7e6260’
- CustomerName **LIKE** ‘%Contosso%’
- CustomerId **NOT IN** (1000, 1001, 1002)
- OrderQuantity=100
- OrderQuantity=‘100’
    - MarketplaceSubscriptionId=’7b487ac0-ce12-b732-dcd6-91a1e4e74a50’ AND CustomerId=’ 0f8b7fa0-eb83-a183-1225-ca153ef807aa’

#### <a name="order-by"></a>ORDER BY

Dieser Abfrageteil gibt die Sortierkriterien für die exportierten Zeilen an. Die Spalten, für die die Reihenfolge definiert werden kann, müssen aus `selectableColumns` und `availableMetrics` des Datasets stammen. Wenn keine Sortierreihenfolge angegeben ist, wird die Spalte standardmäßig auf `DESC` festgelegt. Die Reihenfolge kann für mehrere Spalten definiert werden, indem die Kriterien durch ein Komma getrennt werden.

**Beispiel:**

- **ORDER BY** NormalizedUsage **ASC**, EstimatedExtendedCharge(CC) **DESC**
- **ORDER BY** CustomerName **ASC**, NormalizedUsage

#### <a name="limit"></a>LIMIT

Dieser Abfrageteil gibt die Anzahl der Zeilen an, die exportiert werden. Die angegebene Zahl muss eine positive ganze Zahl ungleich 0 (Null) sein.

#### <a name="timespan"></a>TIMESPAN

Dieser Abfrageteil gibt die Zeitspanne an, für die die Daten exportiert werden müssen. Die möglichen Werte sollten aus dem Feld `availableDateRanges` der Datasetdefinition stammen.

### <a name="case-sensitivity-in-query-specification"></a>Beachtung der Groß-/Kleinschreibung in der Abfragespezifikation

Bei der Spezifikation ist die Groß-/Kleinschreibung nicht relevant. Vordefinierte Schlüsselwörter, Spaltennamen und Werte können mit Groß- oder Kleinbuchstaben angegeben werden.

## <a name="see-also"></a>Siehe auch

- [Liste der Systemabfragen](analytics-system-queries.md)
