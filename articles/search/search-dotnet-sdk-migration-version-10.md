---
title: Upgrade auf .NET SDK-Version 10
titleSuffix: Azure Cognitive Search
description: Migrieren Sie Code von älteren Versionen zum Azure Cognitive Search .NET SDK Version 10. Hier finden Sie Informationen zu Neuheiten und erfahren, welche Änderungen am Code erforderlich sind.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ff3398d326de07ea97b5e854efab1478ef4bad4e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665015"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Upgrade auf Version 10 des Azure Cognitive Search .NET SDK

Wenn Sie die Version 9.0 oder eine frühere Version des [.NET SDK](/dotnet/api/overview/azure/search) verwenden, unterstützt dieser Artikel Sie beim Upgrade Ihrer Anwendung auf Version 10.

Azure Search wurde in Version 10 in Azure Cognitive Search umbenannt, aber Namespaces und Paketnamen sind unverändert. Frühere Versionen des SDK (9.0 und früher) verwenden weiterhin den früheren Namen. Weitere Informationen zur Verwendung des SDK und Beispiele finden Sie unter [Verwenden von Azure Cognitive Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md).

In Version 10 wurden mehrere Features und Fehlerbehebungen hinzugefügt. Dadurch wurde die Version auf dieselbe funktionale Ebene wie die REST-API-Version `2019-05-06` gebracht. In Fällen, in denen eine Änderung vorhandenen Code unterbricht, werden Sie durch die [Schritte geführt, die zum Beheben des Problems erforderlich sind](#UpgradeSteps).

> [!NOTE]
> Wenn Sie Version 8.0-preview oder eine frühere Version verwenden, sollten Sie zuerst ein Upgrade auf Version 9 und anschließend auf Version 10 durchführen. Anweisungen finden Sie unter [Upgrade auf Version 9 des Azure Search .NET SDK](search-dotnet-sdk-migration-version-9.md).
>
> Ihre Suchdienstinstanz unterstützt mehrere REST-API-Versionen, einschließlich der neuesten. Sie können auch ältere Versionen weiterhin verwenden, aber es wird empfohlen, den Code zur neuesten Version zu migrieren. Wenn Sie die REST-API verwenden, müssen Sie die API-Version bei jeder Anforderung über den api-version-Parameter angeben. Wenn Sie das .NET SDK verwenden, legt die Version des verwendeten SDK die Version der REST-API fest. Wenn Sie ein älteres SDK verwenden, können Sie diesen Code weiterhin ohne Änderungen ausführen, selbst wenn der Dienst aktualisiert wird, um eine neuere Version der API zu unterstützen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Neuerungen in Version 10
Version 10 des Azure Cognitive Search .NET SDK ist für die REST-API `2019-05-06` mit diesen Updates konzipiert:

* Die Einführung zwei neuer Qualifikationen: [Qualifikation „Bedingt“](cognitive-search-skill-conditional.md) und [Qualifikation „Textübersetzung“](cognitive-search-skill-text-translation.md).
* [Qualifikation „Shaper“](cognitive-search-skill-shaper.md)-Eingaben wurden neu strukturiert, um Konsolidierung aus geschachtelten Kontexten zu ermöglichen. Weitere Informationen finden Sie in dieser [JSON-Beispieldefinition](./cognitive-search-skill-shaper.md#scenario-3-input-consolidation-from-nested-contexts).
* Das Hinzufügen von zwei neuen [Feldzuordnungsfunktionen](search-indexer-field-mappings.md):
    - [urlEncode](./search-indexer-field-mappings.md#urlencode-function)
    - [urlDecode](./search-indexer-field-mappings.md#urldecode-function)
* In bestimmten Fällen können Fehler und Warnungen, die im [Status der Indexer-Ausführung](/rest/api/searchservice/get-indexer-status) angezeigt werden, zusätzliche Details enthalten, die beim Debuggen nützlich sind. `IndexerExecutionResult` wurde entsprechend diesem Verhalten aktualisiert.
* Einzelne Qualifikationen, die in einem [Skillset](cognitive-search-defining-skillset.md) definiert sind, können optional gekennzeichnet werden, indem eine `name`-Eigenschaft angegeben wird.
* `ServiceLimits` zeigt Grenzwerte für [komplexe Typen](./search-howto-complex-data-types.md) und `IndexerExecutionInfo` zeigt relevante Indexergrenzwerte/-kontingente an.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Schritte zum Upgrade

1. Aktualisieren Sie die NuGet-Referenz für `Microsoft.Azure.Search`, indem Sie entweder die NuGet-Paket-Manager-Konsole verwenden oder mit der rechten Maustaste auf die Projektverweise klicken und in Visual Studio „NuGet-Pakete verwalten...“ auswählen.

2. Nachdem NuGet die neuen Pakete und deren Abhängigkeiten heruntergeladen hat, erstellen Sie Ihr Projekt neu. 

3. Wenn Ihr Build zu einem Fehler führt, müssen Sie sämtliche Buildfehler beheben. Einzelheiten zum Beheben potenzieller Buildfehler finden Sie unter [Breaking Changes in Version 10](#ListOfChanges).

4. Sobald Sie alle Buildfehler und -warnungen behoben haben, können Sie Änderungen an Ihrer Anwendung vornehmen, um die neue Funktionalität zu nutzen. Neue Features im SDK werden unter [Neuerungen in Version 10](#WhatsNew) ausführlich behandelt.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Breaking Changes in Version 10

Version 10 enthält einige Breaking Changes, die neben der Neuerstellung der Anwendung ggf. auch Codeänderungen erfordern.

> [!NOTE]
> Die unten stehende Liste von Änderungen ist nicht vollständig. Einige Änderungen führen wahrscheinlich nicht zu Buildfehlern, sie sind aber technisch gesehen „breaking“, da sie die binäre Kompatibilität mit Assemblys verletzen, die von früheren Versionen der Azure Cognitive Search .NET SDK-Assemblys abhängen. Wichtige Änderungen, die in diese Kategorie fallen, werden ebenfalls zusammen mit den Empfehlungen aufgeführt. Erstellen Sie Ihre Anwendung neu, wenn Sie auf Version 10 upgraden, um binäre Kompatibilitätsprobleme zu vermeiden.

### <a name="custom-web-api-skill-definition"></a>Definition der Qualifikation „Benutzerdefinierte Web-API“

Die Definition der [Qualifikation „Benutzerdefinierte Web-API“](cognitive-search-custom-skill-web-api.md) ist in Version 9 und älter fehlerhaft angegeben. 

Im Modell `WebApiSkill` ist `HttpHeaders` als eine Objekteigenschaft angegeben, die ein Wörterbuch _enthält_. Ein Erstellen eines Skillsets mit einer `WebApiSkill`-Instanz, die auf diese Weise erstellt wurde, führt zu einer Ausnahme, weil die REST-API die Anforderung als fehlerhaft formatiert erachtet. Dieses Problem wurde behoben, indem `HttpHeaders` zu einer **Wörterbucheigenschaft der obersten Ebene** für das `WebApiSkill`-Modell selbst gemacht wurde. Dies wiederum wird von der REST-API als gültige Anforderung angesehen.

Wenn Sie zuvor beispielsweise versucht haben, ein `WebApiSkill`-Modell wie folgt zu instanziieren:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

ändern Sie den Code wie folgt, um den Validierungsfehler von der REST-API zu vermeiden:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Qualifikation „Shaper“ ermöglicht die Konsolidierung von geschachteltem Kontext

Die Qualifikation „Shaper“ kann nun die Konsolidierung von geschachtelten Kontexten unterstützen. Um diese Änderung zu ermöglichen, wurde die `InputFieldMappingEntry`-Klasse so geändert, dass sie instanziiert werden kann, indem nur eine `Source`-Eigenschaft oder sowohl die `SourceContext`-Eigenschaft als auch die `Inputs`-Eigenschaft angegeben werden.

Sie müssen höchstwahrscheinlich keinerlei Änderungen an Code vornehmen. Beachten Sie jedoch, dass nur eine dieser beiden Kombinationen zulässig ist. Dies bedeutet Folgendes:

- Das Erstellen einer `InputFieldMappingEntry`-Instanz, wobei nur `Source` initialisiert wird, ist zulässig.
- Das Erstellen einer `InputFieldMappingEntry`-Instanz, wobei nur `SourceContext` und `Inputs` initialisiert werden, ist zulässig.
- Alle anderen Kombinationen, die diese drei Eigenschaften betreffen, sind unzulässig.

Wenn Sie sich entscheiden, diese neue Funktion zu nutzen, müssen Sie alle betroffenen Clients zuerst auf die Verwendung von Version 10 aktualisieren, bevor Sie diese Änderung bereitstellen. Andernfalls besteht die Möglichkeit, dass ein Update, das ein Client (mit einer älteren Version des SDK) an der Qualifikation „Shaper“ vornimmt, zu Validierungsfehlern führt.

> [!NOTE]
> Obwohl das zugrunde liegende `InputFieldMappingEntry`-Modell so geändert wurde, dass es eine Konsolidierung aus geschachtelten Kontexten zulässt, kann es nur innerhalb der Definition einer „Shaper“-Qualifikation verwendet werden. Eine Verwendung dieser Funktion in anderen Qualifikationen, was zur Kompilierzeit zulässig ist, führt zur Laufzeit zu einem Validierungsfehler.

## <a name="skills-can-be-identified-by-a-name"></a>Qualifikationen können mit einem Namen gekennzeichnet werden

Jede Qualifikation in einem Skillset hat jetzt die neue `Name`-Eigenschaft, die in Ihrem Code initialisiert werden kann, um die Qualifikation zu kennzeichnen. Dies ist optional. Wenn keine Angabe erfolgt (dies ist der Standard, wenn keine explizite Codeänderung vorgenommen wurde), wird einer Qualifikation ein Standardname zugewiesen, für den der 1-basierte Index der Qualifikation im Skillset verwendet wird, dem das Zeichen „#“ vorangestellt wird. Beispielsweise wird in der folgenden Skillsetdefinition (die meisten Initialisierungen fehlen wegen der Übersichtlichkeit):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill` der Name `#1`, `WebApiSkill` der Name `#2`, `ShaperSkill` der Name `#3` usw. zugewiesen.

Wenn Sie Qualifikationen mit einem benutzerdefinierten Namen kennzeichnen möchten, müssen Sie zunächst alle Instanzen der betroffenen Clients auf Version 10 des SDK aktualisieren. Andernfalls besteht die Möglichkeit, dass ein Client, der eine ältere Version des SDK verwendet, der `Name`-Eigenschaft einer Qualifikation den Wert `null` zuweist, was dazu führt, dass der Client wieder das Standardbenennungsschema verwendet.

## <a name="details-about-errors-and-warnings"></a>Details zu Fehlern und Warnungen

Das `ItemError`- und das `ItemWarning`-Modell, die Details zu Fehlern bzw. Warnungen kapseln, die während einer Indexerausführung auftreten, wurden so geändert, dass sie drei neue Eigenschaften mit dem Ziel enthalten, das Debuggen des Indexers zu unterstützen. Diese Eigenschaften sind:

- `Name`: Der Name der Quelle, in der der Fehler aufgetreten ist. Beispielsweise könnte dieser auf eine bestimmte Qualifikation im zugeordneten Skillset verweisen.
- `Details`: Weitere ausführliche Informationen zu dem Fehler oder der Warnung.
- `DocumentationLink`: Ein Link zu einer Anleitung zur Problembehandlung für den jeweiligen Fehler oder die jeweilige Warnung.

> [!NOTE]
> Wir haben begonnen, die zugehörigen Fehler und Warnungen zu strukturieren, um diese nützlichen Details nach Möglichkeit einzubinden. Wir arbeiten daran, dass diese Details für alle Fehler und Warnungen vorhanden sind, aber dies ist ein laufender Arbeitsprozess, sodass diese zusätzlichen Details möglicherweise noch nicht immer verfügbar sind.

## <a name="next-steps"></a>Nächste Schritte

- Änderungen an der Qualifikation „Shaper“ haben die größten möglichen Auswirkungen auf neuen oder vorhandenen Code. Im nächsten Schritt sollten Sie sich dieses Beispiel zur Veranschaulichung der Eingabestruktur erneut ansehen: [Beispiel für JSON-Definition einer „Shaper“-Qualifikation](cognitive-search-skill-shaper.md).
- Lesen Sie die [Übersicht über die KI-Anreicherung](cognitive-search-concept-intro.md).
- Wir freuen uns auf Ihr Feedback zum SDK! Wenn Probleme auftreten, können Sie sich gerne über [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) an uns wenden. Wenn Sie einen Fehler finden, können Sie das Problem im [Azure .NET SDK-GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/issues)melden. Stellen Sie sicher, dass Sie dem Titel des Problems „[Kognitive Azure-Suche]“ voranstellen.