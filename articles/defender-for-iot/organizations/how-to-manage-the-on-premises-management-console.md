---
title: Verwalten der lokalen Verwaltungskonsole
description: Erfahren Sie mehr über die Möglichkeiten der lokalen Verwaltungskonsole wie Sicherung und Wiederherstellung, Definition des Hostnamens und Einrichtung eines Proxys für Sensoren.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 3604b4674a86d719f2fecf841347c0a55f5b9757
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339704"
---
# <a name="manage-the-on-premises-management-console"></a>Verwalten der lokalen Verwaltungskonsole

In diesem Artikel werden die Möglichkeiten in der lokalen Verwaltungskonsole behandelt, wie z. B. Sicherung und Wiederherstellung, Herunterladen der Geräteaktivierungsdatei des Komitees, Aktualisieren von Zertifikaten und Einrichten eines Proxys für Sensoren.

Sie führen das Onboarding der lokalen Verwaltungskonsole über das Azure-Portal durch.

## <a name="upload-an-activation-file"></a>Hochladen einer Aktivierungsdatei

Wenn Sie sich zum ersten Mal anmelden, wird eine Aktivierungsdatei für die lokale Verwaltungskonsole heruntergeladen. Diese Datei enthält die aggregierten zugesicherten Geräte, die während des Onboardingprozesses definiert wurden. Die Liste enthält Sensoren, die mehreren Abonnements zugeordnet sind.

Nach der erstmaligen Aktivierung kann die Anzahl der überwachten Geräte die Anzahl der zugesicherten Geräte überschreiten, die während des Onboardings definiert wurden. Dies kann beispielsweise der Fall sein, wenn Sie weitere Sensoren mit der Verwaltungskonsole verbinden. Bei einer Abweichung zwischen der Anzahl überwachter Geräten und der Anzahl zugesicherter Geräten wird in der Verwaltungskonsole eine Warnung angezeigt. In diesem Fall sollten Sie eine neue Aktivierungsdatei hochladen.

**So laden Sie eine Aktivierungsdatei hoch**

1. Navigieren Sie zur Seite **Preise** von Microsoft Defender für IoT
1. Wählen Sie die Registerkarte **Aktivierungsdatei für die Verwaltungskonsole herunterladen** aus. Die Aktivierungsdatei wird heruntergeladen.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Laden Sie die Aktivierungsdatei herunter.":::

1. Wählen Sie in der Verwaltungskonsole **Systemeinstellungen** aus.
1. Wählen Sie **Aktivierung** aus.
1. Wählen Sie **Datei auswählen** und dann die gespeicherte Datei aus.

## <a name="manage-certificates"></a>Verwalten von Zertifikaten

Nach der Installation der lokalen Verwaltungskonsole wird ein lokales selbstsigniertes Zertifikat generiert und für den Zugriff auf die Webanwendung verwendet. Bei der ersten Anmeldung an der lokalen Verwaltungskonsole werden Administratoren aufgefordert, ein SSL/TLS-Zertifikat anzugeben. 

Möglicherweise müssen Administratoren Zertifikate aktualisieren, die nach der ersten Anmeldung hochgeladen wurden. Dies kann beispielsweise vorkommen, wenn ein Zertifikat abgelaufen ist.

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

## <a name="define-backup-and-restore-settings"></a>Festlegen von Sicherungs- und Wiederherstellungseinstellungen

Die Sicherung des lokalen Verwaltungskonsolensystems erfolgt automatisch täglich. Die Daten werden auf einem anderen Datenträger gespeichert. Der Standardspeicherort ist `/var/cyberx/backups`. 

Sie können diese Datei automatisch in das interne Netzwerk übertragen. 

> [!NOTE]
> Sie können den Sicherungs- und Wiederherstellungsvorgang nur für dieselbe Version durchführen. 

So sichern Sie den Computer mit der lokalen Verwaltungskonsole 

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `sudo cyberx-management-backup -full` ein.

So stellen Sie die neueste Sicherungsdatei wieder her

- Melden Sie sich bei einem Administratorkonto an, und geben Sie `$ sudo cyberx-management-system-restore` ein.

So speichern Sie die Sicherung auf einem externen SMB-Server

