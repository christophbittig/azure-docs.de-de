---
title: Einrichten einer Azure Migrate-Appliance für physische Server
description: Hier erfahren Sie, wie Sie eine Azure Migrate-Appliance für die Ermittlung und Bewertung physischer Server einrichten.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 171b03e53266949f1ceae79dd0bbeae7ab146eda
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500448"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Einrichten einer Appliance für physische Server

In diesem Artikel wird beschrieben, wie Sie die Azure Migrate-Appliance einrichten, wenn Sie mithilfe des Azure Migrate-Tools für die Ermittlung und Bewertung physische Server bewerten.

Die Azure Migrate-Appliance ist eine einfache Appliance, die vom Azure Migrate-Tool zur Ermittlung und Bewertung für folgende Aufgaben verwendet wird:

- Ermitteln lokaler Server
- Senden von Meta- und Leistungsdaten für ermittelte Server an das Azure Migrate-Tool für die Ermittlung und Bewertung

[Erfahren Sie mehr](migrate-appliance.md) über die Azure Migrate-Appliance.


## <a name="appliance-deployment-steps"></a>Schritte für die Appliancebereitstellung

Die Einrichtung der Appliance umfasst Folgendes:

1. Geben Sie einen Appliancenamen ein, und generieren Sie einen Projektschlüssel im Portal.
2. Herunterladen einer gezippten Datei mit dem Azure Migrate-Installationsskript aus dem Azure-Portal.
3. Extrahieren der Inhalte aus der gezippten Datei. Starten der PowerShell-Konsole mit Administratorrechten.
4. Führen Sie das PowerShell-Skript zum Starten des Appliance-Konfigurations-Manager aus.
5. Führen Sie die Erstkonfiguration der Appliance aus, und registrieren Sie sie unter Verwendung des Projektschlüssels im Projekt.

### <a name="generate-the-project-key"></a>Generieren des Projektschlüssels

1. Wählen Sie unter **Migrationsziele** > **Windows, Linux and SQL Servers (Windows-/Linux-Server und SQL Server-Instanzen)**  > **Azure Migrate: Ermittlung und Bewertung** die Option **Ermitteln** aus.
2. Wählen Sie unter **Discover servers (Server ermitteln)**  > **Sind Ihre Server virtualisiert?** die Option **Physische oder andere (AWS, GCP, Xen usw.)** aus.
3. Geben Sie unter **1: Generate project key** (Projektschlüssel generieren) einen Namen für die Azure Migrate-Appliance ein, die Sie für die Ermittlung physischer oder virtueller Server einrichten. Für den Namen können bis zu 14 alphanumerische Zeichen angegeben werden.
1. Klicken Sie auf **Schlüssel generieren**, um mit der Erstellung der erforderlichen Azure-Ressourcen zu beginnen. Schließen Sie die Seite „Discover servers“ (Server ermitteln) nicht, während die Ressourcen erstellt werden.
1. Nach der erfolgreichen Erstellung der Azure-Ressourcen wird ein **Projektschlüssel** generiert.
1. Kopieren Sie den Schlüssel, da Sie ihn benötigen, um die Registrierung der Appliance während der Konfiguration abzuschließen.

   ![Auswahloptionen für „Schlüssel generieren“](./media/tutorial-assess-physical/generate-key-physical-1.png)

### <a name="download-the-installer-script"></a>Herunterladen des Installationsskripts

Klicken Sie in **2: Azure Migrate-Appliance herunterladen** auf **Herunterladen**.

### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Server, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Überprüfen Sie die aktuelle Applianceversion und die Hashwerte:

    **Download** | **Hashwert**
    --- | ---
    [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140334) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> Dasselbe Skript kann verwendet werden, um die physische Appliance für die öffentliche Azure-Cloud oder die Azure Government-Cloud einzurichten.

