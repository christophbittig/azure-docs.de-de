---
title: Abrufen der Kostenanalyse und Festlegen von Budgets für Azure Batch
description: Erfahren Sie, wie Sie eine Kostenanalyse erhalten, ein Budget festlegen und die Kosten für die zugrunde liegenden Computeressourcen und Softwarelizenzen reduzieren, die für die Ausführung Ihrer Batch-Workloads verwendet werden.
ms.topic: how-to
ms.date: 10/04/2021
ms.openlocfilehash: 3590aad01216e5ca7401ce1edd55efb88bf26848
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219203"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Abrufen der Kostenanalyse und Festlegen von Budgets für Azure Batch

In diesem Thema lernen Sie die Kosten kennen, die im Zusammenhang mit Azure Batch anfallen können. Außerdem erfahren Sie, wie Sie ein Budget für einen Batch-Pool oder ein Batch-Konto festlegen und wie Sie die Kosten für Batch-Workloads senken.

## <a name="understand-costs-associated-with-batch-resources"></a>Grundlegendes zu den Batch-Ressourcen verbundenen Kosten

Für Azure Batch selbst fallen keine Kosten an, für die zugrunde liegenden Computeressourcen und Softwarelizenzen, die zum Ausführen von Batch-Workloads verwendet werden, können jedoch Gebühren berechnet werden. Kosten können für VMs (virtuelle Computer) in einem Pool, für die Datenübertragung von der VM oder für in der Cloud gespeicherte Eingabe- oder Ausgabedaten anfallen.

### <a name="virtual-machines"></a>Virtuelle Computer

Virtuelle Computer sind die wichtigste Ressource, die für die Batch-Verarbeitung verwendet wird. Die Kosten für die Verwendung von VMs für Batch wird basierend auf dem Typ, der Menge und der Nutzungsdauer berechnet. Zu den VM-Abrechnungsoptionen gehören die [nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) und die [Reservierung](../cost-management-billing/reservations/save-compute-costs-reservations.md) (Zahlung im Voraus). Beide Optionen für die Bezahlung haben bestimmte Vorteile, die von Ihrer Computeworkload abhängen, und beide wirken sich anders auf Ihre Rechnung aus.

