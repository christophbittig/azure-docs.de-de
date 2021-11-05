---
title: Registrieren bei der SQL IaaS-Agent-Erweiterung (Linux)
description: Hier erfahren Sie, wie Sie Ihre Azure-VM für SQL Server für Linux mit der SQL-IaaS-Agent-Erweiterung registrieren, um Azure-Features zu aktivieren, die Konformität zu gewährleisten und die Verwaltbarkeit zu verbessern.
services: virtual-machines-windows
documentationcenter: na
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-Linux-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 2cf9219f5afc6805c147243ec0157280c839dc87
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096028"
---
# <a name="register-linux-sql-server-vm-with-sql-iaas-agent-extension"></a>Registrieren einer SQL Server für Linux-VM mit der SQL-IaaS-Agent-Erweiterung 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-agent-extension-manually-register-single-vm.md)
> * [Linux](sql-iaas-agent-extension-register-vm-linux.md)


Wenn Sie Ihre SQL Server-VM mit der [SQL-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-linux.md) registrieren, können Sie viele Features für Ihre Azure-VM für SQL Server für Linux nutzen.

## <a name="overview"></a>Übersicht

Durch die Registrierung mit der [SQL Server-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-linux.md) wird die _Ressource_ **Virtueller SQL-Computer** in Ihrem Abonnement erstellt. Dabei handelt es sich um eine _andere_ Ressource als die Ressource für Ihren virtuellen Computer. Wenn Sie die Registrierung der Erweiterung für Ihre SQL Server-VM aufheben, wird die _Ressource_ **Virtueller SQL-Computer** entfernt, während der tatsächliche virtuelle Computer jedoch erhalten bleibt.

Wenn Sie die SQL-IaaS-Agent-Erweiterung verwenden möchten, müssen Sie zunächst [Ihr Abonnement beim Anbieter **Microsoft.SqlVirtualMachine** registrieren](#register-subscription-with-rp). Dadurch kann die SQL-IaaS-Agent-Erweiterung Ressourcen in diesem bestimmten Abonnement erstellen.

> [!IMPORTANT]
> Die SQL-IaaS-Agent-Erweiterung sammelt Daten ausschließlich, um Kunden zu ermöglichen, bei der Verwendung von SQL Server in Azure Virtual Machines optionale Vorteile zu nutzen. Microsoft verwendet diese Daten ohne vorherige Zustimmung des Kunden nicht für Lizenzierungsüberprüfungen. Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

## <a name="prerequisites"></a>Voraussetzungen

Um Ihre SQL Server-VM mit der Erweiterung registrieren zu können, benötigen Sie Folgendes:

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/).
- Ein in der öffentlichen Cloud oder Azure Government-Cloud bereitgestelltes Azure-Ressourcenmodell des Typs [Ubuntu Linux-VM](../../../virtual-machines/linux/quick-create-portal.md) mit [SQL Server 2017 (oder höher)](https://www.microsoft.com/sql-server/sql-server-downloads)
- Die aktuelle Version von [Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell (mindestens 5.0)](/powershell/azure/install-az-ps).

## <a name="register-subscription-with-rp"></a>Registrieren eines Abonnements mit RP

Um Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren zu können, müssen Sie zunächst Ihr Abonnement beim Ressourcenanbieter **Microsoft.SqlVirtualMachine** registrieren. Dadurch kann die SQL-IaaS-Agent-Erweiterung Ressourcen in Ihrem Abonnement erstellen. Verwenden Sie hierzu das Azure-Portal, die Azure CLI oder Azure PowerShell.

### <a name="azure-portal"></a>Azure-Portal

Registrieren Sie Ihr Abonnement beim Ressourcenanbieter, indem Sie das Azure-Portal verwenden:

1. Öffnen Sie das Azure-Portal, und wechseln Sie zu **Alle Dienste**.
1. Wechseln Sie zu **Abonnements**, und wählen Sie das gewünschte Abonnement aus.
1. Wählen Sie auf der Seite **Abonnements** unter **Einstellungen** die Option **Ressourcenanbieter** aus.
1. Geben Sie im Filter **sql** ein, um die SQL-bezogenen Ressourcenanbieter aufzurufen.
1. Wählen Sie je nach gewünschter Aktion für den Anbieter **Microsoft.SqlVirtualMachine** eine der Optionen **Registrieren**, **Erneut registrieren** oder **Registrierung aufheben** aus.


![Ändern des Anbieters](../windows/media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)

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

## <a name="register-vm"></a>VM registrieren

Die SQL-IaaS-Agent-Erweiterung unter Linux ist nur im Lightweight-Modus verfügbar, der nur das Ändern des Lizenztyps und die Edition von SQL Server unterstützt. Verwenden Sie die Azure-Befehlszeilenschnittstelle oder Azure PowerShell, um Ihre SQL Server-VM mit der Erweiterung im Modus „Lightweight“ für eingeschränkte Funktionalität zu registrieren. 

Geben Sie den SQL Server-Lizenztyp an. Sie können zwischen nutzungsbasierter Bezahlung (`PAYG`), Azure-Hybridvorteil (`AHUB`) zum Verwenden Ihrer eigenen Lizenz und Notfallwiederherstellung (`DR`) zum Aktivieren des [kostenlosen DR-Replikats](../windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) auswählen.

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

## <a name="verify-registration-status"></a>Überprüfen des Registrierungsstatus

Sie können über das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder Azure PowerShell überprüfen, ob Ihre SQL Server-VM bereits mit der SQL-IaaS-Agent-Erweiterung registriert wurde.


### <a name="azure-portal"></a>Azure-Portal

Überprüfen Sie den Registrierungsstatus mithilfe des Azure-Portals: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wechseln Sie zu Ihrer Ressource Virtuelle SQL-Computer.
1. Wählen Sie die SQL Server-VM in der Liste aus. Wenn Ihre SQL Server-VM hier nicht aufgeführt ist, wurde sie wahrscheinlich nicht mit der SQL-IaaS-Agent-Erweiterung registriert.

### <a name="command-line"></a>Befehlszeile

Überprüfen Sie den aktuellen Registrierungsstatus der SQL Server-VM mit der Azure CLI oder Azure PowerShell. `ProvisioningState` zeigt `Succeeded` an, wenn die Registrierung erfolgreich abgeschlossen wurde.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Überprüfen Sie den Registrierungsstatus mithilfe der Azure CLI:

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


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern](frequently-asked-questions-faq.yml)
* [Preisinformationen für SQL Server auf virtuellen Windows-Computern](../windows/pricing-guidance.md)
* [SQL Server auf Windows-VMs – Versionshinweise](../windows/doc-changes-updates-release-notes.md)
