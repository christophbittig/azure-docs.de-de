---
title: LUIS-Glossar
description: In diesem Glossar werden Begriffe erläutert, auf die Sie möglicherweise beim Arbeiten mit der LUIS-API stoßen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/28/2021
ms.openlocfilehash: 25bdf291d7d836523655b131f485721e3a29593b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434577"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Language Understanding-Glossar mit allgemeinem Vokabular und Konzepten
In diesem Glossar für Language Understanding (LUIS) werden Begriffe erläutert, auf die Sie möglicherweise beim Arbeiten mit dem LUIS-Dienst stoßen.

## <a name="active-version"></a>Aktive Version

Die aktive Version ist die [Version](luis-how-to-manage-versions.md) Ihrer App, die aktualisiert wird, wenn Sie über das LUIS-Portal Änderungen am Modell vornehmen. Wenn Sie im LUIS-Portal Änderungen an einer Version vornehmen möchten, bei der es sich nicht um die aktive Version handelt, müssen Sie diese Version zunächst als aktive Version festlegen.

## <a name="active-learning"></a>Aktives Lernen

Aktives Lernen ist eine Technik des maschinellen Lernens, bei der das Modell des maschinellen Lernens verwendet wird, um informative neue Beispiele zu identifizieren, die zu bezeichnen sind. In LUIS bezieht sich aktives Lernen auf das Hinzufügen von Äußerungen aus dem Endpunktdatenverkehr, deren aktuelle Vorhersagen unklar sind, um Ihr Modell zu verbessern. Klicken Sie auf „Endpunktäußerungen überprüfen“, um die zu bezeichnenden Äußerungen anzuzeigen.

Weitere Informationen:
* [Konzeptionelle Informationen](luis-concept-review-endpoint-utterances.md)
* [Tutorial zur Überprüfung von Endpunktäußerungen](luis-tutorial-review-endpoint-utterances.md)
* Verbessern der LUIS-App durch [Überprüfen der Endpunktäußerungen](luis-how-to-review-endpoint-utterances.md)

## <a name="application-app"></a>Anwendung (App)

In LUIS entspricht Ihre Anwendung oder App einer Sammlung von Machine Learning-Modellen, die auf demselben Dataset aufbauen und zusammenarbeiten, um Absichten und Entitäten für ein bestimmtes Szenario vorherzusagen. Jede Anwendung verfügt über einen separaten Vorhersageendpunkt.

Wenn Sie einen Bot für die Personalabteilung erstellen, haben Sie möglicherweise eine Reihe von Absichten, z. B. „Urlaubszeit planen“, „nach Leistungen fragen“ und „persönliche Daten aktualisieren“ und Entitäten für jede dieser Absichten, die Sie in einer einzelnen Anwendung zusammenfassen.

## <a name="authoring"></a>Erstellen

Bei der Erstellung handelt es sich um die Fähigkeit zum Erstellen, Verwalten und Bereitstellen einer LUIS-App – entweder über das LUIS-Portal oder über die Erstellungs-APIs.

### <a name="authoring-key"></a>Erstellungsschlüssel

