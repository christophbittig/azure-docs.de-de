---
title: Vergleichen von Azure Data Factory mit Data Factory Version 1
description: In diesem Artikel wird Azure Data Factory mit Azure Data Factory Version 1 verglichen.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: tutorials
ms.topic: overview
ms.date: 04/09/2018
ms.openlocfilehash: 47dc63568683007d714997a2921ce27387609ba2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722380"
---
# <a name="compare-azure-data-factory-with-data-factory-version-1"></a>Vergleichen von Azure Data Factory mit Data Factory Version 1

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird Data Factory mit Data Factory Version 1 verglichen. Eine Einführung in Data Factory finden Sie unter [Einführung in Azure Data Factory](introduction.md). Eine Einführung in Data Factory Version 1 finden Sie unter [Einführung in Azure Data Factory](v1/data-factory-introduction.md). 

## <a name="feature-comparison"></a>Funktionsvergleiche
In der folgenden Tabelle werden die Features von Data Factory mit den Features von Data Factory Version 1 verglichen. 

| Funktion | Version 1 | Aktuelle Version | 
| ------- | --------- | --------- | 
| Datasets | Eine benannte Ansicht mit Daten, in der auf die Daten verwiesen wird, die Sie in Ihren Aktivitäten als Ein- und Ausgabe verwenden möchten. Datasets bestimmen Daten in verschiedenen Datenspeichern, z.B. Tabellen, Dateien, Ordnern und Dokumenten. Ein Azure Blob-Dataset gibt beispielsweise den Blobcontainer und -ordner in Azure Blob Storage an, aus dem die Aktivität die Daten lesen soll.<br/><br/>Mit **Verfügbarkeit** wird das Modell für die Aufteilung in Verarbeitungsfenster für das Dataset (beispielsweise stündlich, täglich usw.) definiert. | Die Datasets sind in der aktuellen Version identisch. Sie müssen aber keine **Verfügbarkeits** zeitpläne für die Datasets definieren. Sie können eine Triggerressource definieren, mit der Pipelines über ein Taktplaner-Paradigma geplant werden können. Weitere Informationen finden Sie unter [Trigger](concepts-pipeline-execution-triggers.md#trigger-execution) und [Datasets](concepts-datasets-linked-services.md). | 
| Verknüpfte Dienste | Verknüpfte Dienste ähneln Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die für Data Factory zum Herstellen einer Verbindung mit externen Ressourcen erforderlich sind. | Verknüpfte Dienste haben sich im Vergleich zu Data Factory V1 nicht verändert, verfügen aber über eine neue **connectVia**-Eigenschaft für die Nutzung der Integration Runtime-Computeumgebung der aktuellen Version von Data Factory. Weitere Informationen finden Sie unter [Integration Runtime in Azure Data Factory](concepts-integration-runtime.md) sowie unter [Eigenschaften des verknüpften Diensts für Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties). |
| Pipelines | Eine Data Factory kann eine oder mehrere Pipelines haben. Bei einer Pipeline handelt es sich um eine logische Gruppierung von Aktivitäten, die zusammen eine Aufgabe bilden. Zur Planung und Ausführung von Pipelines werden „startTime“, „endTime“ und „isPaused“ verwendet. | Bei Pipelines handelt es sich um Gruppen von Aktivitäten, die für Daten durchgeführt werden. Die Zeitplanung von Aktivitäten in der Pipeline wurde aber in neue Triggerressourcen unterteilt. Sie können sich Pipelines in der aktuellen Version von Data Factory eher als „Workfloweinheiten“ vorstellen, die Sie separat über Trigger planen. <br/><br/>Pipelines weisen in der aktuellen Version von Data Factory keine „Zeitfenster“ für die Ausführung auf. Die Konzepte startTime, endTime und isPaused aus Data Factory V1 sind in der aktuellen Version von Data Factory nicht mehr vorhanden. Weitere Informationen finden Sie unter [Pipelineausführung und Trigger in Azure Data Factory](concepts-pipeline-execution-triggers.md) und [Pipelines und Aktivitäten in Azure Data Factory](concepts-pipelines-activities.md). |
| activities | Mit Aktivitäten werden Aktionen definiert, die Sie auf Ihre Daten in einer Pipeline anwenden. Aktivitäten für die Datenverschiebung (Kopieraktivität) und Datentransformation (z.B. Hive, Pig und MapReduce) werden unterstützt. | In der aktuellen Version von Data Factory sind Aktivitäten weiterhin definierte Aktionen in einer Pipeline. Die aktuelle Version von Data Factory führt neue [Ablaufsteuerungsaktivitäten](concepts-pipelines-activities.md#control-flow-activities) ein. Sie verwenden diese Aktivitäten in einer Ablaufsteuerung (für Schleifen und Verzweigungen). Aktivitäten für die Datenverschiebung und Datentransformation, die in V1 unterstützt wurden, werden auch in der aktuellen Version unterstützt. Sie können Transformationsaktivitäten definieren, ohne Datasets in der aktuellen Version zu verwenden. |
| Hybriddatenverschiebung und Aktivitätsverteilung | Das [Datenverwaltungsgateway](v1/data-factory-data-management-gateway.md) wird nun als Integration Runtime bezeichnet und hat das Verschieben von Daten zwischen dem lokalen Standort und der Cloud unterstützt.| Das Datenverwaltungsgateway wird jetzt als selbstgehostete Integrationslaufzeit bezeichnet. Die Funktionen sind die gleichen wie in V1. <br/><br/> Die Azure-SSIS-Integration Runtime in der aktuellen Version unterstützt auch das Bereitstellen und Ausführen von SSIS-Paketen (SQL Server Integration Services) in der Cloud. Weitere Informationen finden Sie unter [Integrationslaufzeit in Azure Data Factory](concepts-integration-runtime.md).|
| Parameter | Nicht verfügbar | Parameter sind Schlüssel-Wert-Paare mit schreibgeschützten Konfigurationseinstellungen, die in Pipelines definiert sind. Beim manuellen Ausführen der Pipeline können Sie Argumente für die Parameter übergeben. Wenn Sie einen Planer-Trigger verwenden, kann der Trigger auch Werte für die Parameter übergeben. Die Parameterwerte werden von Aktivitäten in der Pipeline genutzt.  |
| Ausdrücke | In Data Factory V1 können Sie Funktionen und Systemvariablen in Datenauswahlabfragen und Aktivitäts-/Dataseteigenschaften verwenden. | In der aktuellen Version von Data Factory können Sie Ausdrücke an einer beliebigen Stelle eines JSON-Zeichenfolgenwerts verwenden. Weitere Informationen finden Sie unter [Ausdrücke und Funktionen in Azure Data Factory](control-flow-expression-language-functions.md).|
| Pipelineausführungen | Nicht verfügbar | Eine einzelne Instanz einer Pipelineausführung. Beispiel: Angenommen, Sie verfügen über eine Pipeline, die um 8:00, 9:00 und 10:00 Uhr ausgeführt wird. In diesem Fall erfolgen drei separate Ausführungen der Pipeline (Pipelineausführungen). Jede Pipelineausführung besitzt eine eindeutige Pipelineausführungs-ID. Die Pipelineausführungs-ID ist eine GUID, die die jeweilige Pipelineausführung eindeutig definiert. Zur Instanziierung von Pipelineausführungen werden in der Regel Argumente an in Pipelines definierte Parameter übergeben. |
| Aktivitätsausführungen | Nicht verfügbar | Eine Instanz einer Aktivitätsausführung in einer Pipeline. | 
| Triggerausführungen | Nicht verfügbar | Eine Instanz einer Triggerausführung. Weitere Informationen finden Sie unter [Trigger](concepts-pipeline-execution-triggers.md). |
| Scheduling | Die Zeitplanung basiert auf der Start- und Endzeit der Pipeline und der Datasetverfügbarkeit. | Planer-Trigger oder Ausführung über externen Planer. Weitere Informationen finden Sie unter [Pipelineausführung und -trigger](concepts-pipeline-execution-triggers.md). |

Die folgenden Abschnitte enthalten weitere Informationen zu den Funktionen der aktuellen Version. 

## <a name="control-flow"></a>Ablaufsteuerung  
Zur Unterstützung der Integration von vielfältigen Integrationsabläufen und -mustern eines modernen Data Warehouse ermöglicht die aktuelle Version von Data Factory ein neues flexibles Datenpipelinemodell, das nicht mehr an Zeitreihendaten gebunden ist. Einige allgemeine Abläufe, die bislang nicht möglich waren, sind nun möglich. Sie werden in den folgenden Abschnitten beschrieben.   

### <a name="chaining-activities"></a>Verketten von Aktivitäten
In V1 mussten Sie die Ausgabe einer Aktivität als Eingabe einer anderen Aktivität konfigurieren, um sie zu verketten. In der aktuellen Version können Sie Aktivitäten in einer Sequenz innerhalb einer Pipeline verketten. Sie können die **dependsOn**-Eigenschaft in einer Aktivitätsdefinition verwenden, um eine Verkettung mit einer Upstream-Aktivität herzustellen. Weitere Informationen und ein Beispiel finden Sie unter [Pipelines und Aktivitäten in Azure Data Factory](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) sowie unter [Verzweigen und Verketten von Aktivitäten in einer Data Factory-Pipeline](tutorial-control-flow.md). 

### <a name="branching-activities"></a>Verzweigen von Aktivitäten
In der aktuellen Version können Sie Aktivitäten in einer Pipeline verzweigen. Die [Aktivität „If Condition“](control-flow-if-condition-activity.md) bietet die gleiche Funktionalität wie eine `if`-Anweisung in Programmiersprachen. Sie wertet eine Aktivitätengruppe aus, wenn die Bedingung als `true` ausgewertet wird, und eine weitere Aktivitätengruppe, wenn die Bedingung als `false` ausgewertet wird. Beispiele für das Verzweigen von Aktivitäten finden Sie im Tutorial [Verzweigen und Verketten von Aktivitäten in einer Data Factory-Pipeline](tutorial-control-flow.md).

### <a name="parameters"></a>Parameter 
Sie können Parameter auf Pipelineebene definieren und Argumente übergeben, während die Pipelineausführung bei Bedarf oder mithilfe eines Triggers gestartet wird. Aktivitäten können die an die Pipeline übergebenen Argumente nutzen. Weitere Informationen finden Sie unter [Pipelineausführung und Trigger in Azure Data Factory](concepts-pipeline-execution-triggers.md). 

### <a name="custom-state-passing"></a>Übergeben von benutzerdefinierten Zuständen
Aktivitätsausgaben (einschließlich des Zustands) können von einer Folgeaktivität in der Pipeline genutzt werden. In der JSON-Definition einer Aktivität können Sie beispielsweise auf die Ausgabe der vorherigen Aktivität zugreifen, indem Sie die folgende Syntax verwenden: `@activity('NameofPreviousActivity').output.value`. Mit diesem Feature können Sie Workflows erstellen, bei denen Werte Aktivitäten durchlaufen können.

### <a name="looping-containers"></a>Schleifencontainer
Mit der [ForEach](control-flow-for-each-activity.md)-Aktivität wird eine wiederholte Ablaufsteuerung in Ihrer Pipeline definiert. Diese Aktivität durchläuft eine Sammlung und führt angegebene Aktivitäten in einer Schleife aus. Die Schleifenimplementierung dieser Aktivität ähnelt der Foreach-Schleifenstruktur in Programmiersprachen. 

Die [Until](control-flow-until-activity.md)-Aktivität erfüllt die gleiche Funktion wie eine do-until-Schleifenstruktur in Programmiersprachen. Sie führt eine Reihe von Aktivitäten in einer Schleife aus, bis die der Aktivität zugeordnete Bedingung als `true` ausgewertet wird. In Data Factory können Sie einen Timeoutwert für die Until-Aktivität angeben.  

### <a name="trigger-based-flows"></a>Triggerbasierte Abläufe
Pipelines können nach Bedarf (ereignisbasiert, d.h. Blogbeitrag) oder zeitgesteuert ausgelöst werden. Der Artikel [Pipelineausführung und Trigger in Azure Data Factory](concepts-pipeline-execution-triggers.md) enthält ausführliche Informationen zu Triggern. 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>Aufrufen einer Pipeline über eine andere Pipeline
Mit der [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md) kann eine Data Factory-Pipeline eine andere Pipeline aufrufen.

### <a name="delta-flows"></a>Deltaabläufe
Ein Anwendungsfall für Schlüssel in ETL-Mustern sind Deltaladevorgänge. Bei diesen Ladevorgängen werden nur die Daten geladen, die sich seit dem Laden des letzten Durchlaufs einer Pipeline geändert haben. Mit den neuen Funktionen in der aktuellen Version, z.B. [Lookup-Aktivität](control-flow-lookup-activity.md), flexible Zeitplanung und Ablaufsteuerung, wird dieser Anwendungsfall auf natürliche Weise ermöglicht. Ein Tutorial mit einer Schritt-für-Schritt-Anleitung finden Sie unter [Tutorial: Inkrementelles Kopieren](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Weitere Aktivitäten der Ablaufsteuerung
Im Folgenden finden Sie einige weitere Ablaufsteuerungsaktivitäten, die von der aktuellen Version von Data Factory unterstützt werden. 

Steuerungsaktivität | BESCHREIBUNG
---------------- | -----------
[ForEach-Aktivität](control-flow-for-each-activity.md) | Definiert eine wiederholte Ablaufsteuerung in Ihrer Pipeline. Diese Aktivität wird verwendet, um eine Sammlung zu durchlaufen. Sie führt die angegebenen Aktivitäten in einer Schleife aus. Die Schleifenimplementierung dieser Aktivität ähnelt der Foreach-Schleifenstruktur in Programmiersprachen.
[Webaktivität](control-flow-web-activity.md) | Ruft einen benutzerdefinierten REST-Endpunkt über eine Data Factory-Pipeline auf. Sie können Datasets und verknüpfte Dienste zur Verwendung und für den Zugriff durch die Aktivität übergeben. 
[Lookup-Aktivität](control-flow-lookup-activity.md) | Liest oder sucht einen Datensatz- oder Tabellennamenwert in einer beliebigen externen Quelle. Auf die Ausgabe kann durch nachfolgende Aktivitäten verwiesen werden. 
[Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md) | Ruft die Metadaten von beliebigen Daten in Azure Data Factory ab. 
[Warteaktivität](control-flow-wait-activity.md) | Hält die Pipeline für einen bestimmten Zeitraum an.

## <a name="deploy-ssis-packages-to-azure"></a>Bereitstellen von SSIS-Paketen in Azure 
Verwenden Sie Azure-SSIS, wenn Sie Ihre SSIS-Workloads in die Cloud verschieben, eine Data Factory mit der aktuellen Version erstellen und eine Azure-SSIS-Integration Runtime bereitstellen möchten.

Die Azure-SSIS-Integrationslaufzeit ist ein vollständig verwalteter Cluster mit virtuellen Azure-Computern (Knoten), die speziell für die Ausführung von SSIS-Paketen in der Cloud bestimmt sind. Nach dem Bereitstellen einer Azure-SSIS-Integrationslaufzeit können Sie die gleichen Tools verwenden, die Sie auch zum Bereitstellen von SSIS-Paketen in einer lokalen SSIS-Umgebung verwenden. 

So können Sie beispielsweise SQL Server Data Tools oder SQL Server Management Studio verwenden, um SSIS-Pakete für diese Runtime in Azure bereitzustellen. Eine Schritt-für-Schritt-Anleitung finden Sie im Tutorial [Bereitstellen von SQL Server Integration Services-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md). 

## <a name="flexible-scheduling"></a>Flexible Zeitplanung
In der aktuellen Version der Data Factory müssen Sie keine Zeitpläne für die Datasetverfügbarkeit definieren. Sie können eine Triggerressource definieren, mit der Pipelines über ein Taktplaner-Paradigma geplant werden können. Sie können auch Parameter über einen Trigger an Pipelines übergeben, um ein flexibles Modell für die Zeitplanung und Ausführung zu erhalten. 

Pipelines weisen in der aktuellen Version von Data Factory keine „Zeitfenster“ für die Ausführung auf. Die Konzepte „startTime“, „endTime“ und „isPaused“ aus Data Factory V1 sind in der aktuellen Version von Data Factory nicht vorhanden. Weitere Informationen zum Erstellen und anschließenden Planen einer Pipeline in der aktuellen Version von Data Factory finden Sie unter [Pipelineausführung und Trigger in Azure Data Factory](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>Unterstützung für weitere Datenspeicher
Die aktuelle Version unterstützt das Kopieren von Daten für mehr Datenspeicher als V1. Eine Liste mit den unterstützten Datenspeichern finden Sie in den folgenden Artikeln:

- [Version 1 – Unterstützte Datenspeicher](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Aktuelle Version – Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Unterstützung für einen bedarfsgesteuerten Spark-Cluster
Die aktuelle Version unterstützt die Erstellung eines bedarfsgesteuerten Azure HDInsight Spark-Clusters. Geben Sie zum Erstellen eines bedarfsgesteuerten Spark-Clusters den Clustertyp „Spark“ in der Definition Ihres bedarfsgesteuerten verknüpften HDInsight-Diensts an. Anschließend können Sie die Spark-Aktivität in Ihrer Pipeline konfigurieren, um diesen verknüpften Dienst zu verwenden. 

Wenn die Aktivität zur Laufzeit ausgeführt wird, erstellt der Data Factory-Dienst den Spark-Cluster für Sie automatisch. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Transformieren von Daten mit der Spark-Aktivität in Azure Data Factory](transform-data-using-spark.md)
- [Bedarfsgesteuerter verknüpfter Azure HDInsight-Dienst](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Benutzerdefinierte Aktivitäten
In V1 implementieren Sie den Code einer (benutzerdefinierten) DotNet-Aktivität, indem Sie ein .NET-Klassenbibliotheksprojekt mit einer Klasse erstellen, die die Execute-Methode der IDotNetActivity-Schnittstelle implementiert. Aus diesem Grund müssen Sie Ihren benutzerdefinierten Code in .NET Framework 4.5.2 schreiben und ihn auf Windows-basierten Azure Batch-Poolknoten ausführen. 

Für eine benutzerdefinierte Aktivität in der aktuellen Version müssen Sie keine .NET-Schnittstelle implementieren. Sie können Befehle, Skripts und Ihren eigenen benutzerdefinierten, als ausführbare Datei kompilierten Code direkt ausführen. 

Weitere Informationen finden Sie unter [Vergleich: Benutzerdefinierte V2-Aktivität und Version 1 der DotNet-Aktivität (benutzerdefiniert)](transform-data-using-dotnet-custom-activity.md#compare-v2-v1).

## <a name="sdks"></a>SDKs
 Die aktuelle Version von Data Factory verfügt über einen umfassenderen SDK-Satz, der zum Erstellen, Verwalten und Überwachen von Pipelines verwendet werden kann.

- **.NET-SDK**: Das .NET SDK wurde in der aktuellen Version aktualisiert.

- **PowerShell**: Die PowerShell-Cmdlets wurden in der aktuellen Version aktualisiert. Die Cmdlets für die aktuelle Version enthalten **DataFactoryV2** im Namen, z.B. Get-AzDataFactoryV2. 

- **Python SDK**: Dieses SDK ist in der aktuellen Version neu.

- **REST-API**: Die REST-API wurde in der aktuellen Version aktualisiert. 

Die für die aktuelle Version aktualisierten SDKs sind nicht mit V1-Clients abwärtskompatibel. 

## <a name="authoring-experience"></a>Benutzeroberfläche für die Erstellung

| | Version 2 | Version 1 |
| ------ | -- | -- | 
| **Azure portal** | [Ja](quickstart-create-data-factory-portal.md) | Nein |
| **Azure PowerShell** | [Ja](quickstart-create-data-factory-powershell.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-powershell.md) |
| **.NET SDK** | [Ja](quickstart-create-data-factory-dot-net.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-vs.md) |
| **REST-API** | [Ja](quickstart-create-data-factory-rest-api.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-rest-api.md) |
| **Python SDK** | [Ja](quickstart-create-data-factory-python.md) | Nein |
| **Resource Manager: Vorlage** | [Ja](quickstart-create-data-factory-resource-manager-template.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>Rollen und Berechtigungen

Mit der Rolle „Mitwirkender“ der Data Factory-Version 1 können Ressourcen von der aktuellen Version von Data Factory erstellt und verwaltet werden. Weitere Informationen finden Sie unter [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md#data-factory-contributor).

## <a name="monitoring-experience"></a>Benutzeroberfläche für die Überwachung
In der aktuellen Version können Sie Data Factorys auch unterstützen, indem Sie [Azure Monitor](monitor-using-azure-monitor.md) verwenden. Die neuen PowerShell-Cmdlets unterstützen die Überwachung von [Integrationslaufzeiten](monitor-integration-runtime.md). Sowohl V1 als auch V2 unterstützen die visuelle Überwachung unter Verwendung einer Überwachungsanwendung, die über das Azure-Portal gestartet werden kann.


## <a name="next-steps"></a>Nächste Schritte
Ausführliche Informationen zum Erstellen einer Data Factory finden Sie in den folgenden Schnellstartanleitungen: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [REST-API](quickstart-create-data-factory-rest-api.md).