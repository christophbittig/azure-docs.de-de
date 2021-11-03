---
title: 'Schnellstart: Bereitstellen eines AKS-Clusters mithilfe von PowerShell'
description: Hier erfahren Sie, wie Sie über PowerShell schnell einen Kubernetes-Cluster erstellen, eine Anwendung bereitstellen und die Leistung in Azure Kubernetes Service (AKS) überwachen.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f9556d1169180bd55ac5ff646ee920f630ad96d0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058671"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Schnellstart: Bereitstellen eines Azure Kubernetes Service-Clusters mit PowerShell

Azure Kubernetes Service (AKS) ist ein verwalteter Kubernetes-Dienst, mit dem Sie schnell Cluster bereitstellen und verwalten können. In diesem Schnellstart führen Sie folgende Schritte aus:
* Bereitstellen eines AKS-Clusters mithilfe von PowerShell
* Ausführen einer Anwendung mit mehreren Containern im Cluster, die ein Web-Front-End und eine Redis-Instanz enthält
* Überwachen der Integrität des Clusters und der Pods, in denen Ihre Anwendung ausgeführt wird

Weitere Informationen zum Erstellen eines Windows Server-Knotenpools finden Sie unter [Erstellen eines AKS-Clusters, der Windows Server-Container unterstützt][windows-container-powershell].

![In Azure Kubernetes Service bereitgestellte Abstimmungs-App](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Für diese Schnellstartanleitung werden Grundkenntnisse in Bezug auf die Kubernetes-Konzepte vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Falls Sie PowerShell lokal ausführen, müssen Sie das Az PowerShell-Modul installieren und mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie mithilfe des Cmdlets [Set-AzContext](/powershell/module/az.accounts/set-azcontext) die ID des entsprechenden Abonnements aus, in dem die Ressourcen fakturiert werden sollen.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Wenn Sie eine Ressourcengruppe erstellen, werden Sie zur Angabe eines Speicherorts aufgefordert. Bei diesem Speicherort handelt es sich um Folgendes:
* Speicherort der Metadaten der Ressourcengruppe
* Ausführungsort für Ihre Ressourcen in Azure, sofern Sie im Rahmen der Ressourcenerstellung keine andere Region angeben

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen **myResourceGroup** in der Region **eastus** erstellt.

Erstellen Sie mit dem Cmdlet [New-AzResourceGroup][new-azresourcegroup] eine neue Ressourcengruppe.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Ausgabe für erfolgreich erstellte Ressourcengruppe:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Erstellen eines ACS-Clusters

1. Verwenden Sie das Befehlszeilenprogramm `ssh-keygen`, um ein SSH-Schlüsselpaar zu generieren. Weitere Informationen finden Sie unter:
    * [Kurzanleitung: Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../virtual-machines/linux/mac-create-ssh-keys.md)
    * [Verwenden von SSH-Schlüsseln mit Windows in Azure](../virtual-machines/linux/ssh-from-windows.md)

1. Verwenden Sie das Cmdlet [New-AzAksCluster][new-azakscluster], um einen AKS-Cluster zu erstellen. Azure Monitor für Container ist standardmäßig aktiviert.

    Im folgenden Beispiel wird ein Cluster mit dem Namen **myAKSCluster** mit einem Knoten erstellt.

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

Nach wenigen Minuten ist die Ausführung des Befehls abgeschlossen, und es werden Informationen zum Cluster zurückgegeben.

> [!NOTE]
> Beim Erstellen eines AKS-Clusters wird automatisch eine zweite Ressourcengruppe erstellt, um die AKS-Ressourcen zu speichern. Weitere Informationen finden Sie unter [Warum werden zwei Ressourcengruppen mit AKS erstellt?](./faq.md#why-are-two-resource-groups-created-with-aks).

## <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl][kubectl]. `kubectl` ist bei Verwendung von Azure Cloud Shell bereits installiert.

1. Installieren Sie `kubectl` mithilfe des Cmdlets `Install-AzAksKubectl` lokal:

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. Verwenden Sie das Cmdlet [Import-AzAksCredential][import-azakscredential], um `kubectl` für die Verbindung mit Ihrem Kubernetes-Cluster zu konfigurieren. Das folgende Cmdlet lädt Anmeldeinformationen herunter und konfiguriert die Kubernetes-Befehlszeilenschnittstelle für deren Verwendung:

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. Überprüfen Sie die Verbindung mit dem Cluster mithilfe des Befehls [kubectl get][kubectl-get]. Dieser Befehl gibt eine Liste der Clusterknoten zurück.

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    Die Ausgabe zeigt den in den vorherigen Schritten erstellten einzelnen Knoten. Stellen Sie sicher, dass der Knotenstatus *Bereit* lautet:

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
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

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    Die Ausgabe zeigt die erfolgreich erstellten Bereitstellungen und Dienste:

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar. Dieser Vorgang kann einige Minuten dauern.

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service][kubectl-get] mit dem Argument `--watch`:

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Die Ausgabe von **EXTERNAL-IP** für den `azure-vote-front`-Dienst lautet zu Beginn *pending*:

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Nachdem die externe IP-Adresse (**EXTERNAL-IP**) von *ausstehend* in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Azure-Abstimmungs-App in Aktion zu sehen.

![In Azure Kubernetes Service bereitgestellte Abstimmungs-App](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Zeigen Sie die von Azure Monitor für Container erfassten Integritätsmetriken der Clusterknoten und Pods im Azure-Portal an.

## <a name="delete-the-cluster"></a>Löschen des Clusters

Zum Vermeiden von Azure-Gebühren sollten Sie nicht benötigte Ressourcen bereinigen. Entfernen Sie die Ressourcengruppe, den Containerdienst und alle zugehörigen Ressourcen mit dem Cmdlet [Remove-AzResourceGroup][remove-azresourcegroup].

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: /previous-versions/azure/dev-spaces/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azakscluster]: /powershell/module/az.aks/new-azakscluster
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md