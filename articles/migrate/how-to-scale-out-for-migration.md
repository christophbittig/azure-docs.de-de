---
title: Einrichten einer Azure Migrate-Appliance für die horizontale Skalierung für die VMware-Migration ohne Agent
description: Hier erfahren Sie, wie Sie eine Azure Migrate-Appliance für die horizontale Skalierung einrichten, um virtuelle Hyper-V-Computer zu migrieren.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 9a041511dcd921c7bf4a9316cdbf3d7617ef66b7
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503713"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>Skalieren der Migration von virtuellen VMware-Computern zu Azure ohne Agents

In diesem Artikel erfahren Sie, wie Sie eine Appliance für die horizontale Skalierung verwenden, um mithilfe der Migrationsmethode des Azure Migrate-Servermigrationstools für die Migration von VMware-VMs ohne Agent eine große Anzahl von VMware-VMs zu Azure zu migrieren.

Mithilfe der Migrationsmethode für VMware-VMs ohne Agent können Sie folgende Aktionen ausführen:

- Replizieren von bis zu 300 VMs gleichzeitig von einem einzelnen vCenter-Server mithilfe einer Azure Migrate-Appliance
- Replizieren von bis zu 500 VMs gleichzeitig von einem einzelnen vCenter-Server durch das Bereitstellen einer zweiten Appliance für die horizontale Skalierung für die Migration

In diesem Artikel wird Folgendes behandelt:

- Hinzufügen einer Appliance für die horizontale Skalierung für die Migration von VMware-VMs ohne Agent
- Migrieren von bis zu 500 VMs gleichzeitig mithilfe der Appliance für die horizontale Skalierung

##  <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen Sie folgende Schritte ausführen:

- Erstellen Sie das Azure Migrate-Projekt.
- Stellen Sie die Azure Migrate-Appliance (primäre Appliance) bereit, und ermitteln Sie die virtuellen VMware-Computer, die von Ihrem vCenter-Server verwaltet werden.
- Konfigurieren Sie die Replikation für mindestens einen virtuellen Computer, der migriert werden soll.
> [!IMPORTANT]
> Sie müssen über mindestens einen replizierenden virtuellen Computer im Projekt verfügen, bevor Sie eine Appliance für die horizontale Skalierung für die Migration hinzufügen können.

Weitere Informationen zum Ausführen des obigen Verfahrens finden Sie im Tutorial zum [Migrieren von VMware-VMs zu Azure mithilfe der Migrationsmethode ohne Agent](./tutorial-migrate-vmware.md).

## <a name="deploy-a-scale-out-appliance"></a>Bereitstellen einer Appliance für die horizontale Skalierung

Führen Sie die im Folgenden aufgeführten Schritte aus, um eine Appliance für die horizontale Skalierung hinzuzufügen:

1. Klicken Sie auf **Ermitteln** > **Sind Ihre Computer virtualisiert?** 
1. Klicken Sie auf **Ja, mit VMware vSphere-Hypervisor**.
1. Wählen Sie im nächsten Schritt die Replikation ohne Agent aus.
1. Wählen Sie für das Menü „Select the type of appliance“ (Art der Appliance auswählen) die Option **Scale-out an existing primary appliance** (Vorhandene primäre Appliance horizontal skalieren) aus.
1. Wählen Sie die primäre Appliance aus (die Appliance, mit der der Ermittlungsvorgang durchgeführt wurde), die Sie horizontal skalieren möchten.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Seite „Computer ermitteln“ für das Onboarding der horizontalen Skalierung":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Generieren des Azure Migrate-Projektschlüssels

1. Geben Sie unter **Generate Azure Migrate project key** (Azure Migrate-Projektschlüssel generieren) einen Suffixnamen für die Appliance für die horizontale Skalierung an. Das Suffix darf nur alphanumerische Zeichen enthalten und eine Länge von maximal 14 Zeichen aufweisen.
2. Klicken Sie auf **Schlüssel generieren**, um mit der Erstellung der erforderlichen Azure-Ressourcen zu beginnen. Schließen Sie die Ermittlungsseite nicht, während die Ressourcen erstellt werden.
3. Kopieren Sie den generierten Schlüssel. Sie benötigen den Schlüssel später, um die Registrierung der Appliance für die horizontale Skalierung abzuschließen.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. Laden Sie das Installationsprogramm für die Appliance für die horizontale Skalierung herunter.

