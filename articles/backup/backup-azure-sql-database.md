---
title: Sichern von SQL Server-Datenbanken in Azure
description: In diesem Artikel erfahren Sie, wie Sie SQL Server in Azure sichern. In diesem Artikel wird auch die SQL Server-Wiederherstellung beschrieben.
ms.topic: conceptual
ms.date: 08/20/2021
ms.openlocfilehash: aae8493579c96607c5623bb04495ec21c99cf667
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101438"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informationen zur SQL Server-Sicherung auf virtuellen Azure-Computern

[Azure Backup](backup-overview.md) bietet eine streambasierte, spezialisierte Lösung zum Sichern von SQL Server-Instanzen, die auf Azure-VMs ausgeführt werden. Diese Lösung ist an den Vorteilen von Azure Backup ausgerichtet: Sicherungen ohne Infrastruktur, langfristige Speicherung und zentrale Verwaltung. Außerdem bietet sie die folgenden Vorteile speziell für SQL Server:

1. Workloadabhängige Sicherungen, die alle Sicherungstypen unterstützen: vollständige, differenzielle und Protokollsicherungen
2. RPO (Recovery Point Objective) von 15 Minuten mit häufigen Protokollsicherungen
3. Zeitpunktwiederherstellung von bis zu einer Sekunde
4. Sicherung und Wiederherstellung einzelner Datenbankebenen

>[!Note]
>Die momentaufnahmebasierte Sicherung für SQL-Datenbanken in Azure-VMs befindet sich jetzt in der Vorschauphase. Dieses einzigartige Angebot nutzt die Möglichkeiten von Momentaufnahmen, die zu einer besseren RTO und verringerten Auswirkungen auf den Server beitragen, sowie die Vorteile häufiger Protokollsicherungen, um eine niedrige RPO zu erzielen. Wenn Sie Fragen haben oder Zugriff benötigen, schreiben Sie uns unter [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).

