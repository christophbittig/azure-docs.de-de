---
title: Gruppierungs- und Filteroptionen in Azure Cost Management
description: In diesem Artikel wird erläutert, wie Sie Gruppierungs- und Filteroptionen in Cost Management verwenden.
author: bandersmsft
ms.author: banders
ms.date: 10/11/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 8cc4a0bfd7503a48b3df6887c05f268fcfae5733
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856332"
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
| **Abrechnungszeitraum** | Die Kosten für die nutzungsbasierte Zahlung werden nach dem Monat unterteilt, in dem sie berechnet wurden bzw. werden. | Verwenden Sie **Abrechnungszeitraum**, um eine genaue Darstellung der berechneten Kosten für die nutzungsbasierte Zahlung zu erhalten. Fügen Sie vor und nach dem Abrechnungszeitraum zwei zusätzliche Tage ein, wenn Sie nach einem benutzerdefinierten Datumsbereich filtern. Eine Beschränkung auf die genauen Datumsangaben des Abrechnungszeitraums führt dazu, dass die Angaben nicht mit der Rechnung übereinstimmen. Unter dem Abrechnungszeitraum werden die Kosten aller Rechnungen angezeigt. Verwenden Sie die **Rechnungs-ID**, um nach einer bestimmten Rechnung zu filtern. Dies gilt nur für Abonnements mit nutzungsbasierter Zahlung, da die Abrechnung für EA- und MCA-Konten nach Kalendermonaten erfolgt. Für EA/MCA-Konten können Kalendermonate in der Datumsauswahl oder die monatliche Granularität verwendet werden, um dasselbe Ziel zu erreichen. |
| **Gebührentyp** | Unterteilt nach Kosten für Verbrauch, Einkauf, Erstattung und nicht verwendete Reservierungen. | Reservierungseinkäufe und Erstattungen sind nur verfügbar, wenn die tatsächlichen Kosten verwendet werden (nicht für amortisierte Kosten). Kosten für nicht verwendete Reservierungen sind nur verfügbar, wenn Sie sich die amortisierten Kosten ansehen. |
| **Abteilung** | Die Kosten werden nach EA-Abteilung unterteilt. | Ist nur für EA und Verwaltungsgruppen verfügbar. Abonnements mit nutzungsbasierter Zahlung verfügen nicht über eine Abteilung, und hierfür wird **Keine Abteilung** oder **Nicht zugewiesen** angezeigt. |
| **Registrierungskonto** | Kosten werden nach EA-Kontobesitzer unterteilt. | Ist nur für EA-Abrechnungskonten, -Abteilungen und -Verwaltungsgruppen verfügbar. Abonnements mit nutzungsbasierter Zahlung verfügen nicht über EA-Registrierungskonten, und hierfür wird **Kein Registrierungskonto** oder **Nicht zugewiesen** angezeigt. |
| **Frequency** | Nach nutzungsbasierten, einmaligen und wiederkehrenden Kosten unterteilt. | |
| **Rechnungs-ID** | Die Kosten werden nach abgerechneter Rechnung unterteilt. | Für nicht berechnete Gebühren ist noch keine Rechnungs-ID vorhanden, und EA-Kosten enthalten keine Rechnungsdetails. Hierfür wird **Keine Rechnungs-ID** angezeigt.  |
| **Location** | Die Kosten werden nach Ressourcenstandort oder -region unterteilt. | Für Käufe und Marketplace-Nutzung wird ggf. **Nicht zugewiesen** oder **Kein Ressourcenstandort** angezeigt. |
| **Verbrauchseinheit** | Kosten werden nach Verbrauchseinheit unterteilt. | Für Käufe und Marketplace-Nutzung wird **Keine Verbrauchseinheit** angezeigt. Sehen Sie sich den **Gebührentyp** an, um die Käufe und den **Herausgebertyp** und somit auch die Marketplace-Gebühren zu ermitteln. |
| **Vorgang** | Die AWS-Kosten werden nach Vorgang unterteilt. | Dies gilt nur für AWS-Bereiche und -Verwaltungsgruppen. Azure-Daten verfügen nicht über einen Vorgang, und hierfür wird **Kein Vorgang** angezeigt. Verwenden Sie stattdessen **Verbrauchseinheit**. |
| **Preismodell** | Die Kosten werden nach Bedarf, Reservierung oder Nutzung an einem Ort unterteilt. | Käufe werden als „Bedarfsabhängig“ (**OnDemand**) angezeigt. Bei Anzeige von **Nicht zutreffend** sollten Sie nach **Reservierung** gruppieren. So können Sie ermitteln, ob es sich um eine Reservierung oder eine bedarfsabhängige Nutzung handelt, und **Gebührentyp** verwenden, um die Käufe zu identifizieren.
| **Anbieter** | Die Kosten werden nach Anbietertyp unterteilt: Azure, Microsoft 365, Dynamics 365, AWS und so weiter. | Bezeichner für Produkt und Branche. |
| **Herausgebertyp** | Die Kosten werden nach Microsoft-, Azure-, AWS- und Marketplace-Kosten unterteilt. | Werte sind **Microsoft** für MCA-Konten und **Azure** für EA-Konten und Konten mit nutzungsbasierter Bezahlung. |
| **Reservierung** | Kosten werden nach Reservierung unterteilt. | Für alle Nutzungen oder Käufe, die keiner Reservierung zugeordnet sind, wird **Keine Reservierung** angezeigt. Gruppieren Sie nach **Herausgebertyp**, um andere Azure-, AWS- oder Marketplace-Käufe zu identifizieren. |
| **Ressource** | Die Kosten werden nach Ressource unterteilt. | Für Marketplace-Käufe wird **Andere Marketplace-Käufe** und für Azure-Käufe (z. B. Reservierungen und Supportgebühren) wird **Andere Azure-Käufe** angezeigt. Gruppieren oder filtern Sie nach **Herausgebertyp**, um andere Azure-, AWS- oder Marketplace-Käufe zu identifizieren. |
| **Ressourcengruppe** | Die Kosten werden nach Ressourcengruppe unterteilt. | Käufe, Mandantenressourcen ohne Zuordnung zu Abonnements, Abonnementressourcen ohne Bereitstellung in einer Ressourcengruppe und klassische Ressourcen weisen keine Ressourcengruppe auf, und hierfür wird **Andere Marketplace-Käufe**, **Andere Azure-Käufe**, **Weitere Mandantenressourcen**, **Weitere Abonnementressourcen**, **$system** oder **Weitere Gebühren** angezeigt. |
| **Ressourcentyp** | Die Kosten werden nach Ressourcentyp unterteilt. | Käufe und klassische Dienste verfügen nicht über einen Azure Resource Manager-Ressourcentyp, und hierfür wird **Andere**, **Klassische Dienste** oder **Kein Ressourcentyp** angezeigt. |
| **Dienstname** oder **Kategorie der Verbrauchseinheit** | Die Kosten werden nach Azure-Dienst unterteilt. | Für Käufe und Marketplace-Nutzung wird **Kein Dienstname** oder **Nicht zugewiesen** angezeigt. |
| **Dienstebene** oder **Unterkategorie der Verbrauchseinheit** | Die Kosten werden nach der Unterklassifizierung der Verbrauchseinheit für die Azure-Nutzung unterteilt. | Käufe und Marketplace-Nutzung sind leer, oder es wird hierfür **Nicht zugewiesen** angezeigt. |
| **Abonnement** | Die Kosten werden nach Azure-Abonnement und verknüpftem AWS-Konto unterteilt. | Für Käufe und Mandantenressourcen wird ggf. **Kein Abonnement** angezeigt. |
| **Tag** | Die Kosten werden nach Tagwerten für einen bestimmten Tagschlüssel unterteilt. | Käufe, Mandantenressourcen ohne Zuordnung zu Abonnements, Abonnementressourcen ohne Bereitstellung in einer Ressourcengruppe oder klassische Ressourcen können nicht markiert werden, und hierfür wird **Tags nicht verfügbar** angezeigt. Für Dienste, die keine Tags in Nutzungsdaten enthalten, wird **Tags nicht unterstützt** angezeigt. In allen übrigen Fällen, in denen Tags für eine Ressource nicht angegeben sind, wird **Ohne Tags** angezeigt. Erfahren Sie mehr zur [Tagunterstützung für Azure-Ressourcen](../../azure-resource-manager/management/tag-support.md). |

