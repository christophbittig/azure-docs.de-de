---
title: 'Schlüsselworterkennung: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Ein Überblick über die Features, Funktionen und Einschränkungen für die Schlüsselwörterkennung, die das Speech Software Development Kit (SDK) verwenden.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: a91f3e6e59647d6817f05137e284fbee4400dbd8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491786"
---
# <a name="keyword-recognition"></a>Schlüsselworterkennung

Die Schlüsselworterkennung erkennt ein Wort oder einen kurzen Ausdruck innerhalb eines Audiodatenstroms. Dies wird auch als Schlüsselwortsuche bezeichnet. 

Der häufigste Anwendungsfall der Schlüsselworterkennung ist die Sprachaktivierung virtueller Assistenten. „Hey Cortana“ ist z. B. das Schlüsselwort für den Cortana-Assistenten. Bei Erkennung des Schlüsselworts wird eine szenariospezifische Aktion ausgeführt. Bei Szenarien mit virtuellen Assistenten ist eine häufige resultierende Aktion die Spracherkennung von Audioinformationen, die auf das Schlüsselwort folgen.

Im Allgemeinen lauschen virtuelle Assistenten immer. Die Schlüsselworterkennung dient als Datenschutzgrenze für den Benutzer. Eine Schlüsselwortanforderung fungiert als Tor, das verhindert, dass zusammenhangslose Audioinformationen des Benutzers vom lokalen Gerät in die Cloud gelangen.

Um ein Gleichgewicht zwischen Genauigkeit, Wartezeit und Berechnungskomplexität zu erreichen, wird die Schlüsselworterkennung als mehrstufiges System implementiert. Für alle Stufen, die über die erste hinausgehen, werden Audiodaten nur dann verarbeitet, wenn die vorherige Stufe das betreffende Schlüsselwort erkannt zu haben glaubt.

Das aktuelle System ist mit mehreren Stufen konzipiert, die sich über Edge und Cloud erstrecken:

![Mehrere Stufen der Schlüsselworterkennung über Edge und Cloud.](media/custom-keyword/kw-recognition-multi-stage.png)

Die Genauigkeit der Schlüsselworterkennung wird anhand der folgenden Metriken gemessen:
* **Akzeptanzrate für richtige Begriffe** – Misst die Fähigkeit des Systems, das Schlüsselwort zu erkennen, wenn es von einem Endbenutzer gesprochen wird. Dies wird auch als True Positive-Rate bezeichnet. 
* **Akzeptanzrate für falsche Begriffe** – Misst die Fähigkeit des Systems, Audiodaten herauszufiltern, die nicht das von einem Benutzer gesprochene Schlüsselwort sind. Dies wird auch als False Positive-Rate bezeichnet.

Ziel ist es, die Akzeptanzrate für richtige Begriffe zu maximieren und gleichzeitig die Akzeptanzrate für falsche Begriffe zu minimieren. Das aktuelle System ist darauf ausgelegt, ein Schlüsselwort oder einen Ausdruck zu erkennen, dem eine kurze Zeit der Stille vorausgeht. Die Erkennung eines Schlüsselworts in der Mitte eines Satzes oder einer Äußerung wird nicht unterstützt.

## <a name="custom-keyword-for-on-device-models"></a>Benutzerdefiniertes Schlüsselwort für gerätebasierte Modelle

