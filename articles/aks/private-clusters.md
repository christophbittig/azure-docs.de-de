---
title: Erstellen eines privaten Azure Kubernetes Service-Clusters
description: Erfahren Sie, wie Sie einen privaten Azure Kubernetes Service-Cluster (AKS) erstellen.
services: container-service
ms.topic: article
ms.date: 8/30/2021
ms.openlocfilehash: b0e89a59e9051de255be21103121569e45a2621a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440543"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Erstellen eines privaten Azure Kubernetes Service-Clusters

In einem privaten Cluster besitzt die Steuerungsebene oder der API-Server interne IP-Adressen, die im Dokument [RFC1918 – -Allocation for Private Internet](https://tools.ietf.org/html/rfc1918) definiert sind. Durch die Verwendung eines privaten Clusters können Sie sicherstellen, dass der Netzwerkdatenverkehr zwischen Ihrem API-Server und den Knotenpools ausschließlich im privaten Netzwerk verbleibt.

Die Steuerungsebene oder der API-Server befindet sich in einem von Azure Kubernetes Service (AKS) verwalteten Azure-Abonnement. Der Cluster oder Knotenpool eines Kunden befindet sich im Abonnement des Kunden. Der Server und der Cluster oder Knotenpool können über den [Azure Private Link-Dienst][private-link-service] im virtuellen Netzwerk des API-Servers und einen privaten Endpunkt, der im Subnetz des AKS-Clusters des Kunden verfügbar gemacht wird, miteinander kommunizieren.

## <a name="region-availability"></a>Regionale Verfügbarkeit

Ein privater Cluster ist in öffentlichen Regionen, Azure Government-Regionen und Azure China 21ViaNet-Regionen mit [AKS-Unterstützung](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service) verfügbar.

> [!NOTE]
> Azure Government-Standorte werden unterstützt, allerdings wird US Gov Texas derzeit aufgrund von fehlender Unterstützung von Private Link nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* Azure CLI, Version 2.2.0 oder höher
* Der Private Link-Dienst wird nur von Load Balancer Standard unterstützt. Der Load Balancer Basic wird nicht unterstützt.  
* Um einen benutzerdefinierten DNS-Server zu verwenden, fügen Sie die Azure DNS-IP-Adresse 168.63.129.16 als Upstream-DNS-Server im benutzerdefinierten DNS-Server hinzu.

## <a name="create-a-private-aks-cluster"></a>Erstellen eines privaten AKS-Clusters

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie eine Ressourcengruppe, oder verwenden Sie eine vorhandene Ressourcengruppe für Ihren AKS-Cluster.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Standardnetzwerke (Basic) 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Dabei gilt: `--enable-private-cluster` ist ein obligatorisches Flag für einen privaten Cluster. 

### <a name="advanced-networking"></a>Erweiterte Netzwerke  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Dabei gilt: `--enable-private-cluster` ist ein obligatorisches Flag für einen privaten Cluster. 

> [!NOTE]
> Wenn CIDR der Docker-Bridge-Adresse (172.17.0.1/16) mit der Subnetz-CIDR in Konflikt steht, ändern Sie die Docker-Bridge-Adresse entsprechend.

## <a name="configure-private-dns-zone"></a>Konfigurieren einer privaten DNS-Zone 

Zum Konfigurieren einer privaten DNS-Zone können die folgenden Parameter verwendet werden.

- „system“ (Standardwert). Wenn das Argument „--private-dns-zone“ weggelassen wird, erstellt AKS eine private DNS-Zone in der Knotenressourcengruppe.
- „none“; Standardwert wird „Öffentliches DNS“, was bedeutet, dass AKS keine private DNS-Zone erstellt.  
- „CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID“ erfordert, dass Sie eine private DNS-Zone in diesem Format für die globale Azure-Cloud erstellen: `privatelink.<region>.azmk8s.io` oder `<subzone>.privatelink.<region>.azmk8s.io`. In Zukunft benötigen Sie die Ressourcen-ID dieser Private DNS-Zone.  Außerdem benötigen Sie eine benutzerseitig zugewiesene Identität oder einen benutzerseitig zugewiesenen Dienstprinzipal, der/dem mindestens die Rollen `private dns zone contributor` und `vnet contributor` zugewiesen wurden.
  - Wenn sich „Private DNS Zone“ in einem anderen Abonnement als der AKS-Cluster befindet, müssen Sie Microsoft.ContainerServices in beiden Abonnements registrieren.
  - „fqdn-subdomain“ kann bei „CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID“ nur verwendet werden, um Unterdomänenfunktionen für `privatelink.<region>.azmk8s.io` bereitzustellen.

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Erstellen eines privaten AKS-Clusters mit privater DNS-Zone

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```
### <a name="create-a-private-aks-cluster-with-a-byo-private-dns-subzone-preview"></a>Erstellen eines privaten AKS-Clusters mit einer benutzerdefinierten privaten DNS-Subzone (Vorschau)

Voraussetzungen:

* Azure CLI >= 2.29.0 oder Azure CLI mit Erweiterung „aks-preview“ Version 0.5.34 oder höher.

### <a name="register-the-enableprivateclustersubzone-preview-feature"></a>Registrieren der Previewfunktion `EnablePrivateClusterSubZone`

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Sie müssen das Featureflag `EnablePrivateClusterSubZone` für Ihr Abonnement aktivieren, um einen privaten AKS-Cluster mit SubZone zu erstellen.

Registrieren Sie das Featureflag `EnablePrivateClusterSubZone` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnablePrivateClusterSubZone"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnablePrivateClusterSubZone')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung aks-preview

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="create-a-private-aks-cluster-with-custom-private-dns-zone"></a>Erstellen eines privaten AKS-Clusters mit einer benutzerdefinierten privaten DNS-Zone

```azurecli-interactive
# Custom Private DNS Zone name should be in format "privatelink.<region>.azmk8s.io"
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId>
```

### <a name="create-a-private-aks-cluster-with-custom-private-dns-subzone"></a>Erstellen eines privaten AKS-Clusters mit einer benutzerdefinierten privaten DNS-Subzone

```azurecli-interactive
# Custom Private DNS Zone name should be in format "<subzone>.privatelink.<region>.azmk8s.io"
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId>
```

### <a name="create-a-private-aks-cluster-with-custom-private-dns-zone-and-custom-subdomain"></a>Erstellen eines privaten AKS-Clusters mit einer benutzerdefinierten privaten DNS-Zone und einer benutzerdefinierten Unterdomäne

```azurecli-interactive
# Custom Private DNS Zone name could be in formats "privatelink.<region>.azmk8s.io" or "<subzone>.privatelink.<region>.azmk8s.io"
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain>
```

### <a name="create-a-private-aks-cluster-with-a-public-fqdn"></a>Erstellen eines privaten AKS-Clusters mit einem öffentlichen FQDN

Voraussetzungen:

* Azure CLI >= 2.28.0 oder Azure CLI mit Erweiterung „aks-preview“ Version 0.5.29 oder höher.
* Wenn Sie ARM oder die REST-API verwenden, muss die AKS-API-Version 2021-05-01 oder höher sein.

Die Option „Öffentliches DNS“ kann genutzt werden, um Routingoptionen für Ihren privaten Cluster zu vereinfachen.  

![Öffentliche DNS](https://user-images.githubusercontent.com/50749048/124776520-82629600-df0d-11eb-8f6b-71c473b6bd01.png)

1. Wenn Sie einen privaten AKS-Cluster bereitstellen, erstellt AKS standardmäßig einen zusätzlichen öffentlichen FQDN und den entsprechenden A-Eintrag im öffentlichen Azure-DNS. Die Agent-Knoten verwendet weiterhin den A-Eintrag in der privaten DNS-Zone, um die private IP-Adresse des privaten Endpunkts für die Kommunikation mit dem API-Server aufzulösen.

2. Wenn Sie `--private-dns-zone none` verwenden, hat der Cluster nur einen öffentlichen FQDN. Wenn Sie diese Option verwenden, wird keine private DNS-Zone erstellt oder für die Namensauflösung des FQDN des API-Servers verwendet. Die IP-Adresse der API ist weiterhin privat und nicht öffentlich routingfähig.

3. Wenn der öffentliche FQDN nicht gewünscht ist, können Sie `--disable-public-fqdn` verwenden, um ihn zu deaktivieren. (Keine („none“) private DNS-Zone darf öffentlichen FQDN nicht deaktivieren.)

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <private-dns-zone-mode> --disable-public-fqdn
az aks update -n <private-cluster-name> -g <private-cluster-resource-group> --disable-public-fqdn
```

## <a name="options-for-connecting-to-the-private-cluster"></a>Optionen zum Herstellen einer Verbindung mit dem privaten Cluster

Der Endpunkt des API-Servers weist keine öffentliche IP-Adresse auf. Zum Verwalten des API-Servers müssen Sie einen virtuellen Computer verwenden, der Zugriff auf das Azure Virtual Network (VNET) des AKS-Clusters hat. Es gibt mehrere Optionen zum Einrichten der Netzwerkkonnektivität mit dem privaten Cluster.

* Erstellen Sie einen virtuellen Computer in demselben Azure Virtual Network (VNET) wie dem des AKS-Clusters.
* Verwenden Sie einen virtuellen Computer in einem separaten Netzwerk, und richten Sie ein [Peering virtueller Netzwerke][virtual-network-peering] ein.  Weitere Informationen zu dieser Option finden Sie im nachstehenden Abschnitt.
* Verwenden Sie eine [ExpressRoute- oder VPN][express-route-or-VPN]-Verbindung.
* Verwenden Sie das [AKS-Run Command-Funktion](#aks-run-command).

Das Erstellen eines virtuellen Computers in demselben VNET wie dem des AKS-Clusters ist die einfachste Option.  ExpressRoute und VPNs (virtuelle private Netzwerke) erhöhen die Kosten und erfordern zusätzliche Netzwerkkomplexität.  Beim Peering virtueller Netzwerke müssen Sie Ihre Netzwerk-CIDR-Bereiche planen, um sicherzustellen, dass es keine überlappenden Bereiche gibt.

### <a name="aks-run-command"></a>AKS-Skriptausführung

Wenn Sie heute auf einen privaten Cluster zugreifen müssen, müssen Sie dies innerhalb des virtuellen Clusternetzwerks oder eines gepeerten Netzwerks oder Client-Rechners vornehmen. Dies erfordert in der Regel, dass der Computer über VPN oder Express Route mit dem virtuellen Cluster Netzwerk oder einer Jumpbox verbunden ist, die im virtuellen Cluster Netzwerk erstellt werden soll. Mit dem AKS-Fahrbefehl können Sie Befehle in einem AKS-Cluster über die AKS-API ferngesteuert aufrufen. Diese Funktion bietet eine API, mit der Sie z. B. Just-in-Time-Befehle von einem entfernten Laptop für einen privaten Cluster ausführen können. Dies kann beim schnellen Just-in-Time-Zugriff auf einen privaten Cluster sehr hilfreich sein, wenn sich der Client-Rechner nicht im privaten Netzwerk des Clusters befindet, während die gleichen RBAC-Kontrollen und der private API-Server beibehalten und durchgesetzt werden.

### <a name="prerequisites"></a>Voraussetzungen

* Azure CLI-Version 2.24.0 oder höher

### <a name="use-aks-run-command"></a>Verwenden Sie den AKS Ausführungsbefehl

Einfacher Befehl

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

Stellen Sie ein Manifest bereit anhand des Anhängens der spezifischen Datei

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

Bereitstellen eines Manifests anhand des Anhängens eines gesamten Ordners

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Führen Sie eine Helm-Installation durch und übergeben Sie das spezifische Werte-Manifest

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```
> [!NOTE]
> Sichern Sie den Zugriff auf die AKS-Skriptausführung, indem Sie eine benutzerdefinierte Rolle mit den Berechtigungen „Microsoft.ContainerService/managedClusters/runcommand/action“ und „Microsoft.ContainerService/managedclusters/commandResults/read“ erstellen und bestimmten Benutzern und/oder Gruppen in Kombination mit Just-In-Time-Zugriff oder Richtlinien für bedingten Zugriff zuweisen. 

## <a name="virtual-network-peering"></a>Peering in virtuellen Netzwerken

Wie bereits erwähnt, ist das Peering virtueller Netzwerke eine Möglichkeit für den Zugriff auf Ihren privaten Cluster. Wenn Sie das Peering virtueller Netzwerke verwenden möchten, müssen Sie eine Verknüpfung zwischen dem virtuellen Netzwerk und der privaten DNS-Zone einrichten.
    
1. Wechseln Sie im Azure-Portal zur Knotenressourcengruppe.  
2. Wählen Sie die private DNS-Zone aus.   
3. Wählen Sie im linken Bereich den Link **Virtuelles Netzwerk** aus.  
4. Erstellen Sie eine neue Verknüpfung, um das virtuelle Netzwerk des virtuellen Computers der privaten DNS-Zone hinzuzufügen. Es dauert ein paar Minuten, bis der DNS-Zonenlink verfügbar wird.  
5. Navigieren Sie im Azure-Portal zu der Ressourcengruppe mit dem virtuellen Netzwerk Ihres Clusters.  
6. Wählen Sie im rechten Bereich das virtuelle Netzwerk aus. Der Name des virtuellen Netzwerks hat die Form *aks-vnet-\** .  
7. Wählen Sie im linken Bereich **Peerings** aus.  
8. Wählen Sie **Hinzufügen** aus, fügen Sie das virtuelle Netzwerk des virtuellen Computers hinzu, und erstellen Sie dann das Peering.  
9. Wechseln Sie zu dem virtuellen Netzwerk, in dem sich der virtuelle Computer befindet, wählen Sie **Peerings** und dann das virtuelle AKS-Netzwerk aus, und erstellen Sie dann das Peering. Wenn die Adressbereiche im virtuellen AKS-Netzwerk mit denen im virtuellen Netzwerk des virtuellen Computers in Konflikt stehen, schlägt das Peering fehl. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Hub-and-Spoke mit benutzerdefiniertem DNS

[Hub-and-Spoke-Architekturen](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) werden in Azure häufig für die Bereitstellung von Netzwerken verwendet. In vielen dieser Bereitstellungen sind die DNS-Einstellungen in den Spoke-VNets so konfiguriert, dass sie auf eine zentrale DNS-Weiterleitung verweisen, um eine lokale und Azure-basierte DNS-Auflösung zu ermöglichen. Beim Bereitstellen eines AKS-Clusters in einer solchen Netzwerkumgebung sind einige besondere Überlegungen zu berücksichtigen.

![Hub-and-Spoke für privaten Cluster](media/private-clusters/aks-private-hub-spoke.png)

1. Wenn ein privater Cluster bereitgestellt wird, werden standardmäßig ein privater Endpunkt (1) und eine private DNS-Zone (2) in der vom Cluster verwalteten Ressourcengruppe erstellt. Der Cluster verwendet einen A-Eintrag in der privaten Zone, um die IP-Adresse des privaten Endpunkts für die Kommunikation mit dem API-Server aufzulösen.

2. Die private DNS-Zone ist nur mit dem virtuellen Netzwerk verbunden, an das die Clusterknoten angefügt sind (3). Dies bedeutet, dass der private Endpunkt nur von Hosts in diesem verknüpften virtuellen Netzwerk aufgelöst werden kann. In Szenarien, in denen im VNET kein benutzerdefiniertes DNS konfiguriert ist (Standardkonfiguration), funktioniert dies problemlos, da die Hosts auf 168.63.129.16 für das DNS verweisen, das Einträge in der privaten DNS-Zone aufgrund der Verknüpfung auflösen kann.

3. In Szenarien, in denen das virtuelle Netzwerk, das Ihren Cluster enthält, über benutzerdefinierte DNS-Einstellungen verfügt (4), schlägt die Clusterbereitstellung fehl, es sei denn, die private DNS-Zone ist mit dem virtuellen Netzwerk verknüpft, das die benutzerdefinierten DNS-Auflösungen enthält (5). Diese Verknüpfung kann manuell erstellt werden, nachdem die private Zone während der Clusterbereitstellung erstellt wurde, oder über die Automatisierung bei Erkennung der Erstellung der Zone mithilfe von ereignisbasierten Bereitstellungsmechanismen (z. B. Azure Event Grid und Azure Functions).

> [!NOTE]
> Wenn Sie [Bring Your Own Route Table with kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) (Verwenden einer eigenen Routingtabelle mit kubenet) und Bring Your Own DNS with Private Cluster (Verwenden eines eigenen DNS-Servers mit Private Cluster) verwenden, tritt bei der Clustererstellung ein Fehler auf. Nach dem Fehler bei der Clustererstellung müssen Sie dem Subnetz die [Routingtabelle](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) in der Knotenressourcengruppe zuordnen, um die Erstellung zu einem Erfolg zu machen.

## <a name="limitations"></a>Einschränkungen 
* IP-autorisierte Bereiche können nicht auf den privaten API-Serverendpunkt angewandt werden, sie gelten nur für den öffentlichen API-Server.
* [Die Einschränkungen des Azure Private Link-Diensts][private-link-service] gelten für private Cluster.
* Keine Unterstützung für Azure DevOps Microsoft-gehostete Agents mit privaten Clustern. Erwägen Sie die Verwendung von [selbstgehosteten Agents](/azure/devops/pipelines/agents/agents?tabs=browser). 
* Für Kunden, die Azure Container Registry für die Arbeit mit privatem AKS aktivieren müssen, muss ein Peering des virtuellen Netzwerks der Container Registry mit dem virtuellen Netzwerk des Agent-Clusters ausgeführt werden.
* Keine Unterstützung für das Konvertieren vorhandener AKS-Cluster in private Cluster
* Das Löschen oder Ändern des privaten Endpunkts im Kundensubnetz führt dazu, dass der Cluster nicht mehr funktioniert. 

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
