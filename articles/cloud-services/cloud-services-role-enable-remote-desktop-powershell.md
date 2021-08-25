---
title: Verwenden von PowerShell zum Aktivieren von Remotedesktop für eine Rolle
description: Konfigurieren einer Azure-Clouddienstanwendung für Remotedesktopverbindungen mithilfe der PowerShell
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 3ebc3cf6a576a15c482120017ea14f26e376c6f6
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823210"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-powershell"></a>Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services (klassisch) mit PowerShell

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

> [!div class="op_single_selector"]
> * [Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Mit Remotedesktop können Sie auf den Desktop einer Rolle zugreifen, die in Azure ausgeführt wird. Mithilfe einer Remotedesktopverbindung können Sie Probleme mit Ihrer Anwendung diagnostizieren und beheben, während diese ausgeführt wird.

In diesem Artikel wird beschrieben, wie Sie Remotedesktop mithilfe von PowerShell für Cloud Services-Rollen aktivieren. Informationen zu den erforderlichen Komponenten für diesen Artikel finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/). PowerShell ermöglicht die Remotedesktoperweiterung, sodass Sie Remotedesktop auch nach der Bereitstellung der Anwendung aktivieren können.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurieren von Remotedesktop über PowerShell
Mit dem Cmdlet [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) können Sie Remotedesktop für bestimmte Rollen oder für alle Rollen Ihrer Clouddienstbereitstellung aktivieren. Das Cmdlet ermöglicht die Angabe des Benutzernamens und Kennworts für Remotedesktopbenutzer über den *Credential* -Parameter, der ein PSCredential-Objekt annimmt.

Wenn Sie PowerShell interaktiv verwenden, können Sie das PSCredential-Objekt durch Aufrufen des Cmdlets [Get-Credentials](/powershell/module/microsoft.powershell.security/get-credential) auf einfache Weise festlegen.

```powershell
$remoteusercredentials = Get-Credential
```

Dieser Befehl zeigt ein Dialogfeld an, in dem Sie den Benutzernamen und das Kennwort für einen Benutzer auf sichere Weise eingeben können.

Da PowerShell Automatisierungsszenarien unterstützt, können Sie das **PSCredential** -Objekt auch so einrichten, dass keine Benutzerinteraktion erforderlich ist. Zunächst müssen Sie ein sicheres Kennwort einrichten. Sie geben zunächst ein Klartextkennwort an und konvertieren es dann mit [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)in eine sichere Zeichenfolge. Als Nächstes müssen Sie diese sichere Zeichenfolge mit [ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring)in eine verschlüsselte Standardzeichenfolge konvertieren. Anschließend können Sie diese verschlüsselte Standardzeichenfolge mit [Set-Content](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176959(v=technet.10))in einer Datei speichern.

Sie können auch eine sichere Kennwortdatei erstellen, damit Sie nicht jedes Mal das Kennwort eingeben müssen. Darüber hinaus ist eine sichere Kennwortdatei besser als eine Nur-Text-Datei. Verwenden Sie das folgende PowerShell-Skript zum Erstellen einer sicheren Kennwortdatei:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Stellen Sie beim Festlegen des Kennworts sicher, dass die [Komplexitätsvoraussetzungen](/previous-versions/windows/it-pro/windows-server-2003/cc786468(v=ws.10))erfüllt sind.

Zum Erstellen des Anmeldeinformationsobjekts aus der sicheren Kennwortdatei müssen Sie den Inhalt der Datei auslesen und mit [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)in eine sichere Zeichenfolge zurückkonvertieren.

Das Cmdlet [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) nimmt auch den *Expiration* -Parameter an, der einen **DateTime** -Wert für den Zeitpunkt angibt, an dem das Benutzerkonto abläuft. Beispielsweise könnten Sie das Konto so einrichten, dass es ein paar Tage ab dem aktuellen Datum und der aktuellen Uhrzeit abläuft.

