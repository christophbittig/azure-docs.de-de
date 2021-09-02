---
title: Verwendungsbereich und Vorgehensweise – Personalisierung
description: Personalisierung kann in jeder Situation angewendet werden, in der Ihre Anwendung das richtige Element oder Produkt bzw. die richtige Aktion auswählen kann, das/die angezeigt werden soll – um die Erfahrung zu verbessern, bessere Geschäftsergebnisse zu erzielen oder die Produktivität zu steigern.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: bc8b0a09212c54ae1e0328e5d1dbfd6d8f213981
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830189"
---
# <a name="where-and-how-to-use-personalizer"></a>Verwendungsbereich und Vorgehensweise – Personalisierung

Verwenden Sie Personalisierung in jeder Situation, in der Ihre Anwendung die richtige Aktion (Inhalt) auswählen muss, die angezeigt werden soll – um die Erfahrung zu verbessern, bessere Geschäftsergebnisse zu erzielen oder die Produktivität zu steigern.

Personalisierung verwendet vertiefendes Lernen, um die dem Benutzer anzuzeigende Aktion (Inhalt) auszuwählen. Die Auswahl kann drastisch variieren, abhängig von der Menge, Qualität und Verteilung der an den Dienst gesendeten Daten.

## <a name="example-use-cases-for-personalizer"></a>Beispielanwendungsfall für die Personalisierung

* **Absichtsklärung und -unterscheidung**: Verhelfen Sie Ihren Benutzern zu einer besseren Erfahrung, wenn ihre Absicht nicht eindeutig ist, indem Sie eine Option anbieten, die personalisiert ist.
* **Standardvorschläge** für Menüs und Optionen: Lassen Sie den Bot als ersten Schritt das wahrscheinlichste Element auf personalisierte Weise vorschlagen, anstatt ein unpersönliches Menü oder eine Liste von Alternativen zu präsentieren.
* **Bot-Merkmale und Ton**: Erwägen Sie für Bots, die Ton, Ausführlichkeit und Schreibstil variieren können, diese Eigenschaften zu variieren.
* **Benachrichtigungs- und Warnungsinhalt**: Entscheiden Sie, welchen Text Sie für Warnmeldungen verwenden möchten, um die Benutzer stärker einzubeziehen.
* **Zeitliche Steuerung von Benachrichtigungen und Warnungen**: Nutzen Sie personalisierte Informationen dazu, wann Benachrichtigungen am besten an Benutzer gesendet werden, um sie stärker einzubeziehen.


## <a name="expectations-required-to-use-personalizer"></a>Für die Verwendung der Personalisierung erforderliche Erwartungen

Sie können die Personalisierung in Meetingsituationen anwenden, oder Sie können die folgenden Richtlinien implementieren.

|Vorgabe|Erklärung|
|--|--|
|Geschäftsziel|Sie haben einen Unternehmens- oder Benutzerfreundlichkeitsziel für Ihre Anwendung.|
|Inhalt|Sie haben eine Stelle in Ihrer Anwendung, wo das Treffen einer kontextbezogenen Entscheidung, was den Benutzern angezeigt wird, dieses Ziel verbessern wird.|
|Inhaltsmenge|Sie verfügen über weniger als 50 Aktionen, denen pro Aufruf ein Rang zugewiesen werden muss.|
|Aggregieren von Daten|Die beste Auswahl kann und sollte aus dem kollektiven Benutzerverhalten und der Belohnungsgesamtbewertung abgeleitet werden.|
|Ethische Verwendung|Die Verwendung von maschinellem Lernen für die Personalisierung befolgt die [Leitlinien für die verantwortungsvolle Verwendung](ethics-responsible-use.md) und die von Ihnen ausgewählten Optionen.
|Beste Einzeloption|Die kontextbezogene Entscheidung kann als Rangfolge der besten Option (Aktion) aus einer begrenzten Anzahl von Auswahlmöglichkeiten ausgedrückt werden.|
|Bewertetes Ergebnis|Wie gut die nach Rang sortierte Auswahl für Ihre Anwendung funktioniert hat, kann bestimmt werden, indem ein Aspekt des Benutzerverhaltens gemessen und als _[Belohnungsbewertung](concept-rewards.md)_ zum Ausdruck kommt.|
|Relevante zeitliche Steuerung|Die Belohnungsbewertung führt nicht zu viele störende oder externe Faktoren ein. Die Experimentdauer ist kurz genug, sodass die Belohnungsbewertung berechnet werden kann, während sie noch relevant ist.|
|Ausreichende Kontextmerkmale|Sie können den Kontext für die Rangfolge als eine Liste von mindestens 5 [Merkmalen](concepts-features.md) ausdrücken, von denen Sie glauben, dass sie beim Treffen der richtigen Auswahl hilfreich sind, und die keine benutzerspezifischen Informationen enthalten.|
|Ausreichende Aktionsmerkmale|Sie besitzen Informationen über jede Inhaltsauswahl, _Aktion_, als Liste von mindestens 5 [Merkmalen](concepts-features.md), von denen Sie glauben, dass sie der Personalisierung beim Treffen der richtigen Auswahl helfen werden.|
|Tägliche Daten|Es gibt genügend Ereignisse, um das Niveau optimaler Personalisierung halten zu können, wenn sich das Problem im Laufe der Zeit verschiebt (z. B. Vorlieben bei Nachrichten oder Mode). Personalisierung passt sich an kontinuierliche Änderungen in der realen Welt an, aber Ergebnisse werden nur dann optimal sein, wenn genügend Ereignisse und Daten zum Lernen vorhanden sind, um neue Muster zu ermitteln und sich auf diese einzustellen. Sie sollten einen Anwendungsfall auswählen, der häufig genug auftritt. Erwägen Sie, nach Anwendungsfällen zu suchen, die mindestens 500-mal am Tag auftreten.|
|Verlaufsdaten|Ihre Anwendung kann Daten lange genug behalten, um einen Verlauf von mindestens 100.000 Interkationen zu sammeln. Dies gestattet es der Personalisierung, ausreichende Daten zu sammeln, um Offlineauswertungen und Richtlinienoptimierungen durchzuführen.|

