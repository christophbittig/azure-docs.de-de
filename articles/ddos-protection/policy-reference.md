---
title: Integrierte Richtliniendefinitionen für Azure DDoS Protection Standard
description: Auflistung der integrierten Azure Policy-Richtliniendefinitionen für Azure DDoS Protection Standard. Diese integrierten Richtliniendefinitionen bieten allgemeine Ansätze für die Verwaltung von Azure-Ressourcen.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 5519d72d4d6d33381ce4381c7fd4e65c4d94d01f
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807950"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Integrierte Azure Policy-Definitionen für Azure DDoS Protection Standard

Diese Seite ist ein Indes der integrierten [Azure Policy](../governance/policy/overview.md)-Richtliniendefinitionen für Azure DDoS Protection Standard. Weitere Azure Policy-Integrationen für andere Dienste finden Sie unter [Integrierte Azure Policy-Richtliniendefinitionen](../governance/policy/samples/built-in-policies.md).

Die Namen der einzelnen integrierten Richtliniendefinitionen sind Links zur entsprechenden Richtliniendefinition im Azure-Portal. Verwenden Sie den Link in der Spalte **Version**, um die Quelle im [Azure Policy-GitHub-Repository](https://github.com/Azure/azure-policy) anzuzeigen.

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtuelle Netzwerke müssen durch Azure DDoS Protection Standard geschützt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Schützen Sie Ihre virtuellen Netzwerke mit Azure DDoS Protection Standard vor volumetrischen Angriffen und Protokollangriffen. Weitere Informationen finden Sie unter [https://aka.ms/ddosprotectiondocs](./ddos-protection-overview.md).|Modify, Audit, Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Für öffentliche IP-Adressen in Azure DDoS Protection Standard müssen Ressourcenprotokolle aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Aktivieren Sie Ressourcenprotokolle für öffentliche IP-Adressen in Diagnoseeinstellungen, um Daten an einen Log Analytics-Arbeitsbereich zu streamen. Sie erhalten per Benachrichtigung, anhand von Berichten und Datenflussprotokollen detaillierten Einblick in Angriffsdatenverkehr und Maßnahmen zum Entschärfen von DDoS-Angriffen.|AuditIfNotExists, DeployIfNotExists, Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Integrationen im [Azure Policy-GitHub-Repository](https://github.com/Azure/azure-policy) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../governance/policy/concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../governance/policy/concepts/effects.md).
