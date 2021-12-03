---
title: Verwenden des Azure Digital Twins-Explorers
titleSuffix: Azure Digital Twins
description: Grundlegendes zur Verwendung der Features von Azure Digital Twins-Explorer
author: baanders
ms.author: baanders
ms.date: 10/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f77efc7ab6880db771ea7f53fe9cfb2858c6ecc0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327894"
---
# <a name="use-azure-digital-twins-explorer-preview"></a>Verwenden von Azure Digital Twins-Explorer (Vorschau)

Der [Azure Digital Twins-Explorer](concepts-azure-digital-twins-explorer.md) ist ein Tool für die Visualisierung von Azure Digital Twins und die Arbeit mit ihnen. In diesem Artikel werden die Features von Azure Digital Twins-Explorer und deren Verwendung zum Verwalten der Daten in Ihrer Azure Digital Twins-Instanz beschrieben. Sie können mit dem Azure Digital Twins-Explorer über Klicks oder [Tastenkombinationen interagieren](#accessibility-and-advanced-settings).

>[!NOTE]
>Dieses Tool ist aktuell als **öffentliche Vorschauversion** verfügbar.

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

### <a name="switch-contexts-within-the-app"></a>Wechseln von Kontexten innerhalb der App

In der Anwendung können Sie auch ändern, welche Instanz derzeit mit dem Explorer verbunden ist, indem Sie auf der oberen Symbolleiste den Instanznamen auswählen.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/instance-url-1.png" alt-text="Screenshot: Azure Digital Twins-Explorer. Der Instanzname in der oberen Symbolleiste ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/instance-url-1.png":::

Dadurch wird das **modale Dialogfeld „Azure Digital Twins URL“** (Azure Digital Twins-URL) aufgerufen, in dem Sie nach *https://* den Hostnamen einer anderen Azure Digital Twins-Instanz eingeben können, um eine Verbindung mit dieser Instanz herzustellen.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/instance-url-2.png" alt-text="Screenshot: Azure Digital Twins-Explorer. Das modale Dialogfeld „Azure Digital Twins URL“ zeigt ein bearbeitbares Feld an, das „https://“ und einen Hostnamen enthält." lightbox="media/how-to-use-azure-digital-twins-explorer/instance-url-2.png":::

>[!NOTE]
>Derzeit ist die Möglichkeit zum Wechseln von Kontexten innerhalb der App für persönliche Microsoft-Konten (MSA) **nicht** verfügbar. MSA-Benutzer müssen aus der richtigen Instanz im Azure-Portal auf den Explorer zugreifen oder über einen [direkten Link zur Umgebung](#link-to-your-environment) eine Verbindung mit einer bestimmten Instanz herstellen.


## <a name="query-your-digital-twin-graph"></a>Abfragen des digitalen Zwillingsgraphen

Sie können den Bereich **Abfrage-Explorer** verwenden, um [Abfragen](concepts-query-language.md) für Ihren Graphen auszuführen.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel.png" alt-text="Screenshot: Azure Digital Twins-Explorer. Der Bereich „Abfrage-Explorer“ ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel.png":::

Geben Sie die Abfrage ein, die Sie ausführen möchten, und wählen Sie die Schaltfläche **Run Query** (Abfrage ausführen) aus. Dadurch werden die Abfrageergebnisse im Bereich **Twin Graph** (Zwillingsgraph) geladen.

>[!NOTE]
> Abfrageergebnisse, die Beziehungen enthalten, können nur im Bereich **Twin Graph** (Zwillingsgraph) gerendert werden, wenn die Ergebnisse mindestens einen Zwilling enthalten. Während Abfragen, die nur Beziehungen zurückgeben, in Azure Digital Twins möglich sind, können Sie diese nur im [Ausgabebereich](#accessibility-and-advanced-settings) im Azure Digital Twins-Explorer anzeigen.

### <a name="overlay-query-results"></a>Überlagern von Abfrageergebnissen

Sie können das Kontrollkästchen **Overlay results** (Ergebnisse überlagern) aktivieren, bevor Sie Ihre Abfrage ausführen, wenn die Ergebnisse gegenüber den derzeit im Bereich **Twin Graph** angezeigten Ergebnissen hervorgehoben werden sollen, anstatt den Inhalt des Bereichs vollständig durch die neuen Abfrageergebnisse zu ersetzen.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-overlay-results.png" alt-text="Screenshot: Bereich „Abfrage-Explorer“ im Azure Digital Twins-Explorer. Das Kontrollkästchen „Overlay results“ (Ergebnisse überlagern) ist aktiviert, und zwei Zwillinge werden im größeren Graphen hervorgehoben, der im Bereich „Twin Graph“ angezeigt wird." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-overlay-results.png":::

Wenn das Abfrageergebnis etwas enthalten, das derzeit nicht im Bereich **Twin Graph** angezeigt wird, wird das Element der vorhandenen Ansicht hinzugefügt.

### <a name="save-and-rerun-queries"></a>Speichern und erneutes Ausführen von Abfragen

Abfragen können in Ihrem Browser im lokalen Speicher gespeichert werden, sodass sie einfach ausgewählt und erneut ausgeführt werden können.

>[!NOTE]
>Lokaler Browserspeicher bedeutet, dass gespeicherte Abfragen in anderen Browsern als dem, in dem Sie sie gespeichert haben, nicht verfügbar sind, und dass sie auf unbestimmte Zeit im Browserspeicher verbleiben, bis der lokale Speicher gelöscht wird.

Um eine Abfrage zu speichern, geben Sie sie in das Abfragefeld ein, und wählen Sie rechts im Bereich das Symbol **Speichern** aus. Wenn Sie dazu aufgefordert werden, geben Sie einen Namen für die gespeicherte Suche ein.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-save.png" alt-text="Screenshot: Azure Digital Twins-Explorer. Das Symbol „Speichern“ ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-save.png":::

Nachdem die Abfrage gespeichert wurde, können Sie im Dropdownmenü **Saved Queries** (Gespeicherte Abfragen) auswählen, um sie einfach erneut auszuführen.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-saved-queries.png" alt-text="Screenshot: Bereich „Abfrage-Explorer“ des Azure Digital Twins-Explorers. Das Dropdownmenü „Saved Queries“ (Gespeicherte Abfragen) ist hervorgehoben und zeigt zwei Beispielabfragen an." lightbox="media/how-to-use-azure-digital-twins-explorer/query-explorer-panel-saved-queries.png":::

Um eine gespeicherte Abfrage zu löschen, wählen Sie das Symbol **X** neben dem Namen der Abfrage aus, wenn das Dropdownmenü **Saved Queries** (Gespeicherte Abfragen) geöffnet ist.

>[!TIP]
>Bei großen Graphen wird empfohlen, nur eine begrenzte Teilmenge abzufragen und dann den Rest des Graphen nach Bedarf zu laden. Sie können im Bereich **Twin Graph** auf einen Zwilling doppelklicken, um weitere verknüpfte Knoten abzurufen.

## <a name="explore-the-twin-graph"></a>Erkunden des Twin Graph

Im Bereich **Twin Graph** (Zwillingsdiagramm) können Sie die Zwillinge und Beziehungen in Ihrer Instanz untersuchen.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel.png" alt-text="Screenshot: Azure Digital Twins-Explorer. Der Bereich „Twin Graph“ ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel.png":::

Sie können diesen Bereich verwenden, um [Ihre Zwillinge und Beziehungen anzuzeigen](#view-twins-and-relationships).

Der Bereich „Twin Graph“ bietet auch mehrere Möglichkeiten zum Anpassen der Graphanzeige:
* [Bearbeiten des Layouts des Zwillingsgraphen](#edit-twin-graph-layout)
* [Steuern der Erweiterung des Zwillingsgraphen](#control-twin-graph-expansion)
* [Anzeigen und Ausblenden von Zwillingsgraphelementen](#show-and-hide-twin-graph-elements)
* [Filtern und Hervorheben von Zwillingsgraphelementen](#filter-and-highlight-twin-graph-elements)

### <a name="view-twins-and-relationships"></a>Anzeigen von Zwillingen und Beziehungen

Führen Sie eine Abfrage mithilfe des [Abfrage-Explorers](#query-your-digital-twin-graph) aus, um die Zwillinge und Beziehungen im Abfrageergebnis anzuzeigen, das im Bereich **Twin Graph** angezeigt wird.

>[!TIP]
>Die Abfrage zum Anzeigen aller Zwillinge und Beziehungen ist `SELECT * FROM digitaltwins`.

#### <a name="view-twin-and-relationship-properties"></a>Anzeigen von Zwillings- und Beziehungseigenschaften

Um die Eigenschaftswerte eines Zwillings oder einer Beziehung anzuzeigen, wählen Sie den Zwilling oder die Beziehung im Bereich **Twin Graph** aus, und verwenden Sie die Schaltfläche **Toggle property inspector** (Eigenschaftenanalyse umschalten), um den Bereich **Zwillingseigenschaften** bzw. den Bereich **Beziehungseigenschaften** zu erweitern. In diesem Bereich werden alle dem Element zugeordneten Eigenschaften sowie deren Werte angezeigt. Er enthält auch Standardwerte für Eigenschaften, die noch nicht festgelegt wurden.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png" alt-text="Screenshot: Bereich „Twin Graph“ im Azure Digital Twins-Explorer. Der Zwilling „FactoryA“ ist ausgewählt, und der Bereich „Zwillingseigenschaften“ ist erweitert und zeigt die Eigenschaften des Zwillings an." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png":::

##### <a name="data-type-icons"></a>Datentyp-Symbole

Die in den Dialogfeldern **Zwillingseigenschaften** und **Beziehungseigenschaften** angezeigten Eigenschaften werden jeweils mit einem Symbol dargestellt, das den Typ des Feldes aus dem DTDL-Modell angibt. Sie können den Mauszeiger über ein Symbol bewegen, um den zugehörigen Typ anzuzeigen.

Die folgende Tabelle zeigt die möglichen Datentypen und die entsprechenden Symbole. Die Tabelle enthält auch Links von jedem Datentyp zu seiner Schemabeschreibung in der [DTDL-Spezifikation](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

| Symbol | Datentyp |
| --- | --- |
| ![boolesches Symbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/boolean.svg) | [boolean](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![Komponenten-Symbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/component.svg) | [Komponente](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) |
| ![Datumssymbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/date.svg) | [date](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![DatumZeit-Symbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/datetime.svg) | [dateTime](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![Dauer-Symbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/duration.svg) | [duration](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![Enum-Symbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/enum.svg) | [enum](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#enum) |
| ![Kartensymbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/map.svg) | [map](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#map) |
| ![Numerisches Symbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/numeric.svg) | Numerische Typen, einschließlich [Double, Float, Integer und Long](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![Objekt-Symbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/object.svg) | [object](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#object) |
| ![Zeichenfolgensymbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/string.svg) | [string](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |
| ![Zeit-Symbol](./media/how-to-use-azure-digital-twins-explorer/data-icons/time.svg) | [time](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#primitive-schemas) |

##### <a name="errors"></a>Errors

Im Bereich „Zwillingseigenschaften“ werden Fehlermeldungen angezeigt, wenn der Zwilling oder einige seiner Eigenschaften nicht mehr mit dem zugehörigen Modell übereinstimmen. 

Es gibt zwei mögliche Fehlerszenarien, die jeweils eine eigene Fehlermeldung ergeben:
* **Ein oder mehrere vom Zwilling verwendete Modelle fehlen**. Infolgedessen werden alle Eigenschaften, die mit diesem Modell verbunden sind, im Fenster Zwillingseigenschaften als "fehlend" gekennzeichnet. Dies kann passieren, wenn das Modell seit der Erstellung des Zwillings gelöscht wurde.
* **Einige Eigenschaften des Zwillings sind nicht Teil des Zwillingsmodells**. Nur diese Eigenschaften werden im Fenster Zwillingseigenschaften als "fehlend" gekennzeichnet. Dies kann passieren, wenn das Modell für den Zwilling ersetzt oder geändert wurde, seit die Eigenschaften festgelegt wurden, und die Eigenschaften in der neuesten Version des Modells nicht mehr vorhanden sind.

Die beiden Fehlermeldungen sind in der folgenden Abbildung dargestellt:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/properties-errors.png" alt-text="Screenshot des Azure Digital Twins-Explorer Zwillingseigenschaften-Panels, das zwei Fehlermeldungen zeigt. Eine Fehlermeldung zeigt an, dass Modelle fehlen, die andere, dass den Eigenschaften ein Modell fehlt. " lightbox="media/how-to-use-azure-digital-twins-explorer/properties-errors.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="view-a-twins-relationships"></a>Anzeigen der Beziehungen eines Zwillings

Sie können auch schnell den Code aller Beziehungen anzeigen, die sich auf einen bestimmten Zwilling beziehen (einschließlich eingehender und ausgehender Beziehungen).

Klicken Sie hierzu mit der rechten Maustaste auf einen Zwilling im Graphen, und wählen Sie **Get relationships** (Beziehungen abrufen) aus. Dadurch wird ein modales Dialogfeld **Relationship Information** (Beziehungsinformationen) geöffnet, das die [JSON-Darstellung](concepts-twins-graph.md#relationship-json-format) aller eingehenden und ausgehenden Beziehungen anzeigt.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-get-relationships.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. In der Mitte des Bildschirms wird ein modales Dialogfeld „Relationship Information“ (Beziehungsinformationen) mit eingehenden und ausgehenden Beziehungen angezeigt." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-get-relationships.png":::

### <a name="edit-twin-graph-layout"></a>Bearbeiten des Layouts des Zwillingsgraphen

Sie können die Zwillinge in verschiedenen Konfigurationen neu anordnen, indem Sie auf sie klicken und sie auf dem Bildschirm „Twin Graph“ verschieben.

Sie können auch einen von mehreren Layoutalgorithmen über die Optionen im Menü **Choose Layout** (Layout wählen) auf den Graphen anwenden. 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-layout.png" alt-text="Screenshot: Bereich „Twin Graph“ im Azure Digital Twins-Explorer. Die Schaltfläche „Choose Layout“ (Layout wählen) ist hervorgehoben und zeigt ein Menü mit den Layoutoptionen Cola, Dagre, fCoSE und Klay an." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-layout.png":::

### <a name="control-twin-graph-expansion"></a>Steuern der Erweiterung des Zwillingsgraphen

Beim Anzeigen eines Abfrageergebnisses im Bereich **Twin Graph** können Sie auf einen Zwilling doppelklicken, damit der Graph seine Beziehungen und zugehörigen Zwillinge abruft und diese anzeigt, wenn sie nicht bereits in der Ansicht vorhanden sind. Sie können diese Erweiterung anpassen, indem Sie eine Größe und Richtung festlegen, um zu bestimmen, wie viele Zwillinge abgerufen werden sollen.

Verwenden Sie die Option **Expansion Level** (Erweiterungsebene), um die Anzahl der Ebenen festzulegen, die erweitert werden sollen. Diese Zahl gibt an, wie viele Ebenen von Beziehungen vom ausgewählten Zwilling abgerufen werden sollen.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-level.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. Die Schaltfläche „Expansion Level“ (Erweiterungsebene) ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-level.png":::

Verwenden Sie die Schaltfläche **Expansion Direction** (Erweiterungsrichtung), um anzugeben, welchen Beziehungstypen beim Erweitern gefolgt werden soll. Auf diese Weise können Sie zwischen nur eingehenden, nur ausgehenden oder eingehenden sowie ausgehenden Beziehungen auswählen.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-direction.png" alt-text="Screenshot: Bereich „Twin Graph“ im Azure Digital Twins-Explorer. Die Schaltfläche „Expansion Direction“ (Erweiterungsrichtung) ist hervorgehoben und zeigt ein Menü mit den Optionen „In“ (Eingehend), „Out“ (Ausgehend) und „In/Out“ (Eingehend/Ausgehend) an." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-expansion-direction.png":::

### <a name="show-and-hide-twin-graph-elements"></a>Anzeigen und Ausblenden von Zwillingsgraphelementen

Sie können die Option umschalten, um Zwillinge oder Beziehungen in der Graphenansicht auszublenden. 

Um einen Zwilling oder eine Beziehung auszublenden, klicken Sie im Fenster **Twin Graph** mit der rechten Maustaste darauf. Dadurch wird ein Menü mit Optionen zum Ausblenden des Elements oder anderer verwandter Elemente geöffnet.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-hide.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. Der Zwilling „FactoryA“ ist ausgewählt, und es gibt ein Menü mit Optionen zum Ausblenden ausgewählter Elemente, Ausblenden ausgewählter Elemente und untergeordneter Elemente, Ausblenden aller anderen Elemente sowie Ausblenden der nicht untergeordneten Elemente." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-hide.png":::

Sie können auch mehrere Zwillinge oder Beziehungen gleichzeitig ausblenden, indem Sie die Tasten STRG/CMD oder UMSCHALT drücken, um mehrere Elemente desselben Typs im Graphen gleichzeitig auszuwählen. Klicken Sie wie zuvor beschrieben mit der rechten Maustaste, um die Optionen zum Ausblenden anzuzeigen.

Um alle Zwillinge erneut anzuzeigen, nachdem einige ausgeblendet waren, verwenden Sie die Schaltfläche **Show All** (Alle anzeigen). Um alle Beziehungen anzuzeigen, verwenden Sie die Schaltfläche **Show All Relationships** (Alle Beziehungen anzeigen).

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-show.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. Die Schaltflächen „Show All“ (Alle anzeigen) und „Show All Relationships“ (Alle Beziehungen anzeigen) sind hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-show.png":::

### <a name="filter-and-highlight-twin-graph-elements"></a>Filtern und Hervorheben von Zwillingsgraphelementen

Sie können die Zwillinge und Beziehungen, die im Diagramm angezeigt werden, nach Text filtern, indem Sie dieses **Filtersymbol** auswählen:

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-filter-text.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. Das Textfiltersymbol ist ausgewählt und zeigt die Registerkarte „Filter“ an, auf der Sie einen Suchbegriff eingeben können." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-filter-text.png":::

Sie können die Zwillinge und Beziehungen, die im Diagramm erscheinen, auch durch Text hervorheben, indem Sie das Symbol **Hervorheben** wählen:

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-text.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. Das Textfiltersymbol ist ausgewählt und zeigt die Registerkarte „Highlight“ (Hervorheben) an, auf der Sie einen Suchbegriff eingeben können." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-text.png":::

## <a name="manage-twins-and-graph"></a>Verwalten von Zwillingen und Graphen

Der Azure Digital Twins-Explorer bietet mehrere Möglichkeiten zum Verwalten der [Zwillinge](concepts-twins-graph.md#digital-twins) und [Beziehungen](concepts-twins-graph.md#relationships-a-graph-of-digital-twins) in Ihrer Instanz.

In diesem Abschnitt wird beschrieben, wie Sie die folgenden Verwaltungsaktivitäten durchführen:
* [Anzeigen einer flachen Liste von Zwillingen und Beziehungen](#view-flat-list-of-twins-and-relationships)
* [Erstellen von Zwillingen](#create-twins) mit oder ohne Anfangseigenschaften
* Erstellen von [Beziehungen](#create-relationships) zwischen Zwillingen
* [Bearbeiten von Zwillings- und Beziehungseigenschaften](#edit-twin-and-relationship-properties)
* [Löschen von Zwillingen und Beziehungen](#delete-twins-and-relationships)

Informationen zu Anzeigeoptionen für Zwillinge und Beziehungen finden Sie unter [Untersuchen von Zwillingen und des Zwillingsgraphen](#explore-the-twin-graph).

### <a name="view-flat-list-of-twins-and-relationships"></a>Anzeigen einer flachen Liste von Zwillingen und Beziehungen

Im Bereich **Zwillinge** wird eine flache Liste der Zwillinge und der zugehörigen Beziehungen angezeigt. Sie können Zwillinge nach dem Namen suchen und diese erweitern, um Details zu ihren eingehenden und ausgehenden Beziehungen zu erhalten.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twins-panel.png" alt-text="Screenshot: Bereich „Zwillinge“ im Azure Digital Twins-Explorer. Ein Zwilling ist hervorgehoben, und seine Beziehungen werden angezeigt." lightbox="media/how-to-use-azure-digital-twins-explorer/twins-panel.png":::

### <a name="create-twins"></a>Erstellen von Zwillingen

Sie können einen neuen digitalen Zwilling aus seiner Modelldefinition im Bereich **Models** (Modelle) erstellen.

Um einen Zwilling aus einem Modell zu erstellen, suchen Sie nach diesem Modell in der Liste, und wählen Sie dann neben dem Modellnamen die Menüpunkte. Wählen Sie anschließend **Create a Twin** (Einen Zwilling erstellen). Sie werden aufgefordert, einen **Namen** für den neuen Zwilling einzugeben, der eindeutig sein muss. Speichern Sie den Zwilling dann. Er wird Ihrem Graphen hinzugefügt.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-create-a-twin.png" alt-text="Screenshot: Bereich „Models“ (Modelle) im Azure Digital Twins-Explorer. Die Menüpunkte für ein einzelnes Modell sind hervorgehoben, und die Menüoption „Create a Twin“ (Zwilling erstellen) ist ebenfalls hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-create-a-twin.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Um Ihrem Zwilling Eigenschaftswerte hinzuzufügen, siehe [Eigenschaften von Zwillingen und Beziehungen bearbeiten](#edit-twin-and-relationship-properties).

### <a name="create-relationships"></a>Erstellen von Beziehungen

Um eine Beziehung zwischen zwei Zwillingen zu erstellen, wählen Sie zunächst den Quellzwilling für die Beziehung im Fenster **Twin Graph** (Zwillingsdiagramm) aus. Halten Sie als dann eine Taste STRG/CMD oder UMSCHALT gedrückt, während Sie einen zweiten Zwilling als Ziel der Beziehung auswählen.

Sobald die beiden Zwillinge gleichzeitig ausgewählt sind, klicken Sie mit der rechten Maustaste auf den Zielzwilling. Dadurch wird ein Menü mit einer Option **Add relationships** (Beziehungen hinzufügen) angezeigt, um Beziehungen zwischen ihnen hinzuzufügen.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-add-relationship.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. Die Zwillinge „FactoryA“ und „Consumer“ sind ausgewählt, und in einem Menü wird die Option zum Hinzufügen von Beziehungen angezeigt." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-add-relationship.png":::

Daraufhin wird das Dialogfeld **Create Relationship** (Beziehung erstellen) angezeigt, in dem der Quellzwilling und der Zielzwilling der Beziehung zu sehen sind, gefolgt von einem Dropdownmenü **Relationship** (Beziehung), das die Beziehungstypen enthält, die der Quellzwilling haben kann (definiert in seinem DTDL-Modell). Wählen Sie eine Option für den Beziehungstyp aus, und **speichern** Sie die neue Beziehung.

### <a name="edit-twin-and-relationship-properties"></a>Zwillings- und Beziehungseigenschaften bearbeiten

Um die Eigenschaftswerte eines Zwillings oder einer Beziehung anzuzeigen, wählen Sie das Element im Bereich **Twin Graph** aus, und verwenden Sie die Schaltfläche **Toggle property inspector** (Eigenschaftenanalyse umschalten), um den Bereich **Zwillingseigenschaften** bzw. den Bereich **Beziehungseigenschaften** zu erweitern.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png" alt-text="Screenshot: Bereich „Twin Graph“ im Azure Digital Twins-Explorer. Der Zwilling „FactoryA“ ist ausgewählt, und der Bereich „Zwillingseigenschaften“ ist erweitert und zeigt die Eigenschaften des Zwillings an." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties.png":::

Sie können diesen Bereich verwenden, um beschreibbare Eigenschaften direkt zu bearbeiten. Aktualisieren Sie ihre Werte inline und wählen Sie die Schaltfläche **Änderungen speichern** oben im Bedienfeld, um sie zu speichern. Wenn die Aktualisierung gespeichert wird, wird auf dem Bildschirm ein modales Fenster mit dem JSON Patch-Vorgang angezeigt, der von der [Update-API](/rest/api/azure-digitaltwins/) angewendet wurde.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties-save.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. In der Mitte des Bildschirms wird ein modales Dialogfeld „Path Information“ (Pfadinformationen) mit JSON Patch-Code angezeigt." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-highlight-graph-properties-save.png":::

>[!TIP]
> Die in den Dialogfeldern **Zwillingseigenschaften** und **Beziehungseigenschaften** angezeigten Eigenschaften werden jeweils mit einem Symbol dargestellt, das den Typ des Feldes aus dem DTDL-Modell angibt. Für weitere Informationen über die Typensymbole siehe [Datentypensymbole](#data-type-icons).

### <a name="delete-twins-and-relationships"></a>Löschen von Zwillingen und Beziehungen

Um einen Zwilling oder eine Beziehung zu löschen, klicken Sie im Fenster **Twin Graph** mit der rechten Maustaste darauf. Dadurch wird ein Menü mit einer Option zum Löschen des Elements angezeigt.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-delete.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. Der Zwilling „FactoryA“ ist ausgewählt, und es gibt ein Menü, das eine Option zum Löschen von Zwillingen enthält." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-delete.png":::

Sie können auch mehrere Zwillinge oder Beziehungen gleichzeitig löschen, indem Sie die Tasten STRG/CMD oder UMSCHALT drücken, um mehrere Elemente desselben Typs im Graphen gleichzeitig auszuwählen. Klicken Sie wie zuvor beschrieben mit der rechten Maustaste, um die Elemente zu löschen.

Sie können auch alle Zwillinge in Ihrer Instanz gleichzeitig löschen, indem Sie auf der oberen Symbolleiste die Schaltfläche **Delete All Twins** (Alle Zwillinge löschen) verwenden.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/delete-all-twins.png" alt-text="Screenshot des Azure Digital Twins-Explorers. Das Symbol „Alle Zwillinge löschen“ ist ausgewählt." lightbox="media/how-to-use-azure-digital-twins-explorer/delete-all-twins.png":::

## <a name="explore-models-and-the-model-graph"></a>Untersuchen von Modellen und des Modellgraphen

Modelle können sowohl im Bereich **Models** (Modelle) auf der linken Seite des Azure Digital Twins-Explorer-Bildschirms als auch im Bereich **Model Graph** (Modellgraph) in der Mitte des Bildschirms angezeigt werden.

Der Bereich **Models** (Modelle): :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel.png" alt-text="Screenshot des Azure Digital Twins-Explorers. Der Bereich „Models“ (Modelle) ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel.png":::

Der Bereich **Model Graph** (Modellgraph): :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="Screenshot des Azure Digital Twins-Explorers. Der Bereich „Model Graph“ (Modellgraph) ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

Sie können diese Bereiche verwenden, um [Ihre Modelle anzuzeigen](#view-models).

Der Bereich „Model Graph“ bietet auch mehrere Möglichkeiten zum Anpassen der Graphanzeige:
* [Bearbeiten des Modellgraphlayouts](#edit-model-graph-layout)
* [Filtern und Hervorheben von Modellgraphelementen](#filter-and-highlight-model-graph-elements)
* [Hochladen von Modellimages](#upload-model-images) zur Darstellung von Modellen in den Graphen

### <a name="view-models"></a>Modelle anzeigen

Sie können eine unformatierte Liste der Modelle in Ihrer Instanz im Bereich **Models** (Modelle) anzeigen. Diese Liste kann über die **Suchleiste** im Bereich durchsucht werden.

Sie können den Bereich **Model Graph** (Modellgraph) verwenden, um eine grafische Darstellung der Modelle in Ihrer Instanz zusammen mit den Beziehungen, der Vererbung und den Komponenten, die sie miteinander verbinden, anzuzeigen.

#### <a name="view-model-definition"></a>Anzeigen der Modelldefinition

Um die vollständige Definition eines Modells anzuzeigen, suchen Sie dieses Modell im Bereich **Models** (Modelle), und wählen Sie die Menüpunkte neben dem Modellnamen. Wählen Sie anschließend **View Model** (Modell anzeigen). Dadurch wird ein modales Dialogfeld **Model Information** (Modellinformationen) angezeigt, das die unformatiert DTDL-Definition des Modells enthält.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-view.png" alt-text="Screenshot: Bereich „Models“ (Modelle) im Azure Digital Twins-Explorer. Die Menüpunkte für ein einzelnes Modell sind hervorgehoben, und die Menüoption „View Model“ (Modell anzeigen) ist ebenfalls hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-view.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Sie können auch die vollständige Definition eines Modells anzeigen, indem Sie es im Bereich **Model Graph** (Modellgraph) auswählen und die Schaltfläche **Toggle model details** (Modelldetails umschalten) verwenden, um den Bereich **Model Detail** (Modelldetails) zu erweitern. In diesem Bereich wird auch der vollständige DTDL-Code für das Modell angezeigt.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-graph-details.png" alt-text="Screenshot: Bereich „Model Graph“ (Modellgraph) im Azure Digital Twins-Explorer. Das Modell „Floor“ ist ausgewählt und der Bereich „Model details“ (Modelldetails) erweitert. Er zeigt den DTDL-Code des Modells an." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-graph-details.png":::

### <a name="edit-model-graph-layout"></a>Bearbeiten des Modellgraphlayouts

Sie können die Modelle in verschiedenen Konfigurationen neu anordnen, indem Sie auf sie klicken und sie auf dem Bildschirm „Model Graph“ verschieben.

Sie können auch einen von mehreren Layoutalgorithmen über die Optionen im Menü **Layout wählen** auf den Graphen anwenden. 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-layout.png" alt-text="Screenshot des Azure Digital Twins Explorer Modellgraphen-Panels. Die Schaltfläche &quot;Layout wählen&quot; ist hervorgehoben und zeigt ein Menü mit den Layout-Optionen Cola, Dagre, fCoSE, Klay und d3Force an." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-layout.png":::

### <a name="filter-and-highlight-model-graph-elements"></a>Filtern und Hervorheben von Modellgraphelementen

Sie können die Typen von Verbindungen filtern, die im Modellgraphen angezeigt werden. Wenn Sie einen der Verbindungstypen über die Schalter in diesem Menü deaktivieren, wird verhindert, dass dieser Verbindungstyp im Graphen angezeigt wird.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-connections.png" alt-text="Screenshot des Bereichs „Model Graph“ des Azure Digital Twins-Explorers. Das Filtermenü für Beziehungen, Vererbung und Komponenten ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-connections.png":::

Sie können die im Graphen angezeigten Modelle und Verbindungen auch nach Text filtern, indem Sie das folgende **Filtersymbol** auswählen:

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-text.png" alt-text="Screenshot des Bereichs „Model Graph“ des Azure Digital Twins-Explorers. Das Textfiltersymbol ist ausgewählt und zeigt die Registerkarte „Filter“ an, auf der Sie einen Suchbegriff eingeben können." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-filter-text.png":::

Sie können die im Graphen angezeigten Modelle und Verbindungen nach Text hervorheben, indem Sie das folgende **Hervorhebungssymbol** auswählen:

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-text.png" alt-text="Screenshot des Bereichs „Model Graph“ des Azure Digital Twins-Explorers. Das Textfiltersymbol ist ausgewählt und zeigt die Registerkarte „Highlight“ (Hervorheben) an, auf der Sie einen Suchbegriff eingeben können." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel-highlight-text.png":::

### <a name="upload-model-images"></a>Hochladen von Modellimages

Sie können benutzerdefinierte Images hochladen, um verschiedene Modelle in den Ansichten „Model Graph“ (Modellgraph) und [Twin Graph](#explore-the-twin-graph) (Zwillingsgraph) darzustellen. Sie können Images für einzelne Modelle oder für mehrere Modelle gleichzeitig hochladen.

>[!NOTE]
>Diese Images werden im lokalen Browserspeicher gespeichert. Dies bedeutet, dass die Images in anderen Browsern als dem, in dem Sie sie gespeichert haben, nicht verfügbar sind, und dass sie auf unbestimmte Zeit im Browserspeicher verbleiben, bis der lokale Speicher gelöscht wird.

Um ein Image für ein einzelnes Modell hochzuladen, suchen Sie dieses Modell im Bereich **Models** (Modelle), und wählen Sie die Menüpunkte neben dem Modellnamen aus. Wählen Sie anschließend **Upload Model Image** (Modellimage hochladen). Navigieren Sie auf Ihrem Computer im angezeigten Dateiauswahlfeld zu der Imagedatei, die Sie für dieses Modell hochladen möchten. Wählen Sie **Open** (Öffnen) aus, um sie hochzuladen.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-one-image.png" alt-text="Screenshot: Bereich „Models“ (Modelle) im Azure Digital Twins-Explorer. Die Menüpunkte für ein einzelnes Modell sind hervorgehoben, und die Menüoption „Upload Model Image“ (Modellimage hochladen) ist ebenfalls hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-one-image.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Sie können auch einen Massenupload von Modellimages ausführen.

Verwenden Sie zunächst die folgenden Anweisungen, um die Imagedateinamen vor dem Hochladen festzulegen. Dies ermöglicht dem Azure Digital Twins-Explorer, die Images nach dem Hochladen automatisch den richtigen Modellen zuzuweisen. 
1. Beginnen Sie mit dem Wert der Modell-ID (z. B. `dtmi:example:Floor;1`).
1. Ersetzen Sie Vorkommen von „:“ durch „_“ (das Beispiel wird zu `dtmi_example_Floor;1`).
1. Ersetzen Sie Vorkommen von „;“ durch „-“ (das Beispiel wird zu `dtmi_example_Floor-1`).
1. Stellen Sie sicher, dass die Datei über eine Imageerweiterung verfügt (das Beispiel wird ähnlich wie `dtmi_example_Floor-1.png`).

> [!NOTE]
> Wenn Sie versuchen, ein Image hochzuladen, das gemäß den oben genannten Kriterien keinem vorhandenen Modell entspricht, wird das Image weder hochgeladen noch gespeichert.

Um Images gleichzeitig hochzuladen, verwenden Sie das Symbol **Modellimages hochladen** oben im Bereich „Models“ (Modelle). Wählen Sie im Dateiauswahlfeld aus, welche Imagedateien hochgeladen werden sollen. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-images.png" alt-text="Screenshot des Bereichs „Models“ des Azure Digital Twins-Explorers. Das Symbol „Modellimages hochladen“ ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-images.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-models"></a>Verwalten von Modellen

Sie können den Bereich **Models** (Modelle) auf der linken Seite des Azure Digital Twins-Explorer-Bildschirms verwenden, um Verwaltungsaktivitäten für den gesamten Modellsatz oder für einzelne Modelle durchzuführen. 

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel.png" alt-text="Screenshot: Azure Digital Twins-Explorer. Der Bereich „Models“ (Modelle) ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel.png":::

In diesem Bereich können Sie die folgenden Modellverwaltungsaktivitäten durchführen:
* [Hochladen von Modellen](#upload-models) in Ihre Azure Digital Twins-Instanz
* [Löschen von Modellen](#delete-models) aus Ihrer Instanz
* [Aktualisieren von Modellen](#refresh-models) zum erneuten Laden der Liste aller Modelle in diesen Bereich

Informationen zu den Anzeigeoptionen für Modelle finden Sie unter [Untersuchen von Modellen und des Modellgraphen.](#explore-models-and-the-model-graph)

### <a name="upload-models"></a>Hochladen von Modellen

Sie können Modelle von Ihrem Computer hochladen, indem Sie sie einzeln auswählen oder einen gesamten Ordner mit Modellen gleichzeitig hochladen.

Um ein Modell oder mehrere Modelle hochzuladen, die einzeln ausgewählt wurden, wählen Sie das Symbol **Modell hochladen** aus, das einen Pfeil zeigt, der nach oben weist.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload.png" alt-text="Screenshot des Bereichs „Models“ des Azure Digital Twins-Explorers. Das Symbol „Modell hochladen“ ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Navigieren Sie auf Ihrem Computer im angezeigten Dateiauswahlfeld zu den Modellen, die Sie hochladen möchten. Sie können eine oder mehrere JSON-Modelldateien auswählen und **Öffnen** auswählen, um sie hochzuladen.

Um einen Ordner mit Modellen (einschließlich aller darin gespeicherten Inhalte) hochzuladen, wählen Sie das Symbol **Modellverzeichnis hochladen** aus, das einen Dateiordner zeigt.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-directory.png" alt-text="Screenshot des Bereichs „Models“ des Azure Digital Twins-Explorers. Das Symbol „Modellverzeichnis hochladen“ ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-upload-directory.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Navigieren Sie im angezeigten Dateiauswahlfeld auf Ihrem Computer zu einem Ordner mit JSON-Modelldateien. Wählen Sie **Öffnen** aus, um diesen Ordner der obersten Ebene und seinen Gesamten Inhalt hochzuladen.

>[!IMPORTANT]
>Wenn ein Modell in seiner Definition auf ein anderes Modell verweist (z. B. beim Definieren von Beziehungen oder Komponenten), muss das Modell, auf das verwiesen wird, in der Instanz vorhanden sein, um das Modell hochzuladen, das es verwendet. Wenn Sie Modelle nacheinander hochladen, bedeutet dies, dass Sie das Modell hochladen sollten, auf das verwiesen wird, **bevor** Sie Modelle hochladen, die es verwenden. Wenn Sie Modelle in einem Massenvorgang hochladen, können Sie sie im gleichen Import auswählen. Azure Digital Twins leitet dann die Reihenfolge ab, in der sie hochgeladen werden sollen.

### <a name="delete-models"></a>Löschen von Modellen

Sie können den Bereich „Models“ (Modelle) verwenden, um einzelne Modelle oder alle Modelle in Ihrer Instanz gleichzeitig zu löschen.

Um ein einzelnes Modell zu löschen, suchen Sie dieses Modell in der Liste, und wählen Sie die Menüpunkte neben dem Modellnamen aus. Wählen Sie anschließend **Delete Model** (Modell löschen).

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-one.png" alt-text="Screenshot: Bereich „Models“ (Modelle) im Azure Digital Twins-Explorer. Die Menüpunkte für ein einzelnes Modell sind hervorgehoben, und die Menüoption „Delete Model“ (Modell löschen) ist ebenfalls hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-one.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Um alle Modelle in Ihrer Instanz gleichzeitig zu löschen, wählen Sie oben im Bereich „Models“(Modelle) das Symbol „**Alle Modelle löschen**“ aus.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-all.png" alt-text="Screenshot des Bereichs „Models“ des Azure Digital Twins-Explorers. Das Symbol „Alle Modelle löschen“ ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-delete-all.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="refresh-models"></a>Aktualisieren von Modellen

Wenn Sie den Azure Digital Twins-Explorer öffnen, sollten im Bereich „Models“ (Modelle) automatisch alle verfügbaren Modelle in Ihrer Umgebung angezeigt werden. 

Sie können den Bereich jedoch jederzeit manuell aktualisieren, um die Liste aller Modelle in Ihrer Azure Digital Twins-Instanz erneut zu laden. Wählen Sie hierzu das Symbol **Refresh models** (Modelle aktualisieren). 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/models-panel-refresh.png" alt-text="Screenshot des Bereichs „Models“ des Azure Digital Twins-Explorers. Das Symbol „Modelle aktualisieren“ ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/models-panel-refresh.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="importexport-graph"></a>Importieren/Exportieren eines Graphen

Im Bereich **Twin Graph** (Zwillingsgraph) sind Optionen zum [Importieren](#import-graph) und [Exportieren](#export-graph-and-models) von Graphfeatures verfügbar.

### <a name="import-graph"></a>Importieren eines Graphen

Sie können das Importfeature verwenden, um Ihrer Instanz Zwillinge, Beziehungen und Modelle hinzuzufügen. Dies kann nützlich sein, um viele Zwillinge, Beziehungen und/oder Modelle gleichzeitig zu erstellen.

#### <a name="create-import-file"></a>Erstellen einer Importdatei

Der erste Schritt beim Importieren eines Graphen ist das Erstellen einer Datei, die die Zwillinge und Beziehungen darstellt, die Sie hinzufügen möchten.

Die Importdatei kann in einem der beiden folgenden Formate vorliegen:
* Im **benutzerdefinierten Excel-basierte Format**, das im weiteren Verlauf dieses Abschnitts beschrieben wird. Damit können Sie Zwillinge und Beziehungen hochladen.
* Im **JSON-basierten Format**, das beim [Graphexport](#export-graph-and-models) generiert wird. Diese Datei kann Zwillinge, Beziehungen und/oder Modelle enthalten.

Verwenden Sie das folgende Format, um einen benutzerdefinierten Graphen in Excel zu erstellen.

Jede Zeile stellt ein zu erstellendes Element dar: einen Zwilling, eine Beziehung oder eine Kombination aus Zwilling und entsprechender Beziehung.
Verwenden Sie die folgenden Spalten, um die Zwillings- oder Beziehungsdaten zu strukturieren. Die Spaltennamen können angepasst werden, sie sollten jedoch in dieser Reihenfolge bleiben.

| ModelID | ID | Beziehung (Quelle) | Name der Beziehung | Anfängliche Daten |
| --- | --- | --- | --- | --- |
| *Optional*<br>Die DTMI-Modell-ID für einen Zwilling, der erstellt werden soll.<br><br>Sie können diese Spalte für eine Zeile leer lassen, wenn diese Zeile nur eine Beziehung (keine Zwillinge) erstellen soll. | *Erforderlich*<br>Die eindeutige ID für einen Zwilling.<br><br>Wenn in dieser Zeile ein neuer Zwilling erstellt wird, ist dies die ID des neuen Zwillings.<br>Wenn in der Zeile Beziehungsinformationen enthalten sind, wird diese ID als **Ziel** der Beziehung verwendet. | *Optional*<br>Die ID eines Zwillings, der der **Quellzwilling** für eine neue Beziehung sein soll.<br><br>Sie können diese Spalte für eine Zeile leer lassen, wenn diese Zeile nur einen Zwilling (keine Beziehungen) erstellen soll. | *Optional*<br>Der Name für die neue Beziehung, die erstellt werden soll. Die Beziehungsrichtung verläuft **vom** Zwilling in Spalte C **zum** Zwilling in Spalte B. | *Optional*<br>Eine JSON-Zeichenfolge, die Eigenschafteneinstellungen für den zu erstellenden Zwilling enthält. Die Eigenschaften müssen mit den Eigenschaften übereinstimmen, die im Modell in Spalte A definiert sind. |

Hier sehen Sie eine XLSX-Beispieldatei, mit der ein kleiner Graph mit zwei Etagen und zwei Räumen erstellt wird.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/import-example.png" alt-text="Screenshot: Graphdaten in Excel. Die Spaltenüberschriften entsprechen den oben aufgeführten Feldern in der angegebenen Reihenfolge, und die Zeilen enthalten entsprechende Datenwerte." lightbox="media/how-to-use-azure-digital-twins-explorer/import-example.png":::

Sie können diese Datei und zusätzliche XLSX-Graphbeispiele im [Azure Digital Twins-Explorer-Repository auf GitHub](https://github.com/Azure-Samples/digital-twins-explorer/tree/main/client/examples) anzeigen.

>[!NOTE]
>Die in der XLSX-Datei beschriebenen Eigenschaften und Beziehungen müssen mit den in den Modelldefinitionen für die zugehörigen Zwillinge definierten Eigenschaften und Beziehungen übereinstimmen.

#### <a name="import-file-to-azure-digital-twins-explorer"></a>Importieren einer Datei in Azure Digital Twins-Explorer

Sobald eine Datei auf Ihrem Computer vorhanden ist, die für den Import bereit ist, wählen Sie im Bereich „Twin Graph“ (Zwillingsgraph) das Symbol **Graph importieren** aus.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. Die Schaltfläche „Import Graph“ (Graph importieren) ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import.png":::

Navigieren Sie im angezeigten Dateiauswahlfeld auf Ihrem Computer zu der Graphdatei (XLSX- oder JSON-Datei), die Sie hochladen möchten, und wählen Sie **Öffnen** aus, um sie hochzuladen.

Azure Digital Twins öffnet einen Bereich **Import**, in dem eine Vorschau des zu importierenden Graphen angezeigt wird. Wählen Sie zur Bestätigung in der oberen rechten Ecke des Bereichs auf das Symbol **Speichern**.

Wenn der Import erfolgreich ist, wird in einem modalen Fenster die Anzahl der hochgeladenen Modelle, Zwillinge und Beziehungen angezeigt.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import-successful.png" alt-text="Screenshot des Azure Digital Twins-Explorer Zwillingsdiagrammfensters. In der Mitte des Bildschirms wird ein Modal für den erfolgreichen Import angezeigt, das vier importierte Zwillinge und zwei importierte Beziehungen anzeigt." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-import-successful.png":::

### <a name="export-graph-and-models"></a>Exportieren von Graphen und Modellen

Sie können die Exportfunktion verwenden, um teilweise oder vollständige Diagramme zu exportieren, einschließlich Modellen, Zwillingen und Beziehungen. Der Export serialisiert die Zwillinge und Beziehungen aus den letzten Abfrageergebnissen sowie alle Modelle in der Instanz in ein JSON-basiertes Format, das Sie auf Ihren Computer herunterladen können.

Verwenden Sie zunächst den Bereich [Abfrage-Explorer](#query-your-digital-twin-graph), um eine Abfrage auszuführen, die die Zwillinge und Beziehungen auswählt, die Sie herunterladen möchten. Dadurch werden sie im Bereich „Twin Graph“ (Zwillingsgraph) mit Daten aufgefüllt.

>[!TIP]
>Die Abfrage zum Anzeigen aller Zwillinge und Beziehungen ist `SELECT * FROM digitaltwins`.

Sobald im Bereich „Twin Graph“ der Teil des Diagramms angezeigt wird, den Sie herunterladen möchten, wählen Sie das Symbol **Graph exportieren** aus.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-export.png" alt-text="Screenshot des Bereichs „Twin Graph“ des Azure Digital Twins-Explorers. Die Schaltfläche „Export Graph“ (Graph exportieren) ist hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/twin-graph-panel-export.png":::

Durch diese Aktion wird ein Link **Download** im Feld „Twin Graph“ aktiviert. Wählen Sie diesen Link aus, um eine JSON-basierte Darstellung des Abfrageergebnisses und aller Modelle in Ihrer Instanz auf Ihren Computer herunterzuladen.

>[!TIP]
>Diese Datei kann bearbeitet und/oder über das [Importfeature](#import-graph) erneut in Azure Digital Twins hochgeladen werden.

## <a name="link-to-your-environment"></a>Link zu Ihrer Umgebung

Sie können Ihre Azure Digital Twins-Explorer-Umgebung für andere Benutzer freigeben, um an Aufgaben zusammenzuarbeiten. In diesem Abschnitt wird beschrieben, wie Sie Ihre Azure Digital Twins-Explorer-Umgebung an eine andere Person senden und überprüfen, ob diese über die entsprechenden Zugriffsberechtigungen verfügt.

Um Ihre Umgebung freizugeben, können Sie einen Link an den Empfänger senden, der ein Azure Digital Twins-Explorer-Fenster öffnet, das mit Ihrer Instanz verbunden ist. Verwenden Sie den folgenden Link, und ersetzen Sie die Platzhalter für die **Mandanten-ID** und den **Hostnamen** Ihrer Azure Digital Twins-Instanz. 

`https://explorer.digitaltwins.azure.net/?tid=<tenant-ID>&eid=<Azure-Digital-Twins-host-name>`

>[!NOTE]
> Dem Wert für den Platzhalter für den Hostnamen wird hier **kein** *https://* vorangestellt.

Hier sehen Sie ein Beispiel für eine URL mit den ausgefüllten Platzhalterwerten:

`https://explorer.digitaltwins.azure.net/?tid=00a000aa-00a0-00aa-0a0aa000aa00&eid=ADT-instance.api.wcus.digitaltwins.azure.net`

Damit der Empfänger die Instanz im sich ergebenden Azure Digital Twins-Explorer-Fenster anzeigen kann, muss er sich bei seinem Azure-Konto anmelden und über **Azure Digital Twins-Datenleserzugriff** auf die Instanz verfügen (mehr über Azure Digital Twins-Rollen erfahren Sie unter [Sicherheit](concepts-security.md)). Damit der Empfänger Änderungen am Graphen und an den Daten vornehmen kann, muss er die Rolle **Azure Digital Twins-Datenbesitzer** für die Instanz innehaben.

### <a name="link-with-a-query"></a>Verknüpfen mit einer Abfrage

Möglicherweise möchten Sie eine Umgebung freigeben und eine Abfrage angeben, die beim Start ausgeführt werden soll, um einen Untergraphen oder eine benutzerdefinierte Ansicht für einen Teamkollegen hervorzuheben. Beginnen Sie dazu mit der URL für die Umgebung, und fügen Sie der URL den Abfragetext als QueryString-Parameter hinzu:

`https://explorer.digitaltwins.azure.net/?tid=<tenant-ID>&eid=<Azure-Digital-Twins-host-name>&query=<query-text>`

Der Abfragetext sollte URL-codiert sein. 

>[!TIP]
>Sie können den Abfragetext aus dem **Abfrage-Explorer-Fenster** kopieren und an der richtigen Stelle am Ende der URL in das URL-Fenster einfügen. Beim Übermitteln dieser URL sollte der Abfragetext so konvertiert werden, dass die richtige URL-Codierung verwendet wird.
>
> Sie können auch einen unabhängigen URL-Encoder verwenden, um den Abfragetext zu konvertieren.

Hier ein Beispiel für den Parameter für eine Abfrage an `SELECT * FROM digitaltwins`:

`...&query=SELECT%20*%20FROM%20digitaltwins`

Sie können die vollständige URL dann freigeben.

## <a name="accessibility-and-advanced-settings"></a>Barrierefreiheit und erweiterte Einstellungen

Sie können mehrere erweiterte Einstellungen für Azure Digital Twins-Explorer aktivieren, um Ihre Benutzeroberfläche anzupassen oder den Zugriff zu erleichtern.

Über das Symbol **Tastaturkurzbefehle** in der Symbolleiste oben rechts können Sie eine Liste der Tastaturkurzbefehle anzeigen, mit denen Sie im Azure Digital Twins-Explorer navigieren können.

 :::image type="content" source="media/how-to-use-azure-digital-twins-explorer/keyboard-shortcuts.png" alt-text="Screenshot: Azure Digital Twins-Explorer. Das Symbol „Tastenkombinationen“ ist in der oberen Symbolleiste hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/keyboard-shortcuts.png":::

Unter dem Menüpunkt Einstellungen in der oberen rechten Symbolleiste können Sie auf verschiedene erweiterte Funktionen zugreifen:
* **Eager Loading**: Wenn eine Abfrage Zwillinge zurückgibt, die Beziehungen zu anderen Zwillingen aufweisen, die **nicht** in den Abfrageergebnissen enthalten sind, werden die „fehlenden“ Zwillinge von dieser Funktion vor dem Rendern des Graphen geladen.
* **Zwischenspeichern**: Wenn dieses Feature aktiviert ist, verwaltet der Azure Digital Twins-Explorer einen lokalen Cache mit Beziehungen und Modellen im Arbeitsspeicher, um die Abfrageleistung zu verbessern. Diese Caches werden bei Schreibvorgängen für die relevanten Elemente sowie bei der Browseraktualisierung gelöscht. Dieses Feature ist standardmäßig deaktiviert.
* **Konsole**: Dieses Feature ermöglicht die Anzeige eines Konsolenfensters, das einfache Shellfunktionen für das Arbeiten mit dem Graphen verwenden kann.
* **Ausgabe**: Dieses Feature ermöglicht die Anzeige eines Ausgabefensters, das eine Diagnosenablaufverfolgung von Vorgängen zeigt.
* **Hoher Kontrast**: Dieses Feature ändert die Farben des Azure Digital Twins-Explorers, sodass sie mit größerem Kontrast angezeigt werden.

Sie können das **Bereichslayout anpassen**, indem Sie die Position der Bereiche bearbeiten, aus denen der Azure Digital Twins-Explorer besteht (Abfrage-Explorer, Zwillinge, Modelle, Zwillingsgraph, Modellgraph). Um einen Bereich an eine andere Position zu verschieben, klicken Sie auf den Bereichsnamen, halten die Maustaste gedrückt und ziehen ihn an die neue gewünschte Position.

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/panels.png" alt-text="Screenshot: Azure Digital Twins-Explorer. Die Namen der Bereiche Abfrage-Explorer, Modelle, Zwillingsgraph und Modellgraph sind hervorgehoben." lightbox="media/how-to-use-azure-digital-twins-explorer/panels.png":::

Die Bereichspositionen werden bei der Aktualisierung des Browserfensters zurückgesetzt.

## <a name="next-steps"></a>Nächste Schritte 

Erfahren Sie mehr über das Schreiben von Abfragen für den Azure Digital Twins-Zwillingsgraphen: 
* [Abfragesprache](concepts-query-language.md)
* [Abfragen des Zwillingsgraphen](how-to-query-graph.md)