Im folgenden PowerShell-Beispiel wird die Remotedesktoperweiterung für einen Clouddienst festgelegt:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Sie können optional auch den Bereitstellungsslot und die Bereitstellungsrollen angeben, für die Sie Remotedesktop aktivieren möchten. Wenn diese Parameter nicht angegeben werden, aktiviert das Cmdlet standardmäßig Remotedesktop für alle Rollen im Slot zur Bereitstellung der **Produktion**.

Die Remotedesktoperweiterung ist immer einer Bereitstellung zugeordnet. Wenn Sie eine neue Bereitstellung für den Dienst erstellen, müssen Sie Remotedesktop für diese Bereitstellung aktivieren. Wenn Remotedesktop immer aktiviert sein soll, empfiehlt es sich, die PowerShell-Skripts in Ihren Bereitstellungsworkflow zu integrieren.

## <a name="remote-desktop-into-a-role-instance"></a>Remotedesktop in einer Rolleninstanz

Mit dem Cmdlet [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) stellen Sie eine Remotedesktopverbindung mit einer bestimmten Rolleninstanz Ihres Clouddiensts her. Sie können den *LocalPath* -Parameter zum lokalen Herunterladen der RDP-Datei verwenden. Sie können alternativ den *Launch* -Parameter verwenden, um das Dialogfeld „Remotedesktopverbindung“ für den Zugriff auf die Rolleninstanz des Clouddiensts direkt zu starten.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Überprüfen, ob die Remotedesktoperweiterung für einen Dienst aktiviert ist

Das Cmdlet [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) zeigt an, ob Remotedesktop für eine Dienstbereitstellung aktiviert oder deaktiviert ist. Das Cmdlet gibt den Benutzernamen des Remotedesktopbenutzers und die Rollen zurück, für die die Remotedesktoperweiterung aktiviert ist. Standardmäßig geschieht dies auf dem Bereitstellungsslot, und Sie können stattdessen den Stagingslot wählen.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Entfernen der Remotedesktoperweiterung für einen Dienst

Wenn Sie die Remotedesktoperweiterung bereits für eine Bereitstellung aktiviert haben und die Remotedesktopeinstellungen aktualisieren müssen, entfernen Sie zunächst die Erweiterung. Aktivieren Sie sie erneut mit den neuen Einstellungen. Wenn Sie beispielsweise ein neues Kennwort für das Remotebenutzerkonto festlegen möchten, oder das Konto abgelaufen ist. Dies ist nur bei vorhandenen Bereitstellungen erforderlich, für die die Remotedesktoperweiterung aktiviert ist. Bei neuen Bereitstellungen können Sie dagegen die Erweiterung einfach direkt anwenden.

Zum Entfernen der Remotedesktoperweiterung aus einer Bereitstellung können Sie das Cmdlet [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/remove-azureserviceremotedesktopextension) verwenden. Sie können optional auch den Bereitstellungsslot und die Bereitstellungsrollen angeben, für die Sie die Remotedesktoperweiterung entfernen möchten.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Um die Erweiterungskonfiguration vollständig zu entfernen, sollten Sie das Cmdlet *remove* mit dem **UninstallConfiguration** -Parameter aufrufen.
>
> Mit dem **UninstallConfiguration** -Parameter wird die gesamte Erweiterungskonfiguration deinstalliert, die auf den Dienst angewendet wurde. Jede Erweiterungskonfiguration ist mit der Dienstkonfiguration verbunden. Durch Aufrufen des Cmdlets *remove* ohne den **UninstallConfiguration**-Parameter wird die Zuordnung der <mark>Bereitstellung</mark> zur Erweiterungskonfiguration aufgehoben und die Erweiterung faktisch entfernt. Die Erweiterungskonfiguration ist jedoch weiterhin dem Dienst zugeordnet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[Konfigurieren von Clouddiensten](cloud-services-how-to-configure-portal.md)