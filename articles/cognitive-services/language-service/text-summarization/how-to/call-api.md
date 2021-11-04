---
title: Zusammenfassen von Text mit der API für extraktive Zusammenfassung
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie Text mit der API für die extraktive Zusammenfassung zusammenfassen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-summarization, ignite-fall-2021
ms.openlocfilehash: 21be719780b4633456ceea89c0cb6c6295480348
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096033"
---
# <a name="how-to-use-text-summarization-preview"></a>Verwenden der Textzusammenfassung (Vorschauversion)

> [!IMPORTANT] 
> Die extraktive Zusammenfassung ist ein Vorschaufeature, das „WIE BESEHEN“ und „MIT ALLEN MÄNGELN“ zur Verfügung gestellt wird. Daher sollte die extraktive Zusammenfassung (Vorschauversion) nicht in einer Produktionsumgebung implementiert oder bereitgestellt werden. Jede Verwendung der extraktiven Zusammenfassung liegt in der alleinigen Verantwortung des Kunden. 

Im Allgemeinen gibt es zwei Ansätze für die automatische Textzusammenfassung: extraktiv und abstrakt. Diese API ermöglicht die extraktive Zusammenfassung.

Die extraktive Zusammenfassung ist ein Feature, das eine Zusammenfassung erzeugt, indem Sätze extrahiert werden, die zusammen die wichtigsten oder relevantesten Informationen des ursprünglichen Inhalts darstellen.

Dieses Feature wurde entwickelt, um Inhalte zu kürzen, die Benutzer als zum Lesen zu lang betrachten. Die extraktive Zusammenfassung fasst Artikel und Dokumente zu Kernsätzen zusammen.

Die von der API verwendeten KI-Modelle werden vom Dienst bereitgestellt. Sie müssen lediglich Inhalte für die Analyse senden.

## <a name="features"></a>Features

> [!TIP]
> Informationen zu den ersten Schritte zur Verwendung dieses Features finden Sie im [Schnellstartartikel](../quickstart.md). Sie können auch Beispielanforderungen in [Language Studio](../../language-studio.md) erstellen, ohne Code schreiben zu müssen.

Die API für die extraktive Zusammenfassung verwendet Verarbeitungstechniken für natürliche Sprache, um Kernsätze in einem unstrukturierten Textdokument zu finden. Diese Sätze vermitteln zusammen die Hauptidee des Dokuments.

Die extraktive Zusammenfassung gibt eine Rangfolge als Teil der Systemantwort zusammen mit extrahierten Sätzen und ihrer Position in den ursprünglichen Dokumenten zurück. Eine Rangfolge gibt an, als wie relevant für die Hauptidee eines Dokuments ein Satz eingestuft wird. Das Modell gibt für jeden Satz eine Bewertung zwischen 0 und 1 (einschließlich) an und gibt pro Anforderung die Sätze mit der höchsten Bewertung zurück. Wenn Sie beispielsweise eine Zusammenfassung mit drei Sätzen anfordern, gibt der Dienst die drei am höchsten bewerteten Sätze zurück.

In Azure Cognitive Service für Language gibt es ein weiteres Feature, die [Schlüsselbegriffserkennung](./../../key-phrase-extraction/how-to/call-api.md), mit der Schlüsselinformationen extrahiert werden können. Berücksichtigen Sie bei der Entscheidung zwischen Schlüsselbegriffsextraktion und extraktiver Zusammenfassung Folgendes:
* Die Schlüsselbegriffsextraktion gibt Ausdrücke zurück, während die extraktive Zusammenfassung Sätze zurückgibt.
* Die extraktive Zusammenfassung gibt Sätze zusammen mit einer Rangfolge zurück. Pro Anforderung werden die Sätze mit der höchsten Bewertung zurückgegeben.

## <a name="determine-how-to-process-the-data-optional"></a>Festlegen der Art der Datenverarbeitung (optional)

### <a name="specify-the-text-summarization-model"></a>Festlegen des Textzusammenfassungsmodells

Standardmäßig verwendet die Textzusammenfassung das neueste verfügbare KI-Modell für Ihren Text. Sie können Ihre API-Anforderungen auch für die Verwendung einer bestimmten Modellversion konfigurieren. Das festgelegte Modell wird verwendet, um Textzusammenfassungsvorgänge durchzuführen.