Informationen zu den Bedingungen finden Sie unter [Grundlegendes zu den Bedingungen in der Datei für die Azure-Nutzung und -Gebühren](../understand/understand-usage.md).

## <a name="changes-to-publisher-type-values"></a>Änderungen an Werten für den Herausgebertyp

Für Kunden mit einer MCA-Vereinbarung wird der `Publisher type`-Wert **Azure** durch den Wert **Microsoft** ersetzt, um anzeigen, dass er für alle von Microsoft erhobenen Gebühren gilt, die in Cost Management + Billing nachverfolgt werden. Diese Änderung ist seit dem 14. Oktober 2021 wirksam.

Beachten Sie Folgendes:

- Gespeicherte Ansichten in der Kostenanalyse: Alle gespeicherten Ansichten, die den Filter `PublisherType` = **Azure** verwenden, werden entsprechend dem neuen Wert **Microsoft** aktualisiert. 
    - Erforderliche Aktion: Keine.
- Budgets mit dem `PublisherType`-Filter: Alle Budgets, die mit dem Filter `PublisherType` = **Azure**  erstellt wurden, werden entsprechend dem neuen Wert **Microsoft** aktualisiert.
    - Erforderliche Aktion: Keine.
- Exporte: Alle Exportdateien, die vor dem 14. Oktober 2021 generiert wurden, weisen im `PublisherType field` den Wert **Azure** und nach dem 14. Oktober 2021 den Wert **Microsoft** auf.
    - Aktion erforderlich: *Stellen Sie sicher, dass die geänderten Werte berücksichtigt werden, wenn Sie neue Dateien mit Dateien zusammenführen, die vor dem 14. Oktober 2021 heruntergeladen wurden.*
