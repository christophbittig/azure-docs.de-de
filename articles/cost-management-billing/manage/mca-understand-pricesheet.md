---
title: Begriffe auf dem Preisblatt Ihrer Microsoft-Kundenvereinbarung – Azure
description: Erfahren Sie, wie Sie die Nutzung und Rechnung für eine Microsoft-Kundenvereinbarung lesen und verstehen.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 997bf9e2b88269985c85220c4960179d148a4190
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637470"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Begriffe auf dem Preisblatt Ihrer Microsoft-Kundenvereinbarung

Dieser Artikel bezieht sich auf ein Azure-Abrechnungskonto für eine Microsoft-Kundenvereinbarung. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben.](#check-access-to-a-microsoft-customer-agreement)

Wenn Sie über die Rolle „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ verfügen, können Sie im Azure-Portal das Preisblatt Ihrer Organisation herunterladen. Weitere Informationen finden Sie unter [Anzeigen und Herunterladen der Preise Ihrer Organisation](ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Begriffe und Beschreibungen auf Ihrem Preisblatt

Im folgenden Abschnitt werden wichtige Begriffe beschrieben, die auf dem Preisblatt für eine Microsoft-Kundenvereinbarung angezeigt werden.

| **Feldname**   | **Beschreibung**   |
| --- | --- |
| basePrice  | Der Marktpreis zum Zeitpunkt der Kundenanmeldung oder der Marktpreis zum Zeitpunkt des Starts der Dienstverbrauchseinheit, wenn dieser nach der Anmeldung erfolgt.   |
| billingAccountId  | Eindeutiger Bezeichner für das Abrechnungskonto.   |
| billingAccountName  | Name des Abrechnungskontos.  |
| billingCurrency | Währung, in der Gebühren gebucht werden. |
| billingProfileId  | Eindeutiger Bezeichner für das Abrechnungsprofil.   |
| billingProfileName  | Name des zum Empfangen von Rechnungen eingerichteten Abrechnungsprofils. Die Preise auf dem Preisblatt sind diesem Abrechnungsprofil zugeordnet. |
| currency | Währung, in der alle Preise angegeben werden. |
| discount | Der Preisnachlass, der auf den Graduation-Tarif, den Free-Tarif oder bei Rabatten vom Typ „Enthaltene Menge“ oder vereinbarten Rabatten gewährt wird, sofern anwendbar. Dieser wird als Prozentsatz angegeben. |
| effectiveEndDate  | Enddatum des gültigen Preises. |
| effectiveStartDate  | Startdatum, an dem der Preis in Kraft tritt. |
| includedQuantity | Mengen eines bestimmten Diensts, auf die ein Kunde Anspruch hat, ohne dass inkrementelle Gebühren anfallen. |
| marketPrice | Der derzeit geltende Marktpreis für einen bestimmten Dienst. |
| meterId  | Eindeutiger Bezeichner für die Verbrauchseinheit. |
| meterCategory  | Name der Klassifizierungskategorie der Verbrauchseinheit. Beispiele: _Clouddienste_, _Netzwerk_ usw. |
| meterName  | Name der Verbrauchseinheit. Die Verbrauchseinheit stellt die bereitstellbare Ressource eines Azure-Diensts dar. |
| meterSubCategory  | Name der Unterklassifizierungskategorie der Verbrauchseinheit.  |
| meterType  |  Name des Verbrauchseinheitstyps. |
| meterRegion  | Der Name der Region, in der die Verbrauchseinheit für den Dienst verfügbar ist. Gibt den Standort des Rechenzentrums für bestimmte Dienste an, die basierend auf dem Standort des Rechenzentrums berechnet werden.    |
| Produkt  | Name des Produkts, für das die Gebühren anfallen, z. B. Basic SQL DB vs. Standard SQL DB  |
| productId  | Eindeutiger Bezeichner für das Produkt, dessen Verbrauchseinheit genutzt wird. |
| productOrderName  | Name des erworbenen Produktplans. |
| serviceFamily  | Art des Azure-Dienstes, z. B. Compute, Analytics, Security |
| tierMinimumUnits  | Definiert die untere Grenze des Tarifbereichs, für den Preise definiert sind. Bei einem Bereich von 0 bis 100 wäre der Wert von „tierMinimumUnits“ z. B. „0“.  |
| unitOfMeasure  | Identifiziert die Abrechnungsmaßeinheiten für den Dienst. Computedienste werden beispielsweise pro Stunde abgerechnet. |
| unitPrice  | Preis pro Einheit zum Zeitpunkt der Abrechnung (nicht der gültige Mischpreis), bezogen auf eine Verbrauchseinheit und einen Produktauftragsnamen.  Hinweis: Der Preis pro Einheit ist nicht identisch mit dem effektiven Preis in den Downloads der Nutzungsdetails, wenn es sich um Dienste mit unterschiedlichen Preisen für die verschiedenen Stufen handelt.  Bei Diensten mit mehrstufiger Preisgestaltung ist der gültige Preis eine stufenübergreifende Mischrate, und es wird dafür kein stufenspezifischer Einzelpreis angezeigt. Der Mischpreis oder der gültige Preis ist der Nettopreis für die verbrauchte Menge über mehrere Stufen hinweg (wobei für jede Stufe ein bestimmter Einzelpreis gilt). |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen und Herunterladen der Preise Ihrer Organisation](ea-pricing.md)
