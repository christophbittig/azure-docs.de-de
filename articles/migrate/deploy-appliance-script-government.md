---
title: Einrichten einer Azure Migrate-Appliance in Azure Government
description: Hier erfahren Sie, wie Sie eine Azure Migrate-Appliance in Azure Government einrichten.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 9586685bc5fd482bf3e87dcfd6848a08c74dfc4f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509666"
---
# <a name="set-up-an-appliance-for-azure-government-cloud"></a>Einrichten einer Appliance für Azure Government Cloud

Befolgen Sie die Anleitungen in diesem Artikel, um eine [Azure Migrate-Appliance](./migrate-appliance-architecture.md) für Azure Government Cloud bereitzustellen, die Folgendes ausführt:

- Ermittlung, Bewertung und Replikation ohne Agent von Servern, die in einer VMware-Umgebung ausgeführt werden
- Ermittlung und Bewertung von Servern, die in einer Hyper-V-Umgebung ausgeführt werden
- Ermittlung und Bewertung von physischen Servern oder Servern, die in anderen Clouds wie AWS, GCP, Xen usw. ausgeführt werden

Wenn Sie eine Appliance in der öffentlichen Cloud einrichten möchten, lesen Sie [diesen Artikel](deploy-appliance-script.md).

> [!NOTE]
> Die Option zum Bereitstellen einer Appliance mithilfe einer Vorlage (für Server, die in einer VMware- oder Hyper-V-Umgebung ausgeführt werden) wird in Azure Government nicht unterstützt. Sie können nur das Installationsskript verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Sie können das Skript verwenden, um die Azure Migrate-Appliance auf einem vorhandenen physischen oder virtualisierten Server bereitzustellen.

