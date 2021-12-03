---
title: Unterstützte Azure Resource Manager-Ressourcentypen
description: Stellen Sie eine Liste der Azure Resource Manager-Ressourcentypen bereit, die von Azure Resource Graph und dem Änderungsverlauf unterstützt werden.
ms.date: 10/12/2021
ms.topic: reference
ms.custom: generated
ms.openlocfilehash: 9b3ce54d9554164b375ed1a08fdbcce8429dbb21
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054807"
---
# <a name="azure-resource-graph-table-and-resource-type-reference"></a>Azure Resource Graph-Tabelle und Ressourcentypreferenz

Azure Resource Graph unterstützt die folgenden **Ressourcentypen** von [Azure Resource Manager](../../../azure-resource-manager/management/overview.md). Jeder **Ressourcentyp** ist Teil einer **Tabelle** in Resource Graph.

## <a name="advisorresources"></a>advisorresources

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für advisorresources](../samples/samples-by-table.md#advisorresources).

- microsoft.advisor/configurations
- microsoft.advisor/recommendations
  - Beispielabfrage: [Abrufen der Zusammenfassung der Kosteneinsparungen aus Azure Advisor](../samples/samples-by-category.md#get-cost-savings-summary-from-azure-advisor)
  - Beispiel-Abfrage: [Liste der Arc-fähigen Server, auf denen nicht die letzte freigegebene Agentenversion läuft](../samples/samples-by-category.md#list-arc-enabled-servers-not-running-latest-released-agent-version)
- microsoft.advisor/recommendations/suppressions
- microsoft.advisor/suppressions

## <a name="alertsmanagementresources"></a>alertsmanagementresources

- microsoft.alertsmanagement/alerts

## <a name="desktopvirtualizationresources"></a>desktopvirtualizationresources

- microsoft.chaos/experiments/statuses
- microsoft.chaos/targets
- microsoft.chaos/targets/capabilities
- microsoft.desktopvirtualization/hostpools/sessionhosts

## <a name="extendedlocationresources"></a>extendedlocationresources

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für extendedlocationresources](../samples/samples-by-table.md#extendedlocationresources).

- microsoft.extendedlocation/customlocations/enabledresourcetypes
  - Beispielabfrage: [Abrufen aktivierter Ressourcentypen für benutzerdefinierte Speicherorte mit Azure Arc-Unterstützung](../samples/samples-by-category.md#get-enabled-resource-types-for-azure-arc-enabled-custom-locations)
  - Beispielabfrage: [Auflisten benutzerdefinierter Standorte mit Azure Arc-Unterstützung mit VMware- oder SCVMM-Unterstützung](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)

## <a name="guestconfigurationresources"></a>guestconfigurationresources

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für guestconfigurationresources](../samples/samples-by-table.md#guestconfigurationresources).

- microsoft.guestconfiguration/guestconfigurationassignments
  - Beispielabfrage: [Anzahl der Computer im Bereich der Gastkonfigurationsrichtlinien](../samples/samples-by-category.md#count-machines-in-scope-of-guest-configuration-policies)
  - Beispielabfrage:[Anzahl der nicht konformen Gastkonfigurationszuweisungen](../samples/samples-by-category.md#count-of-non-compliant-guest-configuration-assignments)
  - Beispielabfrage: [Ermitteln aller Gründe dafür, warum ein Computer nicht konform für Gastkonfigurationszuweisungen ist](../samples/samples-by-category.md#find-all-reasons-a-machine-is-non-compliant-for-guest-configuration-assignments)

## <a name="healthresources"></a>healthresources

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für healthresources](../samples/samples-by-table.md#healthresources).

- microsoft.resourcehealth/availabilitystatuses
  - Beispielabfrage: [Anzahl der virtuellen Computer nach Verfügbarkeitsstatus und Abonnement-ID](../samples/samples-by-category.md#count-of-virtual-machines-by-availability-state-and-subscription-id)
  - Beispielabfrage: [Liste der virtuellen Computer und ihres Verfügbarkeitsstatus nach Ressourcen-IDs](../samples/samples-by-category.md#list-of-virtual-machines-and-associated-availability-states-by-resource-ids)
  - Beispielabfrage: [Liste der virtuellen Computer nach Verfügbarkeitsstatus und Energiezustand mit Ressourcen-IDs und Ressourcengruppen](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - Beispielabfrage: [Liste der virtuellen Computer, die nicht verfügbar sind, nach Ressourcen-IDs](../samples/samples-by-category.md#list-of-virtual-machines-that-are-not-available-by-resource-ids)

## <a name="iotsecurityresources"></a>iotsecurityRessourcen

- microsoft.iotsecurity/standorte/geraetegruppen/alarmierungen
  - Beispielabfrage: [Alle neuen Warnungen der letzten 30 Tage abrufen](../samples/samples-by-category.md#get-all-new-alerts-from-the-past-30-days)
- microsoft.iotsecurity/standorte/geraetegruppen/geraete
  - Beispielabfrage: [IoT-Geräte, die sich in Ihrem Netzwerk befinden, nach Betriebssystem zählen](../samples/samples-by-category.md#count-how-many-iot-devices-there-are-in-your-network-by-operation-system)
- microsoft.iotsecurity/standorte/geraetegruppen/empfehlungen
  - Beispielabfrage: [Alle Empfehlungen mit hohem Schweregrad abrufen](../samples/samples-by-category.md#get-all-high-severity-recommendations)
- microsoft.iotsecurity/vor-Ort-Sensoren
- microsoft.iotsecurity/sensoren
  - Beispielabfrage: [Alle Sensoren nach Typ zählen](../samples/samples-by-category.md#count-all-sensors-by-type)
- microsoft.iotsecurity/standorte
  - Beispielabfrage: [Standorte mit einem bestimmten Tagwert auflisten](../samples/samples-by-category.md#list-sites-with-a-specific-tag-value)

## <a name="kubernetesconfigurationresources"></a>kubernetesconfigurationresources

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für kubernetesconfigurationresources](../samples/samples-by-table.md#kubernetesconfigurationresources).

- microsoft.kubernetesconfiguration/extensions
  - Beispielabfrage: [Auflisten aller Azure Arc-fähigen Kubernetes-Cluster mit der Azure Monitor-Erweiterung](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-with-azure-monitor-extension)
  - Beispielabfrage: [Auflisten aller Azure Arc-fähigen Kubernetes-Cluster ohne Azure Monitor-Erweiterung](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
- microsoft.kubernetesconfiguration/fluxconfigurations
- microsoft.kubernetesconfiguration/sourcecontrolconfigurations

## <a name="maintenanceresources"></a>maintenanceresources

- microsoft.maintenance/applyupdates
- microsoft.maintenance/configurationassignments
- microsoft.maintenance/updates

## <a name="patchassessmentresources"></a>patchassessmentresources

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für patchassessmentresources](../samples/samples-by-table.md#patchassessmentresources).

- microsoft.compute/virtualmachines/patchassessmentresults
- microsoft.compute/virtualmachines/patchassessmentresults/softwarepatches
- microsoft.hybridcompute/machines/patchassessmentresults
- microsoft.hybridcompute/machines/patchassessmentresults/softwarepatches

## <a name="patchinstallationresources"></a>patchinstallationresources

- microsoft.compute/virtualmachines/patchinstallationresults
- microsoft.compute/virtualmachines/patchinstallationresults/softwarepatches
- microsoft.hybridcompute/machines/patchinstallationresults
- microsoft.hybridcompute/machines/patchinstallationresults/softwarepatches

## <a name="policyresources"></a>policyresources

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für policyresources](../samples/samples-by-table.md#policyresources).

- microsoft.policyinsights/policystates
  - Beispielabfrage: [Konformität nach Richtlinienzuweisung](../samples/samples-by-category.md#compliance-by-policy-assignment)
  - Beispielabfrage: [Konformität nach Ressourcentyp](../samples/samples-by-category.md#compliance-by-resource-type)
  - Beispielabfrage: [Auflisten aller nicht konformen Ressourcen](../samples/samples-by-category.md#list-all-non-compliant-resources)
  - Beispielabfrage: [Zusammenfassen der Ressourcenkonformität nach Zustand](../samples/samples-by-category.md#summarize-resource-compliance-by-state)
  - Beispielabfrage: [Zusammenfassen der Ressourcenkonformität nach Speicherort](../samples/samples-by-category.md#summarize-resource-compliance-by-state-per-location)

## <a name="recoveryservicesresources"></a>recoveryservicesresources

- microsoft.dataprotection/backupvaults/backupinstances
- microsoft.dataprotection/backupvaults/backupjobs
- microsoft.dataprotection/backupvaults/backuppolicies
- microsoft.recoveryservices/vaults/alerts
- Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems (Sicherungselemente)
- microsoft.recoveryservices/vaults/backupjobs
- microsoft.recoveryservices/vaults/backuppolicies

## <a name="resourcecontainers"></a>resourcecontainers

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für resourcecontainers](../samples/samples-by-table.md#resourcecontainers).

- microsoft.management/managementgroups
  - Beispielabfrage: [Anzahl der Abonnements pro Verwaltungsgruppe](../samples/samples-by-category.md#count-of-subscriptions-per-management-group)
  - Beispielabfrage: [Auflisten aller Verwaltungsgruppenvorgänger für eine angegebene Verwaltungsgruppe](../samples/samples-by-category.md#list-all-management-group-ancestors-for-a-specified-management-group)
- Microsoft.Resources/subscriptions/resourceGroups (Ressourcengruppen)
  - Beispielabfrage: [Ergebnisse aus zwei Abfragen in einem einzelnen Ergebnis kombinieren](../samples/samples-by-category.md)
  - Beispielabfrage: [Suchen von Speicherkonten mit einem bestimmten Tag (unter Vernachlässigung der Groß-/Kleinschreibung) in der Ressourcengruppe](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - Beispielabfrage: [Suchen von Speicherkonten mit einem bestimmten Tag (unter Berücksichtigung der Groß-/Kleinschreibung) in der Ressourcengruppe](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)

## <a name="resources"></a>ressourcen

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für resources](../samples/samples-by-table.md#resources).

- 84codes.CloudAMQP/servers (CloudAMQP)
- Citrix.Services/XenAppEssentials (Citrix Virtual Apps Essentials)
- Citrix.Services/XenDesktopEssentials (Citrix Virtual Desktops Essentials)
- conexlink.mycloudit/accounts
- crypteron.datasecurity/apps
- Dynatrace.Observability/monitors (Dynatrace)
- GitHub.Enterprise/accounts (GitHub AE)
- gridpro.evops/accounts
- gridpro.evops/accounts/eventrules
- gridpro.evops/accounts/requesttemplates
- gridpro.evops/accounts/views
- hive.streaming/services
- incapsula.waf/accounts
- LiveArena.Broadcast/services (LiveArena-Broadcast)
- Mailjet.Email/services (Mailjet Email Service)
- micorosft.web/kubeenvironments
- Microsoft.AAD/domainServices (Azure AD Domain Services)
- microsoft.aadiam/azureadmetrics
- microsoft.aadiam/privateLinkForAzureAD (Private Link für Azure AD)
- microsoft.aadiam/tenants
- Microsoft.AgFoodPlatform/farmBeats (Azure FarmBeats)
- microsoft.aisupercomputer/accounts
- microsoft.aisupercomputer/accounts/jobgroups
- microsoft.aisupercomputer/accounts/jobgroups/jobs
- microsoft.alertsmanagement/actionrules
- microsoft.alertsmanagement/resourcehealthalertrules
- microsoft.alertsmanagement/smartdetectoralertrules
- Microsoft.AnalysisServices/servers (Analysis Services)
- Microsoft.AnyBuild/clusters (AnyBuild-Cluster)
- Microsoft.ApiManagement/service (API Management-Dienste)
- microsoft.appassessment/migrateprojects
- Microsoft.AppConfiguration/configurationStores (App Configuration)
- Microsoft.AppPlatform/Spring (Azure Spring Cloud)
- microsoft.archive/collections
- Microsoft.Attestation/attestationProviders (Nachweisanbieter)
- microsoft.authorization/elevateaccessroleassignment
- Microsoft.Authorization/resourceManagementPrivateLinks (private Ressourcenmanagementverbindung)
- microsoft.automanage/accounts
- microsoft.automanage/configurationprofilepreferences
- microsoft.automanage/configurationprofiles
- Microsoft.Automation/AutomationAccounts (Automation-Konten)
- microsoft.automation/automationaccounts/configurations
- Microsoft.Automation/automationAccounts/runbooks (Runbook)
- microsoft.autonomousdevelopmentplatform/accounts
- Microsoft.AutonomousSystems/workspaces (Bonsai)
- Microsoft.AVS/privateClouds (private AVS-Clouds)
- microsoft.azconfig/configurationstores
- Microsoft.AzureActiveDirectory/b2cDirectories (B2C-Mandanten)
- Microsoft.AzureActiveDirectory/guestUsages (Gastnutzung)
- Microsoft.AzureArcData/dataControllers (Azure Arc-Datencontroller)
- Microsoft.AzureArcData/postgresInstances (Azure Arc-fähige PostgreSQL Hyperscale-Servergruppen)
- Microsoft.AzureArcData/sqlManagedInstances (SQL Managed Instance – Azure Arc)
- Microsoft.AzureArcData/sqlServerInstances (SQL Server – Azure Arc)
- microsoft.azurecis/autopilotenvironments
- microsoft.azurecis/dstsserviceaccounts
- microsoft.azurecis/dstsserviceclientidentities
- microsoft.azuredata/datacontrollers
- microsoft.azuredata/hybriddatamanagers
- microsoft.azuredata/postgresinstances
- microsoft.azuredata/sqlbigdataclusters
- microsoft.azuredata/sqlinstances
- microsoft.azuredata/sqlmanagedinstances
- microsoft.azuredata/sqlserverinstances
- Microsoft.AzureData/sqlServerRegistrations (SQL Server-Registrierungen)
- Microsoft.AzurePercept/accounts (Azure Percept-Konten)
- microsoft.azuresphere/catalogs
- microsoft.azuresphere/catalogs/products
- microsoft.azuresphere/catalogs/products/devicegroups
- microsoft.azurestack/edgesubscriptions
- microsoft.azurestack/linkedsubscriptions
- Microsoft.Azurestack/registrations (Azure Stack Hub)
- Microsoft.AzureStackHCI/clusters (Azure Stack HCI)
- microsoft.azurestackhci/galleryimages
- microsoft.azurestackhci/networkinterfaces
- microsoft.azurestackhci/virtualharddisks
- Microsoft.AzureStackHci/virtualMachines (Azure Stack HCI virtual machine - Azure Arc)
- microsoft.azurestackhci/virtualmachines/extensions
- microsoft.azurestackhci/virtualnetworks
- microsoft.backupsolutions/vmwareapplications
- microsoft.baremetal/consoleconnections
- Microsoft.BareMetal/crayServers (Cray-Server)
- Microsoft.BareMetal/monitoringServers (Überwachungsserver)
- Microsoft.BareMetalInfrastructure/bareMetalInstances (BareMetal-Instanzen)
- Microsoft.Batch/batchAccounts (Batch-Konten)
- microsoft.batchai/clusters
- microsoft.batchai/fileservers
- microsoft.batchai/jobs
- microsoft.batchai/workspaces
- Microsoft.Bing/accounts (Bing-Ressourcen)
- microsoft.bingmaps/mapapis
- microsoft.biztalkservices/biztalk
- Microsoft.Blockchain/blockchainMembers (Azure Blockchain-Dienst)
- Microsoft.Blockchain/cordaMembers (Corda)
- Microsoft.Blockchain/watchers (Blockchain Data Manager)
- Microsoft.BotService/botServices (Bot Service)
- Microsoft.Cache/Redis (Azure Cache for Redis)
- Microsoft.Cache/RedisEnterprise (Redis Enterprise)
- microsoft.cascade/sites
- Microsoft.Cdn/CdnWebApplicationFirewallPolicies (Web Application Firewall-Richtlinien (WAF))
- microsoft.cdn/profiles (Front Door Standard/Premium (Vorschau))
- Microsoft.Cdn/Profiles/AfdEndpoints (Endpunkte)
- microsoft.cdn/profiles/endpoints (Endpunkte)
- Microsoft.CertificateRegistration/certificateOrders (App Service Certificate)
- microsoft.chaos/chaosexperiments (Chaosexperimente (klassisch))
- microsoft.chaos/experiments (Chaosexperimente)
- microsoft.classicCompute/domainNames (Clouddienste (klassisch))
- Microsoft.ClassicCompute/VirtualMachines (VMs (klassisch))
- Microsoft.ClassicNetwork/networkSecurityGroups (Netzwerksicherheitsgruppe (klassisch))
- Microsoft.ClassicNetwork/reservedIps (IP-Reservierung (klassisch))
- Microsoft.ClassicNetwork/virtualNetworks (virtuelle Netzwerke (klassisch))
- Microsoft.ClassicStorage/StorageAccounts (Speicherkonten (klassisch))
- microsoft.cloudes/accounts
- microsoft.cloudsearch/indexes
- Microsoft.CloudTest/accounts (CloudTest-Konten)
- Microsoft.CloudTest/hostedpools (gehostete 1ES-Pools)
- Microsoft.CloudTest/images (CloudTest-Images)
- Microsoft.CloudTest/pools (CloudTest-Pools)
- Microsoft.ClusterStor/nodes (ClusterStors)
- microsoft.codesigning/codesigningaccounts
- microsoft.codespaces/plans
- Microsoft.Cognition/syntheticsAccounts (Synthetics-Konten)
- Microsoft.CognitiveServices/accounts (Cognitive Services)
- Microsoft.Compute/availabilitySets (Verfügbarkeitsgruppen)
- Microsoft.Compute/capacityReservationGroups (Capacity Reservation Groups)
- microsoft.compute/capacityreservationgroups/capacityreservations
- microsoft.compute/capacityreservations
- Microsoft.Compute/cloudServices (Clouddienste (erweiterter Support))
- Microsoft.Compute/diskAccesses (Datenträgerzugriffe)
- Microsoft.Compute/diskEncryptionSets (Datenträgerverschlüsselungssätze)
- Microsoft.Compute/disks (Datenträger)
- Microsoft.Compute/galleries (Kataloge mit freigegebenen Images)
- Microsoft.Compute/galleries/applications (VM-Anwendungen)
- Microsoft.Compute/galleries/applications/versions (VM-Anwendungsversionen)
- Microsoft.Compute/galleries/images (Imagedefinitionen)
- Microsoft.Compute/galleries/images/versions (Imageversionen)
- Microsoft.Compute/hostgroups (Hostgruppen)
- Microsoft.Compute/hostgroups/hosts (Hosts)
- Microsoft.Compute/images (Images)
- Microsoft.Compute/ProximityPlacementGroups (Näherungsplatzierungsgruppen)
- Microsoft.Compute/restorePointCollections (Wiederherstellungspunktsammlungen)
- microsoft.compute/sharedvmextensions
- microsoft.compute/sharedvmextensions/versions
- microsoft.compute/sharedvmimages
- microsoft.compute/sharedvmimages/versions
- Microsoft.Compute/snapshots (Momentaufnahmen)
- Microsoft.Compute/sshPublicKeys (SSH-Schlüssel)
- microsoft.compute/swiftlets
- Microsoft.Compute/VirtualMachines (VMs)
  - Beispielabfrage: [Anzahl der virtuellen Computer nach Energiezustand](../samples/samples-by-category.md#count-of-virtual-machines-by-power-state)
  - Beispielabfrage: [Anzahl der virtuellen Computer nach Betriebssystemtyp](../samples/samples-by-category.md#count-virtual-machines-by-os-type)
  - Beispielabfrage: [Anzahl der virtuellen Computer nach Betriebssystemtyp mit Erweiterungen](../samples/samples-by-category.md#count-virtual-machines-by-os-type-with-extend)
  - Beispielabfrage: [Auflisten aller Erweiterungen, die auf einem virtuellen Computer installiert sind](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
  - Beispielabfrage: [Auflisten nicht ausgeführter Computer und des letzten Konformitätsstatus](../samples/samples-by-category.md#list-machines-that-are-not-running-and-the-last-compliance-status)
  - Beispielabfrage: [Auflisten der virtuellen Computer nach Verfügbarkeitsstatus und Energiezustand mit Ressourcen-IDs und Ressourcengruppen](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - Beispielabfrage: [Auflisten virtueller Computer mit zugehöriger Netzwerkschnittstelle und öffentlicher IP-Adresse](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
  - Beispielabfrage: [Anzeigen aller virtuellen Computer nach Name in absteigender Reihenfolge](../samples/samples-by-category.md#show-all-virtual-machines-ordered-by-name-in-descending-order)
  - Beispielabfrage: [Anzeigen der ersten fünf virtuellen Computer nach Name und Betriebssystemtyp](../samples/samples-by-category.md#show-first-five-virtual-machines-by-name-and-their-os-type)
  - Beispielabfrage: [Zusammenfassen virtueller Computer anhand der erweiterten Eigenschaft für Energiezustände](../samples/samples-by-category.md#summarize-virtual-machine-by-the-power-states-extended-property)
  - Beispielabfrage: [Einem regulären Ausdruck entsprechende virtuelle Computer](../samples/samples-by-category.md#virtual-machines-matched-by-regex)
- microsoft.compute/virtualmachines/extensions
  - Beispielabfrage: [Auflisten aller Erweiterungen, die auf einem virtuellen Computer installiert sind](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
- microsoft.compute/virtualmachines/runcommands
- Microsoft.Compute/virtualMachineScaleSets (VM-Skalierungsgruppen)
  - Beispielabfrage: [Abrufen der Kapazität und Größe von VM-Skalierungsgruppen](../samples/samples-by-category.md#get-virtual-machine-scale-set-capacity-and-size)
- Microsoft.ConfidentialLedger/ledgers (Confidential Ledgers)
- Microsoft.Confluent/organizations (Confluent-Organisationen)
- Microsoft.ConnectedCache/cacheNodes (Connected Cache-Ressourcen)
- Microsoft.ConnectedVehicle/platformAccounts (Connected Vehicle-Plattformen)
- microsoft.connectedvmwarevsphere/clusters
- microsoft.connectedvmwarevsphere/datastores
- microsoft.connectedvmwarevsphere/hosts
- microsoft.connectedvmwarevsphere/resourcepools
- Microsoft.connectedVMwareVSphere/vCenters (VMware vCenters)
- Microsoft.ConnectedVMwarevSphere/VirtualMachines (VMware + AVS Virtuelle Computer)
- microsoft.connectedvmwarevsphere/virtualmachines/extensions
- microsoft.connectedvmwarevsphere/virtualmachinetemplates
- microsoft.connectedvmwarevsphere/virtualnetworks
- Microsoft.ContainerInstance/containerGroups (Containerinstanzen)
- Microsoft.ContainerRegistry/registries (Containerregistrierungen)
- microsoft.containerregistry/registries/agentpools
- microsoft.containerregistry/registries/buildtasks
- Microsoft.ContainerRegistry/registries/replications (Replikationen von Containerregistrierungen)
- microsoft.containerregistry/registries/taskruns
- microsoft.containerregistry/registries/tasks
- Microsoft.ContainerRegistry/registries/webhooks (Webhooks für Containerregistrierungen)
- microsoft.containerservice/containerservices
- Microsoft.ContainerService/managedClusters (Kubernetes-Dienste)
  - Beispielabfrage: [Liste der betroffenen Ressourcen bei der Übertragung eines Azure-Abonnements](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.containerservice/openshiftmanagedclusters
- microsoft.contoso/clusters
- microsoft.contoso/employees
- microsoft.contoso/installations
- microsoft.contoso/towers
- microsoft.costmanagement/connectors
- microsoft.customproviders/resourceproviders
- microsoft.d365customerinsights/instances
- Microsoft.Dashboard/grafana (Grafana-Arbeitsbereiche)
- Microsoft.DataBox/jobs (Azure Data Box)
- Microsoft.DataBoxEdge/dataBoxEdgeDevices (Azure Stack Edge/Data Box Gateway)
- Microsoft.Databricks/workspaces (Azure Databricks-Dienste)
- Microsoft.DataCatalog/catalogs (Data Catalog)
- microsoft.datacatalog/datacatalogs
- Microsoft.DataCollaboration/workspaces (Projekt-CI)
- Microsoft.Datadog/monitors (Datadog)
- Microsoft.DataFactory/dataFactories (Data Factorys)
- Microsoft.DataFactory/factories (Data Factorys (V2))
- Microsoft.DataLakeAnalytics/accounts (Data Lake Analytics)
- Microsoft.DataLakeStore/accounts (Data Lake Storage Gen1)
  - Beispielabfrage: [Liste der betroffenen Ressourcen bei der Übertragung eines Azure-Abonnements](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.datamigration/controllers
- Microsoft.DataMigration/services (Azure Database Migration Service)
- Microsoft.DataMigration/services/projects (Azure-Datenbankmigrationsprojekte)
- microsoft.datamigration/slots
- microsoft.datamigration/sqlmigrationservices
- Microsoft.DataProtection/BackupVaults (Sicherungstresore)
- Microsoft.DataProtection/resourceGuards (Ressourcenwächter (Vorschau))
- microsoft.dataprotection/resourceoperationgatekeepers
- microsoft.datareplication/replicationfabrics
- Microsoft.DataReplication/replicationVaults (Site Recovery-Tresore)
- Microsoft.DataShare/accounts (Datenfreigaben)
- Microsoft.DBforMariaDB/servers (Azure Database for MariaDB-Server)
- Microsoft.DBforMySQL/flexibleServers (flexible Azure Database for MySQL-Server)
- Microsoft.DBforMySQL/servers (Azure Database for MySQL-Server)
- Microsoft.DBforPostgreSQL/flexibleServers (flexible Azure Database for PostgreSQL-Server)
- Microsoft.DBforPostgreSQL/serverGroups (Azure Database for PostgreSQL-Servergruppen)
- Microsoft.DBforPostgreSQL/serverGroupsv2 (Azure Database für PostgreSQL-Servergruppen)
- Microsoft.DBforPostgreSQL/servers (Azure Database for PostgreSQL-Server)
- Microsoft.DBforPostgreSQL/serversv2 (Azure Database for PostgreSQL-Server v2)
- microsoft.dbforpostgresql/singleservers
- microsoft.delegatednetwork/controller
- microsoft.delegatednetwork/delegatedsubnets
- microsoft.delegatednetwork/orchestratorinstances
- microsoft.delegatednetwork/orchestrators
- microsoft.deploymentmanager/artifactsources
- Microsoft.DeploymentManager/Rollouts (Rollouts)
- microsoft.deploymentmanager/servicetopologies
- microsoft.deploymentmanager/servicetopologies/services
- microsoft.deploymentmanager/servicetopologies/services/serviceunits
- microsoft.deploymentmanager/steps
- Microsoft.DesktopVirtualization/ApplicationGroups (Anwendungsgruppen)
- Microsoft.DesktopVirtualization/HostPools (Hostpools)
- Microsoft.DesktopVirtualization/ScalingPlans (Skalierungspläne)
- Microsoft.DesktopVirtualization/Workspaces (Arbeitsbereiche)
- microsoft.devices/elasticpools
- microsoft.devices/elasticpools/iothubtenants
- Microsoft.Devices/IotHubs (IoT Hub)
- Microsoft.Devices/ProvisioningServices (Device Provisioning Service)
- Microsoft.DeviceUpdate/Accounts (Geräteupdate für IoT-Hubs)
- microsoft.deviceupdate/accounts/instances
- microsoft.devops/pipelines (DevOps Starter)
- microsoft.devspaces/controllers
- microsoft.devtestlab/labcenters
- Microsoft.DevTestLab/labs (DevTest Labs)
- microsoft.devtestlab/labs/servicerunners
- Microsoft.DevTestLab/labs/virtualMachines (VMs)
- microsoft.devtestlab/schedules
- Microsoft.DigitalTwins/digitalTwinsInstances (Azure Digital Twins)
- Microsoft.DocumentDB/cassandraClusters (Azure Managed Instance for Apache Cassandra)
- Microsoft.DocumentDb/databaseAccounts (Azure Cosmos DB-Konten)
  - Beispielabfrage: [Auflisten von Azure Cosmos DB mit bestimmten Schreibstandorten](../samples/samples-by-category.md#list-azure-cosmos-db-with-specific-write-locations)
- Microsoft.DomainRegistration/domains (App Service-Domänen)
- microsoft.dynamics365fraudprotection/instances
- Microsoft.EdgeOrder/addresses (Azure Edge Hardware Center-Adresse)
- microsoft.edgeorder/ordercollections
- Microsoft.EdgeOrder/orderItems (Azure Edge Hardware Center)
- microsoft.edgeorder/orders
- Microsoft.Elastic/monitors (Elasticsearch (Elastic Cloud))
- microsoft.enterpriseknowledgegraph/services
- Microsoft.EventGrid/domains (Event Grid-Domänen)
- Microsoft.EventGrid/partnerNamespaces (Event Grid-Partnernamespaces)
- Microsoft.EventGrid/partnerRegistrations (Event Grid-Partnerregistrierungen)
- Microsoft.EventGrid/partnerTopics (Event Grid-Partnerthemen)
- Microsoft.EventGrid/systemTopics (Event Grid-Systemthemen)
- Microsoft.EventGrid/topics (Event Grid-Themen)
- Microsoft.EventHub/clusters (Event Hubs-Cluster)
- Microsoft.EventHub/namespaces (Event Hubs-Namespaces)
- Microsoft.Experimentation/experimentWorkspaces (Experimentarbeitsbereiche)
- Microsoft.ExtendedLocation/CustomLocations (benutzerdefinierte Standorte)
  - Beispielabfrage: [Auflisten benutzerdefinierter Standorte mit Azure Arc-Unterstützung mit VMware- oder SCVMM-Unterstützung](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)
- microsoft.falcon/namespaces
- Microsoft.Fidalgo/devcenters (Fidalgo-DevCenters)
- microsoft.fidalgo/machinedefinitions
- microsoft.fidalgo/networksettings
- Microsoft.Fidalgo/projects (Fidalgo-Projekte)
- Microsoft.Fidalgo/projects/environments (Fidalgo-Umgebungen)
- Microsoft.FluidRelay/fluidRelayServers (Fluid Relay)
- microsoft.footprintmonitoring/profiles
- microsoft.gaming/titles
- Microsoft.Genomics/accounts (Genomics-Konten)
- microsoft.guestconfiguration/automanagedaccounts
- Microsoft.HanaOnAzure/hanaInstances (SAP HANA in Azure)
- Microsoft.HanaOnAzure/sapMonitors (Azure Monitor für SAP-Lösungen)
- microsoft.hardwaresecuritymodules/dedicatedhsms
- Microsoft.HDInsight/clusterpools (HDInsight-Clusterpools)
- Microsoft.HDInsight/clusterpools/clusters (HDInsight gen2-Cluster)
- microsoft.hdinsight/clusterpools/clusters/sessionclusters
- Microsoft.HDInsight/clusters (HDInsight-Cluster)
- Microsoft.HealthBot/healthBots (Azure Health Bot)
- Microsoft.HealthcareApis/services (Azure API for FHIR)
- microsoft.healthcareapis/services/privateendpointconnections
- Microsoft.HealthcareApis/workspaces (Healthcare APIs-Arbeitsbereiche)
- Microsoft.HealthcareApis/workspaces/dicomservices (DICOM-Dienste)
- Microsoft.HealthcareApis/workspaces/fhirservices (FHIR-Dienste)
- Microsoft.HealthcareApis/workspaces/iotconnectors (IoT-Connectors)
- Microsoft.HpcWorkbench/instances (HPC Workbenches (Vorschau))
- Microsoft.HybridCompute/machines (Server – Azure Arc)
  - Beispielabfrage: [Abrufen der Anzahl und des Prozentsatzes von Arc-fähigen Servern nach Domäne](../samples/samples-by-category.md#get-count-and-percentage-of-arc-enabled-servers-by-domain)
  - Beispielabfrage: [Auflisten aller Erweiterungen, die auf einem Server mit Azure Arc-Unterstützung installiert sind](../samples/samples-by-category.md#list-all-extensions-installed-on-an-azure-arc-enabled-server)
  - Beispiel-Abfrage: [Liste der Arc-fähigen Server, auf denen nicht die letzte freigegebene Agentenversion läuft](../samples/samples-by-category.md#list-arc-enabled-servers-not-running-latest-released-agent-version)
- microsoft.hybridcompute/machines/extensions
  - Beispielabfrage: [Auflisten aller Erweiterungen, die auf einem Server mit Azure Arc-Unterstützung installiert sind](../samples/samples-by-category.md#list-all-extensions-installed-on-an-azure-arc-enabled-server)
- Microsoft.HybridCompute/privateLinkScopes (Azure Arc: Private Link-Bereiche)
- microsoft.hybridcontainerservice/provisionedclusters
- Microsoft.HybridData/dataManagers (StorSimple Data Manager)
- Microsoft.HybridNetwork/devices (Azure-Netzwerkfunktions-Manager – Geräte (Vorschau))
- Microsoft.HybridNetwork/networkFunctions (Azure-Netzwerkfunktions-Manager – Netzwerkfunktionen (Vorschau))
- microsoft.hybridnetwork/virtualnetworkfunctions
- Microsoft.ImportExport/jobs (Import-/Exportaufträge)
- microsoft.industrydatalifecycle/basemodels
- microsoft.industrydatalifecycle/custodiancollaboratives
- microsoft.industrydatalifecycle/dataconsumercollaboratives
- microsoft.industrydatalifecycle/derivedmodels
- microsoft.industrydatalifecycle/membercollaboratives
- microsoft.industrydatalifecycle/modelmappings
- microsoft.industrydatalifecycle/pipelinesets
- microsoft.insights/actiongroups
- microsoft.insights/activitylogalerts
- microsoft.insights/alertrules
- microsoft.insights/autoscalesettings
- microsoft.insights/components (Application Insights)
- microsoft.insights/datacollectionendpoints (Datensammlungsendpunkte)
- microsoft.insights/datacollectionrules (Datensammlungsregeln)
- microsoft.insights/guestdiagnosticsettings
- microsoft.insights/metricalerts
- microsoft.insights/notificationgroups
- microsoft.insights/notificationrules
- Microsoft.Insights/privateLinkScopes (Azure Monitor: Private Link-Bereiche)
- microsoft.insights/querypacks
- microsoft.insights/scheduledqueryrules
- microsoft.insights/webtests (Verfügbarkeitstests)
- microsoft.insights/workbooks (Azure-Arbeitsmappen)
- microsoft.insights/workbooktemplates (Vorlagen für Azure-Arbeitsmappen)
- Microsoft.IntelligentITDigitalTwin/digitalTwins (Minervas)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/assets (Ressourcen)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/executionPlans (Bereitstellungen)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/testPlans (Sammlungen)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/tests (Skripts)
- Microsoft.IoTCentral/IoTApps (IoT Central-Anwendungen)
- microsoft.iotspaces/graph
- microsoft.keyvault/hsmpools
- microsoft.keyvault/managedhsms
- Microsoft.KeyVault/vaults (Schlüsseltresore)
  - Beispielabfrage: [Anzahl von Key Vault-Ressourcen](../samples/samples-by-category.md#count-key-vault-resources)
  - Beispielabfrage: [Schlüsseltresore mit Abonnementname](../samples/samples-by-category.md#key-vaults-with-subscription-name)
  - Beispielabfrage: [Liste der betroffenen Ressourcen bei der Übertragung eines Azure-Abonnements](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- Microsoft.Kubernetes/connectedClusters (Kubernetes – Azure Arc)
  - Beispielabfrage: [Auflisten aller Azure Arc-fähigen Kubernetes-Cluster ohne Azure Monitor-Erweiterung](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
  - Beispielabfrage: [Auflisten aller Azure Arc-fähigen Kubernetes-Ressourcen](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-resources)
- Microsoft.Kusto/clusters (Azure Data Explorer-Cluster)
- Microsoft.Kusto/clusters/databases (Azure Data Explorer-Datenbanken)
- Microsoft.LabServices/labAccounts (Lab Services)
- microsoft.labservices/labplans
- microsoft.labservices/labs
- Microsoft.LoadTestService/LoadTests (cloudnative Auslastungstests)
- Microsoft.Logic/integrationAccounts (Integrationskonten)
- Microsoft.Logic/integrationServiceEnvironments (Integrationsdienstumgebungen)
- Microsoft.Logic/integrationServiceEnvironments/managedApis (verwalteter Connector)
- Microsoft.Logic/workflows (Logik-Apps)
- Microsoft.Logz/monitors (Logz-Hauptkonto)
- Microsoft.Logz/monitors/accounts (Logz-Unterkonto)
- Microsoft.MachineLearning/commitmentPlans (Machine Learning Studio (klassisch): Webdienstpläne)
- Microsoft.MachineLearning/webServices (Machine Learning Studio (klassisch): Webdienste)
- Microsoft.MachineLearning/workspaces (Machine Learning Studio (klassisch): Arbeitsbereiche)
- microsoft.machinelearningcompute/operationalizationclusters
- microsoft.machinelearningexperimentation/accounts/workspaces
- microsoft.machinelearningservices/aisysteminventories
- microsoft.machinelearningservices/modelinventories
- microsoft.machinelearningservices/modelinventory
- microsoft.machinelearningservices/virtualclusters
- Microsoft.MachineLearningServices/workspaces (Machine Learning)
- microsoft.machinelearningservices/workspaces/batchendpoints
- microsoft.machinelearningservices/workspaces/batchendpoints/deployments
- microsoft.machinelearningservices/workspaces/inferenceendpoints
- microsoft.machinelearningservices/workspaces/inferenceendpoints/deployments
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints (Machine Learning Online-Endpunkte)
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments (Machine Learning Online-Bereitstellungen)
- Microsoft.Maintenance/maintenanceConfigurations (Wartungskonfigurationen)
- microsoft.maintenance/maintenancepolicies
- microsoft.managedidentity/groups
- Microsoft.ManagedIdentity/userAssignedIdentities (verwaltete Identitäten)
  - Beispielabfrage: [Liste der betroffenen Ressourcen bei der Übertragung eines Azure-Abonnements](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.managednetwork/managednetworkgroups
- microsoft.managednetwork/managednetworkpeeringpolicies
- microsoft.managednetwork/managednetworks
- microsoft.managednetwork/managednetworks/managednetworkgroups
- microsoft.managednetwork/managednetworks/managednetworkpeeringpolicies
- Microsoft.Maps/accounts (Azure Maps-Konten)
- Microsoft.Maps/accounts/creators (Azure Maps-Erstellerressourcen)
- microsoft.maps/accounts/privateatlases
- Microsoft.MarketplaceApps/classicDevServices (klassische Entwicklungsdienste)
- microsoft.media/mediaservices (Media Services)
- microsoft.media/mediaservices/liveevents (Liveereignisse)
- microsoft.media/mediaservices/streamingendpoints (Streamingendpunkte)
- microsoft.media/mediaservices/transforms
- microsoft.media/videoanalyzers (Video Analyzer)
- microsoft.microservices4spring/appclusters
- microsoft.migrate/assessmentprojects
- microsoft.migrate/migrateprojects
- microsoft.migrate/movecollections
- Microsoft.Migrate/projects (Migrationsprojekte)
- Microsoft.MixedReality/holographicsBroadcastAccounts (Holographics Broadcast-Konten)
- Microsoft.MixedReality/objectAnchorsAccounts (Object Anchors-Konten)
- Microsoft.MixedReality/objectUnderstandingAccounts (Object Understanding-Konten)
- Microsoft.MixedReality/remoteRenderingAccounts (Remote Rendering-Konten)
- Microsoft.MixedReality/spatialAnchorsAccounts (Spatial Anchors-Konten)
- microsoft.mixedreality/surfacereconstructionaccounts
- Microsoft.MobileNetwork/mobileNetworks (Mobile Netzwerke)
- microsoft.mobilenetwork/mobilenetworks/datanetworks
- microsoft.mobilenetwork/mobilenetworks/services
- microsoft.mobilenetwork/mobilenetworks/simpolicies
- Microsoft.MobileNetwork/mobileNetworks/sites (Mobile Netzwerkstandorte)
- microsoft.mobilenetwork/mobilenetworks/slices
- microsoft.mobilenetwork/networks
- microsoft.mobilenetwork/networks/sites
- Microsoft.MobileNetwork/packetCoreControlPlanes (Arc für Netzwerkfunktionen – Paketkerne)
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes/attacheddatanetworks
- Microsoft.MobileNetwork/sims (Sims)
- microsoft.mobilenetwork/sims/simprofiles
- microsoft.monitor/accounts
- Microsoft.NetApp/netAppAccounts (NetApp-Konten)
- microsoft.netapp/netappaccounts/backuppolicies
- Microsoft.NetApp/netAppAccounts/capacityPools (Kapazitätspools)
- Microsoft.NetApp/netAppAccounts/capacityPools/Volumes (Volumes)
- microsoft.netapp/netappaccounts/capacitypools/volumes/mounttargets
- Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots (Momentaufnahmen)
- microsoft.netapp/netappaccounts/capacitypools/volumes/subvolumes
- Microsoft.NetApp/netAppAccounts/snapshotPolicies (Snapshot-Richtlinien)
- Microsoft.Network/applicationGateways (Anwendungsgateways)
- Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies (Web Application Firewall-Richtlinien (WAF))
- Microsoft.Network/applicationSecurityGroups (Anwendungssicherheitsgruppen)
- Microsoft.Network/azureFirewalls (Firewalls)
- Microsoft.Network/bastionHosts (Bastion)
- Microsoft.Network/connections (Verbindungen)
- Microsoft.Network/customIpPrefixes (benutzerdefinierte IP-Präfixe)
- microsoft.network/ddoscustompolicies
- Microsoft.Network/ddosProtectionPlans (DDoS-Schutzpläne)
- microsoft.network/dnsforwardingrulesets
- microsoft.network/dnsresolvers
- Microsoft.Network/dnsZones (DNS-Zonen)
- microsoft.network/dscpconfigurations
- Microsoft.Network/expressRouteCircuits (ExpressRoute-Leitungen)
- microsoft.network/expressroutecrossconnections
- microsoft.network/expressroutegateways
- Microsoft.Network/expressRoutePorts (ExpressRoute Direct)
- Microsoft.Network/firewallPolicies (Firewallrichtlinien)
- Microsoft.Network/frontdoors (Front Door)
- Microsoft.Network/FrontDoorWebApplicationFirewallPolicies (Web Application Firewall-Richtlinien (WAF))
- microsoft.network/ipallocations
- Microsoft.Network/ipGroups (IP-Gruppen)
- Microsoft.Network/LoadBalancers (Load Balancer)
- Microsoft.Network/localnetworkgateways (lokale Netzwerkgateways)
- microsoft.network/mastercustomipprefixes
- Microsoft.Network/natGateways (NAT-Gateways)
- Microsoft.Network/NetworkExperimentProfiles (Internet Analyzer-Profile)
- microsoft.network/networkintentpolicies
- Microsoft.Network/networkinterfaces (Netzwerkschnittstellen)
  - Beispielabfrage: [Abrufen von virtuellen Netzwerken und Subnetzen von Netzwerkschnittstellen](../samples/samples-by-category.md#get-virtual-networks-and-subnets-of-network-interfaces)
  - Beispielabfrage: [Auflisten virtueller Computer mit zugehöriger Netzwerkschnittstelle und öffentlicher IP-Adresse](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/networkManagers (Netzwerk-Manager)
- microsoft.network/networkprofiles
- Microsoft.Network/NetworkSecurityGroups (Netzwerksicherheitsgruppen)
  - Beispielabfrage: [Anzeigen nicht zugeordneter Netzwerksicherheitsgruppen](../samples/samples-by-category.md#show-unassociated-network-security-groups)
- microsoft.network/networksecurityperimeters
- microsoft.network/networkvirtualappliances
- microsoft.network/networkwatchers (Network Watcher)
- microsoft.network/networkwatchers/connectionmonitors
- microsoft.network/networkwatchers/flowlogs (NSG-Datenflussprotokolle)
- microsoft.network/networkwatchers/lenses
- microsoft.network/networkwatchers/pingmeshes
- microsoft.network/p2svpngateways
- Microsoft.Network/privateDnsZones (Privates DNS-Zonen)
- microsoft.network/privatednszones/virtualnetworklinks
- microsoft.network/privateendpointredirectmaps
- Microsoft.Network/privateEndpoints (private Endpunkte)
- Microsoft.Network/privateLinkServices (Private Link-Dienste)
- Microsoft.Network/PublicIpAddresses (öffentliche IP-Adressen)
  - Beispielabfrage: [Auflisten virtueller Computer mit zugehöriger Netzwerkschnittstelle und öffentlicher IP-Adresse](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/publicIpPrefixes (öffentliche IP-Präfixe)
- Microsoft.Network/routeFilters (Routenfilter)
- Microsoft.Network/routeTables (Routingtabellen)
- microsoft.network/sampleresources
- microsoft.network/securitypartnerproviders
- Microsoft.Network/serviceEndpointPolicies (Dienstendpunktrichtlinien)
- Microsoft.Network/trafficmanagerprofiles (Traffic Manager-Profile)
- microsoft.network/virtualhubs
- microsoft.network/virtualhubs/bgpconnections
- microsoft.network/virtualhubs/ipconfigurations
- Microsoft.Network/virtualNetworkGateways (VNet-Gateways)
- Microsoft.Network/virtualNetworks (virtuelle Netzwerke)
- microsoft.network/virtualnetworktaps
- microsoft.network/virtualrouters
- Microsoft.Network/virtualWans (Virtual WAN)
- microsoft.network/vpngateways
- microsoft.network/vpnserverconfigurations
- microsoft.network/vpnsites
- microsoft.networkfunction/azuretrafficcollectors
- Microsoft.NotificationHubs/namespaces (Notification Hub-Namespaces)
- Microsoft.NotificationHubs/namespaces/notificationHubs (Notification Hub)
- microsoft.nutanix/interfaces
- microsoft.nutanix/nodes
- microsoft.objectstore/osnamespaces
- microsoft.offazure/hypervsites
- microsoft.offazure/importsites
- microsoft.offazure/mastersites
- microsoft.offazure/serversites
- microsoft.offazure/vmwaresites
- microsoft.openlogisticsplatform/applicationworkspaces
- Microsoft.OpenLogisticsPlatform/workspaces (Open Supply Chain-Plattform)
- microsoft.operationalinsights/clusters
- Microsoft.OperationalInsights/querypacks (Log Analytics-Abfragepakete)
- Microsoft.OperationalInsights/workspaces (Log Analytics-Arbeitsbereiche)
- Microsoft.OperationsManagement/solutions (Lösungen)
- microsoft.operationsmanagement/views
- Microsoft.Orbital/contactProfiles (ContactProfiles)
- Microsoft.Orbital/GroundStations (GroundStations)
- microsoft.orbital/orbitalendpoints
- microsoft.orbital/orbitalgateways
- microsoft.orbital/orbitalgateways/orbitall2connections
- microsoft.orbital/orbitalgateways/orbitall3connections
- Microsoft.Orbital/spacecrafts (Spacecrafts)
- Microsoft.Peering/peerings (Peerings)
- Microsoft.Peering/peeringServices (Peering Service)
- Microsoft.Portal/dashboards (freigegebene Dashboards)
- microsoft.portalsdk/rootresources
- microsoft.powerbi/privatelinkservicesforpowerbi
- microsoft.powerbi/tenants
- microsoft.powerbi/workspacecollections
- microsoft.powerbidedicated/autoscalevcores
- Microsoft.PowerBIDedicated/capacities (Power BI Embedded)
- microsoft.powerplatform/accounts
- microsoft.powerplatform/enterprisepolicies
- microsoft.projectbabylon/accounts
- microsoft.providerhubdevtest/regionalstresstests
- Microsoft.Purview/Accounts (Purview-Konten)
- Microsoft.Quantum/Workspaces (Quantum-Arbeitsbereiche)
- Microsoft.RecommendationsService/accounts (Intelligent Recommendations-Konten)
- Microsoft.RecommendationsService/accounts/modeling (Modellierung)
- Microsoft.RecommendationsService/accounts/serviceEndpoints (Dienstendpunkte)
- Microsoft.RecoveryServices/vaults (Recovery Services-Tresore)
- Microsoft.RedHatOpenShift/openShiftClusters (OpenShift-Cluster)
- Microsoft.Relay/namespaces (Relays)
- microsoft.remoteapp/collections
- microsoft.resiliency/chaosexperiments
- Microsoft.ResourceConnector/Appliances (Ressourcenbrücken)
- Microsoft.resourcegraph/queries (Resource Graph-Abfragen)
- Microsoft.Resources/deploymentScripts (Bereitstellungsskripts)
- Microsoft.Resources/templateSpecs (Vorlagenspezifikationen)
- microsoft.resources/templatespecs/versions
- Microsoft.SaaS/applications (Software-as-a-Service (SaaS, klassisch))
- Microsoft.SaaS/resources (SaaS)
- Microsoft.Scheduler/jobCollections (Scheduler-Auftragssammlungen)
- Microsoft.Scom/managedInstances (Aquila-Instanzen)
- microsoft.scvmm/clouds
- Microsoft.scvmm/virtualMachines (SCVMM-VM – Azure Arc)
- microsoft.scvmm/virtualmachinetemplates
- microsoft.scvmm/virtualnetworks
- microsoft.scvmm/vmmservers
- Microsoft.Search/searchServices (Suchdienste)
- microsoft.security/assignments
- microsoft.security/automations
- microsoft.security/customentitystoreassignments
- microsoft.security/iotsecuritysolutions
- microsoft.security/securityconnectors
- Microsoft.SecurityDetonation/chambers (Sicherheitsdetonationskammern)
- Microsoft.ServiceBus/namespaces (Service Bus-Namespaces)
- Microsoft.ServiceFabric/clusters (Service Fabric-Cluster)
- microsoft.servicefabric/containergroupsets
- Microsoft.ServiceFabric/managedclusters (verwaltete Service Fabric-Cluster)
- microsoft.servicefabricmesh/applications
- microsoft.servicefabricmesh/gateways
- microsoft.servicefabricmesh/networks
- microsoft.servicefabricmesh/secrets
- microsoft.servicefabricmesh/volumes
- Microsoft.ServicesHub/connectors (Services Hub-Connectors)
- Microsoft.SignalRService/SignalR (SignalR)
- Microsoft.SignalRService/WebPubSub (Web PubSub Service)
- microsoft.singularity/accounts
- microsoft.skytap/nodes
- microsoft.solutions/appliancedefinitions
- microsoft.solutions/appliances
- Microsoft.Solutions/applicationDefinitions (Dienstkatalog: verwaltete Anwendungsdefinitionen)
- Microsoft.Solutions/applications (verwaltete Anwendungen)
- microsoft.solutions/jitrequests
- microsoft.spoolservice/spools
- Microsoft.Sql/instancePools (Instanzenpools)
- Microsoft.Sql/managedInstances (SQL Managed Instance)
- Microsoft.Sql/managedInstances/databases (verwaltete Datenbanken)
- Microsoft.Sql/servers (SQL-Server)
- Microsoft.Sql/servers/databases (SQL-Datenbanken)
  - Beispielabfrage: [Liste der betroffenen Ressourcen bei der Übertragung eines Azure-Abonnements](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
  - Beispielabfrage: [Auflisten von SQL-Datenbanken und den zugehörigen Pools für elastische Datenbanken](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- Microsoft.Sql/servers/elasticpools (elastische SQL-Pools)
  - Beispielabfrage: [Auflisten von SQL-Datenbanken und den zugehörigen Pools für elastische Datenbanken](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- microsoft.sql/servers/jobaccounts
- Microsoft.Sql/servers/jobAgents (Agent für elastische Aufträge)
- Microsoft.Sql/virtualClusters (virtuelle Cluster)
- microsoft.sqlvirtualmachine/sqlvirtualmachinegroups
- Microsoft.SqlVirtualMachine/SqlVirtualMachines (SQL-VMs)
- microsoft.sqlvm/dwvm
- Microsoft.Storage/StorageAccounts (Speicherkonten)
  - Beispielabfrage: [Suchen von Speicherkonten mit einem bestimmten Tag (unter Vernachlässigung der Groß-/Kleinschreibung) in der Ressourcengruppe](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - Beispielabfrage: [Suchen von Speicherkonten mit einem bestimmten Tag (unter Berücksichtigung der Groß-/Kleinschreibung) in der Ressourcengruppe](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)
  - Beispielabfrage: [Auflisten aller Speicherkonten mit einem bestimmten Tagwert](../samples/samples-by-category.md#list-all-storage-accounts-with-specific-tag-value)
  - Beispielabfrage: [Liste der betroffenen Ressourcen bei der Übertragung eines Azure-Abonnements](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.storagecache/amlfilesystems
- Microsoft.StorageCache/caches (HPC-Caches)
- Microsoft.StoragePool/diskPools (Disk Pools)
- Microsoft.StorageSync/storageSyncServices (Storage-Synchronisierungsdienste)
- Microsoft.StorageSyncDev/storageSyncServices (Storage-Synchronisierungsdienste)
- Microsoft.StorageSyncInt/storageSyncServices (Storage-Synchronisierungsdienste)
- Microsoft.StorSimple/Managers (StorSimple-Geräte-Manager)
- Microsoft.StreamAnalytics/clusters (Stream Analytics-Cluster)
- Microsoft.StreamAnalytics/StreamingJobs (Stream Analytics-Aufträge)
- microsoft.swiftlet/virtualmachines
- microsoft.swiftlet/virtualmachinesnapshots
- Microsoft.Synapse/privateLinkHubs (Azure Synapse Analytics (Private Link-Hubs))
- Microsoft.Synapse/workspaces (Azure Synapse Analytics)
- Microsoft.Synapse/workspaces/bigDataPools (Apache Spark-Pools)
- microsoft.synapse/workspaces/eventstreams
- Microsoft.Synapse/workspaces/kustopools (Data Explorer-Pools, Vorschau)
- microsoft.synapse/workspaces/sqldatabases
- Microsoft.Synapse/workspaces/sqlPools (dedizierte SQL-Pools)
- microsoft.terraformoss/providerregistrations
- Microsoft.TestBase/testBaseAccounts (Test Base-Konten)
- microsoft.testbase/testbaseaccounts/packages
- microsoft.testbase/testbases
- Microsoft.TimeSeriesInsights/environments (Time Series Insights-Umgebungen)
- Microsoft.TimeSeriesInsights/environments/eventsources (Time Series Insights-Ereignisquellen)
- Microsoft.TimeSeriesInsights/environments/referenceDataSets (Time Series Insights-Referenzdatasets)
- microsoft.token/stores
- microsoft.tokenvault/vaults
- Microsoft.VideoIndexer/accounts (Video Analyzer for Media)
- Microsoft.VirtualMachineImages/imageTemplates (Imagevorlagen)
- microsoft.visualstudio/account (Azure DevOps-Organisationen)
- microsoft.visualstudio/account/extension
- microsoft.visualstudio/account/project (DevOps-Starter)
- microsoft.vmware/arczones
- microsoft.vmware/resourcepools
- microsoft.vmware/vcenters
- microsoft.vmware/virtualmachines
- microsoft.vmware/virtualmachinetemplates
- microsoft.vmware/virtualnetworks
- Microsoft.VMwareCloudSimple/dedicatedCloudNodes (CloudSimple-Knoten)
- Microsoft.VMwareCloudSimple/dedicatedCloudServices (CloudSimple-Dienste)
- Microsoft.VMwareCloudSimple/virtualMachines (CloudSimple-VMs)
- microsoft.vmwareonazure/privateclouds
- microsoft.vmwarevirtustream/privateclouds
- microsoft.vsonline/accounts
- Microsoft.VSOnline/Plans (Visual Studio-Onlinepläne)
- microsoft.web/apimanagementaccounts
- microsoft.web/apimanagementaccounts/apis
- microsoft.web/certificates
- Microsoft.Web/connectionGateways (lokale Datengateways)
- Microsoft.Web/connections (API-Verbindungen)
- microsoft.web/containerapps
- Microsoft.Web/customApis (benutzerdefinierter Logic Apps-Connector)
- Microsoft.Web/HostingEnvironments (App Service-Umgebungen)
- Microsoft.Web/KubeEnvironments (App Service-Kubernetes-Umgebungen)
- Microsoft.Web/serverFarms (App Service-Pläne)
- Microsoft.Web/sites (App Services)
- microsoft.web/sites/premieraddons
- Microsoft.Web/sites/slots (App Service (Slots))
- Microsoft.Web/StaticSites (Static Web Apps)
- Microsoft.Web/WorkerApps (Container Apps)
- Microsoft.WindowsESU/multipleActivationKeys (Windows-Mehrfachaktivierungsschlüssel)
- Microsoft.WindowsIoT/DeviceServices (Windows 10 IoT Core Services)
- microsoft.workloadbuilder/migrationagents
- microsoft.workloadbuilder/workloads
- myget.packagemanagement/services
- NGINX.NGINXPLUS/nginxDeployments (NGINX-Bereitstellung)
- Paraleap.CloudMonix/services (CloudMonix)
- Pokitdok.Platform/services (PokitDok-Plattform)
- private.contoso/employees
- Providers.Test/statefulIbizaEngines (Meine Ressourcen)
- RavenHq.Db/databases (RavenHQ)
- Raygun.CrashReporting/apps (Raygun)
- Sendgrid.Email/accounts (SendGrid-Konten)
- sparkpost.basic/services
- stackify.retrace/services
- test.shoebox/testresources
- test.shoebox/testresources2
- TrendMicro.DeepSecurity/accounts (Deep Security SaaS)
- u2uconsult.theidentityhub/services
- Wandisco.Fusion/fusionGroups (LiveData Planes)
- Wandisco.Fusion/fusionGroups/azureZones (Azure-Zonen)
- Wandisco.Fusion/fusionGroups/azureZones/plugins (Plug-Ins)
- Wandisco.Fusion/fusionGroups/hiveReplicationRules (Hive-Replikationsregeln)
- Wandisco.Fusion/fusionGroups/managedOnPremZones (lokale Zonen)
- wandisco.fusion/fusiongroups/onpremzones
- Wandisco.Fusion/fusionGroups/replicationRules (Replikationsregeln)
- Wandisco.Fusion/migrators (LiveData-Migrationen)
- Wandisco.Fusion/migrators/exclusionTemplates (Ausschlüsse)
- Wandisco.Fusion/migrators/liveDataMigrations (Migrationen)
- Wandisco.Fusion/migrators/metadataMigrations (Metadatenmigrationen)
- Wandisco.Fusion/migrators/metadataTargets (Metadatenziele)
- Wandisco.Fusion/migrators/pathMappings (Pfadzuordnungen)
- Wandisco.Fusion/migrators/targets (Ziele)

## <a name="securityresources"></a>securityresources

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für securityresources](../samples/samples-by-table.md#securityresources).

- microsoft.security/assessments
  - Beispielabfrage: [Anzahl fehlerfreier, fehlerhafter und nicht anwendbarer Ressourcen pro Empfehlung](../samples/samples-by-category.md#count-healthy-unhealthy-and-not-applicable-resources-per-recommendation)
  - Beispielabfrage: [Auflisten von Azure Security Center-Empfehlungen](../samples/samples-by-category.md#list-azure-security-center-recommendations)
  - Beispielabfrage: [Auflisten der Ergebnisse der Sicherheitsrisikobewertung für die Containerregistrierung](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - Beispielabfrage: [Auflisten der Ergebnisse der Sicherheitsrisikobewertung von Qualys](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/assessments/subassessments
  - Beispielabfrage: [Auflisten der Ergebnisse der Sicherheitsrisikobewertung für die Containerregistrierung](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - Beispielabfrage: [Auflisten der Ergebnisse der Sicherheitsrisikobewertung von Qualys](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/insights/classification (Data Sensitivity Security Insights (Vorschau))
- microsoft.security/iotalerts
  - Beispielabfrage: [Abrufen aller IoT-Warnungen auf dem Hub, nach Typ gefiltert](../samples/samples-by-category.md#get-all-iot-alerts-on-hub-filtered-by-type)
  - Beispielabfrage: [Abrufen bestimmter IoT-Warnungen](../samples/samples-by-category.md#get-specific-iot-alert)
- microsoft.security/locations/alerts (Sicherheitswarnungen)
- microsoft.security/pricings
  - Beispielabfrage: [Anzeigen des Azure Defender-Tarifs pro Abonnement](../samples/samples-by-category.md#show-azure-defender-pricing-tier-per-subscription)
- microsoft.security/regulatorycompliancestandards
  - Beispielabfrage: [Einhaltung gesetzlicher Bestimmungen pro Konformitätsstandard](../samples/samples-by-category.md#regulatory-compliance-state-per-compliance-standard)
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments
  - Beispielabfrage: [Bewertungen der Einhaltung gesetzlicher Bestimmungen](../samples/samples-by-category.md#regulatory-compliance-assessments-state)
- microsoft.security/securescores
  - Beispielabfrage: [Sicherheitsbewertung pro Verwaltungsgruppe](../samples/samples-by-category.md#secure-score-per-management-group)
  - Beispielabfrage: [Sicherheitsbewertung pro Abonnement](../samples/samples-by-category.md#secure-score-per-subscription)
- microsoft.security/securescores/securescorecontrols
  - Beispielabfrage: [Kontrolle von Sicherheitsbewertungen pro Abonnement](../samples/samples-by-category.md#controls-secure-score-per-subscription)
- microsoft.security/softwareinventories
- microsoft.security/softwareinventory

## <a name="servicehealthresources"></a>servicehealthresources

Beispielabfragen für diese Tabelle finden Sie unter [Resource Graph-Beispielabfragen für servicehealthresources](../samples/samples-by-table.md#servicehealthresources).

- microsoft.resourcehealth/events
  - Beispielabfrage: [Auswirkungen von Active Service Health-Ereignisabonnements](../samples/samples-by-category.md#active-service-health-event-subscription-impact)
  - Beispielabfrage: [Alle aktiven Ereignisse zur Integritätsempfehlung](../samples/samples-by-category.md#all-active-health-advisory-events)
  - Beispielabfrage: [Alle aktiven geplante Wartungsereignisse](../samples/samples-by-category.md#all-active-planned-maintenance-events)
  - Beispielabfrage: [Alle aktiven Service Health-Ereignisse](../samples/samples-by-category.md#all-active-service-health-events)
  - Beispielabfrage: [Alle aktiven Dienstproblem-Ereignisse](../samples/samples-by-category.md#all-active-service-issue-events)

## <a name="workloadmonitorresources"></a>workloadmonitorresources

- microsoft.workloadmonitor/monitors

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Abfragesprache](../concepts/query-language.md).
- Erfahren Sie mehr über das [Erkunden von Ressourcen](../concepts/explore-resources.md).
- Sehen Sie sich Beispiele für [einfache Abfragen](../samples/starter.md) an.
