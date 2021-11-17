---
title: Maschinelles Lernen mit Azure Arc-Unterstützung (Vorschauversion)
description: Konfigurieren von Azure Kubernetes Service und Kubernetes-Clustern mit Azure Arc-Unterstützung zum Trainieren und zum Rückschluss von Machine Learning-Modellen in Azure Machine Learning
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 635153d510b18bc0ce97033094abf21a3b6d3d74
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491626"
---
# <a name="configure-kubernetes-clusters-for-machine-learning-preview"></a>Konfigurieren von Kubernetes-Clustern für maschinelles Lernen (Vorschau)

Erfahren Sie, wie Sie Azure Kubernetes Service (AKS) und Kubernetes-Cluster mit Azure Arc-Unterstützung für das Trainieren und Rückschließen von Workloads für maschinelles Lernen konfigurieren.

## <a name="what-is-azure-arc-enabled-machine-learning"></a>Was ist maschinelles Lernen mit Azure Arc-Unterstützung?

Mit Azure Arc können Sie Azure-Dienste in jeder Kubernetes-Umgebung ausführen, egal ob lokal, in mehreren Clouds oder im Edge.

Maschinelles Lernen mit Azure Arc-Unterstützung erlaubt Ihnen, Azure Kubernetes Service und Kubernetes-Cluster mit Azure Arc-Unterstützung für das Training, den Rückschluss und die Verwaltung von Machine Learning-Modellen in Azure Machine Learning zu konfigurieren und zu verwenden.

## <a name="machine-learning-on-azure-kubernetes-service"></a>Maschinelles Lernen in Azure Kubernetes Service

Um Azure Kubernetes Service-Cluster für Azure Machine Learning-Workloads für Training und Rückschluss zu verwenden, müssen Sie diese nicht mit Azure Arc verbinden.

