---
title: Bewährte Methoden für die Verwendung der multivariaten Anomalieerkennungs-API
titleSuffix: Azure Cognitive Services
description: Bewährte Methoden für die Verwendung der multivariaten Anomalieerkennungs-API zum Anwenden von Anomalieerkennung auf Ihre Zeitreihendaten.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: Anomalieerkennung, maschinelles Lernen, Algorithmen
ms.openlocfilehash: 4114771276f4fec6dfef0e953ef9f52e165db510
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297321"
---
# <a name="best-practices-for-using-the-anomaly-detector-multivariate-api"></a>Bewährte Methoden für die Verwendung der multivariaten Anomalieerkennungs-API

Dieser Artikel enthält Anleitungen zu empfohlenen Methoden, die bei der Verwendung der MVAD-APIs (Multivariate Anomaly Detector, multivariate Anomalieerkennung) angewendet werden sollten. In diesem Tutorial gehen Sie wie folgt vor:

> [!div class="checklist"]
> * **API-Nutzung:** Erfahren Sie, wie Sie die multivariate Anomalieerkennung ohne Fehler verwenden.
> * **Datentechnik:** Erfahren Sie, wie Sie Ihre Daten am besten aufbereiten, um mit der multivariaten Anomalieerkennung eine höhere Genauigkeit zu erzielen.
> * **Häufige Probleme:** Erfahren Sie, wie Sie häufige Probleme vermeiden, die bei Kunden auftreten.
> * **Häufig gestellte Fragen:** Erhalten Sie Antworten auf häufig gestellte Fragen.

## <a name="api-usage"></a>API-Verwendung

Befolgen Sie die Anweisungen in diesem Abschnitt, um Fehler bei der Verwendung der multivariaten Anomalieerkennung zu vermeiden. Sollten weiterhin Fehler auftreten, finden Sie in der [vollständigen Liste der Fehlercodes](./troubleshoot.md) Erläuterungen und Maßnahmen, die Sie ergreifen können.

[!INCLUDE [mvad-input-params](../includes/mvad-input-params.md)]

[!INCLUDE [mvad-data-schema](../includes/mvad-data-schema.md)]


## <a name="data-engineering"></a>Datentechnik

Nachdem Sie nun den Code mit MVAD-APIs fehlerfrei ausführen, stellt sich die Frage: Wie lässt sich die Modellgenauigkeit verbessern?

### <a name="data-quality"></a>Datenqualität

* Da das Modell normale Muster aus historischen Daten lernt, sollten die Trainingsdaten den **allgemeinen Normalzustand** des Systems darstellen. Es ist für das Modell schwierig, diese Art von Mustern zu lernen, wenn die Trainingsdaten mit Anomalien angefüllt sind. Ein empirischer Schwellenwert für eine ungewöhnliche Rate liegt bei maximal **einem Prozent**, um eine gute Genauigkeit zu erzielen.
* Im Allgemeinen sollte das **Fehlwertverhältnis der Trainingsdaten unter 20 % liegen**. Zu viele fehlende Daten können dazu führen, dass automatisch aufgefüllte Werte (meist lineare oder konstante Werte) als normale Muster gelernt werden. Das kann wiederum dazu führen, dass echte (nicht fehlende) Datenpunkte als Anomalien erkannt werden.
    Es gibt jedoch Fälle, in denen ein hoher Anteil fehlender Daten akzeptabel ist. Beispiel: Sie verfügen über zwei Variablen (Zeitreihe) in einer Gruppe, die den Modus `Outer` zum Ausrichten der Zeitstempel verwenden. Eine hat eine Granularität von einer Minute, die andere eine Granularität von einer Stunde. Das führt dazu, dass für die stündliche Variable von Haus aus mindestens 59 von 60 Datenpunkten (98,33 Prozent) fehlen. In solchen Fällen ist es in Ordnung, die stündliche Variable mit dem einzigen verfügbaren (nicht fehlenden) Wert zu füllen, wenn dieser in der Regel nicht zu stark schwankt.

### <a name="data-quantity"></a>Datenmenge

