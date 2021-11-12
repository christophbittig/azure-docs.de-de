---
title: Spaltenmuster bei der Zuordnungdatenflusses
titleSuffix: Azure Data Factory & Azure Synapse
description: Erstellen Sie verallgemeinerte Datentransformationsmuster unter Verwendung von Spaltenmustern bei der Zuordnungdatenflüssen mit Azure Data Factory oder Synapse Analytics.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 7b8343c06dd0815f8c0fb44fa00f85c2c0195b13
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058523"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Verwenden von Spaltenmustern in Mappingdatenflüssen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In verschiedenen Mappingdatenfluss-Transformationen kann auf Vorlagenspalten mithilfe von Mustern (anstelle von hartcodierten Spaltennamen) verwiesen werden. Dieser Abgleich wird als *Spaltenmuster* bezeichnet. Sie können Muster definieren, um Spalten auf der Grundlage von Name, Datentyp, Stream, Ursprung oder Position abzugleichen, anstatt exakte Feldnamen zu verwenden. Spaltenmuster sind in zwei Szenarien hilfreich:

* Wenn Sie eingehende Quellfelder häufig ändern – etwa im Falle von sich ändernden Spalten in Textdateien oder NoSQL-Datenbanken. Dieses Szenario wird als [Schemaabweichung](concepts-data-flow-schema-drift.md) bezeichnet.
* Wenn Sie einen Vorgang für eine große Gruppe von Spalten ausführen möchten, um beispielsweise alle Spalten, deren Spaltenname „total“ enthält, in einen Double-Wert umzuwandeln.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Spaltenmuster in „Abgeleitete Spalte“ und „Aggregieren“

Um ein Spaltenmuster in einer Transformation einer abgeleiteten Spalte, eines Aggregats oder eines Fensters hinzuzufügen, klicken Sie oberhalb der Spaltenliste auf **Hinzufügen**, oder klicken Sie auf das Pluszeichen („+“) neben einer vorhandenen abgeleiteten Spalte. Wählen Sie **Spaltenmuster hinzufügen** aus.

:::image type="content" source="media/data-flow/add-column-pattern.png" alt-text="Screenshot: Plussymbol zum Hinzufügen von Spaltenmustern":::

Verwenden Sie den [Ausdrucks-Generator](concepts-data-flow-expression-builder.md), um die Übereinstimmungsbedingung einzugeben. Erstellen Sie einen booleschen Ausdruck, um Spalten auf der Grundlage von `name`, `type`, `stream`, `origin` und `position` der Spalte abzugleichen. Das Muster wirkt sich sowohl auf Spalten mit Datendrift als auch auf definierte Spalten aus, bei denen die Bedingung zutrifft (also „true“ zurückgegeben wird).


:::image type="content" source="media/data-flow/edit-column-pattern.png" alt-text="Screenshot: Registerkarte mit den Einstellungen der abgeleiteten Spalte":::

Das obige Spaltenmuster stimmt mit jeder Spalte vom Typ „Double“ überein und erstellt eine abgeleitete Spalte pro Übereinstimmung. Durch Angeben von `$$` als Spaltennamensfeld wird jede übereinstimmende Spalte mit demselben Namen aktualisiert. Der Wert jeder Spalte ist der vorhandene Wert, der auf zwei Dezimalstellen gerundet wird.

Um zu überprüfen, ob die Übereinstimmungsbedingung korrekt ist, können Sie auf der Registerkarte **Untersuchen** das Ausgabeschema definierter Spalten überprüfen oder auf der Registerkarte **Datenvorschau** eine Momentaufnahme der Daten anzeigen. 

:::image type="content" source="media/data-flow/columnpattern3.png" alt-text="Screenshot: Registerkarte „Ausgabeschema“":::

### <a name="hierarchical-pattern-matching"></a>Hierarchischer Musterabgleich

Sie können auch einen Musterabgleich in komplexen hierarchischen Strukturen erstellen. Erweitern Sie den Abschnitt `Each MoviesStruct that matches`, in dem Sie für jede Hierarchie im Datenstrom aufgefordert werden. Anschließend können Sie übereinstimmende Muster für Eigenschaften innerhalb der ausgewählten Hierarchie erstellen.

:::image type="content" source="media/data-flow/patterns-hierarchy.png" alt-text="Screenshot zeigt ein hierarchisches Spaltenmuster.":::

## <a name="rule-based-mapping-in-select-and-sink"></a>Regelbasierte Zuordnung in „Auswählen“ und „Senke“

Beim Zuordnen von Spalten in Quell- und Auswahltransformationen können Sie entweder eine feste Zuordnung oder regelbasierte Zuordnungen hinzufügen. Gleichen Sie basierend auf den Werten `name`, `type`, `stream`, `origin` und `position` von Spalten ab. Es kann eine beliebige Kombination aus festen und regelbasierten Zuordnungen verwendet werden. Standardmäßig werden alle Projektionen mit mehr als 50 Spalten auf eine regelbasierte Zuordnung festgelegt, die einen Abgleich auf Übereinstimmung für jede Spalte vornimmt und den eingegebenen Namen ausgibt. 

Klicken Sie zum Hinzufügen einer regelbasierten Zuordnung auf **Zuordnung hinzufügen**, und wählen Sie **Rule-based mapping** (Regelbasierte Zuordnung) aus.

