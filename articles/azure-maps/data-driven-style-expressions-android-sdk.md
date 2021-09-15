---
title: Datengesteuerte Formatvorlagenausdrücke in Android-Karten | Microsoft Azure Maps
description: Hier erhalten Sie Informationen zu datengesteuerten Formatvorlagenausdrücken. Hier erfahren Sie, wie Sie diese Ausdrücke im Azure Maps Android SDK verwenden, um Formatvorlagen in Karten anzupassen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 7c73c52045504dfeeaccf5292aea7ab07b75d8d8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438531"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Datengesteuerte Formatvorlagenausdrücke (Android SDK)

Mit Ausdrücken können Sie Geschäftslogik auf Formatvorlagenoptionen anwenden, bei denen die Eigenschaften beachtet werden, die für jede Form in einer Datenquelle definiert sind. Ausdrücke können zum Filtern von Daten in einer Datenquelle oder Datenebene verwendet werden. Ausdrücke können aus bedingter Logik bestehen, z. B. aus if-Anweisungen. Und sie können verwendet werden, um Daten mithilfe von Zeichenfolgenoperatoren, logischen Operatoren und mathematischen Operatoren zu bearbeiten.

Datengesteuerte Formatvorlagen verringern die Menge an Code, die zum Implementieren von Geschäftslogik für die Formatierung benötigt wird. Bei Verwendung mit Ebenen werden Ausdrücke zur Renderingzeit in einem separaten Thread ausgewertet. Diese Funktion bietet im Vergleich zur Auswertung der Geschäftslogik im UI-Thread bessere Leistung.

Das Azure Maps Android SDK unterstützt fast dieselben Formatvorlagenausdrücke wie das Azure Maps Web SDK. Daher lassen sich alle Konzepte, die unter [Datengesteuerte Formatvorlagenausdrücke (Web SDK)](data-driven-style-expressions-web-sdk.md) beschrieben sind, ebenfalls auf eine Android-App übertragen. Alle Formatvorlagenausdrücke im Azure Maps Android SDK sind unter dem `com.microsoft.azure.maps.mapcontrol.options.Expression`-Namespace verfügbar. Es gibt viele verschiedene Arten von Formatvorlagenausdrücken.

