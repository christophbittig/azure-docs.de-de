---
title: Integrierte Richtliniendefinitionen für Azure Security Center
description: Hier finden Sie eine Liste der integrierten Azure Policy-Richtliniendefinitionen für Azure Security Center. Diese integrierten Richtliniendefinitionen bieten allgemeine Ansätze für die Verwaltung von Azure-Ressourcen.
ms.date: 10/11/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 55ef6c25cd7d40f407c2036bb4c58794f0fe239b
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807274"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Integrierte Azure Policy-Definitionen für Azure Security Center

Diese Seite enthält einen Index der integrierten [Azure Policy](../governance/policy/overview.md)-Richtliniendefinitionen im Zusammenhang mit Azure Security Center. Die folgenden Gruppierungen von Richtliniendefinitionen sind verfügbar:

- Die Gruppe [Initiativen](#azure-security-center-initiatives) listet die Definitionen der Azure Policy-Initiativen in der Kategorie „Security Center“ auf.
- In der Gruppe [Standardinitiative](#azure-security-center-initiatives) werden alle Azure Policy-Definitionen aufgeführt, die Teil der Security Center-Standardinitiative [Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction) sind. Diese von Microsoft erstellte, weit verbreitete Benchmark basiert auf Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit.
- Die Gruppe [Kategorie](#azure-security-center-category) listet alle Azure Policy-Definitionen in der Kategorie „Security Center“ auf.

Weitere Informationen zu Sicherheitsrichtlinien finden Sie unter [Arbeiten mit Sicherheitsrichtlinien](./tutorial-security-policy.md). Weitere Azure Policy-Integrationen für andere Dienste finden Sie unter [Integrierte Azure Policy-Richtliniendefinitionen](../governance/policy/samples/built-in-policies.md).

Die Namen der einzelnen integrierten Richtliniendefinitionen sind Links zur entsprechenden Richtliniendefinition im Azure-Portal. Verwenden Sie den Link in der Spalte **Version**, um die Quelle im [Azure Policy-GitHub-Repository](https://github.com/Azure/azure-policy) anzuzeigen.

## <a name="azure-security-center-initiatives"></a>Azure Security Center-Initiativen

Informationen zu den integrierten Initiativen, die von Security Center überwacht werden, finden Sie in der folgenden Tabelle:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Standardinitiative von Security Center (Vergleichstest für die Azure-Sicherheit)

Informationen zu den integrierten Richtlinien, die von Security Center überwacht werden, finden Sie in der folgenden Tabelle:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center-Kategorie

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie sich über Azure Policy-Sicherheitsrichtliniendefinitionen in Security Center informiert. Um mehr über Initiativen, Richtlinien und deren Zusammenhang mit den Security Center-Empfehlungen zu erfahren, lesen Sie [Was sind Sicherheitsrichtlinien, Initiativen und Empfehlungen?](security-policy-concept.md)
