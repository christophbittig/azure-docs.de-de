---
title: Zuteilen von Azure-Kosten
description: In diesem Artikel wird erläutert, wie Sie Kostenzuteilungsregeln erstellen, um die Kosten von Abonnements, Ressourcengruppen oder Tags an andere zu verteilen.
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 7696dc683d41080f5be2c97ebcb0cdf314e2098d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706271"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Erstellen und Verwalten von Azure-Kostenzuteilungsregeln (Vorschauversion)

Große Unternehmen verfügen häufig über Azure-Dienste oder -Ressourcen, die zentral verwaltet, aber von verschiedenen internen Abteilungen oder Geschäftseinheiten genutzt werden. In der Regel möchte das für die zentrale Verwaltung zuständige Team die Kosten der gemeinsam genutzten Dienste wieder den internen Abteilungen oder Geschäftseinheiten der Organisation zuteilen, die diese Dienste aktiv nutzen. In diesem Artikel erhalten Sie grundlegende Informationen zur Kostenzuteilung und erfahren, wie Sie diese in Cost Management verwenden.

Mit der Kostenzuteilung können Sie die Kosten für gemeinsam genutzte Dienste von Abonnements, Ressourcengruppen oder Tags anderen Abonnements, Ressourcengruppen oder Tags in Ihrer Organisation neu zuweisen bzw. an diese verteilen. Durch die Kostenzuteilung werden die Kosten der gemeinsam genutzten Dienste auf ein anderes Abonnement, Ressourcengruppen oder Tags im Besitz der internen Abteilungen oder Geschäftseinheiten verlagert, von denen sie genutzt werden. Mit anderen Worten: Die Kostenzuteilung unterstützt Sie bei der Verwaltung und bietet Ihnen einen übergreifenden Überblick über die _Zurechenbarkeit von Kosten_.

Die Kostenzuteilung hat keinen Einfluss auf Ihre Abrechnung. Die Abrechnungspflichten ändern sich nicht. Der Hauptzweck der Kostenzuteilung besteht darin, Ihnen das Rückbuchen von Kosten an andere Abteilungen oder Geschäftseinheiten zu ermöglichen. Alle Rückbuchungsprozesse erfolgen in Ihrer Organisation außerhalb von Azure. Mit der Kostenzuteilung können Sie Kosten rückbuchen, indem Sie ihre erneute Zuweisung oder Verteilung anzeigen.

Zugeteilte Kosten werden in der Kostenanalyse angezeigt. Sie werden als zusätzliche zugeordnete Elemente der Zielabonnements, -ressourcengruppen oder -tags angezeigt, die Sie beim Erstellen einer Kostenzuteilungsregel angeben.

> [!NOTE]
> Das Cost Management-Feature der Kostenzuteilung befindet sich derzeit in der Public Preview-Phase. Einige Features in Cost Management werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

- Die Kostenzuteilung unterstützt derzeit nur Kunden mit einer [Microsoft-Kundenvereinbarung](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) oder einem [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/).
- Zum Erstellen oder Verwalten einer Kostenzuteilungsregel müssen Sie ein Unternehmensadministratorkonto für [Enterprise Agreements](../manage/understand-ea-roles.md) verwenden oder Besitzer eines [Abrechnungskontos](../manage/understand-mca-roles.md) für Microsoft-Kundenvereinbarungen sein.

## <a name="create-a-cost-allocation-rule"></a>Erstellen einer Kostenzuteilungsregel

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com/) beim Azure-Portal an.
2. Navigieren Sie zu **Cost Management + Abrechnung** > **Cost Management**.
3. Wählen Sie unter **Einstellungen** > **Konfiguration** die Option **Kostenzuteilung (Vorschau)** aus.
4. Stellen Sie sicher, dass das richtige EA-Registrierungs- oder Abrechnungskonto ausgewählt ist.
5. Wählen Sie **+ Hinzufügen** aus.
6. Geben Sie für den Namen der Kostenzuteilungsregel beschreibenden Text ein.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Beispiel für das Erstellen eines Regelnamens" lightbox="./media/allocate-costs/rule-name.png" :::

Das Startdatum für die Auswertung der Regel wird verwendet, um die vorab ausgefüllten Kostenzuteilungsprozentsätze zu generieren.

1. Klicken Sie auf **Quellen hinzufügen**, und wählen Sie dann Abonnements, Ressourcengruppen oder Tags aus, um die zu verteilenden Kosten auszuwählen.
2. Klicken Sie auf **Ziele hinzufügen**, und wählen Sie dann Abonnements, Ressourcengruppen oder Tags aus, denen die Kosten zugeteilt werden sollen.
3. Wiederholen Sie diese Schritte, falls Sie weitere Kostenzuteilungsregeln erstellen müssen.

## <a name="configure-the-allocation-percentage"></a>Konfigurieren des Zuteilungsprozentsatzes

