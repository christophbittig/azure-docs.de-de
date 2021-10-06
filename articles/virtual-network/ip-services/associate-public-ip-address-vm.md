---
title: Zuordnen einer öffentlichen IP-Adresse zu einem virtuellen Computer
titlesuffix: Azure Virtual Network
description: Hier erfahren Sie, wie Sie einem virtuellen Computer (VM) mithilfe des Azure-Portals oder der Azure CLI eine öffentliche IP-Adresse zuordnen.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: dfaaaf4239e5479b53ebc696f1efe72472b302ef
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368209"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Zuordnen einer öffentlichen IP-Adresse zu einem virtuellen Computer

In diesem Artikel erfahren Sie, wie Sie einem vorhandenen virtuellen Computer eine öffentliche IP-Adresse zuordnen. Wenn Sie einen virtuellen Computer mit dem Internet verbinden möchten, muss dem Computer eine öffentliche IP-Adresse zugeordnet sein. Wenn Sie einen neuen virtuellen Computer mit einer öffentlichen IP-Adresse erstellen möchten, können Sie dafür das [Azure-Portal](virtual-network-deploy-static-pip-arm-portal.md), die [Azure-Befehlszeilenschnittstelle (CLI)](virtual-network-deploy-static-pip-arm-cli.md) oder [PowerShell](virtual-network-deploy-static-pip-arm-ps.md) verwenden. Für öffentliche IP-Adressen fällt eine geringe Gebühr an. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/ip-addresses/). Die Anzahl der öffentlichen IP-Adressen, die pro Abonnement verwendet werden können, ist begrenzt. Informationen dazu finden unter [Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Sie können das [Azure-Portal](#azure-portal), die [Azure CLI](#azure-cli) oder [PowerShell](#powershell) verwenden, um einem virtuellen Computer eine öffentliche IP-Adresse zuzuordnen.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie nach dem virtuellen Computer, dem Sie eine öffentliche IP-Adresse hinzufügen möchten, und wählen Sie ihn aus.
3. Wählen Sie unter **Einstellungen** die Option **Netzwerk** aus, und wählen Sie dann die Netzwerkschnittstelle aus, der Sie die öffentliche IP-Adresse hinzufügen möchten, wie in der folgenden Abbildung gezeigt:

   ![Auswählen der Netzwerkschnittstelle](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Öffentliche IP-Adressen werden Netzwerkschnittstellen zugeordnet, die an einen virtuellen Computer angefügt sind. In der obigen Abbildung besitzt der virtuelle Computer nur eine Netzwerkschnittstelle. Wenn ein virtueller Computer über mehrere Netzwerkschnittstellen verfügt, werden alle angezeigt, und Sie wählen diejenige Schnittstelle aus, der Sie die öffentliche IP-Adresse zuordnen möchten.

4. Klicken Sie auf **IP-Konfigurationen**, und wählen Sie eine IP-Konfiguration aus, wie in der folgenden Abbildung gezeigt:

   ![Auswählen der IP-Konfiguration](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Öffentliche IP-Adressen werden IP-Konfigurationen für eine Netzwerkschnittstelle zugeordnet. In der obigen Abbildung weist die Netzwerkschnittstelle nur eine IP-Konfiguration auf. Wenn eine Netzwerkschnittstelle mehrere IP-Konfigurationen aufweist, werden alle angezeigt, und Sie wählen diejenige Konfiguration aus, der Sie die öffentliche IP-Adresse zuordnen möchten.

5. Klicken Sie auf **Aktiviert**, und wählen Sie dann **IP-Adresse (*Erforderliche Einstellungen konfigurieren*)** aus. Wählen Sie eine vorhandene öffentliche IP-Adresse aus. Dadurch wird das Feld **Öffentliche IP-Adresse auswählen** automatisch geschlossen. Wenn keine verfügbaren öffentlichen IP-Adressen aufgelistet werden, müssen Sie eine Adresse erstellen. Informationen dazu finden Sie unter [Erstellen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md#create-a-public-ip-address). Klicken Sie auf **Speichern**, wie in der folgenden Abbildung gezeigt, und schließen Sie das Feld für die IP-Konfiguration.

   ![Aktivieren der öffentlichen IP-Adresse](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Bei den angezeigten öffentlichen IP-Adressen handelt es sich um diejenigen Adressen, die in der gleichen Region vorhanden sind wie der virtuelle Computer. Wenn in der Region mehrere öffentliche IP-Adressen erstellt wurde, werden alle hier angezeigt. Wenn eine Adresse abgeblendet dargestellt wird, liegt dies daran, dass diese Adresse bereits einer anderen Ressource zugeordnet ist.

6. Zeigen Sie die öffentliche IP-Adresse an, die der IP-Konfiguration zugewiesen ist, wie in der folgenden Abbildung dargestellt. Möglicherweise dauert es einige Sekunden, bis eine IP-Adresse angezeigt wird.

   ![Anzeigen zugewiesener öffentlicher IP-Adressen](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Die Adresse wird aus einem Adresspool zugewiesen, der in der jeweiligen Azure-Region verwendet wird. Eine Liste der in den einzelnen Regionen verwendeten Adresspools finden Sie hier: [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (IP-Adressbereiche für Microsoft Azure-Rechenzentren). Es kann jede beliebige Adresse aus den in der entsprechenden Region verwendeten Pools zugewiesen werden. Wenn die Adresse aus einem bestimmten Pool in der Region stammen muss, verwenden Sie ein [Präfix für öffentliche IP-Adressen](public-ip-address-prefix.md).

7. [Lassen Sie Netzwerkdatenverkehr zum virtuellen Computer zu](#allow-network-traffic-to-the-vm), indem Sie Sicherheitsregeln in einer Netzwerksicherheitsgruppe verwenden.

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), oder verwenden Sie Azure Cloud Shell. Azure Cloud Shell ist eine kostenlose Bash-Shell, die Sie direkt im Azure-Portal ausführen können. Die Azure CLI ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Klicken Sie in den folgenden CLI-Beispielen auf die Schaltfläche **Jetzt testen**. Durch den Klick auf **Jetzt testen** wird eine Cloud Shell aufgerufen, bei der Sie sich mit Ihrem Azure-Konto anmelden können.

1. Wenn Sie die CLI lokal in Bash verwenden, melden Sie sich mit `az login` bei Azure an.
2. Eine öffentliche IP-Adresse ist der IP-Konfiguration einer Netzwerkschnittstelle zugeordnet, die an einen virtuellen Computer angefügt ist. Verwenden Sie den Befehl [az network nic-ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update), um einer IP-Konfiguration eine öffentliche IP-Adresse zuzuordnen. Das folgende Beispiel ordnet die vorhandene öffentliche IP-Adresse *myVMPublicIP* zur IP-Konfiguration *ipconfigmyVM* der vorhandenen Netzwerkschnittstelle *myVMVMNic* zu, die sich in der Ressourcengruppe *myResourceGroup* befindet.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Wenn Sie nicht über eine vorhandene öffentliche IP-Adresse verfügen, verwenden Sie den Befehl [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um eine solche Adresse zu erstellen. Der folgende Befehl beispielsweise erstellt eine öffentliche IP-Adresse mit dem Namen *myVMPublicIP* in der Ressourcengruppe *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Dieser Befehl erstellt eine öffentliche IP-Adresse mit Standardwerten für verschiedene Einstellungen, die Sie möglicherweise anpassen möchten. Weitere Informationen zu den Einstellungen für öffentliche IP-Adressen finden Sie unter [Erstellen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md#create-a-public-ip-address). Die Adresse wird aus einem Pool mit öffentlichen IP-Adressen zugewiesen, der für die jeweilige Azure-Region verwendet wird. Eine Liste der in den einzelnen Regionen verwendeten Adresspools finden Sie hier: [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (IP-Adressbereiche für Microsoft Azure-Rechenzentren).

   - Wenn Sie die Namen der an Ihren virtuellen Computer angefügten Netzwerkschnittstellen nicht kennen, verwenden Sie den Befehl [az vm nic list](/cli/azure/vm/nic#az_vm_nic_list), um die Netzwerkschnittstellen anzuzeigen. Der folgende Befehl listet z.B. die Namen der Netzwerkschnittstellen auf, die an den virtuellen Computer namens *myVM* in der Ressourcengruppe namens *myResourceGroup* angefügt sind:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Die Ausgabe enthält eine oder mehrere Zeilen ähnlich dem folgenden Beispiel:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Im oben genannten Beispiel lautet der Name der Netzwerkschnittstelle *myVMVMNic*.

   - Wenn Sie die Namen der IP-Konfigurationen für eine Netzwerkschnittstelle nicht kennen, verwenden Sie den Befehl [az network nic ip-config list](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list), um die Konfigurationen abzurufen. Der folgende Befehl listet z.B. die Namen der IP-Konfigurationen auf, die für die Netzwerkschnittstelle namens *myVMVMNic* in der Ressourcengruppe namens *myResourceGroup* vorhanden sind:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Verwenden Sie den Befehl [az vm list-ip-addresses](/cli/azure/vm#az_vm_list_ip_addresses), um die öffentlichen IP-Adressen anzuzeigen, die der IP-Konfiguration zugewiesen sind. Das folgende Beispiel zeigt die IP-Adressen, die dem vorhandenen virtuellen Computer *myVM* in der Ressourcengruppe namens *myResourceGroup* zugewiesen sind.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Die Adresse wird aus einem Adresspool zugewiesen, der in der jeweiligen Azure-Region verwendet wird. Eine Liste der in den einzelnen Regionen verwendeten Adresspools finden Sie hier: [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (IP-Adressbereiche für Microsoft Azure-Rechenzentren). Es kann jede beliebige Adresse aus den in der entsprechenden Region verwendeten Pools zugewiesen werden. Wenn die Adresse aus einem bestimmten Pool in der Region stammen muss, verwenden Sie ein [Präfix für öffentliche IP-Adressen](public-ip-address-prefix.md).

4. [Lassen Sie Netzwerkdatenverkehr zum virtuellen Computer zu](#allow-network-traffic-to-the-vm), indem Sie Sicherheitsregeln in einer Netzwerksicherheitsgruppe verwenden.

## <a name="powershell"></a>PowerShell

Installieren Sie [PowerShell](/powershell/azure/install-az-ps), oder verwenden Sie Azure Cloud Shell. Azure Cloud Shell ist eine kostenlose Shell, die Sie direkt im Azure-Portal ausführen können. PowerShell ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Klicken Sie in den folgenden PowerShell-Beispielen auf die Schaltfläche **Jetzt testen**. Durch den Klick auf **Jetzt testen** wird eine Cloud Shell aufgerufen, bei der Sie sich mit Ihrem Azure-Konto anmelden können.

1. Wenn Sie PowerShell lokal verwenden, melden Sie sich mit `Connect-AzAccount` bei Azure an.
2. Eine öffentliche IP-Adresse ist der IP-Konfiguration einer Netzwerkschnittstelle zugeordnet, die an einen virtuellen Computer angefügt ist. Verwenden Sie die Befehle [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) und [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig), um das virtuelle Netzwerk und das zugehörige Subnetz abzurufen, in dem sich die Netzwerkschnittstelle befindet. Verwenden Sie danach den Befehl [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface), um eine Netzwerkschnittstelle abzurufen, und den Befehl [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), um eine vorhandene öffentliche IP-Adresse abzurufen. Anschließend verwenden Sie den Befehl [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig), um die öffentliche IP-Adresse zur IP-Konfiguration zuzuordnen. Mit dem Befehl [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) schreiben Sie die neue IP-Konfiguration in die Netzwerkschnittstelle.

   Das folgende Beispiel ordnet die vorhandene öffentliche IP-Adresse *myVMPublicIP* zur IP-Konfiguration *ipconfigmyVM* der vorhandenen Netzwerkschnittstelle *myVMVMNic* zu, die sich im Subnetz *myVMSubnet* des virtuellen Netzwerks *myVMVNet* befindet. Alle Ressourcen befinden sich in der Ressourcengruppe namens *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Wenn Sie nicht über eine vorhandene öffentliche IP-Adresse verfügen, verwenden Sie den Befehl [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress), um eine solche Adresse zu erstellen. Der folgende Befehl beispielsweise erstellt eine *dynamische* öffentliche IP-Adresse mit dem Namen *myVMPublicIP* in der Ressourcengruppe *myResourceGroup* in der Region *eastus*.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Dieser Befehl erstellt eine öffentliche IP-Adresse mit Standardwerten für verschiedene Einstellungen, die Sie möglicherweise anpassen möchten. Weitere Informationen zu den Einstellungen für öffentliche IP-Adressen finden Sie unter [Erstellen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md#create-a-public-ip-address). Die Adresse wird aus einem Pool mit öffentlichen IP-Adressen zugewiesen, der für die jeweilige Azure-Region verwendet wird. Eine Liste der in den einzelnen Regionen verwendeten Adresspools finden Sie hier: [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (IP-Adressbereiche für Microsoft Azure-Rechenzentren).

   - Wenn Sie die Namen der an Ihren virtuellen Computer angefügten Netzwerkschnittstellen nicht kennen, verwenden Sie den Befehl [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM), um die Netzwerkschnittstellen anzuzeigen. Der folgende Befehl listet z.B. die Namen der Netzwerkschnittstellen auf, die an den virtuellen Computer namens *myVM* in der Ressourcengruppe namens *myResourceGroup* angefügt sind:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Die Ausgabe enthält eine oder mehrere Zeilen ähnlich dem folgenden Beispiel. In der Beispielausgabe lautet der Name der Netzwerkschnittstelle *myVMVMNic*.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Wenn Sie den Namen des virtuellen Netzwerks bzw. des Subnetzes nicht kennen, in dem sich die Netzwerkschnittstelle befindet, verwenden Sie den Befehl `Get-AzNetworkInterface`, um die Informationen anzuzeigen. Der folgende Befehl ruft z.B. die Informationen zum virtuellen Netzwerk und Subnetz für die Netzwerkschnittstelle namens *myVMVMNic* in der Ressourcengruppe namens *myResourceGroup* ab:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Die Ausgabe enthält eine oder mehrere Zeilen ähnlich dem folgenden Beispiel. In der Beispielausgabe ist *myVMVNET* der Name des virtuellen Netzwerks, und *myVMSubnet* ist der Name des Subnetzes.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Wenn Sie die Namen der IP-Konfigurationen für eine Netzwerkschnittstelle nicht kennen, verwenden Sie den Befehl [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface), um die Konfigurationen abzurufen. Der folgende Befehl listet z.B. die Namen der IP-Konfigurationen auf, die für die Netzwerkschnittstelle namens *myVMVMNic* in der Ressourcengruppe namens *myResourceGroup* vorhanden sind:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Die Ausgabe enthält eine oder mehrere Zeilen ähnlich dem folgenden Beispiel. In der Beispielausgabe ist *ipconfigmyVM* der Name einer IP-Konfiguration.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Verwenden Sie den Befehl [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), um die öffentlichen IP-Adressen anzuzeigen, die der IP-Konfiguration zugewiesen sind. Das folgende Beispiel zeigt die Adresse, die der öffentlichen IP-Adresse *myVMPublicIP* in der Ressourcengruppe *myResourceGroup* zugewiesen ist.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Wenn Sie den Namen der öffentlichen IP-Adresse nicht kennen, die einer IP-Konfiguration zugewiesen ist, führen Sie die folgenden Befehle aus, um die Adresse abzurufen:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Die Ausgabe enthält eine oder mehrere Zeilen ähnlich dem folgenden Beispiel. In der Beispielausgabe ist *myVMPublicIP* der Name der öffentlichen IP-Adresse, die der IP-Konfiguration zugewiesen ist.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Die Adresse wird aus einem Adresspool zugewiesen, der in der jeweiligen Azure-Region verwendet wird. Eine Liste der in den einzelnen Regionen verwendeten Adresspools finden Sie hier: [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (IP-Adressbereiche für Microsoft Azure-Rechenzentren). Es kann jede beliebige Adresse aus den in der entsprechenden Region verwendeten Pools zugewiesen werden. Wenn die Adresse aus einem bestimmten Pool in der Region stammen muss, verwenden Sie ein [Präfix für öffentliche IP-Adressen](public-ip-address-prefix.md).

4. [Lassen Sie Netzwerkdatenverkehr zum virtuellen Computer zu](#allow-network-traffic-to-the-vm), indem Sie Sicherheitsregeln in einer Netzwerksicherheitsgruppe verwenden.

## <a name="allow-network-traffic-to-the-vm"></a>Zulassen von Netzwerkdatenverkehr zum virtuellen Computer

Bevor Sie eine Internetverbindung mit der öffentlichen IP-Adresse herstellen können, müssen Sie sicherstellen, dass die erforderlichen Ports geöffnet sind. Dies gilt für alle der Netzwerkschnittstelle zugeordneten Netzwerksicherheitsgruppen bzw. das Subnetz, in dem sich die Netzwerkschnittstelle befindet. Obwohl Sicherheitsgruppen den Datenverkehr an die private IP-Adresse einer Netzwerkschnittstelle filtern, übersetzt Azure die öffentliche IP-Adresse in eine private IP-Adresse, sobald Internetdatenverkehr bei der öffentlichen IP-Adresse eingeht. Wenn also eine Netzwerksicherheitsgruppe den Datenfluss verhindert, tritt bei der Kommunikation mit der öffentlichen IP-Adresse ein Fehler auf. Sie können die Sicherheitsregeln, die für eine Netzwerkschnittstelle und das zugehörige Subnetz angewendet werden, im [Portal](../../virtual-network/diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), mit der [CLI](../../virtual-network/diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) oder mithilfe von [PowerShell](../../virtual-network/diagnose-network-traffic-filter-problem.md#diagnose-using-powershell) anzeigen.

## <a name="next-steps"></a>Nächste Schritte

Lassen Sie eingehenden Datenverkehr an Ihren virtuellen Computer mithilfe einer Netzwerksicherheitsgruppe zu. Informationen zum Erstellen einer Netzwerksicherheitsgruppe finden Sie unter [Arbeiten mit Netzwerksicherheitsgruppen](../../virtual-network/manage-network-security-group.md#work-with-network-security-groups). Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Sicherheitsgruppen](../../virtual-network/network-security-groups-overview.md).
