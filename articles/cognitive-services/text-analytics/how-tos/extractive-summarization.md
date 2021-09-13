---
title: Zusammenfassen von Text mit der Textanalyse-API für die extraktive Zusammenfassung
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie mit der Textanalyse-API für die extraktive Zusammenfassung von Azure Cognitive Services Text zusammenfassen können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: aahi
ms.openlocfilehash: f8db386da9890be2a07f201243dbbb4beaf5c499
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444075"
---
# <a name="how-to-summarize-text-with-text-analytics-preview"></a>Gewusst wie: Zusammenfassen von Text mit Textanalyse (Vorschauversion)

> [!IMPORTANT] 
> Die Textanalyse für die extraktive Zusammenfassung ist eine Vorschaufunktion, die „WIE BESEHEN“ und „MIT ALLEN MÄNGELN“ zur Verfügung gestellt wird. Daher sollte die Textanalyse für die extraktive Zusammenfassung (Vorschau) nicht in einer Produktionsumgebung implementiert oder bereitgestellt werden. Jede Verwendung der Textanalyse für die extraktive Zusammenfassung liegt in der alleinigen Verantwortung des Kunden. 

Im Allgemeinen gibt es zwei Ansätze für die automatische Textzusammenfassung: extraktiv und abstrakt. Die Textanalyse-API bietet eine extraktive Zusammenfassung ab Version `3.2-preview.1`.

Die extraktive Zusammenfassung ist ein Feature in der Azure-Textanalyse, das eine Zusammenfassung erzeugt, indem Sätze extrahiert werden, die zusammen die wichtigsten oder relevantesten Informationen innerhalb des ursprünglichen Inhalts darstellen.

Dieses Feature wurde entwickelt, um Inhalte zu kürzen, die Benutzer als zum Lesen zu lang betrachten. Die extraktive Zusammenfassung fasst Artikel und Dokumente zu Kernsätzen zusammen.

Die von der API verwendeten KI-Modelle werden vom Dienst bereitgestellt. Sie müssen lediglich Inhalte für die Analyse senden.

## <a name="extractive-summarization-and-features"></a>Extraktive Zusammenfassung und Features

Das Feature für die extraktive Zusammenfassung in der Textanalyse verwendet Verarbeitungstechniken für natürliche Sprache, um Kernsätze in einem unstrukturierten Textdokument zu finden. Diese Sätze vermitteln zusammen die Hauptidee des Dokuments.

Die extraktive Zusammenfassung gibt eine Rangfolge als Teil der Systemantwort zusammen mit extrahierten Sätzen und ihrer Position in den ursprünglichen Dokumenten zurück. Eine Rangfolge gibt an, als wie relevant für die Hauptidee eines Dokuments ein Satz eingestuft wird. Das Modell gibt für jeden Satz eine Bewertung zwischen 0 und 1 (einschließlich) an und gibt pro Anforderung die Sätze mit der höchsten Bewertung zurück. Wenn Sie beispielsweise eine Zusammenfassung mit drei Sätzen anfordern, gibt der Dienst die drei am höchsten bewerteten Sätze zurück.

Eine weitere Funktion in der Textanalyse, die [Extraktion von Schlüsselbegriffen](./text-analytics-how-to-keyword-extraction.md), kann Schlüsselinformationen extrahieren. Berücksichtigen Sie bei der Entscheidung zwischen Schlüsselbegriffsextraktion und extraktiver Zusammenfassung Folgendes:
* Die Schlüsselbegriffsextraktion gibt Ausdrücke zurück, während die extraktive Zusammenfassung Sätze zurückgibt.
* Die extraktive Zusammenfassung gibt Sätze zusammen mit einer Rangfolge zurück. Pro Anforderung werden die Sätze mit der höchsten Bewertung zurückgegeben.

## <a name="sending-a-rest-api-request"></a>Senden einer REST-API-Anforderung 

> [!TIP]
> Sie können auch die neueste Vorschauversion der Clientbibliothek für die extraktive Zusammenfassung verwenden. Im Folgenden sind auf GitHub verfügbare Beispiele aufgeführt. 
> * [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample8_ExtractSummary.md)
> * [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeExtractiveSummarization.java)
> * [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_extract_summary.py)

### <a name="preparation"></a>Vorbereitung

