---
title: Konzepte – Sicherheit in Azure Kubernetes Service (AKS)
description: Erfahren Sie mehr über die Sicherheit in Azure Kubernetes Service (AKS), einschließlich Master- und Knoten-Kommunikation, Netzwerkrichtlinien und Kubernetes-Geheimnisse.
services: container-service
author: miwithro
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: miwithro
ms.openlocfilehash: b29d7f245ce809745665bbeb4ea5cb858014e23b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490760"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Sicherheitskonzepte für Anwendungen und Cluster in Azure Kubernetes Service (AKS)

Die Containersicherheit schützt die gesamte End-to-End-Pipeline vom Build bis zu den Anwendungsworkloads, die in Azure Kubernetes Service (AKS) ausgeführt werden.

Die sichere Lieferkette umfasst die Buildumgebung und die Registrierung.

Kubernetes enthält Sicherheitskomponenten wie *Podsicherheitsstandards* und *Geheimnisse*. In der Zwischenzeit umfasst Azure Komponenten wie Active Directory, Microsoft Defender für Cloud, Azure Policy, Azure Key Vault, Netzwerksicherheitsgruppen und orchestrierte Clusterupgrades. AKS kombiniert diese Sicherheitskomponenten zu folgenden Zwecken:
* Geben Sie eine vollständige Authentifizierungs- und Autorisierungsgeschichte an.
* Nutzen Sie integrierte AKS-Azure Policy, um Ihre Anwendungen zu schützen.
* End-to-End-Erkenntnis vom Build über Ihre Anwendung mit Microsoft Defender für Container.
* Ausführen der neuesten Betriebssystem-Sicherheitsupdates und Kubernetes-Versionen im AKS-Cluster
* Bereitstellen eines sicheren Datenverkehrs zwischen Pods und sicheren Zugriffs auf sensible Anmeldeinformationen

In diesem Artikel werden die wichtigsten Konzepte vorgestellt, mit denen Sie Anwendungen in AKS schützen können:

