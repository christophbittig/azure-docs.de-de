---
title: Unterstützung des Verschiebevorgangs nach Ressourcentyp
description: Eine Liste der Azure-Ressourcentypen, die in eine neue Ressourcengruppe, ein neues Abonnement oder eine neue Region verschoben werden können.
ms.topic: conceptual
ms.date: 10/04/2021
ms.openlocfilehash: b5086256142c4ce72111dddcacaf19c4a40cdc35
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429336"
---
# <a name="move-operation-support-for-resources"></a>Unterstützung des Verschiebevorgangs für Ressourcen

In diesem Artikel wird aufgeführt, für welche Azure-Ressourcentypen der Verschiebevorgang unterstützt wird. Außerdem finden Sie hier Informationen zu speziellen Bedingungen, die beim Verschieben einer Ressource berücksichtigt werden müssen.

> [!IMPORTANT]
> In den meisten Fällen kann eine untergeordnete Ressource nicht unabhängig von der übergeordneten Ressource verschoben werden. Der Ressourcentyp von untergeordneten Ressourcen hat das Format `<resource-provider-namespace>/<parent-resource>/<child-resource>`. `Microsoft.ServiceBus/namespaces/queues` ist beispielsweise eine untergeordnete Ressource von `Microsoft.ServiceBus/namespaces`. Wenn Sie die übergeordnete Ressource verschieben, wird automatisch auch die untergeordnete Ressource verschoben. Wenn Sie in diesem Artikel keine untergeordnete Ressource sehen, können Sie davon ausgehen, dass sie zusammen mit der übergeordneten Ressource verschoben wird. Falls die übergeordnete Ressource das Verschieben nicht unterstützt, kann die untergeordnete Ressource nicht verschoben werden.

