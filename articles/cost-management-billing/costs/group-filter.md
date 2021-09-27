---
title: Gruppierungs- und Filteroptionen in Azure Cost Management
description: In diesem Artikel wird erläutert, wie Sie Gruppierungs- und Filteroptionen in Azure Cost Management verwenden.
author: bandersmsft
ms.author: banders
ms.date: 09/15/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 89344ccfe70a3d0becef103bd0bd3ffee79bb80c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616458"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Gruppierungs- und Filteroptionen in der Kostenanalyse

Die Kostenanalyse beinhaltet zahlreiche Gruppierungs- und Filteroptionen. In diesem Artikel erfahren Sie, wann Sie sie verwenden müssen.

Das Video zur [Cost Management-Berichterstellung anhand von Dimensionen und Tags](https://www.youtube.com/watch?v=2Vx7V17zbmk) enthält weitere Informationen zu den Gruppierungs- und Filteroptionen. Weitere Videos finden Sie im [YouTube-Kanal zu Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Gruppen- und Filtereigenschaften

In der folgenden Tabelle sind einige der häufigsten in der Kostenanalyse verfügbaren Gruppierungs- und Filteroptionen und deren Anwendungsmöglichkeiten aufgeführt.

| Eigenschaft | Verwendung | Notizen |
| --- | --- | --- |
| **Verfügbarkeitszonen** | Die AWS-Kosten werden nach Verfügbarkeitszonen unterteilt. | Dies gilt nur für AWS-Bereiche und -Verwaltungsgruppen. Azure-Daten enthalten keine Verfügbarkeitszone, und hierfür wird **Keine Verfügbarkeitszone** angezeigt. |
| **Abrechnungszeitraum** | Die Kosten für die nutzungsbasierte Zahlung werden nach dem Monat unterteilt, in dem sie berechnet wurden bzw. werden. | Verwenden Sie **Abrechnungszeitraum**, um eine genaue Darstellung der berechneten Kosten für die nutzungsbasierte Zahlung zu erhalten. Fügen Sie vor bzw. nach dem Abrechnungszeitraum zwei zusätzliche Tage ein, wenn Sie nach einem benutzerdefinierten Datumsbereich filtern. Eine Beschränkung auf die genauen Datumsangaben des Abrechnungszeitraums führt dazu, dass die Angaben nicht mit der Rechnung übereinstimmen. Unter dem Abrechnungszeitraum werden die Kosten aller Rechnungen angezeigt. Verwenden Sie die **Rechnungs-ID**, um nach einer bestimmten Rechnung zu filtern. Dies gilt nur für Abonnements mit nutzungsbasierter Zahlung, da die Abrechnung für EA- und MCA-Konten nach Kalendermonaten erfolgt. Für EA/MCA-Konten können Kalendermonate in der Datumsauswahl oder die monatliche Granularität verwendet werden, um dasselbe Ziel zu erreichen. |
| **Gebührentyp** | Unterteilt nach Kosten für Verbrauch, Einkauf, Erstattung und nicht verwendete Reservierungen. | Reservierungseinkäufe und Erstattungen sind nur verfügbar, wenn die tatsächlichen Kosten verwendet werden (nicht für amortisierte Kosten). Kosten für nicht verwendete Reservierungen sind nur verfügbar, wenn Sie sich die amortisierten Kosten ansehen. |
| **Abteilung** | Die Kosten werden nach EA-Abteilung unterteilt. | Ist nur für EA und Verwaltungsgruppen verfügbar. Abonnements mit nutzungsbasierter Zahlung verfügen nicht über eine Abteilung, und hierfür wird **Keine Abteilung** oder **Nicht zugewiesen** angezeigt. |
| **Registrierungskonto** | Kosten werden nach EA-Kontobesitzer unterteilt. | Ist nur für EA-Abrechnungskonten, -Abteilungen und -Verwaltungsgruppen verfügbar. Abonnements mit nutzungsbasierter Zahlung verfügen nicht über EA-Registrierungskonten, und hierfür wird **Kein Registrierungskonto** oder **Nicht zugewiesen** angezeigt. |
| **Frequency** | Nach nutzungsbasierten, einmaligen und wiederkehrenden Kosten unterteilt. | |
| **Rechnungs-ID** | Die Kosten werden nach abgerechneter Rechnung unterteilt. | Für nicht berechnete Gebühren ist noch keine Rechnungs-ID vorhanden, und EA-Kosten enthalten keine Rechnungsdetails. Hierfür wird **Keine Rechnungs-ID** angezeigt.  |
| **Location** | Die Kosten werden nach Ressourcenstandort oder -region unterteilt. | Für Käufe und Marketplace-Nutzung wird ggf. **Nicht zugewiesen** oder **Kein Ressourcenstandort** angezeigt. |
| **Verbrauchseinheit** | Kosten werden nach Verbrauchseinheit unterteilt. | Für Käufe und Marketplace-Nutzung wird **Keine Verbrauchseinheit** angezeigt. Sehen Sie sich den **Gebührentyp** an, um die Käufe und den **Herausgebertyp** und somit auch die Marketplace-Gebühren zu ermitteln. |
| **Vorgang** | Die AWS-Kosten werden nach Vorgang unterteilt. | Dies gilt nur für AWS-Bereiche und -Verwaltungsgruppen. Azure-Daten verfügen nicht über einen Vorgang, und hierfür wird **Kein Vorgang** angezeigt. Verwenden Sie stattdessen **Verbrauchseinheit**. |
| **Preismodell** | Die Kosten werden nach Bedarf, Reservierung oder Nutzung an einem Ort unterteilt. | Käufe werden als „Bedarfsabhängig“ (**OnDemand**) angezeigt. Bei Anzeige von **Nicht zutreffend** sollten Sie nach **Reservierung** gruppieren. So können Sie ermitteln, ob es sich um eine Reservierung oder eine bedarfsabhängige Nutzung handelt, und **Gebührentyp** verwenden, um die Käufe zu identifizieren.
| **Anbieter** | Kosten werden nach AWS und Azure unterteilt. | Ist nur für Verwaltungsgruppen verfügbar. |
| **Herausgebertyp** | Wird nach AWS-, Azure- und Marketplace-Kosten unterteilt. |  |
| **Reservierung** | Kosten werden nach Reservierung unterteilt. | Für alle Nutzungen oder Käufe, die keiner Reservierung zugeordnet sind, wird **Keine Reservierung** angezeigt. Gruppieren Sie nach **Herausgebertyp**, um andere Azure-, AWS- oder Marketplace-Käufe zu identifizieren. |
| **Ressource** | Die Kosten werden nach Ressource unterteilt. | Für Marketplace-Käufe wird **Andere Marketplace-Käufe** und für Azure-Käufe (z. B. Reservierungen und Supportgebühren) wird **Andere Azure-Käufe** angezeigt. Gruppieren oder filtern Sie nach **Herausgebertyp**, um andere Azure-, AWS- oder Marketplace-Käufe zu identifizieren. |
| **Ressourcengruppe** | Die Kosten werden nach Ressourcengruppe unterteilt. | Käufe, Mandantenressourcen ohne Zuordnung zu Abonnements, Abonnementressourcen ohne Bereitstellung in einer Ressourcengruppe und klassische Ressourcen weisen keine Ressourcengruppe auf, und hierfür wird **Andere Marketplace-Käufe**, **Andere Azure-Käufe**, **Weitere Mandantenressourcen**, **Weitere Abonnementressourcen**, **$system** oder **Weitere Gebühren** angezeigt. |
| **Ressourcentyp** | Die Kosten werden nach Ressourcentyp unterteilt. | Käufe und klassische Dienste verfügen nicht über einen Azure Resource Manager-Ressourcentyp, und hierfür wird **Andere**, **Klassische Dienste** oder **Kein Ressourcentyp** angezeigt. |
| **Dienstname** oder **Kategorie der Verbrauchseinheit** | Die Kosten werden nach Azure-Dienst unterteilt. | Für Käufe und Marketplace-Nutzung wird **Kein Dienstname** oder **Nicht zugewiesen** angezeigt. |
| **Dienstebene** oder **Unterkategorie der Verbrauchseinheit** | Die Kosten werden nach der Unterklassifizierung der Verbrauchseinheit für die Azure-Nutzung unterteilt. | Käufe und Marketplace-Nutzung sind leer, oder es wird hierfür **Nicht zugewiesen** angezeigt. |
| **Abonnement** | Die Kosten werden nach Azure-Abonnement und verknüpftem AWS-Konto unterteilt. | Für Käufe und Mandantenressourcen wird ggf. **Kein Abonnement** angezeigt. |
| **Tag** | Die Kosten werden nach Tagwerten für einen bestimmten Tagschlüssel unterteilt. | Käufe, Mandantenressourcen ohne Zuordnung zu Abonnements, Abonnementressourcen ohne Bereitstellung in einer Ressourcengruppe oder klassische Ressourcen können nicht markiert werden, und hierfür wird **Tags nicht verfügbar** angezeigt. Für Dienste, die keine Tags in Nutzungsdaten enthalten, wird **Tags nicht unterstützt** angezeigt. In allen übrigen Fällen, in denen Tags für eine Ressource nicht angegeben sind, wird **Ohne Tags** angezeigt. Erfahren Sie mehr zur [Tagunterstützung für Azure-Ressourcen](../../azure-resource-manager/management/tag-support.md). |

Informationen zu den Bedingungen finden Sie unter [Grundlegendes zu den Bedingungen in der Datei für die Azure-Nutzung und -Gebühren](../understand/understand-usage.md).

## <a name="next-steps"></a>Nächste Schritte

- [Kostenanalyse beginnen](./quick-acm-cost-analysis.md).
