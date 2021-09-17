---
title: 'Tutorial: Erstellen und Verwalten virtueller Azure-Netzwerke für virtuelle Windows-Computer'
description: In diesem Tutorial erfahren Sie, wie Sie Azure PowerShell zum Erstellen und Verwalten virtueller Azure-Netzwerke für virtuelle Windows-Computer verwenden.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.collection: windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0ff9ba076c1ac24f6afe7d99e593f3f5f0bfb142
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688595"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Tutorial: Erstellen und Verwalten virtueller Azure-Netzwerke für virtuelle Windows-Computer mit Azure PowerShell

**Gilt für**: :heavy_check_mark: Windows-VMs

Virtuelle Azure-Computer nutzen Azure-Netzwerke für interne und externe Kommunikation. Dieses Tutorial enthält ausführliche Informationen zum Bereitstellen von zwei virtuellen Computern sowie zum Konfigurieren von Azure-Netzwerken für diese virtuellen Computer. In den Beispielen in diesem Tutorial wird davon ausgegangen, dass auf den virtuellen Computern eine Webanwendung mit einem Datenbank-Back-End gehostet wird. Eine Anwendung wird im Tutorial jedoch nicht bereitgestellt. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks und des Subnetzes
> * Erstellen einer öffentlichen IP-Adresse
> * Erstellen eines virtuellen Front-End-Computers
> * Netzwerkdatenverkehr absichern
> * Erstellen des virtuellen Back-End-Computers


## <a name="vm-networking-overview"></a>Übersicht über VM-Netzwerke

Virtuelle Azure-Netzwerke ermöglichen sichere Netzwerkverbindungen zwischen virtuellen Computern, dem Internet und anderen Azure-Diensten wie Azure SQL-Datenbank. Virtuelle Netzwerke werden in logische Segmente, sogenannte Subnetze, unterteilt. Subnetze dienen zur Steuerung des Netzwerkdatenflusses und als Sicherheitsgrenze. Bei der Bereitstellung eines virtuellen Computers ist in der Regel eine virtuelle Netzwerkschnittstelle enthalten, die an ein Subnetz angefügt ist.

Während der Durchführung dieses Tutorials werden die folgenden Ressourcen erstellt:

![Virtuelles Netzwerk mit zwei Subnetzen](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet*: Das virtuelle Netzwerk, das von den virtuellen Computer verwendet wird, um miteinander und mit dem Internet zu kommunizieren.
- *myFrontendSubnet*: Das Subnetz in *myVNet*, das von den Front-End-Ressourcen verwendet wird.
- *myPublicIPAddress*: Die öffentliche IP-Adresse, die für den Zugriff auf *myFrontendVM* über das Internet verwendet wird.
- *myFrontentNic*: Die von *myFrontendVM* für die Kommunikation mit *myBackendVM* verwendete Netzwerkschnittstelle.
- *myFrontendVM*: Der virtuelle Computer, der für die Kommunikation zwischen dem Internet und *myBackendVM* verwendet wird.
- *myBackendNSG*: Die Netzwerksicherheitsgruppe, die die Kommunikation zwischen *myFrontendVM* und *myBackendVM* steuert.
- *myBackendSubnet*: Das *myBackendNSG* zugeordnete Subnetz, das von den Back-End-Ressourcen verwendet wird.
- *myBackendNic*: Die von *myBackendVM* für die Kommunikation mit *myFrontendVM* verwendete Netzwerkschnittstelle.
- *myBackendVM*: Der virtuelle Computer, der Port 1433 für die Kommunikation mit *myFrontendVM* verwendet.


## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.


## <a name="create-subnet"></a>Erstellen eines Subnetzes 

In diesem Tutorial wird ein einzelnes virtuelles Netzwerk mit zwei Subnetzen erstellt: Einem Front-End-Subnetz zum Hosten einer Webanwendung und einem Back-End-Subnetz zum Hosten eines Datenbankservers.

Bevor Sie ein virtuelles Netzwerk erstellen können, müssen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe erstellen. Das folgende Beispiel erstellt am Standort *EastUS* eine Ressourcengruppe mit dem Namen *myRGNetwork*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Erstellen Sie mit [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) eine Subnetzkonfiguration namens *myFrontendSubnet*:

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Erstellen Sie dann eine Subnetzkonfiguration namens *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Virtuelles Netzwerk erstellen

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) unter Verwendung von *myFrontendSubnet* und *myBackendSubnet* ein VNET namens *myVNet*:

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Zu diesem Zeitpunkt wurde ein Netzwerk erstellt und in zwei Subnetze unterteilt: eines für Front-End-Dienste und eines für Back-End-Dienste. Im nächsten Abschnitt werden virtuelle Computer erstellt und mit diesen Subnetzen verbunden.

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Dank einer öffentlichen IP-Adresse kann über das Internet auf Azure-Ressourcen zugegriffen werden. Die Zuordnungsmethode der öffentlichen IP-Adresse kann als „dynamisch“ oder „statisch“ konfiguriert werden. Standardmäßig werden öffentliche IP-Adressen dynamisch zugeordnet. Dynamische IP-Adressen werden freigegeben, wenn die Zuordnung eines virtuellen Computers aufgehoben wird. Dieses Verhalten bewirkt, dass sich die IP-Adresse bei jedem Vorgang ändert, der eine Aufhebung der VM-Zuordnung beinhaltet.

Als Zuordnungsmethode kann die statische Zuordnung festgelegt werden. Dadurch wird sichergestellt, dass die IP-Adresse einem virtuellen Computer zugewiesen bleibt, auch wenn seine Zuordnung aufgehoben ist. Wenn Sie eine statische IP-Adresse verwenden, kann die IP-Adresse selbst nicht angegeben werden. Stattdessen wird sie aus einem Pool verfügbarer Adressen zugeordnet.

