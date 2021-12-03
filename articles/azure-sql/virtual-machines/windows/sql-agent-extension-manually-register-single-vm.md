---
title: Sich mit SQL IaaS Extension (Windows) registrieren
description: Hier erfahren Sie, wie Sie Ihren SQL-Server auf Windows Azure VM mit der SQL-IaaS-Agent-Erweiterung registrieren, um Azure-Funktionen zu aktivieren, um die Konformität zu gewährleisten und die Verwaltbarkeit zu verbessern.
services: virtual-machines-windows
documentationcenter: na
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 20177246aa0b39dc7a7c254c4dfb964d0444b631
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044017"
---
# <a name="register-windows-sql-server-vm-with-sql-iaas-extension"></a>Einen virtuellen Computer des Windows SQL-Servers mit SQL IaaS-Erweiterung registrieren
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-agent-extension-manually-register-single-vm.md)
> * [Linux](../linux/sql-iaas-agent-extension-register-vm-linux.md)

Wenn Sie Ihren virtuellen Computer des SQL-Servers mit der [SQL-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) registrieren, können Sie eine Vielzahl an Funktionen für Ihren SQL-Server auf dem virtuellen Computer von Windows Azure freischalten. 

In diesem Artikel erfahren Sie, wie Sie eine einzelne SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren. Alternativ können Sie alle virtuellen Computer des SQL-Servers in einem Abonnement [automatisch](sql-agent-extension-automatic-registration-all-vms.md) oder [mehrere virtuelle Computer per Skript in einem Massenvorgang](sql-agent-extension-manually-register-vms-bulk.md) registrieren.

> [!NOTE]
> Ab September 2021 ist für die Registrierung bei der SQL IaaS-Erweiterung im Vollmodus kein Neustart des SQL Server-Dienstes erforderlich. 

## <a name="overview"></a>Übersicht

Durch die Registrierung mit der [SQL Server-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) wird die [**Virtueller SQL-Computer**-_Ressource_](manage-sql-vm-portal.md) in Ihrem Abonnement erstellt. Dabei handelt es sich um eine _andere_ Ressource als die Ressource für Ihren virtuellen Computer. Wenn Sie die Registrierung der Erweiterung für Ihre SQL Server-VM aufheben, wird die _Ressource_ **Virtueller SQL-Computer** entfernt, während der tatsächliche virtuelle Computer jedoch erhalten bleibt.

Beim Bereitstellen eines Azure Marketplace-Images einer SQL Server-VM über das Azure-Portal wird die SQL Server-VM mit der Erweiterung automatisch registriert. Wenn Sie jedoch SQL Server auf einer Azure-VM selbst installieren oder eine Azure-VM von einer benutzerdefinierten VHD bereitstellen, müssen Sie Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren, um alle Featurevorteile nutzen und von einer einfachen Verwaltbarkeit profitieren zu können.

