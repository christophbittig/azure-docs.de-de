---
title: Datensammlung und Berichterstellung | Azure Arc-fähige Datendienste
description: Hier wird der Datentyp, der von Arc-fähigen Datendiensten an Microsoft übertragen wird erläutert.
author: dnethi
ms.author: dinethi
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: conceptual
ms.date: 07/30/2021
ms.custom: template-concept
ms.openlocfilehash: 189021997362da8508d2e60c23cc3acb1238ca55
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346061"
---
# <a name="azure-arc-data-services-data-collection-and-reporting"></a>Die Azure Arc Datendienste-Datensammlung und Berichterstellung

In diesem Artikel werden die Daten beschrieben, die Azure Arc-fähige Datendienste an Microsoft übertragen. 


## <a name="related-products"></a>Verwandte Produkte

Die Azure Arc-fähigen Datendienste können einige oder alle der folgenden Produkte verwenden:

- SQL MI – Azure Arc 
- PostgreSQL Hyperscale – Azure Arc
- Azure Data Studio

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

- Azure CLI (az)
- Azure Data CLI (`azdata`) 

## <a name="directly-connected"></a>Direkt verbunden

Wenn ein Cluster für eine direkte Verbindung mit Azure konfiguriert ist, werden einige Daten automatisch an Microsoft übertragen. 

In der folgenden Tabelle werden der Datentyp, die Art des Sendens und die Anforderung beschrieben.  

