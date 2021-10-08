---
title: Ermitteln der zu erwerbenden Azure-Reservierung
description: Dieser Artikel unterstützt Sie beim Erwerb der optimalen Reservierung.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 09/20/2021
ms.author: banders
ms.openlocfilehash: 7482835f09ec5de0beff6b97b190c5eedfb11b7f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604320"
---
# <a name="determine-what-reservation-to-purchase"></a>Ermitteln der zu erwerbenden Reservierung

Mit Ausnahme von Azure Databricks sind alle Reservierungen stundenbasiert. Reservierungen sollten auf der Grundlage der konsistenten Basisnutzung erworben werden. Die zu erwerbende Reservierung kann auf unterschiedliche Weise ermittelt werden, und dieser Artikel unterstützt Sie bei der Entscheidungsfindung.

Wenn Sie mehr Kapazität erwerben als Sie in der Vergangenheit genutzt haben, führt dies zu einer nicht optimal ausgelasteten Reservierung. Eine Unterauslastung sollte nach Möglichkeit vermieden werden. Nicht genutzte reservierte Kapazität verfällt (sprich: sie wird nicht auf die nächste Stunde übertragen). Nutzung, die über die reservierte Menge hinausgeht, wird zu den höheren Preisen für die nutzungsbasierte Bezahlung abgerechnet.

## <a name="analyze-usage-data"></a>Analysieren der Nutzungsdaten

In den folgenden Abschnitten erfahren Sie, wie Sie Ihre täglichen Nutzungsdaten analysieren, um Ihre Basisnutzung sowie die zu erwerbende Reservierung zu ermitteln.

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>Analysieren der Nutzung für den Erwerb einer reservierten VM-Instanz

Ermitteln Sie die passende VM-Größe für den Erwerb. Eine für virtuelle Computer der ES-Serie erworbene Reservierung gilt beispielsweise nicht für virtuelle Computer der E-Serie (und umgekehrt).

Virtuelle Computer der Promo-Serie erhalten keinen Reservierungsrabatt und müssen daher aus der Analyse entfernt werden.

Filtern Sie Ihre Nutzungsdaten wie folgt, um sie auf die relevante VM-Nutzung einzugrenzen:

- Filtern Sie **MeterCategory** (Kategorie der Verbrauchseinheit) nach **Virtual Machines** (Virtuelle Computer).
- Sehen Sie sich in den Zusatzinformationen (**AdditionalInfo**) den Diensttyp (**ServiceType**) an. Die Angabe gibt Aufschluss über die passende VM-Größe. Beispiel: Standard E32.
- Ermitteln Sie anhand des Felds für den Ressourcenstandort (**ResourceLocation**) das Rechenzentrum mit der Nutzung.

Ignorieren Sie Ressourcen mit einer täglichen Nutzung von weniger als 24 Stunden.

