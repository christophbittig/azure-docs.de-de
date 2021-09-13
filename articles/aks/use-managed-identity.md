---
title: Verwenden verwalteter Identitäten in Azure Kubernetes Service
description: Erfahren Sie, wie Sie verwaltete Identitäten in Azure Kubernetes Service (AKS) verwenden.
services: container-service
ms.topic: article
ms.date: 05/12/2021
ms.openlocfilehash: dbc02f8b65235a47fc523665ea6337774a6eb557
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343503"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Verwenden verwalteter Identitäten in Azure Kubernetes Service

Derzeit erfordert ein AKS-Cluster (Azure Kubernetes Service) und insbesondere der Kubernetes-Cloudanbieter zum Erstellen zusätzlicher Ressourcen in Azure Container Service, wie Lastenausgleichsmodule und verwaltete Datenträger, eine Identität. Dabei kann es sich um eine *verwaltete Identität* oder einen *Dienstprinzipal* handeln. Wenn Sie einen [Dienstprinzipal](kubernetes-service-principal.md) verwenden, müssen Sie diesen bereitstellen oder in Ihrem Namen durch AKS erstellen lassen. Wenn Sie eine verwaltete Identität verwenden, wird diese automatisch von AKS erstellt. Cluster mit Dienstprinzipalen erreichen irgendwann einen Zustand, in dem der Dienstprinzipal erneuert werden muss, damit der Cluster ordnungsgemäß funktioniert. Das Verwalten von Dienstprinzipalen erhöht die Komplexität, daher ist es einfacher, stattdessen verwaltete Identitäten zu verwenden. Dieselben Berechtigungsanforderungen gelten sowohl für Dienstprinzipale als auch für verwaltete Identitäten.

