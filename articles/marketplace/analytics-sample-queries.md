---
title: Beispielabfragen für die programmgesteuerte Analyse
description: Verwenden Sie diese Beispielabfragen, um programmgesteuert auf Analysedaten zum kommerziellen Marketplace von Microsoft zuzugreifen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 8/06/2021
ms.openlocfilehash: ac276f495ac2a5eb3bee6ac0f682185cf8424611
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355882"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Beispielabfragen für die programmgesteuerte Analyse

Dieser Artikel enthält Beispielabfragen zu den Berichten über Bestellungen, Nutzung und Kunden des kommerziellen Marketplace von Microsoft. Sie können diese Abfragen verwenden, indem Sie den API-Endpunkt [Berichtsabfrage erstellen](analytics-programmatic-access.md#create-report-query-api) aufrufen. Falls erforderlich, kann die [Berichtsabfrage erstellen](analytics-programmatic-access.md#create-report-query-api)-API geändert werden, um weitere Spalten hinzuzufügen, den Berechnungszeitraum anzupassen und Filterbedingungen hinzuzufügen. Die unterstützten Zeiträume sind sechs Monate (6M), 12 Monate (12M) und der benutzerdefinierte Zeitraum.

Ausführliche Informationen zu den Spaltennamen, Attributen und Beschreibungen finden Sie in den folgenden Tabellen:

- [Tabelle der Kundendetails](customer-dashboard.md#customer-details-table)
- [Tabelle „Auftragsdetails“](orders-dashboard.md#orders-details-table)
- [Tabelle „Nutzungsdetails“](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Abfragen des Berichts „Kunden“

Diese Beispielabfragen gelten für den Bericht „Kunden“.

| **Abfragebeschreibung** | **Beispielabfrage** |
| --- | --- |
| Aktive Kunden des Partners bis zum gewählten Datum | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Abgewanderte Kunden des Partners bis zum gewählten Datum | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Liste der neuen Kunden aus einem bestimmten geografischen Gebiet in den letzten sechs Monaten | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Abfragen des Berichts „Nutzung“

Diese Beispielabfragen gelten für den Bericht „Nutzung“.

| **Abfragebeschreibung** | **Beispielabfrage** |
| --- | --- |
| Normalisierte VM-Nutzung (Virtual Machine, virtueller Computer) für den Marketplace-Lizenztyp „Abrechnung über Azure“ für die letzten 6M | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Tatsächliche VM-Nutzung für den Marketplace-Lizenztyp „Abrechnung über Azure“ für die letzten 12M | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| Normalisierte VM-Nutzung für den Marketplace-Lizenztyp „Bring-your-own-License“ (BYOL) der letzten 6 M. | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Tatsächliche VM-Nutzung für den Marketplace-Lizenztyp „Bring-your-own-License“ (BYOL) der letzten 6 M. | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Basierend auf dem Nutzungsdatum, der täglichen gesamten normalisierten Nutzung und „Geschätzten erweiterten Gebühren (PC/CC)“ für kostenpflichtige Pläne für den letzten Monat | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| Basierend auf dem Nutzungsdatum, der täglichen gesamten tatsächlichen Nutzung und „Geschätzten erweiterten Gebühren (PC/CC)“ für kostenpflichtige Pläne für den letzten Monat | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| Für einen bestimmten Angebotsnamen normalisierte VM-Nutzung für den Marketplace-Lizenztyp „Abrechnung über Azure“ für die letzten 6M | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Für einen bestimmten Angebotsnamen gemessene Nutzung für die letzten 6M | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Abfragen des Berichts „Bestellungen“

Diese Beispielabfragen gelten für den Bericht „Bestellungen“.

| **Abfragebeschreibung** | **Beispielabfrage** |
| --- | --- |
| Bestellungenbericht für den Azure-Lizenztyp „Enterprise“ für die letzten 6M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Bestellungenbericht für den Azure-Lizenztyp „Nutzungsbasierte Bezahlung“ für die letzten 6M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Bestellungenbericht für einen bestimmten Angebotsnamen für die letzten 6M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Bestellungenbericht für aktive Bestellungen für die letzten 6M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Bestellungenbericht für stornierte Bestellungen für die letzten 6M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
| Bericht zu den Bestellungen mit Laufzeitbeginn, Laufzeitende, geschätzten Kosten und Währung | `SELECT OrderId, TermStartId, TermEndId, estimatedcharges from ISVOrderV2 WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Bericht zu den Bestellungen für aktive Testversionen für die letzten 6 Monate | `SELECT OrderId from ISVOrderV2 WHERE OrderStatus = ‘Active’ and HasTrial = ‘True’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Nächste Schritte

- [APIs für den Zugriff auf kommerzielle Marketplace-Analysedaten](analytics-available-apis.md)
