---
title: Aktualisieren des Linux-Betriebssystems für Azure Service Fabric
description: Erfahren Sie mehr über Optionen zum Migrieren Ihres Azure Service Fabric-Clusters zu einem anderen Linux-Betriebssystem.
manager: tassb
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 4a5b1a97fde977fa4ba64a4a23d0c57725595c37
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700229"
---
# <a name="upgrading-linux-os-for-azure-service-fabric"></a>Aktualisieren des Linux-Betriebssystems für Azure Service Fabric

In diesem Dokument wird die Anleitung zum Migrieren Ihres Azure Service Fabric für Linux-Clusters von Ubuntu Version 16.04 LTS zu 18.04 LTS beschrieben. Jede Betriebssystemversion erfordert ein eigenes SF-Runtimepaket, das die in diesem Dokument beschriebenen Schritte erfordert, um eine reibungslose Migration zu ermöglichen.

## <a name="overview"></a>Übersicht

Die allgemeine Vorgehensweise sieht wie folgt aus:

1. Stellen Sie die ARM-Ressource (Azure Resource Manager) „vmImage“ des Service Fabric-Clusters auf „Ubuntu18_04“ um, um zukünftige Codeupgrades für diese Betriebssystemversion zu erhalten. Dieser temporäre Betriebssystemkonflikt mit vorhandenen Knotentypen blockiert Rollouts automatischer Codeupgrades, um einen sicheren Rollover sicherzustellen.

    * Vermeiden Sie das Ausführen manueller SF-Clustercodeupgrades während der Betriebssystemmigration. Dies kann dazu führen, dass die Knoten mit alten Knotentypen in einen Zustand gelangen, der einen Benutzereingriff erfordert.

2. Erstellen Sie für jeden Knotentyp im Cluster einen weiteren Knotentyp für das Ubuntu 18.04-Betriebssystemimage für die zugrunde liegende VM-Skalierungsgruppe. Jeder neue Knotentyp wird die Rolle seines alten Pendants übernehmen.

    * Ein neuer primärer Knotentyp muss erstellt werden, um den alten Knotentyp zu ersetzen, der wie folgt markiert ist: „isPrimary“: true.
    
    * Für jeden zusätzlichen nicht primären Knotentyp werden diese Knotentypen auf ähnliche Weise gekennzeichnet: „isPrimary“: false.

    * Stellen Sie nach dem Erstellen des neuen Knotentyps des Zielbetriebssystem sicher, dass vorhandene Workloads weiterhin ordnungsgemäß funktionieren. Wenn Probleme festgestellt werden, beheben Sie die Änderungen, die in der App oder in vorinstallierten Computerpaketen erforderlich sind, bevor Sie mit dem Entfernen des alten Knotentyps fortfahren.
