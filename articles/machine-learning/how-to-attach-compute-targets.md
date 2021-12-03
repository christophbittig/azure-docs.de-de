---
title: Einrichten von Computezielen für das Training und für Rückschlüsse
titleSuffix: Azure Machine Learning
description: Fügen Sie Computeressourcen (Computeziele) Ihrem Arbeitsbereich hinzu, um damit Machine Learning-Modelle zu trainieren und Rückschlüsse zu ziehen.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1, ignite-fall-2021
ms.openlocfilehash: 3c3d33fc783c3e499ab8fcb3794c929cf1359dcb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553973"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Einrichten von Computezielen für das Training und die Bereitstellung von Modellen

Hier erfahren Sie, wie Sie Azure-Computeressourcen an Ihren Azure Machine Learning-Arbeitsbereich anfügen.  Anschließend können Sie diese Ressourcen als [Computeziele](concept-compute-target.md) für Rückschlüsse und das Training in Ihren Machine Learning-Tasks verwenden.

In diesem Artikel erfahren Sie, wie Sie Ihren Arbeitsbereich für die Verwendung dieser Computeressourcen einrichten:

* Ihr lokaler Computer
* Virtuelle Remotecomputer
* Apache Spark-Pools (powered by Azure Synapse Analytics)
* Azure HDInsight
* Azure Batch
* Azure Databricks: wird nur in [Machine Learning-Pipelines](how-to-create-machine-learning-pipelines.md) als Computeziel beim Training verwendet.
* Azure Data Lake Analytics
* Azure Container Instances
* Azure Kubernetes Service und Kubernetes mit Azure Arc-Unterstützung (Vorschauversion)

Informationen zur Verwendung von Computezielen, die von Azure Machine Learning verwaltet werden, finden Sie unter den folgenden Links:

* [Azure Machine Learning-Computeinstanz](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning Compute-Cluster](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes Service-Cluster](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Die [Azure CLI-Erweiterung für Machine Learning Service](reference-azure-machine-learning-cli.md), das [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) oder die [Visual Studio Code-Erweiterung für Azure Machine Learning](how-to-setup-vs-code.md).

## <a name="limitations"></a>Einschränkungen

* **Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für die gleichen Compute-Ressourcen** in Ihrem Arbeitsbereich. Verknüpfen Sie beispielsweise einen Azure Kubernetes Service-Clusters nicht unter zwei verschiedenen Namen mit einem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

    Falls Sie ein Computeziel erneut verknüpfen möchten (etwa zum Ändern der TLS-Einstellung oder einer anderen Clusterkonfigurationseinstellung), müssen Sie zunächst die vorhandene Verknüpfung entfernen.

## <a name="whats-a-compute-target"></a>Was ist ein Computeziel?

Mit Azure Machine Learning können Sie Ihr Modell auf verschiedenen Ressourcen oder Umgebungen trainieren, die zusammenfassend als [__Computeziele__](concept-azure-machine-learning-architecture.md#compute-targets) bezeichnet werden. Ein Computeziel kann ein lokaler Computer oder eine Cloudressource sein, wie beispielsweise Azure Machine Learning Compute, Azure HDInsight oder ein virtueller Remotecomputer.  Ebenso können Sie Computeziele für die Modellimplementierung erstellen. Dies wird unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md) beschrieben.


## <a name="local-computer"></a><a id="local"></a>Lokaler Computer

Wenn Sie Ihren lokalen Computer für das **Training** verwenden, ist es nicht erforderlich, ein Computeziel zu erstellen.  [Übermitteln Sie einfach die Trainingsausführung](how-to-set-up-training-targets.md) von Ihrem lokalen Computer aus.

Wenn Sie Ihren lokalen Computer für **Rückschlüsse** verwenden, muss Docker installiert sein. Zum Ausführen der Bereitstellung definieren Sie den vom Webdienst verwendeten Port in [LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#deploy-configuration-port-none-). Folgen Sie dann dem normalen Bereitstellungsprozess, wie in [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md) beschrieben.

## <a name="remote-virtual-machines"></a><a id="vm"></a>Virtuelle Remotecomputer

Azure Machine Learning unterstützt auch das Anfügen einer Azure-VM. Die VM muss eine Azure-DSVM (Data Science Virtual Machine) sein. Die VM bietet eine zusammengestellte Auswahl an Tools und Frameworks für die Entwicklung des maschinellen Lernens über den gesamten Lebenszyklus. Weitere Informationen zum Verwenden der DSVM mit Azure Machine Learning finden Sie unter [Konfigurieren einer Entwicklungsumgebung](./how-to-configure-environment.md#dsvm).

> [!TIP]
> Anstelle eines Remote VMs, empfiehlt es sich, die [Azure Machine Learning Compute-Instanz](concept-compute-instance.md)zu verwenden. Dabei handelt es sich um eine vollständig verwaltete, cloudbasierte Compute-Lösung, die für Azure Machine Learning spezifisch ist. Weitere Informationen hierzu finden Sie unter [Erstellen und Verwalten einer Azure Machine Learning-Compute-Instanz](how-to-create-manage-compute-instance.md).

1. **Erstellen**: Azure Machine Learning kann keine Remote-VM für Sie erstellen. Stattdessen müssen Sie die VM erstellen und sie dann Ihrem Arbeitsbereich hinzufügen. Informationen zum Erstellen einer DSVM finden Sie in [Bereitstellen der Data Science Virtual Machine für Linux (Ubuntu)](./data-science-virtual-machine/dsvm-ubuntu-intro.md).

    > [!WARNING]
    > Von Azure Machine Learning werden nur virtuelle Computer unterstützt, auf denen **Ubuntu** ausgeführt wird. Wenn Sie eine VM erstellen oder eine vorhandene VM auswählen, müssen Sie eine VM auswählen, die Ubuntu verwendet.
    > 
    > Von Azure Machine Learning wird außerdem vorausgesetzt, dass der virtuelle Computer über eine __öffentliche IP-Adresse__ verfügt.

1. **Anfügen**: Wenn Sie einen vorhandenen virtuellen Computer als Computeziel anfügen möchten, müssen Sie die Ressourcen-ID, den Benutzernamen und das Kennwort für den virtuellen Computer angeben. Die Ressourcen-ID des virtuellen Computers kann unter Verwendung der Abonnement-ID, des Ressourcengruppennamens und des Namens des virtuellen Computers im folgenden Zeichenfolgenformat erstellt werden: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`.

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Sie können die DSVM auch [über Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute) an Ihren Arbeitsbereich anfügen.

    > [!WARNING]
    > Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für die gleichen DSVM in Ihrem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

1. **Konfigurieren**: Erstellen Sie eine Ausführungskonfiguration für das DSVM-Computeziel. Zum Erstellen und Konfigurieren der Trainingsumgebung in der DSVM werden Docker und Conda verwendet.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

> [!TIP]
> Wenn Sie eine VM aus Ihrem Arbeitsbereich __entfernen__ (trennen) wollen, verwenden Sie die Methode [RemoteCompute.detach()](/python/api/azureml-core/azureml.core.compute.remotecompute#detach--).
>
> Azure Machine Learning löscht die VM nicht für Sie. Sie müssen die VM manuell löschen, indem Sie das Azure-Portal, die CLI oder das SDK für die Azure-VM verwenden.

## <a name="apache-spark-pools"></a><a id="synapse"></a>Apache Spark-Pools

Die Integration von Azure Synapse Analytics in Azure Machine Learning (Vorschau) ermöglicht Ihnen das Anfügen eines von Azure Synapse unterstützten Apache Spark-Pools für die interaktive Datenuntersuchung und -aufbereitung. Diese Integration bietet Ihnen eine dedizierte Computing-Ressource für Data Wrangling im großen Stil. Weitere Informationen finden Sie unter [Anfügen von Apache Spark-Pools (powered by Azure Synapse Analytics)](how-to-link-synapse-ml-workspaces.md#attach-synapse-spark-pool-as-a-compute).

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight ist eine beliebte Plattform für Big Data-Analysen. Die Plattform stellt Apache Spark bereit, das zum Training Ihres Modells verwendet werden kann.

1. **Erstellen**: Azure Machine Learning kann keinen HDInsight-Cluster für Sie erstellen. Stattdessen müssen Sie den Cluster erstellen und ihn dann Ihrem Azure Machine Learning Arbeitsbereich hinzufügen. Weitere Informationen finden Sie unter [Erstellen eines Spark-Clusters in HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

    > [!WARNING]
    > Von Azure Machine Learning wird vorausgesetzt, dass der HDInsight-Cluster über eine __öffentliche IP-Adresse__ verfügt.

    Beim Erstellen des Clusters müssen Sie einen SSH-Benutzernamen und ein Kennwort angeben. Notieren Sie diese Werte, da Sie sie benötigen, wenn Sie HDInsight als Computeziel verwenden.
    
    Stellen Sie nach der Erstellung des Clusters eine Verbindung mit dem Hostnamen „\<clustername>-ssh.azurehdinsight.net“ her, wobei \<clustername> der Name ist, den Sie für den Cluster angegeben haben. 

1. **Anfügen**: Wenn Sie einen HDInsight-Cluster als Computeziel anfügen möchten, müssen Sie den die Ressourcen-ID, den Benutzernamen und das Kennwort für den HDInsight-Cluster angeben. Die Ressourcen-ID des HDInsight-Clusters kann unter Verwendung der Abonnement-ID, des Ressourcengruppennamens und des Namens des HDInsight-Clusters im folgenden Zeichenfolgenformat erstellt werden: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`.

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Sie können den HDInsight-Cluster auch [über Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute) an Ihren Arbeitsbereich anfügen.

    > [!WARNING]
    > Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für den gleichen HDInsight in Ihrem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

1. **Konfigurieren**: Erstellen Sie eine Ausführungskonfiguration für das HDI-Computeziel. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]

> [!TIP]
> Wenn Sie einen HDInsight-Cluster aus dem Arbeitsbereich __entfernen__ (trennen) möchten, verwenden Sie die [HDInsightcompute.detach ()](/python/api/azureml-core/azureml.core.compute.hdinsight.hdinsightcompute#detach--)-Methode.
>
> Der HDInsight-Cluster wird von Azure Machine Learning nicht für Sie gelöscht. Sie müssen es manuell über das Azure-Portal, die CLI oder das SDK für Azure HDInsight löschen.

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch wird verwendet, um umfangreiche, auf Parallelverarbeitung ausgelegte HPC-Anwendungen effizient in der Cloud auszuführen. AzureBatchStep kann in einer Azure Machine Learning-Pipeline verwendet werden, um Aufträge an einen Azure Batch Pool von Computern zu übermitteln.

Um Azure Batch als Computeziel anzufügen, müssen Sie das Azure Machine Learning SDK verwenden und die folgenden Informationen angeben:

-    **Azure Batch-Computename**: Ein Computeanzeigename, der innerhalb des Arbeitsbereichs verwendet wird.
-    **Azure Batch-Kontoname**: Der Name des Azure Batch-Kontos.
-    **Ressourcengruppe**: Die Ressourcengruppe, die das Azure Batch-Konto enthält.

Der folgende Code veranschaulicht, wie Azure Batch als Computeziel angefügt wird:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für den gleichen Azure Batch in Ihrem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

## <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks ist eine Apache Spark-basierte Umgebung in der Azure-Cloud. Sie kann mit einer Azure Machine Learning-Pipeline als Computeziel verwendet werden.

> [!IMPORTANT]
> Azure Machine Learning kann kein Azure Databricks-Computeziel erstellen. Stattdessen müssen Sie einen Azure Databricks-Arbeitsbereich erstellen und ihn dann Ihren Azure Machine Learning-Arbeitsbereich anfügen. Informationen zum Erstellen einer Arbeitsbereichsressource finden Sie im Dokument [Ausführen eines Spark-Auftrags in Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).
> 
> Um einen Azure Databricks-Arbeitsbereich aus einem __anderen Azure-Abonnement__ anfügen zu können, muss Ihnen (Ihrem Azure AD-Konto) die Rolle **Mitwirkender** für den Azure Databricks-Arbeitsbereich erteilt werden. Überprüfen Sie Ihren Zugriff im [Azure-Portal](https://ms.portal.azure.com/).

Geben Sie zum Anfügen von Azure Databricks als Computeziel die folgenden Informationen an:

* __Databricks-Computename__: Der Name, der dieser Computeressource zugewiesen werden soll.
* __Name des Databricks-Arbeitsbereichs__: Der Name des Azure Databricks-Arbeitsbereichs.
* __Databricks-Zugriffstoken__: Das zur Authentifizierung bei Azure Databricks verwendete Zugriffstoken. Informationen zum Generieren eines Zugriffstokens finden Sie im Dokument [Authentifizierung](/azure/databricks/dev-tools/api/latest/authentication).

Der folgende Code veranschaulicht, wie Azure Databricks mit dem Azure Machine Learning-SDK als Computeziel angefügt wird:

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Ein ausführlicheres Beispiel finden Sie in einem [Beispiel-Notebook](https://aka.ms/pl-databricks) auf GitHub.

> [!WARNING]
> Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für den gleichen Azure Databricks in Ihrem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

## <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics ist eine umfangreiche Datenanalyseplattform in der Azure-Cloud. Sie kann mit einer Azure Machine Learning-Pipeline als Computeziel verwendet werden.

Erstellen Sie vor der Verwendung ein Azure Data Lake Analytics-Konto. Informationen zum Erstellen dieser Ressource finden Sie im Dokument [Erste Schritte mit Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Um Data Lake Analytics als Computeziel anzufügen, müssen Sie das Azure Machine Learning SDK verwenden und die folgenden Informationen angeben:

* __Computename__: Der Name, der dieser Computeressource zugewiesen werden soll.
* __Ressourcengruppe__: Die Ressourcengruppe, die das Data Lake Analytics-Konto enthält.
* __Kontoname__: Der Name des Data Lake Analytics-Kontos.

Der folgende Code veranschaulicht, wie Data Lake Analytics als Computeziel angefügt wird:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Ein ausführlicheres Beispiel finden Sie in einem [Beispiel-Notebook](https://aka.ms/pl-adla) auf GitHub.

> [!WARNING]
> Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für das gleiche ADLA in Ihrem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

> [!TIP]
> Azure Machine Learning-Pipelines können nur ausgeführt werden, wenn die Daten im Standarddatenspeicher des Data Lake Analytics-Kontos gespeichert werden. Wenn die Daten, die Sie verwenden möchten, in einem nicht standardmäßigen Speicher gespeichert sind, können Sie die Daten mithilfe von [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep) vor dem Trainieren kopieren.

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container Instances

Azure Container Instances (ACI) werden dynamisch erstellt, wenn Sie ein Modell bereitstellen. Es gibt keine andere Möglichkeit, ACI zu erstellen und an Ihren Arbeitsbereich anzufügen. Weitere Informationen finden Sie unter [Bereitstellen eines Modells in Azure Container Instances](how-to-deploy-azure-container-instance.md).

## <a name="kubernetes-preview"></a><a id="kubernetes"></a>Kubernetes (Vorschau)

Azure Machine Learning bietet die folgenden Optionen zum Anfügen eigener Kubernetes-Cluster für Training und Rückschlüsse:

* [Azure Kubernetes Service](../aks/intro-kubernetes.md) Azure Kubernetes Service bietet verwaltete Cluster in Azure.
* [Kubernetes mit Azure Arc-Unterstützung](../azure-arc/kubernetes/overview.md) Verwenden Sie Kubernetes-Cluster mit Azure Arc-Unterstützung, wenn Ihr Cluster außerhalb von Azure gehostet wird.

[!INCLUDE [arc-enabled-machine-learning-create-training-compute](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

Verwenden Sie eine der folgenden Methoden, um einen Kubernetes-Cluster von Ihrem Arbeitsbereich zu trennen:

```python
compute_target.detach()
```

> [!WARNING]
> Beim Trennen eines Clusters **wird der Cluster nicht gelöscht**. Informationen zum Löschen eines Azure Kubernetes Service-Clusters finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster). Informationen zum Löschen eines Kubernetes-Clusters mit Azure Arc-Unterstützung finden Sie im [Azure Arc-Schnellstart](../azure-arc/kubernetes/quickstart-connect-cluster.md#7-clean-up-resources).

## <a name="notebook-examples"></a>Notebook-Beispiele

Beispiele für das Training mit verschiedenen Computeziele finden Sie in diesen Notebooks:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie die Computeressource zum [Konfigurieren und Übermitteln einer Trainingsausführung](how-to-set-up-training-targets.md).
* [Tutorial: Trainieren eines Modells](tutorial-train-models-with-aml.md) verwendet ein verwaltetes Computeziel zum Trainieren eines Modells.
* Erfahren Sie, wie [Hyperparameter optimiert werden](how-to-tune-hyperparameters.md), um bessere Modelle zu erstellen.
* Erfahren Sie nach der Erstellung eines trainierten Modells, [wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).
* [Verwenden von Azure Machine Learning mit virtuellen Azure-Netzwerken](./how-to-network-security-overview.md)
