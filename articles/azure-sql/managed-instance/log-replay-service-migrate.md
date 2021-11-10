---
title: Migrieren von Datenbanken zu SQL Managed Instance mithilfe des Protokollwiedergabediensts
description: Es wird beschrieben, wie Sie Datenbanken aus SQL Server zu SQL Managed Instance migrieren, indem Sie den Protokollwiedergabedienst verwenden.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: mathoma
ms.date: 09/21/2021
ms.openlocfilehash: 8dbe12ec428820f14cce427e4780ec4d5d4fd5c8
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893308"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Migrieren von Datenbanken aus SQL Server zu SQL Managed Instance mit dem Protokollwiedergabedienst (Vorschau)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In diesem Artikel wird beschrieben, wie Sie die Datenbankmigration von SQL Server 2008 bis 2019 zu Azure SQL Managed Instance konfigurieren, indem Sie den Protokollwiedergabedienst (Log Replay Service, LRS) verwenden, der sich derzeit in der öffentlichen Vorschauphase befindet. Hierbei handelt es sich um einen Clouddienst, der für SQL Managed Instance aktiviert ist und auf der SQL Server-Technologie für den Protokollversand basiert. 

Für [Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) und den Protokollwiedergabedienst werden die gleiche zugrunde liegende Migrationstechnologie und die gleichen APIs verwendet. Mit der Veröffentlichung des Protokollwiedergabediensts verbessern wir die Voraussetzungen für komplexe benutzerdefinierte Migrationen und die Hybridarchitektur zwischen lokalen SQL Server-Instanzen und SQL Managed Instance.

## <a name="when-to-use-log-replay-service"></a>Wann sollte der Protokollwiedergabedienst verwendet werden?

Wenn bei Ihnen die Verwendung von Azure Database Migration Service für die Migration nicht möglich ist, können Sie den Protokollwiedergabedienst direkt mit PowerShell, Azure CLI-Cmdlets oder APIs nutzen, um Datenbankmigrationen zu SQL Managed Instance manuell zu erstellen und zu orchestrieren. 

Die Verwendung des Protokollwiedergabediensts kann in den folgenden Fällen hilfreich sein:
- Sie benötigen eine bessere Kontrolle über Ihr Datenbankmigrationsprojekt.
- Es besteht nur eine geringe Toleranz in Bezug auf Ausfallzeiten bei der Migrationsübernahme.
- Die ausführbare Datei von Database Migration Service kann in Ihrer Umgebung nicht installiert werden.
- Für die ausführbare Datei von Database Migration Service besteht kein Zugriff auf Datenbanksicherungen.
- Es besteht kein Zugriff auf das Hostbetriebssystem, oder es sind keine Administratorrechte vorhanden.
- Sie können in Ihrer Umgebung keine Netzwerkports für Azure öffnen.
- Netzwerk Drosselung oder Proxy Blockierungs Probleme in Ihrer Umgebung.
- Sicherungen werden über die Option `TO URL` direkt in Azure Blob Storage gespeichert.
- Sie müssen differenzielle Sicherungen verwenden.

> [!NOTE]
> Wir empfehlen Ihnen, die Migration von Datenbanken von SQL Server zu SQL Managed Instance mit dem Database Migration Service zu automatisieren. Hierbei wird auf dem Back-End der gleiche Clouddienst für den Protokollwiedergabedienst mit Protokollversand im Modus `NORECOVERY` genutzt. Erwägen Sie die manuelle Verwendung des Protokollwiedergabediensts zum Orchestrieren von Migrationen, falls Ihre Szenarien von Database Migration Service nicht vollständig unterstützt werden.

## <a name="how-it-works"></a>Funktionsweise

Zum Erstellen einer benutzerdefinierten Lösung zum Migrieren von Datenbanken zur Cloud mit dem Protokollwiedergabedienst sind einige Orchestrierungsschritte erforderlich. Dies ist weiter unten in diesem Abschnitt im Diagramm und der zugehörigen Tabelle dargestellt.

Die Migration umfasst vollständige Datenbanksicherungen in SQL Server mit Aktivierung von `CHECKSUM` und das Kopieren von Sicherungsdateien in Azure Blob Storage. Der Protokollwiedergabedienst wird zum Wiederherstellen von Sicherungsdateien aus Blob Storage in SQL Managed Instance verwendet. Blob Storage dient als zwischengeschalteter Speicher zwischen SQL Server und SQL Managed Instance.

