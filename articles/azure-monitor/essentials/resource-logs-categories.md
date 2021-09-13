---
title: 'Azure Monitor-Ressourcenprotokolle: unterstützte Dienste und Kategorien'
description: 'Referenz zu Azure Monitor: Erläuterung der unterstützten Dienste und Ereignisschemas für Azure-Ressourcenprotokolle.'
ms.topic: reference
ms.date: 08/04/2021
ms.openlocfilehash: 2cbe701f3fbedacf29124c823743966be1f7ba32
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122343083"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Unterstützte Kategorien für Azure-Ressourcenprotokolle

> [!NOTE]
> Diese Liste wird größtenteils automatisch generiert. Jegliche Änderungen, die über GitHub an dieser Liste vorgenommen werden, können ohne Warnung überschrieben werden. Informationen dazu, wie die Liste dauerhaft aktualisiert werden kann, erhalten Sie beim Autor dieses Artikels.

[Azure Monitor-Ressourcenprotokolle](../essentials/platform-logs-overview.md) sind von Azure-Diensten ausgegebene Protokolle, die die Vorgänge der jeweiligen Dienste oder Ressourcen beschreiben. Für alle Ressourcenprotokolle, die über Azure Monitor verfügbar sind, wird ein Schema der obersten Ebene gemeinsam genutzt. Auf diese Weise kann jeder Dienst für seine eigenen Ereignisse flexibel eindeutige Eigenschaften ausgeben.

Ressourcenprotokolle wurden zuvor als Diagnoseprotokolle bezeichnet. Der Name wurde im Oktober 2019 geändert, da die Typen der von Azure Monitor gesammelten Protokolle nicht mehr nur die Azure-Ressource umfassen.

Ein Schema wird mit einer Kombination aus dem Ressourcentyp (in der `resourceId`-Eigenschaft verfügbar) und dem `category`-Element eindeutig identifiziert. Es gibt ein allgemeines Schema für alle Ressourcenprotokolle mit dienstspezifischen Feldern, die dann für verschiedene Protokollkategorien hinzugefügt werden. Weitere Informationen finden Sie unter [Allgemeines und dienstspezifisches Schema für Azure-Ressourcenprotokolle]().

## <a name="costs"></a>Kosten

