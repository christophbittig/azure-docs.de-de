---
title: Die Erweiterung für den SQL Server-IaaS-Agent (Windows)
description: In diesem Artikel erfahren Sie, wie die SQL Server-IaaS-Agent-Erweiterung bei der Automatisierung bestimmter SQL Server-Verwaltungsaufgaben auf Windows Azure-VMs hilft. Hierzu gehören Features wie automatisierte Sicherungen und Patches, Azure Key Vault-Integration, Lizenzierungsverwaltung, Speicherkonfiguration und zentrale Verwaltung aller SQL Server-VM-Instanzen.
services: virtual-machines-windows
documentationcenter: ''
author: adbadram
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: seo-lt-2019, ignite-fall-2021
ms.openlocfilehash: 35ea46e4ce2b7c4ebbb6fdfa24bbe1d60e679bb7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345698"
---
# <a name="automate-management-with-the-windows-sql-server-iaas-agent-extension"></a>Automatisieren der Verwaltung mit der Windows SQL Server-IaaS-Agent-Erweiterung
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-iaas-agent-extension-automate-management.md)
> * [Linux](../linux/sql-server-iaas-agent-extension-linux.md)



Die Erweiterung für den SQL Server-IaaS-Agent (SqlIaasExtension) kann auf virtuellen Windows Azure-Computern ausgeführt werden, um Verwaltungsaufgaben zu automatisieren. 

Dieser Artikel bietet eine Übersicht über die Erweiterung. Informationen zum Installieren der SQL Server-IaaS-Erweiterung für SQL Server auf Azure-VMs finden Sie in den Artikeln zur [automatischen Installation](sql-agent-extension-automatic-registration-all-vms.md) und zur [Installation auf einzelnen VMs](sql-agent-extension-manually-register-single-vm.md) bzw. zur [Masseninstallation auf VMs](sql-agent-extension-manually-register-vms-bulk.md). 

> [!NOTE]
> Ab September 2021 ist für die Registrierung bei der SQL-IaaS-Erweiterung im vollständigen Modus kein Neustart des SQL Server-Diensts mehr erforderlich. 

## <a name="overview"></a>Übersicht

Die Erweiterung für SQL Server-IaaS-Agent ermöglicht die Integration mit dem Azure-Portal und schaltet je nach Verwaltungsmodus eine Reihe von Featurevorteilen für SQL Server auf virtuellen Azure-Computern frei: 

