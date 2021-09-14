---
title: Verwalten von Sicherungen mit der rollenbasierten Zugriffssteuerung in Azure
description: Verwenden Sie die rollenbasierte Zugriffssteuerung in Azure zum Verwalten des Zugriffs auf Vorgänge der Sicherungsverwaltung im Recovery Services-Tresor.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: fdde385ca49a61a8fb2c2bba81311035dca3e324
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123215183"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Verwenden der rollenbasierten Zugriffssteuerung in Azure zum Verwalten von Azure Backup-Wiederherstellungspunkten

Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure ermöglicht eine präzise Zugriffsverwaltung für Azure. Mithilfe von Azure RBAC können Sie Aufgaben in Ihrem Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen.

> [!IMPORTANT]
> Rollen, die von Azure Backup bereitgestellt werden, sind auf Aktionen beschränkt, die im Azure-Portal, über die REST-API oder mit PowerShell- oder CLI-Cmdlets für Recovery Services-Tresore ausgeführt werden können. Aktionen, die auf der Benutzeroberfläche des Azure Backup-Agent-Clients oder der Benutzeroberfläche von System Center Data Protection Manager oder der Benutzeroberfläche von Azure Backup Server ausgeführt werden, werden von diesen Rollen nicht gesteuert.

Azure Backup bietet drei integrierte Rollen, um Vorgänge der Sicherungsverwaltung zu steuern. Weitere Informationen zu [in Azure integrierten Rollen](../role-based-access-control/built-in-roles.md)

