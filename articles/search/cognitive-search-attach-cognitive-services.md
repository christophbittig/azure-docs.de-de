---
title: Anfügen von Cognitive Services an ein Skillset
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie eine Cognitive Services-Ressource für mehrere Dienste an eine KI-Anreicherungspipeline in Azure Cognitive Search anfügen.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 0fe9a87e82ab391fc0e1ccfca95ad48a0ef5dc61
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772465"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Anfügen einer Cognitive Services-Ressource an ein Skillset in der kognitiven Azure-Suche

Wenn Sie in Azure Cognitive Search eine [KI-Anreicherungspipeline](cognitive-search-concept-intro.md) konfigurieren, können Sie eine begrenzte Anzahl von Dokumenten kostenlos anreichern. Für umfangreichere und häufigere Workloads sollten Sie eine abrechenbare [Cognitive Services-Ressource für mehrere Dienste](../cognitive-services/cognitive-services-apis-create-account.md) anfügen. Eine Ressource für mehrere Dienste bietet „Cognitive Services“ statt einzelner Dienste, und der Zugriff wird über einen einzelnen API-Schlüssel gewährt.

Ein Ressourcenschlüssel wird in einem Skillset angegeben und ermöglicht es Microsoft, Ihnen die Nutzung dieser APIs in Rechnung zu stellen:

