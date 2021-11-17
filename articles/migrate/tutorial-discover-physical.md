---
title: Ermitteln physischer Server mit der Ermittlung und Bewertung von Azure Migrate
description: Hier erfahren Sie, wie Sie lokale physische Server mit der Ermittlung und Bewertung von Azure Migrate ermitteln.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 11/12/2021
ms.custom: mvc
ms.openlocfilehash: 1a12e8ddf419b4e7b26d82e76a59de43fb73ddb4
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402407"
---
# <a name="tutorial-discover-physical-servers-with-azure-migrate-discovery-and-assessment"></a>Tutorial: Ermitteln physischer Server mit der Ermittlung und Bewertung von Azure Migrate

Im Rahmen der Migration zu Azure ermitteln Sie Ihre Server für die Bewertung und Migration.

In diesem Tutorial wird gezeigt, wie Sie lokale physische Server mit dem Azure Migrate-Tool zur Ermittlung und Bewertung und einer einfachen Azure Migrate-Appliance ermitteln. Sie stellen die Appliance als physischen Server bereit, um kontinuierlich Server- und Leistungsmetadaten zu ermitteln.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten eines Azure-Kontos
> * Vorbereiten von physischen Servern für die Ermittlung
> * Erstellt ein Projekt.
> * Einrichten der Azure Migrate-Appliance
> * Starten der kontinuierlichen Ermittlung

> [!NOTE]
> Tutorials zeigen den schnellsten Weg zum Ausprobieren eines Szenarios, dabei kommen die Standardoptionen zum Einsatz.  

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, überprüfen Sie, ob die folgenden Voraussetzungen erfüllt sind:

**Anforderung** | **Details**
--- | ---
**Appliance** | Sie benötigen einen Server für die Ausführung der Azure Migrate-Appliance. Der Server muss über Folgendes verfügen:<br/><br/> - Installation von Windows Server 2016.<br/> _(Derzeit wird die Bereitstellung einer Appliance nur unter Windows Server 2016 unterstützt.)_<br/><br/> - 16 GB RAM, 8 virtuelle CPUs, ungefähr 80 GB Speicherplatz auf dem Datenträger<br/><br/> - eine statische oder dynamische IP-Adresse sowie Internetzugriff (entweder direkt oder über einen Proxy)<br/><br/> - Ausgehende Internetverbindung mit den erforderlichen [URLs](migrate-appliance.md#url-access) von der Appliance aus
**Windows-Server** | Lassen Sie eingehende Verbindungen am WinRM-Port 5985 (HTTP) zu, sodass die Appliance Konfigurations- und Leistungsmetadaten pullen kann.
**Linux-Server** | Lassen Sie eingehende Verbindungen über Port 22 (TCP) zu.

> [!NOTE]
> Die Installation der Azure Migrate-Appliance auf einem Server, auf dem die [Replikationsappliance](migrate-replication-appliance.md) oder der Mobilitätsdienst-Agent installiert ist, wird nicht unterstützt.  Vergewissern Sie sich, dass der Applianceserver nicht bereits verwendet wurde, um die Replikationsappliance einzurichten, bzw. dass der Mobilitätsdienst-Agent auf dem Server installiert ist.

## <a name="prepare-an-azure-user-account"></a>Vorbereiten eines Azure-Benutzerkontos

Zum Erstellen eines Projekts und Registrieren der Azure Migrate-Appliance benötigen Sie ein Konto mit den folgenden Berechtigungen:
- Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ für ein Azure-Abonnement
- Berechtigungen zum Registrieren von AAD-Apps (Azure Active Directory)

Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements. Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um die Berechtigungen wie folgt zuzuweisen:

1. Suchen Sie im Azure-Portal nach „Abonnements“, und wählen Sie unter **Dienste** die Option **Abonnements** aus.

    ![Suchfeld, um nach dem Azure-Abonnement zu suchen](./media/tutorial-discover-physical/search-subscription.png)

2. Wählen Sie auf der Seite **Abonnements** das Abonnement aus, in dem Sie ein Projekt erstellen möchten.
3. Wählen Sie im Abonnement die Option **Zugriffssteuerung (IAM)**  > **Zugriff überprüfen** aus.
4. Suchen Sie unter **Zugriff überprüfen** nach dem entsprechenden Benutzerkonto.
5. Klicken Sie unter **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

    ![Suchen nach einem Benutzerkonto, um den Zugriff zu überprüfen und eine Rolle zuzuweisen](./media/tutorial-discover-physical/azure-account-access.png)

6. Wählen Sie unter **Rollenzuweisung hinzufügen** die Rolle „Mitwirkender“ oder „Besitzer“ und das Konto aus (in unserem Beispiel „azmigrateuser“). Klicken Sie anschließend auf **Speichern**.

    ![Die Seite „Rollenzuweisung“ wird geöffnet, auf der Sie dem Konto eine Rolle zuweisen können.](./media/tutorial-discover-physical/assign-role.png)

1. Ihr Azure-Konto benötigt **Berechtigungen zum Registrieren von AAD-Apps**, um die Appliance registrieren zu können.
1. Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Benutzer** > **Benutzereinstellungen**.
1. Vergewissern Sie sich unter **Benutzereinstellungen**, dass Azure AD-Benutzer Anwendungen registrieren können (standardmäßig auf **Ja** festgelegt).

    ![Überprüfen unter „Benutzereinstellungen“, ob Benutzer Active Directory-Apps registrieren können](./media/tutorial-discover-physical/register-apps.png)

9. Wenn die Einstellungen für „App-Registrierungen“ auf „Nein“ festgelegt sind, fordern Sie den Mandantenadministrator/globalen Administrator auf, die erforderliche Berechtigung zuzuweisen. Alternativ kann der Mandantenadministrator/globale Administrator einem Konto die Rolle **Anwendungsentwickler** zuweisen, um die Registrierung von AAD-Apps zuzulassen. [Weitere Informationen](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-physical-servers"></a>Vorbereiten physischer Server

Richten Sie ein Konto ein, das von der Appliance für den Zugriff auf die physischen Server verwendet werden kann.

**Windows-Server**

- Verwenden Sie für Windows-Server ein Domänenkonto für in die Domäne eingebundene Server und ein lokales Konto für nicht in die Domäne eingebundene Server. 
- Das Benutzerkonto sollte diesen Gruppen hinzugefügt werden: Remoteverwaltungsbenutzer, Leistungsüberwachungsbenutzer und Leistungsprotokollbenutzer. 
- Wenn die Gruppe „Remoteverwaltungsbenutzer“ nicht vorhanden ist, fügen Sie der Gruppe ein Benutzerkonto hinzu: **WinRMRemoteWMIUsers_** .
- Das Konto benötigt diese Berechtigungen, damit die Appliance eine CIM-Verbindung mit dem Server herstellen und die erforderlichen Konfigurations- und Leistungsmetadaten aus den hier aufgeführten WMI-Klassen pullen kann.
- In einigen Fällen werden durch das Hinzufügen des Kontos zu diesen Gruppen unter Umständen nicht die erforderlichen Daten aus WMI-Klassen zurückgegeben, da das Konto möglicherweise nach [UAC](/windows/win32/wmisdk/user-account-control-and-wmi) gefiltert ist. Um die UAC-Filterung außer Kraft zu setzen, muss das Benutzerkonto über die erforderlichen Berechtigungen für den CIMV2-Namespace und die untergeordneten Namespaces auf dem Zielserver verfügen. Sie können die [hier](troubleshoot-appliance.md) beschriebenen Schritte ausführen, um die erforderlichen Berechtigungen zu aktivieren.

    > [!Note]
    > Stellen Sie unter Windows Server 2008 und 2008 R2 sicher, dass WMF 3.0 auf den Servern installiert ist.

**Linux-Server**

- Sie benötigen ein root-Konto auf den Linux-Servern, die Sie ermitteln möchten. Alternativ können Sie ein Benutzerkonto mit sudo-Berechtigungen bereitstellen.
- Die Unterstützung zum Hinzufügen eines Benutzerkontos mit sudo-Zugriff wird standardmäßig mit dem neuen Installationsskript für die Appliance bereitgestellt, das seit dem 20. Juli 2021 aus dem Portal heruntergeladen werden kann.
- Für ältere Appliances können Sie die Funktion anhand der folgenden Schritte aktivieren:
    1. Öffnen Sie auf dem Server, auf dem die Appliance ausgeführt wird, den Registrierungs-Editor.
    1. Navigieren Sie zu HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
    1. Erstellen Sie den Registrierungsschlüssel „isSudo“ mit dem DWORD-Wert 1.

    :::image type="content" source="./media/tutorial-discover-physical/issudo-reg-key.png" alt-text="Screenshot: Aktivieren der sudo-Unterstützung.":::

- Wenn Sie die Konfigurations- und Leistungsmetadaten vom Zielserver ermitteln möchten, müssen Sie sudo-Zugriff für die [hier](migrate-appliance.md#linux-server-metadata) aufgeführten Befehle aktivieren. Stellen Sie sicher, dass Sie „NOPASSWD“ für das Konto aktiviert haben, um die erforderlichen Befehle auszuführen, ohne bei jedem Aufruf des sudo-Befehls ein Kennwort eingeben zu müssen.
- Die folgenden Linux-Betriebssystemdistributionen werden für die Ermittlung durch Azure Migrate mit einem Konto mit sudo-Zugriff unterstützt:

    Betriebssystem | Versionen 
    --- | ---
    Red Hat Enterprise Linux | 6, 7, 8
    Cent OS | 6.6, 8.2
    Ubuntu | 14.04, 16.04, 18.04
    SUSE Linux | 11.4, 12.4
    Debian | 7, 10
    Amazon Linux | 2.0.2021
    CoreOS-Container | 2345.3.0

- Wenn Sie kein root-Konto oder Benutzerkonto mit sudo-Zugriff bereitstellen können, können Sie den Registrierungsschlüssel „isSudo“ in der Registrierung „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance“ auf den Wert „0“ festlegen und mithilfe der folgenden Befehle ein Konto mit den erforderlichen Funktionen angeben, bei dem es sich nicht um das root-Konto handelt:

**Befehl** | **Zweck**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(wenn „/usr/sbin/fdisk“ nicht vorhanden ist)_ | Erfassen der Daten zur Datenträgerkonfiguration
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Erfassen der Daten zur Datenträgerleistung
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Erfassen der BIOS-Seriennummer
chmod a+r /sys/class/dmi/id/product_uuid | Erfassen der BIOS-GUID

## <a name="set-up-a-project"></a>Einrichten eines Projekts

Richten Sie ein neues Projekt ein.

1. Wählen Sie im Azure-Portal **Alle Dienste** aus, und suchen Sie nach **Azure Migrate**.
2. Wählen Sie unter **Dienste** die Option **Azure Migrate** aus.
3. Wählen Sie unter **Übersicht** die Option **Create project** (Projekt erstellen) aus.
5. Wählen Sie unter **Create project** (Projekt erstellen) Ihr Azure-Abonnement und die Ressourcengruppe aus. Falls noch keine vorhanden ist, erstellen Sie eine Ressourcengruppe.
6. Geben Sie unter **Projektdetails** den Projektnamen und die geografische Region an, in der Sie das Projekt erstellen möchten. Beachten Sie die unterstützten geografischen Regionen für [öffentliche Clouds](migrate-support-matrix.md#supported-geographies-public-cloud) und [Azure Government-Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Felder für Projektname und Region](./media/tutorial-discover-physical/new-project.png)

7. Klicken Sie auf **Erstellen**.
8. Warten Sie einige Minuten, bis das Projekt bereitgestellt wurde. Das Tool **Azure Migrate: Ermittlung und Bewertung** wird dem neuen Projekt standardmäßig hinzugefügt.

![Seite mit dem standardmäßig hinzugefügten Serverbewertungstool](./media/tutorial-discover-physical/added-tool.png)

> [!NOTE]
> Wenn Sie bereits ein Projekt erstellt haben, können Sie dieses Projekt zum Registrieren zusätzlicher Appliances verwenden, um mehr Server zu ermitteln und zu bewerten. [Weitere Informationen](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Einrichten der Appliance

Die Azure Migrate-Appliance ermittelt Server und sendet Konfigurations- und Leistungsmetadaten zu Servern an Azure Migrate. Die Appliance kann durch Ausführung eines PowerShell-Skripts eingerichtet werden, das aus dem Projekt heruntergeladen werden kann.

Die Einrichtung der Appliance umfasst Folgendes:

1. Geben Sie einen Appliancenamen ein, und generieren Sie einen Projektschlüssel im Portal.
2. Herunterladen einer gezippten Datei mit dem Azure Migrate-Installationsskript aus dem Azure-Portal.
3. Extrahieren der Inhalte aus der gezippten Datei. Starten der PowerShell-Konsole mit Administratorrechten.
4. Führen Sie das PowerShell-Skript zum Starten des Appliance-Konfigurations-Manager aus.
5. Führen Sie die Erstkonfiguration der Appliance aus, und registrieren Sie sie unter Verwendung des Projektschlüssels im Projekt.

### <a name="1-generate-the-project-key"></a>1. Generieren des Projektschlüssels

1. Wählen Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Ermittlung und Bewertung** die Option **Ermitteln** aus.
2. Wählen Sie unter **Discover servers (Server ermitteln)**  > **Sind Ihre Server virtualisiert?** die Option **Physische oder andere (AWS, GCP, Xen usw.)** aus.
3. Geben Sie unter **1: Generate project key** (Projektschlüssel generieren) einen Namen für die Azure Migrate-Appliance ein, die Sie für die Ermittlung physischer oder virtueller Server einrichten. Für den Namen können bis zu 14 alphanumerische Zeichen angegeben werden.
1. Klicken Sie auf **Schlüssel generieren**, um mit der Erstellung der erforderlichen Azure-Ressourcen zu beginnen. Schließen Sie die Seite „Discover servers“ (Server ermitteln) nicht, während die Ressourcen erstellt werden.
1. Nach der erfolgreichen Erstellung der Azure-Ressourcen wird ein **Projektschlüssel** generiert.
1. Kopieren Sie den Schlüssel, da Sie ihn benötigen, um die Registrierung der Appliance während der Konfiguration abzuschließen.

  [ ![Auswahloptionen für „Schlüssel generieren“](./media/tutorial-assess-physical/generate-key-physical-inline-1.png)](./media/tutorial-assess-physical/generate-key-physical-expanded-1.png#lightbox)

### <a name="2-download-the-installer-script"></a>2. Herunterladen des Installationsskripts

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
> Das gleiche Skript kann verwendet werden, um eine physische Appliance für die öffentliche Azure-Cloud oder die Azure Government-Cloud mit Konnektivität für öffentliche oder private Endpunkte einzurichten.


### <a name="3-run-the-azure-migrate-installer-script"></a>3. Ausführen des Azure Migrate-Installationsskripts

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Server, der die Appliance hostet.  Führen Sie das Skript nicht auf einem Server mit einer vorhandenen Azure Migrate-Appliance aus.

2. Starten Sie PowerShell auf dem oben genannten Server mit Administratorberechtigungen (erhöhten Rechten).

3. Ändern Sie das PowerShell-Verzeichnis in den Ordner, in den die Inhalte der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.

4. Führen Sie das Skript `AzureMigrateInstaller.ps1` mit folgendem Befehl aus:

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. Treffen Sie eine Auswahl aus den Szenario-, Cloud- und Konnektivitätsoptionen, um eine Appliance mit der gewünschten Konfiguration bereitzustellen. Mit der nachfolgend gezeigten Auswahl richten Sie beispielsweise eine Appliance zum Ermitteln und Bewerten **physischer Server** _(oder in anderen Clouds wie AWS, GCP, Xen, usw. ausgeführter Server)_ für ein Azure Migrate-Projekt mit **Standardkonnektivität** _(öffentlicher Endpunkt)_ in einer **öffentlichen Azure-Cloud** ein.

   :::image type="content" source="./media/tutorial-discover-physical/script-physical-default-inline.png" alt-text="Screenshot: Einrichten einer Appliance mit der gewünschten Konfiguration" lightbox="./media/tutorial-discover-physical/script-physical-default-expanded.png":::

6. Das Installationsskript führt folgende Schritte aus:

   - Installation von Agents und einer Webanwendung.
   - Installation von Windows-Rollen, darunter beispielsweise Windows-Aktivierungsdienst, IIS und PowerShell ISE.
   - Download und Installation eines wiederbeschreibbaren IIS-Moduls.
   - Aktualisierung eines Registrierungsschlüssels (HKLM) mit dauerhaften Einstellungsdetails für Azure Migrate.
   - Erstellung der folgenden Dateien in diesem Pfad:
     - **Konfigurationsdateien:** `%ProgramData%\Microsoft Azure\Config`
     - **Protokolldateien:** `%ProgramData%\Microsoft Azure\Logs`

Nach der erfolgreichen Ausführung des Skripts wird der Appliance-Konfigurations-Manager automatisch gestartet.

> [!NOTE]
> Bei Problemen können Sie zum Troubleshooting unter „C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Zeitstempel</em>.log“ auf die Skriptprotokolle zugreifen.

### <a name="verify-appliance-access-to-azure"></a>Überprüfen des Appliancezugriffs auf Azure

Stellen Sie sicher, dass die Appliance eine Verbindung mit Azure-URLs für [öffentliche](migrate-appliance.md#public-cloud-urls) Clouds und [Azure Government](migrate-appliance.md#government-cloud-urls)-Clouds herstellen kann.

### <a name="4-configure-the-appliance"></a>4. Konfigurieren der Appliance

Führen Sie die Ersteinrichtung der Appliance durch.

1. Öffnen Sie in einem Browser auf einem beliebigen Server, der eine Verbindung mit der Appliance herstellen kann, die URL der Appliance-Web-App: **https://*Name oder IP-Adresse der Appliance*: 44368**.

   Alternativ können Sie auch auf dem Desktop auf die App-Verknüpfung klicken, um die App zu öffnen.
1. Akzeptieren Sie die **Lizenzbedingungen**, und lesen Sie die Drittanbieterinformationen.
1. Gehen Sie in der Web-App unter **Erforderliche Komponenten einrichten** wie folgt vor:
   - **Konnektivität**: Die App überprüft, ob der Server über Internetzugriff verfügt. Wenn der Server einen Proxy verwendet:
     - Klicken Sie auf **Proxy einrichten**, um die Proxyadresse im Format http://ProxyIPAddress oder http://ProxyFQDN) und den überwachenden Port anzugeben.
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
1. Fügen Sie auf der neuen Registerkarte den Gerätecode ein, und melden Sie sich mit Ihrem Azure-Benutzernamen und dem zugehörigen Kennwort an. Die Anmeldung mit einer PIN wird nicht unterstützt.
1. Falls Sie die Registerkarte für die Anmeldung versehentlich schließen, ohne die Anmeldung durchzuführen, müssen Sie die Browserregisterkarte des Appliance-Konfigurations-Managers aktualisieren, um die Schaltfläche „Anmelden“ wieder zu aktivieren.
1. Wechseln Sie nach der erfolgreichen Anmeldung wieder zur vorherigen Registerkarte mit dem Appliance-Konfigurations-Manager.
1. Wenn das für die Protokollierung verwendete Azure-Benutzerkonto über die richtigen [Berechtigungen]() für die während der Schlüsselgenerierung erstellten Azure-Ressourcen verfügt, wird die Registrierung der Appliance initiiert.
1. Nachdem die Appliance erfolgreich registriert wurde, können Sie die Registrierungsdetails anzeigen, indem Sie auf **Details anzeigen** klicken.

## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Stellen Sie nun eine Verbindung zwischen der Appliance und den zu ermittelnden physischen Servern her, und starten Sie die Ermittlung.

1. Wählen Sie in **Schritt 1: Angeben von Anmeldeinformationen für die Ermittlung von physischen oder virtuellen Windows- und Linux-Servern** die Option **Anmeldeinformationen hinzufügen** aus.
1. Wählen Sie für Windows-Server den Quelltyp **Windows-Server** aus, geben Sie einen Anzeigenamen für die Anmeldeinformationen ein, und fügen Sie den Benutzernamen und das Kennwort hinzu. Klicken Sie auf **Speichern**.
1. Wenn Sie die kennwortbasierte Authentifizierung für Linux-Server verwenden, wählen Sie den Quelltyp **Linux Server (Password-based)** (Linux-Server (kennwortbasiert)) aus, geben Sie einen Anzeigenamen für die Anmeldeinformationen ein, und fügen Sie den Benutzernamen und das Kennwort hinzu. Klicken Sie auf **Speichern**.
1. Wenn Sie auf SSH-Schlüsseln basierende Authentifizierung für Linux-Server verwenden, wählen Sie den Quelltyp **Linux-Server (auf SSH-Schlüsseln basierend)** aus, geben Sie einen Anzeigenamen für die Anmeldeinformationen ein, fügen Sie den Benutzernamen hinzu, suchen Sie nach der Datei mit dem privaten SSH-Schlüssel, und wählen Sie diese aus. Klicken Sie auf **Speichern**.

    - Azure Migrate unterstützt den privaten SSH-Schlüssel, der vom Befehl „ssh-keygen“ mithilfe von RSA-, DSA-, ECDSA- und ed25519-Algorithmen generiert wird.
    - Zurzeit unterstützt Azure Migrate keinen auf einer Passphrase basierenden SSH-Schlüssel. Verwenden Sie einen SSH-Schlüssel ohne eine Passphrase.
    - Derzeit wird die von PuTTY generierte Datei mit dem privaten SSH-Schlüssel von Azure Migrate nicht unterstützt.
    - Azure Migrate unterstützt das OpenSSH-Format der Datei mit dem privaten SSH-Schlüssel. Dies ist hier dargestellt:
    
    ![Unterstütztes Format für privaten SSH-Schlüssel](./media/tutorial-discover-physical/key-format.png)

1. Wenn Sie mehrere Anmeldeinformationen gleichzeitig hinzufügen möchten, klicken Sie auf **Weitere hinzufügen**, um die Angeben zu speichern und weitere Anmeldeinformationen hinzuzufügen. Es werden mehrere Anmeldeinformationen für die Ermittlung physischer Server unterstützt.
1. Klicken Sie in **Schritt 2: Bereitstellen von Details zu physischen oder virtuellen Servern** auf **Ermittlungsquelle hinzufügen**, um den **IP-Adresse/FQDN**-Wert des Servers und den Anzeigenamen für Anmeldeinformationen zum Herstellen einer Verbindung mit dem Server anzugeben.
1. Sie können entweder jeweils **ein einzelnes Element** oder **mehrere Elemente** in einem Schritt hinzufügen. Es besteht auch die Möglichkeit, Serverdetails über **CSV importieren** bereitzustellen.


    - Wenn Sie auf **Add single item** (Einzelnes Element hinzufügen) klicken, können Sie den Betriebssystemtyp auswählen, den Anzeigenamen für die Anmeldeinformationen angeben, den **IP-Adresse/FQDN**-Wert des Servers hinzufügen und dann auf **Speichern** klicken.
    - Wenn Sie auf **Add multiple items** (Mehrere Elemente hinzufügen) klicken, können Sie mehrere Datensätze auf einmal hinzufügen, indem Sie **IP-Adresse/FQDN** für den Server mit dem Anzeigenamen für die Anmeldeinformationen im Textfeld angeben. Überprüfen** Sie die hinzugefügten Einträge, und klicken Sie auf **Speichern**.
    - Wenn Sie **CSV importieren** _(standardmäßig ausgewählt)_ auswählen, können Sie eine CSV-Vorlagendatei herunterladen, die Datei mit dem **IP-Adresse/FQDN**-Wert des Servers und einem Anzeigenamen für die Anmeldeinformationen auffüllen. Importieren Sie die Datei dann in die Appliance, **überprüfen** Sie die Datensätze in der Datei, und klicken Sie auf **Speichern**.

1. Wenn Sie auf „Speichern“ klicken, versucht die Appliance, die Verbindung zu den hinzugefügten Servern zu überprüfen, und zeigt den **Überprüfungszustand** in der Tabelle für jeden Server an.
    - Sollte bei der Überprüfung eines Servers ein Fehler auftreten, sehen Sie sich den Fehler an, indem Sie auf **Fehler bei der Überprüfung** in der Spalte „Status“ klicken. Beheben Sie das Problem, und wiederholen Sie die Überprüfung.
    - Um einen Server zu entfernen, klicken Sie auf **Löschen**.
1. Sie können die Verbindung mit Servern jederzeit vor Beginn der Ermittlung **erneut überprüfen**.
1. Klicken Sie auf **Ermittlung starten**, um die Ermittlung der erfolgreich überprüften Server zu starten. Nachdem die Ermittlung erfolgreich gestartet wurde, können Sie den Ermittlungsstatus für jeden Server in der Tabelle überprüfen.

Es dauert etwa zwei Minuten, bis die Ermittlung von 100 Servern abgeschlossen ist und ihre Metadaten im Azure-Portal angezeigt werden.

## <a name="verify-servers-in-the-portal"></a>Überprüfen von Servern im Portal

Nach Abschluss der Ermittlung können Sie überprüfen, ob die Server im Portal angezeigt werden.

1. Öffnen Sie das Azure Migrate-Dashboard.
2. Klicken Sie unter **Azure Migrate – Server** > **Azure Migrate: Ermittlung und Bewertung** auf das Symbol mit der Anzahl für **Ermittelte Server**.

## <a name="delete-servers"></a>Löschen von Servern
Nachdem die Ermittlung initiiert wurde, können Sie alle hinzugefügten Server aus dem Appliancekonfigurations-Manager löschen, indem Sie in der Tabelle nach **Ermittlungsquelle hinzufügen** suchen und auf **Löschen** klicken.

>[!NOTE]
> Wenn Sie einen Server löschen möchten, auf dem die Ermittlung initiiert wurde, werden die laufende Ermittlung und Bewertung beendet, was sich möglicherweise auf die Vertrauenswürdigkeit der Bewertung auswirkt, die diesen Server enthält. [Weitere Informationen](/azure/migrate/common-questions-discovery-assessment#why-is-the-confidence-rating-of-my-assessment-low)

## <a name="next-steps"></a>Nächste Schritte

- [Bewerten Sie physische Server](tutorial-assess-physical.md) für die Migration zu Azure.
- [Informieren Sie sich über die Daten](migrate-appliance.md#collected-data---physical), die von der Appliance im Zuge der Ermittlung gesammelt werden.