**Verwenden Sie die Personalisierung nicht**, wenn das personalisierte Verhalten nicht bei allen Benutzern entdeckt werden kann. Beispiel: Das Verwenden der Personalisierung für Vorschläge zu einer ersten Pizzabestellung aus einer Liste von 20 möglichen Menüelementen ist nützlich. Im Gegensatz dazu kann der Kontakt aus der Kontaktliste der Benutzer, der angerufen werden kann, wenn Hilfe bei der Kinderbetreuung benötigt wird (wie z. B. „Oma“), nicht für Ihre gesamte Benutzerbasis personalisiert werden.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Verwenden von Personalisierung in einer Webanwendung

Das Hinzufügen einer Lernschleife zu einer Webanwendung umfasst folgende Aktionen:

* Bestimmen Sie, welche Erfahrung Sie personalisieren möchten, welche Aktionen und Merkmale Sie haben, welche Kontextmerkmale verwendet werden sollen, und welche Belohnung Sie festlegen werden.
* Fügen Sie einen Verweis auf das Personalisierung-SDK in Ihrer Anwendung hinzu.
* Rufen Sie die Rang-API auf, wenn Sie bereit sind, zu personalisieren.
* Speichern Sie die Ereignis-ID. Sie senden später eine Belohnung mit der Relevanz-API.
1. Rufen Sie die Aktivierung für das Ereignis auf, sobald Sie sind Sie sicher, dass der Benutzer Ihre personalisierte Seite gesehen hat.
1. Warten Sie auf die Auswahl des Benutzers aus nach Rang sortiertem Inhalt.
1. Rufen Sie Relevanz-API auf, um anzugeben, wie gut die Ausgabe der Rang-API funktioniert hat.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Verwenden von Personalisierung mit einem Chatbot

In diesem Beispiel sehen Sie, wie mit Personalisierung ein Standardvorschlag gemacht wird, anstatt den Benutzer jedes Mal durch eine Reihe von Menüs oder Optionen zu schicken.

* Rufen Sie den [Code](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) für dieses Beispiel ab.
* Richten Sie Ihre Bot-Lösung ein. Achten Sie darauf, Ihre LUIS-Anwendung zu veröffentlichen.
* Verwalten Sie Aufrufe der Rang- und Relevanz-API für Bots.
    * Fügen Sie Code zum Verwalten der LUIS-Absichtsverarbeitung hinzu. Wenn **Keine** als am höchsten eingestufte Absicht zurückgegeben wird oder die Punktzahl der am höchsten eingestufte Absicht unter dem Schwellenwert Ihrer Geschäftslogik liegt, senden Sie die Absichtsliste an Personalisierung, um die Absichten zu bewerten.
    * Zeigen Sie dem Benutzer die Absichtsliste als auswählbare Links an, wobei die erste Absicht die am höchsten eingestufte Absicht aus der Antwort der Rang-API ist.
    * Erfassen Sie die Auswahl des Benutzers, und senden Sie diese im Aufruf der Relevanz-API.

### <a name="recommended-bot-patterns"></a>Empfohlene Bot-Muster

* Rufen Sie die Rang-API der Personalisierung immer auf, wenn eine Unterscheidung erforderlich ist, anstatt Ergebnisse für jeden Benutzer zwischenzuspeichern. Das Ergebnis der Vereindeutigung einer Absicht kann sich im Laufe der Zeit für eine Person ändern, und wenn die Rang-API Abweichungen untersuchen kann, wird das allgemeine Lernen beschleunigt.
* Wählen Sie eine Interaktion, die bei vielen Benutzern üblich ist, damit Sie genügend Daten zur Personalisierung haben. Beispielsweise können einleitende Fragen besser geeignet sein, als kleinere Klarstellungen tief im Konversationsgraphen, zu denen u. U. nur wenige Benutzer gelangen.
* Verwenden Sie Rang-API-Aufrufe, um Konversationen vom Typ „erster Vorschlag ist richtig“ zu ermöglichen, bei denen der Benutzer gefragt wird „Möchten Sie X?“ oder „Meinten Sie X?“ und dann einfach bestätigen kann, anstatt dem Benutzer ein Menü mit Optionen zur Auswahl zu geben. Beispiel: Benutzer: „Ich möchte einen Kaffee bestellen.“ Bot: „Möchten Sie einen doppelten Espresso?“ Auf diese Weise ist auch das Relevanzsignal stark, da direkt auf den einen Vorschlag Bezug genommen wird.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Verwenden der Personalisierung mit einer Empfehlungslösung