- [Sicherheitskonzepte für Anwendungen und Cluster in Azure Kubernetes Service (AKS)](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Sicherheit](#build-security)
  - [Registrierungssicherheit](#registry-security)
  - [Clustersicherheit](#cluster-security)
  - [Knotensicherheit](#node-security)
    - [Computeisolation](#compute-isolation)
  - [Clusterupgrades](#cluster-upgrades)
    - [Absperren und Ausgleichen](#cordon-and-drain)
  - [Netzwerksicherheit](#network-security)
    - [Azure-Netzwerksicherheitsgruppen](#azure-network-security-groups)
  - [Anwendungssicherheit](#application-security)
  - [Kubernetes-Geheimnisse](#kubernetes-secrets)
  - [Nächste Schritte](#next-steps)

## <a name="build-security"></a>Buildsicherheit

Als Einstiegspunkt für die Lieferkette ist es wichtig, eine statische Analyse von Imagebuilds durchzuführen, bevor sie über die Pipeline heraufgestuft werden. Dies schließt die Sicherheitsrisiko- und Konformitätsbewertung ein.  Es geht nicht darum, einen Build abzubrechen, da er ein hohes Sicherheitsrisiko aufwies, da dies die Entwicklung unterbricht. Es geht darum, den „Anbieterstatus“ zu untersuchen, um basierend auf Sicherheitsrisiken segmentieren zu können, die von den Entwicklungsteams umsetzbar sind.  Nutzen Sie auch „Toleranzperioden“, um Entwicklern Zeit zum Beheben identifizierter Probleme zu geben. 

## <a name="registry-security"></a>Registrierungssicherheit

Durch die Bewertung des Sicherheitsrisikostatus des Images in der Registrierung werden Abweichungen erkannt und auch Images abgefangen, die nicht aus Ihrer Buildumgebung stammen. Verwenden Sie [Notary V2](https://github.com/notaryproject/notaryproject), um Signaturen an Ihre Images anzufügen, um sicherzustellen, dass Bereitstellungen von einem vertrauenswürdigen Speicherort stammen.

## <a name="cluster-security"></a>Clustersicherheit

In AKS sind die Kubernetes-Masterkomponenten Bestandteil des verwalteten Diensts, der von Microsoft bereitgestellt, verwaltet und gewartet wird. Jeder AKS-Cluster verfügt über seinen eigenen dedizierten Kubernetes-Master mit einem einzelnen Mandanten, um API-Server, Scheduler usw. bereitzustellen.

Standardmäßig verwendet der Kubernetes-API-Server eine öffentliche IP-Adresse und einen vollqualifizierten Domänennamen (FQDN). Sie können den Zugriff auf den API-Serverendpunkt mithilfe von [autorisierten IP-Adressbereichen][authorized-ip-ranges] einschränken. Alternativ können Sie einen vollständig [privaten Cluster][private-clusters] erstellen, um den Zugriff des API-Servers auf Ihr virtuelles Netzwerk einzuschränken.

Mithilfe der rollenbasierten Zugriffssteuerung von Kubernetes (Kubernetes RBAC) und Azure RBAC können Sie den Zugriff auf den API-Server steuern. Weitere Informationen finden Sie unter [Azure AD-Integration mit AKS][aks-aad].

## <a name="node-security"></a>Knotensicherheit

AKS-Knoten sind virtuelle Azure-Computer (VMs), die von Ihnen verwaltet und gewartet werden. 
* Auf Linux-Knoten wird eine optimierte Ubuntu-Distribution mit `containerd` oder Docker als Containerruntime ausgeführt. 
* Auf Windows Server-Knoten wird eine optimierte Version von Windows Server 2019 mit `containerd` oder Docker als Containerruntime ausgeführt.

Wenn ein AKS-Cluster erstellt oder zentral hochskaliert wird, werden die Knoten automatisch mit den aktuellen Betriebssystem-Sicherheitsupdates und -konfigurationen bereitgestellt.

> [!NOTE]
> AKS-Cluster mit:
> * Kubernetes Version 1.19 und höher für Linux-Knotenpools verwenden `containerd` als Containerruntime. Die Verwendung von `containerd` mit Windows Server 2019-Knotenpools befindet sich derzeit in der Vorschau. Weitere Informationen finden Sie unter [Hinzufügen eines Windows Server-Knotenpools mit `containerd`][aks-add-np-containerd].
> * Ältere Kubernetes-Versionen (vor Version 1.19) für Linux-Knotenpools verwenden Docker als Containerruntime. Für Windows Server 2019-Knotenpools wird Docker als Standardcontainerruntime verwendet.

### <a name="node-security-patches"></a>Sicherheitspatches für Knoten

#### <a name="linux-nodes"></a>Linux-Knoten
Jeden Abend werden über die Sicherheitsupdate-Verteilungskanäle Sicherheitspatches für Linux-Knoten in AKS zur Verfügung gestellt. Dieses Verhalten wird im Rahmen der Bereitstellung von Knoten in einem AKS-Cluster automatisch konfiguriert. Neustarts werden für Knoten nicht automatisch ausgeführt, wenn ein Sicherheitspatch oder Kernelupdate es erfordern würde, um Störungen und eventuelle negative Einflüsse auf ausgeführte Workloads zu minimieren. Weitere Informationen zum Umgang mit Knotenneustarts finden Sie im Artikel [Anwenden von Sicherheits- und Kernelupdates auf Linux-Knoten in Azure Kubernetes Service (AKS)][aks-kured].

Nächtliche Updates wenden Sicherheitsupdates auf das Betriebssystem auf dem Knoten an, aber das Knotenimage, das zum Erstellen von Knoten für Ihren Cluster verwendet wird, bleibt unverändert. Wenn Ihrem Cluster ein neuer Linux-Knoten hinzugefügt wird, wird das ursprüngliche Image zum Erstellen des Knotens verwendet. Dieser neue Knoten empfängt alle Sicherheits- und Kernelupdates, die während der automatischen Überprüfung jede Nacht verfügbar sind, bleibt jedoch ungepatcht, bis alle Überprüfungen und Neustarts abgeschlossen sind. Sie können das Knotenimageupgrade verwenden, um nach Knotenimages zu suchen und diese zu aktualisieren, die von Ihrem Cluster verwendet werden. Ausführlichere Informationen zu Knotenimageupgrades finden Sie unter [Upgrade für AKS-Knotenimages (Azure Kubernetes Service)][node-image-upgrade].

#### <a name="windows-server-nodes"></a>Windows Server-Knoten

Für Windows Server-Knoten werden die neuesten Updates von Windows Update nicht automatisch ausgeführt und angewendet. Planen Sie Upgrades des Windows Server-Knotenpools in Ihrem AKS-Cluster passend zum regulären Windows Update-Freigabezyklus und Ihrem eigenen Überprüfungsprozess. Während dieses Upgrades werden Knoten erstellt, die das neueste Windows Server-Image und die neuesten Windows Server-Patches ausführen und die älteren Knoten entfernen. Weitere Informationen zu diesem Prozess finden Sie unter [Durchführen eines Upgrades für einen Knotenpool in AKS][nodepool-upgrade].

### <a name="node-deployment"></a>Knotenbereitstellung
Knoten werden in einem Subnetz des privaten virtuellen Netzwerks ohne öffentliche IP-Adresse bereitgestellt. Zur Problembehandlung und Verwaltung ist SSH standardmäßig aktiviert, und es kann nur über die interne IP-Adresse darauf zugegriffen werden.

### <a name="node-storage"></a>Knotenspeicher
Die Knoten verwenden Azure Managed Disks, um Speicher bereitzustellen. Bei den meisten VM-Knotengrößen handelt es sich bei Azure Managed Disks um Premium-Datenträger, die von Hochleistungs-SSDs unterstützt werden. Die auf verwalteten Datenträgern gespeicherten Daten werden im Ruhezustand auf der Azure-Plattform automatisch verschlüsselt. Zur Verbesserung der Redundanz werden Azure Managed Disks sicher im Azure-Rechenzentrum repliziert.

### <a name="hostile-multi-tenant-workloads"></a>Feindliche Workloads mit mehreren Mandanten

Derzeit sind Kubernetes-Umgebungen nicht sicher vor einer feindlichen Verwendung mit mehreren Mandanten. Mithilfe zusätzlicher Sicherheitsfeatures wie *Podsicherheitsrichtlinien* oder Kubernetes RBAC für Knoten können Exploits effizient blockiert werden. Um echte Sicherheit bei der Ausführung feindlicher Workloads mit mehreren Mandanten zu erzielen, vertrauen Sie nur einem Hypervisor. Die Sicherheitsdomäne für Kubernetes wird zum gesamten Cluster und nicht zu einem einzelnen Knoten. 

Für diese Art von feindlichen Workloads mit mehreren Mandanten sollten Sie physisch isolierte Cluster verwenden. Weitere Informationen zu Möglichkeiten zur Isolierung von Workloads finden Sie unter [Bewährte Methoden für die Isolierung der Cluster in AKS][cluster-isolation].

### <a name="compute-isolation"></a>Computeisolation

Bestimmte Workloads erfordern möglicherweise aufgrund von Compliance- oder gesetzlichen Anforderungen einen hohen Grad an Isolation von anderen Kundenworkloads. Für diese Workloads bietet Azure [isolierte VMs](../virtual-machines/isolation.md) zur Verwendung als Agentknoten in einem AKS-Cluster. Diese VMs sind für einen bestimmten Hardwaretyp isoliert und für einen einzelnen Kunden bestimmt. 

Wählen Sie beim Erstellen eines AKS-Clusters oder Hinzufügen eines Knotenpools [eine der isolierten VM-Größen](../virtual-machines/isolation.md) als **Knotengröße** aus.

## <a name="cluster-upgrades"></a>Clusterupgrades

Azure bietet Tools für die Upgradeorchestrierung, um ein Upgrade eines AKS-Clusters und seiner Komponenten durchzuführen, Sicherheit und Compliance zu gewährleisten und auf die neuesten Features zuzugreifen. Diese Upgradeorchestrierung umfasst sowohl die Kubernetes-Master- als auch die Agent-Komponenten. 

Um den Upgradevorgang zu starten, geben Sie eine der [aufgelisteten, verfügbaren Kubernetes-Versionen](supported-kubernetes-versions.md) an. Azure verwendet dann für jeden AKS-Knoten den sicheren Vorgang des Absperrens und Ausgleichens und führt das Upgrade aus.

### <a name="cordon-and-drain"></a>Absperren und Ausgleichen

Während des Upgrades werden AKS-Knoten einzeln vom Cluster abgesperrt, damit auf ihnen keine neuen Pods geplant werden. Die Knoten werden dann wie folgt ausgeglichenund aktualisiert:

1.  Ein neuer Knoten wird im Knotenpool bereitgestellt. 
    * Dieser Knoten führt das neueste Betriebssystemimage und die neuesten Patches aus.
1. Einer der bereits vorhandenen Knoten wird für das Upgrade identifiziert. 
1. Pods auf dem identifizierten Knoten werden ordnungsgemäß beendet und auf den anderen Knoten im Knotenpool geplant.
1. Der geleerte Knoten wird aus dem AKS-Cluster gelöscht.
1. Die Schritte 1 bis 4 werden wiederholt, bis alle Knoten im Rahmen des Upgradevorgangs erfolgreich ersetzt wurden.

Weitere Informationen finden Sie unter [Aktualisieren eines AKS-Clusters][aks-upgrade-cluster].

## <a name="network-security"></a>Netzwerksicherheit

Für Konnektivität und Sicherheit bei lokalen Netzwerken können Sie Ihren AKS-Cluster in Subnetzen eines vorhandenen virtuellen Azure-Netzwerks bereitstellen. Diese virtuellen Netzwerke stellen über Azure-Site-to-Site-VPN oder ExpressRoute eine Verbindung zurück zu Ihrem lokalen Netzwerk her. Definieren Sie Kubernetes-Eingangscontroller mit privaten, internen IP-Adressen, um den Zugriff von Diensten auf die interne Netzwerkverbindung zu beschränken.

### <a name="azure-network-security-groups"></a>Azure-Netzwerksicherheitsgruppen

Zum Filtern des Datenverkehrsflusses in virtuellen Netzwerken verwendet Azure Netzwerksicherheitsgruppen-Regeln. Diese Regeln bestimmen die Quell- und Ziel-IP-Adressbereiche, Ports und Protokolle, denen der Zugriff auf Ressourcen gewährt oder verweigert wird. Standardregeln werden erstellt, um TLS-Datenverkehr zum Kubernetes-API-Server zu ermöglichen. Sie erstellen Dienste mit Lastenausgleichsmodulen, Portzuordnungen oder Eingangsrouten. AKS ändert automatisch die Netzwerksicherheitsgruppe für den Datenverkehrsfluss.

Wenn Sie Ihr eigenes Subnetz für Ihren AKS-Cluster (egal ob Azure CNI oder Kubenet) bereitstellen, ändern Sie **nicht** die von AKS verwaltete Netzwerksicherheitsgruppe auf NIC-Ebene. Erstellen Sie stattdessen weitere Netzwerksicherheitsgruppen auf Subnetzebene, um den Datenverkehrsfluss zu ändern. Stellen Sie sicher, dass diese nicht den für die Verwaltung des Clusters erforderlichen Datenverkehr beeinträchtigen, z. B. den Zugriff auf das Lastenausgleichsmodul, die Kommunikation mit der Steuerungsebene und den [ausgehenden][aks-limit-egress-traffic] Datenverkehr.

### <a name="kubernetes-network-policy"></a>Kubernetes-Netzwerkrichtlinie

Zur Einschränkung von Netzwerkdatenverkehr zwischen Pods in Ihrem Cluster bietet AKS Unterstützung für [Kubernetes-Netzwerkrichtlinien][network-policy]. Mit Netzwerkrichtlinien können Sie den Zugriff auf bestimmte Netzwerkpfade im Cluster basierend auf Namespaces und Bezeichnungsselektoren zulassen oder verweigern.

## <a name="application-security"></a>Anwendungssicherheit

Nutzen Sie zum Schutz von Pods, die in AKS ausgeführt werden, [Microsoft Defender für Kubernetes][azure-defender-for-kubernetes], um Cyberangriffe auf Ihre Anwendungen zu erkennen und einzuschränken, die in Ihren Pods ausgeführt werden.  Führen Sie kontinuierliche Überprüfungen durch, um Abweichungen im Sicherheitsrisikostatus Ihrer Anwendung zu erkennen, und implementieren Sie einen „blauen/grünen/Canary-“Prozess, um die anfälligen Images zu patchen und zu ersetzen. 


## <a name="kubernetes-secrets"></a>Kubernetes-Geheimnisse

Mit einem Kubernetes-*Geheimnis* fügen Sie sensible Daten wie Anmeldeinformationen oder Schlüssel für den Zugriff in Pods ein. 
1. Erstellen Sie ein Geheimnis mit der Kubernetes-API. 
1. Definieren Sie Ihren Pod oder Ihre Bereitstellung, und fordern Sie ein bestimmtes Geheimnis an. 
    * Geheimnisse werden nur für Knoten mit einem geplanten Pod bereitgestellt, der diese erfordert.
    * Das Geheimnis wird in *tmpfs* gespeichert und nicht auf den Datenträger geschrieben. 
1. Wenn Sie den letzten Pod auf einem Knoten löschen, der ein Geheimnis erfordert, wird das Geheimnis aus „tmpfs“ des Knotens gelöscht. 
   * Geheimnisse werden in einem bestimmten Namespace gespeichert, und nur Pods im gleichen Namespace können darauf zugreifen.

Die Verwendung von Geheimnissen reduziert die vertraulichen Informationen, die im YAML-Manifest für den Pod oder den Dienst definiert werden. Stattdessen fordern Sie im Rahmen Ihres YAML-Manifests das im Kubernetes-API-Server gespeicherte Geheimnis an. Mit diesem Ansatz erhält nur der spezielle Pod Zugriff auf das Geheimnis. 

> [!NOTE]
> Die unformatierten geheimen Manifestdateien enthalten die geheimen Daten im Base64-Format (weitere Einzelheiten finden Sie in der [offiziellen Dokumentation][secret-risks]). Behandeln Sie diese Dateien wie vertrauliche Informationen, und committen Sie sie niemals in die Quellcodeverwaltung.

Kubernetes-Geheimnisse werden in „etcd“ gespeichert, einem verteilten Schlüssel-Wert-Speicher. Der etcd-Speicher wird vollständig von AKS verwaltet, und [Daten werden im Ruhezustand innerhalb der Azure Platform verschlüsselt][encryption-atrest]. 

## <a name="next-steps"></a>Nächste Schritte

Die ersten Schritte zum Sichern Ihrer AKS-Cluster sind unter [Aktualisieren eines AKS-Clusters][aks-upgrade-cluster] beschrieben.

Entsprechende bewährte Methoden finden Sie unter [Best Practices für Clustersicherheit und Upgrades in Azure Kubernetes Service (AKS)][operator-best-practices-cluster-security] und [Best Practices für Podsicherheit in Azure Kubernetes Service (AKS)][developer-best-practices-pod-security].

Weitere Informationen zu den wesentlichen Konzepten von Kubernetes und AKS finden Sie unter folgenden Themen:

- [Kubernetes/AKS: Cluster und Workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS: Identität][aks-concepts-identity]
- [Kubernetes/AKS: virtuelle Netzwerke][aks-concepts-network]
- [Kubernetes/AKS: Speicher][aks-concepts-storage]
- [Kubernetes/AKS: Skalierung][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[azure-defender-for-kubernetes]: ../defender-for-cloud/container-security.md
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-add-np-containerd]: windows-container-cli.md#add-a-windows-server-node-pool-with-containerd-preview
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-kured]: node-updates-kured.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
[node-image-upgrade]: node-image-upgrade.md
