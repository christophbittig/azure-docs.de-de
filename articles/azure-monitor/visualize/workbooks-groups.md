---
title: Gruppen in Azure Monitor-Arbeitsmappen
description: Erfahren Sie, wie Sie Gruppen in Azure Monitor-Arbeitsmappen verwenden.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 4b3ce70b7648b6fb92c34dfbe166f91054e84ec9
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129261"
---
# <a name="how-to-use-groups-in-workbooks"></a>Verwenden von Gruppen in Arbeitsmappen

Mithilfe eines Gruppenelements in einer Arbeitsmappe können Sie eine Reihe von Schritten in einer Arbeitsmappe logisch gruppieren.

Gruppen in Arbeitsmappen sind für verschiedene Zwecke nützlich:

- Layout
  - In Szenarien, in denen Elemente vertikal angeordnet werden sollen, können Sie eine Gruppe von Elementen erstellen, die alle gestapelt werden, und die Formatierung der Gruppe auf eine prozentuale Breite festlegen, anstatt die prozentuale Breite für jedes einzelne Element festzulegen.
- Sichtbarkeit
  - In Szenarien, in denen viele Elemente gemeinsam aus- bzw. eingeblendet werden sollen, können Sie die Sichtbarkeit der gesamten Gruppe von Elementen festlegen, anstatt die Sichtbarkeitseinstellungen für jedes einzelne Element festzulegen. Dies kann bei Vorlagen nützlich sein, die Registerkarten verwenden, da Sie eine Gruppe als Inhalt der Registerkarte verwenden können und die gesamte Gruppe basierend auf einem Parameter, der von der ausgewählten Registerkarte festgelegt wird, aus- bzw. eingeblendet werden kann.
