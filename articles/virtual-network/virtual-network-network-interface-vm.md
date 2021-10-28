---
title: 'Azure-VMs: Hinzufügen oder Entfernen von Netzwerkschnittstellen'
description: Erfahren Sie, wie Sie Netzwerkschnittstellen zu virtuellen Computern hinzufügen oder aus diesen entfernen können.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: fcdc5c86c1b5cf39fb4140afc1db661db8ead11d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224827"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Hinzufügen von Netzwerkschnittstellen zu virtuellen Computern oder Entfernen von Netzwerkschnittstellen aus diesen

Hier erfahren Sie, wie Sie beim Erstellen eines virtuellen Azure-Computers (Virtual Machine, VM) eine vorhandene Netzwerkschnittstelle hinzufügen. Außerdem erfahren Sie, wie Sie einem vorhandenen virtuellen Computer im Zustand „Beendet (Zuordnung aufgehoben)“ Netzwerkschnittstellen hinzufügen oder von diesem entfernen. Eine Netzwerkschnittstelle ermöglicht einem virtuellen Azure-Computer die Kommunikation mit dem Internet sowie mit Azure und lokalen Ressourcen. Ein virtueller Computer kann über eine oder mehrere Netzwerkschnittstellen verfügen. 

Weitere Informationen zum Hinzufügen, Ändern oder Entfernen von IP-Adressen für eine Netzwerkschnittstelle finden Sie unter [Verwalten von IP-Adressen für Netzwerkschnittstellen](./ip-services/virtual-network-network-interface-addresses.md). Informationen zum Erstellen, Ändern oder Löschen von Netzwerkschnittstellen finden Sie unter [Verwalten von Netzwerkschnittstellen](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Falls Sie noch nicht über ein Azure-Konto verfügen, richten Sie ein Azure-Konto mit einem aktiven Abonnement ein. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Führen Sie eine dieser Aufgaben aus, bevor Sie mit dem Rest dieses Artikels starten:

- **Portalbenutzer**: Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

- **PowerShell-Benutzer:** Führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder in PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Wechseln Sie auf der Browserregisterkarte „Azure Cloud Shell“ zur Dropdownliste **Umgebung auswählen**, und wählen Sie dann **PowerShell** aus, falls nicht bereits ausgewählt.

    Wenn Sie PowerShell lokal ausführen, müssen Sie mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az.Network` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Führen Sie zum Starten `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

- **Azure CLI-Benutzer**: Führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder über die CLI auf Ihrem Computer aus. Verwenden Sie bei lokaler Ausführung von Azure CLI die Version 2.0.26 oder höher. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Führen Sie zum Starten `az login` aus, um eine Verbindung mit Azure herzustellen.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Hinzufügen vorhandener Netzwerkschnittstellen zu einer neuen VM

Wenn Sie einen virtuellen Computer im Portal erstellen, wird eine Netzwerkschnittstelle mit Standardeinstellungen erstellt, und die Netzwerkschnittstelle wird an den virtuellen Computer angefügt. Im Portal ist es nicht möglich, einem neuen virtuellen Computer vorhandene Netzwerkschnittstellen hinzuzufügen oder einen virtuellen Computer mit mehreren Netzwerkschnittstellen zu erstellen. Diese beiden Aufgaben können Sie mithilfe der CLI oder mit PowerShell ausführen. Machen Sie sich unbedingt mit den [Einschränkungen](#constraints) vertraut. Wenn Sie einen virtuellen Computer mit mehreren Netzwerkschnittstellen erstellen, müssen Sie auch das Betriebssystem für die ordnungsgemäße Verwendung der Schnittstellen nach der Erstellung des virtuellen Computers konfigurieren. Erfahren Sie, wie Sie [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) oder [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) für mehrere Netzwerkschnittstellen konfigurieren.

### <a name="commands"></a>Befehle

Vor dem Erstellen des virtuellen Computers müssen Sie [eine Netzwerkschnittstelle erstellen](virtual-network-network-interface.md#create-a-network-interface).

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Hinzufügen einer Netzwerkschnittstelle zu einem vorhandenen virtuellen Computer

Gehen Sie wie folgt vor, um Ihrem virtuellen Computer eine Netzwerkschnittstelle hinzuzufügen:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um nach einem vorhandenen virtuellen Computer zu suchen. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.

2. Wählen Sie den Namen Ihres virtuellen Computers aus. Der virtuelle Computer muss die Anzahl an Netzwerkschnittstellen, die Sie hinzufügen möchten, unterstützen. Informationen dazu, wie viele Netzwerkschnittstellen jede VM-Größe unterstützt, finden Sie unter [Größen für virtuelle Linux-Computer in Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder [Größen für virtuelle Windows-Computer in Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Wählen Sie auf der VM-Befehlsleiste die Option **Beenden** aus, und klicken Sie dann im Bestätigungsdialogfeld auf **OK**. Warten Sie dann, bis der **Status** des virtuellen Computers in **Beendet (Zuordnung aufgehoben)** geändert wird.

4. Wählen Sie auf der VM-Menüleiste die Optionen **Netzwerk** > **Netzwerkschnittstelle anfügen** aus. Wählen Sie dann in **Vorhandene Netzwerkschnittstelle anfügen** die Netzwerkschnittstelle aus, die Sie anfügen möchten, und wählen Sie **OK** aus.

    >[!NOTE]
    >Für die ausgewählte Netzwerkschnittstelle darf kein beschleunigter Netzwerkbetrieb aktiviert sein, ihr darf keine IPv6-Adresse zugewiesen sein, und sie muss sich im selben virtuellen Netzwerk befinden, in dem sich die derzeit an den virtuellen Computer angefügte Netzwerkschnittstelle befindet.

    Wenn keine Netzwerkschnittstelle vorhanden ist, müssen Sie zuerst eine erstellen. Wählen Sie dazu **Netzwerkschnittstelle erstellen** aus. Weitere Informationen zum Erstellen einer Netzwerkschnittstelle finden Sie unter [Erstellen einer Netzwerkschnittstelle](virtual-network-network-interface.md#create-a-network-interface). Weitere Informationen zu zusätzlichen Einschränkungen beim Hinzufügen von Netzwerkschnittstellen zu virtuellen Computern finden Sie unter [Einschränkungen](#constraints).

5. Wählen Sie auf der VM-Menüleiste die Optionen **Übersicht** > **Starten** aus, um den virtuellen Computer neu zu starten.

Jetzt können Sie das VM-Betriebssystem für die ordnungsgemäße Verwendung mehrerer Netzwerkschnittstellen konfigurieren. Erfahren Sie, wie Sie [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) oder [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) für mehrere Netzwerkschnittstellen konfigurieren.

### <a name="commands"></a>Befehle

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (Verweis); [detaillierte Schritte](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (Verweis); [detaillierte Schritte](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Anzeigen der Netzwerkschnittstellen für einen virtuellen Computer

Sie können die derzeit zu einem virtuellen Computer hinzugefügten Netzwerkschnittstellen anzeigen, um mehr über die Konfiguration der einzelnen Netzwerkschnittstellen und die diesen zugewiesenen IP-Adressen zu erfahren. 

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um nach einem vorhandenen virtuellen Computer zu suchen. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.

    >[!NOTE]
    >Melden Sie sich mit einem Konto an, dem für Ihr Abonnement die Rolle „Besitzer“, „Mitwirkender“ oder „Netzwerkmitwirkender“ zugewiesen ist. Weitere Informationen zum Zuweisen von Rollen zu Konten finden Sie unter [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Wählen Sie den Namen des virtuellen Computers aus, dessen angefügte Netzwerkschnittstellen Sie anzeigen möchten.

3. Wählen Sie auf der VM-Menüleiste die Option **Netzwerk** aus.

Weitere Informationen zu Netzwerkschnittstellen-Einstellungen finden Sie im Artikel [Verwalten von Netzwerkschnittstellen](virtual-network-network-interface.md). Weitere Informationen zum Hinzufügen, Ändern oder Entfernen der einer Netzwerkschnittstelle zugewiesenen IP-Adressen finden Sie unter [Verwalten von IP-Adressen für Netzwerkschnittstellen](./ip-services/virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Befehle

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az vm nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Entfernen einer Netzwerkschnittstelle von einem virtuellen Computer

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um nach einem vorhandenen virtuellen Computer zu suchen. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.

2. Wählen Sie den Namen des virtuellen Computers aus, dessen angefügte Netzwerkschnittstellen Sie anzeigen möchten.

3. Wählen Sie auf der VM-Symbolleiste die Option **Beenden** aus.

4. Warten Sie, bis der **Status** des virtuellen Computers in **Beendet (Zuordnung aufgehoben)** geändert wird.

5. Wählen Sie auf der VM-Menüleiste die Optionen **Netzwerk** > **Netzwerkschnittstelle trennen** aus.

6. Wählen Sie im Dialogfeld **Netzwerkschnittstelle trennen** die Netzwerkschnittstelle aus, die Sie trennen möchten. Klicken Sie anschließend auf **OK**.

    >[!NOTE]
    >Wenn nur eine Netzwerkschnittstelle aufgeführt ist, können Sie diese nicht trennen, weil immer mindestens eine Netzwerkschnittstelle an einen virtuellen Computer angefügt sein muss.

### <a name="commands"></a>Befehle

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (Verweis); [detaillierte Schritte](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (Verweis); [detaillierte Schritte](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Einschränkungen

- An einen virtuellen Computer muss mindestens eine Netzwerkschnittstelle angefügt sein.

- Es können an einen virtuellen Computer nur so viele Netzwerkschnittstellen angefügt sein, wie die VM-Größe unterstützt. Weitere Informationen dazu, wie viele Netzwerkschnittstellen jede VM-Größe unterstützt, finden Sie unter [Größen für virtuelle Linux-Computer in Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder [Größen für virtuelle Windows-Computer in Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Alle Größen unterstützen mindestens zwei Netzwerkschnittstellen.

- Die Netzwerkschnittstellen, die Sie einem virtuellen Computer hinzufügen, können derzeit nicht an einen anderen virtuellen Computer angefügt werden. Weitere Informationen zum Erstellen von Netzwerkschnittstellen finden Sie unter [Erstellen einer Netzwerkschnittstelle](virtual-network-network-interface.md#create-a-network-interface).

- In der Vergangenheit konnten Netzwerkschnittstellen nur VMs hinzugefügt werden, die mehrere Netzwerkschnittstellen unterstützten und mit mindestens zwei Netzwerkschnittstellen erstellt wurden. Einem mit einer Netzwerkschnittstelle erstellten virtuellen Computer konnte keine Netzwerkschnittstelle hinzugefügt werden, auch wenn die VM-Größe mehrere Netzwerkschnittstellen unterstützte. Im Gegenzug konnten Sie nur Netzwerkschnittstellen von einem virtuellen Computer mit mindestens drei Netzwerkschnittstellen entfernen, da virtuelle Computer, die mit zwei Netzwerkschnittstellen erstellt wurden, immer mindestens zwei Netzwerkschnittstellen haben mussten. Diese Einschränkungen gelten nicht mehr. Sie können nun einen virtuellen Computer mit einer beliebigen Anzahl von Netzwerkschnittstellen (bis zu der von der VM-Größe unterstützten Anzahl) erstellen.

- Standardmäßig ist die erste an einen virtuellen Computer angefügte Netzwerkschnittstelle die *primäre* Netzwerkschnittstelle. Alle anderen Netzwerkschnittstellen auf dem virtuellen Computer sind *sekundäre* Netzwerkschnittstellen.

- Sie können steuern, an welche Netzwerkschnittstelle ausgehender Datenverkehr gesendet wird. Standardmäßig sendet ein virtueller Computer jedoch den gesamten ausgehenden Datenverkehr an die IP-Adresse, die der primären IP-Konfiguration der primären Netzwerkschnittstelle zugewiesen ist.

- In der Vergangenheit mussten alle virtuellen Computer in der gleichen Verfügbarkeitsgruppe über eine einzelne Netzwerkschnittstelle (oder über mehrere Netzwerkschnittstellen) verfügen. In einer Verfügbarkeitsgruppe können nun VMs mit bis zu der von der VM-Größe unterstützten Anzahl an Netzwerkschnittstellen vertreten sein. Sie können eine VM erst dann einer Verfügbarkeitsgruppe hinzufügen, nachdem sie erstellt wurde. Weitere Informationen zu Verfügbarkeitsgruppen finden Sie unter [Verwalten der Verfügbarkeit von VMs in Azure](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

- Sie können Netzwerkschnittstellen auf demselben virtuellen Computer mit unterschiedlichen Subnetzen in einem virtuellen Netzwerk verbinden. Die Netzwerkschnittstellen müssen jedoch alle mit demselben virtuellen Netzwerk verbunden sein.

- Sie können eine beliebige IP-Adresse für eine beliebige IP-Konfiguration einer beliebigen primären oder sekundären Netzwerkschnittstelle einem Back-End-Pool von Azure Load Balancer hinzufügen. Bisher konnte nur die primäre IP-Adresse für die primäre Netzwerkschnittstelle einem Back-End-Pool hinzugefügt werden. Weitere Informationen zu IP-Adressen und -Konfigurationen finden Sie im Artikel [Hinzufügen, Ändern oder Entfernen von IP-Adressen](./ip-services/virtual-network-network-interface-addresses.md).

- Beim Löschen eines virtuellen Computers werden die an ihn angefügten Netzwerkschnittstellen nicht gelöscht. Wenn Sie einen virtuellen Computer löschen, werden die Netzwerkschnittstellen von dem virtuellen Computer getrennt. Sie können diese Netzwerkschnittstellen zu anderen VMs hinzufügen oder löschen.

- Soll die optimale dokumentierte Leistung erzielt werden, ist beschleunigter Netzwerkbetrieb erforderlich. In einigen Fällen müssen Sie beschleunigten Netzwerkbetrieb für virtuelle [Windows](create-vm-accelerated-networking-powershell.md)- oder [Linux](create-vm-accelerated-networking-cli.md)-Computer explizit aktivieren.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Erstellen eines virtuellen Computers mit mehreren Netzwerkschnittstellen oder IP-Adressen finden Sie unter den in der folgenden Tabelle aufgeführten Links:

|Aufgabe|Tool|
|---|---|
|Erstellen eines virtuellen Computers mit mehreren Netzwerkschnittstellenkarten (NICs)|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Erstellen eines virtuellen Computers mit einer NIC und mehreren IPv4-Adressen|[CLI](./ip-services/virtual-network-multiple-ip-addresses-cli.md), [PowerShell](./ip-services/virtual-network-multiple-ip-addresses-powershell.md)|
|Erstellen eines virtuellen Computers mit einer NIC und einer IPv6-Adresse (mit Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-Vorlage](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|