Viele Unternehmen verwenden Empfehlungs-Engines, Marketing- und Kampagnentools, Benutzergruppensegmentierung und -clustering, kollaboratives Filtern und andere Mittel, um Kunden Produkte aus einem umfangreichen Katalog zu empfehlen.

Im [GitHub-Repository „Microsoft Recommenders“](https://github.com/Microsoft/Recommenders) finden Sie Beispiele und bewährte Methoden für den Aufbau von Empfehlungssystemen, bereitgestellt als Jupyter-Notebooks. Es stellt funktionierende Beispiele für die Vorbereitung von Daten, das Erstellen von Modellen, die Auswertung, die Optimierung und Operationalisierung der Empfehlungs-Engines für viele gängige Ansätze bereit, einschließlich xDeepFM, SAR, ALS, RBM, DKN.

Personalisierung kann mit einer Empfehlungs-Engine zusammenarbeiten, wenn sie vorhanden ist.

* Empfehlungs-Engines nutzen große Mengen von Elementen (z. B. 500.000) und empfehlen eine Teilmenge (z. B. die Top-20) von Hunderten oder Tausenden von Optionen.
* Personalisierung nutzt eine kleine Anzahl von Aktionen mit vielen Informationen über diese und weist diesen für einen vorgegebenen, umfangreichen Kontext in Echtzeit eine Rangfolge zu, während die meisten Empfehlungs-Engines nur ein paar Attribute von Benutzern, Produkten und deren Interaktionen verwenden.
* Personalisierung ist darauf ausgelegt, ständig Benutzervorlieben autonom zu untersuchen, was zu besseren Ergebnissen führt, wenn sich Inhalte schnell ändern, z. B. bei Nachrichten, Liveveranstaltungen, Liveinhalten von Communitys, Inhalten mit täglichen Aktualisierungen oder saisonalen Inhalten.

Ein gängiges Szenario besteht darin, die Ausgabe einer Empfehlungs-Engine zu nehmen (z. B. die Top-20 Produkte für einen bestimmten Kunden) und diese als Eingabeaktionen für die Personalisierung zu verwenden.

## <a name="adding-content-safeguards-to-your-application"></a>Hinzufügen von Sicherheitsmechanismen für Inhalt zu Ihrer Anwendung

Wenn Ihre Anwendung starke Abweichungen bei den Inhalten zulässt, die den Benutzern angezeigt werden, und einige dieser Inhalte für einige Benutzer unsicher oder ungeeignet sind, sollten Sie im Voraus planen, um sicherzustellen, dass die richtigen Sicherheitsvorkehrungen getroffen werden, um zu verhindern, dass Ihre Benutzer unzulässige Inhalte sehen. Das beste Muster zum Implementieren von Sicherheitsmechanismen ist:
    * Rufen Sie die Aktionen ab, denen ein Rang zugewiesen werden soll.
    * Filtern Sie Aktionen aus, die nicht für die Zielgruppe geeignet sind.
    * Weisen Sie nur den geeigneten Aktionen einen Rang zu.
    * Zeigen Sie dem Benutzer die am besten bewertete Aktion an.

In einigen Architekturen ist die obige Sequenz u. U. schwer zu implementieren. In diesem Fall gibt es einen alternativen Ansatz für die Implementierung von Sicherheitsmaßnahmen nach Rangzuweisung, es muss jedoch eine Vorkehrung getroffen werden, damit Aktionen, die nicht unter die Sicherheitsmaßnahmen fallen, nicht zum Trainieren des Personalisierungsmodells verwendet werden.

* Rufen Sie die Aktionen, denen ein Rang zugewiesen werden soll, mit deaktivierter Lernfunktion ab.
* Weisen Sie den Aktionen Ränge zu.
* Überprüfen Sie, ob die erste Aktion gültig ist.
    * Wenn die erste Aktion gültig ist, aktivieren Sie die Lernfunktion für diesen Rang, und zeigen Sie sie dann dem Benutzer an.
    * Wenn die erste Aktion nicht gültig ist, aktivieren Sie das Lernen für diese Rangfolge nicht, und entscheiden Sie anhand Ihrer eigenen Logik oder alternativer Ansätze, was Sie dem Benutzer zeigen möchten. Auch wenn Sie die Option mit dem zweitbesten Rang verwenden, aktivieren Sie das Lernen für diese Rangfolge nicht.


## <a name="next-steps"></a>Nächste Schritte

[Ethik und verantwortungsvolle Verwendung](ethics-responsible-use.md).