Jede VM in einem Pool, der mit [Konfiguration des virtuellen Computers](nodes-and-pools.md#virtual-machine-configuration) erstellt wurde, verfügt über einen zugeordneten Betriebssystemdatenträger, für den von Azure verwaltete Datenträger verwendet werden. Von Azure verwaltete Datenträger weisen zusätzliche Kosten auf, und auch für andere Datenträger-Leistungsstufen gelten noch andere Kosten.

### <a name="storage"></a>Storage

Bei der Bereitstellung von Anwendungen auf Batch-Knoten (VMs) mit [Anwendungspaketen](batch-application-packages.md) werden Ihnen die Azure Storage-Ressourcen in Rechnung gestellt, die von Ihren Anwendungspaketen verbraucht wurden. Darüber hinaus wird Ihnen die Speicherung von Eingabe- oder Ausgabedateien berechnet, z. B. Ressourcendateien und andere Protokolldaten.

Im Allgemeinen liegen die Kosten für Speicherdaten in Verbindung mit Batch deutlich unter den Kosten für Computeressourcen.

### <a name="networking-resources"></a>Netzwerkressourcen

In Batch-Pools werden Netzwerkressourcen verwendet, von denen einige Kosten verursachen. Insbesondere werden für Pools vom Typ „VM-Konfiguration“ Standard-Lastenausgleichsmodule verwendet, für die statische IP-Adressen erforderlich sind. Die von Batch verwendeten Lastenausgleichsmodule sind für im Modus „Benutzerabonnement“ konfigurierte [Konten](accounts.md#batch-accounts) sichtbar, jedoch nicht für Konten im Modus „Batch-Dienst“.

Für Standard-Lastenausgleichsmodule fallen Gebühren für alle Daten an, die für Batch-Pool-VMs übergeben werden. Für einige Batch-APIs, mit denen Daten aus Poolknoten (z. B. Task/Knotendatei abrufen), Taskanwendungspaketen, Ressourcen-/Ausgabedateien und Containerimages abgerufen werden, fallen ebenfalls Gebühren an.

### <a name="additional-services"></a>Zusätzliche Dienste

Je nach den Diensten, die Sie mit Ihrer Batch-Lösung nutzen, fallen unter Umständen weitere Gebühren an. Mit dem [Preisrechner](https://azure.microsoft.com/pricing/calculator/) können Sie die Kosten für die einzelnen zusätzlichen Dienste ermitteln. Zu den häufig mit Batch verwendeten Diensten, die mit Kosten verbunden sein können, gehören:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- VMs mit Grafikanwendungen

## <a name="view-cost-analysis-and-create-budgets"></a>Anzeigen der Kostenanalyse und Erstellen von Budgets

[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) ermöglicht Ihnen das Planen, Analysieren und Verringern Ihrer Ausgaben, sodass Sie von Ihren Cloudinvestitionen maximal profitieren. Die Nutzungskosten sind für alle Azure-Dienste verfügbar, einschließlich Azure Batch. Sie können Batch-Kosten anzeigen und filtern, zukünftige Kosten prognostizieren und Ausgabenlimits sowie Warnungen für den Fall festlegen, dass die jeweiligen Grenzwerte erreicht werden.

Im Azure-Portal können Sie Budgets und Ausgabenwarnungen für Ihre Batch-Pools oder Ihr Batch-Konto erstellen. Budgets und Warnungen sind hilfreich, um Beteiligte über Risiken der Ausgabenüberschreitung zu benachrichtigen. Es ist aber durchaus möglich, eine Verzögerung bei der Ausgabe von Warnungen einzurichten und ein Budget geringfügig zu überschreiten.

Der folgende Screenshot zeigt ein Beispiel für die Ansicht **Kostenanalyse** für ein Abonnement. Die Ansicht ist so gefiltert, dass nur die akkumulierten Kosten angezeigt werden, die allen Batch-Konten zugeordnet sind. In den unteren Diagrammen wird gezeigt, wie die Gesamtkosten für den ausgewählten Zeitraum nach verbrauchten Diensten, Standorten und Verbrauchseinheiten kategorisiert werden können. Auch wenn dies ein Beispiel ist und nicht die für Ihre Abonnements anfallenden Kosten widerspiegelt, ist es typisch, dass die höchsten Kosten für die VMs entstehen, die für Batch-Poolknoten zugeordnet werden.

:::image type="content" source="media/batch-budget/subscription-cost-analysis.png" alt-text="Screenshot: Kostenanalyse im Azure-Portal für alle Batch-Konten in einem Abonnement":::

Eine weitere Ebene der Kostenanalysedetails können Sie durch Angeben eines **Ressourcenfilters** erhalten. Für Batch-Konten entsprechen diese Werte dem Batch-Kontonamen plus dem Poolnamen. So können Sie die Kosten für einen bestimmten Pool, mehrere Pools oder ein oder mehrere Konten anzeigen.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Anzeigen der Kostenanalyse für einen Batch-Pool

#### <a name="batch-service-pool-allocation-mode"></a>Poolzuordnungsmodus für den Batch-Dienst

Gehen Sie für Batch-Konten, die mit dem Poolzuordnungsmodus für den Batch-Dienst erstellt wurden, folgendermaßen vor:

1. Navigieren Sie im Azure-Portal zu **Cost Management + Billing**.
1. Wählen Sie im Abschnitt **Abrechnungsbereiche** Ihr Abonnement aus.
1. Wählen Sie unter **Cost Management** die Option **Kostenanalyse** aus.
1. Wählen Sie **+ Filter hinzufügen**. Wählen Sie in der ersten Dropdownliste **Ressource** aus.
1. Wählen Sie in der zweiten Dropdownliste den Batch-Pool aus. Nach Auswahl des Pools wird die Kostenanalyse für Ihren Pool angezeigt. Im folgenden Screenshot werden Beispieldaten gezeigt.
   :::image type="content" source="media/batch-budget/pool-cost-analysis.png" alt-text="Screenshot: Kostenanalyse eines Batch-Pools im Azure-Portal":::

In der sich ergebenden Kostenanalyse werden die Kosten des Pools und die Ressourcen angezeigt, die zu diesen Kosten beitragen. In diesem Beispiel sind die im Pool verwendeten VMs die teuerste Ressource.

> [!NOTE]
> Der Pool in diesem Beispiel verwendet die **VM-Konfiguration**, die [für die meisten Pools empfohlen](batch-pool-cloud-service-to-virtual-machine-configuration.md) wird und deren Gebühren auf der Preisstruktur für Microsoft Azure Virtual Machines basieren. Für Pools, für die die **Cloud Services-Konfiguration** verwendet wird, erfolgt die Abrechnung auf Grundlage der Preisstruktur, die auf der Seite mit den Cloud Services-Preisen angegeben ist.

[Tags](../azure-resource-manager/management/tag-resources.md) können Batch-Konten zugeordnet werden, sodass sie zur weiteren Kostenfilterung verwendet werden können. Beispielsweise lassen sich Projekt-, Benutzer- oder Gruppeninformationen anhand von Tags einem Batch-Konto zuordnen. Tags können Batch-Pools derzeit nicht zugeordnet werden.

#### <a name="user-subscription-pool-allocation-mode"></a>Poolzuordnungsmodus für Benutzerabonnements

Gehen Sie für Batch-Konten, die mit dem Poolzuordnungsmodus für Benutzerabonnements erstellt wurden, folgendermaßen vor:

1. Navigieren Sie im Azure-Portal zu **Cost Management + Billing**.
1. Wählen Sie im Abschnitt **Abrechnungsbereiche** Ihr Abonnement aus.
1. Wählen Sie unter **Cost Management** die Option **Kostenanalyse** aus.
1. Wählen Sie **+ Filter hinzufügen**. Wählen Sie in der ersten Dropdownliste **Tag** aus.
1. Wählen Sie in der zweiten Dropdownliste **Poolname** aus.
1. Wählen Sie in der dritten Dropdownliste den Batch-Pool aus. Nach Auswahl des Pools wird die Kostenanalyse für Ihren Pool angezeigt. Im folgenden Screenshot werden Beispieldaten gezeigt.
   :::image type="content" source="media/batch-budget/user-subscription-pool.png" alt-text="Screenshot: Kostenanalyse eines Batch-Pools für Benutzerabonnements im Azure-Portal":::

Hinweis: Wenn Sie Kostendaten für alle Pools im Batch-Konto eines Benutzerabonnements anzeigen möchten, können Sie in der zweiten Dropdownliste **batchaccountname** und in der dritten Dropdownliste den Namen Ihres Batch-Kontos auswählen. 

> [!NOTE]
> Pools, die von Batch-Konten des Benutzerabonnements erstellt wurden, werden nicht unter dem Filter **Ressource** angezeigt. Ihre Nutzung wird jedoch dennoch aufgeführt, wenn unter dem Dienstnamen nach „Virtual Machines“ gefiltert wird.

### <a name="create-a-budget-for-a-batch-pool"></a>Erstellen eines Budgets für einen Batch-Pool

Budgets können erstellt und Kostenwarnungen ausgegeben werden, wenn verschiedene Prozentsätze eines Budgets erreicht werden, z. B. 60 %, 80 % und 100 %. Für die Budgets kann mindestens ein Filter angegeben werden, sodass Batch-Kontokosten mit unterschiedlicher Granularität überwacht und Warnungen ausgegeben werden können.

1. Wählen Sie auf der Seite **Kostenanalyse** die Option **Budget: Keines** aus.
1. Wählen Sie **Neues Budget erstellen >** aus.
1. Konfigurieren Sie in dem daraufhin angezeigten Fenster ein Budget speziell für Ihren Pool. Weitere Informationen finden Sie im [Tutorial: Erstellen und Verwalten von Azure-Budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Minimieren der mit Azure Batch verbundenen Kosten

Abhängig von Ihrem Szenario möchten Sie Ihre Kosten eventuell so weit wie möglich reduzieren. Nutzen Sie eine oder mehrere dieser Strategien, um die Effizienz Ihrer Workloads zu maximieren und die möglichen Kosten zu senken.

### <a name="reduce-pool-node-use"></a>Reduzieren der Poolknotennutzung

Die höchsten Kosten für die Batch-Nutzung werden in der Regel von den VMs verursacht, die für Poolknoten zugeordnet werden. Bei VM-Konfigurationspools können auch die zugeordneten verwalteten Datenträger, die als VM-Betriebssystemdatenträger verwendet werden, erheblich zu den Kosten beitragen.

Bewerten Sie Ihre Batch-Anwendung, um zu ermitteln, ob Poolknoten von Auftragsaufgaben optimal ausgelastet werden oder ob sich Poolknoten länger als erwartet im Leerlauf befinden. Möglicherweise können Sie die Anzahl zugeordneter Poolknoten verringern, die Hochskalierungsrate für Poolknoten reduzieren oder die Herunterskalierungsrate erhöhen, um die Auslastung zu steigern.

Neben der benutzerdefinierten Überwachung können [Batch-Metriken ](batch-diagnostics.md#view-batch-metrics) dabei helfen, Knoten zu identifizieren, die zwar zugeordnet sind, sich aber im Leerlauf befinden. Sie können eine Metrik für die meisten Poolknotenzustände auswählen, die Sie anhand von Batch-Überwachungsmetriken im Azure-Portal anzeigen möchten. Beispielsweise können „Anzahl von Knoten im Leerlauf“ und „Anzahl ausgeführter Knoten“ angezeigt werden, um einen Hinweis auf die Nutzung der Poolknoten zu erhalten.

### <a name="ensure-pool-nodes-are-able-to-run-tasks"></a>Sicherstellen, dass Poolknoten Aufgaben ausführen können

Die für einen Pool aufgelisteten zugeordneten Knoten verursachen normalerweise Kosten. Es ist jedoch möglich, dass sich Poolknoten in einem Zustand befinden, in dem keine Aufgaben ausgeführt werden können, z. B. „unusable“ oder „starttaskfailed“. Batch-APIs oder -Metriken können verwendet werden, um diese Kategorie von VMs zu überwachen und zu erkennen. Anschließend kann der Grund für diese Zustände ermittelt werden, und Sie können Korrekturmaßnahmen ergreifen, um diese fehlerhaften Knoten zu reduzieren oder zu beseitigen.

### <a name="use-the-right-pool-node-vm-size"></a>Verwenden der richtigen VM-Größe für den Poolknoten

Stellen Sie sicher, dass die richtige VM-Größe verwendet wird, damit die VMs bei der Ausführung von Aufgaben gut ausgelastet sind und gleichzeitig die Leistung bereitstellen, die zum Ausführen Ihrer Auftragsaufgaben in der erforderlichen Zeit erforderlich ist. Poolknoten-VMs sind in einigen Situationen möglicherweise nicht vollständig ausgelastet, z. B. bei geringer CPU-Auslastung. Sie können Kosten sparen, indem Sie eine VM-Größe zu einem niedrigeren Preis auswählen.

Zum Ermitteln der VM-Auslastung können Sie sich bei einem Knoten anmelden, wenn Sie Aufgaben zum Anzeigen von Leistungsdaten ausführen oder Leistungsdaten anhand von [Überwachungsfunktionen](monitoring-overview.md) wie Application Insights von Poolknoten abrufen.

### <a name="use-pool-slots-to-reduce-node-requirements"></a>Verwenden von Poolslots zum Reduzieren von Knotenanforderungen

Für einen Pool können mehrere Aufgabenslots angegeben werden, sodass die entsprechende Anzahl von Aufgaben auf den einzelnen Knoten parallel ausgeführt werden kann. Anhand von Poolaufgabenslots lässt sich die Anzahl der in einem Pool verwendeten Knoten reduzieren, indem größere VM-Größen ausgewählt und mehrere Aufgaben parallel auf dem Knoten ausgeführt werden. So wird ein gute Auslastung des Knotens sichergestellt. Wenn Knoten nicht ausgelastet sind, kann die Auslastung anhand von Slots erhöht werden. Für eine Singlethread-Aufgabenanwendung könnte beispielsweise ein Slot pro Kern konfiguriert werden. Es ist auch möglich, mehr Slots als Kerne zu verwenden. Dies ist beispielsweise sinnvoll, wenn die Anwendung durch das Warten auf die Rückgabe von Aufrufen externer Dienste erheblich blockiert wird.

Durch Festlegen von [`taskSchedulingPolicy`](/rest/api/batchservice/pool/add#taskschedulingpolicy) auf `pack` können Sie eine optimale Nutzung der VMs sicherstellen, da bei der Skalierung Knoten, die keine Aufgaben ausführen, leichter entfernt werden können.

### <a name="use-low-priority-virtual-machines"></a>Verwenden Sie virtuelle Computer mit niedriger Priorität

[VMs mit niedriger Priorität](batch-low-pri-vms.md) führen zu einer Reduzierung der Kosten von Batch-Workloads, indem in Azure überschüssige Rechenkapazität genutzt wird. Wenn Sie in Ihren Pools VMs mit niedriger Priorität angeben, nutzt Batch diese überschüssige Kapazität zum Ausführen Ihrer Workload. Wenn Sie VMs mit niedriger Priorität anstelle von dedizierten VMs verwenden, kann dies zu erheblichen Kosteneinsparungen führen. Beachten Sie, dass VMs mit niedriger Priorität nicht für alle Workloads geeignet sind, weil möglicherweise keine Kapazität für die Zuordnung zur Verfügung steht oder weil andere Vorgänge vorrangig verarbeitet werden.

### <a name="use-ephemeral-os-disks"></a>Verwendung ephemerer OS-Festplatten

Poolknoten verwenden standardmäßig verwaltete Datenträger, die Kosten verursachen. VM-Konfigurationspools können in einigen VM-Größen [kurzlebige Betriebssystemdatenträger](create-pool-ephemeral-os-disk.md) verwenden, bei denen der Betriebssystemdatenträger im VM-Cache oder auf einer temporären SSD erstellt wird, um zusätzliche Kosten im Zusammenhang mit verwalteten Datenträgern zu vermeiden.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Kaufen von Reservierungen für VM-Instanzen

Falls Sie Batch längere Zeit nutzen möchten, können Sie bei den Kosten für die VMs sparen, indem Sie für Ihre Workloads [Azure-Reservierungen](../cost-management-billing/reservations/save-compute-costs-reservations.md) verwenden. Reservierungsraten sind erheblich niedriger als die Raten bei der nutzungsbasierten Bezahlung. Für VM-Instanzen, die ohne Reservierung verwendet werden, wird die Rate für die nutzungsbasierte Bezahlung berechnet. Wenn Sie eine Reservierung erwerben, wird der Reservierungsrabatt angewandt. Bei einer Verpflichtung zu Ein- oder Dreijahresplänen für [VM-Instanzen](../virtual-machines/prepay-reserved-vm-instances.md) werden erhebliche Rabatte auf die VM-Nutzung angewendet, auch für [VMs, die über Batch-Pools genutzt werden](../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).

Wichtiger Hinweis: Der Reservierungsrabatt erlischt, wenn er nicht genutzt wird.  Wenn eine Stunde lang keine entsprechenden Ressourcen verwendet werden, verlieren Sie die Reservierungsmenge für diese Stunde. Ungenutzte reservierte Stunden können nicht übertragen werden und gehen daher verloren, wenn sie nicht verwendet werden. In Batch-Workloads wird die Anzahl zugeordneter VMs häufig entsprechend der Auslastung skaliert. Daher weisen sie variierende Lasten auf, darunter auch Zeiträume, in denen keine Last vorhanden ist. Der Reservierungsbetrag muss daher sorgfältig bemessen werden, weil reservierte Stunden verloren gehen, wenn Batch-VMs unter die Reservierungsmenge herunterskaliert werden.

### <a name="use-automatic-scaling"></a>Verwenden der Autoskalierung

Bei der [Autoskalierung](batch-automatic-scaling.md) wird die Anzahl von VMs in Ihrem Batch-Pool je nach dem Bedarf des aktuellen Auftrags dynamisch skaliert. Indem der Pool basierend auf der Lebensdauer eines Auftrags skaliert wird, kann durch die Autoskalierung sichergestellt werden, dass VMs zentral hochskaliert und nur verwendet werden, wenn ein Auftrag durchgeführt werden muss. Wenn der Auftrag abgeschlossen ist oder keine Aufträge vorhanden sind, werden die VMs automatisch herunterskaliert, um Computeressourcen zu sparen. Die Skalierung ermöglicht es Ihnen, die Gesamtkosten Ihrer Batch-Lösung zu reduzieren, indem Sie nur die benötigten Ressourcen verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Azure Cost Management + Billing](../cost-management-billing/cost-management-billing-overview.md).
- Informieren Sie sich über die [Verwendung von VMs mit niedriger Priorität mit Batch](batch-low-pri-vms.md).