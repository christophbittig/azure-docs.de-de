---
title: 'Tutorial: Durchführen eines Lastenausgleichs für virtuelle Windows-Computer in Azure'
description: In diesem Tutorial erfahren Sie, wie Sie Azure PowerShell zum Erstellen eines Load Balancers für eine hoch verfügbare und sichere Anwendung über drei virtuelle Windows-Computer verwenden.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/03/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 1becf1dde4c094bc3a904dee2945640f4d2db01a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690105"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>Tutorial: Durchführen eines Lastenausgleichs bei virtuellen Windows-Computern in Azure zum Erstellen einer hoch verfügbaren Anwendung mit Azure PowerShell
**Gilt für:** :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Lastenausgleich bietet ein höheres Maß an Verfügbarkeit durch Verteilung der eingehenden Anforderungen auf mehrere virtuelle Computer. In diesem Tutorial lernen Sie die verschiedenen Komponenten von Azure Load Balancer kennen, die den Datenverkehr verteilen und Hochverfügbarkeit bereitstellen. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen einer Azure Load Balancer-Instanz
> * Erstellen des Integritätstests für den Load Balancer
> * Erstellen von Load Balancer-Regeln
> * Verwenden der Benutzerdefinierten Skripterweiterung zum Erstellen einer einfachen IIS-Website
> * Erstellen und Anfügen von virtuellen Computern an einen Load Balancer
> * Anzeigen eines Load Balancers im Betrieb
> * Hinzufügen und Entfernen von virtuellen Computern zu bzw. aus einem Load Balancer

## <a name="azure-load-balancer-overview"></a>Übersicht über den Azure Load Balancer
Ein Azure Load Balancer ist ein Load Balancer der Schicht 4 (TCP, UDP), der Hochverfügbarkeit durch Verteilen des eingehenden Datenverkehrs auf fehlerfreie virtuelle Computer bietet. Der Integritätstest eines Load Balancers überwacht einen bestimmten Port auf jedem virtuellen Computer und verteilt Datenverkehr nur an einen betriebsbereiten virtuellen Computer.

Sie definieren eine Front-End-IP-Konfiguration, die eine oder mehrere öffentliche IP-Adressen enthält. Mit dieser Front-End-IP-Konfiguration sind der Load Balancer und Ihre Anwendungen über das Internet zugänglich. 

Virtuelle Computer werden über die zugehörige virtuelle Netzwerkschnittstelle (NIC) mit einem Load Balancer verbunden. Zum Verteilen von Datenverkehr auf die virtuellen Computer enthält ein Back-End-Adresspool die IP-Adressen der virtuellen NICs, die mit dem Load Balancer verbunden sind.

Zur Steuerung des Datenverkehrsflusses definieren Sie Load Balancer-Regeln für spezifische Ports und Protokolle, die den virtuellen Computern zugeordnet sind.

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

