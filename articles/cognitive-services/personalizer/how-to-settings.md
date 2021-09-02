---
title: Konfigurieren der Personalisierung
description: Die Dienstkonfiguration umfasst die Art, wie der Dienst Belohnungen behandelt, wie oft der Dienst Untersuchungen durchführt, wie oft das Modell neu trainiert wird und wie viele Daten gespeichert werden.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: c868004bcf8ec23eb6607d791f4e9762ad20eca0
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829937"
---
# <a name="configure-personalizer-learning-loop"></a>Konfigurieren der Lernschleife der Personalisierung

Die Dienstkonfiguration umfasst die Art, wie der Dienst Belohnungen behandelt, wie oft der Dienst Untersuchungen durchführt, wie oft das Modell neu trainiert wird und wie viele Daten gespeichert werden.

Konfigurieren Sie die Lernschleife auf der Seite **Konfiguration** im Azure-Portal für diese Personalisierungsressource.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>Planen von Konfigurationsänderungen

Da einige Konfigurationsänderungen [Ihr Modell zurücksetzen](#settings-that-include-resetting-the-model), sollten Sie Ihre Konfigurationsänderungen planen.

Wenn Sie planen, den [Ausbildungsmodus](concept-apprentice-mode.md) zu verwenden, stellen Sie sicher, dass Sie Ihre Personalisierungskonfiguration überprüfen, bevor Sie in den Ausbildungsmodus wechseln.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Einstellungen, die das Zurücksetzen des Modells einbeziehen

Die folgenden Aktionen lösen ein erneutes Training des Modells unter Verwendung der bis zu den letzten zwei Tagen verfügbaren Daten aus.

* Relevanz
* Durchsuchen

Verwenden Sie zum [Löschen](how-to-manage-model.md) aller Ihrer Daten die Seite **Modell- und Lerneinstellungen**.

## <a name="configure-rewards-for-the-feedback-loop"></a>Konfigurieren von Relevanzen für die Feedbackschleife

Konfigurieren Sie den Dienst für die Verwendung von Relevanzen durch die Lernschleife. Änderungen an den folgenden Werten setzen das aktuelle Personalisierungsmodell zurück und trainieren es mit den Daten der letzten 2 Tage neu.

> [!div class="mx-imgBorder"]
> ![Konfigurieren der Relevanzwerte für die Feedbackschleife](media/settings/configure-model-reward-settings.png)

|Wert|Zweck|
|--|--|
|Belohnungswartezeit|Legt die Zeitspanne, während der die Personalisierung Werte für einen Rangfolgeaufruf sammelt werden, ab dem Zeitpunkt fest, zu dem der Rangfolgeaufruf erfolgt. Dieser Wert wird festgelegt durch die Frage: „How long should Personalizer wait for rewards calls?“ (Wie lange sollte die Personalisierung auf Belohnungsaufrufe warten?) Jede Belohnung, die nach diesem Zeitfenster eingeht, wird protokolliert, aber nicht für das Lernen verwendet.|
|Standardbelohnung|Wenn während des Zeitfensters für das Warten auf die Belohnung kein Belohnungsaufruf bei der Personalisierung eingeht, weist die Personalisierung die Standardbelohnung zu. Standardmäßig und in den meisten Szenarien ist die Standardrelevanz 0 (null).|
|Belohnungsaggregation|Wenn mehrere Belohnungen für den gleichen Rangfolge-API-Aufruf empfangen werden, wird diese Aggregationsmethode verwendet: **sum** oder **earliest**. „Earliest“ wählt das am frühesten empfangene Ergebnis aus, und der Rest wird verworfen. Dies ist nützlich, wenn Sie eine eindeutige Relevanz zwischen möglicherweise doppelten Aufrufen wünschen. |

Wählen Sie nach dem Ändern dieser Werte unbedingt **Speichern** aus.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Konfigurieren der Erkundung zum Anpassen der Lernschleife

Die Personalisierung kann neue Muster ermitteln und im Laufe der Zeit den Verhaltensänderungen der Benutzer durch Durchsuchen von Alternativen anpassen, anstatt die Vorhersage des trainierten Modells zu verwenden. Der Wert **Erkundung** bestimmt, wie viel Prozent der Rangfolgeaufrufe mit einem Durchsuchen beantwortet werden.

Änderungen an diesem Wert setzen das aktuelle Personalisierungsmodell zurück und trainieren es mit den Daten der letzten 2 Tage neu.

![Der Wert „Erkundung“ bestimmt, wie viel Prozent der Rangfolgeaufrufe mit einem Durchsuchen beantwortet werden.](media/settings/configure-exploration-setting.png)

Wählen Sie nach dem Ändern dieses Werts unbedingt **Speichern** aus.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Konfigurieren der Häufigkeit der Modellaktualisierung für das Modelltraining

Die **Häufigkeit der Modellaktualisierung** legt fest, wie oft das vom Rangfolgeaufruf verwendete Modell aktualisiert wird.

|Einstellung „Häufigkeit“|Zweck|
|--|--|
|1 Minute|Eine Modellaktualisierungshäufigkeit von einer Minute empfiehlt sich beim **Debuggen** von Anwendungscode mit Personalisierung, bei Demos sowie beim interaktiven Testen von Machine Learning-Aspekten.|
|15 Minuten|Eine hohe Modellaktualisierungshäufigkeit ist hilfreich, wenn **Veränderungen beim Benutzerverhalten genau nachverfolgt** werden sollen. Beispiele wären etwa Websites mit Livenachrichten, viralen Inhalten oder Livegeboten für Produkte. In solchen Szenarien kann beispielsweise eine Frequenz von 15 Minuten verwendet werden. |
|1 Stunde|In den meisten Anwendungsfällen ist eine niedrigere Aktualisierungshäufigkeit sinnvoll.|

![„Model update frequency“ legt fest, wie oft ein neues Personalisierungsmodell neu trainiert wird.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Wählen Sie nach dem Ändern dieses Werts unbedingt **Speichern** aus.

## <a name="data-retention"></a>Beibehaltung von Daten

**Data retention period** (Zeitraum für die Datenaufbewahrung) legt fest, wie viele Tage die Personalisierung Datenprotokolle aufbewahrt. Frühere Datenprotokolle sind zur Durchführung von [Offlineauswertungen](concepts-offline-evaluation.md) erforderlich, die verwendet werden, um die Effektivität der Personalisierung zu messen und die Lernrichtlinie zu optimieren.

Wählen Sie nach dem Ändern dieses Werts unbedingt **Speichern** aus.



## <a name="next-steps"></a>Nächste Schritte

[Informationen zum Verwalten Ihres Modells](how-to-manage-model.md)
