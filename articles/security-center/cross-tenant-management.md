---
title: Mandantenübergreifende Verwaltung in Microsoft Defender für Cloud | Microsoft-Dokumentation
description: Erfahren Sie, wie die mandantenübergreifende Verwaltung zum Verwalten des Sicherheitsstatus mehrerer Mandanten in Defender für Cloud unter Verwendung von Azure Lighthouse eingerichtet wird.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9db4a9b47de9e8977d994afd5f2d48cb1e0f52af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095898"
---
# <a name="cross-tenant-management-in-defender-for-cloud"></a>Mandantenübergreifende Verwaltung in Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Die mandantenübergreifende Verwaltung ermöglicht Ihnen die Anzeige und Verwaltung des Sicherheitsstatus mehrerer Mandanten in Defender für Cloud mithilfe von [Azure Lighthouse](../lighthouse/overview.md). Verwalten Sie effizient mehrere Mandanten aus einer einzigen Ansicht heraus, ohne sich bei den einzelnen Mandantenverzeichnissen anmelden zu müssen.

- Dienstanbieter können den Sicherheitsstatus von Ressourcen für mehrere Kunden innerhalb ihres eigenen Mandanten verwalten.

- Sicherheitsteams von Organisationen mit mehreren Mandanten können ihren Sicherheitsstatus von einem einzigen Standort aus anzeigen und verwalten.

## <a name="set-up-cross-tenant-management"></a>Einrichten der mandantenübergreifenden Verwaltung

Die [delegierte Azure-Ressourcenverwaltung](../lighthouse/concepts/architecture.md) ist eine der Hauptkomponenten von Azure Lighthouse. Richten Sie die mandantenübergreifende Verwaltung ein, indem Sie den Zugriff auf Ressourcen verwalteter Mandanten anhand der folgenden Anweisungen aus der Azure Lighthouse-Dokumentation an Ihren eigenen Mandanten delegieren: [Onboarding eines Kunden in Azure Lighthouse durchführen](../lighthouse/how-to/onboard-customer.md).


## <a name="how-does-cross-tenant-management-work-in-defender-for-cloud"></a>Wie funktioniert die mandantenübergreifende Verwaltung in Defender für Cloud?

Sie können Abonnements für mehrere Mandanten auf die gleiche Weise überprüfen und verwalten, wie Sie mehrere Abonnements auf einem einzelnen Mandanten verwalten.

Klicken Sie in der oberen Menüleiste auf das Filtersymbol, und wählen Sie aus den Verzeichnissen der einzelnen Mandanten die Abonnements aus, die Sie anzeigen möchten.

  ![Filtern von Mandanten](./media/cross-tenant-management/cross-tenant-filter.png)

Die Ansichten und Aktionen sind im Grunde identisch. Im Folgenden finden Sie einige Beispiele:

- **Verwalten von Sicherheitsrichtlinien**: Verwalten Sie in einer einzigen Ansicht den Sicherheitsstatus vieler Ressourcen mit [Richtlinien](tutorial-security-policy.md), ergreifen Sie Maßnahmen mit Sicherheitsempfehlungen, und erfassen und verwalten Sie sicherheitsrelevante Daten.
- **Verbessern von Secure Score und Konformitätsstatus:** Mithilfe der mandantenübergreifenden Sichtbarkeit können Sie den gesamten Sicherheitsstatus all Ihrer Mandanten anzeigen und ermitteln, wo und wie Sie die [Sicherheitsbewertung](secure-score-security-controls.md) und den [Compliancestatus](regulatory-compliance-dashboard.md) für jeden einzelnen Mandanten optimieren können.
- **Umsetzen von Empfehlungen**: Sie können eine [Empfehlung](review-security-recommendations.md) für viele Ressourcen von verschiedenen Mandanten gleichzeitig überwachen und umsetzen. Anschließend können Sie die Sicherheitslücken, die das höchste Risiko darstellen, sofort für alle Mandanten beseitigen.
- **Verwalten von Warnungen**: Ermitteln Sie [Warnungen](alerts-overview.md) auf den verschiedenen Mandanten. Ergreifen Sie Maßnahmen für Ressourcen, die nicht mit handlungsrelevanten [Schritten zur Bereinigung](managing-and-responding-alerts.md) konform sind.

- **Verwalten erweiterter Cloudschutzfeatures und mehr**: Verwalten Sie die verschiedenen Dienste zum Schutz vor Bedrohungen, z. B. [Just-In-Time (JIT)-VM-Zugriff](just-in-time-access-usage.md), [adaptive Netzwerkhärtung](adaptive-network-hardening.md), [adaptive Anwendungssteuerung](adaptive-application-controls.md) und mehr.
 
## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wird erläutert, wie die mandantenübergreifende Verwaltung in Defender für Cloud funktioniert. Um zu erfahren Sie, wie Azure Lighthouse die mandantenübergreifenden Verwaltung in einem Unternehmen, in dem mehrere Azure AD-Mandanten vorhanden sind, vereinfachen kann, lesen Sie [Azure Lighthouse in Unternehmensszenarien](../lighthouse/concepts/enterprise.md).
