---
title: Identitätsbasierter Datenzugriff auf Speicherdienste in Azure
titleSuffix: Machine Learning
description: Erfahren Sie, wie Sie identitätsbasierten Datenzugriff verwenden, um mit Azure Machine Learning-Datenspeichern und dem Machine Learning Python SDK eine Verbindung mit Speicherdiensten in Azure herzustellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: b251c85752af69ce58e48f93be29cb07f1bc9966
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492737"
---
# <a name="connect-to-storage-by-using-identity-based-data-access"></a>Verbindung zum Speicher über identitätsbasierten Datenzugriff

In diesem Artikel erfahren Sie, wie Sie eine Verbindung mit Speicherdiensten in Azure mithilfe von identitätsbasiertem Datenzugriff und Azure Machine Learning-Datenspeicher herstellen, indem Sie das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro) verwenden.  

Für Datenspeicher wird normalerweise der auf Anmeldeinformationen basierende Datenzugriff genutzt, um zu verifizieren, dass Sie über die Berechtigung zum Zugreifen auf den Speicherdienst verfügen. Verbindungsinformationen, z. B. Ihre Abonnement-ID und Tokenautorisierung, werden im [Schlüsseltresor](https://azure.microsoft.com/services/key-vault/) gespeichert, der dem Arbeitsbereich zugeordnet ist. Wenn Sie einen Datenspeicher erstellen, für den der identitätsbasierte Zugriff verwendet wird, wird anhand Ihres Azure-Kontos ([Azure Active Directory-Token](../active-directory/fundamentals/active-directory-whatis.md)) überprüft, ob Sie über die Berechtigung für den Zugriff auf den Speicherdienst verfügen. In diesem Szenario werden keine Anmeldeinformationen für die Authentifizierung gespeichert. Nur die Speicherkontoinformationen werden im Datenspeicher gespeichert. 

Informationen zum Erstellen von Datenspeichern, für die die Authentifizierung mit Anmeldeinformationen genutzt wird, z. B. Zugriffsschlüssel oder Dienstprinzipale, finden Sie unter [Herstellen einer Verbindung mit Speicherdiensten in Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Identitätsbasierter Datenzugriff in Azure Machine Learning

Es gibt zwei Szenarien, in denen Sie den identitätsbasierten Datenzugriff in Azure Machine Learning anwenden können. Diese Szenarien eignen sich gut für den identitätsbasierten Zugriff, wenn Sie mit vertraulichen Daten arbeiten und eine präzisere Datenzugriffsverwaltung benötigen:

- Zugreifen auf Speicherdienste
- Trainieren von Machine Learning-Modellen mit privaten Daten

### <a name="accessing-storage-services"></a>Zugreifen auf Speicherdienste

Sie können eine Verbindung mit Speicherdiensten per identitätsbasiertem Zugriff herstellen, indem Sie Azure Machine Learning-Datenspeicher oder [Azure Machine Learning-Datasets](how-to-create-register-datasets.md) nutzen. 

Ihre Anmeldeinformationen für die Authentifizierung werden in der Regel in einem Datenspeicher aufbewahrt, mit dem sichergestellt wird, dass Sie über die Berechtigung für den Zugriff auf den Speicherdienst verfügen. Wenn diese Anmeldeinformationen über Datenspeicher registriert sind, kann jeder Benutzer mit der Arbeitsbereichsrolle „Leser“ sie abrufen. Dieser Umfang des Zugriffs kann für einige Organisationen ein Sicherheitsproblem darstellen. [Erfahren Sie mehr zur Rolle „Leser“ für Arbeitsbereiche](how-to-assign-roles.md#default-roles). 

Wenn Sie den identitätsbasierten Datenzugriff nutzen, werden Sie von Azure Machine Learning zum Eingeben Ihres Azure Active Directory-Tokens für die Authentifizierung für den Datenzugriff aufgefordert, und Ihre Anmeldeinformationen werden nicht im Datenspeicher aufbewahrt. Dieser Ansatz ermöglicht die Verwaltung des Datenzugriffs auf Speicherebene und sorgt für die Vertraulichkeit der Anmeldeinformationen. 

Dieses Verhalten gilt auch für Folgendes:

* [Erstellen eines Datasets direkt aus Speicher-URLs](#use-data-in-storage). 
* Nutzen Sie Daten interaktiv über ein Jupyter Notebook auf Ihrem lokalen Computer bzw. Ihrer [Compute-Instanz](concept-compute-instance.md).

> [!NOTE]
> Bei der Authentifizierung mit Anmeldeinformationen wird Folgendes gespeichert: Abonnement-ID, SAS-Token (Shared Access Signature) und Speicherzugriffsschlüssel sowie Dienstprinzipalinformationen, z. B. Client-IDs und Mandanten-IDs.

### <a name="model-training-on-private-data"></a>Modelltraining für private Daten

Bei bestimmten Machine Learning-Szenarien werden Modelle auch mit privaten Daten trainiert. In diesen Fällen müssen wissenschaftliche Fachkräfte für Daten (Data Scientists) Trainingsworkflows ausführen, ohne dass sie die vertraulichen Eingabedaten zu Gesicht bekommen. Bei diesem Szenario wird eine verwaltete Identität der Trainingscomputeressource für die Authentifizierung des Datenzugriffs verwendet. Mit diesem Ansatz können Speicheradministratoren dem Leser von Speicherblobdaten Zugriff auf die verwaltete Identität gewähren, die die Computeressource für Training verwendet, um den Trainingsauftrag auszuführen. Den einzelnen wissenschaftlichen Fachkräften für Daten muss kein Zugriff gewährt werden. Weitere Informationen finden Sie unter [Einrichten der verwalteten Identität für einen Computecluster](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.

- Ein Azure Storage-Konto mit einem unterstützten Speichertyp. Diese Speichertypen werden für die Vorschauversion unterstützt: 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md)

- Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/install).

- Ein Azure Machine Learning-Arbeitsbereich.
  
  Sie können wahlweise [einen Azure Machine Learning-Arbeitsbereich erstellen](how-to-manage-workspace.md) oder [mithilfe des Python SDKs einen vorhandenen Arbeitsbereich verwenden](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Berechtigungen für den Speicherzugriff

Um sicherzustellen, dass eine sichere Verbindung mit Ihrem Speicherdienst in Azure hergestellt wird, müssen Sie für Azure Machine Learning über die Berechtigung zum Zugreifen auf den entsprechenden Datenspeicher verfügen.

Beim identitätsbasierten Datenzugriff werden nur Verbindungen mit den folgenden Speicherdiensten unterstützt:

* Azure Blob Storage
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL-Datenbank

Für den Zugriff auf diese Speicherdienste benötigen Sie mindestens die Zugriffsberechtigung [Leser von Speicherblobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Nur Speicherkontobesitzer können [Ihre Zugriffsebene über das Azure-Portal ändern](../storage/blobs/assign-azure-role-data-access.md).

Wenn Sie ein Modell auf einem Remotecomputeziel trainieren, muss der Compute-Identität vom Speicherdienst mindestens die Rolle „Leser von Speicherblobdaten“ gewährt werden. Informieren Sie sich über das [Einrichten einer verwalteten Identität auf einem Computecluster](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Verwenden von virtuellen Netzwerken

Standardmäßig kann Azure Machine Learning nicht mit einem Speicherkonto kommunizieren, das sich hinter einer Firewall oder in einem virtuellen Netzwerk befindet.

Sie können Speicherkonten so konfigurieren, dass der Zugriff nur in bestimmten virtuellen Netzwerken gestattet ist. Diese Konfiguration erfordert zusätzliche Schritte, um sicherzustellen, dass keine Daten außerhalb des Netzwerks kompromittiert wurden. Dieses Verhalten gilt auch für den auf Anmeldeinformationen basierenden Datenzugriff. Weitere Informationen finden Sie unter [Konfigurieren virtueller Netzwerkszenarien](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Erstellen und Registrieren von Datenspeichern

Wenn Sie einen Speicherdienst in Azure als Datenspeicher registrieren, erstellen Sie diesen Datenspeicher automatisch und registrieren ihn in einem bestimmten Arbeitsbereich. Einen Leitfaden zu erforderlichen Berechtigungstypen finden Sie unter [Berechtigungen für den Speicherzugriff](#storage-access-permissions). Unter [Arbeiten mit virtuellen Netzwerken](#work-with-virtual-networks) erfahren Sie, wie Sie eine Verbindung mit dem Datenspeicher hinter virtuellen Netzwerken herstellen können.

Beachten Sie im folgenden Code das Fehlen der Authentifizierungsparameter, z. B. `sas_token`, `account_key`, `subscription_id` und der Dienstprinzipal `client_id`. Das Fehlen dieser Parameter ist ein Hinweis darauf, dass Azure Machine Learning den identitätsbasierten Datenzugriff für die Authentifizierung verwenden wird. Das Erstellen von Datenspeichern erfolgt typischerweise interaktiv in einem Notebook oder über das Studio. Daher wird Ihr Azure Active Directory-Token für die Datenzugriffsauthentifizierung verwendet.

> [!NOTE]
> Datenspeichernamen dürfen nur Kleinbuchstaben, Zahlen und Unterstriche enthalten. 

### <a name="azure-blob-container"></a>Azure-Blobcontainer

Um einen Azure-Blobcontainer als Datenspeicher zu registrieren, verwenden Sie [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Mit dem folgenden Code wird der Datenspeicher `credentialless_blob` erstellt, beim Arbeitsbereich `ws` registriert und anschließend der Variablen `blob_datastore` zugewiesen. Dieser Datenspeicher greift auf den Blobcontainer `my_container_name` unter dem Speicherkonto `my-account-name` zu.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Verwenden Sie [register_azure_data_lake()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-), um einen Datenspeicher zu registrieren, der eine Verbindung mit Azure Data Lake Storage Gen1 herstellt.

Mit dem folgenden Code wird der Datenspeicher `credentialless_adls1` erstellt, beim Arbeitsbereich `workspace` registriert und anschließend der Variablen `adls_dstore` zugewiesen. Dieser Datenspeicher greift auf das Azure Data Lake Storage-Konto `adls_storage` zu.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Verwenden Sie [register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-), um einen Datenspeicher zu registrieren, der eine Verbindung mit Azure Data Lake Storage Gen2 herstellt.

Mit dem folgenden Code wird der Datenspeicher `credentialless_adls2` erstellt, beim Arbeitsbereich `ws` registriert und anschließend der Variablen `adls2_dstore` zugewiesen. Dieser Datenspeicher greift auf das Dateisystem `tabular` unter dem Speicherkonto `myadls2` zu.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```
### <a name="azure-sql-database"></a>Azure SQL-Datenbank
Für eine Azure SQL-Datenbank verwenden Sie [register_azure_sql_database()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none--subscription-id-none--resource-group-none--grant-workspace-access-false----kwargs-), um einen Datenspeicher zu registrieren, der mit einem Azure SQL-Datenbankspeicher verbunden ist.

Mit dem folgenden Code wird der Datenspeicher `credentialless_sqldb` erstellt und beim Arbeitsbereich `ws` registriert. Anschließend wird er der Variablen `sqldb_dstore` zugewiesen. Dieser Datenspeicher greift auf die Datenbank `mydb` im `myserver` SQL DB-Server zu.  

```python
# createn sqldatabase datastore without credentials
                                                       
sqldb_dstore = Datastore.register_azure_sql_database(workspace=ws,
                                                       datastore_name='credentialless_sqldb',
                                                       server_name='myserver',
                                                       database_name='mydb')                                                       
                                                   
```

## <a name="use-data-in-storage"></a>Verwenden von Daten im Speicher

Es wird empfohlen, dass Sie [Azure Machine Learning-Datasets](how-to-create-register-datasets.md) verwenden, wenn Sie mit Ihren Daten im Speicher mit Azure Machine Learning interagieren. 

Datasets packen Ihre Daten in ein selten ausgewertetes Objekt für Aufgaben des maschinellen Lernens wie das Training. Mithilfe von Datasets können Sie zudem Dateien in allen Formaten aus Azure-Speicherdiensten, z. B. Azure Blob Storage und Azure Data Lake Storage, auf ein Computeziel [herunterladen oder darin einbinden](how-to-train-with-datasets.md#mount-vs-download).


Für die Erstellung von Datasets mit identitätsbasiertem Zugriff haben Sie die folgenden Optionen. Bei dieser Art der Dataseterstellung wird Ihr Azure Active Directory-Token für die Authentifizierung des Datenzugriffs verwendet. 

*  Verweisen auf Pfade aus Datenspeichern, für die auch der identitätsbasierte Datenzugriff verwendet wird: 
<br>Im folgenden Beispiel ist `blob_datastore` bereits vorhanden und verwendet den identitätsbasierten Datenzugriff.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Überspringen Sie die Datenspeichererstellung, und erstellen Sie Datasets direkt aus Speicher-URLs. Diese Funktionalität unterstützt derzeit nur Azure-Blobs und Azure Data Lake Storage Gen1 und Gen2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

Wenn Sie einen Trainingsauftrag übermitteln, für den ein Dataset genutzt wird, das per identitätsbasiertem Datenzugriff erstellt wurde, wird die verwaltete Identität der Trainingscomputeressource für die Authentifizierung des Datenzugriffs verwendet. Ihr Azure Active Directory-Token wird nicht verwendet. Stellen Sie bei diesem Szenario sicher, dass der verwalteten Identität der Computeressource vom Speicherdienst mindestens die Rolle „Leser von Speicherblobdaten“ gewährt wird. Weitere Informationen finden Sie unter [Einrichten der verwalteten Identität für Computecluster](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Azure Machine Learning-Datasets](how-to-create-register-datasets.md).
* [Trainieren mit Datasets](how-to-train-with-datasets.md)
* [Erstellen eines Datenspeichers mit schlüsselbasiertem Datenzugriff](how-to-access-data.md)