Bei [Azure Monitor Log Analytics](https://azure.microsoft.com/pricing/details/monitor/), [Azure Storage](https://azure.microsoft.com/product-categories/storage/), [Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/) und Partnern, die direkt mit Azure Monitor integrieren ([z. B. Datadog](../../partner-solutions/datadog/overview.md)), fallen Kosten für das Erfassen und Speichern von Daten an. Nutzen Sie die vorherigen Links zu Preisseiten für diese Dienste, um die Kosten nachvollziehen zu können. Ressourcenprotokolle sind nur ein Datentyp, den Sie an diese Speicherorte senden können. 

Zusätzlich fallen möglicherweise Kosten für den Export von einigen Kategorien von Ressourcenprotokollen in diese Speicherorte an. Die Protokolle, die eventuell Exportkosten verursachen, sind in der folgenden Tabelle aufgeführt. Weitere Informationen zu den Preisen für das Exportieren finden Sie im Abschnitt *Plattformprotokolle* auf der Seite [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="supported-log-categories-per-resource-type"></a>Unterstützte Protokollkategorien pro Ressourcentyp

Es folgt eine Liste der Arten von Protokollen, die für jeden Ressourcentyp verfügbar sind. 

Einige Kategorien werden möglicherweise nur für bestimmte Ressourcentypen unterstützt. Wenn Sie der Meinung sind, dass eine Ressource fehlt, lesen Sie die ressourcenspezifische Dokumentation. Beispielsweise sind Kategorien des Typs „Microsoft.Sql/servers/databases“ nicht für alle Datenbanktypen verfügbar. Weitere Informationen finden Sie unter den [Informationen zur SQL-Datenbank-Diagnoseprotokollierung](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Wenn Sie der Meinung sind, dass noch etwas fehlt, können Sie unten in diesem Artikel einen GitHub-Kommentar öffnen.


## <a name="microsoftaaddomainservices"></a>Microsoft.AAD/DomainServices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AccountLogon|AccountLogon|Nein|
|AccountManagement|AccountManagement|Nein|
|DetailTracking|DetailTracking|Nein|
|DirectoryServiceAccess|DirectoryServiceAccess|Nein|
|LogonLogoff|LogonLogoff|Nein|
|ObjectAccess|ObjectAccess|Nein|
|PolicyChange|PolicyChange|Nein|
|PrivilegeUse|PrivilegeUse|Nein|
|SystemSecurity|SystemSecurity|Nein|


## <a name="microsoftaadiamtenants"></a>microsoft.aadiam/tenants

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Signin|Signin|Ja|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Engine|Engine|Nein|
|Dienst|Dienst|Nein|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|GatewayLogs|Protokolle im Zusammenhang mit dem ApiManagement-Gateway|Nein|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Audit|Audit|Ja|
|HttpRequest|HTTP-Anforderungen|Ja|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|ApplicationConsole|Anwendungskonsole|Nein|
|SystemLogs|Systemprotokolle|Nein|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AuditEvent|Protokollkategorie für AuditEvent-Meldungen|Nein|
|ERR|Protokollkategorie für Fehlermeldungen|Nein|
|INF|Protokollkategorie für Informationsmeldungen|Nein|
|WRN|Protokollkategorie für Warnmeldungen|Nein|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DscNodeStatus|DSC-Knotenstatus|Nein|
|JobLogs|Auftragsprotokolle|Nein|
|JobStreams|Auftragsdatenströme|Nein|


## <a name="microsoftautonomousdevelopmentplatformaccounts"></a>Microsoft.AutonomousDevelopmentPlatform/accounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Audit|Audit|Ja|
|Bei Betrieb|Bei Betrieb|Ja|
|Anforderung|Anforderung|Ja|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|ServiceLog|Dienstprotokolle|Nein|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|BlockchainApplication|Blockchainanwendung|Nein|
|FabricOrderer|Fabric-Orderer|Nein|
|FabricPeer|Fabric-Peer|Nein|
|Proxy|Proxy|Nein|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|BlockchainApplication|Blockchainanwendung|Nein|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|BotRequest|Anforderungen von den Kanälen an den Bot|Nein|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|WebApplicationFirewallLogs|Web Application Firewall-Protokolle|Nein|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AzureCdnAccessLog|Azure CDN-Zugriffsprotokoll|Nein|
|FrontDoorAccessLog|FrontDoor-Zugriffsprotokoll|Ja|
|FrontDoorHealthProbeLog|FrontDoor-Integritätstestprotokoll|Ja|
|FrontDoorWebApplicationFirewallLog|FrontDoor-Web Application Firewall-Protokoll|Ja|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|CoreAnalytics|Ruft die Metriken des Endpunkts ab, z.B. Bandbreite, ausgehenden Datenverkehr usw.|Nein|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Regelflussereignis der Netzwerksicherheitsgruppe|Regelflussereignis der Netzwerksicherheitsgruppe|Nein|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Audit|Überwachungsprotokolle|Nein|
|RequestResponse|Anforderungs- und Antwortprotokolle|Nein|
|Trace|Ablaufverfolgungsprotokolle|Nein|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AuthOperational|Protokolle zum Authentifizierungsbetrieb|Ja|
|CallDiagnosticsPRIVATEPREVIEW|Anrufdiagnoseprotokolle – PRIVATE VORSCHAU|Ja|
|CallSummaryPRIVATEPREVIEW|Anrufzusammenfassungsprotokolle – PRIVATE VORSCHAU|Ja|
|ChatOperational|Protokolle zum Chatbetrieb|Nein|
|SMSOperational|Protokolle zum SMS-Betrieb|Nein|
|Verwendung|Verwendungsdatensätze|Nein|


## <a name="microsoftconnectedvehicleplatformaccounts"></a>Microsoft.ConnectedVehicle/platformAccounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Protokolle|MCVP-Protokolle|Ja|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|ContainerRegistryLoginEvents|Anmeldeereignisse|Nein|
|ContainerRegistryRepositoryEvents|RepositoryEvent-Protokolle|Nein|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|cluster-autoscaler|Automatische Kubernetes-Clusterskalierung|Nein|
|guard|guard|Nein|
|kube-apiserver|Kubernetes-API-Server|Nein|
|kube-audit|Kubernetes-Überwachung|Nein|
|kube-audit-admin|Protokolle des Kubernetes-Überwachungsadministrators|Nein|
|kube-controller-manager|Kubernetes-Controller-Manager|Nein|
|kube-scheduler|Kubernetes-Scheduler|Nein|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AuditLogs|Überwachungsprotokolle für MiniRP-Aufrufe|Nein|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft.D365CustomerInsights/instances

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Audit|Überwachen von Ereignissen|Nein|
|Bei Betrieb|Betriebsereignisse|Nein|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|accounts|Databricks-Konten|Nein|
|clusters|Databricks-Cluster|Nein|
|dbfs|Databricks-Dateisystem|Nein|
|featureStore|Featurespeicher von Databricks|Ja|
|genie|Databricks-Genie|Ja|
|globalInitScripts|Globale Databricks-Init-Skripts|Ja|
|iamRole|Databricks-IAM-Rolle|Ja|
|instancePools|Instanzenpools|Nein|
|jobs|Databricks-Aufträge|Nein|
|mlflowAcledArtifact|Databricks-MLFlow-Artefakt in ACL|Ja|
|mlflowExperiment|Databricks-MLFlow-Experiment|Ja|
|Notebook|Databricks-Notebook|Nein|
|RemoteHistoryService|Databricks-Remoteverlaufsdienst|Ja|
|secrets|Databricks-Geheimnisse|Nein|
|sqlanalytics|Databricks-SQL-Analysen|Ja|
|sqlPermissions|Databricks-SQL-Berechtigungen|Nein|
|ssh|Databricks-SSH|Nein|
|Arbeitsbereich|Databricks-Arbeitsbereich|Nein|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|CollaborationAudit|Zusammenarbeitsüberwachung|Ja|
|DataAssets|Datenressourcen|Nein|
|Pipelines|Pipelines|Nein|
|Proposals|Vorschläge|Nein|
|Skripts|Skripts|Nein|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|ActivityRuns|Pipeline-Aktivitätsausführungsprotokoll|Nein|
|PipelineRuns|Pipelineausführungsprotokoll|Nein|
|SandboxActivityRuns|Sandbox-Aktivitätsausführungsprotokoll|Ja|
|SandboxPipelineRuns|Sandbox-Pipelineausführungsprotokoll|Ja|
|SSISIntegrationRuntimeLogs|SSIS Integration Runtime-Protokolle|Nein|
|SSISPackageEventMessageContext|Kontext von SSIS-Paketereignismeldungen|Nein|
|SSISPackageEventMessages|SSIS-Paketereignismeldungen|Nein|
|SSISPackageExecutableStatistics|Statistiken zu ausführbaren Dateien in SSIS-Paketen|Nein|
|SSISPackageExecutionComponentPhases|Komponentenphasen von SSIS-Paketausführungen|Nein|
|SSISPackageExecutionDataStatistics|Statistiken von SSIS-Paketausführungsdaten|Nein|
|TriggerRuns|Triggerausführungsprotokoll|Nein|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Audit|Überwachungsprotokolle|Nein|
|Requests|Anforderungsprotokolle|Nein|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Audit|Überwachungsprotokolle|Nein|
|Requests|Anforderungsprotokolle|Nein|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|ReceivedShareSnapshots|Empfangene Freigabemomentaufnahmen|Nein|
|SentShareSnapshots|Gesendete Freigabemomentaufnahmen|Nein|
|Freigaben|Freigaben|Nein|
|ShareSubscriptions|Freigabeabonnements|Nein|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|MySqlAuditLogs|MariaDB-Überwachungsprotokolle|Nein|
|MySqlSlowLogs|MariaDB-Serverprotokolle|Nein|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|MySqlAuditLogs|MySQL-Überwachungsprotokolle|Nein|
|MySqlSlowLogs|Protokolle zu langsamen MySQL-Abfragen|Nein|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|MySqlAuditLogs|MySQL-Überwachungsprotokolle|Nein|
|MySqlSlowLogs|MySQL Server-Protokolle|Nein|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-Serverprotokolle|Nein|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft.DBForPostgreSQL/serverGroupsv2

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-Serverprotokolle|Ja|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-Serverprotokolle|Nein|
|QueryStoreRuntimeStatistics|Laufzeitstatistik des PostgreSQL-Abfragespeichers|Nein|
|QueryStoreWaitStatistics|Wartestatistik des PostgreSQL-Abfragespeichers|Nein|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|PostgreSQLLogs|PostgreSQL-Serverprotokolle|Nein|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Prüfpunkt|Prüfpunkt|Nein|
|Fehler|Fehler|Nein|
|Verwaltung|Verwaltung|Nein|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|Nein|
|Prüfpunkt|Prüfpunkt|Nein|
|Verbindung|Verbindung|Nein|
|Fehler|Fehler|Nein|
|HostRegistration|HostRegistration|Nein|
|Verwaltung|Verwaltung|Nein|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Prüfpunkt|Prüfpunkt|Nein|
|Fehler|Fehler|Nein|
|Feed|Feed|Nein|
|Verwaltung|Verwaltung|Nein|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|C2DCommands|C2D-Befehle|Nein|
|C2DTwinOperations|C2D-Zwillingsvorgänge|Nein|
|Configurations|Configurations|Nein|
|Verbindungen|Verbindungen|Nein|
|D2CTwinOperations|D2CTwinOperations|Nein|
|DeviceIdentityOperations|Geräteidentitätsvorgänge|Nein|
|DeviceStreams|Gerätestreams (Vorschau)|Nein|
|DeviceTelemetry|Gerätetelemetrie|Nein|
|DirectMethods|Direkte Methoden|Nein|
|DistributedTracing|Verteilte Ablaufverfolgung (Vorschauversion)|Nein|
|FileUploadOperations|Dateiuploadvorgänge|Nein|
|JobsOperations|Auftragsvorgänge|Nein|
|Routen|Routen|Nein|
|TwinQueries|Zwillingsabfragen|Nein|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|C2DCommands|C2D-Befehle|Nein|
|C2DTwinOperations|C2D-Zwillingsvorgänge|Nein|
|Configurations|Configurations|Nein|
|Verbindungen|Verbindungen|Nein|
|D2CTwinOperations|D2CTwinOperations|Nein|
|DeviceIdentityOperations|Geräteidentitätsvorgänge|Nein|
|DeviceStreams|Gerätestreams (Vorschau)|Nein|
|DeviceTelemetry|Gerätetelemetrie|Nein|
|DirectMethods|Direkte Methoden|Nein|
|DistributedTracing|Verteilte Ablaufverfolgung (Vorschauversion)|Nein|
|FileUploadOperations|Dateiuploadvorgänge|Nein|
|JobsOperations|Auftragsvorgänge|Nein|
|Routen|Routen|Nein|
|TwinQueries|Zwillingsabfragen|Nein|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DeviceOperations|Gerätevorgänge|Nein|
|ServiceOperations|Dienstoperationen|Nein|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|Nein|
|EventRoutesOperation|EventRoutesOperation|Nein|
|ModelsOperation|ModelsOperation|Nein|
|QueryOperation|QueryOperation|Nein|
|ResourceProviderOperation|ResourceProviderOperation|Ja|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/DatabaseAccounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|CassandraRequests|CassandraRequests|Nein|
|ControlPlaneRequests|ControlPlaneRequests|Nein|
|DataPlaneRequests|DataPlaneRequests|Nein|
|GremlinRequests|GremlinRequests|Nein|
|MongoRequests|MongoRequests|Nein|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|Nein|
|PartitionKeyStatistics|PartitionKeyStatistics|Nein|
|QueryRuntimeStatistics|QueryRuntimeStatistics|Nein|
|TableApiRequests|TableApiRequests|Ja|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DeliveryFailures|Protokolle für Zustellungsfehler|Nein|
|PublishFailures|Protokolle für Veröffentlichungsfehler|Nein|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DeliveryFailures|Protokolle für Zustellungsfehler|Nein|
|PublishFailures|Protokolle für Veröffentlichungsfehler|Nein|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DeliveryFailures|Protokolle für Zustellungsfehler|Nein|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DeliveryFailures|Protokolle für Zustellungsfehler|Nein|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DeliveryFailures|Protokolle für Zustellungsfehler|Nein|
|PublishFailures|Protokolle für Veröffentlichungsfehler|Nein|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|ArchiveLogs|Archivprotokolle|Nein|
|AutoScaleLogs|Protokolle zur automatischen Skalierung|Nein|
|CustomerManagedKeyUserLogs|Protokolle für kundenseitig verwalteten Schlüssel|Nein|
|EventHubVNetConnectionEvent|VNet/IP-Filterung-Verbindungsprotokolle|Nein|
|KafkaCoordinatorLogs|Kafka-Koordinatorprotokolle|Nein|
|KafkaUserErrorLogs|Kafka-Benutzerfehlerprotokolle|Nein|
|OperationalLogs|Betriebsprotokolle|Nein|


## <a name="microsoftexperimentationexperimentworkspaces"></a>microsoft.experimentation/experimentWorkspaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Anforderung|Anforderung|Nein|


## <a name="microsofthealthcareapisworkspacesdicomservices"></a>Microsoft.HealthcareApis/workspaces/dicomservices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AuditLogs|Überwachungsprotokolle|Ja|


## <a name="microsofthealthcareapisworkspacesfhirservices"></a>Microsoft.HealthcareApis/workspaces/fhirservices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AuditLogs|FHIR-Überwachungsprotokolle|Ja|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AutoscaleEvaluations|Bewertungen der Autoskalierung|Nein|
|AutoscaleScaleActions|Skalierungsaktionen der Autoskalierung|Nein|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AppAvailabilityResults|Verfügbarkeitsergebnisse|Nein|
|AppBrowserTimings|Browserzeiten|Nein|
|AppDependencies|Abhängigkeiten|Nein|
|AppEvents|Ereignisse|Nein|
|AppExceptions|Ausnahmen|Nein|
|AppMetrics|Metriken|Nein|
|AppPageViews|Seitenaufrufe|Nein|
|AppPerformanceCounters|Leistungsindikatoren|Nein|
|AppRequests|Requests|Nein|
|AppSystemEvents|Systemereignisse|Nein|
|AppTraces|Traces|Nein|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AuditEvent|Überwachungsereignis|Nein|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AuditEvent|Überwachungsprotokolle|Nein|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Befehl|Befehl|Nein|
|FailedIngestion|Fehlgeschlagene Erfassungsvorgänge|Nein|
|IngestionBatching|Batchverarbeitung der Datenerfassung|Nein|
|Abfrage|Abfrage|Nein|
|SucceededIngestion|Erfolgreiche Erfassungsvorgänge|Nein|
|TableDetails|Tabellendetails|Nein|
|TableUsageStatistics|Tabellenverwendungsstatistik|Nein|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/IntegrationAccounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|IntegrationAccountTrackingEvents|Integrationskonto –Nachverfolgen von Ereignissen|Nein|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/Workflows

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|WorkflowRuntime|Diagnoseereignisse zur Workflowlaufzeit|Nein|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|Nein|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|Nein|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|Nein|
|AmlComputeJobEvent|AmlComputeJobEvent|Nein|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|Nein|
|ComputeInstanceEvent|ComputeInstanceEvent|Ja|
|DataLabelChangeEvent|DataLabelChangeEvent|Ja|
|DataLabelReadEvent|DataLabelReadEvent|Ja|
|DataSetChangeEvent|DataSetChangeEvent|Ja|
|DataSetReadEvent|DataSetReadEvent|Ja|
|DataStoreChangeEvent|DataStoreChangeEvent|Ja|
|DataStoreReadEvent|DataStoreReadEvent|Ja|
|DeploymentEventACI|DeploymentEventACI|Ja|
|DeploymentEventAKS|DeploymentEventAKS|Ja|
|DeploymentReadEvent|DeploymentReadEvent|Ja|
|EnvironmentChangeEvent|EnvironmentChangeEvent|Ja|
|EnvironmentReadEvent|EnvironmentReadEvent|Ja|
|InferencingOperationACI|InferencingOperationACI|Ja|
|InferencingOperationAKS|InferencingOperationAKS|Ja|
|ModelsActionEvent|ModelsActionEvent|Ja|
|ModelsChangeEvent|ModelsChangeEvent|Ja|
|ModelsReadEvent|ModelsReadEvent|Ja|
|PipelineChangeEvent|PipelineChangeEvent|Ja|
|PipelineReadEvent|PipelineReadEvent|Ja|
|RunEvent|RunEvent|Ja|
|RunReadEvent|RunReadEvent|Ja|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|KeyDeliveryRequests|Schlüsselübermittlungsanforderungen|Nein|
|MediaAccount|Integritätsstatus des Medienkontos|Ja|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationgateways

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|ApplicationGatewayAccessLog|Application Gateway-Zugriffsprotokoll|Nein|
|ApplicationGatewayFirewallLog|Application Gateway-Firewallprotokoll|Nein|
|ApplicationGatewayPerformanceLog|Application Gateway-Leistungsprotokoll|Nein|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AzureFirewallApplicationRule|Azure Firewall-Anwendungsregel|Nein|
|AzureFirewallDnsProxy|Azure Firewall-DNS-Proxy|Nein|
|AzureFirewallNetworkRule|Azure Firewall-Netzwerkregel|Nein|


## <a name="microsoftnetworkbastionhosts"></a>microsoft.network/bastionHosts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|BastionAuditLogs|Bastion-Überwachungsprotokolle|Nein|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|PeeringRouteLog|Routentabellenprotokolle zum Peering|Nein|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|FrontdoorAccessLog|Frontdoor-Zugriffsprotokoll|Nein|
|FrontdoorWebApplicationFirewallLog|Frontdoor-Web Application Firewall-Protokoll|Nein|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|LoadBalancerAlertEvent|Load Balancer-Warnereignisse|Nein|
|LoadBalancerProbeHealthStatus|Integritätsstatus der Load Balancer-Stichprobe|Nein|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|NetworkSecurityGroupEvent|Ereignis der Netzwerksicherheitsgruppe|Nein|
|NetworkSecurityGroupFlowEvent|Regelflussereignis der Netzwerksicherheitsgruppe|Nein|
|NetworkSecurityGroupRuleCounter|Regelzähler der Netzwerksicherheitsgruppe|Nein|


## <a name="microsoftnetworkp2svpngateways"></a>microsoft.network/p2svpngateways

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|GatewayDiagnosticLog|Gatewaydiagnoseprotokolle|Nein|
|IKEDiagnosticLog|IKE-Diagnoseprotokolle|Nein|
|P2SDiagnosticLog|P2S-Diagnoseprotokolle|Nein|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DDoSMitigationFlowLogs|Datenflussprotokolle von Entscheidungen zur DDoS-Risikominderung|Nein|
|DDoSMitigationReports|Berichte zur DDoS-Risikominderung|Nein|
|DDoSProtectionNotifications|DDoS-Schutz-Benachrichtigungen|Nein|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|ProbeHealthStatusEvents|Traffic Manager-Testintegritätsergebnisse (Ereignis)|Nein|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>microsoft.network/virtualnetworkgateways

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|GatewayDiagnosticLog|Gatewaydiagnoseprotokolle|Nein|
|IKEDiagnosticLog|IKE-Diagnoseprotokolle|Nein|
|P2SDiagnosticLog|P2S-Diagnoseprotokolle|Nein|
|RouteDiagnosticLog|Routendiagnoseprotokolle|Nein|
|TunnelDiagnosticLog|Tunneldiagnoseprotokolle|Nein|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|VMProtectionAlerts|VM-Schutz-Warnungen|Nein|


## <a name="microsoftnetworkvpngateways"></a>microsoft.network/vpngateways

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|GatewayDiagnosticLog|Gatewaydiagnoseprotokolle|Nein|
|IKEDiagnosticLog|IKE-Diagnoseprotokolle|Nein|
|RouteDiagnosticLog|Routendiagnoseprotokolle|Nein|
|TunnelDiagnosticLog|Tunneldiagnoseprotokolle|Nein|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|OperationalLogs|Betriebsprotokolle|Nein|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Audit|Audit|Ja|


## <a name="microsoftpowerbitenants"></a>Microsoft.PowerBI/tenants

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Engine|Engine|Nein|

## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft.PowerBI/tenants/workspaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Engine|Engine|Nein|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Engine|Engine|Nein|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DataSensitivityLogEvent|DataSensitivity|Ja|
|ScanStatusLogEvent|ScanStatus|Nein|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AddonAzureBackupAlerts|Add-On: Azure Backup-Warnungsdaten|Nein|
|AddonAzureBackupJobs|Add-On: Azure Backup-Auftragsdaten|Nein|
|AddonAzureBackupPolicy|Add-On: Azure Backup-Richtliniendaten|Nein|
|AddonAzureBackupProtectedInstance|Add-On: Azure Backup-Daten für geschützte Instanz|Nein|
|AddonAzureBackupStorage|Add-On: Azure Backup-Speicherdaten|Nein|
|AzureBackupReport|Azure Backup-Berichtsdaten|Nein|
|AzureSiteRecoveryEvents|Azure Site Recovery-Ereignisse|Nein|
|AzureSiteRecoveryJobs|Azure Site Recovery-Aufträge|Nein|
|AzureSiteRecoveryProtectedDiskDataChurn|Datenänderungen auf mit Azure Site Recovery geschützten Datenträgern|Nein|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery-Wiederherstellungspunkte|Nein|
|AzureSiteRecoveryReplicatedItems|Replizierte Azure Site Recovery-Elemente|Nein|
|AzureSiteRecoveryReplicationDataUploadRate|Uploadrate für Azure Site Recovery-Replikationsdaten|Nein|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery-Replikationsstatistiken|Nein|
|CoreAzureBackup|Kern: Azure Backup-Daten|Nein|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|HybridConnectionsEvent|HybridConnections Events|Nein|
|HybridConnectionsLogs|HybridConnectionsLogs|Nein|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|OperationLogs|Vorgangsprotokolle|Nein|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|OperationalLogs|Betriebsprotokolle|Nein|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AllLogs|Protokolle für Azure SignalR Service|Nein|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft.SignalRService/WebPubSub

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AllLogs|Azure Web PubSub-Dienstprotokolle|Ja|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DevOpsOperationsAudit|Überwachungsprotokolle für DevOps-Vorgänge|Nein|
|ResourceUsageStats|Nutzungsstatistiken zu Ressourcen|Nein|
|SQLSecurityAuditEvents|SQL-Sicherheitsüberwachungsereignis|Nein|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Errors|Errors|Nein|
|QueryStoreRuntimeStatistics|Laufzeitstatistik des Abfragespeichers|Nein|
|QueryStoreWaitStatistics|Wartestatistik des Abfragespeichers|Nein|
|SQLInsights|SQL-Informationen|Nein|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AutomaticTuning|Automatische Optimierung|Nein|
|Blöcke|Blöcke|Nein|
|DatabaseWaitStatistics|Wartestatistik der Datenbank|Nein|
|Deadlocks|Deadlocks|Nein|
|DevOpsOperationsAudit|Überwachungsprotokolle für DevOps-Vorgänge|Nein|
|DmsWorkers|DMS-Worker|Nein|
|Errors|Errors|Nein|
|ExecRequests|Ausführungsanforderungen|Nein|
|QueryStoreRuntimeStatistics|Laufzeitstatistik des Abfragespeichers|Nein|
|QueryStoreWaitStatistics|Wartestatistik des Abfragespeichers|Nein|
|RequestSteps|Anforderungsschritte|Nein|
|SQLInsights|SQL-Informationen|Nein|
|SqlRequests|SQL-Anforderungen|Nein|
|SQLSecurityAuditEvents|SQL-Sicherheitsüberwachungsereignis|Nein|
|Zeitlimits|Zeitlimits|Nein|
|Wartevorgänge|Wartevorgänge|Nein|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|StorageDelete|StorageDelete|Ja|
|StorageRead|StorageRead|Ja|
|StorageWrite|StorageWrite|Ja|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Erstellen|Erstellen|Nein|
|Ausführung|Ausführung|Nein|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|BuiltinSqlReqsEnded|Integrierter SQL-Pool, beendete Anforderungen|Nein|
|GatewayApiRequests|Synapse-Gateway-API-Anforderungen|Nein|
|IntegrationActivityRuns|Integrationsaktivitätsausführungen|Ja|
|IntegrationPipelineRuns|Integrationspipelineausführungen|Ja|
|IntegrationTriggerRuns|Integrationstriggerausführungen|Ja|
|SQLSecurityAuditEvents|SQL-Sicherheitsüberwachungsereignis|Nein|
|SynapseRbacOperations|Synapse-RBAC-Vorgänge|Nein|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|BigDataPoolAppsEnded|Big Data-Pool, beendete Anwendungen|Nein|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|DmsWorkers|DMS-Worker|Nein|
|ExecRequests|Ausführungsanforderungen|Nein|
|RequestSteps|Anforderungsschritte|Nein|
|SqlRequests|SQL-Anforderungen|Nein|
|SQLSecurityAuditEvents|SQL-Sicherheitsüberwachungsereignis|Nein|
|Wartevorgänge|Wartevorgänge|Nein|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Eingehende Daten|Eingehende Daten|Nein|
|Verwaltung|Verwaltung|Nein|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|Eingehende Daten|Eingehende Daten|Nein|
|Verwaltung|Verwaltung|Nein|


## <a name="microsoftwebhostingenvironments"></a>Microsoft.Web/hostingEnvironments

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|Plattformprotokolle der App Service-Umgebung|Nein|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Antivirenbericht-Überwachungsprotokolle|Nein|
|AppServiceAppLogs|App Service-Anwendungsprotokolle|Nein|
|AppServiceAuditLogs|Zugriffsüberwachungsprotokolle|Nein|
|AppServiceConsoleLogs|App Service-Konsolenprotokolle|Nein|
|AppServiceFileAuditLogs|Überwachungsprotokolle für Website-Inhaltsänderungen|Nein|
|AppServiceHTTPLogs|HTTP-Protokolle|Nein|
|AppServiceIPSecAuditLogs|IPSecurity-Überwachungsprotokolle|Nein|
|AppServicePlatformLogs|App Service-Plattformprotokolle|Nein|
|FunctionAppLogs|Funktionsanwendungsprotokolle|Nein|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

|Category|Anzeigename der Kategorie|Exportkosten|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Antivirenbericht-Überwachungsprotokolle|Nein|
|AppServiceAppLogs|App Service-Anwendungsprotokolle|Nein|
|AppServiceAuditLogs|Zugriffsüberwachungsprotokolle|Nein|
|AppServiceConsoleLogs|App Service-Konsolenprotokolle|Nein|
|AppServiceDiagnosticToolsLogs|Protokolle des Berichtsdiagnosetools|Ja|
|AppServiceFileAuditLogs|Überwachungsprotokolle für Website-Inhaltsänderungen|Nein|
|AppServiceHTTPLogs|HTTP-Protokolle|Nein|
|AppServiceIPSecAuditLogs|IPSecurity-Überwachungsprotokolle|Nein|
|AppServicePlatformLogs|App Service-Plattformprotokolle|Nein|
|FunctionAppLogs|Funktionsanwendungsprotokolle|Nein|


## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Ressourcenprotokollen](../essentials/platform-logs-overview.md)
* [Streamen von Ressourcenprotokollen an **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Ändern der Diagnoseeinstellungen für Ressourcenprotokolle mithilfe der Azure Monitor-REST-API](/rest/api/monitor/diagnosticsettings)
* [Analysieren von Protokollen aus Azure Storage mit Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)