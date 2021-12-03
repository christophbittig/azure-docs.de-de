---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 10/18/2021
ms.topic: include
ms.openlocfilehash: 74209403f26404435a58e76efffd69e54435d221
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354166"
---
Defender für Cloud bietet einen Echtzeit-Bedrohungsschutz für Ihre Containerumgebungen und generiert Warnungen für verdächtige Aktivitäten. Mit diesen Informationen können Sie schnell Sicherheitsprobleme lösen und die Sicherheit Ihrer Container verbessern.

Defender für Cloud bietet Bedrohungsschutz auf unterschiedlichen Ebenen: 

* **Hostebene (bereitgestellt von Microsoft Defender für Server):** Mit dem gleichen Log Analytics-Agent, der von Defender für Cloud auch auf anderen VMs verwendet wird, überwacht Microsoft Defender für Server Ihre Linux-Kubernetes-Knoten auf verdächtige Aktivitäten wie Webshellerkennung und Verbindungen mit bekannten verdächtigen IP-Adressen. Der Agent führt bei der Überwachung auch containerspezifische Analysen durch, z. B. für die Erstellung privilegierter Container, den verdächtigen Zugriff auf API-Server und SSH-Server (Secure Shell), die in einem Docker-Container ausgeführt werden.

    Wenn Sie die Agents nicht auf Ihren Hosts installieren, profitieren Sie nicht von allen Vorteilen und Sicherheitswarnungen des Bedrohungsschutzes. Sie erhalten weiterhin Warnungen im Zusammenhang mit der Netzwerkanalyse und der Kommunikation mit schädlichen Servern.

    >[!IMPORTANT]
    > Derzeit wird die Installation des Log Analytics-Agents in Azure Kubernetes Service-Clustern, die in VM-Skalierungsgruppen ausgeführt werden, nicht unterstützt.

    Eine Liste der Warnungen auf Hostebene finden Sie in der [Referenztabelle der Warnungen](../articles/security-center/alerts-reference.md#alerts-containerhost).


* **Clusterebene (bereitgestellt von Microsoft Defender für Kubernetes):** Auf Clusterebene basiert der Bedrohungsschutz auf der Analyse der Überwachungsprotokolle von Kubernetes. Aktivieren Sie die erweiterten Sicherheitsfeatures, um diese Überwachung **ohne Agent** zu ermöglichen. Wenn Ihr Cluster lokal oder von einem anderen Cloudanbieter gehostet wird, aktivieren Sie [Kubernetes mit Azure Arc-Unterstützung und die Defender-Erweiterung](../articles/security-center/defender-for-kubernetes-azure-arc.md).

    Defender für Cloud überwacht die Protokolle Ihres Clusters, um Warnungen auf dieser Ebene zu generieren. Beispiele für Ereignisse auf dieser Ebene sind verfügbar gemachte Kubernetes-Dashboards und die Erstellung von Rollen mit hohen Berechtigungen und von sensiblen Einbindungen.

    >[!NOTE]
    > Defender für Cloud generiert Sicherheitswarnungen für Aktionen und Bereitstellungen, die nach der Aktivierung des Plans „Defender für Kubernetes“ in Ihrem Abonnement ausgeführt werden. 

    Eine Liste der Warnungen auf Clusterebene finden Sie in der [Referenztabelle der Warnungen](../articles/security-center/alerts-reference.md#alerts-k8scluster).

Außerdem wird die Bedrohungslandschaft von unserem globalen Team von Sicherheitsforschern ständig überwacht. Sie fügen containerspezifische Warnungen und Sicherheitsrisiken hinzu, sobald sie erkannt werden.

> [!TIP]
> Sie können Containerwarnungen simulieren, indem Sie die Anweisungen in [diesem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270) befolgen.
