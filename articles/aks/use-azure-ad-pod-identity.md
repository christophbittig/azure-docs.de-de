---
title: Verwenden von verwalteten Azure Active Directory-Podidentitäten in Azure Kubernetes Service (Vorschauversion)
description: Erfahren Sie, wie Sie verwaltete Azure AD-Podidentitäten in Azure Kubernetes Service (AKS) verwenden.
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: 3792dbfe187ef6e4b850338448bde5417b78e687
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501759"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Verwenden von verwalteten Azure Active Directory-Podidentitäten in Azure Kubernetes Service (Vorschauversion)

Für verwaltete Azure Active Directory-Podidentitäten (Azure AD) werden Kubernetes-Primitive verwendet, um [verwaltete Identitäten für Azure-Ressourcen][az-managed-identities] und Identitäten in Azure AD Pods zuzuordnen. Administratoren erstellen Identitäten und Bindungen als Kubernetes-Primitive, die Pods den Zugriff auf Azure-Ressourcen ermöglichen, für die Azure AD als Identitätsanbieter genutzt wird.

> [!NOTE]
> Das in diesem Dokument beschriebene Feature der über Pods verwalteten Identitäten (Vorschau) wird durch Version 2 der über Pods verwalteten Identitäten (Vorschau) ersetzt.

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

### <a name="operation-mode-options"></a>Betriebsmodusoptionen

Azure AD-Podidentität unterstützt zwei Betriebsmodi:
 
