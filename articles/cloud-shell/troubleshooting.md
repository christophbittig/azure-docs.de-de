---
title: Problembehandlung für Azure Cloud Shell | Microsoft-Dokumentation
description: Problembehandlung für Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7cdd812347450a8798ed4fb8e6e69f71c725449f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225391"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Problembehandlung und Einschränkungen bei Azure Cloud Shell

Zu den bekannten Lösungen für die Behandlung von Problemen in Azure Cloud Shell gehören Folgende:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Allgemeine Problembehandlung

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Fehler beim Ausführen von AzureAD-Cmdlets in PowerShell

- **Details**: Beim Ausführen von AzureAD-Cmdlets wie `Get-AzureADUser` in Cloud Shell wird möglicherweise folgender Fehler angezeigt: `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`. 
- **Lösung:** Führen Sie das `Connect-AzureAD`-Cmdlet aus. In der Vergangenheit wurde dieses Cmdlet beim PowerShell-Start automatisch in Cloud Shell ausgeführt. Für eine verkürzte Startzeit wird das Cmdlet nun nicht mehr automatisch ausgeführt. Sie können das frühere Verhalten wiederherstellen, indem Sie `Connect-AzureAD` zur $PROFILE-Datei in PowerShell hinzufügen.

### <a name="early-timeouts-in-firefox"></a>Frühe Timeouts in Firefox

- **Details**: Cloud Shell nutzt einen offenen Websocket für die Weitergabe der Ein- und Ausgaben an den Browser. Firefox verfügt über vordefinierte Richtlinien, die den Websocket vorzeitig schließen und damit frühe Timeouts in Cloud Shell verursachen können.
- **Lösung:** Öffnen Sie Firefox, und navigieren Sie im Feld „URL“ zu „about:config“. Suchen Sie nach „network.websocket.timeout.ping.request“, und ändern Sie den Wert von 0 in 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Deaktivieren von Cloud Shell in einer gesperrten Netzwerkumgebung

- **Details**: Administratoren möchten möglicherweise den Zugriff auf Cloud Shell für Benutzer deaktivieren. Cloud Shell nutzt den Zugriff auf die Domäne `ux.console.azure.com`, der verweigert werden kann, wodurch jeglicher Zugriff auf Einstiegspunkte von Cloud Shell unterbrochen wird, einschließlich des Zugriffs auf portal.azure.com, shell.azure.com, die Azure-Kontoerweiterung für Visual Studio Code und docs.microsoft.com. In der US Government-Cloud ist der Einstiegspunkt `ux.console.azure.us`, und es gibt keine Entsprechung für shell.azure.us.
- **Lösung:** Beschränken Sie den Zugriff auf `ux.console.azure.com` oder `ux.console.azure.us` über die Netzwerkeinstellungen für Ihre Umgebung. Das Cloud Shell-Symbol ist dann immer noch im Azure-Portal vorhanden, es kann aber keine Verbindung mit dem Dienst hergestellt werden.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Speicherdialogfeld – Fehler: 403 RequestDisallowedByPolicy

- **Details**: Das Erstellen eines Speicherkontos über Cloud Shell ist aufgrund einer von Ihrem Administrator eingerichteten Azure-Richtlinienzuweisung nicht erfolgreich. Zu den Fehlermeldung zählt: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Lösung:** Wenden Sie sich an Ihren Azure-Administrator, um die Azure-Richtlinienzuweisung, die die Speichererstellung verhindert, zu entfernen oder zu aktualisieren.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Speicherdialogfeld – Fehler: 400 DisallowedOperation

- **Details**: Mit einem Azure Active Directory-Abonnement kann kein Speicher erstellt werden.
- **Lösung:** Verwenden Sie ein Azure-Abonnement, das die Erstellung von Speicherressourcen unterstützt. Azure AD-Abonnements sind nicht in der Lage, Azure-Ressourcen zu erstellen.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminalausgabe – Fehler: Fehler beim Herstellen einer Verbindung mit dem Terminal: Websocket kann nicht eingerichtet werden. Drücken Sie `Enter`, um erneut eine Verbindung herzustellen.
- **Details**: Cloud Shell erfordert, dass eine Websocketverbindung mit der Cloud Shell-Infrastruktur hergestellt werden kann.
- **Lösung:** Überprüfen Sie, ob Sie Ihre Netzwerkeinstellungen so konfiguriert haben, dass HTTPS- und Websocketanforderungen an Domänen unter *.console.azure.com gesendet werden können.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Festlegen Ihrer Cloud Shell-Verbindung für die Unterstützung von TLS 1.2
 - **Details**: Um die Version von TLS für Ihre Verbindung mit Cloud Shell zu definieren, müssen Sie browserspezifische Einstellungen festlegen.
 - **Lösung:** Navigieren Sie zu den Sicherheitseinstellungen Ihres Browsers, und aktivieren Sie das Kontrollkästchen neben „TLS 1.2 verwenden“.

