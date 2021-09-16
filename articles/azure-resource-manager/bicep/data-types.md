---
title: Datentypen in Bicep
description: In diesem Artikel werden die Datentypen beschrieben, die in Bicep verfügbar sind.
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: f520e314aff783a78e1656c16721f0fb8504215b
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221695"
---
# <a name="data-types-in-bicep"></a>Datentypen in Bicep

In diesem Artikel werden die in [Bicep](./overview.md) unterstützten Datentypen beschrieben.

## <a name="supported-types"></a>Unterstützte Typen

In einer Bicep-Vorlage können Sie die folgenden Datentypen verwenden:

* array
* bool
* INT
* Objekt (object)
* secureObject – durch Modifizierer in Bicep angegeben
* secureString – durch Modifizierer in Bicep angegeben
* Zeichenfolge

## <a name="arrays"></a>Arrays

Arrays beginnen mit einer linken eckigen Klammer (`[`) und enden mit einer rechten eckigen Klammer (`]`). In Bicep muss ein Array in mehreren Zeilen deklariert werden. Verwenden Sie keine Kommas zwischen Werten.

In einem Array wird jedes Element durch den [beliebigen Typ](bicep-functions-any.md) dargestellt. Sie können über ein Array verfügen, in dem jedes Element denselben Datentyp hat, oder ein Array, das unterschiedliche Datentypen enthält.

Das folgende Beispiel zeigt ein Array von ganzen Zahlen und ein Array verschiedener Typen.

```bicep
var integerArray = [
  1
  2
  3
]

var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

Arrays in Bicep basieren auf 0. Beispielsweise ergibt der Ausdruck `exampleArray[0]` den Wert 1 und `exampleArray[2]` entspricht 3. Der Index des Indexers kann selbst ein anderer Ausdruck sein. Der Ausdruck `exampleArray[index]` wird zu 2 ausgewertet. Ganzzahlige Indexer sind nur für den Ausdruck von Arraytypen zulässig.

```bicep
var index = 1

var exampleArray = [
  1
  2
  3
]
```

## <a name="booleans"></a>Boolesche Werte

Wenn Sie boolesche Werte angeben, verwenden Sie `true` oder `false`. Umschließen Sie den Wert nicht mit Anführungszeichen.

```bicep
param exampleBool bool = true
```

## <a name="integers"></a>Ganze Zahlen

Wenn Sie ganzzahlige Werte angeben, verwenden Sie keine Anführungszeichen.

```bicep
param exampleInt int = 1
```

Für Integer, die als Inlineparameter übergeben werden, ist der Wertebereich möglicherweise durch das SDK oder Befehlszeilentool, das Sie zur Bereitstellung verwenden, eingeschränkt. Wenn Sie beispielsweise PowerShell zum Bereitstellen eines Bicep verwenden, können Integertypen im Bereich von -2147483648 bis 2147483647 liegen. Um diese Einschränkung zu vermeiden, geben Sie große Werte in einer [Parameterdatei](parameter-files.md) an. Ressourcentypen wenden ihre eigenen Grenzwerte für Integereigenschaften an.

Gleitkomma-, Dezimal- oder Binärformate werden derzeit nicht unterstützt.

## <a name="objects"></a>Objekte

Objekte beginnen mit einer linken geschweiften Klammer (`{`) und enden mit einer rechten geschweiften Klammer (`}`). In Bicep muss ein Objekt in mehreren Zeilen deklariert werden. Jede Eigenschaft in einem Objekt besteht aus einem Schlüssel und einem Wert. Der Schlüssel und der Wert werden durch einen Doppelpunkt (`:`) getrennt. Ein Objekt lässt beliebige Eigenschaften jedes Typs zu.

In Bicep ist der Schlüssel nicht in Anführungszeichen eingeschlossen. Verwenden Sie keine Kommas zwischen Eigenschaften.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

Eigenschaftenaccessoren werden verwendet, um auf Eigenschaften eines Objekts zuzugreifen. Sie werden mit dem `.`-Operator erstellt.

```bicep
var a = {
  b: 'Dev'
  c: 42
  d: {
    e: true
  }
}

output result1 string = a.b // returns 'Dev' 
output result2 int = a.c // returns 42
output result3 bool = a.d.e // returns true
```

Eigenschaftenaccessoren können mit jedem Objekt verwendet werden, einschließlich Parametern und Variablen von Objekttypen sowie Objektliteralen. Die Verwendung eines Eigenschaftsaccessors für einen Ausdruck des Nichtobjekttyps ist ein Fehler.

Sie können auch die Syntax `[]` für den Zugriff auf eine Eigenschaft verwenden. Im folgenden Beispiel wird `Development` zurückgegeben.

```bicep
var environmentSettings = {
  dev: {
    name: 'Development'
  }
  prod: {
    name: 'Production'
  }
}

