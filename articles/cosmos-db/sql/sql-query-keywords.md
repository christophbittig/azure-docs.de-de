---
title: SQL-Schlüsselwörter für Azure Cosmos DB
description: Erfahren Sie mehr über SQL-Schlüsselwörter für Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: tisande
ms.openlocfilehash: 680b383cee86ec4233579b9305c47e60ad4d5ff4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625340"
---
# <a name="keywords-in-azure-cosmos-db"></a>Schlüsselwörter in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

In diesem Artikel werden die Schlüsselwörter beschrieben, die in SQL-Abfragen in Azure Cosmos DB verwendet werden können.

## <a name="between"></a>BETWEEN

Mit dem Schlüsselwort `BETWEEN` können Abfragen für Bereiche von Zeichenfolgen- oder numerischen Werten ausgedrückt werden. Die folgende Abfrage gibt beispielsweise alle Elemente zurück, in denen das erste Kind die erste bis fünfte Klasse (einschließlich) besucht.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Sie können auch das Schlüsselwort `BETWEEN` in der `SELECT`-Klausel verwenden, wie im folgenden Beispiel gezeigt.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

In der SQL-API können Sie – im Gegensatz zu ANSI SQL – Bereichsabfragen für Eigenschaften gemischter Typen ausdrücken. Beispielsweise kann `grade` in einigen Elementen eine Zahl (z.B. `5`) und in anderen eine Zeichenfolge (z.B. `grade4`) sein. In diesen Fällen (etwa in JavaScript) gibt der Vergleich zwischen den beiden unterschiedlichen Typen `Undefined` zurück, sodass das Element übersprungen wird.

## <a name="distinct"></a>DISTINCT

Das Schlüsselwort `DISTINCT` entfernt Duplikate aus der Projektion der Abfrage.

In diesem Beispiel projiziert die Abfrage Werte für alle Nachnamen:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Die Ergebnisse sind:

```json
[
    "Andersen"
]
```

