---
title: Herunterladen der Azure-Rechnung
description: Hier erfahren Sie, wie Sie Ihre Azure-Rechnung herunterladen oder anzeigen können.
keywords: Rechnung, Rechnungsdownload, Azure-Rechnung
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: bc159ccab9d2a059d98de59e123d3e708ade9b2b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450075"
---
# <a name="download-or-view-your-azure-billing-invoice"></a>Herunterladen oder Anzeigen Ihrer Azure-Rechnung

Für die meisten Abonnements können Sie die Rechnung aus dem [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) herunterladen oder per E-Mail zugeschickt bekommen. Wenn Sie Azure-Kunde mit einem Enterprise Agreement (EA-Kunde) sind, können Sie die Rechnungen Ihrer Organisation nicht herunterladen. Rechnungen werden an den Benutzer gesendet, der für den Empfang von Rechnungen für die Registrierung festgelegt wurde.

Nur bestimmte Rollen (z. B. „Kontoadministrator“ oder „Unternehmensadministrator“) verfügen über die Berechtigung zum Abrufen der Rechnung. Weitere Informationen zum Zugriff auf Abrechnungsinformationen finden Sie unter [Verwalten des Zugriffs auf die Azure-Abrechnung mithilfe von Rollen](manage-billing-access.md).

Wenn Sie über eine Microsoft-Kundenvereinbarung verfügen, muss Ihnen die Rolle „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ zugewiesen sein, um Abrechnungsinformationen anzuzeigen. Weitere Informationen zu Abrechnungsrollen für Microsoft-Kundenvereinbarungen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Herunterladen Ihrer Azure-Rechnungen (PDF-Format)

Für die meisten Abonnements können Sie die Rechnung aus dem Azure-Portal herunterladen. Wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben, finden Sie weitere Informationen unter „Herunterladen von Rechnungen für ein Abrechnungsprofil“.

### <a name="download-invoices-for-an-individual-subscription"></a>Herunterladen von Rechnungen für ein einzelnes Abonnement

