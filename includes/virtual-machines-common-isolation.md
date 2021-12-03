---
title: Datei einfügen
description: include file
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 10/20/2021
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: b5d51a00a35fa34236a62c48f1ee01402ca2f1c9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521040"
---
Azure Compute bietet VM-Größen, die für einen bestimmten Hardwaretyp isoliert und für einen einzelnen Kunden bestimmt sind. Die Größen isolierter VMs gelten für eine bestimmte Hardwaregeneration und sind veraltet, sobald die Hardwaregeneration außer Betrieb gesetzt wird.

Die Größen isolierter VMs eignen sich am besten für Workloads, die ein hohes Maß an Isolation von den Workloads anderer Kunden erfordern, beispielsweise um Compliance zu erzielen und gesetzliche Anforderungen zu erfüllen.  Durch die Verwendung isolierter Größen wird sichergestellt, dass Ihr virtueller Computer als einziger in der jeweiligen Serverinstanz ausgeführt wird. 


Da isolierte VMs groß sind, können Kunden die Ressourcen dieser VMs zudem unterteilen, indem sie die [Azure-Support für geschachtelte VMs](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) verwenden.

Zu den aktuellen Angebote isolierter virtueller Computer zählen Folgende:
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_E104i_v5
* Standard_E104is_v5
* Standard_E104id_v5
* Standard_E104ids_v5
* Standard_F72s_v2
* Standard_M128ms
* Standard_DC8_v2


> [!NOTE]
> Die Größen von isolierten VMs haben eine durch die Hardware begrenzte Lebensdauer. Ausführliche Informationen hierzu finden Sie weiter unten.

## <a name="deprecation-of-isolated-vm-sizes"></a>Eingestellte Unterstützung von Größen isolierter VMs

Die Größen von isolierten VMs haben eine durch die Hardware begrenzte Lebensdauer. Azure wird 12 Monate vor dem offiziellen Datum für „Veraltet ab“ der Größen entsprechende Erinnerungen ausgeben und ein aktualisiertes isoliertes Angebot für Sie bereitstellen.

| Size | Datum für die Außerbetriebnahme der Isolation | 
| --- | --- |
| Standard_DS15_v2  | 15. Mai 2021      |
| Standard_D15_v2   | 15. Mai 2021      |
| Standard_G5       | 15. Februar 2022 |
| Standard_GS5      | 15. Februar 2022 |
| Standard_E64i_v3  | 15. Februar 2022 |
| Standard_E64is_v3 | 15. Februar 2022 |
| Standard_DC8_v2   | 15. Februar 2022 |


## <a name="faq"></a>Häufig gestellte Fragen
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>F: Wird die Unterstützung der Größe eingestellt oder nur ihr Feature „Isolation“?
**A**: Derzeit wird nur das Feature „Isolation“ der VM-Größen außer Kraft gesetzt. Die veralteten isolierten Größen bleiben im nicht isolierten Zustand bestehen. Wenn keine Isolation erforderlich ist, muss nichts unternommen werden und die VM funktioniert weiterhin erwartungsgemäß.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>F: Gibt es eine Ausfallzeit, wenn meine VM auf nicht isolierte Hardware verschoben wird?
**A:** Wenn keine Isolation erforderlich ist, muss nichts unternommen werden und es treten keine Ausfallzeiten auf. Wenn dagegen eine Isolation erforderlich ist, finden Sie in unserer Ankündigung die empfohlene Ersatzgröße. Bei der Auswahl der Ersatzgröße müssen unsere Kunden die Größe ihrer VMs ändern.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>F: Gibt es Kostenänderungen beim Umstieg auf eine nicht isolierte VM?
**A:** Nein

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>F: Wann werden die anderen isolierten Größen außer Betrieb genommen?
**A:** Wir werden 12 Monate vor der offiziellen Einstellung der Unterstützung der Größen von isolierten VMs Erinnerungen bereitstellen. Unsere neueste Ankündigung umfasst die Einstellung des Features „Isolation“ von Standard_G5, Standard_GS5, Standard_E64i_v3 und Standard_E64i_v3.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>F: Ich bin ein Azure Service Fabric-Kunde, der die Dauerhaftigkeitsstufen Silver oder Gold verwendet. Wirkt sich diese Änderung auf mich aus?
**A:** Nein. Die von den [Dauerhaftigkeitsstufen](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) von Service Fabric bereitgestellten Garantien werden auch nach dieser Änderung weiterhin funktionieren. Wenn Sie aus anderen Gründen physische Hardwareisolierung benötigen, müssen Sie möglicherweise dennoch eine der oben beschriebenen Aktionen ausführen. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>F: Welche Meilensteine gibt es bei der Einstellung der Unterstützung der Isolation von D15_v2 bzw. DS15_v2? 
**A:** 
 
| Date | Aktion |
|---|---| 
| 15. Mai 2020<sup>1</sup> | Ankündigung der Einstellung der D/DS15_v2-Isolation| 
| 15. Mai 2021 | D/DS15_v2-Isolationsgarantie entfernt| 

<sup>1</sup> Bestehende Kunden, die diese Größen verwenden, erhalten eine Ankündigungs-E-Mail mit detaillierten Anweisungen zu den nächsten Schritten.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>F: Was sind die Meilensteine für die Einstellung der Isolation von G5, Gs5, E64i_v3 und E64is_v3? 
**A:** 
 
| Date | Aktion |
|---|---|
| 15. Februar 2021<sup>1</sup> | Ankündigung der Einstellung der G5/GS5/E64i_v3/E64is_v3-Isolation |
| 28. Februar 2022 | G5/GS5/E64i_v3/E64is_v3-Isolationsgarantie entfernt |

<sup>1</sup> Bestehende Kunden, die diese Größen verwenden, erhalten eine Ankündigungs-E-Mail mit detaillierten Anweisungen zu den nächsten Schritten.  

## <a name="next-steps"></a>Nächste Schritte

Kunden können auch die Ressourcen dieser isolierten virtuellen Computer weiter unterteilen, indem sie die [Azure-Unterstützung für geschachtelte virtuelle Computer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) verwenden.
