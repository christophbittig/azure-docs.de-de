---
title: Voraussetzungen für die Bereitstellung von Microsoft Sentinel
description: Hier werden die Aktivitäten vor der Bereitstellung und die Voraussetzungen für die Bereitstellung von Microsoft Sentinel beschrieben.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: bae2e792ab85ec87f1187c632f9582e9d38e87ed
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521128"
---
# <a name="pre-deployment-activities-and-prerequisites-for-deploying-microsoft-sentinel"></a>Aktivitäten vor der Bereitstellung und Voraussetzungen für die Bereitstellung von Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel werden die Aktivitäten vor der Bereitstellung und die Voraussetzungen für die Bereitstellung von Microsoft Sentinel vorgestellt.

## <a name="pre-deployment-activities"></a>Aktivitäten vor der Bereitstellung

Vor der Bereitstellung von Microsoft Sentinel empfehlen wir die Ausführung der folgenden Schritte, um Ihre Bereitstellung so zu konfigurieren, dass sie schnellstmöglich maximalen Nutzen bringt.

1. Bestimmen Sie die erforderlichen [Datenquellen](connect-data-sources.md) und die Anforderungen an die Datengröße, damit Sie das Budget und den zeitlichen Rahmen Ihrer Bereitstellung genau planen können.

    Sie können diese Informationen während der Analyse Ihres geschäftlichen Anwendungsfalls oder durch Auswertung eines bereits vorhandenen SIEM-Systems ermitteln. Wenn Sie bereits über ein SIEM-System verfügen, können Sie Ihre Daten analysieren, um besser zu verstehen, welche Datenquellen den größten Nutzen bieten und in Microsoft Sentinel erfasst werden sollten.

1. Entwerfen Sie Ihren Microsoft Sentinel-Arbeitsbereich. Berücksichtigen Sie dabei z. B. folgende Parameter:

    - Soll ein einzelner oder mehrere Mandanten verwendet werden
    - sämtliche Konformitätsanforderungen für die Datensammlung und -speicherung
    - Steuern des Zugriffs auf Microsoft Sentinel-Daten

    Weitere Informationen finden Sie unter [Arbeitsbereichsarchitektur: Bewährte Methoden](best-practices-workspace-architecture.md) und [Beispielentwürfe für Arbeitsbereiche](sample-workspace-designs.md).

