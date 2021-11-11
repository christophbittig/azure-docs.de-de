---
title: 'Referenz zur Azure Digital Twins-Abfragesprache: SELECT-Klausel'
titleSuffix: Azure Digital Twins
description: Referenzdokumentation zur SELECT-Klausel der Azure Digital Twins-Abfragesprache
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: c911a3ea8607cb83cb3d96a44bfac424042df30a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232866"
---
# <a name="azure-digital-twins-query-language-reference-select-clause"></a>Referenz zur Azure Digital Twins-Abfragesprache: SELECT-Klausel

Dieses Dokument enthält Referenzinformationen zur **SELECT-Klausel** für die [Azure Digital Twins-Abfragesprache](concepts-query-language.md).

Die SELECT-Klausel ist der erste Teil einer Abfrage. Sie gibt die Liste der Spalten an, die von der Abfrage zurückgegeben wird.

Diese Klausel ist für alle Abfragen erforderlich.

## <a name="select-"></a>SELECT *

Verwenden Sie das Zeichen `*` in einer SELECT-Anweisung, um das digitale Zwillingsdokument so zu projizieren, wie es ist, ohne es einer Eigenschaft im Resultset zuzuweisen.

>[!NOTE]
> `SELECT *` ist nur gültige Syntax, wenn die Abfrage kein `JOIN`-Element verwendet. Weitere Informationen zu Abfragen mit `JOIN` finden Sie unter [Referenz zur Azure Digital Twins-Abfragesprache: JOIN-Klausel](reference-query-clause-join.md).

### <a name="syntax"></a>Syntax

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectSyntax":::

### <a name="returns"></a>Rückgabe

Die von der Abfrage zurückgegebenen Eigenschaften.

### <a name="example"></a>Beispiel

Die folgende Abfrage gibt alle digitalen Zwillinge in der Instanz zurück. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectExample":::

## <a name="select-columns-with-projections"></a>SELECT-Spalten mit Projektionen

Sie können Projektionen in der SELECT-Klausel verwenden, um auszuwählen, welche Spalten eine Abfrage zurückgibt. Sie können benannte Sammlungen von Zwillingen und Beziehungen oder Eigenschaften von Zwillingen und Beziehungen angeben.

Projektion wird jetzt sowohl für primitive als auch für komplexe Eigenschaften unterstützt.

### <a name="syntax"></a>Syntax

So projizieren Sie eine Sammlung:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectCollectionSyntax":::

So projizieren Sie eine Eigenschaft:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertySyntax":::

### <a name="returns"></a>Rückgabe

Eine Sammlung von Zwillingen, Eigenschaften oder Beziehungen, die in der Projektion angegeben wird.

