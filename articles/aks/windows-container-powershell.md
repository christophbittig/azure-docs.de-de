---
title: Erstellen eines Windows Server-Containers in einem AKS-Cluster mithilfe von PowerShell
description: Hier erfahren Sie, wie Sie mit PowerShell schnell einen Kubernetes-Cluster erstellen und eine Anwendung in einem Windows Server-Container in Azure Kubernetes Service (AKS) bereitstellen.
services: container-service
ms.topic: article
ms.date: 03/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bd419902055758a089772a2ee99c40c4e66ff750
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235210"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>Erstellen eines Windows Server-Containers auf einem Azure Kubernetes Service (AKS)-Cluster mit PowerShell

Azure Kubernetes Service (AKS) ist ein verwalteter Kubernetes-Dienst, mit dem Sie schnell Cluster bereitstellen und verwalten können. In diesem Artikel stellen Sie einen AKS-Cluster mit PowerShell bereit. Sie stellen auch eine `ASP.NET`-Beispielanwendung in einem Windows Server-Container für den Cluster bereit.

![Abbildung der Navigation zur ASP.NET-Beispielanwendung](media/windows-container-powershell/asp-net-sample-app.png)

Für diesen Artikel werden Grundkenntnisse in Bezug auf die Kubernetes-Konzepte vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Falls Sie PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Az PowerShell-Modul installieren und mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell][install-azure-powershell]. Sie müssen auch das Az.Aks PowerShell-Modul installieren:

```azurepowershell-interactive
Install-Module Az.Aks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) eine bestimmte Abonnement-ID aus.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>Einschränkungen

Die folgenden Einschränkungen gelten für die Erstellung und Verwaltung von AKS-Clustern, die mehrere Knotenpools unterstützen:

* Sie können den ersten Knotenpool nicht löschen.

Die folgenden zusätzlichen Einschränkungen gelten für Windows Server-Knotenpools:

* Der AKS-Cluster kann maximal 10 Knotenpools umfassen.
* Der AKS-Cluster kann maximal 100 Knoten in den einzelnen Knotenpools umfassen.
* Der Name des Windows Server-Knotenpools ist auf 6 Zeichen begrenzt.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Wenn Sie eine Ressourcengruppe erstellen, müssen Sie einen Speicherort angeben. An diesem Speicherort werden die Metadaten der Ressourcengruppe gespeichert. Darüber hinaus werden dort die Ressourcen in Azure ausgeführt, wenn Sie während der Ressourcenerstellung keine andere Region angeben. Erstellen Sie mit dem Cmdlet [New-AzResourceGroup][new-azresourcegroup] eine neue Ressourcengruppe.

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen **myResourceGroup** am Standort **eastus**.

> [!NOTE]
> In diesem Artikel wird die PowerShell-Syntax für die Befehle des Tutorials verwendet. Stellen Sie bei Verwendung von Azure Cloud Shell sicher, dass die Dropdownliste oben links im Cloud Shell-Fenster auf **PowerShell** festgelegt ist.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Die folgende Beispielausgabe zeigt, dass die Ressourcengruppe erfolgreich erstellt wurde:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Verwenden Sie das Befehlszeilenprogramm `ssh-keygen`, um ein SSH-Schlüsselpaar zu generieren. Weitere Informationen finden Sie unter [Kurzanleitung: Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

Um einen AKS-Cluster auszuführen, der Knotenpools für Windows Server-Container unterstützt, muss Ihr Cluster eine Netzwerkrichtlinie verwenden, die das [Azure CNI][azure-cni-about]-Netzwerk-Plug-In (Erweitert) verwendet. Detaillierte Informationen zur Planung der erforderlichen Subnetzbereiche sowie Netzwerküberlegungen finden Sie unter [Konfigurieren von Azure CNI-Netzwerken][use-advanced-networking]. Erstellen Sie mit dem unten aufgeführten Cmdlet [New-AzAksCluster][new-azakscluster] einen AKS-Cluster mit dem Namen **myAKSCluster**. Im folgenden Beispiel werden die erforderlichen Netzwerkressourcen erstellt, wenn sie nicht vorhanden sind.

> [!NOTE]
> Um zu gewährleisten, dass Ihr Cluster zuverlässig funktioniert, sollten Sie mindestens zwei Knoten im Standardknotenpool ausführen.

```azurepowershell-interactive
$Username = Read-Host -Prompt 'Please create a username for the administrator credentials on your Windows Server containers: '
$Password = Read-Host -Prompt 'Please create a password for the administrator credentials on your Windows Server containers: ' -AsSecureString
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName $Username -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> Wenn Sie den AKS-Cluster nicht erstellen können, weil die Version in dieser Region nicht unterstützt wird, können Sie den Befehl `Get-AzAksVersion -Location eastus` verwenden, um die Liste der unterstützten Versionen für diese Region zu ermitteln.

Nach wenigen Minuten ist die Ausführung des Befehls abgeschlossen, und es werden Informationen zum Cluster zurückgegeben. Gelegentlich kann die Bereitstellung des Clusters länger als ein paar Minuten dauern. Warten Sie in diesen Fällen bis zu 10 Minuten.

## <a name="add-a-windows-server-node-pool"></a>Hinzufügen eines Windows Server-Knotenpools