+ [Maschinelles Sehen](https://azure.microsoft.com/services/cognitive-services/computer-vision/) für die Bildanalyse und optische Zeichenerkennung (Optical Character Recognition, OCR)
+ [Textanalyse](https://azure.microsoft.com/services/cognitive-services/text-analytics/) für Spracherkennung, Entitätserkennung, Stimmungsanalyse und Schlüsselbegriffserkennung
+ [Textübersetzung](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

Der Schlüssel wird für die Abrechnung verwendet, aber nicht für Verbindungen. Intern stellt ein Suchdienst eine Verbindung mit einer Cognitive Services-Ressource her, die sich in [derselben physischen Region](https://azure.microsoft.com/global-infrastructure/services/?products=search) befindet.

## <a name="key-requirements"></a>Schlüsselanfoderungen

Ein Ressourcenschlüssel ist für gebührenpflichtige [integrierte Skills](cognitive-search-predefined-skills.md) erforderlich, die mehr als 20 Mal pro Tag für denselben Indexer verwendet werden. Zu den Skills, die Back-End-Aufrufe an Cognitive Services machen, gehören Entitätsverknüpfung, Entitätserkennung, Bildanalyse, Schlüsselbegriffserkennung, Sprachenerkennung, OCR, PII-Erkennung, Stimmung oder Textübersetzung.

Die [benutzerdefinierte Entitätssuche](cognitive-search-skill-custom-entity-lookup.md) wird von Azure Cognitive Search und nicht Cognitive Services berechnet, erfordert jedoch einen Cognitive Services-Ressourcenschlüssel, um Transaktionen über 20 pro Indexer und Tag hinaus zu entsperren. Nur für diesen Skill entsperrt der Ressourcenschlüssel die Anzahl von Transaktionen, hat jedoch keinen Bezug zur Abrechnung.

Sie können den Schlüssel und den Cognitive Services-Abschnitt für Skillsets weglassen, die ausschließlich aus benutzerdefinierten Skills oder Hilfsprogrammskills bestehen („Bedingt“, „Dokumentextraktion“, „Shaper“, „Textzusammenführung“, „Textaufteilung“). Sie können den Abschnitt auch weglassen, wenn Ihre Nutzung gebührenpflichtiger Skills unter 20 Transaktionen pro Indexer und Tag liegt.

## <a name="how-billing-works"></a>Funktionsweise der Abrechnung

+ Azure Cognitive Search verwendet den Cognitive Services-Ressourcenschlüssel, den Sie für ein Skillset bereitstellen, um die Bild- und Textanreicherung abzurechnen. Die Ausführung abrechenbarer Qualifikationen erfolgt nach dem [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

+ Die Bildextraktion ist ein Azure Cognitive Search-Vorgang, der ausgeführt wird, wenn Dokumente vor der Anreicherung entschlüsselt werden. Die Bildextraktion ist in allen Ebenen gebührenpflichtig, mit Ausnahme von 20 kostenlosen täglichen Extraktionen im Tarif „Free“. Die Kosten für die Bildextraktion gelten für Bilddateien in Blobs, eingebettete Bilder in anderen Dateien (PDF- und andere App-Dateien) und für mit [Dokumentextraktion](cognitive-search-skill-document-extraction.md) extrahierte Bilder. Die Preise für die Bildextraktion finden Sie in der [Preisübersicht für die kognitive Azure-Suche](https://azure.microsoft.com/pricing/details/search/).

+ Die Textextraktion erfolgt auch während der [Dokumententschlüsselung](search-indexer-overview.md#document-cracking). Sie kann nicht abgerechnet werden.

+ Qualifikationen, für die Cognitive Services nicht benötigt werden, einschließlich bedingter und Shaper-Qualifikationen sowie Qualifikationen zum Zusammenführen oder Teilen von Text, können nicht abgerechnet werden. 

  Wie bereits erwähnt, ist die [benutzerdefinierte Entitätssuche](cognitive-search-skill-custom-entity-lookup.md) ein Sonderfall, da sie einen Schlüssel erfordert, aber von [Cognitive Search berechnet](https://azure.microsoft.com/pricing/details/search/#pricing) wird.

> [!TIP]
> Um die Kosten für die Skillsetverarbeitung zu senken, aktivieren Sie die [inkrementelle Anreicherung (Vorschau)](cognitive-search-incremental-indexing-conceptual.md), um Anreicherungen zwischenzuspeichern und wiederzuverwenden, die von Änderungen an einem Skillset nicht betroffen sind. Die Zwischenspeicherung erfordert Azure Storage (siehe [Preise](https://azure.microsoft.com/pricing/details/storage/blobs/), aber die kumulativen Kosten für die Ausführung von Skillsets sind niedriger, wenn vorhandene Anreicherungen wiederverwendet werden können, insbesondere für Skillsets, die Bildextraktion und -analyse verwenden).

## <a name="same-region-requirement"></a>Anforderung derselben Region

Cognitive Search und Cognitive Services müssen in derselben physischen Region vorhanden sein, wie auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=search) angegeben. Die meisten Regionen, die Cognitive Search bieten, bieten auch Cognitive Services.

Wenn Sie in einer Region, die nicht über beide Dienste verfügt, KI-Anreicherung auszuführen versuchen, wird die folgende Meldung angezeigt: „Provided key is not a valid CognitiveServices type key for the region of your search service.“ (Der angegebene Schlüssel ist kein gültiger CognitiveServices-Schlüssel für die Region des Suchdiensts.).

> [!NOTE]
> Einige integrierte Qualifikationen basieren auf nicht regionalen Cognitive Services (etwa die [Qualifikation „Textübersetzung“](cognitive-search-skill-text-translation.md)). Die Verwendung einer nicht regionalen Qualifikation bedeutet, dass Ihre Anforderung in einer anderen Region als der Azure Cognitive Search-Region verarbeitet werden kann. Weitere Informationen zu nicht regionalen Diensten finden Sie auf der Seite für [Cognitive Services-Produkte nach Region](https://aka.ms/allinoneregioninfo).

## <a name="use-free-resources"></a>Verwenden kostenloser Ressourcen

Sie können eine eingeschränkte, kostenlose Verarbeitungsoption verwenden, um das Tutorial zur KI-Anreicherung und die Schnellstartübungen abzuschließen.

Kostenlose Ressourcen (begrenzte Anreicherung) sind auf 20 Dokumente pro Tag pro Indexer begrenzt. Sie können [den Indexer zurücksetzen](search-howto-run-reset-indexers.md), um den Zähler zurückzusetzen.

Wenn Sie für die KI-Erweiterung den **Datenimport**-Assistenten verwenden, finden Sie auf der Seite **KI-Anreicherung hinzufügen (optional)** die Option „Cognitive Services-Instanz anfügen“.

![Erweiterter Abschnitt „Cognitive Services-Instanz anfügen“](./media/cognitive-search-attach-cognitive-services/attach1.png "Erweiterter Abschnitt „Cognitive Services-Instanz anfügen“")

## <a name="use-billable-resources"></a>Verwenden abrechenbarer Ressourcen

Für Workloads, bei denen täglich mehr als 20 gebührenpflichtige Anreicherungen erstellt werden, müssen Sie eine Cognitive Services-Ressource anfügen. 

Wenn Sie den **Datenimport**-Assistenten verwenden, können Sie auf der Seite **KI-Anreicherung hinzufügen (Optional)** eine abrechenbare Ressource konfigurieren.

1. Erweitern Sie **Cognitive Services-Instanz anfügen**, und wählen Sie dann **Neue Cognitive Services-Ressource erstellen** aus. Eine neue Registerkarte wird geöffnet, sodass Sie die Ressource erstellen können:

   ![Erstellen einer Cognitive Services-Ressource](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Erstellen einer Cognitive Services-Ressource")

1. Wählen Sie in der Liste **Standort** die Region aus, in der der Suchdienst enthalten ist.

1. Wählen Sie in der Liste **Tarif** den Tarif **S0** aus, um die gesamte Sammlung der Cognitive Services-Funktionen einschließlich der Funktionen für Bildanalyse und Sprache zu erhalten, die die in der kognitiven Azure-Suche enthaltenen integrierten Skills unterstützen.

   Für den Tarif „S0“ finden Sie Sätze für bestimmte Arbeitsauslastungen auf der Seite [Cognitive Services – Preise](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Stellen Sie sicher, dass in der Liste **Wählen Sie ein Angebot aus** die Option **Cognitive Services** ausgewählt ist.
   + Die Preise für **Textanalysen Standard** unter den Funktionen für **Sprache** gelten für die KI-Indizierung.
   + Die Preise für **Maschinelles Sehen S1** sind unter den Funktionen für **Bildanalyse** aufgeführt.

1. Wählen Sie **Erstellen** aus, um die neue Cognitive Services-Ressource bereitzustellen.

1. Kehren Sie zur vorherigen Registerkarte zurück. Wählen Sie **Aktualisieren** aus, um die Cognitive Services-Ressource anzuzeigen, und wählen Sie dann die Ressource aus:

   ![Auswählen der Cognitive Services-Ressource](./media/cognitive-search-attach-cognitive-services/attach2.png "Auswählen der Cognitive Services-Ressource")

1. Erweitern Sie den Abschnitt **Add cognitive skills** (Kognitive Skills hinzufügen), um die spezifischen kognitiven Skills auszuwählen, die für die Daten ausgeführt werden sollen. Führen Sie dann die restlichen Schritte des Assistenten aus.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Anfügen einer vorhandenen Qualifikationsgruppe an eine Cognitive Services-Ressource

Wenn Sie bereits über eine Qualifikationsgruppe verfügen, können Sie diese an eine neue oder andere Cognitive Services-Ressource anfügen.

1. Wählen Sie auf der Übersichtsseite des Suchdiensts **Skillsets** aus:

   ![Registerkarte „Skillsets“](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Registerkarte „Skillsets“")

1. Wählen Sie den Namen der Qualifikationsgruppe und dann eine vorhandene Ressource aus, oder erstellen Sie eine neue Ressource. Wählen Sie zum Bestätigen Ihrer Änderungen **OK** aus.

   ![Liste der Skillsetressourcen](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Liste der Skillsetressourcen")

   Denken Sie daran, dass die Option **Free (begrenzte Anreicherung)** auf 20 Dokumente täglich beschränkt ist und dass Sie **Neue Cognitive Services-Ressource erstellen** zum Bereitstellen einer neuen abrechenbaren Ressource verwenden können. Wenn Sie eine neue Ressource erstellen, wählen Sie **Aktualisieren** aus, um die Liste der Cognitive Services-Ressourcen zu aktualisieren, und wählen Sie dann die Ressource aus.

## <a name="attach-cognitive-services-programmatically"></a>Programmgesteuertes Anfügen von Cognitive Services

Wenn Sie die Qualifikationsgruppe programmgesteuert definieren, fügen Sie einen `cognitiveServices`-Abschnitt zur Qualifikationsgruppe hinzu. Nehmen Sie in diesen Abschnitt den Schlüssel der Cognitive Services-Ressource auf, die Sie der Qualifikationsgruppe zuordnen möchten. Denken Sie daran, dass sich die Ressource in derselben Region wie die Ressource der kognitiven Azure-Suche befinden muss. Nehmen Sie außerdem `@odata.type` auf, und legen Sie ihn auf `#Microsoft.Azure.Search.CognitiveServicesByKey` fest.

Das folgende Beispiel zeigt dieses Muster. Beachten Sie den Abschnitt `cognitiveServices` am Ende der Definition.

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