Sie müssen ein- und ausgehenden Netzwerkdatenverkehr konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren des ein- und ausgehenden Netzwerkdatenverkehrs (AKS)](how-to-access-azureml-behind-firewall.md#azure-kubernetes-services-1).

Informationen zum Bereitstellen der Azure Machine Learning-Erweiterung in Azure Kubernetes Service-Clustern finden Sie im Abschnitt [Bereitstellen der Azure Machine Learning-Erweiterung](#deploy-azure-machine-learning-extension).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Sollten Sie über kein Azure-Abonnement verfügen, können Sie [ein kostenloses Konto erstellen](https://azure.microsoft.com/free), bevor Sie beginnen.
* Kubernetes-Cluster mit Azure Arc-Unterstützung. Weitere Informationen finden Sie im Schnellstarthandbuch [Verbinden eines vorhandenen Kubernetes-Clusters mit Azure Arc ](../azure-arc/kubernetes/quickstart-connect-cluster.md).

    > [!NOTE]
    > Für AKS-Cluster ist das Verbinden mit Azure Arc **optional**.

* Erfüllen der [Azure Arc Netzwerkanforderungen](/azure/azure-arc/kubernetes/quickstart-connect-cluster?tabs=azure-cli#meet-network-requirements)

    > [!IMPORTANT]
    > Cluster, die hinter einem ausgehenden Proxyserver oder einer Firewall ausgeführt werden, benötigen zusätzliche Netzwerkkonfigurationen.
    >
    > Weitere Informationen finden Sie unter [Konfigurieren des ein- und ausgehenden Netzwerkdatenverkehrs (Kubernetes mit Azure Arc-Unterstützung)](how-to-access-azureml-behind-firewall.md#arc-kubernetes).

* Erfüllen der [Voraussetzungen für Clustererweiterungen bei Kubernetes-Clustern mit Azure Arc-Unterstützung](../azure-arc/kubernetes/extensions.md#prerequisites)
  * Azure CLI ab Version 2.24.0
  * Azure CLI k8s-Erweiterung ab Version 1.0.0

* Ein Azure Machine Learning-Arbeitsbereich. Bevor Sie beginnen, [erstellen Sie einen Arbeitsbereich](how-to-manage-workspace.md?tabs=python), wenn Sie noch keinen haben.
  * Python-SDK für Azure Machine Learning ab Version 1.30
* Anmelden bei Azure über die Azure CLI

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```  

* **Nur Azure RedHat OpenShift Service (ARO) und OpenShift Container Platform (OCP)**

    * Ein Kubernetes-Cluster des Typs ARO oder OCP wird ausgeführt. Weitere Informationen finden Sie unter [Erstellen eines ARO-Kubernetes-Clusters](/azure/openshift/tutorial-create-cluster) und [Erstellen eines OCP-Kubernetes-Clusters](https://docs.openshift.com/container-platform/4.6/installing/installing_platform_agnostic/installing-platform-agnostic.html).
    * Gewähren Sie privilegierten Zugriff auf AzureML-Dienstkonten.

        Führen Sie `oc edit scc privileged` aus, und fügen Sie Folgendes hinzu: 

        * ```system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa```
        * ```system:serviceaccount:azureml:{EXTENSION NAME}-kube-state-metrics``` **(Hinweis:** ```{EXTENSION NAME}``` **muss mit dem Erweiterungsnamen übereinstimmen, der im Schritt** ```az k8s-extension create --name```  **verwendet wird.)**
        * ```system:serviceaccount:azureml:cluster-status-reporter```
        * ```system:serviceaccount:azureml:prom-admission```
        * ```system:serviceaccount:azureml:default```
        * ```system:serviceaccount:azureml:prom-operator```
        * ```system:serviceaccount:azureml:csi-blob-node-sa```
        * ```system:serviceaccount:azureml:csi-blob-controller-sa```
        * ```system:serviceaccount:azureml:load-amlarc-selinux-policy-sa```
        * ```system:serviceaccount:azureml:azureml-fe```
        * ```system:serviceaccount:azureml:prom-prometheus```

## <a name="deploy-azure-machine-learning-extension"></a>Bereitstellen der Azure Machine Learning-Erweiterung

Kubernetes mit Azure Arc-Unterstützung verfügt über eine Clustererweiterungsfunktion, mit der Sie verschiedene Agents installieren können, einschließlich Azure Policy-Definitionen, Überwachung, maschinelles Lernen und vieles mehr. Für Azure Machine Learning muss die *Clustererweiterung Microsoft.AzureML.Kubernetes* verwendet werden, um den Azure Machine Learning-Agent im Kubernetes-Cluster bereitzustellen. Sobald die Azure Machine Learning-Erweiterung installiert ist, können Sie den Cluster an einen Azure Machine Learning-Arbeitsbereich anfügen und für die folgenden Szenarios verwenden:

* [Training](#training)
* [Nur Echtzeitrückschluss](#inferencing)
* [Training und Rückschluss](#training-inferencing)

> [!TIP]
> Durch das ausschließliche Trainieren von Clustern wird auch der Batchrückschluss als Teil der Azure Machine Learning-Pipelines unterstützt.

Verwenden Sie die den Befehl [`create`](/cli/azure/k8s-extension?view=azure-cli-latest&preserve-view=true) der Azure CLI-Erweiterung `k8s-extension`, um die Azure Machine Learning-Erweiterung in Ihrem Azure Arc-fähigen Kubernetes-Cluster bereitzustellen.

> [!IMPORTANT]
> Legen Sie den `--cluster-type`-Parameter auf `managedClusters` fest, um die Azure Machine Learning-Erweiterung in AKS-Clustern bereitzustellen.

Im Folgenden finden Sie eine Liste der Konfigurationseinstellungen, die für verschiedene Bereitstellungsszenarios von Azure Machine Learning-Erweiterungen verwendet werden können.

Sie können ```--config``` oder ```--config-protected``` verwenden, um eine Liste von Schlüssel-Wert-Paaren für Azure Machine Learning anzugeben.

> [!TIP]
> Legen Sie den Parameter `openshift` auf `True` fest, um die Azure Machine Learning-Erweiterung für ARO- und OCP-Kubernetes-Cluster bereitzustellen.

| Schlüsselname der Konfigurationseinstellung  | BESCHREIBUNG  | Training | Rückschluss | Training und Rückschluss |
|---|---|---|---|---|
| ```enableTraining``` | Standardwert: `False`. Auf `True` festlegen, um eine Erweiterungsinstanz zum Trainieren von ML-Modellen zu erstellen. |  **&check;** | – |  **&check;** |
|```logAnalyticsWS```  | Standardwert: `False`. Die Azure Machine Learning-Erweiterung ist in den Azure LogAnalytics-Arbeitsbereich integriert. Auf `True` festlegen, um Protokollanzeige- und Analysefunktionen im LogAnalytics-Arbeitsbereich zu nutzen. Es können LogAnalytics-Arbeitsbereichskosten anfallen. | Optional | Optional | Optional |
|```installNvidiaDevicePlugin```  | Standardwert: `True`. Das NVIDIA-Geräte-Plug-In ist zum Trainieren und Rückschließen auf NVIDIA-GPU-Hardware erforderlich. Die Azure Machine Learning-Erweiterung installiert das Nvidia-Geräte-Plug-In standardmäßig während der Azure Machine Learning-Instanzerstellung, unabhängig davon, ob der Kubernetes-Cluster über GPU-Hardware verfügt oder nicht. Wird auf `False` festgelegt, wenn Sie keine GPU verwenden möchten oder das NVIDIA-Geräte-Plug-In bereits installiert ist.  | Optional |Optional | Optional |
| ```enableInference``` | Standardwert: `False`.  Wird auf `True` festgelegt, um eine Erweiterungsinstanz zum Rückschließen von ML-Modellen zu erstellen. | – | **&check;** |  **&check;** |
| ```allowInsecureConnections``` | Standardwert: `False`. Wird für die Bereitstellung der Azure Machine Learning-Erweiterung mit HTTP-Endpunktunterstützungn für den Rückschluss auf `True` festgelegt, wobei ```sslCertPemFile``` und ```sslKeyPemFile``` nicht bereitgestellt werden. | – | Optional |  Optional |
| ```sslCertPemFile``` & ```ssKeyPMFile``` | Pfad zu SSL-Zertifikat und -Schlüsseldatei (PEM-codiert). Erforderlich für die Bereitstellung der AzureML-Erweiterung mit HTTPS-Endpunktunterstützung für Rückschlüsse. | – | Optional |  Optional |
| ```privateEndpointNodeport``` | Standardwert: `False`.  Wird für die Bereitstellung der Azure Machine Learning-Erweiterung mit Unterstützung für den privaten Endpunkt für den ML-Rückschluss über NodePort auf `True` festgelegt. | – | Optional |  Optional |
| ```privateEndpointILB``` | Standardwert: `False`.  Wird für die Bereitstellung der Azure Machine Learning-Erweiterung mit Unterstützung für den privaten Endpunkt für den ML-Rückschluss über internen ServiceType-Lastenausgleich auf `True` festgelegt. | –| Optional |  Optional |
| ```inferenceLoadBalancerHA``` | Standardwert: `True`. Standardmäßig stellt die Azure Machine Learning-Erweiterung mehrere Replikate der Eingangsdatencontroller für Hochverfügbarkeit zur Verfügung. Wird auf `False` festgelegt, wenn Sie über eingeschränkte Clusterressourcen verfügen oder eine Azure Machine Learning-Erweiterung nur zu Entwicklungs- und Testzwecken bereitstellen möchten. Wenn Sie keinen Hochverfügbarkeitslastenausgleich verwenden, wird nur ein Replikat des Eingangsdatencontrollers bereitgestellt. | – | Optional |  Optional |
|```openshift``` | Standardwert: `False`. Wird auf `True` für die Bereitstellung der Azure Machine Learning-Erweiterung auf einem ARO- oder OCP-Cluster festgelegt. Der Bereitstellungsprozess kompiliert automatisch ein Richtlinienpaket und lädt dieses auf jeden Knoten, damit der Azure Machine Learning-Dienstvorgang ordnungsgemäß funktioniert. | Optional | Optional |  Optional |

> [!WARNING]
> Wenn das NVIDIA-Geräte-Plug-In bereits in Ihrem Cluster installiert sind, kann die Neuinstallation zu einem Erweiterungsinstallationsfehler führen. Legen Sie `installNvidiaDevicePlugin` auf `False` fest, um Bereitstellungsfehler zu verhindern.

### <a name="deploy-extension-for-training-workloads"></a>Bereitstellen der Erweiterung für Trainingsworkloads <a id="training"></a>

Verwenden Sie den folgenden Azure CLI-Befehl, um die Azure Machine Learning-Erweiterung bereitzustellen und Trainingsworkloads in Ihrem Kubernetes-Cluster zu aktivieren:

```azurecli
az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --config enableTraining=True --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
```

### <a name="deploy-extension-for-real-time-inferencing-workloads"></a>Bereitstellen der Erweiterung für Echtzeitrückschlussworkloads <a id="inferencing"></a>

Wählen Sie je nach Netzwerkeinrichtung, Kubernetes-Verteilungsvariante und dem Ort, an dem Ihr Kubernetes-Cluster gehostet wird (lokal oder in der Cloud), eine der folgenden Optionen aus, um die Azure Machine Learning-Erweiterung bereitzustellen und Rückschlussworkloads in Ihrem Kubernetes-Cluster zu aktivieren.

#### <a name="public-endpoints-support-with-public-load-balancer"></a>Unterstützung öffentlicher Endpunkte mit öffentlichem Lastenausgleich

* **HTTPS**

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file> --resource-group <resource-group> --scope cluster
    ```

* **HTTP**

    > [!WARNING]
    > Die Unterstützung öffentlicher HTTP-Endpunkte mit öffentlichem Lastenausgleich ist die am wenigsten sichere Methode zum Bereitstellen der Azure Machine Learning-Erweiterung für Echtzeitrückschlussszenarios und wird daher **NICHT** empfohlen.

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name>  --configuration-settings enableInference=True allowInsecureConnections=True --resource-group <resource-group> --scope cluster
    ```

#### <a name="private-endpoints-support-with-internal-load-balancer"></a>Unterstützung privater Endpunkte mit internem Lastenausgleich

* **HTTPS**

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointILB=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file> --resource-group <resource-group> --scope cluster
    ```

* **HTTP**

   ```azurecli
   az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointILB=True allowInsecureConnections=True --resource-group <resource-group> --scope cluster
   ```

#### <a name="endpoints-support-with-nodeport"></a>Endpunktunterstützung mit NodePort

Die Verwendung eines NodePort-Diensts bietet Ihnen die Möglichkeit, eine eigene Lastenausgleichslösung einzurichten, Umgebungen zu konfigurieren, die nicht vollständig von Kubernetes unterstützt werden, oder sogar um die IP-Adressen eines oder mehrerer Knoten direkt verfügbar zu machen.

Wenn Sie die Bereitstellung mit dem NodePort-Dienst durchführen, wird die Bewertungs-URL (oder Swagger-URL) durch eine der Knoten-IP-Adressen (z. B. ```http://<NodeIP><NodePort>/<scoring_path>```) ersetzt und bleibt auch dann unverändert, wenn der Knoten nicht verfügbar ist. Sie können sie jedoch durch eine beliebige andere Knoten-IP-Adresse ersetzen.

* **HTTPS**

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster --config enableInference=True privateEndpointNodeport=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file>
    ```

* **HTTP**

   ```azurecli
   az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointNodeport=True allowInsecureConnections=Ture --resource-group <resource-group> --scope cluster
   ```

### <a name="deploy-extension-for-training-and-inferencing-workloads"></a>Bereitstellen der Erweiterung für Trainings- und Rückschlussworkloads <a id="training-inferencing"></a>

Verwenden Sie den folgenden Azure CLI-Befehl, um die Azure Machine Learning-Erweiterung bereitzustellen und den Echtzeitrückschluss für Cluster, den Batchrückschluss und Trainingsworkloads in Ihrem Kubernetes-Cluster zu aktivieren.

```azurecli
az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableTraining=True enableInference=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file>--resource-group <resource-group> --scope cluster
```

## <a name="resources-created-during-deployment"></a>Während der Bereitstellung erstellte Ressourcen

Sobald die Azure Machine Learning-Erweiterung bereitgestellt wurde, werden die folgenden Ressourcen in Azure und Ihrem Kubernetes-Cluster erstellt, abhängig von den Workloads, die Sie in Ihrem Cluster ausführen.

|Ressourcenname  |Ressourcentyp |Training |Rückschluss |Training und Rückschluss|
|---|---|---|---|---|
|Azure ServiceBus|Azure-Ressource|**&check;**|**&check;**|**&check;**|
|Azure Relay|Azure-Ressource|**&check;**|**&check;**|**&check;**|
|{EXTENSION-NAME}|Azure-Ressource|**&check;**|**&check;**|**&check;**|
|aml-operator|Kubernetes-Bereitstellung|**&check;**|–|**&check;**|
|{EXTENSION-NAME}-kube-state-metrics|Kubernetes-Bereitstellung|**&check;**|**&check;**|**&check;**|
|{EXTENSION-NAME}-prometheus-operator|Kubernetes-Bereitstellung|**&check;**|**&check;**|**&check;**|
|amlarc-identity-controller|Kubernetes-Bereitstellung|–|**&check;**|**&check;**|
|amlarc-identity-proxy|Kubernetes-Bereitstellung|–|**&check;**|**&check;**|
|azureml-fe|Kubernetes-Bereitstellung|–|**&check;**|**&check;**|
|inference-operator-controller-manager|Kubernetes-Bereitstellung|–|**&check;**|**&check;**|
|metrics-controller-manager|Kubernetes-Bereitstellung|**&check;**|**&check;**|**&check;**|
|relayserver|Kubernetes-Bereitstellung|**&check;**|**&check;**|**&check;**|
|cluster-status-reporter|Kubernetes-Bereitstellung|**&check;**|**&check;**|**&check;**|
|nfd-master|Kubernetes-Bereitstellung|**&check;**|–|**&check;**|
|gateway|Kubernetes-Bereitstellung|**&check;**|**&check;**|**&check;**|
|csi-blob-controller|Kubernetes-Bereitstellung|**&check;**|–|**&check;**|
|csi-blob-node|Kubernetes-Daemonset|**&check;**|–|**&check;**|
|fluent-bit|Kubernetes-Daemonset|**&check;**|**&check;**|**&check;**|
|k8s-host-device-plugin-daemonset|Kubernetes-Daemonset|**&check;**|**&check;**|**&check;**|
|nfd-worker|Kubernetes-Daemonset|**&check;**|–|**&check;**|
|prometheus-prom-prometheus|Kubernetes-StatefulSet|**&check;**|**&check;**|**&check;**|
|frameworkcontroller|Kubernetes-StatefulSet|**&check;**|–|**&check;**|

> [!IMPORTANT]
> Azure ServiceBus- und Azure Relay-Ressourcen befinden sich in derselben Ressourcengruppe wie die Arc-Clusterressource. Diese Ressourcen werden für die Kommunikation mit dem Kubernetes-Cluster verwendet, und durch deren Änderung werden die angefügten Computeziele zerstört.

> [!NOTE]
> **{EXTENSION-NAME}** ist der vom Azure CLI-Befehl ```az k8s-extension create --name``` angegebene Erweiterungsname.

## <a name="verify-your-azureml-extension-deployment"></a>Überprüfen der Bereitstellung Ihrer AzureML-Erweiterung

```azurecli
az k8s-extension show --name arcml-extension --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
```

Suchen Sie in der Antwort nach `"extensionType": "arcml-extension"` und `"installState": "Installed"`. Beachten Sie, dass möglicherweise während der ersten Minuten `"installState": "Pending"` angezeigt wird.

Wenn `installState` **Installiert** angezeigt wird, führen Sie den folgenden Befehl auf Ihrem Computer mit einer auf Ihren Cluster verweisenden kubeconfig-Datei aus, um zu überprüfen, ob sich alle Pods unter dem Namespace *azureml* im Status *Wird ausgeführt* befinden:

```bash
kubectl get pods -n azureml
```

## <a name="attach-arc-cluster"></a>Anfügen eines Arc-Clusters

### <a name="studio"></a>[Studio](#tab/studio)

Wenn Sie einen Kubernetes-Cluster mit Azure Arc-Unterstützung anfügen, steht er Ihrem Arbeitsbereich für das Training zur Verfügung.

1. Navigieren Sie zu [Azure Machine Learning Studio](https://ml.azure.com).
1. Wählen Sie unter **Verwalten** die Option **Compute** aus.
1. Wählen Sie den Tab **Angefügte Computeressourcen** aus.
1. Wählen Sie **+Neu > Kubernetes (Vorschauversion)** aus.

   ![Anfügen eines Kubernetes-Clusters](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. Geben Sie einen Computenamen ein, und wählen Sie Ihren Kubernetes-Cluster mit Azure Arc-Unterstützung aus der Dropdownliste aus.

   **(Optional)** Weisen Sie systemseitig oder benutzerseitig zugewiesene verwaltete Identitäten zu. Dank verwalteter Identitäten müssen Entwickler keine Anmeldeinformationen mehr verwalten. Weitere Informationen finden Sie unter [Übersicht über verwaltete Identitäten](/azure/active-directory/managed-identities-azure-resources/overview).

   ![Konfigurieren des Kubernetes-Clusters](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster-2.png)

1. Wählen Sie **Anfügen** aus.

    Auf dem Tab „Angefügte Computeressourcen“ ist der anfängliche Zustand Ihres Clusters *Wird erstellt*. Wenn der Cluster erfolgreich angefügt wurde, ändert sich der Status in *Erfolgreich*. Andernfalls ändert sich der Status in *Fehlgeschlagen*.

    ![Bereitstellen von Ressourcen](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="python-sdk"></a>[Python SDK](#tab/sdk)

Sie können das Python SDK von Azure Machine Learning verwenden, um Azure Arc-fähige Kubernetes-Cluster mithilfe der [`attach_configuration`](/python/api/azureml-core/azureml.core.compute.kubernetescompute.kubernetescompute?view=azure-ml-py&preserve-view=true)-Methode als Computeziele anzufügen.

Der folgende Python-Code zeigt, wie Sie einen Azure Arc-fähigen Kubernetes-Cluster anfügen und als Computeziel mit aktivierter verwalteter Identität verwenden.

Dank verwalteter Identitäten müssen Entwickler keine Anmeldeinformationen mehr verwalten. Weitere Informationen finden Sie unter [Übersicht über verwaltete Identitäten](/azure/active-directory/managed-identities-azure-resources/overview).

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
from azureml.core.workspace import Workspace
import os

ws = Workspace.from_config()

# Specify a name for your Kubernetes compute
amlarc_compute_name = "<COMPUTE_CLUSTER_NAME>"

# resource ID for the Kubernetes cluster and user-managed identity
resource_id = "/subscriptions/<sub ID>/resourceGroups/<RG>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>"

user_assigned_identity_resouce_id = ['subscriptions/<sub ID>/resourceGroups/<RG>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity name>']

ns = "default" 

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")


# assign user-assigned managed identity
amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns,  identity_type ='UserAssigned',identity_ids = user_assigned_identity_resouce_id) 

# assign system-assigned managed identity
# amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns,  identity_type ='SystemAssigned') 

amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)
amlarc_compute.wait_for_completion(show_output=True)

# get detailed compute description containing managed identity principle ID, used for permission access. 
print(amlarc_compute.get_status().serialize())
```

Verwenden Sie den `identity_type`-Parameter, um verwaltete `SystemAssigned`- oder `UserAssigned`-Identitäten zu aktivieren.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Sie können einen AKS- oder Azure Arc-fähigen Kubernetes-Cluster mithilfe der CLI von Azure Machine Learning 2.0 (Vorschau) anfügen.

Verwenden Sie den Befehl [`attach`](/cli/azure/ml/compute?view=azure-cli-latest&preserve-view=true) der Azure Machine Learning-CLI, und legen Sie das Argument `--type` auf `kubernetes` fest, um Ihren Kubernetes-Cluster mithilfe der CLI von Azure Machine Learning 2.0 anzufügen.

> [!NOTE]
> Die Computeanfügungsunterstützung für AKS- oder Azure Arc-fähige Kubernetes-Cluster erfordert eine Version der `ml`-Erweiterung der Azure CLI, die höher oder gleich 2.0.1a4 ist. Weitere Informationen finden Sie unter [Installieren und Einrichten der CLI (v2)](how-to-configure-cli.md).

Die folgenden Befehle zeigen, wie Sie einen Azure Arc-fähigen Kubernetes-Cluster anfügen und als Computeziel mit aktivierter verwalteter Identität verwenden.

**AKS**

```azurecli
az ml compute attach --resource-group <resource-group-name> --workspace-name <workspace-name> --name amlarc-compute --resource-id "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Kubernetes/managedclusters/<cluster-name>" --type kubernetes --identity-type UserAssigned --user-assigned-identities "subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>" --no-wait
```

**Kubernetes mit Azure Arc-Aktivierung**

```azurecli
az ml compute attach --resource-group <resource-group-name> --workspace-name <workspace-name> --name amlarc-compute --resource-id "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster-name>" --type kubernetes --user-assigned-identities "subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>" --no-wait
```

Verwenden Sie das `identity_type`-Argument, um verwaltete `SystemAssigned`- oder `UserAssigned`-Identitäten zu aktivieren.

> [!IMPORTANT]
> `--user-assigned-identities` ist nur für verwaltete `UserAssigned`-Identitäten erforderlich. Sie können zwar eine Liste mit durch Kommas getrennten und vom Benutzer verwalteten Identitäten bereitstellen, es wird jedoch nur die erste Identität verwendet, wenn Sie Ihren Cluster anfügen.

---

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Auswählen verschiedener Instanztypen für Trainings- und Rückschlussworkloads](how-to-kubernetes-instance-type.md)
- [Trainieren von Modellen per CLI (v2)](how-to-train-cli.md)
- [Übermitteln einer Trainingsausführung an ein Computeziel](how-to-set-up-training-targets.md)
- [Optimieren von Hyperparametern](how-to-tune-hyperparameters.md)
- [Erstellen eines Modells mithilfe von scikit-learn](how-to-train-scikit-learn.md)
- [Trainieren eines TensorFlow-Modells](how-to-train-tensorflow.md)
- [Trainieren eines PyTorch-Modells](how-to-train-pytorch.md)
- [Trainieren mithilfe von Azure Machine Learning-Pipelines](how-to-create-machine-learning-pipelines.md)
- [Lokales Trainieren von Modellen mit ausgehenden Proxyservern](../azure-arc/kubernetes/quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server)
