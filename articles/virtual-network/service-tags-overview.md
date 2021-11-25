---
title: Übersicht über Azure-Diensttags
titlesuffix: Azure Virtual Network
description: Hier erhalten Sie Informationen zu Diensttags. Diensttags reduzieren die Komplexität bei der Erstellung von Sicherheitsregeln.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2021
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 2affaf7b66c1a7872e1db2b92871c5d07e77cdd2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324629"
---
# <a name="virtual-network-service-tags"></a>Diensttags in virtuellen Netzwerken
<a name="network-service-tags"></a>

Ein Diensttag steht für eine Gruppe von IP-Adresspräfixen eines bestimmten Azure-Diensts. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Tag automatisch, wenn sich die Adressen ändern. Auf diese Weise wird die Komplexität häufiger Updates an Netzwerksicherheitsregeln minimiert.

Sie können Diensttags verwenden, um Netzwerkzugriffssteuerungen in [Netzwerksicherheitsgruppen](./network-security-groups-overview.md#security-rules) oder in der [Azure Firewall](../firewall/service-tags.md) zu definieren. Verwenden Sie Diensttags anstelle von spezifischen IP-Adressen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. **ApiManagement**) im entsprechenden Feld *Quelle* oder *Ziel* einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. 

> [!NOTE] 
> Ab Mai 2021 können Sie auch Diensttags anstelle von expliziten IP-Adressbereichen in [benutzerdefinierten Routen](./virtual-networks-udr-overview.md)verwenden. Dieses Feature befindet sich derzeit in der öffentlichen Vorschauphase. 

Mithilfe von Diensttags können Sie Netzwerkisolation erreichen und Ihre Azure-Ressourcen vor dem allgemeinen Internet schützen, während Sie auf Azure-Dienste mit öffentlichen Endpunkten zugreifen. Erstellen Sie Regeln für eingehende/ausgehende Netzwerksicherheitsgruppen, um Datenverkehr in das **Internet** und aus diesem zu verweigern und Datenverkehr in die **Azure-Cloud** oder andere [verfügbare Diensttags](#available-service-tags) von bestimmten Azure-Diensten und aus diesen zuzulassen.

![Netzwerkisolation von Azure-Diensten mithilfe von Diensttags](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Verfügbare Diensttags
Die folgende Tabelle listet alle Diensttags auf, die zur Verwendung in Regeln für [Netzwerksicherheitsgruppen](./network-security-groups-overview.md#security-rules) verfügbar sind.

In den Spalten wird angegeben, ob das jeweilige Tag:

- für Regeln geeignet ist, die eingehenden oder ausgehenden Datenverkehr abdecken.
- einen [regionalen](https://azure.microsoft.com/regions) Bereich unterstützt.
- in [Azure Firewall](../firewall/service-tags.md)-Regeln verwendet werden kann.

Standardmäßig spiegeln Diensttags die Bereiche für die gesamte Cloud wider. Einige Diensttags ermöglichen eine präzisere Steuerung, indem sie die entsprechenden IP-Adressbereiche auf eine bestimmte Region beschränken. Das Diensttag **Storage** beispielsweise repräsentiert Azure Storage für die gesamte Cloud, **Storage.WestUS** schränkt den Bereich jedoch auf die IP-Adressbereiche der Region „USA, Westen“ ein. Die folgende Tabelle gibt an, ob die einzelnen Diensttags einen solchen regionalen Bereich unterstützen. Beachten Sie, dass die für jedes Tag aufgeführte Anweisung eine Empfehlung darstellt. Beispielsweise kann das AzureCloud-Tag verwendet werden, um eingehenden Datenverkehr zuzulassen. Dies wird für die meisten Szenarien jedoch nicht empfohlen, da es bedeutet, dass Datenverkehr von allen Azure-IP-Adressen, einschließlich der von anderen Azure-Kunden verwendeten, zugelassen wird. 

| Tag | Zweck | Eingehend oder ausgehend möglich? | Regional möglich? | Einsatz mit Azure Firewall möglich? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Aktionsgruppe | Eingehend | Nein | Nein |
| **ApiManagement** | Verwaltungsdatenverkehr für dedizierte Azure API Management-Bereitstellungen. <br/><br/>**Hinweis**: Dieses Tag stellt den Azure API Management-Dienstendpunkt für die Steuerungsebene pro Region dar. Dadurch können Kunden Verwaltungsvorgänge für die APIs, Vorgänge, Richtlinien und NamedValues ausführen, die für den API Management Dienst konfiguriert sind.  | Eingehend | Ja | Ja |
| **ApplicationInsightsAvailability** | Verfügbarkeit von Application Insights | Eingehend | Nein | Nein |
| **AppConfiguration** | App-Konfiguration | Ausgehend | Nein | Nein |
| **AppService**    | Azure App Service Dieses Tag wird für ausgehende Sicherheitsregeln zu Web-Apps und Funktions-Apps empfohlen.  | Ausgehend | Ja | Ja |
| **AppServiceManagement** | Verwaltungsdatenverkehr für dedizierte Bereitstellungen der App Service-Umgebung. | Beide | Nein | Ja |
| **AzureActiveDirectory** | Azure Active Directory | Ausgehend | Nein | Ja |
| **AzureActiveDirectoryDomainServices** | Verwaltungsdatenverkehr für dedizierte Azure Active Directory Domain Services-Bereitstellungen. | Beide | Nein | Ja |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection | Ausgehend | Nein | Nein |
| **AzureArcInfrastructure** | Server mit Azure Arc-Unterstützung, Kubernetes mit Azure Arc-Unterstützung und Datenverkehr der Gastkonfiguration.<br/><br/>**Hinweis**: Dieses Tag weist eine Abhängigkeit von den Tags **AzureActiveDirectory**, **AzureTrafficManager** und **AzureResourceManager** auf. Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar.| Ausgehend | Nein | Ja |
| **AzureAttestation** | Azure Attestation.<br/><br/>**Hinweis**: Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar. | Ausgehend | Nein | Ja | 
| **AzureBackup** |Azure Backup.<br/><br/>**Hinweis**: Dieses Tag weist eine Abhängigkeit vom Tag **Storage** und **AzureActiveDirectory** auf. | Ausgehend | Nein | Ja |
| **AzureBotService** | Azure Bot Service | Ausgehend | Nein | Nein |
| **AzureCloud** | Alle [öffentlichen IP-Adressen im Rechenzentrum](https://www.microsoft.com/download/details.aspx?id=56519). | Ausgehend | Ja | Ja |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Dieses Tag oder die von diesem Tag abgedeckten IP-Adressen können verwendet werden, um Indexern einen sicheren Zugriff auf Datenquellen zu gewähren. Weitere Informationen finden Sie in der [Dokumentation zur Indexerverbindung](../search/search-indexer-troubleshooting.md#connection-errors). <br/><br/> **Hinweis**: Die IP-Adresse des Suchdiensts ist nicht in der Liste der IP-Adressbereiche für dieses Diensttag enthalten, und **es muss außerdem der IP-Firewall der Datenquellen hinzugefügt werden**. | Eingehend | Nein | Nein |
| **AzureConnectors** | Dieses Tag stellt die IP-Adresspräfixe für verwaltete Connectors dar, die eingehende Webhook-Rückrufe an den Azure Logic Apps-Dienst und ausgehende Aufrufe an die entsprechenden Dienste wie Azure Storage oder Azure Event Hubs senden. | Eingehend/Ausgehend | Ja | Ja |
| **AzureContainerRegistry** | Azure Container Registry. | Ausgehend | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos DB. | Ausgehend | Ja | Ja |
| **AzureDatabricks** | Azure Databricks | Beide | Nein | Nein |
| **AzureDataExplorerManagement** | Azure Data Explorer-Verwaltung | Eingehend | Nein | Nein |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Ausgehend | Nein | Ja |
| **AzureDeviceUpdate** | Device Update for IoT Hub | Beide | Nein | Ja |
| **AzureDevSpaces** | Azure Dev Spaces | Ausgehend | Nein | Nein |
| **AzureDevOps** | Azure Dev Ops.<br/><br/>**Hinweis**: Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar.| Eingehend | Nein | Ja |
| **AzureDigitalTwins** | Azure Digital Twins.<br/><br/>**Hinweis**: Dieses Tag oder die von diesem Tag abgedeckten IP-Adressen können verwendet werden, um den Zugriff auf Endpunkte einzuschränken, die für Ereignisrouten konfiguriert sind. Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar. | Eingehend | Nein | Ja |
| **AzureEventGrid** | Azure Event Grid: | Beide | Nein | Nein |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door | Beide | Nein | Nein |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>**Hinweis**: Dieses Tag weist eine Abhängigkeit von den Tags **AzureActiveDirectory**, **AzureFrontDoor.Frontend** und **AzureFrontDoor.FirstParty** auf. | Ausgehend | Nein | Nein |
| **AzureIoTHub** | Azure IoT Hub. | Ausgehend | Ja | Nein |
| **AzureKeyVault** | Azure Key Vault:<br/><br/>**Hinweis**: Dieses Tag weist eine Abhängigkeit vom Tag **AzureActiveDirectory** auf. | Ausgehend | Ja | Ja |
| **AzureLoadBalancer** | Das Lastenausgleichsmodul der Azure-Infrastruktur. Das Tag wird in eine [virtuelle IP-Adresse des Hosts](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16) umgewandelt, die als Ausgangspunkt für die Integritätstests von Azure dient. Dies umfasst nur den Testdatenverkehr, nicht den tatsächlichen Datenverkehr zu Ihrer Back-End-Ressource. Sie können diese Regel außer Kraft setzen, wenn Azure Load Balancer nicht verwendet wird. | Beide | Nein | Nein |
| **AzureMachineLearning** | Azure Machine Learning. | Beide | Nein | Ja |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon und benutzerdefinierte Metriken (GiG-Endpunkte).<br/><br/>**Hinweis:** Für Log Analytics ist auch das Tag **Storage** erforderlich. Wenn Linux-Agents verwendet werden, ist auch das **GuestAndHybridManagement**-Tag erforderlich. | Ausgehend | Nein | Ja |
| **AzureOpenDatasets** | Azure Open Datasets<br/><br/>**Hinweis**: Dieses Tag weist eine Abhängigkeit von den Tags **AzureFrontDoor.Frontend** und **Storage** auf. | Ausgehend | Nein | Nein |
| **AzurePlatformDNS** | Der grundlegende (standardmäßige) DNS-Dienst für die Infrastruktur.<br/><br>Sie können dieses Tag verwenden, um den standardmäßigen DNS-Dienst zu deaktivieren. Bei Verwendung dieses Tags ist Vorsicht geboten. Es wird empfohlen, die [Überlegungen zur Azure-Plattform](./network-security-groups-overview.md#azure-platform-considerations) durchzulesen. Außerdem wird empfohlen, vor der Verwendung dieses Tags entsprechende Tests auszuführen. | Ausgehend | Nein | Nein |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), ein grundlegender Infrastrukturdienst.<br/><br/>Sie können dieses Tag verwenden, um den standardmäßigen IMDS-Dienst zu deaktivieren. Bei Verwendung dieses Tags ist Vorsicht geboten. Es wird empfohlen, die [Überlegungen zur Azure-Plattform](./network-security-groups-overview.md#azure-platform-considerations) durchzulesen. Außerdem wird empfohlen, vor der Verwendung dieses Tags entsprechende Tests auszuführen. | Ausgehend | Nein | Nein |
| **AzurePlatformLKM** | Windows-Lizenzierungs- oder Schlüsselverwaltungsdienst.<br/><br/>Sie können dieses Tag verwenden, um die Standardwerte für die Lizenzierung zu deaktivieren. Bei Verwendung dieses Tags ist Vorsicht geboten. Es wird empfohlen, die [Überlegungen zur Azure-Plattform](./network-security-groups-overview.md#azure-platform-considerations) durchzulesen.  Außerdem wird empfohlen, vor der Verwendung dieses Tags entsprechende Tests auszuführen. | Ausgehend | Nein | Nein |
| **AzureResourceManager** | Azure Resource Manager | Ausgehend | Nein | Nein |
| **AzureSignalR** | Azure SignalR | Ausgehend | Nein | Nein |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>**Hinweis**: Dieses Tag weist eine Abhängigkeit von den Tags **AzureActiveDirectory**, **AzureKeyVault**, **EventHub**, **GuestAndHybridManagement** und **Storage** auf. | Ausgehend | Nein | Nein |
| **AzureSphere** | Dieses Tag oder die von diesem Tag abgedeckten IP-Adressen können verwendet werden, um den Zugriff auf Azure Sphere Security Services einzuschränken. </br> **Hinweis**: Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar. | Beide | Nein | Ja | 
| **AzureStack** | Azure Stack Bridge-Dienste. </br> Dieses Tag stellt den Endpunkt des Azure Stack Bridge-Diensts pro Region dar. </br> *Hinweis: Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar.* | Ausgehend | Nein | Ja |
| **AzureTrafficManager** | Test-IP-Adressen von Azure Traffic Manager.<br/><br/>Weitere Informationen zu Test-IP-Adressen von Traffic Manager finden Sie unter [Häufig gestellte Fragen (FAQ) zu Traffic Manager](../traffic-manager/traffic-manager-faqs.md). | Eingehend | Nein | Ja |  
| **AzureUpdateDelivery** | Für den Zugriff auf Windows-Updates. <br/><br/>**Hinweis**: Dieses Tag bietet Zugriff auf Windows Update-Metadatendienste. Zum erfolgreichen Herunterladen von Updates müssen Sie auch das Diensttag **AzureFrontDoor.FirstParty** aktivieren und Ausgangssicherheitsregeln mit wie folgt definiertem Protokoll und Port konfigurieren: <ul><li>AzureUpdateDelivery: TCP, Port 443</li><li>AzureFrontDoor.FirstParty: TCP, Port 80</li></ul>*Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar.*| Ausgehend | Nein | Nein |  
| **BatchNodeManagement** | Verwaltungsdatenverkehr für dedizierte Azure Batch-Bereitstellungen. | Beide | Nein | Ja |
| **CognitiveServicesManagement** | Die Adressbereiche des Datenverkehrs für Azure Cognitive Services. | Beide | Nein | Nein |
| **DataFactory**  | Azure Data Factory | Beide | Nein | Nein |
| **DataFactoryManagement** | Verwaltungsdatenverkehr für Azure Data Factory | Ausgehend | Nein | Nein |
| **Dynamics365ForMarketingEmail** | Die Adressbereiche für den Marketing-E-Mail-Dienst von Dynamics 365. | Ausgehend | Ja | Nein |
| **EOPExternalPublishedIPs** | Dieses Tag stellt die IP-Adressen dar, die für die PowerShell im „Security & Compliance Center“ verwendet werden. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Security & Compliance Center-PowerShell mithilfe des Moduls EXO V2](/powershell/exchange/connect-to-scc-powershell). <br/><br/> **Hinweis**: Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar. | Beide | Nein | Ja |
| **EventHub** | Azure Event Hubs. | Ausgehend | Ja | Ja |
| **GatewayManager** | Verwaltungsdatenverkehr für dedizierte Azure VPN Gateway- und Application Gateway-Bereitstellungen. | Eingehend | Nein | Nein |
| **GuestAndHybridManagement** | Azure Automation und Gastkonfiguration | Ausgehend | Nein | Ja |
| **HDInsight** | Azure HDInsight. | Eingehend | Ja | Nein |
| **Internet** | Der IP-Adressraum, der außerhalb des virtuellen Netzwerks liegt und über das öffentliche Internet erreichbar ist.<br/><br/>Der Adressbereich schließt den [Azure-eigenen öffentlichen IP-Adressraum](https://www.microsoft.com/download/details.aspx?id=41653) ein. | Beide | Nein | Nein |
| **LogicApps** | Logik-Apps | Beide | Nein | Nein |
| **LogicAppsManagement** | Verwaltungsdatenverkehr für Logik-Apps. | Eingehend | Nein | Nein |
| **MicrosoftAzureFluidRelay** | Dieses Tag stellt die IP-Adressen dar, die für Azure Microsoft Fluid Relay Server verwendet werden. | Ausgehend | Nein | Nein |
| **MicrosoftCloudAppSecurity** | Microsoft Defender für Cloud Apps. | Ausgehend | Nein | Nein |
| **MicrosoftContainerRegistry** | Containerregistrierung für Microsoft-Containerimages. <br/><br/>**Hinweis**: Dieses Tag weist eine Abhängigkeit vom Tag **AzureFrontDoor.FirstParty** auf. | Ausgehend | Ja | Ja |
| **PowerBI** | in Power BI synchronisiert wird. **Hinweis**: Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar. | Beide | Nein | Nein|
| **PowerPlatformInfra** | Dieses Tag stellt die IP-Adressen dar, die von der Infrastruktur zum Hosten von Power Platform-Diensten verwendet werden. **Hinweis**: Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar. | Ausgehend | Nein | Nein |
| **PowerQueryOnline** | Power Query Online | Beide | Nein | Nein |
| **ServiceBus** | Azure Service Bus-Datenverkehr, der die Dienstebene „Premium“ verwendet. | Ausgehend | Ja | Ja |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>**Hinweis**: Dieses Tag stellt den Service Fabric-Dienstendpunkt für die Steuerungsebene pro Region dar. Dadurch können Kunden Verwaltungsvorgänge für ihre Service Fabric-Cluster aus ihrem VNET vornehmen (Beispiel für Endpunkt https:// westus.servicefabric.azure.com). | Beide | Nein | Nein |
| **SQL** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB und Azure Synapse Analytics.<br/><br/>**Hinweis**: Dieses Tag stellt den Dienst dar, aber nicht bestimmte Instanzen des Diensts. Beispielsweise steht das Tag für den Azure SQL-Datenbank-Dienst, aber nicht für eine bestimmte SQL-Datenbank oder einen bestimmten SQL-Server. Dieses Tag gilt nicht für die verwaltete SQL-Instanz. | Ausgehend | Ja | Ja |
| **SqlManagement** | Verwaltungsdatenverkehr für dedizierte SQL-Bereitstellungen. | Beide | Nein | Ja |
| **Storage** | „Azure Storage“. <br/><br/>**Hinweis**: Dieses Tag stellt den Dienst dar, aber nicht bestimmte Instanzen des Diensts. Beispielsweise steht das Tag für den Azure Storage-Dienst, aber nicht für ein bestimmtes Azure Storage-Konto. | Ausgehend | Ja | Ja |
| **StorageSyncService** | Speichersynchronisierungsdienst | Beide | Nein | Nein |
| **WindowsAdminCenter** | Ermöglicht dem Windows Admin Center-Back-End-Dienst die Kommunikation mit der Kundeninstallation von Windows Admin Center. **Hinweis**: Dieses Tag ist derzeit nicht über das Azure-Portal konfigurierbar. | Ausgehend | Nein | Ja |
| **WindowsVirtualDesktop** | Windows Virtual Desktop | Beide | Nein | Ja |
| **VirtualNetwork** | Der Adressraum des virtuellen Netzwerks (alle für das virtuelle Netzwerk definierten IP-Adressbereiche), alle verbundenen lokalen Adressräume, [per Peering verknüpfte](virtual-network-peering-overview.md) virtuelle Netzwerke, virtuelle Netzwerke, die mit einem [Gateway des virtuellen Netzwerks](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json) verbunden sind, die [virtuelle IP-Adresse des Hosts](./network-security-groups-overview.md#azure-platform-considerations) und Adresspräfixe, die in [benutzerdefinierten Routen](virtual-networks-udr-overview.md) verwendet werden. Dieses Tag kann auch Standardrouten enthalten. | Beide | Nein | Nein |

> [!NOTE]
>
> - Mit Diensttags von Azure-Diensten werden die Adresspräfixe der jeweils verwendeten Cloud angegeben. Beispielsweise unterscheiden sich die zugrunde liegenden IP-Adressbereiche, die dem Wert des **Sql**-Tags in der öffentlichen Azure-Cloud entsprechen, von den zugrunde liegenden Bereichen in der chinesischen Azure-Cloud.
>
> - Wenn Sie einen [VNET-Dienstendpunkt](virtual-network-service-endpoints-overview.md) für einen Dienst implementieren, z. B. Azure Storage oder Azure SQL-Datenbank, fügt Azure eine [Route](virtual-networks-udr-overview.md#optional-default-routes) zu einem Subnetz des virtuellen Netzwerks für den Dienst hinzu. Die Adresspräfixe für die Route sind die gleichen Adresspräfixe bzw. CIDR-Bereiche wie diejenigen für das entsprechende Diensttag.


### <a name="tags-supported-in-the-classic-deployment-model"></a>Unterstützte Tags im klassischen Bereitstellungsmodell

Das klassische Bereitstellungsmodell (vor Azure Resource Manager) unterstützt einen kleinen Teil der in der obigen Tabelle aufgeführten Tags. Die Tags im klassischen Bereitstellungsmodell werden anders geschrieben, wie in der folgenden Tabelle gezeigt:

| Resource Manager-Tag | Entsprechendes Tag im klassischen Bereitstellungsmodell |
|---|---|
| **AzureLoadBalancer** | AZURE_LOADBALANCER |
| **Internet** | INTERNET |
| **VirtualNetwork** | VIRTUAL_NETWORK |


## <a name="service-tags-on-premises"></a>Lokale Diensttags  
Sie können die aktuellen Informationen zu Diensttag und Bereich abrufen und in Ihren lokalen Firewallkonfigurationen verwenden. Bei diesen Informationen handelt es sich um die zum jetzigen Zeitpunkt gültige Liste der IP-Adressbereiche, die den einzelnen Diensttags entsprechen. Sie können die Informationen programmgesteuert oder über einen JSON-Dateidownload abrufen (siehe Beschreibung in den folgenden Abschnitten).

### <a name="use-the-service-tag-discovery-api"></a>Verwendung der Diensttagermittlungs-API
Sie können die aktuelle Liste der Diensttags zusammen mit Details zum IP-Adressbereich programmgesteuert abrufen:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/network#az_network_list_service_tags)

Um beispielsweise alle Präfixe für das Tag „Storage Service“ abzurufen, können Sie die folgenden PowerShell-Cmdlets verwenden: 

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus2
$storage = $serviceTags.Values | Where-Object { $_.Name -eq "Storage" }
$storage.Properties.AddressPrefixes
```

> [!NOTE]
> 
> - Es dauert bis zu 4 Wochen, bis neue Diensttagdaten in den API-Ergebnissen alle Azure-Regionen übergreifend weitergeleitet werden. 
> - Sie müssen authentifiziert sein und über eine Rolle mit Leseberechtigungen für Ihr aktuelles Abonnement verfügen. 
> - Die API-Daten stellen diese Tags dar, die mit NSG-Regeln verwendet werden können, die eine Teilmenge der Tags darstellen, die sich derzeit in der herunterladbaren JSON-Datei befinden. 

### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Ermitteln von Diensttags mithilfe von herunterladbaren JSON-Dateien 
Sie können JSON-Dateien mit der aktuellen Liste der Diensttags zusammen mit Details zum IP-Adressbereich herunterladen. Diese Listen werden wöchentlich aktualisiert und veröffentlicht. Hier finden Sie die Speicherorte für die einzelnen Clouds:

- [Azure öffentlich](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Deutschland](https://www.microsoft.com/download/details.aspx?id=57064)   

Die IP-Adressbereiche in diesen Dateien liegen in CIDR-Notation vor. 

Die folgenden AzureCloud-Tags weisen keine regionalen Namen auf, die gemäß dem normalen Schema formatiert sind: 
- AzureCloud.centralfrance (Zentralfrankreich)
- AzureCloud.southfrance (Südfrankreich)
- AzureCloud.germanywc (West- und Mitteldeutschland)
- AzureCloud.germanyn (Norddeutschland)
- AzureCloud.norwaye (Ostnorwegen)
- AzureCloud.norwayw (Westnorwegen)
- AzureCloud.switzerlandn (Nordschweiz)
- AzureCloud.switzerlandw (Westschweiz)
- AzureCloud.usstagee (OstUSASTG)
- AzureCloud.usstagec (Süd-und MittelUSASTG)


> [!NOTE]
> Ein Teil dieser Informationen wurde in XML-Dateien für [Azure öffentlich](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064) und [Azure Deutschland](https://www.microsoft.com/download/details.aspx?id=54770) veröffentlicht. Diese XML-Downloads werden zum 30. Juni 2020 eingestellt und stehen nach diesem Datum nicht mehr zur Verfügung. Sie sollten stattdessen wie in den vorherigen Abschnitten beschrieben die Ermittlungs-API oder JSON-Dateidownloads verwenden.

> [!TIP]
> 
> - Sie können Updates zwischen verschiedenen Veröffentlichungen anhand der erhöhten *changeNumber*-Werte in der JSON-Datei erkennen. Jeder Teilabschnitt (z. B. **Storage.WestUS**) weist einen eigenen *changeNumber*-Wert auf, der bei Änderungen erhöht wird. Die oberste Ebene des *changeNumber*-Werts einer Datei wird erhöht, wenn einer der Teilabschnitte geändert wird.
>
> - Beispiele für die Analyse der Diensttaginformationen (z. B. zum Abrufen aller Adressbereiche für „Storage“ in „USA, Westen“) finden Sie in der Dokumentation zur [Diensttagermittlungs-API in PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag).
>
> - Wenn Diensttags neue IP-Adressen hinzugefügt werden, werden diese frühestens nach einer Woche in Azure verwendet. Dadurch haben Sie Zeit, die Systeme zu aktualisieren, von denen ggf. die den Diensttags zugeordneten IP-Adressen nachverfolgt werden müssen.


## <a name="next-steps"></a>Nächste Schritte
- [Erstellen einer Netzwerksicherheitsgruppe](tutorial-filter-network-traffic.md).
