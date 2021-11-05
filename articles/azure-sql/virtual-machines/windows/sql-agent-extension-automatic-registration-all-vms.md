---
title: Automatische Registrierung von VMs mit der SQL-IaaS-Agent-Erweiterung
description: Hier erfahren Sie, wie Sie im Azure-Portal die automatische Registrierungsfunktion aktivieren, um alle bisherigen und zukünftigen SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung zu registrieren.
author: adbadram
ms.author: adbadram
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.custom: devx-track-azurepowershell
ms.reviewer: mathoma
ms.openlocfilehash: 6a29c239b223c016d436136a225ca1bd8784b919
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071874"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>Automatische Registrierung von VMs mit der SQL-IaaS-Agent-Erweiterung
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Aktivieren Sie die automatische Registrierungsfunktion im Azure-Portal, um alle aktuellen und zukünftigen SQL Server-Instanzen auf virtuellen Azure-Computern (Virtual Machines, VMs) mit der [SQL-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) automatisch im Modus „Lightweight“ zu registrieren. Standardmäßig werden virtuelle Azure-Computer, auf denen SQL Server 2016 oder höher installiert ist, automatisch bei der Erweiterung für den SQL-IaaS-Agent registriert, wenn sie vom [CEIP-Dienst](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server) erkannt werden. Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

In diesem Artikel erfahren Sie, wie Sie die automatische Registrierungsfunktion aktivieren. Alternativ können Sie [eine einzelne VM](sql-agent-extension-manually-register-single-vm.md) oder [mehrere Ihrer VMs in einem Massenvorgang](sql-agent-extension-manually-register-vms-bulk.md) mit der SQL-IaaS-Agent-Erweiterung registrieren. 

> [!NOTE]
> Ab September 2021 ist für die Registrierung bei der SQL-IaaS-Erweiterung im vollständigen Modus kein Neustart des SQL Server-Diensts mehr erforderlich. 

## <a name="overview"></a>Übersicht

Wenn Sie Ihre SQL Server-VM mit der [SQL-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) registrieren, können Sie den vollen Funktionsumfang nutzen. 

Wenn die automatische Registrierung aktiviert ist, wird täglich ein Auftrag ausgeführt, um zu ermitteln, ob SQL Server auf allen nicht registrierten virtuellen Computern im Abonnement installiert ist. Hierzu werden die Binärdateien der Erweiterung für den SQL-IaaS-Agent auf den virtuellen Computer kopiert. Anschließend wird einmalig ein Hilfsprogramm ausgeführt, um nach der SQL Server-Registrierungsstruktur zu suchen. Wenn die SQL Server-Struktur erkannt wird, erfolgt die Registrierung des virtuellen Computers mit der Erweiterung im Modus „Lightweight“. Ist in der Registrierung keine SQL Server-Struktur vorhanden, werden die Binärdateien entfernt. Bei der automatischen Registrierung kann es bis zu 4 Tage dauern, bis neu erstellte SQL Server-VMs erkannt werden.

> [!CAUTION]
> Wenn die SQL Server-Struktur nicht in der Registrierung vorhanden ist, kann das Entfernen der Binärdateien beeinträchtigt werden, wenn [Ressourcensperren](../../../governance/blueprints/concepts/resource-locking.md#locking-modes-and-states) vorhanden sind. 


Nach der Aktivierung der automatischen Registrierung für ein Abonnement werden alle aktuellen und zukünftigen VMs, auf denen SQL Server installiert ist, mit der SQL-IaaS-Agent-Erweiterung im Modus „Lightweight“ registriert. **Dies geschieht ohne Ausfallzeiten, und der SQL Server-Dienst muss nicht neu gestartet werden.** Sie müssen weiterhin [manuell auf den Modus „Vollständige Verwaltbarkeit“ aktualisieren](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full), um den vollständigen Fuktionssatz nutzen zu können. Der Lizenztyp wird standardmäßig automatisch auf den des VM-Images eingestellt. Wenn Sie ein Image mit nutzungsbasierter Bezahlung für Ihren virtuellen Computer verwenden, lautet Ihr Lizenztyp `PAYG`, andernfalls ist Ihr Lizenztyp standardmäßig `AHUB`. 

Standardmäßig werden Azure-VMs, auf denen SQL Server 2016 oder höher installiert ist, automatisch bei der Erweiterung für den SQL-IaaS-Agent registriert, wenn sie vom [CEIP-Dienst](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server) erkannt werden.  Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

> [!IMPORTANT]
> Die SQL-IaaS-Agent-Erweiterung sammelt Daten ausschließlich, um Kunden zu ermöglichen, bei der Verwendung von SQL Server in Azure Virtual Machines optionale Vorteile zu nutzen. Microsoft verwendet diese Daten ohne vorherige Zustimmung des Kunden nicht für Lizenzierungsüberprüfungen. Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).




