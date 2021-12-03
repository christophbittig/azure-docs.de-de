---
title: Anfügen von Cognitive Services an ein Skillset
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie eine Cognitive Services-Ressource für mehrere Dienste an eine KI-Anreicherungspipeline in Azure Cognitive Search anfügen.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: ce369b60fa1b12823acdb1f5045e403bcaa3f5dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037661"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Anfügen einer Cognitive Services-Ressource an ein Skillset in der kognitiven Azure-Suche

Wenn Sie in Azure Cognitive Search eine optionale [KI-Anreicherungspipeline](cognitive-search-concept-intro.md) konfigurieren, können Sie eine begrenzte Anzahl von Dokumenten kostenlos anreichern. Für umfangreichere und häufigere Workloads sollten Sie eine abrechenbare [**Cognitive Services-Ressource für mehrere Dienste**](../cognitive-services/cognitive-services-apis-create-account.md) anfügen. Eine Ressource für mehrere Dienste bietet „Cognitive Services“ statt einzelner Dienste, und der Zugriff wird über einen einzelnen API-Schlüssel gewährt.

Ein Ressourcenschlüssel für mehrere Dienste wird in einem Skillset angegeben und ermöglicht es Microsoft, Ihnen die Nutzung dieser APIs in Rechnung zu stellen:

