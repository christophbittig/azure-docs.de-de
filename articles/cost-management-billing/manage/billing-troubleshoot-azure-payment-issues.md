---
title: Behandeln von Azure-Zahlungsproblemen
description: Hier wird das Beheben eines Problems beim Aktualisieren des Zahlungsinformationskontos im Azure-Portal erläutert.
author: bandersmsft
ms.reviewer: lishepar
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 05/13/2021
ms.author: banders
ms.openlocfilehash: a8e2ddd11724b39f6439cc59de012c0a60ab0ff6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438738"
---
# <a name="troubleshoot-azure-payment-issues"></a>Behandeln von Azure-Zahlungsproblemen

Unter Umständen tritt ein Problem oder Fehler auf, wenn Sie versuchen, die Zahlungsinformationen zum Konto im Microsoft Azure-Portal zu aktualisieren.

Wählen Sie zum Beheben des Problems unten den Artikel aus, in dem der aufgetretene Fehler am ehesten beschrieben wird.

## <a name="my-credit-card-was-declined-when-i-tried-to-sign-up-for-azure"></a>Meine Kreditkarte wurde abgelehnt, als ich mich für Azure registrieren wollte.

Informationen zur Problembehandlung bei einer abgelehnten Karte finden Sie unter [Beheben von Problemen mit einer abgelehnten Karte bei der Azure-Registrierung](troubleshoot-declined-card.md).

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Anzeigen von Abonnements in meinem Konto zum Aktualisieren der Zahlungsmethode nicht möglich

Möglicherweise verwenden Sie eine E-Mail-ID, die sich von der für die Abonnements verwendeten E-Mail-ID unterscheidet.

Informationen zur Problembehebung finden Sie unter [Anmeldefehler „Keine Abonnements gefunden“ für das Azure-Portal](no-subscriptions-found.md).

## <a name="unable-to-use-a-virtual-or-prepaid-credit-or-debit-card-as-a-payment-method"></a>Virtuelle Kredit- oder Debitkarten oder Prepaid-Kreditkarten oder -Debitkarten können nicht als Zahlungsmethode verwendet werden.

*   Virtuelle Kreditkarten oder Prepaid-Kreditkarten werden als Zahlungsmittel für Azure-Abonnements nicht akzeptiert.
*   Debitkarten werden als Zahlungsmittel für Azure-Abonnements nicht akzeptiert.

Weitere Informationen finden Sie unter [Behandeln von Problemen mit einer abgelehnten Karte bei der Azure-Registrierung](troubleshoot-declined-card.md).

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Entfernen einer Kreditkarte aus einer gespeicherten Zahlungsmethode nicht möglich

Eine Kreditkarte kann standardmäßig nicht aus dem aktiven Abonnement entfernt werden.

Wenn eine vorhandene Karte gelöscht werden muss, muss dem Abonnement entweder eine neue Karte hinzugefügt werden, um das alte Zahlungsinstrument erfolgreich löschen zu können, oder Sie müssen das Abonnement kündigen. Dadurch wird das Abonnement dauerhaft gelöscht und die Karte entfernt.

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Löschen einer alten Zahlungsmethode nach dem Hinzufügen einer neuen Zahlungsmethode nicht möglich

Das neue Zahlungsmittel ist unter Umständen nicht mit dem Abonnement verknüpft. Informationen dazu, wie Sie dem Abonnement das Zahlungsinstrument zuordnen, finden Sie unter [Hinzufügen, Aktualisieren oder Entfernen einer Kreditkarte für Azure](change-credit-card.md).

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Zahlungsmethode kann aufgrund der Fehlermeldung *Löschen der Zahlungsmethode nicht möglich* nicht gelöscht werden

Dies tritt wegen eines ausstehenden Betrags auf. Begleichen Sie alle ausstehenden Beträge, bevor Sie die Zahlungsmethode löschen.

## <a name="unable-to-make-payment-for-a-subscription"></a>Die Zahlung für ein Abonnement kann nicht ausgeführt werden

Wenn Sie die Fehlermeldung *Die Zahlung ist überfällig. Es liegt ein Problem mit Ihrer Zahlungsmethode vor* oder *Die Informationen konnten nicht gespeichert werden. Schließen Sie den Browser, und versuchen Sie es erneut* erhalten, kann die Ursache eine ausstehende Zahlung für die Karte sein, da die Karte von Ihrem Finanzinstitut abgelehnt wurde.

Stellen Sie sicher, dass das Saldo der Kreditkarte ausreicht, um eine Zahlung zu tätigen. Wenn dies nicht der Fall ist, verwenden Sie eine andere Karte, um die Zahlung vorzunehmen, oder wenden Sie sich an Ihr Finanzinstitut, um das Problem zu beheben.

Überprüfen Sie mit Ihrer Bank die folgenden Probleme:

- Internationale Transaktionen sind nicht aktiviert.
- Die Karte verfügt über ein Kreditlimit, und der Saldo muss beglichen werden.
- Eine wiederholte Zahlungen für die Karte ist aktiviert.

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Die Zahlungsmethode kann aufgrund von Browserproblemen nicht geändert werden (der Browser antwortet nicht, lädt nicht, usw.)

Melden Sie sich von allen aktiven Azure-Sitzungen ab, und führen Sie dann die Schritte im Artikel [InPrivate-Browsen in Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) aus, um eine InPrivate-Sitzung innerhalb von Microsoft Edge oder Internet Explorer zu starten.

Führen Sie in der privaten Sitzung die Schritte unter [Ändern einer Kreditkarte](change-credit-card.md) aus, um die Kreditkarteninformationen zu aktualisieren oder zu ändern.

Sie können Sie auch Folgendes durchführen:

- Aktualisieren Sie Ihren Browser.
- Verwenden Sie einen anderen Browser.
- Löschen Sie zwischengespeicherte Cookies.

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Mein Abonnement ist nach der Aktualisierung der Zahlungsmethode immer noch deaktiviert.

Dieses Problem tritt wegen eines ausstehenden Betrags auf. Begleichen Sie alle ausstehenden Beträge, bevor Sie die Zahlungsmethode löschen.

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Änderung der Zahlungsmethode aufgrund einer Seite mit XML-Fehlerantwort nicht möglich

Sie erhalten diese Meldung, wenn Sie das [Azure-Portal](https://portal.azure.com/) verwenden, um eine neue Kreditkarte hinzuzufügen.

Zum Hinzuzufügen von Kartendetails müssen Sie sich mit der E-Mail-Adresse des Kontoadministrators beim „Azure-Kontoportal“ anmelden.

## <a name="additional-help-resources"></a>Zusätzliche Hilferessourcen

Weitere Artikel für die Problembehandlung bei Azure-Abrechnung und -Abonnements

- [Karte abgelehnt](troubleshoot-declined-card.md)
- [Probleme bei der Abonnementanmeldung](troubleshoot-sign-in-issue.md)
- [Keine Abonnements gefunden](no-subscriptions-found.md)
- [Enterprise-Kostenansicht deaktiviert](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Setzen Sie sich mit uns in Verbindung, um Hilfe zu erhalten.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

- [Dokumentation zur Azure-Abrechnung](../index.yml)