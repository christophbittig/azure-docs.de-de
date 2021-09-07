---
title: Abfragen des Zwillingsgraphen
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Informationen im Zwillingsdiagramm von Azure Digital Twins abfragen.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9d8a72f4457c6759dc93fcdeae03abd1d4c8b168
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729829"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Abfragen des Zwillingsdiagramms von Azure Digital Twins

Dieser Artikel bietet Abfragebeispiele und Anleitungen zur Verwendung der **Azure Digital Twins-Abfragesprache** zum Abfragen von Informationen aus Ihrem [Zwillingsgraphen](concepts-twins-graph.md). (Eine Einführung in die Abfragesprache finden Sie unter [Abfragesprache](concepts-query-language.md).)

Der vorliegende Artikel enthält Beispielabfragen, die die Struktur der Abfragesprache sowie gängige Abfragevorgänge für digitale Zwillinge veranschaulichen. Außerdem wird beschrieben, wie Sie die von Ihnen geschriebenen Abfragen mithilfe der [Abfrage-API](/rest/api/digital-twins/dataplane/query) von Azure Digital Twins oder mit einem [SDK](concepts-apis-sdks.md#overview-data-plane-apis) ausführen.

> [!NOTE]
> Wenn Sie die Beispielabfragen unten mit einem API- oder SDK-Aufruf ausführen, müssen Sie den Abfragetext in eine einzelne Zeile zusammenfassen.

[!INCLUDE [digital-twins-query-reference.md](../../includes/digital-twins-query-reference.md)]

## <a name="show-all-digital-twins"></a>Anzeigen aller digitalen Zwillinge

Mit der folgenden grundlegenden Abfrage wird eine Liste aller digitalen Zwillinge in der Instanz zurückgegeben:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>Abfrage nach Eigenschaft

Abrufen von digitalen Zwillingen nach **Eigenschaften** (einschließlich ID und Metadaten):

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty1":::

Wie in der obigen Abfrage gezeigt, wird die ID eines digitalen Zwillings mithilfe des Metadatenfelds `$dtId` abgefragt.

>[!TIP]
> Wenn Sie Cloud Shell verwenden, um eine Abfrage mit Metadatenfeldern auszuführen, die mit `$` beginnen, müssen Sie `$` mit einem Graviszeichen als Escapezeichen versehen, um Cloud Shell mitzuteilen, dass es sich dabei nicht um eine Variable handelt und dieses Zeichen im Abfragetext als Literal behandelt werden soll.

Sie können Zwillinge auch auf der Grundlage abrufen, **ob eine bestimmte Eigenschaft definiert ist**. Mit der folgenden Abfrage werden Zwillinge abgerufen, die eine definierte *Location*-Eigenschaft aufweisen:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty2":::

Diese Abfrage kann sie dabei unterstützen, Zwillinge anhand ihrer *tag*-Eigenschaften abzurufen, wie unter [Hinzufügen von Tags zu digitalen Zwillingen](how-to-use-tags.md) beschrieben. Mit der folgenden Abfrage werden alle Zwillinge abgerufen, die mit dem Tag *red* markiert wurden:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerTags1":::

Sie können Zwillinge auch auf der Grundlage des **Typs einer Eigenschaft** abrufen. Mit der nachstehenden Abfrage werden Zwillinge abgerufen, deren *Temperature*-Eigenschaft eine Zahl ist:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty3":::

>[!TIP]
> Wenn es sich bei einer Eigenschaft um eine Eigenschaft vom Typ `Map` handelt, können die Zuordnungsschlüssel und Werte wie im folgenden Beispiel direkt in der Abfrage verwendet werden:
> :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>Abfrage nach Modell

Der `IS_OF_MODEL`-Operator kann verwendet werden, um anhand des [Modells](concepts-models.md) des Zwillings zu filtern.

Er berücksichtigt [Vererbung](concepts-models.md#model-inheritance) und [Versionsverwaltung](how-to-manage-model.md#update-models) des Modells und wird in **TRUE** für einen bestimmten Zwilling ausgewertet, wenn der Zwilling eine der folgenden Bedingungen erfüllt:

* Der Zwilling implementiert das Modell direkt, das für `IS_OF_MODEL()` bereitgestellt wird, und die Versionsnummer des Modells auf dem Zwilling ist *größer oder gleich* der Versionsnummer des bereitgestellten Modells.
* Der Zwilling implementiert ein Modell, das das für `IS_OF_MODEL()` bereitgestellte Modell *erweitert*, und die Versionsnummer des erweiterten Modells des Zwillings ist *größer oder gleich* der Versionsnummer des bereitgestellten Modells.

Wenn Sie z. B. nach Zwillingen des Modells `dtmi:example:widget;4` abfragen, gibt die Abfrage alle Zwillinge zurück, die auf **Version 4 oder höher** des **Widgets**-Modells basieren, sowie auch Zwillinge, die auf Version **4 oder höher** von **Modellen basieren, die vom Widget erben**.

`IS_OF_MODEL` kann verschiedene Parameter annehmen, und der Rest dieses Abschnitts beschäftigt sich mit den verschiedenen Überladungsoptionen.

Die einfachste Verwendung von `IS_OF_MODEL` nimmt nur einen `twinTypeName`-Parameter entgegen: `IS_OF_MODEL(twinTypeName)`.
Hier sehen Sie eine Beispielabfrage, die in diesem Parameter einen Wert übergibt:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel1":::

Um eine zu durchsuchende Zwillingssammlung anzugeben, wenn mehrere vorhanden sind (z. B. bei Verwendung von `JOIN`), fügen Sie den Parameter `twinCollection` hinzu: `IS_OF_MODEL(twinCollection, twinTypeName)`.
Hier ist eine Beispielabfrage, die einen Wert für diesen Parameter hinzufügt:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel2":::

Um eine genaue Übereinstimmung zu überprüfen, fügen Sie den Parameter `exact` hinzu: `IS_OF_MODEL(twinTypeName, exact)`.
Hier ist eine Beispielabfrage, die einen Wert für diesen Parameter hinzufügt:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel3":::

Sie können auch alle drei Argumente gemeinsam übergeben: `IS_OF_MODEL(twinCollection, twinTypeName, exact)`.
Nachfolgend sehen Sie eine Beispielabfrage, in der für alle drei Parameter ein Wert angegeben ist:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>Abfragen nach Beziehung

Für Abfragen anhand der **Beziehungen** von digitalen Zwillingen weist die Azure Digital Twins-Abfragesprache eine besondere Syntax auf.

Beziehungen werden in der `FROM`-Klausel in den Abfragebereich übernommen. Anders als in „klassischen“ SQL-Sprachen sind die einzelnen Ausdrücke in dieser `FROM`-Klausel keine Tabelle. Stattdessen drückt die `FROM`-Klausel das entitätsübergreifende Durchlaufen von Beziehungen aus. Zum Durchlaufen von Beziehungen verwendet Azure Digital Twins eine benutzerdefinierte Version von `JOIN`.

Denken Sie daran, dass im Azure Digital Twins-[Modell](concepts-models.md) Beziehungen nicht unabhängig von Zwillingen vorliegen. Dies bedeutet, dass Beziehungen hier nicht unabhängig abgefragt werden können und an einen Zwilling gebunden sein müssen.
Um dies zu berücksichtigen, wird das Schlüsselwort `RELATED` in der `JOIN`-Klausel verwendet, um den Satz eines bestimmten Beziehungstyps aus der Zwillingssammlung abzurufen. Die Abfrage muss dann mithilfe eines Filters in der `WHERE`-Klausel angeben, welche bestimmten Zwillinge in der Beziehungsabfrage verwendet werden sollen (unter Verwendung der `$dtId`-Werte der Zwillinge).

Die folgenden Abschnitte zeigen Beispiele dafür, wie dies aussehen kann.

### <a name="basic-relationship-query"></a>Grundlegende Beziehungsabfrage

Hier sehen Sie ein Beispiel für eine beziehungsbasierte Abfrage. Mit diesem Codeausschnitt werden alle digitalen Zwillinge mit der *ID*-Eigenschaft „ABC“ und alle digitalen Zwillinge, die zu diesen digitalen Zwillingen eine *contains*-Beziehung haben, ausgewählt.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship1":::

Der Typ der Beziehung (im obigen Beispiel `contains`) wird mithilfe des Felds **name** der Beziehung aus der zugehörigen [DTDL-Definition](concepts-models.md#basic-relationship-example) angegeben.

> [!NOTE]
> Der Entwickler muss dieses `JOIN` nicht mit einem Schlüsselwert in der `WHERE`-Klausel korrelieren (oder in der `JOIN`-Definition einen Schlüsselwert angeben). Diese Korrelation wird automatisch vom System berechnet, da die Beziehungseigenschaften die Zielentität direkt identifizieren.

### <a name="query-by-the-source-or-target-of-a-relationship"></a>Abfragen nach der Quelle oder dem Ziel einer Beziehung

Sie können die Beziehungsabfragestruktur verwenden, um einen digitalen Zwilling zu identifizieren, der die Quelle oder das Ziel einer Beziehung ist.

Sie können z. B. mit einem Quellzwilling beginnen und seinen Beziehungen folgen, um die Zielzwillinge der Beziehungen zu ermitteln. Mithilfe der nachfolgenden Beispielabfrage werden die Zielzwillinge der *feeds*-Beziehungen ermittelt, die aus dem Zwilling „source-twin“ stammen.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationshipSource":::

Sie können auch mit dem Ziel der Beziehung beginnen und die Beziehung zurückverfolgen, um den Quellzwilling zu ermitteln. Hier ist ein Beispiel für eine Abfrage, die den Quellzwilling einer *feeds*-Beziehung zum Zwilling „target-twin“ ermittelt.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationshipTarget":::

### <a name="query-the-properties-of-a-relationship"></a>Abfragen der Eigenschaften einer Beziehung

Ähnlich wie die Eigenschaften digitaler Zwillinge über DTDL beschrieben werden, können Beziehungen ebenfalls über Eigenschaften verfügen. Sie können Zwillinge **auf der Grundlage der Eigenschaften ihrer Beziehungen** abfragen.
Die Azure Digital Twins-Abfragesprache ermöglicht das Filtern und Projizieren von Beziehungen, indem der Beziehung innerhalb der `JOIN`-Klausel ein Alias zugewiesen wird.

Als Beispiel sei eine *servicedBy*-Beziehung angenommen, die über die *reportedCondition*-Eigenschaft verfügt. In der nachstehenden Abfrage erhält diese Beziehung den Alias „R“, um auf die zugehörige Eigenschaft zu verweisen.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship2":::

Beachten Sie im obigen Beispiel, dass *reportedCondition* eine Eigenschaft der *servicedBy*-Beziehung selbst ist, NICHT eines digitalen Zwillings, der über eine *servicedBy*-Beziehung verfügt.

### <a name="query-with-multiple-joins"></a>Abfragen mit mehreren JOINs

Es werden bis zu fünf `JOIN`-Elemente in einer einzelnen Abfrage unterstützt. Dies ermöglicht es, mehrere Beziehungsebenen gleichzeitig zu durchlaufen. 

Um Abfragen für mehrere Beziehungsebenen auszuführen, verwenden Sie eine einzelne `FROM`-Anweisung gefolgt von N `JOIN`-Anweisungen, wobei die `JOIN`-Anweisungen Beziehungen zum Ergebnis einer vorherigen `FROM`- oder `JOIN`-Anweisung ausdrücken.

Hier ist ein Beispiel für eine Abfrage mit mehreren Joins, die alle LEDs in den Leuchten der Räume 1 und 2 abruft.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>Zählen von Elementen

Sie können die Anzahl von Elementen in einem Resultset mit der `Select COUNT`-Klausel zählen:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount1":::

Fügen Sie eine `WHERE`-Klausel hinzu, um die Anzahl von Elementen zu zählen, die ein bestimmtes Kriterium erfüllen. Im Folgenden finden Sie einige Beispiele für das Zählen mit einem angewendeten Filter basierend auf dem Typ des Zwillingsmodells (weitere Informationen zu dieser Syntax finden Sie unter [Abfragen nach Modell](#query-by-model) weiter unten):

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount2":::

Sie können auch `COUNT` zusammen mit der `JOIN`-Klausel verwenden. Nachfolgend sehen Sie eine Abfrage, mit der alle LEDs in den Leuchten der Räume 1 und 2 gezählt werden:

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>Filterergebnisse: Auswählen der obersten Elemente

Mithilfe der `Select TOP`-Klausel können Sie die verschiedenen obersten Elemente in einer Abfrage auswählen.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>Filterergebnisse: Angeben eines Rückgabesatzes mit Projektionen

Wenn Sie Projektionen in der `SELECT`-Anweisung verwenden, können Sie auswählen, welche Spalten von einer Abfrage zurückgegeben werden. Projektion wird jetzt sowohl für primitive als auch für komplexe Eigenschaften unterstützt. Weitere Informationen zu Projektionen mit Azure Digital Twins finden Sie in der [Referenzdokumentation zur SELECT-Klausel](reference-query-clause-select.md#select-columns-with-projections).

Die folgende Beispielabfrage verwendet eine Projektion, um Zwillinge und Beziehungen zurückzugeben. Die folgende Abfrage projiziert die Angaben Consumer, Factory und Edge aus einem Szenario, in dem eine Factory mit einer ID von *ABC* mit dem Consumer durch eine Beziehung von *Factory.customer* verbunden ist und diese Beziehung als *Edge* dargestellt wird.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections1":::

Sie können Projektion auch verwenden, um eine Eigenschaft eines Zwillings zurückzugeben. Die folgende Abfrage projiziert die Eigenschaft *Name* der Consumers, die mit der Factory mit einer ID von *ABC* durch eine Beziehung von *Factory.customer* verbunden sind.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections2":::

Sie können Projektion auch verwenden, um eine Eigenschaft einer Beziehung zurückzugeben. Wie im vorhergehenden Beispiel projiziert die folgende Abfrage die Eigenschaft *Name* der Consumers, die mit der Factory mit einer ID von *ABC* über eine Beziehung von *Factory.customer* verbunden sind. Jetzt gibt sie aber auch zwei Eigenschaften dieser Beziehung zurück: *prop1* und *prop2*. Dies geschieht durch Benennen der Beziehung *Edge* und Erfassen der zugehörigen Eigenschaften.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections3":::

Sie können auch Aliase verwenden, um Abfragen mit Projektion zu vereinfachen.

Mit der folgenden Abfrage werden dieselben Vorgänge wie im vorherigen Beispiel durchgeführt, aber für die Eigenschaftsnamen werden die Aliase `consumerName`, `first`, `second` und `factoryArea` verwendet.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections4":::

Hier sehen Sie eine ähnliche Abfrage zum Abrufen derselben Menge wie oben, bei der aber nur die Eigenschaft *Consumer.name* als `consumerName` und die vollständige Factory als Zwilling projiziert wird.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>Erstellen effizienter Abfragen mit dem IN-Operator

Sie können die Anzahl der erforderlichen Abfragen deutlich verringern, indem Sie ein Array aus Zwillingen erstellen und die Abfrage mit dem `IN`-Operator durchführen. 

Stellen Sie sich beispielsweise ein Szenario vor, in dem Gebäude aus Etagen und Etagen aus Räumen bestehen. Um in einem Gebäude nach heißen Räumen zu suchen, besteht eine Möglichkeit darin, diese Schritte durchzuführen.

1. Etagen im Gebäude basierend auf der `contains`-Beziehung suchen.

    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="INOperatorWithout":::

2. Wenn Sie Räume suchen möchten, können Sie eine Abfrage mit einer Sammlung der Etagen im Gebäude (in der nachstehenden Abfrage als Floor bezeichnet) ausführen, anstatt die Etagen einzeln zu überprüfen und jeweils eine `JOIN`-Abfrage zum Auffinden der Räume auszuführen.

    In der Client-App:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    In der Abfrage:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>Weitere zusammengesetzte Abfragebeispiele

Mithilfe von Kombinationsoperatoren können Sie jeden der oben genannten Abfragetypen **kombinieren**, um mehr Details in eine einzelne Abfrage einzubeziehen. Es folgen einige weitere Beispiele für zusammengesetzte Abfragen, die mehrere Zwillingsdeskriptortypen gleichzeitig abfragen.

* Von den Geräten, über die Room 123 verfügt, geben Sie die MxChip-Geräte zurück, die die Rolle des Operators übernehmen
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples1":::
* Rufen Sie Zwillinge ab, die eine Beziehung namens *Enthält* mit einem anderen Zwilling aufweisen, der die ID *id1* besitzt
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples2":::
* Rufen Sie alle Räume dieses Raummodells ab, die in floor11 enthalten sind
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>Ausführen von Abfragen mit der API

Nachdem Sie sich für eine Abfragezeichenfolge entschieden haben, führen Sie einen Aufruf der [Abfrage-API](/rest/api/digital-twins/dataplane/query) aus.

Sie können die API direkt aufrufen oder eins der [SDKs](concepts-apis-sdks.md#overview-data-plane-apis) verwenden, die für Azure Digital Twins verfügbar sind.

Der folgende Codeausschnitt veranschaulicht den [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)-Aufruf aus einer Client-App:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

Durch die in diesem Aufruf verwendete Abfrage wird eine Liste mit digitalen Zwillingen zurückgegeben (im obigen Beispiel durch Objekte vom Typ [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) dargestellt). Der Rückgabetyp Ihrer Daten für die jeweilige Abfrage hängt von den Begriffen ab, die Sie mit der Anweisung `SELECT` angeben:
* Bei Abfragen, die mit `SELECT * FROM ...` beginnen, wird eine Liste mit digitalen Zwillingen zurückgegeben. (Diese können als Objekte vom Typ `BasicDigitalTwin` oder als andere benutzerdefinierte Typen für digitale Zwillinge serialisiert werden, die Sie ggf. erstellt haben.)
* Bei Abfragen, die im Format `SELECT <A>, <B>, <C> FROM ...` beginnen, wird ein Wörterbuch mit den Schlüsseln `<A>`, `<B>` und `<C>` zurückgegeben.
* Andere Formate von `SELECT`-Anweisungen können für die Rückgabe benutzerdefinierter Daten erstellt werden. Sie können das Erstellen eigener Klassen erwägen, um benutzerdefinierte Resultsets zu verarbeiten. 

### <a name="query-with-paging"></a>Abfrage mit Paging

Abfrageaufrufe unterstützen Paging. Im Folgenden finden Sie ein vollständiges Beispiel mit Verwendung von `BasicDigitalTwin` als Abfrageergebnistyp mit Fehlerbehandlung und Auslagerung:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Azure Digital Twins-APIs und SDKs](concepts-apis-sdks.md), einschließlich der Abfrage-API, die zum Ausführen der Abfragen in diesem Artikel verwendet wird.