Sie können auch eindeutige Objekte projizieren. In diesem Fall kommt das Feld „lastName“ in keinem der beiden Dokumente vor, sodass die Abfrage ein leeres Objekt zurückgibt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Die Ergebnisse sind:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` kann auch in der Projektion in einer Unterabfrage verwendet werden:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Diese Abfrage projiziert ein Array mit dem Wert von givenName für jedes untergeordnete Element, aus dem die Duplikate entfernt wurden. Dieses Array hat den Alias ChildNames und wird in der äußeren Abfrage projiziert.

Die Ergebnisse sind:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Abfragen mit einer Aggregatsystemfunktion und einer Unterabfrage mit `DISTINCT` werden nur in bestimmten SDK-Versionen unterstützt. Abfragen mit der folgenden Form werden z. B. nur in den unten angegebenen SDK-Versionen unterstützt:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

**Unterstützte SDK-Versionen:**

|**SDK**|**Unterstützte Versionen**|
|-------|----------------------|
|.NET SDK|3.18.0 oder höher|
|Java-SDK|4.19.0 oder höher|
|Node.js SDK|Nicht unterstützt|
|Python SDK|Nicht unterstützt|

Für Abfragen mit einer Aggregatsystemfunktion und einer Unterabfrage mit `DISTINCT` gibt es einige weitere Einschränkungen:

|**Einschränkung**|**Beispiel**|
|-------|----------------------|
|WHERE-Klausel in der äußeren Abfrage|SELECT COUNT(1) FROM (SELECT DISTINCT VALUE c.lastName FROM c) AS lastName WHERE lastName = "Smith"|
|ORDER BY-Klausel in der äußeren Abfrage|SELECT VALUE COUNT(1) FROM (SELECT DISTINCT VALUE c.lastName FROM c) AS lastName ORDER BY lastName|
|GROUP BY-Klausel in der äußeren Abfrage|SELECT COUNT(1) as annualCount, d.year FROM (SELECT DISTINCT c.year, c.id FROM c) AS d GROUP BY d.year|
|Geschachtelte Unterabfrage|SELECT COUNT(1) FROM (SELECT y FROM (SELECT VALUE StringToNumber(SUBSTRING(d.date, 0, 4 FROM (SELECT DISTINCT c.date FROM c) d) AS y WHERE y > 2012)|
|Mehrere Aggregationen|SELECT COUNT(1) as AnnualCount, SUM(d.sales) as TotalSales FROM (SELECT DISTINCT c.year, c.sales, c.id FROM c) AS d|

## <a name="like"></a>LIKE

Gibt je nachdem, ob eine bestimmte Zeichenfolge mit einem angegebenen Muster übereinstimmt, einen booleschen Wert zurück. Ein Muster kann normale Zeichen und Platzhalterzeichen einschließen. Sie können logisch äquivalente Abfragen schreiben, indem Sie entweder das `LIKE`-Schlüsselwort oder die [RegexMatch](sql-query-regexmatch.md)-Systemfunktion verwenden. Sie werden unabhängig von der ausgewählten Option dieselbe Indexauslastung beobachten. Daher sollten Sie `LIKE` verwenden, wenn Sie dessen Syntax anderen regulären Ausdrücken vorziehen.

> [!NOTE]
> Da `LIKE` einen Index verwenden kann, sollten Sie [einen Bereichsindex erstellen](./../index-policy.md) für Eigenschaften, die Sie mit `LIKE` vergleichen.

Sie können folgende Platzhalterzeichen mit „LIKE“ verwenden:

| Platzhalter | BESCHREIBUNG                                                  | Beispiel                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | Eine Zeichenfolge aus null oder mehr Zeichen                      | WHERE   c.description LIKE   “%SO%PS%”      |
| _ (Unterstrich)     | Ein einzelnes Zeichen                                       | WHERE   c.description LIKE   “%SO_PS%”      |
| [ ]                  | Beliebiges einzelnes Zeichen im angegebenen Bereich ([a-f]) oder in der angegebenen Menge ([abcdef]). | WHERE   c.description LIKE   “%SO[t-z]PS%”  |
| [^]                  | Beliebiges einzelnes Zeichen, das sich nicht im angegebenen Bereich ([^a-f]) oder in der angegebenen Menge ([^abcdef]) befindet. | WHERE   c.description LIKE   “%SO[^abc]PS%” |


### <a name="using-like-with-the--wildcard-character"></a>Verwenden von NOT LIKE mit dem Platzhalterzeichen %

Das Zeichen `%` entspricht einer Zeichenfolge aus null oder mehr Zeichen. Wenn Sie z. B. ein `%` am Anfang und am Ende des Musters platzieren, gibt die folgende Abfrage alle Elemente mit einer Beschreibung zurück, die `fruit` enthält:

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

Wenn Sie das Zeichen `%` nur am Ende des Musters verwendet haben, werden nur Elemente mit einer Beschreibung zurückgegeben, die mit `fruit` beginnt:

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>Verwenden von „NOT LIKE“

Im folgenden Beispiel werden alle Elemente mit einer Beschreibung zurückgegeben, die nicht `fruit` enthält:

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>Verwenden der ESCAPE-Klausel

Mithilfe der ESCAPE-Klausel können Sie nach Mustern suchen, die ein oder mehrere Platzhalterzeichen enthalten. Wenn Sie z. B. nach Beschreibungen suchen möchten, in denen die Zeichenfolge `20-30%` enthalten ist, sollten Sie die `%` nicht als Platzhalterzeichen interpretieren.

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>Verwenden von Platzhalterzeichen als Literale

Sie können Platzhalterzeichen in eckige Klammern einschließen, um sie als Literalzeichen zu behandeln. Wenn Sie ein Platzhalterzeichen in Klammern einschließen, entfernen Sie alle besonderen Attribute. Hier einige Beispiele:

| Muster           | Bedeutung |
| ----------------- | ------- |
| LIKE   “20-30[%]” | 20-30 %  |
| LIKE   “[_]n”     | _n      |
| LIKE   “[ [ ]”    | [       |
| LIKE   “]”        | ]       |

## <a name="in"></a>IN

Mit dem Schlüsselwort IN können Sie überprüfen, ob ein angegebener Wert mit einem Wert in einer Liste übereinstimmt. Die folgende Abfrage gibt beispielsweise alle Familienelemente zurück, in denen `id` den Text `WakefieldFamily` oder `AndersenFamily` enthält.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Im folgenden Beispiel werden alle Elemente zurückgegeben, in denen der Bundesstaat einem der angegebenen Werte entspricht:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Die SQL-API unterstützt eine [Iteration über JSON-Arrays](sql-query-object-array.md#Iteration) mit einem neuen Konstrukt, das mit dem Schlüsselwort IN in der Quelle FROM hinzugefügt wurde.

Wenn Sie Ihren Partitionsschlüssel in den `IN`-Filter einbeziehen, filtert Ihre Abfrage automatisch nur nach den relevanten Partitionen.

## <a name="top"></a>TOP

Das Schlüsselwort TOP gibt die ersten `N` Abfrageergebnisse in einer nicht definierten Reihenfolge zurück. Es empfiehlt sich, TOP mit der `ORDER BY`-Klausel zu verwenden, um die Ergebnisse auf die ersten `N` geordneten Werte zu beschränken. Die Kombination dieser beiden Klauseln ist die einzige Möglichkeit, um zuverlässig anzugeben, welche Zeilen von TOP betroffen sind.

Sie können TOP mit einem konstanten Wert wie im folgenden Beispiel oder mit einem Variablenwert mithilfe von parametrisierten Abfragen verwenden.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Die Ergebnisse sind:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte](sql-query-getting-started.md)
- [Joins](sql-query-join.md)
- [Unterabfragen](sql-query-subquery.md)
