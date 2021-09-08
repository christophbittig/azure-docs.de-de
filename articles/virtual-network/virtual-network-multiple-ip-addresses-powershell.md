---
title: 'Mehrere IP-Adressen für virtuelle Azure-Computer: PowerShell | Microsoft-Dokumentation'
description: Informationen zum Zuweisen mehrerer IP-Adressen zu einem virtuellen Computer mit PowerShell. | Resource Manager
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: allensu
ms.openlocfilehash: 721119cd8927ee78639dd0e1449a36deb82938ef
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397119"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Zuweisen von mehreren IP-Adressen zu virtuellen Computern mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

In diesem Artikel wird beschrieben, wie Sie über das Azure Resource Manager-Bereitstellungsmodell mithilfe von PowerShell einen virtuellen Computer erstellen. Einer einzelnen NIC, die mit dem klassischen Bereitstellungsmodell erstellt wurde, können nicht mehrere IP-Adressen zugewiesen werden, obwohl eine klassische VM über mehrere NICs verfügen kann, die jeweils eine eigene IP-Adresse besitzen. Weitere Informationen zu den Azure-Bereitstellungsmodellen finden Sie im Artikel zum Thema [Understand deployment models (Bereitstellungsmodelle verstehen)](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Erstellen eines virtuellen Computers mit mehreren IP-Adressen

In den folgenden Schritten wird beschrieben, wie gemäß dem Szenario beispielhaft ein virtueller Computer mit mehreren IP-Adressen erstellt werden kann. Ändern Sie die Variablenwerte gemäß den Anforderungen Ihrer Implementierung.

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, und führen Sie die restlichen Schritte in diesem Abschnitt innerhalb einer einzelnen PowerShell-Sitzung aus. Wenn Sie PowerShell nicht installiert und konfiguriert haben, befolgen Sie die Anweisungen im Artikel [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/) .
2. Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Konto an.
3. Ersetzen Sie *myResourceGroup* und *westus* durch einen Namen und Standort Ihrer Wahl. Erstellen Sie eine Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Erstellen Sie ein virtuelles Netzwerk und Subnetz am gleichen Standort, in dem sich auch Ihre Ressourcengruppe befindet:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Erstellen Sie eine Netzwerksicherheitsgruppe (NSG) und eine Regel. Die Netzwerksicherheitsgruppe sichert den virtuellen Computer mithilfe von eingehenden und ausgehenden Regeln. In diesem Fall wird eine Regel für eingehenden Datenverkehr für Port 3389 erstellt, die eingehende Remotedesktopverbindungen zulässt.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Definieren Sie die primäre IP-Konfiguration für die Netzwerkkarte. Ändern Sie in dem von Ihnen erstellten Subnetz die Adresse 10.0.0.4 in eine gültige Adresse, wenn Sie nicht den zuvor definierten Wert verwendet haben. Bevor Sie eine statische IP-Adresse zuweisen, sollten Sie überprüfen, ob diese nicht bereits verwendet wird. Geben Sie den Befehl `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet` ein. Wenn die Adresse verfügbar ist, gibt die Ausgabe *TRUE* zurück. Wenn sie nicht verfügbar ist, gibt die Ausgabe *FALSE* und eine Liste der verfügbaren Adressen zurück. 

    Ersetzen Sie in den folgenden Befehlen **\<replace-with-your-unique-name>** durch den zu verwendenden eindeutigen DNS-Namen. Der Name muss über alle öffentlichen IP-Adressen innerhalb einer Azure-Region eindeutig sein. Dies ist ein optionaler Parameter. Er kann entfernt werden, wenn Sie Verbindungen zum virtuellen Computer nur über die öffentliche IP-Adresse herstellen möchten.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Wenn Sie einer Netzwerkkarte mehrere IP-Konfigurationen zuweisen, muss eine Konfiguration mit dem Wert *-Primary* zugewiesen werden.

    > [!NOTE]
    > Für öffentliche IP-Adressen fällt eine geringe Gebühr an. Weitere Informationen zu den Preisen finden Sie auf der Seite [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses) . Die Anzahl der öffentlichen IP-Adressen, die in einem Abonnement verwendet werden können, ist beschränkt. Weitere Informationen über die Einschränkungen finden Sie im Artikel zu den [Azure-Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) .

7. Definieren Sie die sekundären IP-Konfigurationen für die Netzwerkkarte. Sie können Konfigurationen je nach Bedarf hinzufügen oder entfernen. Jeder IP-Konfiguration muss eine private IP-Adresse zugewiesen sein. Jeder Konfiguration kann optional eine einzige öffentliche IP-Adresse zugewiesen sein.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Erstellen Sie die Netzwerkkarte, und weisen Sie ihr die drei IP-Konfigurationen zu:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >In diesem Artikel werden alle Konfigurationen einer einzigen Netzwerkkarte zugewiesen, aber Sie können jeder an den virtuellen Computer angefügten Netzwerkkarte mehrere IP-Konfigurationen zuweisen. Erfahren Sie im Artikel [Bereitstellen von Multi-NIC-VMs mit PowerShell](../virtual-machines/windows/multiple-nics.md), wie Sie einen virtuellen Computer mit mehreren NICs erstellen.

9. Erstellen Sie den virtuellen Computer mithilfe der folgenden Befehle:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Fügen Sie die privaten IP-Adressen dem Betriebssystem des virtuellen Computers hinzu. Führen Sie dazu die Schritte für Ihr Betriebssystem im Abschnitt [Hinzufügen von IP-Adressen zu einem VM-Betriebssystem](#os-config) in diesem Artikel aus. Fügen Sie dem Betriebssystem nicht die öffentlichen IP-Adressen hinzu.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Hinzufügen von IP-Adressen zu einem virtuellen Computer

Sie können einer Azure-Netzwerkschnittstelle private und öffentliche IP-Adressen hinzufügen. Führen Sie dazu die folgenden Schritte aus. In den Beispielen in den folgenden Abschnitten wird davon ausgegangen, dass Sie bereits einen virtuellen Computer mit den im [Szenario](#scenario) in diesem Artikel beschriebenen drei IP-Konfigurationen erstellt haben, dies ist jedoch nicht erforderlich.

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, und führen Sie die restlichen Schritte in diesem Abschnitt innerhalb einer einzelnen PowerShell-Sitzung aus. Wenn Sie PowerShell nicht installiert und konfiguriert haben, befolgen Sie die Anweisungen im Artikel [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/) .
2. Ändern Sie die Werte der folgenden Variablen in den Namen der NIC, der Sie IP-Adressen hinzufügen möchten, und der Ressourcengruppe und des Standorts, in der bzw. an dem die NIC vorhanden ist:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Wenn Sie den Namen der zu ändernden NIC nicht kennen, geben Sie die folgenden Befehle ein. Ändern Sie dann die Werte der vorherigen Variablen:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Erstellen Sie mithilfe des folgenden Befehls eine Variable, und legen Sie sie auf die vorhandene NIC fest:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. Ändern Sie in den folgenden Befehlen die Werte *MyVNet* und *MySubnet* in die Namen des virtuellen Netzwerks und Subnetzes, mit denen die Netzwerkkarte verbunden ist. Geben Sie die Befehle ein, um das VNET und die Subnetzobjekte, mit denen die NIC verbunden ist, aufzurufen:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Wenn Sie den Namen des VNET oder Subnetzes nicht kennen, mit denen die NIC verbunden ist, geben Sie den folgenden Befehl ein:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Suchen Sie in der Ausgabe nach Text, der dem Text in der folgenden Beispielausgabe ähnelt:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    In dieser Ausgabe ist *MyVnet* das virtuelle Netzwerk und *MySubnet* das Subnetz, mit dem die Netzwerkkarte verbunden ist.

5. Führen Sie gemäß Ihren Anforderungen die Schritte eines der folgenden Abschnitte aus:

   **Hinzufügen einer privaten IP-Adresse**

   Um eine private IP-Adresse zu einer NIC hinzuzufügen, müssen Sie eine IP-Konfiguration erstellen. Der folgende Befehl erstellt eine Konfiguration mit der statischen IP-Adresse 10.0.0.7. Wenn Sie eine statische IP-Adresse angeben, darf es sich dabei nicht um eine für das Subnetz verwendete Adresse handeln. Es wird empfohlen, dass Sie die Adresse zuerst auf Ihre Verfügbarkeit testen, indem Sie den Befehl `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` eingeben. Wenn die IP-Adresse verfügbar ist, gibt die Ausgabe *TRUE* zurück. Wenn sie nicht verfügbar ist, gibt die Ausgabe *FALSE* und eine Liste der verfügbaren Adressen zurück.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Sie können so viele Konfigurationen wie benötigt erstellen. Verwenden Sie dabei eindeutige Namen für die Konfigurationen und privaten IP-Adressen (bei Konfigurationen mit statischen IP-Adressen).

   Fügen Sie die private IP-Adresse dem Betriebssystem des virtuellen Computers hinzu. Führen Sie dazu die Schritte für Ihr Betriebssystem im Abschnitt [Hinzufügen von IP-Adressen zu einem VM-Betriebssystem](#os-config) in diesem Artikel aus.

   **Hinzufügen einer öffentlichen IP-Adresse**

   Eine öffentliche IP-Adresse wird hinzugefügt, indem eine öffentliche IP-Adressressource entweder einer neuen oder einer vorhandenen IP-Konfiguration zugeordnet wird. Führen Sie gemäß Ihren Anforderungen die Schritte des entsprechenden Abschnitts durch.

   > [!NOTE]
   > Für öffentliche IP-Adressen fällt eine geringe Gebühr an. Weitere Informationen zu den Preisen finden Sie auf der Seite [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses) . Die Anzahl der öffentlichen IP-Adressen, die in einem Abonnement verwendet werden können, ist beschränkt. Weitere Informationen über die Einschränkungen finden Sie im Artikel zu den [Azure-Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) .
   >

   **Zuordnen der öffentlichen IP-Adressressource zu einer neuen IP-Konfiguration**

   Immer, wenn Sie in einer neuen IP-Konfiguration eine öffentliche IP-Adresse hinzufügen, müssen Sie auch eine private IP-Adresse hinzufügen, denn in jeder IP-Konfiguration muss es eine private IP-Adresse geben. Sie können entweder eine vorhandene öffentliche IP-Adressressource hinzufügen oder eine neue erstellen. Verwenden Sie den folgenden Befehl, um eine neue zu erstellen:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Um eine neue IP-Konfiguration mit einer statischen privaten IP-Adresse und der zugeordneten öffentlichen IP-Adressressource *myPublicIp3* zu erstellen, geben Sie den folgenden Befehl ein:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Zuordnen der öffentlichen IP-Adressressource zu einer vorhandenen IP-Konfiguration**

   Eine öffentliche IP-Adressressource kann nur einer IP-Konfiguration zugeordnet werden, der noch keine öffentliche IP-Adressressource zugeordnet ist. Ob einer IP-Konfiguration eine öffentliche IP-Adresse zugeordnet ist, können Sie mithilfe des folgenden Befehls ermitteln:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Ihnen wird daraufhin eine Ausgabe angezeigt, die in etwa wie folgt aussieht:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Da die Spalte **PublicIpAddress (öffentliche IP-Adresse)** für *IpConfig-3* leer ist, ist derzeit keine öffentliche IP-Adressressource zugeordnet. Sie können IpConfig-3 eine vorhandene öffentliche IP-Adressressource zuordnen oder mit dem folgenden Befehl eine erstellen:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Geben Sie den folgenden Befehl ein, um die öffentliche IP-Adressressource der vorhandenen IP-Konfiguration namens *IpConfig-3* zuzuordnen:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Geben Sie den folgenden Befehl ein, um die NIC mit der IP-Konfiguration festzulegen:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Geben Sie den folgenden Befehl ein, um die privaten IP-Adressen und die der NIC zugewiesene öffentliche IP-Adressressource anzuzeigen:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Fügen Sie die private IP-Adresse dem Betriebssystem des virtuellen Computers hinzu. Führen Sie dazu die Schritte für Ihr Betriebssystem im Abschnitt [Hinzufügen von IP-Adressen zu einem VM-Betriebssystem](#os-config) in diesem Artikel aus. Fügen Sie dem Betriebssystem nicht die öffentliche IP-Adresse hinzu.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