- Leistung
  - In Fällen, in denen Sie über eine sehr große Vorlage mit vielen Abschnitten oder Registerkarten verfügen, können Sie jeden Abschnitt in eine eigene Untervorlage konvertieren und Gruppen verwenden, um alle Untervorlagen innerhalb der Vorlage der obersten Ebene zu laden. Die Inhalte der Untervorlagen werden erst dann geladen oder ausgeführt, wenn ein Benutzer diese Gruppen sichtbar macht. Weitere Informationen zum Aufteilen einer großen Vorlage in viele Vorlagen finden Sie [hier](#how-to-split-a-large-template-into-many-templates).

## <a name="using-groups"></a>Verwenden von Gruppen

Wenn Sie der Arbeitsmappe eine Gruppe hinzufügen möchten, wählen Sie *Hinzufügen* und dann *Gruppe hinzufügen* aus.

![„Gruppe hinzufügen“ auswählen](./media/workbooks-groups/add-group.png)

Es folgt ein Screenshot einer Gruppe im Lesemodus mit zwei Elementen: einem Textelement und einem Abfrageelement.  

![Gruppe im Lesemodus](./media/workbooks-groups/groups-view.png)

Beim Bearbeiten der Arbeitsmappe können Sie sehen, dass sich diese beiden Elemente tatsächlich innerhalb eines Gruppenelements befinden:

![Gruppe im Bearbeitungsmodus ](./media/workbooks-groups/groups-edit.png)

Im Screenshot oben befindet sich die Gruppe im Bearbeitungsmodus, der anzeigt, dass sie zwei Elemente enthält (innerhalb des gestrichelten Bereichs). Jeder Schritt kann sich unabhängig voneinander im Bearbeitungs- oder Lesemodus befinden. Beispielsweise befindet sich der Textschritt im Bearbeitungsmodus und der Abfrageschritt im Lesemodus.

## <a name="scoping"></a>Bereichsdefinition

Aktuell wird eine Gruppe als neuer Bereich in der Arbeitsmappe gehandhabt. Alle Parameter, die in der Gruppe erstellt werden, sind nur innerhalb der Gruppe sichtbar. Dies gilt auch für die Zusammenführung. Es sind nur Daten innerhalb der Gruppe oder auf der übergeordneten Ebene zu sehen.

## <a name="group-types"></a>Gruppentypen

Mit dem Gruppenelement können Sie einer Arbeitsmappe eine Gruppe von Elementen hinzufügen. Als Autor einer Arbeitsmappe geben Sie an, um welchen Gruppentyp es sich handelt. Es gibt zwei Gruppentypen:

- Bearbeitbar
  - Die Gruppe in der Arbeitsmappe ermöglicht das Hinzufügen, Entfernen oder Bearbeiten des Inhalts der Elemente in der Gruppe. Dies wird am häufigsten für Layout- und Sichtbarkeitszwecke verwendet.
- Aus Vorlage
  - Eine Gruppe in der Arbeitsmappe wird über ihre ID aus den Inhalten einer anderen Arbeitsmappe geladen. Der Inhalt dieser Arbeitsmappe wird zur Laufzeit geladen und mit der Arbeitsmappe zusammengeführt. Sie können den Inhalt der Gruppe im Bearbeitungsmodus nicht ändern, da er beim nächsten Laden des Elements wieder neu aus der Vorlage geladen wird. Verwenden Sie beim Laden einer Gruppe aus einer Vorlage die vollständige Azure-Ressourcen-ID einer vorhandenen Arbeitsmappe.

## <a name="load-types"></a>Ladetypen

Der Inhalt einer Gruppe kann auf verschiedene Arten geladen werden. Als Autor einer Arbeitsmappe können Sie angeben, wann und wie der Inhalt einer Gruppe geladen werden soll.

### <a name="lazy-the-default"></a>Verzögert (Standard)

Die Gruppe wird nur geladen, wenn das Element sichtbar ist. Dadurch kann eine Gruppe von Registerkartenelementen verwendet werden. Wenn die Registerkarte niemals ausgewählt wird, wird auch die Gruppe niemals sichtbar und daher der Inhalt nicht geladen.

Bei Gruppen, die aus einer Vorlage erstellt werden, wird der Inhalt der Vorlage nicht abgerufen, und die Elemente in der Gruppe werden erst erstellt, wenn die Gruppe sichtbar wird. Dem Benutzer werden Fortschrittsdrehfelder für die gesamte Gruppe angezeigt, während der Inhalt abgerufen wird.

### <a name="explicit"></a>Explizit

In diesem Modus wird eine Schaltfläche an der Stelle angezeigt, an der sich die Gruppe befinden würde. Es wird kein Inhalt abgerufen oder erstellt, bis der Benutzer explizit auf die Schaltfläche klickt, um den Inhalt zu laden. Dies ist in Szenarien nützlich, in denen der Inhalt möglicherweise teuer zu berechnen ist oder selten genutzt wird. Der Autor kann den Text angeben, der auf der Schaltfläche angezeigt werden soll.

Nachfolgend sehen Sie einen Screenshot mit Einstellungen für explizites Laden und einer konfigurierten Schaltfläche „Mehr laden“.

![Einstellungen für explizites Laden](./media/workbooks-groups/groups-explicitly-loaded.png)

Die Gruppe vor dem Laden in die Arbeitsmappe:

![Explizite Gruppe vor dem Laden in die Arbeitsmappe](./media/workbooks-groups/groups-explicitly-loaded-before.png)

Die Gruppe nach dem Laden in die Arbeitsmappe:

![Explizite Gruppe nach dem Laden in eine Arbeitsmappe](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Immer

In diesem Modus wird der Inhalt der Gruppe immer geladen und erstellt, sobald die Arbeitsmappe geladen wird. Dies wird am häufigsten verwendet, wenn eine Gruppe nur zu Layoutzwecken genutzt wird, wobei der Inhalt immer sichtbar ist.

## <a name="using-templates-inside-a-group"></a>Verwenden von Vorlagen innerhalb einer Gruppe

Wenn eine Gruppe für das Laden aus einer Vorlage konfiguriert ist, wird dieser Inhalt standardmäßig verzögert geladen. Er wird also nur geladen, wenn die Gruppe sichtbar ist.

Beim Laden einer Vorlage in eine Gruppe versucht die Arbeitsmappe, alle Parameter, die in der zu ladenden Vorlage deklariert sind, mit bereits in der Gruppe vorhandenen Parametern zusammenzuführen. Alle Parameter, die bereits in der Arbeitsmappe mit identischen Namen vorhanden sind, werden aus der zu ladenden Vorlage zusammengeführt. Wenn alle Parameter in einem Parameterschritt zusammengeführt werden, wird der gesamte Parameterschritt ausgeblendet.

### <a name="example-1-all-parameters-have-identical-names"></a>Beispiel 1: Alle Parameter haben identische Namen

Angenommen, eine Vorlage weist zwei Parameter im oberen Bereich auf.

- `TimeRange`: ein Zeitbereichsparameter
- `Filter`: ein Textparameter

![Bearbeiten eines Parameterelements: „Parameter der obersten Ebene“](./media/workbooks-groups/groups-top-level-params.png)

Dann lädt ein Gruppenelement eine zweite Vorlage, die zwei eigene Parameter und einen Textschritt enthält, wobei die Parameter dieselben Namen haben:

![Bearbeiten eines Parameterelements für zweite Vorlage](./media/workbooks-groups/groups-merged-away.png)

Wenn die zweite Vorlage in die Gruppe geladen wird, werden die doppelten Parameter zusammengeführt. Da alle Parameter zusammengeführt werden, wird der innere Parameterschritt ebenfalls zusammengeführt, was dazu führt, dass die Gruppe nur den Textschritt enthält.

### <a name="example-2-one-parameter-has-an-identical-name"></a>Beispiel 2: Ein Parameter hat einen identischen Namen

Angenommen, die Vorlage einer Gruppe weist zwei Parameter im oberen Bereich auf.

- `TimeRange`: ein Zeitbereichsparameter
- `FilterB`: ein Textparameter (hierbei handelt es sich nicht um `Filter` wie bei der Vorlage oben)

![Bearbeiten eines Gruppenelements mit dem Ergebnis, dass Parameter zusammengeführt werden](./media/workbooks-groups/groups-wont-merge-away.png)

Wenn die Vorlage für das Gruppenelement geladen wird, wird der Parameter `TimeRange` aus der Gruppe zusammengeführt. Anschließend weist die Arbeitsmappe den anfänglichen Parameterschritt mit `TimeRange` und `Filter` auf, und der Parameterschritt der Gruppe enthält nur `FilterB`.

![Ergebnis bei Parametern, die nicht zusammengeführt werden](./media/workbooks-groups/groups-wont-merge-away-result.png)

Wenn die geladene Vorlage `TimeRange` und `Filter` enthielte (anstelle von `FilterB`), würde die resultierende Arbeitsmappe einen Parameterschritt und eine Gruppe enthalten, in der nur noch der Textschritt verbleibt.

## <a name="how-to-split-a-large-template-into-many-templates"></a>Aufteilen einer großen Vorlage in viele Vorlagen

Zur Verbesserung der Leistung ist es vorteilhaft, eine große Vorlage in mehrere kleinere Vorlagen zu unterteilen, mit denen Teile des Inhalts verzögert oder nach Bedarf vom Benutzer geladen werden. Dadurch wird das anfängliche Laden schneller ausgeführt, weil die Vorlage der obersten Ebene viel kleiner sein kann.

Wenn Sie eine Vorlage aufteilen, müssen Sie die Vorlage im Endeffekt in viele Vorlagen (Untervorlagen) aufteilen, die alle einzeln funktionieren. Wenn also die Vorlage der obersten Ebene einen Parameter `TimeRange` enthält, der von anderen Schritten verwendet wird, muss auch die Untervorlage über einen Parameterschritt verfügen, der einen Parameter mit dem genauen Namen definiert. Auf diese Weise können die Untervorlagen unabhängig funktionieren und in größeren Vorlagen in Gruppen geladen werden.

Zum Umwandeln einer größeren Vorlage in mehrere Untervorlagen führen Sie die folgenden Schritte aus:

1.  Erstellen Sie eine neue leere Gruppe nahe dem oberen Bereich der Arbeitsmappe nach den gemeinsam genutzten Parametern. Diese neue Gruppe wird letztendlich zu einer Untervorlage.
2. Erstellen Sie eine Kopie des gemeinsam genutzten Parameterschritts, und verwenden Sie dann *In Gruppe verschieben*, um die Kopie in die in Schritt 1 erstellte Gruppe zu verschieben. Durch diesen Parameterschritt kann die Untervorlage unabhängig von der äußeren Vorlage verwendet werden und wird beim Laden in die äußere Vorlage zusammengeführt.
    > [!NOTE]
    > Untervorlagen müssen aus technischer Sicht diese zusammengeführten Parameter nicht enthalten, wenn nicht beabsichtigt ist, dass die Untervorlagen jemals selbst sichtbar sind. Sie werden dadurch jedoch sehr schwer zu bearbeiten oder zu debuggen, sollte dies später erforderlich sein.

3. Verschieben Sie jedes Element in der Arbeitsmappe, das in der Untervorlage enthalten sein soll, in die in Schritt 1 erstellte Gruppe.
4. Wenn die einzelnen Schritte, die in Schritt 3 verschoben wurden, bedingte Sichtbarkeit aufweisen, wird diese zur Sichtbarkeit der äußeren Gruppe (wie bei Registerkarten). Entfernen Sie sie aus den Elementen innerhalb der Gruppe, und fügen Sie diese Sichtbarkeitseinstellung der Gruppe selbst hinzu. Speichern Sie jetzt, um zu verhindern, dass Änderungen verloren gehen, und/oder exportieren und speichern Sie eine Kopie des JSON-Inhalts.
5. Wenn Sie möchten, dass diese Gruppe aus einer Vorlage geladen wird, können Sie die Symbolleistenschaltfläche *Bearbeiten* in der Gruppe verwenden. Dadurch wird nur der Inhalt dieser Gruppe als Arbeitsmappe in einem neuen Fenster geöffnet. Sie können diese dann nach Bedarf speichern und diese Arbeitsmappenansicht schließen (schließen Sie nicht den Browser, sondern nur diese Ansicht, um zur Arbeitsmappe zurückzukehren, die Sie zuvor bearbeitet haben).
6. Anschließend können Sie den Gruppenschritt in das Laden aus einer Vorlage ändern und das Feld für die Vorlagen-ID auf die in Schritt 5 erstellte Arbeitsmappe/Vorlage festlegen. Um mit den IDs von Arbeitsmappen zu arbeiten, muss die Quelle die vollständige Azure Resource ID einer gemeinsamen Arbeitsmappe sein. Klicken Sie auf *Laden*. Der Inhalt dieser Gruppe wird nun aus dieser Untervorlage geladen, anstatt in dieser äußeren Arbeitsmappe gespeichert zu werden.

## <a name="next-steps"></a>Nächste Schritte
- [Übersicht über Arbeitsmappen](./workbooks-overview.md)
- [Verwenden von JSONPath bei Arbeitsmappen](workbooks-jsonpath.md)