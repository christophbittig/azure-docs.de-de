---
title: 'Microsoft Defender für Kubernetes: Vorteile und Features'
description: Erfahren Sie mehr über die Vorteile und Features von Microsoft Defender für Kubernetes.
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: overview
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: b3e9b55fae1a7d00c191838f637d03248386c4dc
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526743"
---
# <a name="introduction-to-microsoft-defender-for-kubernetes"></a>Einführung in Microsoft Defender für Kubernetes

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für Kubernetes ist der Plan für erhöhte Sicherheit, der Ihren Kubernetes-Clustern unabhängig von ihrem Ausführungsort Schutz bietet.

Defender für Cloud eignet sich zum Schutz von Clustern in u. a.:

- **Azure Kubernetes Service (AKS):** Der verwaltete Dienst von Microsoft für die Entwicklung, Bereitstellung und Verwaltung von containerisierten Anwendungen

- **Lokale Umgebungen und Multicloudumgebungen:** Verwenden einer [Erweiterung für Kubernetes mit Azure Arc-Unterstützung](defender-for-kubernetes-azure-arc.md)

Microsoft Defender für Cloud und AKS bilden eine cloudnative Kubernetes-Sicherheitslösung mit Umgebungshärtung, Workloadschutz und Laufzeitschutz, wie unter [Containersicherheit in Defender für Cloud](container-security.md) beschrieben.

Die Bedrohungserkennung auf Hostebene für Ihre Linux-AKS-Knoten ist verfügbar, wenn Sie [Microsoft Defender für Server](defender-for-servers-introduction.md) und den zugehörigen Log Analytics-Agent aktivieren. Wenn Ihr Cluster jedoch in einer VM-Skalierungsgruppe für Azure Kubernetes Service bereitgestellt wird, wird der Log Analytics-Agent derzeit nicht unterstützt.



## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|**Microsoft Defender für Kubernetes** wird gemäß den Angaben auf der Seite [Preise](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet.|
|Erforderliche Rollen und Berechtigungen:|**Sicherheitsadministrator** kann Warnungen verwerfen.<br>Der **Sicherheitsleseberechtigte** kann Ergebnisse anzeigen.|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-kubernetes"></a>Welche Vorteile bietet Microsoft Defender für Kubernetes?

Microsoft Defender für Kubernetes bietet **Bedrohungsschutz auf Clusterebene** durch Überwachung der Clusterprotokolle.

Beispiele für sicherheitsrelevante Ereignisse, die von Microsoft Defender für Kubernetes überwacht werden, sind verfügbar gemachte Kubernetes-Dashboards und die Erstellung von Rollen mit hohen Berechtigungen und von sensiblen Einbindungen. Eine vollständige Liste der Warnungen auf Clusterebene finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-k8scluster).

> [!TIP]
> Sie können Containerwarnungen simulieren, indem Sie die Anweisungen in [diesem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270) befolgen.

Außerdem wird die Bedrohungslandschaft von unserem globalen Team von Sicherheitsforschern ständig überwacht. Sie fügen containerspezifische Warnungen und Sicherheitsrisiken hinzu, sobald sie erkannt werden.

>[!NOTE]
> Defender für Cloud generiert Sicherheitswarnungen für Aktionen und Bereitstellungen, die nach der Aktivierung des Plans „Defender für Kubernetes“ in Ihrem Abonnement ausgeführt werden.

## <a name="faq---microsoft-defender-for-kubernetes"></a>Häufig gestellte Fragen: Microsoft Defender für Kubernetes

- [Kann ich Schutzmaßnahmen ohne den Log Analytics-Agent erhalten?](#can-i-still-get-cluster-protections-without-the-log-analytics-agent)
- [Erlaubt AKS mir, benutzerdefinierte VM-Erweiterungen auf meinen AKS-Knoten zu installieren?](#does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes)
- [Wenn auf meinem Cluster bereits ein Agent für Azure Monitor für Containers ausgeführt wird, brauche ich dann auch den Log Analytics-Agent?](#if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too)
- [Unterstützt Microsoft Defender für Kubernetes AKS mit VMSS-Knoten (VM-Skalierungsgruppe)?](#does-microsoft-defender-for-kubernetes-support-aks-with-virtual-machine-scale-set-nodes)

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Kann ich Schutzmaßnahmen ohne den Log Analytics-Agent erhalten?

Der Plan **Microsoft Defender für Kubernetes** bietet Schutz auf Clusterebene. Wenn Sie außerdem den Log Analytics-Agent von **Microsoft Defender für Server** bereitstellen, erhalten Sie den Bedrohungsschutz für Ihre Knoten, der mit diesem Plan bereitgestellt wird. Weitere Informationen finden Sie unter [Einführung in Microsoft Defender für Server](defender-for-servers-introduction.md).

Es wird empfohlen, beide Komponenten bereitzustellen, um einen möglichst umfassenden Schutz zu gewährleisten.

Wenn Sie den Agent nicht auf Ihren Hosts installieren, profitieren Sie nicht von allen Vorteilen und Sicherheitswarnungen des Bedrohungsschutzes. Sie erhalten weiterhin Warnungen im Zusammenhang mit der Netzwerkanalyse und der Kommunikation mit schädlichen Servern.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Erlaubt AKS mir, benutzerdefinierte VM-Erweiterungen auf meinen AKS-Knoten zu installieren?

Damit Defender für Cloud Ihre AKS-Knoten überwachen kann, muss auf diesen der Log Analytics-Agent ausgeführt werden.

AKS ist ein verwalteter Dienst, und da der Log Analytics-Agent eine von Microsoft verwaltete Erweiterung ist, wird er auch auf AKS-Clustern unterstützt. Wenn Ihr Cluster jedoch in einer VM-Skalierungsgruppe für Azure Kubernetes Service bereitgestellt wird, wird der Log Analytics-Agent derzeit nicht unterstützt.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Wenn auf meinem Cluster bereits ein Agent für Azure Monitor für Containers ausgeführt wird, brauche ich dann auch den Log Analytics-Agent?

Damit Defender für Cloud Ihre Knoten überwachen kann, muss auf diesen der Log Analytics-Agent ausgeführt werden.

Wenn auf Ihren Clustern bereits der Azure Monitor für Container-Agent ausgeführt wird, können Sie auch den Log Analytics-Agent installieren, und die beiden Agents können problemlos nebeneinander verwendet werden.

[Erfahren Sie mehr über den Azure Monitor für Container-Agent](../azure-monitor/containers/container-insights-manage-agent.md).

### <a name="does-microsoft-defender-for-kubernetes-support-aks-with-virtual-machine-scale-set-nodes"></a>Unterstützt Microsoft Defender für Kubernetes AKS mit VMSS-Knoten (VM-Skalierungsgruppe)?

Wenn Ihr Cluster in einer VM-Skalierungsgruppe für Azure Kubernetes Service bereitgestellt wurde, wird der Log Analytics-Agent derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über den Kubernetes-Schutz in Defender für Cloud erfahren, einschließlich Microsoft Defender für Kubernetes.

> [!div class="nextstepaction"]
> [Aktivieren des erweiterten Schutzes](enable-enhanced-security.md)

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Streamen von Warnungen in eine SIEM-, SOAR- oder IT Service Management-Lösung](export-to-siem.md)
- [Verweistabelle zu Warnungen](alerts-reference.md)
