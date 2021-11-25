---
title: Vorbereiten von Daten für Custom Speech – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Wenn Sie die Genauigkeit der Spracherkennung von Microsoft testen oder Ihre benutzerdefinierten Modelle trainieren möchten, benötigen Sie Audio- und Textdaten. Auf dieser Seite werden die einzelnen Datentypen, ihre Verwendung und ihre Verwaltung beschrieben.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: eur
ms.custom: ignite-fall-2021
ms.openlocfilehash: 559081847ae83776bdf2d915cd194ccf6ffddb1f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546384"
---
# <a name="prepare-data-for-custom-speech"></a>Vorbereiten von Daten für Custom Speech

Wenn Sie die Genauigkeit der Spracherkennung von Microsoft testen oder Ihre benutzerdefinierten Modelle trainieren möchten, benötigen Sie Audio- und Textdaten. Auf dieser Seite werden die Datentypen behandelt, die für ein benutzerdefiniertes Sprachmodell erforderlich sind.

## <a name="data-diversity"></a>Vielfältigkeit der Daten

Text- und Audiodaten zum Testen und Trainieren eines benutzerdefinierten Modells müssen Beispiele von unterschiedlichen Sprechern und Szenarien enthalten, die Ihr Modell erkennen soll.
Berücksichtigen Sie beim Sammeln von Daten für das Testen und Trainieren benutzerdefinierter Modelle diese Faktoren:

* Ihre Text- und Sprachaudiodaten müssen alle Arten von verbalen Aussagen abdecken, die Ihre Benutzer bei der Interaktion mit Ihrem Modell abgeben. Beispielsweise muss ein Modell, das die Temperatur erhöht und senkt, zu Aussagen trainiert werden, die Personen zum Anfordern solcher Änderungen abgeben können.
* Ihre Daten müssen alle Sprachvarianten enthalten, die das Modell erkennen muss. Viele Faktoren können die Sprache verändern, einschließlich Akzenten, Dialekten, Sprachmischung, Alter, Geschlecht, Sprachwiedergabe, Belastung und Tageszeit.
* Sie müssen Beispiele aus unterschiedlichen Umgebungen (drinnen, draußen, mit Straßenlärm) einschließen, in denen das Modell verwendet wird.
* Die Audiodaten müssen mit Hardwaregeräten gesammelt werden, die auch im Produktionssystem verwendet werden. Wenn Ihr Modell Sprache identifizieren muss, die auf Aufnahmegeräten mit unterschiedlicher Qualität aufgezeichnet wird, müssen die Audiodaten, die Sie zum Trainieren des Modells bereitstellen, auch diese verschiedenen Szenarien widerspiegeln.
* Sie können Ihrem Modell später weitere Daten hinzufügen. Achten Sie jedoch darauf, dass das Dataset vielfältig und repräsentativ für Ihre Projektanforderungen ist.
* Das Einschließen von Daten, die *nicht* zu den Erkennungsanforderungen Ihres benutzerdefinierten Modells gehören, kann die Erkennungsqualität insgesamt beeinträchtigen. Sie sollten daher keine Daten einschließen, die das Modell nicht transkribieren soll.

Ein Modell, das für eine Teilmenge von Szenarien trainiert wurde, wird nur in diesen Szenarien gut funktionieren. Wählen Sie die Daten sorgfältig aus, damit sie sämtliche Szenarien abdecken, die mit Ihrem benutzerdefinierten Modell erkannt werden sollen.

> [!TIP]
> Beginnen Sie mit kleinen Sätzen von Beispieldaten, die der Sprache und der Akustik Ihres Modells entsprechen.
> Zeichnen Sie z. B. ein kleines, aber repräsentatives Audiobeispiel auf derselben Hardware und in derselben akustischen Umgebung auf, in der Ihr Modell in Produktionsszenarien verwendet werden soll.
> Kleine Datasets mit repräsentativen Daten können Probleme offenlegen, bevor Sie mit dem Sammeln von wesentlich größeren Datasets beginnen.
>
> Zum schnellen Einstieg sollten Sie Beispieldaten verwenden. In diesem GitHub-Repository finden Sie <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">Custom Speech-Beispieldaten</a>.

## <a name="data-types"></a>Datentypen

