---
title: Erstellen einer Skalierungsgruppe, die Azure-Spot-VMs nutzt
description: Erfahren Sie, wie Sie Azure-VM-Skalierungsgruppen erstellen, die Azure-Spot-VMs nutzen, um Kosten zu sparen.
author: mimckitt
ms.author: mimckitt
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 10/22/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 842394ed341da88fdb37ff6deb7ccdc519ac2f8e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060503"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Azure-Spot-VMs und VM-Skalierungsgruppen 

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie Azure Spot-VMs für Skalierungsgruppen verwenden, profitieren Sie von unserer ungenutzten Kapazität und erzielen erhebliche Kosteneinsparungen. Wenn die Kapazität von Azure wieder benötigt wird, werden die Azure-Spot-VM-Instanzen durch die Azure-Infrastruktur entfernt. Aus diesem Grund eignen sich Azure-Spot-VM-Instanzen hervorragend für Workloads, die Unterbrechungen tolerieren, z. B. Batchverarbeitungsaufträge, Dev/Test-Umgebungen, umfangreiche Computeworkloads und mehr.

Die verfügbare Kapazität kann abhängig von der Größe, Region, Tageszeit usw. variieren. Beim Bereitstellen von Azure-Spot-VM-Instanzen für Skalierungsgruppen wird die Instanz von Azure nur zugeordnet, wenn Kapazität verfügbar ist, für diese Instanzen aber keine SLA besteht. Eine Azure-Spot-VM-Skalierungsgruppe wird in einer einzelnen Fehlerdomäne bereitgestellt und garantiert keine Hochverfügbarkeit.

## <a name="limitations"></a>Einschränkungen

Die folgenden Größen werden für Azure-Spot-VMs nicht unterstützt:
 - B-Serie
 - Promoversionen beliebiger Größe (z. B. Promogrößen Dv2, NV, NC, H)

Mit Ausnahme von Microsoft Azure China 21ViaNet können Azure-Spot-VMs in jeder beliebigen Region bereitgestellt werden.

