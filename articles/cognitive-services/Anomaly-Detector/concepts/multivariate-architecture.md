---
title: Predictive Maintenance-Architektur für die Verwendung der multivariaten Anomalieerkennungs-API
titleSuffix: Azure Cognitive Services
description: Referenzarchitektur für die Verwendung der multivariaten Anomalieerkennungs-APIs, um die Anomalieerkennung zum Zweck vorausschauender Wartung auf Ihre Zeitreihendaten anzuwenden.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: Anomalieerkennung, maschinelles Lernen, Algorithmen
ms.openlocfilehash: 9de0a774ab2464d3b59ee8875453255e92ea5c45
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297242"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Predictive Maintenance-Lösung mit Erkennung von multivariaten Anomalien

Viele verschiedene Branchen benötigen Predictive Maintenance-Lösungen, um Risiken zu verringern und umsetzbare Erkenntnisse durch die Verarbeitung der Daten ihrer Ausrüstung zu gewinnen. Predictive Maintenance wertet den Zustand von Geräten mithilfe von Onlineüberwachung aus. Das Ziel besteht darin, Wartung durchzuführen, bevor die Ausrüstung nachlässt oder ausfällt.

Die Überwachung des Integritätsstatus von Geräten kann schwierig sein, da jede Komponente innerhalb der Ausrüstung Dutzende von Signalen generieren kann, z. B. Vibration, Ausrichtung und Drehung.  Dies kann sogar noch komplexer sein, wenn zwischen diesen Signalen ein impliziter Zusammenhang besteht, sodass sie zusammen überwacht und analysiert werden müssen. Das Definieren verschiedener Regeln für diese Signale und ihre manuelle Korrelation miteinander kann aufwändig sein. Das multivariate Feature der Anomalieerkennung ermöglicht Folgendes:

* Mehrere miteinander in einem Zusammenhang stehende Signale können gemeinsam überwacht werden, und die Beziehungen zwischen ihnen werden im Modell berücksichtigt.
* Bei jeder erfassten Anomalie kann der Beitragsrang verschiedener Signale bei der Erklärung der Anomalie und der Analyse der Grundursache von Vorfällen hilfreich sein.
* Das multivariate Anomalieerkennungsmodell ist für nicht überwachte Ausführung konzipiert. Für verschiedene Gerätetypen können spezifische Modelle trainiert werden.

Hier stellen wir eine Referenzarchitektur für eine Predictive Maintenance-Lösung bereit, die auf multivariater Anomalieerkennung basiert.

## <a name="reference-architecture"></a>Referenzarchitektur

[ ![Architekturdiagramm, das mit der Erfassung von Sensordaten am Edge auf einem Industriegerät beginnt und die Verarbeitungs-/Analysepipeline bis zur Generierung der Endausgabe einer Vorfallswarnung nach der Ausführung der Anomalieerkennung weiterverfolgt.](../media/multivariate-architecture/multivariate-architecture.png) ](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

In der Architektur oben werden aus den Sensordaten eingehende Streamingereignisse in Azure Data Lake gespeichert und dann von einem Datentransformationsmodul verarbeitet, um in ein Zeitreihenformat konvertiert zu werden. In der Zwischenzeit löst das Streamingereignis Echtzeiterkennung mit dem trainierten Modell aus. Im Allgemeinen gibt es ein Modul zum Verwalten des Lebenszyklus des multivariaten Modells, etwa den *Bridgedienst* in dieser Architektur.

**Modelltraining**: Vor der Verwendung der multivariaten Anomalieerkennung zum Erkennen von Anomalien für eine Komponente oder ein Gerät. Wir müssen ein Modell mit bestimmten Signalen (Zeitreihen) trainieren, die von dieser Entität generiert werden. Der *Bridgedienst* ruft Verlaufsdaten ab und übermittelt einen Trainingsauftrag an die Anomalieerkennung. Anschließend bewahrt er die Modell-ID im *Modellmetaspeicher* auf.

**Modellvalidierung**: Die Trainingszeit eines bestimmten Modells kann variieren, je nach dem Volumen der Trainingsdaten. Der *Bridgedienst* kann den Modellstatus und die Diagnoseinformationen in regelmäßigen Abständen abfragen. Es kann erforderlich sein, die Qualität des Modells zu überprüfen, bevor es online gestellt wird. Wenn das Szenario Bezeichnungen enthält, können diese Bezeichnungen zum Prüfen der Modellqualität verwendet werden. Andernfalls können die Diagnoseinformationen verwendet werden, um die Modellqualität zu bewerten, und Sie können außerdem die Erkennung von Verlaufsdaten mit dem trainierten Modell durchführen und das Ergebnis auswerten, um die Gültigkeit des Modells nachzuprüfen.

**Modellrückschluss**: Die Onlineerkennung wird mit dem gültigen Modell ausgeführt, und die Ergebnis-ID kann in der *Rückschlusstabelle* gespeichert werden. Sowohl der Trainingsprozess als auch der Rückschlussprozess erfolgen asynchron. Im Allgemeinen kann eine Erkennungsaufgabe innerhalb von Sekunden abgeschlossen werden. Die für die Erkennung verwendeten Signale sollten die gleichen sein, die für das Training verwendet wurden. Wenn wir beispielsweise Vibration, Ausrichtung und Drehung für das Training verwendet haben, sollten diese drei Signale bei der Erkennung als Eingabe enthalten sein.

**Vorfallswarnung** Die Ergebnisse der Erkennung können mit Ergebnis-IDs abgefragt werden. Jedes Ergebnis enthält den Schweregrad der einzelnen Anomalie und ihren Beitragsrang. Der Beitragsrang kann verwendet werden, um zu verstehen, warum diese Anomalie aufgetreten ist, und welches Signal diesen Vorfall verursacht hat. Es können verschiedene Schwellenwerte für den Schweregrad zum Auslösen von Warnungen und Benachrichtigungen festgelegt werden, die an Techniker im Außendienst gesendet werden, um Wartungsarbeiten durchzuführen.

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstarts](../quickstarts/client-libraries-multivariate.md).
- [Bewährte Methoden:](../concepts/best-practices-multivariate.md)In diesem Artikel werden empfohlene Muster für die Verwendung mit den multivariaten APIs beschrieben.