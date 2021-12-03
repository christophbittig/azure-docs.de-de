---
title: PowerShell-Modul AADCloudSyncTools für die Azure AD Connect-Cloudsynchronisierung
description: In diesem Artikel wird das Installieren des Agents für die Azure AD Connect-Cloudbereitstellung beschrieben.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/03/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d45c331c8af6dda32b7a47dbdb517adf1d8a4d93
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500296"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>PowerShell-Modul AADCloudSyncTools für die Azure AD Connect-Cloudsynchronisierung

Das Modul AADCloudSyncTools stellt eine Reihe nützlicher Tools bereit, die Sie zum Verwalten Ihrer Bereitstellungen der Azure AD Connect-Cloudsynchronisierung verwenden können.

## <a name="prerequisites"></a>Voraussetzungen
Die folgenden Voraussetzungen müssen erfüllt sein:

- Alle Voraussetzungen für dieses Modul können mithilfe von `Install-AADCloudSyncToolsPrerequisites` automatisch installiert werden.
- Dieses Modul verwendet die MSAL-Authentifizierung, daher muss das Modul MSAL.PS installiert sein. Führen Sie zur Überprüfung in einem PowerShell-Fenster `Get-module MSAL.PS -ListAvailable` aus. Wenn das Modul ordnungsgemäß installiert ist, erhalten Sie eine Antwort. Sie können für die Installation der aktuellen Version von MSAL.PS `Install-AADCloudSyncToolsPrerequisites` verwenden.
- Obwohl das PowerShell-Modul AzureAD keine Voraussetzung für die Funktionalität dieses Moduls darstellt, ist es äußerst hilfreich. Aus diesem Grund wird es auch automatisch mit `Install-AADCloudSyncToolsPrerequisites` installiert. 
- Für die Installation von Modulen aus dem PowerShell-Katalog ist eine Erzwingung von TLS 1.2 erforderlich. Das Cmdlet `Install-AADCloudSyncToolsPrerequisites` legt die Erzwingung von TLS 1.2 fest, bevor alle erforderlichen Komponenten installiert werden. Um sicherzustellen, dass Sie Module manuell installieren können, legen Sie vor der Verwendung von `Install-Module` Folgendes in der PowerShell-Sitzung fest:
  ```
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>Installieren des PowerShell-Moduls AADCloudSyncTools
Führen Sie die folgenden Schritte aus, um das Modul AADCloudSyncTools zu installieren und zu verwenden:

1. Öffnen Sie Windows PowerShell mit Administratorrechten.
2. Geben Sie den folgenden Befehl ein (Sie können ihn auch kopieren und einfügen): `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`.
3. Drücken Sie die EINGABETASTE.
4. Um zu überprüfen, ob das Modul importiert wurde, geben Sie folgenden Befehl ein (Sie können ihn auch kopieren und einfügen): `Get-module AADCloudSyncTools`.
5. Jetzt sollten Informationen zum Modul angezeigt werden.
6. Führen Sie als Nächstes `Install-AADCloudSyncToolsPrerequisites` aus, um die Voraussetzungen für das Modul AADCloudSyncTools zu installieren.
7. Beim ersten Ausführen wird das Modul PoweShellGet installiert, wenn es noch nicht vorhanden ist. Schließen Sie zum Installieren des neuen Moduls PowerShellGet das PowerShell-Fenster, und öffnen Sie eine neue PowerShell-Sitzung mit Administratorrechten. 
8. Importieren Sie das Modul erneut wie in Schritt 2 beschrieben.
9. Ausführen von `Install-AADCloudSyncToolsPrerequisites` zur Installation der Module MSAL und AzureAD
11. Alle erforderlichen Komponenten sollten erfolgreich installiert worden sein. ![Installieren des Moduls](media/reference-powershell/install-1.png)
12. Jedes Mal, wenn Sie das AADCloudSyncTools-Modul in einer neuen PowerShell-Sitzung verwenden möchten, geben Sie folgenden Befehl ein bzw. kopieren Sie ihn, und fügen Sie ihn ein:
```
Import-module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
```


## <a name="aadcloudsynctools--cmdlets"></a>Cmdlets in AADCloudSyncTools
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Fordert mit dem MSAL.PS-Modul ein Token für den Azure AD-Administrator für den Zugriff auf Microsoft Graph an 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Exportiert alle Problembehandlungsdaten und packt sie in eine komprimierte Datei. Dies geschieht folgendermaßen:
 1. Festlegen der ausführliche Ablaufverfolgung und Start der Erfassung von Daten aus dem Bereitstellungs-Agent (wie `Start-AADCloudSyncToolsVerboseLogs`)
 <br>Diese Ablaufverfolgungsprotokolle finden Sie im Ordner `C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace` </br>
 2. Beenden der Datensammlung nach 3 Minuten und Deaktivieren der ausführlichen Ablaufverfolgung (wie `Stop-AADCloudSyncToolsVerboseLogs`)
 <br>Sie können mit `-TracingDurationMins` eine andere Dauer angeben oder die ausführliche Ablaufverfolgung mit `-SkipVerboseTrace` vollständig überspringen. </br>
 3. Sammelt Ereignisanzeigeprotokolle für die letzten 24 Stunden
 4. Komprimieren aller Agent-Protokolle, ausführlichen Protokolle und Ereignisanzeigeprotokolle in einer komprimierten ZIP-Datei im Ordner „Dokumente“ des Benutzers
 <br>Sie können mit `-OutputPath <folder path>` einen anderen Ausgabeordner angeben. </br>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Zeigt Details zum Azure AD-Mandanten und den Status interner Variablen an

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Verwendet Graph, um AD2AAD-Dienstprinzipale abzurufen, und gibt die Informationen zum Synchronisierungsauftrag zurück.
Kann auch mit der jeweiligen Synchronisierungsauftrags-ID als Parameter aufgerufen werden.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Verwendet Graph, um AD2AAD-Dienstprinzipale abzurufen, und gibt den Zeitplan zum Synchronisierungsauftrag zurück.
Kann auch mit der jeweiligen Synchronisierungsauftrags-ID als Parameter aufgerufen werden.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Verwendet Graph, um AD2AAD-Dienstprinzipale abzurufen, und gibt das Schema des Synchronisierungsauftrags zurück.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Verwendet Graph, um das Schema des Synchronisierungsauftrags für die angegebene Synchronisierungsauftrags-ID abzurufen, und gibt alle Bereiche der Filtergruppe aus.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Verwendet Graph, um AD2AAD-Dienstprinzipale abzurufen, und gibt die Einstellungen des Synchronisierungsauftrags zurück.
Kann auch mit der jeweiligen Synchronisierungsauftrags-ID als Parameter aufgerufen werden.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Verwendet Graph, um AD2AAD-Dienstprinzipale abzurufen, und gibt den Status des Synchronisierungsauftrags zurück.
Kann auch mit der jeweiligen Synchronisierungsauftrags-ID als Parameter aufgerufen werden.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Verwendet Graph, um die Dienstprinzipale für AD2AAD und/oder SyncFabric abzurufen.
Ohne Parameter werden nur AD2AAD-Dienstprinzipale zurückgegeben.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Prüft, ob PowerShellGet v2.2.4.1 oder höher und die Module AzureAD und MSAL.PS vorhanden sind, und installiert diese andernfalls.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Ruft eine Webanforderung auf, für die als Parameter der URI, die Methode und der Text angegeben sind

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Verwendet Azure AD PowerShell, um das aktuelle Konto (sofern vorhanden) zu löschen, und setzt die Authentifizierung mit dem Synchronisierungskonto auf ein neues Synchronisierungskonto in Azure AD zurück.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Startet eine vollständige Synchronisation.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Setzt die Synchronisierung am letzten Wasserzeichen fort.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Ändert „AADConnectProvisioningAgent.exe.config“, um die ausführliche Ablaufverfolgung zu aktivieren, und startet den Dienst AADConnectProvisioningAgent neu. Sie können den Neustart des Diensts zwar mit „-SkipServiceRestart“ verhindern, dann werden jedoch die Konfigurationsänderungen nicht übernommen.  Diese Überwachungsprotokolle finden Sie im Ordner C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Ändert „AADConnectProvisioningAgent.exe.config“, um die ausführliche Ablaufverfolgung zu deaktivieren, und startet den Dienst AADConnectProvisioningAgent neu. Sie können den Neustart des Diensts zwar mit „-SkipServiceRestart“ verhindern, dann werden jedoch die Konfigurationsänderungen nicht übernommen.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Unterbricht die Synchronisierung.

## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Worum handelt es sich bei der Azure AD Connect-Cloudsynchronisierung?](what-is-cloud-sync.md)
