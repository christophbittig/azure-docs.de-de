---
title: Vereinfachungstransformation im Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Denormalisieren Sie hierarchische Daten mithilfe der Vereinfachungstransformation.
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: e632260e8af6e4bac9fac9ec43f25bf636b98b4d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122639993"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Vereinfachungstransformation im Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verwenden Sie die Vereinfachungstransformation, um Arraywerte in hierarchischen Strukturen wie JSON auszuwählen und in einzelne Zeilen aufzulösen. Dieser Prozess wird als „Denormalisierung“ bezeichnet.

## <a name="configuration"></a>Konfiguration

Die Vereinfachungstransformation enthält die folgenden Konfigurationseinstellungen:

![Einstellungen vereinfachen](media/data-flow/flatten1.png "Vereinfachen von Einstellungen")

### <a name="unroll-by"></a>Auflösen

Wählen Sie ein Array aus, das aufgelöst werden soll. Die Ausgabedaten enthalten eine Zeile pro Element in jedem Array. Wenn das Array „Auflösen“ in der Eingabezeile NULL oder leer ist, gibt es eine einzige Ausgabezeile mit nicht aufgelösten Werten als NULL.

### <a name="unroll-root"></a>Stamm auflösen

Standardmäßig löst die Vereinfachungstransformation ein Array an den Anfang der Hierarchie auf, in der es sich befindet. Sie können optional ein Array auswählen, bei dem der Stamm aufgelöst werden soll. „Stamm auflösen“ muss ein Array komplexer Objekte sein, dass entweder das Array „Auflösen“ ist oder dieses Array enthält. Wenn „Stamm auflösen“ ausgewählt wird, enthalten die Ausgabedaten mindestens eine Zeile pro Elemente im aufgelösten Stamm. Wenn die Eingabezeile keine Elemente in „Stamm auflösen“ enthält, wird sie aus den Ausgabedaten gelöscht. Wenn Sie „Stamm auflösen“ auswählen, wird immer eine Anzahl von Zeilen ausgegeben, die kleiner als das Standardverhalten oder mit ihm identisch ist.

### <a name="flatten-mapping"></a>Vereinfachen der Zuordnung

Wählen Sie ähnlich wie bei der Auswahltransformation die Projektion der neuen Struktur aus eingehenden Feldern und dem denormalisierten Array aus. Wenn ein denormalisiertes Array zugeordnet wird, stimmt der Datentyp der Ausgabespalte mit demjenigen des Arrays überein. Wenn das Array „Auflösen“ ein Array komplexer Objekte ist, das Subarrays enthält, wird bei der Zuordnung eines Elements aus diesem Subarray ein Array ausgegeben.

Überprüfen Sie Ihre Ausgabe der Zuordnung auf der Registerkarte „Überprüfen“ und in der Datenvorschau.

## <a name="rule-based-mapping"></a>Regelbasierte Zuordnung

Die Transformation zur Vereinfachung unterstützt die regelbasierte Zuordnung, sodass Sie dynamische und flexible Transformationen erstellen können, die Arrays basierend auf Regeln und Strukturen basierend auf Hierarchieebenen vereinfachen.

![Muster vereinfachen](media/data-flow/flatten-pattern.png "Muster vereinfachen")

### <a name="matching-condition"></a>Vergleichsbedingung

Geben Sie eine Musterabgleichsbedingung für die Spalte oder Spalten ein, die mithilfe von exakten Übereinstimmungen oder Mustern vereinfacht werden sollen. Beispiel: ```like(name,'cust%')```

### <a name="deep-column-traversal"></a>Tiefer Spaltendurchlauf

Mithilfe dieser optionalen Einstellung werden alle Unterspalten eines komplexen Objekts einzeln verarbeitet, statt das komplexe Objekt als ganze Spalte zu verarbeiten.

### <a name="hierarchy-level"></a>Hierarchieebene

Wählen Sie die Ebene der Hierarchie aus, die Sie erweitern möchten.

### <a name="name-matches-regex"></a>Name stimmt überein (RegEx)

Optional können Sie ihren Namensabgleich als regulären Ausdruck in diesem Feld ausdrücken, anstatt die obige Vergleichsbedingung zu verwenden.

## <a name="examples"></a>Beispiele

Sehen Sie sich das nachstehende JSON-Objekt im Zusammenhang mit den folgenden Beispielen zur Vereinfachungstransformation an.

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Kein „Stamm auflösen“ bei Zeichenfolgenarray

| Auflösen | Stamm auflösen | Projektion |
| --------- | ----------- | ---------- |
| goods.customers | Keine | name <br> customer = goods.customer |

#### <a name="output"></a>Ausgabe

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Kein „Stamm auflösen“ bei komplexem Array

| Auflösen | Stamm auflösen | Projektion |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItems | Keine | name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemNameitemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> location = location |

#### <a name="output"></a>Ausgabe

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Dasselbe Stammverzeichnis wie „Array auflösen“

| Auflösen | Stamm auflösen | Projektion |
| --------- | ----------- | ---------- |
| goods.orders | goods.orders | name <br> goods.orders.shipped.orderItems.itemName <br> goods.customers <br> location |

#### <a name="output"></a>Ausgabe

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>„Stamm auflösen“ bei komplexem Array

| Auflösen | Stamm auflösen | Projektion |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | goods.orders |name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemNameitemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> location = location |

#### <a name="output"></a>Ausgabe

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Beispiel

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Nächste Schritte

* Verwenden der [Pivotierungstransformation](data-flow-pivot.md) zum Pivotieren von Zeilen zu Spalten
* Verwenden Sie die [Entpivotierungstransformation](data-flow-unpivot.md) zum Pivotieren von Spalten in Zeilen.
