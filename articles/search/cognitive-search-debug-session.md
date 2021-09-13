---
title: Konzepte von Debugsitzungen (Vorschau)
titleSuffix: Azure Cognitive Search
description: Die über das Azure-Portal aufgerufenen Debugsitzungen bieten eine IDE-ähnliche Umgebung, in der Sie Fehler identifizieren und beheben sowie Änderungen überprüfen und mithilfe von Push an Skillsets in der KI-Anreicherungspipeline übertragen können. Die Debugsitzungen befinden sich in der Vorschauphase.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: db4b08b4403345d7926ec07e94f75cc779fce843
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346580"
---
# <a name="debug-sessions-in-azure-cognitive-search"></a>Debugsitzungen in Azure Cognitive Search

Bei den Debugsitzungen handelt es sich um einen visuellen Editor, der mit einem vorhandenen Skillset im Azure-Portal arbeitet. In einer Debugsitzung können Sie Fehler identifizieren und beheben sowie Änderungen überprüfen und mithilfe von Push an Produktionsskillsets in der KI-Anreicherungspipeline übertragen.

> [!Important]
> Bei Debugsitzungen handelt es sich um eine Previewfunktion, die ohne Vereinbarung zum Servicelevel bereitgestellt wird und nicht für Produktionsworkloads vorgesehen ist. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="using-debug-sessions"></a>Verwenden von Debugsitzungen

Wenn Sie eine Sitzung starten, erstellt der Dienst eine Kopie des Skillsets, des Indexers und des Index, wenn ein einzelnes Dokument für das Testen des Skillsets verwendet wird. In der Sitzung vorgenommene Änderungen werden in der Sitzung gespeichert. Die in der Debugsitzung vorgenommenen Änderungen wirken sich nicht auf das Produktionsskillset aus, es sei denn, die Änderungen werden dorthin committet. Durch das Committen von Änderungen wird das Produktionsskillset überschrieben.

Während einer Debugsitzung können Sie im Kontext eines bestimmten Dokuments ein Skillset bearbeiten sowie alle Knoten in der Anreicherungsstruktur untersuchen und validieren. Wenn die Probleme mit der Anreicherungspipeline behoben wurden, können die Änderungen in der Sitzung gespeichert und an das Produktionsskillset committet werden. 

Wenn die Anreicherungspipeline keine Fehler enthält, kann eine Debugsitzung verwendet werden, um ein Dokument inkrementell anzureichern sowie alle Änderungen vor dem Commit zu testen und zu validieren.

## <a name="creating-a-debug-session"></a>Erstellen einer Debugsitzung

Sie können im Azure-Portal auf der Seite **Übersicht** Ihres Suchdiensts eine Debugsitzung erstellen. Sie müssen über eine vorhandene KI-Anreicherungspipeline einschließlich einer Datenquelle, eines Skillsets, eines Indexers und eines Index verfügen, um eine Debugsitzung starten zu können. Zum Konfigurieren einer Debugsitzung müssen Sie die Sitzung benennen und ein universelles Speicherkonto bereitstellen, das zum Zwischenspeichern der Ausführungen von Skills während der Indexerausführung verwendet wird. Außerdem müssen Sie den auszuführenden Indexer auswählen. Der Indexer verweist auf die Datenquelle, das Skillset und den Index. Die Debugsitzung verwendet standardmäßig das erste Dokument in der Datenquelle. Alternativ können Sie ein zu durchlaufendes Dokument in der Datenquelle angeben.

> :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="Erstellen einer Debugsitzung":::

## <a name="debug-session-features"></a>Debugsitzungsfeatures

Die Debugsitzung beginnt mit dem Ausführen des Skillsets für das ausgewählte Dokument. Sie erfasst zusätzliche Metadaten für jeden Vorgang im Skillset. Die durch das Ausführen der Skills in der Pipeline erstellten Metadaten werden an die folgenden Features übermittelt, mit denen dann Probleme mit dem Skillset identifiziert und behoben werden.

## <a name="ai-enrichments"></a>AI Enrichments (KI-Anreicherungen)

Beim Ausführen von Skills wächst eine Anreicherungsstruktur, die das Dokument darstellt. Die Verwendung einer Struktur zur Visualisierung von Ausgaben von Skills oder Anreicherungen bietet einen umfassenden Überblick über alle durchgeführten Anreicherungen. Sie können das gesamte Dokument ansehen und alle Knoten der Anreicherungsstruktur untersuchen. Diese Perspektive vereinfacht das Formen von Objekten. Dieses Format bietet auch visuelle Hinweise auf Typ, Pfad und Inhalt der einzelnen Knoten in der Struktur.

## <a name="skill-graph"></a>Skill Graph (Skilldiagramm)

In der Ansicht **Skill Graph** (Skilldiagramm) wird eine hierarchische visuelle Darstellung des Skillsets bereitgestellt. In diesem Diagramm ist die Reihenfolge, in der die Skills ausgeführt werden, von oben nach unten dargestellt. Die Skills, die von der Ausgabe anderer Skills abhängen, werden weiter unten im Diagramm angezeigt. Skills auf derselben Ebene in der Hierarchie können parallel ausgeführt werden. 