Wenn Sie die SQL-IaaS-Agent-Erweiterung verwenden möchten, müssen Sie zunächst [Ihr Abonnement beim Anbieter **Microsoft.SqlVirtualMachine** registrieren](#register-subscription-with-rp). Dadurch kann die SQL-IaaS-Agent-Erweiterung Ressourcen in diesem bestimmten Abonnement erstellen. Dann können Sie Ihren virtuellen Computer des SQL-Servers mit der Erweiterung registrieren. 

Standardmäßig werden virtuelle Azure-Computer, auf denen SQL Server 2016 oder höher installiert ist, automatisch bei der Erweiterung für den SQL-IaaS-Agent registriert, wenn sie vom [CEIP-Dienst](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server) erkannt werden.  Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

> [!IMPORTANT]
> Die SQL-IaaS-Agent-Erweiterung sammelt Daten ausschließlich, um Kunden zu ermöglichen, bei der Verwendung von SQL Server in Azure Virtual Machines optionale Vorteile zu nutzen. Microsoft verwendet diese Daten ohne vorherige Zustimmung des Kunden nicht für Lizenzierungsüberprüfungen. Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

## <a name="prerequisites"></a>Voraussetzungen

Um Ihre SQL Server-VM mit der Erweiterung registrieren zu können, benötigen Sie Folgendes:

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/).
- Ein in der öffentlichen Cloud oder Azure Government-Cloud bereitgestelltes Azure-Ressourcenmodell des Typs [Windows Server 2008-VM (oder höher)](../../../virtual-machines/windows/quick-create-portal.md) mit [SQL Server 2008 (oder höher)](https://www.microsoft.com/sql-server/sql-server-downloads).
- Die aktuelle Version von [Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell (mindestens 5.0)](/powershell/azure/install-az-ps).
- Mindestens .NET Framework 4.5.1.

## <a name="register-subscription-with-rp"></a>Registrieren eines Abonnements mit RP

Um Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren zu können, müssen Sie zunächst Ihr Abonnement beim Ressourcenanbieter **Microsoft.SqlVirtualMachine** registrieren. Dadurch kann die SQL-IaaS-Agent-Erweiterung Ressourcen in Ihrem Abonnement erstellen. Verwenden Sie hierzu das Azure-Portal, die Azure CLI oder Azure PowerShell.

### <a name="azure-portal"></a>Azure-Portal

Registrieren Sie Ihr Abonnement beim Ressourcenanbieter, indem Sie das Azure-Portal verwenden:

1. Öffnen Sie das Azure-Portal, und wechseln Sie zu **Alle Dienste**.
1. Wechseln Sie zu **Abonnements**, und wählen Sie das gewünschte Abonnement aus.
1. Wählen Sie auf der Seite **Abonnements** unter **Einstellungen** die Option **Ressourcenanbieter** aus.
1. Geben Sie im Filter **sql** ein, um die SQL-bezogenen Ressourcenanbieter aufzurufen.
1. Wählen Sie je nach gewünschter Aktion für den Anbieter **Microsoft.SqlVirtualMachine** eine der Optionen **Registrieren**, **Erneut registrieren** oder **Registrierung aufheben** aus.

   ![Ändern des Anbieters](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)

### <a name="command-line"></a>Befehlszeile

Registrieren Sie Ihr Azure-Abonnement beim Anbieter **Microsoft.SqlVirtualMachine** entweder mithilfe der Azure-Befehlszeilenschnittstelle oder mit Azure PowerShell.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Registrieren Sie Ihr Abonnement mithilfe der Azure CLI beim Ressourcenanbieter:

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registrieren Sie Ihr Abonnement mithilfe von Azure PowerShell beim Ressourcenanbieter:

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="full-mode"></a>Modus „Vollständig“

Sie können Ihren virtuellen Computer des SQL-Servers entweder direkt im vollständigen Modus registrieren, indem Sie Azure CLI und Azure PowerShell verwenden, oder ein Upgrade auf den vollständigen Modus vom eingeschränkten Modus durchführen, indem Sie das Azure-Portal, die Azure CLI oder Azure PowerShell verwenden. Das Upgrade von virtuellen Computern im _NoAgent-Modus_ wird erst unterstützt, wenn das Betriebssystem auf Windows 2008 R2 und höher aktualisiert wurde.  

Ab September 2021 ist für die Registrierung Ihres virtuellen Computers des SQL-Servers im Vollmodus kein Neustart des SQL Server-Diensts mehr erforderlich. 

Weitere Informationen zum Modus „Vollständig“ finden Sie unter [Verwaltungsmodi](sql-server-iaas-agent-extension-automate-management.md#management-modes).

### <a name="register-in-full-mode"></a>Registrieren im Vollmodus

Geben Sie den SQL Server-Lizenztyp an. Sie können zwischen nutzungsbasierter Bezahlung (`PAYG`), Azure-Hybridvorteil (`AHUB`) zum Verwenden Ihrer eigenen Lizenz und Notfallwiederherstellung (`DR`) zum Aktivieren des [kostenlosen DR-Replikats](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) auswählen.


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Registrieren eines virtuellen Computers des SQL-Servers im Vollmodus mit Azure CLI:

```azurecli-interactive
# Register Enterprise or Standard self-installed VM in Lightweight mode
az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> --sql-mgmt-type Full
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registrieren eines virtuellen Computers des SQL-Servers im Vollmodus mit Azure PowerShell:

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
-LicenseType <license_type> -SqlManagementType Full
```

---


### <a name="upgrade-to-full"></a>Upgrade auf „Vollständig“

Für SQL Server-VMs, auf denen die Erweiterung im Modus *Lightweight* registriert wurde, können Sie über das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder Azure PowerShell ein Upgrade auf den Modus _Vollständig_ durchführen. Für SQL Server-VMs im Modus _NoAgent_ kann ein Upgrade auf _Vollständig_ durchgeführt werden, nachdem das Betriebssystem auf Windows 2008 R2 oder höher aktualisiert wurde. Es ist nicht möglich, ein Downgrade auszuführen. Dazu müssen Sie die [Registrierung der SQL-IaaS-Agent-Erweiterung für die SQL Server-VM aufheben](#unregister-from-extension). Dadurch wird die _Ressource_ **Virtueller SQL-Computer** entfernt, der tatsächliche virtuelle Computer aber nicht gelöscht.

#### <a name="azure-portal"></a>Azure-Portal

Aktualisieren Sie die Erweiterung auf Vollmodus auf dem Azure-Portal:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wechseln Sie zu Ihrer Ressource [Virtuelle SQL-Computer](manage-sql-vm-portal.md#access-the-resource).
1. Wählen Sie Ihre SQL Server-VM aus, und navigieren Sie zur Seite **Übersicht**.
1. Wählen Sie für SQL Server-VMs mit dem IaaS-Erweiterungsmodus „NoAgent“ oder „Lightweight“ die Meldung **Mit dem aktuellen SQL-IaaS-Erweiterungsmodus sind nur Lizenztyp- und Editionsaktualisierungen verfügbar...** aus.

   ![Auswahlmöglichkeiten zum Ändern des Modus im Portal](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Wählen Sie **Bestätigen** aus, um ein Upgrade des SQL Server-IaaS-Erweiterungsmodus auf „Vollständig“ durchzuführen.

  ![Wählen Sie **Bestätigen** aus, um ein Upgrade des SQL Server-IaaS-Erweiterungsmodus auf „Vollständig“ durchzuführen.](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

#### <a name="command-line"></a>Befehlszeile

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Aktualisieren Sie die Erweiterung auf Vollmodus bei Azure CLI:

```azurecli-interactive
# Update to full mode
az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Aktualisieren Sie die Erweiterung auf Vollmodus bei Azure PowerShell: 

```powershell-interactive
# Get the existing  Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full -Location $vm.Location
```

---

## <a name="lightweight-mode"></a>Modus „Lightweight“

Verwenden Sie die Azure-Befehlszeilenschnittstelle oder Azure PowerShell, um Ihre SQL Server-VM mit der Erweiterung im Modus „Lightweight“ für eingeschränkte Funktionalität zu registrieren. 

Geben Sie den SQL Server-Lizenztyp an. Sie können zwischen nutzungsbasierter Bezahlung (`PAYG`), Azure-Hybridvorteil (`AHUB`) zum Verwenden Ihrer eigenen Lizenz und Notfallwiederherstellung (`DR`) zum Aktivieren des [kostenlosen DR-Replikats](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) auswählen.

Failover-Cluster-Instanzen und virtuelle Computer des SQL-Servers mit mehreren Instanzen können nur mit der SQL-IaaS-Agent-Erweiterung im eingeschränkten (Lightweight) Modus registriert werden.

Weitere Informationen zum Modus „Lightweight“ finden Sie unter [Verwaltungsmodi](sql-server-iaas-agent-extension-automate-management.md#management-modes).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Registrieren einer SQL Server-VM im Modus „Lightweight“ mit der Azure CLI:

```azurecli-interactive
# Register Enterprise or Standard self-installed VM in Lightweight mode
az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registrieren einer SQL Server-VM im Modus „Lightweight“ mit Azure PowerShell:

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType <license_type>  -SqlManagementType LightWeight  
```

---

## <a name="noagent-mode"></a>NoAgent-Modus

Installationen von SQL Server 2008 und SQL Server 2008 R2 unter Windows Server 2008 (_nicht R2_) können nur mit der SQL-IaaS-Agent-Erweiterung im [Modus „NoAgent“](sql-server-iaas-agent-extension-automate-management.md#management-modes) registriert werden. Diese Option stellt die Konformität sicher und ermöglicht die Überwachung der SQL Server-VM im Azure-Portal mit eingeschränktem Funktionsumfang.

Geben Sie als **Lizenztyp** entweder `AHUB`, `PAYG` oder `DR` an. Geben Sie als **Imageangebot** entweder `SQL2008-WS2008` oder `SQL2008R2-WS2008` an.

Verwenden Sie zum Registrieren Ihrer SQL-Server-2008-Instanz (`SQL2008-WS2008`) oder Ihrer SQL-Server-2008-R2-Instanz (`SQL2008R2-WS2008`) auf Ihrem virtuellen Computer des Windows-Servers 2008 Azure CLI oder Azure PowerShell. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Registrieren Ihres virtuellen SQL Server-Computers im Modus „NoAgent“ mit Azure CLI:

```azurecli-interactive
az sql vm create -n sqlvm -g myresourcegroup -l eastus |
--license-type <license type>  --sql-mgmt-type NoAgent 
--image-sku Enterprise --image-offer <image offer> 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registrieren Ihres virtuellen SQL Server-Computers im Modus „NoAgent“ mit Azure PowerShell:

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
```

---


## <a name="check-management-mode"></a>Verwaltungsmodus überprüfen

Verwenden Sie Azure PowerShell, um zu überprüfen, in welchem Verwaltungsmodus sich Ihre SQL Server IaaS-Agent-Erweiterung befindet. 

Überprüfen Sie den Modus der Erweiterung mit Azure PowerShell:  

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```


## <a name="verify-registration-status"></a>Überprüfen des Registrierungsstatus

Sie können über das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder Azure PowerShell überprüfen, ob Ihre SQL Server-VM bereits mit der SQL-IaaS-Agent-Erweiterung registriert wurde.

### <a name="azure-portal"></a>Azure-Portal

Überprüfen Sie den Registrierungsstatus mithilfe des Azure-Portals:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu Ihrem [SQL Server-VMs](manage-sql-vm-portal.md).
1. Wählen Sie die SQL Server-VM in der Liste aus. Wenn Ihre SQL Server-VM hier nicht aufgeführt ist, wurde sie wahrscheinlich nicht mit der SQL-IaaS-Agent-Erweiterung registriert.
1. Überprüfen Sie den Wert unter **Status**. Wenn das Feld **Status** den Wert **Erfolgreich** aufweist, wurde die SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registriert.

   ![Überprüfen des Status per SQL RP-Registrierung](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

Alternativ können Sie den Status überprüfen, indem Sie im Bereich **Support und Problembehandlung** in der Ressource **Virtueller SQL-Computer** die Option **Reparieren** auswählen. Der Bereitstellungsstatus für die SQL IaaS-Agent-Erweiterung kann **Erfolgreich** oder **Fehler** sein. 

### <a name="command-line"></a>Befehlszeile

Überprüfen Sie den aktuellen Registrierungsstatus der SQL Server-VM mit der Azure CLI oder Azure PowerShell. `ProvisioningState` zeigt `Succeeded` an, wenn die Registrierung erfolgreich abgeschlossen wurde.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Überprüfen Sie den Registrierungsstatus mithilfe von Azure CLI: 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Überprüfen Sie den Registrierungsstatus mithilfe von Azure PowerShell: 

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Ein Fehler weist darauf hin, dass die SQL Server-VM nicht mit der Erweiterung registriert wurde.

## <a name="repair-extension"></a>Reparieren der Erweiterung

Es ist möglich, dass sich Ihre SQL IaaS-Agent-Erweiterung in einem fehlerhaften Zustand befindet. Verwenden Sie das Azure-Portal, um die SQL IaaS-Agent-Erweiterung zu reparieren. 

So reparieren Sie die Erweiterung mit dem Azure-Portal:  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu Ihrem [SQL Server-VMs](manage-sql-vm-portal.md).
1. Wählen Sie die SQL Server-VM in der Liste aus. Wenn Ihre SQL Server-VM hier nicht aufgeführt ist, wurde sie wahrscheinlich nicht mit der SQL-IaaS-Agent-Erweiterung registriert.
1. Wählen Sie **Reparieren** unter **Support und Problembehandlung** auf der Ressourcenseite **Virtueller SQL-Computer** aus. 

   :::image type="content" source="media/sql-agent-extension-manually-register-single-vm/repair-extension.png" alt-text="Auswählen von **Reparieren** unter **Support und Problembehandlung** auf der Ressourcenseite **Virtueller SQL-Computer**":::   

1. Wenn der Bereitstellungsstatus **Fehler** ist, wählen Sie **Reparieren** aus, um die Erweiterung zu reparieren. Wenn der Status **Erfolgreich** ist, können Sie das Kontrollkästchen neben **Reparatur erzwingen** aktivieren, um die Erweiterung unabhängig vom Status zu reparieren. 

   ![Wenn der Bereitstellungsstatus **Fehler** ist, wählen Sie **Reparieren** aus, um die Erweiterung zu reparieren. Wenn der Status **Erfolgreich** ist, können Sie das Kontrollkästchen neben **Reparatur erzwingen** aktivieren, um die Erweiterung unabhängig vom Status zu reparieren.](./media/sql-agent-extension-manually-register-single-vm/force-repair-extension.png)


## <a name="unregister-from-extension"></a>Aufheben der Registrierung der Erweiterung

Um die Registrierung Ihrer SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung aufzuheben, löschen Sie die *Ressource* „Virtueller SQL-Computer“ im Azure-Portal oder mithilfe der Azure-Befehlszeilenschnittstelle. Das Löschen der SQL-VM-*Ressource* löscht nicht die SQL Server-VM. Sie müssen die Registrierung des virtuellen SQL-Computers mit der SQL-IaaS-Agent-Erweiterung aufheben, um den Verwaltungsmodus „Vollständig“ herabstufen zu können.

>[!CAUTION]
> **Seien Sie äußerst vorsichtig**, wenn Sie die Registrierung Ihres virtuellen Computers des SQL-Servers bei der Erweiterung aufheben. Führen Sie die Schritte sorgfältig aus, da **die Möglichkeit besteht, den virtuellen Computer versehentlich zu löschen**, wenn Sie versuchen, die *Ressource* zu entfernen.


### <a name="azure-portal"></a>Azure-Portal

Führen Sie die folgenden Schritte aus, um die Registrierung des virtuellen Computers des SQL-Servers bei der Erweiterung mithilfe des Azure-Portals aufzuheben:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zur SQL Server-VM-Ressource.

   ![Ressource „Virtuelle SQL-Computer“](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Klicken Sie auf **Löschen**.

   ![Auswählen von „Löschen“ in der oberen Navigationsleiste](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Geben Sie den Namen der SQL-VM ein, und **deaktivieren Sie das Kontrollkästchen neben dem virtuellen Computer**.

   ![Deaktivieren der VM, um das Löschen der eigentlichen VM zu verhindern, und anschließend Auswählen von „Löschen“, um mit dem Löschen der SQL-VM-Ressource fortzufahren](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   > [!WARNING]
   > Wenn Sie das Kontrollkästchen neben dem Namen des virtuellen Computers nicht deaktivieren, wird der virtuelle Computer vollständig *gelöscht*. Deaktivieren Sie das Kontrollkästchen, um die Registrierung der Erweiterung für die SQL Server-VM aufzuheben, aber *den eigentlichen virtuellen Computer nicht zu löschen*.

1. Wählen Sie **Löschen** aus, um das Löschen der SQL-VM-*Ressource* und nicht der SQL Server-VM zu bestätigen.

### <a name="command-line"></a>Befehlszeile

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Registrierung Ihres virtuellen Computers des SQL-Servers bei der Erweiterung mithilfe von Azure CLI aufzuheben, verwenden Sie den Befehl [az sql vm delete](/cli/azure/sql/vm#az_sql_vm_delete). Dadurch wird die *Ressource* des virtuellen Computers des SQL-Servers entfernt, aber der virtuelle Computer wird nicht gelöscht.

So heben Sie die Registrierung des virtuellen Computers des SQL-Servers bei Azure CLI auf: 

```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Registrierung Ihres virtuellen Computers (VM) des SQL-Servers bei der Erweiterung mithilfe von Azure PowerShell aufzuheben, verwenden Sie den Befehl [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Dadurch wird die *Ressource* des virtuellen Computers des SQL-Servers entfernt, aber der virtuelle Computer wird nicht gelöscht.

So heben Sie die Registrierung des virtuellen Computers des SQL-Servers bei Azure PowerShell auf: 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <SQL VM resource name>
```

---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern](frequently-asked-questions-faq.yml)
* [Preisinformationen für SQL-Server bei virtuellen Azure-Computern](../windows/pricing-guidance.md)
* [Neuerungen für SQL Server auf Azure-VMs](../windows/doc-changes-updates-release-notes-whats-new.md)