Konfigurieren Sie den Zuteilungsprozentsatz, um festzulegen, wie die Kosten zwischen den angegebenen Zielen proportional aufgeteilt werden. Sie können manuell ganzzahlige Prozentsätze festlegen, um eine Zuteilungsregel zu erstellen. Alternativ können Sie die Kosten auf Grundlage der aktuellen Nutzung von Compute, Speicher oder Netzwerk proportional auf die angegebenen Ziele verteilen.

Wenn Sie Kosten nach Compute-, Speicher- oder Netzwerkkosten verteilen, wird der proportionale Prozentsatz durch Auswerten der Kosten des ausgewählten Ziels abgeleitet. Die Kosten werden dem Ressourcentyp für den aktuellen Abrechnungsmonat zugeordnet.

Wenn Sie Kosten proportional zu den Gesamtkosten verteilen, wird der proportionale Prozentsatz nach der Kostensumme bzw. den Gesamtkosten der ausgewählten Ziele für den aktuellen Abrechnungsmonat zugewiesen.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="Beispiel für den Zuteilungsprozentsatz" lightbox="./media/allocate-costs/cost-distribution.png" :::

Die definierten vorab ausgefüllten Prozentsätze sind feststehende Werte. Sie werden für alle laufenden Zuteilungen verwendet. Die Prozentsätze ändern sich nur, wenn die Regel manuell aktualisiert wird.

1. Wählen Sie eine der folgenden Optionen in der Liste **Prozentsatz vorab festlegen auf** aus.
    - **Gleichmäßig verteilen**: Weist jedem Ziel einen geraden prozentualen Anteil der Gesamtkosten zu.
    - **Gesamtkosten**: Erstellt basierend auf den Gesamtkosten ein proportionales Verhältnis der Ziele. Das Verhältnis wird verwendet, um die Kosten der ausgewählten Quellen zu verteilen.
    - **Computekosten**: Erstellt basierend auf den Azure-Computekosten (Ressourcentypen im [Microsoft.Compute](/azure/templates/microsoft.compute/allversions)-Namespace) ein proportionales Verhältnis der Ziele. Das Verhältnis wird verwendet, um die Kosten der ausgewählten Quellen zu verteilen.
    - **Speicherkosten**: Erstellt basierend auf den Azure-Speicherkosten (Ressourcentypen im [Microsoft.Storage](/azure/templates/microsoft.storage/allversions)-Namespace) ein proportionales Verhältnis der Ziele. Das Verhältnis wird verwendet, um die Kosten der ausgewählten Quellen zu verteilen.
    - **Netzwerkkosten**: Erstellt basierend auf den Azure-Netzwerkkosten (Ressourcentypen im [Microsoft.Network](/azure/templates/microsoft.network/allversions)-Namespace) ein proportionales Verhältnis der Ziele. Das Verhältnis wird verwendet, um die Kosten der ausgewählten Quellen zu verteilen.
    - **Benutzerdefiniert**: Ermöglicht die manuelle Angabe eines ganzzahligen Prozentsatzes. Die angegebenen Werte müssen insgesamt 100 % ergeben.
1. Nachdem Sie die Regel konfiguriert haben, wählen Sie **Erstellen** aus.

Die Verarbeitung der Zuteilungsregel wird gestartet. Wenn die Regel aktiv ist, werden alle Kosten der ausgewählten Quellen den angegebenen Zielen zugeteilt.

> [!NOTE] 
> Die Verarbeitung der neuen Regel kann bis zu zwei Stunden dauern. Erst dann ist sie abgeschlossen und aktiv.

Im folgenden Video wird die Erstellung einer Kostenzuteilungsregel veranschaulicht:

