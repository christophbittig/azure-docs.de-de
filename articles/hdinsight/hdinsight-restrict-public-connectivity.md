---
title: Einschränken der öffentlichen Konnektivität in Azure HDInsight (Vorschau)
description: Erfahren Sie, wie Sie den Zugriff auf alle ausgehenden öffentlichen IP-Adressen entfernen.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 8d4fc269137b9d11ab0db046288f1d548b911d7a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716408"
---
# <a name="restrict-public-connectivity-in-azure-hdinsight-preview"></a>Einschränken der öffentlichen Konnektivität in Azure HDInsight (Vorschau)

## <a name="overview"></a>Übersicht
In der [Standardarchitektur für virtuelle Netzwerke](./hdinsight-virtual-network-architecture.md) von Azure HDInsight kommuniziert der HDInsight-Ressourcenanbieter über das öffentliche Netzwerk mit dem Cluster. In diesem Artikel erfahren Sie mehr über die erweiterten Steuerelemente, mit denen Sie einen eingeschränkten HDInsight-Cluster erstellen können, bei dem die eingehende Konnektivität auf das private Netzwerk beschränkt ist. Falls Sie öffentliche Konnektivität mit Ihren HDInsight-Clustern und abhängigen Ressourcen zulassen müssen, sollten Sie die Konnektivität des Clusters einschränken, indem Sie die Richtlinien zum [Steuern des Netzwerkdatenverkehrs in Azure HDInsight](./control-network-traffic.md) befolgen. Zusätzlich zur Einschränkung der öffentlichen Konnektivität fügen wir Unterstützung für Private Link-fähige Abhängigkeitsressourcen hinzu, die für die Verwendung mit diesen Clustern konfiguriert werden können.

Im folgenden Diagramm ist dargestellt, wie eine potenzielle HDInsight-Architektur für virtuelle Netzwerke aussehen kann, wenn `resourceProviderConnection` auf *Outbound* (Ausgehend) festgelegt ist:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagramm: HDInsight-Architektur mit einer ausgehenden Ressourcenanbieterverbindung":::

> [!NOTE]
> Das Einschränken der öffentlichen Konnektivität ist eine Voraussetzung für die Aktivierung von Private Link und sollte nicht als dieselbe Funktion betrachtet werden.

## <a name="initialize-a-restricted-cluster"></a>Initialisieren eines eingeschränkten Clusters

Standardmäßig nutzt der HDInsight-Ressourcenanbieter eine *eingehende* Verbindung mit dem Cluster über öffentliche IP-Adressen. Wenn die Netzwerkeigenschaft `resourceProviderConnection` auf *Outbound* (Ausgehend) festgelegt ist, werden die Verbindungen mit dem HDInsight-Ressourcenanbieter umgekehrt, damit sie immer aus dem Cluster zum Ressourcenanbieter initiiert werden. In dieser Konfiguration ist es ohne eingehende Verbindung nicht erforderlich, eingehende Diensttags in der Netzwerksicherheitsgruppe (NSG) zu konfigurieren oder die Firewall bzw. das virtuelle Netzwerkgerät über benutzerdefinierte Routen (User Defined Routes, UDR) zu umgehen.

Nachdem Sie Ihren Cluster erstellt haben, sollten Sie eine ordnungsgemäße DNS-Auflösung einrichten, indem Sie DNS-Einträge hinzufügen, die für Ihren eingeschränkten HDInsight-Cluster erforderlich sind. Der folgende DNS-Eintrag vom Typ CNAME (kanonischer Name) wird in der von Azure verwalteten öffentlichen DNS-Zone erstellt: `azurehdinsight.net`.

```dns
<clustername>    CNAME    <clustername>-int
```

Für den Zugriff auf den Cluster mit Cluster-FQDNs können Sie entweder die privaten IP-Adressen des internen Lastenausgleichsmoduls direkt nutzen, oder Ihre eigene private DNS-Zone verwenden (in diesem Fall muss der Zonenname `azurehdinsight.net` lauten), um die Clusterendpunkte je nach Ihren Anforderungen außer Kraft zu setzen. Für Ihre private DNS-Zone `azurehdinsight.net` können Sie z. B. Ihre privaten IP-Adressen nach Bedarf hinzufügen:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

> [!NOTE]
> Es wird davon abgeraten, eingeschränkte Cluster im selben VNet (mit einer privaten DNS-Zone für `azurehdinsight.net`) wie andere Cluster zu verwenden, bei denen die öffentliche Konnektivität aktiviert ist, da dies zu unbeabsichtigtem DNS-Auflösungsverhalten/Konflikten führen kann.

Um Ihnen die DNS-Einrichtung zu erleichtern, geben wir die FQDNs und die entsprechenden privaten IP-Adressen als Teil der `GET`-Clusterantwort zurück. Für die ersten Schritte können Sie diesen PowerShell-Codeausschnitt verwenden.

