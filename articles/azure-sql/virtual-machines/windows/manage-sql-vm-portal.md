---
title: Verwalten von SQL Server-VMs in Azure im Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie im Azure-Portal auf die Ressource „Virtuelle SQL-Computer“ für eine in Azure gehostete SQL Server-VM zugreifen, um SQL Server-Einstellungen zu ändern.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2021
ms.author: pamela
ms.reviewer: mathoma
ms.custom: ignite-fall-2021
ms.openlocfilehash: 602cb3affc441c92dfa29ac41de893c96a617ff7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301872"
---
# <a name="manage-sql-server-vms-by-using-the-azure-portal"></a>Verwalten von SQL Server-VMs über das Azure-Portal
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Im [Azure-Portal](https://portal.azure.com) ist die Ressource [**Virtuelle SQL-Computer**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) ein unabhängiger Verwaltungsdienst zum Verwalten von SQL Server auf virtuellen Azure-Computern (Virtual Machines, VMs), die bei der SQL Server-IaaS-Agent-Erweiterung registriert wurden. Sie können mit der Ressource alle SQL Server-VMs gleichzeitig anzeigen und Einstellungen für SQL Server ändern: 

![Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-manage.png)

Der Ressourcenverwaltungspunkt **Virtuelle SQL-Computer** unterscheidet sich von der Ressource **Virtueller Computer**, die zum Verwalten des virtuellen Computers verwendet wird, z. B. zum Starten, Beenden oder Neustarten. 


## <a name="prerequisite"></a>Voraussetzung 

Die Ressource **Virtuelle SQL-Computer** ist nur für SQL Server-VMs verfügbar, die [bei der SQL-IaaS-Agent-Erweiterung registriert wurden](sql-agent-extension-manually-register-single-vm.md). 


## <a name="access-the-resource"></a>Zugreifen auf die Ressource

Führen Sie zum Zugreifen auf die Ressource **SQL-VMs** folgende Schritte aus:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). 
1. Wählen Sie **Alle Dienste** aus. 
1. Geben Sie in das Suchfeld **SQL-VMs** ein.
1. (Optional): Wählen Sie das Sternsymbol neben **SQL-VMs** aus, um diese Option Ihrem Menü **Favoriten** hinzuzufügen. 
1. Wählen Sie **Virtuelle SQL-Computer** aus. 

   ![Ermitteln von virtuellen SQL Server-Computern in allen Diensten](./media/manage-sql-vm-portal/sql-vm-search.png)

