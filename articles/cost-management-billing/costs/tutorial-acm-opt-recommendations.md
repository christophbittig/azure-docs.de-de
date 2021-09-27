---
title: 'Tutorial: Verringern der Azure-Kosten mit Empfehlungen'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Optimierungsempfehlungen die Azure-Kosten senken können.
author: bandersmsft
ms.author: banders
ms.date: 09/15/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: f8b8b99c1cf2be554703eb49de8c6ff2a39b53be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128567697"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Tutorial: Optimieren von Kosten mithilfe von Empfehlungen

Azure Cost Management unterbreitet Ihnen mithilfe von Azure Advisor Empfehlungen zur Kostenoptimierung. Azure Advisor zeigt Ihnen Möglichkeiten der Optimierung und Steigerung der Effizienz auf, indem ungenutzte oder nicht ausreichend genutzte Ressourcen ermittelt werden. Dieses Tutorial führt Sie durch ein Beispiel, bei dem Sie nicht ungenutzte Azure-Ressourcen identifizieren und anschließend Maßnahmen zur Kostenreduzierung ergreifen.

Sehen Sie sich das Video zum [Optimieren von Cloudinvestitionen in Azure Cost Management](https://www.youtube.com/watch?v=cSNPoAb-TNc) an, um mehr über die Verwendung von Advisor zur Kostenoptimierung zu erfahren. Weitere Videos finden Sie im [YouTube-Kanal zu Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/cSNPoAb-TNc]

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Anzeigen von Empfehlungen zur Kostenoptimierung, um mögliche ineffiziente Nutzungen aufzudecken
> * Umsetzen einer Empfehlung, um die Größe eines virtuellen Computers auf eine kostengünstigere Option umzustellen
> * Überprüfen der Aktion, um sicherzustellen, dass die Größe des virtuellen Computers erfolgreich geändert wurde

## <a name="prerequisites"></a>Voraussetzungen
Empfehlungen sind für eine Vielzahl von Bereichen und Azure-Kontotypen verfügbar. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](understand-cost-mgt-data.md). Zum Aufrufen von Kostendaten benötigen Sie für einen oder mehrere der folgenden Bereiche mindestens Lesezugriff. Weitere Informationen zu Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).

- Subscription
- Resource group

Bei einem neuen Abonnement können Cost Management-Features nicht sofort genutzt werden. Es kann bis zu 48 Stunden dauern, bis Sie alle Cost Management-Features verwenden können. Sie benötigen außerdem aktive virtuelle Computer, die schon mindestens 14 Tage aktiv sind.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com/) beim Azure-Portal an.

## <a name="view-cost-optimization-recommendations"></a>Anzeigen von Empfehlungen zur Kostenoptimierung

Um Empfehlungen zur Kostenoptimierung für ein Abonnement anzuzeigen, öffnen Sie den gewünschten Bereich im Azure-Portal, und wählen Sie **Advisor-Empfehlungen** aus.

Um Empfehlungen zur Kostenoptimierung für ein Abonnement anzuzeigen, öffnen Sie im Azure-Portal den gewünschten Bereich und wählen Sie **Advisor-Empfehlungen** aus. In **Bereich** können Sie zu einem anderen Bereich wechseln, z.B. zu einer Verwaltungsgruppe. Wählen Sie im Menü **Advisor-Empfehlungen** aus. Weitere Informationen zu Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).

