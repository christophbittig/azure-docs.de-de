---
title: Verwenden von verwalteten Azure Active Directory-Podidentitäten in Azure Kubernetes Service (Vorschauversion)
description: Es wird beschrieben, wie Sie verwaltete AAD-Podidentitäten in Azure Kubernetes Service (AKS) verwenden.
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: 1ecf9b45983dbc34938593424644a646dc3d96cb
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101228"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Verwenden von verwalteten Azure Active Directory-Podidentitäten in Azure Kubernetes Service (Vorschauversion)

Für verwaltete Azure Active Directory-Podidentitäten werden Kubernetes-Primitive verwendet, um [verwaltete Identitäten für Azure-Ressourcen][az-managed-identities] und Identitäten in Azure Active Directory (AAD) Pods zuzuordnen. Administratoren erstellen Identitäten und Bindungen als Kubernetes-Primitive, die Pods den Zugriff auf Azure-Ressourcen ermöglichen, für die AAD als Identitätsanbieter genutzt wird.

> [!NOTE]
>Das in diesem Dokument beschriebene Feature der über Pods verwalteten Identitäten (Vorschau) wird durch Version 2 der über Pods verwalteten Identitäten (Vorschau) ersetzt.
> Wenn Sie über eine vorhandene AADPODIDENTITY-Installation verfügen, ist eine Option für die Migration zu Version 2 verfügbar. Ausführlichere Migrationsinformationen werden bereitgestellt, wenn die für das zweite Quartal 2022 angepeilte Public Preview-Phase näher rückt. Das Aktivieren dieses Features bedeutet, dass die MIC-Komponente nicht benötigt wird.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voraussetzungen

Die folgenden Ressourcen müssen installiert sein:

* Azure CLI, Version 2.20.0 oder höher
* Erweiterung `aks-preview`, Version 0.5.5 oder höher

### <a name="limitations"></a>Einschränkungen

* Für einen Cluster sind maximal 200 Podidentitäten zulässig.
* Für einen Cluster sind maximal 200 Ausnahmen für Podidentitäten zulässig.
* Verwaltete Podidentitäten sind nur in Linux-Knotenpools verfügbar.

### <a name="register-the-enablepodidentitypreview"></a>Registrieren von `EnablePodIdentityPreview`

Registrieren Sie das Feature `EnablePodIdentityPreview`:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Installieren der Azure-Befehlszeilenschnittstelle `aks-preview`

Sie benötigen außerdem mindestens Version 0.5.5 der Erweiterung *aks-preview* für die Azure-Befehlszeilenschnittstelle. Installieren Sie die Erweiterung *aks-preview* der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-azure-container-networking-interface-cni"></a>Erstellen eines AKS-Clusters mit Azure Container Networking Interface (CNI)

> [!NOTE]
> Dies ist die standardmäßig empfohlene Konfiguration.

