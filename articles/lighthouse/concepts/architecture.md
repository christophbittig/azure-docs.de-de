---
title: Azure Lighthouse-Architektur
description: Erfahren Sie mehr über die Beziehung zwischen Mandanten in Azure Lighthouse und den Ressourcen, die im Mandanten des Kunden erstellt wurden, die diese Beziehung ermöglichen.
ms.date: 09/13/2021
ms.topic: conceptual
ms.openlocfilehash: 92890199b11da69fac58ec4ff2bccc1ab347d076
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629121"
---
# <a name="azure-lighthouse-architecture"></a>Azure Lighthouse-Architektur

Mit Azure Lighthouse vereinfachen Dienstanbieter die Kundenengagement- und Onboardingerfahrung und können gleichzeitig delegierte Ressourcen bedarfsorientiert mit Agilität und Genauigkeit verwalten. Autorisierte Benutzer, Gruppen und Dienstprinzipale können direkt im Kontext eines Kundenabonnements arbeiten, ohne ein Konto im Azure Active Directory (Azure AD)-Mandanten dieses Kunden zu haben oder Miteigentümer des Mandanten des Kunden zu sein. Der Mechanismus zur Unterstützung dieses Zugriffs wird als delegierte Azure-Ressourcenverwaltung bezeichnet.

:::image type="content" source="../media/delegation.jpg" alt-text="Diagramm zur Veranschaulichung der delegierten Azure-Ressourcenverwaltung.":::

> [!TIP]
> Azure Lighthouse kann auch [in einem Unternehmen verwendet werden, das über mehrere eigene Azure AD-Mandanten verfügt](enterprise.md), um die mandantenübergreifende Verwaltung zu vereinfachen.

Dieses Thema behandelt die Beziehung zwischen Mandanten in Azure Lighthouse und den Ressourcen, die im Mandanten des Kunden erstellt wurden, die diese Beziehung ermöglichen.

## <a name="delegation-resources-created-in-the-customer-tenant"></a>Im Kundenmandanten erstellte Delegierungsressourcen