| Unterstützte Versionen | Aktuelle Version |
|--|--|
| `2021-08-01` | `2021-08-01` |

### <a name="input-languages"></a>Eingabesprachen

Wenn Sie Dokumente übermitteln, die von der Schlüsselbegriffserkennung verarbeitet werden sollen, können Sie angeben, in welchen [unterstützten Sprachen](../language-support.md) die Dokumente geschrieben sind. Wenn Sie keine Sprache angeben, wird die Schlüsselbegriffserkennung standardmäßig auf Englisch festgelegt. Die API gibt möglicherweise Offsets in der Antwort zurück, um verschiedene [mehrsprachige und Emoji-Codierungen](../../concepts/multilingual-emoji-support.md) zu unterstützen. 

## <a name="submitting-data"></a>Übermitteln der Daten

Sie übermitteln Dokumente als Textzeichenfolgen an die API. Die Analyse erfolgt, wenn die Anforderung eingeht. Da es sich um eine asynchrone API handelt, kann es zu einer Verzögerung zwischen dem Senden einer API-Anforderung und dem Erhalt der Ergebnisse kommen.  Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in den nachstehend aufgeführten Datengrenzwerten.

Wenn Sie dieses Feature verwenden, sind die API-Ergebnisse ab der Erfassung der Anforderung wie in der Antwort angegeben 24 Stunden lang verfügbar. Nach diesem Zeitraum werden die Ergebnisse endgültig gelöscht und stehen nicht mehr zum Abruf zur Verfügung.

Verwenden Sie den Parameter `sentenceCount`, um anzugeben, wie viele Sätze zurückgegeben werden, wobei `3` der Standardwert ist. Der Bereich liegt zwischen 1 und 20.

Sie können auch mit dem Parameter `sortby` angeben, in welcher Reihenfolge die extrahierten Sätze zurückgegeben werden. Dabei sind die Optionen `Offset` oder `Rank` verfügbar, wobei `Offset` der Standardwert ist. 


|Parameterwert  |BESCHREIBUNG  |
|---------|---------|
|Rang    | Sätze werden gemäß ihrer Relevanz im Eingabedokument vom Dienst sortiert.        |
|Offset    | Die ursprüngliche Reihenfolge der Sätze im Eingabedokument wird beibehalten.        |

## <a name="getting-text-summarization-results"></a>Abrufen von Textzusammenfassungsergebnissen

Wenn Sie Ergebnisse von der Sprachenerkennung erhalten, können Sie die Ergebnisse an eine Anwendung streamen oder die Ausgabe in einer Datei im lokalen System speichern.

Im Folgenden finden Sie ein Beispiel für Inhalte, die Sie zur Zusammenfassung übermitteln können, die mithilfe des Microsoft-Blogartikels [Ganzheitliche Darstellung in Richtung integrativer KI](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/) extrahiert werden. Dieser Artikel ist nur ein Beispiel dafür, dass die API viel längeren Eingabetext verarbeiten kann. Weitere Informationen finden Sie im Abschnitt „Datengrenzwerte“.
 
*„Bei Microsoft verfolgen wir das Ziel, KI über den aktuellen Stand der Technik hinaus zu verbessern. Dabei wählen wir einen ganzheitlicheren, benutzerzentrierten Ansatz für das Lernen und Verstehen. Als Chief Technology Officer von Azure AI Cognitive Services arbeite ich mit einem Team aus Wissenschaftler*innen und Engineers zusammen, um dieses Vorhaben in die Tat umzusetzen. In meiner Rolle sehe ich die Beziehung der drei Attribute der menschlichen Wahrnehmung aus einer ganz besonderen Perspektive: einsprachiger Text (X), Audiosignale oder visuelle Signale (Y) und mehrsprachiger Text (Z). Die Schnittmenge dieser drei Attribute ist jedoch der Schlüssel. Wir bezeichnen das als XYZ-Code (Abbildung 1). Durch das Zusammenspiel dieser Faktoren können wir leistungsfähigere KI schaffen, die sprechen, hören, sehen und Menschen besser verstehen kann. Wir sind davon überzeugt, dass wir mit dem XYZ-Code unser langfristiges Ziel erreichen können, den domänenübergreifenden Lerntransfer für verschiedene Modalitäten und Sprachen. Wir möchten vortrainierte Modelle besitzen, die zusammen Repräsentationen erlernen können, um ähnlich wie der moderne Mensch eine breite Palette an KI-Downstreamtasks zu erledigen. In den letzten fünf Jahren haben wir das menschliche Leistungsniveau bereits bei Benchmarks für die Spracherkennung, die maschinelle Übersetzung, die Beantwortung von Fragen, das Verständnis beim maschinellen Lesen und die Bilderkennung erreicht. Diese fünf Durchbrüche waren wegweisend für das noch ambitioniertere Ziel, einen großen Sprung im Bereich KI zu wagen und ein mehrsprachiges Lernen für mehrere Sinne zu erzielen, das dem menschlichen Lernen und Verstehen noch näher kommt. Ich bin davon überzeugt, dass der XYZ-Code ein wichtiger Grundbaustein für dieses Ziel ist, er zusammen mit externen Wissensquellen für diese KI-Downstreamtasks verwendet wird.“*