Erstellen Sie einen AKS-Cluster mit Azure CNI, für den eine verwaltete Podidentität aktiviert ist. Im Folgenden wird der Befehl [az group create][az-group-create] verwendet, um eine Ressourcengruppe mit dem Namen *myResourceGroup* zu erstellen, und der Befehl [az aks create][az-aks-create], um in der Ressourcengruppe *myResourceGroup* einen AKS-Cluster mit dem Namen *myAKSCluster* zu erstellen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```
> [!NOTE]
> Azure Active Directory-Podidentität unterstützt zwei Betriebsmodi:
> 
> 1. Standardmodus: In diesem Modus werden die folgenden zwei Komponenten im AKS-Cluster bereitgestellt: 
>     * [Managed Identity Controller (MIC)](https://azure.github.io/aad-pod-identity/docs/concepts/mic/): Ein Kubernetes-Controller, der über den Kubernetes-API-Server auf Änderungen an Pods, [AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) und [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/) überwacht. Wenn eine relevante Änderung erkannt wird, fügt der MIC nach Bedarf [AzureAssignedIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureassignedidentity/) hinzu oder löscht sie. Insbesondere bei der Planung eines Pods weist der MIC die verwaltete Identität in Azure der zugrunde liegenden VMSS zu, die während der Erstellungsphase vom Knotenpool verwendet wird. Wenn alle Pods, die die Identität verwenden, gelöscht werden, wird die Identität aus der VMSS des Knotenpools entfernt, es sei denn, dieselbe verwaltete Identität wird von anderen Pods verwendet. Der MIC führt ähnliche Aktionen aus, wenn AzureIdentity oder AzureIdentityBinding erstellt oder gelöscht werden.
>     * [Node Management Identity (NMI)](https://azure.github.io/aad-pod-identity/docs/concepts/nmi/): Dies ist ein Pod, der auf jedem Knoten im AKS-Cluster als DaemonSet ausgeführt wird. NMI fängt Sicherheitstokenanforderungen an den [Azure Instance Metadata Service](../virtual-machines/linux/instance-metadata-service.md?tabs=linux) auf jedem Knoten ab, leitet sie an sich selbst weiter und überprüft, ob der Pod Zugriff auf die Identität hat, für die er ein Token anfordert, und ruft das Token im Auftrag der Anwendung vom Azure Active Directory-Mandanten ab.
> 2. Verwalteter Modus: In diesem Modus gibt es nur die NMI. Die Identität muss vom Benutzer manuell zugewiesen und verwaltet werden. Weitere Informationen finden Sie unter [Podidentität im verwalteten Modus](https://azure.github.io/aad-pod-identity/docs/configure/pod_identity_in_managed_mode/).
>
>Wenn Sie die Azure Active Directory-Podidentität mittels Helm-Chart oder YAML-Manifest installieren (wie in den [Installationsanweisungen](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) gezeigt), können Sie zwischen den Modi `standard` und `managed` wählen. Wenn Sie die Azure Active Directory-Podidentität dagegen mithilfe des [AKS-Cluster-Add-Ons](/azure/aks/use-azure-ad-pod-identity) installieren (wie in diesem Artikel gezeigt), wird der Modus `managed` verwendet.

Verwenden Sie [az aks get-credentials][az-aks-get-credentials], um sich bei Ihrem AKS-Cluster anzumelden. Mit diesem Befehl wird auch das `kubectl`-Clientzertifikat auf Ihren Entwicklungscomputer heruntergeladen und konfiguriert.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Aktualisieren eines vorhandenen AKS-Clusters mit Azure CNI

Aktualisieren Sie einen vorhandenen AKS-Cluster mit Azure CNI, um eine verwaltete Podidentität zu integrieren.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Verwenden des kubenet-Netzwerk-Plug-Ins mit verwalteten Azure Active Directory-Podidentitäten 

> [!IMPORTANT]
> Das Ausführen von AAD-Poditentitäten in einem Cluster mit kubenet ist aufgrund der Sicherheitsimplikationen keine empfohlene Konfiguration. Befolgen Sie die Schritte zur Risikominderung und konfigurieren Sie Richtlinien, bevor Sie AAD-Poditentitäten in einem Cluster mit kubenet aktivieren.

## <a name="mitigation"></a>Minderung

Um das Sicherheitsrisiko auf Clusterebene zu minimieren, können Sie CAP_NET_RAW-Angriffe mithilfe der integrierten Azure-Richtlinie „Container in einem Kubernetes-Cluster dürfen nur zulässige Funktionen verwenden“ begrenzen.  

Fügen Sie NET_RAW zu „Zu löschende Funktionen“ hinzu:

![image](https://user-images.githubusercontent.com/50749048/118558790-206b8880-b735-11eb-9e48-236b81116812.png)

Wenn Sie Azure Policy nicht nutzen, können Sie den OpenPolicyAgent-Zugriffscontroller zusammen mit dem Gatekeeper-Webhook „Validating“ verwenden. Wenn Sie Gatekeeper bereits in Ihrem Cluster installiert haben, fügen Sie das ConstraintTemplate vom Typ „K8sPSPCapabilities“ hinzu:

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
Fügen Sie eine Vorlage hinzu, um das Erzeugen von Pods mit der NET_RAW-Funktion einzuschränken:

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Erstellen eines AKS-Clusters mit kubenet-Netzwerk-Plug-In

Erstellen Sie einen AKS-Cluster mit dem kubenet-Netzwerk-Plug-In, für den eine verwaltete Podidentität aktiviert ist.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Aktualisieren eines vorhandenen AKS-Clusters mit kubenet-Netzwerk-Plug-In

Aktualisieren Sie einen vorhandenen AKS-Cluster mit dem kubenet-Netzwerk-Plug-In, um eine per Pod verwaltete Identität zu integrieren.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>Erstellen einer Identität

Erstellen Sie eine Identität, indem Sie den Befehl [az identity create][az-identity-create] verwenden, und legen Sie die Variablen *IDENTITY_CLIENT_ID* und *IDENTITY_RESOURCE_ID* fest.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Zuweisen von Berechtigungen für die verwaltete Identität

Zum Ausführen des Demos muss die verwaltete Identität *IDENTITY_CLIENT_ID* in der Ressourcengruppe, die die VM-Skalierungsgruppe Ihres AKS-Clusters enthält, über Berechtigungen vom Typ „Mitwirkender für virtuelle Computer“ verfügen.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Virtual Machine Contributor" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Erstellen einer Podidentität

Erstellen Sie mit `az aks pod-identity add` eine Podidentität für den Cluster.

> [!IMPORTANT]
> Sie müssen über die relevanten Berechtigungen (beispielsweise „Besitzer“) für Ihr Abonnement verfügen, um die Identität erstellen und die Rollenbindung der Clusteridentität zuweisen zu können.
> 
> Die Clusteridentität muss für die zuzuweisende Identität über Berechtigungen vom Typ „Operator für verwaltete Identität“ verfügen.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Wenn Sie verwaltete Podidentitäten in Ihrem AKS-Cluster aktivieren, wird dem Namespace *kube-system* eine AzurePodIdentityException mit dem Namen *aks-addon-exception* hinzugefügt. AzurePodIdentityException ermöglicht Pods mit bestimmten Bezeichnungen den Zugriff auf den Azure Instance Metadata Service-Endpunkt (IMDS), ohne dass dieser Vorgang vom Node Managed Identity-Server (NMI) unterbrochen wird. Mit *aks-addon-exception* können AKS-Erstanbieter-Add-Ons, z. B. eine verwaltete AAD-Podidentität, betrieben werden, ohne dass eine manuelle Konfiguration von AzurePodIdentityException erfolgen muss. Optional können Sie ein AzurePodIdentityException-Element hinzufügen, entfernen und aktualisieren, indem Sie `az aks pod-identity exception add`, `az aks pod-identity exception delete`, `az aks pod-identity exception update` oder `kubectl` verwenden.
> POD_IDENTITY_NAME muss ein gültiger [DNS-Unterdomänenname] sein, wie in [RFC 1123] definiert. 

> [!NOTE]
> Wenn Sie die Podidentität mithilfe von `pod-identity add` zuweisen, wird von der Azure CLI versucht, der Clusteridentität über die Podidentität (*IDENTITY_RESOURCE_ID*) die Rolle „Operator für verwaltete Identität“ zuzuweisen.

## <a name="run-a-sample-application"></a>Ausführen einer Beispielanwendung

Damit ein Pod eine verwaltete AAD-Podidentität verwenden kann, benötigt der Pod die Bezeichnung *aadpodidbinding* mit einem Wert, der mit einem Selektor einer *AzureIdentityBinding* übereinstimmt. Erstellen Sie eine `demo.yaml`-Datei mit dem folgenden Inhalt, um eine Beispielanwendung mit Verwendung einer verwalteten AAD-Podidentität auszuführen. Ersetzen Sie *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* und *IDENTITY_RESOURCE_GROUP* durch die Werte aus den vorherigen Schritten. Ersetzen Sie *SUBSCRIPTION_ID* durch Ihre Abonnement-ID.

> [!NOTE]
> In den vorherigen Schritten haben Sie die Variablen *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* und *IDENTITY_RESOURCE_GROUP* erstellt. Sie können einen Befehl wie `echo` verwenden, um den für Variablen festgelegten Wert anzuzeigen, z. B. `echo $IDENTITY_NAME`.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: $POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=$SUBSCRIPTION_ID
      - --clientid=$IDENTITY_CLIENT_ID
      - --resourcegroup=$IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Beachten Sie Folgendes: Die Poddefinition verfügt über die Bezeichnung *aadpodidbinding* mit einem Wert, der dem Namen der Podidentität entspricht, für die Sie im vorherigen Schritt `az aks pod-identity add` ausgeführt haben.

Stellen Sie `demo.yaml` in demselben Namespace wie Ihre Podidentität bereit, indem Sie `kubectl apply` verwenden:

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Vergewissern Sie sich mit `kubectl logs`, dass die Ausführung der Beispielanwendung erfolgreich war.

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Vergewissern Sie sich in den Protokollen, dass die Beschaffung des Tokens und der *GET*-Vorgang erfolgreich waren.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```
## <a name="run-an-application-with-multiple-identities"></a>Ausführen einer Anwendung mit mehreren Identitäten