Erstellen Sie mithilfe von [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) eine öffentliche IP-Adresse namens *myPublicIPAddress*:

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Sie können den Parameter „-AllocationMethod“ in `Static` ändern, um eine statische öffentliche IP-Adresse zuzuweisen.

## <a name="create-a-front-end-vm"></a>Erstellen eines virtuellen Front-End-Computers

Ein virtueller Computer muss für die Kommunikation in einem virtuellen Netzwerk über eine virtuelle Netzwerkschnittstelle (NIC) verfügen. Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) eine Netzwerkschnittstelle:

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) den Benutzernamen und das Kennwort für das Administratorkonto auf dem virtuellen Computer fest. Diese Anmeldeinformationen benötigen Sie, um in weiteren Schritten eine Verbindung mit der VM herzustellen:

```azurepowershell-interactive
$cred = Get-Credential
```

Erstellen Sie die virtuellen Computer mit [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Netzwerkdatenverkehr absichern

Eine Netzwerksicherheitsgruppe (NSG) enthält eine Liste mit Sicherheitsregeln, mit denen Netzwerkdatenverkehr für Ressourcen, die mit virtuellen Azure-Netzwerken (VNet) verbunden sind, zugelassen oder abgelehnt wird. Netzwerksicherheitsgruppen können Subnetzen oder einzelnen Netzwerkschnittstellen zugeordnet werden. Eine Netzwerksicherheitsgruppe, die einer Netzwerkschnittstelle zugewiesen ist, gilt nur für den zugeordneten virtuellen Computer. Wenn eine NSG einem Subnetz zugeordnet ist, gelten die Regeln für alle Ressourcen, die mit dem Subnetz verbunden sind.

### <a name="network-security-group-rules"></a>Netzwerksicherheitsgruppen-Regeln

NSG-Regeln definieren Netzwerkports, über die Datenverkehr zugelassen oder verweigert wird. Die Regeln können Quell- und Ziel-IP-Adressbereiche enthalten, um Datenverkehr zwischen bestimmten Systemen oder Subnetzen zu steuern. NSG-Regeln enthalten darüber hinaus eine Priorität (zwischen 1 und 4.096). Regeln werden gemäß ihrer Priorität ausgewertet. Eine Regel mit einer Priorität von 100 wird vor einer Regel der Priorität 200 ausgewertet.

Alle NSGs enthalten eine Gruppe von Standardregeln. Die Standardregeln können zwar nicht gelöscht werden, haben aber niedrigste Priorität und können somit durch selbst erstellte Regeln außer Kraft gesetzt werden.

- **Virtuelles Netzwerk:** Datenverkehr wird aus einem bzw. in ein virtuelles Netzwerk in ein- und ausgehender Richtung zugelassen.
- **Internet:** Ausgehender Datenverkehr wird zugelassen, aber eingehender Datenverkehr wird blockiert.
- **Lastenausgleich:** Lassen Sie für den Lastenausgleich (Load Balancer) von Azure die Überprüfung der Integrität der virtuellen Computer und der Rolleninstanzen zu. Sie können diese Regel außer Kraft setzen, wenn Sie keine Gruppe mit Lastenausgleich verwenden.

### <a name="create-network-security-groups"></a>Erstellen von Netzwerksicherheitsgruppen

Erstellen Sie eine eingehende Regel namens *myFrontendNSGRule*, um den eingehenden Webdatenverkehr für *myFrontendVM* mithilfe von [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) zuzulassen:

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Durch Erstellen einer Netzwerksicherheitsgruppe für das Back-End-Subnetz können Sie den Datenverkehr zu *myBackendVM* nur auf *myFrontendVM* beschränken. Im folgenden Beispiel wird eine NSG-Regel namens *myBackendNSGRule* erstellt:

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Fügen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) die Netzwerksicherheitsgruppe *myFrontendNSG* hinzu:

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Fügen Sie mit „New-AzNetworkSecurityGroup“ die Netzwerksicherheitsgruppe *myBackendNSG* hinzu:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Fügen Sie die Netzwerksicherheitsgruppen zu den Subnetzen hinzu:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Erstellen eines virtuellen Back-End-Computers

Der virtuelle Back-End-Computer kann für dieses Tutorial am einfachsten mithilfe eines SQL Server-Images erstellt werden. In diesem Tutorial wird nur der virtuelle Computer mit dem Datenbankserver erstellt, es werden jedoch keine Informationen für den Zugriff auf die Datenbank angegeben.

Erstellen Sie *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Legen Sie mit „Get-Credential“ den Benutzernamen und das Kennwort für das Administratorkonto auf dem virtuellen Computer fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Erstellen Sie *myBackendVM*.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

In dem Image in diesem Beispiel ist SQL Server installiert, wird aber in diesem Tutorial nicht verwendet. SQL Server ist enthalten, damit Sie sehen, wie Sie einen virtuellen Computer für die Verarbeitung des Webdatenverkehrs und einen virtuellen Computer für die Verarbeitung der Datenbankverwaltung konfigurieren können.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Azure-Netzwerke in Bezug auf virtuelle Computer erstellt und erhalten. 

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks und des Subnetzes
> * Erstellen einer öffentlichen IP-Adresse
> * Erstellen eines virtuellen Front-End-Computers
> * Netzwerkdatenverkehr absichern
> * Erstellen eines virtuellen Back-End-Computers

Informationen zum Schützen Ihrer VM-Datenträger finden Sie unter [Sicherung und Notfallwiederherstellung für Azure IaaS-Datenträger](../backup-and-disaster-recovery-for-azure-iaas-disks.md).
