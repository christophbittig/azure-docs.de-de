---
title: Failover und Notfallwiederherstellung
titleSuffix: Azure Machine Learning
description: Informieren Sie sich, wie Sie die Notfallwiederherstellung planen und die Geschäftskontinuität für Azure Machine Learning aufrechterhalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.openlocfilehash: 0a18a2bb7efd673ff6e2d6460890e80d52609b02
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564839"
---
# <a name="failover-for-business-continuity-and-disaster-recovery"></a>Failover für Business Continuity & Disaster Recovery

Planen Sie zum Maximieren der Betriebszeit voraus, um die Geschäftskontinuität aufrechtzuerhalten und die Notfallwiederherstellung mit Azure Machine Learning vorzubereiten. 

Microsoft möcht sicherstellen, dass Azure-Dienste immer verfügbar sind. Es kann jedoch zu ungeplanten Dienstausfällen kommen. Es wird empfohlen, einen Notfallwiederherstellungsplan für die Behandlung regionaler Dienstausfälle zu erstellen. In diesem Artikel lernen Sie Folgendes:

* Planen Sie eine sich auf mehrere Regionen erstreckende Bereitstellung von Azure Machine Learning und zugehörigen Ressourcen.
* Erstellen Sie einen Entwurf für die Hochverfügbarkeit Ihrer Lösung.
* Initiieren Sie ein Failover in eine andere Region.

> [!NOTE]
> Azure Machine Learning selbst bietet kein automatisches Failover bzw. keine automatische Notfallwiederherstellung.

Falls Sie Ihren Arbeitsbereich oder die entsprechenden Komponenten versehentlich gelöscht haben, enthält dieser Artikel auch die derzeit unterstützten Wiederherstellungsoptionen.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Grundlegendes zu Azure-Diensten für Azure Machine Learning

Azure Machine Learning ist von mehreren Azure-Diensten abhängig und verfügt über mehrere Ebenen. Einige dieser Dienste werden in Ihrem Abonnement (Kundenabonnement) bereitgestellt. Sie sind für die Hochverfügbarkeitskonfiguration dieser Dienste verantwortlich. Andere Dienste werden in einem Microsoft-Abonnement erstellt und von Microsoft verwaltet. 

Die Azure-Dienste umfassen Folgendes:

* **Azure Machine Learning-Infrastruktur**: Eine von Microsoft verwaltete Umgebung für den Azure Machine Learning-Arbeitsbereich.

* **Zugeordnete Ressourcen**: Ressourcen, die während der Erstellung des Azure Machine Learning-Arbeitsbereichs in Ihrem Abonnement bereitgestellt werden. Zu diesen Ressourcen zählen Azure Storage, Azure Key Vault, Azure Container Registry (ACR) und Application Insights. Sie sind für das Konfigurieren von Hochverfügbarkeitseinstellungen für diese Ressourcen verantwortlich.
  * Standardspeicher enthält Daten wie etwa Modelle, Trainingsprotokolldaten und Datasets.
  * Key Vault enthält Anmeldeinformationen für Azure Storage, Container Registry und Datenspeicher.
  * Container Registry enthält ein Docker-Image für die Trainings- und Rückschlussumgebungen.
  * Application Insights dient zum Überwachen von Azure Machine Learning.

* **Computeressourcen**: Ressourcen, die Sie nach der Bereitstellung des Arbeitsbereichs erstellen. Beispielsweise können Sie eine Compute-Instanz oder einen Computecluster erstellen, um ein Machine Learning-Modell zu trainieren.
  * Compute-Instanz und Computecluster: Von Microsoft verwaltete Umgebungen für die Modellentwicklung.
  * Weitere Ressourcen: Microsoft Computeressourcen, die an Azure Machine Learning angefügt werden können, z. B. Azure Kubernetes Service (AKS), Azure Databricks, Azure Container Instances und Azure HDInsight. Sie sind für das Konfigurieren von Hochverfügbarkeitseinstellungen für diese Ressourcen verantwortlich.

* **Weitere Datenspeicher**: Azure Machine Learning kann für Trainingsdaten weitere Datenspeicher einbinden, z. B. Azure Storage, Azure Data Lake Storage und Azure SQL-Datenbank.  Diese Datenspeicher werden in Ihrem Abonnement bereitgestellt. Sie sind für das Konfigurieren ihrer Hochverfügbarkeitseinstellungen verantwortlich.