### <a name="run-the-azure-migrate-installer-script"></a>Ausführen des Azure Migrate-Installationsskripts

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Server, der die Appliance hostet.  Führen Sie das Skript nicht auf einem Server mit einer vorhandenen Azure Migrate-Appliance aus.
2. Starten Sie PowerShell auf dem oben genannten Server mit Administratorberechtigungen (erhöhten Rechten).
3. Ändern Sie das PowerShell-Verzeichnis in den Ordner, in den die Inhalte der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.
4. Führen Sie das Skript mit dem Namen **AzureMigrateInstaller.ps1** aus, indem Sie den folgenden Befehl ausführen:

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. Treffen Sie eine Auswahl aus den Szenario-, Cloud- und Konnektivitätsoptionen, um eine Appliance mit der gewünschten Konfiguration bereitzustellen. Mit der nachfolgend gezeigten Auswahl richten Sie beispielsweise eine Appliance zum Ermitteln und Bewerten **physischer Server** _(oder in anderen Clouds wie AWS, GCP, Xen, usw. ausgeführter Server)_ für ein Azure Migrate-Projekt mit **Standardkonnektivität** _(öffentlicher Endpunkt)_ in einer **öffentlichen Azure-Cloud** ein.

    :::image type="content" source="./media/tutorial-discover-physical/script-physical-default-1.png" alt-text="Screenshot: Einrichten einer Appliance mit der gewünschten Konfiguration":::

6. Das Installationsskript führt folgende Schritte aus:

    - Installation von Agents und einer Webanwendung.
    - Installation von Windows-Rollen, darunter beispielsweise Windows-Aktivierungsdienst, IIS und PowerShell ISE.
    - Download und Installation eines wiederbeschreibbaren IIS-Moduls.
    - Aktualisierung eines Registrierungsschlüssels (HKLM) mit dauerhaften Einstellungsdetails für Azure Migrate.
    - Erstellung der folgenden Dateien in diesem Pfad:
        - **Konfigurationsdateien**: %Programdata%\Microsoft Azure\Config
        - **Protokolldateien**: %Programdata%\Microsoft Azure\Logs

Nach der erfolgreichen Ausführung des Skripts wird der Appliance-Konfigurations-Manager automatisch gestartet.

> [!NOTE]
> Bei Problemen können Sie zum Troubleshooting unter „C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Zeitstempel</em>.log“ auf die Skriptprotokolle zugreifen.

### <a name="verify-appliance-access-to-azure"></a>Überprüfen des Appliancezugriffs auf Azure

