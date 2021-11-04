---
title: Erstellen eines verknüpften Diensts mit Synapse- und Azure Machine Learning-Arbeitsbereichen (Vorschauversion)
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie Sie Azure Synapse- und Azure Machine Learning-Arbeitsbereiche verknüpfen sowie Apache Spark-Pools anfügen, um eine Benutzeroberfläche mit einheitlichem Data Wrangling zu erhalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: devx-track-python, data4ml, synapse-azureml, contperf-fy21q4
ms.openlocfilehash: e4efd0564be556704f0a4ac2c6ae4b378b19fb2b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564782"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-and-attach-apache-spark-poolspreview"></a>Verknüpfen von Azure Synapse Analytics- und Azure Machine Learning-Arbeitsbereichen sowie Anfügen von Apache Spark-Pools (Vorschau)

In diesem Artikel wird beschrieben, wie Sie einen verknüpften Dienst erstellen, mit dem Ihr [Azure Synapse Analytics-Arbeitsbereich](../synapse-analytics/overview-what-is.md) und [Azure Machine Learning-Arbeitsbereich](concept-workspace.md) verknüpft werden. 

Nachdem Ihr Azure Machine Learning-Arbeitsbereich mit Ihrem Azure Synapse-Arbeitsbereich verknüpft wurde, können Sie einen Apache Spark-Pool (von Azure Synapse Analytics unterstützt) als dedizierte Compute-Umgebung für Data Wrangling im großen Stil anfügen oder das Modelltraining über dasselbe Python-Notebook durchführen.

