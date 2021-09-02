---
title: 'Automatische Optimierung: Personalisierung'
description: Dieser Artikel bietet eine konzeptionelle Übersicht über das Feature zur automatischen Optimierung für den Azure-Personalisierungsdienst.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 603dc55b96d12610bc023487e731c7599841c985
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829415"
---
# <a name="personalizer-auto-optimize-preview"></a>Automatische Optimierung der Personalisierung (Vorschau)


## <a name="introduction"></a>Einführung
Die automatische Optimierung der Personalisierung erspart Ihnen manuellen Aufwand, eine Personalisierungsschleife mit bester Machine Learning-Leistung aufrechtzuerhalten, indem automatisch nach verbesserten Lerneinstellungen gesucht wird, mit deren Hilfe Ihre Modelle trainiert und angewendet werden. Die Personalisierung hat strenge Kriterien zur Anwendung neuer Lerneinstellungen, um sicherzustellen, dass Verbesserungen wahrscheinlich keine Belohnungsverluste nach sich ziehen.

Die automatische Optimierung der Personalisierung befindet sich in der Public Preview und Features, Ansätze und Prozesse werden sich auf Basis des Benutzerfeedbacks ändern.

## <a name="when-to-use-auto-optimize"></a>Verwendung der automatischen Optimierung
In den meisten Fällen ist das Aktivieren der automatischen Optimierung die beste Option. Die automatische Optimierung ist für neue Personalisierungsschleifen standardmäßig *aktiviert.*

Die automatische Optimierung kann in den folgenden Situationen hilfreich sein:
* Sie erstellen Anwendungen, die von vielen Mandanten verwendet werden, und jede erhält ihre eigene(n) Personalisierungsschleife(n), beispielsweise, wenn Sie mehrere E-Commerce-Websites hosten. Mit der automatischen Optimierung können Sie den manuellen Aufwand zum Optimieren der Lerneinstellungen für eine große Anzahl von Personalisierungsschleifen vermeiden.
* Sie haben die Personalisierung bereitgestellt und ihre einwandfreie Funktion überprüft, gute Belohnungen erhalten und sichergestellt, dass in Ihren Features keine Fehler oder Probleme auftreten.

> [!NOTE]
> Die automatische Optimierung überschreibt in regelmäßigen Abständen die Lerneinstellungen der Personalisierung. Wenn Ihr Anwendungsfall oder Ihre Branche die Überwachung und Archivierung von Modellen und Einstellungen erfordert, oder Sie Sicherungen vorheriger Einstellungen benötigen, können Sie mit der Personalisierungs-API Lerneinstellungen abzurufen oder sie über die Azure-Portal herunterladen.

## <a name="how-to-enable-and-disable-auto-optimize"></a>Vorgehensweise beim Aktivieren und Deaktivieren der automatischen Optimierung
Verwenden Sie zum Aktivieren der automatischen Optimierung den Umschalter auf dem Blatt „Modell- und Lerneinstellungen“ im Azure-Portal. 

Alternativ können Sie die automatische Optimierung mithilfe der Personalisierungs-`/configurations/service`-API aktivieren.

Um die automatische Optimierung zu deaktivieren, betätigen Sie den Umschalter.

## <a name="auto-optimize-reports"></a>Berichte zur automatischen Optimierung

Auf dem Blatt „Modell- und Lerneinstellungen“ können Sie den Verlauf der automatisch optimierten Ausführungen und die jeweils ausgeführten Aktionen sehen. 

Die Tabelle zeigt:
* Wann wurde eine automatische Optimierung ausgeführt?
* Welches Datenfenster wurde eingeschlossen?
* Was war die Belohnungsleistung der Lerneinstellungen „Online“, „Baseline“ und „Am besten gefunden“?
* Durchgeführte Aktionen: Ob die Lerneinstellungen aktualisiert wurden oder nicht.

Die Belohnungsleistung verschiedener Lerneinstellungen wird in jeder Verlaufszeile der automatischen Optimierung in absoluten Zahlen und in Form zur Baselineleistung relativer Prozentsätze angezeigt. 

**Beispiel**: Wenn die durchschnittliche Baselinebelohnung auf 0,20 geschätzt wird und das Onlineverhalten der Personalisierung 0,30 erreicht, werden diese als 100 % bzw. 150 % angezeigt. Wenn die automatische Optimierung Lerneinstellungen gefunden hat, die eine durchschnittliche Belohnung von 0,40 erreichen können, werden sie als 200 % angezeigt (0,40 ist 200 % von 0,20). Wenn die Konfidenzspannen dies zulassen, werden die neuen Einstellungen angewendet, und diese würden dann die Personalisierung als Onlineeinstellungen bis zur nächsten Ausführung steuern.