* Das zugrunde liegende Modell der multivariaten Anomalieerkennung verfügt über Millionen von Parametern. Es benötigt eine Mindestanzahl von Datenpunkten, um einen optimalen Parametersatz zu lernen. Die empirische Regel ist, dass **pro Variabler mindestens 15.000 Datenpunkte (Zeitstempel)** erforderlich sind, um das Modell für eine gute Genauigkeit zu trainieren. Allgemein gilt: Je mehr Trainingsdaten, desto höher die Genauigkeit. Wenn Sie allerdings nicht so viele Daten sammeln können, empfiehlt es sich dennoch, mit weniger Daten zu experimentieren und zu prüfen, ob die verminderte Genauigkeit trotzdem akzeptabel ist.
* Bei jedem Aufruf der Rückschluss-API muss sichergestellt werden, dass die Quelldatendatei gerade genug Datenpunkte enthält. Das sind normalerweise `slidingWindow` + die Anzahl von Datenpunkten, für die **wirklich** Rückschlussergebnisse benötigt werden. Beispielsweise kann in einem Streamingszenario bei jedem Rückschluss auf der Grundlage **EINES** Zeitstempels die Datendatei nur das vorangegangene gleitende Fenster (`slidingWindow`) plus **EINEN** Datenpunkt enthalten. Anschließend können Sie eine weitere ZIP-Datei mit der gleichen Anzahl von Datenpunkten (`slidingWindow` + 1) erstellen, aber EINEN Schritt nach rechts rücken und einen weiteren Rückschlussauftrag übermitteln. 

    Alles darüber hinaus oder „vor“ dem vorangegangenen gleitenden Fenster hat keinerlei Einfluss auf das Rückschlussergebnis und führt ggf. nur zu einer Verschlechterung der Leistung. Alles darunter hat unter Umständen einen Fehler vom Typ `NotEnoughInput` zur Folge.


### <a name="timestamp-round-up"></a>Aufrundung von Zeitstempeln

In einer Gruppe von Variablen (Zeitreihe) kann jede Variable aus einer unabhängigen Quelle erfasst werden. Die Zeitstempel verschiedener Variablen können miteinander und mit der bekannten Häufigkeit inkonsistent sein. Hier ist ein einfaches Beispiel.

*Variable-1*

| timestamp | value |
| --------- | ----- |
| 12:00:01  | 1.0   |
| 12:00:35  | 1.5   |
| 12:01:02  | 0.9   |
| 12:01:31  | 2.2   |
| 12:02:08  | 1.3   |

*Variable-2*

| timestamp | value |
| --------- | ----- |
| 12:00:03  | 2.2   |
| 12:00:37  | 2.6   |
| 12:01:09  | 1.4   |
| 12:01:34  | 1.7   |
| 12:02:04  | 2.0   |

Wir haben zwei Variablen von zwei Sensoren erfasst, die alle 30 Sekunden einen Datenpunkt senden. Die Sensoren senden jedoch keine Datenpunkte mit strenger gleichmäßiger Häufigkeit, sondern manchmal früher und manchmal später. Da von der multivariaten Anomalieerkennung Korrelationen zwischen verschiedenen Variablen berücksichtigt werden, müssen Zeitstempel ordnungsgemäß ausgerichtet sein, damit die Metriken den Zustand des Systems korrekt widerspiegeln können. Im obigen Beispiel müssen Zeitstempel der Variablen 1 und 2 vor der Ausrichtung ordnungsgemäß auf ihre Häufigkeit „gerundet“ werden.

Hier sehen Sie, was passiert, wenn sie nicht vorverarbeitet werden. Wenn `alignMode` auf `Outer` festgelegt wird (Vereinigung von zwei Gruppen) sieht die zusammengeführte Tabelle wie folgt aus:

| timestamp | Variable-1 | Variable-2 |
| --------- | -------- | -------- |
| 12:00:01  | 1.0      | `nan`    |
| 12:00:03  | `nan`    | 2.2      |
| 12:00:35  | 1.5      | `nan`    |
| 12:00:37  | `nan`    | 2.6      |
| 12:01:02  | 0.9      | `nan`    |
| 12:01:09  | `nan`    | 1.4      |
| 12:01:31  | 2.2      | `nan`    |
| 12:01:34  | `nan`    | 1.7      |
| 12:02:04  | `nan`    | 2.0      |
| 12:02:08  | 1.3      | `nan`    |

`nan` gibt fehlende Werte an. Die zusammengeführte Tabelle ist wahrscheinlich nicht das, was Sie möglicherweise erwartet haben. Die Variablen 1 und 2 sind verschachtelt, und das MVAD-Modell kann keine Informationen zu Korrelationen zwischen ihnen extrahieren. Wird `alignMode` auf `Inner` festgelegt, ist die zusammengeführte Tabelle leer, da es keinen gemeinsamen Zeitstempel in den Variablen 1 und 2 gibt.

Daher müssen die Zeitstempel der Variablen 1 und 2 vorverarbeitet (auf den jeweils nächsten 30-Sekunden-Zeitstempel gerundet) werden. Dann sieht die neue Zeitreihe wie folgt aus:

*Variable-1*

| timestamp | value |
| --------- | ----- |
| 12:00:00  | 1.0   |
| 12:00:30  | 1.5   |
| 12:01:00  | 0.9   |
| 12:01:30  | 2.2   |
| 12:02:00  | 1.3   |

*Variable-2*

| timestamp | value |
| --------- | ----- |
| 12:00:00  | 2.2   |
| 12:00:30  | 2.6   |
| 12:01:00  | 1.4   |
| 12:01:30  | 1.7   |
| 12:02:00  | 2.0   |