1. Im Portal werden alle SQL Server-VMs aufgelistet, die im Abonnement verfügbar sind. Wählen Sie die zu verwaltende VM aus, um die Ressource **SQL-VMs** zu öffnen. Verwenden Sie das Suchfeld, wenn Ihre SQL Server-VM nicht angezeigt wird. 

   ![Alle verfügbaren SQL Server-VMs](./media/manage-sql-vm-portal/all-sql-vms.png)

   Durch Auswählen der SQL Server-VM wird die Ressource **SQL-VMs** geöffnet: 


   ![Anzeigen der Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> Die Ressource **Virtuelle SQL-Computer** ist für SQL Server-Einstellungen vorgesehen. Wählen Sie im Feld **Virtueller Computer** den Namen des virtuellen Computers aus, um VM-spezifische Einstellungen zu öffnen, die nicht ausschließlich für SQL Server gelten. 


## <a name="license-and-edition"></a>Lizenz und Edition 

Ändern Sie mit der Seite **Konfigurieren** der Ressource „Virtuelle SQL-Computer“ Ihre SQL Server-Lizenzierungsmetadaten für **Nutzungsbasierte Bezahlung**, **Azure-Hybridvorteil** oder **HA/DR** für Ihr [kostenloses Azure-Replikat für die Notfallwiederherstellung](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).



![Ändern der Version und Edition der SQL Server-VM-Metadaten im Azure-Portal mithilfe der Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-license-edition.png)

Sie können die Edition von SQL Server auch über die Seite **Konfigurieren** ändern, z. B. **Enterprise**, **Standard** oder **Entwickler**. 

Das Ändern der Lizenz- und Editionsmetadaten im Azure-Portal wird nur unterstützt, sobald die Version und Edition von SQL Server intern auf dem virtuellen Computer geändert sind. Lesen Sie weitere Informationen zum Ändern von [Version](change-sql-server-version.md) und [Edition](change-sql-server-edition.md) von SQL Server auf Azure-VMs. 

## <a name="storage"></a>Speicher 

Erweitern Sie mit der Seite **Konfigurieren** der Ressource „Virtuelle SQL-Computer“ Ihre Daten-, Protokoll- und tempdb-Laufwerke. 

![Erweitern des Speichers im Azure-Portal mithilfe der Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-storage-configuration.png)

## <a name="patching"></a>Patching

Aktivieren Sie mit der Seite **Patchen** der Ressource „Virtuelle SQL-Computer“ das automatische Patchen Ihres virtuellen Computers und automatische Installieren von Windows- und SQL Server-Updates, die als wichtig gekennzeichnet sind. Sie können hier auch einen Wartungszeitplan konfigurieren, z. B. das tägliche Ausführen von Patches, sowie eine lokale Startzeit für die Wartung und ein Wartungsfenster. 


![Konfigurieren des automatisierten Patchens und Planens im Azure-Portal mithilfe der Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-automated-patching.png)


Weitere Informationen finden Sie unter [Automatisiertes Patchen für SQL Server auf Azure-VMs (Resource Manager)](automated-patching.md). 



## <a name="backups"></a>Backups

Konfigurieren Sie mit der Seite **Sicherungen** der Ressource „Virtuelle SQL-Computer“ Ihre Einstellungen für die automatisierte Sicherung, z. B. die Aufbewahrungsdauer, das zu verwendende Speicherkonto, die Verschlüsselung, ob Systemdatenbanken gesichert werden sollen und einen Sicherungszeitplan. 

![Konfigurieren der automatisierten Sicherung und des Planens im Azure-Portal mithilfe der Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-automated-backup.png)

Weitere Informationen finden Sie unter [Automatisiertes Patchen für SQL Server auf Azure-VMs (Resource Manager)](automated-backup.md). 


## <a name="high-availability-preview"></a>Hochverfügbarkeit (Vorschau)

Erstellen Sie auf der Seite **Hochverfügbarkeit** der Ressource „Virtuelle SQL-Computer“ einen Windows Server-Failovercluster, und konfigurieren Sie eine Always On-Verfügbarkeitsgruppe, einen Verfügbarkeitsgruppenlistener und Azure Load Balancer. Das Konfigurieren von Hochverfügbarkeit im Azure-Portal befindet sich derzeit in der Vorschau. 


![Konfigurieren eines Windows Server-Failoverclusters und einer Always On-Verfügbarkeitsgruppe im Azure-Portal mithilfe der Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-high-availability.png)


Weitere Informationen finden Sie unter [Verwenden des Azure-Portals zum Konfigurieren einer Verfügbarkeitsgruppe (Vorschau) für SQL Server auf einem virtuellen Azure-Computer](availability-group-azure-portal-configure.md).

## <a name="security-configuration"></a>Sicherheitskonfiguration 

Konfigurieren Sie mit der Seite **Sicherheitskonfiguration** der Ressource „Virtuelle SQL-Computer“ SQL Server-Sicherheitseinstellungen, z. B. den zu verwendenden Port, unabhängig davon, ob die SQL-Authentifizierung aktiviert ist, und um die Integration von Azure Key Vault zu aktivieren. 

![Konfigurieren der SQL Server-Sicherheit im Azure-Portal mithilfe der Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-security-configuration.png)

Weitere Informationen finden Sie unter [Bewährte Sicherheitsmethoden](security-considerations-best-practices.md).

<a name="security-center"></a>

## <a name="defender-for-cloud"></a>Defender für Cloud 

Zeigen Sie auf der Seite **Defender für SQL** der Ressource des virtuelle SQL-Computers die Empfehlungen von Defender für Cloud direkt auf dem Blade des virtuellen SQL-Computers an. Aktivieren Sie [Microsoft Defender für SQL](../../../security-center/defender-for-sql-usage.md), um diese Funktion zu nutzen. 

![Konfigurieren von Defender-für-Cloud-Einstellungen der SQL-Server im Azure-Portal mithilfe der Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-security-center.png)

## <a name="sql-assessment-preview"></a>SQL-Bewertung (Vorschau)

Auf der Seite **SQL-Bewertung** der SQL-VM-Ressource können Sie die Integrität Ihrer SQL Server-VM bewerten. Nach Aktivierung des Features werden Ihre SQL Server-Instanzen und -Datenbanken überprüft und es werden Empfehlungen zur Verbesserung der Leistung (Indizes, Statistiken, Ablaufverfolgungsflags usw.) und zur Ermittlung fehlender Konfigurationen gemäß bewährter Methoden angezeigt. Die SQL-Bewertung befindet sich derzeit in der Vorschauphase. 

:::image type="content" source="./media/manage-sql-vm-portal/sql-vm-assessment-workbook.png" alt-text="Anzeigen von SQL-Bewertungen im Azure-Portal auf der Seite der SQL-VM-Ressource." lightbox="./media/manage-sql-vm-portal/sql-vm-assessment-workbook.png":::

Weitere Informationen finden Sie unter [SQL-Bewertung für SQL Server auf Azure-VMs](sql-assessment-for-sql-vm.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln: 

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern](frequently-asked-questions-faq.yml)
* [Preisinformationen für SQL Server auf virtuellen Windows-Computern](pricing-guidance.md)
* [Neuerungen für SQL Server auf virtuellen Azure-Computern](doc-changes-updates-release-notes-whats-new.md)
