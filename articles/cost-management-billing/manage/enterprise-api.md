---
title: Azure-Abrechnungs-APIs für Unternehmen
description: Erhalten Sie Informationen zu den Berichterstellungs-APIs, mit denen Azure-Unternehmenskunden Verbrauchsdaten programmgesteuert abrufen können.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 5f5842f264236015d9ecf50906af62505d8677e0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454122"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Überblick über Berichterstellungs-APIs für Unternehmenskunden

> [!Note]
> Die Azure-Abrechnungs-APIs für die Enterprise-Berichtstellung werden von Microsoft nicht mehr aktualisiert. Verwenden Sie stattdessen die [Azure-Nutzungs](/rest/api/consumption)-APIs.

Die Berichterstellungs-APIs ermöglichen es Azure-Unternehmenskunden, die Verbrauchs- und Abrechnungsdaten in bevorzugte Datenanalysetools abzurufen. Enterprise-Kunden haben ein [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) mit Azure unterzeichnet, um eine ausgehandelte Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) zu leisten und Zugang zu benutzerdefinierten Preisen für Azure-Ressourcen zu erhalten.

Alle Datums- und Uhrzeitparameter, die für APIs benötigt werden, müssen als kombinierte UTC-Werte (Coordinated Universal Time) angegeben werden. Die von APIs zurückgegebenen Werte werden im UTC-Format angezeigt.

## <a name="enabling-data-access-to-the-api"></a>Aktivieren des Datenzugriffs für die API
* **Generieren oder Abrufen des API-Schlüssels**: Melden Sie sich beim Enterprise Portal an, und navigieren Sie zu „Berichte“ > „Nutzung herunterladen“ > „API-Zugriffsschlüssel“, um den API-Schlüssel zu generieren oder abzurufen.
* **Übergeben von Schlüsseln in der API**: Der API-Schlüssel muss für jeden Aufruf für die Authentifizierung und Autorisierung übergeben werden. Die folgende Eigenschaft muss für die HTTP-Header vorhanden sein

|Anforderungsheaderschlüssel | Wert|
|-|-|
|Authorization| Geben Sie den Wert im folgenden Format an: **bearer {API_SCHLÜSSEL}** <br/> Beispiel: bearer eyr....09|

## <a name="consumption-based-apis"></a>Nutzungsbasierte APIs
Ein Swagger-Endpunkt ist [hier](https://consumption.azure.com/swagger/ui/index) für die unten beschriebenen APIs verfügbar, der eine einfache Introspektion der API und die Möglichkeit zum Generieren von Client-SDKs mithilfe von [AutoRest](https://github.com/Azure/AutoRest) oder [Swagger CodeGen](https://swagger.io/swagger-codegen/) ermöglichen sollte. Seit dem 1. Mai 2014 sind Daten über diese API verfügbar.

* **Bilanz und Zusammenfassung**: Die [API für Bilanz und Zusammenfassung](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) bietet eine monatliche Übersicht über Informationen zu Bilanzen, neuen Käufen, Gebühren für den Azure Marketplace, Korrekturen und Überschreitungsgebühren.

* **Verwendungsdetails**: Die [API für Verwendungsdetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) bietet eine tägliche Aufschlüsselung der verbrauchten Mengen und durch eine Registrierung anfallenden geschätzten Kosten. Das Ergebnis umfasst auch Informationen zu Instanzen, Verbrauchseinheiten und Abteilungen. Die API kann nach Abrechnungszeitraum oder einem angegebenen Start- und Enddatum abgefragt werden.

* **Marketplace Store-Gebühren**: Die [API für Marketplace Store-Gebühren](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) gibt die Aufschlüsselung der nutzungsbasierten Marketplace-Gebühren pro Tag für den angegebenen Abrechnungszeitraum oder gemäß angegebener Start- und Enddaten zurück (einmalige Gebühren sind nicht enthalten).

* **Preisblatt**: Die [Preisblatt-API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) stellt die zutreffende Rate für jede Verbrauchseinheit für den angegebenen Registrierungs- und Abrechnungszeitraum bereit.

* **Details der reservierten Instanz**: Die [API zur Nutzung reservierter Instanzen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) gibt die Nutzung der Käufe reservierter Instanzen zurück. Die [API für Gebühren für reservierte Instanzen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) zeigt die vorgenommenen Transaktionsabrechnungen an.

## <a name="data-freshness"></a>Datenaktualität
In der Antwort jeder der oben genannten APIs werden ETags zurückgegeben. Eine Änderung der ETags weist darauf hin, dass die Daten aktualisiert wurden.  Übergeben Sie in nachfolgenden Aufrufen der gleichen API mit den gleichen Parametern das erfasste ETag mit dem Schlüssel „If-None-Match“ im Header der HTTP-Anforderung. Wenn die Daten nicht weiter aktualisiert wurden, lautet der Statuscode der Antwort „NotModified“, und es werden keine Daten zurückgegeben. Bei jeder ETag-Änderung gibt die API das vollständige Dataset für den angeforderten Zeitraum zurück.

## <a name="helper-apis"></a>Hilfs-APIs
 **Liste der Abrechnungszeiträume**: Die [API für Abrechnungszeiträume](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) gibt eine Liste von Abrechnungszeiträumen zurück, die Verbrauchsdaten für die angegebene Registrierung in umgekehrter chronologischer Reihenfolge enthalten. Jeder Zeitraum enthält eine Eigenschaft, die auf die API-Route für die vier Datensätze verweist: BalanceSummary, UsageDetails, Marketplace Charges und PriceSheet.


## <a name="api-response-codes"></a>API-Antwortcodes   
|Statuscode der Antwort|`Message`|BESCHREIBUNG|
|-|-|-|
|200| OK|Kein Fehler|
|400| Ungültige Anforderung| Ungültige Parameter – Datumsbereiche, EA-Nummern usw.|
|401| Nicht autorisiert| API-Schlüssel nicht gefunden, ungültig, abgelaufen usw.|
|404| Nicht verfügbar| Berichtsendpunkt nicht gefunden|
|429 | TooManyRequests | Die Anforderung wurde gedrosselt. Wiederholen Sie den Vorgang, nachdem Sie die im Header <code>x-ms-ratelimit-microsoft.consumption-retry-after</code> angegebene Zeit abgewartet haben.|
|500| Serverfehler| Unerwarteter Fehler beim Verarbeiten der Anforderung|
| 503 | ServiceUnavailable | Der Dienst ist vorübergehend nicht verfügbar. Wiederholen Sie den Vorgang, nachdem Sie die im Header <code>Retry-After</code> angegebene Zeit abgewartet haben.|