1. Wählen Sie im Azure-Portal auf der Seite [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Ihr Abonnement als [Benutzer mit Zugriff auf Rechnungen](manage-billing-access.md) aus.

2. Wählen Sie **Rechnungen** aus.

    ![Screenshot mit der Option „Abrechnung und Nutzung“](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klicken Sie auf die Schaltfläche „Herunterladen“, um eine Kopie Ihrer PDF-Rechnung herunterzuladen, und wählen Sie dann **Rechnung herunterladen** aus. Falls **Nicht verfügbar** angezeigt wird, siehe [Warum sehe ich keine Rechnung für den letzten Abrechnungszeitraum?](#noinvoice)

    ![Screenshot mit Abrechnungszeiträumen, der Option „Herunterladen“ und den Gesamtgebühren für jeden Abrechnungszeitraum](./media/download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. Sie können auch eine tägliche Aufschlüsselung der verbrauchten Mengen und geschätzten Gebühren herunterladen, indem Sie auf **CSV herunterladen** klicken.

    ![Screenshot mit der Option „Rechnung herunterladen“ und Nutzungsseite](./media/download-azure-invoice-daily-usage-date/usageandinvoice.png)

Weitere Informationen über Ihre Rechnung finden Sie unter [Erläuterungen zur Rechnung für Microsoft Azure](../understand/review-individual-bill.md). Hilfe zum Verwalten Ihrer Kosten finden Sie unter [Analysieren unerwarteter Gebühren](../understand/analyze-unexpected-charges.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Herunterladen von Rechnungen für eine Microsoft-Kundenvereinbarung

Rechnungen werden für jedes [Abrechnungsprofil](../understand/mca-overview.md#billing-profiles) in der Microsoft-Kundenvereinbarung generiert. Sie müssen über die Rolle „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ verfügen, um Rechnungen über das Azure-Portal herunterladen zu können.

1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
2. Wählen Sie ein Abrechnungsprofil aus.
3. Wählen Sie **Rechnungen** aus.
4. Suchen Sie im Rechnungsraster nach der Zeile der Rechnung, die Sie herunterladen möchten.
5. Klicken Sie am Ende der Zeile auf die Schaltfläche „Herunterladen“.
6. Wählen Sie im Kontextmenü für den Download **Rechnung** aus.

Wenn keine Rechnung für den letzten Abrechnungszeitraum angezeigt wird, finden Sie unter **Warum sehe ich keine Rechnung für den letzten Abrechnungszeitraum?** weitere Informationen. <!-- Fix this -->
### <a name="why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> Warum sehe ich keine Rechnung für den letzten Abrechnungszeitraum?

Mehrere Gründe können dafür ausschlaggebend sein, dass Sie keine Rechnung sehen:

- Es sind weniger als 30 Tage seit dem Abschluss Ihres Azure-Abonnements vergangen.

- Die Rechnung ist noch nicht erstellt. Warten Sie bis zum Ende des Abrechnungszeitraums.

- Sie haben keine Berechtigung zur Ansicht von Rechnungen. Wenn Sie über eine Microsoft-Kundenvereinbarung verfügen, müssen Sie eine der Rollen „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ besitzen. Für andere Abonnements werden ältere Rechnungen nur angezeigt, wenn Sie der Kontoadministrator sind. Weitere Informationen zum Zugriff auf Abrechnungsinformationen finden Sie unter [Verwalten des Zugriffs auf die Azure-Abrechnung mithilfe von Rollen](manage-billing-access.md).

- Wenn Sie eine kostenlose Testversion oder eine nicht überschrittene monatliche Gutschrift für Ihr Abonnement verwenden, erhalten Sie nur eine Rechnung, wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben.

## <a name="get-your-invoice-in-email-pdf"></a>Empfangen Ihrer Rechnung per E-Mail (PDF)

Sie können die entsprechende Option aktivieren und zusätzliche Empfänger konfigurieren, die Ihre Azure-Rechnung in einer E-Mail erhalten. Diese Funktion ist für bestimmte Abonnements möglicherweise nicht verfügbar, z.B. für Supportangebote, Enterprise Agreements oder Azure in Open. Wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben, helfen Ihnen die Informationen unter [Abrufen der Rechnungen für Abonnements per E-Mail](../understand/download-azure-invoice.md#get-your-billing-profiles-invoice-in-email) weiter.

### <a name="get-your-subscriptions-invoices-in-email"></a>Abrufen der Rechnungen für Abonnements per E-Mail

1. Wählen Sie auf der Seite [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Ihr Abonnement aus. Wählen Sie jedes Abonnement aus, das Sie besitzen. Klicken Sie auf **Rechnungen** und dann auf **Rechnung per E-Mail**.

    ![Screenshot mit dem Ablauf der Aktivierung](./media/download-azure-invoice-daily-usage-date/invoicesdeeplink01.png)

2. Klicken Sie auf **Aktivieren**, und stimmen Sie den Bedingungen zu.

    ![Screenshot mit dem Ablauf von Schritt 2 der Aktivierung](./media/download-azure-invoice-daily-usage-date/invoicearticlestep02.png)

3. Nachdem Sie die Vereinbarung akzeptiert haben, können Sie zusätzliche Empfänger konfigurieren. Wenn ein Empfänger entfernt wird, wird die E-Mail-Adresse nicht mehr gespeichert. Wenn Sie Ihre Meinung ändern, müssen Sie ihn erneut hinzufügen.

    ![Screenshot mit dem Ablauf von Schritt 3 der Aktivierung](./media/download-azure-invoice-daily-usage-date/invoicearticlestep03.png)

Wenn Sie nach dem Ausführen dieser Schritte keine E-Mail erhalten, sollten Sie sicherstellen, dass Ihre E-Mail-Adresse in den [Kommunikationseinstellungen Ihres Profils](https://account.microsoft.com/profile) im Microsoft-Kontocenter korrekt ist.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Kündigen des Erhalts der Rechnungen für Abonnements per E-Mail

Sie können den Erhalt von Rechnungen per E-Mail kündigen, indem Sie die oben aufgeführten Schritte befolgen und auf **Abonnement für Rechnungen per E-Mail kündigen** klicken. Dadurch werden alle E-Mail-Adressen entfernt, an die Rechnungen gesendet werden sollten. Sie können die Empfänger neu konfigurieren, wenn Sie die Rechnungen wieder per E-Mail empfangen möchten.

 ![Screenshot mit dem Ablauf der Kündigung](./media/download-azure-invoice-daily-usage-date/invoicearticlestep04.png)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Abrufen von Rechnungen für die Microsoft-Kundenvereinbarung per E-Mail

Wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben, können Sie Ihre Rechnungen per E-Mail empfangen. Alle Benutzer mit den Rollen „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ und „Rechnungs-Manager“ erhalten die Rechnungen dann per E-Mail. Leseberechtigte können die Einstellungen für den Empfang der Rechnungen per E-Mail jedoch nicht bearbeiten.

1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
1. Wählen Sie ein Abrechnungsprofil aus.
1. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.
1. Wählen Sie unter **Rechnung per E-Mail** die Option **Einstellung für Rechnung per E-Mail aktualisieren** aus.
1. Wählen Sie **Aktivieren** aus.
1. Klicken Sie auf **Aktualisieren**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Kündigen des Erhalts der Rechnungen für Abrechnungsprofile per E-Mail

Sie können den Erhalt von Rechnungen per E-Mail kündigen, indem Sie die oben aufgeführten Schritte befolgen und auf **Deaktivieren** klicken. Alle Benutzer mit den Rollen „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ und „Rechnungs-Manager“ erhalten dann ebenfalls keine Rechnungen mehr per E-Mail. Als Leseberechtigter können Sie die Einstellungen für den Empfang der Rechnungen per E-Mail nicht bearbeiten.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um mehr über Ihre Rechnung und Gebühren zu erfahren:

- [Grundlegendes zu Ihrer Rechnung für Microsoft Azure](../understand/review-individual-bill.md)
- [Grundlegendes zu den Benennungen in Ihrer Azure-Rechnung](../understand/understand-invoice.md)
- [Grundlegendes über Benennungen zu den Gebühren in der Datei mit ausführlichen Nutzungsdaten zu Microsoft Azure](../understand/understand-usage.md)
- [Anzeigen der Azure Enterprise Agreement-Preise für Ihre Organisation](ea-pricing.md)

Wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben, lesen Sie die folgenden Artikel:

- [Grundlegendes zu den Gebühren auf der Rechnung für Ihr Abrechnungsprofil](../understand/review-customer-agreement-bill.md)
- [Grundlegendes zu den Benennungen auf der Rechnung für Ihr Abrechnungsprofil](../understand/mca-understand-your-invoice.md)
- [Grundlegendes zur CSV-Datei für die Azure-Nutzung und -Gebühren für Ihr Abrechnungsprofil](../understand/mca-understand-your-usage.md)
- [Anzeigen und Herunterladen von Steuerdokumenten für Ihr Abrechnungsprofil](../understand/mca-download-tax-document.md)
- [Anzeigen der Azure Enterprise Agreement-Preise für Ihre Organisation](ea-pricing.md)
