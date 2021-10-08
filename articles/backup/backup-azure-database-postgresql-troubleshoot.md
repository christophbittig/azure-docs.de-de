---
title: Behandeln von Problemen mit der Sicherung von Azure Database for PostgreSQL
description: Informationen zur Problembehandlung beim Sichern der Azure Database for PostgreSQL.
ms.topic: troubleshooting
ms.date: 09/22/2021
ms.openlocfilehash: 0dcb5b3a85fd1364d90327648274194d3bc30364
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700180"
---
# <a name="troubleshoot-postgresql-database-backup-by-using-azure-backup-preview"></a>Problembehandlung für die PostgreSQL -Datenbanksicherung mithilfe von Azure Backup (Vorschau)

Dieser Abschnitt enthält Informationen zur Problembehandlung beim Sichern von Azure PostgreSQL-Datenbanken mit Azure Backup.

## <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Sie müssen Backup Vault MSI **Lesezugriff** für den PG-Server einrichten, den Sie sichern oder wiederherstellen möchten.

Zum Herstellen einer sicheren Verbindung mit der PostgreSQL-Datenbank verwendet Azure Backup das Authentifizierungsmodell [Verwaltete Dienstidentität (MSI)](../active-directory/managed-identities-azure-resources/overview.md). Dies bedeutet, dass der Sicherungstresor nur auf die Ressourcen zugreifen kann, denen der Benutzer explizit eine Berechtigung erteilt hat.

Eine System-MSI wird dem Tresor zum Zeitpunkt der Erstellung automatisch zugewiesen. Sie müssen dieser Tresor-MSI den Zugriff auf die PostgreSQL-Server einräumen, von denen Sie Datenbanken sichern möchten.

Schritte:

1. Wechseln Sie im Postgres-Server zum Bereich **Zugriffssteuerung (IAM)** .

    ![Bereich „Zugriffssteuerung“](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Wählen Sie **Rollenzuweisung hinzufügen** aus.

    ![Screenshot zum Hinzufügen von Rollenzuweisungen.](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. Geben Sie im rechten Kontextbereich, der geöffnet wird, Folgendes ein:<br>

   - **Rolle:** Wählen Sie in der Dropdownliste die Rolle **Leser** aus.<br>
   - **Zugriff zuweisen zu:** Wählen Sie in der Dropdownliste aus den Optionen **Benutzer, Gruppe oder Dienstprinzipal** aus.<br>
   - **Auswählen**: Geben Sie den Namen des Sicherungstresors ein, in dem Sie diesen Server und dessen Datenbanken sichern möchten.<br>

    ![Screenshot: Auswählen einer Rolle.](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

## <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Erstellen Sie einen Benutzer für die Datenbanksicherung, der sich mit Azure Active Directory authentifizieren kann:

Dieser Fehler tritt möglicherweise auf, wenn kein Azure Active Directory-Administrator für den PostgreSQL-Server oder kein Sicherungsbenutzer vorhanden ist, der sich mit Azure Active Directory authentifizieren kann.

Schritte:

Fügen Sie einen Active Directory-Administrator für den OSS-Server hinzu:

Dieser Schritt ist erforderlich, um eine Verbindung mit der Datenbank über einen Benutzer herzustellen, der sich mit Azure Active Directory anstelle eines Kennworts authentifizieren kann. Der Azure AD-Administratorbenutzer in Azure Database for PostgreSQL übt die Rolle **azure_ad_admin** aus. Nur mit der Rolle **azure_ad_admin** können neue Datenbankbenutzer erstellt werden, die sich mit Azure AD authentifizieren können.

1. Wechseln Sie im linken Navigationsbereich der Serveransicht zur Registerkarte „Active Directory-Administrator“, und fügen Sie sich selbst (oder eine andere Person) als Active Directory-Administrator hinzu.

    ![Screenshot: Festlegen des Active Directory-Administrators.](./media/backup-azure-database-postgresql/set-admin.png)

1. Stellen Sie sicher, dass Sie **Speichern** auswählen, um die Benutzereinstellungen zum AD-Administrator zu übernehmen.

    ![Screenshot: Speichern der Active Directory-Einstellung für Administratoren.](./media/backup-azure-database-postgresql/save-admin-setting.png)

Die Liste der erforderlichen Schritte zum Erteilen der Berechtigungen finden Sie in [diesem Dokument](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx).

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Richten Sie die Netzwerkleitung ein, indem Sie in der Serveransicht das Flag **Zugriff auf Azure-Dienste zulassen** aktivieren. Legen Sie in der Serveransicht im Bereich **Verbindungssicherheit** das Flag **Zugriff auf Azure-Dienste zulassen** auf **Ja** fest.

>[!Note]
>Stellen Sie vor dem Aktivieren dieses Flags sicher, dass Sie das Flag **Zugriff auf öffentliches Netzwerk verweigern** auf **Nein** festlegen.

![Screenshot: Zulassen des Zugriffs auf Azure-Dienste.](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

## <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Berechtigung zur Wiederherstellung in einem Speicherkontocontainer bei der Wiederherstellung als Dateien

1. Erteilen Sie der Sicherungstresor-MSI die Berechtigung für den Zugriff auf die Speicherkontocontainer über das Azure-Portal.
    1. Wechseln Sie zu **Speicherkonto** -> **Zugriffssteuerung** -> **Rollenzuweisung hinzufügen**.
    1. Weisen Sie Ihrer Sicherungstresor-MSI zunächst die Rolle **Mitwirkender an Storage-Blobdaten** zu.

    ![Screenshot: Wie man die Rolle „Storage Blob Daten-Mitwirkender“ zuweist.](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Außerdem können Sie mit dem Azure CLI-Befehl [az role assignment create](/cli/azure/role/assignment) differenzierte Berechtigungen für den bestimmten Container erteilen, auf dem Sie die Wiederherstellung durchführen.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Ersetzen Sie den Parameter für die zugewiesene Person durch die **Anwendungs-ID** der MSI-Datei des Tresors und den Umfangsparameter, um auf Ihren spezifischen Container zu verweisen.
    1. Um die **Anwendungs-ID** der Tresor-MSI zu erhalten, wählen Sie unter **Anwendungstyp** die Option **Alle Anwendungen** aus:

        ![Screenshot: Auswählen von "Alle Anwendungen".](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Suchen Sie nach dem Tresornamen, und kopieren Sie die Anwendungs-ID:

        ![Screenshot: Suchen nach Tresornamen.](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Nächste Schritte

[Über Azure Database for PostgreSQL Sicherung (Vorschau)](backup-azure-database-postgresql-overview.md)
