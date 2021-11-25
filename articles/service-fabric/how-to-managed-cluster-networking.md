---
title: Konfigurieren von Netzwerkeinstellungen für verwaltete Service Fabric-Cluster
description: Erfahren Sie, wie Sie Ihre verwalteten Service Fabric-Cluster für NSG-Regeln, RDP-Portzugriff, Lastenausgleichsregeln und vieles mehr konfigurieren.
ms.topic: how-to
ms.date: 11/10/2021
ms.openlocfilehash: 2334618f11533d285154082e0d1b5dadbc41368f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289384"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters"></a>Konfigurieren von Netzwerkeinstellungen für verwaltete Service Fabric-Cluster

Verwaltete Service Fabric-Cluster werden mit einer standardmäßigen Netzwerkkonfiguration erstellt. Diese Konfiguration besteht aus einem [Azure Load Balancer](../load-balancer/load-balancer-overview.md) mit einer öffentlichen IP-Adresse, einem VNET mit einem zugeordneten Subnetz und einer NSG, die für wichtige Clusterfunktionen konfiguriert ist. Es werden auch optionale NSG(-Netzwerksicherheitsgruppen)-Regeln angewendet, z. B. das standardmäßige Zulassen des gesamten ausgehenden Datenverkehrs, was die Kundenkonfiguration vereinfachen soll. In diesem Dokument wird erläutert, wie Sie die folgenden Netzwerkkonfigurationsoptionen und vieles mehr ändern:

