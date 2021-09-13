---
title: Erstellen von Richtlinien für Arrayeigenschaften für Ressourcen
description: Erfahren Sie, wie Sie mit Arrayparametern und Arrayausdrücken arbeiten, den [*]-Alias auswerten und Elemente mit Azure Policy-Definitionsregeln anfügen.
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: aefa5439e7176457494d6c21ffb9aa79acc4fd3c
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351423"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Erstellen von Richtlinien für Arrayeigenschaften für Azure-Ressourcen

Azure Resource Manager-Eigenschaften werden häufig als Zeichenfolgen und boolesche Werte definiert. Im Fall einer 1: n-Beziehung werden komplexe Eigenschaften stattdessen als Arrays definiert. In Azure Policy werden Arrays auf verschiedene Weise verwendet:

- Als Typ eines [Definitionsparameters](../concepts/definition-structure.md#parameters), um mehrere Optionen bereitzustellen
- Als Teil einer [Richtlinienregel](../concepts/definition-structure.md#policy-rule) mit den Bedingungen **in** oder **notIn**
- Teil einer Richtlinienregel, die zählt, wie viele Arraymember eine Bedingung erfüllen.
- Mit [append](../concepts/effects.md#append) und [modify](../concepts/effects.md#modify) ist das Ergebnis die Aktualisierung eines vorhandenen Arrays.

Dieser Artikel befasst sich mit jedem dieser Verwendungsfälle in Azure Policy und enthält mehrere Beispieldefinitionen.

## <a name="parameter-arrays"></a>Parameterarrays

### <a name="define-a-parameter-array"></a>Definieren eines Parameterarrays

Durch das Definieren eines Parameters als Array kann die Richtlinie flexibel gestaltet werden, wenn mehrere Werte erforderlich sind.
Diese Richtliniendefinition lässt einen beliebigen einzelnen Ort für den Parameter **AllowedLocations** zu (der Standardwert ist _eastus2_):

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Da **type** auf _string_ festgelegt ist, kann beim Zuweisen der Richtlinie nur ein Wert festgelegt werden. Wenn diese Richtlinie zugewiesen ist, sind Ressourcen innerhalb des Bereichs nur in einer einzigen Azure-Region zulässig. Die meisten Richtliniendefinitionen müssen eine Liste genehmigter Optionen zulassen, z. B. _eastus2_, _eastus_ und _westus2_.

Verwenden Sie zum Erstellen einer Richtliniendefinition, die mehrere Optionen zulässt, den **Typ** _array_. Diese Richtlinie kann wie folgt umgeschrieben werden:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Nachdem eine Richtliniendefinition gespeichert wurde, kann die **type**-Eigenschaft eines Parameters nicht mehr geändert werden.

Diese neue Parameterdefinition akzeptiert während der Richtlinienzuweisung mehrere Werte. Wenn die Arrayeigenschaft **allowedValues** definiert ist, werden die bei der Zuweisung verfügbaren Werte zusätzlich auf die vordefinierte Liste von Optionen beschränkt. Die Verwendung von **allowedValues** ist optional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Übergeben von Werten an ein Parameterarray während der Zuweisung

Wenn Sie die Richtlinie über das Azure-Portal zuweisen, wird ein Parameter vom **Typ** _array_ als ein einzelnes Textfeld angezeigt. Der Hinweis lautet „Trennen Sie die Werte durch Semikola (;). (Beispiel: London;New York)“. Verwenden Sie die folgende Zeichenfolge, um die zulässigen Werte _eastus2_, _eastus_ und _westus2_ für den Ort an den Parameter zu übergeben:

`eastus2;eastus;westus2`

Das Format für den Parameterwert unterscheidet sich bei der Verwendung der Azure-Befehlszeilenschnittstelle (Azure CLI), von Azure PowerShell oder der REST-API. Die Werte werden in Form einer JSON-Zeichenfolge übergeben, die auch den Namen des Parameters enthält.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Die Befehle zur Verwendung dieser Zeichenfolge mit den einzelnen SDKs lauten wie folgt:

- Azure CLI: Befehl [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) mit dem Parameter **params**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) mit dem Parameter **PolicyParameter**
- REST-API: Im _PUT_-Vorgang [create](/rest/api/policy/policyassignments/create) als Teil des Anforderungstextes als Wert der **properties.parameters**-Eigenschaft

## <a name="using-arrays-in-conditions"></a>Verwenden von Arrays in Bedingungen

### <a name="in-and-notin"></a>„in“ und „notIn“

Die `in`- und `notIn`-Bedingungen funktionieren nur mit Arraywerten. Sie überprüfen, ob ein Wert in einem Array vorhanden ist. Das Array kann ein literales JSON-Array oder ein Verweis auf einen Arrayparameter sein. Beispiel:

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>Wertzählung

Der [Wertzählungsausdruck](../concepts/definition-structure.md#value-count) zählt, wie viele Arraymember eine Bedingung erfüllen. Er bietet eine Möglichkeit, dieselbe Bedingung mehrmals auszuwerten, wobei für jede Iterationen unterschiedliche Werte verwendet werden. Mit der folgenden Bedingung wird beispielsweise überprüft, ob der Ressourcenname mit einem beliebigen Muster aus einem Array von Mustern übereinstimmt:

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Um den Ausdruck auszuwerten, wird die `where`-Bedingung in Azure Policy dreimal ausgewertet, einmal für jeden Member von `[ "test*", "dev*", "prod*" ]`. Dabei wird gezählt, wie viele Male sie als `true` ausgewertet wurde. Bei jeder Iterationen wird der Wert des aktuellen Arraymembers mit dem `pattern`-Indexnamen gekoppelt, der durch `count.name` definiert ist. Auf diesen Wert kann innerhalb der `where`-Bedingung verwiesen werden, indem eine besondere Vorlagenfunktion aufgerufen wird: `current('pattern')`.

| Iteration | `current('pattern')`-Rückgabewert |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

Die Bedingung ist nur dann TRUE, wenn die ermittelte Anzahl größer als 0 ist.

Verwenden Sie einen Parameterverweis anstelle eines Literalarrays, um die Bedingung oben generischer zu gestalten:

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Wenn sich der **Wertzählungsausdruck** nicht unter einem anderen **count**-Ausdruck befindet, ist `count.name` optional, und die `current()`-Funktion kann ohne Argumente verwendet werden:

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

Die **Wertzählung** unterstützt auch Arrays komplexer Objekte, sodass komplexere Bedingungen möglich sind. Die folgende Bedingung definiert z. B. einen gewünschten Tagwert für jedes Namensmuster und überprüft, ob der Ressourcenname mit dem Muster übereinstimmt, jedoch nicht über den erforderlichen Tagwert verfügt:

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

Nützliche Beispiele finden Sie unter [Beispiele für die Wertzählung](../concepts/definition-structure.md#value-count-examples).

## <a name="referencing-array-resource-properties"></a>Verweisen auf Arrayeigenschaften in Ressourcen

Bei vielen Anwendungsfällen werden die Arrayeigenschaften in der ausgewerteten Ressource benötigt. In einigen Szenarien muss auf ein gesamtes Array verwiesen werden (z. B. zur Überprüfung der Länge). In anderen Szenarien ist es erforderlich, dass eine Bedingung jeweils auf die einzelnen Arraymember angewandt wird (z. B. zum Sicherstellen, dass der Zugriff aus dem Internet durch alle Firewallregeln blockiert wird). Entscheidend zum Schreiben von Bedingungen für diese Szenarien ist die Kenntnis der verschiedenen Methoden, mit denen in Azure Policy auf Ressourceneigenschaften verwiesen werden kann, sowie des Verhaltens dieser Verweise beim Referenzieren von Arrayeigenschaften.

### <a name="referencing-resource-properties"></a>Verweisen auf Ressourceneigenschaften

Auf Ressourceneigenschaften kann in Azure Policy mithilfe von [Aliasen](../concepts/definition-structure.md#aliases) verwiesen werden. Zwei Möglichkeiten stehen zur Verfügung, um auf die Werte einer Ressourceneigenschaft in Azure Policy zu verweisen:

- Verwenden Sie die [field](../concepts/definition-structure.md#fields)-Bedingung, um zu überprüfen, ob **alle** ausgewählten Ressourceneigenschaften eine Bedingung erfüllen. Beispiel:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Verwenden Sie die `field()`-Funktion, um auf den Wert einer Eigenschaft zuzugreifen. Beispiel:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

Die field-Bedingung weist ein implizites „alle von“-Verhalten auf. Wenn der Alias eine Sammlung von Werten darstellt, wird mit dieser Bedingung überprüft, ob alle einzelnen Werte die Bedingung erfüllen. Die `field()`-Funktion gibt die durch den Alias dargestellten Werte unverändert zurück, die dann über andere Vorlagenfunktionen bearbeitet werden können.

### <a name="referencing-array-fields"></a>Verweisen auf Arrayfelder

Arrayeigenschaften in Ressourcen werden durch zwei unterschiedliche Typen von Aliasen dargestellt. Durch einen „normalen“ Alias und [Arrayaliase](../concepts/definition-structure.md#understanding-the--alias), an die `[*]` angefügt ist:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Verweisen auf das Array

Der erste Alias stellt einen einzelnen Wert dar: den Wert der `stringArray`-Eigenschaft aus dem Anforderungsinhalt. Da es sich bei dem Wert dieser Eigenschaft um ein Array handelt, ist er in Richtlinienbedingungen nicht nützlich. Beispiel:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Diese Bedingung vergleicht das gesamte `stringArray`-Array mit einem einzelnen Zeichenfolgenwert. Die meisten Bedingungen (einschließlich `equals`) unterstützen nur Zeichenfolgenwerte, sodass der Vergleich eines Arrays mit einer Zeichenfolge wenig sinnvoll ist. Das Hauptszenario, in dem der Verweis auf die Arrayeigenschaft sinnvoll ist, stellt die Überprüfung ihres Vorhandenseins dar:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

Bei der `field()`-Funktion ist der zurückgegebene Wert das Array aus dem Anforderungsinhalt, das dann mit allen [unterstützten Vorlagenfunktionen](../concepts/definition-structure.md#policy-functions) verwendet werden kann, die Arrayargumente unterstützen. Mit der folgenden Bedingung wird beispielsweise überprüft, ob die Länge von `stringArray` größer als 0 (null) ist:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Verweisen auf die Sammlung der Arraymember

Aliase, die die `[*]`-Syntax verwenden, stellen eine **Sammlung von aus einer Arrayeigenschaft ausgewählten Eigenschaftswerten** dar. Dies unterscheidet sich von der Auswahl der Arrayeigenschaft selbst. Im Fall von `Microsoft.Test/resourceType/stringArray[*]` wird eine Sammlung zurückgegeben, die alle Member von `stringArray` enthält. Wie zuvor erwähnt, überprüft eine `field`-Bedingung, ob alle ausgewählten Ressourceneigenschaften die Bedingung erfüllen. Daher ist die folgende Bedingung nur TRUE, wenn **alle** Member von `stringArray` gleich „value“ sind.

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Wenn das Array Objekte enthält, kann ein `[*]`-Alias verwendet werden, um den Wert einer bestimmten Eigenschaft aus jedem Arraymember auszuwählen. Beispiel:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Diese Bedingung ist TRUE, wenn die Werte aller `property`-Eigenschaften in `objectArray` gleich `"value"` sind. Weitere Beispiele finden Sie unter [Zusätzliche \[\*\]-Aliasbeispiele](#additional--alias-examples).

Wenn Sie die `field()`-Funktion verwenden, um auf einen Arrayalias zu verweisen, ist der zurückgegebene Wert ein Array aller ausgewählten Werte. Durch dieses Verhalten wird der gängige Anwendungsfall der `field()`-Funktion, d. h. die Fähigkeit zum Anwenden von Vorlagenfunktionen auf Ressourceneigenschaftswerte, eingeschränkt. In diesem Fall können nur Vorlagenfunktionen verwendet werden, die Arrayargumente unterstützen. Beispielsweise ist es möglich, mit `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` die Länge des Arrays abzurufen. Komplexere Szenarien wie das Anwenden einer Vorlagenfunktion auf die einzelnen Arraymember und das Vergleichen mit einem gewünschten Wert sind jedoch nur bei Verwendung des `count`-Ausdrucks möglich. Weitere Informationen finden Sie unter [Feldzählungsausdrücke](#field-count-expressions).

Zur Zusammenfassung folgt ein Beispiel für einen Ressourceninhalt und die von verschiedenen Aliasen zurückgegebenen ausgewählten Werte:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

Bei Verwendung der field-Bedingung im Beispielressourceninhalt sehen die Ergebnisse wie folgt aus:

| Alias | Ausgewählte Werte |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Leere Sammlung von Werten |
| `Microsoft.Test/resourceType/missingArray[*].property` | Leere Sammlung von Werten |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

Bei Verwendung der `field()`-Funktion im Beispielressourceninhalt sehen die Ergebnisse wie folgt aus:

| Ausdruck | Rückgabewert |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

### <a name="field-count-expressions"></a>Feldzählungsausdrücke

[Feldzählungsausdrücke](../concepts/definition-structure.md#field-count) zählen, wie viele Arraymember eine Bedingung erfüllen, und vergleichen die Anzahl mit einem Zielwert. `Count` ist im Vergleich zu `field` Bedingungen intuitiver und vielseitiger anwendbar für das Auswerten von Arrays. Die Syntax ist:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Bei Verwendung ohne eine `where`-Bedingung gibt `count` lediglich die Länge eines Arrays zurück. Mit dem Beispielressourceninhalt aus dem vorherigen Abschnitt wird der folgende `count`-Ausdruck als `true` ausgewertet, da `stringArray` drei Member hat:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Dieses Verhalten gilt auch bei geschachtelten Arrays. Beispielsweise wird der folgende `count`-Ausdruck als `true` ausgewertet, weil in den `nestedArray`-Arrays vier Arraymember enthalten sind:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

Die eigentliche Stärke von `count` zeigt sich in der `where`-Bedingung. Wenn diese angegeben ist, werden in Azure Policy die Arraymember gezählt und jeweils für die Bedingung ausgewertet. Dabei wird gezählt, wie viele Arraymember als `true` ausgewertet werden. Konkret wird in Azure Policy in jeder Iteration der Auswertung der `where`-Bedingung der einzelne Arraymember „**i**“ ausgewählt und der Ressourceninhalt für die `where`-Bedingung ausgewertet, _*als ob ***i**_ der einzige Member des Arrays wäre_*. Die Tatsache, dass in jeder Iteration nur ein Arraymember zur Verfügung steht, bietet die Möglichkeit, komplexe Bedingungen auf jeden einzelnen Arraymember anzuwenden.

Beispiel:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```

Um den `count`-Ausdruck auszuwerten, wird die `where`-Bedingung in Azure Policy dreimal ausgewertet, einmal für jeden Member von `stringArray`. Dabei wird gezählt, wie viele Male sie als `true` ausgewertet wurde.
Wenn die `where`-Bedingung auf die `Microsoft.Test/resourceType/stringArray[*]`-Arraymember verweist, anstatt dass alle Member von `stringArray` ausgewählt werden, wird jeweils nur ein einzelner Arraymember ausgewählt:

| Iteration | Ausgewählte Werte von `Microsoft.Test/resourceType/stringArray[*]` | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

Der `count`-Ausdruck gibt `1` zurück.

Hier ein komplexerer Ausdruck:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iteration | Ausgewählte Werte | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

Der `count`-Ausdruck gibt `1` zurück.

Aufgrund der Tatsache, dass der `where`-Ausdruck für den **gesamten** Anforderungsinhalt ausgewertet wird (mit Änderungen nur am aktuell ausgewerteten Arraymember), kann die `where`-Bedingung auch auf Felder außerhalb des Arrays verweisen:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  },
  "equals": 0
}
```

| Iteration | Ausgewählte Werte | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Mit geschachtelten Zählungsausdrücken können Bedingungen auf geschachtelte Arrayfelder angewendet werden. Mit der folgenden Bedingung wird beispielsweise überprüft, ob das `objectArray[*]`-Array über genau zwei Member mit dem `nestedArray[*]`-Array verfügt, das mindestens einen Member enthält:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| Iteration | Ausgewählte Werte | Ergebnis der Auswertung der geschachtelten Zählung |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` hat 2 Member => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` hat 2 Member => `true` |

Da beide Member von `objectArray[*]` ein untergeordnetes Array `nestedArray[*]` mit zwei Membern enthalten, gibt der äußere Zählungsausdruck `2` zurück.

Komplexeres Beispiel: Überprüfen Sie, ob das `objectArray[*]`-Array genau zwei Member mit `nestedArray[*]` mit beliebigen Membern enthält, die gleich `2` oder `3` sind:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
        "where": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "in": [ 2, 3 ]
        }
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| Iteration | Ausgewählte Werte | Ergebnis der Auswertung der geschachtelten Zählung
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` enthält `2` => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` enthält `3` => `true` |

Da beide Member von `objectArray[*]` ein untergeordnetes Array `nestedArray[*]` aufweisen, das entweder `2` oder enthält `3`, gibt der äußere Zählungsausdruck `2` zurück.

> [!NOTE]
> Geschachtelte Feldzählungsausdrücke können nur auf geschachtelte Arrays verweisen. Beispielsweise kann der auf `Microsoft.Test/resourceType/objectArray[*]` verweisende Zählungsausdruck eine geschachtelte Zählung aufweisen, die auf das geschachtelte Array `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` gerichtet ist, aber keinen geschachtelten Zählungsausdruck, der auf `Microsoft.Test/resourceType/stringArray[*]` gerichtet ist.

#### <a name="accessing-current-array-member-with-template-functions"></a>Zugreifen auf den aktuellen Arraymember mit Vorlagenfunktionen

Wenn Sie Vorlagenfunktionen verwenden, verwenden Sie die `current()`-Funktion, um auf den Wert des aktuellen Arraymembers oder die Werte seiner Eigenschaften zuzugreifen. Um auf den Wert des aktuellen Arraymembers zuzugreifen, übergeben Sie den Alias, der in `count.field` oder einem seiner untergeordneten Aliase definiert ist, als Argument an die `current()`-Funktion. Beispiel:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| Iteration | `current()`-Rückgabewert | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | Der Wert von `property` im ersten Member von `objectArray[*]`: `value1` | `true` |
| 2 | Der Wert von `property` im ersten Member von `objectArray[*]`: `value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>Die Feldfunktion in where-Bedingungen

Die `field()`-Funktion kann auch verwendet werden, um auf den Wert des aktuellen Arraymembers zuzugreifen, sofern sich der **count**-Ausdruck nicht innerhalb einer **Existenzbedingung** befindet (die `field()`-Funktion verweist immer auf die Ressource, die in der **if**-Bedingung ausgewertet wird). Das Verhalten von `field()` beim Verweisen auf das ausgewertete Array basiert auf den folgenden Konzepten:

1. Arrayaliase werden in einer Sammlung von Werten aufgelöst, die aus allen Arraymembern ausgewählt werden.
1. `field()`-Funktionen, die auf Arrayaliase verweisen, geben ein Array mit den ausgewählten Werten zurück.
1. Durch Verweisen auf den gezählten Arrayalias innerhalb der `where`-Bedingung wird eine Sammlung mit einem einzelnen Wert zurückgegeben, der aus dem Arraymember ausgewählt wird, der in der aktuellen Iteration ausgewertet wird.

Durch dieses Verhalten wird beim Verweisen auf den gezählten Arraymember mit einer `field()`-Funktion innerhalb der `where`-Bedingung **ein Array mit einem einzelnen Member** zurückgegeben. Auch wenn dieses Verhalten vielleicht nicht intuitiv ist, entspricht es dem Grundgedanken, dass Arrayaliase immer eine Sammlung von ausgewählten Eigenschaften zurückgeben. Hier sehen Sie ein Beispiel:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iteration | Ausdruckswerte | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

Wenn also mit einer `field()`-Funktion auf den Wert des gezählten Arrayalias zugegriffen werden muss, ist der Wert mit einer `first()`-Vorlagenfunktion zu umschließen:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iteration | Ausdruckswerte | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Nützliche Beispiele finden Sie unter [Beispiele für Feldzählungen](../concepts/definition-structure.md#field-count-examples).

## <a name="modifying-arrays"></a>Ändern von Arrays

[append](../concepts/effects.md#append) und [modify](../concepts/effects.md#modify) ändern Eigenschaften von Ressourcen bei deren Erstellung oder einem Update. Beim Arbeiten mit Arrayeigenschaften hängt das Verhalten bei diesen Auswirkungen davon ab, ob der Vorgang versucht, den **\[\*\]** -Alias zu ändern oder nicht:

> [!NOTE]
> Die Verwendung des `modify`-Effekts mit Aliasen befindet sich derzeit in der **Vorschau**.

|Alias |Wirkung | Ergebnis |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Azure Policy fügt das gesamte in den Auswirkungsdetails angegebene Array an, wenn nicht vorhanden. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` mit `add`-Vorgang | Azure Policy fügt das gesamte in den Auswirkungsdetails angegebene Array an, wenn nicht vorhanden. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` mit `addOrReplace`-Vorgang | Azure Policy fügt das gesamte in den Auswirkungsdetails angegebene Array an, wenn nicht vorhanden, oder ersetzt das vorhandene Array. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy fügt den in den Auswirkungsdetails angegebenen Arraymember an, wenn nicht vorhanden. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` mit `add`-Vorgang | Azure Policy fügt den in den Auswirkungsdetails angegebenen Arraymember an, wenn nicht vorhanden. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` mit `addOrReplace`-Vorgang | Azure Policy entfernt alle vorhandenen Arraymember und fügt den in den Auswirkungsdetails angegebenen Arraymember an. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy fügt einen Wert an die `action`-Eigenschaft der einzelnen Arraymember an. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` mit `add`-Vorgang | Azure Policy fügt einen Wert an die `action`-Eigenschaft der einzelnen Arraymember an. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` mit `addOrReplace`-Vorgang | Azure Policy fügt bei den einzelnen Arraymembern die `action`-Eigenschaft an oder ersetzt sie, wenn vorhanden. |

Weitere Informationen finden Sie unter [Beispiele für „append“](../concepts/effects.md#append-examples).

## <a name="additional--alias-examples"></a>Zusätzliche [*]-Aliasbeispiele

Es wird empfohlen, die [Feldzählungsausdrücke](#field-count-expressions) zu verwenden, um zu überprüfen, ob alle („all of“) oder mindestens eines („any of“) der Member eines Arrays im Anforderungsinhalt eine Bedingung erfüllen. Bei einigen einfachen Bedingungen ist es jedoch möglich, das gleiche Ergebnis zu erzielen, indem ein Feldaccessor mit einem Arrayalias verwendet wird, wie unter [Verweisen auf die Sammlung der Arraymember](#referencing-the-array-members-collection) beschrieben. Dieses Muster kann in Richtlinienregeln nützlich sein, die den Grenzwert der zulässigen Anzahl von **count**-Ausdrücken überschreiten. Im Folgenden finden Sie Beispiele für häufige Anwendungsfälle:

Die Beispielrichtlinienregel für die unten stehende Szenariotabelle sieht wie folgt aus:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Das **ipRules**-Array für die unten stehende Szenariotabelle sieht wie folgt aus:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Ersetzen Sie für jede der unten stehenden Beispielbedingungen `<field>` durch `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Die Kombination der Bedingung, der Beispielrichtlinienregel und des Arrays der obigen vorhandenen Werte führt zu den folgenden Ergebnissen:

|Bedingung |Ergebnis | Szenario |Erklärung |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nichts |Keine Übereinstimmung |Ein Arrayelement wird als „false“ ausgewertet (127.0.0.1 != 127.0.0.1) und ein Element als „true“ (127.0.0.1 != 192.168.1.1). Die **notEquals**-Bedingung ist daher _false_, und die Auswirkung wird nicht ausgelöst. |
|`{<field>,"notEquals":"10.0.4.1"}` |Richtlinienauswirkung |Keine Übereinstimmung |Beide Arrayelemente werden als „true“ ausgewertet (10.0.4.1 != 127.0.0.1 und 10.0.4.1 != 192.168.1.1). Die **notEquals**-Bedingung ist daher _true_, und die Auswirkung wird ausgelöst. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Richtlinienauswirkung |Eine oder mehrere Übereinstimmungen |Ein Arrayelement wird als „false“ ausgewertet (127.0.0.1 != 127.0.0.1) und ein Element als „true“ (127.0.0.1 != 192.168.1.1). Die **notEquals**-Bedingung ist daher _false_. Der logische Operator wird als TRUE ausgewertet (**nicht** _FALSE_), sodass die Auswirkung ausgelöst wird. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nichts |Eine oder mehrere Übereinstimmungen |Beide Arrayelemente werden als „true“ ausgewertet (10.0.4.1 != 127.0.0.1 und 10.0.4.1 != 192.168.1.1). Die **notEquals**-Bedingung ist daher _true_. Der logische Operator wird als FALSE ausgewertet (**nicht** _TRUE_), sodass die Auswirkung nicht ausgelöst wird. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Richtlinienauswirkung |Nicht alle stimmen überein |Ein Arrayelement wird als „true“ ausgewertet (127.0.0.1 == 127.0.0.1) und ein Element als „false“ (127.0.0.1 == 192.168.1.1). Die **Equals**-Bedingung ist daher _false_. Der logische Operator wird als TRUE ausgewertet (**nicht** _FALSE_), sodass die Auswirkung ausgelöst wird. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Richtlinienauswirkung |Nicht alle stimmen überein |Beide Arrayelemente werden als „false“ ausgewertet (10.0.4.1 == 127.0.0.1 und 10.0.4.1 == 192.168.1.1). Die **Equals**-Bedingung ist daher _false_. Der logische Operator wird als TRUE ausgewertet (**nicht** _FALSE_), sodass die Auswirkung ausgelöst wird. |
|`{<field>,"Equals":"127.0.0.1"}` |Nichts |Alle stimmen überein |Ein Arrayelement wird als „true“ ausgewertet (127.0.0.1 == 127.0.0.1) und ein Element als „false“ (127.0.0.1 == 192.168.1.1). Die **Equals**-Bedingung ist daher _false_, und die Auswirkung wird nicht ausgelöst. |
|`{<field>,"Equals":"10.0.4.1"}` |Nichts |Alle stimmen überein |Beide Arrayelemente werden als „false“ ausgewertet (10.0.4.1 == 127.0.0.1 und 10.0.4.1 == 192.168.1.1). Die **Equals**-Bedingung ist daher _false_, und die Auswirkung wird nicht ausgelöst. |

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](programmatically-create.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).