## <a name="prerequisites"></a>Voraussetzungen

Um Ihre SQL Server-VM mit der Erweiterung registrieren zu können, benötigen Sie Folgendes: 

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/) und mindestens Berechtigungen der [Rolle „Mitwirkender“](../../../role-based-access-control/built-in-roles.md#all).
- Ein in der öffentlichen Cloud oder in der Azure Government-Cloud bereitgestelltes Azure-Ressourcenmodell vom Typ [Virtueller Computer mit Windows Server 2008 R2 (oder höher)](../../../virtual-machines/windows/quick-create-portal.md) mit [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads). Windows Server 2008 wird nicht unterstützt. 


## <a name="enable"></a>Aktivieren

Führen Sie die folgenden Schritte aus, um im Azure-Portal die automatische Registrierung Ihrer virtuellen SQL Server-Computer zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zur Ressourcenseite [**Virtuelle SQL-Computer**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Wählen Sie **Automatische SQL Server-VM-Registrierung** aus, um die Seite **Automatische Registrierung** zu öffnen. 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="„Automatische SQL Server-VM-Registrierung“ auswählen, um die Seite „Automatische Registrierung“ zu öffnen":::

1. Wählen Sie im Dropdownmenü Ihr Abonnement aus. 
1. Lesen Sie die Nutzungsbedingungen, und wählen Sie, wenn Sie zustimmen, **Ich stimme zu** aus. 
1. Wählen Sie **Registrieren** aus, um die Funktion zu aktivieren und alle aktuellen und zukünftigen SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung automatisch zu registrieren. Dadurch wird der SQL Server-Dienst auf keiner der VMs neu gestartet. 

## <a name="disable"></a>Deaktivieren

Verwenden Sie die [Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell](/powershell/azure/install-az-ps), um die automatische Registrierungsfunktion zu deaktivieren. Wenn die automatische Registrierungsfunktion deaktiviert ist, müssen SQL Server-VMs, die dem Abonnement hinzugefügt werden, mit der SQL-IaaS-Agent-Erweiterung manuell registriert werden. Eine Deaktivierung der Funktion führt nicht dazu, dass die Registrierung vorhandener SQL Server-VMs aufgehoben wird, die bereits registriert wurden.



# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie den folgenden Befehl aus, um die automatische Registrierung mit Azure CLI zu deaktivieren: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Führen Sie den folgenden Befehl aus, um die automatische Registrierung mit Azure PowerShell zu deaktivieren: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Aktivieren für mehrere Abonnements

Sie können die automatische Registrierungsfunktion mit PowerShell für mehrere Azure-Abonnements aktivieren. 

Gehen Sie dazu folgendermaßen vor:

1. Speichern Sie [dieses Skript](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/EnableBySubscription.ps1).
1. Navigieren Sie in einem administrativen Eingabeaufforderungs- oder PowerShell-Fenster zu dem Speicherort, in dem Sie das Skript gespeichert haben. 
1. Stellen Sie eine Verbindung mit Azure her `az login`.
1. Führen Sie das Skript aus, wobei Sie die Abonnement-IDs (SubscriptionIds) als Parameter übergeben. Wenn keine Abonnements angegeben werden, aktiviert das Skript die automatische Registrierung für alle Abonnements im Benutzerkonto.    

   Der folgende Befehl aktiviert die automatische Registrierung für zwei Abonnements: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```
   Der folgende Befehl aktiviert die automatische Registrierung für alle Abonnements: 

   ```console
   .\EnableBySubscription.ps1
   ```

Fehler bei der Registrierung werden in der Datei `RegistrationErrors.csv` gespeichert, die sich in dem Verzeichnis befindet, in dem Sie das `.ps1`-Skript gespeichert und ausgeführt haben. 

## <a name="next-steps"></a>Nächste Schritte

Aktualisieren Sie den Verwaltbarkeitsmodus auf [Vollständig](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full), um alle von der SQL-IaaS-Agent-Erweiterung bereitgestellten Funktionen nutzen zu können.