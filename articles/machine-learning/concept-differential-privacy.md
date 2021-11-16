---
title: Differenzieller Datenschutz beim maschinellen Lernen (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, was differenzieller Datenschutz ist und wie differenzielle private Systeme den Datenschutz bewahren.
author: luisquintanilla
ms.author: luquinta
ms.date: 10/21/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.custom: responsible-ml, mktng-kw-nov2021
ms.openlocfilehash: fc3606a4d893152b87fc5f5f47d987c16ce65724
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842555"
---
# <a name="what-is-differential-privacy-in-machine-learning-preview"></a>Was ist differenzieller Datenschutz beim maschinellen Lernen (Vorschau)?

Erfahren Sie etwas über den differenziellen Datenschutz beim maschinellen Lernen und seine Funktionsweise.

In dem Maße, wie die von einer Organisation gesammelten und für Analysen verwendeten Datenmengen zunehmen, nehmen auch die Bedenken hinsichtlich des Datenschutzes und der Sicherheit zu. Analysen erfordern Daten. Je mehr Daten für das Training von Machine Learning-Modellen verwendet werden, desto genauer sind sie in der Regel. Wenn für diese Analysen persönliche Informationen verwendet werden, ist es besonders wichtig, dass die Daten während ihrer gesamten Verwendung privat (geschützt) bleiben.

## <a name="how-differential-privacy-works"></a>Funktionsweise des differenziellen Datenschutzes

Der differenzielle Datenschutz umfasst eine Reihe von Systemen und Methoden, die dazu beitragen, die Daten von Einzelpersonen sicher und privat zu halten. In Machine Learning-Lösungen kann differenzieller Datenschutz erforderlich sein, um die Einhaltung gesetzlicher Bestimmungen zu gewährleisten.

> [!div class="mx-imgBorder"]
> ![Prozess des differenziellen Datenschutzes beim maschinellen Lernen](./media/concept-differential-privacy/differential-privacy-machine-learning.jpg)

In herkömmlichen Szenarien werden die Rohdaten in Dateien und Datenbanken gespeichert. Wenn Benutzer Daten analysieren, verwenden sie in der Regel diese Rohdaten. Dies ist bedenklich, da dadurch die Privacy- und Datenschutzgesetze des Einzelnen verletzt werden könnten. Der differenzielle Datenschutz versucht, diesem Problem zu begegnen, indem er den Daten entsprechende „Stördaten“ oder Zufallselemente hinzufügt, sodass die Benutzer keine einzelnen Datenpunkte identifizieren können. Zumindest bietet ein solches System eine plausible Ablehnbarkeit. Daher wird mit eingeschränkten Auswirkungen auf die Genauigkeit der Daten der Datenschutz von Einzelpersonen beibehalten.

In differenziellen privaten Systemen werden Daten durch Anforderungen freigegeben, die als **Abfragen** bezeichnet werden. Wenn ein Benutzer eine Datenabfrage übermittelt, fügen Operationen, die als **Datenschutzmechanismen** bezeichnet werden, Stördaten zu den angeforderten Daten hinzu. Datenschutzmechanismen geben statt der Rohdaten eine *Angleichung der Daten* zurück. Dieses den Datenschutz wahrende Ergebnis wird in einem **Bericht** festgehalten. Berichte bestehen aus zwei Teilen, den eigentlichen Daten, die berechnet wurden, und einer Beschreibung, wie die Daten erstellt wurden.

## <a name="differential-privacy-metrics"></a>Metriken des differenziellen Datenschutzes

Der differenzielle Datenschutz versucht davor zu schützen, dass ein Benutzer eine unbestimmte Anzahl von Berichten erstellen kann, um letztendlich vertrauliche Daten offenzulegen. Ein als **Epsilon** bezeichneter Wert misst, wie ungenau oder privat ein Bericht ist. Epsilon hat eine umgekehrte Beziehung zu Stördaten oder Datenschutz. Je niedriger der Epsilon-Wert, desto ungenauer (und geschützter) sind die Daten.

Epsilon-Werte sind nicht negativ. Werte unter 1 bieten die völlige plausible Ablehnbarkeit. Alle Werte über 1 sind mit einem höheren Risiko der Offenlegung der tatsächlichen Daten verbunden. Wenn Sie Machine Learning-Lösungen mit differenziellem Datenschutz implementieren, möchten Sie Daten mit Epsilon-Werten zwischen 0 und 1 verwenden.

Ein weiterer direkt mit Epsilon korrelierter Wert ist **Delta**. Delta ist ein Maß für die Wahrscheinlichkeit, dass ein Bericht nicht vollständig privat ist. Je höher das Delta, desto höher das Epsilon. Da diese Werte korreliert sind, wird Epsilon häufiger eingesetzt.

## <a name="limit-queries-with-a-privacy-budget"></a>Begrenzen von Abfragen mit einem Datenschutzbudget

Um den Datenschutz in Systemen sicherzustellen, in denen mehrere Abfragen erlaubt sind, definiert der differenzielle Datenschutz eine Obergrenze für die Anzahl der Abfragen. Diese Obergrenze wird als **Datenschutzbudget** bezeichnet. Datenschutzbudgets verhindern, dass Daten über mehrere Abfragen hinweg neu erstellt werden. Datenschutzbudgets wird ein Epsilon-Betrag zugewiesen, normalerweise zwischen 1 und 3, um das Risiko einer erneuten Identifizierung zu begrenzen. Bei der Erstellung von Berichten wird in den Datenschutzbudgets der Epsilon-Wert der einzelnen Berichte sowie der Gesamtwert für alle Berichte erfasst. Nachdem ein Datenschutzbudget aufgebraucht oder abgelaufen ist, können Benutzer nicht mehr auf Daten zugreifen. 

## <a name="reliability-of-data"></a>Zuverlässigkeit von Daten

Obwohl die Wahrung des Datenschutzes das Ziel sein sollte, gibt es einen Kompromiss, wenn es um die Anwendbarkeit und Zuverlässigkeit der Daten geht. In der Datenanalyse kann die Genauigkeit als ein Maß der Unsicherheit angesehen werden, die durch Fehler bei der Stichprobenentnahme (Sampling) eingeführt wird. Diese Unsicherheit hält sich tendenziell in gewissen Grenzen. **Genauigkeit** aus Sicht des differenziellen Datenschutzes misst stattdessen die Zuverlässigkeit der Daten, die durch die Unsicherheit beeinträchtigt wird, die durch die Datenschutzmechanismen eingeführt wurde. Kurz gesagt, ein höheres Maß an Stördaten oder Datenschutz führt zu Daten, die einen niedrigeren Epsilon-Wert sowie eine geringere Genauigkeit und Zuverlässigkeit aufweisen. 

## <a name="open-source-differential-privacy-libraries"></a>Open-Source-Bibliotheken für den differenziellen Datenschutz

SmartNoise ist ein Open-Source-Projekt, das Komponenten zum Erstellen von Machine Learning-Lösungen mit differenziellem Datenschutz enthält. SmartNoise umfasst folgende allgemeine Komponenten:

- SmartNoise-Kernbibliothek
- SmartNoise SDK-Bibliothek

### <a name="smartnoise-core"></a>SmartNoise-Kernbibliothek

Die Kernbibliothek umfasst die folgenden Datenschutzmechanismen zur Implementierung eines differenziellen privaten Systems:

|Komponente  |BESCHREIBUNG  |
|---------|---------|
|Analyse     | Eine Graphenbeschreibung von willkürlichen Berechnungen. |
|Validierungssteuerelement     | Eine Rust-Bibliothek, die eine Reihe von Tools zur Überprüfung und Ableitung der notwendigen Voraussetzungen dafür enthält, dass eine Analyse differenziell privat ist.          |
|Typ     | Das Medium zur Durchführung der Analyse. Die Referenzruntime ist in Rust geschrieben, aber die Runtimes können je nach Datenbedarf mit jedem beliebigen Berechnungsframework wie SQL und Spark geschrieben werden.        |
|Bindungen     | Sprachbindungen und Hilfsbibliotheken zur Erstellung von Analysen. Gegenwärtig stellt SmartNoise Python-Bindungen zur Verfügung. |

### <a name="smartnoise-sdk"></a>SmartNoise SDK

Die Systembibliothek stellt die folgenden Tools und Dienste für die Arbeit mit Tabellen- und relationalen Daten bereit:

|Komponente  |BESCHREIBUNG  |
|---------|---------|
|Datenzugriff     | Bibliothek, die SQL-Abfragen abfängt und verarbeitet und Berichte erstellt. Diese Bibliothek ist in Python implementiert und unterstützt die folgenden ODBC- und DBAPI-Datenquellen:<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Preston</li><li>Pandas</li></ul>|
|Dienst     | Ausführungsdienst, der einen REST-Endpunkt bereitstellt, um Anforderungen oder Abfragen für freigegebene Datenquellen zu bearbeiten. Der Dienst ist so konzipiert, dass er die Zusammenstellung von differenziellen Datenschutzmodulen ermöglicht, die auf Anforderungen mit unterschiedlichen Delta- und Epsilon-Werten, auch als heterogene Anforderungen bekannt, arbeiten. Diese Referenzimplementierung berücksichtigt zusätzliche Auswirkungen von Abfragen auf korrelierte Daten. |
|Auswerter     | Stochastischer Auswerter, der auf Verstöße hinsichtlich Datenschutz, Genauigkeit und Trend (Bias) prüft. Der Auswerter unterstützt die folgenden Tests: <ul><li>Datenschutzprüfung – Bestimmt, ob ein Bericht die Bedingungen des differenziellen Datenschutzes einhält.</li><li>Genauigkeitsprüfung – Misst, ob die Zuverlässigkeit der Berichte bei einem Vertrauensgrad von 95 % innerhalb der oberen und unteren Begrenzungen fällt.</li><li>Hilfsprogrammprüfung – Bestimmt, ob die Vertrauensgrenzen eines Berichts nicht zu stark von den Daten abweichen und gleichzeitig der Datenschutz maximiert wird.</li><li>Trendprüfung – Misst die Verteilung von Berichten bei wiederholten Abfragen, um sicherzustellen, dass sie nicht unausgewogen sind.</li></ul> |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den differenziellen Datenschutz beim maschinellen Lernen: 

 - [Erstellen Sie ein System mit differenziellem Datenschutz](how-to-differential-privacy.md) in Azure Machine Learning.

 - Weitere Informationen zu den Komponenten von SmartNoise finden Sie in den GitHub-Repositorys für [SmartNoise Core](https://github.com/opendifferentialprivacy/smartnoise-core), [SmartNoise SDK](https://github.com/opendifferentialprivacy/smartnoise-sdk) und [SmartNoise-Beispiele](https://github.com/opendifferentialprivacy/smartnoise-samples).