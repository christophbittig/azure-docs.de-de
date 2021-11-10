---
title: Konfigurieren von Azure-SSIS Integration Runtime für Business Continuity & Disaster Recovery (BCDR)
description: In diesem Artikel wird beschrieben, wie Sie Azure-SSIS Integration Runtime in Azure Data Factory mit einer Azure SQL-Datenbank-/Azure SQL Managed Instance-Failovergruppe für BCDR (Business Continuity & Disaster Recovery) konfigurieren.
services: data-factory
ms.service: data-factory
ms.subservice: integration-services
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/22/2021
ms.openlocfilehash: 4ed454ae92fff2a0a5bfd6437b0ee1ef10d194c0
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846233"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Konfigurieren von Azure-SSIS Integration Runtime für Business Continuity & Disaster Recovery (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure SQL-Datenbank/Azure SQL Managed Instance und SQL Server Integration Services (SSIS) in Azure Data Factory (ADF) stellen in Kombination die empfohlene PaaS-Lösung (Platform-as-a-Service) für die SQL Server-Migration dar. Sie können Ihre SSIS-Projekte in der von Azure SQL-Datenbank/Azure SQL Managed Instance gehostete SSIS-Katalogdatenbank (SSISDB) bereitstellen und Ihre SSIS-Pakete in Azure-SSIS Integration Runtime in ADF ausführen.

Für BCDR (Business Continuity & Disaster Recovery) können Azure SQL-Datenbank/Azure SQL Managed Instance mit einer [Georeplikations-/Failovergruppe](../azure-sql/database/auto-failover-group-overview.md) konfiguriert werden. Dabei wird SSISDB in einer primären Azure-Region mit Lese-/Schreibzugriff (primäre Rolle) fortlaufend in eine sekundäre Region mit schreibgeschütztem Zugriff (sekundäre Rolle) repliziert. Wenn ein Notfall in der primären Region auftritt, wird ein Failover ausgelöst, bei dem die primären und sekundären SSISDB-Instanzen ihre Rollen tauschen.

Für BCDR können Sie auch ein Azure-SSIS IR-Paar (Dual Standby) konfigurieren, das synchron mit der Azure SQL-Datenbank-/Azure SQL Managed Instance-Failovergruppe arbeitet. Dies ermöglicht Ihnen die Ausführung eines Azure-SSIS IR-Paars, von denen immer nur eine Instanz über Zugriff auf die primäre SSISDB-Instanz verfügt, um Pakete abzurufen und auszuführen sowie Paketausführungsprotokolle zu schreiben (primäre Rolle), während die andere Instanz dieselben Aktionen nur für an einem anderen Ort bereitgestellte Pakete durchführen kann, z. B. in Azure Files (sekundäre Rolle). Wenn ein SSISDB-Failover auftritt, tauschen die primären und sekundären Azure-SSIS IR-Instanzen ebenfalls ihre Rollen, und wenn beide ausgeführt werden, kommt es zu nahezu keiner Downtime.

In diesem Artikel wird beschrieben, wie Sie Azure-SSIS IR mit einer Azure SQL-Datenbank-/Azure SQL Managed Instance-Failovergruppe für BCDR konfigurieren.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Konfigurieren eines Azure-SSIS IR-Paars (Dual Standby) mit einer Azure SQL-Datenbank-Failovergruppe

Führen Sie die folgenden Schritte durch, um ein Azure-SSIS IR-Paar (Dual Standby) zu konfigurieren, das synchron mit der Azure SQL-Datenbank-Failovergruppe arbeitet.