- **Featurevorteile:** Die Erweiterung macht verschiedene Automatisierungsfeatures wie Portalverwaltung, flexible Lizenzierung, automatisierte Sicherungen und Patches und andere verfügbar. Weitere Informationen finden Sie unter [Featurevorteile](#feature-benefits) weiter unten in diesem Artikel. 

- **Compliance**: Mit der Erweiterung können Sie auch einfacher Ihrer Verpflichtung gemäß den Nutzungsbedingungen zum Produkt nachkommen, Microsoft zu benachrichtigen, wenn der Azure-Hybridvorteil aktiviert wurde. Auf diese Weise können Sie darauf verzichten, für jede Ressource ein Lizenzregistrierungsformular zu verwalten.  

- **Free:** Die Erweiterung ist in allen drei Verwaltbarkeitsmodi vollständig kostenlos. Es fallen keine zusätzlichen Kosten für die Erweiterung oder für das Ändern von Verwaltungsmodi an. 

- **Vereinfachte Lizenzverwaltung**: Die Erweiterung vereinfacht die SQL Server-Lizenzverwaltung. Außerdem können Sie über das [Azure-Portal](manage-sql-vm-portal.md), PowerShell oder die Azure CLI schnell SQL Server-VMs mit aktiviertem Azure-Hybridvorteil identifizieren: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---



## <a name="feature-benefits"></a>Featurevorteile 

Die SQL Server-IaaS-Agent-Erweiterung macht eine Reihe von Featurevorteilen für die Verwaltung Ihrer SQL Server-VMs verfügbar. 

Diese Vorteile werden in der folgenden Tabelle genauer erläutert: 


| Funktion | Beschreibung |
| --- | --- |
| **Portalverwaltung** | Ermöglicht die [Verwaltung im Portal](manage-sql-vm-portal.md), sodass Sie alle Ihre SQL Server-VMs an einem Ort anzeigen und SQL-spezifische Features direkt über das Portal aktivieren und deaktivieren können. <br/> Verwaltungsmodus: Lightweight und vollständig|  
| **Automatisierte Sicherung** |Automatisiert die Planung von Sicherungen für alle Datenbanken entweder der Standardinstanz oder einer [ordnungsgemäß installierten](./frequently-asked-questions-faq.yml) benannten Instanz von SQL Server in der VM. Weitere Informationen finden Sie unter [Automatisierte Sicherung für SQL Server auf virtuellen Azure-Computern (Resource Manager)](automated-backup-sql-2014.md). <br/> Verwaltungsmodus: Vollständig|
| **Automatisiertes Patchen** |Mit diesem Feature können Sie ein Wartungsfenster konfigurieren, in dem wichtige Windows- und SQL Server-Sicherheitsupdates für die VM ausgeführt werden. Auf diese Weise werden Updates für Ihre Workload während der Spitzenzeiten vermieden. Weitere Informationen finden Sie unter [Automatisiertes Patchen für SQL Server auf virtuellen Azure-Computern (Resource Manager)](automated-patching.md). <br/> Verwaltungsmodus: Vollständig|
| **Azure Key Vault-Integration** |Mit diesem Dienst können Sie Azure Key Vault auf Ihrem virtuellen SQL Server-Computer automatisch installieren und konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren der Azure Key Vault-Integration für SQL Server auf virtuellen Azure-Computern (Resource Manager)](azure-key-vault-integration-configure.md). <br/> Verwaltungsmodus: Vollständig|
| **Anzeigen der Datenträgerauslastung im Portal** | Ermöglicht eine grafische Darstellung der Datenträgerauslastung Ihrer SQL-Datendateien im Azure-Portal.  <br/> Verwaltungsmodus: Vollständig | 
| **Flexible Lizenzierung** | Durch einen [nahtlosen Wechsel](licensing-model-azure-hybrid-benefit-ahb-change.md) zwischen den Lizenzmodellen „Bring-Your-Own-License“ (BYOL, auch als „Azure-Hybridvorteil“ bezeichnet) und „Nutzungsbasierte Bezahlung“ können Sie Kosten sparen. <br/> Verwaltungsmodus: Lightweight und vollständig| 
| **Flexible Version/Edition** | Wenn Sie die [Version](change-sql-server-version.md) oder [Edition](change-sql-server-edition.md) von SQL Server ändern möchten, können Sie einfach die Metadaten im Azure-Portal aktualisieren, ohne die gesamte SQL Server-VM erneut bereitstellen zu müssen.  <br/> Verwaltungsmodus: Lightweight und vollständig| 
| **Integration des Defender für Cloudportals** | Wenn Sie [Microsoft Defender für SQL](../../../security-center/defender-for-sql-usage.md) aktiviert haben, können Sie die Empfehlungen von Defender for Cloud direkt in der Ressource [SQL Virtual Machines](manage-sql-vm-portal.md) des Azure-Portals einsehen. Weitere Informationen finden Sie unter [Bewährte Sicherheitsmethoden](security-considerations-best-practices.md).  <br/> Verwaltungsmodus: Lightweight und vollständig|
| **SQL-Bewertung (Vorschau)** | Ermöglicht Ihnen, die Integrität Ihrer SQL Server-VMs mithilfe von bewährten Methoden für die Konfiguration zu bewerten. Weitere Informationen finden Sie unter [SQL-Bewertung](sql-assessment-for-sql-vm.md).  <br/> Verwaltungsmodus: Vollständig| 


## <a name="management-modes"></a>Verwaltungsmodi

Sie können Ihre SQL-IaaS-Erweiterung in drei Verwaltungsmodi registrieren: 

- Der **Lightweight**-Modus kopiert Erweiterungsbinärdateien auf die VM, installiert jedoch nicht den Agent. Der Lightweight-Modus unterstützt _nur_ das Ändern des Lizenztyps und der Edition von SQL Server und bietet eine eingeschränkte Portalverwaltung. Verwenden Sie diese Option für SQL Server-VMs mit mehreren Instanzen oder für den Einsatz mit einer Failoverclusterinstanz (FCI). Der Lightweight-Modus ist der Standardverwaltungsmodus bei Verwendung des Features [Automatische Registrierung](sql-agent-extension-automatic-registration-all-vms.md) und wenn bei der manuellen Registrierung kein Verwaltungstyp angegeben wurde. Die Verwendung des Modus „Lightweight“ hat keine Auswirkungen auf den Arbeitsspeicher oder die CPU. Außerdem fallen keine Kosten an. 

