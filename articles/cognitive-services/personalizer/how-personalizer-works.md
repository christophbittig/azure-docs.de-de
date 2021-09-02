---
title: Funktionsweise der Personalisierung – Personalisierung
description: Die _Personalisierungsschleife_ verwendet maschinelles Lernen, um das Modell zu erstellen, das die Top-Aktion für Ihre Inhalte vorhersagt. Das Modell wird ausschließlich anhand Ihrer Daten trainiert, die Sie mit den Rang- und Relevanzaufrufen an das Modell gesendet haben.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 9c9d9f2b06a34feca2974bf97fb5d494c6105471
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829928"
---
# <a name="how-personalizer-works"></a>Funktionsweise der Personalisierung

Die Personalisierungsressource, Ihre _Lernschleife_, verwendet maschinelles Lernen, um das Modell zu erstellen, das die Top-Aktion für Ihre Inhalte vorhersagt. Das Modell wird ausschließlich anhand Ihrer Daten trainiert, die Sie mit den **Rang**- und **Relevanzaufrufen** an das Modell gesendet haben. Jede Schleife ist vollkommen unabhängig von den anderen.

## <a name="rank-and-reward-apis-impact-the-model"></a>Rang- und Relevanz-APIs wirken sich auf das Modell aus

Sie senden _Aktionen mit Features_ und _Kontextfeatures_ an die Rang-API. Die **Rangfolge**-API entscheidet über die Verwendung einer der beiden folgenden Optionen:

* _Exploit_ (Nutzen): Das aktuelle Modell entscheidet über die beste Aktion auf Grundlage früherer Daten.
* _Erkunden_: Auswählen einer anderen Aktion als der Top-Aktion. Sie [konfigurieren diesen Prozentsatz](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) für Ihre Personalisierungsressource im Azure-Portal.

Sie bestimmen den Relevanzwert und senden diesen Wert an die Relevanz-API. Die **Relevanz**-API:

* Sammelt Daten zum Trainieren des Modells durch Aufzeichnung der Merkmale und Relevanzbewertungen jedes Rangfolgeaufrufs.
* Verwendet diese Daten zum Aktualisieren des Modells, basierend auf der in der _Lernrichtlinie_ angegebenen Konfiguration.

## <a name="your-system-calling-personalizer"></a>Ihr System, das die Personalisierung aufruft

Die folgende Abbildung zeigt den Fluss der Rangfolge- und Relevanzaufrufe durch die Architektur:

![Alternativer Text](./media/how-personalizer-works/personalization-how-it-works.png "Funktionsweise der Personalisierung")

1. Sie senden _Aktionen mit Features_ und _Kontextfeatures_ an die Rang-API.

    * Die Personalisierung entscheidet, ob das aktuelle Modell genutzt oder neue Auswahlmöglichkeiten für das Modell erkundet werden sollen.
    * Das Rangfolgeergebnis wird an EventHub gesendet.
1. Der oberste Rang wird als _Relevanzaktions-ID_ an Ihr System zurückgegeben.
    Ihr System zeigt diesen Inhalt an und bestimmt anhand Ihrer Geschäftsregeln einen Belohnungsscore.
1. Ihr System gibt die Relevanzbewertung an die Lernschleife zurück.
    * Wenn die Personalisierung die Relevanz empfängt, wird diese an EventHub gesendet.
    * Rang und Relevanz sind korreliert.
    * Das KI-Modell wird auf Grundlage der Korrelationsergebnisse aktualisiert.
    * Die Rückschluss-Engine wird mit dem neuen Modell aktualisiert.

## <a name="personalizer-retrains-your-model"></a>Erneutes Trainieren Ihres Modell durch die Personalisierung

Die Personalisierung trainiert Ihr Modell auf der Grundlage Ihrer Einstellung für die **Häufigkeit von Modellaktualisierungen** in Ihrer Personalisierungsressource im Azure-Portal.

Die Personalisierung verwendet alle derzeit beibehaltenen Daten, basierend auf der Einstellung für die **Datenaufbewahrung** (in Anzahl von Tagen) für Ihre Personalisierungsressource im Azure-Portal.

## <a name="research-behind-personalizer"></a>Die Forschung hinter der Personalisierung

Personalisierung basiert auf innovativer Wissenschaft und Forschung im Bereich des [vertiefenden Lernens](concepts-reinforcement-learning.md), einschließlich Artikeln, Forschungsaktivitäten und Bereichen der laufenden Forschung bei Microsoft Research.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Top-Szenarien](where-can-you-use-personalizer.md) für die Personalisierung