Die folgende Tabelle zeigt, welche Azure-Dienste von Microsoft und welche von Ihnen verwaltet werden. Außerdem werden die Dienste angegeben, die standardmäßig hoch verfügbar sind.

| Dienst | Verwaltet von | Hochverfügbarkeit standardmäßig |
| ----- | ----- | ----- |
| **Azure Machine Learning-Infrastruktur** | Microsoft | |
| **Zugeordnete Ressourcen** |
| Azure Storage | Sie | |
| Key Vault | Sie | ✓ |
| Container Registry | Sie | |
| Application Insights | Sie | Nicht verfügbar |
| **Computeressourcen** |
| Compute-Instanz | Microsoft |  |
| Computecluster | Microsoft |  |
| Andere Computeressourcen, z. B. AKS, <br>Azure Databricks, Container Instances, HDInsight | Sie |  |
| **Andere Datenspeicher** wie Azure Storage, SQL-Datenbank,<br> Azure Database for PostgreSQL und Azure Database for MySQL und <br>Azure Databricks File System | Sie | |

Im weiteren Verlauf dieses Artikels werden die Aktionen beschrieben, die Sie durchführen müssen, damit die einzelnen Dienste hochverfügbar werden.

## <a name="plan-for-multi-regional-deployment"></a>Planen der Bereitstellung in mehreren Regionen

Eine Bereitstellung in mehreren Regionen basiert auf der Erstellung von Azure Machine Learning-Ressourcen und anderen Ressourcen (Infrastruktur) in zwei Azure-Regionen. Wenn ein regionaler Ausfall auftritt, können Sie zur anderen Region wechseln. Beachten Sie bei der Planung des Bereitstellungsorts für Ihre Ressourcen Folgendes:

* __Regionale Verfügbarkeit__: Verwenden Sie Regionen, die sich in der Nähe der Benutzer befinden. Informationen zur regionalen Verfügbarkeit für Azure Machine Learning finden Sie unter [Azure-Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).
* __Azure-Regionspaare__: Regionspaare koordinieren Plattformupdates und priorisieren bei Bedarf Wiederherstellungsvorgänge. Weitere Informationen finden Sie unter [Azure-Regionspaare](../best-practices-availability-paired-regions.md).
* __Dienstverfügbarkeit__: Entscheiden Sie, ob die von Ihrer Lösung verwendeten Ressourcen Zugriffsebenen der Varianten heiß/heiß, heiß/warm oder heiß/kalt aufweisen sollen.
    
    * __Heiß/heiß__: Beide Regionen sind gleichzeitig aktiv, und in einer Region kann die Verwendung sofort beginnen.
    * __Heiß/warm__: Die primäre Region ist aktiv, die sekundäre Region verfügt über wichtige Ressourcen (z. B. bereitgestellte Modelle), die startbereit sind. Weniger wichtige Ressourcen müssten manuell in der sekundären Region bereitgestellt werden.
    * __Heiß/kalt__: Die primäre Region ist aktiv, in der sekundären Region sind Azure Machine Learning-Ressourcen und andere Ressourcen sowie die erforderlichen Daten bereitgestellt. Ressourcen wie Modelle, Modellimplementierungen oder Pipelines müssten manuell bereitgestellt werden.

> [!TIP]
> Abhängig von Ihren Geschäftsanforderungen können Sie verschiedene Azure Machine Learning-Ressourcen jeweils unterschiedlich behandeln. Beispielsweise empfiehlt es sich gegebenenfalls, die Zugriffsebenenvariante heiß/heiß für bereitgestellte Modelle (Rückschluss) und die Variante heiß/kalt für Experimente (Training) zu verwenden.

Azure Machine Learning baut auf weiteren Diensten auf. Einige Dienste können für die Replikation in andere Regionen konfiguriert werden. Andere müssen Sie manuell in mehreren Regionen erstellen. Die folgende Tabelle enthält eine Liste der Dienste, den jeweiligen Replikationsverantwortlichen sowie eine Übersicht über die jeweilige Konfiguration:

| Azure-Dienst | Georeplikation durch | Konfiguration |
| ----- | ----- | ----- |
| Machine Learning-Arbeitsbereich | Sie | Erstellen Sie einen Arbeitsbereich in den ausgewählten Regionen. |
| Machine Learning Compute | Sie | Erstellen Sie die Computeressourcen in den ausgewählten Regionen. Stellen Sie für Computeressourcen, die dynamisch skaliert werden können, sicher, dass beide Regionen ein ausreichendes Computekontingent für Ihre Anforderungen bereitstellen. |
| Key Vault | Microsoft | Verwenden Sie dieselbe Key Vault-Instanz mit dem Azure Machine Learning-Arbeitsbereich und den Ressourcen in beiden Regionen. Von Key Vault wird ein automatisches Failover in eine sekundäre Region ausgeführt. Weitere Informationen finden Sie unter [Azure Key Vault: Verfügbarkeit und Redundanz](../key-vault/general/disaster-recovery-guidance.md).|
| Container Registry | Microsoft | Konfigurieren Sie die Container Registry-Instanz für die Georeplikation von Registrierungen in die jeweilige Region des Regionspaars für Azure Machine Learning. Verwenden Sie dieselbe Instanz für beide Arbeitsbereichsinstanzen. Weitere Informationen finden Sie unter [Georeplikation in Azure Container Registry](../container-registry/container-registry-geo-replication.md). |
| Speicherkonto | Sie | Azure Machine Learning unterstützt kein Failover des __Standardspeicherkontos__ mit georedundantem Speicher (GRS), geozonenredundantem Speicher (GZRS), georedundantem Speicher mit Lesezugriff (RA-GRS) oder geozonenredundantem Speicher mit Lesezugriff (RA-GZRS). Erstellen Sie ein separates Speicherkonto für den Standardspeicher jedes Arbeitsbereichs. </br>Erstellen Sie separate Speicherkonten oder Dienste für andere Datenspeicher. Weitere Informationen finden Sie unter [Azure Storage-Redundanz](../storage/common/storage-redundancy.md). |
| Application Insights | Sie | Erstellen Sie Application Insights für den Arbeitsbereich in beiden Regionen. Wenn Sie den Aufbewahrungszeitraum der Daten und Details anpassen möchten, lesen Sie [Datenerfassung, -aufbewahrung und -speicherung in Application Insights](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept). |

Damit eine schnelle Wiederherstellung und ein Neustart in der sekundären Region möglich sind, empfehlen sich die folgenden Entwicklungsmethoden:

* Verwenden Sie Azure Resource Manager-Vorlagen. Vorlagen sind „Infrastructure-as-Code“ und ermöglichen Ihnen die schnelle Bereitstellung von Diensten in beiden Regionen.
* Aktualisieren Sie die Continuous Integration- und Bereitstellungspipelines für die Bereitstellung in beiden Regionen, um Drift zwischen den beiden Regionen zu vermeiden.
* Beziehen Sie beim Automatisieren von Bereitstellungen die Konfiguration der an den Arbeitsbereich angeschlossenen Computeressourcen ein, z. B. Azure Kubernetes Service.
* Erstellen Sie Rollenzuweisungen für Benutzer in beiden Regionen.
* Erstellen Sie Netzwerkressourcen wie Azure Virtual Networks und private Endpunkte für beide Regionen. Stellen Sie sicher, dass Benutzer Zugriff auf beide Netzwerkumgebungen haben. Beispielsweise VPN- und DNS-Konfigurationen für beide virtuellen Netzwerke.

### <a name="compute-and-data-services"></a>Compute- und Datendienste

Je nach Ihren Anforderungen verfügen Sie möglicherweise über mehrere Compute- oder Datendienste, die von Azure Machine Learning verwendet werden. Beispielsweise könnten Sie Azure Kubernetes Services oder die Azure SQL-Datenbank verwenden. Verwenden Sie die folgenden Informationen, um zu erfahren, wie Sie diese Dienste für Hochverfügbarkeit konfigurieren.

__Computeressourcen__

* **Azure Kubernetes Service:** Siehe [Best Practices für Geschäftskontinuität und Notfallwiederherstellung in Azure Kubernetes Service (AKS)](../aks/operator-best-practices-multi-region.md) und [Erstellen eines Azure Kubernetes Service-Clusters (AKS), der Verfügbarkeitszonen verwendet](../aks/availability-zones.md). Wenn der AKS-Cluster mit Azure Machine Learning Studio, dem SDK oder der CLI erstellt wurde, wird regionsübergreifende Hochverfügbarkeit nicht unterstützt.
* **Azure Databricks**: Siehe [Regionale Notfallwiederherstellung für Azure Databricks-Cluster](/azure/databricks/scenarios/howto-regional-disaster-recovery).
* **Container Instances**: Ein ACI-Orchestrator ist für Failover verantwortlich. Siehe [Azure Container Instances und Containerorchestratoren](../container-instances/container-instances-orchestrator-relationship.md).
* **HDInsight**: Siehe [Von Azure HDInsight unterstützte Hochverfügbarkeitsdienste](../hdinsight/hdinsight-high-availability-components.md).