Klicken Sie unter **Download Azure Migrate appliance** (Azure Migrate-Appliance herunterladen) auf **Herunterladen**. Sie müssen das PowerShell-Installationsprogrammskript herunterladen, um die Appliance für die horizontale Skalierung auf einem vorhandenen Server bereitzustellen, auf dem Windows Server 2016 ausgeführt wird und für den die erforderlichen Hardwarekonfigurationen (32 GB RAM, 8 vCPU, ca. 80 GB Datenträgerspeicher sowie Internetzugriff entweder direkt oder über einen Proxyserver) verwendet werden.

:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Herunterladen des Skripts für die Appliance für die horizontale Skalierung":::

> [!TIP]
> Mithilfe der folgenden Schritte können Sie die Prüfsumme der heruntergeladenen ZIP-Datei validieren:
>
> 1. Öffnen Sie auf dem Server, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
> 2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. Laden Sie die neueste Version des Installationsprogramms für die Skalierungs-Anwendung vom Portal herunter, wenn der berechnete Hashwert nicht mit dieser Zeichenfolge übereinstimmen sollte: 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Ausführen des Azure Migrate-Installationsskripts

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Server, der die Appliance hostet.  Führen Sie das Skript nicht auf einem Server mit einer vorhandenen Azure Migrate-Appliance aus.

2. Starten Sie PowerShell auf dem oben genannten Server mit Administratorberechtigungen (erhöhten Rechten).

3. Ändern Sie das PowerShell-Verzeichnis in den Ordner, in den die Inhalte der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.

