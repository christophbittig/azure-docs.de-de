---
title: Migrieren mithilfe von Azure Data Studio
description: Erfahren Sie, wie Sie die Azure SQL SQL-Migrationserweiterung in Azure Data Studio verwenden, um Datenbanken mit Azure Database Migration Service zu migrieren.
services: database-migration
author: mokabiru
ms.author: mokabiru
manager: ''
ms.reviewer: cheryl.adams
ms.service: dms
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: d2840b5e8386801411a759d1b00f026bfbacad68
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027696"
---
# <a name="migrate-databases-with-azure-sql-migration-extension-for-azure-data-studio-preview"></a>Migrieren von Datenbanken mit der Azure SQL-Migrationserweiterung für Azure Data Studio (Vorschau)

Mit der [Azure SQL-Migrationserweiterung für Azure Data Studio](/sql/azure-data-studio/extensions/azure-sql-migration-extension) können Sie die Bewertungs- und -Migrationsfunktion von SQL Server in Azure Data Studio verwenden.

## <a name="architecture-of-azure-sql-migration-extension-for-azure-data-studio"></a>Architektur der Azure SQL-Migrationserweiterung für Azure Data Studio

Azure Database Migration Service (DMS) ist eine der Kernkomponenten in der Gesamtarchitektur. DMS bietet einen zuverlässigen Migrationsorchestrator, um Datenbankmigrationen zu Azure SQL zu ermöglichen. Erstellen oder verwenden Sie einen vorhandenen DMS mithilfe der Azure SQL-Migrationserweiterung in Azure Data Studio (ADS) erneut.
DMS verwendet die selbstgehostete Integration Runtime von Azure Data Factory, um auf gültige Sicherungsdateien auf Ihrer lokalen Netzwerkfreigabe oder in Ihrem Azure Storage-Konto zuzugreifen und diese hochzuladen.

Der Workflow des Migrationsprozesses ist unten dargestellt.

:::image type="content" source="media/migration-using-azure-data-studio/architecture-ads-sql-migration.png" alt-text="Abbildung der Architektur für die Datenbankmigration mithilfe von Azure Data Studio mit DMS":::