Wenn eine in der Projektion enthaltene Eigenschaft für eine bestimmte Datenzeile nicht vorhanden ist, ist die Eigenschaft nicht im Resultset vorhanden. Ein Beispiel für dieses Verhalten finden Sie unter [Beispiel für das Projizieren einer Eigenschaft: Eigenschaft für Datenzeile nicht vorhanden](#project-property-example-property-not-present-for-a-data-row).

### <a name="examples"></a>Beispiele

#### <a name="example-scenario"></a>Beispielszenario

Nehmen Sie für die folgenden Beispiele ein Zwillingsdiagramm an, das die folgenden Datenelemente enthält:
* Einen Factoryzwilling namens „FactoryA“
    - Enthält eine Eigenschaft namens `name` mit dem Wert `FactoryA`
* Einen Consumerzwilling namens „Contoso“
    - Enthält eine Eigenschaft namens `name` mit dem Wert `Contoso`
* Eine consumerRelationship-Beziehung von FactoryA zu Contoso mit dem Namen `FactoryA-consumerRelationship-Contoso`
    - Enthält eine Eigenschaft namens `managedBy` mit dem Wert `Jeff`

Diese Abbildung veranschaulicht dieses Szenario:

:::row:::
    :::column:::
        :::image type="content" source="media/reference-query-clause-select/projections-graph.png" alt-text="Abbildung, die das oben beschriebene Beispieldiagramm zeigt.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="project-collection-example"></a>Beispiel für das Projizieren einer Sammlung

Im Folgenden finden Sie eine Beispielabfrage, mit der eine Sammlung aus diesem Diagramm projiziert wird. Die folgende Abfrage gibt alle digitalen Zwillinge in der Instanz zurück, indem die gesamte Zwillingssammlung `T` genannt und `T` als zurückzugebende Sammlung projiziert wird. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectCollectionExample":::

Von dieser Abfrage werden die folgenden JSON-Nutzdaten zurückgegeben:

```json
{
  "value": [
    {
      "result": [
        {
          "T": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          }
        },
        {
          "T": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-with-join-example"></a>Beispiel für das Projizieren mit JOIN

Projektion wird häufig verwendet, um eine in einem `JOIN` angegebene Sammlung zurückzugeben. Bei der folgenden Abfrage wird eine Projektion verwendet, um die Daten des Consumers, der Factory und der Beziehung zurückzugeben. Weitere Informationen zur im Beispiel verwendeten `JOIN`-Syntax finden Sie unter [Referenz zur Azure Digital Twins-Abfragesprache: JOIN-Klausel](reference-query-clause-join.md).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectJoinExample":::

Von dieser Abfrage werden die folgenden JSON-Nutzdaten zurückgegeben:

```json
{
  "value": [
    {
      "result": [
        {
          "Consumer": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          },
          "Factory": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          },
          "Relationship": {
            "$etag": "W/\"f01e07c1-19e4-4bbe-a12d-f5761e86d3e8\"",
            "$relationshipId": "FactoryA-consumerRelationship-Contoso",
            "$relationshipName": "consumerRelationship",
            "$sourceId": "FactoryA",
            "$targetId": "Contoso",
            "managedBy": "Jeff"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-property-example"></a>Beispiel für das Projizieren mit einer Eigenschaft

In diesem Beispiel wird eine Eigenschaft projiziert. Die folgende Abfrage verwendet Projektion, um die `name`-Eigenschaft des Consumerzwillings und die `managedBy`-Eigenschaft der Beziehung zurückzugeben.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertyExample":::

Von dieser Abfrage werden die folgenden JSON-Nutzdaten zurückgegeben:

```json
{
  "value": [
    {
      "result": [
        {
          "managedBy": "Jeff",
          "name": "Contoso"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-property-example-property-not-present-for-a-data-row"></a>Beispiel für das Projizieren einer Eigenschaft: Eigenschaft für eine Datenzeile nicht vorhanden

Wenn eine in der Projektion enthaltene Eigenschaft für eine bestimmte Datenzeile nicht vorhanden ist, ist die Eigenschaft nicht im Resultset vorhanden.

Nehmen Sie für dieses Beispiel eine Gruppe von Zwillingen an, die Personen darstellen. Einigen Zwillingen ist ein Alter zugeordnet, anderen nicht.

In der folgenden Abfrage werden die Eigenschaften `name` und `age` projiziert:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertyNotPresentExample":::

Das Ergebnis könnte in etwa wie folgt aussehen, wobei die Eigenschaft `age` für einige Zwillinge im Ergebnis fehlt, da die betreffenden Zwillinge nicht über diese Eigenschaft verfügen.

```json
{
  "value": [
    {
      "result": [
        {
          "name": "John",
          "age": 27
        },
        {
          "name": "Keanu"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

## <a name="select-count"></a>SELECT COUNT

Verwenden Sie diese Methode, um die Anzahl der Elemente im Resultset zu zählen und diese Zahl zurückzugeben.

### <a name="syntax"></a>Syntax

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountSyntax":::

### <a name="arguments"></a>Argumente

Keine.

### <a name="returns"></a>Gibt zurück

Ein `int`-Wert.

### <a name="example"></a>Beispiel

Die folgende Abfrage gibt die Anzahl aller digitalen Zwillinge in der Instanz zurück.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountExample":::

Die folgende Abfrage gibt die Anzahl aller Beziehungen in der Instanz zurück.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountRelationshipsExample":::

## <a name="select-top"></a>SELECT TOP

Verwenden Sie diese Methode, um nur einige der wichtigsten Elemente zurückzugeben, die die Abfrageanforderungen erfüllen.

### <a name="syntax"></a>Syntax

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectTopSyntax":::

### <a name="arguments"></a>Argumente

Ein `int`-Wert, der die Anzahl der wichtigsten Elemente angibt, die ausgewählt werden sollen.

### <a name="returns"></a>Gibt zurück

Eine Sammlung von Zwillingen.

### <a name="example"></a>Beispiel

Die folgende Abfrage gibt nur die ersten fünf digitalen Zwillinge in der Instanz zurück.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectTopExample":::