Folgende [Angebotstypen](https://azure.microsoft.com/support/legal/offer-details/) werden derzeit unterstützt:

-   Enterprise Agreement
-   Angebotscode für nutzungsbasierte Bezahlung (003P)
-   Gesponsert (0036P und 0136P)
- Informationen zu Cloud-Dienstanbietern (CSP) finden Sie in [Partner Center](/partner-center/azure-plan-get-started), oder indem Sie sich direkt an Ihren Partner wenden.

## <a name="pricing"></a>Preise

Die Preise für Azure-Spot-VM-Instanzen variieren je nach Region und SKU. Weitere Informationen finden Sie unter den Preisen für [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) und [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Bei der variablen Preisgestaltung können Sie einen maximalen Preis in US-Dollar (USD) mit bis zu fünf Dezimalstellen festlegen. Der Wert `0.98765` würde beispielsweise einem maximalen Preis von 0,98765 US-Dollar pro Stunde entsprechen. Wenn Sie den maximalen Preis auf `-1` festlegen, wird die Instanz nicht basierend auf dem Preis entfernt. Der Preis für die Instanz entspricht dem aktuellen Preis für Azure-Spot-VM-Instanzen oder dem Preis für eine Standardinstanz, je nachdem, welcher Preis niedriger ist, solange Kapazität und Kontingente verfügbar sind.



## <a name="eviction-policy"></a>Entfernungsrichtlinie

Wenn Sie eine Skalierungsgruppe mit Azure-Spot-VMs erstellen, können Sie die Entfernungsrichtlinie auf *Zuordnung aufheben* (Standardeinstellung) oder *Löschen* festlegen. 

Durch die Richtlinie *Zuordnung aufheben* werden die entfernten Instanzen in den Zustand „Beendet/Zuordnung aufgehoben“ versetzt, sodass Sie entfernte Instanzen erneut bereitstellen können. Es gibt jedoch keine Garantie dafür, dass die Zuordnung erfolgreich ist. Die virtuellen Computer, deren Zuordnung aufgehoben wurde, werden auf Ihr Kontingent an Skalierungsgruppeninstanzen angerechnet, und die zugrunde liegenden Datenträger werden Ihnen in Rechnung gestellt. 

Wenn die Instanzen beim Entfernen gelöscht werden sollen, legen Sie die Entfernungsrichtlinie auf *Löschen* fest. Wenn die Entfernungsrichtlinie zum Löschen festgelegt ist, können Sie neue VMs durch Heraufsetzen der Skalierungsgruppeninstanzenanzahl-Eigenschaft erstellen. Die entfernten VMs werden zusammen mit ihren zugrunde liegenden Datenträgern gelöscht, und darum fallen keine Kosten für ihre Speicherung an. Sie können auch die Funktion zur automatischen Skalierung von Skalierungsgruppen verwenden, um zu versuchen, entfernte VMs automatisch zu kompensieren, es gibt jedoch keine Garantie, dass die Zuordnung erfolgreich ist. Die Funktion für die Autoskalierung sollte nur für Azure-Spot-VM-Skalierungsgruppen verwendet werden, wenn Sie die Entfernungsrichtlinie auf „Löschen“ festlegen. So vermeiden Sie Kosten für Datenträger und das Überschreiten von Kontingentgrenzen. 

Benutzer können Benachrichtigungen in der VM über [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md) abonnieren. Dadurch werden Sie benachrichtigt, wenn Ihre virtuellen Computer entfernt werden, und Sie haben vor dem Entfernen 30 Sekunden Zeit, Aufträge abzuschließen und die VMs herunterzufahren. 

## <a name="eviction-history"></a>Entfernungsverlauf
Sie können den Preisverlauf und Entfernungsraten pro Größe in einer Region im Portal anzeigen. Wählen Sie **Preisverlauf anzeigen und Preise in Regionen in der Nähe vergleichen** aus, um eine Tabelle oder ein Diagramm der Preise für eine bestimmte Größe anzuzeigen.  Die Preis- und Entfernungsraten in den folgenden Abbildungen sind nur Beispiele. 

**Diagramm**:

:::image type="content" source="../virtual-machines/media/spot-chart.png" alt-text="Screenshot: Regionsoptionen mit Unterschied in den Preisen und Entfernungsraten als Diagramm.":::

**Tabelle**:

:::image type="content" source="../virtual-machines/media/spot-table.png" alt-text="Screenshot: Regionsoptionen mit Unterschied in den Preisen und Entfernungsraten als Tabelle.":::

## <a name="try--restore"></a>Versuchen und Wiederherstellen 

Dieses neue Feature auf Plattformebene nutzt KI, um automatisch zu versuchen, entfernte Instanzen von Azure Spot Virtual Machine-Instanzen in einer Skalierungsgruppe wiederherzustellen und so die Zielanzahl von Instanzen beizubehalten. 

Vorteile von „Testen und wiederherstellen“:
- Versuche zur Wiederherstellung von Azure-Spot-VMs, die aufgrund der Kapazität entfernt wurden
- Bei wiederhergestellten Azure-Spot-VMs wird eine längere Ausführungszeit erwartet bei einer niedrigeren Wahrscheinlichkeit der Entfernung aufgrund der Kapazität
- Längere Lebensdauer von Azure-Spot-VMs und damit längere Ausführungszeit von Workloads
- Unterstützt Virtual Machine Scale Sets bei der Einhaltung der Zielanzahl von Azure-Spot-VMs (ähnlich wie bei der Funktion zur Beibehaltung der Zielanzahl für VMs mit nutzungsbasierter Bezahlung)

„Testen und wiederherstellen“ ist in Skalierungsgruppen mit [Autoskalierung](virtual-machine-scale-sets-autoscale-overview.md) deaktiviert. Die Anzahl der VMs in der Skalierungsgruppe wird über die Regeln für die Autoskalierung gesteuert.


## <a name="placement-groups"></a>Platzierungsgruppen

Eine Platzierungsgruppe ist ein ähnliches Konstrukt wie eine Azure-Verfügbarkeitsgruppe und verfügt über eigene Fehler- und Upgradedomänen. Standardmäßig besteht eine Skalierungsgruppe aus einer einzelnen Platzierungsgruppe mit einer maximalen Größe von 100 virtuellen Computern. Wenn die Skalierungsgruppeneigenschaft `singlePlacementGroup` auf *false* festgelegt ist, kann die Skalierungsgruppe mehrere Platzierungsgruppen und bis zu 1.000 virtuelle Computer umfassen. 

> [!IMPORTANT]
> Sofern Sie nicht InfiniBand mit HPC verwenden, wird dringend empfohlen, die Skalierungsgruppeneigenschaft `singlePlacementGroup` auf *false* festzulegen, um mehrere Platzierungsgruppen für eine bessere Skalierung in der Region oder Zone zu ermöglichen. 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Bereitstellen von Azure-Spot-VMs in Skalierungsgruppen

Zum Bereitstellen von Azure-Spot-VMs in Skalierungsgruppen können Sie das neue *Priority*-Flag auf *Spot* festlegen. Für alle VMs in Ihrer Skalierungsgruppe wird „Spot“ festgelegt. Verwenden Sie zum Erstellen einer Skalierungsgruppe mit Azure-Spot-VMs eine der folgenden Methoden:
- [Azure portal](#portal)
- [Azure-Befehlszeilenschnittstelle](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure-Ressourcen-Manager-Vorlagen](#resource-manager-templates)

## <a name="portal"></a>Portal

Das Erstellen einer Skalierungsgruppe, die Azure-Spot-VMs nutzt, entspricht den Ausführungen im Artikel [Erste Schritte](quick-create-portal.md). Wenn Sie eine Skalierungsgruppe bereitstellen, können Sie das Spot-Flag, den Entfernungstyp und die Entfernungsrichtlinie festlegen. Außerdem können Sie die Option zum Wiederherstellen von Instanzen mit ![Erstellen einer Skalierungsgruppe mit Azure Spot Virtual Machines](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-1.png) aktivieren.


## <a name="azure-cli"></a>Azure CLI

Das Erstellen einer Skalierungsgruppe mit Azure-Spot-VMs entspricht den Ausführungen im Artikel [Erste Schritte](quick-create-cli.md). Fügen Sie einfach „--Priority Spot“ und `--max-price` hinzu. In diesem Beispiel verwenden wir `-1` für `--max-price`, sodass die Instanz nicht basierend auf dem Preis entfernt wird.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --eviction-policy Deallocate \
    --max-price -1 \
    --enable-spot-restore True \
    --spot-restore-timeout PT1H
```

## <a name="powershell"></a>PowerShell

Das Erstellen einer Skalierungsgruppe mit Azure-Spot-VMs entspricht den Ausführungen im Artikel [Erste Schritte](quick-create-powershell.md).
Fügen Sie einfach „-Priority Spot“ hinzu, und geben Sie `-max-price` für [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) an.

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1 `
    -EnableSpotRestore `
    -SpotRestoreTimeout 60 `
    -EvictionPolicy delete
```

## <a name="resource-manager-templates"></a>Resource Manager-Vorlagen

Das Erstellen einer Skalierungsgruppe, die Azure-Spot-VMs nutzt, entspricht den Ausführungen im Artikel „Erste Schritte“ zu [Linux](quick-create-template-linux.md) oder [Windows](quick-create-template-windows.md). 

Verwenden Sie für die Bereitstellung von Azure-Spot-VMs mithilfe einer Vorlage die `"apiVersion": "2019-03-01"` oder höher. 

Fügen Sie dem Abschnitt `"virtualMachineProfile":` in Ihrer Vorlage die Eigenschaften `priority`, `evictionPolicy`, `billingProfile` und `spotRestoryPolicy` und dem Abschnitt `"Microsoft.Compute/virtualMachineScaleSets"` die Eigenschaft `"singlePlacementGroup": false,` hinzu:

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                },
                "spotRestorePolicy": {
                  "enabled": "bool",
                  "restoreTimeout": "string"
    },
            },
```

Um die Instanz nach dem Entfernen zu löschen, ändern Sie den `evictionPolicy`-Parameter in `Delete`.


## <a name="simulate-an-eviction"></a>Simulieren einer Entfernung

Sie können die Entfernung einer Azure-Spot-VM [simulieren](/rest/api/compute/virtualmachines/simulateeviction), um zu testen, wie gut die Anwendung auf einen plötzlichen Entfernungsvorgang reagiert. 

Ersetzen Sie Folgendes durch Ihre Informationen: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` bedeutet, dass die simulierte Entfernung erfolgreich war. 

Weitere Informationen finden Sie unter [Testing a simulated eviction notification](../virtual-machines/windows/spot-powershell.md#simulate-an-eviction) (Testen einer simulierten Entfernungsbenachrichtigung).

## <a name="faq"></a>Häufig gestellte Fragen

**F:** Ist eine Azure-Spot-VM-Instanz nach der Erstellung identisch mit einer Standardinstanz?

**A:** Ja, mit der Ausnahme, dass für Azure-Spot-VMs keine SLA besteht und dass sie jederzeit entfernt werden können.


**F:** Was kann ich tun, wenn ich nach dem Entfernen immer noch Kapazität benötige?

**A:** Es wird empfohlen, anstelle von Azure-Spot-VMs Standard-VMs zu verwenden, wenn Sie die Kapazität sofort benötigen.


**F:** Wie wird das Kontingent für eine Azure-Spot-VM verwaltet?

**A:** Für Azure-Spot-VM-Instanzen und Standardinstanzen werden getrennte Kontingentpools verwendet. Das Kontingent für eine Azure-Spot-VM wird von den VMs und Skalierungsgruppeninstanzen gemeinsam genutzt. Weitere Informationen finden Sie unter [Grenzwerte für Azure-Abonnements, -Dienste und -Kontingente sowie allgemeine Beschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md).


**F:** Kann ich für eine Azure-Spot-VM zusätzliches Kontingent anfordern?

**A:** Ja, Sie können eine Anforderung zur Erhöhung Ihres Kontingents für Azure-Spot-VMs über den [Standardprozess zur Kontingentanforderung](../azure-portal/supportability/per-vm-quota-requests.md) übermitteln.


**F:** Kann ich vorhandene Skalierungsgruppen in Azure-Spot-VM-Skalierungsgruppen umwandeln?

**A:** Nein. Das `Spot`-Flag kann nur zum Zeitpunkt der Erstellung festgelegt werden.


**F:** Wenn ich für Skalierungsgruppen mit niedriger Priorität `low` verwende, muss ich ab jetzt stattdessen `Spot` verwenden?

**A:** Zum jetzigen Zeitpunkt funktionieren sowohl `low` als auch `Spot`, Sie sollten aber mit der Umstellung auf `Spot` beginnen.


**F:** Kann ich eine Skalierungsgruppe sowohl mit regulären VMs als auch mit Azure-Spot-VMs erstellen?

**A:** Nein. Eine Skalierungsgruppe kann nicht mehr als einen Prioritätstyp unterstützen.


**F:**  Kann ich die Autoskalierung mit Azure-Spot-VM-Skalierungsgruppen verwenden?

**A:** Ja, Sie können Regeln für die automatische Skalierung für Ihre Azure-Spot-VM-Skalierungsgruppe festlegen. Wenn Ihre VMs entfernt werden, kann von der Funktion für die Autoskalierung versucht werden, neue Azure-Spot-VMs zu erstellen. Denken Sie aber daran: Dieser Kapazität ist nicht garantiert. 


**F:**  Funktioniert die automatische Skalierung mit beiden Entfernungsrichtlinien (Zuordnung aufheben und löschen)?

**A:** Ja, es empfiehlt sich jedoch, die Entfernungsrichtlinie auf „Löschen“ festzulegen, wenn Sie die Autoskalierung verwenden. Der Grund hierfür ist, dass Instanzen, deren Zuordnung aufgehoben wurde, auf die Kapazität in Ihrer Skalierungsgruppe angerechnet werden. Bei Verwendung der automatischen Skalierung erreichen Sie aufgrund der entfernten Instanzen, deren Zuordnung aufgehoben wurde, die angestrebte Anzahl von Instanzen wahrscheinlich recht schnell. Außerdem kann die Spot-Entfernung Auswirkungen auf Ihre Skalierungsvorgänge haben. Beispielsweise kann es vorkommen, dass VMSS-Instanzen bei Skalierungsvorgängen aufgrund verschiedener Spot-Entfernungen unter die festgelegte Mindestanzahl fallen. 


**F:** Wo kann ich Fragen stellen?

**A:** Sie können Ihre Frage in [Q&A](/answers/topics/azure-spot.html) veröffentlichen und mit `azure-spot` markieren. 

## <a name="next-steps"></a>Nächste Schritte

Detaillierte Preisinformationen finden Sie auf der Seite [Linux VM-Skalierungsgruppen – Preise ](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/).
