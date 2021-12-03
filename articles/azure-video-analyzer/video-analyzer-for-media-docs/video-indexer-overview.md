---
title: Was ist Azure Video Analyzer for Media (ehemals Video Indexer)?
titleSuffix: Azure Video Analyzer for Media
description: Dieser Artikel bietet eine Übersicht über den Dienst Azure Video Analyzer for Media (ehemals Video Indexer).
ms.topic: overview
ms.date: 07/15/2021
ms.author: juliako
ms.openlocfilehash: fddcb1cd3562fde14c38c68811b7272a7d710d2b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487525"
---
# <a name="what-is-video-analyzer-for-media"></a>Was ist Video Analyzer for Media?

[!INCLUDE [regulation](./includes/regulation.md)]

Azure Video Analyzer for Media (früher Video Indexer) ist eine Cloudanwendung, die Teil von Azure Applied AI Services ist und auf Azure Media Services und Azure Cognitive Services (z. B. Gesichtserkennung, Textübersetzung, maschinelles Sehen und Speech) basiert. Sie ermöglicht Ihnen, mithilfe der Video- und Audiomodelle von Video Analyzer for Media Erkenntnisse aus Ihren Videos zu gewinnen.

Um mit dem Extrahieren von Erkenntnissen mit Video Analyzer for Media zu beginnen, müssen Sie ein Konto erstellen und Videos hochladen. Wenn Sie Ihre Videos zu Video Analyzer for Media hochladen, analysiert es durch Ausführen verschiedener KI-Modelle sowohl visuelle als auch akustische Informationen. Während Video Analyzer for Media Ihr Video analysiert, werden die Erkenntnisse von den KI-Modellen extrahiert.

Wenn Sie ein Video Analyzer for Media-Konto erstellen und mit Media Services verbinden, werden die Medien- und Metadatendateien in dem Azure-Speicherkonto gespeichert, das mit diesem Media Services-Konto verknüpft ist. Weitere Informationen finden Sie im Artikel zum [Erstellen eines Video Analyzer for Media-Kontos mit Azure-Verbindung](connect-to-azure.md).

Das folgende Diagramm ist eine Darstellung und keine technische Erläuterung, wie Video Analyzer for Media im Back-End funktioniert.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/video-indexer-overview/model-chart.png" alt-text="Video Analyzer for Media-Flussdiagramm":::

## <a name="compliance-privacy-and-security"></a>Compliance, Datenschutz und Sicherheit

Wichtig: Ihre Nutzung von Video Analyzer for Media darf nicht gegen geltende Gesetze verstoßen, und weder Video Analyzer for Media noch ein Azure-Dienst darf auf eine Weise verwendet werden, die die Rechte Dritter verletzt oder für Dritte schädlich ist.

Vor dem Upload eines Videos oder Bilds in Video Analyzer for Media müssen Sie über alle entsprechenden Berechtigungen zur Verwendung des Videos/Bilds verfügen, einschließlich – sofern gesetzlich erforderlich – alle notwendigen Zustimmungen von Einzelpersonen (falls vorhanden), die auf dem Video/Bild zu sehen sind, zur Nutzung, Verarbeitung und Speicherung ihrer Daten in Video Analyzer for Media und Azure. Je nach Rechtsprechung können besondere rechtliche Anforderungen für die Sammlung, Onlineverarbeitung und Speicherung bestimmter Datenkategorien, z. B. biometrische Daten, gelten. Stellen Sie Konformität mit allen rechtlichen Anforderungen sicher, die möglicherweise für Sie gelten, bevor Sie Video Analyzer for Media und Azure für die Verarbeitung und Speicherung von Daten verwenden, die bestimmten rechtlichen Anforderungen unterliegen.

