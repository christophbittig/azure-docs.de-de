---
title: Transformation zum Stringifizieren von Daten in einem Zuordnungsdatenfluss
description: Stringifizieren komplexer Datentypen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: e2d0a5993dddccc65109eb623d8f04f11715ac47
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660037"
---
# <a name="stringify-transformation-in-mapping-data-flow"></a>Transformation zum Stringifizieren in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verwenden Sie die Stringifizierungstransformation, um komplexe Datentypen in Zeichenfolgen zu verwandeln. Dies kann sehr nützlich sein, wenn Sie Spaltendaten, die ursprünglich einen Struktur-, Zuordnungs- oder Arraytyp aufweisen, als eine einzelne Zeichenfolgenentität speichern oder senden müssen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWMTs9]

## <a name="configuration"></a>Konfiguration

Im Konfigurationspanel für Stringifizierungstransformation wählen Sie zuerst den Datentyp aus, der in den Spalten enthalten ist, die Sie inline analysieren möchten. Die Analysetransformation enthält auch die folgenden Konfigurationseinstellungen.

:::image type="content" source="media/data-flow/stringify.png" alt-text="Stringifizierungseinstellungen":::

### <a name="column"></a>Column

Ähnlich wie bei abgeleiteten Spalten und Aggregaten ändern Sie an dieser Stelle eine vorhandene Spalte, indem Sie sie in der Dropdownauswahl auswählen. Sie können hier aber auch den Namen einer neuen Spalte eingeben. ADF speichert die stringifizierten Quelldaten in dieser Spalte. In den meisten Fällen sollten Sie eine neue Spalte definieren, die den Typ des eingehenden komplexen Felds stringifiziert.

### <a name="expression"></a>Ausdruck

Verwenden Sie den Ausdrucks-Generator zum Festlegen des komplexen Quellfelds, das stringifiziert werden soll. Dazu können Sie einfach die Quellspalte mit den eigenständigen Daten, die Sie stringifizieren möchten, auswählen oder aber komplexe Ausdrücke zum Analysieren erstellen.

:::image type="content" source="media/data-flow/stringify-2.png" alt-text="Stringifizierungsausdrücke":::

#### <a name="example-expression"></a>Beispielausdruck

In diesem Beispiel ist ```body.properties.periods``` ein Array innerhalb einer Struktur, die von einer REST-Quelle zurückgegeben wird.

```
body.properties.periods
```

## <a name="data-flow-script"></a>Datenflussskript

```
stringify(mydata = body.properties.periods ? string,
    format: 'json') ~> Stringify1
```

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie die [Vereinfachungstransformation](data-flow-flatten.md) zum Pivotieren von Zeilen zu Spalten.
* Verwenden Sie die [Analysetransformation](data-flow-parse.md), um komplexe eingebettete Typen in separate Spalten zu konvertieren.
* Verwenden Sie die [Transformation für abgeleitete Spalten](data-flow-derived-column.md) zum Pivotieren von Spalten zu Zeilen.
