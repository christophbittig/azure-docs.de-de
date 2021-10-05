---
title: Konzepte – Netzwerke in Azure Kubernetes Service (AKS)
description: Lernen Sie Netzwerke in Azure Kubernetes Service (AKS) kennen, einschließlich kubenet- und Azure CNI-Netzwerke, Eingangscontroller, Lastenausgleichsmodule und statische IP-Adressen.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: bdedde17692aca614c3d33b884950a4cd01b02e4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610710"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Netzwerkkonzepte für Anwendungen in Azure Kubernetes Service (AKS)

Bei einem containerbasierten Ansatz mit Microservices für die Anwendungsentwicklung arbeiten die Anwendungskomponenten zusammen, um die jeweiligen Aufgaben zu verarbeiten. Kubernetes bietet verschiedene Ressourcen, die diese Zusammenarbeit ermöglichen:
* Sie können eine Verbindung mit Anwendungen herstellen und diese intern oder extern verfügbar machen. 
* Sie können hochverfügbare Anwendungen erstellen, indem Sie für Ihre Anwendungen einen Lastenausgleich vornehmen. 
* Für komplexere Anwendungen können Sie den eingehenden Datenverkehr für die SSL/TLS-Terminierung oder ein Weiterleiten mehrerer Komponenten konfigurieren. 
* Aus Sicherheitsgründen können Sie den Fluss des Netzwerkdatenverkehrs in oder zwischen Pods und Knoten beschränken.

In diesem Artikel werden die wichtigsten Konzepte vorgestellt, mit denen Sie Netzwerke für Ihre Anwendungen in AKS bereitstellen:

