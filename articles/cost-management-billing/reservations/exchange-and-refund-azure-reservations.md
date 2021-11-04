---
title: Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen
description: Es wird beschrieben, wie Sie für Azure-Reservierungen einen Umtausch durchführen oder Rückerstattungen erhalten können. Für einen Umtausch oder eine Erstattung von Reservierungen benötigen Sie Besitzerzugriff auf den Reservierungsauftrag.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: 3a750e41ec5d422107dc091fb0965ed07da6a88b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469481"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen

Mit Azure-Reservierungen können Sie Ihre sich ändernden Anforderungen flexibel erfüllen. Sie können eine Reservierung gegen eine andere Reservierung des gleichen Typs tauschen. Beispielsweise können Sie mehrere Computereservierungen, einschließlich Azure Dedicated Host, Azure VMware Solution und Azure Virtual Machines, gleichzeitig zurückgeben. Mit anderen Worten: Reservierungsprodukte sind untereinander austauschbar, wenn es sich um den gleichen Reservierungstyp handelt. In einem anderen Beispiel können Sie mehrere Reservierungstypen für SQL-Datenbank, darunter „Verwaltete Instanzen“ und „Pool für elastische Datenbanken“, gegeneinander austauschen.

Verschiedenartige Reservierungen lassen sich hingegen nicht austauschen. Beispielsweise können Sie eine Cosmos DB-Reservierung nicht gegen eine für SQL-Datenbank austauschen.

Sie können auch eine Reservierung austauschen, um eine weitere Reservierung eines ähnlichen Typs in einer anderen Region zu erwerben. Sie können z. B. eine Reservierung für „USA, Westen 2“ gegen eine für „Europa, Westen“ austauschen.

Wenn Sie eine Reservierung austauschen, können Sie Ihre Laufzeit von einem Jahr in drei Jahre ändern.

Sie können sich Reservierungen auch zurückerstatten lassen, doch darf die Summe aller stornierten Reservierungen in Ihrem Abrechnungsbereich (z. B. EA, Microsoft-Kundenvereinbarung und Microsoft Partner-Vereinbarung) einen Betrag von 50.000 US-Dollar in einem rollierenden Zeitfenster von zwölf Monaten nicht überschreiten.

Reservierte Azure Databricks-Kapazität, Reservierungen für Azure VMware Solution by CloudSimple, Reservierungen für Azure Red Hat Open Shift, Red Hat-Pläne sowie SUSE Linux-Pläne sind nicht erstattungsfähig.