- Heruntergeladene Daten: In allen aus der Kostenanalyse heruntergeladenen Dateien weisen die Nutzungsdetails vor dem 14. Oktober 2021 weiterhin den alten Wert **Azure** im Feld `PublisherType` auf.
    - Aktion erforderlich: *Stellen Sie sicher, dass die geänderten Werte berücksichtigt werden, wenn Sie neue Dateien mit Dateien zusammenführen, die vor dem 14. Oktober 2021 heruntergeladen wurden.*
- REST-API-Aufrufe: Wenn Sie REST-API-Aufrufe in Cost Management + Billing verwenden, die das Feld „PublisherType“ nach dem Wert **Azure** filtern, aktualisieren Sie diese so, dass nach dem 14. Oktober 2021 nach **Microsoft** gefiltert wird.
    - Aktion erforderlich: *Verwenden Sie `Publisher type` = **Microsoft** in Ihren REST-API-Aufrufen, um alle Erstanbietergebühren zu erhalten. Aufrufe mit `Publisher type` = **Azure** geben keine Daten zurück*.

> [!NOTE]
> Diese Änderung wirkt sich nicht auf Sie aus, wenn Sie über ein EA-Konto (Enterprise Agreement) oder ein Konto mit nutzungsbasierter Zahlung (PAYG) verfügen.


## <a name="next-steps"></a>Nächste Schritte

- [Kostenanalyse beginnen](./quick-acm-cost-analysis.md).