Stellen Sie sicher, dass die Appliance eine Verbindung mit Azure-URLs für [öffentliche](migrate-appliance.md#public-cloud-urls) Clouds und [Azure Government](migrate-appliance.md#government-cloud-urls)-Clouds herstellen kann.

### <a name="configure-the-appliance"></a>Konfigurieren der Appliance

Führen Sie die Ersteinrichtung der Appliance durch.

1. Öffnen Sie in einem Browser auf einem beliebigen Computer, der eine Verbindung mit der Appliance herstellen kann, die URL der Appliance-Web-App: **https://*Name oder IP-Adresse der Appliance*: 44368**.

   Alternativ können Sie auch auf dem Desktop auf die App-Verknüpfung klicken, um die App zu öffnen.
2. Akzeptieren Sie die **Lizenzbedingungen**, und lesen Sie die Drittanbieterinformationen.
1. Gehen Sie in der Web-App unter **Erforderliche Komponenten einrichten** wie folgt vor:
    - **Konnektivität**: Die App überprüft, ob der Server über Internetzugriff verfügt. Wenn der Server einen Proxy verwendet:
        - Klicken Sie auf **Proxy einrichten**, um die Proxyadresse (im Format http://ProxyIPAddress oder http://ProxyFQDN) ) und den Überwachungsport anzugeben.
        - Geben Sie die Anmeldeinformationen an, wenn der Proxy eine Authentifizierung erfordert.
        - Es werden nur HTTP-Proxys unterstützt.
        - Wenn Sie Proxydetails hinzugefügt oder den Proxy und/oder die Authentifizierung deaktiviert haben, klicken Sie auf **Speichern**, um die Konnektivitätsprüfung erneut auszulösen.
    - **Uhrzeitsynchronisierung**: Die Uhrzeit wird überprüft. Die Uhrzeit der Appliance muss mit der Internetzeit synchronisiert werden, damit die Ermittlung ordnungsgemäß funktioniert.
    - **Updates installieren**: Das Tool „Azure Migrate: Ermittlung und Bewertung“ überprüft, ob auf der Appliance die neuesten Updates installiert sind. Nachdem die Prüfung abgeschlossen ist, können Sie auf **Appliancedienste anzeigen** klicken, um den Status und die Versionen der auf der Appliance ausgeführten Komponenten anzuzeigen.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrieren der Appliance bei Azure Migrate

1. Fügen Sie den aus dem Portal kopierten **Projektschlüssel** ein. Wenn Sie den Schlüssel nicht haben, wechseln Sie zu **Azure Migrate: Ermittlung und Bewertung > Ermitteln > Vorhandene Appliances verwalten**, wählen Sie den Appliancenamen aus, den Sie bei der Generierung des Schlüssels angegeben haben, und kopieren Sie den entsprechenden Schlüssel.
1. Für die Authentifizierung bei Azure benötigen Sie einen Gerätecode. Wenn Sie auf **Anmelden** klicken, wird ein modales Dialogfeld mit dem Gerätecode angezeigt. Dies ist in der folgenden Abbildung dargestellt.

    ![Modales Dialogfeld mit Gerätecode](./media/tutorial-discover-vmware/device-code.png)

1. Klicken Sie auf **Copy code & Login** (Code kopieren und anmelden), um den Gerätecode zu kopieren und eine Azure-Anmeldeaufforderung in einer neuen Browserregisterkarte zu öffnen. Sollte keine Anmeldung angezeigt werden, vergewissern Sie sich, dass Sie den Popupblocker im Browser deaktiviert haben.
1. Fügen Sie auf der neuen Registerkarte den Gerätecode ein, und melden Sie sich mit Ihrem Azure-Benutzernamen und dem zugehörigen Kennwort an.
   
   Die Anmeldung mit einer PIN wird nicht unterstützt.
3. Falls Sie die Registerkarte für die Anmeldung versehentlich schließen, ohne die Anmeldung durchzuführen, müssen Sie die Browserregisterkarte des Appliance-Konfigurations-Managers aktualisieren, um die Schaltfläche „Anmelden“ wieder zu aktivieren.
1. Wechseln Sie nach der erfolgreichen Anmeldung wieder zur vorherigen Registerkarte mit dem Appliance-Konfigurations-Manager.
4. Wenn das für die Protokollierung verwendete Azure-Benutzerkonto über die richtigen [Berechtigungen](./tutorial-discover-physical.md) für die während der Schlüsselgenerierung erstellten Azure-Ressourcen verfügt, wird die Registrierung der Appliance initiiert.
1. Nachdem die Appliance erfolgreich registriert wurde, können Sie die Registrierungsdetails anzeigen, indem Sie auf **Details anzeigen** klicken.


## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Stellen Sie nun eine Verbindung zwischen der Appliance und den zu ermittelnden physischen Servern her, und starten Sie die Ermittlung.

1. Wählen Sie in **Schritt 1: Angeben von Anmeldeinformationen für die Ermittlung von physischen oder virtuellen Windows- und Linux-Servern** die Option **Anmeldeinformationen hinzufügen** aus.
1. Wählen Sie für Windows-Server den Quelltyp **Windows-Server** aus, geben Sie einen Anzeigenamen für die Anmeldeinformationen ein, und fügen Sie den Benutzernamen und das Kennwort hinzu. Klicken Sie auf **Speichern**.
1. Wenn Sie die kennwortbasierte Authentifizierung für Linux-Server verwenden, wählen Sie den Quelltyp **Linux Server (Password-based)** (Linux-Server (kennwortbasiert)) aus, geben Sie einen Anzeigenamen für die Anmeldeinformationen ein, und fügen Sie den Benutzernamen und das Kennwort hinzu. Klicken Sie auf **Speichern**.
1. Wenn Sie die auf SSH-Schlüsseln basierende Authentifizierung für Linux-Server verwenden, wählen Sie den Quelltyp **Linux Server (SSH key-based)** (Linux-Server (auf SSH-Schlüsseln basierend)) aus, geben Sie einen Anzeigenamen für die Anmeldeinformationen ein, fügen Sie den Benutzernamen hinzu, suchen Sie nach der Datei mit dem privaten SSH-Schlüssel, und wählen Sie diese aus. Klicken Sie auf **Speichern**.

    - Azure Migrate unterstützt den privaten SSH-Schlüssel, der vom Befehl „ssh-keygen“ mithilfe von RSA-, DSA-, ECDSA- und ed25519-Algorithmen generiert wird.
    - Zurzeit unterstützt Azure Migrate keinen auf einer Passphrase basierenden SSH-Schlüssel. Verwenden Sie einen SSH-Schlüssel ohne eine Passphrase.
    - Derzeit wird die von PuTTY generierte Datei mit dem privaten SSH-Schlüssel von Azure Migrate nicht unterstützt.
    - Azure Migrate unterstützt das OpenSSH-Format der Datei mit dem privaten SSH-Schlüssel. Dies ist hier dargestellt:
    
    ![Unterstütztes Format für privaten SSH-Schlüssel](./media/tutorial-discover-physical/key-format.png)
1. Wenn Sie mehrere Anmeldeinformationen gleichzeitig hinzufügen möchten, klicken Sie auf **Weitere hinzufügen**, um die Angeben zu speichern und weitere Anmeldeinformationen hinzuzufügen. Es werden mehrere Anmeldeinformationen für die Ermittlung physischer Server unterstützt.
1. Klicken Sie in **Schritt 2: Bereitstellen von Details zu physischen oder virtuellen Servern** auf **Ermittlungsquelle hinzufügen**, um den **IP-Adresse/FQDN**-Wert des Servers und den Anzeigenamen für Anmeldeinformationen zum Herstellen einer Verbindung mit dem Server anzugeben.
1. Sie können entweder jeweils **ein einzelnes Element** oder **mehrere Elemente** in einem Schritt hinzufügen. Es besteht auch die Möglichkeit, Serverdetails über **CSV importieren** bereitzustellen.

    ![Auswahloptionen zum Hinzufügen der Ermittlungsquelle](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Wenn Sie auf **Add single item** (Einzelnes Element hinzufügen) klicken, können Sie den Betriebssystemtyp auswählen, den Anzeigenamen für die Anmeldeinformationen angeben, den **IP-Adresse/FQDN**-Wert des Servers hinzufügen und dann auf **Speichern** klicken.
    - Wenn Sie auf **Add multiple items** (Mehrere Elemente hinzufügen) klicken, können Sie mehrere Datensätze auf einmal hinzufügen, indem Sie **IP-Adresse/FQDN** für den Server mit dem Anzeigenamen für die Anmeldeinformationen im Textfeld angeben. Überprüfen** Sie die hinzugefügten Einträge, und klicken Sie auf **Speichern**.
    - Wenn Sie **CSV importieren** _(standardmäßig ausgewählt)_ auswählen, können Sie eine CSV-Vorlagendatei herunterladen, die Datei mit dem **IP-Adresse/FQDN**-Wert des Servers und einem Anzeigenamen für die Anmeldeinformationen auffüllen. Importieren Sie die Datei dann in die Appliance, **überprüfen** Sie die Datensätze in der Datei, und klicken Sie auf **Speichern**.

1. Wenn Sie auf „Speichern“ klicken, versucht die Appliance, die Verbindung zu den hinzugefügten Servern zu überprüfen, und zeigt den **Überprüfungszustand** in der Tabelle für jeden Server an.
    - Sollte bei der Überprüfung eines Servers ein Fehler auftreten, sehen Sie sich den Fehler an, indem Sie auf **Fehler bei der Überprüfung** in der Spalte „Status“ klicken. Beheben Sie das Problem, und wiederholen Sie die Überprüfung.
    - Um einen Server zu entfernen, klicken Sie auf **Löschen**.
1. Sie können die Verbindung mit Servern jederzeit vor Beginn der Ermittlung **erneut überprüfen**.
1. Klicken Sie auf **Ermittlung starten**, um die Ermittlung der erfolgreich überprüften Server zu starten. Nachdem die Ermittlung erfolgreich gestartet wurde, können Sie den Ermittlungsstatus für jeden Server in der Tabelle überprüfen.


Daraufhin wird die Ermittlung gestartet. Es dauert ca. 2 Minuten pro Server, bis Metadaten des ermittelten Servers im Azure-Portal angezeigt werden.

## <a name="verify-servers-in-the-portal"></a>Überprüfen von Servern im Portal

Nach Abschluss der Ermittlung können Sie überprüfen, ob die Server im Portal angezeigt werden.

1. Öffnen Sie das Azure Migrate-Dashboard.
2. Klicken Sie auf **Azure Migrate - Windows, Linux and SQL Servers (Azure Migrate: Windows-/Linux-Server und SQL Server-Instanzen)**  > **Azure Migrate: Ermittlung und Bewertung** und dann auf das Symbol mit der Anzahl für **Ermittelte Server**.


## <a name="next-steps"></a>Nächste Schritte

Testen Sie die [Bewertung physischer Server](tutorial-assess-physical.md) mit dem Azure Migrate-Tool für die Ermittlung und Bewertung.
