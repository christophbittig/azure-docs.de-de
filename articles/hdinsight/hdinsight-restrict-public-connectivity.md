---
title: Einschränken der öffentlichen Konnektivität in Azure HDInsight
description: Erfahren Sie, wie Sie den Zugriff auf alle ausgehenden öffentlichen IP-Adressen entfernen.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 457e72f50b24b5850311b2869ab6daa956512ae6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215445"
---
# <a name="restrict-public-connectivity-in-azure-hdinsight"></a>Einschränken der öffentlichen Konnektivität in Azure HDInsight

In der [Standardarchitektur für virtuelle Netzwerke](./hdinsight-virtual-network-architecture.md) von Azure HDInsight kommuniziert der HDInsight-Ressourcenanbieter über ein öffentliches Netzwerk mit dem Cluster. In diesem Artikel erfahren Sie mehr über die erweiterten Steuerelemente, mit denen Sie einen eingeschränkten HDInsight-Cluster erstellen können, dessen eingehende Konnektivität auf ein privates Netzwerk beschränkt ist. 

Falls öffentliche Konnektivität zwischen Ihren HDInsight-Clustern und abhängigen Ressourcen erwünscht ist, sollten Sie die Konnektivität des Clusters einschränken, indem Sie die Richtlinien zum [Steuern des Netzwerkdatenverkehrs in Azure HDInsight](./control-network-traffic.md) befolgen. Zusätzlich zum Einschränken der öffentlichen Konnektivität können Sie Azure Private Link-fähige Abhängigkeitsressourcen für die Verwendung mit HDInsight-Clustern konfigurieren.

Im folgenden Diagramm ist dargestellt, wie eine potenzielle HDInsight-Architektur für virtuelle Netzwerke aussehen kann, wenn `resourceProviderConnection` auf *Outbound* (Ausgehend) festgelegt ist:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagramm: HDInsight-Architektur mit einer ausgehenden Ressourcenanbieterverbindung":::

> [!NOTE]
> Das Einschränken der öffentlichen Konnektivität ist nicht das Gleiche wie Private Link, sondern eine Voraussetzung für dessen Aktivierung.

## <a name="initialize-a-restricted-cluster"></a>Initialisieren eines eingeschränkten Clusters

Standardmäßig nutzt der HDInsight-Ressourcenanbieter eine *eingehende* Verbindung mit dem Cluster über öffentliche IP-Adressen. Wenn die Netzwerkeigenschaft `resourceProviderConnection` auf *Outbound* (Ausgehend) festgelegt ist, werden die Verbindungen mit dem HDInsight-Ressourcenanbieter umgekehrt, damit sie immer im Cluster initiiert werden und zum Ressourcenanbieter hinausgehen. 

In dieser Konfiguration ohne eingehende Verbindung ist es nicht erforderlich, eingehende Diensttags in der Netzwerksicherheitsgruppe zu konfigurieren. Es ist auch nicht erforderlich, die Firewall oder die virtuelle Netzwerkappliance über benutzerdefinierte Routen zu umgehen.

Nachdem Sie Ihren Cluster erstellt haben, sollten Sie eine ordnungsgemäße DNS-Auflösung einrichten, indem Sie DNS-Einträge hinzufügen, die für Ihren eingeschränkten HDInsight-Cluster erforderlich sind. Der folgende DNS-Eintrag vom Typ CNAME (kanonischer Name) wird in der von Azure verwalteten öffentlichen DNS-Zone erstellt: `azurehdinsight.net`.

```dns
<clustername>    CNAME    <clustername>-int
```

Für den Zugriff auf den Cluster mit vollqualifizierten Domänennamen (Fully Qualified Domain Names, FQDNs) können Sie je nach Bedarf eine der folgenden Verfahren verwenden:

- Verwenden Sie die privaten IP-Adressen des internen Lastenausgleichs direkt.
- Verwenden Sie Ihre eigene private DNS-Zone, um die Clusterendpunkte zu überschreiben. In diesem Fall muss der Zonenname `azurehdinsight.net` lauten.

Beispielsweise können Sie für Ihre private DNS-Zone `azurehdinsight.net` nach Bedarf Ihre privaten IP-Adressen hinzufügen:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