output accessorResult string = environmentSettings['dev'].name
```

## <a name="strings"></a>Zeichenfolgen

In Bicep werden Zeichenfolgen mit einfachen Anführungszeichen markiert und müssen in einer einzelnen Zeile deklariert werden. Alle Unicode-Zeichen mit Codepunkten zwischen *0* und *10FFFF* sind zulässig.

```bicep
param exampleString string = 'test value'
```

In der folgenden Tabelle sind die reservierten Zeichen aufgeführt, die mit einem schrägen Schrägstrich (`\`) mit Escapezeichen geschützt werden müssen:

| Escapesequenz | Dargestellter Wert | Notizen |
|:-|:-|:-|
| \\ | \ ||
| \' | ' ||
| \n | Zeilenvorschub (LF) ||
| \r | Wagenrücklauf (CR) ||
| \t | Tabstoppzeichen ||
| \u{x} | Unicode-Codepunkt *x* | *x* stellt einen hexadezimalen Codepunktwert zwischen *0* und *10FFFF* (beide einschließlich) dar. Führende Nullen sind zulässig. Codepunkte oberhalb von *FFFF* werden als Ersatzzeichenpaar ausgegeben.
| \$ | $ | Muss nur mit Escape geschützt werden, wenn darauf *{* folgt. |

```bicep
// evaluates to "what's up?"
var myVar = 'what\'s up?'
```

Alle Zeichenfolgen in Bicep unterstützen Interpolation. Um einen Ausdruck einzufügen, umschließen Sie ihn durch *${* und *}'. Ausdrücke, auf die verwiesen wird, können sich nicht über mehrere Zeilen erstrecken.

```bicep
var storageName = 'storage${uniqueString(resourceGroup().id)}
```

## <a name="multi-line-strings"></a>Mehrzeilige Zeichenfolgen

In Bicep werden Multi-Linienzeichenfolgen zwischen drei einfachen Anführungszeichen (`'''`) definiert, auf die optional ein Zeilenumbruch (die öffnende Sequenz) folgt, und drei einfache Anführungszeichen (`'''` – die schließende Sequenz). Zeichen, die zwischen der öffnenden und der schließenden Sequenz eingegeben werden, werden wörtlich gelesen, und es ist kein Schutz mit Escapezeichen erforderlich oder möglich.

> [!NOTE]
> Da der Bicep-Parser alle Zeichen in Abhängigkeit von den Zeilenenden Ihrer Bicep-Datei liest, können Zeilenumbruchlinien entweder als `\r\n` oder `\n` interpretiert werden.
> Die Interpolation wird derzeit in Multi-Linienzeichenfolgen nicht unterstützt.
> Multi-Linienzeichenfolgen, die `'''` enthalten, werden nicht unterstützt.

```bicep
// evaluates to "hello!"
var myVar = '''hello!'''

// evaluates to "hello!" because the first newline is skipped
var myVar2 = '''
hello!'''

// evaluates to "hello!\n" because the final newline is included
var myVar3 = '''
hello!
'''

// evaluates to "  this\n    is\n      indented\n"
var myVar4 = '''
  this
    is
      indented
'''

// evaluates to "comments // are included\n/* because everything is read as-is */\n"
var myVar5 = '''
comments // are included
/* because everything is read as-is */
'''

// evaluates to "interpolation\nis ${blocked}"
// note ${blocked} is part of the string, and is not evaluated as an expression
myVar6 = '''interpolation
is ${blocked}'''
```

## <a name="secure-strings-and-objects"></a>Sichere Zeichenfolgen und Objekte

Die sichere Zeichenfolge verwendet das gleiche Format wie die Zeichenfolge, und das sichere Objekt verwendet das gleiche Format wie das Objekt. Mit Bicep fügen Sie den `@secure()`-Modifizierer einer Zeichenfolge oder einem Objekt hinzu.

Wenn Sie einen Parameter auf eine sichere Zeichenfolge oder ein sicheres Objekt festlegen, wird der Wert des-Parameters weder im Bereitstellungsverlauf gespeichert noch protokolliert. Wenn Sie diesen sicheren Wert jedoch auf eine Eigenschaft festlegen, die keinen sicheren Wert erwartet, wird der Wert nicht geschützt. Wenn Sie z. B. eine sichere Zeichenfolge auf ein Tag festlegen, wird dieser Wert als reiner Text gespeichert. Verwenden Sie sichere Zeichenfolgen für Kennwörter und Geheimnisse.

Das folgende Beispiel zeigt zwei sichere Parameter:

```bicep
@secure()
param password string

@secure()
param configValues object
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Struktur und Syntax von Bicep finden Sie unter [Bicep-Dateistruktur](./file.md).