Navigieren Sie direkt zu einem Ressourcenanbieter-Namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTHub](#microsoftiothub)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | domainservices | Nein | Nein |  Nein |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings | Nein | Nein | Nein |
> | diagnosticsettingscategories | Nein | Nein | Nein |
> | privatelinkforazuread | Ja | Ja | Nein |
> | tenants | Ja | Ja | Nein |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | supportproviders | Nein | Nein | Nein |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | Nein | Nein | Nein |
> | addsservices | Nein | Nein | Nein |
> | agents | Nein | Nein | Nein |
> | anonymousapiusers | Nein | Nein | Nein |
> | Konfiguration | Nein | Nein | Nein |
> | logs | Nein | Nein | Nein |
> | reports | Nein | Nein | Nein |
> | servicehealthmetrics | Nein | Nein | Nein |
> | services | Nein | Nein | Nein |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | Konfigurationen | Nein | Nein | Nein |
> | generaterecommendations | Nein | Nein | Nein |
> | metadata | Nein | Nein | Nein |
> | empfehlungen | Nein | Nein | Nein |
> | suppressions | Nein | Nein | Nein |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | actionRules | Ja | Ja | Nein |
> | alerts | Nein | Nein | Nein |
> | alertslist | Nein | Nein | Nein |
> | alertsmetadata | Nein | Nein | Nein |
> | alertssummary | Nein | Nein | Nein |
> | alertssummarylist | Nein | Nein | Nein |
> | smartdetectoralertrules | Ja | Ja | Nein |
> | smartgroups | Nein | Nein | Nein |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | servers | Ja | Ja | Nein |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Ein auf die Verbrauchs-SKU festgelegter API Management-Dienst kann nicht verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | Nein | Nein | Nein |
> | Dienst | Ja | Ja | Ja (Vorlage verwenden) <br/><br/> [Regionsübergreifendes Verschieben von API Management](../../api-management/api-management-howto-migrate.md). |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationstores | Ja | Ja | Nein |
> | configurationstores/eventgridfilters | Nein | Nein | Nein |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | spring | Ja | Ja | Nein |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for App Service resources](./move-limitations/app-service-move-limitations.md) (Anleitung zum Verschieben von App Service-Ressourcen).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | Nein | Nein | Ja (Vorlage verwenden)<br/><br/> [Verschieben einer App Service-Apps in eine andere Region](../../app-service/manage-move-across-regions.md) |
> | appidentities | Nein | Nein | Nein |
> | gateways | Nein | Nein | Nein |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | attestationproviders | Ja | Ja | Nein |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | classicadministrators | Nein | Nein | Nein |
> | dataaliases | Nein | Nein | Nein |
> | denyassignments | Nein | Nein | Nein |
> | elevateaccess | Nein | Nein | Nein |
> | findorphanroleassignments | Nein | Nein | Nein |
> | locks | Nein | Nein | Nein |
> | Berechtigungen | Nein | Nein | Nein |
> | policyassignments | Nein | Nein | Nein |
> | policydefinitions | Nein | Nein | Nein |
> | policysetdefinitions | Nein | Nein | Nein |
> | privatelinkassociations | Nein | Nein | Nein |
> | resourcemanagementprivatelinks | Nein | Nein | Nein |
> | roleassignments | Nein | Nein | Nein |
> | roleassignmentsusagemetrics | Nein | Nein | Nein |
> | roledefinitions | Nein | Nein | Nein |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Runbooks müssen sich in der gleichen Ressourcengruppe befinden wie das Automation-Konto.
>
> Weitere Informationen finden Sie unter [Verschieben Ihres Azure Automation-Kontos in ein anderes Abonnement](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | automationaccounts | Ja | Ja | Ja (Vorlage verwenden) <br/><br/> [Verwenden von Georeplikation](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts/configurations | Ja | Ja | Nein |
> | automationaccounts/runbooks | Ja | Ja | Nein |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | privateclouds | Ja | Ja | Nein |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories | Ja | Ja | Nein |
> | b2ctenants | Nein | Nein | Nein |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | datacontrollers | Nein | Nein | Nein |
> | hybriddatamanagers | Nein | Nein | Nein |
> | postgresinstances | Nein | Nein | Nein |
> | sqlinstances | Nein | Nein | Nein |
> | sqlmanagedinstances | Nein | Nein | Nein |
> | sqlserverinstances | Nein | Nein | Nein |
> | sqlserverregistrations | Ja | Ja | Nein |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | Nein | Nein | Nein |
> | registrations | Ja | Ja | Nein |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Nein | Nein | Nein |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts | Ja | Ja | Batch-Konten können nicht direkt aus einer Region in eine andere verschoben werden, Sie können jedoch eine Vorlage verwenden, um eine Vorlage zu exportieren, sie zu ändern und dann die Vorlage in der neuen Region bereitzustellen. <br/><br/> Informationen zum [Verschieben eines Batch-Kontos zwischen Regionen](../../batch/account-move.md) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | billingaccounts | Nein | Nein | Nein |
> | billingperiods | Nein | Nein | Nein |
> | billingpermissions | Nein | Nein | Nein |
> | billingproperty | Nein | Nein | Nein |
> | billingroleassignments | Nein | Nein | Nein |
> | billingroledefinitions | Nein | Nein | Nein |
> | departments | Nein | Nein | Nein |
> | enrollmentaccounts | Nein | Nein | Nein |
> | invoices | Nein | Nein | Nein |
> | transfers | Nein | Nein | Nein |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | Nein | Nein | Nein |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | Nein | Nein | Nein |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers | Nein | Nein | Nein <br/><br/> Das Blockchain-Netzwerk kann keine Knoten in unterschiedlichen Regionen haben. |
> | cordamembers | Nein | Nein | Nein |
> | watchers | Nein | Nein | Nein |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | tokenservices | Nein | Nein | Nein |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | blueprintassignments | Nein | Nein | Nein |
> | blueprints | Nein | Nein | Nein |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | Ja | Ja | Nein |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Wenn die Azure Cache for Redis-Instanz mit einem virtuellen Netzwerk konfiguriert ist, kann die Instanz nicht in ein anderes Abonnement verschoben werden. Weitere Informationen finden Sie unter [Einschränkungen beim Verschieben von Netzwerkressourcen](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | redis | Ja | Ja | Nein |
> | redisenterprise | Nein | Nein | Nein |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations | Nein | Nein | Nein |
> | calculateexchange | Nein | Nein | Nein |
> | calculateprice | Nein | Nein | Nein |
> | calculatepurchaseprice | Nein | Nein | Nein |
> | catalogs | Nein | Nein | Nein |
> | commercialreservationorders | Nein | Nein | Nein |
> | Börse | Nein | Nein | Nein |
> | reservationorders | Nein | Nein | Nein |
> | reservations | Nein | Nein | Nein |
> | ressourcen | Nein | Nein | Nein |
> | validatereservationorder | Nein | Nein | Nein |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nein | Nein | Nein |
> | cdnwebapplicationfirewallpolicies | Ja | Ja | Nein |
> | edgenodes | Nein | Nein | Nein |
> | profiles | Ja | Ja | Nein |
> | profiles/endpoints | Ja | Ja | Nein |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for App Service resources](./move-limitations/app-service-move-limitations.md) (Anleitung zum Verschieben von App Service-Ressourcen).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | certificateorders | Ja | Ja | Nein |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | capabilities | Nein | Nein | Nein |
> | domainnames | Ja | Nein | Nein |
> | quotas | Nein | Nein | Nein |
> | resourcetypes | Nein | Nein | Nein |
> | validatesubscriptionmoveavailability | Nein | Nein | Nein |
> | virtualmachines | Ja | Ja | Nein |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | Nein | Nein | Nein |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | capabilities | Nein | Nein | Nein |
> | expressroutecrossconnections | Nein | Nein | Nein |
> | expressroutecrossconnections/peerings | Nein | Nein | Nein |
> | gatewaysupporteddevices | Nein | Nein | Nein |
> | networksecuritygroups | Nein | Nein | Nein |
> | quotas | Nein | Nein | Nein |
> | reservedips | Nein | Nein | Nein |
> | virtualnetworks | Nein | Nein | Nein |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | disks | Nein | Nein | Nein |
> | images | Nein | Nein | Nein |
> | osimages | Nein | Nein | Nein |
> | osplatformimages | Nein | Nein | Nein |
> | publicimages | Nein | Nein | Nein |
> | quotas | Nein | Nein | Nein |
> | storageaccounts | Ja | Nein | Ja |
> | vmimages | Nein | Nein | Nein |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for Classic deployment model resources](./move-limitations/classic-model-move-limitations.md) (Anleitung zum Verschieben von Ressourcen des klassischen Bereitstellungsmodells). Klassische Bereitstellungsressourcen können mithilfe eines szenariospezifischen Vorgangs zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | Vorgänge | Nein | Nein | Nein |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nein |
> | Cognitive Search | Ja | Ja | Unterstützt mit manuellen Schritten.<br/><br/> Informationen zum [Verschieben Ihres Azure Cognitive Search-Diensts in eine andere Region](../../search/search-howto-move-across-regions.md) |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard | Nein | Nein | Nein |
> | usageaggregates | Nein | Nein | Nein |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for virtual machines](./move-limitations/virtual-machines-move-limitations.md) (Anleitung zum Verschieben virtueller Computer).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | availabilitysets | Ja | Ja |  Ja <br/><br/> Verschieben Sie mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) Verfügbarkeitsgruppen. |
> | diskaccesses | Nein | Nein | Nein |
> | diskencryptionsets | Nein | Nein | Nein |
> | disks | Ja | Ja | Ja <br/><br/> Verschieben Sie mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) Azure-VMs und zugehörige Datenträger. |
> | galleries | Nein | Nein | Nein |
> | galleries/images | Nein | Nein | Nein |
> | galleries/images/versions | Nein | Nein | Nein |
> | hostgroups | Nein | Nein | Nein |
> | hostgroups/hosts | Nein | Nein | Nein |
> | images | Ja | Ja | Nein |
> | proximityplacementgroups | Ja | Ja | Nein |
> | restorepointcollections | Nein | Nein | Nein |
> | restorepointcollections/restorepoints | Nein | Nein | Nein |
> | sharedvmextensions | Nein | Nein | Nein |
> | sharedvmimages | Nein | Nein | Nein |
> | sharedvmimages/versions | Nein | Nein | Nein |
> | snapshots | Ja | Ja | Nein |
> | sshpublickeys | Nein | Nein | Nein |
> | virtualmachines | Ja | Ja | Ja <br/><br/> Verschieben Sie mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) Azure-VMs. |
> | virtualmachines/extensions | Ja | Ja | Nein |
> | virtualmachinescalesets | Ja | Ja | Nein |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregatedcost | Nein | Nein | Nein |
> | balances | Nein | Nein | Nein |
> | budgets | Nein | Nein | Nein |
> | charges | Nein | Nein | Nein |
> | costtags | Nein | Nein | Nein |
> | credits | Nein | Nein | Nein |
> | events | Nein | Nein | Nein |
> | forecasts | Nein | Nein | Nein |
> | lots | Nein | Nein | Nein |
> | marketplaces | Nein | Nein | Nein |
> | pricesheets | Nein | Nein | Nein |
> | products | Nein | Nein | Nein |
> | reservationdetails | Nein | Nein | Nein |
> | reservationrecommendationdetails | Nein | Nein | Nein |
> | reservationrecommendations | Nein | Nein | Nein |
> | reservationsummaries | Nein | Nein | Nein |
> | reservationtransactions | Nein | Nein | Nein |
> | tags | Nein | Nein | Nein |
> | tenants | Nein | Nein | Nein |
> | terms | Nein | Nein | Nein |
> | usagedetails | Nein | Nein | Nein |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | containergroups | Nein | Nein | Nein |
> | serviceassociationlinks | Nein | Nein | Nein |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | registries | Ja | Ja | Nein |
> | registries/agentpools | Ja | Ja | Nein |
> | registries/buildtasks | Ja | Ja | Nein |
> | registries/replications | Ja | Ja | Nein |
> | registries/tasks | Ja | Ja | Nein |
> | registries/webhooks | Ja | Ja | Nein |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | Nein | Nein | Nein |
> | managedclusters | Nein | Nein | Nein |
> | openshiftmanagedclusters | Nein | Nein | Nein |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | Nein | Nein | Nein |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nein | Nein | Nein |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | alerts | Nein | Nein | Nein |
> | billingaccounts | Nein | Nein | Nein |
> | budgets | Nein | Nein | Nein |
> | cloudconnectors | Nein | Nein | Nein |
> | connectors | Ja | Ja | Nein |
> | departments | Nein | Nein | Nein |
> | dimensions | Nein | Nein | Nein |
> | enrollmentaccounts | Nein | Nein | Nein |
> | exports | Nein | Nein | Nein |
> | externalbillingaccounts | Nein | Nein | Nein |
> | forecast | Nein | Nein | Nein |
> | Abfrage | Nein | Nein | Nein |
> | Registrieren | Nein | Nein | Nein |
> | reportconfigs | Nein | Nein | Nein |
> | reports | Nein | Nein | Nein |
> | settings | Nein | Nein | Nein |
> | showbackrules | Nein | Nein | Nein |
> | views | Nein | Nein | Nein |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | hubs | Nein | Nein | Nein |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | requests | Nein | Nein | Nein |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | associations | Nein | Nein | Nein |
> | resourceproviders | Ja | Ja | Nein |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | jobs | Nein | Nein | Nein |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | Nein | Nein | Nein |
> | databoxedgedevices | Nein | Nein | Nein |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Nein | Nein | Nein |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | catalogs | Ja | Ja | Nein |
> | datacatalogs | Nein | Nein | Nein |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionmanagers | Nein | Nein | Nein |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | packages | Nein | Nein | Nein |
> | plans | Nein | Nein | Nein |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | datafactories | Ja | Ja | Nein |
> | factories | Ja | Ja | Nein |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | Nein | Nein | Nein |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nein |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nein |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Nein | Nein | Nein |
> | services/projects | Nein | Nein | Nein |
> | slots | Nein | Nein | Nein |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults | [Ja](../../backup/backup-vault-overview.md#use-azure-portal-to-move-backup-vault-to-a-different-resource-group) | [Ja](../../backup/backup-vault-overview.md#use-azure-portal-to-move-backup-vault-to-a-different-subscription) | Nein |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nein |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | servers | Ja | Ja | Zum Verschieben eines vorhandenen Servers können Sie ein regionsübergreifendes Lesereplikat verwenden. [Weitere Informationen](../../postgresql/howto-move-regions-portal.md)<br/><br/> Wenn der Dienst mit georedundantem Sicherungsspeicher bereitgestellt wird, können Sie die Geowiederherstellung für die Wiederherstellung in anderen Regionen verwenden. [Weitere Informationen](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Nein | Nein | Nein |
> | servers | Ja | Ja | Zum Verschieben eines vorhandenen Servers können Sie ein regionsübergreifendes Lesereplikat verwenden. [Weitere Informationen](../../mysql/howto-move-regions-portal.md)

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Nein | Nein | Nein |
> | servergroups | Nein | Nein | Nein |
> | servers | Ja | Ja | Zum Verschieben eines vorhandenen Servers können Sie ein regionsübergreifendes Lesereplikat verwenden. [Weitere Informationen](../../postgresql/howto-move-regions-portal.md)
> | serversv2 | Ja | Ja | Nein |
> | singleservers | Ja | Ja | Nein |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | Ja | Ja | Nein |
> | rollouts | Ja | Ja | Nein |
> | servicetopologies | Ja | Ja | Nein |
> | servicetopologies/services | Ja | Ja | Nein |
> | servicetopologies/services/serviceunits | Ja | Ja | Nein |
> | steps | Ja | Ja | Nein |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgroups | Ja | Ja | Nein |
> | hostpools | Ja | Ja | Nein |
> | workspaces | Ja | Ja | Nein |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | elasticpools | Nein | Nein | Nein. Die Ressource wird nicht verfügbar gemacht. |
> | elasticpools/iothubtenants | Nein | Nein | Nein. Die Ressource wird nicht verfügbar gemacht. |
> | iothubs | Ja | Ja | Ja. [Weitere Informationen](../../iot-hub/iot-hub-how-to-clone.md) |
> | provisioningservices | Ja | Ja | Nein |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | pipelines | Ja | Ja | Nein |
> | Controller | **Ausstehend** | **Ausstehend** | Nein |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | Controller | Ja | Ja | Nein |
> | AKS-Cluster | **Ausstehend** | **Ausstehend** | Nein<br/><br/> [Weitere Informationen](/previous-versions/azure/dev-spaces/) zum Verschieben in eine andere Region.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | labcenters | Nein | Nein | Nein |
> | labs | Ja | Nein | Nein |
> | labs/environments | Ja | Ja | Nein |
> | labs/servicerunners | Ja | Ja | Nein |
> | labs/virtualmachines | Ja | Nein | Nein |
> | schedules | Ja | Ja | Nein |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | Nein | Nein | Ja, durch Neuerstellen von Ressourcen in einer neuen Region. [Weitere Informationen](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames | Nein | Nein | Nein |
> | databaseaccounts | Ja | Ja | Nein |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | domains | Ja | Ja | Nein |
> | generatessorequest | Nein | Nein | Nein |
> | topleveldomains | Nein | Nein | Nein |
> | validatedomainregistrationinformation | Nein | Nein | Nein |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Ja | Ja | Nein |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | domains | Ja | Ja | Nein |
> | eventsubscriptions | Nein, kann nicht unabhängig verschoben, aber automatisch mit abonnierter Ressource verschoben werden. | Nein, kann nicht unabhängig verschoben, aber automatisch mit abonnierter Ressource verschoben werden. | Nein |
> | extensiontopics | Nein | Nein | Nein |
> | partnernamespaces | Ja | Ja | Nein |
> | partnerregistrations | Nein | Nein | Nein |
> | partnertopics | Ja | Ja | Nein |
> | systemtopics | Ja | Ja | Nein |
> | topics | Ja | Ja | Nein |
> | topictypes | Nein | Nein | Nein |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Ja | Ja | Nein |
> | Namespaces | Ja | Ja | Ja (mit Vorlage)<br/><br/> [Verschieben eines Event Hub-Namespace in eine andere Region](../../event-hubs/move-across-regions.md) |
> | sku | Nein | Nein | Nein |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | Nein | Nein | Nein |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | Namespaces | Ja | Ja | Nein |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | featureproviders | Nein | Nein | Nein |
> | Features | Nein | Nein | Nein |
> | providers | Nein | Nein | Nein |
> | subscriptionfeatureregistrations | Nein | Nein | Nein |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nein | Nein | Nein |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | automanagedaccounts | Nein | Nein | Nein |
> | automanagedvmconfigurationprofiles | Nein | Nein | Nein |
> | guestconfigurationassignments | Nein | Nein | Nein |
> | software | Nein | Nein | Nein |
> | softwareupdateprofile | Nein | Nein | Nein |
> | softwareupdates | Nein | Nein | Nein |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | hanainstances | Nein | Nein | Nein |
> | sapmonitors | Nein | Nein | Nein |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedhsms | Nein | Nein | Nein |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> Sie können HDInsight-Cluster in ein neues Abonnement oder eine neue Ressourcengruppe verschieben. Dagegen können die mit dem HDInsight-Cluster verknüpften Netzwerkressourcen (z.B. virtuelles Netzwerk, NIC oder Load Balancer) nicht zwischen Abonnements verschoben werden. Darüber hinaus kann eine NIC, die an einen virtuellen Computer für den Cluster angefügt ist, nicht in eine neue Ressourcengruppe verschoben werden.
>
> Beim Verschieben eines HDInsight-Clusters in ein neues Abonnement sollten Sie zunächst andere Ressourcen (z.B. das Speicherkonto) verschieben. Verschieben Sie erst anschließend den HDInsight-Cluster.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Ja | Ja | Nein |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Ja | Ja | Nein |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | machines | Ja | Ja | Nein |
> | machines/extensions | Ja | Ja | Nein |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | datamanagers | Ja | Ja | Nein |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | Nein | Nein | Nein |
> | vnfs | Nein | Nein | Nein |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | components | Nein | Nein | Nein |
> | networkscopes | Nein | Nein | Nein |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | jobs | Ja | Ja | Nein |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> Berücksichtigen Sie beim Verschieben in ein neues Abonnement die [Abonnementkontingente](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nein. [Weitere Informationen](../../azure-monitor/faq.yml#how-do-i-move-an-application-insights-resource-to-a-new-region-) |
> | actiongroups | Ja | Ja | Nein |
> | activitylogalerts | Nein | Nein | Nein |
> | alertrules | Ja | Ja | Nein |
> | autoscalesettings | Ja | Ja | Nein |
> | baseline | Nein | Nein | Nein |
> | components | Ja | Ja | Nein |
> | datacollectionrules | Nein | Nein | Nein |
> | diagnosticsettings | Nein | Nein | Nein |
> | diagnosticsettingscategories | Nein | Nein | Nein |
> | eventcategories | Nein | Nein | Nein |
> | eventtypes | Nein | Nein | Nein |
> | extendeddiagnosticsettings | Nein | Nein | Nein |
> | guestdiagnosticsettings | Nein | Nein | Nein |
> | listmigrationdate | Nein | Nein | Nein |
> | logdefinitions | Nein | Nein | Nein |
> | logprofiles | Nein | Nein | Nein |
> | logs | Nein | Nein | Nein |
> | metricalerts | Nein | Nein | Nein |
> | metricbaselines | Nein | Nein | Nein |
> | metricbatch | Nein | Nein | Nein |
> | metricdefinitions | Nein | Nein | Nein |
> | metricnamespaces | Nein | Nein | Nein |
> | metrics | Nein | Nein | Nein |
> | migratealertrules | Nein | Nein | Nein |
> | migratetonewpricingmodel | Nein | Nein | Nein |
> | myworkbooks | Nein | Nein | Nein |
> | notificationgroups | Nein | Nein | Nein |
> | privatelinkscopes | Nein | Nein | Nein |
> | rollbacktolegacypricingmodel | Nein | Nein | Nein |
> | scheduledqueryrules | Ja | Ja | Nein |
> | Topologie | Nein | Nein | Nein |
> | Transaktionen | Nein | Nein | Nein |
> | vminsightsonboardingstatuses | Nein | Nein | Nein |
> | webtests | Ja | Ja | Nein |
> | webtests/gettestresultfile | Nein | Nein | Nein |
> | workbooks | Ja | Ja | Nein |
> | workbooktemplates | Ja | Ja | Nein |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates | Nein | Nein | Nein |
> | iotapps | Ja | Ja | Nein |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | iothub | Ja | Ja | Ja (Hub klonen) <br/><br/> [Klonen eines IoT-Hubs in eine andere Region](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | graph | Ja | Ja | Nein |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Für die Datenträgerverschlüsselung verwendete Schlüsseltresore können nicht in einer Ressourcengruppe im gleichen Abonnement oder zwischen Abonnements verschoben werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | deletedvaults | Nein | Nein | Nein |
> | hsmpools | Nein | Nein | Nein |
> | managedhsms | Nein | Nein | Nein |
> | vaults | Ja | Ja | Nein |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | connectedclusters | Nein | Nein | Nein |
> | registeredsubscriptions | Nein | Nein | Nein |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations | Nein | Nein | Nein |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Ja | Ja | Nein |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | labaccounts | Nein | Nein | Nein |
> | users | Nein | Nein | Nein |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nein | Nein | Nein |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nein | Nein | Nein, dies ist ein globaler Dienst. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | hostingenvironments | Nein | Nein | Nein |
> | integrationaccounts | Ja | Ja | Nein |
> | integrationserviceenvironments | Ja | Nein | Nein |
> | integrationserviceenvironments/managedapis | Ja | Nein | Nein |
> | isolatedenvironments | Nein | Nein | Nein |
> | workflows | Ja | Ja | Nein |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | commitmentplans | Nein | Nein | Nein |
> | webservices | Ja | Nein | Nein |
> | workspaces | Ja | Ja | Nein |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationclusters | Nein | Nein | Nein |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nein | Nein | Nein |
> | teamaccounts | Nein | Nein | Nein |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nein | Nein | Nein |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Nein | Nein | Nein |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationassignments | Nein | Nein | Ja. [Weitere Informationen](../../virtual-machines/move-region-maintenance-configuration.md) |
> | maintenanceconfigurations | Ja | Ja | Ja. [Weitere Informationen](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | Nein | Nein | Nein |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | Identitäten | Nein | Nein | Nein |
> | userassignedidentities | Nein | Nein | Nein |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | Nein | Nein | Nein |
> | managednetworks/managednetworkgroups | Nein | Nein | Nein |
> | managednetworks/managednetworkpeeringpolicies | Nein | Nein | Nein |
> | Benachrichtigung | Nein | Nein | Nein |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | Nein | Nein | Nein |
> | registrationassignments | Nein | Nein | Nein |
> | registrationdefinitions | Nein | Nein | Nein |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | Nein | Nein | Nein |
> | managementgroups | Nein | Nein | Nein |
> | managementgroups/settings | Nein | Nein | Nein |
> | ressourcen | Nein | Nein | Nein |
> | starttenantbackfill | Nein | Nein | Nein |
> | tenantbackfillstatus | Nein | Nein | Nein |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Ja | Ja | Nein, Azure Maps ist ein räumlicher Dienst. |
> | accounts/privateatlases | Ja | Ja | Nein |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | offers | Nein | Nein | Nein |
> | offertypes | Nein | Nein | Nein |
> | privategalleryitems | Nein | Nein | Nein |
> | privatestoreclient | Nein | Nein | Nein |
> | privatestores | Nein | Nein | Nein |
> | products | Nein | Nein | Nein |
> | publishers | Nein | Nein | Nein |
> | Registrieren | Nein | Nein | Nein |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | Nein | Nein | Nein |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | agreements | Nein | Nein | Nein |
> | offertypes | Nein | Nein | Nein |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | Ja | Ja | Nein |
> | mediaservices/liveevents | Ja | Ja | Nein |
> | mediaservices/streamingendpoints | Ja | Ja | Nein |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | appclusters | Nein | Nein | Nein |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects | Nein | Nein | Nein |
> | migrateprojects | Nein | Nein | Nein |
> | movecollections | Nein | Nein | Nein |
> | projects | Nein | Nein | Nein |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | Nein | Nein | Nein |
> | objectunderstandingaccounts | Nein | Nein | Nein |
> | remoterenderingaccounts | Ja | Ja | Nein |
> | spatialanchorsaccounts | Ja | Ja | Nein |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | Nein | Nein | Nein |
> | netappaccounts/capacitypools | Nein | Nein | Nein |
> | netappaccounts/capacitypools/volumes | Nein | Nein | Nein |
> | netappaccounts/capacitypools/volumes/mounttargets | Nein | Nein | Nein |
> | netappaccounts/capacitypools/volumes/snapshots | Nein | Nein | Nein |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for networking resources](./move-limitations/networking-move-limitations.md) (Anleitung zum Verschieben von Netzwerkressourcen).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateways | Nein | Nein | Nein |
> | applicationgatewaywebapplicationfirewallpolicies | Nein | Nein | Nein |
> | applicationsecuritygroups | Ja | Ja | Nein |
> | azurefirewalls | Nein | Nein | Nein |
> | bastionhosts | Nein | Nein | Nein |
> | bgpservicecommunities | Nein | Nein | Nein |
> | connections | Ja | Ja | Nein |
> | ddoscustompolicies | Ja | Ja | Nein |
> | ddosprotectionplans | Nein | Nein | Nein |
> | dnszones | Ja | Ja | Nein |
> | expressroutecircuits | Nein | Nein | Nein |
> | expressroutegateways | Nein | Nein | Nein |
> | expressrouteserviceproviders | Nein | Nein | Nein |
> | firewallpolicies | Ja | Ja | Nein |
> | frontdoors | Nein | Nein | Nein |
> | ipallocations | Ja | Ja | Nein |
> | ipgroups | Ja | Ja | Nein |
> | loadbalancers | Ja: Basic-SKU<br> Ja: Standard-SKU | Ja: Basic-SKU<br>Nein: Standard-SKU | Ja <br/><br/> Verschieben Sie in- und externe Lastenausgleichsmodule mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md). |
> | localnetworkgateways | Ja | Ja | Nein |
> | natgateways | Nein | Nein | Nein |
> | networkexperimentprofiles | Nein | Nein | Nein |
> | networkintentpolicies | Ja | Ja | Nein |
> | networkinterfaces | Ja | Ja | Ja <br/><br/> Verschieben Sie NICs mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md). |
> | networkprofiles | Nein | Nein | Nein |
> | networksecuritygroups | Ja | Ja | Ja <br/><br/> Verschieben Sie Netzwerksicherheitsgruppen (NSGs) mit [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md). |
> | networkwatchers | Nein | Nein | Nein |
> | networkwatchers/connectionmonitors | Ja | Nein | Nein |
> | networkwatchers/flowlogs | Ja | Nein | Nein |
> | networkwatchers/pingmeshes | Ja | Nein | Nein |
> | p2svpngateways | Nein | Nein | Nein |
> | privatednszones | Ja | Ja | Nein |
> | privatednszones/virtualnetworklinks | Ja | Ja | Nein |
> | privatednszonesinternal | Nein | Nein | Nein |
> | privateendpointredirectmaps | Nein | Nein | Nein |
> | privateendpoints | Nein | Nein | Nein |
> | privatelinkservices | Nein | Nein | Nein |
> | publicipaddresses | Ja: Basic-SKU<br>Ja: Standard-SKU | Ja: Basic-SKU<br>Nein: Standard-SKU | Ja<br/><br/> Verwenden Sie den [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) zum Verschieben der Konfigurationen öffentlicher IP-Adressen (IP-Adressen werden nicht beibehalten). |
> | publicipprefixes | Ja | Ja | Nein |
> | routefilters | Nein | Nein | Nein |
> | routetables | Ja | Ja | Nein |
> | securitypartnerproviders | Ja | Ja | Nein |
> | serviceendpointpolicies | Ja | Ja | Nein |
> | trafficmanagergeographichierarchies | Nein | Nein | Nein |
> | trafficmanagerprofiles | Ja | Ja | Nein |
> | trafficmanagerprofiles/heatmaps | Nein | Nein | Nein |
> | trafficmanagerusermetricskeys | Nein | Nein | Nein |
> | virtualhubs | Nein | Nein | Nein |
> | virtualnetworkgateways | Ja | Ja | Nein |
> | virtualnetworks | Ja | Ja | Nein |
> | virtualnetworktaps | Nein | Nein | Nein |
> | virtualrouters | Ja | Ja | Nein |
> | virtualwans | Nein | Nein |
> | vpngateways (Virtual WAN) | Nein | Nein | Nein |
> | vpnserverconfigurations | Nein | Nein | Nein |
> | vpnsites (Virtual WAN) | Nein | Nein | Nein |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | Namespaces | Ja | Ja | Nein |
> | namespaces/notificationhubs | Ja | Ja | Nein |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | Ja | Ja | Nein |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | hypervsites | Nein | Nein | Nein |
> | importsites | Nein | Nein | Nein |
> | serversites | Nein | Nein | Nein |
> | vmwaresites | Nein | Nein | Nein |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Berücksichtigen Sie beim Verschieben in ein neues Abonnement die [Abonnementkontingente](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Arbeitsbereiche, die ein verknüpftes Automation-Konto besitzen, können nicht verschoben werden. Bevor Sie mit einem Verschiebevorgang beginnen, stellen Sie sicher, dass Sie die Verknüpfung jeglicher Automation-Kontos aufheben.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Nein | Nein | Nein |
> | deletedworkspaces | Nein | Nein | Nein |
> | linktargets | Nein | Nein | Nein |
> | storageinsightconfigs | Nein | Nein | Nein |
> | workspaces | Ja | Ja | Nein |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | managementassociations | Nein | Nein | Nein |
> | managementconfigurations | Ja | Ja | Nein |
> | solutions | Ja | Ja | Nein |
> | views | Ja | Ja | Nein |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | legacypeerings | Nein | Nein | Nein |
> | peerasns | Nein | Nein | Nein |
> | peeringlocations | Nein | Nein | Nein |
> | peerings | Nein | Nein | Nein |
> | peeringservicecountries | Nein | Nein | Nein |
> | peeringservicelocations | Nein | Nein | Nein |
> | peeringserviceproviders | Nein | Nein | Nein |
> | peeringservices | Nein | Nein | Nein |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | policyevents | Nein | Nein | Nein |
> | policystates | Nein | Nein | Nein |
> | policytrackedresources | Nein | Nein | Nein |
> | remediations | Nein | Nein | Nein |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | consoles | Nein | Nein | Nein |
> | dashboards | Ja | Ja | Nein |
> | usersettings | Nein | Nein | Nein |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | workspacecollections | Ja | Ja | Nein |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | capacities | Ja | Ja | Nein |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | Nein | Nein | Nein |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | Ja | Ja | Nein |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | availableaccounts | Nein | Nein | Nein |
> | providerregistrations | Nein | Nein | Nein |
> | rollouts | Nein | Nein | Nein |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Nein | Nein | Nein |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

>[!IMPORTANT]
>- Weitere Informationen finden Sie unter [Verschieben eines Recovery Services-Tresors zwischen Azure-Abonnements und Ressourcengruppen](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
>- Siehe [Fortsetzen des Sicherns im Recovery Services-Tresor nach dem Verschieben zwischen Regionen](../../backup/azure-backup-move-vaults-across-regions.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | replicationeligibilityresults | Nein | Nein | Nein |
> | vaults | Ja | Ja | Nein.<br/><br/> Das Verschieben von Recovery Services-Tresoren für Azure Backup zwischen Azure-Regionen wird nicht unterstützt.<br/><br/> In Recovery Services-Tresoren für Azure Site Recovery können Sie [den Tresor deaktivieren und in der Zielregion neu erstellen](../../site-recovery/move-vaults-across-regions.md). |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | Nein | Nein | Nein |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | Namespaces | Ja | Ja | Nein |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | Abfragen | Ja | Ja | Nein |
> | resourcechangedetails | Nein | Nein | Nein |
> | resourcechanges | Nein | Nein | Nein |
> | ressourcen | Nein | Nein | Nein |
> | resourceshistory | Nein | Nein | Nein |
> | subscriptionsstatus | Nein | Nein | Nein |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | Nein | Nein | Nein |
> | emergingissues | Nein | Nein | Nein |
> | events | Nein | Nein | Nein |
> | metadata | Nein | Nein | Nein |
> | Benachrichtigungen | Nein | Nein | Nein |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | deployments | Nein | Nein | Nein |
> | deploymentscripts | Nein | Nein | Ja<br/><br/>[Verschieben von Ressourcen unter Microsoft.Resources in eine neue Region](microsoft-resources-move-regions.md) |
> | deploymentscripts/logs | Nein | Nein | Nein |
> | Verknüpfungen | Nein | Nein | Nein |
> | providers | Nein | Nein | Nein |
> | resourcegroups | Nein | Nein | Nein |
> | ressourcen | Nein | Nein | Nein |
> | subscriptions | Nein | Nein | Nein |
> | tags | Nein | Nein | Nein |
> | templatespecs | Nein | Nein | Ja<br/><br/>[Verschieben von Ressourcen unter Microsoft.Resources in eine neue Region](microsoft-resources-move-regions.md) |
> | templatespecs/versions | Nein | Nein | Nein |
> | tenants | Nein | Nein | Nein |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | Ja | Nein | Nein |
> | saasresources | Nein | Nein | Nein |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> Es ist nicht möglich, mehrere Search-Ressourcen in verschiedenen Regionen gleichzeitig zu verschieben. Verschieben Sie diese stattdessen in mehreren Vorgängen.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | Nein | Nein | Nein |
> | searchservices | Ja | Ja | Nein |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | Nein | Nein | Nein |
> | advancedthreatprotectionsettings | Nein | Nein | Nein |
> | alerts | Nein | Nein | Nein |
> | allowedconnections | Nein | Nein | Nein |
> | applicationwhitelistings | Nein | Nein | Nein |
> | assessmentmetadata | Nein | Nein | Nein |
> | assessments | Nein | Nein | Nein |
> | autodismissalertsrules | Nein | Nein | Nein |
> | automations | Ja | Ja | Nein |
> | autoprovisioningsettings | Nein | Nein | Nein |
> | complianceresults | Nein | Nein | Nein |
> | compliances | Nein | Nein | Nein |
> | datacollectionagents | Nein | Nein | Nein |
> | devicesecuritygroups | Nein | Nein | Nein |
> | discoveredsecuritysolutions | Nein | Nein | Nein |
> | externalsecuritysolutions | Nein | Nein | Nein |
> | informationprotectionpolicies | Nein | Nein | Nein |
> | iotsecuritysolutions | Ja | Ja | Nein |
> | iotsecuritysolutions/analyticsmodels | Nein | Nein | Nein |
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | Nein | Nein | Nein |
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | Nein | Nein | Nein |
> | jitnetworkaccesspolicies | Nein | Nein | Nein |
> | Richtlinien | Nein | Nein | Nein |
> | pricings | Nein | Nein | Nein |
> | regulatorycompliancestandards | Nein | Nein | Nein |
> | regulatorycompliancestandards/regulatorycompliancecontrols | Nein | Nein | Nein |
> | regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments | Nein | Nein | Nein |
> | securitycontacts | Nein | Nein | Nein |
> | securitysolutions | Nein | Nein | Nein |
> | securitysolutionsreferencedata | Nein | Nein | Nein |
> | securitystatuses | Nein | Nein | Nein |
> | securitystatusessummaries | Nein | Nein | Nein |
> | servervulnerabilityassessments | Nein | Nein | Nein |
> | settings | Nein | Nein | Nein |
> | subassessments | Nein | Nein | Nein |
> | Tasks | Nein | Nein | Nein |
> | topologies | Nein | Nein | Nein |
> | workspacesettings | Nein | Nein | Nein |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregations | Nein | Nein | Nein |
> | alertrules | Nein | Nein | Nein |
> | alertruletemplates | Nein | Nein | Nein |
> | automationrules | Nein | Nein | Nein |
> | bookmarks | Nein | Nein | Nein |
> | cases | Nein | Nein | Nein |
> | dataconnectors | Nein | Nein | Nein |
> | entities | Nein | Nein | Nein |
> | entityqueries | Nein | Nein | Nein |
> | incidents | Nein | Nein | Nein |
> | officeconsents | Nein | Nein | Nein |
> | settings | Nein | Nein | Nein |
> | threatintelligence | Nein | Nein | Nein |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | consoleservices | Nein | Nein | Nein |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | gateways | Nein | Nein | Nein |
> | nodes | Nein | Nein | Nein |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | Namespaces | Ja | Ja | Nein |
> | premiummessagingregions | Nein | Nein | Nein |
> | sku | Nein | Nein | Nein |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | Nein | Nein | Nein |
> | clusters | Ja | Ja | Nein |
> | containergroups | Nein | Nein | Nein |
> | containergroupsets | Nein | Nein | Nein |
> | edgeclusters | Nein | Nein | Nein |
> | managedclusters | Nein | Nein | Nein |
> | networks | Nein | Nein | Nein |
> | secretstores | Nein | Nein | Nein |
> | volumes | Nein | Nein | Nein |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | Ja | Ja | Nein |
> | containergroups | Nein | Nein | Nein |
> | gateways | Ja | Ja | Nein |
> | networks | Ja | Ja | Nein |
> | secrets | Ja | Ja | Nein |
> | volumes | Ja | Ja | Nein |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | rollouts | Nein | Nein | Nein |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | signalr | Ja | Ja | Nein |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | hybridusebenefits | Nein | Nein | Nein |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions | Nein | Nein | Nein |
> | applications | Nein | Nein | Nein |
> | jitrequests | Nein | Nein | Nein |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Datenbank und Server müssen sich in der gleichen Ressourcengruppe befinden. Wenn Sie eine SQL Server-Instanz verschieben, werden auch alle ihre Datenbanken verschoben. Dieses Verhalten gilt für Azure SQL-Datenbank- und Azure Synapse Analytics-Datenbanken.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | instancepools | Nein | Nein | Nein |
> | locations | Ja | Ja | Nein |
> | managedinstances | Nein | Nein | Ja <br/><br/> [Erfahren Sie mehr](../../azure-sql/database/move-resources-across-regions.md) über das regionsübergreifende Verschieben verwalteter Instanzen. |
> | managedinstances/databases | Nein | Nein | Ja |
> | servers | Ja | Ja |Ja |
> | servers/databases | Ja | Ja | Ja <br/><br/> [Erfahren Sie mehr](../../azure-sql/database/move-resources-across-regions.md) über das regionsübergreifende Verschieben von Datenbanken.<br/><br/> [Erfahren Sie mehr](../../resource-mover/tutorial-move-region-sql.md) über das Verschieben von Azure SQL-Datenbank-Instanzen mit Azure Resource Mover.  |
> | servers/databases/backuplongtermretentionpolicies | Ja | Ja | Nein |
> | servers/elasticpools | Ja | Ja | Ja <br/><br/> [Erfahren Sie mehr](../../azure-sql/database/move-resources-across-regions.md) über das regionsübergreifende Verschieben von Pools für elastische Datenbanken.<br/><br/> [Erfahren Sie mehr](../../resource-mover/tutorial-move-region-sql.md) über das Verschieben von Pools für elastische Azure SQL-Datenbank-Instanzen mit Azure Resource Mover.  |
> | servers/jobaccounts | Ja | Ja | Nein |
> | servers/jobagents | Ja | Ja | Nein |
> | virtualclusters | Ja | Ja | Ja |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | Ja | Ja | Nein |
> | sqlvirtualmachines | Ja | Ja | Nein |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | storageaccounts | Ja | Ja | Ja<br/><br/> [Verschieben eines Azure Storage-Kontos in eine andere Region](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | caches | Nein | Nein | Nein |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Ja | Ja | Nein |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Nein | Nein | Nein |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Nein | Nein | Nein |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | managers | Nein | Nein | Nein |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Stream Analytics-Aufträge können nicht verschoben werden, während sie ausgeführt werden.

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Nein | Nein | Nein |
> | streamingjobs | Ja | Ja | Nein |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | environments | Nein | Nein | Nein |
> | instances | Nein | Nein | Nein |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | subscriptions | Nein | Nein | Nein |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Nein | Nein | Nein |
> | supporttickets | Nein | Nein | Nein |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Nein | Nein | Nein |
> | workspaces/bigdatapools | Nein | Nein | Nein |
> | workspaces/sqlpools | Nein | Nein | Nein |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | environments | Ja | Ja | Nein |
> | environments/eventsources | Ja | Ja | Nein |
> | environments/referencedatasets | Ja | Ja | Nein |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | stores | Ja | Ja | Nein |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | imagetemplates | Nein | Nein | Nein |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!IMPORTANT]
> Informationen zum Ändern des Abonnements für Azure DevOps finden Sie unter [Change or remove the Azure subscription that your organization uses for billing](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json) (Ändern oder Entfernen des Azure-Abonnements, das von Ihrer Organisation für die Abrechnung verwendet wird).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Nein | Nein | Nein |
> | account/extension | Nein | Nein | Nein |
> | account/project | Nein | Nein | Nein |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | Nein | Nein | Nein |
> | resourcepools | Nein | Nein | Nein |
> | vcenters | Nein | Nein | Nein |
> | virtualmachines | Nein | Nein | Nein |
> | virtualmachinetemplates | Nein | Nein | Nein |
> | virtualnetworks | Nein | Nein | Nein |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedcloudnodes | Nein | Nein | Nein |
> | dedicatedcloudservices | Nein | Nein | Nein |
> | virtualmachines | Nein | Nein | Nein |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | devices | Nein | Nein | Nein |
> | vnfs | Nein | Nein | Nein |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Nein | Nein | Nein |
> | plans | Nein | Nein | Nein |
> | registeredsubscriptions | Nein | Nein | Nein |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Weitere Informationen finden Sie unter [Move guidance for App Service resources](./move-limitations/app-service-move-limitations.md) (Anleitung zum Verschieben von App Service-Ressourcen).

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | availablestacks | Nein | Nein | Nein |
> | billingmeters | Nein | Nein | Nein |
> | certificates | Nein | Ja | Nein |
> | Zertifikate (verwaltet) | Nein | Nein | Nein |
> | connectiongateways | Ja | Ja | Nein |
> | connections | Ja | Ja | Nein |
> | customapis | Ja | Ja | Nein |
> | deletedsites | Nein | Nein | Nein |
> | deploymentlocations | Nein | Nein | Nein |
> | georegions | Nein | Nein | Nein |
> | hostingenvironments | Nein | Nein | Nein |
> | kubeenvironments | Ja | Ja | Nein |
> | publishingusers | Nein | Nein | Nein |
> | empfehlungen | Nein | Nein | Nein |
> | resourcehealthmetadata | Nein | Nein | Nein |
> | runtimes | Nein | Nein | Nein |
> | serverfarms | Ja | Ja | Nein |
> | serverfarms/eventgridfilters | Nein | Nein | Nein |
> | sites | Ja | Ja | Nein |
> | sites/premieraddons | Ja | Ja | Nein |
> | sites/slots | Ja | Ja | Nein |
> | sourcecontrols | Nein | Nein | Nein |
> | staticsites | Nein | Nein | Nein |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | Nein | Nein | Nein |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | deviceservices | Nein | Nein | Nein |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | workloads | Nein | Nein | Nein |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Ressourcentyp | Resource group | Subscription | Regionenverschiebung |
> | ------------- | ----------- | ---------- | ----------- |
> | components | Nein | Nein | Nein |
> | componentssummary | Nein | Nein | Nein |
> | monitorinstances | Nein | Nein | Nein |
> | monitorinstancessummary | Nein | Nein | Nein |
> | monitors | Nein | Nein | Nein |

## <a name="third-party-services"></a>Dienste von Drittanbietern

Derzeit wird der Verschiebevorgang nicht für Drittanbieterdienste unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Befehle zum Verschieben von Ressourcen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](move-resource-group-and-subscription.md).
- Weitere Informationen zum Resource Mover-Dienst finden Sie [hier](../../resource-mover/overview.md).
- Um die Daten als Datei mit durch Trennzeichen getrennten Werten abzurufen, laden Sie zur Unterstützung des Verschiebens von Ressourcengruppen und Abonnements [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) herunter. Wenn Sie diese Eigenschaften und das Verschieben der Region unterstützen möchten, laden Sie [move-support-resources-with-regions.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources-with-regions.csv) herunter.