+ [Maschinelles Sehen](https://azure.microsoft.com/services/cognitive-services/computer-vision/) für die Bildanalyse und optische Zeichenerkennung (Optical Character Recognition, OCR)
+ [Textanalyse](https://azure.microsoft.com/services/cognitive-services/text-analytics/) für Spracherkennung, Entitätserkennung, Stimmungsanalyse und Schlüsselbegriffserkennung
+ [Textübersetzung](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

> [!NOTE]
> Die KI-Anreicherung bietet eine geringe Menge an kostenloser Verarbeitung, sodass Sie kurze Übungen durchführen können, ohne eine Cognitive Services-Ressource anfügen zu müssen. Anreicherungen sind für 20 Dokumente pro Tag und Indexer kostenlos. Sie können [den Indexer zurücksetzen](search-howto-run-reset-indexers.md), um den Zähler zurückzusetzen und die Übung zu wiederholen.

## <a name="azure-portal"></a>[**Azure-Portal**](#tab/cogkey-portal)

1. Erstellen Sie eine [Cognitive Services-Ressource für mehrere Dienste](../cognitive-services/cognitive-services-apis-create-account.md) in [derselben Region](#same-region-requirement) wie Ihr Azure Cognitive Search-Dienst.

1. Fügen Sie den Schlüssel einer Skillsetdefinition hinzu:

   + Falls Sie den [Datenimportassistenten](search-import-data-portal.md) verwenden, geben Sie den Schlüssel im zweiten Schritt, „KI-Anreicherungen hinzufügen“, ein.

   + Wenn Sie einen Schlüssel zu einem neuen oder vorhandenen Skillset hinzufügen, stellen Sie den Schlüssel in der Registerkarte **Cognitive Services** bereit.

   :::image type="content" source="media/cognitive-search-attach-cognitive-services/attach-existing2.png" alt-text="Screenshot der Schlüsselseite" border="true":::

## <a name="rest"></a>[**REST**](#tab/cogkey-rest)

1. Erstellen Sie eine [Cognitive Services-Ressource für mehrere Dienste](../cognitive-services/cognitive-services-apis-create-account.md) in [derselben Region](#same-region-requirement) wie Ihr Azure Cognitive Search-Dienst.

1. Erstellen oder aktualisieren Sie ein Skillset, das Abschnitt `cognitiveServices` im Text der [Skillset-Anfrage](/rest/api/searchservice/create-skillset) angibt:

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="net-sdk"></a>[**.NET SDK**](#tab/cogkey-dotnet)

Der folgende gekürzte Codeausschnitt ist aus [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/tutorial-ai-enrichment/v11/Program.cs).

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();

string searchServiceUri = configuration["SearchServiceUri"];
string adminApiKey = configuration["SearchServiceAdminApiKey"];
string cognitiveServicesKey = configuration["CognitiveServicesKey"];

SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));

// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

---

## <a name="how-the-key-is-used"></a>Verwendung des Schlüssels

Der Schlüssel wird für die Abrechnung verwendet, aber nicht für Verbindungen. Ein Suchdienst stellt über das interne Netzwerk eine Verbindung mit einer Cognitive Services-Ressource her, die sich in [derselben physischen Region](https://azure.microsoft.com/global-infrastructure/services/?products=search) befindet. 

Die schlüsselbasierte Abrechnung gilt, wenn API-Aufrufe an Cognitive Services-Ressourcen 20 API-Aufrufe pro Indexer und Tag überschreiten. Sie können den Indexer nach jedem Indexeraufruf zurücksetzen, um den API-Indikator zurückzusetzen. Die maximale Anzahl von Aufrufen, die frei erfolgen können, ist jedoch auf 20 beschränkt.

 Während der KI-Anreicherung ruft Cognitive Search die Cognitive Services-APIs für [integrierte Qualifikationen](cognitive-search-predefined-skills.md) auf, die auf maschinelles Sehen, Textübersetzung und Textanalyse basieren. Zu den integrierten Skills, die Back-End-Aufrufe an Cognitive Services machen, gehören [Entitätsverknüpfung](cognitive-search-skill-entity-linking-v3.md), [Entitätserkennung](cognitive-search-skill-entity-recognition-v3.md), [Bildanalyse](cognitive-search-skill-image-analysis.md), [Schlüsselbegriffserkennung](cognitive-search-skill-keyphrases.md), [Sprachenerkennung](cognitive-search-skill-language-detection.md), [OCR](cognitive-search-skill-ocr.md), [PII-Erkennung](cognitive-search-skill-pii-detection.md), [Stimmung](cognitive-search-skill-sentiment-v3.md) und [Textübersetzung](cognitive-search-skill-text-translation.md).

Sie können den Schlüssel und den Abschnitt Cognitive Services für Skillsets weglassen, die ausschließlich aus benutzerdefinierten Qualifikationen oder Hilfsqualifikationen bestehen. Sie müssen die Eigenschaft nicht angeben, wenn Ihre Nutzung gebührenpflichtiger Skills unter 20 Transaktionen pro Indexer und Tag liegt.

### <a name="exceptions-and-special-cases"></a>Ausnahmen und Sonderfälle

+ Hilfsqualifikationen, für die Cognitive Services nicht benötigt werden, (nämlich: [bedingte](cognitive-search-skill-conditional.md), [Dokumentextrahierungs-](cognitive-search-skill-document-extraction.md), [Shaper-](cognitive-search-skill-shaper.md), [Textzusammenführungs-](cognitive-search-skill-textmerger.md) und [Textaufteilungsqualifikationen](cognitive-search-skill-textsplit.md)) können nicht abgerechnet werden. 

+ Die [benutzerdefinierte Entitätssuche](cognitive-search-skill-custom-entity-lookup.md) wird von Azure Cognitive Search und nicht Cognitive Services berechnet, erfordert jedoch einen Cognitive Services-Ressourcenschlüssel, um Transaktionen über 20 pro Indexer und Tag hinaus zu entsperren. Nur für diesen Skill entsperrt der Ressourcenschlüssel die Anzahl von Transaktionen, hat jedoch keinen Bezug zur Abrechnung.

### <a name="other-costs-of-ai-enrichment"></a>Andere Kosten für die KI-Anreicherung

Die Bildextraktion ist ein Azure Cognitive Search-Vorgang, der ausgeführt wird, wenn Dokumente vor der Anreicherung entschlüsselt werden. Die Bildextraktion ist in allen Ebenen gebührenpflichtig, mit Ausnahme von 20 kostenlosen täglichen Extraktionen im Tarif „Free“. Die Kosten für die Bildextraktion gelten für Bilddateien in Blobs, eingebettete Bilder in anderen Dateien (PDF- und andere App-Dateien) und für mit [Dokumentextraktion](cognitive-search-skill-document-extraction.md) extrahierte Bilder. Die Preise für die Bildextraktion finden Sie in der [Preisübersicht für die kognitive Azure-Suche](https://azure.microsoft.com/pricing/details/search/).

Die Textextraktion erfolgt auch während der [Dokumententschlüsselung](search-indexer-overview.md#document-cracking). Sie kann nicht abgerechnet werden.

> [!TIP]
> Um die Kosten für die Skillsetverarbeitung zu senken, aktivieren Sie die [inkrementelle Anreicherung (Vorschau)](cognitive-search-incremental-indexing-conceptual.md), um Anreicherungen zwischenzuspeichern und wiederzuverwenden, die von Änderungen an einem Skillset nicht betroffen sind. Die Zwischenspeicherung erfordert Azure Storage (siehe [Preise](https://azure.microsoft.com/pricing/details/storage/blobs/), aber die kumulativen Kosten für die Ausführung von Skillsets sind niedriger, wenn vorhandene Anreicherungen wiederverwendet werden können, insbesondere für Skillsets, die Bildextraktion und -analyse verwenden).

## <a name="same-region-requirement"></a>Anforderung derselben Region

Cognitive Search und Cognitive Services müssen in derselben physischen Region vorhanden sein, wie auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=search) angegeben. Die meisten Regionen, die Cognitive Search bieten, bieten auch Cognitive Services.

Wenn Sie in einer Region, die nicht über beide Dienste verfügt, KI-Anreicherung auszuführen versuchen, wird die folgende Meldung angezeigt: „Provided key is not a valid CognitiveServices type key for the region of your search service.“ (Der angegebene Schlüssel ist kein gültiger CognitiveServices-Schlüssel für die Region des Suchdiensts.).

> [!NOTE]
> Einige integrierte Qualifikationen basieren auf nicht regionalen Cognitive Services (etwa die [Qualifikation „Textübersetzung“](cognitive-search-skill-text-translation.md)). Die Verwendung einer nicht regionalen Qualifikation bedeutet, dass Ihre Anforderung in einer anderen Region als der Azure Cognitive Search-Region verarbeitet werden kann. Weitere Informationen zu nicht regionalen Diensten finden Sie auf der Seite für [Cognitive Services-Produkte nach Region](https://aka.ms/allinoneregioninfo).

## <a name="example-estimate-costs"></a>Beispiel: Schätzen der Kosten

Zum Schätzen der Kosten für die Cognitive Search-Indizierung gehen Sie von einem durchschnittlichen Dokument aus, damit einige Zahlen zur Verfügung stehen. Beispielsweise kann Folgendes angenommen werden:

+ 1.000 PDFs
+ Jeweils sechs Seiten
+ Ein Bild pro Seite (6.000 Bilder)
+ 3.000 Zeichen pro Seite

Als Beispiel dient eine Pipeline, die Dokumententschlüsselung jeder PDF-Datei, Bild- und Textextraktion, optische Zeichenerkennung (OCR) von Bildern und die Erkennung von Entitäten für Organisationen umfasst.

Die in diesem Artikel angegebenen Preise sind hypothetisch. Sie dienen der Veranschaulichung des Schätzungsprozesses. Ihre Kosten können niedriger ausfallen. Die tatsächlichen Preise von Transaktionen finden Sie unter [Cognitive Services – Preise](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Im Fall der Dokumententschlüsselung mit Text- und Bildinhalten ist die Textextraktion derzeit kostenlos. Bei 6.000 Bildern und einem angenommenen Preis von 1 € für jeweils 1.000 extrahierte Bilder belaufen sich die Kosten auf 6,00 € für diesen Schritt.

2. Für die optische Zeichenerkennung (OCR) von 6.000 Bildern in Englisch verwendet die kognitive Qualifikation „OCR“ den besten Algorithmus (DescribeText). Angenommen, jeweils 1.000 zu analysierende Bilder kosten 2,50 €, dann würden für diesen Schritt 15,00 € anfallen.

3. Für die Extraktion von Entitäten würden insgesamt 3 Textdatensätze pro Seite entstehen. Jeder Datensatz umfasst 1.000 Zeichen. Drei Textdatensätze pro Seite multipliziert mit 6.000 Seiten ergibt 18.000 Textdatensätze. Angenommen, die Kosten betragen 2,00 €/1.000 Textdatensätze, dann würde dieser Schritt 36,00 € kosten.

Insgesamt würden Sie also etwa 57,00 € zahlen, um 1.000 PDF-Dokumente dieser Art mit der beschriebenen Qualifikationsgruppe zu erfassen.

## <a name="next-steps"></a>Nächste Schritte

+ [Preisübersicht für die kognitive Azure-Suche](https://azure.microsoft.com/pricing/details/search/)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Erstellen eines Skillsets (REST)](/rest/api/searchservice/create-skillset)
+ [Zuordnen angereicherter Felder](cognitive-search-output-field-mapping.md)
