---
title: Sichern einer App
description: Erfahren Sie, wie Sie Sicherungen Ihrer Apps in Azure App Service erstellen. Ausführen manueller oder geplanter Sicherungen. Anpassen von Sicherungen durch Einschließen der angefügten Datenbank.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 09/02/2021
ms.custom: seodec18
ms.openlocfilehash: f1bd37c1b3557a8106981377f9ed75c50a28c773
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433051"
---
# <a name="back-up-your-app-in-azure"></a>Sichern einer App in Azure

Das Feature zum Sichern und Wiederherstellen in [Azure App Service](overview.md) ermöglicht Ihnen, App-Sicherungen einfach manuell oder nach einem Zeitplan zu erstellen. Sie können die Sicherungen so konfigurieren, dass Sie für einen bis zu unbestimmten Zeitraum aufbewahrt werden. Sie können die App mit einer Momentaufnahme eines früheren Zustands wiederherstellen, indem Sie die vorhandene App überschreiben oder als andere App wiederherstellen.

Informationen zum Wiederherstellen einer App aus einer Sicherung finden Sie unter [Wiederherstellen einer App in Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Was wird gesichert?

App Service kann die folgenden Informationen in einem Azure-Speicherkonto und einem Container sichern, für deren Verwendung Sie die App konfiguriert haben.

* App-Konfiguration
* Dateiinhalte
* Datenbank, die mit Ihrer App verbunden ist.

Die folgenden Datenbanklösungen werden von der Sicherungsfunktion unterstützt:

- [SQL-Datenbank](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure-Datenbank für PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL In-App](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)

> [!NOTE]
> Jede Sicherung ist eine vollständige Offlinekopie Ihrer App. Es gibt keine inkrementellen Aktualisierungen.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Anforderungen und Einschränkungen

* Die Funktion zum Sichern und Wiederherstellen erfordert einen App Service-Plan im Tarif **Standard**, **Premium** oder **Isoliert**. Weitere Informationen zum Skalieren des App Service-Plans zur Verwendung eines höheren Tarifs finden Sie unter [Hochskalieren einer App in Azure](manage-scale-up.md). Im Tarif **Premium** und **Isoliert** ist eine größere Anzahl täglicher Sicherungen zulässig als im Tarif **Standard**.
* Sie benötigen ein Azure-Speicherkonto und einen Container im selben Abonnement wie die App, die Sie sichern möchten. Weitere Informationen zu Azure-Speicherkonten finden Sie unter [Azure Storage-Konto – Übersicht](../storage/common/storage-account-overview.md).
* Sicherungen können bis zu 10 GB App- und Datenbankinhalt umfassen, von denen bis zu 4 GB die Datenbanksicherung umfassen können. Wenn die Sicherungsgröße diesen Grenzwert überschreitet, erhalten Sie eine Fehlermeldung.
* Sicherungen von [Azure Database for MySQL mit aktiviertem TLS](../mysql/concepts-ssl-connection-security.md) werden nicht unterstützt. Wenn eine Sicherung konfiguriert ist, treten Sicherungsfehler auf.
* Sicherungen von [Azure Database for PostgreSQL mit aktiviertem TLS](../postgresql/concepts-ssl-connection-security.md) werden nicht unterstützt. Wenn eine Sicherung konfiguriert ist, treten Sicherungsfehler auf.
* In-App-MySQL-Datenbanken werden automatisch ohne Konfiguration gesichert. Wenn Sie manuelle Einstellungen für In-App-MySQL-Datenbanken festlegen (beispielsweise durch Hinzufügen von Verbindungszeichenfolgen), funktionieren die Sicherungen unter Umständen nicht ordnungsgemäß.
* Die Verwendung eines [Speicherkontos mit aktivierter Firewall](../storage/common/storage-network-security.md) als Ziel für Ihre Sicherungen wird nicht unterstützt. Wenn eine Sicherung konfiguriert ist, treten Sicherungsfehler auf.
* Die Verwendung eines [Speicherkontos mit aktiviertem privaten Endpunkt](../storage/common/storage-private-endpoints.md) für die Sicherung und Wiederherstellung wird nicht unterstützt.

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Erstellen einer manuellen Sicherung

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Seite Ihrer App, und wählen Sie **Sicherungen** aus. Die Seite **Sicherungen** wird angezeigt.

    ![Seite 'Sicherungen'](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Wenn die folgende Meldung angezeigt wird, klicken Sie darauf, um Ihren App Service-Plan zu aktualisieren, damit Sie mit Sicherungen fortfahren können.
    > Weitere Informationen finden Sie unter [Hochskalieren einer App in Azure](manage-scale-up.md).
    > :::image type="content" source="./media/manage-backup/upgrade-plan.png" alt-text="Screenshot: Banner mit einer Meldung zum Aktualisieren des App Service-Plans für den Zugriff auf das Feature „Backup and Restore“ (Sichern und Wiederherstellen)":::
    >
    >

2. Wählen Sie auf der Seite **Sicherung** die Option **Sicherung ist nicht konfiguriert. Klicken Sie hier, um die Sicherung für Ihre App zu konfigurieren** aus.

    ![Klicken Sie auf „Konfigurieren“.](./media/manage-backup/configure-start.png)

3. Klicken Sie auf der Seite **Sicherungskonfiguration** auf **Speicher: nicht konfiguriert**, um ein Speicherkonto zu konfigurieren.

    :::image type="content" source="./media/manage-backup/configure-storage.png" alt-text="Screenshot: Abschnitt „Sicherungsspeicher“ mit ausgewählter Einstellung „Der Speicher ist nicht konfiguriert“":::

4. Wählen Sie das Sicherungsziel durch Auswahl eines **Speicherkontos** und **Containers** aus. Das Speicherkonto muss zum selben Abonnement wie die App gehören, die Sie sichern möchten. Bei Bedarf können Sie auf den entsprechenden Seiten ein neues Speicherkonto oder einen neuen Container erstellen. Wenn Sie fertig sind, klicken Sie auf **Auswählen**.

5. Auf der noch geöffneten Seite **Sicherungskonfiguration** können Sie die Einstellungen für **Datenbank sichern** konfigurieren. Wählen Sie dann die Datenbanken aus, die in die Sicherungen einbezogen werden sollen (SQL-Datenbank oder MySQL), und klicken Sie auf **OK**.

    :::image type="content" source="./media/manage-backup/configure-database.png" alt-text="Screenshot: Abschnitt „Datenbank sichern“ mit der Auswahlmöglichkeit „In Sicherung einbeziehen“":::

    > [!NOTE]
    > Damit eine Datenbank in dieser Liste angezeigt wird, muss die zugehörige Verbindungszeichenfolge auf der Seite **Anwendungseinstellungen** für Ihre App im Abschnitt **Verbindungszeichenfolgen** angegeben sein. 
    >
    > In-App-MySQL-Datenbanken werden automatisch ohne Konfiguration gesichert. Wenn Sie Einstellungen für In-App-MySQL-Datenbanken manuell festlegen (beispielsweise durch Hinzufügen von Verbindungszeichenfolgen), funktionieren die Sicherungen unter Umständen nicht ordnungsgemäß.
    >
    >

6. Klicken Sie auf der Seite **Sicherungskonfiguration** auf **Speichern**.
7. Klicken Sie auf der Seite **Sicherungen** auf **Sichern**.

    ![Schaltfläche "Backup Now"](./media/manage-backup/manual-backup.png)

    Während des Sicherungsvorgangs wird eine Fortschrittsmeldung angezeigt.

Sobald Speicherkonto und Container konfiguriert sind, können Sie jederzeit eine manuelle Sicherung initiieren. Manuelle Sicherungen werden unbegrenzt aufbewahrt.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurieren automatischer Sicherungen

1. Legen Sie auf der Seite **Sicherungskonfiguration** die Option **Geplante Sicherung** auf **Ein** fest.

    ![Automatisierte Sicherungen aktivieren](./media/manage-backup/scheduled-backup.png)

2. Konfigurieren Sie den Sicherungszeitplan wie gewünscht, und wählen Sie **OK**  aus.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Konfigurieren von Teilsicherungen

Mitunter möchten Sie nicht alles in Ihrer App sichern. Hier sind einige Beispiele:

* Sie [richten wöchentliche Sicherungen](#configure-automated-backups) der App ein, die statische Inhalte enthält, die sich nie ändern, z.B. alte Blogbeiträge oder Bilder.
* Ihre App verfügt über mehr als 10 GB an Inhalten (der maximale Umfang, der auf einmal gesichert werden kann).
* Sie möchten die Protokolldateien nicht sichern.

Bei Teilsicherungen können Sie genau auswählen, welche Dateien gesichert werden sollen.

> [!NOTE]
> Einzelne Datenbanken in der Sicherung können maximal 4 GB groß sein, die maximale Gesamtgröße der Sicherung beträgt aber 10 GB.

### <a name="exclude-files-from-your-backup"></a>Ausschließen von Dateien aus der Sicherung

Angenommen, Sie verfügen über eine App mit Protokolldateien und statischen Images, die einmal gesichert wurden und sich nicht ändern werden. In solchen Fällen können Sie diese Ordner und Dateien von der Speicherung in zukünftigen Sicherungen ausschließen. Um Dateien und Ordner von Ihren Sicherungen auszuschließen, erstellen Sie eine `_backup.filter`-Datei im `D:\home\site\wwwroot`-Ordner Ihrer App. Geben Sie die Liste von Dateien und Ordnern an, die Sie in dieser Datei ausschließen möchten.

Sie können auf Ihre Dateien zugreifen, indem Sie zu `https://<app-name>.scm.azurewebsites.net/DebugConsole` navigieren. Melden Sie sich nach Aufforderung bei Ihrem Azure-Konto an.

Geben Sie die Ordner an, die Sie von Ihren Sicherungen ausschließen möchten. Sie möchten z.B. die hervorgehobenen Ordner und Dateien herausfiltern.

![Ordner "Images"](./media/manage-backup/kudu-images.png)

Erstellen Sie eine Datei namens `_backup.filter`, und fügen Sie die oben aufgeführte Liste in die Datei ein, aber entfernen Sie `D:\home`. Geben Sie ein Verzeichnis oder eine Datei pro Zeile an. Der Inhalt der Datei sollte folgendermaßen aussehen:

```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Laden Sie die `_backup.filter`-Datei in das `D:\home\site\wwwroot\`-Verzeichnis Ihrer Website hoch. Verwenden Sie dazu [ftp](deploy-ftp.md) oder eine andere Methode. Wenn Sie möchten, können Sie die Datei direkt mithilfe der Kudu-`DebugConsole` erstellen und den Inhalt dort einfügen.

Führen Sie die Sicherungen wie gewohnt aus: [manuell](#create-a-manual-backup) oder [automatisch](#configure-automated-backups). Jetzt sind alle in `_backup.filter` angegebenen Dateien und Ordner von zukünftigen geplanten oder manuell initiierten Sicherungen ausgeschlossen.

> [!NOTE]
> Sie stellen Teilsicherungen Ihrer Website genauso wieder her, wie Sie eine [normale Sicherung wiederherstellen](web-sites-restore.md)würden. Der Wiederherstellungsvorgang wird richtig ausgeführt.
>
> Bei der Wiederherstellung einer vollständigen Sicherung wird der gesamte Inhalt der Website durch den Inhalt der Sicherung ersetzt. Wenn eine Datei auf der Website vorhanden ist, aber nicht in der Sicherung, wird sie gelöscht. Wenn aber eine Teilsicherung wiederhergestellt wird, bleiben alle Inhalte von eingeschränkten Verzeichnissen bzw. alle eingeschränkten Dateien unverändert.
>

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Speichern von Sicherungen

Nachdem Sie eine oder mehrere Sicherungen für Ihre App vorgenommen haben, werden die Sicherungen auf der Seite **Container** in Ihrem Speicherkonto sowie in Ihrer App angezeigt. Im Speicherkonto besteht jede Sicherung aus einer `.zip`-Datei mit den gesicherten Daten und einer `.xml`-Datei, die ein Manifest des `.zip`-Dateiinhalts enthält. Sie können diese Dateien extrahieren und durchsuchen, wenn Sie auf die Sicherungen zugreifen möchten, ohne eine App-Wiederherstellung auszuführen.

Die Datenbanksicherung für die App wird im Stammverzeichnis der ZIP-Datei gespeichert. Bei SQL-Datenbank ist dies eine BACPAC-Datei (ohne Dateierweiterung), die importiert werden kann. Schritte zum Erstellen einer Datenbank in Azure SQL-Datenbank auf Basis des BACPAC-Exports finden Sie unter [Importieren einer BACPAC-Datei zum Erstellen einer Datenbank in Azure SQL-Datenbank](../azure-sql/database/database-import.md).

> [!WARNING]
> Wenn Sie die Dateien im Container **websitebackups** ändern, kann die Sicherung ungültig werden und nicht mehr wiederhergestellt werden.

## <a name="troubleshooting"></a>Problembehandlung

Auf der Seite **Sicherungen** wird der Status der einzelnen Sicherungen angezeigt. Wenn Sie auf eine fehlerhafte Sicherung klicken, können Sie Protokolldetails zum Fehler anzeigen. Verwenden Sie die folgende Tabelle als Hilfe beim Behandeln von Problemen mit Ihrer Sicherung. Falls der Fehler nicht in der Tabelle dokumentiert ist, sollten Sie ein Supportticket erstellen.

| Fehler | Behebung |
| - | - |
| Fehler beim Speicherzugriff. | Löschen Sie den Sicherungszeitplan, und konfigurieren Sie ihn neu. Oder konfigurieren Sie den Sicherungsspeicher neu. |
| Die gemeinsame Größe der Website und der Datenbank überschreitet den GB-Grenzwert von {0} für Sicherungen. Die Größe Ihrer Inhalte beträgt {1} GB. | [Schließen Sie einige Dateien aus der Sicherung aus](#configure-partial-backups), oder entfernen Sie den Datenbankteil der Sicherung, und verwenden Sie stattdessen extern angebotene Sicherungen. |
| Fehler beim Herstellen einer Verbindung mit der Datenbank {0} auf dem Server {1}: Bei der Authentifizierung des Hosts „{1}“ für den Benutzer „\<username>“ mit der Methode „mysql_native_password“ ist ein Fehler mit der folgenden Meldung aufgetreten: Unbekannte Datenbank „\<db-name>“. | Aktualisieren Sie die Datenbankverbindungszeichenfolge. |
| {0} kann nicht aufgelöst werden. {1} (CannotResolveStorageAccount) | Löschen Sie den Sicherungszeitplan, und konfigurieren Sie ihn neu. |
| Fehler bei der Anmeldung für den Benutzer „{0}“. | Aktualisieren Sie die Datenbank-Verbindungszeichenfolge. |
| Beim Erstellen einer Datenbankkopie von {0} ({1}) wurde eine Ausnahme ausgelöst. Fehler beim Erstellen der Datenbankkopie. | Verwenden Sie in der Verbindungszeichenfolge einen Administrator. |
| Der Serverprinzipal „\<name>“ kann im aktuellen Sicherheitskontext nicht auf die „Master“-Datenbank zugreifen. Der von der Anmeldung angeforderte „Master“ der Datenbank kann nicht geöffnet werden. Fehler bei der Anmeldung. Fehler bei der Anmeldung für den Benutzer „\<name>“. | Verwenden Sie in der Verbindungszeichenfolge einen Administrator. |
| Netzwerkbezogener oder instanzspezifischer Fehler beim Herstellen einer Verbindung mit SQL Server. Der Server wurde nicht gefunden, oder auf ihn kann nicht zugegriffen werden. Stellen Sie sicher, dass der Instanzname richtig und SQL Server so konfiguriert ist, das Remoteverbindungen zulässig sind. (Anbieter: Named Pipes-Anbieter, Fehler: 40 – Es konnte keine Verbindung zu SQL Server hergestellt werden. | Überprüfen Sie, ob die Verbindungszeichenfolge gültig ist. Lassen Sie in den Einstellungen des Datenbankservers die [IP-Adressen für die ausgehende Richtung](overview-inbound-outbound-ips.md) zu. |
| In Anmeldung angeforderter Server „\<name>“ kann nicht geöffnet werden. Fehler bei der Anmeldung. | Überprüfen Sie, ob die Verbindungszeichenfolge gültig ist. |
| Erforderliche Parameter für gültige SAS (Shared Access Signature) fehlen. | Löschen Sie den Sicherungszeitplan, und konfigurieren Sie ihn neu. |
| SSL-Verbindung ist erforderlich. Geben Sie SSL-Optionen an, und wiederholen Sie den Vorgang bei der Verbindungsherstellung. | Verwenden Sie stattdessen das integrierte Sicherungsfeature in Azure MySQL oder Azure PostgresSQL. |

## <a name="automate-with-scripts"></a>Automatisieren mit Skripts

Mit [Azure CLI](/cli/azure/install-azure-cli) oder mit [Azure PowerShell](/powershell/azure/) können Sie mithilfe von Skripts die Sicherungsverwaltung automatisieren.

Beispiele finden Sie unter:

- [Azure CLI-Beispiele](samples-cli.md)
- [Azure PowerShell-Beispiele](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Wiederherstellen einer App aus einer Sicherung finden Sie unter [Wiederherstellen einer App in Azure](web-sites-restore.md).