## <a name="create-multiple-identities"></a>Erstellen von mehreren Identitäten

Erstellen Sie Identitäten, indem Sie den Befehl [az identity create][az-identity-create] verwenden und die Variablen *IDENTITY_CLIENT_ID* und *IDENTITY_RESOURCE_ID* festlegen.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME_1="application-identity_1"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME_1}
export IDENTITY_NAME_2="application-identity_2"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME_2}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_1} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_1} --query id -otsv)"
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_2} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME_2} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identities"></a>Zuweisen von Berechtigungen für die verwalteten Identitäten

Die verwaltete Identität *IDENTITY_CLIENT_ID* muss über Leseberechtigungen in der Ressourcengruppe verfügen, die die VM-Skalierungsgruppe Ihres AKS-Clusters enthält.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID_1" --scope $NODES_RESOURCE_ID
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID_2" --scope $NODES_RESOURCE_ID
```

## <a name="create-pod-identities"></a>Erstellen von Podidentitäten

Erstellen Sie mithilfe von `az aks pod-identity add` Podidentitäten für den Cluster.

> [!IMPORTANT]
> Sie müssen über die entsprechenden Berechtigungen, z. B. `Owner`, für Ihr Abonnement verfügen, um die Identität und die Rollenbindung erstellen zu können.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID_1} --binding-selector foo
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID_2} --binding-selector foo
```