4. Führen Sie das Skript `AzureMigrateInstaller.ps1` mit folgendem Befehl aus:

  `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. Treffen Sie eine Auswahl aus den Szenario-, Cloud-, Konfigurations- und Konnektivitätsoptionen, um die gewünschte Appliance bereitzustellen. Mit der unten angegebenen Auswahl wird beispielsweise eine **Appliance für die horizontale Skalierung** eingerichtet, um gleichzeitige Replikationen auf Servern, die in Ihrer VMware-Umgebung ausgeführt werden, zu initiieren. Die Replikation erfolgt für ein Azure Migrate-Projekt mit **Standardkonnektivität _(öffentlicher Endpunkt)_** in der **öffentlichen Azure-Cloud**.

    :::image type="content" source="./media/how-to-scale-out-for-migration/script-vmware-scaleout-inline.png" alt-text="Screenshot: Einrichten einer Appliance für die horizontale Skalierung" lightbox="./media/how-to-scale-out-for-migration/script-vmware-scaleout-expanded.png":::

6. Das Installationsskript führt folgende Schritte aus:

    - Der Gateway-Agent und der Appliancekonfigurations-Manager werden installiert, um mehr gleichzeitige Serverreplikationen durchführen zu können.
    - Installation von Windows-Rollen, darunter beispielsweise Windows-Aktivierungsdienst, IIS und PowerShell ISE.
    - Download und Installation eines wiederbeschreibbaren IIS-Moduls.
    - Aktualisierung eines Registrierungsschlüssels (HKLM) mit dauerhaften Einstellungsdetails für Azure Migrate.
    - Erstellung der folgenden Dateien in diesem Pfad:
        - **Konfigurationsdateien**: %Programdata%\Microsoft Azure\Config
        - **Protokolldateien**: %Programdata%\Microsoft Azure\Logs

Nach der erfolgreichen Ausführung des Skripts wird der Appliance-Konfigurations-Manager automatisch gestartet.

> [!NOTE]
> Bei Problemen können Sie zum Troubleshooting unter „C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Zeitstempel</em>.log“ auf die Skriptprotokolle zugreifen.


### <a name="4-configure-the-appliance"></a>4. Konfigurieren der Appliance

Bevor Sie beginnen, sollten Sie sicherstellen, dass [diese Azure-Endpunkte](migrate-appliance.md#public-cloud-urls) über die Appliance für die horizontale Skalierung zugänglich sind.

- Öffnen Sie einen Browser auf einem beliebigen Computer, der eine Verbindung mit dem Server für die Appliance für die horizontale Skalierung herstellen kann, und öffnen Sie die URL des Appliancekonfigurations-Managers: **https://*Name oder IP-Adresse der Appliance für die horizontale Skalierung*: 44368**.

   Alternativ können Sie den Konfigurations-Manager mithilfe der Tastenkombination für den Konfigurations-Manager über den Desktop des Servers für die Appliance für die horizontale Skalierung öffnen.
- Akzeptieren Sie die **Lizenzbedingungen**, und lesen Sie die Drittanbieterinformationen.
- Navigieren Sie im Konfigurations-Manager zu **Erforderliche Komponenten einrichten**, und führen Sie die folgenden Schritte aus:
   - **Konnektivität**: Die Appliance überprüft, ob der Server über Internetzugriff verfügt. Wenn der Server einen Proxy verwendet:
     1. Klicken Sie auf **Proxy einrichten**, um die Proxyadresse (in der Form http://ProxyIPAddress oder http://ProxyFQDN) und den Lauschport anzugeben.
     2. Geben Sie die Anmeldeinformationen an, wenn der Proxy eine Authentifizierung erfordert.
     3. Es werden nur HTTP-Proxys unterstützt.
     4. Wenn Sie Proxydetails hinzugefügt oder den Proxy und/oder die Authentifizierung deaktiviert haben, klicken Sie auf **Speichern**, um die Konnektivitätsprüfung erneut auszulösen.
   - **Uhrzeitsynchronisierung**: Die Uhrzeit der Appliance muss mit der Internetzeit synchronisiert werden, damit die Ermittlung ordnungsgemäß funktioniert.
   - **Updates installieren**: Die Appliance stellt sicher, dass die neuesten Updates installiert sind. Nachdem die Überprüfung abgeschlossen ist, können Sie auf **View appliance services** (Appliancedienste anzeigen) klicken, um den Status und die Versionen der auf dem Applianceserver ausgeführten Dienste anzuzeigen.
   - **Install VDDK** (VDDK installieren): Die Appliance überprüft, ob das VMware vSphere-VDDK (Virtual Disk Development Kit) installiert ist. Laden Sie bei Bedarf VDDK 6.7 von VMware herunter, und extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei wie auf dem Bildschirm des Appliance-Konfigurations-Managers unter **Installation instructions** (Installationsanweisungen) beschrieben am angegebenen Speicherort auf der Appliance.


### <a name="register-the-appliance-with-azure-migrate"></a>Registrieren der Appliance bei Azure Migrate

1. Fügen Sie den aus dem Portal kopierten **Azure Migrate-Projektschlüssel** ein. Wenn Sie nicht über den Schlüssel verfügen, navigieren Sie zu **Server Assessment > Discover > Manage existing appliances** (Serverbewertung > Ermitteln > Vorhandene Appliances verwalten), wählen Sie den Namen der primären Appliance aus, suchen Sie nach der damit verbundenen Appliance für die horizontale Skalierung, und kopieren Sie den entsprechenden Schlüssel.
1. Für die Authentifizierung bei Azure benötigen Sie einen Gerätecode. Wenn Sie auf **Anmelden** klicken, wird ein modales Dialogfeld mit dem Gerätecode angezeigt. Dies ist in der folgenden Abbildung dargestellt.

   :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modales Dialogfeld mit Gerätecode":::

1. Klicken Sie auf **Copy code & Login** (Code kopieren und anmelden), um den Gerätecode zu kopieren und eine Azure-Anmeldeaufforderung in einer neuen Browserregisterkarte zu öffnen. Sollte keine Anmeldung angezeigt werden, vergewissern Sie sich, dass Sie den Popupblocker im Browser deaktiviert haben.
1. Fügen Sie auf der neuen Registerkarte den Gerätecode ein, und melden Sie sich mit Ihrem Azure-Benutzernamen und dem zugehörigen Kennwort an.
   
   Die Anmeldung mit einer PIN wird nicht unterstützt.
3. Falls Sie die Registerkarte für die Anmeldung versehentlich schließen, ohne die Anmeldung durchzuführen, müssen Sie die Browserregisterkarte des Appliance-Konfigurations-Managers aktualisieren, um die Schaltfläche „Anmelden“ wieder zu aktivieren.
1. Wechseln Sie nach der erfolgreichen Anmeldung wieder zur vorherigen Registerkarte mit dem Appliance-Konfigurations-Manager.
1. Wenn das für die Protokollierung verwendete Azure-Benutzerkonto über die richtigen Berechtigungen für die während der Schlüsselgenerierung erstellten Azure-Ressourcen verfügt, wird die Registrierung der Appliance initiiert.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="Panel 2 im Appliance-Konfigurations-Manager":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Importieren der Appliancekonfiguration von der primären Appliance

Klicken Sie auf **Importieren**, um die erforderlichen Konfigurationsdateien von der primären Appliance abzurufen und den Registrierungsvorgang der Appliance für die horizontale Skalierung abzuschließen.

1. Wenn Sie auf **Importieren** klicken, wird ein Popupfenster mit Anweisungen zum Importieren der erforderlichen Konfigurationsdateien von der primären Appliance geöffnet.

:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Modales Fenster „Import configuration files“ (Konfigurationsdateien importieren)":::

1. Melden Sie sich bei der primären Appliance an (Remotedesktop), und führen Sie die folgenden PowerShell-Befehle aus:

   `PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' `
    
    `PS .\ExportConfigFiles.ps1 `

