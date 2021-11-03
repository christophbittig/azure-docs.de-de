---
title: Tagunterstützung für Ressourcen
description: Zeigt, welche Azure-Ressourcentypen Tags unterstützen. Enthält Details für alle Azure-Dienste.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 863e7fbc686ed27588153979432b6189aabcba50
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248119"
---
# <a name="tag-support-for-azure-resources"></a>Tagunterstützung für Azure-Ressourcen
In diesem Artikel erfahren Sie, ob ein Ressourcentyp [Tags](tag-resources.md) unterstützt. Die Spalte mit der Bezeichnung **Tagunterstützung** zeigt an, ob der Ressourcentyp eine Eigenschaft für das Tag hat. Die Spalte mit der Bezeichnung **Tag in Kostenbericht** gibt an, ob dieser Ressourcentyp das Tag an den Kostenbericht übergibt. Sie können die Kosten nach Tags in der [Cost Management-Kostenanalyse](../../cost-management-billing/costs/group-filter.md) und den [Azure-Abrechnungen und täglichen Nutzungsdaten](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md) anzeigen.

Um die Daten als Datei mit durch Trennzeichen getrennten Werten abzurufen, laden Sie [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv) herunter.

Navigieren Sie direkt zu einem Ressourcenanbieter-Namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzurePercept](#microsoftazurepercept)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BackupSolutions](#microsoftbackupsolutions)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BillingBenefits](#microsoftbillingbenefits)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.CodeSigning](#microsoftcodesigning)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConfidentialLedger](#microsoftconfidentialledger)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.Dashboard](#microsoftdashboard)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DelegatedNetwork](#microsoftdelegatednetwork)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.Diagnostics](#microsoftdiagnostics)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Fidalgo](#microsoftfidalgo)
> - [Microsoft.FluidRelay](#microsoftfluidrelay)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.Graph](#microsoftgraph)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HpcWorkbench](#microsofthpcworkbench)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridConnectivity](#microsofthybridconnectivity)
> - [Microsoft.HybridContainerService](#microsofthybridcontainerservice)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceNotifications](#microsoftmarketplacenotifications)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft.Monitor](#microsoftmonitor)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.NetworkFunction](#microsoftnetworkfunction)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PlayFab](#microsoftplayfab)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.Quota](#microsoftquota)
> - [Microsoft.RecommendationsService](#microsoftrecommendationsservice)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scom](#microsoftscom)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TestBase](#microsofttestbase)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VideoIndexer](#microsoftvideoindexer)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices/oucontainer | Nein | Nein |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nein | Nein |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nein | Nein |
> | addsservices | Nein | Nein |
> | agents | Nein | Nein |
> | anonymousapiusers | Nein | Nein |
> | Konfiguration | Nein | Nein |
> | logs | Nein | Nein |
> | reports | Nein | Nein |
> | servicehealthmetrics | Nein | Nein |
> | services | Nein | Nein |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | advisorScore | Nein | Nein |
> | Konfigurationen | Nein | Nein |
> | generateRecommendations | Nein | Nein |
> | metadata | Nein | Nein |
> | empfehlungen | Nein | Nein |
> | suppressions | Nein | Nein |

> [!NOTE]
> Alle Microsoft.Advisor-Ressourcen sind kostenlos und daher nicht im Kostenbericht enthalten.

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | farmBeats | Ja | Ja |
> | farmBeats/eventGridFilters | Nein | Nein |
> | farmBeats/extensions | Nein | Nein |
> | farmBeatsExtensionDefinitions | Nein | Nein |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | actionRules | Ja | Ja |
> | alerts | Nein | Nein |
> | alertsList | Nein | Nein |
> | alertsMetaData | Nein | Nein |
> | alertsSummary | Nein | Nein |
> | alertsSummaryList | Nein | Nein |
> | migrateFromSmartDetection | Nein | Nein |
> | resourceHealthAlertRules | Ja | Ja |
> | smartDetectorAlertRules | Ja | Ja |
> | smartGroups | Nein | Nein |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | servers | Ja | Ja |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | clusters | Nein | Nein |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | deletedServices | Nein | Nein |
> | getDomainOwnershipIdentifier | Nein | Nein |
> | reportFeedback | Nein | Nein |
> | Dienst | Ja | Ja |
> | service/eventGridFilters | Nein | Nein |
> | validateServiceName | Nein | Nein |

> [!NOTE]
> Azure API Management unterstützt nur das Erstellen von maximal 15-Tagname-Wert-Paaren für jeden Dienst.

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | migrateProjects | Nein | Nein |
> | migrateProjects/assessments | Nein | Nein |
> | migrateProjects/assessments/assessedApplications | Nein | Nein |
> | migrateProjects/assessments/assessedApplications/machines | Nein | Nein |
> | migrateProjects/assessments/assessedMachines | Nein | Nein |
> | migrateProjects/assessments/assessedMachines/applications | Nein | Nein |
> | migrateProjects/assessments/machinesToAssess | Nein | Nein |
> | migrateProjects/sites | Nein | Nein |
> | migrateProjects/sites/applianceConfigurations | Nein | Nein |
> | migrateProjects/sites/machines | Nein | Nein |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Nein |
> | configurationStores/eventGridFilters | Nein | Nein |
> | configurationStores/keyValues | Nein | Nein |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |
> | Spring/apps | Nein | Nein |
> | Spring/apps/deployments | Nein | Nein |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Ja | Ja |
> | defaultProviders | Nein | Nein |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | Nein | Nein |
> | accessReviewScheduleSettings | Nein | Nein |
> | batchResourceCheckAccess | Nein | Nein |
> | classicAdministrators | Nein | Nein |
> | dataAliases | Nein | Nein |
> | dataPolicyManifests | Nein | Nein |
> | denyAssignments | Nein | Nein |
> | diagnosticSettings | Nein | Nein |
> | diagnosticSettingsCategories | Nein | Nein |
> | elevateAccess | Nein | Nein |
> | eligibleChildResources | Nein | Nein |
> | findOrphanRoleAssignments | Nein | Nein |
> | locks | Nein | Nein |
> | Berechtigungen | Nein | Nein |
> | policyAssignments | Nein | Nein |
> | policyDefinitions | Nein | Nein |
> | policyExemptions | Nein | Nein |
> | policySetDefinitions | Nein | Nein |
> | privateLinkAssociations | Nein | Nein |
> | providerOperations | Nein | Nein |
> | resourceManagementPrivateLinks | Ja | Ja |
> | roleAssignmentApprovals | Nein | Nein |
> | roleAssignments | Nein | Nein |
> | roleAssignmentScheduleInstances | Nein | Nein |
> | roleAssignmentScheduleRequests | Nein | Nein |
> | roleAssignmentSchedules | Nein | Nein |
> | roleAssignmentsUsageMetrics | Nein | Nein |
> | roleDefinitions | Nein | Nein |
> | roleEligibilityScheduleInstances | Nein | Nein |
> | roleEligibilityScheduleRequests | Nein | Nein |
> | roleEligibilitySchedules | Nein | Nein |
> | roleManagementPolicies | Nein | Nein |
> | roleManagementPolicyAssignments | Nein | Nein |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | bestPractices | Nein | Nein |
> | bestPractices/versions | Nein | Nein |
> | configurationProfileAssignmentIntents | Nein | Nein |
> | configurationProfileAssignments | Nein | Nein |
> | configurationProfilePreferences | Ja | Ja |
> | configurationProfiles | Ja | Ja |
> | configurationProfiles/versions | Ja | Ja |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ja | Ja |
> | automationAccounts/configurations | Ja | Ja |
> | automationAccounts/hybridRunbookWorkerGroups | Nein | Nein |
> | automationAccounts/hybridRunbookWorkerGroups/hybridRunbookWorkers | Nein | Nein |
> | automationAccounts/jobs | Nein | Nein |
> | automationAccounts/privateEndpointConnectionProxies | Nein | Nein |
> | automationAccounts/privateEndpointConnections | Nein | Nein |
> | automationAccounts/privateLinkResources | Nein | Nein |
> | automationAccounts/runbooks | Ja | Ja |
> | automationAccounts/softwareUpdateConfigurations | Nein | Nein |
> | automationAccounts/webhooks | Nein | Nein |

> [!NOTE]
> Azure Automation unterstützt nur das Erstellen von maximal 15 Tagname-/Wertpaaren pro Automation-Ressource.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | privateClouds | Ja | Ja |
> | privateClouds/addons | Nein | Nein |
> | privateClouds/authorizations | Nein | Nein |
> | privateClouds/cloudLinks | Nein | Nein |
> | privateClouds/clusters | Nein | Nein |
> | privateClouds/clusters/datastores | Nein | Nein |
> | privateClouds/clusters/placementPolicies | Nein | Nein |
> | privateClouds/clusters/virtualMachines | Nein | Nein |
> | privateClouds/globalReachConnections | Nein | Nein |
> | privateClouds/hcxEnterpriseSites | Nein | Nein |
> | privateClouds/scriptExecutions | Nein | Nein |
> | privateClouds/scriptPackages | Nein | Nein |
> | privateClouds/scriptPackages/scriptCmdlets | Nein | Nein |
> | privateClouds/workloadNetworks | Nein | Nein |
> | privateClouds/workloadNetworks/dhcpConfigurations | Nein | Nein |
> | privateClouds/workloadNetworks/dnsServices | Nein | Nein |
> | privateClouds/workloadNetworks/dnsZones | Nein | Nein |
> | privateClouds/workloadNetworks/gateways | Nein | Nein |
> | privateClouds/workloadNetworks/portMirroringProfiles | Nein | Nein |
> | privateClouds/workloadNetworks/publicIPs | Nein | Nein |
> | privateClouds/workloadNetworks/segments | Nein | Nein |
> | privateClouds/workloadNetworks/virtualMachines | Nein | Nein |
> | privateClouds/workloadNetworks/vmGroups | Nein | Nein |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | environments | Nein | Nein |
> | environments/accounts | Nein | Nein |
> | environments/accounts/namespaces | Nein | Nein |
> | environments/accounts/namespaces/configurations | Nein | Nein |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ja | Nein |
> | b2ctenants | Nein | Nein |
> | guestUsages | Ja | Ja |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | DataControllers | Nein | Nein |
> | DataWarehouseInstances | Nein | Nein |
> | PostgresInstances | Nein | Nein |
> | SqlManagedInstances | Nein | Nein |
> | SqlServerInstances | Nein | Nein |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | autopilotEnvironments | Nein | Nein |
> | dstsServiceAccounts | Nein | Nein |
> | dstsServiceClientIdentities | Nein | Nein |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | sqlServerRegistrations | Ja | Ja |
> | sqlServerRegistrations/sqlServers | Nein | Nein |

## <a name="microsoftazurepercept"></a>Microsoft.AzurePercept

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Nein | Nein |
> | accounts/devices | Nein | Nein |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | catalogs | Nein | Nein |
> | catalogs/certificates | Nein | Nein |
> | catalogs/deployments | Nein | Nein |
> | catalogs/devices | Nein | Nein |
> | catalogs/images | Nein | Nein |
> | catalogs/products | Nein | Nein |
> | catalogs/products/devicegroups | Nein | Nein |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Nein | Nein |
> | linkedSubscriptions | Ja | Ja |
> | registrations | Ja | Ja |
> | registrations/customerSubscriptions | Nein | Nein |
> | registrations/products | Nein | Nein |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | clusters | Nein | Nein |
> | clusters/arcsettings | Nein | Nein |
> | clusters/arcsettings/extensions | Nein | Nein |
> | galleryImages | Nein | Nein |
> | networkInterfaces | Nein | Nein |
> | virtualHardDisks | Nein | Nein |
> | virtualmachines | Nein | Nein |
> | virtualmachines/extensions | Nein | Nein |
> | virtualmachines/hybrididentitymetadata | Nein | Nein |
> | virtualNetworks | Nein | Nein |

## <a name="microsoftbackupsolutions"></a>Microsoft.BackupSolutions

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | VMwareApplications | Ja | Ja |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Ja | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ja | Ja |
> | batchAccounts/certificates | Nein | Nein |
> | batchAccounts/pools | Nein | Nein |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nein | Nein |
> | billingAccounts/agreements | Nein | Nein |
> | billingAccounts/appliedReservationOrders | Nein | Nein |
> | billingAccounts/billingPermissions | Nein | Nein |
> | billingAccounts/billingProfiles | Nein | Nein |
> | billingAccounts/billingProfiles/billingPermissions | Nein | Nein |
> | billingAccounts/billingProfiles/billingRoleAssignments | Nein | Nein |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Nein | Nein |
> | billingAccounts/billingProfiles/billingSubscriptions | Nein | Nein |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Nein | Nein |
> | billingAccounts/billingProfiles/customers | Nein | Nein |
> | billingAccounts/billingProfiles/instructions | Nein | Nein |
> | billingAccounts/billingProfiles/invoices | Nein | Nein |
> | billingAccounts/billingProfiles/invoices/pricesheet | Nein | Nein |
> | billingAccounts/billingProfiles/invoices/transactions | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/products | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/transactions | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nein | Nein |
> | billingAccounts/billingProfiles/invoiceSections/validateDeleteInvoiceSectionEligibility | Nein | Nein |
> | billingAccounts/BillingProfiles/patchOperations | Nein | Nein |
> | billingAccounts/billingProfiles/paymentMethodLinks | Nein | Nein |
> | billingAccounts/billingProfiles/paymentMethods | Nein | Nein |
> | billingAccounts/billingProfiles/policies | Nein | Nein |
> | billingAccounts/billingProfiles/pricesheet | Nein | Nein |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Nein | Nein |
> | billingAccounts/billingProfiles/products | Nein | Nein |
> | billingAccounts/billingProfiles/reservations | Nein | Nein |
> | billingAccounts/billingProfiles/transactions | Nein | Nein |
> | billingAccounts/billingProfiles/validateDeleteBillingProfileEligibility | Nein | Nein |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | Nein | Nein |
> | billingAccounts/billingRoleAssignments | Nein | Nein |
> | billingAccounts/billingRoleDefinitions | Nein | Nein |
> | billingAccounts/billingSubscriptions | Nein | Nein |
> | billingAccounts/billingSubscriptions/elevateRole | Nein | Nein |
> | billingAccounts/billingSubscriptions/invoices | Nein | Nein |
> | billingAccounts/createBillingRoleAssignment | Nein | Nein |
> | billingAccounts/createInvoiceSectionOperations | Nein | Nein |
> | billingAccounts/customers | Nein | Nein |
> | billingAccounts/customers/billingPermissions | Nein | Nein |
> | billingAccounts/customers/billingSubscriptions | Nein | Nein |
> | billingAccounts/customers/initiateTransfer | Nein | Nein |
> | billingAccounts/customers/policies | Nein | Nein |
> | billingAccounts/customers/products | Nein | Nein |
> | billingAccounts/customers/transactions | Nein | Nein |
> | billingAccounts/customers/transfers | Nein | Nein |
> | billingAccounts/customers/transferSupportedAccounts | Nein | Nein |
> | billingAccounts/departments | Nein | Nein |
> | billingAccounts/Departments/billingPermissions | Nein | Nein |
> | billingAccounts/departments/billingRoleAssignments | Nein | Nein |
> | billingAccounts/departments/billingRoleDefinitions | Nein | Nein |
> | billingAccounts/departments/billingSubscriptions | Nein | Nein |
> | billingAccounts/departments/enrollmentAccounts | Nein | Nein |
> | billingAccounts/enrollmentAccounts | Nein | Nein |
> | billingAccounts/enrollmentAccounts/billingPermissions | Nein | Nein |
> | billingAccounts/enrollmentAccounts/billingRoleAssignments | Nein | Nein |
> | billingAccounts/enrollmentAccounts/billingRoleDefinitions | Nein | Nein |
> | billingAccounts/enrollmentAccounts/billingSubscriptions | Nein | Nein |
> | billingAccounts/invoices | Nein | Nein |
> | billingAccounts/invoices/transactions | Nein | Nein |
> | billingAccounts/invoices/transactionSummary | Nein | Nein |
> | billingAccounts/invoices | Nein | Nein |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Nein | Nein |
> | billingAccounts / invoiceSections/billingSubscriptions | Nein | Nein |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nein | Nein |
> | billingAccounts/invoiceSections/elevate | Nein | Nein |
> | billingAccounts/invoiceSections/initiateTransfer | Nein | Nein |
> | billingAccounts/invoiceSections/patchOperations | Nein | Nein |
> | billingAccounts/invoiceSections/productMoveOperations | Nein | Nein |
> | billingAccounts/invoiceSections/products | Nein | Nein |
> | billingAccounts/invoiceSections/products/transfer | Nein | Nein |
> | billingAccounts/invoiceSections/products/updateAutoRenew | Nein | Nein |
> | billingAccounts/invoiceSections/transactions | Nein | Nein |
> | billingAccounts/invoiceSections/transfers | Nein | Nein |
> | billingAccounts/lineOfCredit | Nein | Nein |
> | billingAccounts/patchOperations | Nein | Nein |
> | billingAccounts/payableOverage | Nein | Nein |
> | billingAccounts/paymentMethods | Nein | Nein |
> | billingAccounts/payNow | Nein | Nein |
> | billingAccounts/policies | Nein | Nein |
> | billingAccounts/products | Nein | Nein |
> | billingAccounts/promotionalCredits | Nein | Nein |
> | billingAccounts/reservations | Nein | Nein |
> | billingAccounts/savingsPlanOrders | Nein | Nein |
> | billingAccounts/savingsPlanOrders/savingsPlans | Nein | Nein |
> | billingAccounts/savingsPlans | Nein | Nein |
> | billingAccounts/transactions | Nein | Nein |
> | billingPeriods | Nein | Nein |
> | billingPermissions | Nein | Nein |
> | billingProperty | Nein | Nein |
> | billingRoleAssignments | Nein | Nein |
> | billingRoleDefinitions | Nein | Nein |
> | createBillingRoleAssignment | Nein | Nein |
> | departments | Nein | Nein |
> | enrollmentAccounts | Nein | Nein |
> | invoices | Nein | Nein |
> | paymentMethods | Nein | Nein |
> | promotionalCredits | Nein | Nein |
> | Erweiterungen | Nein | Nein |
> | transfers | Nein | Nein |
> | transfers/acceptTransfer | Nein | Nein |
> | transfers/declineTransfer | Nein | Nein |
> | transfers/operationStatus | Nein | Nein |
> | transfers/validateTransfer | Nein | Nein |
> | validateAddress | Nein | Nein |

## <a name="microsoftbillingbenefits"></a>Microsoft.BillingBenefits

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | savingsPlanOrderAliases | Nein | Nein |
> | validate | Nein | Nein |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ja | Ja |
> | cordaMembers | Ja | Ja |
> | watchers | Ja | Ja |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | TokenServices | Ja | Ja |
> | TokenServices/BlockchainNetworks | Nein | Nein |
> | TokenServices/Groups | Nein | Nein |
> | TokenServices/Groups/Accounts | Nein | Nein |
> | TokenServices/TokenTemplates | Nein | Nein |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nein | Nein |
> | blueprintAssignments/assignmentOperations | Nein | Nein |
> | blueprintAssignments/operations | Nein | Nein |
> | blueprints | Nein | Nein |
> | blueprints/artifacts | Nein | Nein |
> | blueprints/versions | Nein | Nein |
> | blueprints/versions/artifacts | Nein | Nein |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices/channels | Nein | Nein |
> | botServices/connections | Nein | Nein |
> | botServices/privateEndpointConnectionProxies | Nein | Nein |
> | botServices/privateEndpointConnections | Nein | Nein |
> | botServices/privateLinkResources | Nein | Nein |
> | hostSettings | Nein | Nein |
> | languages | Nein | Nein |
> | Vorlagen | Nein | Nein |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |
> | Redis/EventGridFilters | Nein | Nein |
> | Redis/privateEndpointConnectionProxies | Nein | Nein |
> | Redis/privateEndpointConnectionProxies/validate | Nein | Nein |
> | Redis/privateEndpointConnections | Nein | Nein |
> | Redis/privateLinkResources | Nein | Nein |
> | redisEnterprise | Ja | Ja |
> | redisEnterprise/databases | Nein | Nein |
> | RedisEnterprise/privateEndpointConnectionProxies | Nein | Nein |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | Nein | Nein |
> | RedisEnterprise/privateEndpointConnections | Nein | Nein |
> | RedisEnterprise/privateLinkResources | Nein | Nein |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nein | Nein |
> | autoQuotaIncrease | Nein | Nein |
> | calculateExchange | Nein | Nein |
> | calculatePrice | Nein | Nein |
> | calculatePurchasePrice | Nein | Nein |
> | catalogs | Nein | Nein |
> | commercialReservationOrders | Nein | Nein |
> | Börse | Nein | Nein |
> | ownReservations | Nein | Nein |
> | placePurchaseOrder | Nein | Nein |
> | reservationOrders | Nein | Nein |
> | reservationOrders/calculateRefund | Nein | Nein |
> | reservationOrders/merge | Nein | Nein |
> | reservationOrders/reservations | Nein | Nein |
> | reservationOrders/reservations/revisions | Nein | Nein |
> | reservationOrders/return | Nein | Nein |
> | reservationOrders/split | Nein | Nein |
> | reservationOrders/swap | Nein | Nein |
> | reservations | Nein | Nein |
> | resourceProviders | Nein | Nein |
> | ressourcen | Nein | Nein |
> | validateReservationOrder | Nein | Nein |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | sites | Nein | Nein |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nein | Nein |
> | CdnWebApplicationFirewallPolicies | Ja | Ja |
> | edgenodes | Nein | Nein |
> | profiles | Ja | Ja |
> | profiles/afdendpoints | Ja | Ja |
> | profiles/afdendpoints/routes | Nein | Nein |
> | profiles/customdomains | Nein | Nein |
> | profiles/endpoints | Ja | Ja |
> | profiles/endpoints/customdomains | Nein | Nein |
> | profiles/endpoints/origingroups | Nein | Nein |
> | profiles/endpoints/origins | Nein | Nein |
> | profiles/origingroups | Nein | Nein |
> | profiles/origingroups/origins | Nein | Nein |
> | profiles/rulesets | Nein | Nein |
> | profiles/rulesets/rules | Nein | Nein |
> | profiles/secrets | Nein | Nein |
> | profiles/securitypolicies | Nein | Nein |
> | validateProbe | Nein | Nein |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ja | Ja |
> | certificateOrders/certificates | Nein | Nein |
> | validateCertificateRegistrationInformation | Nein | Nein |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Änderungen | Nein | Nein |
> | changeSnapshots | Nein | Nein |
> | computeChanges | Nein | Nein |
> | profile | Nein | Nein |
> | resourceChanges | Nein | Nein |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | capabilities | Nein | Nein |
> | domainNames | Nein | Nein |
> | domainNames/capabilities | Nein | Nein |
> | domainNames/internalLoadBalancers | Nein | Nein |
> | domainNames/serviceCertificates | Nein | Nein |
> | domainNames/slots | Nein | Nein |
> | domainNames/slots/roles | Nein | Nein |
> | domainNames/slots/roles/metricDefinitions | Nein | Nein |
> | domainNames/slots/roles/metrics | Nein | Nein |
> | moveSubscriptionResources | Nein | Nein |
> | operatingSystemFamilies | Nein | Nein |
> | operatingSystems | Nein | Nein |
> | quotas | Nein | Nein |
> | resourceTypes | Nein | Nein |
> | validateSubscriptionMoveAvailability | Nein | Nein |
> | virtualMachines | Nein | Nein |
> | virtualMachines/diagnosticSettings | Nein | Nein |
> | virtualMachines/metricDefinitions | Nein | Nein |
> | virtualMachines/metrics | Nein | Nein |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nein | Nein |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | capabilities | Nein | Nein |
> | expressRouteCrossConnections | Nein | Nein |
> | expressRouteCrossConnections/peerings | Nein | Nein |
> | gatewaySupportedDevices | Nein | Nein |
> | networkSecurityGroups | Nein | Nein |
> | quotas | Nein | Nein |
> | reservedIps | Nein | Nein |
> | virtualNetworks | Nein | Nein |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nein | Nein |
> | virtualNetworks/virtualNetworkPeerings | Nein | Nein |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | capabilities | Nein | Nein |
> | disks | Nein | Nein |
> | images | Nein | Nein |
> | osImages | Nein | Nein |
> | osPlatformImages | Nein | Nein |
> | publicImages | Nein | Nein |
> | quotas | Nein | Nein |
> | storageAccounts | Nein | Nein |
> | storageAccounts/blobServices | Nein | Nein |
> | storageAccounts/fileServices | Nein | Nein |
> | storageAccounts/metricDefinitions | Nein | Nein |
> | storageAccounts/metrics | Nein | Nein |
> | storageAccounts/queueServices | Nein | Nein |
> | storageAccounts/services | Nein | Nein |
> | storageAccounts/services/diagnosticSettings | Nein | Nein |
> | storageAccounts/services/metricDefinitions | Nein | Nein |
> | storageAccounts/services/metrics | Nein | Nein |
> | storageAccounts/tableServices | Nein | Nein |
> | storageAccounts/vmImages | Nein | Nein |
> | vmImages | Nein | Nein |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | nodes | Ja | Ja |

## <a name="microsoftcodesigning"></a>Microsoft.CodeSigning

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | codeSigningAccounts | Nein | Nein |
> | codeSigningAccounts/certificateProfiles | Nein | Nein |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | plans | Ja | Nein |
> | registeredSubscriptions | Nein | Nein |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/privateEndpointConnectionProxies | Nein | Nein |
> | accounts/privateEndpointConnections | Nein | Nein |
> | accounts/privateLinkResources | Nein | Nein |
> | deletedAccounts | Nein | Nein |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | RateCard | Nein | Nein |
> | UsageAggregates | Nein | Nein |


## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Ja | Ja |
> | cloudServices | Ja | Ja |
> | cloudServices/networkInterfaces | Nein | Nein |
> | cloudServices/publicIPAddresses | Nein | Nein |
> | cloudServices/roleInstances | Nein | Nein |
> | cloudServices/roleInstances/networkInterfaces | Nein | Nein |
> | cloudServices/roles | Nein | Nein |
> | diskAccesses | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | disks | Ja | Ja |
> | galleries | Ja | Ja |
> | galleries/applications | Ja | Nein |
> | galleries/applications/versions | Ja | Nein |
> | galleries/images | Ja | Nein |
> | galleries/images/versions | Ja | Nein |
> | hostGroups | Ja | Ja |
> | hostGroups/hosts | Ja | Ja |
> | images | Ja | Ja |
> | proximityPlacementGroups | Ja | Ja |
> | restorePointCollections | Ja | Ja |
> | restorePointCollections/restorePoints | Nein | Nein |
> | sharedVMExtensions | Ja | Ja |
> | sharedVMExtensions/versions | Nein | Nein |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages/versions | Nein | Nein |
> | snapshots | Ja | Ja |
> | sshPublicKeys | Ja | Ja |
> | virtualMachines | Ja | Ja |
> | virtualMachines/extensions | Ja | Ja |
> | virtualMachines/metricDefinitions | Nein | Nein |
> | virtualMachines/runCommands | Ja | Ja |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets/extensions | Nein | Nein |
> | virtualMachineScaleSets/networkInterfaces | Nein | Nein |
> | virtualMachineScaleSets/publicIPAddresses | Ja | Nein |
> | virtualMachineScaleSets/virtualMachines | Nein | Nein |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nein | Nein |

> [!NOTE]
> Einem virtuellen Computer, der als generalisiert markiert wurde, kann kein Tag hinzugefügt werden. Sie markieren einen virtuellen Computer als generalisiert mit [Set-AzVm -Generalized](/powershell/module/Az.Compute/Set-AzVM) oder [az vm generalize](/cli/azure/vm#az-vm-generalize).

## <a name="microsoftconfidentialledger"></a>Microsoft.ConfidentialLedger

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Ledgers | Nein | Nein |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Nein | Nein |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | platformAccounts | Nein | Nein |
> | registeredSubscriptions | Nein | Nein |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Cluster | Nein | Nein |
> | Datenspeicher | Nein | Nein |
> | Hosts | Nein | Nein |
> | ResourcePools | Nein | Nein |
> | VCenters | Nein | Nein |
> | VCenters/InventoryItems | Nein | Nein |
> | VirtualMachines | Nein | Nein |
> | VirtualMachines/Extensions | Ja | Ja |
> | VirtualMachines/GuestAgents | Nein | Nein |
> | VirtualMachines/HybridIdentityMetadata | Nein | Nein |
> | VirtualMachineTemplates | Nein | Nein |
> | VirtualNetworks | Nein | Nein |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nein | Nein |
> | Bilanzen | Nein | Nein |
> | Budgets | Nein | Nein |
> | Charges | Nein | Nein |
> | CostTags | Nein | Nein |
> | credits | Nein | Nein |
> | events | Nein | Nein |
> | Vorhersagen | Nein | Nein |
> | lots | Nein | Nein |
> | Marketplaces | Nein | Nein |
> | Pricesheets | Nein | Nein |
> | products | Nein | Nein |
> | ReservationDetails | Nein | Nein |
> | ReservationRecommendationDetails | Nein | Nein |
> | ReservationRecommendations | Nein | Nein |
> | ReservationSummaries | Nein | Nein |
> | ReservationTransactions | Nein | Nein |
> | Tags | Nein | Nein |
> | tenants | Nein | Nein |
> | Begriffe | Nein | Nein |
> | UsageDetails | Nein | Nein |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ja | Ja |
> | serviceAssociationLinks | Nein | Nein |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | registries | Ja | Ja |
> | registries/agentPools | Ja | Ja |
> | registries/builds | Nein | Nein |
> | registries/builds/cancel | Nein | Nein |
> | registries/builds/getLogLink | Nein | Nein |
> | registries/buildTasks | Ja | Ja |
> | registries/buildTasks/steps | Nein | Nein |
> | registries/connectedRegistries | Nein | Nein |
> | registries/connectedRegistries/deactivate | Nein | Nein |
> | registries/eventGridFilters | Nein | Nein |
> | registries/exportPipelines | Nein | Nein |
> | registries/generateCredentials | Nein | Nein |
> | registries/getBuildSourceUploadUrl | Nein | Nein |
> | registries/GetCredentials | Nein | Nein |
> | registries/importImage | Nein | Nein |
> | registries/importPipelines | Nein | Nein |
> | registries/pipelineRuns | Nein | Nein |
> | registries/privateEndpointConnectionProxies | Nein | Nein |
> | registries/privateEndpointConnectionProxies/validate | Nein | Nein |
> | registries/privateEndpointConnections | Nein | Nein |
> | registries/privateLinkResources | Nein | Nein |
> | registries/queueBuild | Nein | Nein |
> | registries/regenerateCredential | Nein | Nein |
> | registries/regenerateCredentials | Nein | Nein |
> | registries/replications | Ja | Ja |
> | registries/runs | Nein | Nein |
> | registries/runs/cancel | Nein | Nein |
> | registries/scheduleRun | Nein | Nein |
> | registries/scopeMaps | Nein | Nein |
> | registries/taskRuns | Nein | Nein |
> | registries/tasks | Ja | Ja |
> | registries/tokens | Nein | Nein |
> | registries/updatePolicies | Nein | Nein |
> | registries/webhooks | Ja | Ja |
> | registries/webhooks/getCallbackConfig | Nein | Nein |
> | registries/webhooks/ping | Nein | Nein |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | ManagedClusters/eventGridFilters | Nein | Nein |
> | openShiftManagedClusters | Ja | Ja |
> | snapshots | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Alerts | Nein | Nein |
> | BenefitUtilizationSummaries | Nein | Nein |
> | BillingAccounts | Nein | Nein |
> | Budgets | Nein | Nein |
> | calculatePrice | Nein | Nein |
> | CloudConnectors | Nein | Nein |
> | Connectors | Ja | Ja |
> | costAllocationRules | Nein | Nein |
> | Departments | Nein | Nein |
> | Dimensionen | Nein | Nein |
> | EnrollmentAccounts | Nein | Nein |
> | Exports | Nein | Nein |
> | ExternalBillingAccounts | Nein | Nein |
> | ExternalBillingAccounts/Alerts | Nein | Nein |
> | ExternalBillingAccounts/Dimensions | Nein | Nein |
> | ExternalBillingAccounts/Forecast | Nein | Nein |
> | ExternalBillingAccounts/Query | Nein | Nein |
> | ExternalSubscriptions | Nein | Nein |
> | ExternalSubscriptions/Alerts | Nein | Nein |
> | ExternalSubscriptions/Dimensions | Nein | Nein |
> | ExternalSubscriptions/Forecast | Nein | Nein |
> | ExternalSubscriptions/Query | Nein | Nein |
> | fetchPrices | Nein | Nein |
> | Forecast | Nein | Nein |
> | GenerateDetailedCostReport | Nein | Nein |
> | GenerateReservationDetailsReport | Nein | Nein |
> | Einblicke | Nein | Nein |
> | Abfrage | Nein | Nein |
> | Registrieren | Nein | Nein |
> | Reportconfigs | Nein | Nein |
> | Berichte | Nein | Nein |
> | ScheduledActions | Nein | Nein |
> | Einstellungen | Nein | Nein |
> | showbackRules | Nein | Nein |
> | Sichten | Nein | Nein |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | DisableLockbox | Nein | Nein |
> | EnableLockbox | Nein | Nein |
> | requests | Nein | Nein |
> | TenantOptedIn | Nein | Nein |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | associations | Nein | Nein |
> | resourceProviders | Ja | Ja |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | instances | Ja | Ja |

## <a name="microsoftdashboard"></a>Microsoft.Dashboard

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | grafana | Nein | Nein |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | jobs | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | workspaces | Ja | Ja |
> | workspaces/dbWorkspaces | Nein | Nein |
> | workspaces/virtualNetworkPeerings | Nein | Nein |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | catalogs | Ja | Ja |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | dataFactories | Ja | Ja |
> | dataFactories/diagnosticSettings | Nein | Nein |
> | dataFactories/metricDefinitions | Nein | Nein |
> | dataFactorySchema | Nein | Nein |
> | factories | Ja | Ja |
> | factories/integrationRuntimes | Nein | Nein |

> [!NOTE]
> Wenn Sie in Ihrer Data Factory über Azure-SSIS Integration Runtime-Instanzen verfügen, werden deren laufende Kosten mit Data Factory-Tags gekennzeichnet. Ausgeführte Azure-SSIS Integration Runtime-Instanzen müssen beendet und neu gestartet werden, damit neue Data Factory-Tags ihren laufenden Kosten zugeordnet werden.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/dataLakeStoreAccounts | Nein | Nein |
> | accounts/storageAccounts | Nein | Nein |
> | accounts/storageAccounts/containers | Nein | Nein |
> | accounts/transferAnalyticsUnits | Nein | Nein |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/eventGridFilters | Nein | Nein |
> | accounts/firewallRules | Nein | Nein |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | DatabaseMigrations | Nein | Nein |
> | services | Ja | Ja |
> | services/projects | Ja | Ja |
> | SqlMigrationServices | Ja | Ja |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Ja | Ja |
> | ResourceGuards | Ja | Ja |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/shares | Nein | Nein |
> | accounts/shares/datasets | Nein | Nein |
> | accounts/shares/invitations | Nein | Nein |
> | accounts/shares/providersharesubscriptions | Nein | Nein |
> | accounts/shares/synchronizationSettings | Nein | Nein |
> | accounts/sharesubscriptions | Nein | Nein |
> | accounts/sharesubscriptions/consumerSourceDataSets | Nein | Nein |
> | accounts/sharesubscriptions/datasetmappings | Nein | Nein |
> | accounts/sharesubscriptions/triggers | Nein | Nein |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | servers | Ja | Ja |
> | servers/advisors | Nein | Nein |
> | servers/keys | Nein | Nein |
> | servers/privateEndpointConnectionProxies | Nein | Nein |
> | servers/privateEndpointConnections | Nein | Nein |
> | servers/privateLinkResources | Nein | Nein |
> | servers/queryTexts | Nein | Nein |
> | servers/recoverableServers | Nein | Nein |
> | servers/resetQueryPerformanceInsightData | Nein | Nein |
> | servers/start | Nein | Nein |
> | servers/stop | Nein | Nein |
> | servers/topQueryStatistics | Nein | Nein |
> | servers/virtualNetworkRules | Nein | Nein |
> | servers/waitStatistics | Nein | Nein |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ja | Ja |
> | getPrivateDnsZoneSuffix | Nein | Nein |
> | servers | Ja | Ja |
> | servers/advisors | Nein | Nein |
> | servers/keys | Nein | Nein |
> | servers/privateEndpointConnectionProxies | Nein | Nein |
> | servers/privateEndpointConnections | Nein | Nein |
> | servers/privateLinkResources | Nein | Nein |
> | servers/queryTexts | Nein | Nein |
> | servers/recoverableServers | Nein | Nein |
> | servers/resetQueryPerformanceInsightData | Nein | Nein |
> | servers/start | Nein | Nein |
> | servers/stop | Nein | Nein |
> | servers/topQueryStatistics | Nein | Nein |
> | servers/upgrade | Nein | Nein |
> | servers/virtualNetworkRules | Nein | Nein |
> | servers/waitStatistics | Nein | Nein |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ja | Ja |
> | getPrivateDnsZoneSuffix | Nein | Nein |
> | serverGroups | Ja | Ja |
> | serverGroupsv2 | Ja | Ja |
> | servers | Ja | Ja |
> | servers/advisors | Nein | Nein |
> | servers/keys | Nein | Nein |
> | servers/privateEndpointConnectionProxies | Nein | Nein |
> | servers/privateEndpointConnections | Nein | Nein |
> | servers/privateLinkResources | Nein | Nein |
> | servers/queryTexts | Nein | Nein |
> | servers/recoverableServers | Nein | Nein |
> | servers/resetQueryPerformanceInsightData | Nein | Nein |
> | servers/topQueryStatistics | Nein | Nein |
> | servers/virtualNetworkRules | Nein | Nein |
> | servers/waitStatistics | Nein | Nein |
> | serversv2 | Ja | Ja |

## <a name="microsoftdelegatednetwork"></a>Microsoft.DelegatedNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Controller | Ja | Ja |
> | delegatedSubnets | Ja | Ja |
> | orchestrators | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | artifactSources | Ja | Ja |
> | rollouts | Ja | Ja |
> | serviceTopologies | Ja | Ja |
> | serviceTopologies/services | Ja | Ja |
> | serviceTopologies/services/serviceUnits | Ja | Ja |
> | steps | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Ja | Ja |
> | applicationgroups/applications | Nein | Nein |
> | applicationgroups/desktops | Nein | Nein |
> | applicationgroups/startmenuitems | Nein | Nein |
> | hostpools | Ja | Ja |
> | hostpools/msixpackages | Nein | Nein |
> | hostpools/sessionhosts | Nein | Nein |
> | hostpools/sessionhosts/usersessions | Nein | Nein |
> | hostpools/usersessions | Nein | Nein |
> | scalingplans | Ja | Ja |
> | workspaces | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools/IotHubTenants | Ja | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Nein | Nein |
> | IotHubs | Ja | Ja |
> | IotHubs/eventGridFilters | Nein | Nein |
> | IotHubs/failover | Nein | Nein |
> | IotHubs/securitySettings | Nein | Nein |
> | ProvisioningServices | Ja | Ja |
> | usages | Nein | Nein |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Nein | Nein |
> | accounts/instances | Nein | Nein |
> | registeredSubscriptions | Nein | Nein |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | pipelines | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Controller | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | labcenters | Ja | Ja |
> | labs | Ja | Ja |
> | labs/environments | Ja | Ja |
> | labs/serviceRunners | Ja | Ja |
> | labs/virtualMachines | Ja | Ja |
> | schedules | Ja | Ja |

## <a name="microsoftdiagnostics"></a>Microsoft.Diagnostics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | AzureKB | Nein | Nein |
> | InsightDiagnostics | Nein | Nein |
> | solutions | Nein | Nein |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Ja | Ja |
> | digitalTwinsInstances/endpoints | Nein | Nein |
> | digitalTwinsInstances/timeSeriesDatabaseConnections | Nein | Nein |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | cassandraClusters | Ja | Ja |
> | databaseAccountNames | Nein | Nein |
> | databaseAccounts | Ja | Ja |
> | restorableDatabaseAccounts | Nein | Nein |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | domains | Ja | Ja |
> | domains/domainOwnershipIdentifiers | Nein | Nein |
> | generateSsoRequest | Nein | Nein |
> | topLevelDomains | Nein | Nein |
> | validateDomainRegistrationInformation | Nein | Nein |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nein | Nein |
> | lcsprojects/clouddeployments | Nein | Nein |
> | lcsprojects/connectors | Nein | Nein |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Adressen | Ja | Ja |
> | orderItems | Ja | Ja |
> | Aufträge | Nein | Nein |
> | productFamiliesMetadata | Nein | Nein |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | domains | Ja | Ja |
> | domains/topics | Nein | Nein |
> | eventSubscriptions | Nein | Nein |
> | extensionTopics | Nein | Nein |
> | partnerNamespaces | Ja | Ja |
> | partnerNamespaces/eventChannels | Nein | Nein |
> | partnerRegistrations | Ja | Ja |
> | partnerTopics | Ja | Ja |
> | partnerTopics/eventSubscriptions | Nein | Nein |
> | systemTopics | Ja | Ja |
> | systemTopics/eventSubscriptions | Nein | Nein |
> | topics | Ja | Ja |
> | topicTypes | Nein | Nein |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | Namespaces | Ja | Ja |
> | namespaces/authorizationrules | Nein | Nein |
> | namespaces/disasterrecoveryconfigs | Nein | Nein |
> | namespaces/eventhubs | Nein | Nein |
> | namespaces/eventhubs/authorizationrules | Nein | Nein |
> | namespaces/eventhubs/consumergroups | Nein | Nein |
> | namespaces/networkrulesets | Nein | Nein |
> | namespaces/privateEndpointConnections | Nein | Nein |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Ja | Ja |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Namespaces | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | featureConfigurations | Nein | Nein |
> | featureProviderNamespaces | Nein | Nein |
> | featureProviders | Nein | Nein |
> | Features | Nein | Nein |
> | providers | Nein | Nein |
> | subscriptionFeatureRegistrations | Nein | Nein |

## <a name="microsoftfidalgo"></a>Microsoft.Fidalgo

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | devcenters | Nein | Nein |
> | devcenters/catalogs | Nein | Nein |
> | devcenters/catalogs/items | Nein | Nein |
> | devcenters/environmentTypes | Nein | Nein |
> | devcenters/mappings | Nein | Nein |
> | machinedefinitions | Nein | Nein |
> | networksettings | Nein | Nein |
> | projects | Nein | Nein |
> | projects/catalogItems | Nein | Nein |
> | projects/environments | Nein | Nein |
> | projects/environments/deployments | Nein | Nein |
> | projects/environmentTypes | Nein | Nein |
> | projects/pools | Nein | Nein |

## <a name="microsoftfluidrelay"></a>Microsoft.FluidRelay

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | fluidRelayServers | Nein | Nein |
> | fluidRelayServers/fluidRelayContainers | Nein | Nein |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | enroll | Nein | Nein |
> | galleryitems | Nein | Nein |
> | generateartifactaccessuri | Nein | Nein |
> | myareas | Nein | Nein |
> | myareas/areas | Nein | Nein |
> | myareas/areas/areas | Nein | Nein |
> | myareas/areas/areas/galleryitems | Nein | Nein |
> | myareas/areas/galleryitems | Nein | Nein |
> | myareas/galleryitems | Nein | Nein |
> | Registrieren | Nein | Nein |
> | ressourcen | Nein | Nein |
> | retrieveresourcesbyid | Nein | Nein |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftgraph"></a>Microsoft.Graph

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | AzureAdApplication | Nein | Nein |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Ja | Ja |
> | autoManagedVmConfigurationProfiles | Ja | Ja |
> | configurationProfileAssignments | Nein | Nein |
> | guestConfigurationAssignments | Nein | Nein |
> | software | Nein | Nein |
> | softwareUpdateProfile | Nein | Nein |
> | softwareUpdates | Nein | Nein |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ja | Ja |
> | sapMonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | clusterPools | Ja | Ja |
> | clusterPools/clusters | Ja | Ja |
> | clusterPools/clusters/instanceViews | Nein | Nein |
> | clusterPools/clusters/serviceConfigs | Nein | Nein |
> | clusterPools/clusters/sessionClusters | Ja | Ja |
> | clusterPools/clusters/sessionClusters/instanceViews | Nein | Nein |
> | clusterPools/clusters/sessionClusters/serviceConfigs | Nein | Nein |
> | clusters | Ja | Ja |
> | clusters/applications | Nein | Nein |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | healthBots | Nein | Nein |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |
> | services/iomtconnectors | Nein | Nein |
> | services/iomtconnectors/connections | Nein | Nein |
> | services/iomtconnectors/mappings | Nein | Nein |
> | services/privateEndpointConnectionProxies | Nein | Nein |
> | services/privateEndpointConnections | Nein | Nein |
> | services/privateLinkResources | Nein | Nein |
> | workspaces | Ja | Ja |
> | workspaces/dicomservices | Ja | Ja |
> | workspaces/eventGridFilters | Nein | Nein |
> | workspaces/fhirservices | Ja | Ja |
> | workspaces/iotconnectors | Ja | Ja |
> | workspaces/iotconnectors/destinations | Nein | Nein |
> | workspaces/iotconnectors/fhirdestinations | Nein | Nein |
> | workspaces/privateEndpointConnectionProxies | Nein | Nein |
> | workspaces/privateEndpointConnections | Nein | Nein |
> | workspaces/privateLinkResources | Nein | Nein |

## <a name="microsofthpcworkbench"></a>Microsoft.HpcWorkbench

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | instances | Nein | Nein |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | machines | Ja | Ja |
> | machines/assessPatches | Nein | Nein |
> | machines/extensions | Ja | Ja |
> | machines/installPatches | Nein | Nein |
> | machines/privateLinkScopes | Nein | Nein |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes/privateEndpointConnectionProxies | Nein | Nein |
> | privateLinkScopes/privateEndpointConnections | Nein | Nein |

## <a name="microsofthybridconnectivity"></a>Microsoft.HybridConnectivity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | -Endpunkte | Nein | Nein |

## <a name="microsofthybridcontainerservice"></a>Microsoft.HybridContainerService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | provisionedClusters | Nein | Nein |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | dataManagers | Ja | Ja |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | devices | Nein | Nein |
> | networkFunctions | Nein | Nein |
> | networkFunctionVendors | Nein | Nein |
> | registeredSubscriptions | Nein | Nein |
> | vendors | Nein | Nein |
> | vendors/vendorSkus | Nein | Nein |
> | vendors/vendorskus/previewSubscriptions | Nein | Nein |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | components | Ja | Ja |
> | networkScopes | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | jobs | Ja | Ja |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | actionGroups | Ja | Ja |
> | activityLogAlerts | Ja | Ja |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | components | Ja | Ja |
> | components/linkedStorageAccounts | Nein | Nein |
> | components/ProactiveDetectionConfigs | Nein | Nein |
> | diagnosticSettings | Nein | Nein |
> | guestDiagnosticSettings | Ja | Ja |
> | guestDiagnosticSettingsAssociation | Ja | Ja |
> | logprofiles | Ja | Ja |
> | metricAlerts | Ja | Ja |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes/privateEndpointConnections | Nein | Nein |
> | privateLinkScopes/scopedResources | Nein | Nein |
> | queryPacks | Ja | Ja |
> | queryPacks/queries | Nein | Nein |
> | scheduledQueryRules | Ja | Ja |
> | webtests | Ja | Ja |
> | workbooks | Ja | Ja |
> | workbooktemplates | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nein | Nein |
> | diagnosticSettingsCategories | Nein | Nein |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nein | Nein |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | alertTypes | Nein | Nein |
> | defenderSettings | Nein | Nein |
> | onPremiseSensors | Nein | Nein |
> | recommendationTypes | Nein | Nein |
> | sensors | Nein | Nein |
> | sites | Nein | Nein |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | deletedManagedHSMs | Nein | Nein |
> | deletedVaults | Nein | Nein |
> | hsmPools | Ja | Ja |
> | managedHSMs | Ja | Ja |
> | vaults | Ja | Ja |
> | vaults/accessPolicies | Nein | Nein |
> | vaults/eventGridFilters | Nein | Nein |
> | vaults/keys | Nein | Nein |
> | vaults/keys/versions | Nein | Nein |
> | vaults/secrets | Nein | Nein |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Nein | Nein |
> | registeredSubscriptions | Nein | Nein |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Erweiterungen | Nein | Nein |
> | fluxConfigurations | Nein | Nein |
> | sourceControlConfigurations | Nein | Nein |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters/attacheddatabaseconfigurations | Nein | Nein |
> | clusters/databases | Nein | Nein |
> | clusters/databases/dataconnections | Nein | Nein |
> | clusters/databases/eventhubconnections | Nein | Nein |
> | clusters/databases/principalassignments | Nein | Nein |
> | clusters/databases/scripts | Nein | Nein |
> | clusters/dataconnections | Nein | Nein |
> | clusters/principalassignments | Nein | Nein |
> | clusters/sharedidentities | Nein | Nein |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Nein |
> | labplans | Ja | Ja |
> | labs | Ja | Ja |
> | users | Nein | Nein |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ja | Ja |
> | integrationAccounts | Ja | Ja |
> | integrationServiceEnvironments | Ja | Ja |
> | integrationServiceEnvironments/managedApis | Nein | Nein |
> | isolatedEnvironments | Ja | Ja |
> | workflows | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ja | Ja |
> | webServices | Ja | Ja |
> | Arbeitsbereiche | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | aisysteminventories | Ja | Ja |
> | virtualclusters | Ja | Ja |
> | workspaces | Ja | Ja |
> | workspaces/batchEndpoints | Ja | Ja |
> | workspaces/batchEndpoints/deployments | Ja | Ja |
> | workspaces/batchEndpoints/deployments/jobs | Nein | Nein |
> | workspaces/batchEndpoints/jobs | Nein | Nein |
> | workspaces/codes | Nein | Nein |
> | workspaces/codes/versions | Nein | Nein |
> | workspaces/components | Nein | Nein |
> | workspaces/components/versions | Nein | Nein |
> | workspaces/computes | Nein | Nein |
> | workspaces/data | Nein | Nein |
> | workspaces/datasets | Nein | Nein |
> | workspaces/datastores | Nein | Nein |
> | workspaces/environments | Nein | Nein |
> | workspaces/eventGridFilters | Nein | Nein |
> | workspaces/jobs | Nein | Nein |
> | workspaces/labelingJobs | Nein | Nein |
> | workspaces/linkedServices | Nein | Nein |
> | workspaces/models | Nein | Nein |
> | workspaces/models/versions | Nein | Nein |
> | workspaces/onlineEndpoints | Ja | Ja |
> | workspaces/onlineEndpoints/deployments | Ja | Ja |

> [!NOTE]
> Arbeitsbereichtags werden nicht an Compute-Cluster und Compute-Instanzen weitergegeben.

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Nein | Nein |
> | configurationAssignments | Nein | Nein |
> | maintenanceConfigurations | Ja | Ja |
> | publicMaintenanceConfigurations | Nein | Nein |
> | updates | Nein | Nein |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Identities | Nein | Nein |
> | userAssignedIdentities | Ja | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nein | Nein |
> | registrationAssignments | Nein | Nein |
> | registrationDefinitions | Nein | Nein |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | getEntities | Nein | Nein |
> | managementGroups | Nein | Nein |
> | managementGroups/settings | Nein | Nein |
> | ressourcen | Nein | Nein |
> | startTenantBackfill | Nein | Nein |
> | tenantBackfillStatus | Nein | Nein |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/creators | Ja | Ja |
> | accounts/eventGridFilters | Nein | Nein |
> | accounts/privateAtlases | Ja | Ja |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | macc | Nein | Nein |
> | offers | Nein | Nein |
> | offerTypes | Nein | Nein |
> | offerTypes/publishers | Nein | Nein |
> | offerTypes/publishers/offers | Nein | Nein |
> | offerTypes/publishers/offers/plans | Nein | Nein |
> | offerTypes/publishers/offers/plans/agreements | Nein | Nein |
> | offerTypes/publishers/offers/plans/configs | Nein | Nein |
> | offerTypes/publishers/offers/plans/configs/importImage | Nein | Nein |
> | privategalleryitems | Nein | Nein |
> | privateStoreClient | Nein | Nein |
> | privateStores | Nein | Nein |
> | privateStores/AdminRequestApprovals | Nein | Nein |
> | privateStores/billingAccounts | Nein | Nein |
> | privateStores/bulkCollectionsAction | Nein | Nein |
> | privateStores/collections | Nein | Nein |
> | privateStores/collections/offers | Nein | Nein |
> | privateStores/collections/transferOffers | Nein | Nein |
> | privateStores/collectionsToSubscriptionsMapping | Nein | Nein |
> | privateStores/offers | Nein | Nein |
> | privateStores/offers/acknowledgeNotification | Nein | Nein |
> | privateStores/queryApprovedPlans | Nein | Nein |
> | privateStores/queryNotificationsState | Nein | Nein |
> | privateStores/queryOffers | Nein | Nein |
> | privateStores/RequestApprovals | Nein | Nein |
> | privateStores/requestApprovals/query | Nein | Nein |
> | privateStores/requestApprovals/withdrawPlan | Nein | Nein |
> | products | Nein | Nein |
> | publishers | Nein | Nein |
> | publishers/offers | Nein | Nein |
> | publishers/offers/amendments | Nein | Nein |
> | Registrieren | Nein | Nein |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | updateCommunicationPreference | Nein | Nein |

## <a name="microsoftmarketplacenotifications"></a>Microsoft.MarketplaceNotifications

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | reviewsnotifications | Nein | Nein |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | agreements | Nein | Nein |
> | offertypes | Nein | Nein |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | mediaservices | Ja | Ja |
> | mediaservices/accountFilters | Nein | Nein |
> | mediaservices/assets | Nein | Nein |
> | mediaservices/assets/assetFilters | Nein | Nein |
> | mediaservices/contentKeyPolicies | Nein | Nein |
> | mediaservices/eventGridFilters | Nein | Nein |
> | mediaservices/graphInstances | Nein | Nein |
> | mediaservices/graphTopologies | Nein | Nein |
> | mediaservices/liveEventOperations | Nein | Nein |
> | mediaservices/liveEvents | Ja | Ja |
> | mediaservices/liveEvents/liveOutputs | Nein | Nein |
> | mediaservices/liveOutputOperations | Nein | Nein |
> | mediaservices/mediaGraphs | Nein | Nein |
> | mediaservices/privateEndpointConnectionOperations | Nein | Nein |
> | mediaservices/privateEndpointConnectionProxies | Nein | Nein |
> | mediaservices/privateEndpointConnections | Nein | Nein |
> | mediaservices/streamingEndpointOperations | Nein | Nein |
> | mediaservices/streamingEndpoints | Ja | Ja |
> | mediaservices/streamingLocators | Nein | Nein |
> | mediaservices/streamingPolicies | Nein | Nein |
> | mediaservices/transforms | Nein | Nein |
> | mediaservices/transforms/jobs | Nein | Nein |
> | videoAnalyzers | Ja | Ja |
> | videoAnalyzers/accessPolicies | Nein | Nein |
> | videoAnalyzers/edgeModules | Nein | Nein |
> | videoAnalyzers/livePipelines | Nein | Nein |
> | videoAnalyzers/pipelineJobs | Nein | Nein |
> | videoAnalyzers/pipelineTopologies | Nein | Nein |
> | videoAnalyzers/videos | Nein | Nein |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Ja | Ja |
> | migrateprojects | Ja | Ja |
> | moveCollections | Ja | Ja |
> | projects | Ja | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ja | Ja |
> | objectAnchorsAccounts | Ja | Ja |
> | objectUnderstandingAccounts | Ja | Ja |
> | remoteRenderingAccounts | Ja | Ja |
> | spatialAnchorsAccounts | Ja | Ja |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | mobileNetworks | Nein | Nein |
> | mobileNetworks/dataNetworks | Nein | Nein |
> | mobileNetworks/services | Nein | Nein |
> | mobileNetworks/simPolicies | Nein | Nein |
> | mobileNetworks/sites | Nein | Nein |
> | mobileNetworks/slices | Nein | Nein |
> | networks | Nein | Nein |
> | networks/sites | Nein | Nein |
> | packetCoreControlPlanes | Nein | Nein |
> | packetCoreControlPlanes/packetCoreDataPlanes | Nein | Nein |
> | packetCoreControlPlanes/packetCoreDataPlanes/attachedDataNetworks | Nein | Nein |
> | packetCores | Nein | Nein |
> | sims | Nein | Nein |
> | sims/simProfiles | Nein | Nein |

## <a name="microsoftmonitor"></a>Microsoft.Monitor

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ja | Nein |
> | netAppAccounts/accountBackups | Nein | Nein |
> | netappaccounts/capacitypools | Ja | Ja |
> | netappaccounts/capacitypools/volumes | Ja | Nein |
> | netappaccounts/capacitypools/volumes/snapshots | Nein | Nein |
> | netAppAccounts/capacityPools/volumes/subvolumes | Nein | Nein |
> | netAppAccounts/snapshotPolicies | Ja | Ja |
> | netAppAccounts/volumeGroups | Nein | Nein |

## <a name="microsoftnetworkfunction"></a>Microsoft.NetworkFunction

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | azureTrafficCollectors | Ja | Ja |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ja | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Nein | Nein |
> | azureFirewalls | Ja | Nein |
> | bastionHosts | Ja | Nein |
> | bgpServiceCommunities | Nein | Nein |
> | connections | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatuses | Nein | Nein |
> | dnszones | Ja | Ja |
> | dnszones/A | Nein | Nein |
> | dnszones/AAAA | Nein | Nein |
> | dnszones/all | Nein | Nein |
> | dnszones/CAA | Nein | Nein |
> | dnszones/CNAME | Nein | Nein |
> | dnszones/MX | Nein | Nein |
> | dnszones/NS | Nein | Nein |
> | dnszones/PTR | Nein | Nein |
> | dnszones/recordsets | Nein | Nein |
> | dnszones/SOA | Nein | Nein |
> | dnszones/SRV | Nein | Nein |
> | dnszones/TXT | Nein | Nein |
> | expressRouteCircuits | Ja | Ja |
> | expressRouteCrossConnections | Ja | Ja |
> | expressRouteGateways | Ja | Ja |
> | expressRoutePorts | Ja | Ja |
> | expressRouteServiceProviders | Nein | Nein |
> | firewallPolicies | Ja | Ja |
> | frontdoors | Ja, aber nur eingeschränkt (siehe [Hinweis weiter unten](#frontdoor)) | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ja, aber nur eingeschränkt (siehe [Hinweis weiter unten](#frontdoor)) | Nein |
> | frontdoorWebApplicationFirewallPolicies | Ja, aber nur eingeschränkt (siehe [Hinweis weiter unten](#frontdoor)) | Ja |
> | getDnsResourceReference | Nein | Nein |
> | internalNotify | Nein | Nein |
> | ipGroups | Ja | Ja |
> | loadBalancers | Ja | Ja |
> | localNetworkGateways | Ja | Ja |
> | natGateways | Ja | Ja |
> | networkIntentPolicies | Ja | Ja |
> | networkInterfaces | Ja | Ja |
> | networkProfiles | Ja | Ja |
> | networkSecurityGroups | Ja | Ja |
> | networkWatchers | Ja | Ja |
> | networkwatchers/connectionmonitors | Ja | Nein |
> | networkWatchers/flowLogs | Ja | Nein |
> | networkwatchers/lenses | Ja | Nein |
> | networkwatchers/pingmeshes | Ja | Nein |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatuses | Nein | Nein |
> | privateDnsZones | Ja | Ja |
> | privateDnsZones/A | Nein | Nein |
> | privateDnsZones/AAAA | Nein | Nein |
> | privateDnsZones/all | Nein | Nein |
> | privateDnsZones/CNAME | Nein | Nein |
> | privateDnsZones/MX | Nein | Nein |
> | privateDnsZones/PTR | Nein | Nein |
> | privateDnsZones/SOA | Nein | Nein |
> | privateDnsZones/SRV | Nein | Nein |
> | privateDnsZones/TXT | Nein | Nein |
> | privateDnsZones/virtualNetworkLinks | Ja | Ja |
> | privateEndpoints | Ja | Ja |
> | privateLinkServices | Ja | Ja |
> | publicIPAddresses | Ja | Ja |
> | publicIPPrefixes | Ja | Ja |
> | routeFilters | Ja | Ja |
> | routeTables | Ja | Ja |
> | serviceEndpointPolicies | Ja | Ja |
> | trafficManagerGeographicHierarchies | Nein | Nein |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/heatMaps | Nein | Nein |
> | trafficManagerUserMetricsKeys | Nein | Nein |
> | virtualHubs | Ja | Ja |
> | virtualNetworkGateways | Ja | Ja |
> | virtualNetworks | Ja | Ja |
> | virtualNetworks/subnets | Nein | Nein |
> | virtualNetworkTaps | Ja | Ja |
> | virtualWans | Ja | Nein |
> | vpnGateways | Ja | Ja |
> | vpnSites | Ja | Ja |
> | webApplicationFirewallPolicies | Ja | Ja |

<a id="frontdoor"></a>

> [!NOTE]
> Für Azure Front Door Service können Sie beim Erstellen von Ressourcen zwar Tags anwenden, aber das Aktualisieren und Hinzufügen von Tags wird derzeit nicht unterstützt.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Nein | Nein |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Namespaces | Ja | Nein |
> | namespaces/notificationHubs | Ja | Nein |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Nein | Nein |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ja | Ja |
> | ImportSites | Ja | Ja |
> | MasterSites | Ja | Ja |
> | ServerSites | Ja | Ja |
> | VMwareSites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | deletedWorkspaces | Nein | Nein |
> | linkTargets | Nein | Nein |
> | querypacks | Ja | Ja |
> | storageInsightConfigs | Nein | Nein |
> | workspaces | Ja | Ja |
> | workspaces/dataExports | Nein | Nein |
> | workspaces/dataSources | Nein | Nein |
> | workspaces/linkedServices | Nein | Nein |
> | workspaces/linkedStorageAccounts | Nein | Nein |
> | workspaces/metadata | Nein | Nein |
> | workspaces/query | Nein | Nein |
> | workspaces/scopedPrivateLinkProxies | Nein | Nein |
> | workspaces/storageInsightConfigs | Nein | Nein |
> | workspaces/tables | Nein | Nein |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nein | Nein |
> | managementconfigurations | Ja | Ja |
> | solutions | Ja | Ja |
> | views | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | cdnPeeringPrefixes | Nein | Nein |
> | legacyPeerings | Nein | Nein |
> | lookingGlass | Nein | Nein |
> | peerAsns | Nein | Nein |
> | peerings | Ja | Ja |
> | peeringServiceCountries | Nein | Nein |
> | peeringServiceProviders | Nein | Nein |
> | peeringServices | Ja | Ja |

## <a name="microsoftplayfab"></a>Microsoft.PlayFab

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | PlayerAccountPools | Nein | Nein |
> | Titel | Nein | Nein |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | attestations | Nein | Nein |
> | eventGridFilters | Nein | Nein |
> | policyEvents | Nein | Nein |
> | policyMetadata | Nein | Nein |
> | policyStates | Nein | Nein |
> | policyTrackedResources | Nein | Nein |
> | remediations | Nein | Nein |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | consoles | Nein | Nein |
> | dashboards | Ja | Ja |
> | tenantconfigurations | Nein | Nein |
> | userSettings | Nein | Nein |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Ja | Ja |
> | tenants | Ja | Ja |
> | tenants/workspaces | Nein | Nein |
> | workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | autoScaleVCores | Ja | Ja |
> | capacities | Ja | Ja |
> | servers | Ja | Ja |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | enterprisePolicies | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | deletedAccounts | Nein | Nein |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nein | Nein |
> | providerRegistrations/customRollouts | Nein | Nein |
> | providerRegistrations/defaultRollouts | Nein | Nein |
> | providerRegistrations/resourceActions | Nein | Nein |
> | providerRegistrations/resourceTypeRegistrations | Nein | Nein |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | deletedAccounts | Nein | Nein |
> | getDefaultAccount | Nein | Nein |
> | removeDefaultAccount | Nein | Nein |
> | setDefaultAccount | Nein | Nein |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Arbeitsbereiche | Nein | Nein |

## <a name="microsoftquota"></a>Microsoft.Quota

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | quotaRequests | Nein | Nein |
> | quotas | Nein | Nein |
> | usages | Nein | Nein |

## <a name="microsoftrecommendationsservice"></a>Microsoft.RecommendationsService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Nein | Nein |
> | accounts/modeling | Nein | Nein |
> | accounts/serviceEndpoints | Nein | Nein |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nein | Nein |
> | vaults | Ja | Ja |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Namespaces | Ja | Ja |
> | namespaces/authorizationrules | Nein | Nein |
> | namespaces/hybridconnections | Nein | Nein |
> | namespaces/hybridconnections/authorizationrules | Nein | Nein |
> | namespaces/privateEndpointConnections | Nein | Nein |
> | namespaces/wcfrelays | Nein | Nein |
> | namespaces/wcfrelays/authorizationrules | Nein | Nein |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | appliances | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Abfragen | Ja | Ja |
> | resourceChangeDetails | Nein | Nein |
> | resourceChanges | Nein | Nein |
> | ressourcen | Nein | Nein |
> | resourcesHistory | Nein | Nein |
> | subscriptionsStatus | Nein | Nein |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nein | Nein |
> | childAvailabilityStatuses | Nein | Nein |
> | childResources | Nein | Nein |
> | emergingissues | Nein | Nein |
> | events | Nein | Nein |
> | impactedResources | Nein | Nein |
> | metadata | Nein | Nein |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | deployments | Ja | Nein |
> | deployments/operations | Nein | Nein |
> | deploymentScripts | Ja | Ja |
> | deploymentScripts/logs | Nein | Nein |
> | deploymentStacks | Nein | Nein |
> | deploymentStacks/snapshots | Nein | Nein |
> | Verknüpfungen | Nein | Nein |
> | providers | Nein | Nein |
> | resourceGroups | Ja | Nein |
> | subscriptions | Ja | Nein |
> | templateSpecs | Ja | Ja |
> | templateSpecs/versions | Ja | Ja |
> | tenants | Nein | Nein |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | applications | Ja | Ja |
> | ressourcen | Ja | Ja |
> | saasresources | Nein | Nein |

## <a name="microsoftscom"></a>Microsoft.Scom

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | managedInstances | Nein | Nein |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | clouds | Nein | Nein |
> | VirtualMachines | Nein | Nein |
> | VirtualMachineTemplates | Nein | Nein |
> | VirtualNetworks | Nein | Nein |
> | vmmservers | Nein | Nein |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nein | Nein |
> | searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nein | Nein |
> | advancedThreatProtectionSettings | Nein | Nein |
> | alerts | Nein | Nein |
> | alertsSuppressionRules | Nein | Nein |
> | allowedConnections | Nein | Nein |
> | antiMalwareSettings | Nein | Nein |
> | applicationWhitelistings | Nein | Nein |
> | assessmentMetadata | Nein | Nein |
> | assessments | Nein | Nein |
> | Zuweisungen | Ja | Ja |
> | autoDismissAlertsRules | Nein | Nein |
> | automations | Ja | Ja |
> | AutoProvisioningSettings | Nein | Nein |
> | Compliances | Nein | Nein |
> | connectedContainerRegistries | Nein | Nein |
> | connectors | Nein | Nein |
> | customAssessmentAutomations | Ja | Ja |
> | customEntityStoreAssignments | Ja | Ja |
> | dataCollectionAgents | Nein | Nein |
> | deviceSecurityGroups | Nein | Nein |
> | discoveredSecuritySolutions | Nein | Nein |
> | externalSecuritySolutions | Nein | Nein |
> | InformationProtectionPolicies | Nein | Nein |
> | ingestionSettings | Nein | Nein |
> | insights | Nein | Nein |
> | iotSecuritySolutions | Ja | Ja |
> | iotSecuritySolutions/analyticsModels | Nein | Nein |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Nein | Nein |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Nein | Nein |
> | iotSecuritySolutions/iotAlerts | Nein | Nein |
> | iotSecuritySolutions/iotAlertTypes | Nein | Nein |
> | iotSecuritySolutions/iotRecommendations | Nein | Nein |
> | iotSecuritySolutions/iotRecommendationTypes | Nein | Nein |
> | jitNetworkAccessPolicies | Nein | Nein |
> | jitPolicies | Nein | Nein |
> | Richtlinien | Nein | Nein |
> | pricings | Nein | Nein |
> | regulatoryComplianceStandards | Nein | Nein |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Nein | Nein |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Nein | Nein |
> | secureScoreControlDefinitions | Nein | Nein |
> | secureScoreControls | Nein | Nein |
> | secureScores | Nein | Nein |
> | secureScores/secureScoreControls | Nein | Nein |
> | securityConnectors | Ja | Ja |
> | securityContacts | Nein | Nein |
> | securitySolutions | Nein | Nein |
> | securitySolutionsReferenceData | Nein | Nein |
> | securityStatuses | Nein | Nein |
> | securityStatusesSummaries | Nein | Nein |
> | serverVulnerabilityAssessments | Nein | Nein |
> | settings | Nein | Nein |
> | sqlVulnerabilityAssessments | Nein | Nein |
> | standards | Ja | Ja |
> | subAssessments | Nein | Nein |
> | Tasks | Nein | Nein |
> | topologies | Nein | Nein |
> | workspaceSettings | Nein | Nein |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nein | Nein |
> | diagnosticSettingsCategories | Nein | Nein |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | aggregations | Nein | Nein |
> | alertRules | Nein | Nein |
> | alertRuleTemplates | Nein | Nein |
> | automationRules | Nein | Nein |
> | bookmarks | Nein | Nein |
> | cases | Nein | Nein |
> | dataConnectors | Nein | Nein |
> | dataConnectorsCheckRequirements | Nein | Nein |
> | enrichment | Nein | Nein |
> | entities | Nein | Nein |
> | entityQueries | Nein | Nein |
> | entityQueryTemplates | Nein | Nein |
> | incidents | Nein | Nein |
> | metadata | Nein | Nein |
> | officeConsents | Nein | Nein |
> | onboardingStates | Nein | Nein |
> | settings | Nein | Nein |
> | sourceControls | Nein | Nein |
> | threatIntelligence | Nein | Nein |
> | watchlists | Nein | Nein |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | consoleServices | Nein | Nein |
> | serialPorts | Nein | Nein |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | Namespaces | Ja | Ja |
> | namespaces/authorizationrules | Nein | Nein |
> | namespaces/disasterrecoveryconfigs | Nein | Nein |
> | namespaces/eventgridfilters | Nein | Nein |
> | namespaces/networkrulesets | Nein | Nein |
> | namespaces/privateEndpointConnections | Nein | Nein |
> | namespaces/queues | Nein | Nein |
> | namespaces/queues/authorizationrules | Nein | Nein |
> | namespaces/topics | Nein | Nein |
> | namespaces/topics/authorizationrules | Nein | Nein |
> | namespaces/topics/subscriptions | Nein | Nein |
> | namespaces/topics/subscriptions/rules | Nein | Nein |
> | premiumMessagingRegions | Nein | Nein |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | applications | Ja | Ja |
> | clusters | Ja | Ja |
> | clusters/applications | Nein | Nein |
> | containerGroups | Ja | Ja |
> | containerGroupSets | Ja | Ja |
> | edgeclusters | Ja | Ja |
> | edgeclusters/applications | Nein | Nein |
> | managedclusters | Ja | Ja |
> | managedclusters/applications | Nein | Nein |
> | managedclusters/applications/services | Nein | Nein |
> | managedclusters/applicationTypes | Nein | Nein |
> | managedclusters/applicationTypes/versions | Nein | Nein |
> | managedclusters/nodetypes | Nein | Nein |
> | networks | Ja | Ja |
> | secretstores | Ja | Ja |
> | secretstores/certificates | Nein | Nein |
> | secretstores/secrets | Nein | Nein |
> | volumes | Ja | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | applications | Ja | Ja |
> | containerGroups | Ja | Ja |
> | gateways | Ja | Ja |
> | networks | Ja | Ja |
> | secrets | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | linkers | Nein | Nein |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nein | Nein |
> | providerRegistrations/resourceTypeRegistrations | Nein | Nein |
> | rollouts | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | SignalR/eventGridFilters | Nein | Nein |
> | WebPubSub | Ja | Ja |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/accountQuotaPolicies | Nein | Nein |
> | accounts/groupPolicies | Nein | Nein |
> | accounts/jobs | Nein | Nein |
> | accounts/models | Nein | Nein |
> | accounts/storageContainers | Nein | Nein |
> | images | Nein | Nein |
> | quotas | Nein | Nein |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nein | Nein |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ja | Ja |
> | applications | Ja | Ja |
> | jitRequests | Ja | Ja |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | longtermRetentionManagedInstance/longtermRetentionDatabase/longtermRetentionBackup | Nein | Nein |
> | longtermRetentionServer/longtermRetentionDatabase/longtermRetentionBackup | Nein | Nein |
> | managedInstances | Ja | Ja |
> | managedinstances/databases | Nein | Nein |
> | managedInstances/databases/backupShortTermRetentionPolicies | Nein | Nein |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nein | Nein |
> | managedInstances/databases/vulnerabilityAssessments | Nein | Nein |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Nein | Nein |
> | managedInstances/encryptionProtector | Nein | Nein |
> | managedInstances/keys | Nein | Nein |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nein | Nein |
> | managedInstances/vulnerabilityAssessments | Nein | Nein |
> | servers | Ja | Ja |
> | servers/administrators | Nein | Nein |
> | servers/communicationLinks | Nein | Nein |
> | servers/databases | Ja (siehe [Hinweis unten](#sqlnote)) | Ja |
> | servers/encryptionProtector | Nein | Nein |
> | servers/firewallRules | Nein | Nein |
> | servers/keys | Nein | Nein |
> | servers/restorableDroppedDatabases | Nein | Nein |
> | servers/serviceobjectives | Nein | Nein |
> | servers/tdeCertificates | Nein | Nein |
> | virtualClusters | Nein | Nein |

<a id="sqlnote"></a>

> [!NOTE]
> Die Masterdatenbank unterstützt keine Tags, diese werden jedoch in anderen Datenbanken, z. B. Azure Synapse Analytics-Datenbanken, unterstützt. Azure Synapse Analytics-Datenbanken müssen sich im aktiven (nicht angehaltenen) Zustand befinden.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ja | Ja |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Nein | Nein |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | Nein | Nein |
> | storageAccounts | Ja | Ja |
> | storageAccounts/blobServices | Nein | Nein |
> | storageAccounts/encryptionScopes | Nein | Nein |
> | storageAccounts/fileServices | Nein | Nein |
> | storageAccounts/queueServices | Nein | Nein |
> | storageAccounts/services | Nein | Nein |
> | storageAccounts/services/metricDefinitions | Nein | Nein |
> | storageAccounts/tableServices | Nein | Nein |
> | usages | Nein | Nein |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | amlFilesystems | Ja | Ja |
> | caches | Ja | Ja |
> | caches/storageTargets | Nein | Nein |
> | usageModels | Nein | Nein |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nein | Nein |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServers | Nein | Nein |
> | storageSyncServices/syncGroups | Nein | Nein |
> | storageSyncServices/syncGroups/cloudEndpoints | Nein | Nein |
> | storageSyncServices/syncGroups/serverEndpoints | Nein | Nein |
> | storageSyncServices/workflows | Nein | Nein |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | managers | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters/privateEndpoints | Nein | Nein |
> | streamingjobs | Ja (siehe Hinweis unten) | Ja |

> [!NOTE]
> Sie können kein Tag hinzufügen, wenn Streamingaufträge ausgeführt werden. Beenden Sie die Ressource, um ein Tag hinzuzufügen.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Nein | Nein |
> | acceptOwnership | Nein | Nein |
> | acceptOwnershipStatus | Nein | Nein |
> | aliases | Nein | Nein |
> | cancel | Nein | Nein |
> | changeTenantRequest | Nein | Nein |
> | changeTenantStatus | Nein | Nein |
> | CreateSubscription | Nein | Nein |
> | enable | Nein | Nein |
> | Richtlinien | Nein | Nein |
> | rename | Nein | Nein |
> | SubscriptionDefinitions | Nein | Nein |
> | SubscriptionOperations | Nein | Nein |
> | subscriptions | Nein | Nein |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | kustoOperations | Nein | Nein |
> | privateLinkHubs | Ja | Ja |
> | workspaces | Ja | Ja |
> | workspaces/bigDataPools | Ja | Ja |
> | workspaces/kustoPools | Ja | Ja |
> | workspaces/kustoPools/attacheddatabaseconfigurations | Nein | Nein |
> | workspaces/kustoPools/databases | Nein | Nein |
> | workspaces/kustoPools/databases/dataconnections | Nein | Nein |
> | workspaces/operationStatuses | Nein | Nein |
> | workspaces/sqlDatabases | Ja | Ja |
> | workspaces/sqlPools | Ja | Ja |

## <a name="microsofttestbase"></a>Microsoft.TestBase

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | testBaseAccounts | Nein | Nein |
> | testBaseAccounts/customerEvents | Nein | Nein |
> | testBaseAccounts/emailEvents | Nein | Nein |
> | testBaseAccounts/flightingRings | Nein | Nein |
> | testBaseAccounts/packages | Nein | Nein |
> | testBaseAccounts/packages/favoriteProcesses | Nein | Nein |
> | testBaseAccounts/packages/osUpdates | Nein | Nein |
> | testBaseAccounts/testSummaries | Nein | Nein |
> | testBaseAccounts/testTypes | Nein | Nein |
> | testBaseAccounts/usages | Nein | Nein |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | environments | Ja | Nein |
> | environments/accessPolicies | Nein | Nein |
> | environments/eventsources | Ja | Nein |
> | environments/privateEndpointConnectionProxies | Nein | Nein |
> | environments/privateEndpointConnections | Nein | Nein |
> | environments/privateLinkResources | Nein | Nein |
> | environments/referenceDataSets | Ja | Nein |

## <a name="microsoftvideoindexer"></a>Microsoft.VideoIndexer

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Nein | Nein |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Ja | Ja |
> | imageTemplates/runOutputs | Nein | Nein |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | arczones | Nein | Nein |
> | resourcepools | Nein | Nein |
> | vcenters | Nein | Nein |
> | VCenters/InventoryItems | Nein | Nein |
> | virtualmachines | Nein | Nein |
> | virtualmachinetemplates | Nein | Nein |
> | virtualnetworks | Nein | Nein |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ja | Ja |
> | dedicatedCloudServices | Ja | Ja |
> | virtualMachines | Ja | Ja |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Nein |
> | plans | Ja | Nein |
> | registeredSubscriptions | Nein | Nein |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nein | Nein |
> | diagnosticSettingsCategories | Nein | Nein |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nein | Nein |
> | apiManagementAccounts/apiAcls | Nein | Nein |
> | apiManagementAccounts/apis | Nein | Nein |
> | apiManagementAccounts/apis/apiAcls | Nein | Nein |
> | apiManagementAccounts/apis/connectionAcls | Nein | Nein |
> | apiManagementAccounts/apis/connections | Nein | Nein |
> | apiManagementAccounts/apis/connections/connectionAcls | Nein | Nein |
> | apiManagementAccounts/apis/localizedDefinitions | Nein | Nein |
> | apiManagementAccounts/connectionAcls | Nein | Nein |
> | apiManagementAccounts/connections | Nein | Nein |
> | billingMeters | Nein | Nein |
> | certificates | Ja | Ja |
> | connectionGateways | Ja | Ja |
> | connections | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Nein | Nein |
> | functionAppStacks | Nein | Nein |
> | generateGithubAccessTokenForAppserviceCLI | Nein | Nein |
> | hostingEnvironments | Ja | Ja |
> | hostingEnvironments/eventGridFilters | Nein | Nein |
> | hostingEnvironments/multiRolePools | Nein | Nein |
> | hostingEnvironments/workerPools | Nein | Nein |
> | kubeEnvironments | Ja | Ja |
> | publishingUsers | Nein | Nein |
> | empfehlungen | Nein | Nein |
> | resourceHealthMetadata | Nein | Nein |
> | runtimes | Nein | Nein |
> | serverFarms | Ja | Ja |
> | serverFarms/eventGridFilters | Nein | Nein |
> | serverFarms/firstPartyApps | Nein | Nein |
> | serverFarms/firstPartyApps/keyVaultSettings | Nein | Nein |
> | sites | Ja | Ja |
> | sites/config  | Nein | Nein |
> | sites/eventGridFilters | Nein | Nein |
> | sites/hostNameBindings | Nein | Nein |
> | sites/networkConfig | Nein | Nein |
> | sites/premieraddons | Ja | Ja |
> | sites/slots | Ja | Ja |
> | sites/slots/eventGridFilters | Nein | Nein |
> | sites/slots/hostNameBindings | Nein | Nein |
> | sites/slots/networkConfig | Nein | Nein |
> | sourceControls | Nein | Nein |
> | staticSites | Ja | Ja |
> | validate | Nein | Nein |
> | verifyHostingEnvironmentVnet | Nein | Nein |
> | webAppStacks | Nein | Nein |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Ja | Ja |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ja | Ja |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | migrationAgents | Nein | Nein |
> | workloads | Nein | Nein |
> | workloads/instances | Nein | Nein |
> | workloads/versions | Nein | Nein |
> | workloads/versions/artifacts | Nein | Nein |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Tagunterstützung | Tag in Kostenbericht |
> | ------------- | ----------- | ----------- |
> | monitors | Nein | Nein |

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Anwenden von Tags auf Ressourcen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](tag-resources.md).