> [!NOTE]
> Wenn Sie verwaltete Podidentitäten in Ihrem AKS-Cluster aktivieren, wird dem Namespace *kube-system* eine AzurePodIdentityException mit dem Namen *aks-addon-exception* hinzugefügt. AzurePodIdentityException ermöglicht Pods mit bestimmten Bezeichnungen den Zugriff auf den Azure Instance Metadata Service-Endpunkt (IMDS), ohne dass dieser Vorgang vom Node Managed Identity-Server (NMI) unterbrochen wird. Mit *aks-addon-exception* können AKS-Erstanbieter-Add-Ons, z. B. eine verwaltete AAD-Podidentität, betrieben werden, ohne dass eine manuelle Konfiguration von AzurePodIdentityException erfolgen muss. Optional können Sie ein AzurePodIdentityException-Element hinzufügen, entfernen und aktualisieren, indem Sie `az aks pod-identity exception add`, `az aks pod-identity exception delete`, `az aks pod-identity exception update` oder `kubectl` verwenden.

## <a name="run-a-sample-application-with-multiple-identities"></a>Ausführen einer Beispielanwendung mit mehreren Identitäten

Damit ein Pod eine verwaltete AAD-Podidentität verwenden kann, benötigt der Pod die Bezeichnung *aadpodidbinding* mit einem Wert, der mit einem Selektor einer *AzureIdentityBinding* übereinstimmt. Erstellen Sie eine `demo.yaml`-Datei mit dem folgenden Inhalt, um eine Beispielanwendung mit Verwendung einer verwalteten AAD-Podidentität auszuführen. Ersetzen Sie *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* und *IDENTITY_RESOURCE_GROUP* durch die Werte aus den vorherigen Schritten. Ersetzen Sie *SUBSCRIPTION_ID* durch Ihre Abonnement-ID.

> [!NOTE]
> In den vorherigen Schritten haben Sie die Variablen *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* und *IDENTITY_RESOURCE_GROUP* erstellt. Sie können einen Befehl wie `echo` verwenden, um den für Variablen festgelegten Wert anzuzeigen, z. B. `echo $IDENTITY_NAME`.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: foo
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=$SUBSCRIPTION_ID
      - --clientid=$IDENTITY_CLIENT_ID
      - --resourcegroup=$IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Beachten Sie Folgendes: Die Poddefinition verfügt über die Bezeichnung *aadpodidbinding* mit einem Wert, der dem Namen der Podidentität entspricht, für die Sie im vorherigen Schritt `az aks pod-identity add` ausgeführt haben.

Stellen Sie `demo.yaml` in demselben Namespace wie Ihre Podidentität bereit, indem Sie `kubectl apply` verwenden:

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Vergewissern Sie sich mit `kubectl logs`, dass die Ausführung der Beispielanwendung erfolgreich war.

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Vergewissern Sie sich in den Protokollen, dass die Beschaffung des Tokens und der *GET*-Vorgang erfolgreich waren.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)" export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## Clean up

To remove AAD pod-managed identity from your cluster, remove the sample application and the pod identity from the cluster. Then remove the identity.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[RFC 1123]: https://tools.ietf.org/html/rfc1123
[DNS-Unterdomänenname]: https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names
