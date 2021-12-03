---
title: Was ist Azure Lighthouse?
description: Mit Azure Lighthouse können Dienstanbieter verwaltete Dienste für Ihre Kunden bereitstellen, bei denen bedarfsorientiert eine höhere Automatisierung und Effizienz erzielt werden können.
ms.date: 11/02/2021
ms.topic: overview
ms.openlocfilehash: 6279ef69c60c7a6d76fe0dfbea68934394c23c06
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343114"
---
# <a name="what-is-azure-lighthouse"></a>Was ist Azure Lighthouse?

Azure Lighthouse ermöglicht die Verwaltung mehrerer Mandanten mit hoher Skalierbarkeit, stärkerer Automatisierung und verbesserter Governance für alle Ressourcen.

Mit Azure Lighthouse können Dienstanbieter verwaltete Dienste mithilfe [umfassender und stabiler Tools bereitstellen, die in die Azure-Plattform integriert sind](concepts/architecture.md). Kunden behalten die Kontrolle darüber, wer auf ihren Mandanten zugreifen kann, auf welche Ressourcen ein Zugriff möglich ist und welche Aktionen durchgeführt werden können. [Unternehmensorganisationen](concepts/enterprise.md), die Ressourcen in mehreren Mandanten verwalten, können ebenfalls Azure Lighthouse zum Optimieren von Verwaltungsaufgaben verwenden.

Mithilfe der [mandantenübergreifenden Verwaltungsmöglichkeiten](concepts/cross-tenant-management-experience.md) können Sie effizienter mit Azure-Diensten wie [Azure Policy](how-to/policy-at-scale.md), [Microsoft Sentinel](how-to/manage-sentinel-workspaces.md), [Azure Arc](how-to/manage-hybrid-infrastructure-arc.md) und vielen mehr arbeiten. Benutzer können im [Aktivitätsprotokoll](how-to/view-service-provider-activity.md), das im Mandanten des Kunden gespeichert wird und von Benutzern im verwaltenden Mandanten angezeigt werden kann, nachverfolgen, welche Änderungen von wem vorgenommen wurden.