## <a name="create-azure-load-balancer"></a>Erstellen eines Azure Load Balancers
In diesem Abschnitt wird erläutert, wie Sie die einzelnen Komponenten des Load Balancers erstellen und konfigurieren können. Um Ihren Lastenausgleich erstellen zu können, müssen Sie zunächst mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe erstellen. Das folgende Beispiel erstellt am Standort *EastUS* eine Ressourcengruppe mit dem Namen *myResourceGroupLoadBalancer*:

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse
Um über das Internet auf Ihre App zugreifen zu können, benötigen Sie eine öffentliche IP-Adresse für den Load Balancer. Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) eine öffentliche IP-Adresse. Das folgende Beispiel erstellt in der Ressourcengruppe *MyResourceGroupLoadBalancer* eine öffentliche IP-Adresse mit dem Namen *MyPublicIP*:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Einrichten eines Load Balancers
Erstellen Sie mit [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) einen Front-End-IP-Pool. Im folgenden Beispiel wird ein Front-End-IP-Pools namens *myFrontEndPool* erstellt und die Adresse *myPublicIP* angefügt: 

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Adresspool. In den verbleibenden Schritten werden die virtuellen Computer an diesen Back-End-Pool angefügt. Im folgenden Beispiel wird ein Back-End-Adresspool namens *myBackEndPool* erstellt:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "myBackEndPool"
```

Erstellen Sie nun mit [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) den Lastenausgleich. Im folgenden Beispiel wird mithilfe der in den vorherigen Schritten erstellten Front-End- und Back-End-IP-Pools ein Lastenausgleich namens *myLoadBalancer* erstellt:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests
Damit der Load Balancer den Status Ihrer App überwachen kann, verwenden Sie einen Integritätstest. Abhängig von der Reaktion auf Integritätsüberprüfungen werden der Load Balancer-Rotation durch den Integritätstest dynamisch virtuelle Computer hinzugefügt oder daraus entfernt. Falls bei einem virtuellen Computer innerhalb eines Intervalls von 15 Sekunden zwei aufeinanderfolgende Fehler auftreten, wird er standardmäßig aus der Load Balancer-Verteilung entfernt. Integritätstests werden auf der Grundlage eines Protokolls oder einer spezifischen Integritätsprüfungsseite für Ihre App erstellt. 

Im folgenden Beispiel wird ein TCP-Test erstellt. Sie können auch benutzerdefinierte HTTP-Tests für differenzierte Integritätsprüfungen erstellen. Bei Verwendung eines benutzerdefinierten HTTP-Tests müssen Sie die Integritätsprüfungsseite erstellen, z.B. *healthcheck.aspx*. Der Test muss die HTTP-Antwort **200 OK** zurückgeben, damit der Load Balancer den Host nicht aus der Rotation entfernt.

Erstellen Sie mithilfe von [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) einen TCP-Integritätstest. Im folgenden Beispiel wird ein Integritätstest namens *myHealthProbe* erstellt, der die einzelnen virtuellen Computer an *TCP*-Port *80* überwacht:

```azurepowershell-interactive
Add-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Aktualisieren Sie den Lastenausgleich mit [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer), um den Integritätstest anzuwenden:

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel
Mithilfe einer Load Balancer-Regel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt werden soll. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport. Um sicherzustellen, dass nur fehlerfreie virtuelle Computer Datenverkehr empfangen, definieren Sie zudem den zu verwendenden Integritätstest.

Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) eine Lastenausgleichsregel. Im folgenden Beispiel wird eine Lastenausgleichsregel mit dem Namen *myLoadBalancerRule* erstellt und der Datenverkehr an *TCP*-Port *80* ausgeglichen:

```azurepowershell-interactive
$probe = Get-AzLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Aktualisieren Sie den Lastenausgleich mit [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks
Vor der Bereitstellung mehrerer virtueller Computer und dem Testen des Load Balancers müssen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen erstellen. Weitere Informationen zu virtuellen Netzwerken finden Sie im Tutorial [Verwalten von virtuellen Azure-Netzwerken](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen
Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVnet* mit einem Subnetz namens *mySubnet* erstellt:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Virtuelle NICs werden mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) erstellt. Im folgenden Beispiel werden drei virtuelle NICs erstellt (jeweils eine virtuelle NIC pro virtuellem Computer, den Sie in den folgenden Schritten für Ihre App erstellen). Sie können jederzeit weitere virtuelle NICs und virtuelle Computer erstellen und dem Load Balancer hinzufügen:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern
Fügen Sie die virtuellen Computer in eine Verfügbarkeitsgruppe ein, um die Hochverfügbarkeit Ihrer App zu optimieren.

Erstellen Sie mithilfe von [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) eine Verfügbarkeitsgruppe. Im folgenden Beispiel wird eine Verfügbarkeitsgruppe namens *myAvailabilitySet* erstellt:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) den Benutzernamen und das Kennwort des Administrators der VMs fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Nun können Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) die virtuellen Computer erstellen. Im folgenden Beispiel werden drei VMs und die erforderlichen Komponenten des virtuellen Netzwerks erstellt, falls sie nicht bereits vorhanden sind:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Mit dem Parameter `-AsJob` wird die VM als Hintergrundaufgabe erstellt, sodass die PowerShell-Aufforderungen für Sie zurückgegeben werden. Sie können die Details von Hintergrundaufträgen mit dem `Job`-Cmdlet anzeigen. Die Erstellung und Konfiguration aller drei virtuellen Computer dauert einige Minuten.


### <a name="install-iis-with-custom-script-extension"></a>Installieren von IIS mit benutzerdefinierter Skripterweiterung
In einem vorherigen Tutorial zum [Anpassen eines virtuellen Windows-Computers](tutorial-automate-vm-deployment.md) haben Sie erfahren, wie die Anpassung für virtuelle Computer mit der benutzerdefinierten Skripterweiterung für Windows automatisiert wird. Sie können den gleichen Ansatz verwenden, um IIS für Ihre virtuellen Computer zu installieren und konfigurieren.

Verwenden Sie [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension), um die benutzerdefinierte Skripterweiterung zu installieren. Die Erweiterung führt `powershell Add-WindowsFeature Web-Server` zum Installieren des IIS-Webservers aus und aktualisiert dann die Seite *Default.htm* mit dem Hostnamen der VM:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Testen des Load Balancers
Rufen Sie mit [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse Ihres Lastenausgleichs ab. Im folgenden Beispiel wird die IP-Adresse für *myPublicIP* abgerufen, die wir zuvor erstellt haben:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

Geben Sie die öffentliche IP-Adresse in einem Webbrowser ein. Die Website wird mit dem Hostnamen der VM angezeigt, an die der Load Balancer den Datenverkehr wie im folgenden Beispiel verteilt hat:

![Ausführen der IIS-Website](./media/tutorial-load-balancer/running-iis-website.png)

Sie können eine erzwungene Aktualisierung Ihres Webbrowsers durchführen, um zu verfolgen, wie der Load Balancer den Datenverkehr auf alle drei virtuellen Computer verteilt, auf denen Ihre App ausgeführt wird.


## <a name="add-and-remove-vms"></a>VMs hinzufügen und entfernen
Für die virtuellen Computer, auf denen Ihre App ausgeführt wird, sind unter Umständen gelegentlich Wartungsarbeiten erforderlich (etwa die Installation von Betriebssystemupdates). Zur Bewältigung eines höheren Datenverkehrsaufkommens für Ihre App müssen gegebenenfalls weitere virtuelle Computer hinzugefügt werden. In diesem Abschnitt erfahren Sie, wie Sie einen virtuellen Computer aus dem Load Balancer entfernen oder dem Load Balancer einen virtuellen Computer hinzufügen.

### <a name="remove-a-vm-from-the-load-balancer"></a>Entfernen eines virtuellen Computers aus dem Load Balancer
Rufen Sie mit [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) die NIC ab, und legen Sie dann die Eigenschaft *LoadBalancerBackendAddressPools* der virtuellen NIC auf *$null* fest. Zum Schluss aktualisieren Sie die virtuelle NIC:

```azurepowershell-interactive
$nic = Get-AzNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzNetworkInterface -NetworkInterface $nic
```

Sie können eine erzwungene Aktualisierung Ihres Webbrowsers durchführen, um zu verfolgen, wie der Load Balancer den Datenverkehr auf die beiden verbleibenden virtuellen Computer verteilt, auf denen Ihre App ausgeführt wird. Nun können Sie Wartungsarbeiten für den virtuellen Computer durchführen und beispielsweise Betriebssystemupdates installieren oder den virtuellen Computer neu starten.

### <a name="add-a-vm-to-the-load-balancer"></a>Hinzufügen eines virtuellen Computers zum Load Balancer
Wenn Sie eine VM-Wartung durchgeführt haben oder die Kapazität erweitern möchten, legen Sie die Eigenschaft *LoadBalancerBackendAddressPools* der virtuellen NIC auf den Back-End-Adresspool (*BackendAddressPool*) von [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) fest:

Rufen Sie den Load Balancer ab:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Load Balancer erstellt und ihm einen virtuellen Computer angefügt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Azure Load Balancer-Instanz
> * Erstellen des Integritätstests für den Load Balancer
> * Erstellen von Load Balancer-Regeln
> * Verwenden der Benutzerdefinierten Skripterweiterung zum Erstellen einer einfachen IIS-Website
> * Erstellen und Anfügen von virtuellen Computern an einen Load Balancer
> * Anzeigen eines Load Balancers im Betrieb
> * Hinzufügen und Entfernen von virtuellen Computern zu bzw. aus einem Load Balancer

Im nächsten Tutorial erfahren Sie, wie Sie VM-Netzwerke verwalten.

> [!div class="nextstepaction"]
> [Verwalten virtueller Computer und virtueller Netzwerke](./tutorial-virtual-network.md)