3. Markieren Sie den alten primären Knotentyp wie folgt: „isPrimary“: false. Dies führt zu einer Reihe von Upgrades mit langer Ausführungszeit, um für alle Seedknoten den Übergang durchzuführen.
4. (NUR für Dauerhaftigkeitsknoten vom Typ „Bronze“): Stellen Sie die Verbindung mit dem Cluster über [sfctl](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl) / [PowerShell](https://docs.microsoft.com/powershell/module/ServiceFabric/?view=azureservicefabricps) / [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient?view=azure-dotnet) her, und deaktivieren Sie alle Knoten des alten Knotentyps.
5. Entfernen Sie die alten Knotentypen.

> [!NOTE]
> Az PowerShell generiert einen neuen DNS-Namen für den hinzugefügten Knotentyp, sodass externer Datenverkehr an diesen Endpunkt umgeleitet werden muss.


## <a name="ease-of-use-steps-for-non-production-clusters"></a>Einfache Verwendungsschritte für Nicht-Produktionscluster

> [!NOTE]
> Die folgenden Schritte veranschaulichen, wie Sie schnell einen Prototyp der Knotentypmigration über Az PowerShell-Cmdlets in einem Nur-TEST-Cluster erstellen. Für Produktionscluster mit echtem Geschäftsdatenverkehr werden die gleichen Schritte erwartet, indem ARM-Upgrades durchgeführt werden, um die Wiederholbarkeit und eine konsistente deklarative Quelle der Wahrheit zu erhalten.

1. Aktualisieren Sie die vmImage-Einstellung auf der Service Fabric-Clusterressource mit [Update-AzServiceFabricVmImage](https://docs.microsoft.com/powershell/module/az.servicefabric/update-azservicefabricvmimage):

    [Azure PowerShell:](https://docs.microsoft.com/powershell/azure/install-az-ps)
    ```powershell
    # Replace subscriptionId, resourceGroup, clusterName with ones corresponding to your cluster.
    $subscriptionId="cea219db-0593-4b27-8bfa-a703332bf433"
    Login-AzAccount; Select-AzSubscription -SubscriptionId $subscriptionId

    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    # Update cluster vmImage to target OS. This registers the SF runtime package type that is supplied for upgrades.
    Update-AzServiceFabricVmImage -ResourceGroupName $resourceGroup -ClusterName $clusterName -VmImage Ubuntu18_04
    ```

2. Fügen Sie für jeden vorhandenen Knotentyp eine neue Knotentypentsprechung hinzu:

    ```powershell
    $nodeTypeName="nt1u18"
    # You can customize this to fetch a password from a secure store.
    $securePassword = ConvertTo-SecureString -String 'Yourpassword123!@#' -AsPlainText -Force

    # Ensure last upgrade is done - Ready means the next command can be issued.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Add new primary node type. Omit the IsPrimaryNodeType parameter for non-primary node types.
    Add-AzServiceFabricNodeType -ResourceGroupName $resourceGroup  -ClusterName $clusterName -NodeType $nodeTypeName -Capacity 5 -VmUserName testuser -VmPassword $securePassword -DurabilityLevel Silver -Verbose -VMImageSku 18.04-LTS -IsPrimaryNodeType $true

    # Redirect traffic to new node type dns
    # dns-Contoso01SFCluster-nt1u18.westus2.cloudapp.azure.com
    ```

3. Aktualisieren Sie den alten primären Knotentyp auf einen nicht primären Knoten, um ein Rollover von Seedknoten und Systemdiensten auf den neuen Knotentyp durchzuführen:

    ```powershell
    # Query to ensure background upgrades are done.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Update old nodetype to isPrimary: false
    $oldNodeTypeName="nt1"
    Update-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -IsPrimaryNodeType $false -NodeType $oldNodeTypeName -Verbose

    # Ensure node type is showing isPrimary: False before proceeding.
    Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup
    ```

    Beispielausgabe:
    ```
    NodeTypes :
              NodeTypeDescription :
                  Name : nt1
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Bronze
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : False
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
              NodeTypeDescription :
                  Name : nt1u18
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Silver
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : True
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
    ```

4. Um Dauerhaftigkeitsknoten vom Typ „Bronze“ zu entfernen, deaktivieren Sie zuerst die Knoten, bevor Sie mit dem Entfernen des alten Knotentyps fortfahren. Stellen Sie eine Verbindung mit einem Clusterknoten über *SSH* her, und führen Sie die folgenden Befehle aus:

    ```bash
    # as root user:

    # install jq tool to automatically parse JSON responses
    apt-get install jq -fy

    # retrieve the thumbprint to be used for establishing a fabric client
    dataroot=$(cat /etc/servicefabric/FabricDataRoot)
    nodename=_nt1_0
    cat $dataroot/$nodename/Fabric/ClusterManifest.current.xml | grep ClientCertThumbprints
    # 0777FE1A43E306F332D96DA339EF6834D0E4A453

    # verify node count
    sfctl cluster select --endpoint https://Contoso01SFCluster.westus2.cloudapp.azure.com:19080 --pem /var/lib/waagent/0777FE1A43E306F332D96DA339EF6834D0E4A453.pem --no-verify

    # sample command to list all nodes
    sfctl node list

    # for each node part of the node type to be removed, disable the node:
    nodeTypeBeingDisabled=nt1
    nodes=$(sfctl node list | jq --arg nodeTypeBeingDisabled "$nodeTypeBeingDisabled" '.items[] | select(.type==$nodeTypeBeingDisabled) | .name' | sed s/\"//g)
    echo $nodes
    for n in $nodes; do echo "Disabling $n"; sfctl node disable --node-name $n --deactivation-intent RemoveNode --timeout 300; done
    ```

5. Entfernen Sie den vorherigen Knotentyp, indem Sie das SF-Attribut für den Knotentyp der Clusterressource entfernen und die zugehörigen VM-Skalierungsgruppen und Netzwerkressourcen außer Betrieb setzen.

    ```powershell
    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    $oldNodeTypeName="nt1"

    # Remove the Service Fabric node type, associated virtual machine scale set resource, and any trailing networking resources that are no longer used. 
    Remove-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -NodeType $oldNodeTypeName
    ```

    > [!NOTE]
    > In einigen Fällen kann dies zu dem folgenden Fehler führen. In diesem Fall können Sie über Service Fabric Explorer (SFX) feststellen, dass sich der InfrastructureService für den entfernten Knotentyp in einem Fehlerzustand befindet. Versuchen Sie, das Problem zu beheben, indem Sie das Entfernen wiederholen.
    ```
    Remove-AzServiceFabricNodeType : Code: ClusterUpgradeFailed, Message: Long running operation failed with status 'Failed'
    ```

Sobald bestätigt wurde, dass die Workloads erfolgreich auf die neuen Knotentypen migriert wurden und die alten Knotentypen gelöscht wurden, kann der Cluster mit den nachfolgenden Upgrades der Service Fabric-Runtimecodeversion und -Konfiguration fortfahren.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Service Fabric-Upgrades](service-fabric-cluster-upgrade-version-azure.md)
* Anpassen Ihrer [Service Fabric-Clustereinstellungen](service-fabric-cluster-fabric-settings.md)
* Erfahren Sie mehr über die [Dauerhaftigkeitsmerkmale](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) eines Clusters.
* Erfahren Sie mehr über [Knotentypen und VM-Skalierungsgruppen](service-fabric-cluster-nodetypes.md).
* Erfahren Sie mehr über [Service Fabric-Clusterskalierung](service-fabric-cluster-scaling.md).
* [Ab- und Aufskalieren Ihres Clusters](service-fabric-cluster-scale-in-out.md)
* [Entfernen eines Knotentyps in Azure Service Fabric](service-fabric-how-to-remove-node-type.md)