1. Mithilfe des Azure-Portals bzw. der ADF-Benutzeroberfläche können Sie eine neue Azure-SSIS IR-Instanz mit Ihrem primären Azure SQL-Datenbank-Server erstellen, um SSISDB in der primären Region zu hosten. Wenn Sie bereits über eine Azure-SSIS IR-Instanz verfügen, die bereits auf Ihrem primären Azure SQL-Datenbank-Datenbank-Server an SSISDB angefügt ist und weiterhin ausgeführt wird, müssen Sie diese zunächst beenden, um sie neu zu konfigurieren. Dabei handelt es sich um Ihre primäre Azure-SSIS IR-Instanz.

   Wenn Sie auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration Runtime-Setup** die [Verwendung von SSISDB aktivieren](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb), müssen Sie auch das Kontrollkästchen **Azure-SSIS Integration Runtime-Paar (Dual Standby) mit SSISDB-Failover verwenden** aktivieren. Geben Sie unter **Name des Dual Standby-Paars** einen Namen ein, an dem Sie Ihr Paar primärer und sekundärer Azure-SSIS IR-Instanzen identifizieren können. Wenn Sie mit der Erstellung Ihrer primären Azure-SSIS IR-Instanz fertig sind, wird diese gestartet und an eine primäre SSISDB-Instanz angefügt, die für Sie mit Lese- und Schreibzugriff erstellt wird. Wenn Sie die Instanz gerade neu konfiguriert haben, müssen Sie sie neu starten.