|Die Datenkategorie|Welche Daten werden gesendet?|Wie werden sie gesendet?|Ist dies erforderlich?
|:----|:----|:----|:----|
|Die operativen Daten|Metriken und Protokolle|Automatisch, wenn dies so konfiguriert ist|No
Die Abrechnungs- und Bestandsdaten|Der Bestand wie z. B. die Anzahl der Instanzen und die Nutzung wie z. B. die Anzahl der verbrauchten virtuellen Kerne|Automatisch |Yes
Diagnose|Die Diagnoseinformationen zu Problembehandlungszwecken|Manuell exportiert und für den Microsoft-Support zur Verfügung gestellt|Das gilt nur für den Bereich der Problembehandlung unter Beachtung der Standard-[Datenschutzrichtlinien](https://privacy.microsoft.com/privacystatement)
Customer Experience Improvement Program (CEIP)|[CEIP-Zusammenfassung](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)|Automatisch, wenn zulässig|No

## <a name="indirectly-connected"></a>Indirekt verbunden

Wenn ein Cluster nicht für eine direkte Verbindung mit Azure konfiguriert ist, überträgt es die betrieblichen Daten oder die Abrechnungs- und Bestandsdaten nicht automatisch an Microsoft. Um Daten an Microsoft zu übertragen, müssen Sie den Export konfigurieren. 

In der folgenden Tabelle werden der Datentyp, die Art des Sendens und die Anforderung beschrieben.  

|Die Datenkategorie|Welche Daten werden gesendet?|Wie werden sie gesendet?|Ist dies erforderlich?
|:----|:----|:----|:----|
|Die operativen Daten|Metriken und Protokolle|Manuell|No
Die Abrechnungs- und Bestandsdaten|Der Bestand wie z. B. die Anzahl der Instanzen und die Nutzung wie z. B. die Anzahl der verbrauchten virtuellen Kerne|Manuell |Ja
Diagnose|Die Diagnoseinformationen zu Problembehandlungszwecken|Manuell exportiert und für den Microsoft-Support zur Verfügung gestellt|Das gilt nur für den Bereich der Problembehandlung unter Beachtung der Standard-[Datenschutzrichtlinien](https://privacy.microsoft.com/privacystatement)
Customer Experience Improvement Program (CEIP)|[CEIP-Zusammenfassung](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)|Automatisch, wenn zulässig|No

## <a name="detailed-description-of-data"></a>Eine detaillierte Beschreibung der Daten

In diesem Abschnitt finden Sie weitere Details zu den Informationen, die mit den Azure Arc-fähigen Datendiensten an Microsoft übertragen werden.

### <a name="operational-data"></a>Betriebliche Daten

Die betrieblichen Daten werden für alle Datenbankinstanzen und für die Arc-fähige Datendienstplattform gesammelt. Es gibt zwei Arten von betrieblichen Daten: 

- Metriken: Die leistungs- und kapazitätsbezogene Metriken, die in einer Influx-Datenbank erfasst werden, die als Teil von Arc-fähigen Datendiensten bereitgestellt wird. Sie können diese Metriken im bereitgestellten Grafana-Dashboard anzeigen. 

- Protokolle: Die Protokolle, die von allen Komponenten ausgegeben werden, einschließlich Fehler-, Warnungs- und Informationsereignissen, werden in einer Elasticsearch-Datenbank gesammelt, die als Teil von Arc-fähigen Datendiensten bereitgestellt wird. Sie können die Protokolle im bereitgestellten Kibana-Dashboard anzeigen. 

Die lokal gespeicherten operativen Daten erfordern integrierte Administratorrechte, um sie in Grafana/Kibana anzeigen zu können. 

Die operativen Daten verlassen Ihre Umgebung nicht, es sei denn, Sie entscheiden sich für den Export/Upload (indirekt verbundener Modus) oder das automatische Senden (direkt verbundener Modus) der Daten an Azure Monitor/Log Analytics. Die Daten werden in einem Log Analytics-Arbeitsbereich gespeichert, den Sie steuern. 

Wenn die Daten an Azure Monitor oder Log Analytics gesendet werden, können Sie auswählen, in welcher Azure-Region oder in welchem Rechenzentrum sich der Log Analytics-Arbeitsbereich befindet. Danach kann der Zugriff zum Anzeigen oder Kopieren aus anderen Speicherorten von Ihnen gesteuert werden. 

### <a name="billing-and-inventory-data"></a>Die Abrechnungs- und Bestandsdaten 

Die Abrechnungsdaten werden für die Nachverfolgung der abrechnungsfähigen Nutzung verwendet. Diese Daten sind für die Ausführung des Diensts unerlässlich und müssen manuell oder automatisch in alle Modi übertragen werden. 

Jede Datenbankinstanz und der Datencontroller werden in Azure als eine Azure-Ressource im Azure Resource Manager widergespiegelt. 

Es gibt drei Typen von Ressourcen: 

- Die Arc-fähige SQL verwaltete Instanz 
- Die Arc-fähige PostgreSQL Hyperscale Servergruppen 
- SQL Server auf Azure Arc-fähigen Servern 
- Datencontroller 

In den folgenden Abschnitten werden die Eigenschaften, Typen und Beschreibungen gezeigt, die über jeden Ressourcentyp gesammelt und gespeichert werden: 

### <a name="sql-server-on-azure-arc-enabled-servers"></a>SQL Server auf Azure Arc-fähigen Servern 
- Die SQL Server-Edition. 
   - `string: Edition` 
- Die Ressourcen-ID der Containerressource (Azure Arc für Server). 
   - `string: ContainerResourceId` 
- Der Zeitpunkt, an dem die Ressource erstellt wurde. 
   - `string: CreateTime` 
- Die Anzahl der logischen Prozessoren, die von der SQL Server Instanz verwendet werden.
   - `string: VCore` 
- Cloud connectivity status. 
   - `string: Status` 
- SQL Server update level. 
   - `string: PatchLevel` 
- SQL Server collation. 
   - `string: Collation`
- Die aktuelle Version des Microsoft SQL Servers.
   - `string: CurrentVersion`
- Der SQL Server-Instanzname. 
   - `string: InstanceName`
- Dynamic TCP ports used by SQL Server. 
   - `string: TcpDynamicPorts`
- Static TCP ports used by SQL Server.
   - `string: TcpStaticPorts` 
- SQL Server product ID.
   - `string: ProductId`
- Der SQL Server Bereitstellungszustand.
   - `string: ProvisioningState`

### <a name="data-controller"></a>Datencontroller 

- Standortinformationen
   - `public OnPremiseProperty OnPremiseProperty` 
- Die unbearbeiteten Kubernetes-Informationen (`kubectl get datacontroller`) 
   - `object: K8sRaw` 
- Last uploaded date from on-premises cluster.
   - `System.DateTime: LastUploadedDate` 
- Der Zustand des Datencontrollers
   - `string: ProvisioningState` 

### <a name="postgresql-hyperscale-server-group"></a>Die PostgreSQL Hyperscale-Servergruppe 

- Die Datencontroller- ID
   - `string: DataControllerId`
- Der Name des Instanzadministrators
   - `string: Admin`
- Der Benutzername und das Kennwort für die Standardauthentifizierung
   - `kubectl get postgres12` – Die unbearbeiteten Kubernetes-Informationen (`public: BasicLoginInformation BasicLoginInformation`) 
   - `object: K8sRaw` –  Last uploaded date from on premises cluster. 
   - `System.DateTime: LastUploadedDate` 
- Der Bereitstellungszustand der Gruppe
   - `string: ProvisioningState` 

### <a name="sql-managed-instance"></a>Verwaltete SQL-Instanz 

- Die verwaltete Instanz-ID
   - `public string: DataControllerId` 
- Der Benutzername des Instanzadministrators 
   - `string: Admin` 
- Die Startzeit der Instanz 
   - `string: StartTime`
- Die Beendigungszeit der Instanz 
   - `string: EndTime` 
- Die unbearbeiteten Kubernetes -Informationen (`kubectl get sqlmi`) 
   - `object: K8sRaw` 
- Der Benutzername und das Kennwort für die Standardauthentifizierung. 
   - `public: BasicLoginInformation BasicLoginInformation`
- Last uploaded date from on-premises cluster. 
   - `public: System.DateTime LastUploadedDate` 
- Der Bereitstellungszustand der verwalteten SQL-Instanz
   - `public string: ProvisioningState` 

### <a name="examples"></a>Beispiele

Ein Beispiel für ein JSON-Dokument mit den Ressourcenbestandsdaten, das an Azure gesendet wird, um die Azure-Ressourcen in Ihrem Abonnement zu erstellen. 

```json
{ 

        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711", 

        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373", 

        "instanceName": "sqlInstance001", 

        "instanceNamespace": "arc", 

        "instanceType": "<resource>", 

        "location": "eastus", 

        "resourceGroupName": "production-resources", 

        "subscriptionId": "<subscription_id>", 

        "isDeleted": false, 

        "externalEndpoint": "32.191.39.83:1433", 

        "vCores": "2", 

        "createTimestamp": "05/29/2020 23:13:17", 

        "updateTimestamp": "05/29/2020 23:13:17" 

    } 
```

 

Die Abrechnungsdaten erfassen die Startzeit („erstellt“) und die Beendigungszeit („gelöscht“) einer bestimmten Instanz sowie alle Start- und Uhrzeitwerte. Das geschieht immer, wenn eine Änderung der Anzahl von Kernen erfolgt, die für eine bestimmte Instanz verfügbar („Kernlimit“) sind. 

```json
{ 

          "requestType": "usageUpload", 

          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d", 

          "name": "DataControllerTestName", 

          "subscriptionId": "<subscription_id>", 

          "resourceGroup": "production-resources", 

          "location": "eastus", 

          "uploadRequest": { 

            "exportType": "usages", 

            "dataTimestamp": "2020-06-17T22:32:24Z", 

            "data": "[{\"name\":\"sqlInstance001\", 

                       \"namespace\":\"arc\", 

                       \"type\":\"<resource type>\", 

                       \"eventSequence\":1,  

                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\", 

                       \"startTime\":\"2020-06-17T19:11:47.7533333\", 

                       \"endTime\":\"2020-06-17T19:59:00\", 

                       \"quantity\":1, 

                       \"id\":\"<subscription_id>\"}]", 

           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK" 

```

### <a name="diagnostic-data"></a>Diagnosedaten

In Support-Situationen werden Sie möglicherweise aufgefordert, Ihre Datenbankinstanz-Protokolle, Kubernetes-Protokolle und andere Diagnoseprotokolle zur Verfügung zu stellen. Das Supportteam stellt Ihnen einen sicheren Speicherort für den Upload zur Verfügung. Die dynamischen Verwaltungsansichten (DMVs) können auch die Diagnosedaten bereitstellen. Die verwendeten DMVs oder Abfragen können die Details zu den Datenbankschema-Metadaten enthalten, aber in der Regel keine Kundendaten. Die Diagnosedaten enthalten keine Kennwörter, Cluster-IPs oder individuell identifizierbaren Daten. Diese werden bereinigt, und die Protokolle werden nach Möglichkeit für die Speicherung anonymisiert. Sie werden nicht automatisch übertragen, und der Administrator muss sie manuell hochladen. 

|Feldname  |Notizen  |
|---------|---------|
|Fehlerprotokolle |Die Protokolldateien, die Fehler erfassen können, Kunden- oder personenbezogene Daten enthalten (siehe unten), sind eingeschränkt und werden vom Benutzer freigegeben |
|DMVs      |Die dynamischen Verwaltungssichten können Abfrage- und Abfragepläne enthalten, werden jedoch vom Benutzer eingeschränkt und freigegeben     |
|Ansichten    |Die Ansichten können Kundendaten enthalten, sie sind jedoch eingeschränkt und werden nur vom Benutzer freigegeben     |
|Absturzabbilder – Kundendaten | Die maximale Aufbewahrungsdauer für die Absturzabbilder ist 30 Tage – sie kann Zugriffssteuerungsdaten enthalten <br/><br/> Es können sich Statistikobjekte, Datenwerte in Zeilen und Abfragetexte in Kundenabsturzabbildern befinden    |
|Absturzabbilder – personenbezogene Daten | Computer, Anmeldungen/Benutzernamen, E-Mails, Standortinformationen, Kundenidentifikation – erfordern die Zustimmung des Benutzers  |

## <a name="next-steps"></a>Nächste Schritte
[Hochladen von Nutzungsdaten in Azure Monitor](upload-usage-data.md)
