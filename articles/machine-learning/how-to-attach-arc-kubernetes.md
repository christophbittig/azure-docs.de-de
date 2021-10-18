---
title: Maschinelles Lernen mit Azure Arc-Unterstützung (Vorschauversion)
description: Konfigurieren von Kubernetes-Clustern mit Azure Arc-Unterstützung zum Trainieren von Machine Learning-Modellen in Azure Machine Learning
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: mlops
ms.date: 06/18/2021
ms.topic: how-to
ms.openlocfilehash: c3aea87e32aef24bfc17637720e81d30da0d30eb
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713271"
---
# <a name="configure-azure-arc-enabled-machine-learning-preview"></a>Konfigurieren des maschinellen Lernens mit Azure Arc-Unterstützung (Vorschauversion)

Erfahren Sie, wie Sie das maschinelle Lernen mit Azure Arc-Unterstützung für das Training konfigurieren.

## <a name="what-is-azure-arc-enabled-machine-learning"></a>Was ist maschinelles Lernen mit Azure Arc-Unterstützung?

Mit Azure Arc können Sie Azure-Dienste in jeder Kubernetes-Umgebung ausführen, egal ob lokal, in mehreren Clouds oder im Edge.

Maschinelles Lernen mit Azure Arc-Unterstützung erlaubt es Ihnen, Azure Arc-fähige Kubernetes-Cluster für das Training und die Verwaltung von Modellen in Azure Machine Learning zu konfigurieren und zu verwenden.

Maschinelles Lernen mit Azure Arc-Unterstützung unterstützt folgende Trainingsszenarien:

* Trainieren von Modellen per CLI (v2)
  * Verteiltes Training
  * Hyperparameter-Sweeping
* Trainieren von Modellen mit dem Python-SDK für Azure Machine Learning
  * Hyperparameteroptimierung