- [Verwalten von NSG-Regeln](#nsgrules)
- [Verwalten des RDP-Zugriffs](#rdp)
- [Verwalten der Lastenausgleichskonfiguration](#lbconfig)
- [Aktivieren von IPv6](#ipv6)
- [Verwendung eines eigenen virtuellen Netzwerks](#byovnet)
- [Verwendung eines eigenen Lastenausgleichs](#byolb)
- [Aktivieren des beschleunigten Netzwerkbetriebs](#accelnet)
- [Konfigurieren von Hilfssubnetzen](#auxsubnet)


<a id="nsgrules"></a>
## <a name="manage-nsg-rules"></a>Verwalten von NSG-Regeln

### <a name="nsg-rules-guidance"></a>Leitfaden zu NSG-Regeln

Berücksichtigen Sie die folgenden Überlegungen, wenn Sie neue NSG-Regeln für Ihren verwalteten Cluster erstellen.

* Verwaltete Service Fabric-Cluster reservieren den NSG-Regelprioritätsbereich 0 bis 999 für essenzielle Funktionen. Sie können keine benutzerdefinierten NSG-Regeln mit einem Prioritätswert unter 1000 erstellen.
* Verwaltete Service Fabric-Cluster reservieren den NSG-Regelprioritätsbereich 3001 bis 4000 für die Erstellung optionaler NSG-Regeln. Diese Regeln werden automatisch hinzugefügt, um die Konfiguration zu vereinfachen und zu beschleunigen. Sie können diese Regeln außer Kraft setzen, indem Sie benutzerdefinierte Regeln im Bereich 1000 bis 3000 hinzufügen.
* Benutzerdefinierte Regeln müssen eine Priorität im Bereich 1000 bis 3000 aufweisen.

### <a name="apply-nsg-rules"></a>Anwenden von NSG-Regeln
Verwaltete Service Fabric-Cluster ermöglichen Ihnen die Zuweisung von NSG-Regeln direkt in der Clusterressource Ihrer Bereitstellungsvorlage.

Verwenden Sie die Eigenschaft [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) Ihrer *Microsoft.ServiceFabric/managedclusters*-Ressource (Version `2021-05-01` oder höher), um NSG-Regeln zuzuweisen. Beispiel:

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "properties": {
    "networkSecurityRules": [
      {
        "name": "AllowCustomers",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33000-33499",
        "access": "Allow",
        "priority": 2001,
        "direction": "Inbound"
      },
      {
        "name": "AllowARM",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "AzureResourceManager",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33500-33699",
        "access": "Allow",
        "priority": 2002,
        "direction": "Inbound"
      },
      {
        "name": "DenyCustomers",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33700-33799",
        "access": "Deny",
        "priority": 2003,
        "direction": "Outbound"
      },
      {
        "name": "DenyRDP",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "destinationPortRange": "3389",
        "access": "Deny",
        "priority": 2004,
        "direction": "Inbound",
        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
      }
    ],
    "fabricSettings": [
      "..."
    ]
  }
}
```

## <a name="clientconnection-and-httpgatewayconnection-default-and-optional-rules"></a>ClientConnection- und HttpGatewayConnection-Standardregeln und optionale Regeln
### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG-Regel: SFMC_AllowServiceFabricGatewayToSFRP

Es wird eine standardmäßige NSG-Regel hinzugefügt, um es dem Service Fabric-Ressourcenanbieter zu ermöglichen, auf den clientConnectionPort und den httpGatewayConnectionPort des Clusters zuzugreifen. Diese Regel erlaubt den Zugriff auf die Ports über das Diensttag „ServiceFabric“.

>[!NOTE]
>Diese Regel wird immer hinzugefügt und kann nicht außer Kraft gesetzt werden.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayToSFRP",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.",
        "protocol": "TCP",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "ServiceFabric",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 500,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>NSG-Regel: SFMC_AllowServiceFabricGatewayPorts

Diese optionale Regel ermöglicht es Kunden, auf SFX zuzugreifen, mit PowerShell eine Verbindung zum Cluster herzustellen und Cluster-API-Endpunkte in Service Fabric aus dem Internet zu nutzen, indem LB-Ports für clientConnectionPort und httpGatewayPort geöffnet werden.

>[!NOTE]
>Diese Regel wird nicht hinzugefügt, wenn bereits eine benutzerdefinierte Regel mit denselben Zugriffs-, Richtungs- und Protokollwerten für ein und denselben Port vorhanden ist. Sie können diese Regel mit benutzerdefinierten NSG-Regeln außer Kraft setzen.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3001,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

<a id="rdp"></a>
## <a name="enable-access-to-rdp-ports-from-internet"></a>Aktivieren des Zugriffs auf RDP-Ports über das Internet

Verwaltete Service Fabric-Cluster erlauben standardmäßig keinen Zugriff auf die RDP-Ports über das Internet. Sie können den eingehenden Zugriff auf RDP-Ports aus dem Internet aktivieren, indem Sie die folgende Eigenschaft in einer verwalteten Service Fabric-Clusterressource festlegen.

```json
"allowRDPAccess": true
```

Wenn die Eigenschaft allowRDPAccess auf TRUE festgelegt ist, wird Ihrer Clusterbereitstellung die folgende NSG-Regel hinzugefügt.

```json
{
    "name": "SFMC_AllowRdpPort",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

Verwaltete Service Fabric-Cluster erstellen automatisch NAT-Regeln für eingehenden Datenverkehr für jede Instanz in einem Knotentyp. Wenn Sie die Portzuordnungen ermitteln möchten, mit denen Sie bestimmte Instanzen erreichen können (Clusterknoten), befolgen Sie die Schritte unten:

Suchen Sie mithilfe des Azure-Portals nach den NAT-Regeln für eingehenden Datenverkehr für den verwalteten Cluster für das Remotedesktopprotokoll (RDP).

1. Navigieren Sie zur verwalteten Clusterressourcengruppe innerhalb Ihres Abonnements, die mit dem folgenden Format benannt ist: SFC_{cluster-id}.

2. Wählen Sie den Lastenausgleich für den Cluster mit dem folgenden Format aus: LB-{cluster-name}.

3. Klicken Sie auf der Seite für Ihren Lastenausgleich auf „NAT-Regeln für eingehenden Datenverkehr“. Überprüfen Sie die NAT-Regeln für eingehenden Datenverkehr, um den eingehenden Front-End-Port zu bestätigen, um eine Portzuordnung für einen Knoten anzuzielen. 

   Der folgende Screenshot zeigt die NAT-Regeln für eingehenden Datenverkehr für drei verschiedene Knotentypen:

   ![Eingehende NAT-Regeln][Inbound-NAT-Rules]

   Standardmäßig befindet sich der Front-End-Port für Windows-Cluster im Bereich von 50000 und höher, und der Zielport ist 3389. Dies ist dem RDP-Dienst auf dem Zielknoten zugeordnet.
   > [!NOTE]
   > Wenn Sie das BYOLB-Feature benutzen und RDP verwenden möchten, müssen Sie dafür einen NAT-Pool separat konfigurieren. Dadurch werden nicht automatisch NAT-Regeln für diese Knotentypen erstellt.

4. Stellen Sie eine Remoteverbindung mit dem spezifischen Knoten (Skalierungsgruppeninstanz) her. Sie können den Benutzernamen und das Kennwort, die Sie beim Erstellen des Clusters erstellt haben, oder beliebige andere Anmeldeinformationen, die Sie konfiguriert haben, verwenden.

Der folgende Screenshot zeigt die Verwendung einer Remotedesktopverbindung, um eine Verbindung zum Apps-Knoten (Instanz 0) in einem Windows-Cluster herzustellen:

![Remotedesktopverbindung][sfmc-rdp-connect]

<a id="lbconfig"></a>
## <a name="modify-default-load-balancer-configuration"></a>Ändern der Lastenausgleichs-Standardkonfiguration

### <a name="load-balancer-ports"></a>Lastenausgleichsports

Verwaltete Service Fabric-Cluster erstellen eine NSG-Regel im Standardprioritätsbereich für alle LB-Ports (Lastenausgleich), die im Abschnitt „loadBalancingRules“ unter den Eigenschaften *ManagedCluster* konfiguriert sind. Diese Regel öffnet Lastenausgleichsports für eingehenden Datenverkehr aus dem Internet.  

>[!NOTE]
>Diese Regel wird dem optionalen Prioritätsbereich hinzugefügt und kann durch Hinzufügen von benutzerdefinierten NSG-Regeln außer Kraft gesetzt werden.

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>Lastenausgleichstests

Verwaltete Service Fabric-Cluster erstellen automatisch Lastenausgleichstests für Fabric Gateway-Ports sowie für alle Ports, die in den Eigenschaften für verwaltete Cluster im Abschnitt `loadBalancingRules` konfiguriert wurden.

```json
{
  "value": [
    {
        "name": "FabricTcpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19000,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "FabricHttpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "probe1_tcp_8080",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 8080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
            {
                "id": "<>"
            }
        ]
      },
      "type": "Microsoft.Network/loadBalancers/probes"
    }
  ]
}
```

<a id="ipv6"></a>
## <a name="enable-ipv6-preview"></a>Aktivieren von IPv6 (Vorschau)
Verwaltete Cluster aktivieren IPv6 nicht standardmäßig. Dieses Feature ermöglicht eine vollständige IPv4/IPv6-Funktion mit dualen Stapeln vom Lastenausgleichs-Front-End zu den Back-End-Ressourcen. Alle Änderungen, die Sie an der Lastenausgleichs-Konfiguration oder den NSG-Regeln des verwalteten Clusters vornehmen, wirken sich sowohl auf das IPv4- als auch auf das IPv6-Routing aus.

> [!NOTE]
> Diese Einstellung ist im Portal nicht verfügbar und kann nach der Erstellung des Clusters nicht mehr geändert werden.

1. Legen Sie die folgende Eigenschaft in einer verwalteten Service Fabric-Clusterressource fest.
   ```json
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                "enableIpv6": true
                },
            }
   ```

2. Stellen Sie Ihren IPv6-fähigen verwalteten Cluster bereit. Passen Sie die [Beispielvorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-IPv6) nach Bedarf an oder erstellen Sie Ihre eigene Vorlage.
   Im folgenden Beispiel erstellen wir eine Ressourcengruppe namens `MyResourceGroup` in `westus` und stellen einen Cluster bereit, in der diese Funktion aktiviert ist.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```
   Nach der Bereitstellung befinden sich das virtuelle Netzwerk und die Ressourcen Ihrer Cluster im dualen Stapel. Daher verfügt der Front-End-Lastenausgleich des Clusters über eine eindeutige DNS-Adresse, z. B. `mycluster-ipv6.southcentralus.cloudapp.azure.com`, die einer öffentlichen IPv6-Adresse auf dem Azure Load Balancer und privaten IPv6-Adressen auf den VMs zugeordnet ist. 


<a id="byovnet"></a>
## <a name="bring-your-own-virtual-network-preview"></a>Verwendung eines eigenen virtuellen Netzwerks (Vorschau)
Mit diesem Feature können Kunden ein vorhandenes virtuelles Netzwerk verwenden, indem sie ein dediziertes Subnetz angeben, in dem der verwaltete Cluster seine Ressourcen bereitstellt. Dies kann nützlich sein, wenn Sie bereits über ein konfiguriertes VNet und Subnetz mit zugehörigen Sicherheitsrichtlinien und Datenverkehrsrouting verfügen, die Sie verwenden möchten. Sobald die Bereitstellung an ein vorhandenes virtuelles Netzwerk durch Sie erfolgt ist, ist es einfach, andere Netzwerkfeatures wie Azure ExpressRoute, Azure-VPN-Gateway, eine Netzwerksicherheitsgruppe und Peering virtueller Netzwerke zu integrieren. Darüber hinaus können Sie bei Bedarf [Ihren eigenen Azure-Lastenausgleich verwenden](#byolb).

> [!NOTE]
> Bei Verwendung von BYOVNET werden verwaltete Clusterressourcen in einem Subnetz bereitgestellt. 

> [!NOTE]
> Diese Einstellung kann nicht mehr geändert werden, nachdem der Cluster erstellt wurde, und der verwaltete Cluster weist dem bereitgestellten Subnetz eine NSG zu. Setzen Sie die NSG-Zuweisung nicht außer Kraft, da der Datenverkehr möglicherweise unterbrochen wird.

**Verwendung eines eigenen virtuellen Netzwerks:**

1. Sie können den Dienst `Id` aus Ihrem Abonnement für die Service Fabric-Ressourcenanbieteranwendung erhalten.
   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > Stellen Sie sicher, dass Sie sich im richtigen Abonnement befinden. Die Prinzipal-ID ändert sich, wenn sich das Abonnement in einem anderen Mandanten befindet.

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   Notieren Sie sich die **ID** aus der vorstehenden Ausgabe als **principalId** für die Verwendung in einem späteren Schritt.

   |Name der Rollendefinition|Rollendefinitions-ID|
   |----|-------------------------------------|
   |Mitwirkender von virtuellem Netzwerk|4d97b98b-1d4f-4787-a291-c67834d212e7|

   Notieren Sie sich die Eigenschaftswerte `Role definition name` und `Role definition ID` für die Verwendung in einem späteren Schritt.

2. Fügen Sie der Service Fabric-Ressourcenanbieteranwendung eine Rollenzuweisung hinzu. Das Hinzufügen einer Rollenzuweisung ist eine einmalige Aktion. Sie fügen die Rolle hinzu, indem Sie die folgenden PowerShell-Befehle ausführen oder wie unten beschrieben eine ARM-Vorlage (Azure Resource Manager-Vorlage) konfigurieren. 

   In den folgenden Schritten beginnen wir mit einem vorhandenen virtuellen Netzwerk mit dem Namen „ExistingRG-vnet“ in der Ressourcengruppe „ExistingRG“. Das Subnetz ist standardmäßig benannt.

   Rufen Sie die erforderlichen Informationen aus dem vorhandenen VNet ab.

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzVirtualNetwork -Name ExistingRG-vnet -ResourceGroupName ExistingRG
   ```
   Notieren Sie sich den folgenden Subnetznamen und den Eigenschaftswert `Id`, der vom Abschnitt `Subnets` in der Antwort ausgegeben wird, für die Antwort, die Sie in späteren Schritten verwenden werden.

   ```JSON
   Subnets:[
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/virtualNetworks/ExistingRG-vnet/subnets/default"
   }]
   ```

   Führen Sie den folgenden PowerShell-Befehl mithilfe der Prinzipal-ID, des Rollendefinitionsnamens aus Schritt 2 und des oben abgerufenen Zuweisungsbereichs `Id` aus:
   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
   ```

   Sie können die Rollenzuweisung auch mithilfe einer ARM-Vorlage (Azure Resource Manager-Vorlage) hinzufügen, die mit den richtigen Werten für `principalId`, `roleDefinitionId`, `vnetName` und `subnetName` konfiguriert ist:

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('VNetRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > VNetRoleAssignmentID muss eine [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16) sein. Wenn Sie eine Vorlage erneut bereitstellen, einschließlich dieser Rollenzuweisung, vergewissern Sie sich, dass die GUID die gleiche wie die ursprünglich verwendete ist. Wir empfehlen, dies isoliert auszuführen oder nach der Bereitstellung diese Ressource aus der Clustervorlage zu entfernen, da sie nur einmal erstellt werden muss.

   Im Folgenden finden Sie eine vollständige [Azure Resource Manager(ARM)-Vorlage, mit der ein VNet-Subnetz erstellt und die Rollenzuweisung durchgeführt werden kann](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOVNET/createVNet-assign-role.json) als Beispiel. Diese Vorlage können Sie für diesen Schritt verwenden.

3. Konfigurieren Sie die Eigenschaft `subnetId` für die Clusterbereitstellung, nachdem die Rolle wie unten gezeigt eingerichtet wurde:

   ```JSON
    "resources": [
        {
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            },
            "properties": {
                "subnetId": "subnetId",
            ...
            }
   ```
   Beziehen Sie sich auf die [Beispielvorlage zur Verwendung eines eigenen VNet-Clusters](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOVNET) oder passen Sie Ihre eigene Vorlage an.

4. Stellen Sie die konfigurierte Azure Resource Manager(ARM)-Vorlage für verwaltete Cluster bereit.

   Im folgenden Beispiel erstellen wir eine Ressourcengruppe namens `MyResourceGroup` in `westus` und stellen einen Cluster bereit, in der diese Funktion aktiviert ist.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   Wenn Sie Ihr eigenes VNet-Subnetz verwenden, wird der öffentliche Endpunkt weiterhin vom Ressourcenanbieter erstellt und verwaltet, jedoch im konfigurierten Subnetz. Die Funktion lässt nicht zu, dass Sie die öffentliche IP-Adresse angeben bzw. die statische IP-Adresse auf dem Azure Load Balancer erneut verwenden. Sie können [Ihren eigenen Azure Load Balancer verwenden](#byolb), und zwar zusammen mit dieser Funktion oder allein, wenn Sie diese oder andere Lastenausgleichs-Szenarien benötigen, die nicht nativ unterstützt werden.

<a id="byolb"></a>
## <a name="bring-your-own-azure-load-balancer-preview"></a>Verwendung eines eigenen Azure Load Balancers (Vorschau)
Verwaltete Cluster erstellen einen öffentlichen standardisierten Azure Load Balancer und einen vollqualifizierten Domänennamen mit einer statischen öffentlichen IP-Adresse für den primären und sekundären Knotentyp. Mit der Funktion „bring your own load balancer“ (eigenen Load Balancer einbringen) können Sie einen vorhandenen Azure Load Balancer für sekundäre Knotentypen sowohl für eingehenden als auch für ausgehenden Datenverkehr verwenden. Wenn Sie Ihren eigenen Azure Load Balancer verwenden, können Sie Folgendes:

* Einen vorkonfigurierte statische IP-Adresse für den Load Balancer für privaten oder öffentlichen Datenverkehr nutzen.
* Einen Load Balancer einem bestimmten Knotentyp zuordnen.
* Konfigurieren Sie Netzwerksicherheitsgruppenregeln pro Knotentyp, da jeder Knotentyp in einem eigenen Subnetz bereitgestellt wird
* Eventuell vorhandene Richtlinien und Kontrollen verwalten.
* Konfigurieren eines ausschließlich internen Lastenausgleichs (Load Balancer) und Verwenden des Standardlastenausgleichs für externen Datenverkehr

> [!NOTE]
> Bei Verwendung von BYOVNET werden verwaltete Clusterressourcen unabhängig von zusätzlichen konfigurierten Lastenausgleichsressourcen in einem Subnetz mit einer NSG bereitgestellt.

> [!NOTE]
> Sie können nach der Bereitstellung eines Knotentyps nicht vom Standardlastenausgleich zu einem benutzerdefinierten Lastenausgleich (Load Balancer) wechseln, aber Sie können die Konfiguration des benutzerdefinierten Lastenausgleichs nach der Bereitstellung ändern, wenn diese aktiviert ist.

**Funktionsanforderungen**
 * SKU-Typen „Basic Azure Load Balancer“ und „Standard Azure Load Balancer“ werden unterstützt.
 * Back-End- und NAT-Pools müssen auf dem Azure Load Balancer konfiguriert werden.
 * Sie müssen die ausgehende Konnektivität entweder mithilfe eines bereitgestellten öffentlichen Lastenausgleichs oder des öffentlichen Standardlastenausgleichs aktivieren.

Hier sind einige Beispielszenarien, in denen Kunden dies verwenden können:

In diesem Beispiel möchte ein Kunde Datenverkehr über einen vorhandenen Azure Load Balancer, der mit einer vorhandenen statischen IP-Adresse konfiguriert ist, an zwei Knotentypen weiterleiten.

![Verwendung eines eigenen Load Balancers, Beispiel 1][sfmc-byolb-example-1]

In diesem Beispiel möchte ein Kunde Datenverkehr über vorhandene Azure Load Balancers routen. Dies soll dazu dienen, ihn bei der unabhängigen Verwaltung des Datenverkehrsflusses zu seinen Anwendungen zu unterstützen, die auf separaten Knotentypen ausgeführt werden. Bei der Einrichtung wie in diesem Beispiel befindet sich jeder Knotentyp hinter seiner eigenen verwalteten NSG.

![Verwendung eines eigenen Load Balancers, Beispiel 2][sfmc-byolb-example-2]

In diesem Beispiel möchte ein Kunde Datenverkehr über vorhandene interne Azure Load-Balancer-Systeme routen. Dies hilft ihnen, den Datenverkehrsfluss zu ihren Anwendungen unabhängig voneinander zu verwalten, die auf separaten Knotentypen ausgeführt werden. Bei der Einrichtung wie in diesem Beispiel befindet sich jeder Knotentyp hinter seiner eigenen verwalteten NSG und verwendet den Standardlastenausgleich für externen Datenverkehr.

![Verwendung eines eigenen Load Balancers, Beispiel 3][sfmc-byolb-example-3]

So konfigurieren Sie die Verwendung eines eigenen Lastenausgleichs:

1. Sie können den Dienst `Id` aus Ihrem Abonnement für die Service Fabric-Ressourcenanbieteranwendung erhalten:

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > Stellen Sie sicher, dass Sie sich im richtigen Abonnement befinden. Die Prinzipal-ID ändert sich, wenn sich das Abonnement in einem anderen Mandanten befindet.

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   Notieren Sie sich die **ID** aus der vorstehenden Ausgabe als **principalId** für die Verwendung in einem späteren Schritt.

   |Name der Rollendefinition|Rollendefinitions-ID|
   |----|-------------------------------------|
   |Mitwirkender von virtuellem Netzwerk|4d97b98b-1d4f-4787-a291-c67834d212e7|

   Notieren Sie sich die Eigenschaftswerte `Role definition name` und `Role definition ID` für die Verwendung in einem späteren Schritt.

2. Fügen Sie der Service Fabric-Ressourcenanbieteranwendung eine Rollenzuweisung hinzu. Das Hinzufügen einer Rollenzuweisung ist eine einmalige Aktion. Sie fügen die Rolle hinzu, indem Sie die folgenden PowerShell-Befehle ausführen oder wie unten beschrieben eine ARM-Vorlage (Azure Resource Manager-Vorlage) konfigurieren.

   In den folgenden Schritten beginnen wir mit einem vorhandenen Lastenausgleich mit dem Namen Existing-LoadBalancer1 in der Ressourcengruppe Existing-RG. 

   Rufen Sie die erforderlichen `Id`-Eigenschaftsinformationen aus dem vorhandenen Azure Load Balancer ab. Sie führen diese Schritte aus: 

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzLoadBalancer -Name "Existing-LoadBalancer1" -ResourceGroupName "Existing-RG"
   ```
   Beachten Sie folgenden `Id`, den Sie im nächsten Schritt verwenden:
   ```JSON
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/loadBalancers/Existing-LoadBalancer1"
   }
   ```
   Führen Sie den folgenden PowerShell-Befehl mithilfe der Prinzipal-ID, des Rollendefinitionsnamens aus Schritt 2 und des oben abgerufenen Zuweisungsbereichs `Id` aus:

   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/loadBalancers/<LoadBalancerName>"
   ```

   Sie können die Rollenzuweisung auch mithilfe einer ARM-Vorlage (Azure-Ressourcen-Manager-Vorlage) hinzufügen, die mit den richtigen Werten für `principalId`, `roleDefinitionId`“ konfiguriert ist:

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('loadBalancerRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > loadBalancerRoleAssignmentID muss eine [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16) sein. Wenn Sie eine Vorlage erneut bereitstellen, einschließlich dieser Rollenzuweisung, vergewissern Sie sich, dass die GUID die gleiche wie die ursprünglich verwendete ist. Wir empfehlen, dies isoliert auszuführen oder nach der Bereitstellung diese Ressource aus der Clustervorlage zu entfernen, da sie nur einmal erstellt werden muss.

   In dieser Beispielvorlage erfahren Sie, [wie Sie einen öffentlichen Lastenausgleich erstellen und eine Rolle zuweisen](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOLB/createlb-and-assign-role.json).


3. Konfigurieren Sie die erforderliche ausgehende Konnektivität für den Knotentyp. Sie müssen einen öffentlichen Lastenausgleich konfigurieren, um ausgehende Verbindungen zu ermöglichen, oder den öffentlichen Standardlastenausgleich verwenden. 
   
   Konfigurieren Sie `outboundRules`, um einen öffentlichen Load Balancer für die Bereitstellung ausgehender Konnektivität zu konfigurieren. Weitere Informationen finden Sie im Beispiel [ zum Erstellen eines Lastenausgleichs und Zuweisen eines Rolle-Samples in einer Azure Ressourcen-Manager(ARM)-Vorlage](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOLB/createlb-and-assign-role.json).
   
   oder
   
   Um den Knotentyp für die Verwendung des Standardlastenausgleichs zu konfigurieren, legen Sie Folgendes in Ihrer Vorlage fest: 
   
   * Die apiVersion der verwalteten Service-Fabric-Clusterressource sollte **2021-11-01-Vorschau** oder höher lauten.

   ```json
      {
      "apiVersion": "[variables('sfApiVersion')]",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
      ...
      "properties": {
          "isPrimary": false,
          "useDefaultPublicLoadBalancer": true
          ...
      }
   ```

4. Konfigurieren Sie optional einen eingehenden Anwendungsport und einen zugehörigen Test für Ihren Azure Load Balancer.
   Ein Beispiel hierfür finden Sie unter [„Verwendung eines eigenen Lastenausgleichs-Samples in einer Azure Ressourcen-Manager-(ARM-)Vorlage“](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOLB).

5. Konfigurieren Sie optional die NSG-Regeln des verwalteten Clusters, die auf den Knotentyp angewendet werden, um jeglichen erforderlichen Datenverkehr zu ermöglichen, den Sie auf dem Azure Load Balancer konfiguriert haben, oder der Datenverkehr wird blockiert.
   Ein Beispiel für eingehenden NSG-Regel-Konfiguration finden Sie unter [„Verwendung eines eigenen Lastenausgleich-Samples in einer Azure Ressourcen-Manager(ARM)-Vorlage“](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOLB). Suchen Sie in der Vorlage nach der Eienschaft `networkSecurityRules`.

6. Bereitstellen der konfigurierten ARM-Vorlage für verwaltete Cluster: In diesem Schritt verwenden wir die Arm-Vorlage [Verwendung eines eigenen Lastenausgleich-Samples in einer Azure Ressourcen-Manager(ARM)-Vorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOLB) (Bring your own load balancer sample Azure Resource Manager (ARM) template).

   Im Folgenden wird eine Ressourcengruppe namens `MyResourceGroup` in `westus` erstellt und ein Cluster wird mithilfe eines vorhandenen Lastenausgleichs bereitgestellt.
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   Nach der Bereitstellung ist der sekundäre Knotentyp für die Verwendung des angegebenen Lastenausgleichs für eingehenden und ausgehenden Datenverkehr konfiguriert. Die Service Fabric-Clientverbindungs- und -Gatewayendpunkte verweisen weiterhin auf das öffentliche DNS der statischen IP-Adresse des primären Knotentyps des verwalteten Clusters.



<a id="accelnet"></a>
## <a name="enable-accelerated-networking-preview"></a>Aktivieren des beschleunigten Netzwerkbetriebs (Vorschau)
Der beschleunigte Netzwerkbetrieb ermöglicht die E/A-Virtualisierung mit einzelnem Stamm (Single Root I/O Virtualization, SR-IOV) für einen virtuellen Computer (VM) der VM-Skalierunggruppe, die die zugrunde liegende Ressource für Knotentypen ist. Dieser Hochleistungspfad umgeht den Host vom Datenpfad, wodurch Latenz, Jitter und CPU-Auslastung für die anspruchsvollsten Netzwerkworkloads reduziert werden. Von Service Fabric verwaltete Clusterknotentypen können mit beschleunigtem Netzwerkbetrieb auf [unterstützten SKUs virtueller Computer](../virtual-machines/sizes.md) bereitgestellt werden. Weitere Informationen finden Sie unter [Einschränkungen und Bedingungen](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints). 

* Bitte beachten Sie, dass der beschleunigte Netzwerkbetrieb in den meisten universellen, compute-optimierten Instanzgrößen mit mindestens 2 vCPUs unterstützt wird. Bei Instanzen, die Hyperthreading unterstützen, wird der beschleunigte Netzwerkbetrieb auf VM-Instanzen mit mindestens 4 vCPUs unterstützt.

Aktivieren Sie den beschleunigten Netzwerkbetrieb, indem Sie die Eigenschaft `enableAcceleratedNetworking` in Ihrer Ressourcen-Manager-Vorlage wie folgt deklarieren:

* Die apiVersion der verwalteten Service-Fabric-Clusterressource sollte **2021-11-01-Vorschau** oder höher lauten.

```json
   {
   "apiVersion": "[variables('sfApiVersion')]",
   "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
   ...
   "properties": {
       ...
       "enableAcceleratedNetworking": true,
       ...
   }
```

Um den beschleunigten Netzwerkbetrieb für einen vorhandenen Service-Fabric-Cluster zu aktivieren, müssen Sie zunächst einen Service-Fabric-Cluster erweitern, indem Sie einen neuen Knotentyp hinzufügen und Folgendes ausführen:

1) Bereitstellen eines Knotentyps mit aktiviertem beschleunigten Netzwerkbetrieb
2) Migrieren Ihrer Dienste und deren Status zum bereitgestellten Knotentyp mit aktiviertem beschleunigten Netzwerkbetrieb

Die Erweiterung der Infrastruktur ist erforderlich, um beschleunigten Netzwerkbetrieb für einen vorhandenen Cluster zu ermöglichen, da die Aktivierung von beschleunigtem Netzwerkbetrieb zu Ausfallzeiten führen würde, da alle virtuellen Computer in einer Verfügbarkeitsgruppe beendet werden müssen und ihre Zuordnung aufgehoben werden muss, bevor beschleunigter Netzwerkbetrieb für eine vorhandene NIC aktiviert werden kann.


<a id="auxsubnet"></a>
## <a name="configure-auxiliary-subnets-preview"></a>Konfigurieren von Hilfssubnetzen (Vorschau)
Zusätzliche Subnetze bieten die Möglichkeit, zusätzliche verwaltete Subnetze ohne Knotentyp zum Unterstützen von Fallszenarien wie [Private Link Service](../private-link/private-link-service-overview.md) und [Bastion-Hosts](../bastion/bastion-overview.md) zu erstellen.

Konfigurieren Sie weitere Subnetze, indem Sie die Eigenschaft `auxiliarySubnets` und die erforderlichen Parameter in Ihrer Ressourcen-Manager-Vorlage wie folgt deklarieren:

* Die apiVersion der verwalteten Service-Fabric-Clusterressource sollte **2021-11-01-Vorschau** oder höher lauten.

```JSON
    "resources": [
        {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                "auxiliarySubnets": [
                  {
                  "name" : "mysubnet",
                  "enableIpv6" : "true"
                  }
                ]              
```

Siehe [vollständige Liste der verfügbaren Parameter](/azure/templates/microsoft.servicefabric/2021-11-01/managedclusters) 

## <a name="next-steps"></a>Nächste Schritte
[Konfigurationsoptionen für verwaltete verwaltete Service Fabric-Cluster](how-to-managed-cluster-configuration.md)
[Verwaltete Service Fabric-Cluster – Übersicht](overview-managed-cluster.md)

<!--Image references-->
[Inbound-NAT-Rules]: ./media/how-to-managed-cluster-networking/inbound-nat-rules.png
[sfmc-rdp-connect]: ./media/how-to-managed-cluster-networking/sfmc-rdp-connect.png
[sfmc-byolb-example-1]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-1.png
[sfmc-byolb-example-2]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-2.png
[sfmc-byolb-example-3]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-3.png