1. **SQL-Quellserver**: SQL Server-Instanz, lokal, in einer privaten Cloud oder auf einem beliebigen virtuellen Computer in der öffentlichen Cloud. Alle Editionen von SQL Server 2008 und höher werden unterstützt.
1. **Azure SQL als Ziel**: Unterstützte Azure SQL-Ziele sind Azure SQL Managed Instance oder SQL Server in Azure Virtual Machines (registriert bei der SQL IaaS-Agent-Erweiterung im [Vollständigen Verwaltungsmodus](../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md#management-modes)).
1. **Netzwerkdateifreigabe**: Server Message Block -Netzwerkdateifreigabe (SMB), in der Sicherungsdateien für die zu migrierenden Datenbanken gespeichert werden. Azure Storage-Blobcontainer und Azure Storage-Dateifreigaben werden ebenfalls unterstützt.
1. **Azure Data Studio**: Laden Sie die [Azure SQL-Migrationserweiterung in Azure Data Studio](/sql/azure-data-studio/extensions/azure-sql-migration-extension) herunter, und installieren Sie sie.
1. **Azure DMS**: Azure-Dienst, der Migrationspipelines orchestriert, um Datenverschiebungsaktivitäten aus der lokalen Umgebung zu Azure auszuführen. DMS ist der selbstgehosteten Integration Runtime (IR) von Azure Data Factory (ADF) zugeordnet und bietet die Möglichkeit, die selbstgehostete IR zu registrieren und zu überwachen.
1. **Selbstgehostete Integration Runtime (IR)** : Die selbstgehostete IR sollte auf einem Computer installiert sein, der eine Verbindung mit dem SQL-Quellserver und dem Speicherort der Sicherungsdateien herstellen kann. DMS stellt die Authentifizierungsschlüssel bereit und registriert die selbstgehostete IR.
1. **Hochladen von Sicherungsdateien in Azure Storage**: DMS verwendet eine selbstgehostete IR, um gültige Sicherungsdateien vom lokalen Sicherungsspeicherort in Ihr bereitgestelltes Azure Storage-Konto hochzuladen. Datenverschiebungsaktivitäten und Pipelines werden automatisch im Migrationsworkflow erstellt, um die Sicherungsdateien hochzuladen.
1. **Wiederherstellen von Sicherungen im Azure SQL-Ziel**: DMS stellt Sicherungsdateien aus Ihrem Azure Storage-Konto auf dem unterstützten Azure SQL-Ziel wieder her. 
    > [!IMPORTANT]
    > Im Onlinemigrationsmodus lädt DMS kontinuierlich die Quellsicherungsdateien in Azure Storage hoch und stellt sie im Ziel wieder her, bis Sie den letzten Schritt des Umstiegs auf das Ziel abgeschlossen haben.
    >
    > Im Offlinemigrationsmodus lädt DMS die Quellsicherungsdateien in Azure Storage hoch und stellt sie im Ziel wieder her, ohne dass Sie eine Übernahme durchführen müssen.

## <a name="prerequisites"></a>Voraussetzungen

Folgende Voraussetzungen von Azure Database Migration Service gelten für alle unterstützten Migrationsszenarien:

* [Herunterladen und Installieren von Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Installieren der Azure SQL-Migrationserweiterung](/sql/azure-data-studio/extensions/azure-sql-migration-extension) aus Azure Data Studio Marketplace
* Verwenden Sie ein Azure-Konto, das einer der unten aufgeführten integrierten Rollen zugewiesen ist:
    - Mitwirkender für die Zielinstanz von Azure SQL Managed Instance (und Storage-Konto zum Hochladen Ihrer Datenbanksicherungsdateien von der SMB-Netzwerkfreigabe).
    - Rolle „Besitzer“ oder „Mitwirkender“ für die Azure-Ressourcengruppen, die die Zielinstanz von Azure SQL Managed Instance oder das Azure-Speicherkonto enthalten.
    - Rolle „Besitzer“ oder „Mitwirkender“ für das Azure-Abonnement.
* Erstellen Sie eine Zielinstanz von [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md) oder [SQL Server in Azure Virtual Machine](../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).
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

* Stellen Sie bei Verwendung der selbstgehosteten Integration Runtime sicher, dass der Computer, auf dem die Runtime installiert ist, eine Verbindung mit der SQL Server-Quellinstanz und der Netzwerkdateifreigabe herstellen kann, auf der sich Sicherungsdateien befinden. Der ausgehende Port 445 muss aktiviert sein, um Zugriff auf die Netzwerkdateifreigabe zu ermöglichen.
* Wenn Sie Azure Database Migration Service zum ersten Mal verwenden, stellen Sie sicher, dass der Ressourcenanbieter Microsoft.DataMigration in Ihrem Abonnement registriert ist. Sie können die Schritte zum [Registrieren des Ressourcenanbieters](./quickstart-create-data-migration-service-portal.md#register-the-resource-provider) ausführen.

### <a name="recommendations-for-using-self-hosted-integration-runtime-for-database-migrations"></a>Empfehlungen für die Verwendung der selbstgehosteten Integration Runtime für Datenbankmigrationen
- Verwenden Sie eine einzelne selbstgehostete Integration Runtime für mehrere SQL Server-Quelldatenbanken.
- Installieren Sie auf einem Computer nur jeweils eine Instanz der selbstgehosteten Integration Runtime.
- Ordnen Sie einem DMS nur eine selbstgehostete Integration Runtime zu.
- Die selbstgehostete Integration Runtime verwendet Ressourcen (Arbeitsspeicher/CPU) auf dem Computer, auf dem sie installiert ist. Installieren Sie die selbstgehostete Integration Runtime auf einem anderen Computer als auf Ihrem SQL-Quellserver. Wenn sich die selbstgehostete Integration Runtime in der Nähe der Datenquelle befindet, dauert es weniger lange, bis die selbstgehostete Integration Runtime eine Verbindung mit der Datenquelle hergestellt hat. 
- Verwenden Sie die selbstgehostete Integration Runtime nur, wenn sich Ihre Datenbanksicherungen in einer lokalen SMB-Netzwerkfreigabe befinden. Die selbstgehostete Integration Runtime ist für Datenbankmigrationen nicht erforderlich, wenn sich Ihre Quelldatenbanksicherungen bereits im Azure Storage-Blobcontainer befinden.
- Es werden bis zu 10 gleichzeitige Datenbankmigrationen pro selbstgehosteter Integration Runtime auf einem einzelnen Computer empfohlen. Um die Anzahl gleichzeitiger Datenbankmigrationen zu erhöhen, skalieren Sie die selbstgehostete Runtime auf bis zu vier Knoten auf, oder erstellen Sie eine separate selbstgehostete Integration Runtime auf verschiedenen Computern.
- Konfigurieren Sie die selbstgehostete Integration Runtime für automatische Aktualisierung, um automatisch alle neuen Features, Fehlerbehebungen und Verbesserungen anzuwenden, die veröffentlicht werden. Weitere Informationen finden Sie unter [Automatische Aktualisierung der selbstgehosteten Integration Runtime](../data-factory/self-hosted-integration-runtime-auto-update.md).

## <a name="known-issues-and-limitations"></a>Einschränkungen und bekannte Probleme
- Das Überschreiben vorhandener Datenbanken mit DMS in Ihrer Zielinstanz von Azure SQL Managed Instance oder SQL Server in Azure Virtual Machines wird nicht unterstützt.
- Das Konfigurieren von Hochverfügbarkeit und Notfallwiederherstellung auf Ihrem Ziel entsprechend der Quelltopologie wird von DMS nicht unterstützt.
- Die folgenden Serverobjekte werden nicht unterstützt:
    - Anmeldungen
    - Aufträge des SQL Server-Agents
    - Anmeldeinformationen
    - SSIS-Pakete
    - Serverrollen
    - Serverüberwachung
- Das Automatisieren von Migrationen mit Azure Data Studio mithilfe von PowerShell oder der CLI wird nicht unterstützt.
- Die Migration zu Azure SQL-Datenbank wird nicht unterstützt.
- Azure-Speicherkonten, die durch bestimmte Firewallregeln geschützt oder mit einem privaten Endpunkt konfiguriert sind, werden für Migrationen nicht unterstützt.
- Sie können keine vorhandene selbstgehostete Integration Runtime verwenden, die aus Azure Data Factory für Datenbankmigrationen mit DMS erstellt wurde. Anfänglich sollte die selbstgehostete Integration Runtime mithilfe der Azure SQL-Migrationserweiterung in Azure Data Studio erstellt werden. Sie kann für weitere Datenbankmigrationen wiederverwendet werden.
> [!IMPORTANT]
> **Bekanntes Problem beim Migrieren mehrerer Datenbanken zu SQL Server auf einer Azure-VM**: Die gleichzeitige Migration mehrerer Datenbanken zur gleichen SQL Server-Instanz auf einer Azure-VM führt für die meisten Datenbanken zu Migrationsfehlern. Stellen Sie sicher, dass Sie zu einem beliebigen Zeitpunkt nur eine Einzeldatenbank zu einer SQL Server-Instanz auf einer Azure-VM migrieren.  

## <a name="pricing"></a>Preise
- Azure Database Migration Service kann in Azure Data Studio kostenlos mit der Azure SQL-Migrationserweiterung verwendet werden. Sie können mehrere SQL Server-Datenbanken mithilfe des Azure Database Migration Service ohne Kosten für die Verwendung des Diensts oder der Azure SQL-Migrationserweiterung migrieren.
- Für die Migration Ihrer Datenbanken von einer lokalen Datenbank zu Azure entstehen keine Datenverschiebungs- oder Dateneingangskosten. Wenn die Quelldatenbank aus einer anderen Region oder einer Azure-VM verschoben wird, fallen je nach Bandbreitenanbieter und Routingszenario möglicherweise [Bandbreitengebühren](https://azure.microsoft.com/pricing/details/bandwidth/) an.
- Stellen Sie Ihren eigenen Computer oder lokalen Server bereit, um Azure Data Studio zu installieren.
- Für den Zugriff auf Datenbanksicherungen von Ihrer lokalen Netzwerkfreigabe ist eine selbstgehostete Integration Runtime erforderlich.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht und weitere Informationen zur Installation der Azure SQL-Migrationserweiterung finden Sie unter [Azure SQL-Migrationserweiterung für Azure Data Studio](/sql/azure-data-studio/extensions/azure-sql-migration-extension).