Informationen zu den zurzeit unterstützten Sicherungs- und Wiederherstellungsszenarien finden Sie in der [Unterstützungsmatrix](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Sicherungsprozess

Die Lösung nutzt die nativen SQL-APIs, um Sicherungen Ihrer SQL-Datenbankinstanzen zu erstellen.

* Nachdem Sie die SQL Server-VM angegeben haben, die Sie schützen und deren Datenbanken Sie abfragen möchten, installiert der Azure Backup-Dienst eine Erweiterung zur Workloadsicherung mit dem Namen `AzureBackupWindowsWorkload` auf dem virtuellen Computer.
* Diese Erweiterung besteht aus einem Koordinator und einem SQL-Plug-In. Während der Koordinator für das Auslösen von Workflows für verschiedene Vorgänge wie Konfigurieren der Sicherung, Sicherung und Wiederherstellung zuständig ist, ist das Plug-In für den tatsächlichen Datenfluss verantwortlich.
* Um die Datenbanken auf diesem virtuellen Computer ermitteln zu können, erstellt Azure Backup das Konto `NT SERVICE\AzureWLBackupPluginSvc`. Dieses Konto wird zum Sichern und Wiederherstellen verwendet und erfordert SQL-Systemadministratorberechtigungen. Weil es sich beim Konto `NT SERVICE\AzureWLBackupPluginSvc` um ein [virtuelles Dienstkonto](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts) handelt, ist keine Kennwortverwaltung erforderlich. Azure Backup verwendet das Konto `NT AUTHORITY\SYSTEM` für die Ermittlung von und Anfragen an Datenbanken. Für dieses Konto muss es also eine öffentliche Anmeldung in SQL geben. Wenn Sie den virtuellen SQL Server-Computer nicht über Azure Marketplace erstellt haben, erhalten Sie möglicherweise den Fehler **UserErrorSQLNoSysadminMembership**. Gehen Sie in diesem Fall [wie folgt vor](#set-vm-permissions):
* Sobald Sie die Konfiguration des Schutzes der ausgewählten Datenbanken auslösen, richtet der Sicherungsdienst den Koordinator mit den Sicherungszeitplänen und anderen Richtliniendetails ein, die die Erweiterung lokal auf dem virtuellen Computer zwischenspeichert.
* Zum geplanten Zeitpunkt kommuniziert der Koordinator mit dem Plug-In, und es startet das Streaming der Sicherungsdaten von der SQL Server-Instanz mit VDI.  
* Weil das Plug-In die Daten direkt an den Recovery Services-Tresor sendet, ist kein Stagingspeicherort erforderlich. Die Daten werden verschlüsselt und vom Azure Backup-Dienst in Speicherkonten gespeichert.
* Wenn die Datenübertragung abgeschlossen ist, bestätigt der Koordinator den Commit mit dem Sicherungsdienst.

  ![Die Architektur von SQL Backup](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Vorbereitung

Überprüfen Sie zunächst die folgenden Anforderungen:

1. Vergewissern Sie sich, dass in Azure eine SQL Server-Instanz ausgeführt wird. Im Marketplace können Sie [schnell eine SQL Server-Instanz erstellen](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md).
2. Informieren Sie sich über [Funktionsaspekte](sql-support-matrix.md#feature-considerations-and-limitations) und [Unterstützung von Szenarien](sql-support-matrix.md#scenario-support).
3. [Lesen Sie häufig gestellte Fragen](faq-backup-sql-server.yml) zu diesem Szenario.

## <a name="set-vm-permissions"></a>Einrichten von Berechtigungen für virtuelle Computer

  Wenn Sie die Ermittlung in einer SQL Server-Instanz ausführen, führt Azure Backup Folgendes aus:

* Fügt die Erweiterung AzureBackupWindowsWorkload hinzu.
* Erstellt das Konto „NT SERVICE\AzureWLBackupPluginSvc“, um Datenbanken auf dem virtuellen Computer zu ermitteln. Dieses Konto wird zum Sichern und Wiederherstellen verwendet und erfordert Systemadministratorberechtigungen für SQL Server.
* Ermittelt auf einer VM ausgeführte Datenbanken. Azure Backup verwendet das Konto NT AUTHORITY\SYSTEM. Dieses Konto muss eine öffentliche Anmeldung in SQL Server ermöglichen.

Wenn Sie die SQL Server-VM nicht in Azure Marketplace erstellt haben oder wenn Sie mit SQL Server 2008 oder 2008 R2 arbeiten, erhalten Sie möglicherweise die Fehlermeldung **UserErrorSQLNoSysadminMembership**.

Informationen zur Erteilung von Berechtigungen bei Ausführung von **SQL 2008** und **2008 R2** unter Windows 2008 R2 finden Sie [hier](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Korrigieren Sie bei allen anderen Versionen die Berechtigungen wie folgt:

  1. Verwenden Sie ein Konto mit SQL Server-Systemadministratorberechtigungen, um sich bei SQL Server Management Studio (SSMS) anzumelden. Wenn Sie keine speziellen Berechtigungen benötigen, sollte die Windows-Authentifizierung funktionieren.
  2. Öffnen Sie auf der SQL Server-Instanz den Ordner **Sicherheit/Anmeldungen**.

      ![Öffnen von Ordner „Sicherheit/Anmeldungen“, um Konten anzuzeigen](./media/backup-azure-sql-database/security-login-list.png)

  3. Klicken Sie mit der rechten Maustaste auf den Ordner **Anmeldungen**, und wählen Sie **Neue Anmeldung** aus. Wählen Sie in **Anmeldung – Neu** die Option **Suche** aus.

      ![Auswählen von „Suchen“ im Dialogfeld „Anmeldung – Neu“](./media/backup-azure-sql-database/new-login-search.png)

  4. Das virtuelle Windows-Dienstkonto **NT SERVICE\AzureWLBackupPluginSvc** wurde bei der Registrierung des virtuellen Computers und der SQL-Ermittlungsphase erstellt. Geben Sie den Kontonamen ein, wie in **Namen des auszuwählenden Objekts eingeben** dargestellt. Wählen Sie **Namen überprüfen** aus, um den Namen aufzulösen. Klicken Sie auf **OK**.

      ![Klicken auf „Namen überprüfen“, um den unbekannten Dienstnamen aufzulösen](./media/backup-azure-sql-database/check-name.png)

  5. Stellen Sie in **Serverrollen** sicher, dass die Rolle **sysadmin** ausgewählt ist. Klicken Sie auf **OK**. Die erforderlichen Berechtigungen sollten jetzt vorhanden sein.

      ![Sicherstellen, dass die Serverrolle „Sysadmin“ ausgewählt ist](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Ordnen Sie nun die Datenbank dem Recovery Services-Tresor zu. Klicken Sie im Azure-Portal in der Liste **Geschützte Server** mit der rechten Maustaste auf den fehlerhaften Server, und wählen Sie **Datenbanken neu ermitteln** aus.

      ![Überprüfen der entsprechenden Serverberechtigungen](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Der Fortschritt kann im Bereich **Benachrichtigungen** verfolgt werden. Wenn die ausgewählten Datenbanken gefunden wurden, wird eine Erfolgsmeldung angezeigt.

      ![Meldung über erfolgreiche Bereitstellung](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Sind auf Ihrem SQL-Server mehrere Instanzen von SQL Server installiert, müssen Sie allen SQL-Instanzen Systemadministratorberechtigungen für das Konto **NT Service\AzureWLBackupPluginSvc** hinzufügen.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Erteilen der SQL Server-Berechtigung „sysadmin“ für SQL 2008 und SQL 2008 R2

Fügen Sie der SQL Server-Instanz die Anmeldungen **NT AUTHORITY\SYSTEM** und **NT Service\AzureWLBackupPluginSvc** hinzu:

1. Wechseln Sie im Objekt-Explorer zur SQL Server-Instanz.
2. Navigieren Sie zu „Sicherheit -> Anmeldungen“.
3. Klicken Sie mit der rechten Maustaste auf „Anmeldungen“, und wählen Sie *Neue Anmeldung* aus.

    ![Neue Anmeldung mit SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Wechseln Sie zur Registerkarte „Allgemein“, und geben Sie **NT AUTHORITY\SYSTEM** als Anmelde-ID ein.

    ![Anmelde-ID für SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Wechseln Sie zu *Serverrollen*, und wählen Sie die Rollen *Öffentlich* und *sysadmin* aus.

    ![Auswählen von Rollen in SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Wechseln Sie zu *Status*. *Erteilen* Sie die Berechtigung zum Herstellen der Verbindung mit der Datenbank-Engine, und melden Sie sich als *Aktiviert* an.

    ![Erteilen von Berechtigungen in SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Wählen Sie „OK“ aus.
8. Wiederholen Sie die gleiche Schrittreihenfolge (1-7 oben), um die Anmeldung „NT Service\AzureWLBackupPluginSvc“ zur SQL Server-Instanz hinzuzufügen. Wenn die Anmeldung bereits vorhanden ist, stellen Sie sicher, dass sie die Serverrolle „sysadmin“ hat und ihr unter „Status“ die Berechtigung zum Herstellen der Verbindung mit der Datenbank-Engine erteilt und „Anmeldung“ auf „Aktiviert“ festgelegt wurde.
9. Führen Sie nach dem Erteilen der Berechtigung im Portal eine **erneute Ermittlung von Datenbanken** durch: Tresor **->** Sicherungsinfrastruktur **->** Workload in Azure VM:

    ![Erneutes Ermitteln von Datenbanken im Azure-Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Alternativ können Sie die Erteilung der Berechtigungen automatisieren, indem Sie die folgenden PowerShell-Befehle im Administratormodus ausführen. Der Instanzname ist standardmäßig auf MSSQLSERVER festgelegt. Ändern Sie nötigenfalls das Argument mit dem Namen der Instanz im Skript:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Informieren Sie sich über](backup-sql-server-database-azure-vms.md) das Sichern von SQL Server-Datenbanken.
* [Informieren Sie sich über](restore-sql-database-azure-vm.md) das Wiederherstellen von gesicherten SQL Server-Datenbanken.
* [Informieren Sie sich über](manage-monitor-sql-database-backup.md) das Verwalten von gesicherten SQL Server-Datenbanken.
