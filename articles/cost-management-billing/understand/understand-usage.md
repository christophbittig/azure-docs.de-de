---
title: Grundlegendes zu detaillierter Nutzung und Gebühren
description: Hier erfahren Sie, wie Sie Ihre Datei zu detaillierter Nutzung und Gebühren lesen und verstehen. Zeigen Sie eine Liste der in der Datei verwendeten Begriffe und Beschreibungen an.
author: bandersmsft
ms.reviewer: micflan
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: banders
ms.openlocfilehash: ae265fd760a20c1e79032f2a7d0d97a459dd5c69
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999452"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Grundlegendes zu den Bedingungen in der Datei für die Azure-Nutzung und -Gebühren

Die Datei zu detaillierter Nutzung und Gebühren enthält die tägliche bewertete Nutzung basierend auf vereinbarten Tarifen, Einkäufen (z.B. Reservierungen, Marketplace-Gebühren) und Rückerstattungen für den angegebenen Zeitraum.
Die Gebühren beinhalten keine Gutschriften, Steuern oder andere Gebühren und Rabatte.
Die folgende Tabelle zeigt, welche Gebühren für jeden Kontotyp enthalten sind.

Kontotyp | Azure-Nutzung | Marketplace-Nutzung | Einkäufe | Rückerstattungen
--- | --- | --- | --- | ---
Enterprise Agreement (EA) | Ja | Ja | Ja | Nein
Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) | Ja | Ja | Ja | Ja
Nutzungsbasierte Bezahlung (Pay-as-you-go, PAYG) | Ja | Ja | Nein | Nein

Weitere Informationen zu Marketplace-Bestellungen (auch als externe Dienstleistungen bezeichnet) finden Sie unter [Grundlegendes zu Azure-Gebühren für externe Dienste](understand-azure-marketplace-charges.md).

Anweisungen für den Download finden Sie unter [Abrufen von Azure-Rechnungen und täglichen Nutzungsdaten](../manage/download-azure-invoice-daily-usage-date.md).
Sie können Ihre Nutzungs- und Gebühren-CSV-Datei in Microsoft Excel oder einer anderen Tabellenkalkulationsanwendung öffnen.

## <a name="list-of-terms-and-descriptions"></a>Liste der Begriffe und Beschreibungen

In der folgenden Tabelle werden die wichtigen Begriffe in der neuesten Version der Datei für die Azure-Nutzung und -Gebühren erläutert.
Die Liste umfasst Konten für nutzungsbasierte Bezahlung (Pay-as-you-go, PAYG), Enterprise Agreement (EA) und Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA).