- [Dienste](#services)
- [Virtuelle Azure-Netzwerke](#azure-virtual-networks)
- [Eingangscontroller](#ingress-controllers)
- [Netzwerkrichtlinien](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes-Grundlagen

Um den Zugriff auf Ihre Anwendungen oder zwischen Anwendungskomponenten zu erlauben, stellt Kubernetes eine Abstraktionsschicht für virtuelle Netzwerke bereit. Kubernetes-Knoten stellen eine Verbindung mit einem virtuellen Netzwerk her und bieten dadurch eingehende und ausgehende Konnektivität für Pods. Die Komponente *kube-proxy*, die auf jedem Knoten ausgeführt wird, stellt diese Netzwerkfunktionen bereit.

In Kubernetes:
* Pods werden von *Diensten* logisch gruppiert, um den direkten Zugriff auf einen bestimmten Port über eine IP-Adresse oder einen DNS-Namen zu erlauben. 
* Sie können Datenverkehr über einen *Lastenausgleich* verteilen. 
* Ein komplexeres Routing von Anwendungsdatenverkehr kann auch mit *Eingangscontrollern* erzielt werden. 
* Die Sicherheit und das Filtern des Netzwerkdatenverkehrs für Pods kann mit Kubernetes-*Netzwerkrichtlinien* ermöglicht werden.

Außerdem vereinfacht die Azure-Plattform virtuelle Netzwerke für AKS-Cluster. Beim Erstellen eines Kubernetes-Lastenausgleichs erstellen und konfigurieren Sie auch die zugrunde liegende Azure Load Balancer-Ressource. Wenn Sie Netzwerkports für Pods öffnen, werden die entsprechenden Regeln für Azure-Netzwerksicherheitsgruppen konfiguriert. Für das HTTP-Anwendungsrouting kann Azure auch *externes DNS* konfigurieren, wenn neue Eingangsrouten konfiguriert werden.

## <a name="services"></a>Dienste

Kubernetes verwendet *Dienste*, um die Netzwerkkonfiguration für Anwendungsworkloads zu vereinfachen. Dabei werden eine Reihe von Pods logisch miteinander gruppiert und Netzwerkkonnektivität bereitgestellt. Folgende Arten von Diensten sind verfügbar:

- **Cluster IP** 
  
  Erstellt eine interne IP-Adresse zur Verwendung innerhalb des AKS-Clusters. Ideal für rein interne Anwendungen, die andere Workloads im Cluster unterstützen.

    ![Diagramm mit Cluster IP-Datenverkehrsfluss in einem AKS-Cluster][aks-clusterip]

- **NodePort** 

  Erstellt eine Port-Zuordnung auf dem zugrunde liegenden Knoten, über die mithilfe von Knoten-IP-Adresse und Port der direkte Zugriff auf die Anwendung erfolgen kann.

    ![Diagramm mit NodePort-Datenverkehrsfluss in einem AKS-Cluster][aks-nodeport]

- **LoadBalancer** 

  Erstellt eine Azure Load Balancer-Ressource, konfiguriert eine externe IP-Adresse und verbindet die angeforderten Pods mit dem Back-End-Pool des Load Balancer. An den gewünschten Ports werden Regeln für den Lastenausgleich erstellt, damit der Datenverkehr der Kunden die Anwendung erreichen kann. 

    ![Diagramm mit Load Balancer-Datenverkehrsfluss in einem AKS-Cluster][aks-loadbalancer]

    Als zusätzliche Steuerung und für das Routing des eingehenden Datenverkehrs können Sie auch einen [Eingangscontroller](#ingress-controllers) verwenden.

- **ExternalName** 

  Erstellt einen bestimmten DNS-Eintrag für einen einfacheren Anwendungszugriff.

Die IP-Adresse für Lastenausgleichsmodule und Dienste kann entweder dynamisch zugewiesen werden, oder Sie können eine vorhandene statische IP-Adresse angeben. Sie können sowohl interne als auch externe statische IP-Adressen zuweisen. Vorhandene statische IP-Adressen sind häufig an einen DNS-Eintrag gebunden.

Sie können sowohl *interne* als auch *externe* Lastenausgleichsmodule erstellen. Internen Lastenausgleichsmodulen wird nur eine private IP-Adresse zugeordnet, sodass nicht über das Internet darauf zugegriffen werden kann.

## <a name="azure-virtual-networks"></a>Virtuelle Azure-Netzwerke

In AKS können Sie einen Cluster bereitstellen, der eines der beiden folgenden Netzwerkmodelle verwendet:

- *Kubenet*-Netzwerke

  Die Netzwerkressourcen werden normalerweise bei der Bereitstellung des AKS-Clusters erstellt und konfiguriert.

- *Azure Container Networking Interface (CNI)* -Netzwerke
 
  Der AKS-Cluster ist mit vorhandenen virtuellen Netzwerkressourcen und -konfigurationen verbunden.

### <a name="kubenet-basic-networking"></a>Kubenet-Netzwerke – „Basic“ (Grundlegend)

Die Netzwerkoption *kubenet* ist die Standardkonfiguration für die AKS-Clustererstellung. Mit *kubenet* geschieht Folgendes:
1. Knoten erhalten eine IP-Adresse aus dem Subnetz des virtuellen Azure-Netzwerks. 
1. Pods erhalten eine IP-Adresse aus einem logisch anderen Adressraum als dem Subnetz des virtuellen Azure-Netzwerks der Knoten. 
1. Die Netzwerkadressübersetzung (NAT, Network Address Translation) wird dann so konfiguriert, dass die Pods Ressourcen im virtuellen Azure-Netzwerk erreichen können. 
1. Die Quell-IP-Adresse des Datenverkehrs wird in die primäre IP-Adresse des Knotens übersetzt.

Knoten verwenden das Kubernetes-Plug-In [kubenet][kubenet]. Sie haben folgende Möglichkeiten:
* Lassen Sie die Azure-Plattform die virtuellen Netzwerke für Sie erstellen und konfigurieren. 
* Stellen Sie Ihren AKS-Cluster in einem bestehenden Subnetz des virtuellen Netzwerks bereit. 

Denken Sie daran, dass nur die Knoten eine routingfähige IP-Adresse erhalten. Die Pods verwenden NAT, um mit anderen Ressourcen außerhalb des AKS-Clusters zu kommunizieren. Dieser Ansatz reduziert die Anzahl der IP-Adressen, die Sie in Ihrem Netzwerkadressraum für die Verwendung von Pods reservieren müssen.

Weitere Informationen finden Sie unter [Konfigurieren von kubernet-Netzwerken für AKS-Cluster][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI-Netzwerke – „Advanced“ (Erweitert)

Mit Azure CNI erhält jeder Pod eine IP-Adresse aus dem Subnetz und kann direkt angesprochen werden. Diese IP-Adressen müssen im Voraus geplant werden und in Ihrem Netzwerkadressraum eindeutig sein. Jeder Knoten verfügt über einen Konfigurationsparameter für die maximale Anzahl von Pods, die er unterstützt. Die entsprechende Anzahl von IP-Adressen pro Knoten wird dann im Voraus reserviert. Ohne Planung kann dieser Ansatz dazu führen, dass sich bei zunehmenden Anwendungsanforderungen die IP-Adressen erschöpfen oder dass Cluster in einem größeren Subnetz neu erstellt werden müssen.

Anders als bei Kubenet wird der Datenverkehr zu Endpunkten in demselben virtuellen Netzwerk nicht per NAT zur primären IP-Adresse des Knotens geleitet. Die Quelladresse für den Datenverkehr im virtuellen Netzwerk ist die Pod-IP-Adresse. Datenverkehr außerhalb des virtuellen Netzwerks wird weiterhin per NAT zur primären IP-Adresse des Knotens geleitet.

Knoten verwenden das Kubernetes-Plug-In [Azure CNI][cni-networking].

![Diagramm mit zwei Knoten jeweils mit Bridges für die Verbindungsherstellung mit einem Azure VNET][advanced-networking-diagram]

Weitere Informationen finden Sie unter [Konfigurieren der Azure CNI für einen AKS-Cluster][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Vergleich der Netzwerkmodelle

Sowohl kubenet als auch Azure CNI bieten Netzwerkkonnektivität für Ihre AKS-Cluster. Es gibt jedoch jeweils Vor- und Nachteile. Ganz allgemein gelten die folgenden Überlegungen:

* **kubenet**
    * Spart IP-Adressraum
    * Verwendet den internen oder externen Lastenausgleich von Kubernetes, um die Pods von außerhalb des Clusters zu erreichen
    * Erfordert eine manuelle Verwaltung und Wartung von benutzerdefinierten Routen (UDRs)
    * Maximal 400 Knoten pro Cluster
* **Azure CNI**
    * Pods erhalten vollständige VM-Konnektivität und können direkt über die private IP-Adresse aus verbundenen Netzwerken erreicht werden.
    * Erfordert mehr IP-Adressraum

Folgende Unterschiede treten im Verhalten von kubenet und Azure CNI auf:

| Funktion                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Bereitstellen von Clustern in vorhandenen oder neuen virtuellen Netzwerken                                            | Unterstützt – benutzerdefinierte Routen werden manuell angewandt. | Unterstützt |
| Pod-Pod-Konnektivität                                                                         | Unterstützt | Unterstützt |
| Pod-VM-Konnektivität; VM im gleichen virtuellen Netzwerk                                          | Funktioniert, wenn vom Pod initiiert | Funktioniert in beide Richtungen |
| Pod-VM-Konnektivität; VM in virtuellem Peernetzwerk                                            | Funktioniert, wenn vom Pod initiiert | Funktioniert in beide Richtungen |
| Lokaler Zugriff per VPN oder ExpressRoute                                                | Funktioniert, wenn vom Pod initiiert | Funktioniert in beide Richtungen |
| Zugriff auf Ressourcen, die von Dienstendpunkten geschützt werden                                             | Unterstützt | Unterstützt |
| Verfügbarmachen von Kubernetes-Diensten über einen Lastenausgleichsdienst, App Gateway oder einen Eingangscontroller | Unterstützt | Unterstützt |
| Standard: Azure DNS und private Zonen                                                          | Unterstützt | Unterstützt |
| Unterstützung für Windows Knotenpools                                                               | Nicht unterstützt | Unterstützt |

DNS wird sowohl mit kubenet- als auch Azure CNI-Plug-In von CoreDNS angeboten, einer Bereitstellung, die in AKS mit eigener automatischer Skalierung ausgeführt wird. Weitere Informationen zu CoreDNS in Kubernetes finden Sie unter [Anpassen des DNS-Diensts](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). CoreDNS ist standardmäßig so konfiguriert, dass unbekannte Domänen an die DNS-Funktionalität der Azure Virtual Network-Instanz weitergeleitet werden, in der der AKS-Cluster bereitgestellt wird. Daher können Azure DNS und private Zonen für Pods verwendet werden, die in AKS ausgeführt werden.

### <a name="support-scope-between-network-models"></a>Supportumfang der Netzwerkmodelle

Unabhängig vom verwendeten Netzwerkmodell können kubenet und Azure CNI mit einer der folgenden Methoden bereitgestellt werden:

* Die Azure-Plattform kann die Ressourcen des virtuellen Netzwerks automatisch erstellen und konfigurieren, wenn Sie einen AKS-Cluster erstellen.
* Sie können die Ressourcen des virtuellen Netzwerks manuell erstellen und konfigurieren und an diese Ressourcen anfügen, wenn Sie Ihren AKS-Cluster erstellen.

Auch wenn Funktionen wie Dienstendpunkte oder benutzerdefinierte Routen (UDRs) mit kubenet und Azure CNI unterstützt werden, legen die [Supportrichtlinien für AKS][support-policies] fest, welche Änderungen Sie vornehmen können. Beispiel:

* Wenn Sie die Ressourcen des virtuellen Netzwerks für einen AKS-Cluster manuell erstellen, werden Sie unterstützt, wenn Sie Ihre eigenen benutzerdefinierten Routen oder Dienstendpunkte konfigurieren.
* Wenn die Azure-Plattform die Ressourcen des virtuellen Netzwerks automatisch für Ihren AKS-Cluster erstellt, können Sie diese von AKS verwalteten Ressourcen nicht manuell ändern, um Ihre eigenen benutzerdefinierten Routen oder Dienstendpunkte zu konfigurieren.

## <a name="ingress-controllers"></a>Eingangsdatencontroller

Beim Erstellen eines Diensts vom Typ „LoadBalancer“ erstellen Sie auch eine zugrunde liegende Azure Load Balancer-Ressource. Der Load Balancer wird zum Verteilen von Datenverkehr an die Pods in Ihrem Dienst an einem bestimmten Port konfiguriert. 

Der Load Balancer arbeitet nur auf Schicht 4. Auf Schicht 4 hat der Dienst keine Kenntnis von den tatsächlichen Anwendungen und kann keine weiteren Überlegungen zum Netzwerkrouting vornehmen.

*Eingangscontroller* fungieren auf Schicht 7 und können intelligentere Regeln zum Verteilen des Anwendungsdatenverkehrs verwenden. Eingangscontroller leiten HTTP-Datenverkehr in der Regel auf Grundlage der eingehenden URL an verschiedene Anwendungen weiter.

![Diagramm mit Eingangsdatenverkehrsfluss in einem AKS-Cluster][aks-ingress]

### <a name="create-an-ingress-resource"></a>Erstellen einer Eingangsressource
In AKS können Sie eine Dateneingangsressource mit NGINX, einem ähnlichen Tool oder der AKS-Funktion für das HTTP-Anwendungsrouting erstellen. Wenn Sie das HTTP-Anwendungsrouting für einen AKS-Cluster aktivieren, erstellt die Azure-Plattform den Eingangscontroller und einen *externen DNS-Controller*. Wenn in Kubernetes neue Eingangsressourcen erstellt werden, werden in einer clusterspezifischen DNS-Zone die erforderlichen DNS-A-Einträge erstellt. 

Weitere Informationen finden Sie unter [Bereitstellen von HTTP-Anwendungsrouting][aks-http-routing].

### <a name="application-gateway-ingress-controller-agic"></a>Application Gateway-Eingangscontroller (AGIC)

Mit dem ACIG-Add-On (Application Gateway Ingress Controller, Application Gateway-Eingangscontroller) nutzen AKS-Kunden den nativen L7-Lastenausgleich von Application Gateway in Azure, um Cloudsoftware im Internet verfügbar zu machen. AGIC überwacht den Kubernetes-Hostcluster und aktualisiert fortlaufend eine Application Gateway-Instanz, wodurch ausgewählte Dienste im Internet bereitgestellt werden. 

Weitere Informationen zum ACIG-Add-On für AKS finden Sie unter [Was ist der Application Gateway-Eingangscontroller?][agic-overview].

### <a name="ssltls-termination"></a>SSL/TLS-Terminierung

Die SSL/TLS-Terminierung ist ein weiteres allgemeines Feature des Dateneingangs. Bei großen Webanwendungen, auf die über HTTPS zugegriffen wird, übernimmt die Eingangsressource die TLS-Terminierung, sodass sie nicht innerhalb der Anwendung selbst erfolgt. Um die automatische Generierung und Konfiguration der TLS-Zertifizierung bereitzustellen, können Sie die Eingangsressource für die Verwendung von Anbietern wie „Let's Encrypt“ konfigurieren. 

Weitere Informationen zum Konfigurieren eines NGINX-Eingangscontrollers mit Let's Encrypt finden Sie unter [Eingang und TLS][aks-ingress-tls].

### <a name="client-source-ip-preservation"></a>Beibehaltung der Clienquell-IP

Konfigurieren Sie den Eingangscontroller so, dass die Quell-IP des Clients bei Anforderungen an Container im AKS-Cluster beibehalten wird. Wenn der Eingangscontroller die Anforderung eines Clients an einen Container im AKS-Cluster weiterleitet, ist die ursprüngliche Quell-IP dieser Anforderung für den Zielcontainer nicht verfügbar. Wenn Sie die *Beibehaltung der Clientquell-IP* aktivieren, ist die Quell-IP für den Client im Anforderungsheader unter *X-Forwarded-For* verfügbar. 

Wenn Sie die Beibehaltung der Clientquell-IP auf dem Eingangscontroller verwenden, können Sie kein TLS-Pass-Through verwenden. Die Beibehaltung der Clientquell-ID und TLS-Pass-Through-können mit anderen Diensten verwendet werden, z. B. dem *LoadBalancer*-Typ.

## <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Eine Netzwerksicherheitsgruppe filtert den Datenverkehr für virtuelle Computer wie die AKS-Knoten. Beim Erstellen von Diensten (z. B. LoadBalancer) konfiguriert die Azure-Plattform automatisch alle erforderlichen Netzwerksicherheitsgruppen-Regeln. 

Sie müssen Netzwerksicherheitsgruppen-Regeln nicht manuell konfigurieren, um den Datenverkehr für Pods in einem AKS-Cluster zu filtern. Definieren Sie einfach alle erforderlichen Ports und die Weiterleitung als Teil Ihrer Kubernetes-Dienstmanifeste. Überlassen Sie das Erstellen oder Aktualisieren der entsprechenden Regeln der Azure-Plattform. 

Sie können auch Netzwerkrichtlinien verwenden, um automatisch Filterregeln für den Datenverkehr auf Pods anzuwenden.

## <a name="network-policies"></a>Netzwerkrichtlinien

Standardmäßig können alle Pods in einem AKS-Cluster Datenverkehr ohne Einschränkungen senden und empfangen. Aus Sicherheitsgründen definieren Sie Regeln, die den Datenverkehrsfluss steuern, z. B.:
* Back-End-Anwendungen werden nur für erforderliche Front-End-Dienste verfügbar gemacht. 
* Datenbankkomponenten sind nur für die Anwendungsebenen zugänglich, die eine Verbindung damit herstellen.

Netzwerkrichtlinien sind ein in AKS verfügbares Kubernetes-Feature, mit dem Sie den Datenverkehrsfluss zwischen Pods steuern können. Anhand von Einstellungen wie zugewiesene Bezeichnungen, Namespace oder Port für den Datenverkehr können Sie Datenverkehr an den Pod zulassen oder verweigern. Während Netzwerksicherheitsgruppen für AKS-Knoten besser geeignet sind, stellen Netzwerkrichtlinien eine geeignetere cloudnative Methode zum Steuern des Datenverkehrsflusses für Pods dar. Wenn Pods in einem AKS-Cluster dynamisch erstellt werden, können erforderliche Netzwerkrichtlinien automatisch angewendet werden.

Weitere Informationen finden Sie unter [Sicherer Datenverkehr zwischen Pods durch Netzwerkrichtlinien in Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Nächste Schritte

Um mit AKS-Netzwerken zu beginnen, erstellen und konfigurieren Sie einen AKS-Cluster mit Ihren eigenen IP-Adressbereichen unter Verwendung von [kubenet][aks-configure-kubenet-networking] oder [Azure CNI][aks-configure-advanced-networking].

Entsprechende bewährte Methoden finden Sie unter [Best Practices für Netzwerkkonnektivität und Sicherheit in AKS][operator-best-practices-network].

Weitere Informationen zu den wesentlichen Konzepten von Kubernetes und AKS finden Sie in den folgenden Artikeln:

- [Kubernetes/AKS: Cluster und Workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS: Zugriff und Identität][aks-concepts-identity]
- [Kubernetes/AKS: Sicherheit][aks-concepts-security]
- [Kubernetes/AKS: Speicher][aks-concepts-storage]
- [Kubernetes/AKS: Skalierung][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
