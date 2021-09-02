---
title: Terminologie – Personalisierung
description: Die Personalisierung verwendet Terminologie aus dem vertiefenden Lernen. Diese Begriffe werden im Azure-Portal und in den APIs verwendet.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: bdd26d167a94826be4330f878df01cde43e853cb
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829919"
---
# <a name="personalizer-terminology"></a>Terminologie zur Personalisierung

Die Personalisierung verwendet Terminologie aus dem vertiefenden Lernen. Diese Begriffe werden im Azure-Portal und in den APIs verwendet.

## <a name="conceptual-terminology"></a>Konzeptionelle Terminologie

* **Lernschleife**: Sie erstellen eine Personalisierungsressource, eine sogenannte _Lernschleife_, für jeden Teil Ihrer Anwendung, der von der Personalisierung profitieren kann. Wenn Sie mehr als eine zu personalisierende Erfahrung haben, erstellen Sie für jede eine Schleife.

* **Modell**: Ein Personalisierungsmodell erfasst alle Daten, die über Benutzerverhalten gelernt wurden, erhält Trainingsdaten aus der Kombination der Argumente, die Sie an die Rangfolge- und Relevanzaufrufe senden, und besitzt ein Trainingsverhalten, das von der Lernrichtlinie bestimmt wird.

* **Onlinemodus**: Das standardmäßige [Lernverhalten](#learning-behavior) für die Personalisierung, bei dem Ihre Lernschleife maschinelles Lernen verwendet, um das Modell zu erstellen, das die **Top-Aktion** für Ihre Inhalte vorhersagt.

* **Ausbildungsmodus**: Ein [Lernverhalten](#learning-behavior), das beim Warmstart eines Personalisierungsmodels beim Training hilft, ohne die Ergebnisse und Aktionen der Anwendung zu beeinflussen.

## <a name="learning-behavior"></a>Lernverhalten:

* **Onlinemodus**: Gibt die beste Aktion zurück. Ihr Modell antwortet auf Rangaufrufe mit der besten Aktion und verwendet Relevanzsaufrufe, um zu lernen und die Auswahl im Lauf der Zeit zu verbessern.
* **[Ausbildungsmodus](concept-apprentice-mode.md)** : Lernen Sie im Ausbildungsmodus. Ihr Modell lernt, indem es das Verhalten Ihres bestehenden Systems beobachtet. Rangfolgeaufrufe geben immer die **Standardaktion** (Baseline) der Anwendung zurück.

## <a name="personalizer-configuration"></a>Personalisierungskonfiguration

Die Personalisierung wird über das [Azure-Portal](https://portal.azure.com) konfiguriert.

* **Relevanzen**: Konfigurieren Sie die Standardwerte für die Wartezeit für Relevanz, die Standardrelevanz und die Relevanzaggregationsrichtlinie.

* **Erkundung**: Konfigurieren Sie den Prozentsatz der Rangaufrufe, die für die Erkundung verwendet werden sollen

* **Häufigkeit der Modellaktualisierung**: Wie oft das Modell neu trainiert wird.

* **Datenaufbewahrung**: Wie viele Tage an Daten zu speichern sind. Dies kann sich auf Offlineauswertungen auswirken, die zur Verbesserung Ihrer Lernschleife verwendet werden.

## <a name="use-rank-and-reward-apis"></a>Verwenden von Rang- und Relevanz-APIs

* **Rang**: In Anbetracht der Aktionen mit Features und der Kontextfeatures verwenden Sie „explore“ (erkunden) oder „exploit“ (nutzen), um die oberste Aktion (Inhaltselement) zurückzugeben.

    * **Aktionen**: Aktionen sind die Inhaltselemente, z. B. Produkte oder Werbeaktionen, unter denen ausgewählt wird. Die Personalisierung wählt die Top-Aktion (zurückgegebene Relevanzaktions-ID) aus, die Ihren Benutzern über die Rang-API angezeigt werden soll.

    * **Context**: Um eine genauere Rangfolge bereitzustellen, geben Sie Informationen zu Ihrem Kontext an, beispielsweise:
        * Ihre Benutzer.
        * Das Gerät, das sie verwenden.
        * Die aktuelle Zeit.
        * Andere Daten über die aktuelle Situation.
        * Vergangene Daten über den Benutzer oder Kontext.

        Ihre spezifische Anwendung kann abweichende Kontextinformationen haben.

    * **[Merkmale](concepts-features.md)** : Eine Einheit von Informationen über ein Inhaltselement oder einen Benutzerkontext. Stellen Sie sicher, dass Sie nur aggregierte Features verwenden. Verwenden Sie keine bestimmten Zeiten, Benutzer-IDs oder andere nicht aggregierte Daten als Features.

        * Ein _Aktionsfeature_ sind Metadaten über den Inhalt.
        * Ein _Kontextfeature_ sind Metadaten über den Kontext, in dem der Inhalt angezeigt wird.

* **Exploration** (Erkunden): Der Personalisierungsdienst erkundet, wenn er, anstatt die beste Aktion zurückzugeben, eine andere Aktion für den Benutzer auswählt. Der Personalisierungsdienst kann durch Erkunden Verschiebungen und Stagnation vermeiden sowie sich an laufendes Benutzerverhalten anpassen.

* **Exploitation** (Nutzen): Der Personalisierungsdienst verwendet das aktuelle Modell, um über die beste Aktion auf Grundlage zurückliegender Daten zu entscheiden.

* **Experimentdauer**: Die Zeitspanne, die der Personalisierungsdienst auf eine Relevanz wartet, beginnend ab dem Zeitpunkt, zu dem der Rangfolgeaufruf für dieses Ereignis erfolgte.

* **Inaktive Ereignisse**: Ein inaktives Ereignis ist eines, für das Sie die Rangfolge aufgerufen haben, sich aber nicht sicher sind, ob der Benutzer jemals das Ergebnis sehen wird, weil dies Entscheidungen der Clientanwendung unterliegt. Inaktive Ereignisse ermöglichen es Ihnen, Personalisierungsergebnisse zu erstellen und zu speichern und dann zu entscheiden, sie später zu verwerfen, ohne dass dies das Machine Learning-Modell beeinträchtigt.


* **Relevanz**: Ein Maß dafür, wie der Benutzer auf die von der Rang-API zurückgegebene Relevanzaktions-ID reagiert hat, als eine Bewertung zwischen 0 und 1. Der Wert von 0 bis 1 wird von Ihrer Geschäftslogik festgelegt, basierend darauf, wie die Auswahl dabei geholfen hat, Ihre geschäftlichen Ziele der Personalisierung zu erreichen. Die Lernschleife speichert diese Relevanz nicht als individuellen Benutzerverlauf.

## <a name="evaluations"></a>Auswertungen

### <a name="offline-evaluations"></a>Offlineauswertungen

* **Auswertung**: Eine Offlineauswertung ermittelt die beste Lernrichtlinie für Ihre Schleife auf der Grundlage der Daten Ihrer Anwendung.

* **Lernrichtlinie**: Die Art und Weise, wie die Personalisierung ein Modell bei jedem Ereignis trainiert, wird durch einige Parameter bestimmt, die die Funktionsweise des Machine Learning-Algorithmus beeinflussen. Eine neue Lernschleife beginnt mit einer standardmäßigen **Lernrichtlinie**, die eine moderate Leistung erzielen kann. Im Zuge von [Auswertungen](concepts-offline-evaluation.md) erstellt die Personalisierung neue Lernrichtlinien, die speziell für die Anwendungsfälle Ihrer Schleife optimiert sind. Mit Richtlinien, die im Zuge der Auswertung generiert und für die einzelnen spezifischen Schleifen optimiert wurden, verbessert sich die Leistung der Personalisierung erheblich. Die Lernrichtlinie wird im Bereich der **Modell- und Lerneinstellungen** für die Personalisierungsressource im Azure-Portal als _Lerneinstellungen_ bezeichnet.

### <a name="apprentice-mode-evaluations"></a>Auswertungen im Ausbildungsmodus

Der Ausbildungsmodus bietet die folgenden **Auswertungsmetriken**:
* **Baseline – durchschnittliche Relevanz**:  Durchschnittliche Relevanzen für die Standardwerte der Anwendung (Baseline).
* **Personalisierung – durchschnittliche Relevanz**: Durchschnitt der gesamten Relevanzen, die Personalisierungsprogramme potenziell erreicht hätten.
* **Durchschnittliche gleitende Relevanz**: Verhältnis von Baseline und Personalisierungsrelevanz – normalisiert über die letzten 1000 Ereignisse.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Ethik und verantwortungsvolle Verwendung](ethics-responsible-use.md)