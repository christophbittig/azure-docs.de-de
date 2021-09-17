---
title: Empfohlene Richtlinien für Azure-Dienste.
description: Hier wird beschrieben, wie Sie empfohlene Richtlinien für Azure-Dienste wie Azure Virtual Machines finden und anwenden können.
ms.date: 08/17/2021
ms.topic: conceptual
ms.custom: generated
ms.openlocfilehash: 06f68dec2c2a1ec8fa717dc721916c070a653b10
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351323"
---
# <a name="recommended-policies-for-azure-services"></a>Empfohlene Richtlinien für Azure-Dienste.

Neue Kunden von Azure Policy suchen oft nach gemeinsamen Richtliniendefinitionen zur Verwaltung und Steuerung ihrer Ressourcen. **Empfohlene Richtlinien** von Azure Policy bietet eine entsprechende Liste von gemeinsamen Richtliniendefinitionen für die ersten Schritte. Die **empfohlenen Richtlinien** für unterstützte Ressourcen sind in die Portalumgebung für diese Ressource eingebettet.

Weitere Azure Policy-Integrationen finden Sie unter [Integrierte Azure Policy-Richtliniendefinitionen](../samples/built-in-policies.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Die **empfohlenen Richtlinien** für [Azure Virtual Machines](../../../virtual-machines/index.yml) befinden sich auf der Seite **Übersicht** für virtuelle Computer und auf der Registerkarte **Funktionen**. Wählen Sie auf der Karte _Azure Policy_ den Text „Not configured“ (Nicht konfiguriert) oder „# assigned“ (zugewiesene Anzahl) aus, um einen Seitenbereich mit den empfohlenen Richtlinien zu öffnen. Alle Richtliniendefinitionen, die bereits einem Bereich zugewiesen sind, zu dem der virtuelle Computer gehört, werden abgeblendet dargestellt. Wählen Sie die empfohlenen Richtlinien, die auf diesen virtuellen Computer angewendet werden sollen, und dann **Richtlinien zuweisen** aus, um für jede eine Zuweisung zu erstellen.

Wenn eine Organisation mit der [Anordnung ihrer Ressourcen und der Ressourcenhierarchie](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions) die Einsatzreife erlangt, empfiehlt es sich, bei diesen Richtlinienzuweisungen von „Eine pro Ressource“ auf die Abonnement- oder [Verwaltungsgruppenebene](../../management-groups/index.yml) überzugehen.

### <a name="azure-virtual-machines-recommended-policies"></a>Empfohlene Richtlinien für Azure Virtual Machines

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[VMs überwachen, für die keine Notfallwiederherstellung konfiguriert ist](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Hiermit werden VMs überwacht, für die keine Notfallwiederherstellung konfiguriert ist. Weitere Informationen zur Notfallwiederherstellung finden Sie unter [https://aka.ms/asr-doc](../../../site-recovery/index.yml). |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Diese Richtlinie überwacht virtuelle Computer, die keine verwalteten Datenträger verwenden. |Überwachung |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[Azure Backup muss für Virtual Machines aktiviert sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Schützen Sie Ihre Azure Virtual Machines-Instanzen, indem Sie Azure Backup aktivieren. Azure Backup ist eine sichere und kostengünstige Lösung zum Schutz von Daten für Azure. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](./effects.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