Der Protokollwiedergabedienst überwacht Blob Storage auf neue differenzielle oder Protokollsicherungen, die nach der Wiederherstellung der vollständigen Sicherung hinzugefügt werden. Diese neuen Dateien werden vom Protokollwiedergabedienst dann automatisch wiederhergestellt. Sie können den Dienst zum Überwachen des Status von Sicherungsdateien nutzen, die unter SQL Managed Instance wiederhergestellt werden, und den Prozess bei Bedarf anhalten.

Für den Protokollwiedergabedienst ist für Sicherungsdateien keine bestimmte Namenskonvention erforderlich. Vom Dienst werden alle Dateien überprüft, die in Blob Storage gespeichert werden, und die Sicherungskette wird erstellt, indem nur die Dateiheader gelesen werden. Datenbanken weisen während des Migrationsprozesses den Status „Wird wiederhergestellt“ auf. Die Datenbanken werden im Modus [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) wiederhergestellt. Sie können daher erst wieder zum Lesen oder Schreiben verwendet werden, nachdem der Migrationsprozess abgeschlossen ist. 

Beachten Sie Folgendes, wenn Sie mehrere Datenbanken migrieren:
 
- Legen Sie die Sicherungen für die einzelnen Datenbanken in Blob Storage in einem separaten Ordner ab.
- Starten Sie den Protokollwiedergabedienst für jede Datenbank separat.
- Geben Sie verschiedene Pfade zu separaten Blob Storage-Ordnern an. 

Sie können den Protokollwiedergabedienst entweder im Modus *AutoVervollständigen* oder *Kontinuierlich* starten. Beim Starten im Modus „AutoVervollständigen“ wird der Migrationsprozess automatisch beendet, nachdem die letzte angegebene Sicherungsdatei wiederhergestellt wurde. Wenn der Protokollwiedergabedienst im Modus „Kontinuierlich“ gestartet wird, werden alle neu hinzugefügten Sicherungsdateien fortlaufend wiederhergestellt, und die Migration wird nur bei einer manuellen Übernahme beendet. 

Wir empfehlen Ihnen, die Übernahme manuell durchzuführen, nachdem die letzte Log Tail-Sicherung erstellt wurde und dafür in SQL Managed Instance die Wiederherstellung angezeigt wird. Mit dem abschließenden Übernahmeschritt wird die Datenbank online geschaltet und ist für die Lese- und Schreibverwendung in SQL Managed Instance verfügbar.

Nachdem der Protokollwiedergabedienst beendet wurde – entweder automatisch bei AutoVervollständigen oder manuell bei der Übernahme –, können Sie den Wiederherstellungsvorgang für eine Datenbank, die in SQL Managed Instance in den Onlinezustand versetzt wurde, nicht fortsetzen. Sie müssen die Datenbank löschen, um weitere Sicherungsdateien wiederherstellen zu können, nachdem die Migration per AutoVervollständigen oder Übernahme beendet wurde. Darüber hinaus müssen Sie die gesamte Sicherungskette wiederherstellen, indem Sie den Protokollwiedergabedienst neu starten.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagramm: Orchestrierungsschritte des Protokollwiedergabediensts für SQL Managed Instance" border="false":::
    