:::image type="content" source="media/data-flow/rule2.png" alt-text="Screenshot: Ausgewählte regelbasierte Zuordnung unter „Zuordnung hinzufügen“":::

Jede regelbasierte Zuordnung erfordert zwei Eingaben: die Bedingung, gemäß der der Abgleich auf Übereinstimmung erfolgen soll, und den Namen jeder zugeordneten Spalte. Beide Werte werden über den [Ausdrucks-Generator](concepts-data-flow-expression-builder.md) eingegeben. Geben Sie im linken Ausdrucksfeld Ihre boolesche Übereinstimmungsbedingung ein. Geben Sie im rechten Ausdrucksfeld das Zuordnungsziel für die übereinstimmende Spalte an.

:::image type="content" source="media/data-flow/rule-based-mapping.png" alt-text="Screenshot: Eine Zuordnung":::

Verwenden Sie die `$$`-Syntax, um auf den eingegebenen Namen einer übereinstimmenden Spalte zu verweisen. Nehmen wir das obige Bild als Beispiel: Ein Benutzer wünscht einen Abgleich auf Übereinstimmung aller Zeichenfolgenspalten, deren Namen kürzer als sechs Zeichen sind. Wenn eine eingehende Spalte mit`test` benannt wurde, wird die Spalte durch den Ausdruck `$$ + '_short'` in `test_short` umbenannt. Wenn dies die einzige vorhandene Zuordnung ist, werden alle Spalten, die die Bedingung nicht erfüllen, aus den ausgegebenen Daten entfernt.

Muster stimmen sowohl mit abweichenden als auch definierten Spalten überein. Um zu prüfen, welche definierten Spalten von einer Regel zugeordnet werden, klicken Sie neben der Regel auf das Brillensymbol. Überprüfen Sie die Ausgabe mithilfe der Datenvorschau.

### <a name="regex-mapping"></a>Zuordnung mit regulärem Ausdruck

Wenn Sie auf das nach unten zeigende Chevronsymbol klicken, können Sie eine RegEx-Zuordnungsbedingung angeben. Eine RegEx-Zuordnungsbedingung gleicht alle Spaltennamen ab, die der angegebenen RegEx-Bedingung entsprechen. Sie kann in Kombination mit standardmäßigen regelbasierten Zuordnungen verwendet werden.

:::image type="content" source="media/data-flow/regex-matching.png" alt-text="Screenshot: RegEx-Zuordnungsbedingung mit Hierarchieebene und Namensübereinstimmung":::

Das obige Beispiel stimmt mit dem RegEx-Muster `(r)` oder einem beliebigen Spaltennamen überein, der den Kleinbuchstaben r enthält. Ähnlich wie bei der standardmäßigen regelbasierten Zuordnung werden alle übereinstimmenden Spalten von der Bedingung auf der rechten Seite mit der `$$`-Syntax geändert.

### <a name="rule-based-hierarchies"></a>Regelbasierte Hierarchien

Wenn Ihre definierte Projektion eine Hierarchie hat, können Sie die regelbasierte Zuordnung verwenden, um die Unterspalten der Hierarchie zuzuordnen. Geben Sie eine Übereinstimmungsbedingung und die komplexe Spalte an, deren Unterspalten Sie zuordnen möchten. Jede übereinstimmende Unterspalte wird mithilfe der rechts angegebenen Regel „Name as“ (Benennen als) ausgegeben.

:::image type="content" source="media/data-flow/rule-based-hierarchy.png" alt-text="Screenshot: Regelbasierte Zuordnung für eine Hierarchie":::

Das obige Beispiel stimmt mit allen Unterspalten der komplexen Spalte `a` überein. `a` enthält die beiden Unterspalten `b` und `c`. Das Ausgabeschema weist die beiden Spalten `b` und `c` auf, da die Bedingung „Name as“ `$$` ist.

## <a name="pattern-matching-expression-values"></a>Ausdruckswerte für den Musterabgleich

* `$$` wird zur Laufzeit in den Namen oder Wert der jeweiligen Übereinstimmung umgewandelt. Stellen Sie sich `$$` als Entsprechung zu `this` vor.
* `$0` wird zur Laufzeit in die aktuelle Übereinstimmung des Spaltennamens für skalare Typen umgewandelt. Bei hierarchischen Typen stellt `$0` den Hierarchiepfad der übereinstimmenden Spalte dar.
* `name` stellt den Namen der einzelnen eingehenden Spalten dar.
* `type` stellt den Datentyp der einzelnen eingehenden Spalten dar. Die Liste der Datentypen im Datenflusstypsystem finden Sie [hier](concepts-data-flow-overview.md#data-flow-data-types).
* `stream` stellt den Namen dar, der dem jeweiligen Datenstrom oder der Transformation in Ihrem Flow zugeordnet ist.
* `position` ist die Ordinalposition von Spalten in Ihrem Datenfluss.
* `origin` ist die Transformation, von der eine Spalte stammt oder bei der sie zuletzt aktualisiert wurde.

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich ausführlicher über die [Ausdruckssprache](data-flow-expression-functions.md) von Mappingdatenflüssen für Datentransformationen.
* Verwenden Sie Spaltenmuster in der [Senkentransformation](data-flow-sink.md) und der [Auswahltransformation](data-flow-select.md) mit regelbasierter Zuordnung.
