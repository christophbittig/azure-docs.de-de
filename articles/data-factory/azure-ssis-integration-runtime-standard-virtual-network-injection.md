---
title: Konfigurieren eines virtuellen Netzwerks für die Standardeinschleusung von Azure-SSIS Integration Runtime
description: Erfahren Sie hier, wie Sie ein virtuelles Netzwerk für die Standardeinschleusung von Azure-SSIS Integration Runtime konfigurieren.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ee88df19385041de660da311fe51c66099d15cc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095123"
---
# <a name="standard-virtual-network-injection-method"></a>Standard-Einschleusungsmethode für virtuelle Netzwerke

Wenn Sie SQL Server Integration Services (SSIS) in Azure Data Factory (ADF) verwenden, gibt es zwei Methoden, um Ihre Azure-SSIS Integration Runtime (IR) mit einem virtuellen Netzwerk zu verbinden: Standard und Express. Wenn Sie die Standardmethode verwenden, müssen Sie Ihr virtuelles Netzwerk so konfigurieren, dass es die folgenden Anforderungen erfüllt:

- Stellen Sie sicher, *dass Microsoft.Batch* ein registrierter Ressourcenanbieter im Azure-Abonnement ist, der über das virtuelle Netzwerk verfügt, dem Azure-SSIS IR beitreten können. Ausführliche Anweisungen finden Sie im Abschnitt [Azure Batch als Ressourcenanbieter registrieren](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

- Stellen Sie sicher, dass der Benutzer, der Azure-SSIS IR erstellt, die erforderlichen Berechtigungen für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) zum Beitreten zum virtuellen Netzwerk/Subnetz erhält, um dem virtuellen Netzwerk/Teilnetz beizutreten.  Weitere Informationen finden Sie weiter unten im Abschnitt [Auswählen von Berechtigungen für virtuelle Netzwerke](#perms).

- Wählen Sie ein geeignetes Subnetz im virtuellen Netzwerk aus, Azure-SSIS IR beitreten soll. Weitere Informationen finden Sie weiter unten im Abschnitt Auswählen [eines Subnetzes.](#subnet)

Je nach Ihrem spezifischen Szenario können Sie optional Folgendes konfigurieren:

- Wenn Sie Ihre eigenen statischen öffentlichen IP-Adressen (BYOIP) für den ausgehenden Datenverkehr Ihrer Azure-SSIS IR verwenden möchten, lesen Sie den Abschnitt [Konfigurieren statischer öffentlicher IP-Adressen](#ip) weiter unten.

- Wenn Sie Ihren eigenen DNS-Server (Domain Name System) im virtuellen Netzwerk verwenden möchten, lesen Sie den Abschnitt [Konfigurieren eines benutzerdefinierten DNS-Servers](#dns) weiter unten.

- Wenn Sie eine Netzwerksicherheitsgruppe (NSG) verwenden möchten, um eingehenden/ausgehenden Datenverkehr im Subnetz einzuschränken, lesen Sie den Abschnitt [Konfigurieren einer NSG](#nsg) weiter unten.

- Wenn Sie benutzerdefinierte Routen (User-Defined Routes, UDRs) verwenden möchten, um ausgehenden Datenverkehr zu überwachen/zu überprüfen, lesen Sie den Abschnitt[ Konfigurieren von UDRs](#udr) weiter unten.

- Stellen Sie sicher, dass die Ressourcengruppe des virtuellen Netzwerks (oder die Ressourcengruppe der öffentlichen IP-Adressen, wenn Sie Ihre eigenen öffentlichen IP-Adressen verwenden) bestimmte Azure-Netzwerkressourcen erstellen und löschen kann. Weitere Informationen finden Sie unter [Konfigurieren der relevanten Ressourcengruppe.](#rg) 

- Wenn Sie Ihre Azure-SSIS IR wie im Artikel [Benutzerdefiniertes Setup für Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md) beschrieben anpassen, nutzt unser interner Prozess zum Verwalten der Knoten private IP-Adressen aus einem vordefinierten Bereich von *172.16.0.0* bis *172.31.255.255*. Daher sollten Sie sicherstellen, dass die privaten IP-Adressbereiche Ihrer virtuellen und lokalen Netzwerke nicht mit diesem Bereich kollidieren.

Dieses Diagramm veranschaulicht die für die Azure-SSIS IR erforderlichen Verbindungen:

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="Diagramm der für die Azure-SSIS IR erforderlichen Verbindungen":::

## <a name="select-virtual-network-permissions"></a><a name="perms"></a>Auswählen von virtuellen Netzwerkberechtigungen

Um die standardmäßige Einschleusung virtueller Netzwerke zu aktivieren, müssen dem Benutzer, der Azure-SSIS IR erstellt, die erforderlichen RBAC-Berechtigungen zum Beitreten zum virtuellen Netzwerk/Subnetz erteilt werden.

- Wenn Sie Ihre Azure SSIS IR in ein virtuelles Azure Resource Manager-Netzwerk einbinden, haben Sie zwei Möglichkeiten:

  - Verwenden Sie die integrierte Rolle *Netzwerkmitwirkender*. Diese Rolle umfasst die Berechtigung _Microsoft.Network/\*_ , die jedoch einen deutlich größeren Umfang als erforderlich hat.

  - Erstellen Sie eine benutzerdefinierte Rolle, die nur die erforderliche Berechtigung _Microsoft.Network/virtualNetworks/\*/join/action_ aufweist. Wenn Sie beim Verknüpfen der Azure-SSIS IR mit einem virtuellen Azure Resource Manager-Netzwerk auch Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwenden möchten, fügen Sie zudem die Berechtigung _Microsoft.Network/publicIPAddresses/\*/join/action_ mit in die Rolle ein.

  - Ausführliche Anweisungen finden Sie im Abschnitt [Erteilen von Berechtigungen für virtuelle Netzwerke](azure-ssis-integration-runtime-virtual-network-configuration.md#grantperms).

- Wenn Sie Ihre Azure-SSIS IR mit einem klassischen virtuellen Netzwerk verknüpfen, empfiehlt es sich, die integrierte Rolle *Mitwirkender für klassische virtuelle Computer* zu verwenden. Andernfalls müssen Sie eine benutzerdefinierte Rolle definieren, welche die Berechtigung zum Einbinden in das virtuelle Netzwerk enthält. Außerdem müssen Sie dieser integrierten/benutzerdefinierten Rolle *MicrosoftAzureBatch* zuweisen.

## <a name="select-a-subnet"></a><a name="subnet"></a>Wählen Sie ein Subnetz aus

Um die standardmäßige Einschleusung virtueller Netzwerke zu aktivieren, müssen Sie ein geeignetes Subnetz auswählen, damit Ihre Azure-SSIS IR beitreten kann:

- Wählen Sie nicht das GatewaySubnet aus, da es virtuellen Netzwerken vorbehalten ist.

- Stellen Sie sicher, dass das ausgewählte Subnetz über IP-Adressen für mindestens die doppelte Anzahl Ihrer Azure-SSIS IR-Knoten verfügt. Diese sind erforderlich, um Unterbrechungen beim Rollout von Patches/Upgrades für Ihre Azure-SSIS IR zu vermeiden. Azure reserviert auch einige IP-Adressen, die nicht in allen Subnetzen verwendet werden können. Die erste und letzte IP-Adresse werden für Protokollkonformität reserviert, während drei weitere Adressen für Azure-Dienste reserviert werden. Weitere Informationen finden Sie im Abschnitt [Einschränkungen für Subnetz-IP-Adressen](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

- Verwenden Sie kein Subnetz, das ausschließlich von anderen Azure-Diensten genutzt wird (z. B. Azure SQL Managed Instance, App Service usw.). 

## <a name="configure-static-public-ip-addresses"></a><a name="ip"></a>Konfigurieren einer statischen öffentlichen IP-Adresse

Wenn Sie ihre eigenen statischen öffentlichen IP-Adressen für den ausgehenden Datenverkehr von Azure-SSIS IR beim Verknüpfen mit einem virtuellen Netzwerk verwenden möchten, damit Sie diese in Ihren Firewalls zulassen können, stellen Sie sicher, dass sie die folgenden Anforderungen erfüllen:

- Es müssen genau zwei nicht verwendete Adressen bereitgestellt werden, die noch nicht mit anderen Azure-Ressourcen verknüpft sind. Die zusätzliche Adresse wird bei der regelmäßigen Aktualisierung Ihrer Azure-SSIS IR verwendet. Beachten Sie, dass eine einzige öffentliche IP-Adresse nicht von Ihren aktiven Azure-SSIS-IRs gemeinsam genutzt werden kann.

- Beide Adressen müssen statische Standardadressen sein. Weitere Details finden Sie unter [SKUs der öffentlichen IP-Adresse](../virtual-network/ip-services/public-ip-addresses.md#sku).

- Beide Adressen müssen einen DNS-Namen aufweisen. Wenn Sie bei der Erstellung noch keinen DNS-Namen angegeben haben, können Sie dies im Azure-Portal erledigen.

  :::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Azure-SSIS-Integrationslaufzeit":::

- Die Adressen und das virtuelle Netzwerk müssen sich im selben Abonnement und in derselben Region befinden.

## <a name="configure-a-custom-dns-server"></a><a name="dns"></a>Konfigurieren eines benutzerdefinierten DNS-Servers 

Wenn Sie Ihren eigenen DNS-Server im virtuellen Netzwerk verwenden möchten, um Ihre privaten Hostnamen aufzulösen, stellen Sie sicher, dass er auch globale Azure-Hostnamen auflösen kann (z. B. Ihre Azure Blob Storage namens `<your storage account>.blob.core.windows`.).

Es wird empfohlen, ihren eigenen DNS-Server so zu konfigurieren, dass nicht aufgelöste DNS-Anforderungen an die IP-Adresse rekursiver Azure-Konfliktlöser weitergeleitet werden (*168.63.129.16*).

Weitere Informationen finden Sie im Abschnitt [DNS-Servernamensauflösung](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!NOTE]
> Verwenden Sie bitte einen vollqualifizierten Domänennamen für Ihren privaten Hostnamen (verwenden Sie z. B. `<your_private_server>.contoso.com` anstelle von `<your_private_server>`). Alternativ können Sie ein benutzerdefiniertes Standardsetup in Ihrer Azure-SSIS IR verwenden, um automatisch Ihr eigenes DNS-Suffix (z. B. `contoso.com`) an einen nicht qualifizierten Domänennamen mit einer einzelnen Bezeichnung anfügen und in einen FQDN ändern, bevor Sie es in DNS-Abfragen verwenden. Weitere Informationen finden Sie im Abschnitt [Beispiele für das benutzerdefinierte  Standardsetup](how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

## <a name="configure-an-nsg"></a><a name="nsg"></a>Konfigurieren einer Netzwerksicherheitsgruppe (NSG)

Wenn Sie eine NSG in dem Subnetz verwenden möchten, das mit Ihrer Azure-SSIS IR verknüpft ist, lassen Sie den folgenden ausgehenden Datenverkehr zu:

| Direction | Transportprotokoll | `Source` | Quellports | Destination | Zielports | Kommentare | 
|-----------|--------------------|--------|--------------|-------------|-------------------|----------| 
| Eingehend | TCP | *BatchNodeManagement* | * | *VirtualNetwork* | *29876, 29877* (wenn Sie die SSIS IR mit einem virtuellen Azure Resource Manager-Netzwerk verknüpfen)<br/><br/>*10100, 20100, 30100* (wenn Sie Ihre SSIS IR mit einem klassischen virtuellen Netzwerk verbinden)| Der Data Factory-Dienst nutzt diese Ports für die Kommunikation mit den Knoten Ihrer Azure-SSIS IR im virtuellen Netzwerk.<br/><br/>Unabhängig davon, ob Sie eine NSG im Subnetz erstellen, konfiguriert Data Factory immer eine NSG auf der Netzwerkschnittstellenkarte (NIC), die an virtuelle Computer angefügt ist, die Ihre Azure-SSIS IR hosten.<br/><br/>Nur eingehender Datenverkehr von Data Factory-IP-Adressen für die angegebenen Ports wird durch diese NSG auf NIC-Ebene zugelassen.<br/><br/>Auch wenn Sie diese Ports für den Internetdatenverkehr auf Subnetzebene öffnen, wird Datenverkehr von anderen IP-Adressen als Data Factory-IP-Adressen auf NIC-Ebene blockiert. | 
| Eingehend | TCP | *CorpNetSaw* | * | *VirtualNetwork* | *3389* | (Optional) Nur erforderlich, wenn Ein Microsoft-Supporttechniker Sie auffordert, Port *3389* für die erweiterte Problembehandlung zu öffnen.Gleich nach Abschluss der Problembehandlung kann der Port wieder geschlossen werden.<br/><br/>Mit dem Servicetag *CorpNetSaw* können nur SAW-Computer (Secure Access Workstation) im Microsoft-Unternehmensnetzwerk über Remotedesktopprotokoll (RDP) auf Ihre Azure-SSIS IR zugreifen.<br/><br/>Dieses Diensttag kann im Portal nicht ausgewählt werden und steht nur über Azure PowerShell/CLI zur Verfügung.<br/><br/>In der NSG auf NIC-Ebene ist Port *3389* standardmäßig geöffnet. Sie können ihn jedoch mit einer NSG auf Subnetzebene steuern, während ausgehender Datenverkehr auf ihren Azure-SSIS IR Knoten gemäß Windows Firewallregel standardmäßig nicht zulässig ist. | 

| Direction | Transportprotokoll | `Source` | Quellports | Destination | Zielports | Kommentare |
|-----------|--------------------|--------|--------------|-------------|-------------------|----------|
| Ausgehend | TCP | *VirtualNetwork* | * | *AzureCloud* | *443* | Erforderlich, damit Ihre Azure-SSIS IR auf Azure-Dienste wie Azure Storage und Azure Event Hubs zugreifen kann. | 
| Ausgehend | TCP | *VirtualNetwork* | * | *Internet* | *80* | (Optional) Ihr Azure-SSIS IR verwendet diesen Port, um eine Zertifikatsperrliste (Certificate Revocation List, CRL) aus dem Internet herunterzuladen.<br/><br/>Wenn Sie diesen Datenverkehr blockieren, kann es beim Starten ihrer Azure-SSIS IR zu einer Leistungsbeeinträchtigung kommen und die Möglichkeit zum Überprüfen von CRLs bei der Verwendung von Zertifikaten verloren gehen. Dies wird unter Sicherheitsgesichtspunkten nicht empfohlen.<br/><br/>Wenn Sie Ziele auf bestimmte FQDNs einengen möchten, lesen Sie den Abschnitt **Konfigurieren von UDRs** weiter unten | 
| Ausgehend | TCP | *VirtualNetwork* | * | *Sql/VirtualNetwork* | *1433, 11000–11999* | (Optional) Nur erforderlich, wenn Sie Azure SQL-Datenbank-Server/Verwaltete Instanz zum Hosten des SSIS-Katalogs (SSISDB) verwenden.<br/><br/>Wenn Ihr(e) Azure SQL-Datenbank-Server/Verwaltete Instanz mit einem öffentlichen Endpunkt/VNET-Dienstendpunkt konfiguriert ist, verwenden Sie das Servicetag *SQL* als Ziel.<br/><br/>Wenn Ihr(e) Azure SQL-Datenbank-Server/Verwaltete Instanz mit einem privaten Endpunkt konfiguriert ist, verwenden Sie das Servicetag *VirtualNetwork* als Ziel.<br/><br/>Wenn die Verbindungsrichtlinie Ihres Servers auf *Proxy* statt auf *Redirect* festgelegt ist, wird nur Port *1433* benötigt. | 
| Ausgehend | TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *443* | (Optional) Nur erforderlich, wenn Sie Azure Storage-Blobcontainer zum Speichern Ihrer standardmäßigen benutzerdefinierten Setupskripts/-dateien verwenden.<br/><br/>Wenn Ihr Azure Storage mit einem öffentlichen Endpunkt/VNET-Serviceendpunkt konfiguriert ist, verwenden Sie das Servicetag *Storage* als Ziel.<br/><br/>Wenn Ihr Azure Storage mit einem privaten Endpunkt konfiguriert ist, verwenden Sie das Servicetag *VirtualNetwork* als Ziel. | 
| Ausgehend | TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *445* | (Optional) Nur erforderlich, wenn Sie auf Azure Files zugreifen müssen.<br/><br/>Wenn Ihr Azure Storage mit einem öffentlichen Endpunkt/VNET-Serviceendpunkt konfiguriert ist, verwenden Sie das Servicetag *Storage* als Ziel.<br/><br/>Wenn Ihr Azure Storage mit einem privaten Endpunkt konfiguriert ist, verwenden Sie das Servicetag *VirtualNetwork* als Ziel. | 

## <a name="configure-udrs"></a><a name="udr"></a>Konfigurieren von UDRs

Wenn Sie den ausgehenden Datenverkehr von Ihrer Azure-SSIS IR überwachen/überprüfen möchten, können Sie benutzerdefinierte Routen [(User-Defined Routes, UDRs)](../virtual-network/virtual-networks-udr-overview.md) verwenden, um ihn über [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) erzwungenes Tunneling, das eine BGP-Route (Border Gateway Protocol)-Route *0.0.0.0/0* anzeigt, an das virtuelle Netzwerk, an ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA), das als Firewall konfiguriert ist, an das virtuelle Netzwerkgerät (Network Virtual Appliance, NVA) oder an den [Azure Firewall-Service](../firewall/overview.md) umzuleiten.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="NVA-Szenario für Azure-SSIS IR":::

Damit dies funktioniert, müssen Sie Folgendes sicherstellen:

- Der Datenverkehr zwischen Azure Batch Verwaltungsdienst und Ihrem Azure-SSIS IR sollte nicht an eine Firewall-Appliance/einen Firewalldienst weitergeleitet werden.

- Die Firewallappliance muss ausgehenden Datenverkehr zulassen, der von Azure-SSIS IR benötigt wird.

Wenn der Datenverkehr zwischen dem Azure Batch-Verwaltungsdienst und Ihrem Azure-SSIS IR an ein Firewallgerät/einen Firewallservice geroutet wird, wird er aufgrund von asymmetrischem Routing unterbrochen. UDRs müssen für diesen Datenverkehr definiert werden, sodass er über dieselben Routen laufen kann, über die er hereingekommen ist. Sie können bestimmte UDRs definieren, um Datenverkehr zwischen Azure Batch-Verwaltungsdiensten und der Azure-SSIS IR mit *Internet* als dem nächsten Schritt weiterzuleiten.

Wenn sich Ihr Azure-SSIS IR beispielsweise im *Vereinigtes Königreich, Süden* befindet und Sie den ausgehenden Datenverkehr mithilfe von Azure Firewall überprüfen möchten, können Sie zuerst die IP-Adressbereiche für das Servicetag *BatchNodeManagement.UKSouth* über den [Downloadlink Servicetag-IP-Bereich](https://www.microsoft.com/download/details.aspx?id=56519) oder die [Servicetag-Erkennungs-API](../virtual-network/service-tags-overview.md#service-tags-on-premises) abrufen. Wenden Sie dann die folgenden UDRs von verknüpften IP-Bereichs Routen mit dem Typ des nächsten Hops als *Internet* zusammen mit der Route *0.0.0.0/0* mit dem Typ *virtuelles Gerät an*.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Azure Batch-UDR-Einstellungen":::

> [!NOTE]
> Dieser Ansatz führt zu zusätzlichen Wartungskosten, da Sie regelmäßig die relevanten IP-Adressbereiche überprüfen und UDRs für neue hinzufügen müssen, um zu vermeiden, dass Ihre Azure-SSIS IR unterbrochen wird. Es wird empfohlen, sie monatlich zu überprüfen, da es einen weiteren Monat dauert, bis ein neuer IP-Adressbereich für das entsprechende Servicetag in Kraft tritt. 

Sie können das folgende PowerShell-Skript ausführen, um UDRs für den Azure Batch Verwaltungsdienst hinzuzufügen:

```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your route table]"
$RouteTableResourceName = "[resource name of your route table]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start adding UDRs to your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch Azure service tag, please confirm that your location is valid."
}
```

Entsprechend unserer Anleitung im Abschnitt [Konfigurieren einer Netzwerksicherheitsgruppe (NSG)](#nsg) weiter oben müssen Sie ähnliche Regeln für die Firewallappliance/den Firewalldienst implementieren, um den ausgehenden Datenverkehr von Ihrer Azure-SSIS IR zuzulassen:

- Wenn Sie die Azure Firewall verwenden, gilt Folgendes:
  - Sie müssen Port *443* für ausgehenden TCP-Datenverkehr mit dem Servicetag *DataFactoryManagement* als Ziel öffnen.

  - Wenn Sie Azure SQL-Datenbank Server/Verwaltete Instanz zum Hosten von SSISDB verwenden, müssen Sie die Ports *1433, 11000-11999* für ausgehenden TCP-Datenverkehr mit *dem Servicetag "Sql/VirtualNetwork"* als Ziel öffnen.

  - Wenn Sie Azure Storage Blobcontainer zum Speichern Ihrer standardmäßigen benutzerdefinierten Setupskripts/-dateien verwenden, müssen Sie Port *443* für ausgehenden TCP-Datenverkehr mit dem Servicetag *Storage/VirtualNetwork* als Ziel öffnen.

  - Wenn Sie auf Azure Files zugreifen müssen, müssen Sie Port *445* für ausgehenden TCP-Datenverkehr mit dem Diensttag *Storage/VirtualNetwork* als Ziel öffnen.

- Sie andere Firewallappliances/-dienste verwenden:
  - Sie müssen Port *443* für ausgehenden TCP-Datenverkehr mit *0.0.0.0/0* oder dem folgenden Azure-umgebungsspezifischen FQDN als Ziel öffnen.

    | Azure-Umgebung | FQDN |
    |-------------------|-------|
    | <b>Azure öffentlich</b> | <ul><li><b>Azure Data Factory (Verwaltung)</b><ul><li>_\*.frontend.clouddatahub.net_</li></ul></li><li><b>Azure Storage (Verwaltung)</b><ul><li>_\*.blob.core.windows.net_</li><li>_\*.table.core.windows.net_</li></ul></li><li><b>Azure Container Registry (Benutzerdefiniertes Setup)</b><ul><li>_\*.azurecr.io_</li></ul></li><li><b>Event Hubs (Logging)</b><ul><li>_\*.servicebus.windows.net_</li></ul></li><li><b>Microsoft-Protokollierungsdienst (Interne Verwendung)</b><ul><li>_gcs.prod.monitoring.core.windows.net_</li><li>_prod.warmpath.msftcloudes.com_</li><li>_azurewatsonanalysis-prod.core.windows.net_</li></ul></li></ul> |
    | <b>Azure Government</b> | <ul><li><b>Azure Data Factory (Verwaltung)</b><ul><li>_\*.frontend.datamovement.azure.us_</li></ul></li><li><b>Azure Storage (Verwaltung)</b><ul><li>_\*.blob.core.usgovcloudapi.net_</li><li>_\*.table.core.usgovcloudapi.net_</li></ul></li><li><b>Azure Container Registry (Benutzerdefiniertes Setup)</b><ul><li>_\*.azurecr.us_</li></ul></li><li><b>Event Hubs (Logging)</b><ul><li>_\*.servicebus.usgovcloudapi.net_</li></ul></li><li><b>Microsoft-Protokollierungsdienst (Interne Verwendung)</b><ul><li>_fairfax.warmpath.usgovcloudapi.net_</li><li>_azurewatsonanalysis.usgovcloudapp.net_</li></ul></li></ul> |
    | <b>Azure China 21Vianet</b> | <ul><li><b>Azure Data Factory (Verwaltung)</b><ul><li>_\*.frontend.datamovement.azure.cn_</li></ul></li><li><b>Azure Storage (Verwaltung)</b><ul><li>_\*.blob.core.chinacloudapi.cn_</li><li>_\*.table.core.chinacloudapi.cn_</li></ul></li><li><b>Azure Container Registry (Benutzerdefiniertes Setup)</b><ul><li>_\*.azurecr.cn_</li></ul></li><li><b>Event Hubs (Logging)</b><ul><li>_\*.servicebus.chinacloudapi.cn_</li></ul></li><li><b>Microsoft-Protokollierungsdienst (Interne Verwendung)</b><ul><li>_mooncake.warmpath.chinacloudapi.cn_</li><li>_azurewatsonanalysis.chinacloudapp.cn_</li></ul></li></ul> |

  - Wenn Sie Azure SQL-Datenbank server/Verwaltete Instanz zum Hosten von SSISDB verwenden, müssen Sie die Ports *1433, 11000-11999* für ausgehenden TCP-Datenverkehr mit *0.0.0.0/0* oder Ihren Azure SQL-Datenbank-Server/verwaltete  Instanz-FQDN als Ziel öffnen.

  - Wenn Sie Azure Storage Blobcontainer zum Speichern Ihrer standardmäßigen benutzerdefinierten Setupskripts/-dateien verwenden, müssen Sie Port *443* für ausgehenden TCP-Datenverkehr mit dem Servicetag *0.0.0.0/0* oder Ihrem Azure Blob Storage-FQDN als Ziel öffnen.

  - Wenn Sie auf Azure Files zugreifen müssen, müssen Sie Port *445* für ausgehenden TCP-Datenverkehr mit *0.0.0.0/0* oder Ihrem Azure Files-FQDN als Ziel öffnen.

- Wenn Sie einen VNET-Dienstendpunkt für Azure Storage/Container Registry/Event Hubs/SQL konfigurieren, indem Sie *Microsoft.Storage*/*Microsoft.ContainerRegistry*/*Microsoft.EventHub*/*Microsoft.Sql* Ressourcen in Ihrem Subnetz aktivieren, wird der sämtliche Datenverkehr zwischen Ihrer Azure-SSIS IR und diesen Diensten in den gleichen/gekoppelten Regionen an das Azure-Backbonenetzwerk anstatt an Ihre Firewall appliance/service geroutet.

- Sie sollten Port *80* für ausgehenden TCP-Datenverkehr mit der folgenden Zertifikatsperrliste (Certificate Revocation List, CRL) als Ziel öffnen:

  - *crl.microsoft.com:80*
  - *mscrl.microsoft.com:80*
  - *crl3.digicert.com:80*
  - *crl4.digicert.com:80*
  - *ocsp.digicert.com:80*
  - *cacerts.digicert.com:80*
  
  Wenn Sie Zertifikate mit unterschiedlichen CRLs verwenden, sollten Sie auch deren Downloadwebsites als Ziel hinzufügen. Weitere Informationen finden Sie im Artikel zur [Funktionsweise der Zertifikatsperrung](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

  Wenn Sie diesen Datenverkehr blockieren, kann es beim Starten ihrer Azure-SSIS IR zu einer Leistungsbeeinträchtigung kommen und die Möglichkeit zum Überprüfen von CRLs bei der Verwendung von Zertifikaten verloren gehen. Dies wird unter Sicherheitsgesichtspunkten nicht empfohlen.

Wenn Sie den ausgehenden Datenverkehr aus Ihrem Azure-SSIS IR nicht überwachen/untersuchen müssen, können Sie UDRs verwenden, um den gesamten Datenverkehr mit dem *Internet* als dem nächsten Schritt zu erzwingen:

- Wenn Sie Azure ExpressRoute verwenden, können Sie eine UDR für die Route *0.0.0.0/0* in Ihrem Subnetz  mit dem *Internet* als dem nächsten Schritt konfigurieren. 

- Wenn Sie ein virtuelles Netzwerkgerät verwenden, können Sie den vorhandenen UDR für  Route *0.0.0.0/0* in Ihrem Subnetz ändern, um den nächsten Schritt von *Virtuelles Gerät* in *Internet* zu ändern.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="Hinzufügen einer Route":::

> [!NOTE]
> Die Angabe einer Route mit dem *Internet* as dem nächsten Schritt bedeutet nicht, dass der gesamte Datenverkehr über das Internet gesendet wird. Solange die Zieladresse zu einem der Azure-Dienste gehört, wird der gesamte Datenverkehr von Azure über das Azure-Backbonenetzwerk statt über das Internet an diese Adresse weitergeleitet.

## <a name="configure-the-relevant-resource-group"></a><a name="rg"></a>Konfigurieren der relevanten Ressourcengruppe

Um die standardmäßige Einstellung virtueller Netzwerke zu aktivieren, muss Ihr Azure-SSIS IR bestimmte Netzwerkressourcen in derselben Ressourcengruppe wie das virtuelle Netzwerk erstellen. Diese Ressourcen umfassen Folgendes:

- Ein Azure-Lastenausgleich mit dem Namen _\<Guid\>-azurebatch-cloudserviceloadbalancer_.
- Eine öffentliche Azure-IP-Adresse mit dem Namen _\<Guid\>-azurebatch-cloudservicepublicip_.
- Eine Netzwerksicherheitsgruppe mit dem Namen _\<Guid\>-azurebatch-cloudservicenetworksecuritygroup_. 

> [!NOTE]
> Sie können jetzt Ihre eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR verwenden. In diesem Szenario erstellen wir nur den Azure-Lastenausgleich und die Netzwerksicherheitsgruppe in derselben Ressourcengruppe wie Ihre statischen öffentlichen IP-Adressen und nicht im virtuellen Netzwerk.

Diese Ressourcen werden beim Start Ihrer Azure-SSIS IR erstellt. Sie werden gelöscht, wenn die Azure-SSIS IR beendet wird. Wenn Sie Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwenden, werden diese beim Beenden der Azure-SSIS IR nicht gelöscht. Damit Ihre Azure-SSIS IR ordnungsgemäß beendet werden kann, sollten Sie diese Netzwerkressourcen nicht in anderen Ressourcen wiederverwenden.

Stellen Sie sicher, dass es keine Ressourcensperre für die Ressourcengruppe oder das Abonnement gibt, zu der bzw. dem das virtuelle Netzwerk gehört bzw. die öffentlichen IP-Adressen gehören. Wenn Sie eine Schreibschutzsperre oder eine Löschsperre konfigurieren, kann beim Starten und Beenden Ihrer Azure-SSIS IR ein Fehler auftreten oder die IR nicht mehr reagieren.

Stellen Sie sicher, dass Sie keine Azure Richtlinien-Zuweisung haben, die verhindert, dass die folgenden Ressourcen unter der Ressourcengruppe oder dem Abonnement erstellt werden, zu der bzw. dem das virtuelle Netzwerk gehört bzw. die öffentlichen IP-Adressen gehören: 

- *Microsoft.Network/LoadBalancers* 
- *Microsoft.Network/NetworkSecurityGroups* 
- *Microsoft.Network/PublicIPAddresses* 

Stellen Sie sicher, dass das Ressourcenkontingent für Ihr Abonnement für diese Ressourcen ausreicht. Insbesondere müssen Sie für jede Azure-SSIS IR, die in einem virtuellen Netzwerk erstellt wurden, die doppelte Anzahl dieser Ressourcen reservieren, da die zusätzlichen Ressourcen verwendet werden, wenn wir ihr Azure-SSIS IR regelmäßig aktualisieren.

## <a name="faq"></a><a name="faq"></a>Häufig gestellte Fragen

- Wie kann ich die öffentliche IP-Adresse schützen, die in meiner Azure-SSIS IR für eingehende Verbindungen verfügbar gemacht wird? Ist es möglich, die öffentliche IP-Adresse zu entfernen?
 
  Derzeit wird automatisch eine öffentliche IP-Adresse erstellt, wenn Ihre Azure-SSIS IR mit einem virtuellen Netzwerk verknüpft wird. Wir verfügen über eine NSG auf NIC-Ebene, die ausschließlich eingehende Verbindungen von Azure Batch-Verwaltungsdiensten mit Ihrer Azure-SSIS IR zulässt. Sie können auch eine NSG auf Subnetzebene festlegen, um eingehende Verbindungen zu schützen.

  Wenn eine öffentliche IP-Adresse nicht verfügbar gemacht werden soll, können Sie eine [selbstgehostete IR als Proxy für die Azure-SSIS IR konfigurieren](self-hosted-integration-runtime-proxy-ssis.md) anstatt die Azure-SSIS IR zu einem virtuellen Netzwerk hinzuzufügen, wenn dies auf Ihr Szenario zutrifft.
 
- Kann ich die öffentliche IP-Adresse meines Azure-SSIS IR in die Positivliste der Firewall für meine Datenquellen aufnehmen?

  Sie können jetzt Ihre eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR verwenden. In diesem Fall können Sie Ihre IP-Adressen in die Firewall-Zulassungsliste für Ihre Datenquellen aufnehmen. Sie können auch andere, unten aufgeführte Optionen in Erwägung ziehen, um den Datenzugriff ausgehend von Ihrer Azure-SSIS IR je nach Ihrem jeweiligen Szenario zu sichern:

  - Wenn Ihre Datenquelle lokal ist, können Sie, nachdem Sie ein virtuelles Netzwerk mit Ihrem lokalen Netzwerk verbunden haben und Ihre Azure-SSIS IR mit dem Subnetz des virtuellen Netzwerks verknüpft haben, den privaten IP-Adressbereich dieses Subnetzes zur Positivliste der Firewall für Ihre Datenquelle hinzufügen.

  - Wenn es sich bei der Datenquelle um einen Azure-Dienst handelt, der VNET-Dienstendpunkte unterstützt, können Sie in Ihrem virtuellen Netzwerk einen VNET-Dienstendpunkt konfigurieren und Ihre Azure-SSIS IR mit dem betreffenden Subnetz verknüpfen. Anschließend können Sie der Firewall für die Datenquelle eine VNET-Regel für dieses Subnetz hinzufügen.

  - Wenn es sich bei Ihrer Datenquelle um einen Nicht-Azure-Clouddienst handelt, können Sie den ausgehenden Datenverkehr von Ihrer Azure-SSIS IR über eine NVA/NVA an ihre statische öffentliche IP-Adresse Azure Firewall routen. Anschließend können Sie die statische öffentliche IP-Adresse Ihrer NVA/Azure Firewall zur Positivliste der Firewall für Ihre Datenquelle hinzufügen.

  - Wenn keine der oben genannten Optionen Ihre Anforderungen erfüllt, können Sie die [Konfiguration einer selbstgehosteten IR als Proxy für Ihre Azure-SSIS IR](self-hosted-integration-runtime-proxy-ssis.md) in Erwägung ziehen. Anschließend können Sie die statische öffentliche IP-Adresse des Hostcomputers für die selbstgehostete IR zur Positivliste der Firewall für Ihre Datenquelle hinzufügen.

- Warum muss ich zwei statische öffentliche Adressen angeben, wenn ich meine eigenen Adressen für die Azure-SSIS IR verwenden möchte?

  Die Azure-SSIS IR wird regelmäßig automatisch aktualisiert. Beim Upgrade werden neue Knoten erstellt und alte werden gelöscht. Um Ausfallzeiten zu vermeiden, werden die alten Knoten jedoch erst dann gelöscht, wenn die neuen einsatzbereit sind. Daher kann Ihre erste statische öffentliche IP-Adresse, die von den alten Knoten verwendet wird, nicht sofort freigegeben werden, sodass eine zweite statische öffentliche IP-Adresse zum Erstellen der neuen Knoten benötigt wird.

- Ich verwende meine eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR. Warum kann die IR immer noch nicht auf meine Datenquellen zugreifen?

  Vergewissern Sie sich, dass beide statischen öffentlichen IP-Adressen der Positivliste der Firewall für Ihre Datenquellen hinzugefügt wurden. Bei jedem Upgrade der Azure-SSIS IR erfolgt ein Wechsel der statischen öffentlichen IP-Adresse zwischen den beiden eigenen Adressen. Wenn Sie nur auch eine Adresse zur Positivliste hinzufügen, ist nach dem Upgrade der Datenzugriff für Ihre Azure-SSIS IR unterbrochen.

  Wenn es sich bei Ihrer Datenquelle um einen Azure-Dienst handelt, überprüfen Sie, ob sie mit VNET-Dienstendpunkten konfiguriert wurde. Wenn dies der Fall ist, wechselt der Datenverkehr von Azure-SSIS IR zu Ihrer Datenquelle, um die privaten IP-Adressen zu verwenden, die von Azure-Diensten verwaltet werden, und das Hinzufügen eigener statischer öffentlicher IP-Adressen zur Firewall-Positivliste für Ihre Datenquelle wird nicht wirksam.

## <a name="next-steps"></a>Nächste Schritte

- [Verknüpfen einer Azure-SSIS IR mit einem virtuellen Netzwerk über die ADF-Benutzeroberfläche](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Verknüpfen von Azure-SSIS IR mit einem virtuellen Netzwerk über Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 

- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Es wird der Azure-SQL-Datenbank-Server zum Hosten von SSISDB verwendet. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Es enthält Anweisungen zur Verwendung des Azure-SQL-Datenbank-Servers, der mit einem VNET-Dienstendpunkt/einer IP-Firewallregel/einem privaten Endpunkt konfiguriert ist, oder einer Azure SQL verwalteten Instanz, die mit einem virtuellen Netzwerk zum Hosten von SSISDB verknüpft wird. Außerdem wird veranschaulicht, wie Sie Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.
