---
title: Überwachen von Nutzung und Ausgaben mit Kostenwarnungen in Azure Cost Management
description: In diesem Artikel wird beschrieben, wie Sie Nutzung und Ausgaben mithilfe von Kostenwarnungen in Cost Management überwachen können.
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 547dac5984ecc88c452913f406196ade27e74fcd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705954"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Verwenden von Kostenwarnungen zum Überwachen von Verbrauch und Ausgaben

Dieser Artikel enthält grundlegende Informationen zur Verwendung von Cost Management-Warnungen zum Überwachen der Azure-Nutzung und der entsprechenden Ausgaben. Kostenwarnungen werden automatisch generiert, wenn Azure-Ressourcen genutzt werden. In den Warnungen werden alle aktiven Kostenverwaltungs- und Abrechnungswarnungen zusammen angezeigt. Wenn Ihr Verbrauch einen bestimmten Schwellenwert erreicht, werden in Cost Management Warnungen generiert. Es gibt drei Typen von Kostenwarnungen: Budgetwarnungen, Guthabenwarnungen und Warnungen zum Ausgabenkontingent für Abteilungen.

## <a name="budget-alerts"></a>Budgetwarnungen

Mit Budgetwarnungen werden Sie benachrichtigt, wenn Ausgaben basierend auf Verbrauch oder Kosten den in der [Warnungsbedingung für das Budget](tutorial-acm-create-budgets.md) definierten Betrag erreichen oder überschreiten. Cost Management-Budgets werden über das Azure-Portal oder die [Azure Consumption](/rest/api/consumption)-API erstellt.

Im Azure-Portal werden Budgets nach Kosten definiert. Bei Verwendung der Azure Consumption-API werden Budgets nach Kosten oder nach Verbrauch definiert. Budgetwarnungen unterstützen kostenbasierte sowie nutzungsbasierte Budgets. Budgetwarnungen werden immer automatisch generiert, wenn die Warnungsbedingungen für das Budget erfüllt sind. Sie können alle Kostenwarnungen im Azure-Portal anzeigen. Immer wenn eine Warnung generiert wird, wird sie in den Kostenwarnungen angezeigt. Außerdem wird eine Warnung per E-Mail an alle Personen in der Liste der Warnungsempfänger für das Budget gesendet.

Sie können die Budget-API verwenden, um E-Mail-Warnungen in einer anderen Sprache zu senden. Weitere Informationen finden Sie unter [Unterstützte Gebietsschemas für Budgetwarnungs-E-Mails](manage-automation.md#supported-locales-for-budget-alert-emails).

## <a name="credit-alerts"></a>Guthabenwarnungen

Mit Guthabenwarnungen werden Sie benachrichtigt, wenn die Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) verbraucht ist. Azure-Vorauszahlung ist für Organisationen mit Enterprise Agreements gedacht. Guthabenwarnungen werden bei 90 Prozent und bei 100 Prozent Ihres Azure-Vorauszahlungsguthabens generiert. Wenn eine Warnung generiert wird, wird das bei den Kostenwarnungen und in der E-Mail widergespiegelt, die an die Kontobesitzer gesendet wird.

## <a name="department-spending-quota-alerts"></a>Warnungen bei Ausgabenkontingenten für Abteilungen

Dank der Warnungen bei Ausgabenkontingenten für Abteilungen werden Sie benachrichtig, sobald die Ausgaben einer Abteilung einen bestimmten Schwellenwert eines Kontingents erreichen. Ausgabenkontingente werden im EA-Portal konfiguriert. Wenn ein Schwellenwert erreicht wird, wird eine E-Mail an die Abteilungseigentümer geschickt und es erfolgt eine Anzeige in den Kostenwarnungen. Beispielsweise bei 50 % oder 75 % des Kontingents.

## <a name="supported-alert-features-by-offer-categories"></a>Unterstützte Warnungsfeatures nach Angebotskategorien

Welche Warnungstypen unterstützt werden, hängt vom Typ Ihres Azure-Kontos ab (Microsoft-Angebot). Die folgende Tabelle zeigt die Warnungsfunktionen, die von verschiedenen Microsoft-Angeboten unterstützt werden. Die vollständige Liste der Microsoft-Angebote finden Sie unter [Grundlegendes zu Cost Management-Daten](understand-cost-mgt-data.md).

| Warnungstyp | Enterprise Agreement | Microsoft-Kundenvereinbarung | Direkt via Internet/Nutzungsbasierte Zahlung |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Quelle | ✔ |✘ | ✘ |
| Ausgabenkontingent für Abteilung | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Anzeigen von Kostenwarnungen

Um sich die Kostenwarnungen anzeigen zu lassen, öffnen Sie den gewünschten Bereich im Azure-Portal, und wählen Sie im Menü **Budgets** aus. Verwenden Sie das ovale Steuerelement **Bereich**, um zu einem anderen Bereich zu wechseln. Klicken Sie im Menü auf **Kostenwarnungen**. Weitere Informationen zu Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).

![Beispielabbildung für in Cost Management angezeigte Warnungen](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Die Gesamtzahl aktiver und geschlossener Warnungen wird auf der Seite „Kostenwarnungen“ angezeigt.

Bei allen Warnungen ist auch der Warnungstyp angegeben. In einer Budgetwarnung wird der Grund für die Generierung und der Name des entsprechenden Budgets angezeigt. Jede Warnung enthält das Datum, an dem sie erstellt wurde, den jeweiligen Status und den Bereich (Abonnement oder Verwaltungsgruppe), auf den sich die Warnung bezieht.

Mögliche Werte für den Status sind **Aktiv** und **Geschlossen**. Der Status „Aktiv“ gibt an, dass die Warnung weiterhin relevant ist. Der Status „Dismissed“ (Verworfen) gibt an, dass eine Person die Warnung als nicht mehr relevant gekennzeichnet hat.

Sie können eine Warnung aus der Liste auswählen, um sich ihre Details anzeigen zu lassen. Hier finden Sie weitere Informationen zur Warnung. Budgetwarnungen enthalten einen Link zum Budget. Wenn für eine Budgetwarnung eine Empfehlung vorhanden ist, wird auch ein Link zur Empfehlung angezeigt. Budgetwarnungen, Guthabenwarnungen und Warnungen zum Ausgabenkontingent für Abteilungen enthalten einen Link zu der Kostenanalyse, in der Sie die Kosten für den Bereich der Warnung überprüfen können. Im folgenden Beispiel werden die Ausgaben für eine Abteilung mit Warnungsdetails angezeigt.

![Beispielabbildung für die Ausgaben einer Abteilung mit Warnungsdetails](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Wenn Sie die Details einer geschlossenen Warnung anzeigen, können Sie sie erneut aktivieren, wenn eine manuelle Aktion erforderlich ist. Die folgende Abbildung zeigt ein Beispiel.

![Beispielabbildung für die Optionen zum Schließen und erneuten Aktivieren](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Weitere Informationen

- Wenn Sie noch kein Budget erstellt oder keine Warnungsbedingungen für ein Budget festgelegt haben, finden Sie entsprechende Anweisungen im Tutorial [Erstellen und Verwalten von Budgets](tutorial-acm-create-budgets.md).