![Im Azure-Portal gezeigte Cost Management Advisor-Empfehlungen](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Die Liste der Empfehlungen identifiziert Nutzungsineffizienzen oder zeigt Kaufempfehlungen, die Ihnen helfen können, zusätzliches Geld zu sparen. Die Summe **Potenzielle jährliche Einsparungen** zeigt den Gesamtbetrag, den Sie sparen können, wenn Sie alle Ihre VMs, die den Empfehlungsregeln entsprechen, herunterfahren oder freigeben. Wenn Sie sie nicht herunterfahren möchten, sollten Sie eine Größenänderung auf eine kostengünstigere VM-SKU in Betracht ziehen.

Die Kategorie **Auswirkungen** und die Kategorie **Potenzielle jährliche Einsparungen** sollen helfen, Empfehlungen zu identifizieren, die das Potenzial haben, so viel wie möglich zu sparen.

Empfehlungen mit starken Auswirkungen:
- [Erwerben reservierter VM-Instanzen, um gegenüber dem nutzungsbasierten Modell Kosten einzusparen](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimieren der Kosten für virtuelle Computer durch Ändern der Größe oder Herunterfahren von zu gering ausgelasteten Instanzen](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Verwenden von Storage Standard zum Speichern von Momentaufnahmen für verwaltete Datenträger](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Empfehlungen mit mittleren Auswirkungen:
- [Löschen fehlerhafter Azure Data Factory-Pipelines](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Kostensenkung durch die Beseitigung nicht bereitgestellter ExpressRoute-Verbindungen](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Verringern der Kosten durch Löschen oder Neukonfigurieren von Gateways für virtuelle Netzwerke im Leerlauf](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Umsetzen einer Empfehlung

Azure Advisor überwacht die Verwendung Ihrer virtuellen Computer sieben Tage lang und ermittelt nicht ausgelastete virtuelle Computer. Virtuelle Computer, bei denen an mindestens vier Tagen die CPU-Auslastung unter fünf Prozent und die Netzwerklast unter sieben MB lag, gelten als virtuelle Computer mit geringer Auslastung.

Die CPU-Auslastung von 5 % oder weniger ist die Standardeinstellung, aber Sie können die Einstellungen anpassen. Weitere Informationen zum Anpassen der Einstellung finden Sie unter [Konfigurieren der Regel für die durchschnittliche CPU-Auslastung oder der Empfehlung für VMs mit geringer Auslastung](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Obwohl einige Szenarien zielgerichtet eine geringe Auslastung bewirken, können Sie häufig Kosten sparen, indem Sie die Größe des virtuellen Computers auf eine kostengünstigere Größe verkleinern. Die tatsächlichen Einsparungen können variieren, wenn Sie eine Maßnahme zur Größenänderung auswählen. Schauen wir uns ein Beispiel für die Größenänderung bei einem virtuellen Computer an.

Klicken Sie in der Liste der Empfehlungen auf **Größe eines nicht ausgelasteten virtuellen Computers ändern oder virtuellen Computer herunterfahren**. Wählen Sie in der Liste der Kandidaten für virtuelle Computer einen virtuellen Computer aus, dessen Größe Sie ändern möchten, und klicken Sie dann auf den virtuellen Computer. Die Details des virtuellen Computers werden angezeigt, sodass Sie die Nutzungsmetriken überprüfen können. Der Wert der **potenziellen jährliche Einsparungen** gibt an, was Sie sparen können, wenn Sie die VM herunterfahren oder entfernen. Mit einer Größenänderung einer VM können Sie wahrscheinlich Geld sparen, aber Sie werden nicht den vollen Betrag der möglichen jährlichen Einsparungen erzielen.

![Beispiel von Empfehlungsdetails](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Überprüfen Sie in den VM-Details die Auslastung des virtuellen Computers, um sicherzustellen, dass es sich um einen geeigneten Kandidaten für die Größenänderung handelt.

![Beispiel von VM-Details mit Verlauf der Nutzung](./media/tutorial-acm-opt-recommendations/vm-details.png)

Beachten Sie die aktuelle VM-Größe. Nachdem Sie überprüft haben, ob die Größe des virtuellen Computers geändert werden soll, schließen Sie die VM-Details, sodass wieder die Liste der virtuellen Computer angezeigt wird.

Wählen Sie in der Liste der Kandidaten, die heruntergefahren werden sollen oder deren Größe geändert werden soll, die Option **Von _&lt;FromVirtualMachineSKU&gt;_ auf _&lt;ToVirtualMachineSKU&gt;_ ändern** aus.
![Beispielempfehlung mit Option zum Ändern der Größe des virtuellen Computers](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Als Nächstes wird eine Liste der verfügbaren Optionen für die Größenänderung angezeigt. Wählen Sie diejenige, die für Ihr Szenario die beste Leistung und Kosteneffizienz bietet. Im folgenden Beispiel ändert die gewählte Option die Größe von **Standard_D8s_v3** auf **Standard_D2s_v3**.

![Beispielliste der verfügbaren VM-Größen, in der Sie eine Größe wählen können](./media/tutorial-acm-opt-recommendations/choose-size.png)

Nachdem Sie eine geeignete Größe ausgewählt haben, klicken Sie auf **Größe ändern**, um die Aktion zum Ändern der Größe zu starten.

Die Größenänderung erfordert einen Neustart eines aktiv laufenden virtuellen Computers. Wenn sich der virtuelle Computer in einer Produktionsumgebung befindet, empfehlen wir Ihnen, den Vorgang zur Größenänderung außerhalb der Geschäftszeiten durchzuführen. Durch eine Planung des Neustarts kann Unterbrechungen durch eine vorübergehende Nichtverfügbarkeit reduzieren werden.

## <a name="verify-the-action"></a>Überprüfen der Aktion

Wenn die Größenänderung der VM erfolgreich abgeschlossen ist, wird eine Azure-Benachrichtigung angezeigt.

![Benachrichtigung zur erfolgreichen Änderung der Größe eines virtuellen Computers](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Anzeigen von Empfehlungen zur Kostenoptimierung, um mögliche ineffiziente Nutzungen aufzudecken
> * Umsetzen einer Empfehlung, um die Größe eines virtuellen Computers auf eine kostengünstigere Option umzustellen
> * Überprüfen der Aktion, um sicherzustellen, dass die Größe des virtuellen Computers erfolgreich geändert wurde

Wenn Sie den Artikel über bewährte Methoden im Bereich Cost Management noch nicht gelesen haben, finden Sie hier allgemeine Anleitungen und Grundsätze, die Sie für eine bessere Kostenverwaltung berücksichtigen sollten.

> [!div class="nextstepaction"]
> [Bewährte Methoden für Cost Management](cost-mgt-best-practices.md)
