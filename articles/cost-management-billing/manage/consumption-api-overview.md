---
title: 'Azure-Nutzungs-API: Übersicht'
description: Erfahren Sie, wie Azure-Nutzungs-APIs Ihnen einen programmgesteuerten Zugriff auf Kosten- und Nutzungsdaten zu Ihren Azure-Ressourcen ermöglichen.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: reference
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 6efb4a738314da08be7a52a71b3a91c06f99b5d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128678704"
---
# <a name="azure-consumption-api-overview"></a>Azure-Nutzungs-API: Übersicht

Die Azure-Nutzungs-APIs ermöglichen Ihnen einen programmgesteuerten Zugriff auf Kosten- und Nutzungsdaten zu Ihren Azure-Ressourcen. Diese APIs unterstützen derzeit nur Abonnements des Typs „Enterprise Agreement“ und „Web Direct“ (mit wenigen Ausnahmen). Die APIs werden ständig aktualisiert, um andere Typen von Azure-Abonnements zu unterstützen.

Azure-Nutzungs-APIs bieten Zugriff auf Folgendes:
- Enterprise Agreement- und Web Direct-Kunden
    - Nutzungsdetails
    - Marketplace-Gebühren
    - Reservierungsempfehlungen
    - Reservierungsdetails
    - Reservierungszusammenfassungen
- Nur für Enterprise Agreement-Kunden
    - Preisblatt
    - Budgets
    - Bilanzen

## <a name="usage-details-api"></a>Nutzungsdetails-API

Verwenden Sie die Nutzungsdetails-API zum Abrufen von Daten zu Kosten und Nutzung aller Azure-Ressourcen von Erstanbietern. Die Informationen liegen in Form von Nutzungsdetail-Datensätzen vor, die zurzeit einmal pro Verbrauchszähler, Ressource und Tag ausgegeben werden. Diese Informationen können verwendet werden, um Kosten für alle Ressourcen zu addieren oder die Kosten/Nutzung für bestimmte Ressourcen zu untersuchen.

Die API umfasst:

-    **Verbrauchsdaten auf Zählerebene**: Daten zu Nutzungskosten, dem Zähler, der die Gebühr erzeugt, und der Azure-Ressource, auf die sich die Gebühr bezieht, können eingesehen werden. Alle Nutzungsdetail-Datensätze sind einem täglichen Bucket zugeordnet.
-    **Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)** : Sie können Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com), mithilfe der [Azure CLI](../../role-based-access-control/role-assignments-cli.md) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Abrechnungsleser“, „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
-    **Filterung**: Reduzieren Sie Ihr API-Ergebnis mit den folgenden Filtern auf einen kleineren Satz von Nutzungsdetail-Datensätzen:
    - Start der Nutzung/Ende der Nutzung
    - Ressourcengruppe
    - Ressourcenname
-    **Datenaggregation**: Verwenden Sie OData, um Ausdrücke auf aggregierte Details der Verwendung mithilfe von Tags oder Filtereigenschaften anzuwenden.
-    **Nutzung für verschiedene Angebotstypen**: Nutzungsdetailinformationen sind zurzeit für Enterprise Agreement- und Web Direct-Kunden verfügbar.

Weitere Informationen finden Sie in den technischen Angaben zur [Nutzungsdetails-API](/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>API für Marketplace-Gebühren

Verwenden Sie die API für Marketplace-Gebühren, um Kosten- und Nutzungsdaten für alle Marketplace-Ressourcen abzurufen (Azure-Angebote von Drittanbietern). Diese Daten können verwendet werden, um Kosten für alle Marketplace-Ressourcen zu addieren oder die Kosten/Nutzung für bestimmte Ressourcen zu untersuchen.

Die API umfasst:

-    **Verbrauchsdaten auf Zählerebene**: Daten zu Marketplace-Nutzungskosten, dem Zähler, der die Gebühr erzeugt, und der Azure-Ressource, auf die sich die Gebühr bezieht, können eingesehen werden. Alle Nutzungsdetail-Datensätze sind einem täglichen Bucket zugeordnet.
-    **Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)** : Sie können Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com), mithilfe der [Azure CLI](../../role-based-access-control/role-assignments-cli.md) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Abrechnungsleser“, „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
-    **Filterung**: Reduzieren Sie Ihr API-Ergebnis mit den folgenden Filtern auf einen kleineren Satz von Marketplace-Datensätzen:
    - Start der Nutzung/Ende der Nutzung
    - Ressourcengruppe
    - Ressourcenname
