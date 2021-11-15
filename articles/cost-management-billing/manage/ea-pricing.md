---
title: Anzeigen und Herunterladen der Azure-Preise für Ihre Organisation
description: Erfahren Sie, wie Sie mit der Preisübersicht Ihrer Organisation Preisinformationen anzeigen oder Kosten schätzen können.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 8ee5cc31e0127ead62fa91f6b1bfab66854aac83
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260403"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Anzeigen und Herunterladen der Azure-Preise für Ihre Organisation

Azure-Kunden mit einem Azure Enterprise Agreement (EA), einer Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) oder einer Microsoft-Partnervereinbarung (Microsoft Partner Agreement, MPA) können ihre Preise im Azure-Portal anzeigen und herunterladen. [Erfahren Sie mehr über die Überprüfung des Abrechnungskontotyps](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>Herunterladen der Preise für ein Enterprise Agreement

Je nach den durch den Unternehmensadministrator für Ihre Organisation festgelegten Richtlinien bieten nur bestimmte Administratorrollen Zugriff auf die EA-Preisinformationen Ihrer Organisation. Weitere Informationen finden Sie unter [Informationen zu Azure Enterprise Agreement-Administratorrollen in Azure](understand-ea-roles.md).

1. Melden Sie sich als Unternehmensadministrator beim [Azure-Portal](https://portal.azure.com/) an.
1. Suchen Sie nach *Kostenverwaltung + Abrechnung*.  
   ![Screenshot: Suche im Azure-Portal](./media/ea-pricing/portal-cm-billing-search.png)
1. Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise zuerst ein Abrechnungskonto auswählen.
1. Wählen Sie im Navigationsmenü **Verbrauch + Gebühren** aus.  
   ![Screenshot von „Verbrauch + Gebühren“ unter „Abrechnung“](./media/ea-pricing/ea-pricing-usage-charges-nav.png)
1. Wählen Sie ![Symbol „Herunterladen“](./media/ea-pricing/download-icon.png) für den Monat **Herunterladen** aus.
1. Wählen Sie auf der Seite „Verbrauch + Gebühren“ unter dem Preisblatt **Prepare document** (Dokument vorbereiten) aus. Es kann eine Weile dauern, bis die Datei vorbereitet ist.  
    :::image type="content" source="./media/ea-pricing/download-enterprise-agreement-price-sheet-01.png" alt-text="Screenshot mit den Optionen für „Nutzung und Gebühren herunterladen“" :::
1. Wenn die Datei zum Herunterladen bereit ist, wählen Sie **CSV herunterladen** aus.


## <a name="download-pricing-for-an-mca-or-mpa-account"></a>Herunterladen der Preise für ein MCA- oder MPA-Konto

Bei einer MCA müssen Sie der Besitzer des Abrechnungsprofils, Mitwirkender, Benutzer mit Leseberechtigung oder Rechnungs-Manager sein, um die Preise anzeigen und herunterladen zu können. Bei einer MPA müssen Sie über die Rolle „Globaler Administrator“ und „Administrator-Agent“ in der Partnerorganisation verfügen, damit Sie die Preise anzeigen und herunterladen können.

### <a name="download-price-sheets-for-billed-charges"></a>Herunterladen von Preisblättern für abgerechnete Gebühren

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
1. Suchen Sie nach *Kostenverwaltung + Abrechnung*.
1. Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise zuerst ein Abrechnungskonto auswählen.
1. Wählen Sie **Rechnungen** aus.
1. Suchen Sie im Rechnungsraster nach der Zeile der Rechnung, die dem Preisblatt entspricht, das Sie herunterladen möchten.
1. Klicken Sie am Ende der Zeile auf die Auslassungspunkte (`...`).  
    ![Screenshot mit ausgewählten Auslassungspunkten](./media/ea-pricing/billingprofile-invoicegrid-new.png)
1. Wenn Sie die Preise für die Dienste in der ausgewählten Rechnung anzeigen möchten, wählen Sie **Rechnungspreisblatt** aus.
1. Wenn Sie die Preise für alle Azure-Dienste für den angegebenen Abrechnungszeitraum anzeigen möchten, wählen Sie **Azure-Preisblatt** aus.  
    ![Screenshot mit Kontextmenü mit den Preisblättern](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>Herunterladen der Preisblätter für den aktuellen Abrechnungszeitraum

Wenn Sie über eine MCA verfügen, können Sie die Preise für den aktuellen Abrechnungszeitraum herunterladen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
1. Suchen Sie nach *Kostenverwaltung + Abrechnung*.
1. Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise zuerst ein Abrechnungskonto auswählen.
1. Suchen Sie im Bereich **Übersicht** nach den Downloadlinks unter den Gebühren für den bisherigen Kalendermonat.
1. Wählen Sie **Azure-Preisblatt** aus.  
    ![Screenshot des Blatts „Übersicht“ mit dem Downloadlink](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Schätzen der Kosten mit dem Azure-Preisrechner

Sie können auch anhand der Preise Ihrer Organisation eine Schätzung der Kosten mithilfe des Azure-Preisrechners vornehmen.

1. Wechseln Sie zum [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator).
1. Wählen Sie in der rechten oberen Ecke **Anmelden** aus.
1. Wählen Sie unter **Programs and Offer (Programme und Angebot)**  > **Lizenzierungsprogramm** die Option **Enterprise Agreement (EA)** aus.
1. Wählen Sie unter **Programs and Offer (Programme und Angebot)**  > **Ausgewählter Vertrag** die Option **Nichts ausgewählt** aus.  
    ![Screenshot der verfügbaren Programme und Angebote](./media/ea-pricing/ea-pricing-calculator-estimate.png)
1. Wählen Sie die Organisation aus.
1. Wählen Sie **Übernehmen**.
1. Suchen Sie nach Produkten, und fügen Sie sie dann Ihrer Schätzung hinzu.
1. Die angezeigten geschätzten Preise basieren auf den Preisen für die ausgewählte Organisation.

## <a name="check-your-billing-account-type"></a>Überprüfen des Abrechnungskontotyps
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie EA-Kunde sind, lesen Sie folgende Artikel:

- [Verwalten des Zugriffs auf Ihre EA-Abrechnungsinformationen für Azure](manage-billing-access.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](../understand/download-azure-invoice.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Nutzung und -Gebühren](../understand/download-azure-daily-usage.md)
- [Erläuterungen zur Rechnung für Azure-Kunden mit Enterprise Agreement](../understand/review-enterprise-agreement-bill.md)

Wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben, lesen Sie die folgenden Artikel:

- [Grundlegendes zu den Begriffen auf Ihrem Preisblatt für eine Microsoft-Kundenvereinbarung](mca-understand-pricesheet.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](../understand/download-azure-invoice.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Nutzung und -Gebühren](../understand/download-azure-daily-usage.md)
- [Anzeigen und Herunterladen von Steuerdokumenten für Ihr Abrechnungsprofil](../understand/mca-download-tax-document.md)
- [Grundlegendes zu den Gebühren auf der Rechnung für Ihr Abrechnungsprofil](../understand/review-customer-agreement-bill.md)
