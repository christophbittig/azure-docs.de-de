---
title: 'Konfigurieren von Dienstendpunktrichtlinien: Azure HDInsight'
description: Hier erfahren Sie, wie Sie Dienstendpunktrichtlinien für Ihr virtuelles Netzwerk mit Azure HDInsight konfigurieren.
ms.service: hdinsight
ms.topic: how-to
ms.date: 09/13/2021
ms.openlocfilehash: 500bbdce924313924b8b6f698dff0e17107ebd79
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646566"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Konfigurieren von VNET-Dienstendpunktrichtlinien für Azure HDInsight

In diesem Artikel erfahren Sie, wie Sie Dienstendpunktrichtlinien für virtuelle Netzwerke mit Azure HDInsight implementieren.

## <a name="background"></a>Hintergrund

Azure HDInsight ermöglicht die Erstellung von Clustern in Ihrem eigenen virtuellen Netzwerk. Wenn Sie ausgehenden Datenverkehr aus Ihrem virtuellen Netzwerk an andere Azure-Dienste (beispielsweise Speicherkonten) zulassen müssen, können Sie [Dienstendpunktrichtlinien](../virtual-network/virtual-network-service-endpoint-policies-overview.md) erstellen. Bei Dienstendpunktrichtlinien, die über das Azure-Portal erstellt werden, kann allerdings nur eine Richtlinie für ein einzelnes Konto, für alle Konten in einem Abonnement oder für alle Konten in einer Ressourcengruppe erstellt werden.

Als verwalteter Dienst sammelt Azure HDInsight jedoch Daten und Protokolldateien von jedem Cluster in bestimmten Speicherkonten in der jeweiligen Region. Damit diese Daten aus Ihrem virtuellen Netzwerk HDInsight erreichen, müssen Dienstendpunktrichtlinien erstellt werden, die ausgehenden Datenverkehr an bestimmte, von Azure HDInsight verwaltete Datensammlungspunkte zulassen.

## <a name="service-endpoint-policies-for-hdinsight"></a>Dienstendpunktrichtlinien für HDInsight

Von diesen Dienstendpunktrichtlinien werden folgende Funktionen unterstützt:

- Sammeln von Protokollen und Telemetriedaten im Zusammenhang mit Clustererstellung, Auftragsausführung und Plattformvorgängen (beispielsweise Skalierung)
- Anfügen von virtuellen Festplatten (Virtual Hard Disks, VHDs) an neu erstellte Clusterknoten zur Bereitstellung von Software und Bibliotheken in Ihrem Cluster

Werden keine Dienstendpunktrichtlinien erstellt, um diesen Datenfluss zu ermöglichen, ist die Clustererstellung möglicherweise nicht erfolgreich, und von Azure HDInsight kann keine Unterstützung für Ihre Cluster bereitgestellt werden.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>Erstellen von Dienstendpunktrichtlinien für HDInsight

Vergewissern Sie sich vor der Erstellung neuer Cluster, dass die richtigen Dienstendpunktrichtlinien an Ihr virtuelles Netzwerk angefügt sind. Andernfalls ist die Clustererstellung möglicherweise nicht erfolgreich oder führt zu einem Fehler.

Gehen Sie wie folgt vor, um die erforderlichen Dienstendpunktrichtlinien zu erstellen:

1. Entscheiden Sie sich für eine Region, in der Sie Ihren HDInsight-Cluster erstellen möchten.
1. Suchen Sie in der [Liste der Dienstendpunktrichtlinien-Ressourcen](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json) nach der Region. Diese Liste enthält alle Ressourcengruppen für Speicherkonten zur HDInsight-Verwaltung.
1. Wählen Sie die Ressourcengruppenliste für Ihre Region aus. Hier sehen Sie ein Beispiel mit den Ressourcen für `Canada Central`:

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/hdi31distrorelease",
        "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/bigdatadistro"
    ],
    ```

1. Fügen Sie diese Ressourcengruppenliste in das in der Azure CLI oder in Azure PowerShell geschriebene Setupskript ein:

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    # Be sure to get the most recent list of resource groups from the [list of service endpoint policy resources](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/hdi31distrorelease",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/bigdatadistro")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Falls Sie Ihre Dienstendpunktrichtlinie lieber mithilfe von PowerShell einrichten möchten, verwenden Sie den folgenden Codeausschnitt:
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    # Be sure to get the most recent list of resource groups from the [list of service endpoint policy resources](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/hdi31distrorelease",
    "/subscriptions/fb3429ab-83d0-4bed-95e9-1a8e9455252c/resourceGroups/DistroStorageRG/providers/Microsoft.Storage/storageAccounts/bigdatadistro")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```
> [!IMPORTANT] 
> Es wird empfohlen, dass Sie die aktuelle [Liste der Richtlinienressourcen für Dienstendpunkte](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json) manuell oder per Automatisierung auf einer geplanten Basis erhalten. Dadurch werden CRUD-Probleme verhindert, wenn der JSON-Datei zusätzliche Ressourcengruppen hinzugefügt oder diese aus ihr entfernt werden. 


## <a name="next-steps"></a>Nächste Schritte

* [Virtuelle Netzwerkarchitektur mit Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Konfigurieren eines virtuellen Netzwerkgeräts](./network-virtual-appliance.md)
