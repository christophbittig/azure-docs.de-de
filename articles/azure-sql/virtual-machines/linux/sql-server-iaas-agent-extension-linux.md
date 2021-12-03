---
title: Erweiterung für den SQL Server-IaaS-Agent für Linux
description: In diesem Artikel wird beschrieben, wie die Erweiterung für den SQL Server-IaaS-Agent bei der Automatisierung von verwaltungsspezifischen Aufgaben für Azure-VMs für SQL Server für Linux hilft.
services: virtual-machines-windows
documentationcenter: ''
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.openlocfilehash: 98e448358d31fda34a8bb84024be6daf157d0f78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096027"
---
# <a name="sql-server-iaas-agent-extension-for-linux"></a>Erweiterung für den SQL Server-IaaS-Agent für Linux
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-iaas-agent-extension-automate-management.md)
> * [Linux](sql-server-iaas-agent-extension-linux.md)

Die Erweiterung für den SQL Server-IaaS-Agent (SqlIaasExtension) wird auf Azure-VMs für SQL Server für Linux ausgeführt, um Verwaltungsaufgaben zu automatisieren. 

Dieser Artikel bietet eine Übersicht über die Erweiterung. Weitere Informationen finden Sie unter [Registrieren mit der Erweiterung](sql-iaas-agent-extension-register-vm-linux.md). 


## <a name="overview"></a>Übersicht

Die Erweiterung für den SQL Server-IaaS-Agent ermöglicht die Integration mit dem Azure-Portal und bietet die folgenden Vorteile für Azure-VMs für SQL Server für Linux: 

- **Compliance:** Mit der Erweiterung können Sie auch einfacher Ihrer Verpflichtung gemäß den Nutzungsbedingungen zum Produkt nachkommen, Microsoft zu benachrichtigen, wenn der Azure-Hybridvorteil aktiviert wurde. Auf diese Weise können Sie darauf verzichten, für jede Ressource ein Lizenzregistrierungsformular zu verwalten.  

- **Vereinfachte Lizenzverwaltung:** Die Erweiterung vereinfacht die SQL Server-Lizenzverwaltung. Außerdem können Sie über das Azure-Portal, Azure PowerShell oder die Azure CLI schnell SQL Server-VMs mit aktiviertem Azure-Hybridvorteil identifizieren: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---

- **Kostenlos:** Für die Erweiterung fallen keine zusätzlichen Kosten an. 



## <a name="installation"></a>Installation

[Registrieren](sql-iaas-agent-extension-register-vm-linux.md) Sie Ihre SQL Server-VM mit der Erweiterung für den SQL Server-IaaS-Agent, um die _Ressource_ **Virtueller SQL-Computer** in Ihrem Abonnement zu erstellen. Dabei handelt es sich um eine _andere_ Ressource als die Ressource für Ihren virtuellen Computer. Wenn Sie die Registrierung der Erweiterung für Ihre SQL Server-VM aufheben, wird die _Ressource_ **Virtueller SQL-Computer** aus Ihrem Abonnement entfernt, während der tatsächliche virtuelle Computer jedoch erhalten bleibt.

Die Erweiterung für den SQL Server-IaaS-Agent für Linux ist derzeit nur im Lightweight-Modus verfügbar. 


## <a name="verify-extension-status"></a>Überprüfen des Status der Erweiterung

Sie können den Status der Erweiterung über das Azure-Portal oder mit Azure PowerShell überprüfen. 

### <a name="azure-portal"></a>Azure-Portal

Überprüfen Sie mithilfe des Azure-Portals, ob die Erweiterung installiert ist. 

Wechseln Sie zu Ihrer Ressource **Virtueller Computer** im Azure-Portal (nicht zur Ressource *Virtuelle SQL-Computer*, sondern zur Ressource für Ihre VM). Wählen Sie unter **Einstellungen** die Option **Erweiterungen** aus. Die Erweiterung **SqlIaasExtension** sollte wie im folgenden Beispiel aufgeführt werden: 

![Überprüfen des Status der Erweiterung „SqlIaaSExtension“ für den SQL Server-IaaS-Agent im Azure-Portal](../windows/media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)




### <a name="azure-powershell"></a>Azure PowerShell

Sie können auch das Azure PowerShell-Cmdlet **Get-AzVMSqlServerExtension** verwenden:

```powershell-interactive
  Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
```

Der Befehl oben überprüft, ob der Agent installiert ist, und stellt allgemeine Statusinformationen bereit. Mit den folgenden Befehlen können Sie spezifische Statusinformationen zu automatischen Sicherungen und Patches abrufen:

```powershell-interactive
 $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
 $sqlext.AutoPatchingSettings
 $sqlext.AutoBackupSettings
```

## <a name="limitations"></a>Einschränkungen

Die Erweiterung für den SQL Server-IaaS-Agent unter Linux hat die folgenden Einschränkungen: 

- Nur SQL Server-VMs, die unter dem Ubuntu Linux-Betriebssystem ausgeführt werden, werden unterstützt. Andere Linux-Distributionen werden derzeit nicht unterstützt.
- SQL Server-VMs, die unter Ubuntu Linux Pro ausgeführt werden, werden nicht unterstützt.
- SQL Server-VMs, die auf generalisierten Images ausgeführt werden, werden nicht unterstützt.
- Nur SQL Server-VMs, die über den Azure Resource Manager bereitgestellt werden, werden unterstützt. SQL Server-VMs, die mit dem klassischen Modell bereitgestellt wurden, werden nicht unterstützt. 
- SQL Server mit nur einer einzelnen Instanz. Mehrere Instanzen werden nicht unterstützt. 

## <a name="privacy-statement"></a><a id="in-region-data-residency"></a> Datenschutzbestimmungen

Wenn Sie SQL Server auf Azure-VMs und die SQL Server-IaaS-Erweiterung verwenden, berücksichtigen Sie die folgenden Datenschutzbestimmungen: 

- **Datensammlung:** Die Erweiterung für den SQL Server-IaaS-Agent sammelt Daten ausschließlich zu dem Zweck, Kund*innen bei Verwendung von SQL Server auf virtuellen Azure-Computern optionale Vorteile zu bieten. Microsoft **verwendet diese Daten ohne vorherige Zustimmung von Kund*innen nicht für Lizenzierungsüberprüfungen**. Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

- **Datenresidenz in der Region:** SQL Server auf Azure-VMs und die Erweiterung für den SQL Server-IaaS-Agent verschieben Kundendaten nicht aus der Region, in der die VMs bereitgestellt werden, oder speichern sie dort. 


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Ausführung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Übersicht über SQL Server auf virtuellen Azure-Computern (Linux)](sql-server-on-linux-vm-what-is-iaas-overview.md).

Weitere Informationen finden Sie unter [Häufig gestellte Fragen](frequently-asked-questions-faq.yml).
