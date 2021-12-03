---
title: Automatisches Verlängern von Azure-Reservierungen
description: Erfahren Sie, wie Sie Azure-Reservierungen automatisch verlängern, um weiterhin Reservierungsrabatte zu erhalten.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 7f6cd42395b0255a7c0bd68285dd532363b2141f
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179819"
---
# <a name="automatically-renew-reservations"></a>Automatisches Verlängern von Reservierungen

Sie können Reservierungen verlängern, um automatisch einen Ersatz zu erwerben, wenn eine vorhandene Reservierung abläuft. Die automatische Verlängerung stellt eine einfache Möglichkeit dar, weiterhin Reservierungsrabatte zu erhalten. Darüber hinaus ist es nicht mehr erforderlich, das Auslaufen von Reservierungen genau zu überwachen. Mit der automatischen Verlängerung verhindern Sie den Verlust von Einsparungsvorteilen, da Sie keine manuelle Verlängerung vornehmen müssen. Die Einstellung für die Verlängerung ist standardmäßig deaktiviert. Aktivieren oder deaktivieren Sie die Verlängerungseinstellung jederzeit vor Ablauf der bestehenden Reservierung.

Durch das Verlängern einer Reservierung wird eine neue Reservierung erstellt, wenn die vorhandene Reservierung abläuft. Die Laufzeit der vorhandenen Reservierung wird nicht verlängert.

Melden Sie sich jederzeit für die automatische Verlängerung an. Der Verlängerungspreis ist 30 Tage vor dem Ablauf der vorhandenen Reservierung verfügbar. Wenn Sie die Verlängerung mehr als 30 Tage vor dem Ablauf der Reservierung aktivieren, erhalten Sie 30 Tage vor dem Ablauf eine E-Mail mit den Kosten für die Verlängerung. Der Reservierungspreis kann sich zwischen dem Zeitpunkt, zu dem Sie den Verlängerungspreis sperren, und dem Zeitpunkt der Verlängerung ändern. In diesem Fall entsprechen die Kosten der Verlängerung dem geringeren Betrag der beiden Kosten. Sie können Änderungen an der Reservierungsmenge vornehmen. Dabei wird die Verlängerung so aktualisiert, dass der Marktpreis zum Zeitpunkt der Mengenänderung verwendet wird.

Es besteht keine Verpflichtung zur Verlängerung, und Sie können sich jederzeit von der Verlängerung abmelden, bevor die bestehende Reservierung abläuft.

## <a name="set-up-renewal"></a>Einrichten der Verlängerung

Navigieren Sie im Azure-Portal zu **Reservierungen**.

1. Wählen Sie die Reservierung aus.
2. Klicken Sie auf **Verlängerung**.
3. Aktivieren Sie **Bei Ablauf automatisch eine neue Reservierung erwerben**.  
  ![Beispiel der Reservierungsverlängerung](./media/reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Bei ausbleibender Verlängerung

Ihre Dienste werden weiterhin normal ausgeführt. Nach Ablauf der Reservierung werden Ihnen nutzungsbasierte Tarife für Ihre Nutzung in Rechnung gestellt. Falls für die Reservierung keine automatische Verlängerung vor Ablauf festgelegt wurde, können Sie eine abgelaufene Reservierung nicht verlängern. Wenn Sie weiterhin Einsparungen erzielen möchten, können Sie eine neue Reservierung erwerben.

## <a name="required-renewal-permissions"></a>Erforderliche Erneuerungsberechtigungen

Für das Erneuern einer Reservierung gelten folgende Bedingungen:

- Sie müssen ein Besitzer der vorhandenen Reservierung sein.
- Sie müssen ein Besitzer des Abonnements sein, wenn die Reservierung auf ein einzelnes Abonnement oder eine einzelne Ressourcengruppe beschränkt ist.
- Sie müssen ein Besitzer des Abonnements sein, wenn es einen gemeinsam genutzten Bereich oder Verwaltungsgruppenbereich aufweist.

## <a name="default-renewal-settings"></a>Standardeinstellungen für die Verlängerung

Standardmäßig werden bei der Verlängerung alle Eigenschaften von der ablaufenden Reservierung vererbt. Eine erworbene Reservierungsverlängerung weist die gleichen Werte für SKU, Region, Bereich, Abrechnungsabonnement, Laufzeit und Menge auf.

Sie können jedoch die Menge der Reservierungsverlängerung anpassen, um Ihre Einsparungen zu optimieren.

## <a name="when-the-new-reservation-is-purchased"></a>Beim Erwerb der neuen Reservierung

Wenn die vorhandene Reservierung abläuft, wird eine neue Reservierung erworben. Wir versuchen, Verzögerungen zwischen den beiden Reservierungen zu vermeiden. Durch diese Kontinuität wird sichergestellt, dass Ihre Kosten vorhersagbar bleiben und Sie weiterhin Rabatte erhalten.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Ändern der übergeordneten Reservierung nach Festlegen der Verlängerung

Wenn Sie eine der folgenden Änderungen an der ablaufenden Reservierung vornehmen, wird die Reservierungsverlängerung storniert:

- Split
- Merge
- Übertragen der Reservierung von einem Konto auf ein anderes
- Übertragen der Reservierung eines WebDirect-Abonnements auf ein Enterprise Agreement-Abonnement (EA) oder eine beliebige andere Kaufmethode
- Verlängern der Registrierung

Die neue Reservierung erbt bei der Verlängerung den Umfang und die Einstellung für die Flexibilität der Instanzgröße von der ablaufenden Reservierung.

## <a name="new-reservation-permissions"></a>Berechtigungen der neuen Reservierung

Azure kopiert die Berechtigungen von der ablaufenden Reservierung in die neue Reservierung. Zusätzlich hat der Kontoadministrator des Abonnements für den Reservierungserwerb Zugriff auf die neue Reservierung.

## <a name="potential-renewal-problems"></a>Potenzielle Probleme bei der Verlängerung

Azure kann die Verlängerung in folgenden Fällen nicht verarbeiten:

- Die Zahlung kann nicht erfasst werden.
- Bei der Erneuerung tritt ein Systemfehler auf.
- Die ablaufende SKU ist während der Erneuerung nicht aktiv.
- Das EA wird in ein anderes EA erneuert.

Sie erhalten eine E-Mail-Benachrichtigung, wenn eine der vorstehenden Bedingungen eintritt und die Verlängerung deaktiviert wird.

## <a name="renewal-notification"></a>Benachrichtigung zur Verlängerung

E-Mails zur Verlängerungsbenachrichtigung werden 30 Tage vor Ablauf und noch einmal am Ablaufdatum gesendet. Die E-Mail-Adresse des Absenders lautet `azure-noreply@microsoft.com`. Gegebenenfalls sollten Sie die E-Mail-Adresse Ihrer Liste sicherer bzw. zulässiger Absender hinzufügen.

Abhängig von Ihrer Kaufmethode werden E-Mails an verschiedene Personen gesendet:

- EA-Kunden - E-Mails werden an die im EA-Portal festgelegten Benachrichtigungskontakte oder an Unternehmensadministratoren gesendet, die automatisch für den Erhalt von Nutzungsbenachrichtigungen registriert sind.
- Individuelle Abonnementkunden mit nutzungsbasierten Tarifen: E-Mails werden an Benutzer gesendet, die als Kontoadministratoren eingerichtet wurden.
- Cloud Solution Provider-Kunden: E-Mails werden an den Benachrichtigungskontakt des Partners gesendet.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu Azure-Reservierungen finden Sie unter [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md).