-    **Nutzung für verschiedene Angebotstypen**: Marketplace-Informationen sind zurzeit für Enterprise Agreement- und Web Direct-Kunden verfügbar.

Weitere Informationen finden Sie in den technischen Angaben zur [API für Marketplace-Gebühren](/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>API für Bilanzen

Verwenden Sie die API für Bilanzen, um eine monatliche Übersicht über Informationen zu Bilanzen, neuen Käufen, Gebühren für den Azure Marketplace-Dienst, Korrekturen und Überschreitungsgebühren zu erhalten. Sie können diese Informationen für den aktuellen Abrechnungszeitraum oder einen beliebigen Zeitraum in der Vergangenheit abrufen. Unternehmen können diese Daten verwenden, um einen Vergleich mit manuell berechneten Summengebühren vorzunehmen. Diese API stellt keine ressourcenspezifischen Informationen und keine Aggregatansicht der Kosten bereit.

Die API umfasst:

-    **Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)** : Sie können Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com), mithilfe der [Azure CLI](../../role-based-access-control/role-assignments-cli.md) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Abrechnungsleser“, „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
-    **Nur für Enterprise Agreement-Kunden**: Diese API ist nur für EA-Kunden verfügbar.
    - Kunden benötigen für das Aufrufen dieser API die Berechtigungen eines Unternehmensadministrators.

Weitere Informationen finden Sie in den technischen Angaben zur [API für Bilanzen](/rest/api/consumption/balances).

## <a name="budgets-api"></a>API für Budgets

Enterprise Agreement-Kunden können diese API nutzen, um Kosten- oder Nutzungsbudgets für Ressourcen, Ressourcengruppen oder Abrechnungszähler zu erstellen. Sobald diese Informationen bestimmt wurden, können Sie Warnungen so konfigurieren, dass Sie benachrichtigt werden, wenn benutzerdefinierte Budgetschwellenwerte überschritten werden.

Die API umfasst:

-    **Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)** : Sie können Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com), mithilfe der [Azure CLI](../../role-based-access-control/role-assignments-cli.md) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Abrechnungsleser“, „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
-    **Nur für Enterprise Agreement-Kunden**: Diese API ist nur für EA-Kunden verfügbar.
-    **Konfigurierbare Benachrichtigungen**: Geben Sie Benutzer an, die benachrichtigt werden sollen, wenn das Budget überschritten wird.
-    **Auf Nutzung oder Kosten basierende Budgets**: Erstellen Sie Ihr Budget je nach Bedarf auf Basis des Verbrauchs oder der Kosten.
-    **Filterung**: Filtern Sie Ihr Budget mit den folgenden konfigurierbaren Filtern auf eine kleinere Teilmenge von Ressourcen.
    - Ressourcengruppe
    - Ressourcenname
    - Zähler
-    **Konfigurierbare Budgetzeiträume**: Geben Sie an, wie oft das Budget zurückgesetzt werden soll und wie lange es gültig ist.

Weitere Informationen finden Sie in den technischen Angaben zur [API für Budgets](/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>API für Reservierungsempfehlungen

Verwenden Sie diese API, um Empfehlungen für den Kauf reservierter VM-Instanzen zu erhalten. Die Empfehlungen sind so konzipiert, dass die Kunden die zu erwartenden Kosteneinsparungen und Kaufbeträge analysieren können.

Die API umfasst:

-    **Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)** : Sie können Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com), mithilfe der [Azure CLI](../../role-based-access-control/role-assignments-cli.md) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Abrechnungsleser“, „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
-    **Filterung**: Passen Sie Ihre Empfehlungsergebnisse mit den folgenden Filtern an:
    - `Scope`
    - Rückblickperiode
-    **Reservierungsinformationen für verschiedene Angebotstypen**: Reservierungsinformationen sind zurzeit für Enterprise Agreement- und Web Direct-Kunden verfügbar.

Weitere Informationen finden Sie in den technischen Angaben zur [API für Reservierungsempfehlungen](/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>API für Reservierungsdetails

Verwenden Sie die API für Reservierungsdetails, um Informationen zu bereits erworbenen VM-Reservierungen anzuzeigen, z.B. wie viel Nutzung reserviert wurde und wie viel tatsächlich genutzt wird. Sie können die Daten auf VM-Ebene im Detail anzeigen.

Die API umfasst:

-    **Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)** : Sie können Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com), mithilfe der [Azure CLI](../../role-based-access-control/role-assignments-cli.md) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Abrechnungsleser“, „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
-    **Filterung**: Reduzieren Sie Ihr API-Ergebnis mit den folgenden Filtern auf einen kleineren Satz von Reservierungen:
    - Datumsbereich