Ein Verlauf von bis zu 24 vorherigen Ausführungen der automatischen Optimierung wird für Ihre Analyse beibehalten. Sie können über diese Offlineauswertungen und Berichte für jede weitere Details anzeigen. Außerdem enthalten die Berichte alle Lerneinstellungen in diesem Verlauf, die Sie suchen und herunterladen oder anwenden können.

## <a name="how-it-works"></a>Funktionsweise
Die Personalisierung trainiert ständig die KI-Modelle, die sie basierend auf Belohnungen verwendet. Dieses Training erfolgt nach einigen *Lerneinstellungen*, die Hyperparameter und andere im Trainingsprozess verwendete Werte enthalten. Diese Lerneinstellungen können für Ihre spezifische Personalisierungsinstanz „optimiert“ werden. 

Die Personalisierung kann auch *Offlineauswertungen* durchzuführen. Offlineauswertungen beziehen sich auf frühere Daten und können eine statistische Schätzung der durchschnittlichen Belohnung erzeugen, die nicht zur Personalisierung gehörende Algorithmen und Modelle erzielt haben könnten. Während dieses Prozesses sucht die Personalisierung auch nach besseren Lerneinstellungen und schätzt ihre Leistung (wie viele Belohnungen sie im letzten Zeitraum erzielt hätten) für diesen vergangenen Zeitraum.

#### <a name="auto-optimize-frequency"></a>Häufigkeit der automatischen Optimierung
Die automatische Optimierung wird in regelmäßigen Abständen und auf Basis früherer Daten ausgeführt.
* Wenn Ihre Anwendung in den letzten zwei Wochen mehr als 20 MB Daten an die Personalisierung gesendet hat, werden die Daten der letzten zwei Wochen verwendet.
* Wenn Ihre Anwendung weniger gesendet hat, fügt die Personalisierung Daten aus vorherigen Tagen hinzu, bis genügend Daten zur Optimierung vorhanden sind, oder sie greift auf die am frühesten gespeicherten Daten zu (bis zur durch die Datenaufbewahrungsdauer festgelegten Anzahl von Tagen).

Die genauen Zeiten und Tage, zu denen die automatische Optimierung ausgeführt wird, werden vom Personalisierungsdienst bestimmt und schwanken im Laufe der Zeit.

#### <a name="criteria-for-updating-learning-settings"></a>Kriterien für das Aktualisieren von Lerneinstellungen

Die Personalisierung verwendet diese Belohnungsschätzungen, um zu entscheiden, ob die aktuellen Lerneinstellungen für andere geändert werden sollen. Jede Schätzung ist eine Verteilungskurve mit oberen und unteren Konfidenzgrenzen von 95 %. Die Personalisierung wendet neue Lerneinstellungen nur unter folgenden Bedingungen an:
  * Sie zeigten höhere durchschnittliche Belohnungen im Evaluierungszeitraum, UND
  * Sie weisen eine untere Grenze des Konfidenzintervalls von 95 % auf, das ist *höher* als die untere Grenze des Konfidenzintervalls von 95 % der Onlinelerneinstellungen.
Dieses Kriterium zur Maximierung der Belohnungsverbesserung bei gleichzeitigem Versuch, die Wahrscheinlichkeit des Verlusts zukünftiger Belohnungen zu eliminieren, wird von der Personalisierung verwaltet und basiert auf der Forschung im Bereich der [Seldonschen Algorithmen](https://aisafety.cs.umass.edu/overview.html) und der KI-Sicherheit.

#### <a name="limitations-of-auto-optimize"></a>Einschränkungen der automatischen Optimierung

Die automatische Optimierung der Personalisierung basiert auf einer Auswertung eines vergangenen Zeitraums, um die Leistung in der Zukunft einzuschätzen. Aufgrund externer Faktoren aus der Welt, Ihrer Anwendung und Ihrer Benutzer ist es möglich, dass diese Schätzungen und Vorhersagen zu den Modellen der Personalisierung, die für den letzten Zeitraum durchgeführt wurden, für die Zukunft nicht repräsentativ sind.

Die Vorschau der automatischen Optimierung ist für Personalisierungsschleifen nicht verfügbar, für die die Vorschaufunktion der Personalisierungs-API mit mehreren Slots aktiviert ist. 

## <a name="next-steps"></a>Nächste Schritte

* [Offlineauswertungen](concepts-offline-evaluation.md)
* [Lernrichtlinie und -einstellungen](concept-active-learning.md)
* [Analysieren der Personalisierung mit einer Offlineauswertung](how-to-offline-evaluation.md) 
* [Forschung zur KI-Sicherheit](https://aisafety.cs.umass.edu/overview.html) 