* **Standardmodus**: In diesem Modus werden die beiden folgenden Komponenten im AKS-Cluster bereitgestellt: 
  * [Managed Identity Controller (MIC)](https://azure.github.io/aad-pod-identity/docs/concepts/mic/): Ein MIC ist ein Kubernetes-Controller, der das System über den Kubernetes-API-Server auf Änderungen an Pods, [AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) und [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/) überwacht. Wenn eine relevante Änderung erkannt wird, fügt der MIC nach Bedarf [AzureAssignedIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureassignedidentity/) hinzu oder löscht sie. Insbesondere bei der Planung eines Pods weist der MIC die verwaltete Identität in Azure der zugrunde liegenden VM-Skalierungsgruppe zu, die während der Erstellungsphase vom Knotenpool verwendet wird. Wenn alle Pods, die die Identität verwenden, gelöscht werden, wird die Identität aus der VM-Skalierungsgruppe des Knotenpools entfernt, es sei denn, die gleiche verwaltete Identität wird von anderen Pods verwendet. Der MIC führt ähnliche Aktionen aus, wenn AzureIdentity oder AzureIdentityBinding erstellt oder gelöscht werden.
  * [Node Management Identity (NMI):](https://azure.github.io/aad-pod-identity/docs/concepts/nmi/) NMI ist ein Pod, der auf jedem Knoten im AKS-Cluster als DaemonSet ausgeführt wird. NMI fängt Sicherheitstokenanforderungen an [Azure Instance Metadata Service](../virtual-machines/linux/instance-metadata-service.md?tabs=linux) auf jedem Knoten ab, leitet sie an sich selbst weiter und überprüft, ob der Pod Zugriff auf die Identität hat, für die er ein Token anfordert. Anschließend wird das Token im Auftrag der Anwendung vom Azure AD-Mandanten abgerufen.
* **Verwalteter Modus**: Dieser Modus bietet nur NMI. Die Identität muss vom Benutzer manuell zugewiesen und verwaltet werden. Weitere Informationen finden Sie unter [Podidentität im verwalteten Modus](https://azure.github.io/aad-pod-identity/docs/configure/pod_identity_in_managed_mode/).

Wenn Sie die Azure AD-Podidentität mit einem Helm-Chart oder YAML-Manifest installieren (wie in den [Installationsanweisungen](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) gezeigt), können Sie zwischen den Modi `standard` und `managed` wählen. Wenn Sie die Azure AD-Podidentität dagegen mithilfe des AKS-Cluster-Add-Ons installieren (wie in diesem Artikel gezeigt), wird der Modus `managed` vom Setup verwendet.

## <a name="create-an-aks-cluster-with-azure-container-networking-interface-cni"></a>Erstellen eines AKS-Clusters mit Azure Container Networking Interface (CNI)

> [!NOTE]
> Dies ist die standardmäßig empfohlene Konfiguration.

Erstellen Sie einen AKS-Cluster mit Azure CNI, für den eine verwaltete Podidentität aktiviert ist. Im Folgenden wird der Befehl [az group create][az-group-create] verwendet, um eine Ressourcengruppe mit dem Namen *myResourceGroup* zu erstellen, und der Befehl [az aks create][az-aks-create], um in der Ressourcengruppe *myResourceGroup* einen AKS-Cluster mit dem Namen *myAKSCluster* zu erstellen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

Verwenden Sie [az aks get-credentials][az-aks-get-credentials], um sich bei Ihrem AKS-Cluster anzumelden. Mit diesem Befehl wird auch das `kubectl`-Clientzertifikat auf Ihren Entwicklungscomputer heruntergeladen und konfiguriert.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

> [!NOTE]
> Wenn Sie verwaltete Podidentitäten in Ihrem AKS-Cluster aktivieren, wird dem Namespace *kube-system* eine AzurePodIdentityException mit dem Namen *aks-addon-exception* hinzugefügt. AzurePodIdentityException ermöglicht Pods mit bestimmten Bezeichnungen den Zugriff auf den Azure Instance Metadata Service-Endpunkt (IMDS), ohne vom NMI-Server abgefangen zu werden. Mit *aks-addon-exception* können AKS-Erstanbieter-Add-Ons (z. B. eine verwaltete Azure AD-Podidentität) betrieben werden, ohne dass eine manuelle Konfiguration von AzurePodIdentityException erfolgen muss. Optional können Sie ein AzurePodIdentityException-Element hinzufügen, entfernen und aktualisieren, indem Sie `az aks pod-identity exception add`, `az aks pod-identity exception delete`, `az aks pod-identity exception update` oder `kubectl` verwenden.

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Aktualisieren eines vorhandenen AKS-Clusters mit Azure CNI

Aktualisieren Sie einen vorhandenen AKS-Cluster mit Azure CNI, um eine verwaltete Podidentität zu integrieren.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity
```

## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Verwenden des kubenet-Netzwerk-Plug-Ins mit verwalteten Azure Active Directory-Podidentitäten 

> [!IMPORTANT]
> Das Ausführen von AAD-Poditentitäten in einem Cluster mit kubenet ist aufgrund der Sicherheitsimplikationen keine empfohlene Konfiguration. Befolgen Sie die Schritte zur Risikominderung und konfigurieren Sie Richtlinien, bevor Sie AAD-Poditentitäten in einem Cluster mit kubenet aktivieren.

### <a name="mitigation"></a>Minderung

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

> [!IMPORTANT]
> Sie müssen über die entsprechenden Berechtigungen (z. B. „Besitzer“) für Ihr Abonnement verfügen, um die Identität zu erstellen.

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

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> POD_IDENTITY_NAME muss ein gültiger [DNS-Unterdomänenname] sein, wie in [RFC 1123] definiert. 

> [!NOTE]
> Wenn Sie die Podidentität mithilfe von `pod-identity add` zuweisen, wird von der Azure CLI versucht, der Clusteridentität über die Podidentität (*IDENTITY_RESOURCE_ID*) die Rolle „Operator für verwaltete Identität“ zuzuweisen.

## <a name="run-a-sample-application"></a>Ausführen einer Beispielanwendung

Damit ein Pod eine verwaltete Azure AD-Podidentität verwenden kann, benötigt der Pod die Bezeichnung *aadpodidbinding* mit einem Wert, der mit einem Selektor einer *AzureIdentityBinding* übereinstimmt. Erstellen Sie eine `demo.yaml`-Datei mit dem folgenden Inhalt, um eine Beispielanwendung mit Verwendung einer verwalteten Azure AD-Podidentität auszuführen. Ersetzen Sie *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* und *IDENTITY_RESOURCE_GROUP* durch die Werte aus den vorherigen Schritten. Ersetzen Sie *SUBSCRIPTION_ID* durch Ihre Abonnement-ID.

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

Vergewissern Sie sich in den Protokollen, dass der Abruf eines Tokens und der *GET*-Vorgang erfolgreich waren.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="run-an-application-with-multiple-identities"></a>Ausführen einer Anwendung mit mehreren Identitäten

Damit eine Anwendung mehrere Identitäten verwenden kann, legen Sie beim Erstellen von Podidentitäten `--binding-selector` auf denselben Selektor fest.

```azurecli-interactive
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME_1} --identity-resource-id ${IDENTITY_RESOURCE_ID_1} --binding-selector myMultiIdentitySelector
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME_2} --identity-resource-id ${IDENTITY_RESOURCE_ID_2} --binding-selector myMultiIdentitySelector
```

Legen Sie dann das `aadpodidbinding`-Feld in Ihrem Pod-YAML auf den von Ihnen angegebenen Bindungsselektor fest.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: myMultiIdentitySelector
...
```

## <a name="clean-up"></a>Bereinigen

Um die verwaltete Azure AD-Podidentität aus Ihrem Cluster zu entfernen, müssen Sie die Beispielanwendung und die Podidentität aus dem Cluster entfernen. Dies bewirkt, dass die Identität entfernt wird.

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