## <a name="bash-troubleshooting"></a>Problembehandlung bei Bash

### <a name="cannot-run-the-docker-daemon"></a>Der Docker-Daemon kann nicht ausgeführt werden.

- **Details**: Cloud Shell nutzt einen Container zum Hosten Ihrer Shellumgebung. Daher ist die Ausführung des Daemons nicht zulässig.
- **Lösung:** Nutzen Sie das standardmäßig installierte [Docker Machine](https://docs.docker.com/machine/overview/)-Tool, um Docker-Container von einem Docker-Remotehost aus zu verwalten.

## <a name="powershell-troubleshooting"></a>Problembehandlung bei PowerShell

### <a name="gui-applications-are-not-supported"></a>GUI-Anwendungen werden nicht unterstützt.

- **Details**: Wenn ein Benutzer eine GUI-Anwendung startet, wird die Eingabeaufforderung nicht wieder angezeigt. Wenn ein Benutzer beispielsweise ein privates GitHub-Repository klont, für das die zweistufige Authentifizierung aktiviert ist, wird ein Dialogfeld zur Durchführung der zweistufigen Authentifizierung angezeigt.
- **Lösung:** Schließen Sie die Shell, und öffnen Sie sie erneut.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Problembehandlung bei der Remoteverwaltung von virtuellen Azure-Computern
> [!NOTE]
> Azure-VMs müssen über eine öffentliche IP-Adresse verfügen.

- **Details**: Aufgrund der Standardeinstellungen der Windows-Firewall für WinRM kann der folgende Fehler angezeigt werden: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Lösung:**  Führen Sie `Enable-AzVMPSRemoting` aus, damit das PowerShell-Remoting auf dem Zielcomputer vollständig unterstützt wird.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` führt nicht zur Aktualisierung des Ergebnisses im Azure-Laufwerk

- **Details**: Standardmäßig werden die Ergebnisse von `dir` auf dem Azure-Laufwerk zwischengespeichert, um die Benutzeroberfläche zu optimieren.
- **Lösung:** Nachdem Sie eine Azure-Ressource erstellt, aktualisiert oder entfernt haben, führen Sie `dir -force` aus, um die Ergebnisse auf dem Azure-Laufwerk zu aktualisieren.

## <a name="general-limitations"></a>Allgemeine Einschränkungen

Für Azure Cloud Shell gelten die folgenden bekannten Einschränkungen:

### <a name="quota-limitations"></a>Kontingenteinschränkungen

Azure Cloud Shell hat ein Limit von 20 gleichzeitigen Benutzern pro Mandant pro Region. Wenn Sie versuchen, mehr gleichzeitige Sitzungen als das Limit zu öffnen, wird der Fehler „Tenant User Over Quota“ (Mandantenbenutzer über Kontingent) angezeigt. Wenn Sie einen legitimen Bedarf haben, mehr Sitzungen zu öffnen (z. B. für Trainingssitzungen), wenden Sie sich im Vorfeld Ihres erwarteten Bedarfs an den Support, um eine Erhöhung des Kontingents anzufordern.

Cloud Shell wird als kostenloser Dienst bereitgestellt und dient der Konfiguration Ihrer Azure-Umgebung, nicht als allgemeine Computingplattform. Übermäßige automatisierte Nutzung kann als Verletzung der Azure-Vertragsbedingungen angesehen werden und dazu führen, dass der Zugriff auf Cloud Shell blockiert wird.

### <a name="system-state-and-persistence"></a>Systemstatus und Persistenz

Der Computer mit Ihrer Cloud Shell-Sitzung besteht nur vorübergehend und wird recycelt, wenn die Sitzung 20 Minuten lang inaktiv ist. Für Cloud Shell muss eine Azure-Dateifreigabe bereitgestellt werden. Daher muss Ihr Abonnement Speicherressourcen für den Zugriff auf Cloud Shell einrichten können. Weitere Überlegungen:

- Mit eingebundenem Speicher werden nur Änderungen im `clouddrive`-Verzeichnis gespeichert. In Bash bleibt Ihr `$HOME`-Verzeichnis ebenfalls erhalten.
- Azure-Dateifreigaben können nur innerhalb Ihrer [zugewiesenen Region](persisting-shell-storage.md#mount-a-new-clouddrive) eingebunden werden.
  - Führen Sie in Bash `env` aus, um für Ihre Region die Einstellung `ACC_LOCATION` zu ermitteln.
- Azure Files unterstützt nur Konten mit lokal redundantem Speicher und georedundantem Speicher.

### <a name="browser-support"></a>Browserunterstützung

Cloud Shell unterstützt die aktuellen Versionen der folgenden Browser:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari im privaten Modus wird nicht unterstützt.

### <a name="copy-and-paste"></a>Kopieren und Einfügen

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Usage limits (Nutzungseinschränkungen)

Cloud Shell ist für interaktive Anwendungsfälle konzipiert. Daher werden lange Sitzungen ohne Interaktion ohne Vorwarnung beendet.

### <a name="user-permissions"></a>Benutzerberechtigungen

Berechtigungen werden als reguläre Benutzer ohne sudo-Zugriff festgelegt. Installationen außerhalb des Verzeichnisses `$Home` werden nicht gespeichert.

### <a name="supported-entry-point-limitations"></a>Unterstützte Einstiegspunkteinschränkungen

Cloud Shell-Einstiegspunkte wie Visual Studio Code und Windows-Terminal unterstützen, neben dem Azure-Portal, keine Verwendung von Befehlen, die UX-Komponenten in Cloud Shell ändern, z. B. `Code`.

## <a name="bash-limitations"></a>Bash-Einschränkungen

### <a name="editing-bashrc"></a>Bearbeiten von „.bashrc“

Gehen Sie bei der Bearbeitung von „.bashrc“ vorsichtig vor, da sonst unerwartete Fehler in Cloud Shell auftreten können.

## <a name="powershell-limitations"></a>PowerShell-Einschränkungen

### <a name="preview-version-of-azuread-module"></a>Vorschauversion des Azure AD-Moduls

Derzeit ist `AzureAD.Standard.Preview` verfügbar, wobei es sich um eine Vorschauversion eines auf .NET Standard basierenden Moduls handelt. Dieses Modul verfügt über die gleiche Funktionalität wie `AzureAD`.

## <a name="personal-data-in-cloud-shell"></a>Personenbezogene Daten in Cloud Shell

Azure Cloud Shell nimmt Ihre personenbezogenen Daten ernst. Die vom Azure Cloud Shell-Dienst erfassten und gespeicherten Daten werden verwendet, um Standardwerte für Ihr Benutzererlebnis bereitzustellen, wie Ihre zuletzt verwendete Shell, Ihr bevorzugter Schriftgrad, Ihre bevorzugte Schriftart und Dateifreigabedetails, die das Cloudlaufwerk unterstützen. Wenn Sie diese Daten exportieren oder löschen möchten, hilft Ihnen die folgende Anleitung weiter.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportieren
Um die Benutzereinstellungen zu **exportieren**, die Cloud Shell für Sie speichert (wie bevorzugte Shell, Schriftgrad und Schriftart), führen Sie die folgenden Befehle aus.

1. Starten von Cloud Shell.

2. Führen Sie die folgenden Befehle in Bash oder PowerShell aus:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

Mit PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Löschen
Um die Benutzereinstellungen zu **löschen**, die Cloud Shell für Sie speichert (wie bevorzugte Shell, Schriftgrad und Schriftart), führen Sie die folgenden Befehle aus. Wenn Sie Cloud Shell das nächste Mal starten, werden Sie aufgefordert, erneut eine Dateifreigabe einzubinden. 

>[!Note]
> Wenn Sie Ihre Benutzereinstellungen löschen, wird die eigentliche Azure Files-Freigabe nicht gelöscht. Navigieren Sie zu Ihrer Azure Files-Instanz, um diese Aktion durchzuführen.

1. Starten Sie Cloud Shell oder eine lokale Shell entweder mithilfe von Azure PowerShell oder der installierten Azure CLI.

2. Führen Sie die folgenden Befehle in Bash oder PowerShell aus:

Bash:

  ```
  token=(az account get-access-token --resource "https://management.azure.com/" | jq -r ".access_token")
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

Mit PowerShell:

  ```powershell
  $token= (Get-AzAccessToken -Resource  https://management.azure.com/).Token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Azure Government-Einschränkungen
Auf Azure Cloud Shell in Azure Government kann nur über das Azure-Portal zugegriffen werden.

>[!Note]
> Das Herstellen einer Verbindung mit GCC High- oder Government DoD-Clouds für Exchange Online wird derzeit nicht unterstützt.
