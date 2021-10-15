---
title: Wiederherstellen von Azure Database for PostgreSQL
description: Weitere Informationen zum Wiederherstellen von Azure Database for PostgreSQL-Sicherungen.
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5de8e74fb05eea45e5cb730515b3280c4952951
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359501"
---
# <a name="restore-azure-database-for-postgresql-backups-preview"></a>Wiederherstellen von Azure Database for PostgreSQL-Sicherungen (Vorschau)

In diesem Artikel wird erläutert, wie Sie eine von Azure Backup gesicherte Datenbank auf einem Azure PostgreSQL-Server wiederherstellen.

Sie können eine Datenbank auf jedem beliebigen Azure PostgreSQL-Server innerhalb desselben Abonnements wiederherstellen, wenn der Dienst über die [entsprechenden Berechtigungen](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server) auf dem Zielserver verfügt.

## <a name="restore-a-database-on-the-target-storage-account"></a>Wiederherstellen einer Datenbank im Zielspeicherkonto


1. Wechseln Sie zu **Azure Backup-Tresor** -> **Sicherungsinstanzen**. Wählen Sie die Datenbank aus, und klicken Sie auf **Wiederherstellen**.

   :::image type="content" source="./media/restore-azure-database-postgresql/select-database-for-restore-inline.png" alt-text="Screenshot: Prozess zum Auswählen und Wiederherstellen einer Datenbank." lightbox="./media/restore-azure-database-postgresql/select-database-for-restore-expanded.png":::

   Alternativ können Sie über das [Backup Center](/azure/backup/backup-center-overview) zu dieser Seite navigieren.   
  
1. Wählen Sie auf der Seite **Wiederherstellungspunkt auswählen** aus der Liste aller vollständigen Sicherungen, die für die ausgewählte Sicherungsinstanz verfügbar sind, einen Wiederherstellungspunkt aus. Standardmäßig ist der letzte Wiederherstellungspunkt ausgewählt.

   :::image type="content" source="./media/restore-azure-database-postgresql/select-restore-point-inline.png" alt-text="Screenshot: Prozess zum Auswählen eines Wiederherstellungspunkts." lightbox="./media/restore-azure-database-postgresql/select-restore-point-expanded.png":::

   Wenn sich der Wiederherstellungspunkt auf der Archivebene befindet, müssen Sie den Wiederherstellungspunkt vor der Wiederherstellung erneut aktivieren. Sie müssen folgende Zusatzparameter für die Aktivierung angeben:

   - **Aktivierungspriorität**: Der Standardwert ist **Standard.**
   - **Aktivierungsdauer**: Die maximale Aktivierungsdauer beträgt 30 Tage, und die minimale Aktivierungsdauer beträgt 10 Tage. Der Standardwert ist **15 Tage**. Der Wiederherstellungspunkt wird für diesen Zeitraum im **Sicherungsdatenspeicher** gespeichert.

