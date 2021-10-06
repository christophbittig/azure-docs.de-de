---
title: Trennen einer öffentlichen IP-Adresse von einem virtuellen Azure-Computer
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie eine öffentliche IP-Adresse von einem virtuellen Computer trennen.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: e9bfadd3e2453f0241dc2f7b8bfa5c964333bcf5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368206"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Trennen einer öffentlichen IP-Adresse von einem virtuellen Azure-Computer 

In diesem Artikel erfahren Sie, wie Sie eine öffentliche IP-Adresse von einem virtuellen Azure-Computer (VM) trennen.

Sie können das [Azure-Portal](#azure-portal), die Azure-[Befehlszeilenschnittstelle](#azure-cli) (CLI) oder [PowerShell](#powershell) verwenden, um eine öffentliche IP-Adresse von einer VM zu trennen.

## <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie nach dem virtuellen Computer, dessen öffentliche IP-Adresse Sie trennen möchten, und wählen Sie ihn aus.
3. Wählen Sie auf der Seite des virtuellen Computers **Übersicht** und dann die öffentliche IP-Adresse aus, wie in der folgenden Abbildung dargestellt:

   ![Auswählen der öffentlichen IP-Adresse](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Wählen Sie auf der Seite der öffentlichen IP-Adresse **Übersicht** und dann **Trennen** aus, wie in der folgenden Abbildung dargestellt:

    ![Trennen der öffentlichen IP-Adresse](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. Wählen Sie unter **Öffentliche IP-Adresse trennen** die Option **Ja** aus.

## <a name="azure-cli"></a>Azure CLI

Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), oder verwenden Sie Azure Cloud Shell. Azure Cloud Shell ist eine kostenlose Bash-Shell, die Sie direkt im Azure-Portal ausführen können. Die Azure CLI ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Klicken Sie in den folgenden CLI-Beispielen auf die Schaltfläche **Jetzt testen**. Durch den Klick auf **Jetzt testen** wird eine Cloud Shell aufgerufen, bei der Sie sich mit Ihrem Azure-Konto anmelden können.

1. Wenn Sie die CLI lokal in Bash verwenden, melden Sie sich mit `az login` bei Azure an.
2. Eine öffentliche IP-Adresse ist der IP-Konfiguration einer Netzwerkschnittstelle zugeordnet, die an einen virtuellen Computer angefügt ist. Verwenden Sie den Befehl [az network nic-ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update), um eine öffentliche IP-Adresse von einer IP-Konfiguration zu trennen. Das folgende Beispiel trennt die öffentliche IP-Adresse *myVMPublicIP* von der IP-Konfiguration *ipconfigmyVM* der vorhandenen Netzwerkschnittstelle *myVMVMNic*, die an die VM *myVM* in der Ressourcengruppe *myResourceGroup* angefügt ist.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Wenn Sie die Namen der an Ihren virtuellen Computer angefügten Netzwerkschnittstellen nicht kennen, verwenden Sie den Befehl [az vm nic list](/cli/azure/vm/nic#az_vm_nic_list), um die Netzwerkschnittstellen anzuzeigen. Der folgende Befehl listet z.B. die Namen der Netzwerkschnittstellen auf, die an den virtuellen Computer namens *myVM* in der Ressourcengruppe namens *myResourceGroup* angefügt sind:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Die Ausgabe enthält eine oder mehrere Zeilen ähnlich dem folgenden Beispiel:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Im oben genannten Beispiel lautet der Name der Netzwerkschnittstelle *myVMVMNic*.

   - Wenn Sie die Namen der IP-Konfigurationen für eine Netzwerkschnittstelle nicht kennen, verwenden Sie den Befehl [az network nic ip-config list](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list), um die Konfigurationen abzurufen. Der folgende Befehl listet beispielsweise die Namen der öffentlichen IP-Konfigurationen auf, die für die Netzwerkschnittstelle *myVMVMNic* in der Ressourcengruppe *myResourceGroup* vorhanden sind:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Wenn Sie die Namen einer öffentlichen IP-Konfiguration für eine Netzwerkschnittstelle nicht kennen, verwenden Sie den Befehl [az network nic ip-config show](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_show), um die Konfigurationen abzurufen. Der folgende Befehl listet beispielsweise die Namen der öffentlichen IP-Konfigurationen auf, die für die Netzwerkschnittstelle *myVMVMNic* in der Ressourcengruppe *myResourceGroup* vorhanden sind:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Installieren Sie [PowerShell](/powershell/azure/install-az-ps), oder verwenden Sie Azure Cloud Shell. Azure Cloud Shell ist eine kostenlose Shell, die Sie direkt im Azure-Portal ausführen können. PowerShell ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Klicken Sie in den folgenden PowerShell-Beispielen auf die Schaltfläche **Jetzt testen**. Durch den Klick auf **Jetzt testen** wird eine Cloud Shell aufgerufen, bei der Sie sich mit Ihrem Azure-Konto anmelden können.

1. Wenn Sie PowerShell lokal verwenden, melden Sie sich mit `Connect-AzAccount` bei Azure an.
2. Eine öffentliche IP-Adresse ist der IP-Konfiguration einer Netzwerkschnittstelle zugeordnet, die an einen virtuellen Computer angefügt ist. Verwenden Sie den Befehl [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface), um eine Netzwerkschnittstelle abzurufen. Legen Sie den Wert für die öffentliche IP-Adresse auf NULL fest, und verwenden Sie dann den Befehl [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface), um die neue IP-Konfiguration in die Netzwerkschnittstelle zu schreiben.

   Im folgenden Beispiel wird die öffentliche IP-Adresse *myVMPublicIP* von der Netzwerkschnittstelle *myVMVMNic* getrennt, die an den virtuellen Computer *myVM* angefügt ist. Alle Ressourcen befinden sich in der Ressourcengruppe namens *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Wenn Sie die Namen der an Ihren virtuellen Computer angefügten Netzwerkschnittstellen nicht kennen, verwenden Sie den Befehl [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM), um die Netzwerkschnittstellen anzuzeigen. Der folgende Befehl listet z.B. die Namen der Netzwerkschnittstellen auf, die an den virtuellen Computer namens *myVM* in der Ressourcengruppe namens *myResourceGroup* angefügt sind:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Die Ausgabe enthält eine oder mehrere Zeilen ähnlich dem folgenden Beispiel. In der Beispielausgabe lautet der Name der Netzwerkschnittstelle *myVMVMNic*.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Wenn Sie die Namen der IP-Konfigurationen für eine Netzwerkschnittstelle nicht kennen, verwenden Sie den Befehl [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface), um die Konfigurationen abzurufen. Der folgende Befehl listet z.B. die Namen der IP-Konfigurationen auf, die für die Netzwerkschnittstelle namens *myVMVMNic* in der Ressourcengruppe namens *myResourceGroup* vorhanden sind:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Die Ausgabe enthält eine oder mehrere Zeilen ähnlich dem folgenden Beispiel. In der Beispielausgabe ist *ipconfigmyVM* der Name einer IP-Konfiguration.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [einer VM eine öffentliche IP-Adresse zuordnen](associate-public-ip-address-vm.md).