__Datendienste__:

* **Azure-Blobcontainer/Azure Files/Data Lake Storage Gen2**: Siehe [Azure Storage-Redundanz.](../storage/common/storage-redundancy.md)
* **Data Lake Storage Gen1:** Weitere Informationen finden Sie im [Leitfaden zu Hochverfügbarkeit und Notfallwiederherstellung für Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).
* **SQL-Datenbank:** Weitere Informationen finden Sie unter [Hochverfügbarkeit für Azure SQL-Datenbank und SQL Managed Instance](../azure-sql/database/high-availability-sla.md).
* **Azure Database for PostgreSQL**: Siehe [Hochverfügbarkeitskonzepte von Azure Database for PostgreSQL – Einzelserver](../postgresql/concepts-high-availability.md).
* **Azure Database for MySQL**: Siehe [Informationen zur Geschäftskontinuität in Azure Database for MySQL](../mysql/concepts-business-continuity.md).
* **Azure Databricks File System**: Siehe [Regionale Notfallwiederherstellung für Azure Databricks-Cluster](/azure/databricks/scenarios/howto-regional-disaster-recovery).

> [!TIP]
> Wenn Sie Ihren eigenen kundenseitig verwalteten Schlüssel zum Bereitstellen des Azure Machine Learning-Arbeitsbereichs angeben, wird Azure Cosmos DB ebenfalls in Ihrem Abonnement bereitgestellt. In diesem Fall sind Sie für das Konfigurieren der Hochverfügbarkeitseinstellungen verantwortlich. Siehe [Hochverfügbarkeit mit Azure Cosmos DB](../cosmos-db/high-availability.md).

## <a name="design-for-high-availability"></a>Entwurf für Hochverfügbarkeit

### <a name="deploy-critical-components-to-multiple-regions"></a>Bereitstellen wichtiger Komponenten in mehreren Regionen

Bestimmen Sie den Grad an Geschäftskontinuität, den Sie erreichen möchten. Der Grad kann sich zwischen den Komponenten Ihrer Lösung unterscheiden. Beispielsweise könnten Sie sich für eine Zugriffsebenenkonfiguration vom Typ „heiß/heiß“ für Produktionspipelines oder Modellimplementierungen und eine Zugriffsebenenkonfiguration vom Typ „heiß/kalt“ für Experimente entscheiden.

### <a name="manage-training-data-on-isolated-storage"></a>Verwalten von Trainingsdaten im isolierten Speicher

Indem Sie den Datenspeicher von dem Standardspeicher trennen, der vom Arbeitsbereich für Protokolle verwendet wird, können Sie:

* Dieselben Speicherinstanzen als Datenspeicher an den primären und den sekundären Arbeitsbereich anschließen
* Georeplikation für Datenspeicherkonten nutzen und die Betriebszeit maximieren

### <a name="manage-machine-learning-artifacts-as-code"></a>Verwalten von Machine Learning-Artefakten als Code

Ausführungen in Azure Machine Learning werden durch eine Ausführungsspezifikation definiert. Diese Spezifikation umfasst Abhängigkeiten von Eingabeartefakten, die auf Arbeitsbereichsinstanzebene verwaltet werden, einschließlich Umgebungen, Datasets und Compute. Für die Übermittlung und Bereitstellung von Ausführungen in mehreren Regionen empfehlen sich die folgenden Methoden:

* Verwalten Sie die Codebasis lokal, unterstützt durch ein Git-Repository.
    * Exportieren Sie wichtige Notebooks aus Azure Machine Learning Studio.
    * Exportieren Sie Pipelines, die in Studio als Code erstellt wurden.

      > [!NOTE]
      > Pipelines, die im Studio-Designer erstellt wurden, können derzeit nicht als Code exportiert werden.