Die API für die extraktive Zusammenfassung wird nach Empfang der Anforderung ausgeführt, indem ein Auftrag für das API-Back-End erstellt wird. Wenn der Auftrag erfolgreich war, wird die Ausgabe der API zurückgegeben. Die Ausgabe kann 24 Stunden lang abgerufen werden. Anschließend wird die Ausgabe gelöscht. Aufgrund der Unterstützung von Emojis und mehreren Sprachen enthält der Antworttext unter Umständen Textversätze. Weitere Informationen finden Sie unter [Textversätze in der Ausgabe der Textanalyse-API](../../concepts/multilingual-emoji-support.md).

Im obigen Beispiel gibt die API möglicherweise die folgenden zusammengefassten Sätze zurück:

*„Bei Microsoft verfolgen wir das Ziel, KI über den aktuellen Stand der Technik hinaus zu verbessern. Dabei wählen wir einen ganzheitlicheren, benutzerzentrierten Ansatz für das Lernen und Verstehen.“*

*„In meiner Rolle sehe ich die Beziehung der drei Attribute der menschlichen Wahrnehmung aus einer ganz besonderen Perspektive: einsprachiger Text (X), Audiosignale oder visuelle Signale (Y) und mehrsprachiger Text (Z).“*

*„Die Schnittmenge dieser drei Attribute ist jedoch der Schlüssel. Wir bezeichnen das als XYZ-Code (Abbildung 1). Durch das Zusammenspiel dieser Faktoren können wir leistungsfähigere KI schaffen, die sprechen, hören, sehen und Menschen besser verstehen kann.“*


## <a name="data-limits"></a>Datengrenzwerte

In diesem Abschnitt werden die Grenzwerte für die Größe und die Raten beschrieben, zu denen Sie Daten an die Textzusammenfassungs-API senden können. Beachten Sie, dass die Datengrenzwerte oder Ratenbegrenzungen keine Auswirkungen auf die Preise haben. Die Preise richten sich nach den [Preisdetails](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) für Ihre Sprachressource.

> [!NOTE]
> * Wenn Sie größere Dokumente analysieren müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. 
> * Ein Dokument ist eine einzelne aus Textzeichen bestehende Zeichenfolge.  

| Begrenzung | Wert |
|------------------------|---------------|
| Maximale Zeichenanzahl pro Anforderung  | 125.000 Zeichen für alle übermittelten Dokumente, gemessen mithilfe von [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Max. Anzahl von Dokumenten pro Anforderung | 25 |

Wenn ein Dokument die Grenzwerte überschreitet, lehnt die API die gesamte Anforderung ab und gibt einen Fehler vom Typ `400 bad request` zurück, wenn ein enthaltenes Dokument die maximal zulässige Größe überschreitet.

### <a name="rate-limits"></a>Ratenbegrenzungen

Die Ratenbegrenzung variiert je nach [Tarif](https://aka.ms/unifiedLanguagePricing).

| Tarif          | Anforderungen pro Sekunde | Anforderungen pro Minute |
|---------------|---------------------|---------------------|
| S/Mehrere Dienste | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="see-also"></a>Siehe auch

* [Übersicht über die Textzusammenfassung](../overview.md)