Der [Erstellungsschlüssel](luis-how-to-azure-subscription.md) wird zum Erstellen der App verwendet. Er sollte nicht für Endpunktabfragen auf Produktionsebene verwendet werden. Weitere Informationen finden Sie unter [Schlüsselgrenzwerte](luis-limits.md#key-limits).

### <a name="authoring-resource"></a>Erstellungsressource

Ihre LUIS-[Erstellungsressource](luis-how-to-azure-subscription.md) ist ein verwaltbares Element, das über Azure erhältlich ist. Die Ressource ist Ihr Zugang zu den zugehörigen Erstellungs-, Trainings- und Veröffentlichungsfunktionen des Azure-Diensts. Die Ressource enthält Authentifizierungs-, Autorisierungs- und Sicherheitsinformationen, die Sie für den Zugriff auf den zugehörigen Azure-Dienst benötigen.

Die Erstellungsressource weist die Azure-Art `LUIS-Authoring` auf.

## <a name="batch-test"></a>Batchtest

Batchtests ermöglichen die Überprüfung der Modelle einer aktuellen LUIS-App mit einem konsistenten und bekannten Testsatz von Benutzeräußerungen. Der Batchtest ist in einer [Datei im JSON-Format](./luis-how-to-batch-test.md#batch-test-file) definiert.


Weitere Informationen:
* [Konzepte](./luis-how-to-batch-test.md)
* [Vorgehensweise](luis-how-to-batch-test.md) zum Ausführen eines Batchtests
* [Tutorial](./luis-how-to-batch-test.md): Erstellen und Ausführen eines Batchtests

### <a name="f-measure"></a>F-Measure

Bei Batchtests ein Maß für die Testgenauigkeit.

### <a name="false-negative-fn"></a>False Negative-Ergebnis (FN)

Bei Batchtests stellen die Datenpunkte Äußerungen dar, in denen Ihre App das Fehlen der Zielabsicht/-entität falsch vorhergesagt hat.

### <a name="false-positive-fp"></a>False Positive-Ergebnis (FP)

Bei Batchtests stellen die Datenpunkte Äußerungen dar, in denen Ihre App das Vorhandensein der Zielabsicht/-entität falsch vorhergesagt hat.

### <a name="precision"></a>Precision
Bei Batchtests stellt die Genauigkeit (auch als positiver Vorhersagewert bezeichnet) den Anteil der relevanten Äußerungen unter den abgerufenen Äußerungen dar.

Ein Beispiel für einen Batchtest für Tiere ist die Anzahl der vorhergesagten Schafe geteilt durch die Gesamtzahl der Tiere (Schafe und Nicht-Schafe).

### <a name="recall"></a>Recall (Trefferquote)

Bei Batchtests stellt der Recall (auch als Trefferquote oder Empfindlichkeit bezeichnet) die Fähigkeit von LUIS zu generalisieren dar.

Ein Beispiel für einen Batchtest für Tiere ist die Anzahl der vorhergesagten Schafe geteilt durch die Gesamtzahl der verfügbaren Schafe.

### <a name="true-negative-tn"></a>True Negative-Ergebnis (TN)

Ein True Negative-Ergebnis ist, wenn Ihre App ordnungsgemäß keine Übereinstimmung vorhersagt. Bei Batchtests tritt ein True Negative-Ergebnis auf, wenn Ihre App eine Absicht oder Entität für eine Stichprobe vorhersagt, die nicht mit dieser Absicht oder Entität bezeichnet wurde.

### <a name="true-positive-tp"></a>True Positive-Ergebnis (TP)

Ein True Positiv-Ergebnis (TP) ist, wenn Ihre App ordnungsgemäß eine Übereinstimmung vorhersagt. Bei Batchtests tritt ein True Positive-Ergebnis auf, wenn Ihre App eine Absicht oder Entität für eine Stichprobe vorhersagt, die mit dieser Absicht oder Entität bezeichnet wurde.

## <a name="classifier"></a>Klassifizierung

Eine Klassifizierung ist ein Modell des maschinellen Lernens, das vorhersagt, in welche Kategorie oder Klasse eine Eingabe passt.

Eine [Absicht](#intent) ist ein Beispiel für eine Klassifizierung.

## <a name="collaborator"></a>Projektmitarbeiter

Ein Projektmitarbeiter ist konzeptionell dasselbe wie ein [Mitwirkender](#contributor). Einem Projektmitarbeiter wird Zugriff gewährt, wenn ein Besitzer die E-Mail-Adresse des Mitarbeiters zu einer App hinzufügt, die nicht über die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) gesteuert wird. Wenn Sie immer noch Projektmitarbeiter verwenden, sollten Sie Ihr LUIS-Konto migrieren und LUIS-Erstellungsressourcen verwenden, um Mitarbeiter mithilfe der rollenbasierten Zugriffssteuerung (Azure RBAC) zu verwalten.

## <a name="contributor"></a>Mitwirkender

Ein Mitwirkender ist nicht der [Besitzer](#owner) der App, er verfügt aber über dieselben Berechtigungen zum Hinzufügen, Bearbeiten und Löschen der Absichten, Entitäten und Äußerungen. Ein Mitwirkender stellt die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) für eine LUIS-App bereit.

Weitere Informationen:
* [Vorgehensweise](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) beim Hinzufügen von Mitwirkenden

## <a name="descriptor"></a>Deskriptor

Deskriptor ist der früher für ein [Feature](#features) des maschinellen Lernens verwendete Begriff.

## <a name="domain"></a>Domain

Im LUIS-Kontext ist eine Domäne ein Themenbereich. Ihre Domäne ist spezifisch für Ihr Szenario. Unterschiedliche Domänen verwenden eine bestimmte Sprache und Terminologie, die im Kontext der Domäne eine bestimmte Bedeutung haben. Wenn Sie z. B. eine Anwendung zur Wiedergabe von Musik erstellen, würde Ihre Anwendung Begriffe und Sprache enthalten, die spezifisch für Musik sind, d. h. Wörter wie „Lied, Titel, Album, Liedtext, B-Seite, Künstler“. Beispiele für Domänen finden Sie unter [Vordefinierte Domänen](#prebuilt-domain).

## <a name="endpoint"></a>Endpunkt

### <a name="authoring-endpoint"></a>Erstellungsendpunkt

Die Endpunkt-URL für die LUIS-Erstellung ist der Ort, an dem Sie Ihre App erstellen, trainieren und veröffentlichen. Die Endpunkt-URL enthält die Region oder benutzerdefinierte Unterdomäne der veröffentlichten App sowie die App-ID.

Weitere Informationen zum programmgesteuerten Erstellen Ihrer App finden Sie in der [Entwicklerreferenz](developer-reference-resource.md#rest-endpoints)

### <a name="prediction-endpoint"></a>Vorhersageendpunkt

In der LUIS-Vorhersageendpunkt-URL übermitteln Sie LUIS-Abfragen, nachdem die [LUIS-App](#application-app) erstellt und veröffentlicht wurde. Die Endpunkt-URL enthält die Region oder benutzerdefinierte Unterdomäne der veröffentlichten App sowie die App-ID. Sie finden den Endpunkt auf der Seite **[Azure-Ressourcen](luis-how-to-azure-subscription.md)** Ihrer App, oder Sie können die Endpunkt-URL bei der [Get App Info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37)-API abrufen.

Ihr Zugriff auf den Vorhersageendpunkt wird mit dem LUIS-Vorhersageschlüssel autorisiert.

## <a name="entity"></a>Entität

[Entitäten](luis-concept-entity-types.md) sind Wörter in Äußerungen, die Informationen beschreiben, die zur Erfüllung oder Identifizierung einer Absicht verwendet werden. Wenn Ihre Entität komplex ist und Sie möchten, dass Ihr Modell bestimmte Teile identifiziert, können Sie Ihr Modell in untergeordnete Entitäten unterteilen. Sie können z. B. ein Modell zur Vorhersage einer Adresse, aber auch der untergeordneten Entitäten „Straße“, „Ort“, „Bundesland“ und „Postleitzahl“ erstellen. Entitäten können auch als Features für Modelle verwendet werden. Ihre Antwort aus der LUIS-App wird sowohl die vorhergesagten Absichten als auch alle Entitäten enthalten.

### <a name="entity-extractor"></a>Entitätsextraktor

Ein Entitätsextraktor, der gelegentlich nur als Extraktor bezeichnet wird, ist der Typ eines Modells des maschinellen Lernens, der von LUIS zur Vorhersage von Entitäten verwendet wird.

### <a name="entity-schema"></a>Entitätsschema

Das Entitätsschema ist die Struktur, die Sie für Machine Learning-Entitäten mit untergeordneten Entitäten definieren. Der Vorhersageendpunkt gibt alle extrahierten Entitäten und untergeordneten Entitäten zurück, die im Schema definiert sind.

### <a name="entitys-subentity"></a>Untergeordnete Entität einer Entität

Eine Subentität ist einer Machine-Learning-Entität untergeordnet.

### <a name="non-machine-learning-entity"></a>Entität ohne Machine Learning

Eine Entität, die Textabgleiche verwendet, um Daten zu extrahieren:
* Entität vom Typ „List“
* Entität vom Typ „RegEx“

### <a name="list-entity"></a>Entität vom Typ „List“

Eine [Listenentität](reference-entity-list.md) stellt einen festen, abgeschlossenen Satz verwandter Wörter zusammen mit ihren Synonymen dar. Listenentitäten sind exakte Übereinstimmungen, im Gegensatz zu Entitäten, die durch maschinelles Lernen erworben wurden.

Die Entität wird vorhergesagt, wenn ein Wort in der Listenentität in der Liste enthalten ist. Wenn Sie z. B. eine Listenentität namens „Größe“ besitzen und die Wörter „klein, mittel, groß“ in der Liste enthalten sind, dann wird die Entität „Größe“ für alle Äußerungen vorhergesagt, in denen die Wörter „klein“, „mittel“ oder „groß“ unabhängig vom Kontext verwendet werden.

### <a name="regular-expression"></a>Regulärer Ausdruck

Die Entität [regulärer Ausdruck](reference-entity-regular-expression.md) stellt einen regulären Ausdruck dar. Entitäten mit regulären Ausdrücken sind exakte Übereinstimmungen, im Gegensatz zu Entitäten, die durch maschinelles Lernen erworben wurden.
### <a name="prebuilt-entity"></a>Vordefinierte Entität

Weitere Informationen finden Sie im Eintrag des vordefinierten Modells für [vordefinierte Entität](#prebuilt-entity).

## <a name="features"></a>Features

Beim maschinellen Lernen ist ein Feature ein Merkmal, das dem Modell hilft, ein bestimmtes Konzept zu erkennen. Es ist ein Hinweis, den LUIS verwenden kann, aber keine feste Regel.

Das wird auch als **[Machine-Learning-Feature](luis-concept-feature.md)** bezeichnet.

Diese Hinweise werden in Verbindung mit den Bezeichnungen verwendet, um zu lernen, wie neue Daten vorhergesagt werden können. LUIS unterstützt als Features sowohl Ausdruckslisten als auch andere Modelle.

### <a name="required-feature"></a>Erforderliches Feature

Ein erforderliches Feature ist eine Möglichkeit, die Ausgabe eines LUIS-Modells einzuschränken. Wenn ein Feature für eine Entität als erforderlich markiert ist, muss das Feature im Beispiel vorhanden sein, damit die Entität vorhergesagt werden kann, unabhängig davon, was das Machine Learning-Modell vorhersagt.

Betrachten Sie ein Beispiel, bei dem Sie ein vordefiniertes Nummernfeature verwenden, das Sie für die Entität „Menge“ für einen Bot zur Menübestellung als erforderlich markiert haben. Wenn Ihr Bot `I want a bajillion large pizzas?` erkennt, wird „Bajillion“ nicht als Menge vorhergesagt, unabhängig vom Kontext, in dem es angezeigt wird. „Bajillion“ ist keine gültige Zahl und wird von der vorab erstellten Entität nicht vorhergesagt.

## <a name="intent"></a>Intent

Eine [Absicht](luis-concept-intent.md) stellt eine Aufgabe oder Aktion dar, die der Benutzer ausführen möchte. Sie ist ein vom Benutzer in seiner Eingabe genannter Zweck oder ein Ziel, wie das Buchen eines Flugs oder das Bezahlen einer Rechnung. In LUIS wird eine Äußerung insgesamt als Absicht klassifiziert, aber Teile der Äußerung werden als Entitäten extrahiert.

## <a name="labeling-examples"></a>Beispiele für Bezeichnungen

Bezeichnen oder Markieren ist der Prozess, bei dem ein positives oder negatives Beispiel einem Modell zugeordnet wird.

### <a name="labeling-for-intents"></a>Bezeichnungen für Absichten
In LUIS schließen sich die Absichten innerhalb einer App gegenseitig aus. Das bedeutet, wenn Sie eine Äußerung zu einer Absicht hinzufügen, wird sie als _positives_ Beispiel für diese Absicht und als _negatives_ Beispiel für alle anderen Absichten betrachtet. Negative Beispiele sollten nicht mit der Absicht „Keine“ verwechselt werden, bei der es sich um Äußerungen handelt, die nicht in den Bereich der App fallen.

### <a name="labeling-for-entities"></a>Bezeichnungen für Entitäten
In LUIS [bezeichnen](label-entity-example-utterance.md) Sie ein Wort oder einen Ausdruck in der Beispieläußerung einer Absicht mit einer Entität als _positives_ Beispiel. Die Bezeichnung zeigt die Absicht, was sie für diese Äußerung vorhersagen sollte. Die bezeichneten Äußerungen werden verwendet, um die Absicht zu trainieren.

## <a name="luis-app"></a>LUIS-App

Weitere Informationen finden Sie in der Definition für [Anwendung (App)](#application-app).

## <a name="model"></a>Modell

Ein (durch maschinelles Lernen erworbenes) Modell ist eine Funktion, die eine Vorhersage zu Eingabedaten ausgibt. In LUIS werden Absichtsklassifizierer und Entitätsextraktoren allgemein als „Modelle“ bezeichnet, und wir beziehen uns auf eine Sammlung von Modellen, die zusammen als „App“ trainiert, veröffentlicht und abgefragt werden.

## <a name="normalized-value"></a>Normalisierter Wert

Sie fügen Werte zu Ihren [Listenentitäten](#list-entity) hinzu. Jeder dieser Werte kann eine Liste mit einem oder mehreren Synonymen aufweisen. In der Antwort wird nur der normalisierte Wert zurückgegeben.

## <a name="overfitting"></a>Überanpassung

Eine Überanpassung tritt auf, wenn das Modell auf bestimmte Beispiele fixiert ist und nicht gut generalisieren kann.

## <a name="owner"></a>Besitzer

Jede App hat einen Besitzer. Dies ist die Person, die die App erstellt hat. Der Besitzer verwaltet die Berechtigungen für die Anwendung im Azure-Portal.

## <a name="phrase-list"></a>Ausdrucksliste

Eine [Ausdrucksliste](luis-concept-feature.md) ist eine bestimmte Art von Machine Learning-Feature, das eine Gruppe von Werten (Wörter oder Ausdrücke) enthält, die derselben Klasse angehören und auf ähnliche Weise behandelt werden sollen (z. B. Städte- oder Produktnamen).

## <a name="prebuilt-model"></a>Vordefiniertes Modell

Ein [vordefiniertes Modell](luis-concept-prebuilt-model.md) ist eine Absicht, einen Entität oder eine Kombination aus beidem, zusammen mit bezeichneten Beispielen. Diese allgemeinen vordefinierten Modelle können zu Ihrer App hinzugefügt werden, um die für Ihre App erforderliche Modellentwicklungsarbeit zu reduzieren.

### <a name="prebuilt-domain"></a>Vordefinierte Domäne

Ein vordefinierte Domäne ist eine LUIS-App, die für einen bestimmten Themenbereich wie z. B. die Heimautomatisierung (HomeAutomation) oder Restaurantreservierungen (RestaurantReservation) konfiguriert wurde. Die Absichten, Äußerungen und Entitäten werden für diese Domäne konfiguriert.

### <a name="prebuilt-entity"></a>Vordefinierte Entität

Eine vordefinierte Entität ist eine Entität, die LUIS für allgemeine Arten von Informationen bereitstellt, z. B. „Zahl“, „URL“ und „E-Mail“. Diese werden auf der Grundlage öffentlicher Daten erstellt. Sie können eine vordefinierte Entität als eigenständige Entität oder als Feature zu einer Entität hinzufügen.

### <a name="prebuilt-intent"></a>Vordefinierte Absicht

Eine vordefinierte Absicht ist eine Absicht, die LUIS für allgemeine Arten von Informationen vorsieht, und die mit ihren eigenen bezeichneten Beispieläußerungen bereitgestellt werden.

## <a name="prediction"></a>Vorhersage

Eine Vorhersage ist eine REST-Anforderung an den Azure LUIS-Vorhersagedienst, der neue Daten (Benutzeräußerung) aufnimmt und die trainierte und veröffentlichte Anwendung auf diese Daten anwendet, um festzustellen, welche Absichten und Entitäten gefunden werden.

### <a name="prediction-key"></a>Vorhersageschlüssel

Der [Vorhersageschlüssel](luis-how-to-azure-subscription.md) (früher als Abonnementschlüssel bezeichnet) ist der Schlüssel, der mit dem LUIS-Dienst verbunden ist, den Sie in Azure erstellt haben und der Ihre Verwendung des Vorhersageendpunkts autorisiert.

Dieser Schlüssel ist nicht der Erstellungsschlüssel. Wenn Sie einen Vorhersageendpunktschlüssel besitzen, sollte dieser anstelle des Erstellungsschlüssels für alle Endpunktanforderungen verwendet werden. Sie können Ihren aktuellen Vorhersageschlüssel innerhalb der Endpunkt-URL unten auf der Azure-Ressourcenseite der LUIS-Website anzeigen. Es ist der Wert des Name-Wert-Paars „subscription-key“.

### <a name="prediction-resource"></a>Vorhersageressource

Ihre LUIS-Vorhersageressource ist ein verwaltbares Element, das über Azure erhältlich ist. Die Ressource ist Ihr Zugang zu der zugehörigen Vorhersage des Azure-Diensts. Die Ressource enthält Vorhersagen.

Die Vorhersageressource weist die Azure-Art `LUIS` auf.

### <a name="prediction-score"></a>Vorhersageergebnis

Der [Score](luis-concept-prediction-score.md) ist eine Zahl zwischen 0 und 1, die ein Maß dafür ist, wie sicher das System ist, dass eine bestimmte Eingabeäußerung mit einer bestimmten Absicht übereinstimmt. Ein Score, der näher bei 1 liegt, weist darauf hin, dass das System sehr zuversichtlich in Bezug auf seine Ausgabe ist. Ein Score, der näher bei 0 liegt, weist darauf hin, dass das System zuversichtlich ist, dass die Eingabe nicht mit einer bestimmten Ausgabe übereinstimmt. Scores in der Mitte bedeuten, dass das System sehr unsicher ist, wie es die Entscheidung treffen soll.

Nehmen Sie z. B. ein Modell, das verwendet wird, um festzustellen, ob ein Kundentext eine Lebensmittelbestellung enthält. Es könnte einen Score von 1 für „Ich möchte einen Kaffee bestellen“ (das System ist sehr zuversichtlich, dass dies ein Befehl ist) und einen Score von 0 für „mein Team hat das Spiel gestern Abend gewonnen“ (das System ist sehr zuversichtlich, dass dies KEIN Befehl ist) ergeben. Und es könnte einen Score von 0,5 für „Lasst uns Tee trinken“ haben (ist nicht sicher, ob dies ein Befehl ist).

## <a name="programmatic-key"></a>Programmierschlüssel

Umbenannt in [Erstellungsschlüssel](#authoring-key).

## <a name="publish"></a>Veröffentlichen

[Veröffentlichen](luis-how-to-publish-app.md) bedeutet, dass die aktive Version einer LUIS-App auf einem [Endpunkt](#endpoint) für Staging oder Produktion verfügbar gemacht wird.

## <a name="quota"></a>Kontingent

Das LUIS-Kontingent ist die Einschränkung durch den Azure-Abonnementtarif. Das LUIS-Kontingent kann sowohl im Hinblick auf die Anforderungen pro Sekunde (HTTP-Status 429) als auch auf die Summe der Anforderungen in einem Monat (HTTP-Status 403) eingeschränkt werden.

## <a name="schema"></a>Schema

Ihr Schema umfasst Ihre Absichten und Entitäten zusammen mit den untergeordneten Entitäten. Das Schema wird anfänglich geplant und dann im Laufe der Zeit iteriert. Das Schema umfasst keine App-Einstellungen, Features oder Beispieläußerungen.

## <a name="sentiment-analysis"></a>Standpunktanalyse
Die Stimmungsanalyse liefert positive oder negative Werte zu Äußerungen, die vom [Sprachdienst](../language-service/sentiment-opinion-mining/overview.md) bereitgestellt werden.

## <a name="speech-priming"></a>Sprachvorbereitung

Die Sprachvorbereitung verbessert die Erkennung von gesprochenen Wörtern und Ausdrücken, die häufig in Ihrem Szenario mit [Speech-Diensten](../speech-service/overview.md) verwendet werden. Bei Anwendungen, für die die Sprachvorbereitung aktiviert ist, werden alle mit LUIS bezeichneten Beispiele verwendet, um die Genauigkeit der Spracherkennung durch die Erstellung eines angepassten Sprachmodells für diese spezielle Anwendung zu verbessern. Bei einem Schachspiel würden Sie beispielsweise sicherstellen, dass bei der Benutzeranweisung „Move Knight“ nicht „Move Night“ verstanden wird. Die LUIS-App sollte Beispiele enthalten, in denen „Knight“ als Entität gekennzeichnet ist.

## <a name="starter-key"></a>Startschlüssel

Ein öffentlicher Schlüssel für die ersten Gehversuche in LUIS.

## <a name="synonyms"></a>Synonyme

In LUIS-[Listenentitäten](reference-entity-list.md) können Sie einen normalisierten Wert erstellen, der jeweils eine Liste von Synonymen enthalten kann. Wenn Sie z. B. eine Größenentität erstellen, die normalisierte Werte für „klein“, „mittel“, „groß“ und „sehr groß“ aufweist. Sie könnten für jeden Wert wie folgt Synonyme erstellen:

|Nomalisierter Wert| Synonyme|
|--|--|
|Klein| klein, 8 Unzen|
|Medium| normal, 12 Unzen|
|Groß| groß, 16 Unzen|
|Sehr groß| größter, 24 Unzen|

Das Modell gibt den normalisierten Wert für die Entität zurück, wenn eines der Synonyme in der Eingabe erkannt wird.

## <a name="test"></a>Test

Das [Testen](./luis-interactive-test.md) einer LUIS-App bedeutet die Anzeige von Modellvorhersagen.

## <a name="timezone-offset"></a>Zeitzonenunterschied

Der Endpunkt enthält einen Zeitzonenunterschied ([timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)). Dies ist die Anzahl Minuten, die Sie in der vordefinierten Entität datetimeV2 hinzufügen oder entfernen sollten. Wenn die Äußerung z.B. „Wie spät ist es?“ lautet, wird als datetimeV2-Wert die aktuelle Uhrzeit bei der Clientanforderung zurückgegeben. Wenn Ihre Clientanforderung von einem Bot oder einer anderen Anwendung stammt, die nicht mit Ihrem Botbenutzer übereinstimmt, sollten Sie den Zeitversatz zwischen dem Bot und dem Benutzer übergeben.

Weitere Informationen finden Sie unter [Ändern der Zeitzone von vordefinierten datetimeV2-Entitäten](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Ein [Token](luis-language-support.md#tokenization) ist die kleinste Texteinheit, die von LUIS erkannt werden kann. Dies unterscheidet sich leicht von Sprache zu Sprache.

Für **Englisch** ist ein Token eine fortlaufende Spanne (ohne Leerzeichen oder Interpunktion) von Buchstaben und Zahlen. Ein Leerzeichen ist KEIN Token.

|Ausdruck|Tokenanzahl|Erklärung|
|--|--|--|
|`Dog`|1|Ein einzelnes Wort ohne Interpunktion oder Leerzeichen.|
|`RMT33W`|1|Eine Nummer für einen Datensatzlocator. Sie kann Zahlen und Buchstaben enthalten, aber keine Interpunktion.|
|`425-555-5555`|5|Eine Telefonnummer. Jedes Interpunktionszeichen ist ein einzelnes Token, sodass `425-555-5555` aus fünf Token bestehen würde:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Trainieren

Unter [Training](luis-how-to-train.md) wird verstanden, LUIS alle Änderungen an der aktiven Version seit dem letzten Training beizubringen.

### <a name="training-data"></a>Trainingsdaten

Trainingsdaten sind die Informationen, die zum Trainieren eines Modells benötigt werden. Dazu gehören das Schema, bezeichnete Äußerungen, Features und Anwendungseinstellungen.

### <a name="training-errors"></a>Trainingsfehler

Trainingsfehler sind Vorhersagen zu Ihren Trainingsdaten, die nicht mit ihren Bezeichnungen übereinstimmen.

## <a name="utterance"></a>Äußerung

Eine [Äußerung](luis-concept-utterance.md) ist eine Benutzereingabe, bei der es sich um einen kurzen Text handelt, der repräsentativ für einen Satz in einer Unterhaltung ist. Sie ist eine Aussage in natürlicher Sprache, z. B. „Buche 2 Tickets nach Seattle am nächsten Dienstag“. Beispieläußerungen werden hinzugefügt, um das Modell zu trainieren, und das Modell sagt zur Laufzeit neue Äußerungen vorher

## <a name="version"></a>Version

Eine LUIS-[Version](luis-how-to-manage-versions.md) ist eine spezifische Instanz einer LUIS-App, die einer LUIS-App-ID und dem veröffentlichten Endpunkt zugeordnet ist. Jede LUIS-App hat mindestens eine Version.