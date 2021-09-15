---
title: 'Schnellstart: Herstellen einer Verbindung eines vorhandenen Kubernetes-Clusters mit Azure Arc'
description: In diesem Schnellstart erfahren Sie, wie Sie eine Verbindung mit einem Kubernetes-Cluster mit Azure Arc-Aktivierung herstellen.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 06/30/2021
ms.custom: template-quickstart
keywords: Kubernetes, Arc, Azure, Cluster
ms.openlocfilehash: 16e271cf6183dce74fad3075a2e8336030960a08
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966637"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Schnellstart: Herstellen einer Verbindung eines vorhandenen Kubernetes-Clusters mit Azure Arc

In diesem Schnellstart lernen Sie die Vorteile von Kubernetes mit Azure Arc-Unterstützung kennen und erfahren, wie Sie einen vorhandenen Kubernetes-Cluster mit Azure Arc verbinden. Eine konzeptionelle Übersicht über das Verbinden von Clustern mit Azure Arc finden Sie im Artikel[Architektur eine Azure Arc-fähigen Kubernetes-Agents](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

* [Installieren oder aktualisieren Sie Azure CLI](/cli/azure/install-azure-cli) auf eine Version >= 2.16.0

* Installieren Sie die Azure CLI-Erweiterung **connectedk8s** von Version >= 1.0.0:

  ```console
  az extension add --name connectedk8s
  ```

* Ein funktionierender Kubernetes-Cluster. Wenn Sie über keinen verfügen, haben Sie die folgenden drei Möglichkeiten, um einen Cluster zu erstellen:
    * [Kubernetes in Docker (KIND)](https://kind.sigs.k8s.io/)
    * Erstellen eines Kubernetes-Clusters mithilfe von Docker für [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) oder [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Selbst verwalteter Kubernetes-Cluster mithilfe der [Cluster-API](https://cluster-api.sigs.k8s.io/user/quick-start.html)
    * Wenn Sie einen OpenShift-Cluster mit Azure Arc verbinden möchten, müssen Sie den folgenden Befehl nur einmal in Ihrem Cluster ausführen, bevor Sie `az connectedk8s connect` ausführen:

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > Der Cluster muss mindestens einen Knoten des Betriebssystems und des Architektur Typs `linux/amd64` aufweisen. Cluster mit nur `linux/arm64`-Knoten werden noch nicht unterstützt.

* Eine `kubeconfig`-Datei und ein Kontext, der auf Ihren Cluster verweist.
* Lese- und Schreibberechtigungen für den Azure Arc-aktivierten Kubernetes-Ressourcentyp (`Microsoft.Kubernetes/connectedClusters`).

* Installieren Sie das [neueste Release von Helm 3](https://helm.sh/docs/intro/install).

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)


* [Azure PowerShell ab Version 5.9.0](/powershell/azure/install-az-ps)

* Installieren Sie das PowerShell-Modul **Az.ConnectedKubernetes**:

    ```azurepowershell-interactive
    Install-Module -Name Az.ConnectedKubernetes
    ```

    > [!IMPORTANT]
    > Solange nur eine Vorschauversion des PowerShell-Moduls **Az.ConnectedKubernetes** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren.

* Ein funktionierender Kubernetes-Cluster. Wenn Sie über keinen verfügen, haben Sie die folgenden drei Möglichkeiten, um einen Cluster zu erstellen:
    * [Kubernetes in Docker (KIND)](https://kind.sigs.k8s.io/)
    * Erstellen eines Kubernetes-Clusters mithilfe von Docker für [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) oder [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Selbst verwalteter Kubernetes-Cluster mithilfe der [Cluster-API](https://cluster-api.sigs.k8s.io/user/quick-start.html)
    * Wenn Sie einen OpenShift-Cluster mit Azure Arc verbinden möchten, müssen Sie den folgenden Befehl nur einmal in Ihrem Cluster ausführen, bevor Sie `New-AzConnectedKubernetes` ausführen:

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > Der Cluster muss mindestens einen Knoten des Betriebssystems und des Architektur Typs `linux/amd64` aufweisen. Cluster mit nur `linux/arm64`-Knoten werden noch nicht unterstützt.

* Eine `kubeconfig`-Datei und ein Kontext, der auf Ihren Cluster verweist.
* Lese- und Schreibberechtigungen für den Azure Arc-aktivierten Kubernetes-Ressourcentyp (`Microsoft.Kubernetes/connectedClusters`).

* Installieren Sie das [neueste Release von Helm 3](https://helm.sh/docs/intro/install).

---

## <a name="meet-network-requirements"></a>Erfüllen von Netzwerkanforderungen

> [!IMPORTANT]
> Azure Arc-Agents müssen über beide der folgenden Protokolle/Ports/Ausgehende URLs verfügen, um funktionieren zu können:
> * TCP an Port 443: `https://:443`

| Endpunkt (DNS) | BESCHREIBUNG |
| ----------------- | ------------- |
| `https://management.azure.com` (für Azure Cloud), `https://management.usgovcloudapi.net` (für Azure US Government) | Erforderlich, damit der Agent eine Verbindung mit Azure herstellen und den Cluster registrieren kann. |
| `https://<region>.dp.kubernetesconfiguration.azure.com` (für Azure Cloud), `https://<region>.dp.kubernetesconfiguration.azure.us` (für Azure US Government) | Endpunkt auf Datenebene, über den der Agent Statusinformationen mithilfe von Push übermitteln und Konfigurationsinformationen abrufen kann |
| `https://login.microsoftonline.com`, `login.windows.net` (für Azure Cloud), `https://login.microsoftonline.us` (für Azure US Government) | Erforderlich zum Abrufen und Aktualisieren von Azure Resource Manager-Token. |
| `https://mcr.microsoft.com` | Erforderlich zum Pullen von Containerimages für Azure Arc-Agents.                                                                  |
| `https://gbl.his.arc.azure.com` |  Erforderlich, um den regionalen Endpunkt zum Pullen vom System zugewiesener MSI-Zertifikate (Managed Service Identity) abzurufen |
| `https://*.his.arc.azure.com` (für Azure Cloud), `https://usgv.his.arc.azure.us` (für Azure US Government) |  Erforderlich zum Pullen vom System zugewiesener Zertifikate für verwaltete Dienstidentitäten (MSI). |
|`*.servicebus.windows.net`, `guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com`, `sts.windows.net` | Für Szenarien, die auf [Cluster Connect](cluster-connect.md) und [benutzerdefinierten Speicherorten](custom-locations.md) basieren. |

## <a name="1-register-providers-for-azure-arc-enabled-kubernetes"></a>1. Registrieren von Anbietern für Kubernetes mit Azure Arc-Unterstützung

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Geben Sie die folgenden Befehle ein:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Überwachen Sie den Registrierungsprozess. Die Registrierung kann bis zu 10 Minuten dauern.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

    Nach der Registrierung sollte der `RegistrationState`-Status für diese Namespaces in `Registered` geändert werden.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

1. Geben Sie die folgenden Befehle ein:
    ```azurepowershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```
1. Überwachen Sie den Registrierungsprozess. Die Registrierung kann bis zu 10 Minuten dauern.
    ```azurepowershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```

    Nach der Registrierung sollte der `RegistrationState`-Status für diese Namespaces in `Registered` geändert werden.
---

## <a name="2-create-a-resource-group"></a>2. Erstellen einer Ressourcengruppe

Führen Sie den folgenden Befehl aus:

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group create --name AzureArcTest --location EastUS --output table
```

Ausgabe:
<pre>
Location    Name
----------  ------------
eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name AzureArcTest -Location EastUS
```

Ausgabe:
<pre>
ResourceGroupName : AzureArcTest
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureArcTest
</pre>

---

## <a name="3-connect-an-existing-kubernetes-cluster"></a>3. Verbinden eines vorhandenen Kubernetes-Cluster

Führen Sie den folgenden Befehl aus:

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Ausgabe:
<pre>
Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
</pre>

> [!TIP]
> Mit dem obigen Befehl ohne den angegebenen location-Parameter wird die Kubernetes-Ressource mit Azure Arc-Unterstützung am gleichen Standort wie die Ressourcengruppe erstellt. Wenn Sie die Kubernetes-Ressource mit Azure Arc-Unterstützung an einem anderen Standort erstellen möchten, geben Sie bei Ausführung des Befehls `az connectedk8s connect` entweder `--location <region>` oder `-l <region>` an.

> [!NOTE]
> Wenn Sie mithilfe eines Dienstprinzipals bei der Azure-Befehlszeilenschnittstelle angemeldet sind, muss ein [zusätzlicher Parameter](troubleshooting.md#enable-custom-locations-using-service-principal) festgelegt werden, um das Feature für benutzerdefinierte Standorte im Cluster zu aktivieren.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest -Location eastus
```

Ausgabe:
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

## <a name="4a-connect-using-an-outbound-proxy-server"></a>4a. Herstellen einer Verbindung mit einem ausgehenden Proxyserver

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn sich Ihr Cluster hinter einem ausgehenden Proxyserver befindet, müssen die Azure-Befehlszeilenschnittstelle und die Kubernetes-Agents mit Azure Arc-Aktivierung ihre Anforderungen über den ausgehenden Proxyserver weiterleiten.

1. Legen Sie die für Azure CLI erforderlichen Umgebungsvariablen fest, um den ausgehenden Proxyserver zu verwenden:

    * Wenn Sie die Bash verwenden, führen Sie den folgenden Befehl mit entsprechenden Werten aus:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Wenn Sie PowerShell verwenden, führen Sie den folgenden Befehl mit entsprechenden Werten aus:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. Führen Sie den connect-Befehl mit den angegebenen Proxyparametern aus:

    ```azurecli
    az connectedk8s connect --name <cluster-name> --resource-group <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

    > [!NOTE]
    > * Einige Netzwerkanforderungen, z. B. diejenigen, die die Kommunikation zwischen Diensten im Cluster betreffen, müssen von dem Datenverkehr getrennt werden, der für die ausgehende Kommunikation über den Proxyserver weitergeleitet wird. Der `--proxy-skip-range`-Parameter kann verwendet werden, um den CIDR-Bereich und die Endpunkte durch Kommas getrennt anzugeben, sodass die Kommunikation zwischen den Agents und diesen Endpunkten nicht über den ausgehenden Proxy erfolgt. Mindestens der CIDR-Bereich der Dienste im Cluster sollte als Wert für diesen Parameter angegeben werden. Angenommen, `kubectl get svc -A` gibt eine Liste von Diensten zurück, wobei alle Dienste ClusterIP-Werte im Bereich `10.0.0.0/16` aufweisen. Der für `--proxy-skip-range` anzugebende Wert ist dann „10.0.0.0/16,kubernetes.default.svc“.
    > * `--proxy-http`, `--proxy-https` und `--proxy-skip-range` werden für die meisten ausgehenden Proxyumgebungen erwartet. `--proxy-cert` ist *nur* erforderlich, wenn Sie vertrauenswürdige Zertifikate, die vom Proxy erwartet werden, in den vertrauenswürdigen Zertifikatspeicher der Agent-Pods einfügen müssen.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Wenn sich Ihr Cluster hinter einem ausgehenden Proxyserver befindet, müssen Azure PowerShell und die Kubernetes-Agents mit Azure Arc-Aktivierung ihre Anforderungen über den ausgehenden Proxyserver weiterleiten.

1. Legen Sie die für Azure PowerShell erforderlichen Umgebungsvariablen fest, um den ausgehenden Proxyserver zu verwenden:

    * Führen Sie den folgenden Befehl mit entsprechenden Werten aus:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. Führen Sie den connect-Befehl mit den angegebenen Proxyparametern aus:

    ```azurepowershell
    New-AzConnectedKubernetes -ClusterName <cluster-name> -ResourceGroupName <resource-group> -Location eastus -Proxy 'https://<proxy-server-ip-address>:<port>'
    ```

---

## <a name="5-verify-cluster-connection"></a>5. Überprüfen der Clusterverbindung

Führen Sie den folgenden Befehl aus:

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az connectedk8s list --resource-group AzureArcTest --output table
```

Ausgabe:
<pre>
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzConnectedKubernetes -ResourceGroupName AzureArcTest
```

Ausgabe:
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

> [!NOTE]
> Nach dem Onboarding des Clusters dauert es ungefähr 5 bis 10 Minuten, bis die Clustermetadaten (Clusterversion, Agent-Version, Anzahl der Knoten usw.) auf der Übersichtsseite der Azure Arc-fähigen Kubernetes-Ressource im Azure-Portal angezeigt wird.

## <a name="6-view-azure-arc-agents-for-kubernetes"></a>6. Anzeigen von Azure Arc-Agents für Kubernetes

Kubernetes mit Azure Arc-Aktivierung stellt einige Operatoren im Namespace `azure-arc` bereit.

1. Zeigen Sie diese Bereitstellungen und Pods wie folgt an:

    ```console
    kubectl get deployments,pods -n azure-arc
    ```

1. Vergewissern Sie sich, dass alle Pods den Zustand `Running` aufweisen.

    Ausgabe:
    <pre>

    NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
    deployment.apps/cluster-metadata-operator     1/1             1        1      16h
    deployment.apps/clusteridentityoperator       1/1             1        1      16h
    deployment.apps/config-agent                  1/1             1        1      16h
    deployment.apps/controller-manager            1/1             1        1      16h
    deployment.apps/flux-logs-agent               1/1             1        1      16h
    deployment.apps/metrics-agent                 1/1             1        1      16h
    deployment.apps/resource-sync-agent           1/1             1        1      16h

    NAME                                           READY    STATUS   RESTART AGE
    pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
    pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
    pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
    pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
    pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
    pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
    pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
    </pre>

## <a name="7-clean-up-resources"></a>7. Bereinigen von Ressourcen

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Mit dem folgenden Befehl können Sie die Kubernetes-Ressource mit Azure Arc-Aktivierung, alle zugeordneten Konfigurationsressourcen *und* alle Agents, die auf dem Cluster ausgeführt werden, mit der Azure CLI löschen:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
> Das Löschen der Kubernetes-Ressource mit Azure Arc-Aktivierung mithilfe des Azure-Portals entfernt alle zugehörigen Konfigurationsressourcen, entfernt jedoch *keine* Agents, die auf dem Cluster ausgeführt werden. Die bewährte Methode besteht darin, die Kubernetes-Ressource mit Azure Arc-Aktivierung mithilfe von `az connectedk8s delete` anstatt über das Azure-Portal zu löschen.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Mit dem folgenden Befehl in Azure PowerShell können Sie die Kubernetes-Ressource mit Azure Arc-Aktivierung, alle zugeordneten Konfigurationsressourcen *und* alle Agents löschen, die im Cluster ausgeführt werden:

```azurepowershell
Remove-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest
```

>[!NOTE]
> Das Löschen der Kubernetes-Ressource mit Azure Arc-Aktivierung mithilfe des Azure-Portals entfernt alle zugehörigen Konfigurationsressourcen, entfernt jedoch *keine* Agents, die auf dem Cluster ausgeführt werden. Die bewährte Methode besteht darin, die Kubernetes-Ressource mit Azure Arc-Aktivierung mithilfe von `Remove-AzConnectedKubernetes` anstatt über das Azure-Portal zu löschen.

---

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, um zu erfahren, wie Sie Konfigurationen mithilfe von GitOps für Ihren verbundenen Kubernetes-Cluster bereitstellen.
> [!div class="nextstepaction"]
> [Bereitstellen von Konfigurationen mithilfe von GitOps](tutorial-use-gitops-connected-cluster.md)