- Der **vollständige** Modus installiert den SQL-IaaS-Agent auf der VM, um vollständige Funktionen bereitzustellen. Verwenden Sie diesen Modus zum Verwalten einer SQL Server-VM mit einer einzelnen Instanz. Für den Modus „Vollständig“ werden zwei Windows-Dienste installiert, die minimale Auswirkungen auf den Arbeitsspeicher und die CPU haben. Diese Dienste können über den Task-Manager überwacht werden. Mit der Verwendung des Verwaltbarkeitsmodus „Vollständig“ sind keine Kosten verbunden. Es sind Systemadministratorberechtigungen erforderlich. Ab September 2021 ist ein Neustart des SQL Server-Diensts nicht mehr erforderlich, wenn Sie Ihre SQL Server-VM im vollständigen Verwaltungsmodus registrieren. 

- Der Modus **NoAgent** ist speziell für Installationen von SQL Server 2008 und SQL Server 2008 R2 unter Windows Server 2008 vorgesehen. Es gibt keine Auswirkung auf den Arbeitsspeicher oder die CPU, wenn der Modus „NoAgent“ verwendet wird. Es fallen keine Kosten für die Verwendung des Verwaltbarkeitsmodus „NoAgent“ an, der SQL Server-Dienst wird nicht neu gestartet, und es wird kein Agent auf dem virtuellen Computer installiert. 

Über Azure PowerShell können Sie den aktuellen Modus des SQL Server-IaaS-Agents anzeigen: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>Installation

Registrieren Sie Ihre SQL Server-VM mit der SQL Server-IaaS-Agent-Erweiterung, um die [_Ressource_ **Virtueller SQL-Computer**](manage-sql-vm-portal.md) in Ihrem Abonnement zu erstellen. Dabei handelt es sich um eine _andere_ Ressource als die Ressource für Ihren virtuellen Computer. Wenn Sie die Registrierung der Erweiterung für Ihre SQL Server-VM aufheben, wird die _Ressource_ **Virtueller SQL-Computer** aus Ihrem Abonnement entfernt, während der tatsächliche virtuelle Computer jedoch erhalten bleibt.

Wenn Sie ein Azure Marketplace-Image einer SQL Server-VM über das Azure-Portal bereitstellen, wird die SQL Server-VM mit der Erweiterung automatisch im vollständigen Modus registriert. Wenn Sie jedoch SQL Server auf einer Azure-VM selbst installieren oder eine Azure-VM von einer benutzerdefinierten VHD bereitstellen, müssen Sie Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren, um die Featurevorteile nutzen zu können. 

Wenn Sie die Erweiterung im Lightweight-Modus registrieren, werden die Binärdateien kopiert, der Agent wird jedoch nicht auf der VM installiert. Der Agent wird auf der VM installiert, wenn die Erweiterung im vollständigen Verwaltungsmodus installiert wird. 

Es gibt drei Möglichkeiten, VMs mit der Erweiterung zu registrieren: 
- [Automatisch für alle aktuellen und zukünftigen VMs in einem Abonnement](sql-agent-extension-automatic-registration-all-vms.md)
- [Manuell für eine einzelne VM](sql-agent-extension-manually-register-single-vm.md)
- [Manuell für mehrere VMs in einem Massenvorgang](sql-agent-extension-manually-register-vms-bulk.md)

Standardmäßig werden Azure-VMs, auf denen SQL Server 2016 oder höher installiert ist, automatisch bei der Erweiterung für den SQL-IaaS-Agent registriert, wenn sie vom [CEIP-Dienst](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server) erkannt werden.  Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).


### <a name="named-instance-support"></a>Unterstützung für benannte Instanzen

Die SQL Server-IaaS-Agent-Erweiterung funktioniert mit einer benannten Instanz von SQL Server, wenn es sich dabei um die einzige SQL Server-Instanz handelt, die auf der VM verfügbar ist. Wenn ein virtueller Computer über mehrere benannte SQL Server-Instanzen und keine Standardinstanz verfügt, wird die SQL-IaaS-Erweiterung im Lightweight-Modus registriert und wählt entweder die Instanz mit der höchsten Edition oder die erste Instanz aus, wenn alle Instanzen dieselbe Edition aufweisen. 