1. Im Azure-Portal können Sie auf der Seite **Übersicht** Ihres primären Azure SQL-Datenbank-Servers überprüfen, ob die primäre SSISDB-Instanz erstellt wurde. Nach der Erstellung können Sie auf der Seite **Failovergruppen** eine [Failovergruppe für Ihre primären und sekundären Azure SQL-Datenbank-Server erstellen und SSISDB zu diesen hinzufügen](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#2---create-the-failover-group). Sobald die Failovergruppe erstellt wurde, können Sie auf der Seite **Übersicht** Ihres sekundären Azure SQL-Datenbank-Servers überprüfen, ob die primäre SSISDB-Instanz in eine sekundäre Instanz mit schreibgeschütztem Zugriff repliziert wurde.

1. Mithilfe des Azure-Portals bzw. der ADF-Benutzeroberfläche können Sie eine weitere Azure-SSIS IR-Instanz mit Ihrem sekundären Azure SQL-Datenbank-Server erstellen, um SSISDB in der sekundären Region zu hosten. Dabei handelt es sich um Ihre sekundäre Azure-SSIS IR-Instanz. Stellen Sie für eine vollständige BCDR-Lösung sicher, dass alle Ressourcen ebenfalls in der sekundären Region erstellt werden, von der eine Abhängigkeit besteht, z. B.Azure Storage zum Speichern benutzerdefinierter Setupskripts/-dateien, ADF für die Orchestrierung/Planung von Paketausführungen und mehr.

   Wenn Sie auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration Runtime-Setup** die [Verwendung von SSISDB aktivieren](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb), müssen Sie auch das Kontrollkästchen **Azure-SSIS Integration Runtime-Paar (Dual Standby) mit SSISDB-Failover verwenden** aktivieren. Geben Sie unter **Name des Dual Standby-Paars** denselben Namen ein, den Sie Ihrem Paar primärer und sekundärer Azure-SSIS IR-Instanzen gegeben haben. Wenn Sie die Erstellung Ihrer sekundären Azure-SSIS IR-Instanz abschließen, wird diese gestartet und an die sekundäre SSISDB-Instanz angefügt.

1. Wenn Sie nahezu keine Downtime beim SSISDB-Failover erzielen möchten, sollten Sie die Ausführung beider Azure-SSIS IR-Instanzen beibehalten. Nur Ihre primäre Azure-SSIS IR-Instanz kann auf die primäre SSISDB-Instanz zugreifen, um Pakete abzurufen und auszuführen oder Paketausführungsprotokolle zu schreiben, während Ihre sekundäre Azure-SSIS IR-Instanz dieselben Aktionen nur für dieselben Pakete ausführen kann, die an einem anderen Ort bereitgestellt wurden, z. B. in Azure Files.

   Wenn Sie Ihre Ausführungskosten reduzieren möchten, können Sie Ihre sekundäre Azure-SSIS IR-Instanz nach der Erstellung beenden. Wenn ein SSISDB-Failover auftritt, tauschen Ihre primären und sekundären Azure-SSIS IR-Instanzen ihre Rollen. Wenn Ihre primäre Azure-SSIS IR-Instanz beendet wird, müssen Sie sie neu starten. Je nachdem, ob sie in ein virtuelles Netzwerk eingefügt wird und welche Injektionsmethode verwendet wird, kann die Ausführung 5 Minuten oder 20 bis 30 Minuten dauern.

1. Wenn Sie [ADF für die Orchestrierung/Planung von Paketausführungen verwenden](./how-to-invoke-ssis-package-ssis-activity.md), müssen Sie sicherstellen, dass alle relevanten ADF-Pipelines mit Aktivitäten zum Ausführen von SSIS-Paketen und zugehörige Trigger in Ihre sekundäre ADF-Instanz kopiert werden. Die Trigger sollten in der sekundären Instanz zunächst deaktiviert sein. Wenn ein SSISDB-Failover auftritt, müssen Sie die Trigger aktivieren.

1. Sie können [Ihre Azure SQL-Datenbank-Failovergruppe testen](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#3---test-failover) und auf der [Azure-SSIS IR-Überwachungsseite im ADF-Portal überprüfen](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal), ob Ihre primäre und sekundäre Azure-SSIS IR-Instanzen die Rollen getauscht haben. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Konfigurieren eines Azure-SSIS IR-Paars (Dual Standby) mit einer Azure SQL Managed Instance-Failovergruppe

Führen Sie die folgenden Schritte durch, um ein Azure-SSIS IR-Paar (Dual Standby) zu konfigurieren, das synchron mit der Azure SQL Managed Instance-Failovergruppe arbeitet.

1. Im Azure-Portal können Sie auf der Seite **Failovergruppen** Ihrer primären Instanz von Azure SQL Managed Instance [eine Failovergruppe für Ihre primäre und sekundäre Instanz von Azure SQL Managed Instance erstellen](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal).

1. Mithilfe des Azure-Portals bzw. der ADF-Benutzeroberfläche können Sie eine neue Azure-SSIS IR-Instanz mit Ihrem primären Azure SQL Managed Instance-Server erstellen, um SSISDB in der primären Region zu hosten. Wenn Sie bereits über eine Azure-SSIS IR-Instanz verfügen, die bereits auf Ihrem primären Azure SQL Azure SQL Managed Instance-Datenbank-Server an SSISDB angefügt ist und weiterhin ausgeführt wird, müssen Sie diese zunächst beenden, um sie neu zu konfigurieren. Dabei handelt es sich um Ihre primäre Azure-SSIS IR-Instanz.

   Wenn Sie auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration Runtime-Setup** die [Verwendung von SSISDB aktivieren](./create-azure-ssis-integration-runtime-portal.md#creating-ssisdb), müssen Sie auch das Kontrollkästchen **Azure-SSIS Integration Runtime-Paar (Dual Standby) mit SSISDB-Failover verwenden** aktivieren. Geben Sie unter **Name des Dual Standby-Paars** einen Namen ein, an dem Sie Ihr Paar primärer und sekundärer Azure-SSIS IR-Instanzen identifizieren können. Wenn Sie mit der Erstellung Ihrer primären Azure-SSIS IR-Instanz fertig sind, wird diese gestartet und an eine primäre SSISDB-Instanz angefügt, die für Sie mit Lese- und Schreibzugriff erstellt wird. Wenn Sie die Instanz gerade neu konfiguriert haben, müssen Sie sie neu starten. Außerdem können Sie auf der Seite **Übersicht** Ihrer sekundären Azure SQL Managed Instance-Instanz überprüfen, ob die primäre SSISDB-Instanz in einer sekundären Instanz mit schreibgeschütztem Zugriff repliziert wurde.

1. Mithilfe des Azure-Portals bzw. der ADF-Benutzeroberfläche können Sie eine weitere Azure-SSIS IR-Instanz mit Ihrem sekundären Azure SQL Managed Instance-Server erstellen, um SSISDB in der sekundären Region zu hosten. Dabei handelt es sich um Ihre sekundäre Azure-SSIS IR-Instanz. Stellen Sie für eine vollständige BCDR-Lösung sicher, dass alle Ressourcen ebenfalls in der sekundären Region erstellt werden, von der eine Abhängigkeit besteht, z. B.Azure Storage zum Speichern benutzerdefinierter Setupskripts/-dateien, ADF für die Orchestrierung/Planung von Paketausführungen und mehr.

   Wenn Sie auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration Runtime-Setup** die [Verwendung von SSISDB aktivieren](./create-azure-ssis-integration-runtime-portal.md#creating-ssisdb), müssen Sie auch das Kontrollkästchen **Azure-SSIS Integration Runtime-Paar (Dual Standby) mit SSISDB-Failover verwenden** aktivieren. Geben Sie unter **Name des Dual Standby-Paars** denselben Namen ein, den Sie Ihrem Paar primärer und sekundärer Azure-SSIS IR-Instanzen gegeben haben. Wenn Sie die Erstellung Ihrer sekundären Azure-SSIS IR-Instanz abschließen, wird diese gestartet und an die sekundäre SSISDB-Instanz angefügt.

1. Azure SQL Managed Instance kann vertrauliche Daten in Datenbanken wie SSISDB schützen, indem diese mithilfe eines Datenbank-Hauptschlüssels (DMK, Database Master Key) verschlüsselt werden. Der DMK selbst wird standardmäßig mithilfe eines Diensthauptschlüssels (SMK, Service Master Key) verschlüsselt. Seit September 2021 wird SMK bei der Erstellung der Failovergruppe von Ihrer primären Azure SQL Managed Instance-Instanz zur sekundären Instanz repliziert. Wenn Ihre Failovergruppe zuvor erstellt wurde, löschen Sie alle Benutzerdatenbanken, einschließlich SSISDB, aus Ihrer sekundären Azure SQL Managed Instance-Instanz und erstellen Sie ihre Failovergruppe neu.

1. Wenn Sie nahezu keine Downtime beim SSISDB-Failover erzielen möchten, sollten Sie die Ausführung beider Azure-SSIS IR-Instanzen beibehalten. Nur Ihre primäre Azure-SSIS IR-Instanz kann auf die primäre SSISDB-Instanz zugreifen, um Pakete abzurufen und auszuführen oder Paketausführungsprotokolle zu schreiben, während Ihre sekundäre Azure-SSIS IR-Instanz dieselben Aktionen nur für dieselben Pakete ausführen kann, die an einem anderen Ort bereitgestellt wurden, z. B. in Azure Files.

   Wenn Sie Ihre Ausführungskosten reduzieren möchten, können Sie Ihre sekundäre Azure-SSIS IR-Instanz nach der Erstellung beenden. Wenn ein SSISDB-Failover auftritt, tauschen Ihre primären und sekundären Azure-SSIS IR-Instanzen ihre Rollen. Wenn Ihre primäre Azure-SSIS IR-Instanz beendet wird, müssen Sie sie neu starten. Je nachdem, ob sie in ein virtuelles Netzwerk eingefügt wird und welche Injektionsmethode verwendet wird, kann die Ausführung 5 Minuten oder 20 bis 30 Minuten dauern.

1. Wenn Sie den [Azure SQL Managed Instance-Agent für die Orchestrierung/Planung von Paketausführungen verwenden](./how-to-invoke-ssis-package-managed-instance-agent.md), müssen Sie sicherstellen, dass alle relevanten SSIS-Aufträge mit ihren Auftragsschritten und zugehörigen Zeitplänen in Ihre sekundäre Azure SQL Managed Instance-Instanz kopiert werden. In der sekundären Instanz müssen die Zeitpläne zunächst deaktiviert sein. Führen Sie die folgenden Schritte mit SSMS aus.

   1. Rechtsklicken Sie auf jeden SSIS-Auftrag, und wählen Sie dann die Menüelemente **Script Job as** (Skript für Auftrag als), **CREATE in** und **Neues Abfrage-Editor-Fenster** aus, um das Skript zu generieren.

      :::image type="content" source="media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png" alt-text="Generieren des SSIS-Auftragsskripts":::

   1. Suchen Sie für jedes generierte SSIS-Auftragsskript nach dem Befehl zum Ausführen der gespeicherten Prozedur `sp_add_job`, und ändern/entfernen Sie bei Bedarf die Wertzuweisung des Arguments `@owner_login_name`.

   1. Führen Sie jedes geänderte SSIS-Auftragsskript in Ihrer sekundären Instanz von Azure SQL Managed Instance aus, um den Auftrag mit dessen Auftragsschritten und zugehörigen Zeitplänen zu kopieren.

   1. Erstellen Sie mithilfe des folgenden Skripts einen neuen T-SQL-Auftrag in Ihren primären und sekundären Instanzen von Azure SQL Managed Instance, um die SSIS-Auftragszeitpläne basierend auf den primären/sekundären SSISDB-Rollen jeweils zu aktivieren oder zu deaktivieren, und führen Sie dieses Skript regelmäßig aus. Wenn ein SSISDB-Failover auftritt, werden zuvor deaktivierte SSISDB-Auftragszeitpläne aktiviert, während aktivierte Zeitpläne deaktiviert werden.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. Wenn Sie [ADF für die Orchestrierung/Planung von Paketausführungen verwenden](./how-to-invoke-ssis-package-ssis-activity.md), müssen Sie sicherstellen, dass alle relevanten ADF-Pipelines mit Aktivitäten zum Ausführen von SSIS-Paketen und zugehörige Trigger in Ihre sekundäre ADF-Instanz kopiert werden. Die Trigger sollten in der sekundären Instanz zunächst deaktiviert sein. Wenn ein SSISDB-Failover auftritt, müssen Sie die Trigger aktivieren.

1. Sie können [Ihre Azure SQL Managed Instance-Failovergruppe testen](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal#test-failover) und auf der [Azure-SSIS IR-Überwachungsseite im ADF-Portal überprüfen](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal), ob Ihre primären und sekundären Azure-SSIS IR-Instanzen die Rollen getauscht haben. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Anfügen einer neuen Azure-SSIS IR-Instanz an eine vorhandene von Azure SQL-Datenbank oder Azure SQL Managed Instance gehostete SSISDB-Instanz

Wenn ein Notfall eintritt, der sich auf Ihre vorhandene Azure-SSIS IR-Instanz, aber nicht auf Ihre Instanz von Azure SQL-Datenbank/Azure SQL Managed Instance in derselben Region auswirkt, können Sie die Instanz durch eine neue, sich in einer Region befindende Instanz ersetzen. Führen Sie die folgenden Schritte aus, um Ihre vorhandene von Azure SQL-Datenbank/Azure SQL Managed Instance gehostete SSISDB-Instanz an eine neue Azure-SSIS IR-Instanz anzufügen.

1. Wenn Ihre vorhandene Azure-SSIS IR-Instanz weiterhin ausgeführt wird, müssen Sie diese zunächst über das Azure-Portal, die ADF-Benutzeroberfläche oder über Azure PowerShell beenden. Sie können diesen Schritt überspringen, wenn sich der Notfall auf auch ADF in derselben Region auswirkt.

1. Führen Sie den folgenden Befehl mit SSMS für SSISDB in Ihrer Instanz von Azure SQL-Datenbank/Azure SQL Managed Instance aus, um die Metadaten zu aktualisieren, die Verbindungen mit Ihrer neuen ADF-/Azure-SSIS IR-Instanz ermöglicht.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. Verwenden Sie das [Azure-Portal, die ADF-Benutzeroberfläche](./create-azure-ssis-integration-runtime-portal.md) oder [Azure PowerShell](./create-azure-ssis-integration-runtime-powershell.md), um Ihre neue ADF-/Azure-SSIS IR-Instanz jeweils mit den Namen *YourNewADF*/*YourNewAzureSSISIR* in einer anderen Region zu erstellen. Wenn Sie das Azure-Portal oder die ADF-Benutzeroberfläche verwenden, können Sie den Testverbindungsfehler auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration Runtime-Setup** ignorieren.

## <a name="next-steps"></a>Nächste Schritte

Sie können die folgenden anderen Konfigurationsoptionen für Ihre Azure-SSIS IR-Instanz in Betracht ziehen:

- [Konfigurieren von Paketspeichern für Ihre Azure-SSIS IR-Instanz](./create-azure-ssis-integration-runtime-portal.md#creating-azure-ssis-ir-package-stores)

- [Konfigurieren benutzerdefinierter Setups für Ihre Azure-SSIS IR-Instanz](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Konfigurieren der VNET-Injektion für Ihre Azure-SSIS IR-Instanz](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Konfigurieren einer selbstgehosteten Integration Runtime als Proxy für Ihre Azure-SSIS IR-Instanz](./self-hosted-integration-runtime-proxy-ssis.md)