Standardmäßig wird ein AKS-Cluster mit einem Knotenpool erstellt, der Linux-Container ausführen kann. Verwenden Sie das Cmdlet `New-AzAksNodePool`, um einen Knotenpool hinzuzufügen, der Windows Server-Container neben dem Linux-Knotenpool ausführen kann.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -VmSetType VirtualMachineScaleSets -OsType Windows -Name npwin
```

Der obige Befehl erstellt einen neuen Knotenpool namens **npwin** und fügt ihn dem **myAKSCluster** hinzu. Wenn Sie einen Knotenpool erstellen, um Windows Server-Container auszuführen, hat **VmSize** den Standardwert **Standard_D2s_v3**. Wenn Sie den Parameter **VmSize** festlegen, überprüfen Sie die Liste mit den [eingeschränkten VM-Größen][restricted-vm-sizes]. Die empfohlene Mindestgröße ist **Standard_D2s_v3**. Der vorherige Befehl verwendet auch das Standardsubnetz im Standard-Vnet, das beim Ausführen von `New-AzAksCluster` erstellt wurde.

## <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl][kubectl]. Bei Verwendung von Azure Cloud Shell ist `kubectl` bereits installiert. Um `kubectl` lokal zu installieren, verwenden Sie das Cmdlet `Install-AzAksKubectl`:

```azurepowershell-interactive
Install-AzAksKubectl
```

Verwenden Sie das Cmdlet [Import-AzAksCredential][import-azakscredential], um `kubectl` für die Verbindung mit Ihrem Kubernetes-Cluster zu konfigurieren. Mit diesem Befehl werden die Anmeldeinformationen heruntergeladen, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get][kubectl-get], um eine Liste der Clusterknoten zurückzugeben.

```azurepowershell-interactive
kubectl get nodes
```

Die folgende Beispielausgabe zeigt alle Knoten im Cluster. Vergewissern Sie sich, dass alle Knoten den Status **Bereit** haben:

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Eine Kubernetes-Manifestdatei definiert einen gewünschten Zustand (Desired State) für den Cluster – also beispielsweise, welche Containerimages ausgeführt werden sollen. In diesem Artikel wird ein Manifest verwendet, um alle Objekte zu erstellen, die zum Ausführen der ASP.NET-Beispielanwendung in einem Windows Server-Container erforderlich sind. Dieses Manifest beinhaltet eine [Kubernetes-Bereitstellung][kubernetes-deployment] für die ASP.NET-Beispielanwendung und einen externen [Kubernetes-Dienst][kubernetes-service] für den Zugriff auf die Anwendung über das Internet.

Die ASP.NET-Beispielanwendung wird als Teil des [.NET Framework-Beispiels][dotnet-samples] bereitgestellt und in einem Windows Server-Container ausgeführt. AKS verlangt, dass Windows Server-Container auf Images von **Windows Server 2019** oder höher basieren. Mit der Kubernetes-Manifestdatei muss auch eine [Knotenauswahl][node-selector] definiert werden. So wird Ihrem AKS-Cluster mitgeteilt, dass der Pod Ihrer ASP.NET-Beispielanwendung auf einem Knoten ausgeführt werden soll, für den die Ausführung von Windows Server-Containern möglich ist.

Erstellen Sie eine Datei namens `sample.yaml`, und fügen Sie die folgende YAML-Definition ein. Bei Verwendung von Azure Cloud Shell kann diese Datei mit `vi` oder `nano` erstellt werden – genau wie bei der Verwendung eines virtuellen oder physischen Systems:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Stellen Sie die Anwendung über den Befehl [kubectl apply][kubectl-apply] bereit, und geben Sie den Namen Ihres YAML-Manifests an:

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

In der folgende Beispielausgabe sind die erfolgreich erstellten Bereitstellungen und Dienste aufgeführt:

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar.
Dieser Vorgang kann einige Minuten dauern. Gelegentlich kann die Bereitstellung des Diensts länger als ein paar Minuten dauern. Warten Sie in diesen Fällen bis zu 10 Minuten.

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service][kubectl-get] mit dem Argument `--watch`.

```azurepowershell-interactive
kubectl get service sample --watch
```

Die externe IP-Adresse **EXTERNAL-IP** für den **Beispieldienst** wird zunächst als **ausstehend** angezeigt.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Sobald die externe IP-Adresse (**EXTERNAL-IP**) von pending (**ausstehend**) in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Beispielanwendung in Aktion zu sehen.

![Abbildung der Navigation zur ASP.NET-Beispielanwendung](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> Wenn bei dem Versuch, die Seite zu laden, ein Verbindungstimeout auftritt, sollten Sie mit dem folgenden Befehl überprüfen, ob die Beispiel-App bereit ist: `kubectl get pods --watch`. Manchmal wird der Windows-Container nicht gestartet, wenn Ihre externe IP-Adresse verfügbar ist.

## <a name="delete-cluster"></a>Löschen von Clustern

Wenn der Cluster nicht mehr benötigt wird, entfernen Sie die Ressourcengruppe, den Containerdienst und alle zugehörigen Ressourcen mit dem Cmdlet [Remove-AzResourceGroup][remove-azresourcegroup].

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Wenn Sie den Cluster löschen, wird der vom AKS-Cluster verwendete Azure Active Directory-Dienstprinzipal nicht entfernt. Schritte zum Entfernen des Dienstprinzipals finden Sie unter den [Überlegungen zum AKS-Dienstprinzipal und dessen Löschung][sp-delete]. Wenn Sie eine verwaltete Identität verwendet haben, wird die Identität von der Plattform verwaltet und muss nicht entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen Kubernetes-Cluster und eine `ASP.NET`-Beispielanwendung in einem Windows Server-Container bereitgestellt. [Greifen Sie auf das Kubernetes-Webdashboard zu][kubernetes-dashboard] (für den Cluster, den Sie erstellt haben).

Weitere Informationen zu Azure Container Service sowie ein vollständiges Beispiel vom Code bis zur Bereitstellung finden Sie im Kubernetes-Clustertutorial.

> [!div class="nextstepaction"]
> [AKS-Tutorial][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azakscluster]: /powershell/module/az.aks/new-azakscluster
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