* Erstellen und Verwenden von ML-Pipelines
* Lokales Trainieren von Modellen mit ausgehenden Proxyservern
* Lokales Trainieren von Modells mit NFS-Datenspeicher

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Sollten Sie über kein Azure-Abonnement verfügen, können Sie [ein kostenloses Konto erstellen](https://azure.microsoft.com/free), bevor Sie beginnen.
* Kubernetes-Cluster mit Azure Arc-Unterstützung. Weitere Informationen finden Sie im Schnellstarthandbuch [Verbinden eines vorhandenen Kubernetes-Clusters mit Azure Arc ](../azure-arc/kubernetes/quickstart-connect-cluster.md).
* Erfüllen der [Voraussetzungen für Clustererweiterungen bei Kubernetes-Clustern mit Azure Arc-Unterstützung](../azure-arc/kubernetes/extensions.md#prerequisites)
  * Azure CLI ab Version 2.24.0
  * Azure CLI k8s-Erweiterung ab Version 0.4.3
* Ein Azure Machine Learning-Arbeitsbereich. Bevor Sie beginnen, [erstellen Sie einen Arbeitsbereich](how-to-manage-workspace.md?tabs=python), wenn Sie noch keinen haben.
  * Python-SDK für Azure Machine Learning ab Version 1.30

## <a name="deploy-azure-machine-learning-extension"></a>Bereitstellen der Azure Machine Learning-Erweiterung

Kubernetes mit Azure Arc-Unterstützung verfügt über eine Clustererweiterungsfunktion, mit der Sie verschiedene Agents installieren können, einschließlich Azure Policy-Definitionen, Überwachung, maschinelles Lernen und vieles mehr. Für Azure Machine Learning muss die *Clustererweiterung Microsoft.AzureML.Kubernetes* verwendet werden, um den Azure Machine Learning-Agent im Kubernetes-Cluster bereitzustellen. Sobald die Azure Machine Learning-Erweiterung installiert ist, können Sie den Cluster an einen Azure Machine Learning-Arbeitsbereich anfügen und für das Training verwenden.

Verwenden Sie die Azure CLI-Erweiterung `k8s-extension`, um die Azure Machine Learning-Erweiterung in Ihrem Azure Arc-fähigen Kubernetes-Cluster bereitzustellen.

1. Anmelden an Azure
    
    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

1. Bereitstellen der Azure Machine Learning-Erweiterung

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --configuration-settings enableTraining=True  --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
    ```

    >[!IMPORTANT]
    > Um einen Azure Arc-fähigen Cluster für das Training zu aktivieren, muss `enableTraining` auf **True** festgelegt werden. Durch Ausführen dieses Befehls wird eine Azure Service Bus- und Azure Relay-Ressource in derselben Ressourcengruppe wie der Arc-Cluster erstellt. Diese Ressourcen werden für die Kommunikation mit dem Cluster verwendet. Wenn Sie sie ändern, werden angefügte Cluster, die als Trainingsziele für Computeressourcen verwendet werden, nicht mehr funktionieren.

    Sie können auch die folgenden Einstellungen konfigurieren, wenn Sie die Azure Machine Learning-Erweiterung für das Modelltraining bereitstellen:

    |Schlüsselname der Konfigurationseinstellung  |BESCHREIBUNG  |
    |--|--|
    | ```enableTraining``` | Standardwert: `False`. Auf `True` festlegen, um eine Erweiterungsinstanz zum Trainieren von ML-Modellen zu erstellen.  |
    |```logAnalyticsWS```  | Standardwert: `False`. Die Azure Machine Learning-Erweiterung ist in den Azure LogAnalytics-Arbeitsbereich integriert. Auf `True` festlegen, um Protokollanzeige- und Analysefunktionen im LogAnalytics-Arbeitsbereich zu nutzen. Es können LogAnalytics-Arbeitsbereichskosten anfallen.   |
    |```installNvidiaDevicePlugin```  | Standardwert: `True`. Für das Training auf Nvidia-GPU-Hardware ist das Nvidia-Geräteplugin erforderlich. Die Azure Machine Learning-Erweiterung installiert das Nvidia-Geräte-Plug-In standardmäßig während der Azure Machine Learning-Instanzerstellung, unabhängig davon, ob der Kubernetes-Cluster über GPU-Hardware verfügt oder nicht. Auf `False` festlegen, wenn Sie keine GPU für das Training verwenden möchten oder das Nvidia-Geräteplugin bereits installiert ist.  |
    |```installBlobfuseSysctl```  | Standardmäßig `True` wenn „enableTraining=True“. Blobfuse 1.3.7 ist für das Training erforderlich. Azure Machine Learning installiert Blobfuse standardmäßig beim Erstellen der Erweiterungsinstanz. Legen Sie diese Konfigurationseinstellung auf `False` fest, wenn Blobfuse 1.37 bereits in Ihrem Kubernetes-Cluster installiert ist.   |
    |```installBlobfuseFlexvol```  | Standardmäßig `True` wenn „enableTraining=True“. Blobfuse Flexvolume ist für das Training erforderlich. Azure Machine Learning installiert Blobfuse Flexvolume standardmäßig an Ihrem Standardpfad. Legen Sie diese Konfigurationseinstellung auf `False` fest, wenn Blobfuse Flexvolume bereits in Ihrem Kubernetes-Cluster installiert ist.   |
    |```volumePluginDir```  | Hostpfad für die Installation von Blobfuse Flexvolume. Gilt nur, wenn „enableTraining=True“ gilt. Standardmäßig installiert Azure Machine Learning Blobfuse Flexvolume unter dem Standardpfad */etc/kubernetes/volumeplugins*. Mit dieser Konfigurationseinstellung geben Sie einen benutzerdefinierten Installationsspeicherort an.   |

    > [!WARNING]
    > Wenn das Nvidia-Geräteplugin, Blobfuse und Blobfuse Flexvolume bereits in Ihrem Cluster installiert sind, kann die Neuinstallation zu einem Erweiterungsinstallationsfehler führen. Legen Sie `installNvidiaDevicePlugin`, `installBlobfuseSysctl` und `installBlobfuseFlexvol` auf `False` fest, um Installationsfehler zu vermeiden.

1. Überprüfen der Bereitstellung Ihrer AzureML-Erweiterung

    ```azurecli
    az k8s-extension show --name amlarc-compute --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
    ```

    Suchen Sie in der Antwort nach `"extensionType": "amlarc-compute"` und `"installState": "Installed"`. Beachten Sie, dass möglicherweise während der ersten Minuten `"installState": "Pending"` angezeigt wird.

    Wenn `installState` **Installiert** angezeigt wird, führen Sie den folgenden Befehl auf Ihrem Computer mit einer auf Ihren Cluster verweisenden kubeconfig-Datei aus, um zu überprüfen, ob sich alle Pods unter dem Namespace *azureml* im Status *Wird ausgeführt* befinden:

   ```bash
    kubectl get pods -n azureml
   ```

## <a name="attach-arc-cluster-studio"></a>Anfügen eines Arc-Clusters (Studio)

Wenn Sie einen Kubernetes-Cluster mit Azure Arc-Unterstützung anfügen, steht er Ihrem Arbeitsbereich für das Training zur Verfügung.

1. Navigieren Sie zu [Azure Machine Learning Studio](https://ml.azure.com).
1. Wählen Sie unter **Verwalten** die Option **Compute** aus.
1. Wählen Sie den Tab **Angefügte Computeressourcen** aus.
1. Wählen Sie **+Neu > Kubernetes (Vorschauversion)** aus.

   ![Anfügen eines Kubernetes-Clusters](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. Geben Sie einen Computenamen ein, und wählen Sie Ihren Kubernetes-Cluster mit Azure Arc-Unterstützung aus der Dropdownliste aus.

   ![Konfigurieren des Kubernetes-Clusters](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster.png)

1. (Optional) Für erweiterte Szenarien wählen Sie eine Konfigurationsdatei aus und laden Sie diese hoch.

   ![Konfigurationsdatei hochladen](./media/how-to-attach-arc-kubernetes/upload-configuration-file.png)

1. Wählen Sie **Anfügen** aus.

    Auf dem Tab „Angefügte Computeressourcen“ ist der anfängliche Zustand Ihres Clusters *Wird erstellt*. Wenn der Cluster erfolgreich angefügt wurde, ändert sich der Status in *Erfolgreich*. Andernfalls ändert sich der Status in *Fehlgeschlagen*.

    ![Bereitstellen von Ressourcen](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="advanced-attach-scenario"></a>Erweitertes Anfügeszenario

Verwenden Sie eine JSON-Konfigurationsdatei, um erweiterte Computezielfunktionen auf Kubernetes-Clustern mit Azure Arc-Unterstützung zu konfigurieren.

Hier eine beispielhafte Konfigurationsdatei:

```json
{
   "namespace": "amlarc-testing",
   "defaultInstanceType": "gpu_instance",
   "instanceTypes": {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
         }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         },
         "resources": {
            "requests": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            },
            "limits": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            }
         }
      }
   }
}
```

Die folgenden Eigenschaften des benutzerdefinierten Computeziels können mithilfe einer Konfigurationsdatei konfiguriert werden:

* `namespace` ist standardmäßig der Namespace `default`. Dies ist der Namespace, unter dem Aufträge und Pods ausgeführt werden. Beachten Sie, dass Namespaces bereits vorhanden sein müssen, wenn sie anstelle des Standardnamespaces festgelegt werden sollen. Zum Erstellen von Namespaces sind Clusteradministratorrechte erforderlich.

* `defaultInstanceType` – Der Typ der Instanz, auf der Trainingsaufträge standardmäßig ausgeführt werden. Erforderlicher `defaultInstanceType`, wenn die Eigenschaft `instanceTypes` festgelegt ist. Der Wert von `defaultInstanceType` muss einer der in der Eigenschaft `instanceTypes` definierten Werte sein.

    > [!IMPORTANT]
    > Derzeit werden nur Auftragsübermittlungen unterstützt, die den Computezielnamen verwenden. Daher ist der Standardwert der Konfiguration immer defaultInstanceType.

* `instanceTypes`: Liste der Instanztypen, die für Trainingsaufträge verwendet werden Jeder Instanztyp wird durch die Eigenschaften `nodeSelector` und `resources requests/limits` definiert:

  * `nodeSelector` – Eine oder mehrere Knotenbezeichnungen, die zum Identifizieren von Knoten in einem Cluster verwendet werden. Clusteradministratorberechtigungen sind erforderlich, um Bezeichnungen für Clusterknoten zu erstellen. Wenn diese Eigenschaft festgelegt ist, wird die Ausführung von Trainingsaufträgen auf Knoten mit den angegebenen Knotenbezeichnungen geplant. Sie können `nodeSelector` verwenden, um eine Teilmenge von Knoten als Ziel für die Platzierung von Trainingsworkloads zu verwenden. Dies kann in Szenarien nützlich sein, in denen ein Cluster über unterschiedliche SKUs oder verschiedene Knotentypen wie CPU- oder GPU-Knoten verfügt. Beispielsweise können Sie Knotenbezeichnungen für alle GPU-Knoten erstellen und einen `instanceType` für den GPU-Knotenpool definieren. Trainingsaufträge werden dann ausschließlich für den GPU-Knotenpool geplant. 

  * `resources requests/limits` : Legt Ressourcenanforderungen fest und begrenzt die Ausführung eines Trainingsauftragspods. Der Standardwert ist 1 CPU und 4 GB Arbeitsspeicher.

    >[!IMPORTANT]
    > Standardmäßig wird eine Clusterressource mit 1 CPU und 4 GB Arbeitsspeicher bereitgestellt. Wenn ein Cluster mit niedrigeren Ressourcen konfiguriert ist, schlägt die Ausführung von Aufträgen fehl. Um eine erfolgreiche Ausführung von Aufträgen sicherzustellen, wird empfohlen, Ressourcenanforderungen und -grenzen immer entsprechend den Erfordernissen des Trainingsauftrags anzugeben. Hier eine beispielhafte Standardkonfigurationsdatei:
    >
    > ```json
    > {
    >    "namespace": "default",
    >    "defaultInstanceType": "defaultInstanceType",
    >    "instanceTypes": {
    >       "defaultInstanceType": {
    >          "nodeSelector": null,
    >          "resources": {
    >             "requests": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             },
    >             "limits": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             }
    >          }
    >       }
    >    }
    > }
    > ```

## <a name="attach-arc-cluster-python-sdk"></a>Anfügen eines Arc-Clusters (Python SDK)

Der folgende Python-Code zeigt, wie Sie einen Kubernetes-Cluster mit Azure Arc-Unterstützung anfügen und als Computeziel für das Training verwenden:

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
    print("creating new compute target...")

    amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id) 
    amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
    amlarc_compute.wait_for_completion(show_output=True)
    
     # For a more detailed view of current KubernetesCompute status, use get_status()
    print(amlarc_compute.get_status().serialize())
```

