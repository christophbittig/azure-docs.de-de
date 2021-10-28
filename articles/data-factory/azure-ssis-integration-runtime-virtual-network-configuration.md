---
title: Konfigurieren Sie ein virtuelles Azure-Netzwerk für eine Azure-SSIS Integration Runtime
description: Erfahren Sie, wie Sie ein virtuelles Azure-Netzwerk für eine Azure-SSIS Integration Runtime konfigurieren.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b4274144e8437bd47adad0854fec5c3ed9550ffb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229406"
---
# <a name="virtual-network-configuration-for-an-azure-ssis-integration-runtime"></a>Konfiguration des virtuellen Netzwerks für eine Azure-SSIS Integration Runtime

Achten Sie bei der Einrichtung des virtuellen Netzwerks auf die Einhaltung der folgenden Anforderungen: 

- Stellen Sie sicher, dass `Microsoft.Batch` ein registrierter Anbieter unter dem Abonnement Ihres Subnetzes des virtuellen Netzwerks ist, in dem die Azure-SSIS IR gehostet wird. Fügen Sie der Rolle „Mitwirkender für klassische virtuelle Computer“ für das virtuelle Netzwerk auch `MicrosoftAzureBatch` hinzu, wenn Sie ein klassisches virtuelles Netzwerk verwenden. 

- Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen. Weitere Informationen finden Sie unter [Einrichten von Berechtigungen](#perms).

- Wählen Sie das richtige Subnetz zum Hosten der Azure-SSIS Integration Runtime aus. Weitere Informationen finden Sie unter [Auswählen des Subnetzes](#subnet). 

- Wenn Sie Ihre eigenen öffentlichen IP-Adressen für die Azure-SSIS IR verwenden, lesen Sie [Auswählen der statischen öffentlichen IP-Adressen](#publicIP).

- Wenn Sie Ihren eigenen DNS (Domain Name System)-Server im virtuellen Netzwerk verwenden, finden Sie weitere Informationen unter [Einrichten des DNS-Servers](#dns_server). 

- Wenn Sie eine Netzwerksicherheitsgruppe (NSG) im Subnetz verwenden, finden Sie Weitere Informationen unter [Einrichten einer NSG](#nsg). 

- Wenn Sie Azure ExpressRoute oder eine benutzerdefinierte Route (User-Defined Route, UDR) verwenden, finden Sie weitere Informationen unter [Verwenden von Azure ExpressRoute oder einer UDR](#route). 

- Stellen Sie sicher, dass die Ressourcengruppe des virtuellen Netzwerks (oder die Ressourcengruppe der öffentlichen IP-Adressen, wenn Sie Ihre eigenen öffentlichen IP-Adressen verwenden) bestimmte Azure-Netzwerkressourcen erstellen und löschen kann. Weitere Informationen finden Sie unter [Einrichten der Ressourcengruppe](#resource-group). 

- Wenn Sie die Azure-SSIS IR anpassen, wie im Thema zur [benutzerdefinierten Einrichtung der Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) beschrieben, werden beim internen Prozess zum Verwalten der Knoten private IP-Adressen aus einem vordefinierten Bereich von 172.16.0.0 bis 172.31.255.255 verwendet. Daher sollten Sie sicherstellen, dass die Bereiche privater IP-Adressen Ihrer virtuellen oder lokalen Netzwerke nicht mit diesem Bereich kollidieren.

Dieses Diagramm veranschaulicht die für die Azure-SSIS IR erforderlichen Verbindungen:

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="Diagramm der für die Azure-SSIS IR erforderlichen Verbindungen":::

## <a name="set-up-permissions"></a><a name="perms"></a> Einrichten von Berechtigungen

Der Benutzer, der die Azure-SSIS IR erstellt, muss über die folgenden Berechtigungen verfügen:

- Wenn Sie Ihre SSIS IR in ein virtuelles Azure Resource Manager-Netzwerk einbinden, haben Sie zwei Möglichkeiten:

  - Verwenden Sie die integrierte Rolle „Netzwerkmitwirkender“. Diese Rolle umfasst die Berechtigung _Microsoft.Network/\*_ , die jedoch einen deutlich größeren Umfang als erforderlich hat.

  - Erstellen Sie eine benutzerdefinierte Rolle, die nur die erforderliche Berechtigung _Microsoft.Network/virtualNetworks/\*/join/action_ aufweist. Wenn Sie beim Verknüpfen der IR mit einem virtuellen Azure Resource Manager-Netzwerk Ihre eigenen öffentlichen IP-Adressen für die Azure SSIS IR verwenden möchten, beziehen Sie auch die Berechtigung _Microsoft.Network/publicIPAddresses/*/join/action_ mit in die Rolle ein.

- Wenn Sie die SSIS IR mit einem klassischen virtuellen Netzwerk verknüpfen, wird empfohlen, die integrierte Rolle „Mitwirkender für klassische virtuelle Computer“ zu verwenden. Andernfalls müssen Sie eine benutzerdefinierte Rolle definieren, die die Berechtigung zum Einbinden in das virtuelle Netzwerk enthält.

## <a name="select-the-subnet"></a><a name="subnet"></a> Auswählen des Subnetzes

Hinweise zur Auswahl eines Subnetzes: 

- Sie sollten nicht das GatewaySubnet zum Bereitstellen einer Azure-SSIS IR auswählen, da es Gateways für virtuelle Netzwerke vorbehalten ist. 

- Stellen Sie sicher, dass das ausgewählte Subnetz ausreichend verfügbaren Adressraum für die Azure-SSIS IR aufweist. Sie benötigen mindestens doppelt so viele verfügbare IP-Adressen, wie IR-Knoten vorhanden sind. Einige IP-Adressen innerhalb jedes Subnetzes sind in Azure reserviert. Diese Adressen können nicht verwendet werden. Die erste und letzte IP-Adresse der Subnetze sind aus Gründen der Protokollkonformität reserviert. Darüber hinaus werden drei weitere Adressen für Azure-Dienste verwendet. Weitere Informationen finden Sie unter [Unterliegen die in den Subnetzen verwendeten IP-Adressen bestimmten Beschränkungen?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Verwenden Sie kein Subnetz, das ausschließlich von anderen Azure-Diensten genutzt wird (z. B. SQL Managed Instance einer SQL-Datenbank, App Service usw.). 

## <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Auswählen der statischen öffentlichen IP-Adressen

Wenn Sie Ihre eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR verwenden möchten, während Sie sie mit einem virtuellen Netzwerk verknüpfen, müssen die Adressen die folgenden Anforderungen erfüllen:

- Es müssen genau zwei nicht verwendete Adressen bereitgestellt werden, die noch nicht mit anderen Azure-Ressourcen verknüpft sind. Die zusätzliche Adresse wird bei der regelmäßigen Aktualisierung Ihrer Azure-SSIS IR verwendet. Beachten Sie, dass eine einzige öffentliche IP-Adresse nicht von Ihren aktiven Azure-SSIS-IRs gemeinsam genutzt werden kann.

- Beide Adressen müssen statische Standardadressen sein. Weitere Details finden Sie unter [SKUs der öffentlichen IP-Adresse](../virtual-network/ip-services/public-ip-addresses.md#sku).

- Beide Adressen müssen einen DNS-Namen aufweisen. Wenn Sie bei der Erstellung keinen DNS-Namen angegeben haben, können Sie dies im Azure-Portal erledigen.

:::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Azure-SSIS-Integrationslaufzeit":::

- Die Adressen und das virtuelle Netzwerk müssen sich im selben Abonnement und in derselben Region befinden.

## <a name="set-up-the-dns-server"></a><a name="dns_server"></a> Einrichten des DNS-Servers 
Wenn Sie Ihren eigenen DNS-Server in einem virtuellen Netzwerk verwenden müssen, das mit Ihrer Azure-SSIS IR verknüpft ist, stellen Sie zum Auflösen Ihres privaten Hostnamens sicher, dass auch globale Azure-Hostnamen aufgelöst werden können (z. B. der Azure Storage-Blobname `<your storage account>.blob.core.windows.net`). 

Nachstehend finden Sie eine empfohlene Vorgehensweise: 

-   Konfigurieren Sie den benutzerdefinierten DNS für die Weiterleitung von Anforderungen an Azure DNS. Sie können nicht aufgelöste DNS-Einträge an die IP-Adresse der rekursiven Azure-Resolver (168.63.129.16) auf Ihrem eigenen DNS-Server weiterleiten. 

Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Verwenden Sie einen vollqualifizierten Domänennamen für Ihren privaten Hostnamen (verwenden Sie z. B. `<your_private_server>.contoso.com` anstelle von `<your_private_server>`). Alternativ können Sie ein benutzerdefiniertes Standardsetup auf Ihrer Azure-SSIS IR verwenden, um automatisch ein eigenes DNS-Suffix (z. B. `contoso.com`) an alle nicht qualifizierten einteiligen Domänennamen anzufügen und sie in vollqualifizierte Domänennamen umzuwandeln, bevor diese in DNS-Abfragen verwendet werden. Weitere Informationen finden Sie unter [Beispiele für benutzerdefinierte Standardsetups](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

## <a name="set-up-an-nsg"></a><a name="nsg"></a> Einrichten einer NSG
Wenn Sie eine NSG für das Subnetz implementieren müssen, das von der Azure-SSIS IR verwendet wird, lassen Sie eingehenden und ausgehenden Datenverkehr über die folgenden Ports zu: 

-   **Eingehende Anforderung von Azure-SSIS IR**

| Direction | Transportprotokoll | `Source` | Quellportbereich | Destination | Destination port range | Kommentare |
|---|---|---|---|---|---|---|
| Eingehend | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (wenn Sie die IR mit einem virtuellen Resource Manager-Netzwerk verknüpfen) <br/><br/>10100, 20100, 30100 (wenn Sie die IR mit einem klassischen virtuellen Netzwerk verknüpfen)| Der Data Factory-Dienst nutzt diese Ports für die Kommunikation mit den Knoten der Azure-SSIS IR im virtuellen Netzwerk. <br/><br/> Unabhängig davon, ob Sie eine NSG auf Subnetzebene erstellen, konfiguriert Data Factory immer eine NSG auf der Ebene der Netzwerkschnittstellenkarten (NICs), die an die virtuellen Computer angefügt sind, auf denen die Azure-SSIS IR gehostet wird. Nur eingehenden Datenverkehr von Data Factory-IP-Adressen für die angegebenen Ports wird durch diese NSG auf NIC-Ebene zugelassen. Auch wenn Sie diese Ports für den Internetdatenverkehr auf Subnetzebene öffnen, wird Datenverkehr von anderen IP-Adressen als Data Factory-IP-Adressen auf NIC-Ebene blockiert. |
| Eingehend | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Optional) Diese Regel ist nur erforderlich, wenn Microsoft-Supporter Kunden zum Öffnen für erweiterte Problembehandlung auffordern. Direkt nach Abschluss der Problembehandlung kann sie wieder geschlossen werden. Das Diensttag **CorpNetSaw** gestattet nur sicheren Zugriff auf Arbeitsstationen im Microsoft-Unternehmensnetzwerk, um den Remotedesktop verwenden zu können. Dieses Diensttag kann im Portal nicht ausgewählt werden und steht nur über Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI) zur Verfügung. <br/><br/> Auf NIC-Ebene NSG ist Port 3389 standardmäßig geöffnet, und wir ermöglichen es Ihnen, Port 3389 auf Subnetzebene-NSG zu steuern. In der Zwischenzeit hat Azure-SSIS IR Port 3389 ausgehend von der Windows-Firewallregel auf jedem IR-Knoten für Schutzzwecke standardmäßig nicht zugelassen. |
||||||||

-   **Ausgehende Anforderung von Azure-SSIS IR**

| Direction | Transportprotokoll | `Source` | Quellportbereich | Destination | Destination port range | Kommentare |
|---|---|---|---|---|---|---|
| Ausgehend | TCP | VirtualNetwork | * | AzureCloud | 443 | Die Knoten Ihrer Azure-SSIS IR im virtuellen Netzwerk verwenden diesen Port für den Zugriff auf Azure-Dienste wie Azure Storage und Azure Event Hubs. |
| Ausgehend | TCP | VirtualNetwork | * | Internet | 80 | (Optional) Die Knoten Ihrer Azure-SSIS IR im virtuellen Netzwerk verwenden diesen Port zum Herunterladen einer Zertifikatssperrliste aus dem Internet. Wenn Sie diesen Datenverkehr blockieren, kann es beim Starten der IR zu einer Leistungsherabstufung kommen und die Möglichkeit zum Überprüfen der Zertifikatssperrliste im Hinblick auf die Zertifikatverwendung verloren gehen. Wenn Sie das Ziel auf bestimmte FQDNs weiter eingrenzen möchten, finden Sie entsprechende Informationen im Abschnitt **Verwenden von Azure ExpressRoute oder UDR**.|
| Ausgehend | TCP | VirtualNetwork | * | Sql | 1433, 11000–11999 | (Optional) Diese Regel ist nur erforderlich, wenn die Knoten Ihres Azure-SSIS IR im virtuellen Netzwerk auf eine von Ihrem Server gehostete SSISDB zugreifen. Wenn die Verbindungsrichtlinie Ihres Servers anstatt auf **Redirect** auf **Proxy** festgelegt ist, wird nur Port 1433 benötigt. <br/><br/> Diese ausgehende Sicherheitsregel ist auf eine SSISDB nicht anwendbar, die von Ihrer SQL Managed Instance im virtuellen Netzwerk oder auf der Azure-Datenbank gehostet wird, die bzw. der mit einem privaten Endpunkt konfiguriert wurde. |
| Ausgehend | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000–11999 | (Optional) Diese Regel ist nur erforderlich, wenn die Knoten Ihres Azure-SSIS IR im virtuellen Netzwerk auf eine SSISDB zugreifen, die von Ihrer SQL Managed Instance im virtuellen Netzwerk oder auf der SQL-Datenbank gehostet wird, die mit einem privaten Endpunkt konfiguriert wurde. Wenn die Verbindungsrichtlinie Ihres Servers anstatt auf **Redirect** auf **Proxy** festgelegt ist, wird nur Port 1433 benötigt. |
| Ausgehend | TCP | VirtualNetwork | * | Storage | 445 | (Optional) Diese Regel ist nur erforderlich, wenn Sie das in Azure Files gespeicherte SSIS-Paket ausführen möchten. |
||||||||

## <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Verwenden von Azure ExpressRoute oder UDR
Wenn Sie den ausgehenden Datenverkehr von Azure-SSIS IR überprüfen möchten, können Sie den von Azure-SSIS IR initiierten Datenverkehr über [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Tunnelerzwingung (mit der Ankündigung einer BGP-Route, 0.0.0.0/0, an das virtuelle Netzwerk) an die lokale Firewallappliance oder das virtuelle Netzwerkgerät (Network Virtual Appliance, NVA) als Firewall oder [Azure Firewall](../firewall/index.yml) über [UDRs](../virtual-network/virtual-networks-udr-overview.md) weiterleiten. 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="NVA-Szenario für Azure-SSIS IR":::

Sie müssen die folgenden Schritte ausführen, damit das gesamte Szenario funktioniert.
   -   Eingehender Datenverkehr zwischen Azure Batch-Verwaltungsdiensten und der Azure-SSIS IR kann nicht über die Firewallappliance weitergeleitet werden.
   -   Die Firewallappliance muss ausgehenden Datenverkehr zulassen, der von Azure-SSIS IR benötigt wird.

Eingehender Datenverkehr zwischen Azure Batch-Verwaltungsdiensten und der Azure-SSIS IR kann nicht an die Firewallappliance weitergeleitet werden.´, weil andernfalls der Datenverkehr aufgrund eines asymmetrischen Routingproblems unterbrochen wird. Routen müssen für eingehenden Datenverkehr definiert werden, damit der Datenverkehr auf dieselbe Weise wie beim Eintreffen antworten kann. Sie können bestimmte UDRs definieren, um Datenverkehr zwischen Azure Batch-Verwaltungsdiensten und der Azure-SSIS IR mit **Internet** als Typ des nächsten Hops weiterzuleiten.

Wenn sich Ihre Azure-SSIS IR z. B. unter `UK South` befindet und Sie den ausgehenden Datenverkehr über Azure Firewall überprüfen möchten, rufen Sie zunächst eine IP-Bereichsliste des Diensttags`BatchNodeManagement.UKSouth` über den [Downloadlink für den IP-Bereich des Diensttags](https://www.microsoft.com/download/details.aspx?id=56519) oder über die [Diensttagermittlungs-API](../virtual-network/service-tags-overview.md#service-tags-on-premises) ab. Wenden Sie dann die folgenden UDRs von verknüpften IP-Bereichs Routen mit dem Typ des nächsten Hops als **Internet** zusammen mit der Route „0.0.0.0/0“ mit dem Typ **virtuelles Gerät** an.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Azure Batch-UDR-Einstellungen":::

> [!NOTE]
> Bei diesem Ansatz fallen zusätzliche Wartungskosten an. Damit die Azure-SSIS IR funktionsfähig bleibt, sollten Sie regelmäßig den IP-Adressbereich überprüfen und der UDR neue IP-Adressbereiche hinzufügen. Es wird empfohlen, den IP-Adressbereich monatlich zu überprüfen. Nachdem die neue IP-Adresse im Diensttag angezeigt wurde, dauert es nämlich einen weiteren Monat, bis sie wirksam wird. 

Um die Einrichtung von UDR-Regeln zu vereinfachen, können Sie das folgende PowerShell-Skript ausführen, um UDR-Regeln für Azure Batch Management Services hinzuzufügen:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Damit die Firewallappliance ausgehenden Datenverkehr zulässt, müssen Sie für die unten aufgeführten Ports „ausgehend“ zulassen, die den Anforderungen in NSG-Ausgangsregeln entsprechen.
-   Port 443 mit dem Ziel Azure-Clouddienste.

    Wenn Sie Azure Firewall verwenden, können Sie die Netzwerkregel mit dem Diensttag „AzureCloud“ angeben. Bei einer Firewall der anderen Typen können Sie das Ziel entweder einfach als „alle“ für Port 443 zulassen oder die folgenden FQDNs basierend auf dem Typ Ihrer Azure-Umgebung zulassen:

    | Azure-Umgebung | Endpunkte                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure – Öffentlich      | <ul><li><b>Azure Data Factory (Verwaltung)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (Verwaltung)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure Container Registry (Benutzerdefiniertes Setup)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>Event Hub (Protokollierung)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Microsoft-Protokollierungsdienst (Interne Verwendung)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (Verwaltung)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (Verwaltung)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (Benutzerdefiniertes Setup)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>Event Hub (Protokollierung)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft-Protokollierungsdienst (Interne Verwendung)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure Data Factory (Verwaltung)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (Verwaltung)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (Benutzerdefiniertes Setup)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>Event Hub (Protokollierung)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft-Protokollierungsdienst (Interne Verwendung)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Wie bei den FQDNs von Azure Storage, Azure Container Registry und Event Hub können Sie wahlweise auch die folgenden Dienstendpunkte für Ihr virtuelles Netzwerk aktivieren, sodass der Netzwerkdatenverkehr zu diesen Endpunkten das Azure-Backbone-Netzwerk durchläuft, statt an Ihre Firewallappliance weitergeleitet zu werden:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Port 80 mit dem Ziel CRL-Downloadwebsites.

    Sie sollten folgende FQDNs zulassen, die als CRL-Downloadwebsites (Certificate Revocation List, Zertifikatssperrliste) von Zertifikaten für Azure-SSIS IR-Verwaltungszwecke verwendet werden:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Wenn Sie Zertifikate mit einer anderen CRL verwenden, sollten Sie sie ebenfalls einbeziehen. Weitere Informationen zur [Zertifikatssperrliste](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx) finden Sie hier.

    Wenn Sie diesen Datenverkehr nicht zulassen, kann beim Starten der Azure-SSIS IR eine Leistungsminderung auftreten und es Ihnen nicht mehr möglich sein, die Zertifikatsperrliste auf die Zertifikatsverwendung zu überprüfen. Davon wird aus Sicherheitsgründen abgeraten.

-   Port 1433, 11000–11999 mit dem Ziel Azure SQL-Datenbank (nur erforderlich, wenn die Knoten Ihres Azure-SSIS IR im virtuellen Netzwerk auf eine von Ihrem Server gehostete SSISDB zugreifen).

    Wenn Sie Azure Firewall verwenden, können Sie eine Netzwerkregel mit dem Azure SQL-Diensttag angeben. Andernfalls könnten Sie das Ziel als spezifische Azure SQL-URL in der Firewall-Appliance zulassen.

-   Port 445 mit dem Ziel Azure Storage (nur erforderlich, wenn Sie das in Azure Files gespeicherte SSIS-Paket ausführen).

    Wenn Sie Azure Firewall verwenden, können Sie eine Netzwerkregel mit dem Speicherdiensttag angeben. Andernfalls könnten Sie das Ziel als spezifische Azure Dateifreigabe-URL in der Firewallappliance zulassen.

> [!NOTE]
> Wenn Sie bei Azure SQL und Azure Storage VNET-Dienstendpunkte in Ihrem Subnetz konfigurieren, wird der Datenverkehr zwischen Azure-SSIS IR und Azure SQL in derselben Region und Azure Storage in derselben Region oder einem Regionspaar direkt an das Microsoft Azure-Backbonenetzwerk (statt an Ihre Firewallappliance) weitergeleitet.

Wenn Sie den ausgehenden Datenverkehr von Azure-SSIS IR nicht überprüfen müssen, können Sie einfach die Route anwenden und so erzwingen, dass der gesamte Datenverkehr an den Typ des nächsten Hops, **Internet**, geleitet wird:

-   In einem Azure ExpressRoute-Szenario können Sie in dem Subnetz, in dem die Azure-SSIS IR gehostet wird, eine 0.0.0.0/0-Route anwenden, bei der der Typ des nächsten Hops **Internet** lautet. 
-   In einem NVA-Szenario können Sie die vorhandene 0.0.0.0/0-Route, die auf das die Azure-SSIS IR hostende Subnetz angewendet wird, vom Typ des nächsten Hops **virtuelle Appliance** in **Internet** ändern.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="Hinzufügen einer Route":::

> [!NOTE]
> Die Angabe einer Route mit dem Typ des nächsten Hobs **Internet** bedeutet, dass der gesamte Datenverkehr über das Internet gesendet wird. Solange die Zieladresse für einen der Azure-Dienste gilt, leitet Azure den Datenverkehr über das Azure-Backbonenetzwerk direkt an den Dienst weiter (und nicht ins Internet).

## <a name="set-up-the-resource-group"></a><a name="resource-group"></a> Einrichten der Ressourcengruppe

Die Azure SSIS-IR muss bestimmte Netzwerkressourcen unter der gleichen Ressourcengruppe erstellen wie das virtuelle Netzwerk. Diese Ressourcen umfassen Folgendes:
- Ein Azure-Lastenausgleich mit dem Namen *\<Guid>-azurebatch-cloudserviceloadbalancer*.
- Eine öffentliche Azure-IP-Adresse mit dem Namen *\<Guid>-azurebatch-cloudservicepublicip*.
- Eine Netzwerksicherheitsgruppe mit dem Namen *\<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Sie können jetzt Ihre eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR verwenden. In diesem Szenario erstellen wir nur den Azure-Lastenausgleich und die Netzwerksicherheitsgruppe in derselben Ressourcengruppe wie Ihre statischen öffentlichen IP-Adressen und nicht im virtuellen Netzwerk.

Die betreffenden Ressourcen werden beim Start der Azure-SSIS IR erstellt. Sie werden gelöscht, wenn die Azure-SSIS IR beendet wird. Wenn Sie Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwenden, werden diese beim Beenden der Azure-SSIS IR nicht gelöscht. Damit Ihre Azure-SSIS IR ordnungsgemäß beendet werden kann, sollten Sie diese Netzwerkressourcen nicht in anderen Ressourcen wiederverwenden.

Stellen Sie sicher, dass es keine Ressourcensperre für die Ressourcengruppe oder das Abonnement gibt, zu der bzw. dem das virtuelle Netzwerk gehört bzw. die öffentlichen IP-Adressen gehören. Wenn Sie eine Schreibschutzsperre oder eine Löschsperre konfigurieren, kann beim Starten und Beenden Ihrer Azure-SSIS IR ein Fehler auftreten oder die IR nicht mehr reagieren.

Stellen Sie sicher, dass Sie keine Azure Policy-Zuweisung haben, die verhindert, dass die folgenden Ressourcen unter der Ressourcengruppe oder dem Abonnement erstellt werden, zu der bzw. dem das virtuelle Netzwerk gehört bzw. die öffentlichen IP-Adressen gehören: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

Stellen Sie sicher, dass das Ressourcenkontingent Ihres Abonnements für die vorstehenden drei Netzwerkressourcen ausreicht. Insbesondere müssen Sie bei jeder im virtuellen Netzwerk erstellten Azure-SSIS IR zwei kostenlose Kontingente für jede der vorstehenden drei Netzwerkressourcen reservieren. Das eine zusätzliche Kontingent wird bei der regelmäßigen Aktualisierung Ihrer Azure-SSIS IR verwendet.

## <a name="faq"></a><a name="faq"></a> Häufig gestellte Fragen (FAQ)

- Wie kann ich die öffentliche IP-Adresse schützen, die in meiner Azure-SSIS IR für eingehende Verbindungen verfügbar gemacht wird? Ist es möglich, die öffentliche IP-Adresse zu entfernen?
 
  Derzeit wird automatisch eine öffentliche IP-Adresse erstellt, wenn Ihre Azure-SSIS IR mit einem virtuellen Netzwerk verknüpft wird. Wir verfügen über eine NSG auf NIC-Ebene, die ausschließlich eingehende Verbindungen von Azure Batch-Verwaltungsdiensten mit Ihrer Azure-SSIS IR zulässt. Sie können auch eine NSG auf Subnetzebene festlegen, um eingehende Verbindungen zu schützen.

  Wenn eine öffentliche IP-Adresse nicht verfügbar gemacht werden soll, können Sie eine [selbstgehostete IR als Proxy für die Azure-SSIS IR konfigurieren](./self-hosted-integration-runtime-proxy-ssis.md) anstatt die Azure-SSIS IR zu einem virtuellen Netzwerk hinzuzufügen, wenn dies auf Ihr Szenario zutrifft.
 
- Kann ich die öffentliche IP-Adresse meines Azure-SSIS IR in die Positivliste der Firewall für meine Datenquellen aufnehmen?

  Sie können jetzt Ihre eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR verwenden. In diesem Fall können Sie Ihre IP-Adressen in die Firewall-Zulassungsliste für Ihre Datenquellen aufnehmen. Sie können auch andere, unten aufgeführte Optionen in Erwägung ziehen, um den Datenzugriff ausgehend von Ihrer Azure-SSIS IR abhängig vom jeweiligen Szenario zu sichern:

  - Wenn Ihre Datenquelle lokal ist, können Sie, nachdem Sie ein virtuelles Netzwerk mit Ihrem lokalen Netzwerk verbunden haben und Ihre Azure-SSIS IR mit dem Subnetz des virtuellen Netzwerks verknüpft haben, den privaten IP-Adressbereich dieses Subnetzes zur Positivliste der Firewall für Ihre Datenquelle hinzufügen.
  - Wenn es sich bei der Datenquelle um einen Azure-Dienst handelt, der VNET-Dienstendpunkte unterstützt, können Sie in Ihrem virtuellen Netzwerk einen VNET-Dienstendpunkt konfigurieren und Ihre Azure-SSIS IR mit dem betreffenden Subnetz verknüpfen. Anschließend können Sie der Firewall für die Datenquelle eine VNET-Regel für dieses Subnetz hinzufügen.
  - Wenn Ihre Datenquelle kein Azure-Clouddienst ist, können Sie eine UDR verwenden, um von Ihrer Azure-SSIS IR ausgehenden Datenverkehr über eine statische öffentliche IP-Adresse an eine NVA/Azure Firewall zu routen. Anschließend können Sie die statische öffentliche IP-Adresse Ihrer NVA/Azure Firewall zur Positivliste der Firewall für Ihre Datenquelle hinzufügen.
  - Wenn keine der oben genannten Optionen Ihre Anforderungen erfüllt, können Sie die [Konfiguration einer selbstgehosteten IR als Proxy für Ihre Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md) in Erwägung ziehen. Anschließend können Sie die statische öffentliche IP-Adresse des Hostcomputers für die selbstgehostete IR zur Positivliste der Firewall für Ihre Datenquelle hinzufügen.

- Warum muss ich zwei statische öffentliche Adressen angeben, wenn ich meine eigenen Adressen für die Azure-SSIS IR verwenden möchte?

  Die Azure-SSIS IR wird regelmäßig automatisch aktualisiert. Beim Upgrade werden neue Knoten erstellt und alte werden gelöscht. Um Ausfallzeiten zu vermeiden, werden die alten Knoten jedoch erst dann gelöscht, wenn die neuen einsatzbereit sind. Daher kann Ihre erste statische öffentliche IP-Adresse, die von den alten Knoten verwendet wird, nicht sofort freigegeben werden, sodass eine zweite statische öffentliche IP-Adresse zum Erstellen der neuen Knoten benötigt wird.

- Ich verwende meine eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR. Warum kann die IR immer noch nicht auf meine Datenquellen zugreifen?

  - Vergewissern Sie sich, dass die beiden statischen öffentlichen IP-Adressen zur Positivliste der Firewall für Ihre Datenquellen hinzugefügt wurden. Bei jedem Upgrade der Azure-SSIS IR erfolgt ein Wechsel der statischen öffentlichen IP-Adresse zwischen den beiden eigenen Adressen. Wenn Sie nur auch eine Adresse zur Positivliste hinzufügen, ist nach dem Upgrade der Datenzugriff für Ihre Azure-SSIS IR unterbrochen.
  - Wenn es sich bei Ihrer Datenquelle um einen Azure-Dienst handelt, überprüfen Sie, ob sie mit VNET-Dienstendpunkten konfiguriert wurde. Wenn dies der Fall ist, werden für den Datenverkehr von der Azure-SSIS IR zur Datenquelle die privaten IP-Adressen verwendet, die von Azure-Diensten verwaltet werden, sodass das Hinzufügen Ihrer eigenen statischen öffentlichen IP-Adressen zur Positivliste der Firewall für Ihre Datenquelle unwirksam ist.

## <a name="next-steps"></a>Nächste Schritte
- [Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk – Übersicht](join-azure-ssis-integration-runtime-virtual-network.md)
- [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk über die Azure Data Factory Studio-Benutzeroberfläche](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Verknüpfen Sie eine Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk mithilfe von Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Dabei wird Azure SQL-Datenbank zum Hosten des SSIS-Katalogs verwendet. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Sie erfahren, wie Sie Azure SQL-Datenbank mit VNET-Dienstendpunkten oder mit einer SQL Managed Instance in einem virtuellen Netzwerk verwenden, um den SSIS-Katalog zu hosten. Außerdem wird veranschaulicht, wie Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen. Darüber hinaus enthält er Statusbeschreibungen für die zurückgegebenen Informationen. 
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Außerdem wird gezeigt, wie Sie Ihre Azure SSIS-IR aufskalieren, indem Sie weitere Knoten hinzufügen.