| Vorgang | Details |
| :----------------------------- | :------------------------- |
| **1. Kopieren von Datenbanksicherungen aus SQL Server in Blob Storage** | Kopieren Sie vollständige, differenzielle und Protokollsicherungen aus SQL Server per [AzCopy](../../storage/common/storage-use-azcopy-v10.md) oder [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) in einen Blob Storage-Container. <br /><br />Hierbei können Sie beliebige Dateinamen verwenden. Für den Protokollwiedergabedienst muss keine bestimmte Namenskonvention für Dateien befolgt werden.<br /><br />Bei der Migration mehrerer Datenbanken benötigen Sie für jede Datenbank einen separaten Ordner. |
| **2. Starten des Protokollwiedergabediensts in der Cloud** | Sie können den Dienst mit den folgenden Cmdlets neu starten: PowerShell ([start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) oder Azure CLI ([az_sql_midb_log_replay_start](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Starten Sie den Protokollwiedergabedienst separat für jede Datenbank, die auf einen Sicherungsordner in Blob Storage verweist. <br /><br /> Nachdem der Dienst gestartet wurde, wird mit dem Wiederherstellen der Sicherungen aus dem Blob Storage-Container in SQL Managed Instance begonnen.<br /><br /> Wenn Sie den Protokollwiedergabedienst im Modus „Kontinuierlich“ gestartet haben, achtet der Dienst nach der Wiederherstellung aller anfänglich hochgeladenen Sicherungen auf neue Dateien, die in den Ordner hochgeladen werden. Vom Dienst werden basierend auf der Kette mit den Protokollfolgenummern fortlaufend Protokolle angewendet, bis der Dienst beendet wird. |
| **2.1. Überwachen des Vorgangsstatus** | Sie können den Status des Wiederherstellungsvorgangs mit den folgenden Cmdlets überwachen: PowerShell ([get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) oder Azure CLI ([az_sql_midb_log_replay_show](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2.2. Beenden des Vorgangs bei Bedarf** | Falls Sie den Migrationsprozess beenden müssen, können Sie die folgenden Cmdlets verwenden: PowerShell ([stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) oder Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> Wenn Sie den Vorgang beenden, wird die Datenbank gelöscht, die Sie in SQL Managed Instance wiederherstellen. Nachdem Sie einen Vorgang beendet haben, können Sie den Protokollwiedergabedienst für eine Datenbank nicht fortsetzen. Sie müssen den Migrationsprozess ganz neu starten. |
| **3. Durchführen der Übernahme in die Cloud (bei entsprechender Bereitschaft)** | Beenden Sie die Anwendung und die Workload. Erstellen Sie die letzte Log Tail-Sicherung, und laden Sie sie in Azure Blob Storage hoch.<br /><br /> Beenden Sie die Übernahme, indem Sie für den Protokollwiedergabedienst den Vorgang `complete` mit einem der folgenden Cmdlets initiieren: PowerShell ([complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) oder Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Mit diesem Vorgang wird der Protokollwiedergabedienst beendet. Dies bewirkt, dass die Datenbank in den Onlinezustand versetzt wird und in SQL Managed Instance für Lese- und Schreibvorgänge genutzt werden kann.<br /><br /> Legen Sie für die Verbindungszeichenfolge der Anwendung fest, dass sie nicht mehr auf SQL Server, sondern auf SQL Managed Instance verweist. Sie müssen diesen Schritt selbst orchestrieren, entweder durch eine manuelle Änderung der Verbindungszeichenfolge in Ihrer Anwendung oder automatisch (z. B. wenn Ihre Anwendung die Verbindungszeichenfolge aus einer Eigenschaft oder einer Datenbank lesen kann). |

## <a name="requirements-for-getting-started"></a>Anforderungen für den Einstieg

### <a name="sql-server-side"></a>SQL Server-seitig
- SQL Server 2008-2019
- Vollständige Sicherung von Datenbanken (eine oder mehrere Dateien)
- Differenzielle Sicherung (eine oder mehrere Dateien)
- Protokollsicherung (keine Aufteilung für eine Transaktionsprotokolldatei)
- `CHECKSUM` für Sicherungen aktiviert (obligatorisch)

### <a name="azure-side"></a>Azure-seitig
- PowerShell Az.SQL-Modul, Version 2.16.0 oder höher ([Installation](https://www.powershellgallery.com/packages/Az.Sql/) oder Zugriff per [Azure Cloud Shell](/azure/cloud-shell/))
- Azure CLI, Version 2.19.0 oder höher ([Installation](/cli/azure/install-azure-cli))
- Azure Blob Storage-Container bereitgestellt
- SAS-Sicherheitstoken (Shared Access Signature) mit generierten Berechtigungen „Lesen“ und „Auflisten“ für den Blob Storage-Container

### <a name="azure-rbac-permissions"></a>Azure RBAC-Berechtigungen
Für die Ausführung des Protokollwiedergabediensts über die bereitgestellten Clients wird eine der folgenden Azure-Rollen benötigt:
- Rolle „Besitzer des Abonnements“
- Rolle [„Mitwirkender für verwaltete SQL-Instanzen“](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Benutzerdefinierte Rolle mit der folgenden Berechtigung: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Bewährte Methoden

Wir empfehlen die folgenden bewährten Methoden:
- Führen Sie den [Datenmigrations-Assistenten](/sql/dma/dma-overview) aus, um zu überprüfen, ob Ihre Datenbanken für die Migration zu SQL Managed Instance bereit sind. 
- Teilen Sie vollständige und differenzielle Sicherungen in mehrere Dateien auf, anstatt nur eine Datei zu verwenden.
- Aktivieren Sie die Sicherungskomprimierung.
- Verwenden Sie Cloud Shell für die Ausführung von Skripts, da hierfür immer eine Aktualisierung auf die neuesten veröffentlichten Cmdlets durchgeführt wird.
- Planen Sie, dass die Migration innerhalb von 36 Stunden nach dem Starten des Protokollwiedergabediensts abgeschlossen sein soll. Dies ist eine Toleranzperiode, mit der verhindert werden soll, dass systemseitig verwaltete Softwarepatches installiert werden.
- Speichern Sie alle Sicherungsdateien für eine einzelne Datenbank in einem einzigen Ordner. Verwenden Sie keine Unterordner für dieselbe Datenbank.

> [!IMPORTANT]
> - Sie können die Datenbank, die mit dem Protokollwiedergabedienst wiederhergestellt wird, erst verwenden, nachdem der Migrationsprozess abgeschlossen ist. 
> - Für den Protokollwiedergabedienst wird der schreibgeschützte Zugriff auf Datenbanken während der Migration nicht unterstützt.
> - Nach Abschluss der Migration wird der Migrationsprozess beendet, da das Fortsetzen des Wiederherstellungsprozesses vom Protokollwiedergabedienst nicht unterstützt wird.

## <a name="steps-to-execute"></a>Auszuführende Schritte

### <a name="make-backups-of-sql-server"></a>Erstellen von SQL Server-Sicherungen

Sie können eine der folgenden Optionen verwenden, um Sicherungen von SQL Server zu erstellen:

- Erstellen Sie eine Sicherung im lokalen Datenträgerspeicher, und laden Sie die Dateien dann in Azure Blob Storage hoch, falls in Ihrer Umgebung direkte Sicherungen in Blob Storage nicht zulässig sind.
- Sie können direkte Sicherungen in Blob Storage mit der Option `TO URL` in T-SQL erstellen, falls dies für Ihre Umgebung und Ihre Sicherheitsverfahren zulässig ist. 

Legen Sie für Datenbanken, die Sie migrieren möchten, den Modus für die vollständige Wiederherstellung fest, um Protokollsicherungen zuzulassen.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Verwenden Sie die folgenden T-SQL-Beispielskripts, um im lokalen Speicher manuell vollständige, differenzielle und Protokollsicherungen Ihrer Datenbank zu erstellen. Achten Sie darauf, dass die Option `CHECKSUM` aktiviert ist, da sie für den Protokollwiedergabedienst obligatorisch ist.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Azure Blob Storage wird als zwischengeschalteter Speicher für Sicherungsdateien zwischen SQL Server und SQL Managed Instance verwendet. Führen Sie die folgenden Schritte aus, um ein neues Speicherkonto und einen Blobcontainer im Speicherkonto zu erstellen:

1. [Informationen zu Azure-Speicherkonten](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Erstellen eines Blobcontainers](../../storage/blobs/storage-quickstart-blobs-portal.md) im Speicherkonto

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Kopieren von Sicherungen aus SQL Server nach Azure Blob Storage

Beim Migrieren von Datenbanken zu einer verwalteten Instanz mit dem Protokollwiedergabedienst können Sie die folgenden Ansätze nutzen, um Sicherungen in Blob Storage hochzuladen:
- Verwenden der nativen SQL Server-Funktion für die [Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Verwenden von [AzCopy](../../storage/common/storage-use-azcopy-v10.md) oder des [Azure Storage-Explorers](https://azure.microsoft.com/features/storage-explorer) zum Hochladen von Sicherungen in einen Blobcontainer
- Verwenden des Storage-Explorers im Azure-Portal

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Erstellen von Sicherungen aus SQL Server direkt in Blob Storage
Wenn Ihre Unternehmens- und Netzwerkrichtlinien dies zulassen, ist eine andere Möglichkeit die direkte Erstellung von Sicherungen aus SQL Server in Blob Storage, indem Sie die native SQL Server-Option für die [Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-to-url) verwenden. Wenn Sie diese Option nutzen können, ist es nicht erforderlich, dass Sie Sicherungen im lokalen Speicher erstellen und in Blob Storage hochladen.

Bei diesem Vorgang müssen Sie zunächst ein SAS-Authentifizierungstoken für Blob Storage generieren und es dann in SQL Server importieren. Der zweite Schritt ist das Erstellen von Sicherungen mit der Option `TO URL` in T-SQL. Stellen Sie sicher, dass alle Sicherungen mit aktivierter CHECKSUM-Option durchgeführt werden.

Mit dem unten angegebenen Beispielcode werden Sicherungen in Blob Storage erstellt. Dieses Beispiel enthält keine Anweisungen zum Importieren des SAS-Tokens. Eine ausführliche Anleitung, z. B. zum Generieren und Importieren des SAS-Tokens in SQL Server, finden Sie im Tutorial zum [Verwenden des Microsoft Azure BLOB-Speicherdiensts mit SQL Server 2016](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Generieren eines SAS-Authentifizierungstokens für den Protokollwiedergabedienst zum Zugreifen auf Blob Storage

Azure Blob Storage wird als zwischengeschalteter Speicher für Sicherungsdateien zwischen SQL Server und SQL Managed Instance verwendet. Sie müssen für den Protokollwiedergabedienst ein SAS-Authentifizierungstoken nur mit den Berechtigungen „Auflisten“ und „Lesen“ erstellen. Mit dem Token kann der Protokollwiedergabedienst auf Blob Storage zugreifen und die Sicherungsdateien verwenden, um diese unter SQL Managed Instance wiederherzustellen. 

Führen Sie die folgenden Schritte aus, um das Token zu generieren:

1. Öffnen Sie im Azure-Portal den Storage-Explorer.
2. Erweitern Sie die Option **Blobcontainer**.
3. Klicken Sie mit der rechten Maustaste auf den Blobcontainer, und wählen Sie die Option **Shared Access Signature abrufen** aus.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Screenshot: Auswahl zum Generieren eines SAS-Authentifizierungstokens":::

4. Wählen Sie den Zeitrahmen für den Tokenablauf aus. Stellen Sie sicher, dass das Token für die Dauer Ihres Migrationsprozesses gültig ist.
5. Wählen Sie die Zeitzone für das Token aus: UTC oder Ortszeit.
    
   > [!IMPORTANT]
   > Die Zeitzone des Tokens stimmt unter Umständen nicht mit Ihrer verwalteten Instanz überein. Stellen Sie sicher, dass das SAS-Token unter Berücksichtigung der Zeitzonen über die entsprechende Gültigkeit verfügt. Legen Sie die Zeitzone nach Möglichkeit auf einen früheren und späteren Zeitpunkt des geplanten Migrationsfensters fest.
6. Wählen Sie nur die Berechtigungen **Lesen** und **Auflisten** aus.

   > [!IMPORTANT]
   > Wählen Sie keine anderen Berechtigungen aus. Wenn Sie dies nicht beachten, wird der Protokollwiedergabedienst nicht gestartet. Diese Sicherheitsanforderung ist entwurfsbedingt.
7. Klicken Sie auf **Erstellen**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Screenshot: Auswahl von Gültigkeitsdauer, Zeitzone und Berechtigungen für das SAS-Token und Schaltfläche „Erstellen“":::

Die SAS-Authentifizierung wird mit der von Ihnen angegebenen Gültigkeitsdauer generiert. Sie benötigen die URI-Version des Tokens (wie im folgenden Screenshot dargestellt).

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Screenshot: Beispiel für die URI-Version eines SAS-Tokens":::

### <a name="copy-parameters-from-the-sas-token"></a>Kopieren von Parametern aus dem SAS-Token

Bevor Sie das SAS-Token zum Starten des Protokollwiedergabediensts verwenden, müssen Sie dessen Struktur verstehen. Der URI des generierten SAS-Tokens besteht aus zwei Teilen, die durch ein Fragezeichen (`?`) getrennt sind. Dies ist in diesem Beispiel dargestellt:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Beispiel: URI für ein generiertes SAS-Token für Protokollwiedergabedienst" border="false":::

Der erste Teil, der mit `https://` beginnt und bis zum Fragezeichen (`?`) reicht, wird für den Parameter `StorageContainerURI` verwendet, der als Eingabe an den Protokollwiedergabedienst übergeben wird. Er liefert LRS-Informationen zu dem Ordner, in dem die Datenbanksicherungsdateien gespeichert werden.

Der zweite Teil, der nach dem Fragezeichen (`?`) beginnt und bis zum Ende der Zeichenfolge reicht, ist der Parameter `StorageContainerSasToken`. Dies ist das eigentliche signierte Authentifizierungstoken, das für den angegebenen Zeitraum gültig ist. Dieser Teil muss nicht unbedingt wie im Beispiel mit `sp=` beginnen. Bei Ihnen wird unter Umständen eine andere Zeichenfolge verwendet.

Kopieren Sie die Parameter wie folgt:

1. Kopieren Sie den ersten Teil des Tokens von `https://` bis zum Fragezeichen (`?`). Verwenden Sie diese Zeichenfolge als Parameter `StorageContainerUri` in PowerShell oder der Azure CLI, um den Protokollwiedergabedienst zu starten.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Screenshot: Kopieren des ersten Teils des Tokens":::

2. Kopieren Sie den zweiten Teil des Tokens ab dem Fragezeichen (`?`) bis zum Ende der Zeichenfolge. Verwenden Sie diese Zeichenfolge als Parameter `StorageContainerSasToken` in PowerShell oder der Azure CLI, um den Protokollwiedergabedienst zu starten.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Screenshot: Kopieren des zweiten Teils des Tokens":::
   
> [!NOTE]
> Lassen Sie das Fragezeichen beim Kopieren der beiden Teile des Tokens jeweils weg.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Anmelden bei Azure und Auswählen eines Abonnements

Verwenden Sie das folgende PowerShell-Cmdlet, um sich bei Azure anzumelden:

```powershell
Login-AzAccount
```

Wählen Sie mit dem folgenden PowerShell-Cmdlet das entsprechende Abonnement aus, in dem sich Ihre verwaltete Instanz befindet:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Starten der Migration

Sie starten die Migration, indem Sie den Protokollwiedergabedienst starten. Hierbei können Sie den Dienst entweder im Modus „AutoVervollständigen“ oder „Kontinuierlich“ starten. 

Bei Verwendung des Modus „AutoVervollständigen“ wird der Migrationsprozess automatisch beendet, nachdem die letzte angegebene Sicherungsdatei wiederhergestellt wurde. Diese Option erfordert, dass mit dem Startbefehl der Dateiname der letzten Sicherungsdatei angegeben wird. 

Bei Verwendung des Modus „Kontinuierlich“ werden vom Dienst fortlaufend alle neuen Sicherungsdateien wiederhergestellt, die hinzugefügt werden. Die Migration wird nur bei der manuellen Übernahme beendet. 

### <a name="start-lrs-in-autocomplete-mode"></a>Starten von LRS im AutoVervollständigen-Modus

Verwenden Sie den folgenden PowerShell- oder Azure CLI-Befehl, um den Protokollwiedergabedienst im Modus „AutoVervollständigen“ zu starten. Geben Sie den Namen der letzten Sicherungsdatei an, indem Sie den Parameter `-LastBackupName` verwenden. Nach dem Wiederherstellen der letzten angegebenen Sicherungsdatei initiiert der Dienst automatisch eine Übernahme.

Hier ist ein Beispiel für das Starten des Protokollwiedergabediensts im Modus „AutoVervollständigen“ mit PowerShell angegeben:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Hier ist ein Beispiel für das Starten des Protokollwiedergabediensts im Modus „AutoVervollständigen“ mit der Azure CLI angegeben:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Starten von LRS im kontinuierlichen Modus

Hier ist ein Beispiel für das Starten des Protokollwiedergabediensts im Modus „Kontinuierlich“ mit PowerShell angegeben:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Hier ist ein Beispiel für das Starten des Protokollwiedergabediensts im Modus „Kontinuierlich“ mit der Azure CLI angegeben:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

PowerShell- und CLI-Clients zum Starten von LRS im kontinuierlichen Modus sind synchron. Dies bedeutet, dass Clients warten, bis die API-Antwort einen Erfolg oder Fehler beim Starten des Auftrags gemeldet hat. 

Während dieser Wartezeit wird die Kontrolle vom Befehl nicht zurück an die Eingabeaufforderung gegeben. Wenn Sie ein Skript für die Migration erstellen und es erforderlich ist, dass die Kontrolle vom Startbefehl des Protokollwiedergabediensts sofort wieder zurückgegeben wird, damit der Skriptablauf fortgesetzt werden kann, können Sie PowerShell mit dem Schalter `-AsJob` als Hintergrundauftrag ausführen. Beispiel:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Wenn Sie einen Hintergrundauftrag starten, wird selbst dann sofort ein Auftragsobjekt zurückgegeben, wenn der Abschluss des Auftrags längere Zeit in Anspruch nimmt. Sie können ohne Unterbrechung in der Sitzung weiterarbeiten, während der Auftrag abgeschlossen wird. Ausführliche Informationen zum Ausführen von PowerShell als Hintergrundauftrag finden Sie in der PowerShell-Dokumentation zu [Start-Job](/powershell/module/microsoft.powershell.core/start-job#description).

Wenn Sie einen Azure CLI-Befehl unter Linux als Hintergrundprozess starten, verwenden Sie auf ähnliche Weise das kaufmännische Und-Zeichen (`&`) am Ende des Startbefehls für den Protokollwiedergabedienst:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Nachdem Sie den Protokollwiedergabedienst gestartet haben, werden alle systemseitig verwalteten Softwarepatches für einen Zeitraum von 36 Stunden ausgesetzt. Nach Ablauf dieses Zeitraums wird der Protokollwiedergabedienst mit dem nächsten automatisierten Softwarepatch automatisch beendet. In diesem Fall können Sie die Migration nicht fortsetzen und müssen den Vorgang ganz neu starten. 

## <a name="monitor-the-migration-progress"></a>Überwachen des Migrationsfortschritts

Verwenden Sie den folgenden Befehl, um den Fortschritt der Migration mit PowerShell zu überwachen:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Verwenden Sie den folgenden Befehl, um den Fortschritt der Migration mit der Azure CLI zu überwachen:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Stoppen der Migration

Verwenden Sie die folgenden Cmdlets, falls Sie die Migration beenden müssen. Durch das Beenden der Migration wird die wiederherzustellende Datenbank unter SQL Managed Instance gelöscht, sodass die Migration später nicht mehr fortgesetzt werden kann.

Verwenden Sie den folgenden Befehl, um den Migrationsprozess mit PowerShell zu beenden:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Verwenden Sie den folgenden Befehl, um den Migrationsprozess mit der Azure CLI zu beenden:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Abschließen der Migration (kontinuierlicher Modus)

Gehen Sie wie folgt vor, falls Sie den Protokollwiedergabedienst im Modus „Kontinuierlich“ gestartet haben: Vergewissern Sie sich zunächst, dass alle Sicherungen wiederhergestellt wurden, und initiieren Sie anschließend die Übernahme, um die Migration abzuschließen. Nach der Übernahme wird die Datenbank migriert und ist für den Lese- und Schreibzugriff bereit.

Verwenden Sie den folgenden Befehl, um den Migrationsprozess im Modus „Kontinuierlich“ des Protokollwiedergabediensts mit PowerShell abzuschließen:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Verwenden Sie den folgenden Befehl, um den Migrationsprozess im Modus „Kontinuierlich“ des Protokollwiedergabediensts mit der Azure CLI abzuschließen:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

### <a name="migration-of-multiple-databases"></a>Migration mehrerer Datenbanken
Sie müssen Sicherungsdateien für verschiedene Datenbanken in separaten Ordnern innerhalb des Azure Blob Storage-Containers speichern. Alle Sicherungsdateien für eine einzelne Datenbank müssen sich im selben Ordner befinden, da für eine einzelne Datenbank keine Unterordner vorhanden sein dürfen. Der Protokollwiedergabedienst muss für jede Datenbank separat gestartet werden, die auf den vollständigen URI-Pfad des Azure Blob Storage-Containers und den jeweiligen Datenbankordner verweist.

Nachstehend finden Sie ein Beispiel für die Ordnerstruktur und die URI-Spezifikation, die beim Aufrufen des Protokollwiedergabediensts für mehrere Datenbanken erforderlich sind. Starten Sie den Protokollwiedergabedienst separat für jede Datenbank, und geben Sie dabei den vollständigen URI-Pfad zum Azure Blob Storage-Container und den jeweiligen Datenbankordner an.

```URI
-- Place all backup files for database 1 in its own separate folder within a storage container. No further subfolders are allowed under database1 folder for this database.
https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/database1/<all database 1 backup files>

-- Place all backup files for database 2 in its own separate folder within a storage container. No further subfolders are allowed under database2 folder for this database.
https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/database2/<all database 2 backup files>

-- Place all backup files for database 2 in its own separate folder within a storage container. No further subfolders are allowed under database3 folder for this database.
https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/database3/<all database 3 backup files>
```

## <a name="functional-limitations"></a>Funktionale Beschränkungen

Für den Protokollwiedergabedienst gelten die folgenden funktionalen Beschränkungen:
- Die Datenbank, die Sie wiederherstellen, kann während des Migrationsprozesses nicht für den schreibgeschützten Zugriff verwendet werden.
- Nachdem Sie den Protokollwiedergabedienst gestartet haben, werden systemseitig verwaltete Softwarepatches für einen Zeitraum von 36 Stunden blockiert. Nach Ablauf dieses Zeitfensters wird der Protokollwiedergabedienst mit dem nächsten Softwareupdate beendet. Anschließend müssen Sie den Protokollwiedergabedienst ganz neu starten.
- Für den Protokollwiedergabedienst müssen Datenbanken unter SQL Server mit aktivierter Option `CHECKSUM` gesichert werden.
- Das SAS-Token, das vom Protokollwiedergabedienst verwendet wird, muss für den gesamten Azure Blob Storage-Container generiert werden und darf nur über die Berechtigungen „Lesen“ und „Auflisten“ verfügen.
- Sicherungsdateien für verschiedene Datenbanken müssen in Blob Storage in separaten Ordnern abgelegt werden.
- Sicherungsdateien, die die Zeichen % und $ im Dateinamen enthalten, können von LRS nicht verarbeitet werden. Erwägen Sie, solche Dateinamen umzubenennen.
- Das Speichern von Sicherungen in Unterordnern für eine einzelne Datenbank wird nicht unterstützt. Alle Sicherungen für eine einzelne Datenbank müssen im Stammverzeichnis eines einzigen Ordners gespeichert werden.
- Bei Verwendung mehrerer Datenbanken müssen die Sicherungsdateien für jede Datenbank in einem separaten Ordner gespeichert werden. Der Protokollwiedergabedienst muss für jede Datenbank separat gestartet werden, die auf den vollständigen URI-Pfad mit einem einzelnen Datenbankordner verweist. 
- Für den Protokollwiedergabedienst können bis zu 100 gleichzeitige Wiederherstellungsprozesse pro verwalteter Instanz unterstützt werden.

## <a name="troubleshooting"></a>Problembehandlung

Verwenden Sie nach dem Starten des Protokollwiedergabediensts das Cmdlet für die Überwachung (`get-azsqlinstancedatabaselogreplay` oder `az_sql_midb_log_replay_show`), um den Status des Vorgangs anzuzeigen. Falls der Protokollwiedergabedienst nicht gestartet werden kann und ein Fehler angezeigt wird, sollten Sie eine Überprüfung auf die häufigsten Fehler durchführen:

- Verfügt eine vorhandene Datenbank in SQL Managed Instance über den gleichen Namen wie die Datenbank, die aus SQL Server migriert werden soll? Lösen Sie diesen Konflikt auf, indem Sie eine der Datenbanken umbenennen.
- Wurde für die Erstellung der Datenbanksicherung unter SQL Server die Option `CHECKSUM` verwendet?
- Verfügt das SAS-Token ausschließlich über die Berechtigungen „Lesen“ und „Auflisten“ für den Protokollwiedergabedienst?
- Haben Sie als SAS-Token für den Protokollwiedergabedienst die Zeichenfolge nach dem Fragezeichen (`?`) kopiert, die mit `sv=2020-02-10...` beginnt? 
- Stimmt die Gültigkeitsdauer des SAS-Tokens mit dem Zeitfenster für Starten und Abschließen der Migration überein? Aufgrund der unterschiedlichen Zeitzonen, die für SQL Managed Instance und das SAS-Token verwendet werden, kann es ggf. zu Konflikten kommen. Versuchen Sie, das SAS-Token erneut zu generieren und das Gültigkeitsdauer-Zeitfenster des Tokens vor und nach dem aktuellen Datum zu vergrößern.
- Sind der Datenbankname, der Ressourcengruppenname und der Name der verwalteten Instanz richtig geschrieben?
- Wenn Sie den Protokollwiedergabedienst im Modus „AutoVervollständigen“ gestartet haben: Wurde für die letzte Sicherungsdatei ein gültiger Dateiname angegeben?

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Migrieren von SQL Server zu SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Informieren Sie sich über die [Unterschiede zwischen SQL Server und SQL Managed Instance](transact-sql-tsql-differences-sql-server.md).
- Erfahren Sie mehr über [Bewährte Methoden für Kostenermittlung und Größenanpassung von zu Azure migrierten Workloads](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