```powershell
<#
    This script is offered as an example to help get started with automation and can be adjusted based on your needs.
    This script assumes:
    - HDInsight cluster has already been created and the context where this script is run has permissions to read/write resources in the same resource group.
    - Private DNS zone resource exists in the same subscription as the HDInsight cluster.
We recommend that you use the latest version of Az.HDInsight module

#>
$subscriptionId = "<Replace with subscription for deploying HDInsight clusters, and containing private DNS zone resource>"

$clusterName = "<Replace with cluster name>"
$clusterResourceGroupName = "<Replace with resource group name>"

# For example, azurehdinsight.net for public cloud.
$dnsZoneName = "<Replace with private DNS zone name>"
$dnsZoneResourceGroupName = "<Replace with private DNS zone resource group name>"

Connect-AzAccount -SubscriptionId $subscriptionId

# 1. Get cluster endpoints
$clusterEndpoints = $(Get-AzHDInsightCluster -ClusterName $clusterName ` -ResourceGroupName $clusterResourceGroupName).ConnectivityEndpoints

$endpointMapping = @{}

foreach($endpoint in $clusterEndpoints)
{
    $label = $endpoint.Location.ToLower().Replace(".$dnsZoneName".ToLower(), "")
    $ip = $endpoint.PrivateIPAddress

    $endpointMapping.Add($label, $ip)
}

# 2. Confirm DNS zone exists
Get-AzPrivateDnsZone -ResourceGroupName $dnsZoneResourceGroupName -Name $dnsZoneName -ErrorAction Stop

# 3. Update DNS entries for the cluster in the private DNS zone
#    - If the entries already exist, update to the new IP
#    - If the entries do not exist, create them
$recordSets = Get-AzPrivateDnsRecordSet -ZoneName $dnsZoneName -ResourceGroupName $dnsZoneResourceGroupName -RecordType A

foreach($label in $endpointMapping.Keys)
{
    $updateRecord = $null

    foreach($record in $recordSets)
    {
        if($record.Name -eq $label)
        {
            $updateRecord = $record
            break;
        }
        
    }

    if($null -ne $updateRecord)
    {
        $updateRecord.Records[0].Ipv4Address = $endpointMapping[$label]
        Set-AzPrivateDnsRecordSet -RecordSet $updateRecord | Out-Null
    }
    else
    {
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $dnsZoneResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name $label `
            -RecordType A `
            -Ttl 3600 `
            -PrivateDnsRecord (New-AzPrivateDnsRecordConfig -Ipv4Address $endpointMapping[$label]) | Out-Null
    }
}

```

## <a name="adding-private-link-connectivity-private-endpoints-to-cluster-dependent-resources-optional"></a>Hinzufügen von Private Link-Konnektivität (private Endpunkte) zu clusterabhängigen Ressourcen (optional)

Durch die Konfiguration von `resourceProviderConnection` als *Outbound* (Ausgehend) können Sie außerdem mithilfe privater Endpunkte auf clusterspezifische Ressourcen wie Storage (Azure Data Lake Storage Gen2 und Windows Azure Storage Blob), SQL-Metaspeicher (Apache Ranger, Ambari, Oozie und Hive) und Azure Key Vault zugreifen. Es ist nicht zwingend erforderlich, private Endpunkte für diese Ressourcen zu verwenden, aber wenn Sie planen, private Endpunkte für diese Ressourcen zu verwenden, müssen Sie diese Ressourcen erstellen und die privaten Endpunkte und DNS-Einträge konfigurieren, bevor Sie den HDInsight-Cluster erstellen. Alle diese Ressourcen sollten von innerhalb des Clustersubnetzes zugänglich sein, entweder über einen privaten Endpunkt oder auf andere Weise.
Stellen Sie beim Herstellen einer Verbindung mit Azure Data Lake Storage Gen2 über einen privaten Endpunkt sicher, dass für das Gen2-Speicherkonto ein Endpunkt für „blob“ und „dfs“ festgelegt ist. Weitere Informationen finden Sie unter [Erstellen eines privaten Endpunkts](../private-link/create-private-endpoint-portal.md).

## <a name="using-firewall-optional"></a>Verwenden der Firewall (optional)
HDInsight-Cluster können weiterhin eine Verbindung mit dem öffentlichen Internet herstellen, um ausgehende Abhängigkeiten abzurufen. Wenn Sie den Zugriff noch weiter einschränken möchten, können Sie eine [Firewall konfigurieren](./hdinsight-restrict-outbound-traffic.md), aber das ist keine Voraussetzung.

## <a name="how-to-create-clusters"></a>Wie werden Cluster erstellt?
### <a name="use-arm-template-properties"></a>Verwenden der Eigenschaften von ARM-Vorlagen

Der folgende JSON-Codeausschnitt enthält die beiden Netzwerkeigenschaften, die Sie in Ihrer ARM-Vorlage konfigurieren müssen, um einen privaten HDInsight-Cluster zu erstellen.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

Eine vollständige Vorlage mit vielen dieser HDInsight-Sicherheitsfunktionen für Unternehmen, z. B. Private Link, finden Sie im Artikel zur [Enterprise-Sicherheitsvorlage für HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell

Informationen zur Verwendung von PowerShell finden Sie [hier in dem Beispiel](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

### <a name="use-azure-cli"></a>Mithilfe der Azure-Befehlszeilenschnittstelle
Informationen zur Verwendung der Azure CLI finden Sie [hier](/cli/azure/hdinsight#az_hdinsight_create-examples) in dem Beispiel.

## <a name="next-steps"></a>Nächste Schritte

* [Aktivieren von Private Link in Ihrem Cluster](./hdinsight-private-link.md)
* [Enterprise-Sicherheitspaket für Azure HDInsight](enterprise-security-package.md)
* [Allgemeine Informationen und Richtlinien für die Unternehmenssicherheit in Azure HDInsight](./domain-joined/general-guidelines.md)