Im Gegensatz zum Textanalyse-Feature ist die Extraktionszusammenfassung ein asynchroner Vorgang, auf den Sie über den /analyze-Endpunkt zugreifen können. JSON-Anforderungsdaten sollten dem unter [Asynchrone Anforderungen an den /analyze-Endpunkt](./text-analytics-how-to-call-api.md?tabs=asynchronous#api-request-formats) beschriebenen Format entsprechen.

Die extraktive Zusammenfassung unterstützt eine Vielzahl von Sprachen für die Dokumenteingabe. Weitere Informationen finden Sie unter [Unterstützte Sprachen](../language-support.md).

Ein Dokument muss weniger als 125.000 Zeichen enthalten. Die maximal zulässige Anzahl von Dokumenten in einer Sammlung finden Sie im Artikel [Datengrenzwerte und Ratenbegrenzungen für die Textanalyse-API](../concepts/data-limits.md?tabs=version-3). Die Sammlung wird im Hauptteil der Anforderung übermittelt.

### <a name="structure-the-request"></a>Strukturieren der Anforderung

Erstellen Sie eine POST-Anforderung. Um eine Anforderung schnell zu strukturieren und zu senden, können Sie im folgenden Verweislink [Postman](text-analytics-how-to-call-api.md) oder die **API-Testkonsole** verwenden. 

[Referenz zur Textzusammenfassung](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-2-preview-1/operations/Analyze)

### <a name="request-endpoints"></a>Anforderungsendpunkte

Legen Sie den HTTPS-Endpunkt für die extraktive Zusammenfassung fest, indem Sie eine Textanalyse-Ressource unter Azure verwenden. Beispiel:

> [!NOTE]
> Den Schlüssel und den Endpunkt für Ihre Textanalyseressource finden Sie im Azure-Portal. Sie befinden sich auf der Seite der Ressource mit dem **Schlüssel und Endpunkt**. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze`

Legen Sie einen Anforderungsheader fest, um Ihren Textanalyse-API-Schlüssel einzubeziehen. Geben Sie im Anforderungstext die JSON-Dokumentsammlung an, die Sie für diese Analyse vorbereitet haben.

### <a name="example-request"></a>Beispielanforderung 

Im Folgenden finden Sie ein Beispiel für Inhalte, die Sie zur Zusammenfassung übermitteln können, die mithilfe einer [ganzheitlichen Darstellung in Richtung integrativer KI](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/) zu Demonstrationszwecken extrahiert werden. Die API für die extraktive Zusammenfassung kann viel längeren Eingabetext akzeptieren. Weitere Informationen zu den Datengrenzwerten der Textanalyse-API finden Sie unter [Datengrenzwerte und Ratenbegrenzungen für die Textanalyse-API](../Concepts/data-limits.md).

Eine Anforderung kann mehrere Dokumente enthalten. 

Jedes Dokument weist die folgenden Parameter auf:
* `id` zum Identifizieren eines Dokuments, 
* `language`, um die Quellsprache des Dokuments anzugeben, wobei `en` die Standardsprache ist,
* `text`, um den Dokumenttext anfügen.

Alle Dokumente in einer Anforderung verwenden die folgenden Parameter. Diese Parameter können in der Anforderung in der `tasks`-Definition angegeben werden.
* `model-version`, um anzugeben, welche Version des Modells verwendet werden soll, wobei `latest` die Standardversion ist. Weitere Informationen finden Sie unter [Versionsverwaltung der Modelle in der Textanalyse-API](../concepts/model-versioning.md). 
* `sentenceCount`, um anzugeben, wie viele Sätze zurückgegeben werden, wobei `3` der Standard ist. Der Bereich liegt zwischen 1 und 20.
* `sortBy`, um anzugeben, in welcher Reihenfolge die extrahierten Sätze zurückgegeben werden. Die akzeptierten Werte für `sortBy` sind `Offset` und `Rank`, wobei `Offset` der Standard ist. Der Wert `Offset` ist die Ausgangsposition eines Satzes im ursprünglichen Dokument.    

```json
{
  "analysisInput": {
    "documents": [
      {
        "language": "en",
        "id": "1",
        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding. As Chief Technology Officer of Azure AI Cognitive Services, I have been working with a team of amazing scientists and engineers to turn this quest into a reality. In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z). At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better. We believe XYZ-code will enable us to fulfill our long-term vision: cross-domain transfer learning, spanning modalities and languages. The goal is to have pretrained models that can jointly learn representations to support a broad range of downstream AI tasks, much in the way humans do today. Over the past five years, we have achieved human performance on benchmarks in conversational speech recognition, machine translation, conversational question answering, machine reading comprehension, and image captioning. These five breakthroughs provided us with strong signals toward our more ambitious aspiration to produce a leap in AI capabilities, achieving multisensory and multilingual learning that is closer in line with how humans learn and understand. I believe the joint XYZ-code is a foundational component of this aspiration, if grounded with external knowledge sources in the downstream AI tasks."
      }
    ]
  },
  "tasks": {
    "extractiveSummarizationTasks": [
      {
        "parameters": {
          "model-version": "latest",
          "sentenceCount": 3,
          "sortBy": "Offset"
        }
      }
    ]
  }
}
```

### <a name="post-the-request"></a>Übermitteln der Anforderung

Die Extraktionszusammenfassungs-API wird nach Eingang der Anforderung ausgeführt. Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in der Übersicht im Abschnitt [Datengrenzwerte](../overview.md#data-limits).

Die Textanalyse-API für die extraktive Zusammenfassung ist eine asynchrone API, daher ist kein Text im Antwortobjekt enthalten. Sie benötigen jedoch den Wert des `operation-location`-Schlüssels in den Antwortheadern, um eine GET-Anforderung zum Überprüfen des Auftragsstatus und der Ausgabe zu erstellen.  Nachstehend finden Sie ein Beispiel für den Wert des „operation-location“-Schlüssels im Antwortheader der POST-Anforderung:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze/jobs/<jobID>`