Sie können Ihren ML-Arbeitsbereich und den Synapse-Arbeitsbereich über das [Python SDK](#link-sdk) oder [Azure Machine Learning Studio](#link-studio) verknüpfen.

Sie können zum Verknüpfen von Arbeitsbereichen und Anfügen eines Synapse Spark-Pools auch eine [ARM-Vorlage (Azure Resource Manager)](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-linkedservice-create/azuredeploy.json) verwenden.

>[!IMPORTANT]
> Die Integration von Azure Machine Learning und Azure Synapse befindet sich in der öffentlichen Vorschauphase. Die Funktionen des Pakets `azureml-synapse` sind [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Previewfunktionen, die sich im Laufe der Zeit ändern können.

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md?tabs=python).

* [Erstellen eines Synapse-Arbeitsbereichs im Azure-Portal](../synapse-analytics/quickstart-create-workspace.md)

* [Erstellen eines Apache Spark-Pools über das Azure-Portal, Webtools oder Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)

* Installieren des [Python SDK für Azure Machine Learning](/python/api/overview/azure/ml/intro)

* Zugriff auf [Azure Machine Learning Studio](https://ml.azure.com/)

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Verknüpfen von Arbeitsbereichen mit dem Python SDK

> [!IMPORTANT]
> Um erfolgreich eine Verknüpfung mit dem Synapse-Arbeitsbereich herzustellen, benötigen Sie die Rolle **Besitzer** im Synapse-Arbeitsbereich. Überprüfen Sie Ihren Zugriff im [Azure-Portal](https://ms.portal.azure.com/).
>
> Wenn Sie kein **Besitzer**, sondern nur ein **Mitwirkender** für den Synapse-Arbeitsbereich sind, können Sie nur vorhandene verknüpfte Dienste verwenden. Informieren Sie sich über das [Abrufen und Verwenden eines vorhandenen verknüpften Diensts](#get-an-existing-linked-service).

Der folgende Code verwendet die Klassen [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) und [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration).

* Verknüpfen Sie Ihren Machine Learning-Arbeitsbereich `ws` mit dem Azure Synapse-Arbeitsbereich.
* Registrieren Sie Ihren Synapse-Arbeitsbereich als verknüpften Dienst bei Azure Machine Learning.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> Eine verwaltete Identität (`system_assigned_identity_principal_id`) wird für jeden verknüpften Dienst erstellt. Dieser verwalteten Identität muss die Rolle **Synapse Apache Spark-Administrator** des Synapse-Arbeitsbereichs zugewiesen werden, bevor Sie die Synapse-Sitzung starten. [Weisen Sie die Rolle „Synapse Apache Spark-Administrator“ der verwalteten Identität in Synapse Studio zu.](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)
>
> Verwenden Sie `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` zum Ermitteln von `system_assigned_identity_principal_id` eines bestimmten verknüpften Diensts.

### <a name="manage-linked-services"></a>Verwalten von verknüpften Diensten

Zeigen Sie alle verknüpften Dienste an, die Ihrem Machine Learning-Arbeitsbereich zugeordnet sind.

```python
LinkedService.list(ws)
```

Verwenden Sie die Methode `unregister()`, um die Verknüpfung von Arbeitsbereichen aufzuheben.

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Verknüpfen von Arbeitsbereichen über Studio

Verknüpfen Sie mit den folgenden Schritten den Machine Learning-Arbeitsbereich und den Synapse-Arbeitsbereich über Azure Machine Learning Studio: 

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com/) an.
1. Wählen Sie im linken Bereich im Abschnitt **Verwalten** die Option **Verknüpfte Dienste** aus.
1. Wählen Sie **Integration hinzufügen** aus.
1. Füllen Sie die Felder im Formular **Arbeitsbereich verknüpfen** aus.

    |Feld| BESCHREIBUNG    
    |---|---
    |Name| Geben Sie einen Namen für Ihren verknüpften Dienst ein. Dieser Name wird verwendet, um auf diesen bestimmten verknüpften Dienst zu verweisen.
    |Abonnementname | Wählen Sie den Namen Ihres Abonnements aus, das Ihrem Machine Learning-Arbeitsbereich zugeordnet ist. 
    |Synapse-Arbeitsbereich | Wählen Sie den Synapse-Arbeitsbereich aus, mit dem Sie eine Verknüpfung herstellen möchten.
    
1. Wählen Sie **Weiter** aus, um das Formular **Spark-Pools auswählen (optional)** zu öffnen. Wählen Sie in diesem Formular aus, welcher Synapse Spark-Pool an Ihren Arbeitsbereich angefügt werden soll.

1. Wählen Sie **Weiter** aus, um das Formular **Überprüfung** zu öffnen und Ihre Auswahl zu überprüfen.
1. Wählen Sie **Erstellen** aus, um die Erstellung des verknüpften Diensts abzuschließen.

## <a name="get-an-existing-linked-service"></a>Abrufen eines vorhandenen verknüpften Diensts

Damit Sie eine dedizierte Computeressource für Data Wrangling anfügen können, benötigen Sie einen ML-Arbeitsbereich, der mit einem Azure Synapse Analytics-Arbeitsbereich verknüpft ist. Dies wird als verknüpfter Dienst bezeichnet. 

Zum Abrufen und Verwenden eines vorhandenen verknüpften Diensts sind Berechtigungen vom Typ **Benutzer oder Mitwirkender** für den **Azure Synapse Analytics-Arbeitsbereich** erforderlich.

In diesem Beispiel wird ein vorhandener verknüpfter Dienst (`synapselink1`) aus dem Arbeitsbereich `ws` mit der Methode [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) abgerufen.
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```

## <a name="attach-synapse-spark-pool-as-a-compute"></a>Anfügen eines Synapse Spark-Pools als Computeressource

Wenn Sie den verknüpften Dienst abgerufen haben, fügen Sie einen Synapse Apache Spark-Pool als dedizierte Computeressource für Ihre Data Wrangling-Aufgaben an. 

Sie können Apache Spark-Pools über folgende Komponenten anfügen:
* Azure Machine Learning Studio
* [Azure Resource Manager-Vorlagen](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-linkedservice-create/azuredeploy.json)
* Python-SDK für Azure Machine Learning 

### <a name="attach-a-pool-via-the-studio"></a>Anfügen eines Pools über Studio
Folgen Sie diesen Schritten: 

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com/) an.
1. Wählen Sie im linken Bereich im Abschnitt **Verwalten** die Option **Verknüpfte Dienste** aus.
1. Wählen Sie Ihren Synapse-Arbeitsbereich aus.
1. Wählen Sie oben links **Angefügte Spark-Pools** aus. 
1. Wählen Sie **Anfügen** aus. 
1. Wählen Sie Ihren Apache Spark-Pool in der Liste aus, und geben Sie einen Namen an.  
    1. Diese Liste gibt Aufschluss über die verfügbaren Synapse Spark-Pools, die an Ihre Computeressource angefügt werden können. 
    1. Informationen zum Erstellen eines neuen Synapse Spark-Pools finden Sie unter [Erstellen eines Apache Spark-Pools mit Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
1. Wählen Sie **Attach selected** (Ausgewählte anfügen) aus. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Anfügen eines Pools mit dem Python SDK

Sie können einen Apache Spark-Pool auch mithilfe des **Python SDK** anfügen. 

Mit dem unten genannten Code werden die folgenden Schritte ausgeführt: 
1. Konfiguration von [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute) mit

   1. dem [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice), `linked_service`, den Sie im vorherigen Schritt erstellt oder abgerufen haben. 
   1. dem Typ des Computeziels, das Sie anfügen möchten: `SynapseSpark`
   1. dem Namen des Apache Spark-Pools. Dieser muss einem vorhandenen Apache Spark-Pool entsprechen, der sich in Ihrem Azure Synapse Analytics-Arbeitsbereich befindet.
   
1. Erstellen eines Machine Learning-[`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget) durch Übergabe 
   1. des Machine Learning-Arbeitsbereichs, den Sie verwenden möchten: `ws`
   1. des Namens, mit dem Sie im Azure Machine Learning-Arbeitsbereich auf die Computeressource verweisen möchten 
   1. der Anfügekonfiguration (attach_configuration), die Sie beim Konfigurieren der Synapse-Computeressource angegeben haben
       1. Der Aufruf von „ComputeTarget.attach()“ ist asynchron, daher wird das Beispiel blockiert, bis der Aufruf abgeschlossen ist.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

Überprüfen Sie, ob der Apache Spark-Pool angefügt wurde.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="next-steps"></a>Nächste Schritte

* [Data Wrangling mit Azure Synapse (Vorschau)](how-to-data-prep-synapse-spark-pool.md)
* [Verwenden von Apache Spark in Ihrer Machine Learning-Pipeline mit Azure Synapse (Vorschau)](how-to-use-synapsesparkstep.md)
* [Trainieren eines Modells](how-to-set-up-training-targets.md).