Begriff | Kontotyp | Beschreibung
--- | --- | ---
AccountName | EA, PAYG | Anzeigename des EA-Registrierungs- oder PAYG-Abrechnungskontos.
AccountOwnerId<sup>1</sup> | EA, PAYG | Eindeutiger Bezeichner des EA-Registrierungs- oder PAYG-Abrechnungskontos.
AdditionalInfo | All | Dienstspezifische Metadaten. Dies kann beispielsweise ein Imagetyp für einen virtuellen Computer sein.
BillingAccountId<sup>1</sup> | All | Eindeutiger Bezeichner für das Stammabrechnungskonto.
BillingAccountName | All | Name des Abrechnungskontos.
BillingCurrency | All | Dem Abrechnungskonto zugeordnete Währung.
BillingPeriod | EA, PAYG | Abrechnungszeitraum der Gebühren.
BillingPeriodEndDate | All | Enddatum des Abrechnungszeitraums.
BillingPeriodStartDate | All | Startdatum des Abrechnungszeitraums.
BillingProfileId<sup>1</sup> | All | Eindeutiger Bezeichner des EA-Registrierungs-, PAYG-Abonnement-, MCA-Abrechnungsprofil- oder konsolidierten AWS-Kontos.
BillingProfileName | All | Name des EA-Registrierungs-, PAYG-Abonnement-, MCA-Abrechnungsprofil- oder konsolidierten AWS-Kontos.
ChargeType | All | Gibt an, ob die Gebühren die Nutzung (**Nutzung**), einen Kauf (**Einkauf**) oder eine Erstattung (**Rückerstattung**) darstellt.
ConsumedService | All | Name des Diensts, dem die Gebühren zugeordnet sind.
CostCenter<sup>1</sup> | EA, MCA | Die Kostenstelle, die im Abonnement für die Nachverfolgung der Kosten festgelegt wurde (nur in offenen Abrechnungszeiträumen für MCA-Konten verfügbar).
Kosten | EA, PAYG | Siehe „CostInBillingCurrency“.
CostInBillingCurrency | MCA | Kosten der Gebühren in der Abrechnungswährung vor Gutschriften oder Steuern.
CostInPricingCurrency | MCA | Kosten der Gebühren in der Preiswährung vor Gutschriften oder Steuern.
Währung | EA, PAYG | Siehe „BillingCurrency“.
Date<sup>1</sup> | All | Verbrauchs- oder Kaufdatum der Gebühren.
EffectivePrice | All | Gemischter Einzelpreis für den Zeitraum. Mit den gemischten Preisen wird aus allen Einzelpreisschwankungen (wie z.B. abgestuftem Tiering) der Durchschnitt ermittelt, wodurch der Preis sinkt, da die Menge im Laufe der Zeit wächst.
ExchangeRateDate | MCA | Datum, an dem der Wechselkurs festgelegt wurde.
ExchangeRatePricingToBilling | MCA | Der für die Umrechnung der Kosten aus der Preiswährung in die Abrechnungswährung verwendete Wechselkurs.
Häufigkeit | All | Gibt an, ob eine Gebühr sich voraussichtlich wiederholt. Gebühren fallen entweder einmal (**OneTime**), wiederholt monatlich oder jährlich (**Recurring**) oder basierend auf der Nutzung (**UsageBased**) an.
InvoiceId | PAYG, MCA | Die eindeutige Dokument-ID, die in der PDF-Datei für die Rechnung aufgeführt ist.
InvoiceSection | MCA | Siehe „InvoiceSectionName“.
InvoiceSectionId<sup>1</sup> | EA, MCA | Eindeutiger Bezeichner für EA-Abteilung oder MCA-Rechnungsabschnitt.
InvoiceSectionName | EA, MCA | Name für EA-Abteilung oder MCA-Rechnungsabschnitt.
IsAzureCreditEligible | All | Gibt an, ob die Gebühr mit einer Azure-Gutschrift bezahlt werden kann (Werte: True, False).
Standort | MCA | Standort des Rechenzentrums, in dem die Ressource ausgeführt wird.
MeterCategory | All | Name der Klassifizierungskategorie der Verbrauchseinheit. Beispiele: *Clouddienste* und *Netzwerk*.
MeterId<sup>1</sup> | All | Der eindeutige Bezeichner für die Verbrauchseinheit.
MeterName | All | Der Name der Verbrauchseinheit.
MeterRegion | All | Der Name des Standorts des Rechenzentrums für auf Basis des Standorts abgerechnete Dienste. Siehe „Location“.
MeterSubCategory | All | Name der Unterklassifizierungskategorie der Verbrauchseinheit.
OfferId<sup>1</sup> | All | Der Name des erworbenen Angebots.
PayGPrice | All | Einzelhandelspreis für die Ressource
PartNumber<sup>1</sup> | EA, PAYG | Der Bezeichner, der verwendet wird, um bestimmte Verbrauchseinheitspreise zu erhalten.
PlanName | EA, PAYG | Name des Marketplace-Plans.
PreviousInvoiceId | MCA | Verweis auf die ursprüngliche Rechnung, wenn es sich bei diesem Posten um eine Rückerstattung handelt.
PricingCurrency | MCA | Währung, die verwendet wird, wenn die Bewertung basierend auf vereinbarten Preisen erfolgt.
PricingModel | All | Ein Bezeichner, der angibt, wie die Verbrauchseinheit berechnet wird. (Werte: Bedarfsgesteuert, Reservierung, Spot)
Produkt | All | Der Name des Produkts.
ProductId<sup>1</sup> | MCA | Der eindeutige Bezeichner für das Produkt.
ProductOrderId | All | Der eindeutige Bezeichner für die Produktbestellung.
ProductOrderName | All | Der eindeutige Name für die Produktbestellung.
PublisherName | All | Herausgeber für Marketplace-Dienste.
PublisherType | All | Typ des Herausgebers. Die Kosten werden nach Microsoft/Azure-, Marketplace- und AWS-Kosten unterteilt.  Werte sind `Microsoft` für MCA-Konten und `Azure` für EA-Konten und Konten mit nutzungsbasierter Bezahlung.|
Menge | All | Die Anzahl der erworbenen oder genutzten Einheiten.
ReservationId | EA, MCA | Der eindeutige Bezeichner für die erworbene Reservierungsinstanz.
ReservationName | EA, MCA | Der Name der erworbenen Reservierungsinstanz.
ResourceGroup | All | Name der [Ressourcengruppe](../../azure-resource-manager/management/overview.md), in der sich die Ressource befindet. Nicht alle Gebühren werden durch Ressourcen verursacht, die in Ressourcengruppen bereitgestellt werden. Gebühren, die keiner Ressourcengruppe zugeordnet sind, werden als 0 (null)/leer, **Andere** oder **Nicht zutreffend** angezeigt.
ResourceId<sup>1</sup> | All | Der eindeutige Bezeichner der [Azure Resource Manager](/rest/api/resources/resources)-Ressource.
ResourceLocation | All | Standort des Rechenzentrums, in dem die Ressource ausgeführt wird. Siehe „Location“.
Ressourcenname | EA, PAYG | Der Name der Ressource. Nicht alle Gebühren werden durch bereitgestellte Ressourcen verursacht. Gebühren, die keinem Ressourcentyp zugeordnet sind, werden als 0 (null)/leer, **Andere** oder **Nicht zutreffend** angezeigt.
ResourceType | MCA | Der Ressourceninstanztyp. Nicht alle Gebühren werden durch bereitgestellte Ressourcen verursacht. Gebühren, die keinem Ressourcentyp zugeordnet sind, werden als 0 (null)/leer, **Andere** oder **Nicht zutreffend** angezeigt.
ServiceFamily | MCA | Die Dienstfamilie, zu der der Dienst gehört.
ServiceInfo1 | All | Dienstspezifische Metadaten.
ServiceInfo2 | All | Ein Legacyfeld mit optionalen dienstspezifischen Metadaten.
ServicePeriodEndDate | MCA | Das Enddatum des Bewertungszeitraums, durch den die Preise für den genutzten oder erworbenen Dienst festgelegt wurden.
ServicePeriodStartDate | MCA | Das Startdatum des Bewertungszeitraums, durch den die Preise für den genutzten oder erworbenen Dienst festgelegt wurden.
SubscriptionId<sup>1</sup> | All | Der eindeutige Bezeichner für das Azure-Abonnement.
SubscriptionName | All | Der Name des Azure-Abonnements.
Tags<sup>1</sup> | All | Tags, die der Ressource zugewiesen werden. Umfasst keine Ressourcengruppen-Tags. Kann verwendet werden, um die Kosten für interne verbrauchsbasierte Kostenzuteilung zu gruppieren oder zu verteilen. Weitere Informationen finden Sie unter [Organisieren von Azure-Ressourcen mit Tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Begriff | All | Zeigt den Zeitraum für die Gültigkeit des Angebots an. Beispiel: Bei reservierten Instanzen werden 12 Monate als „Zeitraum“ angezeigt. Für einmalige Käufe oder wiederkehrende Käufe beträgt „Zeitraum“ 1 Monat (SaaS, Marketplace-Support). Dies gilt nicht für den Azure-Verbrauch.
UnitOfMeasure | All | Die Abrechnungsmaßeinheiten für den Dienst. Computedienste werden beispielsweise pro Stunde abgerechnet.
UnitPrice | EA, PAYG | Der Preis pro Einheit für die Gebühren.

_<sup>**1**</sup> Felder zum Erstellen einer eindeutigen ID für einen einzelnen Kostendatensatz_

Beachten Sie, dass einige Felder bei den verschiedenen Kontotypen in Groß-/Kleinschreibung und Abstand abweichen können.
Ältere Versionen von Pay-as-you-go-Nutzungsdateien verfügen über separate Abschnitte für Abrechnung und tägliche Nutzung.

Beachten Sie für Kunden mit einer MCA-Vereinbarung, dass es Änderungen an den Werten für das Feld `PublisherType` gibt. Weitere Informationen finden Sie unter [Änderungen an Werten für den Herausgebertyp](../costs/group-filter.md#publisher-type-value-changes).

### <a name="list-of-terms-from-older-apis"></a>Liste der Begriffe aus älteren APIs
In der folgenden Tabelle werden die in älteren APIs verwendeten Begriffe den neuen Begriffen zugeordnet. Diese Beschreibungen finden Sie in der obigen Tabelle.

Alter Begriff | Neuer Begriff
--- | ---
ConsumedQuantity | Menge
IncludedQuantity | –
InstanceId | resourceId
Rate | EffectivePrice
Einheit | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date

## <a name="ensure-charges-are-correct"></a>Sicherstellen, dass Gebühren richtig sind

Weitere Informationen über detaillierte Nutzung und Gebühren finden Sie in den Grundlegenden Informationen zur Rechnung nach [nutzungsbasierter Bezahlung (Pay-as-you-go)](review-individual-bill.md) oder [Microsoft-Kundenvereinbarung](review-customer-agreement-bill.md).

## <a name="unexpected-usage-or-charges"></a>Unerwartete Nutzung oder Gebühren

Wenn eine nicht erkannte Nutzung oder nicht erkannte Gebühren vorliegen, können Sie verschiedene Aktionen ausführen, um die Ursache dafür zu ermitteln:

- Überprüfen der Rechnung mit Gebühren für die Ressource
- Überprüfen der berechneten Gebühren in der Kostenanalyse
- Ermitteln und Einbeziehen der für die Ressource zuständigen Personen
- Analysieren der Überwachungsprotokolle
- Analysieren von Benutzerberechtigungen für den übergeordneten Bereich der Ressource
- Erstellen einer [Azure-Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458) zum Ermitteln der Gebühren

Weitere Informationen finden Sie unter [Analysieren unerwarteter Gebühren](analyze-unexpected-charges.md).

Beachten Sie, dass die meisten Benutzeraktionen von Azure nicht protokolliert werden. Stattdessen protokolliert Microsoft die Ressourcennutzung für die Abrechnung. Wenn Sie eine Nutzungsspitze in der Vergangenheit erkennen und die Protokollierung nicht aktiviert war, kann Microsoft die Ursache nicht ermitteln. Aktivieren Sie die Protokollierung für den Dienst, für den Sie die erhöhte Nutzung anzeigen möchten, damit das entsprechende technische Team Ihnen bei dem Problem behilflich sein kann.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](download-azure-invoice.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Nutzung und -Gebühren](download-azure-daily-usage.md)