![Übersichtsdiagramm von Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Vorteile

Azure Lighthouse dient Dienstanbietern beim effizienten Erstellen und Bereitstellen verwalteter Dienste als Hilfe. Dies hat unter anderem folgende Vorteile:

- **Bedarfsorientierte Verwaltung**: Kundenbindungs- und Lebenszyklusvorgänge zur Verwaltung von Kundenressourcen sind einfacher und besser skalierbar. Vorhandene APIs, Verwaltungstools und Workflows können mit delegierten Ressourcen, z. B. auch außerhalb von Azure gehosteten Computern, verwendet werden. Dies gilt unabhängig von den Regionen, in denen sie sich befinden.
- **Bessere Transparenz und Kontrolle für Kunden**: Kunden können präzise steuern, welche Bereiche sie für die Verwaltung delegieren und welche Berechtigungen zulässig sind. Sie können [Aktionen von Dienstanbietern überwachen](how-to/view-service-provider-activity.md) und den Zugriff jederzeit vollständig widerrufen.
- **Umfassende und einheitliche Plattformtools:** Für Azure Lighthouse können vorhandene Tools und APIs, [von Azure verwaltete Anwendungen](concepts/managed-applications.md) und Partnerprogramme wie das [Cloud Solution Provider-Programm (CSP)](concepts/cloud-solution-provider.md) genutzt werden. Durch diese Flexibilität werden wichtige Dienstanbieterszenarien unterstützt, einschließlich mehrerer Lizenzierungsmodelle wie EA (Enterprise Agreement), CSP (Cloud Solution Provider) und nutzungsbasierte Bezahlung. Sie können Azure Lighthouse in Ihre vorhandenen Workflows und Anwendungen integrieren und nachverfolgen, welchen Einfluss Sie auf die Kundenbindung haben, indem Sie [Ihre Partner-ID verknüpfen](how-to/partner-earned-credit.md).

## <a name="capabilities"></a>Funktionen

Azure Lighthouse umfasst mehrere Möglichkeiten zur Optimierung von Kundenbindung und Verwaltung:

- **Delegierte Azure-Ressourcenverwaltung**: [Verwalten Sie die Azure-Ressourcen Ihrer Kunden sicher von Ihrem eigenen Mandanten aus](concepts/architecture.md), ohne den Kontext oder Steuerungsebenen wechseln zu müssen. Kundenabonnements und Ressourcengruppen können an bestimmte Benutzer und Rollen im Verwaltungsmandanten delegiert werden, und es besteht die Möglichkeit, den Zugriff bei Bedarf zu entfernen.
- **Neue Azure-Portalerfahrung**: Zeigen Sie mandantenübergreifende Informationen auf der [Seite **Meine Kunden**](how-to/view-manage-customers.md) im Azure-Portal an. Auf einer entsprechenden [Seite **Dienstanbieter**](how-to/view-manage-service-providers.md) können Kunden den Dienstanbieterzugriff anzeigen und verwalten.
- **Azure Resource Manager-Vorlagen**: Verwenden Sie ARM-Vorlagen zum [Durchführen des Onboardings für delegierte Kundenressourcen](how-to/onboard-customer.md) und [Durchführen von mandantenübergreifenden Verwaltungsaufgaben](samples/index.md).
- **Angebote für verwaltete Dienste im Azure Marketplace:** [Bieten Sie Ihre Dienste Kunden als private oder öffentliche Angebote an](concepts/managed-services-offers.md), und nutzen Sie das automatische Onboarding für Azure Lighthouse.

> [!TIP]
> Ein ähnliches Angebot, [Microsoft 365 Lighthouse](/microsoft-365/lighthouse/m365-lighthouse-overview), hilft Dienstanbietern dabei, ihre Microsoft 365-Kunden im großen Stil zu integrieren, zu überwachen und zu verwalten. Microsoft 365 Lighthouse befindet sich derzeit in der Vorschau.

## <a name="pricing-and-availability"></a>Preise und Verfügbarkeit

Es fallen keine zusätzlichen Kosten für die Verwendung von Azure Lighthouse zur Verwaltung von Azure-Ressourcen an. Jeder Azure-Kunde oder -Partner kann Azure Lighthouse verwenden.

## <a name="cross-region-and-cloud-considerations"></a>Überlegungen zu regionsübergreifenden und cloudübergreifenden Szenarien

Azure Lighthouse ist ein nicht regionaler Dienst. Sie können delegierte Ressourcen verwalten, die sich in unterschiedlichen [Regionen](../availability-zones/az-overview.md#regions) befinden. Die Delegierung von Abonnements auf eine [nationale Cloud](../active-directory/develop/authentication-national-cloud.md) und die öffentliche Azure-Cloud oder zwei separate nationale Clouds wird nicht unterstützt.

## <a name="support-for-azure-lighthouse"></a>Unterstützung für Azure Lighthouse

[Öffnen Sie eine Supportanfrage](..//azure-portal/supportability/how-to-create-azure-support-request.md) im Azure-Portal, um Unterstützung bei der Verwendung von Azure Lighthouse zu erhalten. Wählen Sie als **Problemtyp** **Technisch** aus. Wählen Sie ein Abonnement aus und dann **Lighthouse** (unter **Überwachung und Verwaltung**).

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich mit den [technischen Aspekten von Azure Lighthouse](concepts/architecture.md) vertraut.
- Erfahren Sie mehr über [mandantenübergreifende Verwaltungsmöglichkeiten](concepts/cross-tenant-management-experience.md).
- Informieren Sie sich über die [Verwendung von Azure Lighthouse in einem Unternehmen](concepts/enterprise.md).
- Zeigen Sie Details zu [Verfügbarkeit](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all) und [FedRAMP- und DoD CC SRG-Prüfumfang](../azure-government/compliance/azure-services-in-fedramp-auditscope.md) für Azure Lighthouse an.
