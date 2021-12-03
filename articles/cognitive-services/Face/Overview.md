---
title: Was ist der Azure-Gesichtserkennungsdienst?
titleSuffix: Azure Cognitive Services
description: Der Azure-Gesichtserkennungsdienst verfügt über KI-Algorithmen zum Ermitteln, Erkennen und Analysieren menschlicher Gesichter in Bildern.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Gesichtserkennung, Gesichtserkennungssoftware, Gesichtsanalyse, Gesichtsabgleich, Gesichtserkennungs-App, Gesichtserkennungssuche nach Bild, Gesichtserkennungssuche
ms.openlocfilehash: 370d6c17dfaf2d3544506e4e2ab4218f1a48d1fa
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370800"
---
# <a name="what-is-the-azure-face-service"></a>Was ist der Azure-Gesichtserkennungsdienst?

> [!WARNING]
> Am 11. Juni 2020 kündigte Microsoft an, dass keine Technologie zur Gesichtserkennung mehr an die Polizeibehörden in den USA verkauft wird, bis es eine strenge, auf den Menschenrechten basierende Regelung gibt. Daher dürfen Kunden keine Gesichtserkennungsfeatures oder in Azure-Diensten enthaltenen Funktionen (wie Gesichtserkennung oder Video Indexer) verwenden, wenn es sich bei dem Kunden um eine Polizeibehörde in den USA handelt oder wenn der Kunde die Nutzung derartiger Dienste durch oder für eine Polizeibehörde zulässt. Wenn Sie eine neue Gesichtserkennungsressource erstellen, müssen Sie im Azure-Portal bestätigen und sich damit einverstanden erklären, dass Sie den Dienst nicht über oder für eine Polizeibehörde in den USA verwenden, und dass Sie die [RAI-Dokumentation (Responsible AI)](../cognitive-services-apis-create-account-cli.md#prerequisites) gelesen haben und diesen Dienst entsprechend verwenden.

Der Azure-Gesichtserkennungsdienst verfügt über KI-Algorithmen zum Ermitteln, Erkennen und Analysieren menschlicher Gesichter in Bildern. Gesichtserkennungssoftware ist in vielen verschiedenen Szenarien wichtig, z. B. Identitätsüberprüfung, berührungslose Zugangskontrolle und Gesichtsunschärfe aus Datenschutzgründen.

Identitätsüberprüfung: Überprüft die Identität einer Person anhand einer von der Behörde ausgestellten ID-Karte, z. B. einem Pass, eines Führerscheins oder anderen Registrierungsbilds, um den Zugriff auf digitale oder physische Dienste zu gewähren oder ein Konto wiederherzustellen. Bestimmte Zugangsszenarien sind das Eröffnen eines neuen Kontos, das Überprüfen von Beschäftigten oder die Durchführung eines Onlineassessments. Die Identitätsüberprüfung kann einmal durchgeführt werden, wenn eine Person integriert wird, und wiederholt, wenn jemand auf einen digitalen oder physischen Dienst zugreift.

Berührungslose Zugangskontrolle: Im Vergleich zu heutigen Methoden wie Karten oder Tickets ermöglicht die Gesichtserkennung eine verbesserte Zugangskontrolle, während gleichzeitig die Hygiene- und Sicherheitsrisiken durch das Teilen einer Karte, Verlust oder Diebstahl reduziert werden. Die Gesichtserkennung unterstützt den Eincheckprozess von Personen beim Einchecken in Flughäfen, Stadien, Vergnügungsparks oder Gebäuden sowie die Rezeptionen von Büros, Krankenhäusern, Fitnessstudios, Clubs oder Schulen.

Gesichtsbearbeitung: Erkannte Gesichter von Personen, die in einem Video aufgezeichnet wurden, werden bearbeitet oder unscharf gemacht, um ihre Privatsphäre zu schützen.

Diese Dokumentation enthält die folgenden Arten von Artikeln:
* In den [Schnellstarts](./Quickstarts/client-libraries.md) finden Sie Schritt-für-Schritt-Anleitungen, mit denen Sie Aufrufe an den Dienst senden können und in kurzer Zeit Ergebnisse erhalten. 
* Die [Anleitungen](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* Die [konzeptionellen Artikel](./concepts/face-detection.md) enthalten ausführliche Erläuterungen der Funktionen und Features eines Diensts.
* Die [Tutorials](./enrollment-overview.md) sind ausführlichere Leitfäden, in denen die Verwendung dieses Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird.

## <a name="face-detection-and-analysis"></a>Gesichtserkennung und Analyse

Die Gesichtserkennung ist in allen anderen Szenarien als erster Schritt erforderlich. Mit der Erkennungs-API können menschliche Gesichter in einem Bild erkannt und die Rechteckkoordinaten ihrer Positionen zurückgegeben werden. Außerdem wird eine eindeutige ID zurückgegeben, die die gespeicherten Gesichtsdaten darstellt, die in späteren Vorgängen zum Identifizieren oder Überprüfen von Gesichtern verwendet werden.

Optional können bei der Gesichtserkennung auch verschiedene Gesichtsattribute extrahiert werden, z. B. Kopfhaltung, Alter, Emotionen, Gesichtsbehaarung und Brille. Bei diesen Attributen handelt es sich um allgemeine Vorhersagen, nicht um tatsächliche Klassifizierungen. Einige Attribute sind nützlich, um sicherzustellen, dass Ihre Anwendung hochwertige Gesichtsdaten erhält, wenn Benutzer sich selbst einem Gesichtserkennungsdienst hinzufügen (z. B. könnte Ihre Anwendung Benutzern empfehlen, ihre Sonnenbrillen abzunehmen, wenn sie Sonnenbrillen tragen).

> [!NOTE]
> Die Funktion zur Gesichtserkennung ist auch über den [Dienst für maschinelles Sehen](../computer-vision/overview.md) verfügbar. Wenn Sie jedoch weitere Gesichtserkennungsvorgänge wie „Identifizieren“, „Überprüfen“, „Ähnliches suchen“ oder „Gesichtsgruppierung“ verwenden möchten, sollten Sie stattdessen diesen Dienst verwenden.

Weitere Informationen zur Gesichtserkennung und -analyse finden Sie im Artikel zu den Konzepten der [Gesichtserkennungs-API](concepts/face-detection.md). Lesen Sie auch die Referenzdokumentation zur [Erkennungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).


## <a name="identity-verification"></a>Identitätsüberprüfung

Moderne Unternehmen und Apps können mit den Gesichtsidentifikations- und Gesichtsüberprüfungsvorgängen überprüfen, ob es sich bei einem Benutzer um denjenigen handelt, als der er sich ausgibt. 

### <a name="identification"></a>Identifikation

Die Gesichtserkennung kann den „1:n“-Abgleich eines Gesichts in einem Bild mit einer Gruppe von Gesichtern in einem sicheren Repository adressieren. Übereinstimmungskandidaten werden basierend auf der Genauigkeit der Übereinstimmung ihrer Gesichtsdaten mit dem Abfragegesicht zurückgegeben. Dieses Szenario wird verwendet, um einer bestimmten Gruppe von Personen Zugang zu einem Gebäude oder zum Flughafen zu gewähren oder den Benutzer eines Geräts zu überprüfen.

Die folgende Abbildung enthält ein Beispiel für eine Datenbank mit dem Namen `"myfriends"`. Jede Gruppe kann bis zu 1 Million unterschiedliche Personenobjekte enthalten. Für jedes Personenobjekt können bis zu 248 Gesichter registriert sein.

![Raster mit drei Spalten für unterschiedliche Personen, jeweils mit drei Zeilen mit Bildern von Gesichtern](./Images/person.group.clare.jpg)

Nachdem Sie eine Gruppe erstellt und trainiert haben, können Sie die Identifizierung eines neuen erkannten Gesichts anhand der Gruppe durchführen. Wenn das Gesicht als eine Person aus der Gruppe erkannt wird, wird das Personenobjekt zurückgegeben.

### <a name="verification"></a>Überprüfung

Der Überprüfungsvorgang beantwortet die Frage „Gehören diese beiden Gesichter zu derselben Person?“. 

Die Überprüfung ist auch ein 1:1-Abgleich eines Gesichts in einem Bild mit einem einzelnen Gesicht in einem sicheren Repository oder Foto, um sicherzustellen, dass es sich um die gleiche Person handelt. Die Überprüfung kann für die Identitätsüberprüfung verwendet werden, z. B. in einer Banking-App, die es Benutzern ermöglicht, remote ein Kreditkonto zu eröffnen, indem sie ein Selfie und ein Foto eines Lichtbildausweises zur Bestätigung ihrer Identität aufnehmen. 

Weitere Informationen zur Überprüfung der Identifikation finden Sie im Konzeptleitfaden zur [Gesichtserkennung](concepts/face-recognition.md) oder in der Referenzdokumentation zur [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)- und [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)-API.


## <a name="find-similar-faces"></a>Suchen ähnlicher Gesichter

Der Vorgang „Ähnliches suchen“ ermöglicht einen Abgleich zwischen einem Zielgesicht und mehreren Kandidatengesichtern, um eine Untermenge von Gesichtern zu ermitteln, die dem Zielgesicht ähnlich sind. Dies ist hilfreich, wenn eine Gesichtserkennungssuche anhand von Bildern durchgeführt werden soll. 

Der Dienst unterstützt die beiden Arbeitsmodi **matchPerson** und **matchFace**. Im Modus **matchPerson** werden ähnliche Gesichter zurückgegeben, nachdem mit der [Überprüfungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) nach derselben Person gefiltert wurde. Im Modus **matchFace** wird der Filter für dieselbe Person ignoriert. Er gibt eine Liste mit ähnlichen Kandidatengesichtern zurück, die nicht unbedingt zu derselben Person gehören müssen.

Das folgende Beispiel zeigt das Zielgesicht:

![Lächelnde Frau](./Images/FaceFindSimilar.QueryFace.jpg)

Und diese Bilder sind die Kandidatengesichter:

![Fünf Bilder mit lächelnden Personen. Die Bilder A und B zeigen dieselbe Person.](./Images/FaceFindSimilar.Candidates.jpg)

Für die Suche nach vier ähnlichen Gesichtern gibt der Modus **matchPerson** A und B zurück. Diese zeigen dieselbe Person wie das Zielgesicht. Im Modus **matchFace** werden A, B, C und D zurückgegeben, also genau vier Kandidaten – auch wenn es sich teilweise um andere Personen handelt oder die Ähnlichkeit nur gering ist. Weitere Informationen finden Sie im Konzeptleitfaden zur [Gesichtserkennung](concepts/face-recognition.md) oder in der Referenzdokumentation zur [Find Similar-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="group-faces"></a>Gruppieren von Gesichtern

Mit dem Gruppierungsvorgang wird eine Gruppe von unbekannten Gesichtern anhand der Ähnlichkeit in kleinere Gruppen aufgeteilt. Jede Gruppe ist eine zusammenhanglose korrekte Teilmenge der ursprünglichen Gesichtergruppe. Außerdem wird ein einzelnes „messyGroup“-Array zurückgegeben, das die Gesichts-IDs enthält, für die keine Ähnlichkeiten gefunden wurden.

Für alle Gesichter einer zurückgegebenen Gruppe ist die Wahrscheinlichkeit hoch, dass sie zu derselben Person gehören, aber es kann für eine Person auch mehrere unterschiedliche Gruppen geben. Diese Gruppen werden durch einen anderen Faktor unterschieden, z. B. den Ausdruck. Weitere Informationen finden Sie im Konzeptleitfaden zur [Gesichtserkennung](concepts/face-recognition.md) oder in der Referenzdokumentation zur [Group-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="sample-app"></a>Beispiel-App

Die folgenden Beispielanwendungen zeigen einige Verwendungsmöglichkeiten des Gesichtserkennungsdiensts:

- [FamilyNotes UWP app](https://github.com/Microsoft/Windows-appsample-familynotes) (FamilyNotes-UWP-App): Eine UWP-App (Universelle Windows-Plattform), bei der die Gesichtsidentifizierung zusammen mit Spracheingabe, Cortana, Freihand und Kamera in einem Szenario zum Austauschen von Familiennotizen genutzt wird.

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Ressourcen müssen Entwickler, die den Gesichtserkennungsdienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie eine Schnellstartanleitung zum Codieren der Basiskomponenten einer Gesichtserkennungs-App in der Sprache Ihrer Wahl.

- [Schnellstart: Clientbibliothek](quickstarts/client-libraries.md)
