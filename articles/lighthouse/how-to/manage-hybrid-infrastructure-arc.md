---
title: Verwalten einer Hybridinfrastruktur im großen Stil mit Azure Arc
description: Mit Azure Lighthouse Video können Sie die VMs und Kubernetes-Cluster von Kunden außerhalb von Azure effektiv verwalten.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 926ec6d3fdca6b31ee5296832e12a63a2dfa9d65
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122350895"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Verwalten einer Hybridinfrastruktur im großen Stil mit Azure Arc

Als Dienstanbieter haben Sie möglicherweise mehrere Kundenmandanten in [Azure Lighthouse](../overview.md) integriert. Azure Lighthouse ermöglicht Dienstanbietern das gleichzeitige Ausführen von Vorgängen für mehrere Azure Active Directory-Mandanten (Azure AD) im großen Maßstab, wodurch Verwaltungsaufgaben effizienter werden.

[Azure Arc](../../azure-arc/overview.md) hilft beim Vereinfachen komplexer und verteilter Umgebungen für lokale, Edge- und Multicloudumgebungen. Dadurch ermöglicht es die Bereitstellung von Azure-Diensten an jedem Ort und erweitert die Azure-Verwaltung auf alle Infrastrukturen.

Mit [Azure Arc-fähigen Servern](../../azure-arc/servers/overview.md) können Kunden Windows- und Linux-Computer, die außerhalb von Azure in ihrem Unternehmensnetzwerk gehostet werden, genauso verwalten wie native Azure-VMs. Durch das Verbinden eines Hybridcomputers mit Azure wird dieser vernetzt und als Ressource in Azure behandelt. Dienstanbieter können diese Nicht-Azure-Computer zusammen mit den Azure-Ressourcen ihrer Kunden verwalten.

Mithilfe von [Kubernetes mit Azure Arc-Unterstützung](../../azure-arc/kubernetes/overview.md) können Kunden Kubernetes-Cluster innerhalb oder außerhalb von Azure anfügen und konfigurieren. Wenn ein Kubernetes-Cluster an Azure Arc angefügt wird, wird er im Azure-Portal mit einer Azure Resource Manager-ID und einer verwalteten Identität angezeigt. Cluster sind an Azure-Standardabonnements angefügt, befinden sich in einer Ressourcengruppe und können wie jede andere Azure-Ressource Tags erhalten.

In diesem Artikel finden Sie eine Übersicht darüber, wie Dienstanbieter mit Azure Arc-fähigen Servern und Kubernetes mit Azure Arc-Unterstützung auf skalierbare Weise die Hybridumgebung ihrer Kunden mit Sichtbarkeit für alle verwalteten Kundenmandanten verwalten können.

> [!TIP]
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch gelten diese Anweisungen auch für [Unternehmen, die Azure Lighthouse zum Verwalten mehrerer Mandanten verwenden](../concepts/enterprise.md).

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>Verwalten von Hybridservern im großen Stil mit Azure Arc-fähigen Servern

Als Dienstanbieter können Sie lokale Computer mit Windows Server oder Linux außerhalb von Azure verwalten, wenn Ihre Kunden diese über den [Azure Connected Machine-Agent](../../azure-arc/servers/agent-overview.md) mit ihrem Abonnement verbunden haben.

Wenn Sie Ressourcen für ein delegiertes Abonnement im Azure-Portal anzeigen, werden diese verbundenen Computer mit **Azure Arc** gekennzeichnet. Sie können diese verbundenen Computer mithilfe von Azure-Konstrukten wie Azure Policy und Tags verwalten, genau wie Sie die Azure-Ressourcen der Kunden verwalten. Sie können auch über Kundenmandanten hinweg alle verbundenen Hybridcomputer gemeinsam verwalten.

Beispielsweise können Sie [sicherstellen, dass dieselben Richtlinien auf allen Hybridcomputern der Kunden angewandt werden](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md). Sie können auch Azure Security Center verwenden, um die Konformität in allen Hybridumgebungen Ihrer Kunden zu überwachen, oder [mithilfe von Azure Monitor Daten direkt von Ihren Hybridcomputern erfassen](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) und in einem Log Analytics-Arbeitsbereich anzeigen. [Erweiterungen für virtuelle Computer](../../azure-arc/servers/manage-vm-extensions.md) können auf Azure-fremden Windows- und Linux-VMs bereitgestellt werden, um die Verwaltung der Hybridcomputer des Kunden zu vereinfachen.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Verwalten von Kubernetes-Hybridclustern im großen Stil mithilfe von Kubernetes mit Azure Arc-Unterstützung

Sie können Kubernetes-Cluster, die [mit dem Abonnement eines Kunden mit Azure Arc verbunden wurden](../../azure-arc/kubernetes/quickstart-connect-cluster.md), genauso verwalten, als würden sie in Azure ausgeführt werden.

Wenn Ihr Kunde ein [Dienstprinzipalkonto zum Integrieren von Kubernetes-Clustern in Azure Arc](../../azure-arc/kubernetes/create-onboarding-service-principal.md) erstellt hat, können Sie Cluster über dieses Dienstprinzipalkonto integrieren und verwalten. Dies ist für Benutzer im Verwaltungsmandanten möglich, denen beim Integrieren des Abonnements mit dem Dienstprinzipalkonto [in Azure Lighthouse](onboard-customer.md) die integrierte Azure-Rolle „Kubernetes-Cluster – Azure Arc-Onboarding“ zugewiesen wurde.

Sie können [Konfigurationen](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) und [Helm-Charts](../../azure-arc/kubernetes/use-gitops-with-helm.md) mithilfe von GitOps für verbundene Cluster bereitstellen.

Sie können auch verbundene Cluster mit Azure Monitor überwachen und [mithilfe von Azure Policy Clusterkonfigurationen im großen Stil anwenden](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Nächste Schritte

- Erkunden Sie die Leitfäden für den schnellen Einstieg und die Beispiele im [GitHub-Repository zu Azure Arc](https://github.com/microsoft/azure_arc).
- Erfahren Sie, [welche Szenarien für Azure Arc-fähige Server unterstützt werden](../../azure-arc/servers/overview.md#supported-cloud-operations).
- Erfahren Sie, [welche Kubernetes-Distributionen von Azure Arc unterstützt werden](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions).
- Erfahren Sie, wie Sie [eine Richtlinie im großen Stil bereitstellen](policy-at-scale.md).
- Erfahren Sie, wie Sie [Azure Monitor-Protokolle im großen Stil verwenden](monitor-at-scale.md).