Wenn Sie eine benannte Instanz von SQL Server verwenden möchten, stellen Sie eine Azure-VM bereit, installieren Sie eine einzelne benannte SQL Server-Instanz darauf, und registrieren Sie die SQL Server-VM dann mit der [SQL-IaaS-Erweiterung](sql-agent-extension-manually-register-single-vm.md).

Falls Sie stattdessen eine benannte Instanz mit einem SQL Server-Image aus dem Azure Marketplace verwenden möchten, führen Sie die folgenden Schritte aus: 

   1. Stellen Sie eine SQL Server-VM vom Azure Marketplace bereit. 
   1. [Heben Sie die Registrierung der SQL-IaaS-Agent-Erweiterung für die SQL Server-VM auf](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 
   1. Deinstallieren Sie SQL Server in der SQL Server-VM vollständig.
   1. Installieren Sie SQL Server mit einer benannten Instanz in der SQL Server-VM. 
   1. [Registrieren Sie die VM mit der SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md#full-mode). 

## <a name="verify-status-of-extension"></a>Überprüfen des Status der Erweiterung

Sie können den Status der Erweiterung über das Azure-Portal oder mit Azure PowerShell überprüfen. 

### <a name="azure-portal"></a>Azure-Portal

Vergewissern Sie sich im Azure-Portal, dass die Erweiterung installiert ist. 

Wechseln Sie zu Ihrer Ressource **Virtueller Computer** im Azure-Portal (nicht zur Ressource *Virtuelle SQL-Computer*, sondern zur Ressource für Ihre VM). Wählen Sie unter **Einstellungen** die Option **Erweiterungen** aus.  Die Erweiterung **SqlIaasExtension** sollte wie im folgenden Beispiel aufgeführt werden: 

![Status der SQL Server-IaaS-Agent-Erweiterung im Azure-Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


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

Die SQL-IaaS-Agent-Erweiterung unterstützt nur Folgendes: 

- Über den Azure Resource Manager bereitgestellte SQL Server-VMs. SQL Server-VMs, die mit dem klassischen Modell bereitgestellt wurden, werden nicht unterstützt. 
- In der öffentlichen Cloud oder Azure Government-Cloud bereitgestellte SQL Server-VMs. Bereitstellungen in anderen privaten Clouds oder Government Clouds werden nicht unterstützt. 
- Failoverclusterinstanzen (FCIs) im Lightweight-Modus. 
- Benannte Instanzen mit mehreren Instanzen auf einem einzelnen virtuellen Computer im Lightweight-Modus. 


## <a name="privacy-statement"></a><a id="in-region-data-residency"></a> Datenschutzbestimmungen

Wenn Sie SQL Server auf Azure-VMs und die SQL IaaS-Erweiterung verwenden, berücksichtigen Sie die folgenden Datenschutzbestimmungen: 

- **Datensammlung**: Die SQL-IaaS-Agent-Erweiterung sammelt Daten ausschließlich, um Kunden zu ermöglichen, bei der Verwendung von SQL Server unter Azure Virtual Machines optionale Vorteile zu nutzen. Microsoft verwendet diese Daten ohne vorherige Zustimmung des Kunden **nicht für Lizenzierungsüberprüfungen**. Weitere Informationen finden Sie in der [SQL Server-Datenschutzergänzung](/sql/sql-server/sql-server-privacy#non-personal-data).

- **Datenresidenz in der Region**: SQL Server auf Azure-VMs und die SQL IaaS-Agent-Erweiterung verschieben Kundendaten nicht aus der Region, in der die VMs bereitgestellt werden, oder speichern sie dort.


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Installieren der SQL Server-IaaS-Erweiterung für SQL Server auf Azure-VMs finden Sie in den Artikeln zur [automatischen Installation](sql-agent-extension-automatic-registration-all-vms.md) und zur [Installation auf einzelnen VMs](sql-agent-extension-manually-register-single-vm.md) bzw. zur [Masseninstallation auf VMs](sql-agent-extension-manually-register-vms-bulk.md).

Ausführlichere Informationen zur Verwendung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Was ist SQL Server auf virtuellen Azure-Computern?](sql-server-on-azure-vm-iaas-what-is-overview.md).

Weitere Informationen finden Sie unter [Häufig gestellte Fragen](frequently-asked-questions-faq.yml).