> [!NOTE]
> - **Sie benötigen Besitzerzugriff auf den Reservierungsauftrag, um eine vorhandene Reservierung umtauschen oder zurückerstatten zu können**. Sie können [Benutzer, die eine Reservierung verwalten können, hinzufügen oder ändern](./manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
> - Microsoft berechnet derzeit bei Reservierungserstattungen keine Gebühren für die vorzeitige Kündigung. Unter Umständen werden zukünftig Gebühren bei Rückerstattungen fällig. Derzeit ist noch kein Startdatum für die Berechnung der Gebühren festgelegt.

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>Umtauschen oder Zurückerstatten einer vorhandenen Reservierung

Sie können Ihre Reservierung über das [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) umtauschen.

1. Wählen Sie die Reservierungen aus, die Sie umtauschen möchten, und klicken Sie auf **Umtausch**.  
    [![Beispielbild mit Reservierungen, die zurückgegeben werden sollen](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. Wählen Sie das VM-Produkt aus, das Sie erwerben möchten, und geben Sie eine Menge ein. Achten Sie darauf, dass der neue Gesamtbetrag des Kaufs höher als der Umtauschwert ist. [Bestimmen der richtigen Größe vor dem Kauf](../../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)  
    [![Beispielbild mit dem VM-Produkt, das per Umtausch erworben werden soll](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. Überprüfen Sie die Transaktion, und schließen Sie sie ab.  
    [![Beispielbild mit dem VM-Produkt, das per Umtausch erworben werden soll, und Abschluss des Vorgangs](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

Navigieren Sie zum Erhalten einer Erstattung für eine Reservierung zu **Reservierungsdetails**, und klicken Sie auf **Erstattung**.

## <a name="exchange-multiple-reservations"></a>Austauschen mehrerer Reservierungen

Sie können ähnliche Typen von Reservierungen in einem Vorgang zurückgeben.

Wenn Sie Reservierungen austauschen, muss der neue Währungsbetrag des Kaufs größer sein als der Erstattungsbetrag. Wenn der neue Kaufbetrag kleiner als der Erstattungsbetrag ist, erhalten Sie eine Fehlermeldung. Wenn die Fehlermeldung angezeigt wird, verringern Sie die Menge, die Sie zurückgeben möchten, oder erhöhen Sie die Menge zu kaufender Produkte.

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu **Reservierungen**.
1. Aktivieren Sie in der Liste der Reservierungen das Kontrollkästchen für jede Reservierung, die Sie austauschen möchten.
1. Wählen Sie oben auf der Seite **Austausch** aus.
1. Korrigieren Sie bei Bedarf die Rückgabemenge für jede Reservierung.
1. Wenn Sie die automatisch ausgefüllte Rückgabemenge auswählen, können Sie **Alle erstatten** wählen, um die Liste mit der vollen Menge zu füllen, die Sie für jede Reservierung besitzen. Oder Sie können **Für Auslastung optimieren (7 Tage)** wählen, um die Liste mit einer Menge zu füllen, die für die Auslastung basierend auf den letzten sieben Tagen der Nutzung optimiert ist. Klicken Sie auf **Anwenden**.
1. Wählen Sie unten auf der Seite **Weiter: Kaufen** aus.
1. Wählen Sie auf der Registerkarte „Kaufen“ die verfügbaren Produkte aus, die Sie austauschen möchten. Sie können mehrere Produkte verschiedener Typen auswählen.
1. Wählen Sie im Bereich „Wählen Sie das Produkt aus, das Sie erwerben möchten“ die gewünschten Produkte und dann **Zu Warenkorb hinzufügen** aus. Klicken Sie anschließend auf **Schließen**.
1. Klicken Sie dann auf **Weiter: Prüfen**.
1. Überprüfen Sie Ihre zurückzugebenden Reservierungen und neuen zu kaufenden Reservierungen, und wählen Sie dann **Austausch bestätigen** aus.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Umtauschen einer Reservierung ohne Storage Premium gegen eine Reservierung mit Storage Premium

Sie können eine erworbene Reservierung für eine VM-Größe, für die Storage Premium nicht unterstützt wird, gegen eine entsprechende VM-Größe umtauschen, für die diese Unterstützung vorhanden ist. Beispiel: _F1_ gegen _F1s_. Navigieren Sie zum Durchführen des Umtauschs zu „Reservierungsdetails“, und klicken Sie auf **Umtausch**. Beim Umtausch wird der Zeitraum der reservierten Instanz nicht zurückgesetzt und auch keine neue Transaktion erstellt.
Wenn Sie den Umtausch für eine andere Größe, Serie, Region oder Zahlungshäufigkeit durchführen, wird der Zeitraum für die neue Reservierung zurückgesetzt. 

## <a name="how-transactions-are-processed"></a>Verarbeitung von Transaktionen

Zunächst storniert Microsoft die vorhandene Reservierung und erstattet den entsprechenden anteiligen Betrag. Bei einem Umtausch wird der neue Kauf verarbeitet. Microsoft nutzt für die Verarbeitung von Erstattungen eine der folgenden Methoden. Dies richtet sich nach Ihrem Kontotyp und der Zahlungsmethode:

### <a name="enterprise-agreement-customers"></a>Enterprise Agreement-Kunden

Ein Geldbetrag wird der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) für Umtausch und Rückerstattungen hinzugefügt, wenn dies Teil des ursprünglichen Kaufs war. Falls der Zeitraum der Azure-Vorauszahlung für die Nutzung der erworbenen Reservierung nicht mehr aktiv ist, wird das Guthaben dem Zeitraum Ihrer Azure-Vorauszahlung unter dem derzeitigen Enterprise Agreement hinzugefügt. Das Guthaben ist ab dem Datum der Rückerstattung 90 Tage lang gültig. Nicht verwendetes Guthaben verfällt nach 90 Tagen.

Wenn der ursprüngliche Kauf als Überschreitung erfolgte, werden die Ursprungsrechnung für den Erwerb der Reservierung und alle nachfolgenden Rechnungen erneut geöffnet und angepasst. Microsoft gibt eine Gutschrift für die Rückerstattungen aus.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Nutzungsbasierte Bezahlung und das CSP-Programm

Die ursprüngliche Rechnung für den Kauf der Reservierung wird storniert, und anschließend wird eine neue Rechnung für die Erstattung erstellt. Bei einem Umtausch werden für die neue Rechnung die Erstattung und der neue Kauf angezeigt. Der Erstattungsbetrag wird anhand des Kaufbetrags angepasst. Falls Sie nur eine Erstattung für eine Reservierung durchgeführt haben, verbleibt der anteilige Betrag bei Microsoft, und die Anpassung wird bei einem zukünftigen Kauf einer Reservierung vorgenommen. Wenn Sie eine Reservierung zu Tarifen für die nutzungsbasierte Bezahlung gekauft haben und später zu CSP wechseln, kann die Reservierung zurückgegeben und ohne Strafgebühr erneut erworben werden.
Es ist nicht möglich, selbst eine Rückerstattung im CSP vorzunehmen. Wenden Sie sich an den Support Ihres CSP, wenn Sie eine Anforderung zur Rückerstattung haben.

### <a name="pay-as-you-go-credit-card-customers"></a>Kreditkartenkunden mit nutzungsbasierter Bezahlung

Die ursprüngliche Rechnung wird storniert, und eine neue Rechnung wird erstellt. Der Betrag wird über die Kreditkarte erstattet, die für den ursprünglichen Kauf genutzt wurde. [Wenden Sie sich an den Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), falls sich Ihre Kartenangaben geändert haben.

## <a name="cancel-exchange-and-refund-policies"></a>Richtlinien für Stornierung, Umtausch und Rückerstattung

In Azure gelten folgende Richtlinien für Stornierungen, Umtausch und Rückerstattungen:

**Umtauschrichtlinien**

- Sie können mehrere vorhandene Reservierungen zurückgeben, um eine neue Reservierung des gleichen Typs zu erwerben. Ein Wechsel des Typs von Reservierungen ist beim Umtausch nicht möglich. Beispielsweise können Sie keine VM-Reservierung zurückgeben, um eine SQL-Reservierung zu erwerben. Bei einem Umtausch können Sie eine Reservierungseigenschaft ändern, z. B. Familie, Serie, Version, SKU, Region, Menge und Zeitraum.
- Nur Besitzer von Reservierungen können einen Umtausch verarbeiten. [Informieren Sie sich über das Hinzufügen oder Ändern von Benutzern, die eine Reservierung verwalten können](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
- Ein Umtausch wird als Erstattung und erneuter Kauf verarbeitet. Für die Stornierung und den neuen Reservierungserwerb werden separate Transaktionen erstellt. Der anteilige Reservierungsbetrag für die zurückgegebenen Reservierungen wird erstattet. Der neue Kauf wird Ihnen vollständig berechnet. Der anteilige Reservierungsbetrag ist der tägliche anteilige Restwert der zurückgegebenen Reservierung.
- Sie können Reservierungen auch dann umtauschen oder zurückgeben, wenn das für den Kauf der Reservierung verwendete Enterprise Agreement abgelaufen ist und in Form eines neuen Vertrags verlängert wurde.
- Die Laufzeit der neuen Reservierung sollte gleich oder größer als die verbleibende Laufzeit der zurückgegebenen Reservierung sein. Beispiel: Bei einer dreijährigen Reservierung mit einem Wert von 100 US-Dollar pro Monat, die nach der 18. Zahlung umgetauscht wird, sollte die Laufzeit der neuen Reservierung mindestens einem Wert von 1.800 US-Dollar entsprechen (monatlich oder im Voraus bezahlt).
- Für die neue Reservierung, die im Rahmen des Umtauschs erworben wird, gilt ein neuer Zeitraum, der ab dem Umtauschzeitpunkt beginnt.
- Für den Umtausch gibt es keine Strafzahlungen oder jährlichen Grenzwerte.

**Rückerstattungsrichtlinien**

- Zurzeit wird keine Gebühr für die vorzeitige Kündigung berechnet, aber in Zukunft kann bei Stornierungen ggf. eine Gebühr für die vorzeitige Beendigung in Höhe von 12 % anfallen.
- Die stornierte Reservierung darf insgesamt in einem rollierenden Zeitfenster von zwölf Monaten für ein Abrechnungsprofil oder eine einzelne Registrierung den Wert von 50.000 US-Dollar nicht überschreiten. Beispielsweise beträgt bei einer dreijährigen Reservierung mit einem Wert von 100 US-Dollar pro Monat, die im 18. Monat zurückerstattet wird, die Summe für die stornierte Reservierung 1.800 US-Dollar. Nach der Rückerstattung beträgt das neue verfügbare Rückerstattungslimit 48.200 US-Dollar. 365 Tage nach der Erstattung wird das Limit von 48.200 US-Dollar um 1.800 US-Dollar erhöht, und der neue Pool beläuft sich auf 50.000 US-Dollar. Durch jede weitere Reservierungsstornierung für das Abrechnungsprofil oder die EA-Registrierung wird derselbe Pool verringert, und die gleiche Auffüllungslogik wird angewandt.
- Azure verarbeitet keine Rückerstattung, die das Limit von 50.000 US-Dollar in einem Zeitraum von 12 Monaten für ein Abrechnungsprofil oder eine EA-Registrierung überschreitet.
    - Rückerstattungen, die sich aus einem Umtausch ergeben, werden nicht auf das Rückerstattungslimit angerechnet.
- Rückerstattungen werden auf der Grundlage des niedrigsten Preises (Ihr Kaufpreis oder der aktuelle Preis für die Reservierung) berechnet.
- Nur Besitzer von Reservierungsaufträgen können eine Erstattung verarbeiten. [Informieren Sie sich über das Hinzufügen oder Ändern von Benutzern, die eine Reservierung verwalten können](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Verwalten einer Reservierung finden Sie unter [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md).
- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
    - [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
    - [Verwalten von Reservierungen für Ressourcen in Azure](manage-reserved-vm-instance.md)
    - [Grundlegendes zur Anwendung des Rabatts für Azure-Reservierungen auf virtuelle Computer](../manage/understand-vm-reservation-charges.md)
    - [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md)
    - [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)
    - [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](reserved-instance-windows-software-costs.md)
    - [Azure-Reservierungen im CSP-Programm](/partner-center/azure-reservations)
