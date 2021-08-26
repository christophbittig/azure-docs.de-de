---
title: 'Schnellstart: Erstellen eines AKS-Clusters (Azure Kubernetes Service)'
description: Hier erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage schnell einen Kubernetes-Cluster erstellen und eine Anwendung in Azure Kubernetes Service (AKS) bereitstellen.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: b6c5371baba93470f76a99e0e37cd61870ee108f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733582"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Schnellstart: Schnellstart: Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) mithilfe einer ARM-Vorlage

Azure Kubernetes Service (AKS) ist ein verwalteter Kubernetes-Dienst, mit dem Sie schnell Cluster bereitstellen und verwalten können. In diesem Schnellstart führen Sie folgende Schritte aus:
* Bereitstellen eines AKS-Clusters mithilfe einer Azure Resource Manager-Vorlage 
* Ausführen einer Anwendung mit mehreren Containern im Cluster, die ein Web-Front-End und eine Redis-Instanz enthält 

![Abbildung der Navigation zu Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Für diese Schnellstartanleitung werden Grundkenntnisse in Bezug auf die Kubernetes-Konzepte vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)][kubernetes-concepts].

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.kubernetes%2Faks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.61 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

- Um einen AKS-Cluster mithilfe einer Resource Manager-Vorlage zu erstellen, geben Sie einen öffentlichen SSH-Schlüssel an. Wenn Sie diese Ressource benötigen, finden Sie im folgenden Abschnitt Informationen, andernfalls fahren Sie mit dem Abschnitt [Überprüfen der Vorlage](#review-the-template) fort.

### <a name="create-an-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Für den Zugriff auf AKS-Knoten stellen Sie eine Verbindung mithilfe eines SSH-Schlüsselpaars (öffentlich und privat) her, das Sie mithilfe des Befehls `ssh-keygen` generieren. Diese Dateien werden standardmäßig im Verzeichnis *~/.ssh* erstellt. Durch die Ausführung des Befehls `ssh-keygen` wird jedes SSH-Schlüsselpaar mit dem gleichen Namen überschrieben, das bereits am angegebenen Standort vorhanden ist.

1. Navigieren Sie zu [https://shell.azure.com](https://shell.azure.com), um Cloud Shell in Ihrem Browser zu öffnen.

1. Führen Sie den Befehl `ssh-keygen` aus. Das folgende Beispiel erstellt ein SSH-Schlüsselpaar mittels RSA-Verschlüsselung und einer Bitlänge von 2048:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

Weitere Informationen zum Erstellen von SSH-Schlüsseln finden Sie unter [Erstellen und Verwalten von SSH-Schlüsseln für die Authentifizierung in Azure][ssh-keys].

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/aks/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.kubernetes/aks/azuredeploy.json":::

Weitere AKS-Beispiele finden Sie unter [Azure Schnellstartvorlagen][aks-quickstart-templates].

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie die folgende Schaltfläche aus, um sich bei Azure anzumelden und eine Vorlage zu öffnen:

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.kubernetes%2Faks%2Fazuredeploy.json)

2. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein.

    Im Rahmen dieser Schnellstartanleitung behalten Sie die Standardwerte für *Betriebssystem-Datenträgergröße (GB)* , *Agent-Anzahl*, *Größe der Agent-VM*, *Betriebssystemtyp*, und *Kubernetes-Version* bei. Geben Sie Ihre eigenen Werte für die folgenden Vorlagenparameter an:

    * **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
    * **Ressourcengruppe**: Wählen Sie **Neu erstellen**. Geben Sie einen eindeutigen Namen für die Ressourcengruppe an, z. B. *myResourceGroup*, und wählen Sie dann **OK** aus.
    * **Standort**: Wählen Sie einen Ort aus, z. B. **USA, Osten**.
    * **Clustername**: Geben Sie einen eindeutigen Namen für den AKS-Cluster ein, z. B. *myAKSCluster*.
    * **DNS-Präfix**: Geben Sie ein eindeutiges DNS-Präfix für Ihren Cluster ein, z. B. *myakscluster*.
    * **Benutzername des Linux-Administrators**: Geben Sie einen Benutzernamen ein, um mithilfe von SSH eine Verbindung herzustellen, z. B. *azureuser*.
    * **Öffentlicher SSH RSA-Schlüssel**: Kopieren Sie den *öffentlichen* Teil Ihres SSH-Schlüsselpaar, und fügen Sie ihn ein (standardmäßig der Inhalt von *~/.ssh/id_rsa.pub*).

    ![Resource Manager-Vorlage zum Erstellen eines Azure Kubernetes Service-Clusters im Portal](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Klicken Sie auf **Überprüfen + erstellen**.

Die Erstellung des AKS-Clusters dauert einige Minuten. Warten Sie, bis der Cluster erfolgreich bereitgestellt wurde, bevor Sie mit dem nächsten Schritt fortfahren.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

### <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl][kubectl]. `kubectl` ist bei Verwendung von Azure Cloud Shell bereits installiert. 

1. Verwenden Sie für die lokale Installation von `kubectl` den Befehl [az aks install-cli][az-aks-install-cli]:

    ```azurecli
    az aks install-cli
    ```

2. Mit dem Befehl [az aks get-credentials][az-aks-get-credentials] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Mit diesem Befehl werden die Anmeldeinformationen heruntergeladen, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Überprüfen Sie die Verbindung mit dem Cluster mithilfe des Befehls [kubectl get][kubectl-get]. Dieser Befehl gibt eine Liste der Clusterknoten zurück.

    ```console
    kubectl get nodes
    ```

    Die Ausgabe zeigt die in den vorherigen Schritten erstellten Knoten. Vergewissern Sie sich, dass der Status aller Knoten *Bereit* ist:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Ausführen der Anwendung

Eine [Kubernetes-Manifestdatei][kubernetes-deployment] definiert den gewünschten Zustand (Desired State) eines Clusters – also beispielsweise, welche Containerimages ausgeführt werden sollen. 

In dieser Schnellstartanleitung verwenden Sie ein Manifest, um alle Objekte zu erstellen, die zum Ausführen der [Azure Vote-Anwendung][azure-vote-app] benötigt werden. Dieses Manifest umfasst zwei [Kubernetes-Bereitstellungen][kubernetes-deployment]:
* Die Azure Vote-Python-Beispielanwendungen
* Eine Redis-Instanz 

Darüber hinaus werden zwei [Kubernetes-Dienste][kubernetes-service] erstellt:
* Ein interner Dienst für die Redis-Instanz
* Ein externer Dienst für den Zugriff auf die Azure Vote-Anwendung über das Internet

1. Erstellen Sie eine Datei mit dem Namen `azure-vote.yaml`.
    * Bei Verwendung von Azure Cloud Shell kann diese Datei mit `vi` oder `nano` erstellt werden – genau wie bei der Verwendung eines virtuellen oder physischen Systems.
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

### <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar. Dieser Vorgang kann einige Minuten dauern.

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service][kubectl-get] mit dem Argument `--watch`:

```console
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

![Abbildung der Navigation zu Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

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
[azure-dev-spaces]: /previous-versions/azure/dev-spaces/
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

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
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac