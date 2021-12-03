---
title: Sichern von Azure Database for PostgreSQL
description: Weitere Informationen zur Azure Database for PostgreSQL-Sicherung mit Langzeitaufbewahrung (Vorschau)
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 4121326bf36c71c3f98894bd8413dc1ae5f57a06
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458923"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL-Sicherung mit Langzeitaufbewahrung (Vorschau)

In diesem Artikel wird beschrieben, wie Sie einen Azure Database for PostgreSQL-Server sichern.

## <a name="configure-backup-on-azure-postgresql-databases"></a>Konfigurieren der Sicherung für Azure PostgreSQL-Datenbanken

Sie können die Sicherung für mehrere Datenbanken auf mehreren Azure PostgreSQL-Servern konfigurieren. Führen Sie zum Konfigurieren der Sicherung für die Azure PostgreSQL-Datenbanken mit Azure Backup die folgenden Schritte aus:

1. Rufen Sie **Sicherungstresor** ->  **+Sicherung** auf.

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-inline.png" alt-text="Screenshot: Option zum Hinzufügen einer Sicherung" lightbox="./media/backup-azure-database-postgresql/adding-backup-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-details-inline.png" alt-text="Screenshot: Option zum Hinzufügen von Sicherungsinformationen" lightbox="./media/backup-azure-database-postgresql/adding-backup-details-expanded.png":::

   Alternativ können Sie über das [Backup Center](./backup-center-overview.md) zu dieser Seite navigieren. 

1. Wählen bzw. [erstellen Sie eine Sicherungsrichtlinie](#create-backup-policy), die den Sicherungszeitplan und die Aufbewahrungsdauer definiert.

   :::image type="content" source="./media/backup-azure-database-postgresql/create-or-add-backup-policy-inline.png" alt-text="Screenshot: Option zum Hinzufügen einer Sicherungsrichtlinie" lightbox="./media/backup-azure-database-postgresql/create-or-add-backup-policy-expanded.png":::

1. **Auswählen der zu sichernden Azure Postgres-Datenbanken**: Wählen Sie einen der Azure PostgreSQL-Server abonnementübergreifend aus, wenn sie sich in derselben Region wie der Tresor befinden. Klappen Sie die Liste der Datenbanken innerhalb eines Servers auf, indem Sie auf den Pfeil klicken.

   >[!Note]
   >Sie können (und müssen) die Datenbanken nicht *azure_maintenance* und *azure_sys* sichern. Darüber hinaus können Sie eine Datenbank, die bereits in einem Sicherungstresor gesichert ist, nicht sichern.

   :::image type="content" source="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-inline.png" alt-text="Screenshot: Option zum Auswählen einer Azure PostgreSQL-Datenbank" lightbox="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-inline.png" alt-text="Screenshot: Auswählen eines Azure PostgreSQL-Servers" lightbox="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-expanded.png":::


1. **Weisen Sie einen Azure-Schlüsseltresor zu**, in dem die Anmeldeinformationen gespeichert werden, um eine Verbindung mit der ausgewählten Datenbank herzustellen. Klicken Sie auf **Schlüsseltresor und Geheimnis auswählen**, um den Schlüsseltresor einzeln auf Zeilenebene zuzuweisen. Sie können den Schlüsseltresor auch zuweisen, indem Sie die Zeilen mehrfach auswählen und im oberen Menü des Rasters auf „Schlüsseltresor zuweisen“ klicken. 

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-azure-key-vault-inline.png" alt-text="Screenshot: Zuweisen eines Azure-Schlüsseltresors" lightbox="./media/backup-azure-database-postgresql/assign-azure-key-vault-expanded.png"::: 

1. Verwenden Sie eine der folgenden Optionen, um die Geheimnisinformationen anzugeben: 

   1. **Geheimnis-URI eingeben**: Verwenden Sie diese Option, wenn der Geheimnis-URI für Sie freigegeben/bekannt ist. Sie können den **Geheimnis-URI aus dem Schlüsseltresor kopieren:**  -> **Geheimnis (Geheimnis auswählen)**  -> **Geheimnisbezeichner**.

      :::image type="content" source="./media/backup-azure-database-postgresql/enter-secret-uri-inline.png" alt-text="Screenshot: Eingabe des Geheimnis-URI" lightbox="./media/backup-azure-database-postgresql/enter-secret-uri-expanded.png":::  

      Mit dieser Option erhält Azure Backup jedoch keine Informationen zum Schlüsseltresor, auf den Sie verwiesen haben. Daher können die Zugriffsberechtigungen für den Schlüsseltresor nicht inline zugewiesen werden. Der Sicherungsadministrator muss zusammen mit dem Postgres- und/oder Schlüsseltresoradministrator sicherstellen, dass der Zugriff auf den Schlüsseltresor [manuell außerhalb des Sicherungskonfigurationsablaufs gewährt wird](backup-azure-database-postgresql-overview.md#access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server), damit der Sicherungsvorgang erfolgreich ist.

   1. **Schlüsseltresor auswählen**: Verwenden Sie diese Option, wenn Sie den Schlüsseltresor und den Geheimnisnamen kennen. Mit dieser Option können Sie (Sicherungsadministrator mit Schreibzugriff auf den Schlüsseltresor) die Zugriffsberechtigungen für den Schlüsseltresor inline zuweisen. Der Schlüsseltresor und das Geheimnis können bereits vorhanden sein oder neu erstellt werden. Vergewissern Sie sich, dass es sich beim Geheimnis um die Verbindungszeichenfolge des PG-Servers im ADO.net-Format handelt, die mit den Anmeldeinformationen des Datenbankbenutzers aktualisiert wurde, der auf dem Server die Sicherungsberechtigung erhalten hat. Erfahren Sie mehr über das Erstellen der [Geheimnisse im Schlüsseltresor](#create-secrets-in-the-key-vault).

      :::image type="content" source="./media/backup-azure-database-postgresql/assign-secret-store-inline.png" alt-text="Screenshot: Zuweisen eines Geheimnisspeichers" lightbox="./media/backup-azure-database-postgresql/assign-secret-store-expanded.png":::

      :::image type="content" source="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-inline.png" alt-text="Screenshot: Auswahl des Geheimnisses in Azure Key Vault" lightbox="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-expanded.png":::   

1. Wenn die Aktualisierung der Geheimnisinformationen abgeschlossen ist, beginnt die Überprüfung, nachdem die Schlüsseltresorinformationen aktualisiert wurden. Hier überprüft der Sicherungsdienst, ob er über alle erforderlichen [Zugriffsberechtigungen](backup-azure-database-postgresql-overview.md#key-vault-based-authentication-model)() verfügt, um Geheimnisdetails aus dem Schlüsseltresor zu lesen und eine Verbindung mit der Datenbank herzustellen. Wenn eine oder mehrere Zugriffsberechtigungen fehlen, wird eine der folgenden Fehlermeldungen angezeigt: _„Rollenzuweisung nicht durchgeführt“ oder „Benutzer kann keine Rollen zuweisen“_ .

   :::image type="content" source="./media/backup-azure-database-postgresql/validation-of-secret-inline.png" alt-text="Screenshot: Validierung des Geheimnisses" lightbox="./media/backup-azure-database-postgresql/validation-of-secret-expanded.png":::   

   1. **Benutzer kann keine Rollen zuweisen**: Diese Meldung wird angezeigt, wenn Sie (der Sicherungsadministrator) keinen Schreibzugriff auf den PostgreSQL-Server und/oder den Schlüsseltresor haben, um fehlende Berechtigungen zuzuweisen, wie unter **Details anzeigen** aufgeführt. Laden Sie die Zuweisungsvorlage über die Aktionsschaltfläche herunter, und lassen Sie sie vom PostgreSQL- und/oder Schlüsseltresoradministrator ausführen. Es handelt sich um eine ARM-Vorlage, die Ihnen hilft, die notwendigen Berechtigungen für die erforderlichen Ressourcen zuzuweisen. Nachdem die Vorlage erfolgreich ausgeführt wurde, klicken Sie auf der Seite „Sicherung konfigurieren“ auf **Erneut überprüfen**.

      :::image type="content" source="./media/backup-azure-database-postgresql/download-role-assignment-template-inline.png" alt-text="Screenshot: Option zum Herunterladen der Vorlage für die Rollenzuweisung" lightbox="./media/backup-azure-database-postgresql/download-role-assignment-template-expanded.png":::    

   1. **Rollenzuweisung nicht durchgeführt**: Diese Meldung wird angezeigt, wenn Sie (der Sicherungsadministrator) Schreibzugriff auf den PostgreSQL-Server und/oder den Schlüsseltresor haben, um fehlende Berechtigungen zuzuweisen, wie unter **Details anzeigen** aufgeführt. Verwenden Sie die Aktionsschaltfläche **Fehlende Rollen zuweisen** im oberen Aktionsmenü, um Berechtigungen für den PostgreSQL-Server und/oder den Schlüsseltresor inline zuzuweisen.

      :::image type="content" source="./media/backup-azure-database-postgresql/role-assignment-not-done-inline.png" alt-text="Screenshot: Fehler bei nicht erfolgter Rollenzuweisung" lightbox="./media/backup-azure-database-postgresql/role-assignment-not-done-expanded.png":::     

1. Wählen Sie im oberen Menü die Option **Fehlende Rollen zuweisen** aus, und weisen Sie Rollen zu. Sobald der Prozess startet, werden die [fehlenden Zugriffsberechtigungen](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server) auf dem KV- und/oder PG-Server für den Sicherungstresor gewährt. Sie können den Bereich definieren, in dem die Zugriffsberechtigungen erteilt werden sollen. Nach Abschluss der Aktion wird die erneute Überprüfung gestartet.

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-missing-roles-inline.png" alt-text="Screenshot: Option zum Zuweisen fehlender Rollen" lightbox="./media/backup-azure-database-postgresql/assign-missing-roles-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/define-scope-of-access-permission-inline.png" alt-text="Screenshot: Definieren des Umfangs der Zugriffsberechtigung" lightbox="./media/backup-azure-database-postgresql/define-scope-of-access-permission-expanded.png":::     

   - Der Sicherungstresor verwendet Geheimnisse aus dem Schlüsseltresor und führt eine Testverbindung mit der Datenbank aus, um zu überprüfen, ob die Anmeldeinformationen richtig eingegeben wurden. Die Berechtigungen des Datenbankbenutzers werden ebenfalls daraufhin überprüft, [ob der Datenbankbenutzer über sicherungsbezogene Berechtigungen für die Datenbank verfügt](backup-azure-database-postgresql-overview.md#database-users-backup-privileges-on-the-database).

   - Der PostgreSQL-Administrator verfügt standardmäßig über alle Sicherungs- und Wiederherstellungsberechtigungen für die Datenbank. Daher wären Überprüfungen erfolgreich.
   - Ein Benutzer mit geringen Berechtigungen verfügt möglicherweise nicht über Sicherungs-/Wiederherstellungsberechtigungen für die Datenbank. Daher würden die Überprüfungen fehlschlagen. Ein PowerShell-Skript wird dynamisch generiert (eines pro Datensatz/ausgewählter Datenbank). [Führen Sie das PowerShell-Skript aus, um dem Datenbankbenutzer diese Berechtigungen für die Datenbank zuzuweisen](#create-secrets-in-the-key-vault). Alternativ können Sie diese Berechtigungen auch mithilfe des PG-Administrators oder des PSQL-Tools zuweisen.

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-inline.png" alt-text="Screenshot: Zugriff auf Geheimnisse im Schlüsseltresor durch den Sicherungstresor" lightbox="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/run-test-connection.png" alt-text="Screenshot: Prozess zum Starten der Testverbindung":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-inline.png" alt-text="Screenshot: Angeben von Benutzeranmeldeinformationen zum Ausführen des Tests" lightbox="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-expanded.png":::      

1. Behalten Sie die Datensätze mit Status „Erfolg“ für die Sicherungsbereitschaft bei, um mit dem letzten Schritt der Übermittlung des Vorgangs fortzufahren.

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-readiness-as-success-inline.png" alt-text="Screenshot: Sicherungsbereitschaft erfolgreich" lightbox="./media/backup-azure-database-postgresql/backup-readiness-as-success-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/review-backup-configuration-details-inline.png" alt-text="Screenshot: Seite zur Überprüfung der Sicherungskonfiguration" lightbox="./media/backup-azure-database-postgresql/review-backup-configuration-details-expanded.png":::      

1. Übermitteln Sie den Vorgang zur Sicherungskonfiguration, und verfolgen Sie den Fortschritt unter **Sicherungsinstanzen** nach.

   :::image type="content" source="./media/backup-azure-database-postgresql/submit-configure-backup-operation-inline.png" alt-text="Screenshot: Übermittlung der Sicherungskonfiguration und Nachverfolgung des Fortschritts" lightbox="./media/backup-azure-database-postgresql/submit-configure-backup-operation-expanded.png":::      

## <a name="create-backup-policy"></a>Sicherungsrichtlinie erstellen

Sie können während des Ablaufs zur Sicherungskonfiguration eine Sicherungsrichtlinie erstellen. Wechseln Sie alternativ zu **Backup Center** -> **Sicherungsrichtlinien** -> **Hinzufügen**.

1. Geben Sie unter Name einen Namen für die neue Richtlinie ein.

   :::image type="content" source="./media/backup-azure-database-postgresql/enter-name-for-new-policy-inline.png" alt-text="Screenshot: Prozess zum Eingeben eines Namens für die neue Richtlinie" lightbox="./media/backup-azure-database-postgresql/enter-name-for-new-policy-expanded.png":::

1. Legen Sie den Sicherungszeitplan fest. Derzeit ist nur die Option zur wöchentlichen Sicherung verfügbar. Sie können die Sicherungen jedoch an mehreren Wochentagen planen.

1. Legen Sie die Einstellungen für die **Aufbewahrung** fest. Sie können eine oder mehrere Aufbewahrungsregeln hinzufügen. Jede Aufbewahrungsregel nimmt Eingaben für bestimmte Sicherungen und den Datenspeicher sowie die Aufbewahrungsdauer für diese Sicherungen an.

1. Um Ihre Sicherungen in einem der beiden Datenspeicher (oder Ebenen) zu speichern, wählen Sie **Sicherungsdatenspeicher** (Standardebene) oder **Archivdatenspeicher** (in der Vorschau) aus.

1. Wählen Sie **Ablauf** aus, wenn Sie die Sicherung nach Ablauf im Sicherungsdatenspeicher in den Archivdatenspeicher verschieben möchten.

   Die **Standardaufbewahrungsregel** wird angewandt, wenn keine andere Aufbewahrungsregel vorhanden ist. Sie hat einen Standardwert von drei Monaten.

   - Die Aufbewahrungsdauer reicht im **Sicherungsdatenspeicher** von sieben Tagen bis zu zehn Jahren.
   - Die Aufbewahrungsdauer reicht im **Archivdatenspeicher** von sechs Monaten bis zu zehn Jahren.

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-inline.png" alt-text="Screenshot: „Nach Ablauf“ wählen, um die Sicherung nach Ablauf in den Archivdatenspeicher zu verschieben." lightbox="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-expanded.png":::

>[!Note]
>Die Aufbewahrungsregeln werden in einer vordefinierten Reihenfolge ausgewertet. Die oberste Priorität hat die Regel „Jährlich“, gefolgt von der Regel „Monatlich“ und dann von der Regel „Wöchentlich“. Die Standardeinstellungen für die Aufbewahrung werden angewendet, wenn keine anderen Regeln zutreffen. Beispielsweise kann derselbe Wiederherstellungspunkt sowohl die erste erfolgreiche Sicherung, die jede Woche erstellt wird, als auch die erste erfolgreiche Sicherung, die jeden Monat erstellt wird, sein. Da die Priorität der monatlichen Regel jedoch höher als die der wöchentlichen Regel ist, gilt die Aufbewahrungsregel, die der ersten erfolgreichen, monatlich erstellten Sicherung entspricht.
## <a name="create-secrets-in-the-key-vault"></a>Erstellen von Geheimnissen im Schlüsseltresor

Das Geheimnis ist die Verbindungszeichenfolge des PG-Servers im _ADO.net_-Format, die mit den Anmeldeinformationen des Datenbankbenutzers aktualisiert wurde, der auf dem Server die **Sicherungsberechtigungen** erhalten hat. Kopieren Sie die Verbindungszeichenfolge vom PG-Server, und bearbeiten Sie sie in einem Text-Editor, um die _Benutzer-ID und das Kennwort_ zu aktualisieren. 

:::image type="content" source="./media/backup-azure-database-postgresql/pg-server-connection-string-inline.png" alt-text="Screenshot: Verbindungszeichenfolge des PG-Servers als Geheimnis" lightbox="./media/backup-azure-database-postgresql/pg-server-connection-string-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/create-secret-inline.png" alt-text="Screenshot: Option zum Erstellen einer Verbindungszeichenfolge des PG-Servers als Geheimnis" lightbox="./media/backup-azure-database-postgresql/create-secret-expanded.png":::

## <a name="run-powershell-script-to-grant-privileges-to-database-users"></a>Ausführen eines PowerShell-Skripts zum Zuweisen von Berechtigungen für Datenbankbenutzer

Das während der Sicherungskonfigurierung dynamisch generierte PowerShell-Skript akzeptiert den Datenbankbenutzer als Eingabe zusammen mit den PG-Administratoranmeldeinformationen, um dem Datenbankbenutzer sicherungsbezogene Berechtigungen für die Datenbank zu gewähren.

Das [PSQL-Tool](https://www.enterprisedb.com/download-postgresql-binaries) muss auf dem Computer vorhanden sein, und die PATH-Umgebungsvariable muss entsprechend auf den Pfad der PSQL-Tools festgelegt sein.

:::image type="content" source="./media/backup-azure-database-postgresql/psql-set-environment-inline.png" alt-text="Screenshot: Option zum Durchsuchen der Anwendung für Umgebungseinstellungen" lightbox="./media/backup-azure-database-postgresql/psql-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/system-properties-to-set-environment-inline.png" alt-text="Screenshot: Option zum Festlegen der Umgebung in den Systemeigenschaften" lightbox="./media/backup-azure-database-postgresql/system-properties-to-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/adding-environment-variables-inline.png" alt-text="Screenshot: Standardumgebungsvariablen" lightbox="./media/backup-azure-database-postgresql/adding-environment-variables-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/editing-environment-variables-inline.png" alt-text="Screenshot: Umgebungsvariablen, die festgelegt werden müssen" lightbox="./media/backup-azure-database-postgresql/editing-environment-variables-expanded.png":::

Stellen Sie sicher, dass die **Einstellungen für Verbindungssicherheit** in der Azure PostgreSQL-Instanz die IP-Adresse des Computers zulassen, um Netzwerkkonnektivität zuzulassen.

## <a name="generate-an-on-demand-backup"></a>Generieren einer bedarfsgesteuerten Sicherung

Um eine Sicherung ohne den in der Richtlinie angegebenen Zeitplan zu starten, wechseln Sie zu **Sicherungsinstanzen** -> **Jetzt sichern**.
Wählen Sie Aufbewahrungsregeln in der Liste aus, die in der zugehörigen Sicherungsrichtlinie definiert wurden.

:::image type="content" source="./media/backup-azure-database-postgresql/navigate-to-retention-rules-inline.png" alt-text="Screenshot: Option zum Navigieren zur Liste der Aufbewahrungsregeln, die in der zugeordneten Sicherungsrichtlinie definiert wurden" lightbox="./media/backup-azure-database-postgresql/navigate-to-retention-rules-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/choose-retention-rules-inline.png" alt-text="Screenshot: Option zum Auswählen von Aufbewahrungsregeln, die in der zugeordneten Sicherungsrichtlinie definiert wurden" lightbox="./media/backup-azure-database-postgresql/choose-retention-rules-expanded.png":::

## <a name="next-steps"></a>Nächste Schritte

[Problembehandlung für die PostgreSQL -Datenbanksicherung mit Azure Backup](backup-azure-database-postgresql-troubleshoot.md)