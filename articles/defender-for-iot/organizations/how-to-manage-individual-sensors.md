---
title: Verwalten einzelner Sensoren
description: Hier erfahren Sie, wie Sie einzelne Sensoren verwalten können. Dazu gehören das Verwalten von Aktivierungsdateien und Zertifikaten, das Ausführen von Sicherungen sowie das Aktualisieren eines eigenständigen Sensors.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: dbbf08f1e139a265b947cc99c7287765d6a6d03a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339951"
---
# <a name="manage-individual-sensors"></a>Verwalten einzelner Sensoren

Dieser Artikel beschreibt, wie einzelne Sensoren verwaltet werden. Die Aufgaben umfassen das Verwalten von Aktivierungsdateien, Ausführen von Sicherungen und Aktualisieren eines eigenständigen Sensors.

Sie können auch bestimmte Sensorverwaltungsaufgaben über die lokale Verwaltungskonsole ausführen, in der mehrere Sensoren gleichzeitig verwaltet werden können.

Für das Integrieren und Registrieren von Sensoren verwenden Sie das Azure-Portal.

## <a name="manage-sensor-activation-files"></a>Verwalten von Sensoraktivierungsdateien

Ihre Sensoren wurden mit Microsoft Defender für IoT über das Azure-Portal integriert. Jeder Sensor wurde entweder als lokal verbundener Sensor oder als ein mit der Cloud verbundener Sensor integriert.

Auf jeden von Ihnen bereitgestellten Sensor wird eine eindeutige Aktivierungsdatei hochgeladen. Weitere Informationen dazu, wann und wie eine neue Datei verwendet wird, finden Sie unter [Hochladen neuer Aktivierungsdateien](#upload-new-activation-files). Wenn Sie die Datei nicht hochladen können, finden Sie weitere Informationen unter [Problembehandlung beim Hochladen der Aktivierungsdatei](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>Informationen zu Aktivierungsdateien für lokal verbundene Sensoren

Lokal verbundene Sensoren sind einem Azure-Abonnement zugeordnet. Die Aktivierungsdatei für die lokal verbundenen Sensoren enthält ein Ablaufdatum. Einen Monat vor diesem Datum wird oben in der Sensorkonsole eine Warnmeldung angezeigt. Die Warnung wird so lange beibehalten, bis Sie die Aktivierungsdatei aktualisiert haben.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="Screenshot der Systemeinstellungen":::

Sie können auch nach Ablauf der Aktivierungsdatei weiterhin mit Features von Azure Defender für IoT arbeiten. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Informationen zu Aktivierungsdateien für mit der Cloud verbundene Sensoren

Sensoren, die mit der Cloud verbunden sind, werden dem Defender für IoT-Hub zugeordnet. Diese Sensoren sind nicht durch Zeiträume für die Aktivierungsdatei einschränkt. Die Aktivierungsdatei für mit der Cloud verbundene Sensoren wird zur Gewährleistung einer Verbindung mit dem Defender für IoT-Hub verwendet.

### <a name="upload-new-activation-files"></a>Hochladen neuer Aktivierungsdateien

In folgenden Fällen müssen Sie möglicherweise eine neue Aktivierungsdatei für einen integrierten Sensor hochladen:

- Eine Aktivierungsdatei läuft auf einem lokal verbundenen Sensor ab. 

- Sie möchten in einem anderen Sensorverwaltungsmodus arbeiten. 

- Sie möchten einem mit der Cloud verbundenen Sensor einen neuen Defender für IoT-Hub zuweisen.

**Zum Hinzufügen einer neuen Aktivierungsdateien gehen Sie folgendermaßen vor:**

1. Navigieren Sie zur Seite **Sensorverwaltung**.

2. Wählen Sie den Sensor aus, für den Sie eine neue Aktivierungsdatei hochladen möchten.

3. Löschen Sie es.

4. Integrieren Sie den Sensor über die Seite **Onboarding** im neuen Modus oder in einen neuen Defender für IoT-Hub.

5. Laden Sie die Aktivierungsdatei von der Seite **Aktivierungsdatei herunterladen** herunter.

6. Speichern Sie die Datei .

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Herunterladen der Aktivierungsdatei vom Defender für IoT-Hub":::

7. Melden Sie sich bei der Sensorkonsole von Defender für IoT an.

8. Wählen Sie in der Sensorkonsole **Systemeinstellungen** > **Reaktivierung** aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Auswahl „Reaktivierung“ auf dem Bildschirm „Systemeinstellungen“":::

9. Wählen Sie **Hochladen** und dann die gespeicherte Datei aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Hochladen der gespeicherten Datei":::

10. Wählen Sie **Aktivieren** aus.

### <a name="troubleshoot-activation-file-upload"></a>Problembehandlung beim Hochladen der Aktivierungsdatei

Sie erhalten eine Fehlermeldung, wenn die Aktivierungsdatei nicht hochgeladen werden konnte. Es können die folgenden Ereignisse aufgetreten sein:

- **Lokal verbundene Sensoren**: Die Aktivierungsdatei ist ungültig. Wenn die Datei ungültig ist, wechseln Sie im [Azure-Portal zu „Defender für IoT“](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started). Wählen Sie auf der Seite **Sensorverwaltung** den Sensor mit der ungültigen Datei aus, und laden Sie eine neue Aktivierungsdatei herunter.

- **Mit der Cloud verbundene Sensoren**: Der Sensor kann keine Verbindung mit dem Internet herstellen. Überprüfen Sie die Netzwerkkonfiguration des Sensors. Wenn der Sensor für den Zugriff auf das Internet eine Verbindung über einen Webproxy herstellen muss, vergewissern Sie sich auf dem Bildschirm **Sensornetzwerkkonfiguration**, dass der Proxyserver ordnungsgemäß konfiguriert ist. Stellen Sie sicher, dass in der Firewall und/oder im Proxy „\*.azure-devices.net:443“ zulässig ist. Wenn keine Platzhalter unterstützt werden oder Sie mehr Kontrolle wünschen, sollte der FQDN für den spezifischen Defender für IoT-Hub in der Firewall und/oder dem Proxy geöffnet werden. Ausführliche Informationen finden Sie unter [Referenz: IoT Hub-Endpunkte](../../iot-hub/iot-hub-devguide-endpoints.md).  

- **Mit der Cloud verbundene Sensoren**: Die Aktivierungsdatei ist gültig, aber Defender für IoT hat sie abgelehnt. Wenn Sie dieses Problem nicht beheben können, können Sie im **Azure-Portal** eine andere Aktivierung von der Seite [Standorte und Sensoren](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) herunterladen. Wenn das nicht funktioniert, wenden Sie sich an den Microsoft-Support.

## <a name="manage-certificates"></a>Verwalten von Zertifikaten

Nach der Sensorinstallation wird ein lokales selbstsigniertes Zertifikat generiert und für den Zugriff auf die Sensorwebanwendung verwendet. Bei der ersten Anmeldung beim Sensor werden Administratoren aufgefordert, ein SSL/TLS-Zertifikat anzugeben. 

Möglicherweise müssen Sensoradministratoren Zertifikate aktualisieren, die nach der ersten Anmeldung hochgeladen wurden. Dies kann beispielsweise vorkommen, wenn ein Zertifikat abgelaufen ist.

**So aktualisieren Sie ein Zertifikat**

1. Wählen Sie **Systemeinstellungen** aus.

1. Wählen Sie **SSL/TLS-Zertifikate** aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/certificate-upload.png" alt-text="Hochladen eines Zertifikats":::

1. Löschen Sie im Dialogfeld „SSL/TLS-Zertifikate“ das vorhandene Zertifikat, und fügen Sie ein neues hinzu.

    - Fügen Sie einen Zertifikatnamen hinzu.
    - Laden Sie eine CRT-Datei und eine Schlüsseldatei hoch.
    - Laden Sie bei Bedarf eine PEM-Datei hoch.

Wenn der Upload fehlschlägt, wenden Sie sich an Ihren Sicherheits- oder IT-Administrator, oder aber lesen Sie die Informationen unter [Informationen zu Zertifikaten](how-to-deploy-certificates.md).

**So ändern Sie die Zertifikatsüberprüfungseinstellung:**

1. Aktivieren oder deaktivieren Sie den Umschalter **Serverzertifikatüberprüfung aktivieren**. Wenn die Option aktiviert ist und bei der Überprüfung ein Fehler auftritt, wird die Kommunikation zwischen relevanten Komponenten angehalten, und in der Konsole wird ein Überprüfungsfehler angezeigt. Wenn sie deaktiviert ist, wird keine Zertifikatüberprüfung durchgeführt. Weitere Informationen finden Sie unter [Informationen zur Zertifikatüberprüfung](how-to-deploy-certificates.md#about-certificate-validation).

1. Wählen Sie **Speichern** aus.

Weitere Informationen zum erstmaligen Hochladen von Zertifikaten finden Sie unter [Prüfliste für die erstmalige Anmeldung und Aktivierung](how-to-activate-and-set-up-your-sensor.md#first-time-sign-in-and-activation-checklist).

## <a name="connect-a-sensor-to-the-management-console"></a>Verbinden eines Sensors mit der Verwaltungskonsole

In diesem Abschnitt wird beschrieben, wie die Verbindung zwischen dem Sensor und der lokalen Verwaltungskonsole sichergestellt wird. Sie müssen dies tun, wenn Sie in einem Air-Gap-Netzwerk arbeiten und Geräte- und Warnungsinformationen vom Sensor an die Verwaltungskonsole senden möchten. Diese Verbindung ermöglicht der Verwaltungskonsole außerdem das Übertragen von Systemeinstellungen per Push an den Sensor und das Ausführen anderer Verwaltungsaufgaben auf dem Sensor.

Zum Herstellen einer Verbindung gehen Sie folgendermaßen vor:

1. Melden Sie sich bei der lokalen Verwaltungskonsole an.

2. Wählen Sie **Systemeinstellungen** aus.

3. Kopieren Sie im Abschnitt **Sensoreinrichtung – Verbindungszeichenfolge** die automatisch generierte Verbindungszeichenfolge.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Kopieren der Verbindungszeichenfolge auf dem Bildschirm"::: 

4. Melden Sie sich bei der Sensorkonsole an.

5. Wählen Sie im linken Bereich **Systemeinstellungen** aus.

6. Wählen Sie **Verwaltungskonsolenverbindung** aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Screenshot des Dialogfelds „Verwaltungskonsolenverbindung“":::

7. Fügen Sie die **Verbindungszeichenfolge** im entsprechenden Feld ein, und wählen Sie **Verbinden** aus.

8. Weisen Sie in der lokalen Verwaltungskonsole im Fenster **Standortverwaltung** den Sensor einer Zone zu.

## <a name="change-the-name-of-a-sensor"></a>Ändern des Namens eines Sensors

Sie können den Namen Ihrer Sensorkonsole ändern. Der neue Name wird im Konsolenwebbrowser, in den verschiedenen Konsolenfenstern und in Problembehandlungsprotokollen angezeigt.

Die Vorgehensweise zum Ändern von Sensornamen ist bei lokal verbundenen Sensoren und mit der Cloud verbundenen Sensoren unterschiedlich. Der Standardname lautet **sensor**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Ändern des Namens eines lokal verbundenen Sensors

So ändern Sie den Namen

1. Wählen Sie unten im linken Bereich der Konsole die aktuelle Sensorbezeichnung aus.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Screenshot der Sensorbezeichnung":::

1. Geben Sie im Dialogfeld **Sensornamen bearbeiten** einen Namen ein.

1. Wählen Sie **Speichern** aus. Der neue Name wird übernommen.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Ändern des Namens eines mit der Cloud verbundenen Sensors

Wenn der Sensor als ein mit der Cloud verbundener Sensor registriert wurde, ist der Sensorname durch den während der Registrierung zugewiesenen Namen definiert. Der Name ist in der Aktivierungsdatei enthalten, die Sie bei der erstmaligen Anmeldung hochgeladen haben. Um den Namen des Sensors zu ändern, müssen Sie eine neue Aktivierungsdatei hochladen.

So ändern Sie den Namen

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) zur Seite „Standorte und Sensoren“.

1. Löschen Sie den Sensor auf der Seite „Standorte und Sensoren“.

1. Führen Sie die Registrierung mit dem neuen Namen durch, indem Sie auf der Seite „Erste Schritte“ die Option **Sensor integrieren** auswählen.

1. Laden Sie die neue Aktivierungsdatei herunter.

1. Melden Sie sich bei der Sensorkonsole von Defender für IoT an.

1. Wählen Sie in der Sensorkonsole **Systemeinstellungen** und dann **Reaktivierung** aus.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Hochladen Ihrer Aktivierungsdatei zum erneuten Aktivieren des Sensors":::

1. Wählen Sie **Hochladen** und dann die gespeicherte Datei aus.

1. Wählen Sie **Aktivieren** aus.

## <a name="update-the-sensor-network-configuration"></a>Aktualisieren der Sensornetzwerkkonfiguration

Die Sensornetzwerkkonfiguration wurde während der Sensorinstallation definiert. Sie können Konfigurationsparameter ändern. Sie können auch eine Proxykonfiguration einrichten.

Wenn Sie eine neue IP-Adresse erstellen, müssen Sie sich möglicherweise erneut anmelden.

Zum Ändern der Konfiguration gehen Sie folgendermaßen vor:

1. Wählen Sie im seitlichen Menü **Systemeinstellungen** aus.

2. Wählen Sie im Fenster **Systemeinstellungen** die Option **Netzwerk** aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Konfigurieren Ihrer Netzwerkeinstellungen":::

3. Legen Sie die Parameter fest:

    | Parameter | BESCHREIBUNG |
    |--|--|
    | IP-Adresse | Die IP-Adresse des Sensors |
    | Subnetzmaske | Die Maskenadresse |
    | Standardgateway | Die Standardgatewayadresse |
    | DNS | Die DNS-Serveradresse |
    | Hostname | Der Hostname des Sensors |
    | Proxy | Proxyhost- und Portname |

4. Wählen Sie **Speichern** aus.

## <a name="synchronize-time-zones-on-the-sensor"></a>Synchronisieren von Zeitzonen auf dem Sensor

Sie können die Uhrzeit und die Region des Sensors so konfigurieren, dass allen Benutzern dieselbe Uhrzeit und Region angezeigt werden.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Konfigurieren der Uhrzeit und Region":::

| Parameter | BESCHREIBUNG |
|--|--|
| Zeitzone | Die Zeitzonendefinition für:<br />– Warnungen<br />– Widgets für Trends und Statistiken<br />– Data Mining-Berichte<br />   – Risikobewertungsberichte<br />– Angriffsvektoren |
| Datumsformat | Wählen Sie eine der folgenden Formatoptionen aus:<br />– TT/MM/JJJJ hh:mm:ss<br />– MM/TT/JJJJ hh:mm:ss<br />– JJJJ/MM/TT hh:mm:ss |
| Datum und Uhrzeit | Zeigt das aktuelle Datum und die lokale Uhrzeit im ausgewählten Format an.<br />Wenn Ihr tatsächlicher Standort beispielsweise Amerika und New York ist, die Zeitzone aber auf Europa und Berlin festgelegt ist, wird die Uhrzeit entsprechend der Ortszeit in Berlin angezeigt. |

Zum Konfigurieren der Sensorzeit gehen Sie folgendermaßen vor:

1. Wählen Sie im seitlichen Menü **Systemeinstellungen** aus.

2. Wählen Sie im Fenster **Systemeinstellungen** die Option für **Zeit- und Regionseinstellungen** aus.

3. Legen Sie die Parameter fest, und wählen Sie **Speichern** aus.

## <a name="set-up-backup-and-restore-files"></a>Einrichten von Sicherungs- und Wiederherstellungsdateien

Eine Systemsicherung wird automatisch täglich um 3:00 Uhr morgens ausgeführt. Die Daten werden auf einem anderen Datenträger im Sensor gespeichert. Der Standardspeicherort ist `/var/cyberx/backups`.

Sie können diese Datei automatisch in das interne Netzwerk übertragen.

> [!NOTE]
> - Das Sicherungs- und Wiederherstellungsverfahren kann nur zwischen denselben Versionen durchgeführt werden.
> - In manchen Architekturen ist die Sicherung deaktiviert. Sie können sie in der Datei `/var/cyberx/properties/backup.properties` aktivieren.

Wenn Sie einen Sensor mithilfe der lokalen Verwaltungskonsole steuern, können Sie den Sicherungszeitplan des Sensors verwenden, um diese Sicherungen zu sammeln und in der Verwaltungskonsole oder auf einem externen Sicherungsserver zu speichern.

**Gesicherte Elemente:** Konfigurationen und Daten

**Nicht gesicherte Elemente:** PCAP-Dateien und -Protokolle. Sie können PCAP-Dateien und -Protokolle manuell sichern und wiederherstellen.

Sensorsicherungsdateien werden automatisch im folgenden Format benannt: `<sensor name>-backup-version-<version>-<date>.tar`. z. B. `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Zum Konfigurieren der Sicherung gehen Sie folgendermaßen vor:

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `$ sudo cyberx-xsense-system-backup` ein.

So stellen Sie die neueste Sicherungsdatei wieder her

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `$ sudo cyberx-xsense-system-restore` ein.

So speichern Sie die Sicherung auf einem externen SMB-Server

1. Erstellen Sie einen freigegebenen Ordner auf dem externen SMB-Server.

    Rufen Sie den Ordnerpfad, den Benutzernamen und das Kennwort ab, die für den Zugriff auf den SMB-Server erforderlich sind.

2. Erstellen Sie auf dem Sensor ein Verzeichnis für die Sicherungen:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Bearbeiten Sie `fstab`:

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Bearbeiten und erstellen Sie Anmeldeinformationen zur Freigabe für den SMB-Server:

    `sudo nano /etc/samba/user` 

5. Hinzufügen:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Stellen Sie das Verzeichnis bereit:

    `sudo mount -a`

7. Konfigurieren Sie ein Sicherungsverzeichnis für den freigegebenen Ordner auf dem Defender für IoT-Sensor:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Wiederherstellen von Sensoren

Sie können Sicherungen über die Sensorkonsole und mithilfe der CLI wiederherstellen.

**Wiederherstellen über die Konsole:**

- Wählen Sie im Fenster **Systemeinstellungen** des Sensors die Option **Abbild wiederherstellen** aus.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Wiederherstellen des Abbilds durch Auswählen der Schaltfläche":::

In der Konsole werden Wiederherstellungsfehler angezeigt.

**Wiederherstellung mithilfe der CLI:**

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `$ sudo cyberx-management-system-restore` ein.

## <a name="update-a-standalone-sensor-version"></a>Aktualisieren einer eigenständigen Sensorversion

Im folgenden Verfahren wird beschrieben, wie Sie einen eigenständigen Sensor mithilfe der Sensorkonsole aktualisieren. Der Aktualisierungsvorgang dauert ungefähr 30 Minuten.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

2. Wechseln Sie zu Defender für IoT.

3. Navigieren Sie zur Seite **Updates**.

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Screenshot der Seite „Updates“ in Defender für IoT":::

4. Wählen Sie im Abschnitt **Sensoren** die Option **Herunterladen** aus, und speichern Sie die Datei.

5. Wählen Sie in der Seitenleiste der Sensorkonsole **Systemeinstellungen** aus.

6. Wählen Sie im Bereich **Versionsupdate** die Option **Update** aus.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Screenshot des Upgradebereichs":::

7. Wählen Sie die Datei aus, die Sie von der Seite **Updates** in Defender für IoT heruntergeladen haben.

8. Der Aktualisierungsvorgang beginnt. Während dieses Zeitraums wird das System zweimal neu gestartet. Nach dem ersten Neustart (vor Abschluss des Aktualisierungsvorgangs) wird das System mit dem Anmeldefenster geöffnet. Nachdem Sie sich angemeldet haben, wird die Upgradeversion unten links auf der Seitenleiste angezeigt.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Screenshot der Upgradeversion, die nach der Anmeldung angezeigt wird":::

## <a name="forward-sensor-failure-alerts"></a>Weiterleiten von Warnungen für Sensorfehler

Sie können Warnungen an Dritte weiterleiten, um Details zu Folgendem bereitzustellen:

- Getrennte Sensoren

- Remotesicherungsfehler

Diese Informationen werden gesendet, wenn Sie eine Weiterleitungsregel für Systembenachrichtigungen erstellen.

> [!NOTE]
> Administratoren können Systembenachrichtigungen senden.

Zum Senden von Benachrichtigungen gehen Sie folgendermaßen vor:

1. Melden Sie sich bei der lokalen Verwaltungskonsole an.
1. Wählen Sie im seitlichen Menü **Weiterleitung** aus.
1. Erstellen Sie eine Weiterleitungsregel.
1. Wählen Sie **Systembenachrichtigungen melden** aus.

Weitere Informationen zu Weiterleitungsregeln finden Sie unter [Weiterleiten von Warnungsinformationen](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Anpassen von Systemeigenschaften

Systemeigenschaften steuern verschiedene Vorgänge und Einstellungen im Sensor. Das Bearbeiten oder Ändern dieser Eigenschaften kann den Betrieb der Sensorkonsole beeinträchtigen.

Wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com/), bevor Sie Einstellungen ändern.

Zum Zugreifen auf Systemeigenschaften gehen Sie folgendermaßen vor:

1. Melden Sie sich bei der lokalen Verwaltungskonsole oder dem Sensor an.

2. Wählen Sie **Systemeinstellungen** aus.

3. Wählen Sie im Abschnitt **Allgemein** die Option **Systemeigenschaften** aus.

## <a name="see-also"></a>Weitere Informationen

[Forschung und Pakete zur Bedrohungsanalyse](how-to-work-with-threat-intelligence-packages.md)

[Verwalten von Sensoren über die Verwaltungskonsole](how-to-manage-sensors-from-the-on-premises-management-console.md)
