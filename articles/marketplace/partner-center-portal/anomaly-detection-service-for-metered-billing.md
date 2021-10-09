---
title: 'Anomalieerkennungsdienst für getaktete Abrechnung: Microsoft Azure Marketplace'
description: Erfahren Sie mehr über die Erkennung von Anomalien, den Zeitpunkt des Versands von Benachrichtigungen, den Umgang damit und über Supportoptionen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/27/2021
ms.openlocfilehash: ff4600cd1b107fb041123191f80f9da4f50af5ac
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081173"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Anomalieerkennungsdienst für getaktete Abrechnung

Mit dem [Messdienst in Marketplace](../marketplace-metering-service-apis-faq.yml) können Sie Angebote im Rahmen des kommerziellen Marketplace-Programms erstellen, die nicht gemäß Standardeinheiten abgerechnet werden. Bei der getakteten Abrechnung werden Nutzungsereignisse Ihrer Kunden an Microsoft gesendet, woraufhin wir die Abrechnung auf Grundlage dieser Nutzung vornehmen.

Falsch gemeldete Nutzungsdaten können verschiedene Ursachen haben, wie z. B. Programmfehler, falsche Konfigurationen in Ihrer Nutzungserfassung oder Betrug. Falsch gemeldete Nutzungsdaten können zu falschen Kundenabrechnungen und Abrechnungsstreitigkeiten führen.

Um das Risiko abzufedern, arbeitet unser Dienst zur Erkennung von Anomalien mit Algorithmen für maschinelles Lernen, um das normale Verhalten bei getakteter Abrechnung zu bestimmen, die Nutzung bei getakteter Abrechnung zu analysieren und Anomalien mit minimalem Benutzereingriff aufzuzeigen.

Sie werden benachrichtigt, wenn bei Ihrer Nutzung mit getakteter Abrechnung Anomalien festgestellt werden. Dies bietet Ihnen die Gelegenheit, eine Anomalie zu untersuchen und uns zu benachrichtigen, wenn sich bestätigt, dass es sich bei einer Anomalie um ein echtes Problem handelt. An diesem Punkt können Maßnahmen ergriffen werden, um das Problem bei der Kundenabrechnung proaktiv anzugehen.

Zusätzlich zu plötzlichen Spitzen, Schwankungen und Trendänderungen bei Nutzung mit getakteter Abrechnung berücksichtigt unser Modell auch saisonale Effekte. Da die getaktete Abrechnung mittels Überschreitungsdaten kommuniziert wird, ist unser Modell auch in der Lage, lange Zeiträume fehlender Daten problemlos zu bewältigen.

Es folgen Beispiele für Ergebnisse bei Erkennung von Anomalien. Der erwartete Bereich wird als gelbes Band gezeigt. Akzeptable Nutzung mit getakteter Abrechnung wird als grüne Sterne innerhalb des Bands gezeigt. Nutzung mit getakteter Abrechnung außerhalb des Bands wird als roter Punkt gezeigt.  

Außerhalb eines vorhersagbaren Trends erkannte Anomalien:

![Veranschaulicht Anomalien, die außerhalb eines vorhersehbaren Trends erkannt wurden.](media/anomaly-1.png)

Außerhalb eines wiederkehrenden zyklischen Trends erkannte Anomalien:

![Veranschaulicht Anomalien, die außerhalb eines wiederkehrenden zyklischen Trends erkannt wurden.](media/anomaly-2.png)

In einem Aufwärtstrend erkannte Anomalien:

