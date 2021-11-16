---
title: Anzeigen und Herunterladen Ihrer Azure-Rechnung
description: Hier erfahren Sie, wie Sie Ihre Azure-Rechnung anzeigen und herunterladen. Sie können Ihre Rechnung über das Azure-Portal herunterladen oder per E-Mail erhalten.
keywords: Rechnung, Rechnungsdownload, Azure-Rechnung, Azure-Nutzung
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 05/17/2021
ms.author: banders
ms.openlocfilehash: c3e37fa2d4a24344c2d417bd54544bd786fc8e75
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054234"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Anzeigen und Herunterladen der Microsoft Azure-Rechnung

Sie können Ihre Rechnung über das [Azure-Portal](https://portal.azure.com/) herunterladen oder per E-Mail erhalten. Azure-Kunden mit einem Enterprise Agreement (EA-Kunden) können die Rechnung ihrer Organisation nicht herunterladen. Stattdessen werden Rechnungen an die Person gesendet, die für den Empfang von Rechnungen für die Registrierung festgelegt wurde.

## <a name="when-invoices-are-generated"></a>Zeitpunkt der Rechnungsgenerierung

Die Rechnungsgenerierung basiert auf der Art Ihres Abrechnungskontos. Rechnungen werden für Abrechnungskonten vom Typ „Microsoft Online Services-Programm“ (MOSP), „Microsoft-Kundenvereinbarung“ (Microsoft Customer Agreement, MCA) und „Microsoft Partner-Vereinbarung“ (Microsoft Partner Agreement, MPA) erstellt. Rechnungen werden auch für EA-Abrechnungskonten (Enterprise Agreement) generiert. Allerdings werden Rechnungen für EA-Abrechnungskonten nicht im Azure-Portal angezeigt.

Weitere Informationen zu Abrechnungskonten und zur Ermittlung der Art Ihres Abrechnungskontos finden Sie unter [Anzeigen Ihrer Abrechnungskonten im Azure-Portal](../manage/view-all-accounts.md).

### <a name="invoice-status"></a>Rechnungsstatus

Wenn Sie Ihren Rechnungsstatus im Azure-Portal überprüfen, ist jede Rechnung mit einem der folgenden Statussymbole gekennzeichnet:

|  Statussymbol | Beschreibung  |
|---|---|
| ![Symbol für Status „Fällig“](./media/download-azure-invoice/due.svg) | *Fällig* wird angezeigt, wenn eine Rechnung generiert, aber noch nicht bezahlt wurde. |
| ![Symbol für Status „Überfällig“](./media/download-azure-invoice/past-due.svg)  | *Überfällig* wird angezeigt, wenn von Azure versucht wurde, Ihre Zahlungsmethode zu belasten, die Zahlung aber abgelehnt wurde. |
| ![Symbol für Status „Bezahlt“](./media/download-azure-invoice/paid.svg)  | *Bezahlt* wird angezeigt, wenn Azure Ihre Zahlungsmethode erfolgreich belastet hat. |

Wenn eine Rechnung erstellt wird, wird sie im Azure-Portal mit dem Status *Fällig* angezeigt. Der Status „Fällig“ ist normal und zu erwarten.  

Wurde eine Rechnung nicht bezahlt, wird ihr Status als *Überfällig* angezeigt. Ein überfälliges Abonnement wird deaktiviert, wenn die Rechnung nicht bezahlt wird.

## <a name="invoices-for-mosp-billing-accounts"></a>Rechnungen für MOSP-Abrechnungskonten

Ein MOSP-Abrechnungskonto wird erstellt, wenn Sie sich über die Azure-Website für Azure registrieren. Beispiele hierfür sind die Registrierung für ein [Kostenloses Azure-Konto](https://azure.microsoft.com/offers/ms-azr-0044p/), ein [Angebot mit nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) oder als [Visual Studio-Abonnent](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Kunden in ausgewählten Regionen, die sich über die Azure-Website für ein [Angebot mit nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) oder ein [kostenloses Azure-Konto](https://azure.microsoft.com/offers/ms-azr-0044p/) registrieren, verfügen möglicherweise über ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung.

Sollten Sie sich hinsichtlich der Art Ihres Abrechnungskontos nicht sicher sein, lesen Sie den [Abschnitt zum Überprüfen des Typs Ihres Abrechnungskontos](../manage/view-all-accounts.md#check-the-type-of-your-account), bevor Sie mit der Anleitung in diesem Artikel fortfahren. 

Ein MOSP-Abrechnungskonto kann über folgende Rechnungen verfügen:

**Gebühren für Azure-Dienste:** Eine Rechnung wird für jedes Azure-Abonnement mit Azure-Ressourcen generiert, die durch das Abonnement verwendet werden. Die Rechnung enthält Gebühren für einen Abrechnungszeitraum. Der Abrechnungszeitraum wird durch den Tag des Monats bestimmt, an dem das Abonnement erstellt wurde.

Ein Beispiel: John erstellt *Azure-Abonnement 01* am 5. März und *Azure-Abonnement 02* am 10. März. Die Rechnung für *Azure-Abonnement 01* enthält Gebühren, die zwischen dem fünften Tag eines Monats und dem vierten Tag des Folgemonats angefallen sind. Die Rechnung für *Azure-Abonnement 02* enthält Gebühren, die zwischen dem zehnten Tag eines Monats und dem neunten Tag des Folgemonats angefallen sind. Die Rechnungen für alle Azure-Abonnements werden in der Regel an dem Tag des Monats generiert, an dem das Konto erstellt wurde. Die Generierung kann sich jedoch um bis zu zwei Tage verzögern. Wenn John sein Konto also beispielsweise am 2. Februar erstellt hat, werden die Rechnungen für *Azure-Abonnement 01* und *Azure-Abonnement 02* in der Regel jeweils am zweiten Tag des Monats generiert. Manchmal kann es aber auch bis zu zwei Tage länger dauern.

**Azure Marketplace, Reservierungen und Spot-VMs:** Eine Rechnung wird für Reservierungen, Marketplace-Produkte und Spot-VMs generiert, die unter Verwendung eines Abonnements erworben wurden. Die Rechnung enthält jeweils die entsprechenden Gebühren des Vormonats. Ein Beispiel: John hat am 1. März und am 30. März jeweils eine Reservierung erworben. Im April wird eine einzelne Rechnung für beide Reservierungen generiert. Die Rechnungen für Azure Marketplace, Reservierungen und Spot-VMs werden immer ungefähr am neunten Tag des Monats generiert.

Wenn Sie für Azure mit Kreditkarte bezahlen und eine Reservierung erwerben, wird von Azure sofort eine Rechnung generiert. Bei Zahlung auf Rechnung wird die Reservierung dagegen in der nächsten Monatsrechnung abgerechnet.

**Azure-Supportplan:** Für Ihr Supportplanabonnement wird jeweils eine Monatsrechnung generiert. Die erste Rechnung wird am Tag des Kaufs (oder bis zu zwei Tage später) generiert. Spätere Supportplanrechnungen werden in der Regel an dem Tag des Monats generiert, an dem das Konto erstellt wurde. Die Generierung kann sich jedoch um bis zu zwei Tage verzögern.

## <a name="download-your-mosp-azure-subscription-invoice"></a>Herunterladen Ihrer Azure-Abonnementrechnung für ein MOSP

Eine Rechnung wird nur für ein Abonnement generiert, das zu einem Abrechnungskonto für ein MOSP gehört. [Überprüfen Sie Ihren Zugriff auf ein MOSP-Konto.](../manage/view-all-accounts.md#check-the-type-of-your-account) 

Sie müssen über eine Kontoadministratorrolle für ein Abonnement verfügen, um die zugehörige Rechnung herunterladen zu können. Benutzer mit der Rolle „Besitzer“, „Mitwirkender“ oder „Leser“ können die Rechnung herunterladen, wenn der Kontoadministrator ihnen die entsprechende Berechtigung erteilt hat. Weitere Informationen finden Sie unter [Berechtigen von Benutzern zum Herunterladen von Rechnungen](../manage/manage-billing-access.md#opt-in).

1. Wählen Sie im Azure-Portal auf der Seite [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Ihr Abonnement aus.
1. Wählen Sie im Abrechnungsabschnitt die Option **Rechnungen** aus.  
    ![Screenshot: Auswählen der Option „Rechnungen“ für ein Abonnement](./media/download-azure-invoice/select-subscription-invoice.png)
1. Wählen Sie die Rechnung aus, die Sie herunterladen möchten, und klicken Sie anschließend auf **Rechnungen herunterladen**.  
    ![Screenshot: Downloadoption für eine MOSP-Rechnung](./media/download-azure-invoice/downloadinvoice-subscription.png)
1. Sie können auch auf das Downloadsymbol und anschließend im Abschnitt mit den Nutzungsdetails auf die Schaltfläche **Azure-Nutzungsdatei vorbereiten** klicken, um eine Aufschlüsselung mit den täglich verbrauchten Mengen und den Gebühren herunterzuladen. Die Vorbereitung der CSV-Datei kann einige Minuten dauern.  
    ![Screenshot: Seite zum Herunterladen von Rechnung und Nutzungsdaten](./media/download-azure-invoice/usage-and-invoice-subscription.png)

Weitere Informationen über Ihre Rechnung finden Sie unter [Erläuterungen zur Rechnung für Microsoft Azure](../understand/review-individual-bill.md). Hilfe bei der Ermittlung ungewöhnlicher Kosten finden Sie unter [Analysieren unerwarteter Gebühren](analyze-unexpected-charges.md).

## <a name="download-your-mosp-support-plan-invoice"></a>Herunterladen Ihrer MOSP-Supportplanrechnung

Eine Rechnung wird nur für ein Supportplanabonnement generiert, das zu einem MOSP-Abrechnungskonto gehört. [Überprüfen Sie Ihren Zugriff auf ein MOSP-Konto.](../manage/view-all-accounts.md#check-the-type-of-your-account)

Sie müssen über eine Kontoadministratorrolle für das Supportplanabonnement verfügen, um die zugehörige Rechnung herunterladen zu können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
    ![Screenshot: Suche nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/download-azure-invoice/search-cmb.png)
1. Wählen Sie auf der linken Seite die Option **Rechnungen** aus.
1. Wählen Sie Ihr Supportplanabonnement aus.  
    [![Screenshot: Abrechnungsprofilliste einer MOSP-Supportplanrechnung](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1. Wählen Sie die Rechnung aus, die Sie herunterladen möchten, und klicken Sie anschließend auf **Rechnungen herunterladen**.  
    ![Screenshot: Downloadoption für eine MOSP-Supportplanrechnung](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-your-subscription-invoice"></a>Ermöglichen des Herunterladens Ihrer Abonnementrechnung für andere Benutzer

So laden Sie eine Rechnung herunter:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Kontoadministrator für das Abonnement an.

2.  Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suche nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/download-azure-invoice/search-cmb.png)

3.  Wählen Sie auf der linken Seite die Option **Rechnungen** aus.

4.  Wählen Sie Ihr Azure-Abonnement aus, und klicken Sie dann auf **Anderen Benutzern das Herunterladen der Rechnung gestatten**.

    [![Screenshot: Auswählen von „Zugriff auf Rechnung“](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)

5.  Wählen Sie **Ein** und anschließend im oberen Bereich der Seite die Option **Speichern** aus.  
    ![Screenshot: Auswählen von „Ein“ für „Zugriff auf Rechnung“](./media/download-azure-invoice/cmb-access-to-invoice.png)
    
> [!NOTE]
> Microsoft rät davon ab, vertrauliche oder personenbezogene Informationen an Dritte weiterzugeben. Diese Empfehlung gilt auch für die Weitergabe Ihrer Azure-Abrechnung oder -Rechnung an einen Drittanbieter für Kostenoptimierungen. Weitere Informationen finden Sie unter https://azure.microsoft.com/support/legal/ und https://www.microsoft.com/trust-center.

## <a name="get-mosp-subscription-invoice-in-email"></a>Erhalten der MOSP-Abonnementrechnung per E-Mail

Sie müssen über eine Kontoadministratorrolle für ein Abonnement oder einen Supportplan verfügen, um die zugehörige Rechnung per E-Mail erhalten zu können. Nach der Aktivierung können weitere Empfänger hinzugefügt werden, die die Rechnung ebenfalls per E-Mail erhalten sollen.

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2.  Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
3.  Wählen Sie auf der linken Seite die Option **Rechnungen** aus.
4.  Wählen Sie Ihr Azure-Abonnement oder Supportplanabonnement und dann **Rechnung per E-Mail erhalten** aus.  
    [![Screenshot: Option „Rechnung per E-Mail erhalten“](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
5. Klicken Sie auf **Rechnung per E-Mail**, und akzeptieren Sie die Bedingungen.  
    ![Screenshot: Schritt 2 der Aktivierung](./media/download-azure-invoice/invoicearticlestep02.png)
6. Die Rechnung wird an Ihre bevorzugte E-Mail-Adresse für die Kommunikation gesendet. Wählen Sie **Profil aktualisieren** aus, um die E-Mail zu aktualisieren.  
    ![Screenshot: Schritt 3 der Aktivierung](./media/download-azure-invoice/invoicearticlestep03-verifyemail.png)

## <a name="share-subscription-and-support-plan-invoice"></a>Weitergeben der Rechnung für Ihr Abonnement und Ihren Supportplan

Sie können die Rechnung für Ihr Abonnement und Ihren Supportplan jeden Monat an Ihr Buchhaltungsteam weitergeben oder sie an eine Ihrer anderen E-Mail-Adressen senden.

1. Gehen Sie dazu wie unter [Erhalten der MOSP-Abonnementrechnung per E-Mail](#get-mosp-subscription-invoice-in-email) vor, und wählen Sie **Empfänger konfigurieren** aus.  
    [![Screenshot: Auswählen von „Empfänger konfigurieren“](./media/download-azure-invoice/invoice-article-step03.png)](./media/download-azure-invoice/invoice-article-step03-zoomed.png#lightbox)
1. Geben Sie eine E-Mail-Adresse ein, und wählen Sie anschließend **Empfänger hinzufügen** aus. Sie können mehrere E-Mail-Adressen hinzufügen.  
    ![Screenshot: Hinzufügen weiterer Empfänger](./media/download-azure-invoice/invoice-article-step04.png)
1. Wählen Sie nach dem Hinzufügen aller E-Mail-Adressen am unteren Bildschirmrand die Option **Fertig** aus.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Rechnungen für MCA- und MPA-Abrechnungskonten

Ein MCA-Abrechnungskonto (Microsoft Customer Agreement, Microsoft-Kundenvereinbarung) wird erstellt, wenn Ihre Organisation mit einem Microsoft-Vertreter zusammenarbeitet, um eine Microsoft-Kundenvereinbarung zu unterzeichnen. Einige Kunden in ausgewählten Regionen, die sich über die Azure-Website für ein [Angebot mit nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) oder ein [kostenloses Azure-Konto](https://azure.microsoft.com/offers/ms-azr-0044p/) registrieren, verfügen möglicherweise ebenfalls über ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. Weitere Informationen finden Sie unter [Erste Schritte mit dem Abrechnungskonto für eine Microsoft-Kundenvereinbarung](../understand/mca-overview.md).

Ein MPA-Abrechnungskonto (Microsoft Partner Agreement, Microsoft-Partnervereinbarung) wird für CSP-Partner (Cloud Solution Provider, Cloudlösungsanbieter) erstellt, um die Kundenverwaltung in der neuen Handelsumgebung zu ermöglichen. Partner benötigen mindestens einen Kunden mit einem [Azure-Plan](/partner-center/purchase-azure-plan), damit sie das Abrechnungskonto im Azure-Portal verwalten können. Weitere Informationen finden Sie unter [Erste Schritte mit Ihrem Abrechnungskonto für eine Microsoft-Partnervereinbarung](../understand/mpa-overview.md).

Am Anfang jedes Kalendermonats wird in Ihrem Konto eine monatliche Rechnung für jedes Abrechnungsprofil erstellt. Die Rechnung enthält die jeweiligen Gebühren für alle Azure-Abonnements und andere Käufe aus dem Vormonat. Ein Beispiel: John hat *Azure-Abonnement 01* am 5. März und *Azure-Abonnement 02* am 10. März erstellt. Am 28. März hat er das Abonnement *Azure-Support 01* unter Verwendung von *Abrechnungsprofil 01* erworben. John erhält Anfang April eine einzelne Rechnung mit den Gebühren für die Azure-Abonnements und den Supportplan.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Herunterladen einer Rechnung für ein MCA-oder MPA-Abrechnungsprofil

Sie müssen über eine Rolle vom Typ „Besitzer“, „Mitwirkender“, „Leser“ oder „Rechnungs-Manager“ für ein Abrechnungsprofil verfügen, um die zugehörige Rechnung über das Azure-Portal herunterladen zu können. Benutzer mit einer Rolle vom Typ „Besitzer“, „Mitwirkender“ oder „Leser“ für ein Abrechnungskonto können Rechnungen für alle Abrechnungsprofile des Kontos herunterladen.

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2.  Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suche nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/download-azure-invoice/search-cmb.png)

3. Wählen Sie auf der linken Seite die Option **Rechnungen** aus.

    [![Screenshot: Seite „Rechnungen“ für ein MCA-Abrechnungskonto](./media/download-azure-invoice/mca-billingprofile-invoices.png)](./media/download-azure-invoice/mca-billingprofile-invoices-zoomed-in.png#lightbox)

4. Wählen Sie in der Rechnungstabelle die Rechnung aus, die Sie herunterladen möchten.

5. Klicken Sie oben auf der Seite auf die Schaltfläche **Rechnungs-PDF herunterladen**.

    [![Screenshot des Herunterladens der PDF-Rechnung](./media/download-azure-invoice/mca-billingprofile-download-invoice.png)](./media/download-azure-invoice/mca-billingprofile-download-invoice-zoomed-in.png#lightbox)

6. Sie können auch die tägliche Aufschlüsselung der verbrauchten Mengen und geschätzten Gebühren herunterladen, indem Sie auf **Azure-Nutzung herunterladen** klicken. Die Vorbereitung der CSV-Datei kann einige Minuten dauern.

## <a name="get-your-billing-profiles-invoice-in-email"></a>Erhalten der Rechnung Ihres Abrechnungsprofils per E-Mail

Sie müssen über eine Rolle vom Typ „Besitzer“ oder „Mitwirkender“ für das Abrechnungsprofil oder das zugehörige Abrechnungskonto verfügen, um die Einstellung zur Rechnung per E-Mail aktualisieren zu können. Nach der Aktivierung erhalten alle Benutzer, die über eine Rolle vom Typ „Besitzer“, „Mitwirkender“, „Leser“ oder „Rechnungs-Manager“ für ein Abrechnungsprofil verfügen, die zugehörige Rechnung per E-Mail. 

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1.  Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
1.  Wählen Sie auf der linken Seite die Option **Rechnungen** und anschließend oben auf der Seite die Option **Einstellung für Rechnungen per E-Mail** aus.  
    [![Screenshot: Einstellung für Rechnungen per E-Mail](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Sollten Sie über mehrere Abrechnungsprofile verfügen, wählen Sie ein Abrechnungsprofil und anschließend **Ja** aus.  
    [![Screenshot: Aktivierungsoption](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Wählen Sie **Speichern** aus.

Sie können anderen Benutzern Zugriff zum Anzeigen, Herunterladen und Bezahlen von Rechnungen gewähren, indem Sie ihnen die Rolle „Rechnungs-Manager“ für ein MCA- oder MPA-Abrechnungsprofil zuweisen. Wenn Sie sich dafür entschieden haben, Ihre Rechnung per E-Mail zu erhalten, erhalten die Benutzer die Rechnungen ebenfalls per E-Mail.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
1. Wählen Sie auf der linken Seite die Option **Abrechnungsprofile** aus. Wählen Sie in der Liste mit den Abrechnungsprofilen ein Abrechnungsprofil aus, für das Sie die Rolle „Rechnungs-Manager“ zuweisen möchten.  
   ![Screenshot: Abrechnungsprofilliste zum Auswählen eines Abrechnungsprofils](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. Wählen Sie auf der linken Seite die Option **Zugriffssteuerung (IAM)** und anschließend oben auf der Seite die Option **Hinzufügen** aus.  
    ![Screenshot: Zugriffssteuerungsseite](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. Wählen Sie in der Dropdownliste „Rolle“ die Option **Rechnungs-Manager** aus. Geben Sie die E-Mail-Adresse des Benutzers ein, dem Sie Zugriff gewähren möchten. Wählen Sie **Speichern** aus, um die Rolle zuzuweisen.  
    [![Screenshot: Hinzufügen eines Benutzers als Rechnungs-Manager](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)
   

## <a name="share-your-billing-profiles-invoice"></a>Weitergeben der Rechnung Ihres Abrechnungsprofils

Sie können Ihre Rechnung jeden Monat an Ihr Buchhaltungsteam weitergeben oder an eine Ihrer anderen E-Mail-Adressen senden, ohne Ihrem Buchhaltungsteam oder der anderen E-Mail-Adresse Berechtigungen für Ihr Abrechnungsprofil zu erteilen.

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1.  Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
1.  Wählen Sie auf der linken Seite die Option **Rechnungen** und anschließend oben auf der Seite die Option **Einstellung für Rechnungen per E-Mail** aus.  
    [![Screenshot: Einstellung für Rechnungen per E-Mail](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Sollten Sie über mehrere Abrechnungsprofile verfügen, wählen Sie ein Abrechnungsprofil aus.
1.  Fügen Sie im Abschnitt für zusätzliche Empfänger die gewünschten E-Mail-Adressen für den Empfang von Rechnungen hinzu.
    [![Screenshot: Zusätzliche Empfänger für die Rechnungs-E-Mail](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients.png)](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients-zoomed.png#lightbox)
1.  Wählen Sie **Speichern** aus.

##  <a name="why-you-might-not-see-an-invoice"></a>Mögliche Gründe für nicht angezeigte Rechnungen

<a name="noinvoice"></a>

Mehrere Gründe können dafür ausschlaggebend sein, dass Sie keine Rechnung sehen:

- Die Rechnung wurde noch nicht ausgestellt.
    
    - Es sind weniger als 30 Tage seit dem Abschluss Ihres Azure-Abonnements vergangen. 

    - Die Abrechnung von Azure erfolgt wenige Tage nach dem Ende Ihres Abrechnungszeitraums. Unter Umständen wurde also noch keine Rechnung generiert.

- Sie haben keine Berechtigung zur Ansicht von Rechnungen. 
    
    - Im Falle eines MCA- oder MPA-Abrechnungskontos müssen Sie über eine Rolle vom Typ „Besitzer“, „Mitwirkender“, „Leser“ oder „Rechnungs-Manager“ (für ein Abrechnungsprofil) bzw. über eine Rolle vom Typ „Besitzer“, „Mitwirkender“ oder „Leser“ (für das Abrechnungskonto) verfügen, um Rechnungen anzeigen zu können. 
    
    - Bei anderen Abrechnungskonten werden die Rechnungen unter Umständen nur für den Kontoadministrator angezeigt.

- Rechnungen werden in Ihrem Konto nicht unterstützt.

    - Wenn Sie über ein Abrechnungskonto für das Microsoft-Onlineabonnementprogramm (MOSP) verfügen und sich für ein kostenloses Azure-Konto oder für ein Abonnement mit einem monatlichen Guthaben registriert haben, erhalten Sie nur dann eine Rechnung, wenn Sie das monatliche Guthaben überschreiten.

    - Wenn Sie über ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung (MCA) oder eine Microsoft Partner-Vereinbarung (MPA) verfügen, erhalten Sie immer eine Rechnung.

- Sie haben Zugriff auf die Rechnung über eines Ihrer anderen Konten.

    - Dies ist normalerweise der Fall, wenn Sie auf einen Link in der E-Mail klicken, in der Sie aufgefordert werden, Ihre Rechnung im Portal anzuzeigen. Wenn Sie auf den Link klicken, wird folgende Fehlermeldung angezeigt: `We can't display your invoices. Please try again`. Vergewissern Sie sich, dass Sie mit der E-Mail-Adresse angemeldet sind, die über die Berechtigungen zum Anzeigen der Rechnungen verfügt.

- Sie haben Zugriff auf die Rechnung über eine andere Identität. 

    - Einige Kunden haben zwei Identitäten mit derselben E-Mail-Adresse: ein Geschäftskonto und ein Microsoft-Konto. Normalerweise verfügt nur eine der Identitäten über Berechtigungen zum Anzeigen von Rechnungen. Wenn sich die Kunden mit der Identität anmelden, die nicht über die Berechtigungen verfügt, werden die Rechnungen nicht angezeigt. Vergewissern Sie sich, dass Sie für die Anmeldung die richtige Identität verwenden.

- Sie haben sich beim falschen Azure AD-Mandanten (Azur Active Directory) angemeldet. 

    - Ihr Abrechnungskonto ist einem Azure AD-Mandanten zugeordnet. Wenn Sie bei einem falschen Mandanten angemeldet sind, wird die Rechnung für Abonnements in Ihrem Abrechnungskonto nicht angezeigt. Vergewissern Sie sich, dass Sie beim richtigen Azure AD-Mandanten angemeldet sind. Wenn dies nicht der Fall ist, wechseln Sie wie folgt den Mandanten im Azure-Portal:

        1. Wählen Sie oben rechts auf der Seite Ihre E-Mail-Adresse aus.

        2. Wählen Sie **Verzeichnis wechseln** aus.

           ![Screenshot des Auswählens von „Verzeichnis wechseln“ im Portal](./media/download-azure-invoice/select-switch-directory.png)

        3. Wählen Sie im Bereich **Alle Verzeichnisse** die Option **Wechseln** für ein Verzeichnis aus.

           ![Screenshot des Auswählens eines Verzeichnisses im Portal](./media/download-azure-invoice/select-directory.png)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um mehr über Ihre Rechnung und Gebühren zu erfahren:

- [Anzeigen und Herunterladen der Microsoft Azure-Nutzung und -Gebühren](download-azure-daily-usage.md)
- [Grundlegendes zu Ihrer Rechnung für Microsoft Azure](review-individual-bill.md)
- [Grundlegendes zu den Benennungen in Ihrer Azure-Rechnung](understand-invoice.md)

MCA-bezogene Informationen finden Sie hier:

- [Grundlegendes zu den Gebühren auf der Rechnung für Ihr Abrechnungsprofil](review-customer-agreement-bill.md)
- [Grundlegendes zu den Benennungen auf der Rechnung für Ihr Abrechnungsprofil](mca-understand-your-invoice.md)
- [Grundlegendes zur CSV-Datei für die Azure-Nutzung und -Gebühren für Ihr Abrechnungsprofil](mca-understand-your-usage.md)