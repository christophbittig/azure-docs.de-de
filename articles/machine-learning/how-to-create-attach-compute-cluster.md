---
title: Erstellen von Computeclustern
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Computecluster in Ihrem Azure Machine Learning-Arbeitsbereich erstellen. Verwenden Sie den Computecluster als Computeziel für Schulungen oder Rückschlüsse.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 07/09/2021
ms.openlocfilehash: e658a8ed30b15327a68ce1671c19e55253ad6b06
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026708"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Erstellen eines Computeclusters für Azure Machine Learning

Erfahren Sie, wie Sie einen [Computecluster](concept-compute-target.md#azure-machine-learning-compute-managed) in Ihrem Azure Machine Learning-Arbeitsbereich erstellen und verwalten.

Sie können Azure Machine Learning-Computecluster verwenden, um einen Trainings- oder Batchrückschlussprozess in einem Cluster von CPU- oder GPU-Computeknoten in der Cloud zu verteilen. Weitere Informationen zu den VM-Größen mit GPUs finden Sie unter [Für GPU optimierte VM-Größen](../virtual-machines/sizes-gpu.md). 

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen eines Computeclusters
*  Senken Ihrer Computeclusterkosten
* Einrichten einer [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md) für den Cluster

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Die [Azure CLI-Erweiterung für Machine Learning Service](reference-azure-machine-learning-cli.md), das [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) oder die [Visual Studio Code-Erweiterung für Azure Machine Learning](how-to-setup-vs-code.md).

* Wenn Sie das Python SDK verwenden, [richten Sie Ihre Entwicklungsumgebung mit einem Arbeitsbereich ein](how-to-configure-environment.md).  Sobald Ihre Umgebung eingerichtet ist, fügen Sie sie an den Arbeitsbereich in Ihrem Python-Skript an:

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>Was ist ein Computecluster?

Ein Azure Machine Learning-Computecluster ist eine verwaltete Computeinfrastruktur, die Ihnen das einfache Erstellen von Computezielen mit einem oder mehreren Knoten ermöglicht. Der Computecluster ist eine Ressource, die für andere Benutzer in Ihrem Arbeitsbereich freigegeben werden kann. Es wird automatisch zentral hochskaliert, wenn ein Auftrag übermittelt wird, und kann in einem virtuellen Azure-Netzwerk platziert werden kann. Computecluster unterstützen auch **keine Bereitstellung öffentlicher IP-Adressen (Vorschau)** im virtuellen Netzwerk. Das Computeziel wird in einer Containerumgebung ausgeführt und packt die Abhängigkeiten Ihres Modells in einem [Docker-Container](https://www.docker.com/why-docker).

Computecluster können Aufträge sicher in einer [virtuellen Netzwerkumgebung](how-to-secure-training-vnet.md) ausführen, ohne dass Unternehmen hierfür SSH-Ports öffnen müssen. Der Auftrag wird in einer Containerumgebung ausgeführt und packt die Abhängigkeiten Ihres Modells in einen Docker-Container. 

## <a name="limitations"></a>Einschränkungen

* Einige der in diesem Dokument aufgeführten Szenarien sind als __Vorschau__ gekennzeichnet. Vorschaufunktionen werden ohne Vereinbarung zum Servicelevel bereitgestellt und sind nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Computecluster können in einer anderen Region als der Ihres Arbeitsbereichs erstellt werden. Diese Funktionalität befindet sich in der __Vorschau__ und ist nur für __Computecluster__ und nicht für Compute-Instanzen verfügbar. Diese Vorschau ist nicht verfügbar, wenn Sie einen Arbeitsbereich mit aktivierten privaten Endpunkten verwenden. 

    > [!WARNING]
    > Wenn Sie einen Computecluster in einer anderen Region als der Ihres Arbeitsbereichs oder Ihrer Datenspeicher nutzen, können erhöhte Netzwerklatenz und Datenübertragungskosten die Folge sein. Latenz und Kosten können beim Erstellen des Clusters und beim Anwenden von Aufträgen auf den Cluster anfallen.

* Zurzeit wird nur die Erstellung (und nicht die Aktualisierung) von Clustern mittels [ARM-Vorlagen](/azure/templates/microsoft.machinelearningservices/workspaces/computes) unterstützt. Zum Aktualisieren von Compute empfiehlt sich derzeit das SDK, die Azure CLI oder Benutzeroberfläche.

* Bei Azure Machine Learning Compute gelten Standardgrenzwerte, beispielsweise für die Anzahl von Kernen, die zugeordnet werden können. Weitere Informationen finden Sie unter [Verwalten und Anfordern von Kontingenten für Azure-Ressourcen](how-to-manage-quotas.md).

* Azure ermöglicht Ihnen das Einrichten von _Sperren_ für Ressourcen, damit diese nicht gelöscht werden können oder schreibgeschützt sind. __Wenden Sie keine Ressourcensperren auf die Ressourcengruppe an, die Ihren Arbeitsbereich enthält__. Wenn Sie eine Sperre auf die Ressourcengruppe anwenden, die Ihren Arbeitsbereich enthält, werden Skalierungsvorgänge für Azure ML-Computecluster unterbunden. Weitere Informationen zum Sperren von Ressourcen finden Sie unter [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../azure-resource-manager/management/lock-resources.md).

> [!TIP]
> Cluster können in der Regel auf bis zu 100 Knoten skaliert werden, solange Ihr Kontingent für die Anzahl der erforderlichen Kerne ausreicht. Standardmäßig werden Cluster mit aktivierter Kommunikation zwischen den Knoten des Clusters eingerichtet, um beispielsweise MPI-Aufträge zu unterstützen. Sie können Ihre Cluster jedoch auf 1.000 Knoten skalieren, indem Sie einfach [ein Supportticket einreichen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) und die Aufnahme Ihrer Anwendung, des Arbeitsbereichs oder eines bestimmten Clusters in die Zulassungsliste anfordern, um die Kommunikation zwischen den Knoten zu deaktivieren.


## <a name="create"></a>Erstellen

**Geschätzter Zeitaufwand**: Ca. fünf Minuten.

Azure Machine Learning Compute kann in mehreren Ausführungen wiederverwendet werden. Compute kann für andere Benutzer im Arbeitsbereich freigegeben werden und wird zwischen den Ausführungen beibehalten. Dabei werden Knoten basierend auf der Anzahl der übermittelten Ausführungen und der Einstellung „max_nodes“ für Ihren Cluster automatisch hoch- oder herunterskaliert. Die Einstellung „min_nodes“ steuert die Mindestanzahl verfügbarer Knoten.

Das Kontingent dedizierter Kerne pro Region pro VM-Familie und gesamte regionale Kontingent, das für die Erstellung von Computeclustern gilt, ist einheitlich und wird mit dem Kontingent für Azure Machine Learning-Trainingcompute-Instanzen gemeinsam genutzt. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Die Computeressource skaliert automatisch auf Null herunter, wenn sie nicht benötigt wird.   Dedizierte VMs werden erstellt, um Ihre Aufträge bei Bedarf auszuführen.
    
# <a name="python"></a>[Python](#tab/python)


Um eine persistente Azure Machine Learning Compute-Ressource in Python zu erstellen, geben Sie die Eigenschaften **vm_size** und **max_nodes** an. Azure Machine Learning verwendet dann für die restlichen Eigenschaften intelligente Standardwerte.
    
* **vm_size**: Die VM-Familie der von Azure Machine Learning Compute erstellten Knoten.
* **max_nodes**: Die maximale Anzahl von Knoten, auf die das Computeziel bei der Ausführung eines Auftrags in Azure Machine Learning Compute automatisch hochskaliert wird.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Sie können beim Erstellen von Azure Machine Learning Compute auch mehrere erweiterte Eigenschaften konfigurieren. Mithilfe der Eigenschaften können Sie einen persistenten Cluster mit einer festen Größe oder in einem vorhandenen virtuellen Azure-Netzwerk in Ihrem Abonnement erstellen.  Weitere Informationen finden Sie in der [AmlCompute-Klasse](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute).

> [!WARNING]
> Wenn Sie den Parameter `location` auf eine andere Region als die Ihres Arbeitsbereichs oder Ihrer Datenspeicher festlegen, können erhöhte Netzwerklatenz und Datenübertragungskosten die Folge sein. Latenz und Kosten können beim Erstellen des Clusters und beim Anwenden von Aufträgen auf den Cluster anfallen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2 --location westus2
```

> [!WARNING]
> Wenn Sie einen Computecluster in einer anderen Region als der Ihres Arbeitsbereichs oder Ihrer Datenspeicher nutzen, können erhöhte Netzwerklatenz und Datenübertragungskosten die Folge sein. Latenz und Kosten können beim Erstellen des Clusters und beim Anwenden von Aufträgen auf den Cluster anfallen.

Weitere Informationen finden Sie unter [az ml computetarget create amlcompute](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_amlcompute).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Weitere Informationen zum Erstellen eines Computeclusters im Studio finden Sie unter [Erstellen von Computezielen in Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Senken Ihrer Computeclusterkosten

Sie können sich auch dafür entscheiden, [virtuelle Computer mit niedriger Priorität](how-to-manage-optimize-cost.md#low-pri-vm) zu verwenden, um einige oder alle Ihre Workloads auszuführen. Diese virtuellen Computer haben keine garantierte Verfügbarkeit und können während der Verwendung vorzeitig entfernt werden. Sie müssen einen vorzeitig entfernten Auftrag neu starten. 

Verwenden Sie eine der folgenden Methoden, um einen virtuellen Computer mit niedriger Priorität anzugeben:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Legen Sie die `vm-priority` fest:
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Wählen Sie in Studio beim Erstellen einer VM **Niedrige Priorität** aus.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Einrichten einer verwalteten Identität

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Konfigurieren der verwalteten Identität in der Bereitstellungskonfiguration:  

    * Systemseitig zugewiesene verwaltete Identität, die in einem Arbeitsbereich mit dem Namen `ws` erstellt wurde
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * Benutzerseitig zugewiesene verwaltete Identität, die in einem Arbeitsbereich mit dem Namen `ws` erstellt wurde
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Hinzufügen einer verwalteten Identität zu einem vorhandenen Computecluster namens `cpu_cluster`
    
    * Systemseitig zugewiesene verwaltete Identität:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Benutzerseitig zugewiesene verwaltete Identität:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

* Erstellen eines neuen verwalteten Computeclusters mit einer verwalteten Identität

  * Benutzerseitig zugewiesene verwaltete Identität

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Systemseitig zugewiesene verwaltete Identität

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Hinzufügen einer verwalteten Identität zu einem vorhandenen Cluster:

    * Benutzerseitig zugewiesene verwaltete Identität
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Systemseitig zugewiesene verwaltete Identität

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Weitere Informationen finden Sie unter [Einrichten einer verwalteten Identität](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Nutzung von verwalteten Identitäten

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Problembehandlung

Es kann vorkommen, dass Benutzer, die vor dem allgemein verfügbaren Release ihren Azure Machine Learning-Arbeitsbereich über das Azure-Portal erstellt haben, AmlCompute nicht in diesem Arbeitsbereich erstellen können. In einem solchen Fall können Sie entweder eine Supportanfrage für den Dienst erstellen oder über das Portal oder das SDK einen neuen Arbeitsbereich erstellen und die Blockierung dadurch umgehend aufheben.

Wenn Ihr Azure Machine Learning-Computecluster bei der Größenänderung bei (0 > 0) für den Knotenstatus stehenzubleiben scheint, wird dies möglicherweise von Azure-Ressourcensperren verursacht.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie Ihren Computecluster für Folgendes:

* [Übermitteln einer Trainingsausführung an ein Computeziel](how-to-set-up-training-targets.md) 
* [Ausführen von Batchrückschlüssen für große Datenmengen mithilfe von Azure Machine Learning](./tutorial-pipeline-batch-scoring-classification.md).