![Veranschaulicht Anomalien, die in einem Aufwärtstrend erkannt wurden.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Funktionsweise des Dienst zur Erkennung von Anomalien

Die Erkennung von Anomalien ist für die gesamte Nutzung mit getakteter Abrechnung automatisch aktiviert. Wenn Sie die Nutzungsereignisse an Microsoft übermitteln, erstellt der Dienst zur Erkennung von Anomalien auf Grundlage bisheriger Nutzungsdaten ein Modell erwarteter Werte. Dieses Modell wird wöchentlich ausgeführt.

Die Anomalieerkennung funktioniert messtakt- und kundenbezogen. Das bedeutet, dass für jede Verbrauchseinheit mit jedem Kunden ein Modell trainiert wird, das auf dem bisherigen Nutzungsmuster dieser Verbrauchseinheit dieses Kunden basiert.

Das Modell beruht auf der Generierung retrospektiver Konfidenzintervalle. Die Zeitreihenvorhersage ist ein verallgemeinertes additives Modell, das aus einem Trendvorhersage- und einem Saisonalitätsteil besteht. Da das Modell als Regressionsaufgabe formuliert ist, kann es mit langen Zeiträumen fehlender Daten problemlos umgehen. Wenn eine Beobachtung außerhalb der vorhergesagten Konfidenzintervalle liegt, bedeutet dies, dass die Beobachtung nicht auf Grundlage von Verlaufsmustern bei getakteter Abrechnung erklärt werden und daher eine Anomalie darstellen kann.

## <a name="anomaly-detection-notification"></a>Benachrichtigung bei einer Anomalieerkennung

Sie können in Partner Center Anomalien auswerten, verwalten und bestätigen. Weitere Informationen hierzu finden Sie unter [Verwalten von Anomalien bei der getakteten Abrechnung in Partner Center](../anomaly-detection.md).

Um zu verhindern, dass Ihren Kunden bei der getakteten Abrechnung zu hohe Beträge in Rechnung gestellt werden, sollten Sie überprüfen, ob es sich bei den erkannten Anomalien um echte Probleme handelt. Wenn dies der Fall ist, können Sie die falsch gemeldete Nutzung in Partner Center bestätigen.

Es wird empfohlen, zu überprüfen, ob es sich bei erkannten Anomalien um normale Nutzung handelt. Hierdurch können wir für Sie zuverlässigere Anomaliedaten bereitstellen. Wenn eine Anomalie ein potenziell hohes finanzielles Risiko darstellt, können wir Sie kontaktieren, um uns die Nutzung bestätigen zu lassen.

## <a name="when-and-how-to-get-support"></a>Wann und wie erhalte ich Support?

Falls Sie falsche Nutzungsdaten für die getaktete Abrechnung an Microsoft gesendet haben und dies dazu führt, dass dem Kunden zu wenig berechnet wurde bzw. wird, wird dem Kunden die zu wenig gemeldete Nutzung nicht durch uns nachberechnet, und Sie werden nicht für diese Nutzung bezahlt. Der durch die fehlerhafte Meldung entstandene Umsatzverlust muss von Ihnen getragen werden.

Wenn einer der folgenden Fälle zutrifft, können Sie den Nutzungsbetrag in Partner Center anpassen. Dies führt zu einer Erstattung oder Abrechnungskorrektur für Ihre Kunden:

- Sie haben festgestellt, dass es sich bei einer der von uns gefundenen Anomalien um ein echtes Problem handelt und dass die falsch gemeldete Nutzung dazu führen würde, dass dem Kunden zu viel berechnet wird.
- Sie stellen fest, dass Sie falsche Nutzungsangaben an uns übermittelt haben, die dazu führen würden, dass dem Kunden zu viel berechnet wird.

So übermitteln Sie ein Supportticket bezüglich Anomalien bei der getakteten Abrechnung:

#### <a name="workspaces-view"></a>[Ansicht der Arbeitsbereiche](#tab/workspaces-view)

1. Melden Sie sich mit Ihrem Geschäftskonto bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie auf der Startseite die Kachel **„Hilfe + Support“** .

    [ ![Veranschaulicht die Kachel „Hilfe und Support“ auf der Startseite des Partner Centers.](../media/workspaces/partner-center-help-support-tile.png) ](../media/workspaces/partner-center-help-support-tile.png#lightbox)

1. Wählen Sie unter **Meine Supportanfragen** die Option **+ Neue Anfrage** aus.
1. Geben Sie im Feld **„Problemzusammenfassung“** den Begriff **„Abrechnungszähler“** ein.
1. Wählen Sie im Feld **Problemtyp** eine der folgenden Optionen aus:
    - **Commercial Marketplace > Getaktete Abrechnung > Falsche Nutzung für Azure-Anwendungsangebot gesendet**
    - **Commercial Marketplace > Getaktete Abrechnung > Falsche Nutzung für SaaS-Angebot gesendet**
1. Wählen Sie unter **Nächster Schritt** die Option **Lösungen überprüfen** aus.
1. Überprüfen Sie die empfohlenen Dokumente, sofern vorhanden, oder wählen Sie **Problemdetails angeben** aus, um ein Supportticket zu senden.

Weitere Supportoptionen für Herausgeber finden Sie unter [Support für das Programm „Kommerzieller Marketplace“ im Partner Center](../support.md).

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich mit Ihrem Geschäftskonto bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie im Menü oben rechts auf der Seite das Symbol **Support** aus. Der Bereich **Hilfe und Support** wird rechts auf der Seite angezeigt.
1. Wenn Sie Hilfe zum kommerziellen Marketplace benötigen, wählen Sie **Kommerzieller Marketplace** aus.
   ![Veranschaulicht den Supportbereich](../media/support/commercial-marketplace-support-pane.png)
1. Geben Sie im Feld **Problemzusammenfassung** den Text **kommerzieller Marketplace > getaktete Abrechnung** ein.
1. Wählen Sie im Feld **Problemtyp** eine der folgenden Optionen aus:
    - **Commercial Marketplace > Getaktete Abrechnung > Falsche Nutzung für Azure-Anwendungsangebot gesendet**
    - **Commercial Marketplace > Getaktete Abrechnung > Falsche Nutzung für SaaS-Angebot gesendet**
1. Wählen Sie unter **Nächster Schritt** die Option **Lösungen überprüfen** aus.
1. Überprüfen Sie die empfohlenen Dokumente, sofern vorhanden, oder wählen Sie **Problemdetails angeben** aus, um ein Supportticket zu senden.

Weitere Supportoptionen für Herausgeber finden Sie unter [Support für das Programm „Kommerzieller Marketplace“ im Partner Center](../support.md).

---

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zur [Messdienst-API in Marketplace](../marketplace-metering-service-apis.md).
- [Verwalten von Anomalien bei der getakteten Abrechnung in Partner Center](../anomaly-detection.md)
