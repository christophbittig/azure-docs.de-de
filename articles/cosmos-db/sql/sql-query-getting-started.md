---
title: Erste Schritte mit SQL-Abfragen in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie SQL-Abfragen zum Abfragen von Daten aus Azure Cosmos DB verwenden. Sie können Beispieldaten in einen Container in Azure Cosmos DB hochladen und abfragen.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: tisande
ms.openlocfilehash: af71ff52ead86c0cdeea307e736c36ac27536bfa
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029939"
---
# <a name="getting-started-with-sql-queries"></a>Erste Schritte mit SQL-Abfragen
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Für Azure Cosmos DB-SQL-API-Konten gibt es zwei Möglichkeiten, Daten zu lesen:

**Punktlesevorgänge:** Sie können nach einem Schlüssel/Wert anhand einer einzelnen *Element-ID* und einem Partitionsschlüssel suchen. Die Kombination aus *Element-ID* und Partitionsschlüssel ist der Schlüssel, und das Element selbst ist der Wert. Für ein Dokument mit einer Größe von 1 KB erfordern Punktlesevorgänge in der Regel eine [Anforderungseinheit](../request-units.md) mit einer Wartezeit von unter 10 Millisekunden. Lesevorgänge geben ein einzelnes Element zurück.

Hier finden Sie Beispiele für **Punktlesevorgänge** für die verschiedenen SDKs:

