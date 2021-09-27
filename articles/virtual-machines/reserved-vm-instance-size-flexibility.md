---
title: Flexibilität bei der VM-Größe – reservierte Azure-VM-Instanzen
description: Es wird beschrieben, für welche Größenserie ein Reservierungsrabatt gilt, wenn Sie eine reservierte VM-Instanz erwerben.
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.openlocfilehash: 7b556f485feaa1d6073802aac5aa02ad71e86856
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599048"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilität bei der VM-Größe mit reservierten VM-Instanzen

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie eine reservierte VM-Instanz erwerben, können Sie sich entscheiden, die Flexibilität bei der Instanzgröße oder die Kapazitätspriorität zu optimieren. Weitere Informationen zum Festlegen oder Ändern der Optimierungseinstellung für reservierte VM-Instanzen finden Sie unter [Ändern der Optimierungseinstellung für reservierte VM-Instanzen](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

Bei einer für Instanzgrößenflexibilität optimierten reservierten VM-Instanz kann die von Ihnen gekaufte Reservierung für die VM-Größen in derselben Gruppe der Instanzgrößenflexibilität gelten. Wenn Sie beispielsweise eine Reservierung für eine VM-Größe kaufen, die in der DSv2-Serie aufgeführt ist (z. B. Standard_DS3_v2), kann der Reservierungsrabatt für die anderen Größen gelten, die in derselben Gruppe der Instanzgrößenflexibilität aufgeführt sind:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Dieser Reservierungsrabatt gilt jedoch nicht für VM-Größen, die in anderen Gruppen der Instanzgrößenflexibilität aufgeführt sind, beispielsweise SKUs in DSv2-Serie, oberer Speicher: Standard_DS11_v2, Standard_DS12_v2 usw.

Innerhalb der Gruppe der Instanzgrößenflexibilität hängt die Anzahl von VMs, für die der Reservierungsrabatt gilt, von der VM-Größe ab, die Sie beim Kauf einer Reservierung auswählen. Sie ist auch von den Größen der VMs abhängig, die Sie ausführen. Die Spalte „Verhältnis“ stellt einen Vergleich des relativen Speicherbedarfs der einzelnen VM-Größen in dieser Gruppe der Instanzgrößenflexibilität dar. Anhand des Verhältniswerts können Sie berechnen, inwieweit der Reservierungsrabatt für die von Ihnen ausgeführten VMs gilt.

## <a name="examples"></a>Beispiele

In den folgenden Beispielen werden die Größen und Verhältnisse in der Tabelle der DSv2-Serie verwendet.

Sie kaufen eine reservierte VM-Instanz der Größe Standard_DS4_v2, für die das Verhältnis bzw. der relative Speicherbedarf im Vergleich zu den anderen Größen in der Serie 8 ist.

- Szenario 1: Sie führen acht VMs der Größe Standard_DS1_v2 mit einem Verhältnis von 1 aus. Ihr Reservierungsrabatt gilt für alle acht VMs.
- Szenario 2: Sie führen zwei VMs der Größe Standard_DS2_v2 mit einem Verhältnis von jeweils 2 aus. Außerdem führen Sie eine VM der Größe Standard_DS3_v2 mit einem Verhältnis von 4 aus. Der gesamte Speicherbedarf ist 2 + 2 + 4 = 8. Ihr Reservierungsrabatt gilt somit für alle drei VMs.
- Szenario 3: Sie führen eine VM der Größe Standard_DS5_v2 mit einem Verhältnis von 16 aus. Ihr Reservierungsrabatt gilt für die Hälfte der Computekosten dieser VM.
- Szenario 4: Führen Sie eine Standard_DS5_v2 mit einem Verhältnis von 16 aus, und erwerben Sie eine zusätzliche Standard_DS4_v2-Reservierung mit einem Verhältnis von 8. Beide Reservierungen kombinieren den Rabatt und wenden ihn auf die gesamte VM an.

Den folgenden Abschnitten können Sie entnehmen, welche Größen in derselben Gruppe einer Größenserie enthalten sind, wenn Sie eine für Flexibilität bei der Instanzgröße optimierte reservierte VM-Instanz kaufen.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Verhältnis der Instanzgrößenflexibilität für VMs 

Das CSV unten enthält die Gruppen der Instanzgrößenflexibilität, den ArmSkuName und die Verhältnisse.  

[Verhältnisse der Instanzgrößenflexibilität](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Azure behält Link und Schema bei, sodass Sie die Datei programmgesteuert verwenden können.

## <a name="view-vm-size-recommendations"></a>Anzeigen von Empfehlungen für die VM-Größe

Azure zeigt die Empfehlungen für die VM-Größe im Kaufvorgang an. Um die kleinsten Größenempfehlungen anzuzeigen, wählen Sie die Option **Gruppieren nach kleinster Größe** aus.

:::image type="content" source="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" alt-text="Screenshot, der empfohlene Mengen anzeigt." lightbox="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" :::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Was sind Azure-Reservierungen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