Die folgende Tabelle enthält die zulässigen Datentypen, gibt an, wann der jeweilige Datentyp zu verwenden ist, und nennt die empfohlene Datenmenge. Nicht jeder Datentyp ist zum Erstellen eines Modells erforderlich. Die Datenanforderungen sind unterschiedlich, je nachdem, ob Sie einen Test erstellen oder ein Modell trainieren.

| Datentyp | Zum Testen verwendet | Empfohlene Menge | Für Training verwendet | Empfohlene Menge |
|-----------|-----------------|----------|-------------------|----------|
| [Nur Audio](#audio-data-for-testing) | Ja<br>Zur visuellen Prüfung verwendet | Mindestens 5 Audiodateien | Nein | – |
| [Audio + menschenmarkierte Transkripte](#audio--human-labeled-transcript-data-for-trainingtesting) | Ja<br>Zur Bewertung der Genauigkeit verwendet | 0,5 – 5 Stunden Audio | Ja | 1–20 Stunden Audiodaten |
| [Nur-Text](#plain-text-data-for-training) | Nein | Nicht zutreffend | Ja | 1 – 200 MB zugehöriger Text |
| [Strukturierter Text](#structured-text-data-for-training-public-preview) (Öffentliche Vorschauversion) | Nein | Nicht zutreffend | Ja | Bis zu 10 Klassen mit bis zu 4.000 Elementen und bis zu 50.000 Trainingssätzen |
| [Aussprache](#pronunciation-data-for-training) | Nein | Nicht zutreffend | Ja | 1 KB – 1 MB Aussprachetext |

Dateien sollten nach Typ in einem Dataset gruppiert und als ZIP-Datei hochgeladen werden. Jedes Dataset darf nur einen einzelnen Datentyp enthalten.

> [!TIP]
> Wenn Sie ein neues Modell trainieren, beginnen Sie entweder mit Nur-Text-Daten oder mit strukturierten Textdaten. Diese Daten verbessern das Erkennen von speziellen Begriffen und Ausdrücken. Training mit Textdaten ist deutlich schneller als das Training mit Audiodaten (Minuten vs. Tage).

> [!NOTE]
> Nicht alle Basismodelle unterstützen das Training mit Audiodaten. Wenn es von einem Basismodell nicht unterstützt wird, verwendet der Speech-Dienst nur den Text aus den Transkriptionen und ignoriert die Audiodaten. Eine Liste mit Basismodellen, die das Training mit Audiodaten unterstützen, finden Sie unter [Sprachunterstützung](language-support.md#speech-to-text). Selbst wenn ein Basismodell das Training mit Audiodaten unterstützt, verwendet der Dienst möglicherweise nur einen Teil der Audiodaten. Dennoch werden alle Transkriptionen verwendet.
>
> Wenn Sie das für das Training verwendete Basismodell ändern und das Trainingsdataset Audiodaten enthält, überprüfen Sie *immer*, ob das neue ausgewählte Basismodell [das Training mit Audiodaten unterstützt](language-support.md#speech-to-text). Wenn das zuvor verwendete Basismodell kein Training mit Audiodaten unterstützt hat und das Trainingsdataset Audiodaten enthält, verlängert sich die Trainingsdauer mit dem neuen Basismodell **deutlich** und kann schnell einige Stunden oder sogar mehrere Tage und mehr betragen. Dies ist insbesondere dann der Fall, wenn es sich bei der Region Ihres Abonnements für den Speech-Dienst **nicht** um eine [Region mit dedizierter Hardware](custom-speech-overview.md#set-up-your-azure-account) für Training handelt.
>
> Wenn das im obigen Abschnitt beschriebene Problem auftritt, können Sie die Trainingsdauer schnell verkürzen, indem Sie die Menge der Audiodaten im Dataset verringern oder die Audiodaten ganz daraus entfernen, sodass das Dataset nur noch Text enthält. Letzteres ist sehr empfehlenswert, wenn es sich bei der Region Ihres Abonnements für den Speech-Dienst **nicht** um eine [Region mit dedizierter Hardware](custom-speech-overview.md#set-up-your-azure-account) für Training handelt.
>
> In Regionen mit dedizierter Hardware für das Training nutzt der Sprachdienst bis zu 20 Stunden Audiodaten für das Training. In anderen Regionen verwendet er nur bis zu 8 Stunden Audiodaten.

> [!NOTE]
> Das Training mit strukturiertem Text wird nur für diese Gebietsschemas unterstützt: en-US, en-UK, en-IN, de-DE, fr-FR, fr-CA, es-ES, es-MX. Außerdem müssen Sie das aktuellste Basismodell für diese Gebietsschemas verwenden. 
>
> Für Gebietsschemas, die das Training mit strukturiertem Text nicht unterstützen, nimmt der Dienst alle Trainingssätze entgegen, die im Rahmen des Trainings mit Nur-Text-Daten auf keine Klassen verweisen.

## <a name="upload-data"></a>Hochladen von Daten

Navigieren Sie zum Hochladen Ihrer Daten zu [Speech Studio](https://aka.ms/speechstudio/customspeech). Navigieren Sie nach dem Erstellen eines Projekts zur Registerkarte **Speech-Datasets**, und klicken Sie auf **Daten hochladen**, um den Assistenten zu starten und Ihr erstes Dataset zu erstellen. Wählen Sie einen Speech-Datentyp für Ihr Dataset aus, und laden Sie Ihre Daten hoch.

> [!NOTE]
> Wenn Ihre Datasetdateigröße 128 MB überschreitet, können Sie sie nur mithilfe der Option *Azure-Blob oder freigegebener Speicherort* hochladen. Sie können auch die [Spracherkennungs-REST-API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) verwenden, um ein Dataset [beliebiger zulässiger Größe](speech-services-quotas-and-limits.md#model-customization) hochzuladen. Weitere Details finden Sie im [nächsten Abschnitt](#upload-data-using-speech-to-text-rest-api-v30).

Zunächst müssen Sie angeben, ob das Dataset für **Training** oder **Testen** verwendet werden soll. Es gibt viele Arten von Daten, die hochgeladen und zum **Trainieren** oder **Testen** verwendet werden können. Jedes hochgeladene Dataset muss vor dem Upload korrekt formatiert werden und den Anforderungen für den ausgewählten Datentyp entsprechen. Die Anforderungen sind in den folgenden Abschnitten aufgelistet.

Nach dem Hochladen des Datasets haben Sie verschiedene Möglichkeiten:

* Sie können zur Registerkarte **Benutzerdefinierte Modelle trainieren** navigieren, um ein benutzerdefiniertes Modell zu trainieren.
* Sie können zur Registerkarte **Testmodelle** navigieren, um die Qualität nur mit Audiodaten visuell zu überprüfen oder die Genauigkeit mit Audio- und Humantranskriptionsdaten auszuwerten.

### <a name="upload-data-using-speech-to-text-rest-api-v30"></a>Hochladen von Daten mithilfe der Spracherkennungs-REST-API v3.0

Sie können die [Spracherkennungs-REST-API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) verwenden, um alle Vorgänge im Zusammenhang mit Ihren benutzerdefinierten Modellen zu automatisieren. Insbesondere können Sie damit ein Dataset hochladen. Dies ist besonders nützlich, wenn Ihre Datasetdatei 128 MB überschreitet, da derart große Dateien nicht mit der Option *Lokale Datei* in Speech Studio hochgeladen werden können. (Sie können für denselben Zweck auch die Option *Azure-Blob oder freigegebener Speicherort* in Speech Studio verwenden, wie im vorherigen Abschnitt beschrieben).

Verwenden Sie zum Erstellen und Hochladen eines Datasets die Anforderung [Create Dataset](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateDataset).

**Mit der REST-API erstellte Datasets und Speech Studio-Projekte**

Ein mit der Spracherkennungs-REST-API v3.0 erstelltes Dataset wird mit *keinem* der Speech Studio-Projekte verbunden, es sei denn, es wird ein spezieller Parameter im Anforderungstext angegeben (siehe unten). Die Verbindung mit einem Speech Studio-Projekt ist für *keine* Vorgänge zur Modellanpassung erforderlich, wenn sie über die REST-API durchgeführt werden.

Wenn Sie sich bei Speech Studio anmelden, werden Sie über die Benutzeroberfläche benachrichtigt, wenn ein nicht verbundenes Objekt gefunden wird (z. B. Datasets, die über die REST-API ohne Projektreferenz hochgeladen wurden), und es wird angeboten, solche Objekte mit einem bestehenden Projekt zu verbinden. 

Verwenden Sie [Create Dataset](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateDataset) (Dataset erstellen), um das neue Dataset während des Hochladens mit einem bestehenden Projekt in Speech Studio zu verbinden, und füllen Sie die Anforderung gemäß dem folgenden Format aus:
```json
{
  "kind": "Acoustic",
  "contentUrl": "https://contoso.com/mydatasetlocation",
  "locale": "en-US",
  "displayName": "My speech dataset name",
  "description": "My speech dataset description",
  "project": {
    "self": "https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/projects/c1c643ae-7da5-4e38-9853-e56e840efcb2"
  }
}
```

Die für das `project`-Element erforderliche Projekt-URL kann mit der Anforderung [Get Projects](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetProjects) (Projekte abrufen) ermittelt werden.

## <a name="audio--human-labeled-transcript-data-for-trainingtesting"></a>Audio- und Humantranskriptionsdaten für Training/Tests

Audio- und Humantranskriptionsdaten können sowohl zu Trainings- als auch zu Testzwecken verwendet werden. Um die akustischen Aspekte wie geringfügige Akzente, Sprechweisen, Hintergrundgeräusche oder die Genauigkeit der Spracherkennung von Microsoft bei der Verarbeitung Ihrer Audiodateien zu verbessern, müssen Sie Humantranskriptionen (Wort für Wort) für den Vergleich bereitstellen. Zwar ist das Erstellen einer menschenmarkierten Transkription oft sehr zeitaufwendig, doch wird sie benötigt, um die Genauigkeit zu bewerten und das Modell für Ihre Anwendungsfälle zu trainieren. Denken Sie daran, dass die Verbesserung der Erkennung nur so gut ist wie die bereitgestellten Daten. Aus diesem Grund ist es wichtig, dass nur qualitativ hochwertige Transkripte hochgeladen werden.

Audiodateien können am Anfang und am Ende der Aufzeichnung Stille aufweisen. Schließen Sie nach Möglichkeit in jeder Beispieldatei mindestens eine halbe Sekunde Stille vor und nach Sprache ein. Auch wenn Audiodaten mit geringer Aufzeichnungslautstärke oder störenden Hintergrundgeräuschen nicht hilfreich sind, sollte dies keine negativen Auswirkungen auf Ihr benutzerdefiniertes Modell haben. Sie sollten immer ein Upgrade Ihrer Mikrofone und Signalverarbeitungshardware in Erwägung ziehen, bevor Sie Audiostichproben sammeln.

| Eigenschaft                 | Wert                               |
|--------------------------|-------------------------------------|
| Dateiformat              | RIFF (WAV)                          |
| Samplingrate              | 8\.000 Hz oder 16.000 Hz               |
| Kanäle                 | 1 (Mono)                            |
| Maximale Länge pro Audioaufnahme | 2 Stunden (Tests)/60 s (Training) |
| Beispielformat            | PCM, 16 Bit                         |
| Archivierungsformat           | .zip                                |
| Maximale ZIP-Dateigröße         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Beim Hochladen von Trainings- und Testdaten darf die ZIP-Datei maximal 2 GB groß sein. Sie können nur aus *einem* Dataset testen. Achten Sie darauf, dass dieses die geeignete Dateigröße einhält. Darüber hinaus dürfen die einzelnen Trainingsdateien 60 Sekunden nicht überschreiten, andernfalls tritt ein Fehler auf.

In Hinsicht auf Probleme wie die Löschung oder Ersetzung von Wörtern sind erhebliche Datenmengen erforderlich, um die Erkennung zu verbessern. Im Allgemeinen wird empfohlen, wortwörtliche Transkriptionen für 1 bis 20 Stunden Audiodaten bereitzustellen. Aber auch schon 30 Minuten können helfen, die Erkennungsergebnisse zu verbessern. Die Transkriptionen für sämtliche WAV-Dateien sollten in einer einzelnen Textdatei enthalten sein. Jede Zeile der Transkriptionsdatei muss den Namen einer der Audiodateien enthalten – gefolgt von der jeweiligen Transkription. Der Dateiname und die Transkription sollten durch ein Tabulatorzeichen (\t) getrennt werden.

Beispiel:

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> Die Transkription muss als UTF-8-Bytereihenfolge-Marke (byte order mark, BOM) codiert sein.

Der Text der Transkriptionen wird normalisiert, damit diese vom System verarbeitet werden können. Einige wichtige Normalisierungen müssen jedoch noch vor dem Hochladen der Daten in Speech Studio vorgenommen werden. Informationen dazu, welche Sprache bei der Vorbereitung Ihrer Transkriptionen verwendet werden muss, finden Sie unter [Erstellen einer menschenmarkierten Transkription](how-to-custom-speech-human-labeled-transcriptions.md).

Wenn Sie die Audiodateien und die entsprechenden Transkriptionen zusammengetragen haben, packen Sie sie in eine gemeinsame ZIP-Datei, bevor Sie sie in <a href="https://speech.microsoft.com/customspeech" target="_blank">Speech Studio</a> hochladen. Im Folgenden sehen Sie ein Beispieldataset mit drei Audiodateien und einer von einer Person markierten Transkriptionsdatei:

> [!div class="mx-imgBorder"]
> ![Auswählen von Audio im Speech-Portal](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Unter [Einrichten Ihres Azure-Kontos](custom-speech-overview.md#set-up-your-azure-account) finden Sie eine Liste der empfohlenen Regionen für Ihre Speech-Dienstabonnements. Durch das Einrichten des Speech-Abonnements in einer dieser Regionen verringert sich die für das Trainieren des Modells erforderliche Zeit. In diesen Regionen können beim Training zehn Stunden an Audiodaten pro Tag im Vergleich zu gerade einmal einer Stunde pro Tag in anderen Regionen verarbeitet werden. Wenn das Modelltraining nicht innerhalb einer Woche abgeschlossen werden kann, wird für das Modell ein Fehler angezeigt.

Nicht alle Basismodelle unterstützen das Training mit Audiodaten. Wenn das Basismodell das Training mit Audiodaten nicht unterstützt, ignoriert der Dienst die Audiodaten und nutzt nur den Text der Transkriptionen für das Training. In diesem Fall ist das Training identisch mit dem Training mit zugehörigem Text. Eine Liste mit Basismodellen, die das Training mit Audiodaten unterstützen, finden Sie unter [Sprachunterstützung](language-support.md#speech-to-text).

## <a name="plain-text-data-for-training"></a>Nur-Text-Daten für das Training

Sie können domänenbezogene Sätze verwenden, um die Genauigkeit bei der Erkennung von Produktnamen oder branchenspezifischem Fachjargon zu verbessern. Stellen Sie Sätze in einer einzelnen Textdatei zur Verfügung. Um die Genauigkeit zu erhöhen, verwenden Sie Textdaten, die näher an den erwarteten gesprochenen Äußerungen liegen. 

Das Training mit Nur-Text kann in der Regel innerhalb weniger Minuten abgeschlossen werden.

Um ein benutzerdefiniertes Modell mithilfe von Sätzen zu erstellen, müssen Sie eine Liste von Beispieläußerungen bereitstellen. Diese Äußerungen müssen _keine_ vollständigen oder grammatikalisch korrekten Sätze sein, doch sie müssen der gesprochenen Eingabe, die Sie in der Produktionsumgebung erwarten, genau entsprechen. Wenn bestimmte Begriffe eine höhere Gewichtung haben sollen, fügen Sie mehrere Sätze hinzu, die diese Begriffe enthalten.

Als allgemeine Richtlinie gilt: Die Modellanpassung ist am effektivsten, wenn der Trainingstext so nah wie möglich an dem in der Produktion zu erwartenden Text liegt. Themenspezifischer Jargon und Ausdrücke, die Sie ergänzen möchten, sollten im Trainingstext enthalten sein. Versuchen Sie nach Möglichkeit, einen Satz oder ein Schlüsselwort auf einer separaten Zeile einzufügen. Schlüsselwörter und Ausdrücke, die für Sie wichtig sind (z. B. Produktnamen), können Sie mehrmals kopieren. Achten Sie jedoch darauf, nicht zu viel zu kopieren, da sich dies auf die Gesamterkennungsrate auswirken kann.

Stellen Sie anhand der folgenden Tabelle sicher, dass die zugehörige Datendatei für Äußerungen richtig formatiert ist:

| Eigenschaft | Wert |
|----------|-------|
| Textcodierung | UTF-8 BOM |
| Anzahl von Äußerungen pro Zeile | 1 |
| Maximale Dateigröße | 200 MB |

Darüber hinaus sollten Sie die folgenden Einschränkungen beachten:

* Vermeiden Sie es, mehr als dreimal Zeichen, Wörter oder Wortgruppen zu wiederholen. Zum Beispiel: „aaaa“, „yeah yeah yeah yeah“ oder „that's it that's it that's it that's it“. Der Sprachdienst kann Zeilen mit zu vielen Wiederholungen löschen.
* Verwenden Sie keine Sonderzeichen oder UTF-8-Zeichen über `U+00A1`.
* URIs werden zurückgewiesen.
* Bei einigen Sprachen (z. B. Japanisch oder Koreanisch) kann das Importieren großer Mengen von Textdaten sehr lange dauern oder ein Timeout verursachen. Erwägen Sie, die hochgeladenen Daten in Textdateien mit jeweils bis zu 20.000 Zeilen zu unterteilen.

## <a name="structured-text-data-for-training-public-preview"></a>Strukturierte Textdaten für das Training (Öffentliche Vorschauversion)

Häufig folgen die erwarteten Äußerungen einem bestimmten Muster. Ein gängiges Muster ist z. B., dass Äußerungen sich nur durch bestimmte Wörter oder Ausdrücke aus einer Liste unterscheiden. Beispiele hierfür sind: „Ich habe eine Frage zu `product`“, wobei `product` eine Liste möglicher Produkte ist. Oder: „Färbe das `object` `color`“, wobei `object` eine Liste mit geometrischen Formen und `color` eine Liste mit Farben ist. Um die Erstellung von Trainingsdaten zu vereinfachen und eine bessere Modellierung innerhalb des benutzerdefinierten Sprachmodells zu ermöglichen, können Sie einen strukturierten Text im Markdown-Format verwenden, um Listen mit Elementen zu definieren und diese dann in Ihren Trainings-Äußerungen zu referenzieren. Das Markdown-Format unterstützt darüber hinaus auch die Angabe der phonetischen Aussprache von Wörtern. Die Markdowndatei sollte über die Erweiterung `.md` verfügen. Die Syntax des Markdowns ist mit der Syntax der Language Understanding-Modelle (insbesondere der Listenentitäten und Beispieläußerungen) identisch. Weitere Informationen zur vollständigen Markdownsyntax finden Sie im Artikel zum <a href="/azure/bot-service/file-format/bot-builder-lu-file-format" target="_blank">Language Understanding-Markdown</a>.

Im Folgenden finden Sie ein Beispiel für das Markdown-Format:

```markdown
// This is a comment

// Here are three separate lists of items that can be referenced in an example sentence. You can have up to 10 of these
@ list food =
- pizza
- burger
- ice cream
- soda

@ list pet =
- cat
- dog

@ list sports =
- soccer
- tennis
- cricket
- basketball
- baseball
- football

// This is a list of phonetic pronunciations. 
// This adjusts the pronunciation of every instance of these word in both a list or example training sentences 
@ speech:phoneticlexicon
- cat/k ae t
- cat/f i l ai n

// Here are example training sentences. They are grouped into two sections to help organize the example training sentences.
// You can refer to one of the lists we declared above by using {@listname} and you can refer to multiple lists in the same training sentence
// A training sentence does not have to refer to a list.
# SomeTrainingSentence
- you can include sentences without a class reference
- what {@pet} do you have
- I like eating {@food} and playing {@sports}
- my {@pet} likes {@food}

# SomeMoreSentence
- you can include more sentences without a class reference
- or more sentences that have a class reference like {@pet} 
```

Das Training mit strukturiertem Text dauert in der Regel einige Minuten, so wie das Training mit Nur-Text. Außerdem sollten Ihre Beispielsätze und Listen die Art der gesprochenen Eingabe widerspiegeln, die Sie in der Produktionsumgebung erwarten.
Informationen zu Ausspracheeinträgen finden Sie in der Beschreibung des [Universellen Phonemsatzes](phone-sets.md).

In der folgenden Tabelle sind die Einschränkungen und andere Eigenschaften des Markdown-Formats aufgelistet:

| Eigenschaft | Wert |
|----------|-------|
| Textcodierung | UTF-8 BOM |
| Maximale Dateigröße | 200 MB |
| Maximale Anzahl von Beispielsätzen | 50.000 |
| Maximale Anzahl von Listenklassen | 10 |
| Maximale Anzahl von Elementen in einer Listenklasse | 4\.000 |
| Maximale Anzahl von speech:phoneticlexicon-Einträgen | 15000 |
| Maximale Anzahl von Aussprachen pro Wort | 2 |


## <a name="pronunciation-data-for-training"></a>Aussprachedaten für das Training

Wenn die Benutzer auf selten vorkommende Begriffe ohne Standardaussprache treffen oder diese verwenden werden, können Sie eine benutzerdefinierte Aussprachedatei bereitstellen, um die Erkennung zu verbessern. Eine Liste der Sprachen, die benutzerdefinierte Aussprache unterstützen, finden Sie unter **Aussprache** in der Spalte **Anpassungen** in der [Spracherkennungstabelle](language-support.md#speech-to-text).

> [!IMPORTANT]
> Es wird davon abgeraten, benutzerdefinierte Aussprachedateien zu verwenden, um die Aussprache gebräuchlicher Wörter zu ändern.

> [!NOTE]
> Diese Art von Aussprachedateien können nicht mit Trainingsdaten aus strukturiertem Text kombiniert werden. Verwenden Sie für strukturierte Textdaten die phonetische Aussprachefunktion, die im Markdown-Format für strukturierten Text enthalten ist.

Stellen Sie die Aussprache in einer einzelnen Textdatei bereit. Hier sehen Sie Beispiele für eine gesprochene Äußerung und die jeweilige benutzerdefinierte Aussprache:

| Erkannte/angezeigte Form | Gesprochene Form |
|--------------|--------------------------|
| 3CPO | drei c p o |
| CNTK | c n t k |
| IEEE | i doppel e |

Die gesprochene Form ist die jeweilige Lautfolge. Sie kann aus Buchstaben, Wörtern, Silben oder einer Kombination aus allen drei bestehen.

Stellen Sie anhand der folgenden Tabelle sicher, dass die zugehörige Datendatei für Aussprache richtig formatiert ist. Aussprachedateien sind klein und sollten einige KB nicht übersteigen.

| Eigenschaft | Wert |
|----------|-------|
| Textcodierung | UTF-8 BOM (ANSI wird für Englisch ebenfalls unterstützt) |
| Anzahl der Aussprachen pro Zeile | 1 |
| Maximale Dateigröße | 1 MB (1 KB für Free-Tarif) |

## <a name="audio-data-for-testing"></a>Audiodaten für Tests

Audiodaten eignen sich optimal, um die Genauigkeit des Microsoft-Basismodells für die Spracherkennung oder eines benutzerdefinierten Modells zu testen. Denken Sie daran, dass mit Audiodaten die Genauigkeit der Spracherkennung im Hinblick auf die Leistung eines bestimmten Modells überprüft wird. Wenn Sie die Genauigkeit eines Modells bemessen möchten, verwenden Sie [Audio- und Humantranskriptionsdaten](#audio--human-labeled-transcript-data-for-trainingtesting).

Custom Speech erfordert Audiodateien mit den folgenden Eigenschaften:

| Eigenschaft                 | Wert                 |
|--------------------------|-----------------------|
| Dateiformat              | RIFF (WAV)            |
| Samplingrate              | 8\.000 Hz oder 16.000 Hz |
| Kanäle                 | 1 (Mono)              |
| Maximale Länge pro Audioaufnahme | 2 Stunden               |
| Beispielformat            | PCM, 16 Bit           |
| Archivierungsformat           | .zip                  |
| Maximale Archivgröße     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Beim Hochladen von Trainings- und Testdaten darf die ZIP-Datei maximal 2 GB groß sein. Sollten Sie mehr Daten zum Trainieren benötigen, teilen Sie sie auf mehrere ZIP-Dateien auf, und laden Sie sie separat hoch. Später können Sie auswählen, dass Sie *mehrere* Datasets zum Trainieren verwenden möchten. Sie können jedoch nur aus *einem* Dataset testen.

Verwenden Sie <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX</a>, um Audioeigenschaften zu überprüfen oder vorhandene Audiodaten in die entsprechenden Formate zu konvertieren. Im Folgenden sind einige SoX-Beispielbefehle aufgeführt:

| Aktivität | SoX-Befehl |
|---------|-------------|
| Überprüfen des Dateiformats der Audiodaten | `sox --i <filename>` |
| Konvertieren der Audiodatei in einen einzelnen Kanal mit 16 Bit und 16 kHz | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="next-steps"></a>Nächste Schritte

* [Überprüfen Ihrer Daten](how-to-custom-speech-inspect-data.md)
* [Bewerten Ihrer Daten](how-to-custom-speech-evaluate-data.md)
* [Trainieren eines benutzerdefinierten Modells](how-to-custom-speech-train-model.md)
* [Bereitstellen des Modells](./how-to-custom-speech-train-model.md)