1. Kopieren Sie die ZIP-Datei, die Sie durch Ausführen der obigen Befehle erstellt haben, auf die Appliance für die horizontale Skalierung. Die ZIP-Datei enthält Konfigurationsdateien, die zum Registrieren der Appliance für die horizontale Skalierung erforderlich sind.

1. Wählen Sie im Popupfenster, das im vorherigen Schritt geöffnet wurde, den Speicherort der kopierten ZIP-Konfigurationsdatei aus, und klicken Sie auf **Speichern**.

Nachdem die Dateien erfolgreich importiert wurden, wird die Registrierung der Appliance für die horizontale Skalierung abgeschlossen, und der Zeitstempel des letzten erfolgreichen Imports wird angezeigt. Sie können die Registrierungsdetails auch anzeigen, indem Sie auf **Details anzeigen** klicken.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="Screenshot: Geräteregistrierung mit horizontaler Skalierung mit einem Azure Migrate-Projekt":::

An diesem Punkt sollten Sie erneut überprüfen, ob die Appliance für die horizontale Skalierung eine Verbindung mit Ihrer vCenter Server-Instanz herstellen kann. Klicken Sie auf **Revalidate** (Erneut überprüfen), um die vCenter Server-Konnektivität von der Appliance für die horizontale Skalierung zu überprüfen.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="Screenshot: Anzeige der zu validierenden Anmeldeinformationen und Ermittlungsquellen":::

> [!IMPORTANT]
> Wenn Sie die vCenter Server-Anmeldeinformationen für die primäre Appliance bearbeiten, sollten Sie sicherstellen, dass Sie die Konfigurationsdateien noch mal auf der Appliance für die horizontale Skalierung importieren, um die neueste Konfiguration zu erhalten und fortlaufende Replikationen fortzusetzen.<br/> Vergewissern Sie sich, dass Sie die Appliance für die horizontale Skalierung deaktivieren, wenn Sie diese nicht mehr benötigen. [**Informieren Sie sich weiter**](./common-questions-appliance.md), wie Sie die Appliance für die horizontale Skalierung deaktivieren, wenn Sie diese nicht mehr benötigen.

## <a name="replicate"></a>Replikat

1. Nachdem die Appliance für die horizontale Skalierung registriert wurde, klicken Sie auf der Kachel „Azure Migrate: Servermigration“ auf **Replizieren**.

2.  Führen Sie die Schritte auf dem Bildschirm aus, um weitere virtuelle Computer zu replizieren. 

Wenn die Appliance für die horizontale Skalierung eingerichtet ist, können Sie nun 500 VMs gleichzeitig replizieren. Außerdem können Sie über das Azure-Portal VMs in Batches von 200 Elementen migrieren.

Das Azure Migrate-Servermigrationstool kümmert sich um die Verteilung der virtuellen Computer zwischen der primären Appliance und der Appliance für die horizontale Skalierung für die Replikation. Nachdem der Replikationsvorgang abgeschlossen ist, können Sie die virtuellen Computer migrieren.

> [!TIP]
> Wenn Sie eine große Anzahl von virtuellen Computern migrieren möchten, wird empfohlen, diese für die optimale Leistung in Batches von 200 Elementen zu migrieren.
  
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde Folgendes beschrieben:
- Konfigurieren einer Appliance für die horizontale Skalierung
- Replizieren von VMs mithilfe einer Appliance für die horizontale Skalierung


[Informieren Sie sich weiter](./tutorial-migrate-vmware.md) über das Migrieren von Servern zu Azure mithilfe des Azure Migrate-Servermigrationstools.