1. Erstellen Sie einen freigegebenen Ordner auf dem externen SMB-Server.  

   Rufen Sie den Ordnerpfad, den Benutzernamen und das Kennwort ab, die für den Zugriff auf den SMB-Server erforderlich sind. 

2. Erstellen Sie in Defender für IoT ein Verzeichnis für die Sicherungen:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Bearbeiten Sie „fstab“:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Bearbeiten und erstellen Sie Anmeldeinformationen zur Freigabe für den SMB-Server: 

   - `sudo nano /etc/samba/user` 

5. Hinzufügen: 

   - `username=<user name>`

   - `password=<password>` 

6. Stellen Sie das Verzeichnis bereit: 

   - `sudo mount -a` 

7. Konfigurieren Sie in der lokalen Verwaltungskonsole von Defender für IoT ein Sicherungsverzeichnis für den freigegebenen Ordner:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Bearbeiten des Hostnamens

So bearbeiten Sie den im DNS-Server der Organisation konfigurierten Hostnamen der Verwaltungskonsole

1. Wählen Sie im linken Bereich der Verwaltungskonsole **Systemeinstellungen** aus.  

2. Wählen Sie im Bereich „Netzwerk“ der Konsole **Netzwerk** aus. 

3. Geben Sie den Hostnamen ein, der im DNS-Server der Organisation konfiguriert ist. 

4. Wählen Sie **Speichern** aus.

## <a name="define-vlan-names"></a>Festlegen von VLAN-Namen

VLAN-Namen werden nicht zwischen Sensor und Verwaltungskonsole synchronisiert. Legen Sie identische Namen für Komponenten fest.

Wählen Sie im Bereich „Netzwerk“ die Option **VLAN** aus, und fügen Sie den ermittelten VLAN-IDs Namen hinzu. Klicken Sie dann auf **Speichern**.

## <a name="define-a-proxy-to-sensors"></a>Festlegen eines Proxys für Sensoren

Erhöhen Sie die Systemsicherheit, indem Sie verhindern, dass sich Benutzer direkt am Sensor anmelden. Arbeiten Sie stattdessen mit Tunneln des Proxys, damit Benutzer mithilfe einer einzigen Firewallregel in der lokalen Verwaltungskonsole auf den Sensor zugreifen können. Diese Verbesserung schränkt die Möglichkeit eines nicht autorisierten Zugriffs auf die Netzwerkumgebung über den Sensor hinaus ein.

Arbeiten Sie mit einem Proxy in Umgebungen ohne direkte Konnektivität mit Sensoren.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Benutzer.":::

Mit dem folgenden Verfahren wird ein Sensor mit der lokalen Verwaltungskonsole verbunden und Tunneln für diese Konsole aktiviert:

1. Melden Sie sich mit administrativen Anmeldeinformationen bei der CLI der lokalen Verwaltungskonsole der Appliance an.

2. Geben Sie `sudo cyberx-management-tunnel-enable` ein, und drücken Sie dann die **EINGABETASTE**.

4. Geben Sie `--port 10000` ein, und drücken Sie dann die **EINGABETASTE**.

## <a name="adjust-system-properties"></a>Anpassen von Systemeigenschaften