### <a name="advanced-attach-scenario"></a>Erweitertes Anfügeszenario

Der folgende Code zeigt, wie erweiterte Computezieleigenschaften wie Namespace, nodeSelector oder Ressourcenanforderungen und -grenzen konfiguriert werden:

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
   amlarc_compute = ws.compute_targets[amlarc_compute_name]
   if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
      print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")
   ns = "amlarc-testing"
    
   instance_types = {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
        }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         }
      }
   }

   amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns, default_instance_type="gpu_instance", instance_types = instance_types)
 
   amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
   amlarc_compute.wait_for_completion(show_output=True)
    
   # For a more detailed view of current KubernetesCompute status, use get_status()
   print(amlarc_compute.get_status().serialize())
```

## <a name="next-steps"></a>Nächste Schritte

- [Trainieren von Modellen per CLI (v2)](how-to-train-cli.md)
- [Übermitteln einer Trainingsausführung an ein Computeziel](how-to-set-up-training-targets.md)
- [Optimieren von Hyperparametern](how-to-tune-hyperparameters.md)
- [Erstellen eines Modells mithilfe von scikit-learn](how-to-train-scikit-learn.md)
- [Trainieren eines TensorFlow-Modells](how-to-train-tensorflow.md)
- [Trainieren eines PyTorch-Modells](how-to-train-pytorch.md)
- [Trainieren mithilfe von Azure Machine Learning-Pipelines](how-to-create-machine-learning-pipelines.md)
- [Lokales Trainieren von Modellen mit ausgehenden Proxyservern](../azure-arc/kubernetes/quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server)
