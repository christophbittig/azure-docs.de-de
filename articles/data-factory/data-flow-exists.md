---
title: Exists-Transformation in einem Zuordnungsdatenfluss
titleSuffix: Azure Data Factory & Azure Synapse
description: Prüfen auf vorhandene Zeilen mithilfe der Exists-Transformation in Azure Data Factory Mapping Data Flow
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 05/07/2020
ms.openlocfilehash: 7ad68638862c8ff348538b2cf9d73a533854f984
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122640797"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Exists-Transformation in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die Exists-Transformation ist eine Zeilenfilterungstransformation, mit der geprüft wird, ob Ihre Daten in einer anderen Quelle oder einem anderen Datenstrom vorhanden sind. Der Ausgabedatenstrom enthält alle Zeilen im linken Datenstrom, die im rechten Datenstrom entweder vorhanden oder nicht vorhanden sind. Die Exists-Transformation ist mit ```SQL WHERE EXISTS``` und ```SQL WHERE NOT EXISTS``` vergleichbar.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>Konfiguration

1. Wählen Sie in der Dropdownliste **Rechter Datenstrom** den Datenstrom aus, den Sie auf Vorhandensein prüfen möchten.
1. Geben Sie in der Einstellung **Exist-Typ** an, ob das Vorhandensein oder das Nichtvorhandensein der Daten geprüft werden soll.
1. Legen Sie fest, ob Sie einen **benutzerdefinierten Ausdruck** verwenden möchten.
1. Wählen Sie die Schlüsselspalten aus, die Sie als Exists-Bedingungen vergleichen möchten. Standardmäßig sucht der Datenfluss nach Übereinstimmung mit einer Spalte in jedem Datenstrom. Wenn der Vergleich auf einem berechneten Wert basieren soll, zeigen Sie mit dem Mauszeiger auf die Dropdownliste für die Spalte, und wählen Sie **Berechnete Spalte** aus.

![Exists-Einstellungen](media/data-flow/exists.png "Exists-Ausdruck 1")

### <a name="multiple-exists-conditions"></a>Mehrere Exists-Bedingungen

Wenn Sie mehrere Spalten aus jedem Datenstrom vergleichen möchten, fügen Sie eine neue Exists-Bedingung hinzu, indem Sie auf das Pluszeichen neben einer vorhandenen Zeile klicken. Jede weitere Bedingung wird durch eine „und“-Anweisung verknüpft. Der Vergleich von zwei Spalten entspricht dem folgenden Ausdruck:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Benutzerdefinierter Ausdruck

Zum Erstellen eines Freiformausdrucks, der andere Operatoren als „und“ und „gleich“ enthält, wählen Sie das Feld **Benutzerdefinierter Ausdruck** aus. Geben Sie einen benutzerdefinierten Ausdruck über den Datenfluss-Ausdrucks-Generator ein, indem Sie auf das blaue Feld klicken.

![Benutzerdefinierte Exists-Einstellungen](media/data-flow/exists1.png "Benutzerdefinierter Exists-Ausdruck")

## <a name="broadcast-optimization"></a>Broadcastoptimierung

![Broadcastjoin](media/data-flow/broadcast.png "Broadcastjoin")

Wenn bei Join, Lookup- und Exists-Transformationen der Arbeitsspeicher des Workerknotens groß genug für einen oder beide Datenströme ist, können Sie die Leistung optimieren, indem Sie die **Übertragung** aktivieren. Standardmäßig entscheidet die Spark-Engine automatisch, ob eine Seite übertragen werden soll oder nicht. Klicken Sie auf **Fest**, um die zu übertragende Seite manuell auszuwählen.

Es wird nicht empfohlen, die Übertragung über die Option **Aus** zu deaktivieren, es sei denn, für Ihre Joins treten Timeoutfehler auf.

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Beispiel

Das folgende Beispiel ist eine Exists-Transformation mit dem Namen `checkForChanges`, die den linken Datenstrom `NameNorm2` und den rechten Datenstrom `TypeConversions` verwendet.  Die Exists-Bedingung ist der Ausdruck `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region`, der „true“ zurückgibt, wenn sowohl die Spalte `EMPID` als auch die Spalte `Region` in den beiden Datenströmen übereinstimmen. Da auf Vorhandensein geprüft wird, entspricht `negate` dem Wert „false“. Wir aktivieren auf der Registerkarte „Optimieren“ keine Übertragung, sodass `broadcast` den Wert `'none'` hat.

Auf der Data Factory-Benutzeroberfläche sieht diese Transformation wie folgt aus:

![Exists-Beispiel](media/data-flow/exists-script.png "Exists-Beispiel")

Das Datenflussskript für diese Transformation befindet sich im folgenden Codeausschnitt:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Nächste Schritte

Ähnliche Transformationen sind [Lookup](data-flow-lookup.md) und [Join](data-flow-join.md).
