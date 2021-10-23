---
title: 'Überprüfen der Rechnung für Ihre Microsoft Partner-Vereinbarung: Azure'
description: Erfahren Sie, wie Sie Ihre Rechnung und Ihre Ressourcennutzung sowie Gebühren der Rechnung für Ihre Microsoft Partner-Vereinbarung überprüfen.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: acff6020d401390ab514c168241db8daca801691
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705810"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>Tutorial: Überprüfen der Rechnung Ihrer Microsoft Partner-Vereinbarung

Im Abrechnungskonto für eine Microsoft-Partnervereinbarung wird jeden Monat eine Rechnung für jedes Abrechnungsprofil generiert. Die Rechnung enthält alle Kundengebühren des vorherigen Monats. Durch Analysieren der einzelnen Transaktionen im Azure-Portal können Sie die Gebühren auf Ihrer Rechnung verstehen. Sie können Ihre Rechnungen auch im Azure-Portal anzeigen und die Gebühren mit der Nutzungsdetaildatei vergleichen.

Weitere Informationen finden Sie unter [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](download-azure-invoice.md).

Dieses Tutorial gilt nur für Azure-Partner mit einer Microsoft Partner-Vereinbarung.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen abgerechneter Transaktionen im Azure-Portal
> * Überprüfen der ausstehenden Gebühren zum Schätzen Ihrer nächsten Rechnung
> * Analysieren Ihrer Azure-Nutzungsgebühren

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Zugriff auf ein Abrechnungskonto für eine Microsoft Partner-Vereinbarung.

Seit dem Abschluss des Azure-Abonnements müssen mehr als 30 Tage vergangen sein. Die Abrechnung von Azure erfolgt am Ende Ihres Rechnungszeitraums.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

- Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung

Überprüfen Sie den Vertragstyp, um zu bestimmen, ob Sie Zugriff auf ein Abrechnungskonto für eine Microsoft-Partnervereinbarung haben.

Geben Sie im Azure-Portal *Kostenverwaltung + Abrechnung* in das Suchfeld ein, und wählen Sie anschließend **Kostenverwaltung + Abrechnung** aus.

