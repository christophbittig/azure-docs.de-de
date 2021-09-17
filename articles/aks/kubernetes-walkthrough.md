---
title: 'Schnellstart: Bereitstellen eines AKS-Clusters mithilfe der Azure CLI'
description: Hier erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle schnell einen Kubernetes-Cluster erstellen, eine Anwendung bereitstellen und die Leistung in Azure Kubernetes Service (AKS) überwachen.
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: cda15b8d0dfd5074fca75283589c3ea173310b4e
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769948"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Schnellstart: Bereitstellen eines Azure Kubernetes Service-Clusters über die Azure-Befehlszeilenschnittstelle

Azure Kubernetes Service (AKS) ist ein verwalteter Kubernetes-Dienst, mit dem Sie schnell Cluster bereitstellen und verwalten können. In diesem Schnellstart führen Sie folgende Schritte aus:
* Bereitstellen eines AKS-Clusters mithilfe der Azure CLI 
* Ausführen einer Anwendung mit mehreren Containern im Cluster, die ein Web-Front-End und eine Redis-Instanz enthält 
* Überwachen der Integrität des Clusters und der Pods, in denen Ihre Anwendung ausgeführt wird

  ![In Azure Kubernetes Service bereitgestellte Abstimmungs-App](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Für diese Schnellstartanleitung werden Grundkenntnisse in Bezug auf die Kubernetes-Konzepte vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Weitere Informationen zum Erstellen eines Windows Server-Knotenpools finden Sie unter [Erstellen eines AKS-Clusters, der Windows Server-Container unterstützt][windows-container-cli].

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.64 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.
- Die Identität, die Sie zum Erstellen Ihres Clusters verwenden, verfügt über die erforderlichen Mindestberechtigungen. Weitere Informationen zu Zugriff und Identität für AKS finden Sie unter [Zugriffs- und Identitätsoptionen für Azure Kubernetes Service (AKS)](concepts-identity.md).

> [!NOTE]
> Wenn Sie die Befehle in diesem Schnellstart nicht in Azure Cloud Shell, sondern lokal ausführen möchten, führen Sie sie als Administrator aus.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Wenn Sie eine Ressourcengruppe erstellen, werden Sie zur Angabe eines Speicherorts aufgefordert. Bei diesem Speicherort handelt es sich um Folgendes: 
* Speicherort der Metadaten der Ressourcengruppe
* Ausführungsort für Ihre Ressourcen in Azure, sofern Sie im Rahmen der Ressourcenerstellung keine andere Region angeben 

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

Erstellen Sie mit dem Befehl [az group create][az-group-create] eine Ressourcengruppe.


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Ausgabe für erfolgreich erstellte Ressourcengruppe:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>Aktivieren der Clusterüberwachung

Überprüfen Sie, ob *Microsoft.OperationsManagement* und *Microsoft.OperationalInsights* in Ihrem Abonnement registriert sind. So überprüfen Sie den Registrierungsstatus:

```azurecli
az provider show -n Microsoft.OperationsManagement -o table
az provider show -n Microsoft.OperationalInsights -o table
```
 
Sind *Microsoft.OperationsManagement* und *Microsoft.OperationalInsights* nicht registriert, verwenden Sie den folgenden Befehl für die Registrierung:
 
```azurecli
az provider register --namespace Microsoft.OperationsManagement
az provider register --namespace Microsoft.OperationalInsights
```

## <a name="create-aks-cluster"></a>Erstellen eines ACS-Clusters

Erstellen Sie mit dem Befehl [az aks create][az-aks-create] mit dem Überwachungsparameter *--enable-addons* einen AKS-Cluster, um [Azure Monitor für Container][azure-monitor-containers] zu aktivieren. Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* mit einem Knoten erstellt: 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Nach wenigen Minuten ist die Ausführung des Befehls abgeschlossen, und es werden Informationen zum Cluster im JSON-Format zurückgegeben.

> [!NOTE]
> Beim Erstellen eines AKS-Clusters wird automatisch eine zweite Ressourcengruppe erstellt, um die AKS-Ressourcen zu speichern. Weitere Informationen finden Sie unter [Warum werden zwei Ressourcengruppen mit AKS erstellt?](./faq.md#why-are-two-resource-groups-created-with-aks).

## <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl][kubectl]. `kubectl` ist bei Verwendung von Azure Cloud Shell bereits installiert. 

1. Verwenden Sie für die lokale Installation von `kubectl` den Befehl [az aks install-cli][az-aks-install-cli]:

    ```azurecli
    az aks install-cli
    ```

2. Mit dem Befehl [az aks get-credentials][az-aks-get-credentials] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Der unten angegebene Befehl bewirkt Folgendes:  
      * Herunterladen von Anmeldeinformationen und Konfigurieren der Kubernetes-Befehlszeilenschnittstelle für ihre Verwendung
      * Verwenden von `~/.kube/config` (Standardspeicherort für die [Kubernetes-Konfigurationsdatei][kubeconfig-file]). Geben Sie mit *--file* einen anderen Speicherort für Ihre Kubernetes-Konfigurationsdatei an.


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Überprüfen Sie die Verbindung mit dem Cluster mithilfe des Befehls [kubectl get][kubectl-get]. Dieser Befehl gibt eine Liste der Clusterknoten zurück.

    ```azurecli-interactive
    kubectl get nodes
    ```

    Die Ausgabe zeigt den in den vorherigen Schritten erstellten einzelnen Knoten. Stellen Sie sicher, dass der Knotenstatus *Bereit* lautet:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>Ausführen der Anwendung

Eine [Kubernetes-Manifestdatei][kubernetes-deployment] definiert den gewünschten Zustand (Desired State) eines Clusters – also beispielsweise, welche Containerimages ausgeführt werden sollen. 

In dieser Schnellstartanleitung verwenden Sie ein Manifest, um alle Objekte zu erstellen, die zum Ausführen der [Azure Vote-Anwendung][azure-vote-app] benötigt werden. Dieses Manifest umfasst zwei [Kubernetes-Bereitstellungen][kubernetes-deployment]:
* Die Azure Vote-Python-Beispielanwendungen
* Eine Redis-Instanz 

Darüber hinaus werden zwei [Kubernetes-Dienste][kubernetes-service] erstellt:
* Ein interner Dienst für die Redis-Instanz
* Ein externer Dienst für den Zugriff auf die Azure Vote-Anwendung über das Internet

1. Erstellen Sie eine Datei mit dem Namen `azure-vote.yaml`.
    * Bei Verwendung von Azure Cloud Shell kann diese Datei mit `code`, `vi` oder `nano` erstellt werden – genau wie bei der Verwendung eines virtuellen oder physischen Systems.
1. Fügen Sie die folgende YAML-Definition ein:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. Stellen Sie die Anwendung über den Befehl [kubectl apply][kubectl-apply] bereit, und geben Sie den Namen Ihres YAML-Manifests an:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    Die Ausgabe zeigt die erfolgreich erstellten Bereitstellungen und Dienste:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar. Dieser Vorgang kann einige Minuten dauern.

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service][kubectl-get] mit dem Argument `--watch`:

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Die Ausgabe von **EXTERNAL-IP** für den `azure-vote-front`-Dienst lautet zu Beginn *pending*:

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Nachdem die externe IP-Adresse (**EXTERNAL-IP**) von *ausstehend* in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Azure-Abstimmungs-App in Aktion zu sehen.

![In Azure Kubernetes Service bereitgestellte Abstimmungs-App](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Zeigen Sie die von [Azure Monitor für Container][azure-monitor-containers] erfassten Integritätsmetriken der Clusterknoten und Pods im Azure-Portal an. 

## <a name="delete-the-cluster"></a>Löschen des Clusters

Zum Vermeiden von Azure-Gebühren sollten Sie nicht benötigte Ressourcen bereinigen. Führen Sie den Befehl [az group delete][az-group-delete] aus, um die Ressourcengruppe, den Containerdienst und alle dazugehörigen Ressourcen zu entfernen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wenn Sie den Cluster löschen, wird der vom AKS-Cluster verwendete Azure Active Directory-Dienstprinzipal nicht entfernt. Schritte zum Entfernen des Dienstprinzipals finden Sie unter den [Überlegungen zum AKS-Dienstprinzipal und dessen Löschung][sp-delete].
> 
> Wenn Sie eine verwaltete Identität verwendet haben, wird die Identität von der Plattform verwaltet und muss nicht entfernt werden.

## <a name="get-the-code"></a>Abrufen des Codes

In dieser Schnellstartanleitung wurden vorab erstellte Containerimages verwendet, um eine Kubernetes-Bereitstellung zu erstellen. Der dazugehörige Anwendungscode, die Dockerfile-Datei und die Kubernetes-Manifestdatei sind [auf GitHub verfügbar][azure-vote-app].

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Kubernetes-Cluster und eine Anwendung mit mehreren Containern dafür bereitgestellt. [Greifen Sie für Ihren AKS-Cluster auf das Kubernetes-Webdashboard zu.][kubernetes-dashboard]

Weitere Informationen zu Azure Container Service sowie ein vollständiges Beispiel vom Code bis zur Bereitstellung finden Sie im Kubernetes-Clustertutorial.

> [!div class="nextstepaction"]
> [AKS-Tutorial][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install_azure_cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md