* [Mitwirkender für Sicherungen](../role-based-access-control/built-in-roles.md#backup-contributor): Diese Rolle verfügt über alle Berechtigungen zum Erstellen und Verwalten von Sicherungen. Mit dieser Rolle können aber keine Recovery Services-Tresore gelöscht werden, und anderen Personen kann kein Zugriff gewährt werden. Stellen Sie sich diese Rolle als Administrator der Sicherungsverwaltung vor, der alle Vorgänge der Sicherungsverwaltung ausführen kann.
* [Sicherungsoperator](../role-based-access-control/built-in-roles.md#backup-operator): Diese Rolle verfügt über Berechtigungen für alles, was ein Mitwirkender ausführen kann, außer Entfernen von Sicherungen und Verwalten von Sicherungsrichtlinien. Diese Rolle ist gleichbedeutend mit einem Mitwirkenden, es können damit jedoch keine destruktiven Vorgänge ausgeführt werden, z.B. Beenden einer Sicherung mit Löschung von Daten oder Entfernen die Registrierung von lokalen Ressourcen.
* [Sicherungsleser](../role-based-access-control/built-in-roles.md#backup-reader): Diese Rolle verfügt über Berechtigungen zum Anzeigen aller Vorgänge für die Sicherungsverwaltung. Stellen Sie sich diese Rolle für eine Überwachungsperson vor.

Wenn Sie Ihre eigenen Rollen definieren möchten, um eine noch bessere Kontrolle zu haben, helfen Ihnen die Informationen zum [Erstellen von benutzerdefinierten Rollen](../role-based-access-control/custom-roles.md) in Azure RBAC weiter.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Zuordnen der integrierten Backup-Rollen zu Aktionen der Sicherungsverwaltung

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Mindestrollenanforderungen für die Azure-VM-Sicherung

In der folgenden Tabelle sind die Aktionen der Sicherungsverwaltung und die entsprechende minimale Azure-Rolle aufgeführt, die zum Ausführen des jeweiligen Vorgangs erforderlich ist.

| Verwaltungsvorgang | Minimal erforderliche Azure-Rolle | Bereich erforderlich | Alternative |
| --- | --- | --- | --- |
| Erstellen eines Recovery Services-Tresors | Mitwirkender für Sicherungen | Ressourcengruppe mit dem Tresor |   |
| Aktivieren der Sicherung von virtuellen Azure-Computern | Sicherungsoperator | Ressourcengruppe mit dem Tresor |   |
| | Mitwirkender von virtuellen Computern | VM-Ressource |  Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Compute/virtualMachines/write |
| Bedarfsgesteuerte Sicherung eines virtuellen Computers | Sicherungsoperator | Recovery Services-Tresor |   |
| Wiederherstellen eines virtuellen Computers | Sicherungsoperator | Recovery Services-Tresor |   |
| | Mitwirkender | Ressourcengruppe, in der der virtuelle Computer bereitgestellt wird. |   Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Resources/subscriptions/resourceGroups/write Microsoft.DomainRegistration/domains/write, Microsoft.Compute/virtualMachines/write Microsoft.Network/virtualNetworks/read Microsoft.Network/virtualNetworks/subnets/join/action |
| | Mitwirkender von virtuellen Computern | Gesicherte Quell-VM |   Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Compute/virtualMachines/write |
| Wiederherstellen von VM-Sicherung nicht verwalteter Datenträger | Sicherungsoperator | Recovery Services-Tresor |
| | Mitwirkender von virtuellen Computern | Gesicherte Quell-VM | Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Compute/virtualMachines/write |
| | Mitwirkender von Speicherkonto | Speicherkontoressource, in dem Datenträger wiederhergestellt werden sollen |   Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Storage/storageAccounts/write |
| Wiederherstellen von verwalteten Datenträgern aus VM-Sicherung | Sicherungsoperator | Recovery Services-Tresor |
| | Mitwirkender von virtuellen Computern | Gesicherte Quell-VM |    Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Compute/virtualMachines/write |
| | Mitwirkender von Speicherkonto | Temporäres Speicherkonto, das als Teil der Wiederherstellung ausgewählt wurde, um Daten aus dem Tresor zu speichern, bevor sie in verwaltete Datenträger konvertiert werden |   Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Storage/storageAccounts/write |
| | Mitwirkender | Die Ressourcengruppe, in der der/die verwaltete(n) Datenträger wiederhergestellt wird | Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Resources/subscriptions/resourceGroups/write|
| Wiederherstellen von einzelnen Dateien aus VM-Sicherungen | Sicherungsoperator | Recovery Services-Tresor |
| | Mitwirkender von virtuellen Computern | Gesicherte Quell-VM | Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Compute/virtualMachines/write |
| Regionsübergreifende Wiederherstellung | Sicherungsoperator | Abonnement des Recovery Services-Tresors | Dies geschieht zusätzlich zu den oben erwähnten Wiederherstellungsberechtigungen. Speziell für CRR können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen verwenden: "Microsoft.RecoveryServices/locations/backupAadProperties/read" "Microsoft.RecoveryServices/locations/backupCrrJobs/action"         "Microsoft.RecoveryServices/locations/backupCrrJob/action" "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action"          "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read" "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read" |
| Erstellen einer Sicherungsrichtlinie für Azure-VM-Sicherungen | Mitwirkender für Sicherungen | Recovery Services-Tresor |
| Ändern der Sicherungsrichtlinie der Azure-VM-Sicherungen | Mitwirkender für Sicherungen | Recovery Services-Tresor |
| Löschen der Sicherungsrichtlinie der Azure-VM-Sicherungen | Mitwirkender für Sicherungen | Recovery Services-Tresor |
| Beenden der Sicherung (mit Beibehaltung oder Löschung von Daten) für VM-Sicherungen | Mitwirkender für Sicherungen | Recovery Services-Tresor |
| Registrieren einer lokalen Instanz von Windows Server/Client/SCDPM oder Azure Backup Server | Sicherungsoperator | Recovery Services-Tresor |
| Löschen der registrierten lokalen Instanz von Windows Server/Client/SCDPM oder Azure Backup Server | Mitwirkender für Sicherungen | Recovery Services-Tresor |

> [!IMPORTANT]
> Wenn Sie als Teil der VM-Einstellungen den VM-Mitwirkenden in einem VM-Ressourcenbereich angeben und **Sicherung** auswählen, öffnet sich der Bildschirm **Sicherung aktivieren**, obwohl die VM bereits gesichert ist. Dies liegt daran, dass der Aufruf zur Überprüfung des Sicherungsstatus nur auf Abonnementebene funktioniert. Um dies zu vermeiden, navigieren Sie entweder zum Tresor und öffnen die Sicherungselementansicht der VM, oder geben Sie die Rolle „VM-Mitwirkender“ auf Abonnementebene an.

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Mindestrollenanforderungen für Azure-Workloadsicherungen (SQL- und HANA DB-Sicherungen)

In der folgenden Tabelle sind die Aktionen der Sicherungsverwaltung und die entsprechende minimale Azure-Rolle aufgeführt, die zum Ausführen des jeweiligen Vorgangs erforderlich ist.

| Verwaltungsvorgang | Minimal erforderliche Azure-Rolle | Bereich erforderlich | Alternative |
| --- | --- | --- | --- |
| Erstellen eines Recovery Services-Tresors | Mitwirkender für Sicherungen | Ressourcengruppe mit dem Tresor |   |
| Aktivieren der Sicherung von SQL- und/oder HANA-Datenbanken | Sicherungsoperator | Ressourcengruppe mit dem Tresor |   |
| | Mitwirkender von virtuellen Computern | VM-Ressource, auf der die Datenbank installiert ist |  Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Compute/virtualMachines/write |
| Bedarfsgesteuerte Sicherung einer Datenbank | Sicherungsoperator | Recovery Services-Tresor |   |
| Wiederherstellen einer Datenbank oder Wiederherstellen als Dateien | Sicherungsoperator | Recovery Services-Tresor |   |
| | Mitwirkender von virtuellen Computern | Gesicherte Quell-VM |   Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Compute/virtualMachines/write |
| | Mitwirkender von virtuellen Computern | Ziel-VM, in der die Datenbank wiederhergestellt wird oder die Dateien erstellt werden |   Alternativ können Sie anstelle einer integrierten Rolle eine benutzerdefinierte Rolle mit den folgenden Berechtigungen in Erwägung ziehen: Microsoft.Compute/virtualMachines/write |
| Erstellen einer Sicherungsrichtlinie für Azure-VM-Sicherungen | Mitwirkender für Sicherungen | Recovery Services-Tresor |
| Ändern der Sicherungsrichtlinie der Azure-VM-Sicherungen | Mitwirkender für Sicherungen | Recovery Services-Tresor |
| Löschen der Sicherungsrichtlinie der Azure-VM-Sicherungen | Mitwirkender für Sicherungen | Recovery Services-Tresor |
| Beenden der Sicherung (mit Beibehaltung oder Löschung von Daten) für VM-Sicherungen | Mitwirkender für Sicherungen | Recovery Services-Tresor |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Mindestanforderungen an Rollen für die Sicherung von Azure-Dateifreigaben

In der folgenden Tabelle sind die Aktionen der Sicherungsverwaltung und die entsprechende Rolle aufgeführt, die zum Ausführen des Vorgangs für die Azure-Dateifreigabe erforderlich ist.

| Verwaltungsvorgang | Erforderliche Rolle | Ressourcen |
| --- | --- | --- |
| Aktivieren der Sicherung von Azure-Dateifreigaben | Mitwirkender für Sicherungen |Recovery Services-Tresor |
| | Mitwirkender für Speicherkontosicherung | Speicherkontoressource |
| Bedarfsgesteuerte Sicherung eines virtuellen Computers | Sicherungsoperator | Recovery Services-Tresor |
| Wiederherstellen einer Azure-Dateifreigabe | Sicherungsoperator | Recovery Services-Tresor |
| | Mitwirkender für Speicherkontosicherung | Speicherkontoressourcen, in dem Wiederherstellungs-Quelldateifreigaben und -Zieldateifreigaben vorhanden sind |
| Wiederherstellen einzelner Dateien | Sicherungsoperator | Recovery Services-Tresor |
| |Mitwirkender von Speicherkonto|Speicherkontoressourcen, in dem Wiederherstellungs-Quelldateifreigaben und -Zieldateifreigaben vorhanden sind |
| Schutz beenden |Mitwirkender für Sicherungen | Recovery Services-Tresor |
| Aufheben der Registrierung eines Speicherkontos im Tresor |Mitwirkender für Sicherungen | Recovery Services-Tresor |
| |Mitwirkender von Speicherkonto | Speicherkontoressource|

## <a name="next-steps"></a>Nächste Schritte

* [Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): Erste Schritte mit Azure RBAC im Azure-Portal
* Informationen zur Zugriffsverwaltung mit:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
  * [REST-API](../role-based-access-control/role-assignments-rest.md)
* [Behandeln von Problemen bei Azure RBAC](../role-based-access-control/troubleshooting.md): Empfehlungen zur Behebung häufig auftretender Probleme
