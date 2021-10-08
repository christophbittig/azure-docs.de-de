---
title: Überlegungen zum Cloud Solution Provider-Programm
description: Die delegierte Azure-Ressourcenverwaltung unterstützt CSP-Partner bei der Verbesserung der Sicherheit und Kontrolle durch die Aktivierung präziser Berechtigungen.
ms.date: 09/13/2021
ms.topic: conceptual
ms.openlocfilehash: 6a53ec7cfc2e9f82664e57eecf5bf3f587444134
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629102"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse und das Programm für Cloud-Lösungsanbieter

Als [CSP](/partner-center/csp-overview)-Partner (Cloud Solution Provider) können Sie bereits auf die durch das CSP-Programm für Ihre Kunden erstellten Azure-Abonnements zugreifen, indem Sie die Funktion [AOBO (Administer On Behalf Of, Verwalten im Namen von)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) verwenden. Dieser Zugriff gestattet es Ihnen, die Abonnements Ihrer Kunden direkt zu unterstützen, zu konfigurieren und zu verwalten.

Mit [Azure Lighthouse](../overview.md) können Sie die delegierte Azure-Ressourcenverwaltung zusammen mit AOBO verwenden. Dies trägt zur Verbesserung der Sicherheit bei und reduziert unnötigen Zugriff, da Sie präzisere Berechtigungen für Ihre Benutzer vergeben können. Darüber hinaus werden eine höhere Effizienz und Skalierbarkeit ermöglicht, da Ihre Benutzer mit nur einer Anmeldung bei Ihrem Mandanten mehrere Kundenabonnements verwenden können.

> [!TIP]
> Damit die Ressourcen der Kunden geschützt sind, überprüfen und befolgen Sie unsere [empfohlenen Sicherheitsmaßnahmen](recommended-security-practices.md) sowie die [Sicherheitsanforderungen für Partner](/partner-center/partner-security-requirements).

## <a name="administer-on-behalf-of-aobo"></a>Verwalten im Namen von (AOBO)

Mit AOBO erhält jeder Benutzer mit der Rolle [Administrator-Agent](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) in Ihrem Mandanten AOBO-Zugriff auf Azure-Abonnements, die Sie durch das CSP-Programm erstellen. Alle Benutzer, die Zugriff auf Abonnements von Kunden benötigen, müssen Mitglied dieser Gruppe sein. AOBO lässt nicht die Flexibilität zu, unterschiedliche Gruppen zu erstellen, die mit verschiedenen Kunden zusammenarbeiten, oder verschiedene Rollen für Gruppen oder Benutzer zu aktivieren.

![Diagramm der Mandantenverwaltung mithilfe von AOBO](../media/csp-1.jpg)

## <a name="azure-lighthouse"></a>Azure Lighthouse

Mit Azure Lighthouse können Sie verschiedenen Kunden oder Rollen unterschiedliche Gruppen zuweisen, wie im folgenden Diagramm dargestellt. Da Benutzer durch die [delegierte Azure-Ressourcenverwaltung](architecture.md) über die geeignete Zugriffsebene verfügen, können Sie die Anzahl der Benutzer verringern, die über die Rolle „Admin-Agent“ verfügen (und somit über AOBO-Vollzugriff verfügen). Dies hilft, die Sicherheit zu verbessern, indem unnötiger Zugriff auf die Ressourcen deiner Kunden vermieden wird. Außerdem erhalten Sie mehr Flexibilität bei der Verwaltung mehrerer Kunden im großen Stil, indem Sie die [integrierte Azure-Rolle](tenants-users-roles.md#role-support-for-azure-lighthouse) verwenden, die für die Aufgaben der einzelnen Benutzer am besten geeignet ist, ohne einem Benutzer mehr Zugriff als notwendig zu erteilen.

![Diagramm der Mandantenverwaltung mithilfe von AOBO und Azure Lighthouse.](../media/csp-2.jpg)

Das Onboarding eines Abonnements, das Sie über das CSP-Programm erstellt haben, erfolgt gemäß den Schritten, die in [Onboarding eines Kunden zu Azure Lighthouse](../how-to/onboard-customer.md) beschrieben sind. Jeder Benutzer, der in Ihrem Mandanten über die Rolle „Administrator-Agent“ verfügt, kann dieses Onboarding durchführen.

> [!TIP]
> [Verwaltete Dienstangebote](managed-services-offers.md) mit privaten Plänen werden bei Abonnements, die über einen Handelspartner des Cloud Solution Provider-Programms (CSP) eingerichtet wurden, nicht unterstützt. Sie können diese Abonnements in Azure Lighthouse integrieren, indem Sie [Azure Resource Manager-Vorlagen](../how-to/onboard-customer.md) verwenden.

> [!NOTE]
> Die [Seite **Meine Kunden** im Azure-Portal](../how-to/view-manage-customers.md) enthält jetzt einen Abschnitt **Cloud Solution Provider (Vorschau)** , in dem Abrechnungsinformationen und Ressourcen für CSP-Kunden aufgeführt werden, die die [Microsoft-Kundenvereinbarung signiert](/partner-center/confirm-customer-agreement) haben und [den Azure-Plan nutzen](/partner-center/azure-plan-get-started). Weitere Informationen finden Sie unter [Erste Schritte mit Ihrem Abrechnungskonto für eine Microsoft-Partnervereinbarung](../../cost-management-billing/understand/mpa-overview.md).
>
> In diesem Abschnitt werden möglicherweise CSP-Kunden angezeigt, auch wenn diese noch nicht in Azure Lighthouse integriert wurden. Wenn das Onboarding durchgeführt wurde, werden sie auch im Abschnitt **Kunden** angezeigt, wie unter [Anzeigen und Verwalten von Kunden und delegierten Ressourcen](../how-to/view-manage-customers.md) beschrieben. Ebenso muss ein CSP-Kunde nicht im Abschnitt **Cloud Solution Provider (Vorschau)** auf der Seite **Meine Kunden** angezeigt werden, damit Sie das Onboarding dieses Kunden in Azure Lighthouse durchführen können.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie über [Mandantenübergreifende Verwaltungsmöglichkeiten](cross-tenant-management-experience.md).
- Erfahren Sie mehr über das [Onboarding eines Abonnements in Azure Lighthouse](../how-to/onboard-customer.md).
- Erhalten Sie Informationen über das [Programm für Cloud-Lösungsanbieter](/partner-center/csp-overview).