Informationen zu Compliance, Datenschutz und Sicherheit in Video Analyzer for Media finden Sie im [Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx) von Microsoft. Informationen zu den Datenschutzauflagen und zur Behandlung Ihrer Daten sowie Datenaufbewahrungsmethoden durch Microsoft, einschließlich Informationen zur Löschung Ihrer Daten, finden Sie in den [Datenschutzbestimmungen](https://privacy.microsoft.com/PrivacyStatement), in den [Lizenzbedingungen für Onlinedienste](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) (Online Services Terms, OST) und im [Nachtrag zur Datenverarbeitung](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (Data Processing Addendum, DPA) von Microsoft. Wenn Sie Video Analyzer for Media nutzen, erklären Sie sich damit einverstanden, dass Sie OST, DPA und den Datenschutzbestimmungen unterliegen.

## <a name="what-can-i-do-with-video-analyzer-for-media"></a>Welche Möglichkeiten bietet mir Video Analyzer for Media?

Die Erkenntnisse von Video Analyzer for Media können auf viele Szenarien angewendet werden, darunter auch die Folgenden:

* *Intensivsuche*: Verwenden Sie die aus dem Video extrahierten Erkenntnisse, um die Suchfunktion für eine Videobibliothek zu verbessern. Beispielsweise kann die Indizierung von gesprochenem Text und Gesichtern die Suche nach Stellen in einem Video ermöglichen, in denen eine Person bestimmte Wörter benutzt hat oder zwei Personen zusammen gezeigt werden. Die Suche auf Grundlage solcher Erkenntnisse ist für Nachrichtenagenturen, Bildungseinrichtungen, Rundfunkanstalten, Besitzer von Unterhaltungsinhalten, Branchenanwendungen und im Allgemeinen für alle Branchen von Nutzen, die über eine Videobibliothek verfügen, in der Benutzer eine Suche durchführen müssen.
* *Inhaltserstellung*: Erstellen Sie Trailer, Highlightkompilationen, Inhalte von sozialen Medien oder Nachrichtenclips basierend auf den Erkenntnissen, die Video Analyzer for Media aus Ihren Inhalten extrahiert. Keyframes, Szenenmarkierungen und Zeitstempel für die Personen- und Bezeichnungsdarstellungen gestalten den Erstellungsprozess viel reibungsloser und einfacher und ermöglichen es Ihnen, zu den Teilen des Videos zu gelangen, die Sie für die von Ihnen erstellten Inhalte benötigen.
* *Barrierefreiheit*: Unabhängig davon, ob Sie Ihre Inhalte für Menschen mit Behinderungen zur Verfügung stellen möchten oder ob Sie wünschen, dass Ihre Inhalte in verschiedenen Regionen mit verschiedenen Sprachen verteilt werden, können Sie die Transkription und Übersetzung nutzen, die von Video Analyzer for Media in mehreren Sprachen bereitgestellt wird.
* *Monetarisierung*: Video Analyzer for Media kann Sie dabei unterstützen, den kommerziellen Wert von Videos zu steigern. Beispielsweise können Unternehmen aus Branchen, die auf Werbeeinnahmen angewiesen sind (Nachrichtenmedien, soziale Medien usw.) wirkungsvolle Anzeigen schalten, indem sie die extrahierten Erkenntnisse als zusätzliche Signale für den Anzeigenserver nutzen.
* *Inhaltsmoderation*: Verwenden Sie textbasierte und visuelle Moderationsmodelle, um Ihre Benutzer vor unangemessenen Inhalten zu schützen und zu überprüfen, ob die von Ihnen veröffentlichten Inhalte den Werten Ihrer Organisation entsprechen. Sie können bestimmte Videos automatisch blockieren oder Ihre Benutzer vor dem Inhalt warnen.
* *Empfehlungen*: Die aus Videos gewonnenen Erkenntnisse können genutzt werden, um die Kundenbindung zu verbessern, indem für die Benutzer relevante Stellen in einem Video hervorgehoben werden. Indem Sie jedes Video mit zusätzlichen Metadaten kennzeichnen, können Sie den Benutzern die relevantesten Videos empfehlen und den Teil des Videos hervorheben, der ihren Anforderungen entspricht.

## <a name="features"></a>Features

In der folgenden Liste sind die Erkenntnisse aufgeführt, die Sie aus Ihren Videos mithilfe von Video- und Audiomodellen von Video Analyzer for Media abrufen können:

### <a name="video-insights"></a>Erkenntnisse aus Videoaufnahmen

* **Gesichtserkennung:** erkennt und gruppiert im Video gezeigte Gesichter.
* **Prominentenerkennung:** Video Analyzer for Media kann automatisch mehr als eine Million Prominente erkennen, etwa wichtige Politiker/Politikerinnen, Schauspieler/Schauspielerinnen, Athleten/Athletinnen, Forscher/Forscherinnen und Führungskräfte aus dem Wirtschafts- und Technologiebereich. Die Daten zu diesen Prominenten sind auch auf verschiedenen Websites (IMDB, Wikipedia usw.) zu finden.
* **Kontobasierte Gesichtsidentifikation**: Video Analyzer for Media trainiert ein Modell für ein bestimmtes Konto. Gesichter im Video werden dann auf Grundlage des trainierten Modells erkannt. Weitere Informationen finden Sie in den Artikeln zum [Anpassen eines Personenmodells mit der Video Analyzer for Media-Website](customize-person-model-with-website.md) und zum [Anpassen eines Personenmodells mit der Video Analyzer for Media-API](customize-person-model-with-api.md).
* **Extraktion von Gesichtern als Miniaturbild** („bestes Gesicht“): Identifiziert automatisch das am besten aufgenommene Gesicht in der jeweiligen Gruppe von Gesichtern (basierend auf Qualität, Größe und frontaler Position) und extrahiert es als Bildobjekt.
* **Optische Zeichenerkennung** (OCR): extrahiert im Video angezeigten Text.
* **Moderation visueller Inhalte:** erkennt nicht jugendfreie bzw. anzügliche visuelle Inhalte.
* **Identifikation von Beschriftungen:** identifiziert angezeigte visuelle Objekte und Aktionen.
* **Szenensegmentierung**: Bestimmt anhand visueller Hinweise, wann sich eine Szene im Video ändert. Eine Szene stellt ein einzelnes Ereignis dar und besteht aus einer Reihe von aufeinanderfolgenden Aufnahmen, die semantisch zusammenhängen.
* **Szenenwechselerkennung:** Bestimmt anhand visueller Hinweise, wann ein Szenenwechsel im Video erfolgt. Eine Aufnahme (Szenenwechsel) umfasst ist eine Reihe von Bildern, die von derselben Filmkamera aufgenommen wurden. Weitere Informationen finden Sie unter [Szenen, Aufnahmen und Keyframes](scenes-shots-keyframes.md).
* **Erkennung schwarzer Frames:** erkennt schwarze Frames im Video.
* **Extraktion von Keyframes:** erkennt stabile Keyframes in einem Video.
* **Durchlaufender Abspann**: Identifiziert den Anfang und das Ende des durchlaufenden Abspanns am Ende von Fernsehsendungen und Filmen.
* **Erkennung animierter Figuren** (Vorschauversion): Bestimmung, Gruppierung und Erkennung von Figuren in animierten Inhalten mittels Integration von [Cognitive Services Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Weitere Informationen finden Sie unter [Erkennung animierter Figuren](animated-characters-recognition.md).
* **Erkennung von redaktionellen Aufnahmetypen**: Tagging (Kategorisierung) von Aufnahmen auf der Grundlage ihres Typs (etwa Totale, Halbtotale, Nahaufnahme, extreme Nahaufnahme, zwei Personen, mehrere Personen, Außenaufnahme, Innenaufnahme usw.). Weitere Informationen finden Sie unter [Erkennung von redaktionellen Aufnahmetypen](scenes-shots-keyframes.md#editorial-shot-type-detection).
* **Verfolgung von beobachteten Personen** (Vorschau): Erkennt beobachtete Personen in Videos und stellt Informationen wie die Position der Person im Videoframe (mit Begrenzungsrahmen) und den genauen Zeitstempel (Start, Ende) bereit, sodass verlässlich festgestellt werden kann, wann eine Person erscheint. Weitere Informationen finden Sie im Artikel zum [Verfolgen beobachteter Personen in einem Video](observed-people-tracing.md).
    * **Erkannte Bekleidung von Personen**: Erkennt die Art der Bekleidung von Personen im Video und liefert entsprechende Informationen. Beispiele wären etwa lange oder kurze Ärmel, lange oder kurze Hose und Rock oder Kleid. Die erkannte Bekleidung wird den Personen zugeordnet, die sie tragen, und der genaue Zeitstempel (Start, Ende) sowie ein Zuverlässigkeitsgrad für die Erkennung werden bereitgestellt.

### <a name="audio-insights"></a>Erkenntnisse aus Audioaufnahmen

* **Audiotranskription**: Wandelt Spracherkennung in über 50 Sprachen um und lässt Erweiterungen zu. Unterstützte Sprachen: Englisch (USA), Englisch (Vereinigtes Königreich), Englisch (Australien), Spanisch, Spanisch (Mexiko), Französisch, Französisch (Kanada), Deutsch, Italienisch, Chinesisch (Mandarin), Chinesisch (Kantonesisch, traditionell), Chinesisch (vereinfacht), Japanisch, Russisch, Portugiesisch, Hindi, Tschechisch, Niederländisch, Polnisch, Dänisch, Norwegisch, Finnisch, Schwedisch, Thailändisch, Türkisch, Koreanisch, Arabisch (Ägypten), Arabisch (Arabische Republik Syrien), Arabisch (Israel), Arabisch (Irak), Arabisch (Jordanien), Arabisch (Kuwait), Arabisch (Libanon), Arabisch (Oman), Arabisch (Katar), Arabisch (Saudi-Arabien), Arabisch (Vereinigte Arabische Emirate), Arabisch (Palästinensische Autonomiebehörde) und Arabisch (modernes Hocharabisch) (Bahrain).
* **Automatische Sprachenerkennung:** Die Lösung identifiziert automatisch die vorherrschend gesprochene Sprache. Unterstützt werden Chinesisch (Mandarin), Deutsch, Englisch, Französisch, Italienisch, Japanisch, Portugiesisch, Russisch und Spanisch. Falls die Sprache nicht zuverlässig identifiziert werden kann, geht Video Analyzer for Media davon aus, dass die gesprochene Sprache Englisch ist. Weitere Informationen finden Sie unter [Automatisches Identifizieren von gesprochener Sprache mit dem Modell zur Sprachenerkennung](language-identification-model.md).
* **Spracherkennung und Transkription für mehrere Sprachen**: Erkennt die gesprochene Sprache in verschiedenen Segmenten automatisch anhand der Audiodaten. Die Funktion sendet jedes Segment der zu transkribierenden Mediendatei und kombiniert die Transkriptionen dann wieder zu einer einzigen Transkription. Weitere Informationen finden Sie unter [Automatisches Erkennen und Transkribieren mehrsprachiger Inhalte](multi-language-identification-transcription.md).
* **Untertitelung:** erstellt Untertitel in drei Formaten: VTT, TTML und SRT.
* **Verarbeitung von zwei Kanälen:** erkennt automatisch ein getrenntes Transkript und sorgt für eine Zusammenführung auf einer einzelnen Zeitachse.
* **Rauschunterdrückung:** bereinigt (basierend auf Skype-Filtern) Telefonaudio oder verrauschte Aufnahmen.
* **Transkriptanpassung** (CRIS): ermöglicht das Trainieren benutzerdefinierter Spracherkennungsmodelle zur Erstellung branchenspezifischer Transkripte. Weitere Informationen finden Sie in den Artikeln zum [Anpassen eines Sprachmodells mit der Video Analyzer for Media-Website](customize-language-model-with-website.md) und zum [Anpassen eines Sprachmodells mit der Video Analyzer for Media-API](customize-language-model-with-api.md).
* **Sprecheraufzählung:** kann erkennen und zuordnen, welcher Sprecher wann was gesagt hat. In einer einzelnen Audiodatei können sechzehn Sprecher erkannt werden.
* **Sprecherstatistiken:** bietet Statistiken zum Verhältnis zwischen Sprechern und ihrem Anteil an der Konversation.
* **Moderation von Textinhalten:** erkennt im Transkript des Audios anstößigen Text.
* **Audioeffekte** (Vorschau): Die folgenden Audioeffekte werden in den Inhaltsabschnitten ohne Sprache erkannt: Schüsse, Glasbruch, Alarme, Sirenen, Explosionen, Hundegebell, Schreie, Gelächter, Reaktionen einer Menschenmenge (Begeisterung, Applaudieren und Buhrufe) sowie Stille. Hinweis: Die gesamten Ereignisse sind nur verfügbar, wenn in den Uploadvoreinstellungen die erweiterte Audioanalyse ausgewählt wird. Andernfalls sind nur Stille und Reaktionen einer Menschenmenge verfügbar.
* **Emotionserkennung:** erkennt Emotionen basierend auf Sprache (was gesagt wird) und Tonalität der Stimme (wie etwas gesagt wird). Bei den Emotionen kann es sich um Freude, Trauer, Wut oder Angst handeln.
* **Übersetzung:** erstellt Übersetzungen des Audiotranskripts in 54 Sprachen.
* **Erkennung von Audioeffekten** (Vorschau): erkennt verschiedene Akustikereignisse und klassifiziert sie in verschiedene Akustikkategorien (z. B. Schüsse, Schreie, Reaktionen einer Menschenmenge usw.). Die erkannten Akustikereignisse befinden sich in der Untertiteldatei. Die Datei kann vom Video Analyzer for Media-Portal heruntergeladen werden. Weitere Informationen finden Sie im Artikel zum [Erkennen von Audioeffekten.](audio-effects-detection.md)

### <a name="audio-and-video-insights-multi-channels"></a>Erkenntnisse aus Audio- und Videoaufnahmen (mehrere Kanäle)

Bei Indizierung nach einem Kanal steht ein Teilergebnis für die folgenden Modelle zur Verfügung.

* **Extraktion von Schlüsselwörtern:** extrahiert Stichwörter aus Sprache und sichtbarem Text.
* **Extraktion benannter Entitäten**: Extrahiert mithilfe der Verarbeitung natürlicher Sprache (Natural Language Processing, NLP) Marken, Standorte und Personen aus Sprache und visuellem Text.
* **Themenerschließung:** zieht in den Transkripten einen Rückschluss auf Hauptthemen. Eine IPTC-Taxonomie zweiter Ebene ist eingeschlossen.
* **Artefakte**: Extrahiert für jedes der Modelle eine umfangreiche Menge von Artefakten mit höherem Detailgrad.
* **Standpunktanalyse:** erkennt anhand von Sprache und sichtbarem Text positive, negative und neutrale Stimmungen.

## <a name="how-can-i-get-started-with-video-analyzer-for-media"></a>Wie kann ich mich mit Video Analyzer for Media vertraut machen?

Es gibt drei Möglichkeiten, auf die Funktionen von Video Analyzer for Media zuzugreifen:

* Video Analyzer for Media-Portal: eine benutzerfreundliche Lösung, mit der Sie das Produkt bewerten, das Konto verwalten und Modelle anpassen können.

    Weitere Informationen zum Portal finden Sie unter [Erste Schritte mit der Video Analyzer for Media-Website](video-indexer-get-started.md).  

* API-Integration: Alle Funktionen von Video Analyzer for Media sind über eine REST-API verfügbar, über die Sie die Lösung in Ihre Apps und Infrastruktur integrieren können.

    Informationen zu den ersten Schritten als Entwickler finden Sie unter  [Verwenden der Video Analyzer for Media-REST-API](video-indexer-use-apis.md).

* Einbettbares Widget: Ermöglicht es Ihnen, die Video Analyzer for Media -Umgebungen für Erkenntnisse, Player und Editor in Ihre App einzubetten.

    Weitere Informationen finden Sie unter  [Einbetten von visuellen Widgets in Ihre Anwendung](video-indexer-embed-widgets.md).

Wenn Sie die Website nutzen, werden die Erkenntnisse als Metadaten hinzugefügt und im Portal angezeigt. Wenn Sie APIs verwenden, sind die Erkenntnisse als JSON-Datei verfügbar.

## <a name="supported-browsers"></a>Unterstützte Browser

Die folgende Liste zeigt die unterstützten Browser, die Sie für die Video Analyzer for Media-Website und für Ihre Apps, die die Widgets einbetten, verwenden können. Außerdem wird in der Liste die unterstützte Browserversion angezeigt:

- Microsoft Edge, Version: 16
- Firefox, Version: 54
- Chrome, Version: 58
- Safari, Version: 11
- Opera, Version: 44
- Opera Mobile, Version: 59
- Android-Browser, Version: 81
- Samsung-Browser, Version: 7
- Chrome für Android, Version: 87
- Firefox für Android, Version: 83

## <a name="next-steps"></a>Nächste Schritte

Jetzt können Sie sich mit Video Analyzer for Media vertraut machen. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Erste Schritte mit der Video Analyzer for Media-Website](video-indexer-get-started.md).
- [Verarbeiten von Inhalten mit der Video Analyzer for Media-REST-API](video-indexer-use-apis.md).
- [Einbetten von visuellen Widgets in Ihre Anwendung](video-indexer-embed-widgets.md)
