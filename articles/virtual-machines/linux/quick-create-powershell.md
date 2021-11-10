---
title: 'Schnellstart: Erstellen eines virtuellen Linux-Computers mit Azure PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Azure PowerShell einen virtuellen Linux-Computer erstellen.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/31/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 89803665171fce60bdb46f95dd56fa0535fabeb8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063815"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Schnellstart: Erstellen einer Linux-VM mit PowerShell in Azure

**Gilt für:** :heavy_check_mark: Linux-VMs 

Das Azure PowerShell-Modul dient zum Erstellen und Verwalten von Azure-Ressourcen über die PowerShell-Befehlszeile oder mit Skripts. Diese Schnellstartanleitung zeigt, wie Sie mit dem Azure PowerShell-Modul einen virtuellen Linux-Computer in Azure bereitstellen. In dieser Schnellstartanleitung wird das Ubuntu 18.04 LTS-Marketplace-Image von Canonical verwendet. Um den virtuellen Computer in Aktion zu sehen, stellen Sie außerdem eine SSH-Verbindung mit dem virtuellen Computer her und installieren den NGINX-Webserver.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

## <a name="create-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Verwenden Sie [ssh-keygen](https://www.ssh.com/ssh/keygen/), um ein SSH-Schlüsselpaar zu erstellen. Falls Sie bereits über ein SSH-Schlüsselpaar verfügen, können Sie diesen Schritt überspringen.


```azurepowershell-interactive
ssh-keygen -t rsa -b 4096
```

Sie werden aufgefordert, einen Dateinamen für das Schlüsselpaar anzugeben. Alternativ können Sie die **EINGABETASTE** drücken, um den Standardspeicherort `/home/<username>/.ssh/id_rsa` zu verwenden. Sie können bei Bedarf auch ein Kennwort für die Schlüssel erstellen.

Ausführlichere Informationen zum Erstellen von SSH-Schlüsselpaaren finden Sie unter [Verwenden von SSH-Schlüsseln mit Windows in Azure](ssh-from-windows.md).

Wenn Sie Ihr SSH-Schlüsselpaar mithilfe von Cloud Shell erstellen, wird es in einem [automatisch von Cloud Shell erstellten Speicherkonto](../../cloud-shell/persisting-shell-storage.md) gespeichert. Wenn Sie das Speicherkonto oder die darin enthaltene Dateifreigabe löschen, bevor Sie Ihre Schlüssel abgerufen haben, können Sie nicht mehr auf den virtuellen Computer zugreifen. 

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Erstellen virtueller Netzwerkressourcen

Erstellen Sie ein virtuelles Netzwerk, ein Subnetz und eine öffentliche IP-Adresse. Diese Ressourcen dienen dazu, Netzwerkkonnektivität für den virtuellen Computer bereitzustellen und ihn mit dem Internet zu verbinden:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Erstellen Sie eine Azure-Netzwerksicherheitsgruppe und eine Regel für den Datenverkehr. Die Netzwerksicherheitsgruppe schützt den virtuellen Computer mithilfe von Regeln für eingehenden und ausgehenden Datenverkehr. Im folgenden Beispiel wird für TCP-Port 22 eine Regel für eingehenden Datenverkehr erstellt, die SSH-Verbindungen zulässt. Um eingehenden Webdatenverkehr zuzulassen, wird auch für TCP-Port 80 eine Regel für eingehenden Datenverkehr erstellt.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) eine virtuelle Netzwerkschnittstellenkarte (Network Interface Card, NIC). Die virtuelle NIC verbindet den virtuellen Computer mit einem Subnetz, einer Netzwerksicherheitsgruppe und einer öffentlichen IP-Adresse.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Wenn Sie einen virtuellen Computer in PowerShell erstellen möchten, erstellen Sie eine Konfiguration mit Einstellungen wie dem zu verwendenden Image, der Größe und den Authentifizierungsoptionen. Diese Konfiguration wird dann verwendet, um den virtuellen Computer zu erstellen.

Definieren Sie die SSH-Anmeldeinformationen, die Betriebssysteminformationen und die VM-Größe. In diesem Beispiel befindet sich der SSH-Schlüssel in `~/.ssh/id_rsa.pub`. 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1_v2" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "18.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

Kombinieren Sie jetzt die vorherigen Konfigurationsdefinitionen für die Erstellung mit [New-AzVM](/powershell/module/az.compute/new-azvm):

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

Die Bereitstellung des virtuellen Computers dauert ein paar Minuten. Fahren Sie nach Abschluss der Bereitstellung mit dem nächsten Abschnitt fort.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

Erstellen Sie eine SSH-Verbindung mit dem virtuellen Computer unter Verwendung der öffentlichen IP-Adresse. Verwenden Sie zum Anzeigen der öffentlichen IP-Adresse der VM das Cmdlet [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Fügen Sie mithilfe der gleichen Shell, die Sie zum Erstellen des SSH-Schlüsselpaars verwendet haben, den folgenden Befehl in die Shell ein, um eine SSH-Sitzung zu erstellen. Ersetzen Sie *10.111.12.123* durch die IP-Adresse Ihres virtuellen Computers.

```bash
ssh azureuser@10.111.12.123
```

Geben Sie bei Aufforderung den Anmeldebenutzernamen *azureuser* ein. Wenn eine Passphrase mit Ihren SSH-Schlüsseln verwendet wird, müssen Sie diese eingeben, wenn Sie dazu aufgefordert werden.


## <a name="install-nginx"></a>Installieren von NGINX

Wenn Sie den virtuellen Computer in Aktion sehen möchten, installieren Sie den NGINX-Webserver. Aktualisieren Sie über die SSH-Sitzung Ihre Paketquellen, und installieren Sie dann das aktuelle NGINX-Paket.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Geben Sie abschließend `exit` ein, um die SSH-Sitzung zu verlassen.


## <a name="view-the-web-server-in-action"></a>Anzeigen des Webservers in Aktion

Verwenden Sie einen beliebigen Webbrowser, um die Standardwillkommensseite von NGINX anzuzeigen. Geben Sie die öffentliche IP-Adresse Ihres virtuellen Computers als Webadresse ein. Die öffentliche IP-Adresse finden Sie auf der Übersichtsseite für den virtuellen Computer sowie in der weiter oben verwendeten SSH-Verbindungszeichenfolge.

![NGINX-Standardwillkommensseite](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe, die VM und alle dazugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen einfachen virtuellen Computer bereitgestellt, eine Netzwerksicherheitsgruppe und eine Regel erstellt sowie einen einfachen Webserver installiert. Fahren Sie mit dem Tutorial für virtuelle Linux-Computer fort, um weitere Informationen zu virtuellen Azure-Computern zu erhalten.

> [!div class="nextstepaction"]
> [Tutorials für virtuelle Azure Linux-Computer](./tutorial-manage-vm.md)
