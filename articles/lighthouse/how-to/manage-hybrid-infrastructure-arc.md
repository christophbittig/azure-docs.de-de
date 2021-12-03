---
title: Verwalten einer Hybridinfrastruktur im großen Stil mit Azure Arc
description: Mit Azure Lighthouse Video können Sie die VMs und Kubernetes-Cluster von Kunden außerhalb von Azure effektiv verwalten.
ms.date: 09/07/2021
ms.topic: how-to
ms.openlocfilehash: 5acb9cae08795a9c87db0d6353f4870e92849f24
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293059"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Verwalten einer Hybridinfrastruktur im großen Stil mit Azure Arc

[Azure Lighthouse](../overview.md) kann Dienstanbieter bei der Verwendung von Azure Arc zum Verwalten der Hybridumgebungen von Kunden unterstützen, wobei übergreifende Sichtbarkeit für alle verwalteten Azure AD-Mandanten (Azure Active Directory) erzielt wird.

[Azure Arc](../../azure-arc/overview.md) hilft beim Vereinfachen komplexer und verteilter Umgebungen für lokale, Edge- und Multicloudumgebungen. Dadurch ermöglicht es die Bereitstellung von Azure-Diensten an jedem Ort und erweitert die Azure-Verwaltung auf alle Infrastrukturen.

Mit [Azure Arc-fähigen Servern](../../azure-arc/servers/overview.md) können Kunden Windows- und Linux-Computer, die außerhalb von Azure in ihrem Unternehmensnetzwerk gehostet werden, genauso verwalten wie native Azure-VMs. Durch das Verbinden eines Hybridcomputers mit Azure wird dieser vernetzt und als Ressource in Azure behandelt. Dienstanbieter können diese Nicht-Azure-Computer zusammen mit den Azure-Ressourcen ihrer Kunden verwalten.

Mithilfe von [Kubernetes mit Azure Arc-Unterstützung](../../azure-arc/kubernetes/overview.md) können Kunden Kubernetes-Cluster innerhalb oder außerhalb von Azure anfügen und konfigurieren. Wenn ein Kubernetes-Cluster an Azure Arc angefügt wird, wird er im Azure-Portal mit einer Azure Resource Manager-ID und einer verwalteten Identität angezeigt. Cluster sind an Azure-Standardabonnements angefügt, befinden sich in einer Ressourcengruppe und können wie jede andere Azure-Ressource Tags erhalten.

Dieses Thema bietet eine Übersicht darüber, wie Sie Server mit Azure Arc-Unterstützung und Kubernetes mit Azure Arc-Unterstützung in skalierbarer Weise übergreifend über die von Ihnen verwalteten Kundenmandanten hinweg verwenden.

> [!TIP]
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch gelten diese Anweisungen auch für [Unternehmen, die Azure Lighthouse zum Verwalten mehrerer Mandanten verwenden](../concepts/enterprise.md).

## <a name="manage-hybrid-servers-at-scale-with-azure-arcenabled-servers"></a>Verwalten von Hybridservern im großen Stil mit Azure Arc-fähigen Servern

Als Dienstanbieter können Sie lokale Computer mit Windows Server oder Linux außerhalb von Azure verwalten, wenn Ihre Kunden diese über den [Azure Connected Machine-Agent](../../azure-arc/servers/agent-overview.md) mit ihrem Abonnement verbunden haben. Wenn Sie Ressourcen für ein delegiertes Abonnement im Azure-Portal anzeigen, werden diese verbundenen Computer mit **Azure Arc** gekennzeichnet.

Sie können diese verbundenen Computer mithilfe von Azure-Konstrukten wie Azure Policy und Tags verwalten, genau wie Sie die Azure-Ressourcen der Kunden verwalten. Sie können auch über Kundenmandanten hinweg alle verbundenen Hybridcomputer gemeinsam verwalten.

Beispielsweise können Sie [sicherstellen, dass dieselben Richtlinien auf allen Hybridcomputern der Kunden angewandt werden](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md). Außerdem können Sie Microsoft Defender für Cloud auch dafür verwenden, um die Konformität in allen Hybridumgebungen Ihrer Kunden zu überwachen oder um [mithilfe von Azure Monitor Daten direkt aus Hybridcomputern erfassen](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) und in einem Protokollanalyse(Log-Analytics)-Arbeitsbereich sammeln. [Erweiterungen für virtuelle Computer](../../azure-arc/servers/manage-vm-extensions.md) können auf Azure-fremden Windows- und Linux-VMs bereitgestellt werden, um die Verwaltung der Hybridcomputer des Kunden zu vereinfachen.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Verwalten von Kubernetes-Hybridclustern im großen Stil mithilfe von Kubernetes mit Azure Arc-Unterstützung

Sie können Kubernetes-Cluster, die [mit dem Abonnement eines Kunden mit Azure Arc verbunden wurden](../../azure-arc/kubernetes/quickstart-connect-cluster.md), genauso verwalten, als würden sie in Azure ausgeführt werden.

Wenn Ihr Kunde ein [Dienstprinzipalkonto zum Integrieren von Kubernetes-Clustern in Azure Arc](../../azure-arc/kubernetes/create-onboarding-service-principal.md) erstellt hat, können Sie auf dieses Konto zugreifen, um Cluster zu integrieren und zu verwalten. Dazu muss einem Benutzer im Verwaltungsmandanten beim Integrieren des Abonnements mit dem Dienstprinzipalkonto [in Azure Lighthouse](onboard-customer.md) die integrierte Azure-Rolle „Kubernetes-Cluster – Azure Arc-Onboarding“ zugewiesen worden sein.

Sie können [Konfigurationen](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) und [Helm-Charts](../../azure-arc/kubernetes/use-gitops-with-helm.md) mithilfe von GitOps für verbundene Cluster bereitstellen.

Sie können auch verbundene Cluster mit Azure Monitor überwachen und [mithilfe von Azure Policy Clusterkonfigurationen im großen Stil anwenden](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Nächste Schritte

- Erkunden Sie die Leitfäden für den schnellen Einstieg und die Beispiele im [GitHub-Repository zu Azure Arc](https://github.com/microsoft/azure_arc).
- Lernen Sie die [unterstützten Szenarien für Server mit Azure Arc-Unterstützung](../../azure-arc/servers/overview.md#supported-cloud-operations) kennen.
- Erfahren Sie, [welche Kubernetes-Distributionen von Azure Arc unterstützt werden](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions).
