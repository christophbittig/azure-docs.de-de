---
title: Grundlegendes zu Ihrer Azure-Rechnung
description: Erfahren Sie, wie Sie die Nutzung und Abrechnung Ihres Azure-Abonnements anzeigen und verstehen.
author: bandersmsft
ms.reviewer: jureid
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 5864397579c46b1e55e2f8443776ca349abc22e8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586341"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Grundlegendes über Benennungen in Ihrer Microsoft Azure-Rechnung

Die Rechnung enthält eine Zusammenfassung der Gebühren sowie Zahlungsanweisungen. Die Rechnung steht im [Azure-Portal](https://portal.azure.com/) im PDF-Format (Portable Document Format) zum Download bereit oder kann per E-Mail gesendet werden. Weitere Informationen finden Sie unter [Abrufen von Azure-Rechnungen und täglichen Nutzungsdaten](../manage/download-azure-invoice-daily-usage-date.md).

Im Folgenden einige Hinweise, die Sie beachten sollten:

-   Wenn Sie ein kostenloses Testabonnement verwenden, können Sie ausführliche Nutzungsdaten über das Azure-Portal abrufen, es ist jedoch keine Rechnung verfügbar.

-   In der aktuellen Rechnung können bis zu 24 Stunden der Nutzung am Ende des vorherigen Abrechnungszeitraums angezeigt werden.

-   Bei Abrechnungen für internationale Kunden sind die aufgeführten Gebühren nur Schätzwerte, da Banken unterschiedliche Umrechnungsgebühren erheben.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Detaillierte Erläuterungen zu Benennungen und Beschreibungen der Rechnung
In den folgenden Abschnitten werden die meisten Benennungen, die Sie in Ihrer Rechnung finden, sowie die Beschreibungen für jede Benennung aufgeführt.

### <a name="account-information"></a>Kontoinformationen

Der Abschnitt mit den Kontoinformationen der Rechnung befindet sich oben auf der ersten Seite und enthält Informationen über Ihr Profil und Ihr Abonnement.

![Abschnitt mit Kontoinformationen der Rechnung](./media/understand-invoice/account-information-section.png)

| Begriff | BESCHREIBUNG |
| --- | --- |
| Bestellnr. des Kunden |Eine optionale Bestellnummer, die Ihnen zur Nachverfolgung zugewiesen wird |
| Invoice No. |Eine eindeutige, von Microsoft generierte Rechnungsnummer, die für Nachverfolgungszwecke verwendet wird |
| Billing Cycle |Datumsbereich, den diese Rechnung abdeckt |
| Invoice Date |Datum, an dem die Rechnung generiert wurde, in der Regel einen Tag nach dem Ende des Abrechnungszyklus |
| Payment Method |Die Zahlungsart, die für das Konto verwendet wird (Rechnung oder Kreditkarte). |
| Bill to |Rechnungsadresse, die für das Konto aufgeführt ist |
| Abonnementangebot (nutzungsbasierte Bezahlung) |Die Art des erworbenen Abonnementangebots (nutzungsbasierte Bezahlung, BizSpark Plus, Azure Pass usw.). Weitere Informationen finden Sie unter [Azure-Angebotstypen](https://azure.microsoft.com/support/legal/offer-details/). |
| Account Owner Email | Die E-Mail-Adresse, unter der das Microsoft Azure-Konto registriert ist. <br /><br />Weitere Informationen zum Ändern der E-Mail-Adresse finden Sie unter [Ändern von Profilinformationen für Ihr Azure-Konto, beispielsweise Kontakt-E-Mail-Adresse, Adresse und Telefonnummer](../manage/change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Grundlegendes zur Rechnungsübersicht
Der Abschnitt **Rechnungszusammenfassung** der Rechnung fasst die gesamten Transaktionsbeträge seit dem letzten Abrechnungszeitraum und die aktuellen Nutzungsgebühren zusammen.

![Abschnitt „Rechnungszusammenfassung“](./media/understand-invoice/invoice-summary-section.png)

Der Name des Abonnements („Produktionsspeicher“) ist der Name des Abonnements für diese Rechnung.

#### <a name="understand-the-previous-charges"></a>Grundlegendes über die vorherigen Gebühren
In den Abschnitten „Vorheriger Saldo“, „Zahlungen“ und „Restwert“ der Rechnung werden die Transaktionen seit Ihrem letzten Abrechnungszeitraum zusammengefasst.

| Begriff | BESCHREIBUNG |
| --- | --- |
| Previous Balance |Der fällige Gesamtbetrag von Ihrem letzten Abrechnungszeitraum |
| Payments |Sämtliche mit Ihrem letzten Abrechnungszeitraum verrechneten Zahlungen und Guthaben |
| Outstanding balance (from previous billing cycle) |Guthaben oder der verbleibende Saldo in Ihrem Konto seit Ihrem letzten Abrechnungszeitraum |

#### <a name="understand-the-current-charges"></a>Informationen zu aktuellen Gebühren
Der Abschnitt „Aktuelle Gebühren“ der Rechnung enthält Details zu Ihren monatlichen Gebühren für den aktuellen Abrechnungszeitraum.

| Begriff | BESCHREIBUNG |
| --- | --- |
| Usage Charges |Dies sind die monatlichen Gesamtgebühren für ein Abonnement für den aktuellen Abrechnungszeitraum.|
| Discounts |In Ihrem aktuellen Abrechnungszeitraum angewendete Dienstrabatte.|
| Adjustments |Verschiedene Guthaben (Kostenlose Nutzung, Guthaben etc.) oder ausstehende Gebühren für Ihren aktuellen Abrechnungszeitraum.<br/><br/>Wenn Sie beispielsweise den Tarif „Visual Studio Enterprise mit MSDN“ nutzen, wird hier ein monatliches Guthaben angezeigt. Wenn Sie Ihr Abonnement kündigen, werden hier alle monatlichen Nutzungsgebühren angezeigt, die die monatliche Gutschrift überschreiten, die Sie mit Ihrem Abonnementangebot erhalten. Die Gebühren werden in einem Zeitraum vom Anfang des aktuellen Abrechnungszeitraums bis zum Kündigungsdatum des Abonnements erhoben. |

#### <a name="sold-to-and-payment-instructions"></a>Käufer und Zahlungsanweisungen

In der folgenden Tabelle werden Angaben zu Käufer und Zahlungsanweisungen auf der zweiten Seite Ihrer Rechnung beschrieben.

| Begriff |BESCHREIBUNG |
| --- | --- |
| Sold To |Die Profiladresse im Konto. <br/><br/>Weitere Informationen zum Ändern dieser Adresse finden Sie unter [Ändern von Profilinformationen für Ihr Azure-Konto, beispielsweise Kontakt-E-Mail-Adresse, Adresse und Telefonnummer](../manage/change-azure-account-profile.md).|
| Payment Instructions |Anweisungen zur Zahlung je nach Zahlungsmethode (z.B. per Kreditkarte oder Rechnung) |

#### <a name="usage-charges"></a>Nutzungsgebühren

Der Abschnitt „Nutzungsgebühren“ der Rechnung enthält Informationen zu Ihren Gebühren auf Verbrauchseinheitebene.

![Abschnitt „Nutzungsgebühren“](./media/understand-invoice/usage-charges-section.png)

In der folgenden Tabelle werden die Spaltenüberschriften des Abschnitts „Nutzungsgebühren“ in Ihrer Rechnung beschrieben.

| Begriff |BESCHREIBUNG |
| --- | --- |
| Name |Gibt den Dienst der obersten Ebene für die Nutzung an |
| type |Definiert den Typ des Azure-Diensts und kann sich auf den Tarif auswirken |
| Resource |Gibt die Maßeinheit für die genutzte Verbrauchseinheit an |
| Region |Gibt den Standort des Rechenzentrums für bestimmte Dienste an, die basierend auf dem Standort des Rechenzentrums berechnet werden |
| Consumed |Die Menge der Verbrauchseinheit, die während des Abrechnungszeitraums genutzt wurde |
| Enthalten |Die Menge der Verbrauchseinheit, die kostenlos in Ihrem Abrechnungszeitraum inbegriffen ist |
| Billable |Zeigt den Unterschied zwischen der verbrauchten und der enthaltenen Menge an. Dieser Betrag wird Ihnen in Rechnung gestellt. Bei Angeboten mit nutzungsbasierter Bezahlung ohne eine im Angebot enthaltene Menge entspricht der Wert hier dem Wert unter „Verbrauchte Menge“. |
| Rate |Die Rate, die Ihnen pro abrechenbare Einheit in Rechnung gestellt wird |
| value |Zeigt das Ergebnis der Multiplikation der Spalte „Overage Quantity“ mit der Spalte „Rate“ an. Wenn die verbrauchte Menge die enthaltene Menge nicht überschreitet, fallen in dieser Spalte keine Gebühren an. |
| Zwischensumme |Die Summe aller Gebühren vor Steuern für diesen Abrechnungszeitraum |
| Grand Total |Die Summe aller Gebühren nach Steuern für diesen Abrechnungszeitraum |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Wie stelle ich sicher, dass die in meiner Rechnung aufgeführten Gebühren richtig sind?
Wenn Sie mehr über eine Gebühr in Ihrer Rechnung erfahren möchten, lesen Sie den Abschnitt [Grundlegendes über Ihre Rechnung für Microsoft Azure](review-individual-bill.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
