---
title: Anzeigen von Transaktionen für Azure-Reservierungseinkäufe und -erstattungen
description: Erfahren Sie, wie Sie Transaktionen für Azure-Reservierungseinkäufe und -erstattungen anzeigen können.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 06/14/2021
ms.author: banders
ms.openlocfilehash: 402f2b9b8d4e7528e6578f9735e0360b4a049878
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "112114512"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Anzeigen von Transaktionen für Reservierungseinkäufe und -erstattungen

Es gibt verschiedene Möglichkeiten zum Anzeigen von Transaktionen für Reservierungseinkäufe und -erstattungen. Sie können das Azure-Portal, Power BI und REST-APIs verwenden.

## <a name="view-reservation-purchases-in-the-azure-portal"></a>Anzeigen von Reservierungskäufen im Azure-Portal

Abrechnungsleser für Enterprise Agreement und Microsoft-Kundenvereinbarung können akkumulierte Käufe für Reservierungen in der Kostenanalyse anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im Menü auf der linken Seite die Option „Kostenanalyse“ aus.
1. Wenden Sie einen Filter für  **Preismodell** an, und wählen Sie dann **Reservierung** aus.
1. Wenden Sie zum Anzeigen von Käufen für Reservierungen einen Filter für  **Gebührentyp** an, und wählen Sie dann **Kaufen** aus.
1. Legen Sie  **Granularität** auf  **Monatlich** fest.
1. Legen Sie den Diagrammtyp auf  **Säule (gestapelt)** fest.

:::image type="content" source="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" alt-text="Screenshot: Reservierungskäufe in der Kostenanalyse" lightbox="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" :::

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Anzeigen von Reservierungstransaktionen im Azure-Portal

Ein Rechnungsadministrator für die Unternehmensregistrierung oder Microsoft-Kundenvereinbarung kann Reservierungstransaktionen unter der Kostenverwaltung und Abrechnung anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
1. Wählen Sie **Reservierungstransaktionen** aus.
1. Wählen Sie zum Filtern der Ergebnisse **Zeitraum**, **Typ** oder **Beschreibung** aus.
1. Wählen Sie **Übernehmen**.

[![Screenshot: Reservierungstransaktionen im Azure-Portal](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Anzeigen von Reservierungstransaktionen in Power BI

Ein Administrator für die Unternehmensregistrierung kann Reservierungstransaktionen mit der Cost Management-Power BI-App anzeigen.

1. Rufen Sie die [Cost Management-Power BI-App](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) ab.
1. Navigieren Sie zum Bericht für RI-Käufe.

[![Beispiel mit Reservierungstransaktionen](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Weitere Informationen finden Sie unter [Azure Cost Management-Power BI-App für Enterprise Agreements](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md).

## <a name="use-apis-to-get-reservation-transactions"></a>Verwenden von APIs zum Abrufen von Reservierungstransaktionen

Benutzer mit Enterprise Agreement (EA) und Microsoft-Kundenvereinbarung können Daten zu Reservierungstransaktionen mithilfe der [API für Reservierungstransaktionen – Liste](/rest/api/consumption/reservationtransactions/list) abrufen.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Verwalten einer Reservierung finden Sie unter [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md).
- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
  - [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
  - [Verwalten von Reservierungen für Ressourcen in Azure](manage-reserved-vm-instance.md)