1. Wählen Sie auf der Seite **Wiederherstellungsparameter** einen der folgenden Wiederherstellungstypen aus: **Als Datenbank wiederherstellen** oder **Als Dateien wiederherstellen**.

   - **Als Datenbank wiederherstellen**

     Der Zielserver darf mit dem Quellserver identisch sein. Das Überschreiben der ursprünglichen Datenbank wird jedoch nicht unterstützt. Sie können aus dem Server aus allen Abonnements, aber in derselben Region wie der Tresor auswählen.

     1. Wählen Sie in der Dropdownliste **Schlüsseltresor und Geheimnis auswählen** einen Tresor aus, in dem die Anmeldeinformationen für die Verbindung mit dem Zielserver gespeichert sind.

     1. Wählen Sie **Überprüfen + wiederherstellen** aus. Dadurch wird überprüft, ob der Dienst über die [Berechtigungen zur Wiederherstellung auf dem Zielserver](backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore) verfügt. Diese Berechtigungen müssen [manuell erteilt werden](backup-azure-database-postgresql-overview.md#grant-access-on-the-azure-postgresql-server-and-key-vault-manually).

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-database-inline.png" alt-text="Screenshot: ausgewählter Wiederherstellungstyps „Als Datenbank wiederherstellen“." lightbox="./media/restore-azure-database-postgresql/restore-as-database-expanded.png":::

   - **Als Dateien wiederherstellen: Sichern Sie die Sicherungsdateien im Zielspeicherkonto (Blobs).**

     Sie können aus den Speicherkonten aller Abonnements auswählen, die sich in derselben Region wie der Tresor befinden.     

     1. Wählen Sie in der Dropdownliste **Zielcontainer auswählen** einen der Container aus, die für das ausgewählte Speicherkonto gefiltert wurde.
     1. Wählen Sie **Überprüfen + wiederherstellen** aus, um eine Überprüfung auszulösen und zu überprüfen, ob der Sicherungsdienst über die [Wiederherstellungsberechtigungen für das Zielspeicherkonto](#restore-permissions-on-the-target-storage-account) verfügt.

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-files-inline.png" alt-text="Screenshot: ausgewählter Wiederherstellungstyps „Als Dateien wiederherstellen“." lightbox="./media/restore-azure-database-postgresql/restore-as-files-expanded.png":::
   
1. Übermitteln Sie den Wiederherstellungsvorgang, und verfolgen Sie den ausgelösten Auftrag unter **Sicherungsaufträge** nach.
   
   :::image type="content" source="./media/restore-azure-database-postgresql/track-triggered-job-inline.png" alt-text="Screenshot: nachverfolgter ausgelöster Auftrag unter „Sicherungsaufträge“." lightbox="./media/restore-azure-database-postgresql/track-triggered-job-expanded.png":::

>[!NOTE]
>Die Archivunterstützung für Azure Database for PostgreSQL ist als eingeschränkte öffentliche Vorschauversion verfügbar.

## <a name="restore-permissions-on-the-target-storage-account"></a>Wiederherstellungsberechtigungen für das Zielspeicherkonto

Erteilen Sie der MSI des Azure Backup-Tresors die Berechtigung für den Zugriff auf die Speicherkontocontainer über das Azure-Portal.

1. Wechseln Sie zu **Speicherkonto** -> **Zugriffssteuerung** -> **Rollenzuweisung hinzufügen**.

1. Wählen Sie die Rolle **Mitwirkender an Speicherblobdaten** in der Dropdownliste **Rolle** für die MSI des Azure Backup-Tresors aus.

   :::image type="content" source="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-inline.png" alt-text="Screenshot: Prozess der Zuweisung der Berechtigung für den Zugriff auf die Speicherkontocontainer über das Azure-Portal an die MSI des Azure Backup-Tresors." lightbox="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-expanded.png":::

Außerdem können Sie mit dem Azure CLI-Befehl [az role assignment create](/cli/azure/role/assignment) differenzierte Berechtigungen für den bestimmten Container erteilen, auf dem Sie die Wiederherstellung durchführen.

```azurecli
az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
```
Ersetzen Sie den Parameter für die zugewiesene Person durch die _Anwendungs-ID_ der MSI-Datei des Tresors und den Umfangsparameter, um auf Ihren spezifischen Container zu verweisen. Um die **Anwendungs-ID** der Tresor-MSI zu erhalten, wählen Sie unter **Anwendungstyp** die Option **Alle Anwendungen** aus. Suchen Sie nach dem Tresornamen, und kopieren Sie die Anwendungs-ID.

 :::image type="content" source="./media/restore-azure-database-postgresql/select-application-type-for-id-inline.png" alt-text="Screenshot: Prozess zum Abrufen der Anwendungs-ID der Tresor-MSI." lightbox="./media/restore-azure-database-postgresql/select-application-type-for-id-expanded.png":::

 :::image type="content" source="./media/restore-azure-database-postgresql/copy-vault-id-inline.png" alt-text="Screenshot: Prozess zum Kopieren der Anwendungs-ID des Tresors." lightbox="./media/restore-azure-database-postgresql/copy-vault-id-expanded.png":::
 
## <a name="next-steps"></a>Nächste Schritte

[Problembehandlung für die PostgreSQL -Datenbanksicherung mit Azure Backup](backup-azure-database-postgresql-troubleshoot.md)