Durch Klicken auf einen Skill im Diagramm werden die damit verbundenen Skills hervorgehoben sowie die Knoten, die die Eingaben für diesen Skill erstellen, und die, die seine Ausgaben akzeptieren. Für alle Skillknoten werden jeweils dessen Typ, Fehler oder Warnungen und die Anzahl der Ausführungen angezeigt. In der Ansicht **Skill Graph** (Skilldiagramm) wählen Sie aus, welchen Skill Sie debuggen oder verbessern möchten. Wenn Sie einen Skill auswählen, werden die zugehörigen Details im Skilldetailbereich rechts neben dem Diagramm angezeigt.

> :::image type="content" source="media/cognitive-search-debug/skills-graph.png" alt-text="Skill Graph (Skilldiagramm)":::

## <a name="skill-details"></a>Skilldetails

Im Skilldetailbereich wird eine Reihe von Bereichen für das Arbeiten mit einem bestimmten Skill angezeigt, wenn dieser im **Skilldiagramm** ausgewählt ist. Sie können die Details für die Einstellungen des Skills überprüfen und bearbeiten. Die JSON-Definition des Skills wird bereitgestellt. Die Details für die Ausführung des Skills sowie für die Fehler und Warnungen werden ebenfalls angezeigt. Über die Registerkarte **Skill Settings** (Skilleinstellungen) und **Skill JSON Editor** (Skill-JSON-Editor) können Skills direkt bearbeitet werden. Über [`</>`](#expression-evaluator) wird ein Fenster zum Anzeigen und Bearbeiten der Ausdrücke in den Eingaben und Ausgaben des Skills geöffnet.

Geschachtelte Eingabesteuerelemente im Fenster für die Skilleinstellungen können zum Erstellen von komplexen Formen für Projektionen, Ausgabefeldzuordnungen für Felder vom komplexen Typ oder Eingaben für Skills verwendet werden. Wenn sie mit **Expression evaluator** (Ausdrucksauswertung) verwendet werden, stellen geschachtelte Eingaben einen einfachen Generator zum Testen und Validieren von Ausdrücken dar.

## <a name="skill-execution-history"></a>Ausführungsverlauf für Skills

Ein Skill kann in einem Skillset mehrmals für dasselbe Dokument ausgeführt werden. Beispielsweise wird der OCR-Skill einmal für jedes aus einem Dokument extrahierte Bild ausgeführt. Im Skilldetailbereich wird auf der Registerkarte **Ausführungen** der Ausführungsverlauf des Skills angezeigt, in dem die einzelnen Aufrufe des Skills näher betrachtet werden können. 

Der Ausführungsverlauf ermöglicht das Nachverfolgen einer bestimmten Anreicherung bis zu dem Skill, der sie generiert hat. Durch Klicken auf eine Skilleingabe navigieren Sie zu dem Skill, der diese Eingabe generiert hat. Hierdurch kann die Grundursache für ein Problem ermittelt werden, das möglicherweise erst in einem später ausgeführten Skill auftritt. 

Wenn ein potenzielles Problem identifiziert wurde, werden im Ausführungsverlauf Links zu den Skills angezeigt, die die relevanten Eingaben generiert haben, wodurch ein ähnliches Feature wie die Stapelüberwachung bereitgestellt wird. Durch Klicken auf den einer Eingabe zugeordneten Skill navigieren Sie zu diesem Skill, um etwaige Fehler zu beheben oder das Problem weiter zu verfolgen.

Wenn Sie einen benutzerdefinierten Skill erstellen oder einen bei einem benutzerdefinierten Skill auftretenden Fehler debuggen, können Sie eine Anforderung für den Aufruf des Skills im Ausführungsverlauf generieren.

## <a name="enriched-data-structure"></a>Enriched Data Structure (Angereicherte Datenstruktur)

Im Bereich **Enriched Data Structure** (Angereicherte Datenstruktur) werden Anreicherungen über das Skillset für das Dokument angezeigt, und der Kontext für jede Anreicherung und den zugrunde liegenden Skill wird dort detailliert beschrieben. Mit **Expression evaluator** (Ausdrucksauswertung) kann auch der Inhalt für die einzelnen Anreicherungen angezeigt werden.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-display.png" alt-text="Angereicherte Datenstruktur":::

## <a name="expression-evaluator"></a>Expression evaluator (Ausdrucksauswertung)

**Expression evaluator** (Ausdrucksauswertung) ermöglicht einen kurzen Blick auf den Wert eines Pfads. Hier können Sie vor dem Aktualisieren der Eingaben oder des Kontexts für einen Skill oder eine Projektion den Pfad bearbeiten und die Ergebnisse testen.

## <a name="errorswarnings"></a>Fehler/Warnungen

In diesem Fenster werden alle Fehler und Warnungen angezeigt, die das Skillset auslöst, wenn es für das Dokument in der Debugsitzung ausgeführt wird.

## <a name="limitations"></a>Einschränkungen

Debugsitzungen funktionieren mit allen allgemein verfügbaren Datenquellen und den meisten Vorschaudatenquellen. Die MongoDB-API (Vorschau) von Cosmos DB wird aktuell nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun die Elemente von Debugsitzungen kennen, können Sie das Tutorial absolvieren, um praktische Erfahrungen zu sammeln.

> [!div class="nextstepaction"]
> [Tutorial für das Feature „Debugsitzungen“ erkunden](./cognitive-search-tutorial-debug-sessions.md)