Das [Portal „Benutzerdefiniertes Schlüsselwort“ in Speech Studio](https://speech.microsoft.com/customkeyword) ermöglicht es Ihnen, durch die Angabe eines beliebigen Worts oder eines kurzen Ausdrucks Modelle für die Erkennung von Schlüsselwörtern zu generieren, die am Edge ausgeführt werden. Sie können Ihr Schlüsselwortmodell weiter personalisieren, indem Sie die richtigen Aussprachen auswählen.

### <a name="pricing"></a>Preise

Es entstehen keine Kosten, wenn Sie „Benutzerdefiniertes Schlüsselwort“ für die Erstellung von Modellen verwenden, einschließlich der einfachen und erweiterten Modelle. Es entstehen auch keine Kosten für die Ausführung von gerätebasierten Modellen mit dem Speech SDK.

### <a name="types-of-models"></a>Typen von Modellen

Mit „Benutzerdefiniertes Schlüsselwort“ können Sie zwei Typen von gerätebasierten Modellen für jedes beliebige Schlüsselwort generieren.

| Modelltyp | BESCHREIBUNG |
| ---------- | ----------- |
| Basic | Am besten geeignet für Demos oder die schnelle Erstellung von Prototypen. Die Modelle werden mit einem gemeinsamen Basismodell generiert und können bis zu 15 Minuten in Anspruch nehmen, bis sie bereit sind. Die Modelle weisen möglicherweise keine optimalen Genauigkeitsmerkmale auf. |
| Fortgeschrittene | Am besten für die Produktintegration geeignet. Die Modelle werden durch Anpassung eines gemeinsamen Basismodells unter Verwendung simulierter Trainingsdaten generiert, um die Genauigkeitsmerkmale zu verbessern. Es kann bis zu 48 Stunden dauern, bis die Modelle bereit sind. |

> [!NOTE]
> Eine Liste der Regionen, die den Modelltyp **Erweitert** unterstützen, finden Sie in der Dokumentation zur [Unterstützung von Regionen für die Schlüsselworterkennung](keyword-recognition-region-support.md). 

Keiner der Modelltypen erfordert, dass Sie Trainingsdaten hochladen. „Benutzerdefiniertes Schlüsselwort“ übernimmt die komplette Datengenerierung und das Modelltraining.

### <a name="pronunciations"></a>Aussprache

Beim Erstellen eines neuen Modells generiert „Benutzerdefiniertes Schlüsselwort“ automatisch mögliche Aussprachen des angegebenen Schlüsselworts. Sie können den einzelnen Aussprachen lauschen und alle auswählen, die am ehesten die Art und Weise repräsentieren, wie Sie erwarten, dass Endbenutzer das Schlüsselwort aussprechen. Alle anderen Aussprachen sollten nicht ausgewählt werden.

Es ist wichtig, die Aussprache bewusst zu wählen, um die besten Genauigkeitsmerkmale sicherzustellen. Die Auswahl von mehr Aussprachen als nötig kann z. B. zu höheren Akzeptanzraten für falsche Begriffe führen. Die Auswahl von zu wenigen Aussprachen, bei denen nicht alle erwarteten Variationen abgedeckt sind, kann zu niedrigeren Akzeptanzraten für richtige Begriffe führen.

### <a name="testing-models"></a>Testen von Modellen

Nachdem gerätebasierte Modelle von „Benutzerdefiniertes Schlüsselwort“ generiert wurden, können sie direkt im Portal getestet werden. Im Portal können Sie direkt in Ihren Browser sprechen und Ergebnisse der Schlüsselworterkennung abrufen.

## <a name="keyword-verification"></a>Schlüsselwortüberprüfung

Bei der Schlüsselwortüberprüfung handelt es sich um einen Clouddienst, der die Auswirkungen der Annahme falscher Begriffe von gerätebasierten Modellen mit stabilen Modellen reduziert, die in Azure ausgeführt werden. Damit die Schlüsselwortüberprüfung mit Ihrem Schlüsselwort funktioniert, sind weder Optimierung noch Training erforderlich. Inkrementelle Modellupdates werden kontinuierlich für den Dienst bereitgestellt, um die Genauigkeit und Wartezeit zu verbessern. Dies ist für Clientanwendungen vollständig transparent.

### <a name="pricing"></a>Preise

Die Schlüsselwortüberprüfung wird immer in Kombination mit der Spracherkennung verwendet, und es sind über die Kosten für die Spracherkennung hinaus keine weiteren Kosten für die Verwendung der Schlüsselwortüberprüfung erforderlich.

### <a name="keyword-verification-and-speech-to-text"></a>Schlüsselwortüberprüfung und Spracherkennung

Wenn die Schlüsselwortüberprüfung verwendet wird, erfolgt dies immer in Kombination mit der Spracherkennung. Beide Dienste werden parallel ausgeführt. Dies bedeutet, dass Audiodaten zur gleichzeitigen Verarbeitung an beide Dienste gesendet werden.

![Parallele Verarbeitung von Schlüsselwortüberprüfung und Spracherkennung](media/custom-keyword/kw-verification-parallel-processing.png)

Die parallele Ausführung von Schlüsselwortüberprüfung und Spracherkennung bietet die folgenden Vorteile:
* **Keine zusätzliche Wartezeit bei Spracherkennungsergebnissen** – Parallele Ausführung bedeutet, dass die Schlüsselwortüberprüfung keine Wartezeit hinzufügt, und der Client empfängt die auf der Spracherkennung basierenden Ergebnisse genauso schnell. Wenn die Schlüsselwortüberprüfung feststellt, dass das Schlüsselwort in den Audiodaten nicht vorhanden war, wird die Spracherkennungsverarbeitung beendet, was vor unnötiger Verarbeitung durch die Spracherkennung schützt. Die Verarbeitung von Netzwerk- und Cloudmodellen erhöht jedoch die vom Benutzer wahrgenommene Wartezeit der Sprachaktivierung. Weitere Informationen finden Sie unter [Empfehlungen und Richtlinien](keyword-recognition-guidelines.md).
* **Erzwungenes Schlüsselwortpräfix in Spracherkennungsergebnissen** – Die Spracherkennungsverarbeitung sorgt dafür, dass den an den Client gesendeten Ergebnissen das Schlüsselwort vorangestellt wird. Dies ermöglicht eine höhere Genauigkeit in den Spracherkennungsergebnissen für Sprache, die auf das Schlüsselwort folgt.
* **Erhöhtes Spracherkennungstimeout** – Aufgrund des erwarteten Vorhandenseins des Schlüsselworts am Anfang der Audiodaten erlaubt die Spracherkennung eine längere Pause von bis zu fünf Sekunden nach dem Schlüsselwort, bevor das Ende der Sprache bestimmt und die Spracherkennungsverarbeitung beendet wird. Dadurch wird sichergestellt, dass der Endbenutzer sowohl bei gestaffelten Befehlen ( *\<keyword> \<pause> \<command>* ) als auch bei verketteten Befehlen ( *\<keyword> \<command>* ) ordnungsgemäß behandelt wird. 

### <a name="keyword-verification-responses-and-latency-considerations"></a>Überlegungen zu Antworten und Wartezeit bei der Schlüsselwortüberprüfung

Für jede Anforderung an den Dienst gibt die Schlüsselwortüberprüfung eine von zwei Antworten zurück: „Accepted“ (Akzeptiert) oder „Rejected“ (Abgelehnt). Die Verarbeitungswartezeit variiert in Abhängigkeit von der Länge des Schlüsselworts und der Länge des Audiosegments, das das Schlüsselwort enthalten soll. Die Verarbeitungswartezeit umfasst nicht die Netzwerkkosten zwischen dem Client und den Azure Speech-Diensten.

| Antwort der Schlüsselwortüberprüfung | BESCHREIBUNG |
| ----------------------------- | ----------- |
| Akzeptiert | Zeigt an, dass der Dienst davon ausgeht, dass das Schlüsselwort in dem als Teil der Anforderung bereitgestellten Audiodatenstrom vorhanden ist. |
| Rejected (Abgelehnt) | Zeigt an, dass der Dienst davon ausgeht, dass das Schlüsselwort nicht in dem als Teil der Anforderung bereitgestellten Audiodatenstrom vorhanden ist. |

Abgelehnte Fälle führen häufig zu höheren Wartezeiten, da der Dienst mehr Audiodaten verarbeitet als akzeptierte Fälle. Standardmäßig verarbeitet die Schlüsselwortüberprüfung maximal zwei Sekunden an Audiodaten, um nach dem Schlüsselwort zu suchen. Wird festgestellt, dass das Schlüsselwort in den zwei Sekunden nicht vorhanden ist, tritt beim Dienst ein Timeout auf und er signalisiert dem Client als Antwort eine Ablehnung.

### <a name="using-keyword-verification-with-on-device-models-from-custom-keyword"></a>Verwenden der Schlüsselwortüberprüfung mit gerätebasierten Modellen aus „Benutzerdefiniertes Schlüsselwort“

Das Speech SDK ermöglicht die problemlose Verwendung von gerätebasierten Modellen, die mithilfe von „Benutzerdefiniertes Schlüsselwort“ mit Schlüsselwortüberprüfung und Spracherkennung erstellt wurden. Es verarbeitet Folgendes auf transparente Weise:
* Audiozulassung zur Schlüsselwortüberprüfung und Spracherkennung basierend auf dem Ergebnis des gerätebasierten Modells.
* Kommunizieren des Schlüsselworts an den Dienst für die Schlüsselwortüberprüfung.
* Kommunizieren zusätzlicher Metadaten an die Cloud für die Orchestrierung des End-to-End-Szenarios. 

Sie müssen keine Konfigurationsparameter explizit angeben. Alle erforderlichen Informationen werden automatisch aus dem von „Benutzerdefiniertes Schlüsselwort“ generierten gerätebasierten Modell extrahiert.

In den unten verlinkten Beispielen und Tutorials wird die Verwendung des Speech SDK veranschaulicht:
 * [Beispiele für Sprachassistenten auf GitHub](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
 * [Tutorial: Aktivieren der Sprachsteuerung für Ihren Assistenten, der mit Azure Bot Service mit dem C# Speech SDK erstellt wurde](./tutorial-voice-enable-your-bot-speech-sdk.md)
 * [Tutorial: Erstellen einer Anwendung für benutzerdefinierte Befehle mit einfachen Sprachbefehlen](./how-to-develop-custom-commands-application.md)

## <a name="speech-sdk-integration-and-scenarios"></a>Speech SDK: Integration und Szenarien

Das Speech SDK erleichtert die einfache Verwendung personalisierter gerätebasierter Modelle zur Schlüsselworterkennung, die mit „Benutzerdefiniertes Schlüsselwort“ und dem Schlüsselwortüberprüfungsdienst generiert wurden. Um sicherzustellen, dass Ihre Produktanforderungen erfüllt werden können, unterstützt das SDK zwei Szenarien:

| Szenario | BESCHREIBUNG | Beispiele |
| -------- | ----------- | ------- |
| End-to-End-Schlüsselworterkennung mit Spracherkennung | Am besten geeignet für Produkte, die ein angepasstes gerätebasiertes Schlüsselwortmodell von „Benutzerdefiniertes Schlüsselwort“ mit den Azure Speech-Diensten zur Schlüsselwortüberprüfung und Spracherkennung verwenden. Dies ist das häufigste Szenario. | <ul><li>[Beispielcode für Sprach-Assistenten](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)</li><li>[Tutorial: Aktivieren der Sprachsteuerung für Ihren Assistenten, der mit Azure Bot Service mit dem C# Speech SDK erstellt wurde](./tutorial-voice-enable-your-bot-speech-sdk.md)</li><li>[Tutorial: Erstellen einer Anwendung für benutzerdefinierte Befehle mit einfachen Sprachbefehlen](./how-to-develop-custom-commands-application.md)</li></ul> |
| Offlineschlüsselworterkennung | Am besten für Produkte ohne Netzwerkkonnektivität geeignet, die ein angepasstes gerätebasiertes Schlüsselwortmodell von „Benutzerdefiniertes Schlüsselwort“ verwenden. | <ul><li>[Beispiel für C# unter Windows (UWP)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)</li><li>[Beispiel für Java unter Android](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)</li></ul>

## <a name="next-steps"></a>Nächste Schritte

* [Lesen Sie die Schnellstartanleitung, um gerätebasierte Schlüsselworterkennungsmodelle mithilfe von „Benutzerdefiniertes Schlüsselwort“ zu generieren.](custom-keyword-basics.md)
* [Weitere Informationen zu Sprach-Assistenten](voice-assistants.md)