-    **Reservierungsinformationen für verschiedene Angebotstypen**: Reservierungsinformationen sind zurzeit für Enterprise Agreement- und Web Direct-Kunden verfügbar.

Weitere Informationen finden Sie in den technischen Angaben zur [API für Reservierungsdetails](/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>API für Reservierungszusammenfassungen

Verwenden Sie die API für Reservierungszusammenfassungen, um aggregierte Informationen zu zuvor erworbenen VM-Reservierungen anzuzeigen, z.B. wie viel Nutzung reserviert wurde und wie viel tatsächlich im Aggregat verwendet wird.

Die API umfasst:

-    **Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)** : Sie können Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com), mithilfe der [Azure CLI](../../role-based-access-control/role-assignments-cli.md) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Abrechnungsleser“, „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
-    **Filterung**: Passen Sie Ihre Ergebnisse an, wenn Sie das Tagesintervall mit dem folgenden Filter verwenden:
    - Usage Date
-    **Reservierungsinformationen für verschiedene Angebotstypen**: Reservierungsinformationen sind zurzeit für Enterprise Agreement- und Web Direct-Kunden verfügbar.
-    **Täglich oder monatliche Aggregationen**: Aufrufer können angeben, ob sie ihre Reservierungszusammenfassungsdaten im Tages- oder Monatsintervall wünschen.

Weitere Informationen finden Sie in den technischen Angaben zur [API für Reservierungszusammenfassungen](/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Preisblatt-API
Enterprise-Kunden können diese API nutzen, um ihre benutzerdefinierten Preise für alle Zähler abzurufen. Unternehmen können diese Daten in Kombination mit Nutzungsdetails und Marketplace-Nutzungsinformationen verwenden, um Kostenberechnungen anhand von Nutzungs- und Marketplace-Daten auszuführen.

Die API umfasst:

-    **Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)** : Sie können Zugriffsrichtlinien im [Azure-Portal](https://portal.azure.com), mithilfe der [Azure CLI](../../role-based-access-control/role-assignments-cli.md) oder mit [Azure PowerShell-Cmdlets](/powershell/azure/) konfigurieren, um festzulegen, welche Benutzer oder Anwendungen Zugriff auf die Nutzungsdaten eines Abonnements erhalten. Aufrufer müssen zur Authentifizierung standardmäßige Azure Active Directory-Token verwenden. Ordnen Sie dem Aufrufer die Rolle „Abrechnungsleser“, „Leser“, „Besitzer“ oder „Mitwirkender“ zu, um Zugriff auf die Nutzungsdaten für ein bestimmtes Azure-Abonnement zu erhalten.
-    **Nur für Enterprise Agreement-Kunden**: Diese API ist nur für EA-Kunden verfügbar. Web Direct-Kunden können Preise mithilfe der RateCard-API abrufen.

Weitere Informationen finden Sie in den technischen Angaben zur [Preisblatt-API](/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Szenarien

Es folgen einige Beispiele für Szenarien, die über die Verbrauchs-APIs ermöglicht werden:

-    **Rechnungsabgleich**: Hat Microsoft den richtigen Betrag berechnet?  Wie sieht meine Rechnung aus, und kann ich den Betrag selbst berechnen?
-    **Innerbetriebliche Verrechnung**: Wer in meiner Organisation muss, nun da ich weiß, was mir in Rechnung gestellt wird, den fälligen Betrag zahlen?
-    **Kostenoptimierung**: Ich weiß, wie viel mir berechnet wurde, aber wie kann ich mehr aus dem Geld herausholen, das ich für Azure ausgebe?
-    **Kostenverfolgung**: Ich möchte ermitteln, wie viel ich im Lauf der Zeit für Azure ausgebe und wie ich Azure nutze. Welche Trends gibt es? Was kann ich optimieren?
-    **Azure-Ausgaben während des Monats**: Welche Ausgaben sind bisher im aktuellen Monat entstanden? Muss ich meine Ausgaben und/oder die Nutzung von Azure anpassen? Wann nutze ich im Lauf des Monats Azure am meisten?
-    **Einrichten von Warnungen**: Ich möchte basierend auf einem Budget ressourcenbasierte Nutzungswarnungen oder kostenbasierte Warnungen einrichten.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Verwendung von REST-APIs zum Abrufen von Preisen für alle Azure-Dienste finden Sie unter [Übersicht über Azure-Einzelhandelspreise](/rest/api/cost-management/retail-prices/azure-retail-prices).
