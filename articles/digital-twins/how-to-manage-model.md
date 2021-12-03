---
title: Verwalten von DTDL-Modellen
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie DTDL-Modelle in Azure Digital Twins verwalten, einschließlich des Erstellens, Bearbeitens und Löschens.
author: baanders
ms.author: baanders
ms.date: 10/20/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7ae5a3293b7b9ba4712c3762b18b6d9c66eab926
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507168"
---
# <a name="manage-azure-digital-twins-models"></a>Verwalten von Azure Digital Twins-Modellen

In diesem Artikel wird beschrieben, wie Sie die [Modelle](concepts-models.md) in Ihrer Azure Digital Twins-Instanz verwalten. Zu den Verwaltungsvorgängen gehören das Hochladen, Überprüfen, Abrufen und Löschen von Modellen. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="Screenshot von Azure Digital Twins-Explorer mit einem Beispielmodelldiagramm." lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

## <a name="create-models"></a>Erstellen von Modellen

Azure Digital Twins-Modelle werden in DTDL geschrieben und als JSON-Dateien gespeichert. Für [Visual Studio Code](https://code.visualstudio.com/) gibt es auch eine [DTDL-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl), die die Syntaxüberprüfung und andere Features zum Schreiben von DTDL-Dokumenten umfasst.

Angenommen, ein Krankenhaus möchte seine Räume digital überwachen. Jeder Raum enthält einen intelligenten Seifenspender, der das Händewaschen überwacht, sowie Sensoren, die den Durchgangsverkehr im Raum nachverfolgen.

Der erste Schritt zur Lösung besteht darin, Modelle zu erstellen, die verschiedene Aspekte des Krankenhauses darstellen. In diesem Szenario kann ein Patientenzimmer wie folgt beschrieben werden:

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> Dies ist ein Beispieltext für eine JSON-Datei, in der ein Modell definiert und gespeichert wird, das als Teil eines Clientprojekts hochgeladen werden soll. Der REST-API-Aufruf hingegen verwendet ein Array mit Modelldefinitionen wie die obige (die einer `IEnumerable<string>` im .NET SDK zugeordnet ist). Damit Sie dieses Modell direkt in der REST-API verwenden können, sollten Sie es in Klammern setzen.

Dieses Modell definiert einen Namen und eine eindeutige ID für das Patientenzimmer sowie Eigenschaften zur Darstellung der Anzahl der Besucher und zum Status des Händewaschens. Diese Werte werden von Bewegungssensoren und intelligenten Seifenspendern aktualisiert und gemeinsam verwendet, um eine Eigenschaft für das *Händewaschen (in Prozent)* zu berechnen. Das Modell definiert außerdem eine *hasDevices*-Beziehung, die verwendet wird, um einen [digitalen Zwilling](concepts-twins-graph.md) basierend auf dem Raummodell mit den tatsächlichen Geräten zu verbinden.

Mit dieser Methode können Sie Modelle für die Stationen oder Bereiche des Krankenhauses oder sogar das gesamte Krankenhaus definieren.

### <a name="validate-syntax"></a>Überprüfen der Syntax

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>Hochladen von Modellen

Nachdem Sie Modelle erstellt haben, können Sie sie in die Azure Digital Twins-Instanz hochladen.

Wenn Sie zum Hochladen eines Modells bereit sind, können Sie den folgenden Codeausschnitt für das [.NET SDK](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true) verwenden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Sie können auch mehrere Modelle in einer einzelnen Transaktion hochladen. 

Wenn Sie das SDK verwenden, können Sie mehrere Modelldateien mit der `CreateModels`-Methode wie folgt hochladen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

Wenn Sie die [REST-APIs](/rest/api/azure-digitaltwins/) oder die [Azure CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)verwenden, können Sie auch mehrere Modelle hochladen, indem Sie mehrere Modelldefinitionen, die zusammen hochgeladen werden sollen, in einer einzelnen JSON-Datei platzieren. In diesem Fall sollten die Modelle wie im folgenden Beispiel in einem JSON-Array innerhalb der Datei platziert werden:

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

Modelldateien werden beim Hochladen vom Dienst überprüft.

## <a name="retrieve-models"></a>Abrufen von Modellen

Sie können Modelle auflisten und abrufen, die auf Ihrer Azure Digital Twins-Instanz gespeichert sind. 

Optionen
* Ein einzelnes Modell abrufen
* Alle Modelle abrufen
* Abrufen von Metadaten und Abhängigkeiten für Modelle

Hier sehen Sie ein paar Beispielaufrufe:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Die SDK-Aufrufe zum Abrufen von Modellen geben alle `DigitalTwinsModelData`-Objekte zurück. `DigitalTwinsModelData` enthält Metadaten zum Modell, das in der Azure Digital Twins-Instanz gespeichert ist, z. B. Name, DTMI und Erstellungsdatum des Modells. Das Objekt `DigitalTwinsModelData` kann auch das Modell selbst enthalten. Das heißt: Je nach Parametern können Sie die Abrufaufrufe verwenden, um entweder nur Metadaten (was z. B. nützlich ist, wenn Sie eine Liste verfügbarer Tools für die Benutzeroberfläche anzeigen möchten) oder das gesamte Modell abzurufen.

Der Aufruf `RetrieveModelWithDependencies` gibt nicht nur das angeforderte Modell zurück, sondern auch alle Modelle, von denen das angeforderte Modell abhängig ist.

Modelle werden nicht unbedingt genau in dem Dokumentformat zurückgegeben, in dem sie hochgeladen wurden. Azure Digital Twins gewährleistet nur, dass das Rückgabeformat semantisch gleichwertig ist. 

## <a name="update-models"></a>Aktualisieren von Modellen

In diesem Abschnitt werden Überlegungen und Strategien zum Aktualisieren von Modellen beschrieben.

### <a name="before-updating-think-in-the-context-of-your-entire-solution"></a>Vor dem Aktualisieren: Berücksichtigung des Kontexts der gesamten Lösung

Bevor Sie Modelle aktualisieren, sollten Sie in ganzheitlicher Weise über die gesamte Lösung und die Auswirkungen der Modelländerungen nachdenken, die Sie vornehmen werden. Modelle in einer Azure Digital Twins-Lösung sind häufig miteinander verbunden. Daher ist es wichtig, sich kaskadierende Änderungen bewusst zu machen, bei denen die Aktualisierung eines Modells das Aktualisieren mehrerer anderer erfordert. Das Aktualisieren von Modellen wirkt sich auf die Zwillinge aus, die die Modelle verwenden, und kann sich auch auf den Eingangs- und Verarbeitungscode, Clientanwendungen und automatisierte Berichte auswirken.

Im Folgenden finden Sie einige Empfehlungen, mit deren Hilfe Sie Modellübergänge reibungslos verwalten können:
* Statt sich Modelle als getrennte Entitäten vorzustellen, sollten Sie erwägen, bei Bedarf den gesamten Modellsatz weiterzuentwickeln, um bei Bedarf Modelle und ihre Beziehungen gemeinsam auf dem aktuellen Stand zu halten.
* Behandeln Sie Modelle wie Quellcode, und verwalten Sie sie in der Quellcodeverwaltung. Behandeln Sie Modelle und Modelländerungen mit derselben Strenge und Aufmerksamkeit wie anderen Code in der Lösung.

Wenn Sie bereit sind, mit dem Aktualisieren der Modelle fortzufahren, lesen Sie den restlichen Teil dieses Abschnitts, in dem die Strategien beschrieben werden, die Sie zum Implementieren der Aktualisierungen verwenden können.

### <a name="strategies-for-updating-models"></a>Strategien zum Aktualisieren von Modellen

Sobald ein Modell in die Azure Digital Twins-Instanz hochgeladen wurde, ist die Modellschnittstelle unveränderlich. Dies bedeutet, dass keine herkömmliche „Bearbeitung“ von Modellen möglich ist. Azure Digital Twins lässt auch kein erneutes Hochladen desselben Modells zu, wenn in der Instanz bereits ein übereinstimmendes Modell vorhanden ist.

Stattdessen müssen Sie das ursprüngliche Modell ersetzen, wenn Sie Änderungen an einem Modell vornehmen möchten, z. B. `displayName` oder `description` aktualisieren oder Eigenschaften hinzufügen und entfernen.

Beim Ersetzen eines Modells stehen zwei Strategien zur Auswahl:
* [Strategie 1: Hochladen einer neuen Modellversion](#strategy-1-upload-new-model-version): Laden Sie das Modell mit einer neuen Versionsnummer hoch, und aktualisieren Sie die Zwillinge, um dieses neue Modell zu verwenden. Sowohl die neue als auch die alte Version des Modells sind in der Instanz vorhanden, bis Sie eine Version löschen.
    - **Verwenden Sie diese Strategie, wenn** Sie nur einige der Zwillinge aktualisieren möchten, die das Modell verwenden, oder wenn Sie sicherstellen möchten, dass Zwillinge ihren Modellen entsprechen und während des Modellübergangs beschreibbar sind.
* [Strategie 2: Löschen des alten Modells und erneutes Hochladen](#strategy-2-delete-old-model-and-reupload): Löschen Sie das ursprüngliche Modell, und laden Sie das neue Modell mit demselben Namen und derselben ID (DTMI-Wert) hoch. Dadurch wird das alte Modell vollständig durch das neue ersetzt. 
    - **Verwenden Sie diese Strategie, wenn** Sie alle Zwillinge aktualisieren möchten, die dieses Modell gleichzeitig verwenden, sowie den ganzen Code, der auf die Modelle reagiert. Wenn die Modellaktualisierung eine Breaking Change enthält, stimmen Zwillinge während des Übergangs vom alten zum neuen Modell eine kurze Zeit lang nicht mit ihren Modellen überein. Dies bedeutet, dass sie erst wieder aktualisiert werden können, wenn das neue Modell hochgeladen wurde und die Zwillinge dem Modell entsprechen.

>[!NOTE]
> Von Breaking Changes an Modellen außerhalb der Entwicklung wird abgeraten.

In den nächsten Abschnitten werden die einzelnen Strategieoption detaillierter erläutert.

### <a name="strategy-1-upload-new-model-version"></a>Strategie 1: Hochladen einer neuen Modellversion

Diese Option umfasst das Erstellen einer neuen Version des Modells und das Hochladen in die Instanz.

Durch diesen Vorgang werden frühere Versionen des Modells **nicht** überschrieben, sodass mehrere Versionen des Modells in der Instanz gleichzeitig vorhanden sind, bis Sie sie [entfernen](#remove-models). Da die neue und die alte Modellversion nebeneinander vorhanden sind, können Zwillinge entweder die neue Version des Modells oder die ältere Version verwenden. Dies bedeutet, dass das Hochladen einer neuen Version eines Modells nicht automatisch Auswirkungen auf vorhandene Zwillinge hat. Die vorhandenen Zwillinge bleiben als Instanzen der alten Modellversion erhalten, und Sie können diese Zwillinge auf die neue Modellversion aktualisieren, indem Sie sie patchen.

Zur Verwendung dieser Strategie führen Sie die folgenden Schritte aus:

#### <a name="1-create-and-upload-new-model-version"></a>1. Erstellen und Hochladen einer neuen Modellversion 

Um eine neue Version eines bestehenden Modells zu erstellen, beginnen Sie mit der DTDL des ursprünglichen Modells. Sie können die Felder, die Sie ändern möchten, aktualisieren, hinzufügen oder entfernen.

Markieren Sie dieses Modell dann als neuere Version des Modells, indem Sie das `id`-Feld des Modells aktualisieren. Der letzte Abschnitt der Modell-ID, nach dem `;`, stellt die Modellnummer dar. Damit Sie anzeigen können, dass es sich jetzt um eine aktualisierte Version dieses Modells handelt, erhöhen Sie die Zahl am Ende des `id`-Werts auf eine beliebige Zahl, die größer als die aktuelle Versionsnummer ist.

Wenn Ihre frühere Modell-ID z. B. so aussah:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

Version 2 dieses Modells könnte wie folgt aussehen:

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

Dann können Sie die neue Version des Modells in die Instanz [hochladen](#upload-models). 

Diese Version des Modells wird dann in Ihrer Instanz für digitale Zwillinge zur Verfügung stehen. Frühere Versionen des Modells werden dabei **nicht** überschrieben, sodass in der Instanz jetzt mehrere Versionen des Modells gleichzeitig vorhanden sind.

#### <a name="2-update-graph-elements-as-needed"></a>2. Aktualisieren von Graphelementen nach Bedarf

Aktualisieren Sie als Nächstes die **Zwillinge und Beziehungen** in der Instanz, um die neue Modellversion anstelle der alten zu verwenden.

Zum [Aktualisieren der Zwillinge](how-to-manage-twin.md#update-a-digital-twins-model) und [Aktualisieren der Beziehungen](how-to-manage-graph.md#update-relationships) können Sie die folgenden Anweisungen verwenden. Der Patchvorgang zum Aktualisieren des Modells eines Zwillings sieht in etwa wie folgt aus:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

>[!IMPORTANT]
>Verwenden Sie beim Aktualisieren von Zwillingen **denselben Patch**, um sowohl die Modell-ID (auf die neue Modellversion) und alle Felder zu aktualisieren, die im Zwilling geändert werden müssen, damit sie mit dem neuen Modell übereinstimmen.

Möglicherweise müssen Sie auch **Beziehungen** und andere **Modelle** in der Instanz aktualisieren, die auf dieses Modell verweisen, damit sie auf die neue Modellversion verweisen. Zu diesem Zweck müssen Sie einen weiteren Modellaktualisierungsvorgang durchführen. Kehren Sie also zum Anfang dieses Abschnitts zurück, und wiederholen Sie den Vorgang für alle weiteren Modelle, die aktualisiert werden müssen.

#### <a name="3-optional-decommission-or-delete-old-model-version"></a>3. (Optional) Außerbetriebnahme oder Löschung der alten Modellversion

Wenn Sie die alte Modellversion nicht mehr verwenden, können Sie das ältere Modell [außer Betrieb nehmen](#decommissioning). Mithilfe dieser Aktion kann das Modell in der Instanz bestehen bleiben, aber nicht zum Erstellen neuer digitaler Zwillinge verwendet werden.

Sie können das alte Modell auch vollständig [löschen](#deletion), wenn Sie es in der Instanz gar nicht mehr benötigen.

Die oben verlinkten Abschnitte enthalten Beispielcode und Überlegungen zum Außerbetriebnehmen und Löschen von Modellen.

### <a name="strategy-2-delete-old-model-and-reupload"></a>Strategie 2: Löschen des alten Modells und erneutes Hochladen

Anstatt die Version eines Modells schrittweise zu erhöhen, können Sie ein Modell vollständig löschen und ein bearbeitetes Modell erneut in die Instanz hochladen.

In Azure Digital Twins ist kein Hinweis mehr darauf vorhanden, dass das alte Modell jemals hochgeladen wurde. Somit gleicht diese Aktion dem Hochladen eines völlig neuen Modells. Zwillinge im Graphen, die das Modell verwenden, wechseln automatisch zur neuen Definition, sobald sie verfügbar ist. Je nachdem, wie sich die neue Definition von der alten unterscheidet, können diese Zwillinge Eigenschaften und Beziehungen aufweisen, die mit der gelöschten Definition übereinstimmen und für die neue Definition nicht gültig sind. Daher müssen Sie sie möglicherweise patchen, um sicherzustellen, dass sie gültig bleiben.

Zur Verwendung dieser Strategie führen Sie die folgenden Schritte aus:

### <a name="1-delete-old-model"></a>1. Löschen des alten Modells

Da Azure Digital Twins zwei Modelle mit derselben ID nicht zulässt, löschen Sie zunächst das ursprüngliche Modell aus der Instanz. 

>[!NOTE]
> Wenn Sie über andere Modelle verfügen, die von diesem Modell abhängen (durch Vererbung oder Komponenten), müssen Sie diese Verweise entfernen, bevor Sie das Modell löschen können. Sie können diese abhängigen Modelle zuerst aktualisieren, um die Verweise vorübergehend zu entfernen, oder die abhängigen Modelle löschen und in einem späteren Schritt erneut hochladen.

Gehen Sie folgendermaßen vor, um [das ursprüngliche Modell zu löschen](#deletion). Durch diese Aktion bleiben die Zwillinge, die dieses Modell verwendet haben, vorübergehend „verwaist“, da sie jetzt ein Modell verwenden, das nicht mehr vorhanden ist. Dieser Zustand wird im nächsten Schritt repariert, wenn Sie das aktualisierte Modell erneut hochladen.

### <a name="2-create-and-upload-new-model"></a>2. Erstellen und Hochladen des neuen Modells

Beginnen Sie mit der DTDL des ursprünglichen Modells. Sie können die Felder, die Sie ändern möchten, aktualisieren, hinzufügen oder entfernen.

[Laden Sie dann das Modell in die Instanz hoch](#upload-models), als wäre es ein neues Modell, das zum ersten Mal hochgeladen wird.

### <a name="3-update-graph-elements-as-needed"></a>3. Aktualisieren von Graphelementen nach Bedarf

Nachdem das neue Modell nun statt des alten Modells hochgeladen wurde, beginnen die Zwillinge im Graph automatisch mit der Verwendung der neuen Modelldefinition, sobald die Zwischenspeicherung in der Instanz abläuft und zurückgesetzt wird. **Dieser Vorgang kann je nach Graphgröße 10 bis 15 Minuten oder länger dauern**. Danach sollte auf neue und geänderte Eigenschaften im Modell zugegriffen werden können; auf entfernte Eigenschaften kann nicht mehr zugegriffen werden.

>[!NOTE]
> Wenn Sie zuvor andere abhängige Modelle entfernt haben, um das ursprüngliche Modell zu löschen, laden Sie diese jetzt erneut hoch, nachdem der Cache zurückgesetzt wurde. Wenn Sie die abhängigen Modelle aktualisiert haben, um vorübergehend Verweise auf das ursprüngliche Modell zu entfernen, können Sie sie erneut aktualisieren, um den Verweis wiederherzustellen.

Aktualisieren Sie als Nächstes die **Zwillinge und Beziehungen** in der Instanz, damit ihre Eigenschaften mit den vom neuen Modell definierten Eigenschaften übereinstimmen. Bevor dieser Schritt abgeschlossen ist, können die Zwillinge, die nicht mit ihrem Modell übereinstimmen, weiterhin gelesen, aber nicht beschrieben werden. Weitere Informationen zum Zustand von Zwillingen ohne gültiges Modell finden Sie unter [Zwillinge ohne Modelle](#after-deletion-twins-without-models).

Es gibt zwei Möglichkeiten, Zwillinge und Beziehungen für das neue Modell zu aktualisieren, damit sie wieder beschreibbar sind:
* Patchen Sie die Zwillinge und Beziehungen nach Bedarf, damit sie dem neuen Modell entsprechen. Zum [Aktualisieren der Zwillinge](how-to-manage-twin.md#update-a-digital-twin) und [Aktualisieren der Beziehungen](how-to-manage-graph.md#update-relationships) können Sie die folgenden Anweisungen verwenden.
    - **Wenn Sie Eigenschaften hinzugefügt haben**: Das Aktualisieren von Zwillingen und Beziehungen auf die neuen Werte ist nicht erforderlich, da Zwillinge, bei denen die neuen Werte fehlen, weiterhin gültige Zwillinge sind. Sie können sie jedoch patchen, wenn Sie Werte für die neuen Eigenschaften hinzufügen möchten.
    - **Wenn Sie Eigenschaften entfernt haben**: Sie müssen Zwillinge patchen, um die Eigenschaften zu entfernen, die jetzt mit dem neuen Modell ungültig sind.
    - **Wenn Sie Eigenschaften aktualisiert haben**: Sie müssen Zwillinge patchen, um die Werte von geänderten Eigenschaften zu aktualisieren, damit sie mit dem neuen Modell gültig sind.
* Löschen Sie Zwillinge und Beziehungen, von denen das Modell verwendet wird, und erstellen Sie sie neu. Sie können die folgenden Anweisungen dazu verwenden, [Zwillinge zu löschen](how-to-manage-twin.md#delete-a-digital-twin) und [Zwillinge erneut zu erstellen](how-to-manage-twin.md#create-a-digital-twin) und [Beziehungen zu löschen](how-to-manage-graph.md#delete-relationships) und [Beziehungen erneut zu erstellen](how-to-manage-graph.md#create-relationships),
    - Die Durchführung dieses Vorgangs empfiehlt sich, wenn Sie viele Änderungen am Modell vornehmen und es schwierig sein wird, die vorhandenen Zwillinge so zu aktualisieren, dass sie mit ihm übereinstimmen. Die erneute Erstellung kann jedoch kompliziert sein, wenn Sie über viele Zwillinge verfügen, die durch viele Beziehungen miteinander verbunden sind.

## <a name="remove-models"></a>Entfernen von Modellen

Sie haben zwei Möglichkeiten, um Modelle aus dem Dienst zu entfernen:
* **Außerbetriebsetzung:** Wenn ein Modell außer Betrieb gesetzt wurde, können Sie es nicht mehr zum Erstellen neuer digitaler Zwillinge verwenden. Vorhandene digitale Zwillinge, die dieses Modell bereits verwenden, sind nicht betroffen. Deshalb können Sie sie weiterhin aktualisieren, indem Sie z. B. Eigenschaften ändern und Beziehungen hinzufügen oder löschen.
* **Löschen**: Durch diesen Vorgang wird das Modell vollständig aus der Lösung entfernt. Alle Zwillinge, die dieses Modell verwenden, sind keinem gültigen Modell mehr zugeordnet, weshalb sie so behandelt werden, als würden sie über gar kein Modell verfügen. Sie können diese Zwillinge zwar weiterhin lesen, aber Sie können erst wieder Updates an ihnen vornehmen, wenn sie einem anderen Modell zugewiesen werden.

Bei diesen Vorgängen handelt es sich um separate Features, die einander nicht beeinträchtigen. Sie können aber zusammen dazu verwendet werden, ein Modell schrittweise zu entfernen. 

### <a name="decommissioning"></a>Außerbetriebsetzung

Sie können die [DecommissionModel](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.decommissionmodel?view=azure-dotnet&preserve-view=true)-Methode aus dem SDK verwenden, um ein Modell außer Betrieb zu setzen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

Sie können ein Modell auch außer Betrieb setzen, indem Sie den REST-API-Aufruf [DigitalTwinModels Update](/rest/api/digital-twins/dataplane/models/digitaltwinmodels_update) verwenden. Die `decommissioned`-Eigenschaft ist die einzige Eigenschaft, die durch diesen API-Aufruf ersetzt werden kann. Das JSON-Patch-Dokument sieht in etwa wie folgt aus:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-decommission-model.json":::

Der Status „Außerbetriebsetzung“ eines Modells ist in den `ModelData`-Datensätzen enthalten, die von den APIs zum Abrufen von Modellen zurückgegeben werden.

### <a name="deletion"></a>Löschen

Sie können entweder alle Modelle auf einmal aus der Instanz löschen oder einzeln bestimmte Modelle zum Löschen auswählen.

Ein Beispiel für das gleichzeitige Löschen aller Modelle finden Sie im GitHub-Repository [End-to-End-Beispiele für Azure Digital Twins](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/CommandLoop.cs). Die Datei *CommandLoop.cs* enthält eine `CommandDeleteAllModels`-Funktion mit Code zum Löschen aller Modelle in der Instanz.

Orientieren Sie sich zum Löschen eines einzelnen Modells an den Anweisungen und Überlegungen aus dem übrigen Teil dieses Abschnitts.

#### <a name="before-deletion-deletion-requirements"></a>Vor dem Löschen: Löschanforderungen

Generell können Modelle jederzeit gelöscht werden.

Allerdings stellen Modelle, von denen andere Modelle abhängig sind (durch eine `extends`-Beziehung oder weil es sich um eine Komponente handelt), eine Ausnahme dar. Wenn beispielsweise ein ConferenceRoom-Modell ein Room-Modell erweitert und über ein ACUnit-Modell als Komponente verfügt, können Sie die Modelle Room und ACUnit erst löschen, wenn die jeweiligen Verweise für das Modell ConferenceRoom entfernt werden. 

Hierzu können Sie das abhängige Modell aktualisieren, um die Abhängigkeiten zu entfernen, oder das abhängige Modell vollständig löschen.

#### <a name="during-deletion-deletion-process"></a>Beim Löschen: Löschvorgang

Selbst wenn ein Modell die Anforderungen zum sofortigen Löschen erfüllt, sollten Sie zunächst einige Maßnahmen ergreifen, um unbeabsichtigte Folgen für die übrigen Zwillinge zu vermeiden. Im Folgenden wird erläutert, wie Sie den Prozess verwalten können:
1. Setzen Sie zunächst das Modell außer Betrieb.
2. Warten Sie einige Minuten, um sicherzustellen, dass der Dienst alle in letzter Minute gesendeten Erstellungsanforderungen verarbeitet hat, die vor der Außerbetriebnahme gesendet wurden.
3. Fragen Sie die Zwillinge des Modells ab, um alle Zwillinge zu finden, die das außer Betrieb genommene Modell verwenden.
4. Löschen Sie die Zwillinge, wenn Sie sie nicht mehr benötigen, oder patchen Sie sie bei Bedarf an ein neues Modell. Sie können die Zwillinge auch unbeachtet lassen. Dann werden sie zu Zwillingen ohne Modell, sobald das Modell gelöscht wird. Weitere Informationen zu den Auswirkungen dieses Zustands finden Sie im nächsten Abschnitt.
5. Warten Sie einige Minuten, um sicherzustellen, dass die Änderungen übernommen wurden.
6. Löschen Sie das Modell. 

Um ein Modell zu löschen, können Sie den SDK-Aufruf [DeleteModel](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.deletemodel?view=azure-dotnet&preserve-view=true) verwenden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

Sie können ein Modell auch mit dem Rest-API-Aufruf [DigitalTwinModels Delete](/rest/api/digital-twins/dataplane/models/digitaltwinmodels_delete) löschen.

#### <a name="after-deletion-twins-without-models"></a>Nach dem Löschen: Zwillinge ohne Modelle

Nach dem Löschen eines Modells werden sämtliche digitale Zwillinge, die das Modell verwendet haben, als Zwillinge ohne Modell betrachtet. Es gibt zwar keine Abfrage, die Sie verwenden können, um eine Liste aller Zwillinge in diesem Zustand zu erhalten, aber Sie *können* die Zwillinge des jeweiligen gelöschten Modells abfragen, um herauszufinden, welche Zwillinge betroffen sind.

Im Folgenden finden Sie eine Übersicht darüber, wozu Zwillinge ohne Modell (nicht) verwendet werden können.

**Möglich:**
* Den Zwilling abfragen
* Lesen von Eigenschaften
* Ausgehende Beziehungen lesen
* Eingehende Beziehungen hinzufügen oder löschen (z. B. können andere Zwillinge weiterhin Beziehungen *zu* diesem Zwilling herstellen)
  - Das `target` in der Beziehungsdefinition kann weiterhin den DTMI des gelöschten Modells widerspiegeln. Auch eine Beziehung ohne festgelegtes Ziel kann hier funktionieren.      
* Löschen von Beziehungen
* Den Zwilling löschen

**Nicht möglich:**
* Ausgehende Beziehungen bearbeiten (z. B. Beziehungen dieses Zwillings *zu* anderen Zwillingen)
* Eigenschaften bearbeiten

#### <a name="after-deletion-reuploading-a-model"></a>Nach dem Löschen: Erneutes Hochladen eines Modells

Wenn Sie ein Modell löschen, können Sie später ein neues Modell mit derselben ID hochladen. Dann passiert Folgendes:
* Aus Sicht des Lösungsspeichers entspricht dieser Vorgang dem Hochladen eines komplett neuen Modells. Er erinnert sich nicht daran, dass das alte Modell hochgeladen wurde.   
* Wenn der Graph übrig gebliebene Zwillinge enthält, die auf das gelöschte Modell verweisen, werden diese nicht mehr als verwaist angesehen. Die Modell-ID ist dann einschließlich einer neuen Definition wieder gültig. Wenn sich die neue Definition für das Modell jedoch von der gelöschten Modelldefinition unterscheidet, weisen diese Zwillinge möglicherweise Eigenschaften und Beziehungen auf, die der gelöschten Definition entsprechen und mit der neuen nicht gültig sind.

In Azure Digital Twins wird dieser Zustand nicht verhindert. Deshalb sollten Sie darauf achten, Zwillinge ordnungsgemäß zu patchen, damit sie durch den Wechsel der Modelldefinition nicht ihre Gültigkeit verlieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen und Verwalten von digitalen Zwillingen basierend auf Ihren Modellen finden Sie unter
* [Verwalten digitaler Zwillinge](how-to-manage-twin.md)