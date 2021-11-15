---
title: 'Referenz zur Azure Digital Twins-Abfragesprache: MATCH-Klausel'
titleSuffix: Azure Digital Twins
description: Referenzdokumentation zur MATCH-Klausel der Azure Digital Twins-Abfragesprache
author: baanders
ms.author: baanders
ms.date: 10/07/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 8c553d0df1f194c1de9f77e21341cb1022f98158
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270562"
---
# <a name="azure-digital-twins-query-language-reference-match-clause"></a>Referenz zur Azure Digital Twins-Abfragesprache: MATCH-Klausel

Dieses Dokument enthält Referenzinformationen zur **MATCH-Klausel** für die [Azure Digital Twins-Abfragesprache](concepts-query-language.md).

Die `MATCH`-Klausel wird in der Azure Digital Twins-Abfragesprache als Teil der [FROM-Klausel](reference-query-clause-from.md) verwendet. Mit `MATCH` können Sie angeben, welchem Muster beim Durchlaufen von Beziehungen im Azure Digital Twins-Graphen gefolgt werden soll (dies wird auch als Abfragemuster „variabler Hop“ bezeichnet).

Diese Klausel kann bei Abfragen optional genutzt werden.

## <a name="core-syntax-match"></a>Kernsyntax: MATCH

`MATCH` unterstützt beliebige Abfragen, die basierend auf bestimmten Beziehungsbedingungen einen Pfad zwischen Zwillingen mit einer unvorhersehbaren Anzahl von Hops ermitteln. 