* Verwalten Sie Konfigurationen als Code.

    * Vermeiden Sie hartcodierte Verweise auf den Arbeitsbereich. Konfigurieren Sie stattdessen mithilfe einer [Konfigurationsdatei](how-to-configure-environment.md#workspace) einen Verweis auf die Arbeitsbereichsinstanz, und verwenden Sie [Workspace.from_config()](/python/api/azureml-core/azureml.core.workspace.workspace#remarks), um den Arbeitsbereich zu initialisieren. Verwenden Sie zum Automatisieren des Prozesses den Befehl [az ml folder attach](/cli/azure/ml(v1)/folder#ext_azure_cli_ml_az_ml_folder_attach) der [Azure CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).
    * Verwenden Sie Hilfsprogramme für die Ausführungsübermittlung, z. B. [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) und [Pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)).
    * Verwenden Sie [Environments.save_to_directory()](/python/api/azureml-core/azureml.core.environment(class)#save-to-directory-path--overwrite-false-), um die Umgebungsdefinitionen zu speichern.
    * Verwenden Sie ein Dockerfile, wenn Sie benutzerdefinierte Docker-Images verwenden.
    * Verwenden Sie die [Datasetklasse](/python/api/azureml-core/azureml.core.dataset(class)), um die Sammlung von [Datenpfaden](/python/api/azureml-core/azureml.data.datapath) zu definieren, die von der Lösung verwendet werden.
    * Verwenden Sie die [Inferenceconfig](/python/api/azureml-core/azureml.core.model.inferenceconfig)-Klasse, um Modelle als Rückschlussendpunkte bereitzustellen.

## <a name="initiate-a-failover"></a>Initiieren eines Failovers

### <a name="continue-work-in-the-failover-workspace"></a>Fortsetzen der Arbeit im Failoverarbeitsbereich

Wenn der primäre Arbeitsbereich nicht mehr verfügbar ist, können Sie zum sekundären Arbeitsbereich wechseln, um mit dem Experimentieren und der Entwicklung fortzufahren. Ausführungen werden von Azure Machine Learning nicht automatisch an den sekundären Arbeitsbereich gesendet, wenn ein Ausfall vorliegt. Aktualisieren Sie die Codekonfiguration so, dass sie auf die neue Arbeitsbereichsressource verweist. Es wird empfohlen, die Hartcodierung von Arbeitsbereichsverweisen zu vermeiden. Verwenden Sie stattdessen eine [Arbeitsbereichskonfigurationsdatei](how-to-configure-environment.md#workspace), um manuelle Benutzerschritte beim Ändern von Arbeitsbereichen zu minimieren. Stellen Sie außerdem sicher, dass Sie alle Automatisierungen aktualisieren, z. B. Continuous Integration- und Bereitstellungspipelines zum neuen Arbeitsbereich.

Artefakte oder Metadaten können von Azure Machine Learning nicht zwischen Arbeitsbereichsinstanzen synchronisiert oder wiederhergestellt werden. Abhängig von Ihrer Strategie für die Anwendungsbereitstellung müssen Sie möglicherweise Artefakte verschieben oder Eingaben zum Experimentieren, z. B. Datasetobjekte, im Failoverarbeitsbereich neu erstellen, um die Ausführungsübermittlung fortzusetzen. Falls Sie die Ressourcen des primären und des sekundären Arbeitsbereichs so konfiguriert haben, dass zugeordnete Ressourcen mit aktivierter Georeplikation freigegeben werden, sind einige Objekte möglicherweise direkt für den Failoverarbeitsbereich verfügbar. Dies gilt beispielsweise, wenn in beiden Arbeitsbereichen dieselben Docker-Images, konfigurierten Datenspeicher und Azure Key Vault-Ressourcen gemeinsam genutzt werden. Die folgende Abbildung zeigt eine Konfiguration, bei der in zwei Arbeitsbereichen dieselben Images (1), Datenspeicher (2) und derselbe Key Vault (3) gemeinsam genutzt werden.

![Ressourcenkonfiguration – Referenz](./media/how-to-high-availability-machine-learning/bcdr-resource-configuration.png)

> [!NOTE]
> Aufträge, die ausgeführt werden, wenn ein Dienstausfall auftritt, werden nicht automatisch in den sekundären Arbeitsbereich überführt. Es ist auch unwahrscheinlich, dass die Aufträge im primären Arbeitsbereich fortgesetzt und erfolgreich abgeschlossen werden, sobald der Ausfall behoben wurde. Stattdessen müssen diese Aufträge erneut übermittelt werden, entweder im sekundären Arbeitsbereich oder im primären Arbeitsbereich (sobald der Ausfall behoben wurde).

### <a name="moving-artifacts-between-workspaces"></a>Verschieben von Artefakten zwischen Arbeitsbereichen

Abhängig von Ihrem Wiederherstellungsansatz müssen Sie möglicherweise Artefakte wie Dataset- und Modellobjekte zwischen den Arbeitsbereichen kopieren, um die Arbeit fortzusetzen. Derzeit ist die Portabilität von Artefakten zwischen Arbeitsbereichen eingeschränkt. Es wird empfohlen, Artefakte nach Möglichkeit als Code zu verwalten, damit sie in der Failoverinstanz neu erstellt werden können.

Die folgenden Artefakte können mithilfe der [Azure CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md) zwischen Arbeitsbereichen exportiert und importiert werden:

| Artefakt | Exportieren | Importieren |
| ----- | ----- | ----- |
| Modelle | [az ml model download --model-id {ID} --target-dir {PATH}](/cli/azure/ext/azure-cli-ml/ml/model#ext_azure_cli_ml_az_ml_model_download) | [az ml model register –name {NAME} --path {PATH}](/cli/azure/ext/azure-cli-ml/ml/model) |
| Umgebungen | [az ml environment download -n {NAME} -d {PATH}](/cli/azure/ext/azure-cli-ml/ml/environment#ext_azure_cli_ml_az_ml_environment_download) | [az ml environment register -d {PATH}](/cli/azure/ext/azure-cli-ml/ml/environment#ext_azure_cli_ml_az_ml_environment_register) |
| Azure Machine Learning-Pipelines (codegeneriert) | [az ml pipeline get --path {PATH}](/cli/azure/ml(v1)/pipeline#ext_azure_cli_ml_az_ml_pipeline_get) | [az ml pipeline create --name {NAME} -y {PATH}](/cli/azure/ml(v1)/pipeline#ext_azure_cli_ml_az_ml_pipeline_create)

> [!TIP]
> * __Registrierte Datasets__ können nicht heruntergeladen oder verschoben werden. Dies schließt von Azure Machine Learning generierte Datasets ein, z. B. Zwischen-Pipelinedatasets. Datasets, die auf einen freigegebenen Dateispeicherort verweisen, auf den beide Arbeitsbereiche zugreifen können, oder bei dem der zugrunde liegende Datenspeicher repliziert wird, können jedoch in beiden Arbeitsbereichen registriert werden. Verwenden Sie [az ml dataset register](/cli/azure/ml(v1)/dataset#ext_azure_cli_ml_az_ml_dataset_register), um ein Dataset zu registrieren.
> * __Ausführungsausgaben__ werden im Standardspeicherkonto gespeichert, das einem Arbeitsbereich zugeordnet ist. Während bei einem Dienstausfall möglicherweise nicht mehr über die Studio-Benutzeroberfläche auf Ausführungsausgaben zugegriffen werden kann, können Sie direkt über das Speicherkonto auf die Daten zugreifen. Weitere Informationen zum Arbeiten mit in Blobs gespeicherten Daten finden Sie unter [Erstellen, Herunterladen und Auflisten von Blobs mit der Azure-Befehlszeilenschnittstelle](../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="recovery-options"></a>Wiederherstellungsoptionen

### <a name="workspace-deletion"></a>Löschen eines Arbeitsbereichs

Wenn Sie Ihren Arbeitsbereich versehentlich gelöscht haben, ist es derzeit nicht möglich, ihn wiederherzustellen. Sie können Ihre vorhandenen Notebooks jedoch aus dem entsprechenden Speicher abrufen, wenn Sie die folgenden Schritte ausführen:
* Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem Speicherkonto, das mit dem gelöschten Azure Machine Learning-Arbeitsbereich verknüpft war.
* Klicken Sie im Abschnitt „Datenspeicher“ auf der linken Seite auf **Dateifreigaben**.
* Ihre Notebooks befinden sich auf der Dateifreigabe mit dem Namen, der Ihre Arbeitsbereichs-ID enthält. 

![Notebook-Dateifreigabe zur Referenz](./media/how-to-high-availability-machine-learning/notebook-file-share.png)

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie eine [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/), um Azure Machine Learning mit zugeordneten Ressourcen mit Ihren Hochverfügbarkeitseinstellungen bereitzustellen.