![Screenshot, der die Suche im Azure-Portal zeigt](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

Wenn Sie Zugriff auf genau einen Abrechnungsbereich haben, wählen Sie auf der linken Seite **Eigenschaften** aus. Sie haben Zugriff auf ein Abrechnungskonto, wenn der Typ des Abrechnungskontos **Microsoft-Partnervereinbarung** ist.

![Screenshot einer Microsoft Partner-Vereinbarung auf der Seite „Eigenschaften“](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

Wenn Sie Zugriff auf mehrere Abrechnungsbereiche haben, überprüfen Sie in der Spalte „Abrechnungskonto“ den Typ. Sie haben Zugriff auf ein Abrechnungskonto, wenn der Typ des Abrechnungskontos für einen der Bereiche **Microsoft-Partnervereinbarung** ist.

![Screenshot einer Microsoft Partner-Vereinbarung auf der Seite mit der Liste der Abrechnungskonten](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Überprüfen abgerechneter Transaktionen im Azure-Portal

Wählen Sie unter „Kostenverwaltung + Abrechnung“ auf der linken Seite **Alle Transaktionen** aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise zuerst ein Abrechnungskonto, ein Abrechnungsprofil oder einen Kunden und dann **Alle Transaktionen** auswählen.

Auf der Seite „Alle Transaktionen“ werden die folgenden Informationen angezeigt:

![Screenshot der Liste der abgerechneten Transaktionen](./media/review-partner-agreement-bill/all-transactions.png)

|Column  |Definition  |
|---------|---------|
|Date     | Das Datum der Transaktion  |
|Rechnungs-ID     | Der Bezeichner für die Rechnung, auf der die Transaktion in Rechnung gestellt wurde. Wenn Sie eine Supportanfrage senden, teilen Sie dem Azure-Support die ID mit, um die Bearbeitung Ihrer Supportanfrage zu beschleunigen. |
|Transaktionstyp     |  Der Typ der Transaktion, z. B. Erwerb, Stornierung und Nutzungsgebühren  |
|Produktfamilie     | Die Produktkategorie, z. B. „Compute“ für virtuelle Computer oder „Datenbank“ für Azure SQL-Datenbank|
|Produkt-SDKU     | Ein eindeutiger Code, der die Instanz Ihres Produkts identifiziert |
|Amount     |  Der Betrag der Transaktion      |
|Abrechnungsprofil     | Die Transaktion wird auf der Rechnung dieses Abrechnungsprofils angezeigt. |

Suchen Sie nach einer Rechnungs-ID, um die Transaktionen für die Rechnung zu filtern.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Überprüfen der ausstehenden Gebühren zum Schätzen Ihrer nächsten Rechnung

Gebühren werden geschätzt und gelten bis zur Rechnungsstellung als ausstehend. Sie können ausstehende Gebühren für das Abrechnungsprofil Ihrer Microsoft-Partnervereinbarung im Azure-Portal anzeigen, um Ihre nächste Rechnung einschätzen zu können. Ausstehende Gebühren sind Schätzungen und enthalten keine Steuern. Die tatsächlichen Gebühren in Ihrer nächsten Rechnung weichen von den ausstehenden Gebühren ab.

### <a name="view-pending-transactions"></a>Anzeigen von ausstehenden Transaktionen

Nachdem Sie die ausstehenden Gebühren identifiziert haben, können Sie die Gebühren nachvollziehen und verstehen, indem Sie die einzelnen Transaktionen analysieren, die zur Entstehung dieser Gebühren beigetragen haben. Zu diesem Zeitpunkt werden die ausstehenden Nutzungsgebühren nicht auf der Seite „Alle Transaktionen“ angezeigt. Sie können die ausstehenden Nutzungsgebühren auf der Azure-Abonnementseite anzeigen.

Wählen Sie unter „Kostenverwaltung + Abrechnung“ ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto auswählen. Wählen Sie im Abrechnungskonto die Option **Abrechnungsprofile** aus, und wählen Sie dann ein Abrechnungsprofil aus.

Wählen Sie links auf der Seite die Option **Alle Transaktionen** aus.

Suchen Sie nach *ausstehend*. Verwenden Sie den Filter **Zeitraum**, um die ausstehenden Gebühren für den aktuellen oder letzten Monat anzuzeigen.

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Ausstehende Gebühren nach Kunden anzeigen

Wählen Sie unter „Kostenverwaltung + Abrechnung“ ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto auswählen. Wählen Sie im Abrechnungskonto die Option **Abrechnungsprofile** aus, und wählen Sie dann ein Abrechnungsprofil aus.

Wählen Sie links auf der Seite **Kunden** aus.

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

Auf der Seite „Kunden“ werden die Gebühren des aktuellen und des letzten Monats für jeden Kunden angezeigt, der dem Abrechnungsprofil zugeordnet ist. Die Gebühren für den bisherigen Kalendermonat sind die ausstehenden Gebühren für den aktuellen Monat. Sie werden abgerechnet, wenn die Rechnung für den Monat generiert wird. Wenn die Rechnung für den letzten Monat noch nicht generiert wurde, sind die Gebühren im letzten Monat auch ausstehende Gebühren.

### <a name="view-pending-usage-charges"></a>Anzeigen der ausstehenden Nutzungsgebühren

Wählen Sie unter „Kostenverwaltung + Abrechnung“ ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto auswählen. Wählen Sie im Abrechnungskonto die Option **Abrechnungsprofile** aus, und wählen Sie dann ein Abrechnungsprofil aus.

Wählen Sie links auf der Seite **Azure-Abonnements** aus. Auf der Azure-Abonnementseite werden die Gebühren des aktuellen und des letzten Monats für jedes Abonnement im Abrechnungsprofil angezeigt. Die Gebühren für den bisherigen Kalendermonat sind die ausstehenden Gebühren für den aktuellen Monat. Sie werden abgerechnet, wenn die Rechnung für den Monat generiert wird. Wenn die Rechnung für den letzten Monat noch nicht generiert wurde, sind die Gebühren im letzten Monat auch ausstehende Gebühren.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analysieren Ihrer Azure-Nutzungsgebühren

Verwenden Sie die CSV-Datei zu Azure-Nutzung und -Gebühren, um Ihre nutzungsbasierten Gebühren zu analysieren. Sie können die Datei zu Azure-Nutzung und -Gebühren filtern, um die Nutzungsgebühren für jedes in der Rechnungs-PDF aufgelistete Produkt abzustimmen. Um detaillierte Nutzungsgebühren für ein bestimmtes Produkt anzuzeigen, filtern Sie die Spalte **product** in der CSV-Datei zu Azure-Nutzung und -Gebühren, um nur den Namen dieses Produkts einzuschließen.

Sie können auch die Spalte **customerName**  in der CSV-Datei zu Azure-Nutzung und -Gebühren filtern, um die täglichen Nutzungsgebühren für die einzelnen Kunden anzuzeigen. Wenn Sie die täglichen Nutzungsgebühren nach Azure-Abonnement anzeigen möchten, filtern Sie die Spalte **subscriptionName** .

## <a name="pay-your-bill"></a>Bezahlen Ihrer Rechnung

Die Anweisungen zum Bezahlen Ihrer Rechnung finden Sie unten auf der Rechnung. Sie können innerhalb von 60 Tagen nach dem Rechnungsdatum per Überweisung oder per Scheck bezahlen.

Wenn Sie Ihre Rechnung bereits bezahlt haben, können Sie den Status der Zahlung im Azure-Portal auf der Seite „Rechnungen“ überprüfen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Überprüfen abgerechneter Transaktionen im Azure-Portal
> * Überprüfen der ausstehenden Gebühren zum Schätzen Ihrer nächsten Rechnung
> * Analysieren Ihrer Azure-Nutzungsgebühren

Erfahren Sie mehr über die Verwendung von Cost Management für Partner.

> [!div class="nextstepaction"]
> [Erste Schritte mit Cost Management für Partner](../costs/get-started-partners.md)
