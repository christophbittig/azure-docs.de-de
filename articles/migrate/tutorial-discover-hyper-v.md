---
title: Ermitteln von Servern in Hyper-V mit der Ermittlung und Bewertung von Azure Migrate
description: Hier erfahren Sie, wie Sie lokale Server in Hyper-V mit dem Azure Migrate-Tool zur Ermittlung und Bewertung ermitteln.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 11/12/2021
ms.custom: mvc
ms.openlocfilehash: 1c9e0c5a6a0672ec36bfef1d2f8d909b0ddea984
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397678"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Tutorial: Ermitteln von Servern in Hyper-V mit der Ermittlung und Bewertung von Azure Migrate

Im Rahmen der Migration zu Azure ermitteln Sie den lokalen Bestand und die lokalen Workloads.

In diesem Tutorial wird gezeigt, wie Sie lokale Server auf Hyper-V-Hosts mit dem Azure Migrate-Tool zur Ermittlung und Bewertung und einer einfachen Azure Migrate-Appliance ermitteln. Sie stellen die Appliance als Server auf dem Hyper-V-Host bereit, um kontinuierlich Computer- und Leistungsmetadaten zu ermitteln.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten eines Azure-Kontos
> * Vorbereiten der Hyper-V-Umgebung für die Ermittlung
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
**Hyper-V-Host** | Hyper-V-Hosts mit Servern können eigenständig oder Teil eines Clusters sein.<br/><br/> Der Host muss unter Windows Server 2019, Windows Server 2016 oder Windows Server 2012 R2 ausgeführt werden.<br/><br/> Überprüfen Sie, ob eingehende Verbindungen an WinRM-Port 5985 (HTTP) zugelassen sind, damit die Appliance mithilfe einer CIM-Sitzung (Common Information Model) eine Verbindung herstellen und Servermetadaten und -leistungsdaten pullen kann.
**Bereitstellung einer Appliance** | Der Hyper-V-Host benötigt Ressourcen, um einen Server für die Appliance zuzuordnen:<br/><br/> - 16 GB RAM, acht vCPUs und ca. 80 GB Speicherplatz auf dem Datenträger<br/><br/> - Einen externen virtuellen Switch und Internetzugriff für die Appliance (direkt oder über einen Proxy)
**Server** | Server können unter einem beliebigen Windows- oder Linux-Betriebssystem ausgeführt werden.

## <a name="prepare-an-azure-user-account"></a>Vorbereiten eines Azure-Benutzerkontos

Zum Erstellen eines Projekts und Registrieren der Azure Migrate-Appliance benötigen Sie ein Konto mit den folgenden Berechtigungen:
- Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ für ein Azure-Abonnement
- Berechtigungen zum Registrieren von AAD-Apps (Azure Active Directory)

Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements. Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um die Berechtigungen wie folgt zuzuweisen:

1. Suchen Sie im Azure-Portal nach „Abonnements“, und wählen Sie unter **Dienste** die Option **Abonnements** aus.

    ![Suchfeld, um nach dem Azure-Abonnement zu suchen](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Wählen Sie auf der Seite **Abonnements** das Abonnement aus, in dem Sie ein Projekt erstellen möchten.
3. Wählen Sie im Abonnement die Option **Zugriffssteuerung (IAM)**  > **Zugriff überprüfen** aus.
4. Suchen Sie unter **Zugriff überprüfen** nach dem entsprechenden Benutzerkonto.
5. Klicken Sie unter **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

    ![Suchen nach einem Benutzerkonto, um den Zugriff zu überprüfen und eine Rolle zuzuweisen](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. Wählen Sie unter **Rollenzuweisung hinzufügen** die Rolle „Mitwirkender“ oder „Besitzer“ und das Konto aus (in unserem Beispiel „azmigrateuser“). Klicken Sie anschließend auf **Speichern**.

    ![Die Seite „Rollenzuweisung“ wird geöffnet, auf der Sie dem Konto eine Rolle zuweisen können.](./media/tutorial-discover-hyper-v/assign-role.png)

1. Ihr Azure-Konto benötigt **Berechtigungen zum Registrieren von AAD-Apps**, um die Appliance registrieren zu können.
1. Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Benutzer** > **Benutzereinstellungen**.
1. Vergewissern Sie sich unter **Benutzereinstellungen**, dass Azure AD-Benutzer Anwendungen registrieren können (standardmäßig auf **Ja** festgelegt).

    ![Überprüfen unter „Benutzereinstellungen“, ob Benutzer Active Directory-Apps registrieren können](./media/tutorial-discover-hyper-v/register-apps.png)

9. Wenn die Einstellungen für „App-Registrierungen“ auf „Nein“ festgelegt sind, fordern Sie den Mandantenadministrator/globalen Administrator auf, die erforderliche Berechtigung zuzuweisen. Alternativ kann der Mandantenadministrator/globale Administrator einem Konto die Rolle **Anwendungsentwickler** zuweisen, um die Registrierung von AAD-Apps zuzulassen. [Weitere Informationen](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-hyper-v-hosts"></a>Vorbereiten der Hyper-V-Hosts

Sie können Hyper-V-Hosts manuell oder mithilfe eines Skripts vorbereiten. Die Vorbereitungsschritte sind in der Tabelle zusammengefasst. Das Skript bereitet diese automatisch vor.

**Schritt** | **Skript** | **Manuell**
--- | --- | ---
Überprüfen der Hostanforderungen | Das Skript überprüft, ob auf dem Host eine unterstützte Hyper-V-Version und die Hyper-V-Rolle ausgeführt werden.<br/><br/>Es aktiviert den WinRM-Dienst und öffnet die Ports 5985 (HTTP) und 5986 (HTTPS) auf dem Host (erforderlich für die Metadatensammlung). | Der Host muss unter Windows Server 2019, Windows Server 2016 oder Windows Server 2012 R2 ausgeführt werden.<br/><br/> Überprüfen Sie, ob eingehende Verbindungen an WinRM-Port 5985 (HTTP) zugelassen sind, damit die Appliance mithilfe einer CIM-Sitzung (Common Information Model) eine Verbindung herstellen und Servermetadaten und -leistungsdaten pullen kann.<br/><br/> Das Skript wird derzeit nicht auf Hosts mit einem nicht englischen Gebietsschema unterstützt.  
Überprüfen der PowerShell-Version | Es wird überprüft, ob das Skript unter einer unterstützten PowerShell-Version ausgeführt wird. | Überprüfen Sie, ob Sie PowerShell, Version 4.0 oder höher, auf dem Hyper-V-Host ausführen.
Erstellen eines Kontos | Es wird überprüft, ob Sie über die richtigen Berechtigungen für den Hyper-V-Host verfügen.<br/><br/> Ermöglicht das Erstellen eines lokalen Benutzerkontos mit den richtigen Berechtigungen. | Option 1: Bereiten Sie ein Konto mit Administratorzugriff auf den Hyper-V-Hostcomputer vor.<br/><br/> Option 2: Bereiten Sie ein lokales Administratorkonto oder ein Domänenadministratorkonto vor, und fügen Sie das Konto den folgenden Gruppen hinzu: „Remoteverwaltungsbenutzer“, „Hyper-V-Administratoren“ und „Systemmonitorbenutzer“.
Aktivieren von PowerShell-Remoting | Aktiviert PowerShell-Remoting auf dem Host, sodass die Azure Migrate-Appliance über eine WinRM-Verbindung PowerShell-Befehle auf dem Host ausführen kann. | Zum Einrichten auf jedem Host öffnen Sie eine PowerShell-Konsole als Administrator und führen den folgenden Befehl aus: ``` powershell Enable-PSRemoting -force ```
Einrichten von Hyper-V-Integrationsdiensten | Es wird überprüft, ob die Hyper-V-Integrationsdienste auf allen vom Host verwalteten Servern aktiviert sind. | [Aktivieren Sie die Hyper-V Integrationsdienste](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) auf den einzelnen Servern.<br/><br/> Wenn Sie Windows Server 2003 ausführen, [folgen Sie diesen Anleitungen](prepare-windows-server-2003-migration.md).
Delegieren von Anmeldeinformationen, wenn sich Serverdatenträger auf SMB-Remotefreigaben befinden | Delegiert Anmeldeinformationen. | Führen Sie diesen Befehl aus, um CredSSP das Delegieren von Anmeldeinformationen auf Hosts zu ermöglichen, auf denen Server mit Datenträgern auf SMB-Freigaben ausgeführt werden: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> Sie können diesen Befehl remote auf allen Hyper-V-Hosts ausführen.<br/><br/> Wenn Sie in einem Cluster neue Hostknoten hinzufügen, werden diese automatisch zur Ermittlung hinzugefügt, aber Sie müssen CredSSP manuell aktivieren.<br/><br/> Beim Einrichten der Appliance schließen Sie die Einrichtung von CredSSP durch [die Aktivierung für die Appliance](#delegate-credentials-for-smb-vhds) ab. 

### <a name="run-the-script"></a>Ausführen des Skripts

1. Laden Sie das Skript aus dem [Microsoft Download Center](https://aka.ms/migrate/script/hyperv) herunter. Das Skript wird von Microsoft kryptografisch signiert.
2. Überprüfen Sie die Skriptintegrität mithilfe der SHA256-Hashdatei. Der Hashtagwert ist unten angegeben. Führen Sie den folgenden Befehl aus, um den Hash für das Skript zu generieren:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Beispielverwendung:

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. Führen Sie nach der Überprüfung der Skriptintegrität das Skript mit dem folgenden PowerShell-Befehl mit erhöhten Berechtigungen auf jedem Hyper-V-Host aus:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
Hashwert:

**Hash** |  **Wert**
--- | ---
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Einrichten eines Projekts

Richten Sie ein neues Projekt ein.

1. Wählen Sie im Azure-Portal **Alle Dienste** aus, und suchen Sie nach **Azure Migrate**.
2. Wählen Sie unter **Dienste** die Option **Azure Migrate** aus.
3. Wählen Sie unter **Übersicht** die Option **Create project** (Projekt erstellen) aus.
5. Wählen Sie unter **Create project** (Projekt erstellen) Ihr Azure-Abonnement und die Ressourcengruppe aus. Falls noch keine vorhanden ist, erstellen Sie eine Ressourcengruppe.
6. Geben Sie unter **Projektdetails** den Projektnamen und die geografische Region an, in der Sie das Projekt erstellen möchten. Beachten Sie die unterstützten geografischen Regionen für [öffentliche Clouds](migrate-support-matrix.md#supported-geographies-public-cloud) und [Azure Government-Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Felder für Projektname und Region](./media/tutorial-discover-hyper-v/new-project.png)

7. Klicken Sie auf **Erstellen**.
8. Warten Sie einige Minuten, bis das Projekt bereitgestellt wurde. Das Tool **Azure Migrate: Ermittlung und Bewertung** wird dem neuen Projekt standardmäßig hinzugefügt.

![Seite mit dem standardmäßig hinzugefügten Azure Migrate-Tool zur Ermittlung und Bewertung](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Wenn Sie bereits ein Projekt erstellt haben, können Sie dieses Projekt zum Registrieren zusätzlicher Appliances verwenden, um mehr Server zu ermitteln und zu bewerten. [Weitere Informationen](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Einrichten der Appliance

Von Azure Migrate wird eine einfache Azure Migrate-Appliance verwendet. Die Appliance führt die Serverermittlung durch und sendet Metadaten zur Serverkonfiguration und -leistung an Azure Migrate. Die Appliance kann durch Bereitstellen einer VHD-Datei eingerichtet werden, die aus dem Projekt heruntergeladen werden kann.

> [!NOTE]
> Wenn Sie die Appliance aus irgendeinem Grund nicht mithilfe der Vorlage einrichten können, können Sie für die Einrichtung ein PowerShell-Skript auf einem vorhandenen Windows Server 2016-Server verwenden. [Weitere Informationen](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)<br/>
> Die Option zum Bereitstellen einer Appliance mithilfe einer Vorlage wird in Azure Government Cloud nicht unterstützt. [Erfahren Sie mehr](./deploy-appliance-script-government.md) über das Bereitstellen einer Appliance für Azure Government Cloud.

In diesem Tutorial wird die Appliance wie folgt auf einem Server in einer Hyper-V-Umgebung eingerichtet:

1. Geben Sie einen Appliancenamen ein, und generieren Sie einen Projektschlüssel im Portal.
1. Herunterladen einer komprimierten Hyper-V-VHD über das Azure-Portal
1. Erstellen Sie die Appliance, und überprüfen Sie, ob sie eine Verbindung mit „Azure Migrate: Ermittlung und Bewertung“ herstellen kann.
1. Führen Sie die Erstkonfiguration der Appliance aus, und registrieren Sie sie unter Verwendung des Projektschlüssels im Projekt.

### <a name="1-generate-the-project-key"></a>1. Generieren des Projektschlüssels

1. Wählen Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Ermittlung und Bewertung** die Option **Ermitteln** aus.
2. Wählen Sie unter **Discover Servers (Server ermitteln)**  > **Sind Ihre Server virtualisiert?** die Option **Ja, mit Hyper-V** aus.
3. Geben Sie unter **1: Projektschlüssel generieren** einen Namen für die Azure Migrate-Appliance ein, die Sie für die Ermittlung von Servern einrichten. Für den Namen können bis zu 14 alphanumerische Zeichen angegeben werden.
1. Klicken Sie auf **Schlüssel generieren**, um mit der Erstellung der erforderlichen Azure-Ressourcen zu beginnen. Schließen Sie die Seite „Discover servers“ (Server ermitteln) nicht, während die Ressourcen erstellt werden.
1. Nach der erfolgreichen Erstellung der Azure-Ressourcen wird ein **Projektschlüssel** generiert.
1. Kopieren Sie den Schlüssel, da Sie ihn benötigen, um die Registrierung der Appliance während der Konfiguration abzuschließen.

### <a name="2-download-the-vhd"></a>2. Herunterladen der VHD

Wählen Sie in **2: Azure Migrate-Appliance herunterladen** die VHD-Datei aus, und klicken Sie auf **Herunterladen**.

### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.

2. Führen Sie den folgenden PowerShell-Befehl aus, um den Hash für die ZIP-Datei zu generieren:
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Beispielverwendung: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Überprüfen Sie die aktuellen Applianceversionen und Hashwerte:

    - Öffentliche Azure-Cloud:

        **Szenario** | **Download** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140422) |  91A435962F0DC9C7032DE0288D578171A76CC07563AC543C5E1EB25CD31BB231

    - Azure Government:

        **Szenario** _ | _ *Herunterladen** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140424) |  3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

### <a name="3-create-an-appliance"></a>3. Erstellen einer Appliance

Importieren Sie die heruntergeladene Datei, und erstellen Sie eine Appliance.

1. Extrahieren Sie die gezippte VHD-Datei in einem Ordner auf dem Hyper-V-Host, der die Appliance hostet. Drei Ordner werden extrahiert.
2. Öffnen Sie den Hyper-V-Manager. Klicken Sie unter **Aktionen** auf **Virtuellen Computer importieren**.
2. Klicken Sie im Assistenten zum Importieren virtueller Computer unter **Vorbereitung** auf **Weiter**.
3. Geben Sie unter **Ordner suchen** den Ordner an, der die extrahierte VHD enthält. Klicken Sie dann auf **Weiter**.
1. Klicken Sie unter **Virtuellen Computer auswählen** auf **Weiter**.
2. Klicken Sie unter **Importtyp auswählen** auf **Virtuellen Computer kopieren (neue eindeutige ID erstellen)** . Klicken Sie dann auf **Weiter**.
3. Behalten Sie unter **Ziel auswählen** die Standardeinstellung bei. Klicken Sie auf **Weiter**.
4. Behalten Sie unter **Speicherordner** die Standardeinstellung bei. Klicken Sie auf **Weiter**.
5. Geben Sie unter **Netzwerk auswählen** den virtuellen Switch an, der von der Appliance verwendet wird. Der Switch benötigt Internetkonnektivität, um Daten an Azure senden zu können.
6. Überprüfen Sie die Einstellungen unter **Zusammenfassung**. Klicken Sie auf **Fertig stellen**.
7. Starten Sie die Appliance im Hyper-V-Manager unter **Virtuelle Computer**.

### <a name="verify-appliance-access-to-azure"></a>Überprüfen des Appliancezugriffs auf Azure

Stellen Sie sicher, dass die Appliance eine Verbindung mit Azure-URLs für [öffentliche](migrate-appliance.md#public-cloud-urls) Clouds und [Azure Government](migrate-appliance.md#government-cloud-urls)-Clouds herstellen kann.

### <a name="4-configure-the-appliance"></a>4. Konfigurieren der Appliance

Führen Sie die Ersteinrichtung der Appliance durch.

> [!NOTE]
> Wenn Sie die Appliance mithilfe eines [PowerShell-Skripts](deploy-appliance-script.md) und nicht mit der heruntergeladenen VHD einrichten, sind die ersten beiden Schritte in diesem Verfahren nicht relevant.

1. Klicken Sie im Hyper-V-Manager unter **Virtuelle Computer** mit der rechten Maustaste auf die Appliance, und klicken Sie anschließend auf **Verbinden**.
2. Geben Sie die Sprache, die Zeitzone und das Kennwort für die Appliance an.
3. Öffnen Sie in einem Browser auf einem beliebigen Computer, der eine Verbindung mit der Appliance herstellen kann, die URL der Appliance-Web-App: **https://*Name oder IP-Adresse der Appliance*: 44368**.

   Alternativ können Sie auch auf dem Appliancedesktop auf die App-Verknüpfung klicken, um die App zu öffnen.
1. Akzeptieren Sie die **Lizenzbedingungen**, und lesen Sie die Drittanbieterinformationen.
1. Gehen Sie in der Web-App unter **Erforderliche Komponenten einrichten** wie folgt vor:
    - **Konnektivität**: Die App überprüft, ob die Appliance über Internetzugriff verfügt. Falls die Appliance einen Proxy verwendet:
      - Klicken Sie auf **Proxy einrichten**, um die Proxyadresse (im Format http://ProxyIPAddress oder http://ProxyFQDN) und den überwachenden Port anzugeben.
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
4. Wenn das für die Protokollierung verwendete Azure-Benutzerkonto über die richtigen Berechtigungen für die während der Schlüsselgenerierung erstellten Azure-Ressourcen verfügt, wird die Registrierung der Appliance initiiert.
1. Nachdem die Appliance erfolgreich registriert wurde, können Sie die Registrierungsdetails anzeigen, indem Sie auf **Details anzeigen** klicken.

### <a name="delegate-credentials-for-smb-vhds"></a>Delegieren von Anmeldeinformationen für SMB-VHDs

Wenn Sie VHDs in SMBs ausführen, müssen Sie die Delegierung von Anmeldeinformationen von der Appliance an die Hyper-V-Hosts aktivieren. Gehen Sie dazu in der Appliance wie folgt vor:

1. Führen Sie auf der Appliance den folgenden Befehl aus. „HyperVHost1“ und „HyperVHost2“ sind Beispielhostnamen.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Gehen Sie alternativ im Editor für lokale Gruppenrichtlinien auf der Appliance wie folgt vor:
    - Klicken Sie unter **Richtlinie für "Lokaler Computer"**  > **Computerkonfiguration** auf **Administrative Vorlagen** > **System** > **Delegierung von Anmeldeinformationen**.
    - Doppelklicken Sie auf **Delegierung von aktuellen Anmeldeinformationen zulassen**, und wählen Sie **Aktiviert** aus.
    - Klicken Sie unter **Optionen** auf **Anzeigen**, und fügen Sie jeden Hyper-V-Host, den Sie ermitteln möchten, mit dem Präfix **wsman/** der Liste hinzu.
    - Doppelklicken Sie unter **Delegierung von Anmeldeinformationen** auf **Delegierung von aktuellen Anmeldeinformationen mit reiner NTLM-Serverauthentifizierung zulassen**. Fügen Sie auch hier wieder jeden Hyper-V-Host, den Sie ermitteln möchten, mit dem Präfix **wsman/** der Liste hinzu.

## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Stellen Sie von der Appliance aus eine Verbindung mit Hyper-V-Hosts oder -Clustern her, und starten Sie die Serverermittlung.

1. Klicken Sie in **Schritt 1: Angeben von Hyper-V-Hostanmeldeinformationen** auf **Anmeldeinformationen hinzufügen**, um einen Anzeigenamen für die Anmeldeinformationen anzugeben, und fügen Sie **Benutzername** und **Kennwort** für einen Hyper-V-Host/-Cluster hinzu, den die Appliance zur Ermittlung von Servern verwendet. Klicken Sie auf **Speichern**.
1. Wenn Sie mehrere Anmeldeinformationen gleichzeitig hinzufügen möchten, klicken Sie auf **Weitere hinzufügen**, um die Angeben zu speichern und weitere Anmeldeinformationen hinzuzufügen. Für die Ermittlung von Servern in einer Hyper-V-Umgebung werden mehrere Anmeldeinformationen unterstützt.
1. Klicken Sie in **Schritt 2: Bereitstellen von Details zu Hyper-V-Host/-Cluster** auf **Ermittlungsquelle hinzufügen**, um **IP-Adresse/FQDN** für den Hyper-V-Host/-Cluster und den Anzeigenamen für Anmeldeinformationen zum Herstellen einer Verbindung mit dem Host/Cluster anzugeben.
1. Sie können entweder jeweils **ein einzelnes Element** oder **mehrere Elemente** in einem Schritt hinzufügen. Es besteht auch die Möglichkeit, Details zu dem Hyper-V-Host/-Cluster über **CSV importieren** bereitzustellen.

    - Wenn Sie **Einzelnes Element hinzufügen** auswählen, müssen Sie den Anzeigenamen für die Anmeldeinformationen und **IP-Adresse/FQDN** des Hyper-V-Hosts/-Clusters hinzufügen und dann auf **Speichern** klicken.
    - Wenn Sie **Mehrere Elemente hinzufügen** auswählen _(standardmäßig ausgewählt)_ , können Sie mehrere Datensätze auf einmal hinzufügen, indem Sie **IP-Adresse/FQDN** des Hyper-V-Hosts/-Clusters mit dem Anzeigenamen für die Anmeldeinformationen im Textfeld angeben. **Überprüfen** Sie die hinzugefügten Einträge, und klicken Sie auf **Speichern**.
    - Wenn Sie **CSV importieren** auswählen, können Sie eine CSV-Vorlagendatei herunterladen und die Datei mit **IP-Adresse/FQDN** des Hyper-V-Hosts/-Clusters und einem Anzeigenamen für die Anmeldeinformationen auffüllen. Importieren Sie die Datei dann in die Appliance, **überprüfen** Sie die Datensätze in der Datei, und klicken Sie auf **Speichern**.

1. Wenn Sie auf „Speichern“ klicken, versucht die Appliance, die Verbindung zu den hinzugefügten Hyper-V-Hosts/-Clustern zu überprüfen, und zeigt den **Überprüfungszustand** für jeden Host/Cluster in der Tabelle an.
    - Wenn die Hosts/Cluster erfolgreich überprüft werden, können Sie weitere Details anzeigen, indem Sie auf die zugehörigen IP-Adressen/FQDNs klicken.
    - Sollte bei der Überprüfung eines Hosts ein Fehler auftreten, sehen Sie sich den Fehler an, indem Sie auf **Fehler bei der Überprüfung** in der Spalte „Status“ klicken. Beheben Sie das Problem, und wiederholen Sie die Überprüfung.
    - Wenn Sie Hosts oder Cluster entfernen möchten, klicken Sie auf **Löschen**.
    - Es ist nicht möglich, einen bestimmten Host aus einem Cluster zu entfernen. Sie können nur den gesamten Cluster entfernen.
    - Ein Cluster kann auch dann hinzugefügt werden, wenn Probleme mit bestimmten, in dem Cluster enthaltenen Hosts vorliegen.
1. Sie können die Verbindung mit Hosts/Clustern jederzeit vor Beginn der Ermittlung **erneut überprüfen**.
1. Klicken Sie auf **Ermittlung starten**, um die Serverermittlung von den erfolgreich überprüften Hosts/Clustern aus zu starten. Nachdem die Ermittlung erfolgreich gestartet wurde, können Sie den Ermittlungsstatus für jeden Host/Cluster in der Tabelle überprüfen.

Daraufhin wird die Ermittlung gestartet. Es dauert ca. 2 Minuten pro Host, bis Metadaten des ermittelten Servers im Azure-Portal angezeigt werden.

## <a name="verify-servers-in-the-portal"></a>Überprüfen von Servern im Portal

Nach Abschluss der Ermittlung können Sie überprüfen, ob die Server im Portal angezeigt werden.

1. Öffnen Sie das Azure Migrate-Dashboard.
2. Klicken Sie unter **Azure Migrate – Server** > **Azure Migrate: Ermittlung und Bewertung** auf das Symbol mit der Anzahl für **Ermittelte Server**.

## <a name="next-steps"></a>Nächste Schritte

- [Bewerten Sie Server in der Hyper-V-Umgebung](tutorial-assess-hyper-v.md) für die Migration zu virtuellen Azure-Computern.
- [Informieren Sie sich über die Daten](migrate-appliance.md#collected-data---hyper-v), die von der Appliance im Zuge der Ermittlung gesammelt werden.