Wenn das Abonnement oder die Ressourcengruppe eines Kunden in Azure Lighthouse integriert wird, werden zwei Ressourcen erstellt: die **Registrierungsdefinition** und die **Registrierungszuweisung**. Sie können [APIs und Verwaltungstools](cross-tenant-management-experience.md#apis-and-management-tool-support) verwenden, um auf diese Ressourcen zuzugreifen, oder mit ihnen [im Azure-Portal](../how-to/view-manage-customers.md) arbeiten.

### <a name="registration-definition"></a>Registrierungsdefinition

Die Registrierungsdefinition enthält die Details des Azure Lighthouse-Angebots (die verwaltende Mandanten-ID und die Autorisierungen, die bestimmten Benutzern, Gruppen und/oder Dienstprinzipalen im verwaltenden Mandanten integrierte Rollen zuweisen.

Eine Registrierungsdefinition wird auf Abonnementebene für jedes delegierte Abonnement oder in jedem Abonnement erstellt, das eine delegierte Ressourcengruppe enthält. Wenn Sie APIs zum Erstellen einer Registrierungsdefinition verwenden, müssen Sie auf Abonnementebene arbeiten. Wenn Sie beispielsweise Azure PowerShell verwenden, müssen Sie New-AzureRmDeployment verwenden, bevor Sie eine neue Registrierungsdefinition ([New-AzManagedServicesDefinition](/powershell/module/az.managedservices/new-azmanagedservicesdefinition)) erstellen, anstatt New-AzureRmResourceGroupDeployment zu verwenden.

### <a name="registration-assignment"></a>Registrierungszuweisung

Die Registrierungszuweisung weist die Registrierungsdefinition einem bestimmten Bereich zu, d. h. dem/den integrierten Abonnement(s) und/oder der/den Ressourcengruppe(n).

Eine Registrierungszuweisung wird in jedem delegierten Bereich erstellt, sodass sie je nach Onboarding entweder auf Abonnementgruppen- oder Ressourcengruppenebene liegt.

Jede Registrierungszuweisung muss auf eine gültige Registrierungsdefinition auf Abonnementebene verweisen, um die Autorisierungen für diesen Dienstanbieter an den delegierten Bereich zu binden und somit Zugriff zu gewähren.

## <a name="logical-projection"></a>Logische Projektion

Azure Lighthouse erstellt eine logische Projektion von Ressourcen von einem Mandanten auf einen anderen Mandanten. Dadurch können sich autorisierte Benutzer von Dienstanbietern bei ihrem eigenen Mandanten anmelden und autorisiert werden, in delegierten Kundenabonnements und Ressourcengruppen zu arbeiten. Dadurch können Benutzer im Mandanten des Dienstanbieters Verwaltungsvorgänge im Namen ihrer Kunden durchführen, ohne sich bei den einzelnen Kundenmandanten anmelden zu müssen.

Wenn ein Benutzer, eine Gruppe oder ein Dienstprinzipal im Mandanten des Dienstanbieters auf Ressourcen im Mandanten eines Kunden zutritt, empfängt Azure Resource Manager eine Anforderung. Resource Manager authentifiziert diese Anforderungen genau wie bei Anforderungen, die von Benutzern innerhalb des eigenen Mandanten des Kunden gestellt werden. Bei Azure Lighthouse geschieht dies, indem bestätigt wird, dass zwei Ressourcen, die Registrierungsdefinition und die Registrierungszuweisung, im Mandanten des Kunden vorhanden sind. Wenn dies der Fall ist, autorisiert Resource Manager den Zugriff gemäß den Informationen, die von diesen Ressourcen definiert werden.

:::image type="content" source="../media/logical-projection.jpg" alt-text="Diagramm zur Veranschaulichung der logischen Projektion in Azure Lighthouse.":::

Aktivitäten von Benutzern im Mandanten des Dienstanbieters werden im Aktivitätsprotokoll nachverfolgt, das im Mandanten des Kunden gespeichert ist. Dadurch kann der Kunde [sehen, welche Änderungen von wem vorgenommen wurden](../how-to/view-service-provider-activity.md).

## <a name="how-azure-lighthouse-works"></a>Wie Azure Lighthouse funktioniert

Auf der allgemeinen Ebene funktioniert Azure Lighthouse aus Sicht des verwaltenden Mandanten folgendermaßen:

1. Sie ermitteln die [Rollen](tenants-users-roles.md#role-support-for-azure-lighthouse), die Ihre Gruppen, Dienstprinzipale oder Benutzer benötigen, um die Azure-Ressourcen des Kunden zu verwalten.
2. Geben Sie diesen Zugriff an, und integrieren Sie den Kunden in Azure Lighthouse, indem Sie entweder ein Angebot für verwaltete Dienste in [Azure Marketplace](../how-to/publish-managed-services-offers.md) veröffentlichen oder indem Sie [eine Azure Resource Manager Vorlage bereitstellen](../how-to/onboard-customer.md). Dieser Onboardingprozess erstellt die beiden oben beschriebenen Ressourcen (Registrierungsdefinition und Registrierungszuweisung) im Mandanten des Kunden.
3. Nach dem Onboarding des Kunden melden sich autorisierte Benutzer bei Ihrem verwaltenden Mandanten an und führen Aufgaben im vorgegebenen Kundenumfang (Abonnement oder Ressourcengruppe) gemäß dem von Ihnen definierten Zugriff aus. Kunden können alle ausgeführten Aktionen überprüfen und den Zugriff jederzeit entfernen.

Während in den meisten Fällen nur ein Dienstanbieter bestimmte Ressourcen für einen Kunden verwaltet, ist es möglich, dass der Kunde mehrere Delegationen für dasselbe Abonnement oder die gleiche Ressourcengruppe erstellt, sodass mehrere Dienstanbieter Zugriff haben. Dieses Szenario ermöglicht auch ISV-Szenarien, in denen [Ressourcen aus dem Mandanten des Dienstanbieters für mehrere Kunden projiziert](isv-scenarios.md#saas-based-multi-tenant-offerings) werden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Azure CLI](/cli/azure/managedservices) und [Azure PowerShell](/powershell/module/az.managedservices)-Befehle, um mit Registrierungsdefinitionen und Registrierungszuweisungen zu arbeiten.
- Erfahren Sie mehr über [erweiterte Dienste und Szenarien](cross-tenant-management-experience.md#enhanced-services-and-scenarios) für Azure Lighthouse.
- Erfahren Sie mehr über die Funktionsweise von [Mandanten, Benutzern und Rollen](tenants-users-roles.md) mit Azure Lighthouse.