> [!NOTE]
> Es wird nicht empfohlen, eingeschränkte Cluster im gleichen virtuellen Netzwerk (mit einer privaten DNS-Zone für `azurehdinsight.net`) wie andere Cluster zu platzieren, bei denen öffentliche Konnektivität aktiviert ist. Dies kann zu unbeabsichtigtem Verhalten oder Konflikten bei der DNS-Auflösung führen.

Um Ihnen die DNS-Einrichtung zu erleichtern, geben wir die FQDNs und die entsprechenden privaten IP-Adressen als Teil der `GET`-Clusterantwort zurück. Zum Anfangen können Sie diesen PowerShell-Codeausschnitt verwenden:

```powershell
<#
    This script is an example to help you get started with automation and can be adjusted based on your needs.
    This script assumes:
    - The HDInsight cluster has already been created, and the context where this script is run has permissions to read/write resources in the same resource group.
    - The private DNS zone resource exists in the same subscription as the HDInsight cluster.
We recommend that you use the latest version of the Az.HDInsight module.

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

# 2. Confirm that the DNS zone exists.
Get-AzPrivateDnsZone -ResourceGroupName $dnsZoneResourceGroupName -Name $dnsZoneName -ErrorAction Stop

# 3. Update DNS entries for the cluster in the private DNS zone:
#    - If the entries already exist, update to the new IP.
#    - If the entries don't exist, create them.
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

## <a name="add-private-link-connectivity-to-cluster-dependent-resources-optional"></a>Hinzufügen von Private Link-Konnektivität zu clusterabhängigen Ressourcen (optional)

Wenn Sie `resourceProviderConnection` auf *Outbound* (Ausgehend) stellen, können sie auch über private Endpunkte auf clusterspezifische Ressourcen zugreifen. Diese Ressourcen umfassen Folgendes:

- Speicher: Azure Data Lake Storage Gen2 und Azure Blob Storage
- SQL-Metastores: Apache Ranger, Ambari, Oozie und Hive
- Azure-Schlüsseltresor 

Es ist nicht zwingend erforderlich, private Endpunkte für diese Ressourcen zu verwenden. Wenn Sie allerdings planen, private Endpunkte für diese Ressourcen zu verwenden, müssen Sie diese Ressourcen erstellen sowie die privaten Endpunkte und DNS-Einträge konfigurieren, bevor Sie den HDInsight-Cluster erstellen. Alle diese Ressourcen sollten von innerhalb des Clustersubnetzes zugänglich sein, entweder über einen privaten Endpunkt oder auf andere Weise.

Stellen Sie beim Herstellen einer Verbindung mit Azure Data Lake Storage Gen2 über einen privaten Endpunkt sicher, dass für das Gen2-Speicherkonto ein Endpunkt für `blob` und `dfs` festgelegt ist. Weitere Informationen finden Sie unter [Erstellen eines privaten Endpunkts](../private-link/create-private-endpoint-portal.md).

## <a name="use-a-firewall-optional"></a>Verwenden einer Firewall (optional)
HDInsight-Cluster können weiterhin eine Verbindung mit dem öffentlichen Internet herstellen, um ausgehende Abhängigkeiten abzurufen. Wenn Sie den Zugriff einschränken möchten, können Sie eine [Firewall konfigurieren](./hdinsight-restrict-outbound-traffic.md), aber das ist keine Erfordernis.

## <a name="create-clusters"></a>Erstellen von Clustern

Der folgende JSON-Codeausschnitt enthält die beiden Netzwerkeigenschaften, die Sie in Ihrer Azure Resource Manager-Vorlage konfigurieren müssen, um einen privaten HDInsight-Cluster zu erstellen:

```json
networkProperties: {
    "resourceProviderConnection": "Outbound"
}
```

Eine vollständige Vorlage mit vielen dieser HDInsight-Sicherheitsfunktionen für Unternehmen, z. B. Private Link, finden Sie im Artikel zur [Enterprise-Sicherheitsvorlage für HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

Informationen zum Erstellen eines Clusters mithilfe von PowerShell finden Sie im [Beispiel](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

Informationen zum Erstellen eines Clusters mithilfe von Azure CLI finden Sie im [Beispiel](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Nächste Schritte

* [Aktivieren von Private Link in Ihrem Cluster](./hdinsight-private-link.md)
* [Enterprise-Sicherheitspaket für Azure HDInsight](enterprise-security-package.md)
* [Allgemeine Informationen und Richtlinien für die Unternehmenssicherheit in Azure HDInsight](./domain-joined/general-guidelines.md)