| Art der Ausdrücke | BESCHREIBUNG |
|---------------------|-------------|
| [Boolesche Ausdrücke](#boolean-expressions) | Bei booleschen Ausdrücken werden Ausdrücke mit booleschen Operatoren bereitgestellt, um boolesche Vergleiche auszuwerten. |
| [Farbausdrücke](#color-expressions) | Farbausdrücke erleichtern Ihnen das Erstellen und Bearbeiten von Farbwerten. |
| [Bedingte Ausdrücke](#conditional-expressions) | Bei bedingten Ausdrücken werden logische Vorgänge bereitgestellt, die sich wie if-Anweisungen verhalten. |
| [Datenausdrücke](#data-expressions) | Ermöglichen den Zugriff auf die Eigenschaftsdaten eines Features. |
| [Interpolations- und Schrittausdrücke](#interpolate-and-step-expressions) | Interpolations- und Schrittausdrücke können verwendet werden, um Werte entlang einer interpolierten Kurve oder für eine Schrittfunktion zu berechnen. |
| [JSON-basierte Ausdrücke](#json-based-expressions) | Vereinfachen die Wiederverwendung von Formatvorlagenausdrücken, die auf JSON-Rohdaten basieren und mit dem Android SDK für das Web SDK erstellt wurden. |  
| [Ebenenspezifische Ausdrücke](#layer-specific-expressions) | Dies sind spezielle Ausdrücke, die nur für eine einzelne Ebene gelten. |
| [Mathematische Ausdrücke](#math-expressions) | Es werden mathematische Operatoren bereitgestellt, um datengesteuerte Berechnungen innerhalb des Ausdrucksframeworks durchzuführen. |
| [Zeichenfolgenoperator-Ausdrücke](#string-operator-expressions) | Mit Zeichenfolgenoperator-Ausdrücken werden Konvertierungsvorgänge für Zeichenfolgen durchgeführt, z. B. zum Verketten und Ändern der Groß-/Kleinschreibung. |
| [Typenausdrücke](#type-expressions) | Bei Typenausdrücken werden Tools zum Testen und Konvertieren unterschiedlicher Datentypen, z. B. Zeichenfolgen, Zahlen und boolesche Werte, bereitgestellt. |
| [Ausdrücke mit variabler Bindung](#variable-binding-expressions) | Mit Ausdrücken mit variabler Bindung werden die Ergebnisse einer Berechnung in einer Variablen gespeichert, und an einer anderen Stelle eines Ausdrucks kann mehrfach darauf verwiesen werden, ohne dass der gespeicherte Wert erneut berechnet werden muss. |
| [Zoom-Ausdruck](#zoom-expression) | Ruft zur Renderzeit den aktuellen Zoomfaktor der Karte ab. |

> [!NOTE]
> Die Syntax für Ausdrücke ist in Java und Kotlin größtenteils identisch. Wenn Sie die Dokumentation auf Kotlin festgelegt haben, aber Codeblöcke für Java angezeigt werden, ist der Code in beiden Sprachen identisch.

In allen Beispielen in diesem Abschnitt des Dokuments wird das folgende Feature verwendet, um die verschiedenen Verwendungsmöglichkeiten dieser Ausdrücke aufzuzeigen.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

Der folgende Code veranschaulicht, wie dieses GeoJSON-Feature in einer App manuell erstellt wird.

::: zone pivot="programming-language-java-android"

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add properties to the feature.
feature.addNumberProperty("id", 123)
feature.addStringProperty("entityType", "restaurant")
feature.addNumberProperty("revenue", 12345)
feature.addStringProperty("subTitle", "Building 40")
feature.addNumberProperty("temperature", 64)
feature.addStringProperty("title", "Cafeteria")
feature.addStringProperty("zoneColor", "purple")

val abcArray = JsonArray()
abcArray.add("a")
abcArray.add("b")
abcArray.add("c")

feature.addProperty("abcArray", abcArray)

val array1 = JsonArray()
array1.add("a")
array1.add("b")

val array2 = JsonArray()
array1.add("x")
array1.add("y")

val array2d = JsonArray()
array2d.add(array1)
array2d.add(array2)

feature.addProperty("array2d", array2d)

val style = JsonObject()
style.addProperty("fillColor", "red")

feature.addProperty("_style", style)
```

::: zone-end

Der folgende Code zeigt, wie die in eine Zeichenfolge umgewandelte Version des JSON-Objekts in ein GeoJSON-Feature in einer App deserialisiert wird.

::: zone pivot="programming-language-java-android"

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

val feature = Feature.fromJson(featureString)
```

::: zone-end

## <a name="json-based-expressions"></a>JSON-basierte Ausdrücke

Das Azure Maps Web SDK unterstützt auch datengesteuerte Formatvorlagenausdrücke, die mit einem JSON-Array dargestellt werden. Diese Ausdrücke können mithilfe der nativen `Expression`-Klasse im Android SDK neu erstellt werden. Alternativ können diese JSON-basierten Ausdrücke mithilfe einer Webfunktion wie `JSON.stringify` in eine Zeichenfolge konvertiert und an die `Expression.raw(String rawExpression)`-Methode übergeben werden. Nehmen Sie als Beispiel folgenden JSON-Ausdruck.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

Die in eine Zeichenfolge umgewandelte Version des oben aufgeführten Ausdrucks wäre `"['get','title']"`. Sie kann wie folgt in das Android SDK eingelesen werden.

::: zone pivot="programming-language-java-android"

```java
Expression exp = Expression.raw("['get','title']")
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val exp = Expression.raw("['get','title']")
```

::: zone-end

Diese Vorgehensweise kann die Wiederverwendung von Formatvorlagenausdrücken zwischen mobilen Apps und Web-Apps erleichtern, die Azure Maps nutzen.

Dieses Video bietet eine Übersicht über die datengesteuerte Formatierung in Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Datenausdrücke

Mit Datenausdrücken wird der Zugriff auf die Eigenschaftsdaten in einem Feature gewährt.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `accumulated()` | number | Ruft den Wert einer bis dahin akkumulierten Clustereigenschaft ab. Dieser kann nur in der Option `clusterProperties` einer gruppierten `DataSource`-Quelle verwendet werden. |
| `at(number | Expression, Expression)` | value | Ruft ein Element aus einem Array ab. |
| `geometryType()` | Zeichenfolge | Ruft den Geometrietyp des Features ab: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | value | Ruft den Eigenschaftswert aus den Eigenschaften des angegebenen Objekts ab. Gibt null zurück, wenn die angeforderte Eigenschaft fehlt. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | Ermittelt, ob die Eigenschaften eines Features über die angegebene Eigenschaft verfügen. |
| `id()` | value | Ruft die ID des Features ab, falls eine vorhanden ist. |
| `in(string | number | Expression, Expression)` | boolean | Bestimmt, ob ein Element in einem Array vorhanden ist |
| `length(string | Expression)` | number | Ruft die Länge einer Zeichenfolge oder eines Arrays ab. |
| `properties()`| value | Ruft das properties-Objekt des Features ab. |

Die folgenden Web SDK-Formatvorlagenausdrücke werden im Android SDK nicht unterstützt:

- index-of
- Slice

**Beispiele**

Auf die Eigenschaften eines Features kann in einem Ausdruck direkt zugegriffen werden, indem ein `get`-Ausdruck verwendet wird. In diesem Beispiel wird der `zoneColor`-Wert des Features verwendet, um die Farbeigenschaft einer Blasenebene anzugeben.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
)
```

::: zone-end

Das obige Beispiel funktioniert problemlos, wenn alle Punktfeatures über die `zoneColor`-Eigenschaft verfügen. Wenn dies nicht der Fall ist, wird die Farbe wahrscheinlich auf „schwarz“ zurückgesetzt. Verwenden Sie zum Ändern der Fallbackfarbe einen `switchCase`-Ausdruck in Kombination mit dem `has`-Ausdruck, um zu überprüfen, ob die Eigenschaft vorhanden ist. Geben Sie eine Ausweichfarbe für den Fall an, dass die Eigenschaft nicht vorhanden ist.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
)
```

::: zone-end

Blasen- und Symbolebenen rendern standardmäßig die Koordinaten aller Formen in einer Datenquelle. Dieses Verhalten kann die Scheitelpunkte eines Polygons oder einer Linie hervorheben. Die Option `filter` der Ebene kann verwendet werden, um den Geometrietyp der gerenderten Features zu beschränken, indem ein `geometryType`-Ausdruck in einem booleschen Ausdruck verwendet wird. Im folgenden Beispiel wird eine Blasenebene so beschränkt, dass nur `Point`-Features gerendert werden.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
)
```

::: zone-end

Im folgenden Beispiel können sowohl `Point`- als auch `MultiPoint`-Features gerendert werden.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
)
```

::: zone-end

Auf ähnliche Weise wird die Kontur von Polygonen auf Linienebenen gerendert. Fügen Sie zum Deaktivieren dieses Verhaltens auf einer Linienebene einen Filter hinzu, bei dem nur `LineString`- und `MultiLineString`-Features zugelassen werden.  

Im Folgenden finden Sie einige weitere Beispiele für die Verwendung von Datenausdrücken:

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Mathematische Ausdrücke

Bei mathematischen Ausdrücken werden mathematische Operatoren bereitgestellt, um datengesteuerte Berechnungen innerhalb des Ausdrucksframeworks durchzuführen.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `abs(number | Expression)` | number | Berechnet den absoluten Wert der angegebenen Zahl. |
| `acos(number | Expression)` | number | Berechnet den Arkuskosinus der angegebenen Zahl. |
| `asin(number | Expression)` | number | Berechnet den Arkussinus der angegebenen Zahl. |
| `atan(number | Expression)` | number | Berechnet den Arkustangens der angegebenen Zahl. |
| `ceil(number | Expression)` | number | Rundet die Zahl auf die nächste ganze Zahl. |
| `cos(number | Expression)` | number | Berechnet den Kosinus der angegebenen Zahl. |
| `division(number, number)` \| `division(Expression, Expression)` | number | Dividiert die erste Zahl durch die zweite Zahl. Gleichwertiger Web SDK-Ausdruck: `/` |
| `e()` | number | Gibt die mathematische Konstante `e` zurück. |
| `floor(number | Expression)` | number | Rundet die Zahl auf die nächste ganze Zahl ab. |
| `log10(number | Expression)` | number | Berechnet den dekadischen Logarithmus der angegebenen Zahl. |
| `log2(number | Expression)` | number | Berechnet den Logarithmus zur Basis 2 für die angegebene Zahl. |
| `ln(number | Expression)` | number | Berechnet den natürlichen Logarithmus der angegebenen Zahl. |
| `ln2()` | number | Gibt die mathematische Konstante `ln(2)` zurück. |
| `max(numbers... | expressions...)` | number | Berechnet die maximale Anzahl in der angegebenen Zahlengruppe. |
| `min(numbers... | expressions...)` | number | Berechnet die minimale Anzahl in der angegebenen Zahlengruppe. |
| `mod(number, number)` \| `mod(Expression, Expression)` | number | Berechnet den Rest aus der Division der ersten Zahl durch die zweite Zahl. Gleichwertiger Web SDK-Ausdruck: `%` |
| `pi()` | number | Gibt die mathematische Konstante `PI` zurück. |
| `pow(number, number)` \| `pow(Expression, Expression)` | number | Berechnet den Wert, der sich ergibt, wenn der erste Wert mit der zweiten Zahl potenziert wird. |
| `product(numbers... | expressions...)` | number | Multipliziert die angegebenen Zahlen. Gleichwertiger Web SDK-Ausdruck: `*` |
| `round(number | Expression)` | number | Rundet die Zahl auf die nächste ganze Zahl. Halbe Werte werden weg von null gerundet. `round(-1.5)` ergibt beispielsweise `-2`. |
| `sin(number | Expression)` | number | Berechnet den Sinus der angegebenen Zahl. |
| `sqrt(number | Expression)` | number | Berechnet die Quadratwurzel der angegebenen Zahl. |
| `subtract(number | Expression` | number | Subtrahiert von 0 die angegebene Zahl. |
| `subtract(number | Expression, number | Expression)` | number | Subtrahiert die zweite Zahl von der ersten Zahl. |
| `sum(numbers... | expressions...)` | number | Berechnet die Summe der angegebenen Zahlen. |
| `tan(number | Expression)` | number | Berechnet den Tangens der angegebenen Zahl. |

## <a name="boolean-expressions"></a>Boolesche Ausdrücke

Bei booleschen Ausdrücken werden Ausdrücke mit booleschen Operatoren bereitgestellt, um boolesche Vergleiche auszuwerten.

Beim Vergleichen von Werten ist der Vergleich streng typisiert. Werte unterschiedlichen Typs werden immer als ungleich betrachtet. Fälle, in denen bekannt ist, dass die Typen zur Analysezeit unterschiedlich sind, werden als ungültig angesehen und führen zu einem Analysefehler.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | Gibt `true` zurück, wenn alle Eingaben `true` sind, andernfalls `false`. |
| `any(Expression...)` | boolean | Gibt `true` zurück, wenn eine oder mehrere Eingaben `true` sind, andernfalls `false`. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Gibt `true` zurück, wenn die Eingabewerte gleich sind, andernfalls `false`. Die Argumente müssen entweder jeweils Zeichenfolgen oder Zahlen sein. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Gibt `true` zurück, wenn die erste Eingabe streng größer als die zweite Eingabe ist, andernfalls `false`. Die Argumente müssen entweder jeweils Zeichenfolgen oder Zahlen sein. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Gibt `true` zurück, wenn die erste Eingabe größer oder gleich der zweiten Eingabe ist, andernfalls `false`. Die Argumente müssen entweder jeweils Zeichenfolgen oder Zahlen sein. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Gibt `true` zurück, wenn die erste Eingabe streng kleiner als die zweite Eingabe ist, andernfalls `false`. Die Argumente müssen entweder jeweils Zeichenfolgen oder Zahlen sein. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Gibt `true` zurück, wenn die erste Eingabe kleiner oder gleich der zweiten Eingabe ist, andernfalls `false`. Die Argumente müssen entweder jeweils Zeichenfolgen oder Zahlen sein. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Gibt `true` zurück, wenn die Eingabewerte nicht gleich sind, andernfalls `false`. |
| `not(Expression | boolean)` | boolean | Logische Negation. Gibt `true` zurück, wenn für die Eingabe `false` gilt, und `false`, wenn für die Eingabe `true` gilt. |

## <a name="conditional-expressions"></a>Bedingte Ausdrücke

Bei bedingten Ausdrücken werden logische Vorgänge bereitgestellt, die sich wie if-Anweisungen verhalten.

Mit den folgenden Ausdrücken werden bedingte Logikvorgänge für die Eingabedaten durchgeführt. Mit dem Ausdruck `switchCase` wird beispielsweise Logik vom Typ „if/then/else“ bereitgestellt, während der Ausdruck `match` wie eine „switch-Anweisung“ funktioniert.

### <a name="switch-case-expression"></a>switchCase-Ausdruck

Ein `switchCase`-Ausdruck ist eine Art von bedingtem Ausdruck, der die „if/then/else“-Logik bietet. Diese Art Ausdruck durchläuft eine Liste mit booleschen Bedingungen. Er gibt den Ausgabewert der ersten booleschen Bedingung zurück, die als true ausgewertet wird.

Mit dem folgenden Pseudocode wird die Struktur des `switchCase`-Ausdrucks definiert.

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Beispiel**

Im folgenden Beispiel werden verschiedene boolesche Bedingungen durchlaufen, bis eine gefunden wird, deren Auswertung `true` ergibt. Anschließend wird der zugeordnete Wert zurückgegeben. Falls keine boolesche Bedingung `true` ergibt, wird ein Fallbackwert zurückgegeben.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
)
```

::: zone-end

### <a name="match-expression"></a>Match-Ausdruck

Ein `match`-Ausdruck ist eine Art von bedingtem Ausdruck, der eine Logik wie bei einer switch-Anweisung ermöglicht. Die Eingabe kann ein beliebiger Ausdruck sein, z. B. `get( "entityType")`, bei dem eine Zeichenfolge oder eine Zahl zurückgegeben wird. Jeder „Stopp“ muss über eine Bezeichnung verfügen, die entweder ein einzelner Literalwert oder ein Array von Literalwerten ist, deren Werte entweder nur Zeichenfolgen oder nur Zahlen sein dürfen. Es ergibt sich eine Übereinstimmung mit der Eingabe, wenn einer der Werte im Array übereinstimmt. Jede „Stopp“-Bezeichnung muss eindeutig sein. Falls der Eingabetyp nicht mit dem Typ der Bezeichnungen übereinstimmt, ist das Ergebnis der standardmäßige Fallbackwert.

Mit dem folgenden Pseudocode wird die Struktur des `match`-Ausdrucks definiert.

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Beispiele**

Im folgenden Beispiel wird die `entityType`-Eigenschaft eines Punktfeatures einer Blasenebene untersucht und nach einer Übereinstimmung gesucht. Wenn eine Übereinstimmung gefunden wird, wird dieser angegebene Wert zurückgegeben. Andernfalls wird der Fallbackwert zurückgegeben.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

Im folgenden Beispiel wird ein Array zum Auflisten einer Gruppe von Bezeichnungen verwendet, für die jeweils der gleiche Wert zurückgegeben werden sollte. Dieser Ansatz ist viel effizienter als das einzelne Auflisten aller Bezeichnungen. In diesem Fall wird die Farbe „red“ zurückgegeben, wenn die `entityType`-Eigenschaft „restaurant“ oder „grocery_store“ lautet.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(arrayOf("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

### <a name="coalesce-expression"></a>COALESCE-Ausdruck

Bei einem `coalesce`-Ausdruck wird eine Reihe von Ausdrücken durchlaufen, bis der erste Wert erkannt wird, der nicht null ist. Dieser Wert wird dann zurückgegeben.

Mit dem folgenden Pseudocode wird die Struktur des `coalesce`-Ausdrucks definiert.

```java
coalesce(Expression... input)
```

**Beispiel**

Im folgenden Beispiel wird ein `coalesce`-Ausdruck genutzt, um die Option `textField` einer Symbolebene festzulegen. Wenn die `title`-Eigenschaft für das Feature fehlt oder auf `null` festgelegt ist, versucht der Ausdruck, nach der `subTitle`-Eigenschaft zu suchen. Falls sie nicht vorhanden oder `null` festgelegt ist, wird auf eine leere Zeichenfolge zurückgegriffen.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
)
```

::: zone-end

## <a name="type-expressions"></a>Typenausdrücke

Bei Typenausdrücken werden Tools zum Testen und Konvertieren unterschiedlicher Datentypen, z. B. Zeichenfolgen, Zahlen und boolesche Werte, bereitgestellt.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `array(Expression)` | Objekt[] | Bestätigt, dass die Eingabe ein Array ist. |
| `bool(Expression)` | boolean | Bestätigt, dass die Eingabe ein boolescher Wert ist. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | collator | Gibt einen Collator für die Verwendung in Vergleichsvorgängen zurück, die vom Gebietsschema abhängig sind. Die Optionen, bei denen die Groß-/Kleinschreibung und diakritische Zeichen beachtet werden, ergeben standardmäßig „false“. Das locale-Argument gibt das IETF-Sprachtag des zu verwendenden Gebietsschemas an. Wenn keins angegeben ist, wird das Standardgebietsschema verwendet. Wenn das angeforderte Gebietsschema nicht verfügbar ist, verwendet der Collator ein vom System definiertes Fallbackgebietsschema. Verwenden Sie „resolved-locale“, um die Ergebnisse des Gebietsschema-Fallbackverhaltens zu testen.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | boolean \| number \| string \| Object \| Object[] | Gibt einen Literalarray- oder Objektwert zurück. Verwenden Sie diesen Ausdruck, um zu verhindern, dass ein Array oder ein Objekt als Ausdruck ausgewertet wird. Dies ist erforderlich, wenn ein Array oder Objekt von einem Ausdruck zurückgegeben werden muss. |
| `number(Expression)` | number | Bestätigt, dass der Eingabewert eine Zahl ist. |
| `object(Expression)` | Object | Bestätigt, dass der Eingabewert ein Objekt ist. |
| `string(Expression)` | Zeichenfolge | Bestätigt, dass der Eingabewert eine Zeichenfolge ist. |
| `toArray(Expression)` | Objekt[] | Konvertiert den Ausdruck in ein JSON-Objektarray. |
| `toBool(Expression)` | boolean | Konvertiert den Eingabewert in einen booleschen Wert. |
| `toNumber(Expression)` | number | Konvertiert den Eingabewert in eine Zahl, falls dies möglich ist. |
| `toString(Expression)` | Zeichenfolge | Konvertiert den Eingabewert in eine Zeichenfolge. |
| `typeoOf(Expression)` | Zeichenfolge | Gibt eine Zeichenfolge zurück, mit der der Typ des angegebenen Werts beschrieben wird. |

## <a name="color-expressions"></a>Farbausdrücke

Farbausdrücke erleichtern Ihnen das Erstellen und Bearbeiten von Farbwerten.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `color(int)` | color | Konvertiert einen ganzzahligen Farbwert in einen Farbausdruck. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | Erstellt einen Farbwert aus den Komponenten *red*, *green* und *blue*, die zwischen `0` und `255` liegen müssen und deren Alphakomponente `1` lauten muss. Wenn eine Komponente außerhalb des Bereichs liegt, tritt für den Ausdruck ein Fehler auf. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | Erstellt einen Farbwert aus den Komponenten *red*, *green* und *blue*, die zwischen `0` und `255` liegen müssen und deren Alphakomponente zwischen `0` und `1` liegen muss. Wenn eine Komponente außerhalb des Bereichs liegt, tritt für den Ausdruck ein Fehler auf. |
| `toColor(Expression)` | color  | Konvertiert den Eingabewert in eine Farbe. |
| `toRgba(Expression)` | color | Gibt ein Array mit vier Elementen zurück, das die Komponenten red, green, blue und alpha der Eingabefarbe enthält (in dieser Reihenfolge). |

**Beispiel**

Im folgenden Beispiel wird ein RGB-Farbwert erstellt, der für *red* über den Wert `255` und für *green* und *blue* über Werte verfügt, die per Multiplikation von `2.5` mit dem Wert der `temperature`-Eigenschaft berechnet werden. Wenn sich die Temperatur ändert, ändert sich die Farbe in verschiedene Schattierungen von *red*.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
)
```

::: zone-end

Wenn alle Farbparameter Zahlen sind, besteht keine Notwendigkeit, sie mit dem `literal`-Ausdruck zu umschließen. Beispiel:

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
)
```

::: zone-end

> [!TIP]
> Farbwerte in Form von Zeichenfolgen können mit der `android.graphics.Color.parseColor`-Methode in eine Farbe konvertiert werden. Im Folgenden wird eine hexadezimale Farbzeichenfolge in einen Farbausdruck konvertiert, der mit einer Ebene verwendet werden kann.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Zeichenfolgenoperator-Ausdrücke

Mit Zeichenfolgenoperator-Ausdrücken werden Konvertierungsvorgänge für Zeichenfolgen durchgeführt, z. B. zum Verketten und Ändern der Groß-/Kleinschreibung.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | Zeichenfolge | Verkettet mehrere Zeichenfolgen miteinander. Jeder Wert muss eine Zeichenfolge sein. Verwenden Sie den Typenausdruck `toString`, um bei Bedarf andere Werttypen in eine Zeichenfolge zu konvertieren. |
| `downcase(string)` \| `downcase(Expression)` | Zeichenfolge | Konvertiert die angegebene Zeichenfolge in Kleinbuchstaben. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | Bestimmt, ob die Eingabezeichenfolge einen Zeichensatz verwendet, der vom aktuellen Schriftstapel unterstützt wird. Beispiel: `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | Zeichenfolge | Gibt das IETF-Sprachtag des Gebietsschemas zurück, das vom angegebenen Collator verwendet wird. Dies kann verwendet werden, um das standardmäßige Systemgebietsschema zu bestimmen oder um festzustellen, ob ein angefordertes Gebietsschema erfolgreich geladen wurde. |
| `upcase(string)` \| `upcase(Expression)` | Zeichenfolge | Konvertiert die angegebene Zeichenfolge in Großbuchstaben. |

**Beispiel**

Im folgenden Beispiel wird die `temperature`-Eigenschaft des Punktfeatures in eine Zeichenfolge konvertiert und anschließend „°F“ am Ende angefügt.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
)
```

::: zone-end

Mit dem obigen Ausdruck wird in der Karte eine Nadel mit dem Überlagerungstext „64°F“ gerendert. Dies ist in der Abbildung unten dargestellt.

![Beispiel für Zeichenfolgenoperator-Ausdruck](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions&quot;></a>Interpolations- und Schrittausdrücke

Interpolations- und Schrittausdrücke können verwendet werden, um Werte entlang einer interpolierten Kurve oder für eine Schrittfunktion zu berechnen. Für diese Ausdrücke wird ein Ausdruck verwendet, bei dem ein numerischer Wert als Eingabe zurückgegeben wird, z. B. `get(&quot;temperature")`. Der Eingabewert wird anhand von Paaren mit Ein- und Ausgabewerten ausgewertet, um den Wert zu ermitteln, der am besten zur interpolierten Kurve oder Schrittfunktion passt. Die Ausgabewerte werden als „Stopps“ bezeichnet. Bei den Eingabewerten für jeden „Stopp“ muss es sich um Zahlen in aufsteigender Reihenfolge handeln. Die Ausgabewerte müssen eine Zahl, ein Array mit Farben oder eine Farbe sein.

### <a name="interpolate-expression"></a>Interpolate-Ausdruck

Ein `interpolate`-Ausdruck kann verwendet werden, um eine fortlaufende, nahtlose Gruppe mit Werten zu berechnen, indem zwischen Stoppwerten interpoliert wird. Ein `interpolate`-Ausdruck, der Farbwerte zurückgibt, erzeugt einen Farbverlauf, in dem aus Ergebniswerten ausgewählt wird. Der `interpolate`-Ausdruck hat folgende Formate:

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

Es gibt drei Typen von Interpolationsmethoden, die in einem `interpolate`-Ausdruck verwendet werden können:

| Name | BESCHREIBUNG |
|------|-------------|
| `linear()` | Lineare Interpolation zwischen dem „Stopp“-Paar.  |
| `exponential(number)` \| `exponential(Expression)` | Exponentielle Interpolation zwischen den „Stopps“. Es werden ein „base“-Wert angegeben und die Rate gesteuert, mit der die Ausgabe zunimmt. Bei höheren Werten nimmt die Ausgabe zum oberen Bereich hin stärker zu. Ein „base“-Wert nahe 1 führt zu einer Ausgabe, die eher linear zunimmt.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | Interpolation per [kubischer Bézierkurve](https://developer.mozilla.org/docs/Web/CSS/timing-function), die über die angegebenen Steuerungspunkte definiert wird. |

Der `stop`-Ausdruck hat das Format `stop(stop, value)`.

Hier ist ein Beispiel zu den unterschiedlichen Arten von Interpolationen angegeben.

| Linear  | Exponentiell | Kubische Bézierkurve |
|---------|-------------|--------------|
| ![Graph für lineare Interpolation](media/how-to-expressions/linear-interpolation.png) | ![Graph für exponentielle Interpolation](media/how-to-expressions/exponential-interpolation.png) | ![Graph für Interpolation mit kubischer Bézierkurve](media/how-to-expressions/bezier-curve-interpolation.png) |

**Beispiel**

Im folgenden Beispiel wird ein `linear interpolate`-Ausdruck verwendet, um die `bubbleColor`-Eigenschaft einer Blasenebene basierend auf der `temperature`-Eigenschaft des Punktfeatures festzulegen. Wenn der `temperature` Wert kleiner als 60 ist, wird „blue“ zurückgegeben. Liegt der Wert zwischen 60 und 70 (nicht einschließlich), wird Gelb zurückgegeben. Liegt der Wert zwischen 70 und 80 (nicht einschließlich), wird „orange“ (`#FFA500`) zurückgegeben. Liegt er bei 80 oder darüber, wird „red“ zurückgegeben.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

In der folgenden Abbildung wird veranschaulicht, wie die Farben für den obigen Ausdruck ausgewählt werden.

![Beispiel für Interpolate-Ausdruck](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Step-Ausdruck

Ein `step`-Ausdruck kann verwendet werden, um diskrete Schrittergebniswerte zu berechnen, indem eine Treppenfunktion ([Piecewise-Constant Function](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)) genutzt wird, die über Stopps definiert wird.

Der `interpolate`-Ausdruck hat folgende Formate:

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

Bei Step-Ausdrücken wird der Ausgabewert des Stopps direkt vor dem Eingabewert zurückgegeben – oder der erste Eingabewert, wenn die Eingabe kleiner als der erste Stoppwert ist.

**Beispiel**

Im folgenden Beispiel wird ein `step`-Ausdruck verwendet, um die `bubbleColor`-Eigenschaft einer Blasenebene basierend auf der `temperature`-Eigenschaft des Punktfeatures festzulegen. Wenn der `temperature` Wert kleiner als 60 ist, wird „blue“ zurückgegeben. Liegt der Wert zwischen 60 und 70, wird „yellow“ zurückgegeben. Liegt der Wert zwischen 70 und 80 (nicht einschließlich), wird „orange“ zurückgegeben. Liegt er bei 80 oder darüber, wird „red“ zurückgegeben.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

In der folgenden Abbildung wird veranschaulicht, wie die Farben für den obigen Ausdruck ausgewählt werden.

![Beispiel für einen Step-Ausdruck](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Ebenenspezifische Ausdrücke

Spezielle Ausdrücke, die nur für bestimmte Ebenen gelten.

### <a name="heat-map-density-expression"></a>Heatmap Density-Ausdruck

Mit einem Heatmap Density-Ausdruck wird der Wärmebilddichte-Wert für jedes Pixel einer Wärmebildebene abgerufen. Er ist als `heatmapDensity` definiert. Dieser Wert ist eine Zahl zwischen `0` und `1`. Er wird zusammen mit einem `interpolation`- oder `step`-Ausdruck verwendet, um den Farbverlauf zu definieren, der zum Einfügen der Farben in das Wärmebild genutzt wird. Dieser Ausdruck kann nur in der Option `heatmapColor` der Wärmebildebene verwendet werden.

> [!TIP]
> Die Farbe bei Index „0“ in einem interpolation-Ausdruck bzw. die Standardfarbe einer Schrittfarbe definiert die Farbe in einem Bereich ohne Daten. Die Farbe bei Index 0 kann verwendet werden, um eine Hintergrundfarbe zu definieren. Viele Benutzer legen hierfür einen transparenten Wert oder ein halbtransparentes Schwarz fest.

**Beispiel**

In diesem Beispiel wird ein Ausdruck mit linearer Interpolation verwendet, um einen nahtlosen Farbverlauf zum Rendern des Wärmebilds zu erstellen.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
)
```

::: zone-end

Zusätzlich zur Nutzung eines nahtlosen Farbverlaufs zum Versehen eines Wärmebilds mit Farben können Farben auch in Bereichen angegeben werden, indem ein `step`-Ausdruck verwendet wird. Durch Verwenden eines `step`-Ausdrucks für die farbige Darstellung des Wärmebilds wird die Dichte visuell in Bereiche gegliedert, die einer Kontur- oder Radarkarte ähneln.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

Weitere Informationen finden Sie unter [Hinzufügen einer Wärmebildebene](map-add-heat-map-layer-android.md).

### <a name="line-progress-expression"></a>Line Progress-Ausdruck

Mit einem Line Progress-Ausdruck wird der Fortschritt entlang einer Farbverlaufslinie auf einer Linienebene abgerufen. Er ist als `lineProgress()` definiert. Dieser Wert ist eine Zahl zwischen 0 und 1. Er wird in Kombination mit einem `interpolation`- oder `step`-Ausdruck verwendet. Dieser Ausdruck kann nur mit der `strokeGradient`-Option der Linienebene verwendet werden.

> [!NOTE]
> Für die `strokeGradient`-Option der Linienebene muss die `lineMetrics`-Option der Datenquelle auf `true` festgelegt sein.

**Beispiel**

In diesem Beispiel wird der `lineProgress()`-Ausdruck verwendet, um einen Farbverlauf auf den Strich einer Linie anzuwenden.

::: zone pivot="programming-language-java-android"

```java
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

[Livebeispiel](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Text Field Format-Ausdruck

Der `format`-Ausdruck kann mit der Option `textField` der Symbolebene verwendet werden, um eine gemischte Textformatierung zu erzielen. Dieser Ausdruck verwendet mindestens einen `formatEntry`-Ausdruck, der eine Zeichenfolge und eine `formatOptions`-Gruppe angibt, die an das Textfeld angefügt werden soll.

| Ausdruck | BESCHREIBUNG |
|------------|-------------|
| `format(Expression...)` | Gibt formatierten Text zurück, der Anmerkungen zur Verwendung in Textfeldeinträgen im gemischten Format enthält. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Gibt einen formatierten Zeichenfolgeneintrag zur Verwendung im `format`-Ausdruck zurück. |

Die folgenden Formatoptionen sind verfügbar:

| Ausdruck | Beschreibung |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Gibt den Skalierungsfaktor für den Schriftgrad an. Falls angegeben, wird mit diesem Wert die `textSize`-Eigenschaft für die individuelle Zeichenfolge außer Kraft gesetzt. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Gibt eine Farbe an, die beim Rendern auf einen Text angewendet werden soll. |

**Beispiel**

Im folgenden Beispiel wird das Textfeld formatiert, indem Fettdruck hinzugefügt und der Schriftgrad der `title`-Eigenschaft des Features erhöht wird. In diesem Beispiel wird auch die `subTitle`-Eigenschaft des Features mit einem niedrigeren Schriftgrad in einer neuen Zeile hinzugefügt.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(arrayOf("StandardFont-Bold")),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
)
```

::: zone-end

Auf dieser Ebene wird das Punktfeature wie in der folgenden Abbildung gerendert:

![Abbildung: Punktfeature mit formatiertem Textfeld](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Zoom-Ausdruck

Ein `zoom`-Ausdruck wird verwendet, um zur Renderzeit den aktuellen Zoomfaktor der Karte abzurufen. Er ist als `zoom()` definiert. Mit diesem Ausdruck wird eine Zahl zwischen dem minimalen und maximalen Wert des Zoomfaktorbereichs der Karte zurückgegeben. Die interaktiven Kartensteuerelemente in Azure Maps für Web und Android unterstützen 25 Zoomfaktoren, nummeriert von 0 bis 24. Mithilfe des `zoom`-Ausdrucks können Formatvorlagen dynamisch angepasst werden, wenn sich der Zoomfaktor der Karte ändert. Der `zoom`-Ausdruck kann nur mit `interpolate`- und `step`-Ausdrücken verwendet werden.

**Beispiel**

Standardmäßig ist für die in der Wärmebildebene gerenderten Daten ein fester Pixelradius für alle Zoomfaktoren definiert. Wenn die Karte gezoomt wird, werden die Daten aggregiert und die Wärmebildebene verändert sich. Zum Skalieren des Radius für jede Zoomebene kann ein `zoom`-Ausdruck verwendet werden, sodass jeder Datenpunkt den gleichen physischen Bereich der Karte abdeckt. Hierdurch sieht die Wärmebildebene statischer und konsistenter aus. Jede Zoomebene der Karte hat vertikal und horizontal doppelt so viele Pixel wie die vorherige Zoomebene. Wenn der Radius so skaliert wird, dass er sich mit jeder Zoomebene verdoppelt, wird ein Wärmebild erstellt, das auf allen Zoomebenen konsistent aussieht. Hierfür kann der Ausdruck `zoom` mit einem Ausdruck vom Typ `base 2 exponential interpolation` verwendet werden, wobei der Pixelradius für den minimalen Zoomfaktor und ein skalierter Radius für den maximalen Zoomfaktor mit der Berechnung `2 * Math.pow(2, minZoom - maxZoom)` festgelegt ist (unten dargestellt).

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
)
```

::: zone-end

## <a name="variable-binding-expressions"></a>Ausdrücke mit variabler Bindung

Bei Ausdrücken mit variabler Bindung werden die Ergebnisse einer Berechnung in einer Variablen gespeichert. Auf diese Weise kann an anderer Stelle in einem Ausdruck mehrfach auf die Berechnungsergebnisse verwiesen werden. Dies ist eine nützliche Optimierung für Ausdrücke, die viele Berechnungen umfassen.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Speichert einen oder mehrere Werte als Variablen zur Verwendung mit dem `var`-Ausdruck im untergeordneten Ausdruck, mit dem das Ergebnis zurückgegeben wird. |
| `var(Expression expression)` \| `var(string variableName)` | Object | Verweist auf eine Variable, die mit dem `let`-Ausdruck erstellt wurde. |

**Beispiel**

In diesem Beispiel wird ein Ausdruck verwendet, mit dem der Umsatz relativ zum Temperaturverhältnis berechnet wird, und anschließend wird ein `case`-Ausdruck verwendet, um für diesen Wert verschiedene boolesche Vorgänge auszuwerten. Der `let`-Ausdruck wird zum Speichern des Umsatzes in Relation zum Temperaturverhältnis verwendet, sodass er nur einmal berechnet werden muss. Der `var`-Ausdruck verweist auf diese Variable so oft wie nötig, ohne ihn neu berechnen zu müssen.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
)
```

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Ebenen, die Ausdrücke unterstützen:

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Blasenebene](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Linienebene](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Polygonebene](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen eines Wärmebilds](map-add-heat-map-layer-android.md)