Die **Beziehungsbedingung** kann mindestens eines der folgenden Details enthalten:
* [Beziehungsrichtung](#specify-relationship-direction) (von links nach rechts, von rechts nach links oder nicht richtungsgebunden)
* [Beziehungsname](#specify-relationship-name) (einzelner Name oder eine Liste von Möglichkeiten)
* [Anzahl von „Hops“](#specify-number-of-hops) von einem Zwilling zu einem anderen (genaue Anzahl oder Bereich)
* [Eine Abfragevariablenzuweisung](#assign-query-variable-to-relationship-and-specify-relationship-properties) zur Darstellung der Beziehung innerhalb des Abfragetexts. Dadurch können Sie auch nach Beziehungseigenschaften filtern.

Eine Abfrage mit einer `MATCH` -Klausel muss auch die  [WHERE-Klausel](reference-query-clause-where.md) verwenden, um die `$dtId` für mindestens einen der Zwillinge anzugeben, auf den sie verweist.

>[!NOTE]
>`MATCH` ist eine Obermenge aller `JOIN`-Abfragen, die im Abfragespeicher ausgeführt werden können.

### <a name="syntax"></a>Syntax

Dies ist die grundlegende `MATCH`-Syntax. 

Die Platzhalterwerte in der `MATCH`-Klausel, die durch Ihre Werte ersetzt werden sollen, sind `twin_1`, `relationship_condition` und `twin_2`. Die Platzhalterwerte in der `WHERE`-Klausel, die durch Ihre Werte ersetzt werden sollen, sind `twin_or_twin_collection` und `twin_ID`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchSyntax":::

Sie können den Namen eines der Zwillinge auslassen, damit jeder Zwillingsname an dieser Stelle funktioniert.

Sie können auch die Anzahl der Beziehungsbedingungen ändern, um mehrere [verkettete](#combine-match-operations) Beziehungsbedingungen oder überhaupt keine Beziehungsbedingung zu verwenden:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNodeSyntax":::

Weitere Informationen zu den einzelnen Beziehungsbedingungstypen und deren Kombination finden Sie in den anderen Abschnitten dieses Dokuments.

### <a name="example"></a>Beispiel

Dies ist eine Beispielabfrage mit `MATCH`.

Die Abfrage gibt eine [Beziehungsrichtung](#specify-relationship-direction) an und sucht nach Gebäude- und Sensorzwillingen, wobei Folgendes gilt...
* Der Sensor wird durch eine beliebige Beziehung von einem Gebäude mit der `$dtId` Building21 als Ziel angesprochen. 
* der Sensor weist eine Temperatur über 50 auf.
Das Gebäude und der Sensor sind beide im Abfrageergebnis enthalten.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchExample":::

## <a name="specify-relationship-direction"></a>Angeben der Beziehungsrichtung

Verwenden Sie die Beziehungsbedingung in der `MATCH`-Klausel, um eine Beziehungsrichtung zwischen den Zwillingen anzugeben. Mögliche Richtungen sind von links nach rechts, von rechts nach links oder nicht richtungsgebunden. Zyklische Beziehungen werden automatisch erkannt, sodass eine Beziehung nur ein Mal durchlaufen wird.

> [!NOTE]
> Es ist möglich, bidirektionale Beziehungen mithilfe von [Verkettung](#combine-match-operations) darzustellen.

### <a name="syntax"></a>Syntax

>[!NOTE]
> Die Beispiele in diesem Abschnitt konzentrieren sich auf die Beziehungsrichtung. Sie geben keine Beziehungsnamen an, sie verwenden standardmäßig einen einzelnen Hop und weisen den Beziehungen keine Abfragevariablen zu. Anweisungen zur weiteren Verwendung dieser anderen Bedingungen finden Sie unter [Angeben des Beziehungsnamens](#specify-relationship-name), [Angeben der Anzahl von Hops](#specify-number-of-hops) und [Zuweisen einer Abfragevariablen zu einer Beziehung](#assign-query-variable-to-relationship-and-specify-relationship-properties). Informationen dazu, wie Sie mehrere dieser Bedingungen in derselben Abfrage verwenden, finden Sie unter [Kombinieren von MATCH-Vorgängen](#combine-match-operations).

Direktionale Beziehungsbeschreibungen verwenden eine visuelle Darstellung eines Pfeils, um die Richtung der Beziehung anzugeben. Der Pfeil enthält ein Leerzeichen, in eckigen Klammern (`[]`), das für einen optionalen [Beziehungsnamen](#specify-number-of-hops) vorgesehen ist. 

In diesem Abschnitt wird die Syntax für verschiedene Richtungen von Beziehungen veranschaulicht. Die Platzhalterwerte, die durch Ihre Werte ersetzt werden sollen, sind `source_twin` und `target_twin`.

Verwenden Sie für eine Beziehung **von links nach rechts** die folgende Syntax.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionLRSyntax":::

Verwenden Sie für eine Beziehung **von rechts nach links** die folgende Syntax.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionRLSyntax":::

Verwenden Sie für eine **nicht richtungsgebundene** Beziehung die folgende Syntax. Dadurch wird keine Richtung für die Beziehung angegeben, sodass Beziehungen in beliebiger Richtung in das Ergebnis aufgenommen werden.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionNDSyntax":::

>[!TIP]
>Nicht richtungsgebundene Abfragen erfordern zusätzliche Verarbeitung, was zu erhöhter Latenz und höheren Kosten führen kann.

### <a name="examples"></a>Beispiele

Das erste Beispiel zeigt ein richtungsgebundenes Durchlaufen **von links nach rechts**. Diese Abfrage findet die Zwillinge *room* und *factory*. Dabei gilt Folgendes...
* *room* zielt auf *factory* (mit beliebigem Beziehungsnamen) ab.
* *room* weist einen Temperaturwert auf, der größer als 50 ist.
* *factory* weist eine `$dtId` „ABC“ auf.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionLRExample":::

Das folgende Beispiel zeigt ein richtungsgebundenes Durchlaufen **von rechts nach links**. Diese Abfrage ähnelt der Abfrage oben, aber die Richtung der Beziehung zwischen *room* und *factory* wird umgekehrt. Diese Abfrage findet die Zwillinge *room* und *factory*. Dabei gilt Folgendes...
* *factory* zielt auf *room* (mit beliebigem Beziehungsnamen) ab.
* *factory* weist eine `$dtId` „ABC“ auf.
* *room* weist einen Temperaturwert auf, der größer als 50 ist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionRLExample":::

Das folgende Beispiel zeigt ein **nicht richtungsgebundenes** Durchlaufen. Diese Abfrage findet die Zwillinge *room* und *factory*. Dabei gilt Folgendes...
* *room* und *factory* teilen sich einen beliebigen Beziehungsnamen in beide Richtungen
* *factory* weist eine `$dtId` „ABC“ auf.
* *room* weist einen Luftfeuchtigkeitswert auf, der größer als 70 ist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchDirectionNDExample":::

## <a name="specify-relationship-name"></a>Angeben des Beziehungsnamens

Optional können Sie die Beziehungsbedingung in der `MATCH`-Klausel verwenden, um Namen für die Beziehungen zwischen den Zwillingen anzugeben. Sie können einen einzelnen Namen oder eine Liste möglicher Namen angeben. Der optionale Beziehungsname ist als Teil der [Pfeilsyntax zum Angeben der Beziehungsrichtung](#specify-relationship-direction) enthalten.

Wenn Sie keinen Beziehungsnamen angeben, bindet die Abfrage standardmäßig alle Beziehungsnamen ein.

>[!TIP]
>Das Angeben von Beziehungsnamen in der Abfrage kann die Leistung verbessern und Ergebnisse besser vorhersagbar machen.

### <a name="syntax"></a>Syntax

>[!NOTE]
> Die Beispiele in diesem Abschnitt konzentrieren sich auf den Beziehungsnamen. Sie zeigen alle nicht richtungsgebundenen Beziehungen an, sie verwenden standardmäßig einen einzelnen Hop und weisen den Beziehungen keine Abfragevariablen zu. Anweisungen zur weiteren Verwendung dieser anderen Bedingungen finden Sie unter [Angeben der Beziehungsrichtung](#specify-relationship-direction), [Angeben der Anzahl von Hops](#specify-number-of-hops) und [Zuweisen einer Abfragevariablen zu einer Beziehung](#assign-query-variable-to-relationship-and-specify-relationship-properties). Informationen dazu, wie Sie mehrere dieser Bedingungen in derselben Abfrage verwenden, finden Sie unter [Kombinieren von MATCH-Vorgängen](#combine-match-operations).

Geben Sie den Namen einer Beziehung in eckigen Klammern (`[]`) an, die in der `MATCH`-Klausel durchlaufen werden soll. In diesem Abschnitt wird die Syntax zum Angeben benannter Beziehungen gezeigt.

Verwenden Sie für einen **einzelnen Namen** die folgende Syntax. Die Platzhalterwerte, die durch Ihre Werte ersetzt werden sollen, sind `twin_1`, `relationship_name` und `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameSingleSyntax":::

Verwenden Sie für **mehrere mögliche Namen** die folgende Syntax. Die Platzhalterwerte, die durch Ihre Werte ersetzt werden sollen, sind `twin_1`, `relationship_name_option_1`, `relationship_name_option_2`, `twin_2` und der Hinweis, das Muster bei Bedarf für die Anzahl der Beziehungsnamen fortzusetzen, die Sie eingeben möchten.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameMultiSyntax":::

(Standard) Um den Namen **nicht anzugeben**, belassen Sie die Klammern wie hier gezeigt ohne Namensinformationen:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameAllSyntax":::

### <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt einen **einzelnen Beziehungsnamen**. Diese Abfrage findet die Zwillinge *building* und *sensor*. Dabei gilt Folgendes...
* *building* verfügt über eine „contains“-Beziehung zu *sensor* (in beide Richtungen).
* *building* hat eine `$dtId` „Seattle21“.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameSingleExample":::

Das folgende Beispiel zeigt **mehrere mögliche Beziehungsnamen**. Diese Abfrage ähnelt der Abfrage oben, aber es gibt mehrere mögliche Beziehungsnamen, die im Ergebnis enthalten sind. Diese Abfrage findet die Zwillinge *building* und *sensor*. Dabei gilt Folgendes...
* *building* verfügt über eine „contains“- oder „isAssociatedWith“-Beziehung zu *sensor* (in beide Richtungen).
* *building* hat eine `$dtId` „Seattle21“.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameMultiExample":::

Im folgenden Beispiel ist **kein Beziehungsname angegeben**. Daher werden Beziehungen mit einem beliebigen Namen in das Abfrageergebnis aufgenommen. Diese Abfrage findet die Zwillinge *building* und *sensor*. Dabei gilt Folgendes...
* *building* verfügt über eine Beziehung zu *sensor* mit einem beliebigen Namen (in beide Richtungen).
* *building* hat eine `$dtId` „Seattle21“.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchNameAllExample":::

## <a name="specify-number-of-hops"></a>Angeben der Anzahl von Hops

Optional können Sie die Beziehungsbedingung in der `MATCH`-Klausel verwenden, um die Anzahl der Hops für die Beziehungen zwischen den Zwillingen anzugeben. Sie können eine genaue Zahl oder einen Bereich angeben. Dieser optionale Wert ist als Teil der [Pfeilsyntax zum Angeben der Beziehungsrichtung](#specify-relationship-direction) enthalten.

Wenn Sie keine Anzahl von Hops bereitstellen, verwendet die Abfrage standardmäßig einen Hop.

>[!IMPORTANT]
>Wenn Sie eine Anzahl von Hops angeben, die größer als 1 ist, können Sie der [Beziehung keine Abfragevariable zuweisen](#assign-query-variable-to-relationship-and-specify-relationship-properties). Innerhalb derselben Abfrage kann nur eine dieser Bedingungen verwendet werden. 

### <a name="syntax"></a>Syntax

>[!NOTE]
>Die Beispiele in diesem Abschnitt konzentrieren sich auf die Anzahl der Hops. Sie alle zeigen nicht richtungsbezogene Beziehungen ohne Angabe von Namen. Anweisungen zur weiteren Verwendung dieser anderen Bedingungen finden Sie unter [Angeben der Beziehungsrichtung](#specify-relationship-direction) und [Angeben des Beziehungsnamens](#specify-relationship-name). Informationen dazu, wie Sie mehrere dieser Bedingungen in derselben Abfrage verwenden, finden Sie unter [Kombinieren von MATCH-Vorgängen](#combine-match-operations).

Geben Sie die Anzahl der Hops in eckigen Klammern (`[]`) an, die in der `MATCH`-Klausel durchlaufen werden soll.

Verwenden Sie die folgende Syntax, um eine **genaue Anzahl von Hops** anzugeben. Die Platzhalterwerte, die durch Ihre Werte ersetzt werden sollen, sind `twin_1`, `number_of_hops` und `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactSyntax":::

Verwenden Sie die folgende Syntax, um einen **Bereich von Hops** anzugeben. Die Platzhalterwerte, die durch Ihre Werte ersetzt werden sollen, sind `twin_1`, `starting_limit`, `ending_limit` und `twin_2`. Das Startlimit **ist nicht** im Bereich enthalten, während das Endlimit **enthalten ist**.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeSyntax":::

Sie können das Startlimit auch auslassen, um „alles bis zum“ (einschließlich) Endlimit anzugeben. Es muss immer ein Endlimit angegeben werden.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingSyntax":::

(Standard) Um standardmäßig **einen Hop** zu verwenden, belassen Sie die Klammern wie hier gezeigt leer (ohne Hopinformationen):

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneSyntax":::

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird eine **genaue Anzahl von Hops** angegeben. Die Abfrage gibt nur Beziehungen zwischen den Zwillingen *floor* und *room* zurück, bei denen es sich um genau drei Hops handelt.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsExactExample":::

Im folgenden Beispiel wird ein **Bereich von Hops** angegeben. Die Abfrage gibt Beziehungen zwischen den Zwillingen *floor* und *room* zurück, die zwischen 1 und 3 Hops liegen (d. h. die Anzahl der Hops ist entweder 2 oder 3).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeExample1":::

Sie können auch einen Bereich anzeigen, indem Sie nur eine Grenze bereitstellen. Im folgenden Beispiel gibt die Abfrage Beziehungen zwischen den Zwillingen *floor* und *room* zurück, die höchstens 2 Hops betragen (d. h. die Anzahl der Hops ist entweder 1 oder 2).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsRangeEndingExample":::

Im folgenden Beispiel wird keine Anzahl von Hops angegeben, sodass standardmäßig **ein Hop** zwischen den Zwillingen *floor* und *room* verwendet wird.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchHopsOneExample":::

## <a name="assign-query-variable-to-relationship-and-specify-relationship-properties"></a>Zuweisen einer Abfragevariablen zu einer Beziehung (und Angeben von Beziehungseigenschaften)

Optional können Sie der Beziehung, auf die in der `MATCH`-Klausel verwiesen wird, eine Abfragevariable zuweisen, damit Sie im Abfragetext nach Name darauf verweisen können.

Ein nützliches Ergebnis ist dabei die Möglichkeit, nach Beziehungseigenschaften in Ihrer `WHERE`-Klausel zu filtern.

>[!IMPORTANT]
> Das Zuweisen einer Abfragevariablen zur Beziehung wird nur unterstützt, wenn die Abfrage einen einzelnen Hop angibt. Innerhalb einer Abfrage müssen Sie zwischen dem Angeben einer Beziehungsvariablen und dem [Angeben einer größeren Anzahl von Hops](#specify-number-of-hops) wählen.

### <a name="syntax"></a>Syntax

>[!NOTE]
>Die Beispiele in diesem Abschnitt konzentrieren sich auf eine Abfragevariable für die Beziehung. Sie alle zeigen nicht richtungsbezogene Beziehungen ohne Angabe von Namen. Anweisungen zur weiteren Verwendung dieser anderen Bedingungen finden Sie unter [Angeben der Beziehungsrichtung](#specify-relationship-direction) und [Angeben des Beziehungsnamens](#specify-relationship-name). Informationen dazu, wie Sie mehrere dieser Bedingungen in derselben Abfrage verwenden, finden Sie unter [Kombinieren von MATCH-Vorgängen](#combine-match-operations).

Um der Beziehung eine Abfragevariable zu zuweisen, platzieren Sie den Variablennamen in den eckigen Klammern (`[]`). Die unten gezeigten Platzhalterwerte, die durch Ihre Werte ersetzt werden sollen, sind `twin_1`, `relationship_variable` und `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableSyntax":::

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird der Beziehung die Abfragevariable „r“ zugewiesen. Später wird die Variable in der `WHERE`-Klausel verwendet, um anzugeben, dass die Beziehung *rel* eine Nameneigenschaft mit dem Wert „child“ aufweisen soll.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchVariableExample":::

## <a name="combine-match-operations"></a>Kombinieren von MATCH-Vorgängen

Sie können mehrere Beziehungsbedingungen in derselben Abfrage kombinieren. Sie können auch mehrere Beziehungsbedingungen verketten, um bidirektionale Beziehungen oder andere größere Kombinationen auszudrücken.

### <a name="syntax"></a>Syntax

In einer einzelnen Abfrage können Sie die [Beziehungsrichtung](#specify-relationship-direction), den [Beziehungsnamen](#specify-number-of-hops) und **eine** weitere Angabe ([Anzahl der Hops](#specify-number-of-hops) oder eine [Abfragevariablenzuweisung](#assign-query-variable-to-relationship-and-specify-relationship-properties)) kombinieren.

Die folgenden Syntaxbeispiele zeigen, wie diese Attribute kombiniert werden können. Sie können auch beliebige optionale Details auslassen, die in Platzhaltern enthalten sind, um diesen Teil der Bedingung auszulassen.

Verwenden Sie die folgende Syntax innerhalb der Beziehungsbedingung, um die **Beziehungsrichtung, den Beziehungsnamen und die Anzahl der Hops** innerhalb einer einzelnen Abfrage anzugeben. Die Platzhalterwerte, die durch Ihre Werte ersetzt werden sollen, sind `twin_1` und `twin_2`, `optional_left_angle_bracket` und `optional_right_angle_bracket` sowie `relationship_name(s)` und `number_of_hops`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsSyntax":::

Verwenden Sie die folgende Syntax innerhalb der Beziehungsbedingung, um die **Beziehungsrichtung, den Beziehungsnamen und die Abfragevariable für die Beziehung** innerhalb einer einzelnen Abfrage anzugeben. Die Platzhalterwerte, die durch Ihre Werte ersetzt werden sollen, sind `twin_1` und `twin_2`, `optional_left_angle_bracket` und `optional_right_angle_bracket` sowie `relationship_variable` und `relationship_name(s)`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableSyntax":::

>[!NOTE]
>Gemäß den Optionen zum [Angeben der Beziehungsrichtung](#specify-relationship-direction) müssen Sie zwischen einer linken eckigen Klammer für eine Links-nach-Rechts-Beziehung und einer rechten eckigen Klammer für eine Rechts-nach-Links-Beziehung wählen. Sie können nicht beides in denselben Pfeil einbinden. Sie können jedoch bidirektionale Beziehungen durch Verkettung darstellen.

Sie können mehrere Beziehungsbedingungen wie gezeigt **verketten**. Die Platzhalterwerte, die durch Ihre Werte ersetzt werden sollen, sind `twin_1`, alle Instanzen von `relationship_condition` und `twin_2`.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchChainSyntax":::

### <a name="examples"></a>Beispiele

Hier ist ein Beispiel, in dem die **Beziehungsrichtung, der Beziehungsname und die Anzahl der Hops** kombiniert werden. Die folgende Abfrage findet die Zwillinge *floor* und *room*, wobei die Beziehung zwischen *floor* und *room* die folgenden Bedingungen erfüllt:
* Die Beziehung ist von links nach rechts. Dabei ist *floor* die Quelle und *room* das Ziel.
* Die Beziehung hat entweder den Namen „contains“ oder „isAssociatedWith“.
* Die Beziehung verwendet entweder 4 oder 5 Hops.

Die Abfrage gibt auch an, dass der Zwilling *floor* eine `$dtId` „thermostat-15“ aufweist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedHopsExample":::

Hier ist ein Beispiel, in dem die **Beziehungsrichtung, der Beziehungsname und eine benannte Abfragevariable für die Beziehung** kombiniert werden. Die folgende Abfrage findet die Zwillinge *floor* und *room*, wobei die Beziehung zwischen *floor* und *room* einer Abfragevariablen *r* zugewiesen wird und die folgenden Bedingungen erfüllt:
* Die Beziehung ist von links nach rechts. Dabei ist *floor* die Quelle und *room* das Ziel.
* Die Beziehung hat entweder den Namen „contains“ oder „isAssociatedWith“.
* Die Beziehung, der eine Abfragevariable *r* zugewiesen wird, weist eine length-Eigenschaft auf, die gleich 10 ist.

Die Abfrage gibt auch an, dass der Zwilling *floor* eine `$dtId` „thermostat-15“ aufweist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedVariableExample":::

Im folgenden Beispiel werden **verkettete** Beziehungsbedingungen gezeigt. Die Abfrage findet die Zwillinge *floor*, *cafe* und *room*. Dabei gilt Folgendes...
* Die Beziehung zwischen *floor* und *room* erfüllt die folgenden Bedingungen:
    - Die Beziehung ist von links nach rechts. Dabei ist *floor* die Quelle und *cafe* das Ziel.
    - Die Beziehung hat entweder den Namen „contains“ oder „isAssociatedWith“.
    - Die Beziehung, der die Abfragevariable *r* zugewiesen wird, weist eine length-Eigenschaft auf, die gleich 10 ist.
* Die Beziehung zwischen *cafe* und *room* erfüllt die folgenden Bedingungen:
    - Die Beziehung ist von links nach rechts. Dabei ist *floor* die Quelle und *cafe* das Ziel.
    - Die Beziehung hat entweder den Namen „has“ oder „includes“.
    - Die Beziehung verfügt über bis zu 3 Hops (also 1, 2 oder 3 Hops).

Die Abfrage gibt auch an, dass der Zwilling *floor* eine `$dtId` „thermostat-15“ und der Zwilling *cafe* eine Temperatur von 55 aufweist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainExample":::

Sie können auch verkettete Beziehungsbedingungen verwenden, um **bidirektionale Beziehungen** auszudrücken. Die folgende Abfrage findet die Zwillinge *floor*, *room* und *building*. Dabei gilt Folgendes...
* Die Beziehung zwischen *building* und *floor* erfüllt die folgenden Bedingungen:
    - Die Beziehung ist von links nach rechts. Dabei ist *building* die Quelle und *floor* das Ziel.
    - Die Beziehung hat den Namen „isAssociatedWith“.
    - Die Beziehung erhält eine Abfragevariable *r1*.
* Die Beziehung zwischen *floor* und *room* erfüllt die folgenden Bedingungen:
    - Die Beziehung ist von links nach rechts. Dabei ist *room* die Quelle und *floor* das Ziel.
    - Die Beziehung hat den Namen „isAssociatedWith“.
    - Die Beziehung erhält eine Abfragevariable *r2*.

Die Abfrage gibt auch an, dass der Zwilling *building* eine `$dtId` „building-3“ hat und *room* eine Temperatur über 50 aufweist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MatchCombinedChainBDExample":::

## <a name="limitations"></a>Einschränkungen

Für Abfragen mit `MATCH` gelten die folgenden Einschränkungen.
* Pro Abfrageanweisung wird nur ein `MATCH`-Ausdruck unterstützt.
* `$dtId` ist in der `WHERE`-Klausel erforderlich.
* Das Zuweisen einer Abfragevariablen zur Beziehung wird nur unterstützt, wenn die Abfrage einen einzelnen Hop angibt.
* Die in einer Abfrage maximal unterstützte Anzahl von Hops beträgt 10.