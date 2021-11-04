---
title: Rückbuchen von Azure-Reservierungskosten
description: Erfahren Sie, wie Sie Azure-Reservierungskosten für die Rückbuchung anzeigen.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: dfe5a6e1dd95e3f5aa93499eee9c8b6b609fd502
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425821"
---
# <a name="charge-back-azure-reservation-costs"></a>Rückbuchen von Azure-Reservierungskosten

Abrechnungsleser für Enterprise Agreement und Microsoft-Kundenvereinbarung können amortisierte Kostendaten für Reservierungen anzeigen. Sie können die Kostendaten verwenden, um den Geldwert für ein Abonnement, eine Ressourcengruppe, eine Ressource oder ein Tag an ihre Partner zurückzubuchen. Bei amortisierten Daten entspricht der effektive Preis den anteiligen Reservierungskosten pro Stunde. Die Kosten sind die Gesamtkosten der Reservierungsnutzung durch die Ressource an diesem Tag.

Benutzer mit einem einzelnen Abonnement können die amortisierten Kostendaten aus ihrer Nutzungsdatei abrufen. Wenn eine Ressource einen Reservierungsrabatt erhält, enthält der Abschnitt *AdditionalInfo* in der Nutzungsdatei die Reservierungsdetails. Weitere Informationen finden Sie unter [Herunterladen der Nutzung aus dem Azure-Portal](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Anzeigen von Reservierungsnutzungsdaten für Showback und Rückbuchungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zu **Kostenverwaltung + Abrechnung**. 
3. Wählen Sie im linken Navigationsbereich die Option **Kostenanalyse** aus. 
4. Wählen Sie unter **Tatsächliche Kosten** die Metrik **Amortisierte Kosten** aus.
5. Um zu sehen, welche Ressourcen von einer Reservierung verwendet wurden, wenden Sie einen Filter für **Reservierung** an, und wählen Sie dann Reservierungen aus.
6. Legen Sie die **Granularität** auf **Monatlich** oder **Täglich** fest.
7. Legen Sie den Diagrammtyp auf **Tabelle** fest.
8. Legen Sie die Option **Gruppieren nach** auf **Ressource** fest.

[![Beispiel für Ressourcenkosten einer Reservierung, die Sie für Rückbuchungen verwenden können](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Im folgenden Video wird gezeigt, wie Sie die Kosten für die Reservierungsnutzung auf Abonnement-, Ressourcengruppen- und Ressourcenebene im Azure-Portal anzeigen.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>Abrufen der Daten für Showback und Rückbuchungen
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zu **Kostenverwaltung + Abrechnung**. 
3. Wählen Sie im linken Navigationsbereich die Option **Exportieren** aus. 
4. Klicken Sie auf die Schaltfläche **Hinzufügen**.
5. Wählen Sie „Amortisierte Kosten“ als Metrikschaltfläche aus, und richten Sie Ihren Export ein.

Beim effektiven Preis für die Nutzung mit dem Reservierungsrabatt handelt es sich um die anteiligen Kosten der Reservierung. (Er ist also nicht Null.) Dadurch können Sie den monetären Wert der Reservierungsnutzung nach Abonnement, Ressourcengruppe oder Ressource ermitteln und die Reservierungsnutzung bei Bedarf intern verrechnen. Das Dataset enthält außerdem nicht in Anspruch genommene Reservierungsstunden. 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Abrufen von Azure-Verbrauchs- und Reservierungsnutzungsdaten mittels API

Sie können die Daten mithilfe der API abrufen oder aus dem Azure-Portal herunterladen.

Sie rufen die [Nutzungsdetails-API](/rest/api/consumption/usagedetails/list) auf, um die neuen Daten zu erhalten. Detailinformationen zur Terminologie finden Sie unter [Nutzungsbedingungen](../understand/understand-usage.md).

Hier sehen Sie einen Beispielaufruf der Nutzungsdetails-API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Weitere Informationen zu {enrollmentId} und {billingPeriodId} finden Sie im API-Artikel [Nutzungsdetails – Liste](/rest/api/consumption/usagedetails/list).

Die Informationen in der folgenden Tabelle zu Metrik und Filter können bei der Behebung häufiger Reservierungsprobleme helfen.

| **Typ der API-Daten** | API-Aufrufaktion |
| --- | --- |
| **Alle Gebühren (Nutzung und Käufe)** | {metric} durch ActualCost ersetzen |
| **Nutzung, für die ein Reservierungsrabatt gewährt wurde** | {metric} durch ActualCost ersetzen<br><br>{filter} ersetzten durch: properties/reservationId%20ne%20 |
| **Nutzung, für die kein Reservierungsrabatt gewährt wurde** | {metric} durch ActualCost ersetzen<br><br>{filter} ersetzten durch: properties/reservationId%20eq%20 |
| **Amortisierte Gebühren (Nutzung und Käufe)** | {metric} durch AmortizedCost ersetzen |
| **Bericht über nicht genutzte Reservierungen** | {metric} durch AmortizedCost ersetzen<br><br>{filter} ersetzen durch: properties/ChargeType%20eq%20'UnusedReservation' |
| **Reservierungseinkäufe** | {metric} durch ActualCost ersetzen<br><br>{filter} ersetzten durch: properties/ChargeType%20eq%20'Purchase'  |
| **Rückerstattungen** | {metric} durch ActualCost ersetzen<br><br>{filter} ersetzten durch: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Herunterladen der CSV-Nutzungsdatei mit neuen Daten

Als EA-Administrator können Sie die CSV-Datei, die neue Nutzungsdaten enthält, aus dem Azure-Portal herunterladen. Diese Daten sind über das EA-Portal (ea.azure.com) nicht verfügbar. Sie müssen die Nutzungsdatei vom Azure-Portal (portal.azure.com) herunterladen, um die neuen Daten anzuzeigen.

Navigieren Sie im Azure-Portal zu [Kostenverwaltung + Abrechnung](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Wählen sie das Abrechnungskonto aus.
2. Klicken Sie auf **Nutzung + Gebühren**.
3. Klicken Sie auf **Download**.  
![Ein Beispiel, das zeigt, wo Sie die CSV-Datei mit den Nutzungsdaten im Azure-Portal herunterladen können](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Wählen Sie unter **Nutzungsdetails** die Option für **amortisierte Nutzungsdaten** aus.

Die CSV-Dateien, die Sie herunterladen, enthalten Ist-Kosten und amortisierte Kosten.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu Nutzungsdaten von Azure Reservations finden Sie im folgenden Artikel:
  - [Abrufen der Reservierungskosten und -nutzung für Enterprise Agreement und Microsoft-Kundenvereinbarung](understand-reserved-instance-usage-ea.md)
 