1. Nachdem die geschäftlichen Anwendungsfälle, Datenquellen und Datengrößenanforderungen identifiziert wurden, beginnen Sie mit der[Planung Ihres Budgets](azure-sentinel-billing.md), und berücksichtigen Sie die Auswirkungen auf die Kosten für jedes geplante Szenario.

    Stellen Sie sicher, dass Ihr Budget die Kosten für die Datenerfassung sowohl für Microsoft Sentinel als auch für Azure Log Analytics sowie für alle bereitgestellten Playbooks usw. abdeckt.

    Weitere Informationen finden Sie unter

    - [Microsoft Sentinel: Kosten und Abrechnung](azure-sentinel-billing.md)
    - [Microsoft Sentinel – Preise](https://azure.microsoft.com/pricing/details/azure-sentinel/)
    - [Log Analytics – Preise](https://azure.microsoft.com/pricing/details/monitor/)
    - [Logic-Apps (Playbooks) – Preise](https://azure.microsoft.com/pricing/details/logic-apps/)
    - [Integrieren von Azure Data Explorer für die langfristige Protokollaufbewahrung](store-logs-in-azure-data-explorer.md)

1. Benennen Sie einen Techniker oder Architekten, der die Bereitstellung basierend auf Anforderungen und Zeitplänen leitet. Diese Person sollte die Bereitstellung leiten und der Hauptansprechpartner Ihres Teams sein.

## <a name="azure-tenant-requirements"></a>Anforderungen an Azure-Mandanten

Stellen Sie vor der Bereitstellung von Microsoft Sentinel sicher, dass Ihr Azure-Mandant die folgenden Anforderungen erfüllt:

- Für den Zugriff auf Azure und die Bereitstellung von Ressourcen sind eine [Azure Active Directory-Lizenz und ein Mandant](../active-directory/develop/quickstart-create-new-tenant.md) oder ein [einzelnes Konto mit einer gültigen Zahlungsmethode](https://azure.microsoft.com/free/) erforderlich.

- Wenn Sie über einen Mandanten verfügen, benötigen Sie ein [Azure-Abonnement](../cost-management-billing/manage/create-subscription.md), um die Erstellung und Abrechnung der Ressourcen nachverfolgen zu können.

- Wenn Sie über ein Abonnement verfügen, benötigen Sie die [entsprechenden Berechtigungen](../role-based-access-control/index.yml), um mit der Verwendung Ihres Abonnements zu beginnen. Wenn Sie ein neues Abonnement verwenden, sollte ein Administrator oder eine Person mit noch höheren Befugnissen aus dem AAD-Mandanten als [Besitzer/Mitwirkender](../role-based-access-control/rbac-and-directory-admin-roles.md) für das Abonnement festgelegt werden.

    - Weisen Sie Rollen auf der Ebene der Ressourcengruppe zu, um den Zugriff mit den am geringsten verfügbaren Berechtigungen aufrechtzuerhalten.
    - Sie können benutzerdefinierte Rollen festlegen, um mehr Kontrolle über die Berechtigungen und den Zugriff zu erhalten. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung mit Microsoft Intune](../role-based-access-control/custom-roles.md).
    - Verwenden Sie [resource-context](resource-context-rbac.md) oder [table-level RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) für eine zusätzliche Trennung zwischen Benutzern und Sicherheitsbenutzern.

    Weitere Informationen zu anderen Rollen und Berechtigungen, die für Microsoft Sentinel unterstützt werden, finden Sie unter [Berechtigungen in Microsoft Sentinel](roles.md).

- Ein [Log Analytics-Arbeitsbereich](../azure-monitor/logs/quick-create-workspace.md) ist erforderlich, um alle Daten zu speichern, die Microsoft Sentinel erfasst und für seine Erkennungen, Analysen und andere Features verwendet. Weitere Informationen finden Sie unter [Bewährte Methoden für die Microsoft Sentinel-Arbeitsbereichsarchitektur](best-practices-workspace-architecture.md).

> [!TIP]
> Wenn Sie Ihren Microsoft Sentinel-Arbeitsbereich einrichten, [erstellen Sie eine Ressourcengruppe](../azure-resource-manager/management/manage-resource-groups-portal.md), die Microsoft Sentinel und den Ressourcen, die Microsoft Sentinel verwenden, einschließlich des Log Analytics-Arbeitsbereichs, aller Playbooks, Arbeitsmappen usw., zugeordnet ist.
>
> Eine dedizierte Ressourcengruppe ermöglicht die einmalige Zuweisung von Berechtigungen auf Ressourcengruppenebene, wobei Berechtigungen automatisch auf alle relevanten Ressourcen angewendet werden. Durch die Verwaltung des Zugriffs über eine Ressourcengruppe können Sie sicherstellen, dass Sie Microsoft Sentinel effizient verwenden, ohne möglicherweise falsche Berechtigungen auszugeben. Ohne eine Ressourcengruppe für Microsoft Sentinel, in der Ressourcen auf mehrere Ressourcengruppen verteilt sind, kann ein Benutzer oder Dienstprinzipal möglicherweise aufgrund unzureichender Berechtigungen keine erforderliche Aktion ausführen oder Daten anzeigen.
>
> Verwenden Sie für eine verstärkte Zugriffskontrolle auf Ressourcen nach Ebenen zusätzliche Ressourcengruppen, die die Ressourcen enthalten, auf die nur diese Gruppen zugreifen können. Wenn Sie mehrere Ebenen von Ressourcengruppen verwenden, können Sie den Zugriff zwischen diesen Ebenen trennen.
>

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Durchführen des Onboardings für Microsoft Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Einblick in Warnungen](get-visibility.md)
