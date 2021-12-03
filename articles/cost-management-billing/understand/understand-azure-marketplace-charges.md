---
title: Grundlegendes zu Azure-Gebühren für externe Dienste
description: Informieren Sie sich über die Gebührenabrechnung für externe Dienste in Azure (früher Marketplace).
author: bandersmsft
ms.reviewer: sapnakeshari
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3ac9a13342d6e3fb46c9a1c709a22b67b6eeb9a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252020"
---
# <a name="understand-your-azure-external-services-charges"></a>Grundlegendes zu Azure-Gebühren für externe Dienste
Externe Dienste werden von Drittanbietern im Azure Marketplace veröffentlicht. Ein Beispiel für einen externen Dienst, den Sie in Azure erwerben können, der jedoch nicht von Microsoft veröffentlicht wird, ist SendGrid. Einige Microsoft-Produkte sind auch über den Azure Marketplace erhältlich.

## <a name="how-external-services-are-billed"></a>So werden externe Dienste in Rechnung gestellt

- Wenn Sie eine Microsoft-Kundenvereinbarung (MCA) oder eine Microsoft-Partnervereinbarung (MPA) haben, werden Ihre Dienste von Drittanbietern mit dem Rest Ihrer Azure-Dienste in Rechnung gestellt. [Überprüfen Sie Ihren Abrechnungskontotyp](#check-billing-account-type), um zu überprüfen, ob Sie Zugriff auf eine MCA oder eine MPA haben.
- Wenn Sie keine MCA oder MPA haben, werden Ihre externen Dienste separat von Ihren Azure-Diensten in Rechnung gestellt. Pro Abrechnungszeitraum erhalten Sie jeweils zwei Rechnungen: eine für Azure-Dienste und eine für Marketplace-Käufe.
- Jeder externe Dienst verwendet ein eigenes Abrechnungsmodell. Für einige Dienste wird die nutzungsbasierte Bezahlung verwendet, für andere gelten feste monatliche Gebühren.
- Die kostenlosen monatlichen Gutschriften können nicht für externe Dienste verwendet werden. Wenn Sie ein Azure-Abonnement mit [kostenlosen Gutschriften](https://azure.microsoft.com/pricing/spending-limits/) verwenden, können diese Gutschriften nicht zum Begleichen von Gebühren für externe Dienste genutzt werden. Beim Bereitstellen eines neuen externen Diensts oder einer neuen externen Ressource wird eine Warnung angezeigt:

    ![Warnung beim Erwerb von Marketplace-Angeboten](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="external-spending-for-ea-customers"></a>Externe Ausgaben für EA-Kunden

EA-Kunden können im EA-Portal die Ausgaben für externe Dienste anzeigen und Berichte herunterladen. Informationen zum Einstieg finden Sie unter [Azure Marketplace for EA Customers](https://ea.azure.com/helpdocs/azureMarketplace) (Azure Marketplace für EA-Kunden).

Direkte EA-Kunden können Ausgaben für externe Dienste im [Azure-Portal](https://portal.azure.com) anzeigen. Navigieren Sie zum Menü „Verbrauch + Gebühren“, um die Azure Marketplace-Gebühren anzuzeigen und herunterzuladen.

## <a name="view-and-download-invoices-for-external-services"></a>Anzeigen und Herunterladen von Rechnungen für externe Dienste

Wenn Sie eine Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) oder eine Microsoft-Partnervereinbarung (Microsoft Partner Agreement, MPA) haben, werden Ihre Drittanbieterdienste mit dem Rest Ihrer Azure-Dienste in einer einzelnen Rechnung abgerechnet. [Überprüfen Sie Ihren Abrechnungskontotyp](#check-billing-account-type), um zu überprüfen, ob Sie Zugriff auf eine MCA oder eine MPA haben. Wenn dies der Fall ist, erfahren Sie unter [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](download-azure-invoice.md), wie Sie Ihre Drittanbietergebühren einsehen können.

Wenn Sie keine MCA oder MPA haben, erhalten Sie separate Rechnungen über Drittanbietergebühren. 

Azure Marketplace-Gebühren werden in Ihrer Landeswährung ausgewiesen.

Um Ihre Azure Marketplace-Rechnungen über das Azure-Portal anzuzeigen und herunterzuladen, befolgen Sie diese Schritte:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
1. Wählen Sie die Option **Rechnungen** im Menü auf der linken Seite aus.
1. Wählen Sie in der Dropdownliste für Abonnements das Abonnement aus, das Ihren Marketplace-Käufen zugeordnet ist.
1. Sehen Sie sich in der Rechnungsliste die Spalte **Typ** an. Rechnungen für Marketplace-Dienste haben den Typ **Azure Marketplace und Reservierungen**. 

    ![Screenshot: Typ „Azure Marketplace und Reservierungen“ im Rechnungsraster](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. Die Ansicht kann nach Typ gefiltert werden, sodass nur Rechnungen für Azure Marketplace und Reservierungen angezeigt werden. Wählen Sie hierzu den Filter **Typ** aus. Wählen Sie anschließend in der Dropdownliste die Option **Azure Marketplace und Reservierungen** aus.

    ![Screenshot: Filter „Typ“ mit Option „Azure Marketplace und Reservierung“ in der Dropdownliste](./media/understand-azure-marketplace-charges/type-filter.png)

1. Wählen Sie auf der rechten Seite das Downloadsymbol für die Rechnung aus, die Sie herunterladen möchten.

    ![Screenshot: Downloadsymbol für die Rechnung](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. Wählen Sie unter **Rechnung** die blaue Schaltfläche **Herunterladen** aus.

    ![Screenshot: Downloadschaltfläche für Rechnung im Kontextbereich](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>Bezahlen für externe Dienste im Azure-Portal

Wenn Sie eine Microsoft-Kundenvereinbarung (MCA) oder eine Microsoft-Partnervereinbarung (MPA) haben, werden Ihre Dienste von Drittanbietern mit dem Rest Ihrer Azure-Dienste in Rechnung gestellt. [Überprüfen Sie Ihren Abrechnungskontotyp](#check-billing-account-type), um zu überprüfen, ob Sie Zugriff auf eine MCA oder eine MPA haben. Falls ja, können Sie Ihre gesamte Rechnung über das Azure-Portal begleichen, wie unter [Bezahlen Ihrer Rechnung für Microsoft Azure](pay-bill.md) beschrieben.

Falls nicht, können Sie für Ihre Marketplace-Rechnungen wie folgt über das Azure-Portal bezahlen:

1. Suchen Sie mithilfe der Schritte aus dem vorherigen Abschnitt ([Anzeigen und Herunterladen von Rechnungen für externe Dienste](#view-and-download-invoices-for-external-services)) nach Ihren Marketplace-Rechnungen.
1. Wählen Sie für die Rechnung, die Sie bezahlen möchten, den blauen Link **Jetzt zahlen** aus.

    ![Screenshot: Link „Jetzt bezahlen“ im Rechnungsraster](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > Der Link **Jetzt bezahlen** wird nur angezeigt, wenn es sich um eine Rechnung vom Typ **Azure Marketplace und Reservierungen** handelt und die Zahlung der Rechnung fällig oder überfällig ist.

1. Klicken Sie auf der neuen Seite auf den Link **Zahlungsmethode auswählen**.

    ![Screenshot: Link „Zahlungsmethode auswählen“](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. Klicken Sie nach dem Auswählen einer Zahlungsmethode links unten auf der Seite auf die blaue Schaltfläche **Jetzt zahlen**.
    ![Screenshot: Schaltfläche „Jetzt zahlen“](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>Ändern der Standardzahlungsmethode für externe Dienste

Wenn Sie einen externen Dienst erwerben, wählen Sie ein Azure-Abonnement für die Ressource aus. Die Zahlungsmethode des ausgewählten Azure-Abonnements wird zur Zahlungsmethode für den externen Dienst. Um die Zahlungsmethode für einen externen Dienst zu ändern, müssen Sie [die Zahlungsmethode des Azure-Abonnements ändern](../manage/change-credit-card.md), an das dieser externe Dienst gebunden ist. Sie können mit folgenden Schritten das Abonnement ermitteln, an das Ihre Bestellung eines externen Diensts gebunden ist:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie im linken Navigationsmenü auf **Alle Ressourcen**.
     ![Screenshot des Menüelements „Alle Ressourcen“](./media/understand-azure-marketplace-charges/all-resources.png)
1. Suchen Sie Ihren externen Dienst.
1. Achten Sie in der Spalte **Abonnement** auf den Namen des Abonnements.
    ![Screenshot des Abonnementnamens für die Ressource](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Klicken Sie auf den Namen des Abonnements, und [aktualisieren Sie die aktive Zahlungsmethode](../manage/change-credit-card.md).

## <a name="cancel-an-external-service-order"></a>Kündigen eines externen Diensts

Wenn Sie einen externen Dienst kündigen möchten, löschen Sie die Ressource im [Azure-Portal](https://portal.azure.com).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie im linken Navigationsmenü auf **Alle Ressourcen**.
    ![Screenshot des Menüelements „Alle Ressourcen“](./media/understand-azure-marketplace-charges/all-resources.png)
1. Suchen Sie Ihren externen Dienst.
1. Aktivieren Sie das Kontrollkästchen neben der Ressource, die Sie löschen möchten.
1. Wählen Sie in der Befehlsleiste die Option **Löschen** aus.
    ![Screenshot der Schaltfläche „Löschen“](./media/understand-azure-marketplace-charges/delete-button.png)
1. Geben Sie im Bestätigungsblatt *Ja* ein.
    ![Löschen der Ressource](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Klicken Sie auf **Löschen**.

## <a name="check-billing-account-type"></a>Überprüfen des Abrechnungskontotyps
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte
- [Kostenanalyse beginnen](../costs/quick-acm-cost-analysis.md)