Jetzt ist die zusammengeführte Tabelle sinnvoller.

| timestamp | Variable-1 | Variable-2 |
| --------- | -------- | -------- |
| 12:00:00  | 1.0      | 2.2      |
| 12:00:30  | 1.5      | 2.6      |
| 12:01:00  | 0.9      | 1.4      |
| 12:01:30  | 2.2      | 1.7      |
| 12:02:00  | 1.3      | 2.0      |

Werte verschiedener Variablen dicht beieinander liegender Zeitstempel sind gut ausgerichtet, und das MVAD-Modell kann jetzt Korrelationsinformationen extrahieren.

## <a name="common-pitfalls"></a>Häufige Probleme

Unabhängig von der [Fehlercodetabelle](./troubleshoot.md) haben wir von Kunden wie Ihnen einige häufige Probleme bei der Verwendung von MVAD-APIs festgestellt. Die folgende Tabelle hilft Ihnen dabei, diese Probleme zu vermeiden:

| Problem | Auswirkung |Erklärung und Lösung |
| --------- | ----- | ----- |
| Zeitstempel in Trainings- und/oder Rückschlussdaten wurden nicht aufgerundet, um sie an der jeweiligen Datenhäufigkeit der einzelnen Variablen auszurichten. | Die Zeitstempel der Rückschlussergebnisse sind nicht wie erwartet: Es sind entweder zu wenige oder zu viele Zeitstempel.  | Weitere Informationen finden Sie unter [Aufrundung von Zeitstempeln](#timestamp-round-up).  |
| Zu viele anomale Datenpunkte in den Trainingsdaten | Die Modellgenauigkeit wird beeinträchtigt, da anomale Datenpunkte während des Trainings als normale Muster behandelt werden. | Empirisch wurde ermittelt, dass die Anomalitätsrate maximal bei **einem Prozent** liegen sollte. |
| Zu wenig Trainingsdaten | Die Modellgenauigkeit ist beeinträchtigt. | Empirisch wurde ermittelt, dass zum Trainieren eines MVAD-Modells mindestens 15.000 Datenpunkte (Zeitstempel) pro Variabler benötigt werden, um eine gute Genauigkeit zu erzielen.|
| Behandlung aller Datenpunkte mit `isAnomaly`=`true` als Anomalien | Zu viele False Positives | Verwenden Sie sowohl `isAnomaly` als auch `severity` (oder `score`), um vernachlässigbare Anomalien zu erkennen, und nutzen Sie optional Gruppierungen, um die Dauer der Anomalien zu überprüfen und so Zufallsrauschen unterdrücken. Informationen zum Unterschied zwischen `severity` und `score` finden Sie weiter unten im Abschnitt [Häufig gestellte Fragen](#faq).  |
| Unterordner werden in die Datendatei gezippt, die zum Trainieren oder Generieren von Rückschlüssen verwendet wird. | Die CSV-Datendateien in Unterordnern werden beim Trainieren oder Generieren von Rückschlüssen ignoriert. | Die ZIP-Datei darf keine Unterordner enthalten. Ausführliche Informationen zur Ordnerstruktur finden Sie [hier](#folder-structure). |
| Zu viele Daten in der Rückschlussdatendatei (Beispiel: Komprimierung aller historischen Daten in der ZIP-Datei mit den Rückschlussdaten) | Es werden zwar möglicherweise keine Fehler angezeigt, die Leistung ist jedoch beeinträchtigt, wenn Sie versuchen, die ZIP-Datei in das Azure-Blob hochzuladen oder Rückschlüsse zu generieren. | Ausführliche Informationen zur Datenmenge finden Sie [hier](#data-quantity). |
| Erstellen von Anomalieerkennungsressourcen in Azure-Regionen, in denen die multivariate Anomalieerkennung (Multivariate Anomaly Detector, MVAD) noch nicht unterstützt wird, und Aufrufen von MVAD-APIs  | Beim Aufrufen der MVAD-APIs wird der Fehler „Ressource nicht gefunden.“ zurückgegeben. | Während der Vorschauphase ist die multivariate Anomalieerkennung nur in bestimmten Regionen verfügbar. Erstellen Sie ein Lesezeichen für [Neuerungen in der Anomalieerkennung](../whats-new.md), um im Hinblick auf die regionalen Rollouts der multivariaten Anomalieerkennung auf dem neuesten Stand zu bleiben. Sie können auch ein GitHub-Problem melden oder sich unter AnomalyDetector@microsoft.com an uns wenden, um eine Anfrage für bestimmte Regionen zu stellen. |

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="how-does-mvad-sliding-window-work"></a>Wie funktioniert das gleitende MVAD-Fenster?

Die Funktionsweise des gleitenden MVAD-Fensters lässt sich am besten anhand von zwei Beispielen erklären: Angenommen, Sie haben `slidingWindow` auf „1.440“ festgelegt, und Ihre Eingabedaten haben eine Granularität von einer Minute.

* **Streamingszenario:** Sie möchten vorhersagen, ob der EINE Datenpunkt bei „2021-01-02T00:00:00Z“ anomal ist. `startTime` und `endTime` haben den gleichen Wert (2021-01-02T00:00:00Z). Ihre Rückschlussdatenquelle muss jedoch mindestens 1.440 + 1 Zeitstempel enthalten. Denn die multivariate Anomalieerkennung zieht die Daten vor dem Zieldatenpunkt (2021-01-02T00:00:00Z) heran, um zu entscheiden, ob das Ziel eine Anomalie ist. Die Länge der erforderlichen vorangegangenen Daten ist `slidingWindow` (in diesem Fall also 1.440). 1\.440 = 60 · 24, daher müssen Ihre Eingabedaten spätestens bei „2021-01-01T00:00:00Z“ beginnen.

* **Batchszenario:** Sie möchten mehrere Zieldatenpunkte vorhersagen. `endTime` ist bei Ihnen größer als `startTime`. Rückschlüsse in solchen Szenarien werden unter Verwendung eines dynamischen Fensters generiert. Von der multivariaten Anomalieerkennung werden beispielsweise Daten von `2021-01-01T00:00:00Z` bis `2021-01-01T23:59:00Z` (einschließlich) verwendet, um zu bestimmen, ob Daten bei `2021-01-02T00:00:00Z` Anomalien sind. Anschließend werden Daten von `2021-01-01T00:01:00Z` bis `2021-01-02T00:00:00Z` (einschließlich) verwendet, um zu bestimmen, ob Daten bei `2021-01-02T00:01:00Z` Anomalien sind. Diese Vorgehensweise wird bis zum letzten durch `endTime` angegebenen Zeitstempel (oder bis zum allerletzten Zeitstempel) fortgesetzt. Dabei werden 1.440 Datenpunkte verglichen. Ihre Rückschlussdatenquelle muss daher Daten enthalten, die bei `startTime` - `slidingWindow` beginnen und idealerweise insgesamt eine Größe von `slidingWindow` + (`endTime` - `startTime`) aufweisen.

### <a name="why-only-accepting-zip-files-for-training-and-inference"></a>Warum werden nur ZIP-Dateien für Training und Rückschluss akzeptiert?

Wir verwenden ZIP-Dateien, da in Batchszenarien davon auszugehen ist, dass die Trainings- und Rückschlussdaten sehr groß sind und nicht im HTTP-Anforderungstext platziert werden können. Dadurch können Benutzer Batchrückschlüsse auf der Grundlage historischer Daten generieren – entweder für die Modellüberprüfung oder für die Datenanalyse.

Für Streamingrückschlüsse und Daten mit hoher Frequenz kann dies allerdings etwas unpraktisch sein. Wir planen, eine neue API hinzuzufügen, die speziell für Streamingrückschlüsse entwickelt wurde, damit Benutzer Daten im Anforderungstext übergeben können.

### <a name="whats-the-difference-between-severity-and-score"></a>Was ist der Unterschied zwischen `severity` und `score`?

Normalerweise empfiehlt es sich, mithilfe von `severity` Anomalien herauszufiltern, die für Ihr Unternehmen nicht so wichtig sind. Je nach Szenario und Datenmuster haben diese weniger wichtigen Anomalien häufig relativ niedrige Werte für `severity` oder eigenständige (unstetige) hohe Werte für `severity` – etwa zufällige Spitzen.

Wenn Sie komplexere Regeln benötigen als die Schwellenwerte für `severity` oder für die Dauer kontinuierlich hoher `severity`-Werte, können Sie `score` verwenden, um leistungsfähigere Filter zu erstellen. Unter Umständen ist es hilfreich zu verstehen, wie `score` von der multivariaten Anomalieerkennung zur Bestimmung von Anomalien verwendet wird:

Ob ein Datenpunkt anomal ist, wird sowohl aus globaler als auch aus lokaler Perspektive betrachtet. Ist `score` bei einem Zeitstempel höher als ein bestimmter Schwellenwert, wird der Zeitstempel als Anomalie gekennzeichnet. Ist `score` zwar niedriger als der Schwellenwert, in einem Segment aber relativ betrachtet höher, wird es ebenfalls als Anomalie gekennzeichnet.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Verwenden der Clientbibliothek für die multivariate Anomalieerkennung](../quickstarts/client-libraries-multivariate.md)
* [Erfahren Sie mehr über die zugrunde liegenden Algorithmen, auf denen die multivariate Anomalieerkennung aufbaut](https://arxiv.org/abs/2009.02040)