- Der Server, der als Appliance fungiert, muss Windows Server 2016 ausführen und weitere Anforderungen für [VMware](migrate-appliance.md#appliance---vmware), [Hyper-V](migrate-appliance.md#appliance---hyper-v) und [physische Server](migrate-appliance.md#appliance---physical) erfüllen.
- Wenn Sie das Skript auf einem Server ausführen, auf dem die Azure Migrate-Appliance bereits eingerichtet ist, können Sie die vorhandene Konfiguration bereinigen und eine neue Appliance mit der gewünschten Konfiguration einrichten. Wenn Sie das Skript ausführen, erhalten Sie eine Benachrichtigung wie unten gezeigt:
  
    :::image type="content" source="./media/deploy-appliance-script/script-reconfigure-appliance.png" alt-text="Screenshot: Neukonfigurieren einer Appliance.":::

## <a name="set-up-the-appliance-for-vmware"></a>Einrichten der Appliance für VMware

1. Um die Appliance einzurichten, laden Sie die ZIP-Datei namens „AzureMigrateInstaller.zip“ entweder aus dem Portal oder von [hier](https://go.microsoft.com/fwlink/?linkid=2140334) herunter.
1. Extrahieren Sie den Inhalt auf dem Server, auf dem Sie die Appliance bereitstellen möchten.
1. Führen Sie das PowerShell-Skript zum Starten des Appliance-Konfigurations-Manager aus.
1. Richten Sie die Appliance ein, und führen die Erstkonfiguration aus.

### <a name="download-the-script"></a>Herunterladen des Skripts

1. Klicken Sie unter **Migrationsziele** > **Windows-/Linux-Server und SQL Server-Instanzen** > **Azure Migrate: Ermittlung und Bewertung** auf **Ermitteln**.
2. Wählen Sie unter **Server ermitteln** > **Sind Ihre Server virtualisiert?** die Option **Ja, mit VMware vSphere-Hypervisor** aus.
3. Geben Sie einen Appliancenamen ein, und generieren Sie einen Projektschlüssel im Portal.
3. Klicken Sie auf **Herunterladen**, um die ZIP-Datei herunterzuladen.

### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Server, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Überprüfen Sie die aktuelle Applianceversion und die Hashwerte:

    **Download** | **Hashwert**
    --- | ---
    [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140337) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029


### <a name="run-the-script"></a>Führen Sie das Skript aus.

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Server, der die Appliance hostet.  Führen Sie das Skript nicht auf einem Server mit einer vorhandenen Azure Migrate-Appliance aus.

2. Starten Sie PowerShell auf dem oben genannten Server mit Administratorberechtigungen (erhöhten Rechten).

3. Ändern Sie das PowerShell-Verzeichnis in den Ordner, in den die Inhalte der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.

4. Führen Sie das Skript mit dem Namen **AzureMigrateInstaller.ps1** aus, indem Sie den folgenden Befehl ausführen:

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. Treffen Sie eine Auswahl aus den Szenario-, Cloud- und Konnektivitätsoptionen, um eine Appliance mit der gewünschten Konfiguration bereitzustellen. Die unten gezeigte Auswahl richtet beispielsweise eine Appliance für die Ermittlung, Bewertung und Migration von **Servern in Ihrer VMware-Umgebung** für ein Azure Migrate-Projekt mit **Standardkonnektivität _(öffentlicher Endpunkt)_** in **Azure Government Cloud** ein.

   :::image type="content" source="./media/deploy-appliance-script-government/script-vmware-gov-inline.png" alt-text="Screenshot: Einrichten einer Appliance mit der gewünschten Konfiguration für VMware." lightbox="./media/deploy-appliance-script-government/script-vmware-gov-expanded.png":::

6. Das Installationsskript führt folgende Schritte aus:

   - Installation von Agents und einer Webanwendung.
   - Installation von Windows-Rollen, darunter beispielsweise Windows-Aktivierungsdienst, IIS und PowerShell ISE.
   - Download und Installation eines wiederbeschreibbaren IIS-Moduls.
   - Aktualisierung eines Registrierungsschlüssels (HKLM) mit dauerhaften Einstellungsdetails für Azure Migrate.
   - Erstellung der folgenden Dateien in diesem Pfad:
     - **Konfigurationsdateien**: %Programdata%\Microsoft Azure\Config
     - **Protokolldateien**: %Programdata%\Microsoft Azure\Logs

Nach der erfolgreichen Ausführung des Skripts wird der Appliance-Konfigurations-Manager automatisch gestartet.

### <a name="verify-access"></a>Überprüfen des Zugriffs

Stellen Sie sicher, dass die Appliance eine Verbindung mit Azure-URLs für [Azure Government-Clouds](migrate-appliance.md#government-cloud-urls) herstellen kann.

## <a name="set-up-the-appliance-for-hyper-v"></a>Einrichten der Appliance für Hyper-V

1. Um die Appliance einzurichten, laden Sie die ZIP-Datei namens „AzureMigrateInstaller.zip“ entweder aus dem Portal oder von [hier](https://go.microsoft.com/fwlink/?linkid=2140334) herunter.
1. Extrahieren Sie den Inhalt auf dem Server, auf dem Sie die Appliance bereitstellen möchten.
1. Führen Sie das PowerShell-Skript zum Starten des Appliance-Konfigurations-Manager aus.
1. Richten Sie die Appliance ein, und führen die Erstkonfiguration aus.

### <a name="download-the-script"></a>Herunterladen des Skripts

1.  Klicken Sie unter **Migrationsziele** > **Windows-/Linux-Server und SQL Server-Instanzen** > **Azure Migrate: Ermittlung und Bewertung** auf **Ermitteln**.
2.  Wählen Sie unter **Server ermitteln** > **Sind Ihre Server virtualisiert?** die Option **Ja, mit Hyper-V** aus.
3. Geben Sie einen Appliancenamen ein, und generieren Sie einen Projektschlüssel im Portal.
3. Klicken Sie auf **Herunterladen**, um die ZIP-Datei herunterzuladen. 

### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Server, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Überprüfen Sie die aktuelle Applianceversion und die Hashwerte:

    **Download** | **Hashwert**
    --- | ---
    [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140424) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

### <a name="run-the-script"></a>Führen Sie das Skript aus.

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Server, der die Appliance hostet.  Führen Sie das Skript nicht auf einem Server mit einer vorhandenen Azure Migrate-Appliance aus.

2. Starten Sie PowerShell auf dem oben genannten Server mit Administratorberechtigungen (erhöhten Rechten).

3. Ändern Sie das PowerShell-Verzeichnis in den Ordner, in den die Inhalte der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.

4. Führen Sie das Skript mit dem Namen **AzureMigrateInstaller.ps1** aus, indem Sie den folgenden Befehl ausführen:

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. Treffen Sie eine Auswahl aus den Szenario-, Cloud- und Konnektivitätsoptionen, um eine Appliance mit der gewünschten Konfiguration bereitzustellen. Die unten gezeigte Auswahl richtet beispielsweise eine Appliance für die Ermittlung und Bewertung von **Servern in Ihrer Hyper-V-Umgebung** für ein Azure Migrate-Projekt mit **Standardkonnektivität _(öffentlicher Endpunkt)_** in **Azure Government Cloud** ein.

    :::image type="content" source="./media/deploy-appliance-script-government/script-hyperv-gov-inline.png" alt-text="Screenshot: Einrichten einer Appliance mit der gewünschten Konfiguration für Hyper-V." lightbox="./media/deploy-appliance-script-government/script-hyperv-gov-expanded.png":::

6. Das Installationsskript führt folgende Schritte aus:

    - Installation von Agents und einer Webanwendung.
    - Installation von Windows-Rollen, darunter beispielsweise Windows-Aktivierungsdienst, IIS und PowerShell ISE.
    - Download und Installation eines wiederbeschreibbaren IIS-Moduls.
    - Aktualisierung eines Registrierungsschlüssels (HKLM) mit dauerhaften Einstellungsdetails für Azure Migrate.
    - Erstellung der folgenden Dateien in diesem Pfad:
    - **Konfigurationsdateien**: %Programdata%\Microsoft Azure\Config
    - **Protokolldateien**: %Programdata%\Microsoft Azure\Logs

Nach der erfolgreichen Ausführung des Skripts wird der Appliance-Konfigurations-Manager automatisch gestartet.

### <a name="verify-access"></a>Überprüfen des Zugriffs

Stellen Sie sicher, dass die Appliance eine Verbindung mit Azure-URLs für [Azure Government-Clouds](migrate-appliance.md#government-cloud-urls) herstellen kann.


## <a name="set-up-the-appliance-for-physical-servers"></a>Einrichten der Appliance für physische Server

1. Um die Appliance einzurichten, laden Sie die ZIP-Datei namens „AzureMigrateInstaller.zip“ entweder aus dem Portal oder von [hier](https://go.microsoft.com/fwlink/?linkid=2140334) herunter.
1. Extrahieren Sie den Inhalt auf dem Server, auf dem Sie die Appliance bereitstellen möchten.
1. Führen Sie das PowerShell-Skript zum Starten des Appliance-Konfigurations-Manager aus.
1. Richten Sie die Appliance ein, und führen die Erstkonfiguration aus.

### <a name="download-the-script"></a>Herunterladen des Skripts

1. Klicken Sie unter **Migrationsziele** > **Windows-/Linux-Server und SQL Server-Instanzen** > **Azure Migrate: Ermittlung und Bewertung** auf **Ermitteln**.
2. Wählen Sie unter **Server ermitteln** > **Sind Ihre Server virtualisiert?** die Option **Physische oder andere (AWS, GCP, Xen usw.)** aus.
3. Klicken Sie auf **Herunterladen**, um die ZIP-Datei herunterzuladen.

### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Server, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Überprüfen Sie die aktuelle Applianceversion und die Hashwerte:

    **Download** | **Hashwert**
    --- | ---
    [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140338) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> Das gleiche Skript kann verwendet werden, um eine physische Appliance für Azure Government Cloud mit Konnektivität für öffentliche oder private Endpunkte einzurichten.

### <a name="run-the-script"></a>Führen Sie das Skript aus.

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Server, der die Appliance hostet.  Führen Sie das Skript nicht auf einem Server mit einer vorhandenen Azure Migrate-Appliance aus.

2. Starten Sie PowerShell auf dem oben genannten Server mit Administratorberechtigungen (erhöhten Rechten).

3. Ändern Sie das PowerShell-Verzeichnis in den Ordner, in den die Inhalte der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.

4. Führen Sie das Skript mit dem Namen **AzureMigrateInstaller.ps1** aus, indem Sie den folgenden Befehl ausführen:

    `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. Treffen Sie eine Auswahl aus den Szenario-, Cloud- und Konnektivitätsoptionen, um eine Appliance mit der gewünschten Konfiguration bereitzustellen. Mit der nachfolgend gezeigten Auswahl richten Sie beispielsweise eine Appliance zum Ermitteln und Bewerten **physischer Server** _(oder in anderen Clouds wie AWS, GCP, Xen, usw. ausgeführter Server)_ für ein Azure Migrate-Projekt mit **Standardkonnektivität** _(öffentlicher Endpunkt)_ in **Azure Government Cloud** ein.

    :::image type="content" source="./media/deploy-appliance-script-government/script-physical-gov-inline.png" alt-text="Screenshot: Einrichten einer Appliance mit der gewünschten Konfiguration für physische Server." lightbox="./media/deploy-appliance-script-government/script-physical-gov-expanded.png":::

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

### <a name="verify-access"></a>Überprüfen des Zugriffs

Stellen Sie sicher, dass die Appliance eine Verbindung mit Azure-URLs für [Azure Government-Clouds](migrate-appliance.md#government-cloud-urls) herstellen kann.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Bereitstellen der Appliance müssen Sie die Erstkonfiguration ausführen und die Appliance beim Projekt registrieren.

- Richten Sie die Appliance für [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance) ein.
- Richten Sie die Appliance für [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) ein.
- Richten Sie die Appliance für [physische Server](how-to-set-up-appliance-physical.md) ein.