Wenn Sie die Analyse auf der Ebene der Instanzgrößenfamilie durchführen möchten, können Sie die Werte für die Instanzgrößenflexibilität aus [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv) verwenden. Kombinieren Sie die Werte mit Ihren Daten, um die Analyse durchzuführen. Weitere Informationen zur Instanzgrößenflexibilität finden Sie unter [Flexibilität bei der VM-Größe mit reservierten VM-Instanzen](../../virtual-machines/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-usage-for-an-azure-synapse-analytics-reserved-instance-purchase"></a>Analysieren der Nutzung für einen Kauf von reservierten Azure Synapse Analytics-Instanzen

Reservierte Kapazität gilt für den DWU-Preis von Azure Synapse Analytics. Sie gilt nicht für Azure Synapse Analytics-Lizenzkosten oder andere computefremde Kosten.

Filtern Sie Ihre Nutzungsdaten wie folgt, um die relevante Nutzung einzugrenzen:


- Filtern Sie **MeterCategory** (Kategorie der Verbrauchseinheit) nach **SQL Database** (SQL-Datenbank).
- Filtern Sie **MeterName** (Name der Verbrauchseinheit) nach **vCore** (V-Kern).
- Filtern Sie **MeterSubCategory** (Unterkategorie der Verbrauchseinheit) nach allen Nutzungsdatensätzen, deren Name _Compute_ enthält.

Sehen Sie sich in den Zusatzinformationen (**AdditionalInfo**) den Wert für V-Kerne (**vCores**) an. Er gibt Aufschluss über die Anzahl verwendeter V-Kerne. Zur Ermittlung der Menge wird **vCores** mit der Anzahl von Stunden multipliziert, in denen die Datenbank verwendet wurde.

Die Daten geben Aufschluss über die konsistente Verwendung für:

- Datenbanktyp-Kombination: beispielsweise verwaltete Instanz oder Pool für elastische Datenbanken pro Einzeldatenbank.
- Dienstebene: beispielsweise universell oder geschäftskritisch.
- Generation: beispielsweise 5. Generation.
- Resource Location

### <a name="analysis-for-azure-synapse-analytics"></a>Analyse für Azure Synapse Analytics

Reservierte Kapazität gilt für die Azure Synapse Analytics-DWU-Nutzung und wird in Einheiten von jeweils 100 DWU erworben. Filtern Sie Ihre Nutzungsdaten wie folgt, um die relevante Nutzung einzugrenzen:

- Filtern Sie **MeterName** (Name der Verbrauchseinheit) nach **100 DWUs**.
- Filtern Sie **Unterkategorie für Verbrauchseinheit** nach **Compute Optimized Gen2** (Für Compute optimiert, Gen2).

Ermitteln Sie anhand des Felds **Ressourcenspeicherort** die Nutzung für Azure Synapse Analytics in einer Region.

Die Azure Synapse Analytics-Nutzung kann im Laufe des Tages hoch- oder herunterskaliert werden. Wenden Sie sich an das Team, das die Azure Synapse Analytics-Instanz verwaltet, um sich über die Basisnutzung zu informieren.

Navigieren Sie im Azure-Portal zu „Reservierungen“, und erwerben Sie reservierte Azure Synapse Analytics-Kapazität in Vielfachen von 100 DWU.

## <a name="reservation-purchase-recommendations"></a>Empfehlungen für den Reservierungserwerb

Zur Berechnung der Empfehlungen für den Reservierungserwerb werden Ihre stündlichen Nutzungsdaten der letzten sieben, 30 und 60 Tage analysiert. Azure berechnet die Kosten, die entstanden wären, wenn Sie eine Reservierung erworben hätten, und vergleicht sie mit ihren tatsächlichen Kosten für die nutzungsbasierte Bezahlung, die im entsprechenden Zeitraum angefallen sind. Die Berechnung wird für jede Menge durchgeführt, die Sie während des Zeitrahmens verwendet haben. Empfohlen wird die Menge, mit der Sie die höchsten Einsparungen erzielen.

Angenommen, Sie nutzen die meiste Zeit 500 virtuelle Computer, es gibt jedoch gelegentliche Spitzenzeiten mit einer Nutzung von 700 virtuellen Computern. In diesem Beispiel berechnet Azure Ihre Einsparungen sowohl für 500 als auch für 700 virtuelle Computer. Da die Nutzung von 700 virtuellen Computern nur sporadisch auftritt, kommt die Empfehlungsberechnung zu dem Ergebnis, dass die höchsten Einsparungen bei einem Reservierungserwerb für 500 virtuelle Computer erzielt werden, und die Empfehlung wird für diese Menge abgegeben.

Beachten Sie folgende Punkte:

- Reservierungsempfehlungen werden unter Verwendung der bedarfsgesteuerten Nutzungsraten berechnet, die für Sie gelten.
- Empfehlungen werden für einzelne Größen berechnet, nicht für die Instanzgrößenfamilie.
- An dem Tag, an dem Sie Reservierungen für einen Bereich erwerben, verringert sich die empfohlene Menge für den Bereich.
    - Eine bereichsübergreifende Aktualisierung der empfohlenen Reservierungsmenge kann jedoch bis zu 25 Tage dauern. Ein Beispiel: Wenn Sie den Kaufempfehlungen für einen gemeinsamen Bereich folgen, kann es bis zu 25 Tage dauern, bis die Empfehlungen für den Einzelabonnementbereich nach unten korrigiert werden.
- Derzeit generiert Azure keine Empfehlungen für den Verwaltungsgruppenbereich.

## <a name="recommendations-in-the-azure-portal"></a>Empfehlungen im Azure-Portal

Von der Empfehlungsengine berechnete Reservierungskäufe werden im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs) auf der Registerkarte **Empfohlen** angezeigt. Beispiel:

![Abbildung mit Empfehlungen](./media/determine-reservation-purchase/select-product-ri.png)

Informieren Sie sich genauer über [Empfehlungen](reserved-instance-purchase-recommendations.md#recommendations-in-the-azure-portal).

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Empfehlungen in der Power BI-App „Cost Management“

Kunden mit Enterprise Agreement können die Berichte zur Abdeckung reservierter VM-Instanzen für virtuelle Computer und Kaufempfehlungen verwenden. Die Berichte zur Abdeckung geben Aufschluss über die Gesamtnutzung sowie über die Nutzung, die durch reservierte Instanzen abgedeckt ist.

1. Die Cost Management-App erhalten Sie [hier](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
2. Navigieren Sie abhängig von dem Bereich, in dem Sie einkaufen möchten, zum gemeinsamen Bereich oder zum Einzelbereich des Berichts zur Abdeckung reservierter VM-Instanzen.
3. Wählen Sie die Region und die Instanzgrößenfamilie aus, um die Nutzung, die RI-Abdeckung und die Kaufempfehlung für den ausgewählten Filter anzuzeigen.

## <a name="recommendations-in-azure-advisor"></a>Empfehlungen in Azure Advisor

Empfehlungen für den Reservierungserwerb stehen in [Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview) zur Verfügung.

- Von Advisor werden nur Empfehlungen für Einzelabonnementbereiche abgegeben.
- Advisor-Empfehlungen werden auf der Grundlage der letzten 30 Tage berechnet. Die prognostizierten Einsparungen gelten für eine Reservierungsdauer von drei Jahren.
- Wenn Sie eine Reservierung mit gemeinsam genutztem Bereich erwerben, kann es bis zu 30 Tage dauern, bis Advisor-Empfehlungen für den Reservierungserwerb nicht mehr angezeigt werden.

## <a name="recommendations-using-apis"></a>Empfehlungen über APIs

Verwenden Sie die REST-API für [Reservierungsempfehlungen](/rest/api/consumption/reservationrecommendations/list), um Empfehlungen programmgesteuert anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
- [Grundlegendes zur Reservierungsnutzung bei einem Abonnement mit nutzungsbasierten Tarifen ](understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)
- [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](reserved-instance-windows-software-costs.md)