Wenn Sie den Auftragsstatus überprüfen möchten, senden Sie eine GET-Anforderung an die URL im Wert für den „operation-location“-Schlüsselheader der POST-Antwort.  Mithilfe der folgenden Status können Sie den Status eines Auftrags anzeigen: `NotStarted`, `running`, `succeeded`, `failed` oder `rejected`.  

Wenn der Auftrag erfolgreich war, wird die Ausgabe der API im Text der GET-Anforderung zurückgegeben. 


### <a name="view-the-results"></a>Zeigen Sie die Ergebnisse an

Das nachstehende Beispiel zeigt die Antwort auf eine GET-Anforderung.  Die Ausgabe steht zum Abrufen zur Verfügung, bis `expirationDateTime` (24 Stunden ab der Uhrzeit, zu der der Auftrag erstellt wurde) nach Bereinigung der Ausgabe verstrichen ist. Aufgrund der Unterstützung von Emojis und mehreren Sprachen enthält der Antworttext unter Umständen Textversätze. Weitere Informationen finden Sie unter [Textversätze in der Ausgabe der Textanalyse-API](../concepts/text-offsets.md).

### <a name="example-response"></a>Beispielantwort
  
Das Feature für die extraktive Zusammenfassung gibt Folgendes zurück: 

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-06-11T05:43:37Z",
    "createdDateTime": "2021-06-11T05:42:32Z",
    "expirationDateTime": "2021-06-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "sentences": [
                    {
                        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding.",
                        "rankScore": 0.7673416137695312,
                        "Offset": 0,
                        "length": 160
                    },
                    {
                        "text": "In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z).",
                        "rankScore": 0.7644073963165283,
                        "Offset": 324,
                        "length": 192
                    },
                    {
                        "text": "At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better.",
                        "rankScore": 0.7623870968818665,
                        "Offset": 517,
                        "length": 203
                    }    
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-08-01"
    }
}
```

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie Konzepte und Workflows für die extraktive Zusammenfassung unter Verwendung der Textanalyse-API für extraktive Zusammenfassung kennengelernt. Verwenden Sie die extraktive Zusammenfassung zu folgenden Zwecken:

* Unterstützung der Verarbeitung von Dokumenten, um die Effizienz zu verbessern.
* Extrahieren wichtiger Informationen aus langen Dokumenten, Berichten und anderen Textformen.
* Hervorheben von Kernsätzen in Dokumenten.
* Schnelles Überfliegen von Dokumenten in einer Bibliothek.
* Generieren von Nachrichtenfeedinhalten.

## <a name="see-also"></a>Weitere Informationen

* [Übersicht über die Textanalyse](../overview.md)
* [Neuigkeiten](../whats-new.md)
* [Modellversionen](../concepts/model-versioning.md)
