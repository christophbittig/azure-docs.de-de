---
title: 'Tutorial: Onlinemigration von SQL Server zu Azure SQL Managed Instance mithilfe von Azure Data Studio'
titleSuffix: Azure Database Migration Service(DMS)
description: Onlinemigration von SQL Server zu Azure SQL Managed Instance mithilfe von Azure Data Studio mit Azure Database Migration Service
services: dms
author: mokabiru
ms.author: mokabiru
manager: ''
ms.reviewer: cawrites
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 10/05/2021
ms.openlocfilehash: 96c552cd078bc18b11d7991482c280d92b4589d2
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544652"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-azure-data-studio-with-dms-preview"></a>Tutorial: Onlinemigration von SQL Server zu Azure SQL Managed Instance mithilfe von Azure Data Studio mit DMS (Vorschau)

Migrieren Sie mit der Azure SQL-Migrationserweiterung in Azure Data Studio Datenbanken aus einer SQL Server-Instanz mit minimaler Downtime zu [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Informationen zu Methoden, die einen gewissen manuellen Aufwand erfordern, finden Sie im Artikel [Migration einer SQL Server-Instanz zu Azure SQL Managed Instance](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).

In diesem Tutorial migrieren Sie die Datenbank **Adventureworks** mithilfe von Azure Data Studio mit dem Azure Database Migration Service (DMS) mit minimaler Downtime von einer lokalen SQL Server-Instanz zu Azure SQL Managed Instance.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Starten des Assistenten zum *Migrieren zu Azure SQL* in Azure Data Studio
> * Ausführen einer Bewertung Ihrer SQL Server-Quelldatenbank(en)
> * Angeben von Details Ihrer SQL Server-Quellinstanz, des Sicherungsspeicherorts und der Azure SQL Managed Instance-Zielinstanz
> * Erstellen einer neuen Azure Database Migration Service-Instanz und Installieren der selbstgehosteten Integration Runtime, um auf den Quellserver und die Sicherungen zuzugreifen
> * Starten und Überwachen des Fortschritts der Migration
> * Führen Sie die Migrationsübernahme durch, wenn Sie bereit sind.

> [!IMPORTANT]
> Bereiten Sie die Migration vor, und verkürzen Sie die Dauer des Onlinemigrationsvorgangs so weit wie möglich, um die Gefahr von Unterbrechungen zu minimieren, die durch die Neukonfiguration von Instanzen oder geplante Wartung bedingt sind. Im Fall eines solchen Ereignisses muss der Migrationsprozess von Anfang an neu beginnen. Bei einer geplanten Wartung gilt eine Toleranzperiode von 36 Stunden, in der die Konfiguration oder Wartung der Azure SQL Managed Instance-Zielinstanz vor dem Neustart des Migrationsprozesses durchgeführt wird.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In diesem Artikel wird eine Online-Datenbankmigration von SQL Server zu Azure SQL Managed Instance beschrieben. Informationen zur Offline-Datenbankmigration finden Sie unter [Tutorial: Migrieren von SQL Server zu einer SQL Managed Instance-Instanz mit Azure Data Studio mithilfe von DMS offline (Vorschau)](tutorial-sql-server-managed-instance-offline-ads.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* [Herunterladen und Installieren von Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Installieren der Azure SQL-Migrationserweiterung](/sql/azure-data-studio/extensions/azure-sql-migration-extension) aus Azure Data Studio Marketplace
* Verwenden Sie ein Azure-Konto, das einer der unten aufgeführten integrierten Rollen zugewiesen ist:
    - Mitwirkender für die Zielinstanz von Azure SQL Managed Instance (und Storage-Konto zum Hochladen Ihrer Datenbanksicherungsdateien von der SMB-Netzwerkfreigabe).
    - Rolle „Besitzer“ oder „Mitwirkender“ für die Azure-Ressourcengruppen, die die Zielinstanz von Azure SQL Managed Instance oder das Azure-Speicherkonto enthalten.
    - Rolle „Besitzer“ oder „Mitwirkender“ für das Azure-Abonnement.
* Erstellen Sie eine [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md)-Zielinstanz.
* Stellen Sie sicher, dass die zum Herstellen einer Verbindung mit der SQL Server-Quellinstanz verwendeten Anmeldungen Mitglieder der *sysadmin*-Serverrolle sind oder über `CONTROL SERVER`-Berechtigung verfügen. 
* Verwenden Sie eine der folgenden Speicheroptionen für die vollständigen Datenbank- und Transaktionsprotokoll-Sicherungsdateien: 
    - SMB-Netzwerkfreigabe 
    - Dateifreigabe oder Blobcontainer des Azure-Speicherkontos 

    > [!IMPORTANT]
    > - Wenn Ihre Datenbanksicherungsdateien in einer SMB-Netzwerkfreigabe bereitgestellt werden, [erstellen Sie ein Azure Storage-Konto](../storage/common/storage-account-create.md), mit dem der DMS-Dienst die Datenbanksicherungsdateien hochladen kann.  Achten Sie darauf, das Azure Storage-Konto in der gleichen Region zu erstellen, in der auch die Azure Database Migration Service-Instanz erstellt wird.
    > - Azure Database Migration Service initiiert keine Sicherungen, sondern verwendet für die Migration vorhandene Sicherungen, die Sie im Rahmen Ihres Notfallwiederherstellungsplans möglicherweise bereits besitzen.
    > - Sie sollten [Sicherungen mit der `WITH CHECKSUM`-Option](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server) erstellen. 
    > - Jede Sicherung kann entweder in eine separate Sicherungsdatei oder in mehrere Sicherungsdateien geschrieben werden. Das Anfügen mehrerer Sicherungen (vollständig und Transaktionsprotokoll) an ein einzelnes Sicherungsmedium wird jedoch nicht unterstützt. 
    > - Verwenden Sie komprimierte Sicherungen, um die Wahrscheinlichkeit von potenziellen Problemen bei der Migration großer Sicherungen zu verringern.
* Stellen Sie sicher, dass das Dienstkonto, das die SQL Server-Quellinstanz ausführt, über Lese- und Schreibberechtigungen für die SMB-Netzwerkfreigabe verfügt, die Datenbanksicherungsdateien enthält.
* Das Quellzertifikat der SQL Server-Instanz einer durch Transparent Data Encryption (TDE) geschützten Datenbank muss vor der Datenmigration zur Zielinstanz von Azure SQL Managed Instance oder zu SQL Server in Azure Virtual Machine migriert werden. Weitere Informationen finden Sie unter [Migrieren eines Zertifikats einer durch TDE geschützten Datenbank zu Azure SQL Managed Instance](../azure-sql/managed-instance/tde-certificate-migrate.md) und [Verschieben einer durch TDE geschützten Datenbank in eine andere SQL Server-Instanz](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
    > [!TIP]
    > Wenn Ihre Datenbank vertrauliche Daten enthält, die durch [Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio) geschützt sind, wird der Migrationsprozess unter Verwendung von Azure Data Studio mit DMS automatisch Ihre Always Encrypted-Schlüssel zu Azure SQL Managed Instance oder SQL Server in Azure Virtual Machine migrieren.

* Wenn sich Ihre Datenbanksicherungen in einer Netzwerkfreigabe befinden, stellen Sie einen Computer zur Installation der [selbstgehosteten Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md) bereit, um auf Datenbanksicherungen zuzugreifen und sie zu migrieren. Der Migrations-Assistent stellt den Downloadlink und die Authentifizierungsschlüssel bereit, um Ihre selbstgehostete Integration Runtime herunterzuladen und zu installieren. Stellen Sie als Vorbereitung für die Migration sicher, dass auf dem Computer, auf dem Sie die selbstgehostete Integration Runtime installieren möchten, die folgenden Firewallregeln für ausgehenden Datenverkehr und Domänennamen aktiviert sind:

    | Domänennamen                                          | Ausgehende Ports | BESCHREIBUNG                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | Öffentliche Cloud: `{datafactory}.{region}.datafactory.azure.net`<br> oder `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> China: `{datafactory}.{region}.datafactory.azure.cn` | 443            | Erforderlich für die selbstgehostete Integration Runtime, um eine Verbindung mit dem Datenmigrationsdienst herzustellen. <br>Suchen Sie für eine neu erstellte Data Factory-Instanz in der öffentlichen Cloud den FQDN im Schlüssel Ihrer selbstgehosteten Integration Runtime im Format `{datafactory}.{region}.datafactory.azure.net`. Für eine alte Data Factory-Instanz verwenden Sie stattdessen *.frontend.clouddatahub.net, wenn Sie den FQDN nicht im Schlüssel für die selbstgehostete Integration Runtime sehen. |
    | `download.microsoft.com`    | 443            | Erforderlich für die selbstgehostete Integration Runtime zum Herunterladen der Aktualisierungen. Wenn Sie die automatische Aktualisierung deaktiviert haben, können Sie die Konfiguration dieser Domäne überspringen. |
    | `*.core.windows.net`          | 443            | Wird von der selbstgehosteten Integration Runtime verwendet, die eine Verbindung mit dem Azure-Speicherkonto herstellt, um Datenbanksicherungen von Ihrer Netzwerkfreigabe hochzuladen. |

    > [!TIP]
    > Wenn Ihre Datenbanksicherungsdateien bereits in einem Azure-Speicherkonto bereitgestellt werden, ist während der Migration keine selbstgehostete Integration Runtime erforderlich.

* Stellen Sie bei Verwendung der selbstgehosteten Integration Runtime sicher, dass der Computer, auf dem die Runtime installiert ist, eine Verbindung mit der SQL Server-Quellinstanz und der Netzwerkdateifreigabe herstellen kann, auf der sich Sicherungsdateien befinden. Der ausgehende Port 445 muss aktiviert sein, um Zugriff auf die Netzwerkdateifreigabe zu ermöglichen. Weitere Informationen finden Sie unter [Empfehlungen für die Verwendung der selbstgehosteten Integration Runtime für Datenbankmigrationen](migration-using-azure-data-studio.md#recommendations-for-using-self-hosted-integration-runtime-for-database-migrations).
* Wenn Sie Azure Database Migration Service zum ersten Mal verwenden, stellen Sie sicher, dass der Ressourcenanbieter Microsoft.DataMigration in Ihrem Abonnement registriert ist. Sie können die Schritte zum [Registrieren des Ressourcenanbieters](quickstart-create-data-migration-service-portal.md#register-the-resource-provider) ausführen.

## <a name="launch-the-migrate-to-azure-sql-wizard-in-azure-data-studio"></a>Starten des Assistenten zum Migrieren zu Azure SQL in Azure Data Studio

1. Öffnen Sie Azure Data Studio, und wählen Sie das Serversymbol aus, um eine Verbindung mit Ihrer lokalen SQL Server-Instanz (oder der SQL Server-Instanz auf Azure Virtual Machines) herzustellen.
1. Klicken Sie auf der Serververbindung mit der rechten Maustaste, und wählen Sie **Verwalten** aus.
1. Wählen Sie auf der Startseite des Servers **Azure SQL-Migrationserweiterung** aus.
1. Wählen Sie im Azure SQL Migration-Dashboard die Option **Migrieren zu Azure SQL** aus, um den Migrations-Assistenten zu starten.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/launch-migrate-to-azure-sql-wizard.png" alt-text="Starten des Assistenten zum Migrieren zu Azure SQL":::
1. Verknüpfen Sie im ersten Schritt des Migrations-Assistenten Ihr vorhandenes oder neues Azure-Konto mit Azure Data Studio.

## <a name="run-database-assessment-and-select-target"></a>Ausführen der Datenbankbewertung und Auswählen des Ziels

1. Wählen Sie die Datenbank(en) zum Ausführen der Bewertung und dann **Weiter** aus.
1. Wählen Sie „Azure SQL Managed Instance“ als Ziel aus.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/assessment-complete-target-selection.png" alt-text="Bestätigung der Bewertung":::
1. Wählen Sie die Schaltfläche **Ansicht/Auswählen** aus, um Details zu den Bewertungsergebnissen für Ihre Datenbank(en) anzuzeigen, wählen Sie die zu migrierende(n) Datenbank(en) aus, und wählen Sie **OK** aus. Wenn Probleme in den Bewertungsergebnissen angezeigt werden, müssen sie behoben werden, bevor Sie mit den nächsten Schritten fortfahren.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/assessment-issues-details.png" alt-text="Details der Datenbankbewertung":::
1. Geben Sie Ihre **Azure SQL Managed Instance-Zielinstanz** an, indem Sie Ihr Abonnement, Ihren Standort und Ihre Ressourcengruppe in den entsprechenden Dropdownlisten auswählen, und wählen Sie **Weiter** aus.

## <a name="configure-migration-settings"></a>Konfigurieren von Migrationseinstellungen

1. Wählen Sie **Onlinemigration** als Migrationsmodus aus.
    > [!NOTE]
    > Im Onlinemigrationsmodus ist die SQL Server-Quelldatenbank für Lese- und Schreibaktivitäten verfügbar, während Datenbanksicherungen auf der Azure SQL Managed Instance-Zielinstanz kontinuierlich wiederhergestellt werden. Die Ausfallzeit der Anwendung ist auf die Dauer der Übernahme am Ende der Migration beschränkt.
1. Wählen Sie den Speicherort Ihrer Datenbanksicherungen aus. Ihre Datenbanksicherungen können sich entweder auf einer lokalen Netzwerkfreigabe oder in einem Azure-Speicherblobcontainer befinden.
    > [!NOTE]
    > Wenn Ihre Datenbanksicherungen in einer lokalen Netzwerkfreigabe bereitgestellt werden, müssen Sie im nächsten Schritt des Assistenten in DMS die selbstgehostete Integration Runtime einrichten. Die selbstgehostete Integration Runtime ist erforderlich, um auf Ihre Quelldatenbanksicherungen zuzugreifen, die Gültigkeit des Sicherungssets zu überprüfen und sie in das Azure-Speicherkonto hochzuladen.<br/> Wenn sich Ihre Datenbanksicherungen bereits in einem Azure-Speicherblobcontainer befinden, müssen Sie keine selbstgehostete Integration Runtime einrichten.
1. Nachdem Sie den Sicherungsspeicherort ausgewählt haben, geben Sie Details zur SQL Server-Quellinstanz und zum Quellsicherungsspeicherort an.

    |Feld    |BESCHREIBUNG  |
    |---------|-------------|
    |**Anmeldeinformationen: Benutzername**    |Die Anmeldeinformationen (Windows/SQL-Authentifizierung) zum Herstellen einer Verbindung mit der SQL Server-Quellinstanz und Überprüfen der Sicherungsdateien.         |
    |**Anmeldeinformationen: Kennwort**    |Die Anmeldeinformationen (Windows/SQL-Authentifizierung) zum Herstellen einer Verbindung mit der SQL Server-Quellinstanz und Überprüfen der Sicherungsdateien.         |
    |**Netzwerkfreigabespeicherort, der Sicherungen enthält**     |Der Speicherort der Netzwerkfreigabe, der die vollständigen und Transaktionsprotokoll-Sicherungsdateien enthält. Alle ungültigen Dateien oder Sicherungsdateien in der Netzwerkfreigabe, die nicht zum gültigen Sicherungssatz gehören, werden während des Migrationsprozesses automatisch ignoriert.        |
    |**Windows-Benutzerkonto mit Lesezugriff auf den Speicherort der Netzwerkfreigabe**     |Die Windows-Anmeldeinformationen (Benutzername), die zum Abrufen der Sicherungsdateien über Lesezugriff auf die Netzwerkfreigabe verfügen.       |
    |**Kennwort**     |Die Windows-Anmeldeinformationen (Kennwort), die zum Abrufen der Sicherungsdateien über Lesezugriff auf die Netzwerkfreigabe verfügen.         |
    |**Name der Zieldatenbank** |Der Name der Zieldatenbank kann geändert werden, wenn Sie den Datenbanknamen auf dem Ziel während des Migrationsprozesses ändern möchten.            |

1. Geben Sie das **Azure-Speicherkonto** an, indem Sie in den entsprechenden Dropdownlisten **Abonnement**, **Speicherort** und **Ressourcengruppe** auswählen. Dieses Azure-Speicherkonto wird von DMS verwendet, um die Datenbanksicherungen von der Netzwerkfreigabe hochzuladen. Sie müssen keinen Container erstellen, da DMS während des Uploadvorgangs automatisch einen Blobcontainer im angegebenen Speicherkonto erstellt.
    > [!IMPORTANT]
    > Wenn die Loopback-Funktionalität aktiviert ist und sich die SQL Server-Quellinstanz und die Dateifreigabe auf demselben Computer befinden, kann die Quelle nicht mithilfe des FQDN auf die Dateifreigabe zugreifen. Um dieses Problem zu beheben, deaktivieren Sie die Loopback-Überprüfungsfunktionalität anhand [dieser Anweisungen](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

## <a name="create-azure-database-migration-service"></a>Erstellen einer Instanz von Azure Database Migration Service

1. Erstellen Sie eine neue Azure Database Migration Service-Instanz, oder verwenden Sie erneut einen vorhandenen Dienst, den Sie zuvor erstellt haben.
    > [!NOTE]
    > Wenn Sie zuvor eine DMS-Instanz über das Azure-Portal erstellt haben, können Sie diese nicht im Migrations-Assistenten in Azure Data Studio verwenden. Nur DMS-Instanzen, die zuvor mit Azure Data Studio erstellt wurden, können wiederverwendet werden.
1. Wählen Sie die **Ressourcengruppe** aus, in der Sie über eine DMS-Instanz verfügen oder eine neue erstellen müssen. In der Dropdownliste **Azure Database Migration Service** werden alle in der ausgewählten Ressourcengruppe vorhandenen DMS-Instanzen aufgeführt.
1. Um eine vorhandene DMS-Instanz wiederzuverwenden, wählen Sie sie aus der Dropdownliste aus, und der Status der selbstgehosteten Integration Runtime wird unten auf der Seite angezeigt.
1. Wählen Sie zum Erstellen einer neuen DMS-Instanz die Option **Neu erstellen** aus. Geben Sie auf dem Bildschirm **Azure Database Migration Service-Instanz erstellen** den Namen für Ihre DMS-Instanz an, und wählen Sie **Erstellen** aus.
1. Nach der erfolgreichen Erstellung der DMS-Instanz erhalten Sie Details zum Einrichten der **Integration Runtime**.
1. Wählen Sie **Integration Runtime herunterladen und installieren** aus, um den Downloadlink in einem Webbrowser zu öffnen. Schließen Sie den Download ab. Installieren Sie die Integration Runtime auf einem Computer, der die Voraussetzungen für das Herstellen einer Verbindung mit der SQL Server-Quellinstanz und dem Speicherort mit der Quellsicherung erfüllt.
1. Nach Abschluss der Installation wird der **Konfigurations-Manager von Microsoft Integration Runtime** automatisch gestartet, um den Registrierungsprozess zu starten.
1. Kopieren Sie einen der auf dem Bildschirm des Assistenten in Azure Data Studio bereitgestellten Authentifizierungsschlüssel, und fügen Sie ihn ein. Wenn der Authentifizierungsschlüssel gültig ist, wird im Konfigurations-Manager von Integration Runtime ein grünes Häkchensymbol angezeigt, das angibt, dass Sie mit dem **Registrieren** fortfahren können.
1. Nachdem Sie die Registrierung der selbstgehosteten Integration Runtime erfolgreich abgeschlossen haben, schließen Sie den **Konfigurations-Manager von Microsoft Integration Runtime**, und wechseln Sie zurück zum Migrations-Assistenten in Azure Data Studio.
1. Wählen Sie im Bildschirm **Azure Database Migration Service-Instanz erstellen** in Azure Data Studio die Option **Verbindung testen** aus, um zu überprüfen, ob die neu erstellte DMS-Instanz mit der neu registrierten selbstgehosteten Integration Runtime verbunden ist.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/test-connection-integration-runtime-complete.png" alt-text="Testen der Verbindung mit der Integration Runtime":::
1. Überprüfen Sie die Migrationszusammenfassung, und wählen Sie **Fertig** aus, um die Datenbankmigration zu starten.

## <a name="monitor-your-migration"></a>Überwachen der Migration

1. Unter **Datenbankmigrationsstatus** können Sie die laufenden Migrationen, abgeschlossenen Migrationen und fehlerhaften Migrationen nachverfolgen, sofern vorhanden.

    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/monitor-migration-dashboard.png" alt-text="Überwachen des Migrationsdashboards":::
1. Wählen Sie **Laufende Datenbankmigrationen** aus, um laufende Migrationen anzuzeigen und weitere Details durch Auswählen des Datenbanknamens zu erhalten.
1. Auf der Seite mit den Migrationsdetails werden die Sicherungsdateien und der entsprechende Status angezeigt:

    | Status | BESCHREIBUNG |
    |--------|-------------|
    | Empfangen | Sicherungsdatei wurde am Speicherort der Quellsicherung empfangen und überprüft |
    | Hochladen | Integration Runtime lädt gerade die Sicherungsdatei in Azure-Speicher hoch|
    | Hochgeladen | Sicherungsdatei ist in Azure-Speicher hochgeladen |
    | Restoring | Azure Database Migration Service stellt gerade die Sicherungsdatei in Azure SQL Managed Instance wieder her|
    | Wiederherstellen | Die Sicherungsdatei wurde erfolgreich in Azure SQL Managed Instance wiederhergestellt |
    | Canceled | Migrationsprozess wurde abgebrochen |
    | Wird ignoriert. | Die Sicherungsdatei wurde ignoriert, da sie nicht zu einer gültigen Datenbanksicherungskette gehört |

    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/online-to-mi-migration-details-all-backups-restored.png" alt-text="Details zu Sicherung und Wiederherstellung":::

## <a name="complete-migration-cutover"></a>Vervollständigen der Migrationsübernahme

Im letzten Schritt des Tutorials wird die Migrationsübernahme abgeschlossen, um sicherzustellen, dass die migrierte Datenbank in Azure SQL Managed Instance einsatzbereit ist. Dies ist der einzige Teil des Prozesses, der Downtime für Anwendungen erfordert, die eine Verbindung mit der Datenbank herstellen. Daher muss das Timing der Übernahme sorgfältig mit den Projektbeteiligten im Unternehmen, die die Anwendungen nutzen, geplant werden.

Um die Übernahme abzuschließen,

1. beenden Sie alle eingehenden Transaktionen in der Quelldatenbank, und bereiten Sie sich darauf vor, ggf. Änderungen an der Anwendungskonfiguration vorzunehmen, um in Azure SQL Managed Instance auf die Zieldatenbank zu verweisen.
2. nehmen Sie ggf. Sicherungen des Protokollfragments für die Quelldatenbank am angegebenen Sicherungsspeicherort vor.
3. stellen Sie sicher, dass alle Datenbanksicherungen auf der Seite mit den Überwachungsdetails den Status *Wiederhergestellt* aufweisen.
4. wählen Sie auf der Seite mit den Überwachungsdetails die Option *Übernahme abschließen* aus.

Während des Übernahmeprozesses ändert sich der Migrationsstatus von *In Bearbeitung* in *Wird abgeschlossen*. Nach Abschluss des Übernahmeprozesses ändert sich der Migrationsstatus in *Erfolgreich*, um anzugeben, dass die Datenbankmigration erfolgreich war und die migrierte Datenbank einsatzbereit ist.

> [!IMPORTANT]
> Nach der Übernahme kann die Verfügbarkeit von SQL Managed Instance mit nur der Dienstebene „Unternehmenskritisch“ erheblich länger dauern als mit „Universell“, da das Seeding von drei sekundären Replikaten für eine Always On-Verfügbarkeitsgruppe mit Hochverfügbarkeit ausgeführt werden muss. Die Dauer dieses Vorgangs hängt von der Größe der Daten ab. Weitere Informationen finden Sie unter [Dauer von Verwaltungsvorgängen](../azure-sql/managed-instance/management-operations-overview.md#duration).

## <a name="next-steps"></a>Nächste Schritte

* Ein Tutorial, in dem gezeigt wird, wie Sie eine Datenbank mit dem T-SQL-Befehl RESTORE zu SQL Managed Instance migrieren können, finden Sie unter [Wiederherstellen einer Sicherung in SQL Managed Instance mit dem restore-Befehl](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
* Informationen zu SQL Managed Instance finden Sie unter [Was ist SQL Managed Instance?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
* Informationen zum Verbinden von Apps mit SQL Managed Instance finden Sie unter [Verbinden von Anwendungen](../azure-sql/managed-instance/connect-application-instance.md).