Systemeigenschaften steuern verschiedene Vorgänge und Einstellungen in der Verwaltungskonsole. Das Bearbeiten oder Ändern dieser Eigenschaften kann den Betrieb der Verwaltungskonsole beeinträchtigen. Wenden Sie sich an den [Microsoft-Support](https://support.microsoft.com), ehe Sie Einstellungen ändern.

So greifen Sie auf Systemeigenschaften zu 

1. Melden Sie sich bei der lokalen Verwaltungskonsole oder dem Sensor an.

2. Wählen Sie **Systemeinstellungen** aus.

3. Wählen Sie im Abschnitt **Allgemein** die Option **Systemeigenschaften** aus.

## <a name="change-the-name-of-the-on-premises-management-console"></a>Ändern des Namens der lokalen Verwaltungskonsole

Sie können den Namen der lokalen Verwaltungskonsole ändern. Der neue Name wird im Konsolenwebbrowser, in verschiedenen Konsolenfenstern und Problembehandlungsprotokollen angezeigt. Der Standardname ist **Verwaltungskonsole**.

So ändern Sie den Namen

1. Wählen Sie unten im linken Bereich den aktuellen Namen aus.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Screenshot der Version der lokalen Verwaltungskonsole.":::

2. Geben Sie im Dialogfeld **Konfiguration der Verwaltungskonsole bearbeiten** den neuen Namen ein. Der Name darf maximal 25 Zeichen lang sein.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Screenshot der Bearbeitung der Konfiguration der Defender für IoT-Plattform.":::

3. Wählen Sie **Speichern** aus. Der neue Name wird übernommen.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Screenshot mit dem geänderten Namen der Konsole.":::

## <a name="password-recovery"></a>Kennwortwiederherstellung

Die Kennwortwiederherstellung für Ihre lokale Verwaltungskonsole ist an das Abonnement gebunden, zu dem das Gerät gehört. Sie können ein Kennwort nicht wiederherstellen, wenn Sie nicht wissen, zu welchem Abonnement ein Gerät gehört.

So setzen Sie Ihr Kennwort zurück:

1. Besuchen Sie die Anmeldeseite der lokalen Verwaltungskonsole.
1. Wählen Sie **Kennwortwiederherstellung** aus.
1. Kopieren Sie den eindeutigen Bezeichner.
1. Wechseln Sie zur Defender für IoT-Seite **Sites and Sensors** (Standorte und Sensoren), und wählen Sie die Registerkarte **Recover my password** (Kennwort wiederherstellen) aus.
1. Geben Sie den eindeutigen Bezeichner ein, und wählen Sie **Recover** (Wiederherstellen) aus. Die Aktivierungsdatei wird heruntergeladen.
1. Wechseln Sie zur Seite **Password Recovery** (Kennwortwiederherstellung), und laden Sie die Aktivierungsdatei hoch.
1. Wählen Sie **Weiter** aus.
 
   Sie erhalten nun Ihren Benutzernamen und ein neues vom System generiertes Kennwort.

> [!NOTE]
> Der Sensor ist mit dem Abonnement verknüpft, mit dem er ursprünglich verbunden war. Sie können das Kennwort nur wiederherstellen, wenn Sie dasselbe Abonnement verwenden, zu dem es gehört.

## <a name="update-the-software-version"></a>Aktualisieren der Softwareversion

Im folgenden Verfahren wird beschrieben, wie Sie die Softwareversion der lokalen Verwaltungskonsole aktualisieren. Der Aktualisierungsvorgang dauert ungefähr 30 Minuten.

Wenn Sie mit einer lokalen Verwaltungskonsole und verwalteten Sensoren arbeiten, müssen Sie **zuerst die Verwaltungskonsole aktualisieren**.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

1. Wechseln Sie zu Defender für IoT.

1. Navigieren Sie zur Seite **Updates**.

1. Wählen Sie im Abschnitt für die lokale Verwaltungskonsole eine Version aus.

1. Wählen Sie **Herunterladen** aus, und speichern Sie die Datei.

1. Melden Sie sich bei der lokalen Verwaltungskonsole an, und wählen Sie im seitlichen Menü die Option **Systemeinstellungen** aus.

1. Wählen Sie im Bereich **Versionsupdate** die Option **Update** aus.

1. Wählen Sie die Datei aus, die Sie von der Seite **Updates** in Defender für IoT heruntergeladen haben.

## <a name="mail-server-settings"></a>Einstellungen des E-Mail-Servers

Legen Sie die Einstellungen des SMTP-Mailservers für die lokale Verwaltungskonsole fest.

So definieren Sie die Einstellungen

1. Melden Sie sich mit administrativen Anmeldeinformationen bei der Befehlszeilenschnittstelle der lokalen Verwaltungskonsole an.
1. Geben Sie ```nano /var/cyberx/properties/remote-interfaces.properties```ein.
1. Drücken Sie die EINGABETASTE. Die folgende Eingabeaufforderung wird angezeigt.
   `mail.smtp_server=`
   `mail.port=25`
   `mail.sender=`
1. Geben Sie den SMTP-Servernamen und den Absender ein, und drücken Sie die EINGABETASTE.

## <a name="see-also"></a>Siehe auch

[Verwalten von Sensoren über die Verwaltungskonsole](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Verwalten einzelner Sensoren](how-to-manage-individual-sensors.md)