>[!VIDEO https://www.youtube.com/embed/nYzIIs2mx9Q]

## <a name="verify-the-cost-allocation-rule"></a>Überprüfen der Kostenzuteilungsregel

Wenn die Kostenzuteilungsregel aktiv ist, werden die Kosten der ausgewählten Quellen an die angegebenen Zuteilungsziele verteilt. Überprüfen Sie anhand der folgenden Informationen, ob die Kosten den Zielen korrekt zugeteilt werden.

### <a name="view-cost-allocation-for-a-subscription"></a>Anzeigen der Kostenzuteilung für ein Abonnement

Die Auswirkungen der Zuteilungsregel sehen Sie in der Kostenanalyse. Navigieren Sie im Azure-Portal zu [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wählen Sie in der Liste ein Abonnement aus, das als Ziel einer aktiven Kostenzuteilungsregel festgelegt ist. Wählen Sie dann im Menü die Option **Kostenanalyse** aus. Wählen Sie unter „Kostenanalyse“ die Option **Gruppieren nach** und dann **Kostenzuteilung** aus. Die resultierende Ansicht zeigt eine kurze vom Abonnement generierte Kostenaufschlüsselung. Die dem Abonnement zugeteilten Kosten werden wie in der folgenden Abbildung dargestellt ebenfalls angezeigt.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="Beispiel für eine Kostenaufschlüsselung" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>Anzeigen der Kostenzuteilung für eine Ressourcengruppe

Zum Anzeigen der Auswirkungen einer Kostenzuteilungsregel für eine Ressourcengruppe gehen Sie ähnlich vor. Navigieren Sie im Azure-Portal zu [Ressourcengruppen](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). Wählen Sie in der Liste eine Ressourcengruppe aus, die als Ziel einer aktiven Kostenzuteilungsregel festgelegt ist. Wählen Sie dann im Menü die Option **Kostenanalyse** aus. Wählen Sie unter „Kostenanalyse“ die Option **Gruppieren nach** und dann **Kostenzuteilung** aus. Die resultierende Ansicht zeigt eine kurze von der Ressourcengruppe generierte Kostenaufschlüsselung. Die der Ressourcengruppe zugeteilten Kosten werden ebenfalls angezeigt.

### <a name="view-cost-allocation-for-tags"></a>Anzeigen der Kostenzuteilung für Tags

Navigieren Sie im Azure-Portal zu **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**. Wählen Sie unter „Kostenanalyse“ die Option **Filter hinzufügen** aus. Wählen Sie **Tag** und dann den Tagschlüssel aus, und markieren Sie Werte, denen Kosten zugeteilt sind.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="Beispiel für die Anzeige der Kosten für markierte Elemente" lightbox="./media/allocate-costs/tagged-costs.png" :::

### <a name="view-cost-allocation-in-the-downloaded-usage-details-and-in-exports-csv-files"></a>Anzeigen der Kostenzuteilung in den heruntergeladenen CSV-Dateien mit Nutzungsdetails und Exporten

Kostenzuteilungsregeln sind auch in der heruntergeladenen Datei mit den Nutzungsdetails sowie in den exportierten Daten verfügbar. Die Datendateien haben den Spaltennamen `costAllocationRuleName`. Wenn eine Kostenzuteilungsregel für einen Eintrag in der Datei mit Nutzungsdetails oder Exporten gilt, wird die Zeile mit dem Namen der Kostenzuteilungsregel aufgefüllt. Die folgende Beispielabbildung zeigt eine negative Gebühr mit einem Eintrag für das Quellabonnement. Dies ist die Gebühr, von der aus Kosten zugeteilt werden. Es gibt auch eine positive Gebühr für das Ziel der Kostenzuteilungsregel.

:::image type="content" source="./media/allocate-costs/rule-costs-allocated.png" alt-text="Screenshot: Zugeteilte Kosten in der Datei mit den Nutzungsdetails" lightbox="./media/allocate-costs/rule-costs-allocated.png" :::

#### <a name="azure-invoice-reconciliation"></a>Azure-Rechnungsabgleich 

Die Datei mit den Nutzungsdetails wird auch für den Azure-Rechnungsabgleich verwendet. Die Anzeige intern zugeteilter Kosten während des Abgleichs kann verwirrend sein. Sie können alle Kostenzuteilungsregeln herausfiltern, um Verwirrungen zu vermeiden und die Anzeige auf die Daten aus der Rechnung abzustimmen. Nach Entfernung der Kostenzuteilungsregeln sollte Ihre Datei mit den Nutzungsdetails den Kosten entsprechen, die in der Rechnung des abgerechneten Abonnements angegeben sind.

:::image type="content" source="./media/allocate-costs/rule-name-filtered.png" alt-text="Screenshot: Zugeteilte Kosten mit herausgefilterten Regelnamen" lightbox="./media/allocate-costs/rule-name-filtered.png" :::

## <a name="edit-an-existing-cost-allocation-rule"></a>Bearbeiten einer vorhandenen Kostenzuteilungsregel

Sie können eine Kostenzuteilungsregel bearbeiten, um die Quelle oder das Ziel zu ändern oder den vorab ausgefüllten Prozentsatz für Compute, Speicher oder Netzwerk zu aktualisieren. Zum Bearbeiten der Regeln gehen Sie wie bei ihrer Erstellung vor. Die erneute Verarbeitung von geänderten Regeln kann bis zu zwei Stunden in Anspruch nehmen.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

Derzeit wird die Kostenzuteilung in den Kostenanalyse-, Budget- und Prognoseansichten von Cost Management unterstützt. Zugeteilte Kosten werden auch in der Abonnementliste und auf der Übersichtsseite von Abonnements angezeigt.

Folgendes wird in der Public Preview der Kostenzuteilung derzeit nicht unterstützt:

- Von der [Nutzungsdetails](/rest/api/consumption/usagedetails/list)-API verfügbar gemachte Daten
- Bereich für Abrechnungsabonnements
- [Cost Management-Power BI-App](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Power BI Desktop-Connector](/power-bi/connect-data/desktop-connect-azure-cost-management)


## <a name="next-steps"></a>Nächste Schritte

- In den [häufig gestellten Fragen zu Cost Management + Billing](../cost-management-billing-faq.yml) finden Sie Fragen und Antworten zur Kostenzuteilung.
- Erstellen oder Aktualisieren von Zuteilungsregeln mithilfe der [Kostenzuteilungs-REST-API](/rest/api/cost-management/costallocationrules)
- Weitere Informationen zum [Optimieren der Cloudinvestitionen mit Cost Management](cost-mgt-best-practices.md)