- [.NET SDK](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)
- [Java SDK](/java/api/com.azure.cosmos.cosmoscontainer.readitem#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](/javascript/api/@azure/cosmos/item#read-requestoptions-)
- [Python SDK](/python/api/azure-cosmos/azure.cosmos.containerproxy#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

**SQL-Abfragen:** Sie können Daten auch abfragen, indem Sie mit SQL als JSON-Abfragesprache Abfragen schreiben. Abfragen benötigen immer mindestens 2,3 Anforderungseinheiten. Im Allgemeinen weisen sie auch eine höhere und variablere Wartezeit im Vergleich zu Punktlesevorgängen auf. Abfragen können viele Elemente zurückgeben.

Die meisten leseintensiven Workloads in Azure Cosmos DB verwenden eine Kombination von Punktlesevorgängen und SQL-Abfragen. Wenn Sie nur ein einzelnes Element lesen müssen, sind Punktlesevorgänge günstiger und schneller als Abfragen. Punktlesevorgänge müssen die Abfrage-Engine nicht verwenden, um auf Daten zuzugreifen, und sie können die Daten direkt lesen. Natürlich ist es nicht für alle Workloads möglich, Daten ausschließlich mithilfe von Punktlesevorgängen zu lesen. Die Unterstützung von SQL als Abfragesprache und die vom [Schema unabhängige Indexierung](../index-overview.md) bieten also eine flexiblere Möglichkeit, auf Ihre Daten zuzugreifen.

Hier finden Sie Beispiele für **SQL-Abfragen** für die verschiedenen SDKs:

- [.NET SDK](../sql-api-dotnet-v3sdk-samples.md#query-examples)
- [Java SDK](../sql-api-java-sdk-samples.md#query-examples)
- [Node.js SDK](../sql-api-nodejs-samples.md#item-examples)
- [Python SDK](../sql-api-python-samples.md#item-examples)

Im verbleibenden Teil dieser Dokumentation erfahren Sie, wie Sie SQL-Abfragen in Azure Cosmos DB schreiben. SQL-Abfragen können entweder über das SDK oder das Azure-Portal ausgeführt werden.

## <a name="upload-sample-data"></a>Hochladen von Beispieldaten

Öffnen Sie in Ihrem Cosmos DB-Konto der SQL-API den [Data Explorer](../data-explorer.md), um einen Container mit dem Namen `Families` zu erstellen. Verwenden Sie nach dem Erstellen des Containers den Datenstrukturbrowser, um ihn zu suchen und zu öffnen. In Ihrem `Families`-Container wird die Option `Items` direkt unterhalb des Namens des Containers angezeigt. Öffnen Sie diese Option. Daraufhin wird auf der Menüleiste in der Mitte des Bildschirms eine Schaltfläche angezeigt, mit der Sie ein neues Element erstellen können. Sie verwenden diese Funktion, um die folgenden JSON-Elemente zu erstellen.

### <a name="create-json-items"></a>Erstellen von JSON-Elementen

Die folgenden beiden JSON-Elemente sind Dokumente zu den Familien „Andersen“ und „Wakefield“. Sie enthalten Eltern, Kinder, Haustiere, Adresse und Registrierungsinformationen. 

Das erste Element enthält Zeichenfolgen, Zahlen, boolesche Werte, Arrays und geschachtelte Eigenschaften:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Das zweite Element verwendet `givenName` und `familyName` anstelle von `firstName` und `lastName`:

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Abfragen der JSON-Elemente

Führen Sie einige Abfragen für die JSON-Daten aus, um einige der Schlüsselaspekte der SQL-Abfragesprache von Azure Cosmos DB besser zu verstehen.

Die folgende Abfrage gibt die Elemente zurück, in denen das `id`-Feld dem Text `AndersenFamily` entspricht. Da es sich um eine `SELECT *`-Abfrage handelt, ist die Ausgabe der Abfrage das komplette JSON-Element. Weitere Informationen zur SELECT-Syntax finden Sie unter [SELECT-Anweisung](sql-query-select.md).

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Abfrage hat folgende Ergebnisse:

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

Mit der folgenden Abfrage wird die JSON-Ausgabe auf andere Weise neu formatiert. Die Abfrage projiziert ein neues JSON-Objekt `Family` mit zwei ausgewählten Feldern (`Name` und `City`), wenn die Stadt in der Adresse dem Staat entspricht. Dies ist bei „NY, NY“ der Fall.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Die Abfrage hat folgende Ergebnisse:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Die folgende Abfrage gibt alle Vornamen von Kindern der Familie zurück, deren `id` dem Text `WakefieldFamily` entspricht, und zwar geordnet nach der Stadt.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Die Ergebnisse sind:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Bemerkungen

Die Beispiele oben zeigen verschiedene Aspekte der Cosmos DB-Abfragesprache:  

* Da die SQL-API mit JSON-Werten arbeitet, werden baumförmige Entitäten anstelle von Spalten und Zeilen verarbeitet. Sie können auf die Baumknoten in beliebiger Tiefe verweisen, z.B. `Node1.Node2.Node3…..Nodem`, ähnlich dem zweiteiligen Verweis `<table>.<column>` in ANSI SQL.

* Da die Abfragesprache mit schemalosen Daten arbeitet, muss das Typsystem dynamisch gebunden werden. Der gleiche Ausdruck kann unterschiedliche Typen in unterschiedlichen Elementen ergeben. Das Ergebnis einer Abfrage ist ein gültiger JSON-Wert, aber nicht garantiert innerhalb eines festen Schemas.  

* Azure Cosmos DB unterstützt nur strikte JSON-Elemente. Typsystem und Ausdrücke sind auf JSON-Typen beschränkt. Weitere Informationen finden Sie in der [JSON-Spezifikation](https://www.json.org/).  

* Ein Cosmos-Container ist eine schemalose Sammlung von JSON-Elementen. Die Beziehungen innerhalb und zwischen Containerelementen werden implizit nach Eigenständigkeit erfasst, nicht nach Beziehungen von primären Schlüsseln und Fremdschlüsseln. Dieses Feature ist für die elementinternen Verknüpfungen wichtig, die in [Verknüpfungen in Azure Cosmos DB](sql-query-join.md)beschrieben werden.

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in Azure Cosmos DB](../introduction.md)
- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT-Klausel](sql-query-select.md)
- Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    - Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
    - Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](../estimate-ru-with-capacity-planner.md)