*Verwaltete Identitäten* stellen im Wesentlichen einen Wrapper um Dienstprinzipale dar und vereinfachen deren Verwaltung. Die Rotation der Anmeldeinformationen für verwaltete Identitäten erfolgt in Azure Active Directory standardmäßig automatisch alle 46 Tage. AKS unterstützt sowohl systemseitig zugewiesene als auch benutzerseitig zugewiesene Typen verwalteter Identitäten. Diese Identitäten sind derzeit unveränderlich. Weitere Informationen finden Sie im Artikel über [verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Voraussetzungen

Die folgenden Ressourcen müssen installiert sein:

- Azure-Befehlszeilenschnittstelle, Version 2.23.0 oder höher

## <a name="limitations"></a>Einschränkungen

* Das Verschieben/Migrieren von Clustern mit aktivierter verwalteter Identität wird für Mandanten nicht unterstützt.
* Wenn für den Cluster `aad-pod-identity` aktiviert wurde, werden die iptables der Knoten von NMI-Pods (Node Managed Identity) so geändert, dass Aufrufe des Azure Instance Metadata-Endpunkts abgefangen werden. Diese Konfiguration bedeutet, dass jede Anforderung, die an den Metadatenendpunkt gerichtet ist, von NMI abgefangen wird, auch wenn `aad-pod-identity` vom Pod nicht verwendet wird. Die AzurePodIdentityException-CRD kann so konfiguriert werden, dass `aad-pod-identity` darüber informiert wird, dass an den Metadatenendpunkt gerichtete Anforderungen, die von einem Pod stammen, der in der CRD definierte Bezeichnungen abgleicht, ohne Verarbeitung in NMI über einen Proxy zu senden sind. Die Systempods mit der Bezeichnung `kubernetes.azure.com/managedby: aks` im Namespace _kube-system_ müssen in `aad-pod-identity` durch Konfiguration der AzurePodIdentityException-CRD ausgeschlossen werden. Weitere Informationen finden Sie unter [Disable aad-pod-identity for a specific pod or application (Deaktivieren von „aad-pod-identity“ für einen bestimmten Pod oder eine bestimmte Anwendung)](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Installieren zur Konfiguration einer Ausnahme die [YAML-Datei „mic-exception“](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="summary-of-managed-identities"></a>Zusammenfassung der verwalteten Identitäten

AKS verwendet mehrere verwaltete Identitäten für integrierte Dienste und Add-Ons.

| Identity                       | Name    | Anwendungsfall | Standardberechtigungen | Verwendung eigener Identitäten
|----------------------------|-----------|----------|
| Steuerungsebene | Name des AKS-Clusters | Wird von Komponenten der AKS-Steuerungsebene verwendet, um Clusterressourcen wie Lastenausgleichsmodule für eingehenden Datenverkehr und von AKS verwaltete öffentliche IP-Adressen, die Autoskalierung im Cluster sowie CSI-Treiber für Datenträger und Dateien in Azure zu verwalten. | Rolle „Mitwirkender“ für Knotenressourcengruppe | Unterstützt
| Kubelet | Name des AKS-Clusters: agentpool | Authentifizierung bei Azure Container Registry (ACR) | N/V (für Kubernetes v1.15+) | Unterstützt
| Add-On | AzureNPM | Keine Identität erforderlich | Nicht verfügbar | Nein
| Add-On | Azure CNI-Netzwerküberwachung | Keine Identität erforderlich | Nicht verfügbar | Nein
| Add-On | azure-policy (Gatekeeper) | Keine Identität erforderlich | Nicht verfügbar | Nein
| Add-On | azure-policy | Keine Identität erforderlich | Nicht verfügbar | Nein
| Add-On | Calico | Keine Identität erforderlich | Nicht verfügbar | Nein
| Add-On | Dashboard | Keine Identität erforderlich | Nicht verfügbar | Nein
| Add-On | HTTPApplicationRouting | Verwaltung erforderlicher Netzwerkressourcen | Rolle „Leser“ für Knotenressourcengruppe, Rolle „Mitwirkender“ für DNS-Zone | Nein
| Add-On | Anwendungsgateway für eingehenden Datenverkehr | Verwaltung erforderlicher Netzwerkressourcen| Rolle „Mitwirkender“ für Knotenressourcengruppe | Nein
| Add-On | omsagent | Senden von AKS-Metriken an Azure Monitor | Rolle „Herausgeber von Überwachungsmetriken“ | Nein
| Add-On | Virtual-Node (ACI-Connector) | Verwaltung erforderlicher Netzwerkressourcen für Azure Container Instances (ACI) | Rolle „Mitwirkender“ für Knotenressourcengruppe | Nein
| Open-Source-Software-Projekt (OSS) | aad-pod-identity | Ermöglicht Anwendungen den sicheren Zugriff auf Cloudressourcen mit Azure Active Directory (AAD). | Nicht verfügbar | Schritte zum Gewähren von Berechtigungen unter https://github.com/Azure/aad-pod-identity#role-assignment.

## <a name="create-an-aks-cluster-with-managed-identities"></a>Erstellen eines AKS-Clusters mit verwalteten Identitäten

Sie können jetzt einen AKS-Cluster mit verwalteten Identitäten erstellen, indem Sie die folgenden CLI-Befehle verwenden.

Erstellen Sie als Erstes eine Azure-Ressourcengruppe:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Erstellen Sie anschließend einen AKS-Cluster:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Nach der Clustererstellung können Sie Ihre Anwendungsworkloads im neuen Cluster bereitstellen und damit genau wie mit dienstprinzipalbasierten AKS-Clustern interagieren.

Rufen Sie schließlich Anmeldeinformationen für den Zugriff auf den Cluster ab:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="update-an-aks-cluster-to-managed-identities"></a>Aktualisieren eines AKS-Clusters auf verwaltete Identitäten

Sie können einen AKS-Cluster, der derzeit mit Dienstprinzipalen arbeitet, jetzt mithilfe der folgenden CLI-Befehle auf die Verwendung von verwalteten Identitäten aktualisieren.

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```
> [!NOTE]
> Nach der Aktualisierung werden die Steuerungsebene und die Add-On-Pods des Clusters auf die Verwendung der verwalteten Identität umgestellt. Für Kubelet wird jedoch so lange der Dienstprinzipal verwendet, bis Sie den Agentpool upgraden. Führen Sie auf Ihren Knoten `az aks nodepool upgrade --node-image-only` aus, um das Update auf verwaltete Identitäten abzuschließen. 


## <a name="obtain-and-use-the-system-assigned-managed-identity-for-your-aks-cluster"></a>Abrufen und Verwenden der vom System zugewiesenen verwalteten Identität für Ihren AKS-Cluster

Vergewissern Sie sich mit dem folgenden CLI-Befehl, dass Ihr AKS-Cluster die verwaltete Identität verwendet:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "servicePrincipalProfile"
```

Wenn der Cluster verwaltete Identitäten verwendet, wird der `clientId`-Wert „msi“ angezeigt. Ein Cluster, der stattdessen einen Dienstprinzipal verwendet, zeigt stattdessen die Objekt-ID an. Beispiel: 

```output
{
  "clientId": "msi"
}
```

Nachdem Sie überprüft haben, ob der Cluster verwaltete Identitäten verwendet, können Sie die Objekt-ID der vom System zugewiesenen Identität der Steuerungsebene mit dem folgenden Befehl ermitteln:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "identity"
```

```output
{
    "principalId": "<object-id>",
    "tenantId": "<tenant-id>",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
```

> [!NOTE]
> Verwenden Sie zum Erstellen und Verwenden Ihres eigenen VNETs, Ihrer statischen IP-Adresse oder Ihres angeschlossenen Azure-Datenträgers, auf dem sich die Ressourcen außerhalb der Ressourcengruppe „Workerknoten“ befinden, die Prinzipal-ID der systemseitig zugewiesenen verwalteten Identität des Clusters, um eine Rolle zuzuweisen. Weitere Informationen zur Rollenzuweisung finden Sie unter [Delegieren des Zugriffs auf andere Azure-Ressourcen](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Es kann bis zu 60 Minuten dauern, bis die vom Azure-Cloudanbieter verwendeten Berechtigungszuweisungen für die Erstellung von Clustern für verwaltete Identitäten übernommen werden.


## <a name="bring-your-own-control-plane-mi"></a>Verwenden einer eigenen verwalteten Identität für die Steuerungsebene
Eine benutzerdefinierte Steuerungsebenenidentität ermöglicht den Zugriff auf die vorhandene Identität vor der Clustererstellung. Dieses Feature ermöglicht beispielsweise die Verwendung eines benutzerdefinierten VNET oder einer UDR vom Typ „outboundType“ mit einer vorab erstellten verwalteten Identität.

Hierfür muss die Azure CLI-Version 2.15.1 oder höher installiert sein.

### <a name="limitations"></a>Einschränkungen
* USDOD, Mitte, USDOD, Osten und USGov, Iowa, werden in Azure Government derzeit nicht unterstützt.

Sollten Sie über keine verwaltete Identität verfügen, können Sie beispielsweise mithilfe von [az identity create][az-identity-create] eine erstellen.

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Das Ergebnis sollte wie folgt aussehen:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Ist Ihre verwaltete Identität Teil Ihres Abonnements, können Sie sie mithilfe des CLI-Befehls [az identity list][az-identity-list] abfragen.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Nun können Sie den folgenden Befehl verwenden, um Ihren Cluster mit Ihrer vorhandenen Identität zu erstellen:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id>
```

Eine erfolgreiche Clustererstellung mit Ihren eigenen verwalteten Identitäten umfasst folgende userAssignedIdentities-Profilinformationen:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="bring-your-own-kubelet-mi"></a>Bringen Sie Ihre eigene Kubelet-MI mit

Mit einer Kubelet-Identität kann der Zugriff auf die vorhandene Identität vor der Cluster-Erstellung gewährt werden. Diese Funktion ermöglicht Szenarien wie die Verbindung zu ACR mit einer zuvor erstellten verwalteten Identität.

### <a name="prerequisites"></a>Voraussetzungen

- Hierfür muss die Azure CLI, Version 2.26.0 oder höher, installiert sein.

### <a name="limitations"></a>Einschränkungen

- Es funktioniert ausschließlich mit einem benutzerzugeordneten verwalteten Cluster.
- China, Osten und China, Norden werden in Azure China 21Vianet derzeit nicht unterstützt.

### <a name="create-or-obtain-managed-identities"></a>Verwaltete Identitäten erstellen oder abrufen

Wenn Sie noch keine auf der Steuerungsebene verwaltete Identität haben, sollten Sie eine erstellen. Im folgenden Beispiel wird der Befehl [az identity create][az-identity-create] verwendet:

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```

Das Ergebnis sollte wie folgt aussehen:

```output
{                                  
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Wenn Sie noch keine auf der Steuerungsebene verwaltete Identität haben, sollten Sie eine erstellen. Im folgenden Beispiel wird der Befehl [az identity create][az-identity-create] verwendet:

```azurecli-interactive
az identity create --name myKubeletIdentity --resource-group myResourceGroup
```

Das Ergebnis sollte wie folgt aussehen:

```output
{
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity", 
  "location": "westus2",
  "name": "myKubeletIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Wenn Ihre vorhandene verwaltete Identität Teil Ihres Abonnements ist, können Sie den Befehl [az identity list][az-identity-list] verwenden, um sie abzufragen:

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

### <a name="create-a-cluster-using-kubelet-identity"></a>Erstellen eines Clusters anhand der Kubelet-Identität

Sie können jetzt den folgenden Befehl verwenden, um Ihren Cluster mit Ihren vorhandenen Identitäten zu erstellen. Geben Sie die Identitäts-ID der Steuerungsebene über `assign-identity` und die vom Kubelet verwaltete Identität über `assign-kublet-identity` an:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
    --assign-kubelet-identity <kubelet-identity-id>
```

Eine erfolgreiche Clustererstellung unter Verwendung Ihrer eigenen verwalteten Kubelet-Identität enthält die folgende Ausgabe:

```output
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
        "clientId": "<client-id>",
        "principalId": "<principal-id>"
      }
    }
  },
  "identityProfile": {
    "kubeletidentity": {
      "clientId": "<client-id>",
      "objectId": "<object-id>",
      "resourceId": "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity"
    }
  },
```

## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie [Azure Resource Manager-Vorlagen][aks-arm-template] zum Erstellen von verwalteter Identitäts-aktivierten Clustern.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-list]: /cli/azure/identity#az_identity_list
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
