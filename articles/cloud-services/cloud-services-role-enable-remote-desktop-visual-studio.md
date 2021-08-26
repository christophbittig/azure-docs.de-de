---
title: Aktivieren von Remotedesktop für eine Rolle mithilfe von Visual Studio (Azure Cloud Services (klassisch))
description: Konfigurieren einer Azure-Clouddienstanwendung für Remotedesktopverbindungen
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 87ef0b84606de85a41672367426d1a5c47a81695
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823561"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-visual-studio"></a>Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services (klassisch) mithilfe von Visual Studio

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

> [!div class="op_single_selector"]
> * [Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Mit Remotedesktop können Sie auf den Desktop einer Rolle zugreifen, die in Azure ausgeführt wird. Mithilfe einer Remotedesktopverbindung können Sie Probleme mit Ihrer Anwendung diagnostizieren und beheben, während diese ausgeführt wird.

Der Veröffentlichungs-Assistent, den Visual Studio für Clouddienste bereitstellt, umfasst eine Option zum Aktivieren von Remotedesktop während des Veröffentlichungsvorgangs mit Anmeldeinformationen, die Sie bereitstellen. Diese Option ist zur Verwendung beim Einsatz von Visual Studio 2017 Version 15.4 und früher geeignet.

In Verbindung mit Visual Studio 2017 Version 15.5 und höher sollten Sie jedoch die Aktivierung von Remotedesktop über den Veröffentlichungs-Assistenten vermeiden, es sei denn, Sie arbeiten nur als einzelner Entwickler. Für jede Situation, in der das Projekt von anderen Entwicklern geöffnet werden könnte, aktivieren Sie stattdessen Remotedesktop über das Azure-Portal, über PowerShell oder von einer Releasepipeline in einem kontinuierlichen Bereitstellungsworkflow aus. Diese Empfehlung beruht auf einer Änderung der Art, wie Visual Studio über die Clouddienst-VM mit Remotedesktop kommuniziert. Dies wird in diesem Artikel erläutert.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurieren von Remotedesktop über Visual Studio 2017 Version 15.4 und früher

Wenn Sie Visual Studio 2017 Version 15.4 und früher verwenden, können Sie die Option **Remotedesktop für alle Rollen aktivieren** im Veröffentlichungs-Assistenten verwenden. Sie können den Assistenten noch mit Visual Studio 2017 Version 15.5 und höher verwenden, aber verwenden Sie nicht die Remotedesktop-Option.

1. Starten Sie in Visual Studio den Veröffentlichungs-Assistenten, indem Sie mit der rechten Maustaste im Projektmappen-Explorer auf Ihr Clouddienstprojekt klicken und **Veröffentlichen** auswählen.

2. Melden Sie sich ggf. bei Ihrem Azure-Abonnement an, und wählen Sie **Weiter**.

3. Wählen Sie auf der Seite **Einstellungen** die Option **Remotedesktop für alle Rollen aktivieren** und dann den Link **Einstellungen...** zum Öffnen des Dialogfelds **Remotedesktopkonfiguration** aus.

4. Wählen Sie am unteren Rand des Dialogfelds **Weitere Optionen** aus. Dieser Befehl zeigt ein Dropdownlistenfeld an, in dem Sie ein Zertifikat erstellen oder auswählen können, damit Sie die Anmeldeinformationen beim Herstellen einer Verbindung über Remotedesktop verschlüsseln können.

   > [!Note]
   > Die für eine Remotedesktopverbindung erforderlichen Zertifikate unterscheiden sich von den für andere Azure-Vorgänge verwendeten Zertifikaten. Das Remotezugriffszertifikat muss über einen privaten Schlüssel verfügen.

5. Wählen Sie ein Zertifikat aus der Liste oder **&lt;Erstellen...&gt;** aus. Geben Sie beim Erstellen eines neuen Zertifikats einen Anzeigenamen für das neue Zertifikat ein, wenn Sie dazu aufgefordert werden, und wählen Sie **OK** aus. Das neue Zertifikat wird im Dropdownlistenfeld angezeigt.

6. Geben Sie einen Benutzernamen und ein Kennwort ein. Sie können kein vorhandenes Konto verwenden. Verwenden Sie nicht „Administrator“ als Benutzernamen für das neue Konto.

7. Wählen Sie ein Ablaufdatum für das Konto aus. Nach diesem Datum werden Remotedesktopverbindungen blockiert.

8. Nachdem Sie alle erforderlichen Informationen angegeben haben, wählen Sie **OK** aus. Visual Studio fügt die Remotedesktopeinstellungen den `.cscfg`- und `.csdef`-Dateien Ihres Projekts hinzu, einschließlich des Kennworts, das unter Verwendung des ausgewählten Zertifikats verschlüsselt wird.

9. Führen Sie alle verbleibenden Schritte mithilfe der **Weiter**-Schaltfläche aus, und wählen Sie dann **Veröffentlichen** aus, wenn Sie bereit sind, Ihren Clouddienst zu veröffentlichen. Wenn Sie nicht für die Veröffentlichung bereit sind, wählen Sie **Abbrechen** aus, und antworten Sie mit **Ja**, wenn Sie zum Speichern der Änderungen aufgefordert werden. Sie können Ihren Clouddienst später mit diesen Einstellungen veröffentlichen.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Konfigurieren von Remotedesktop bei Verwendung von Visual Studio 2017 Version 15.5 und früher

Mit Visual Studio 2017 Version 15.5 und höher können Sie weiterhin den Veröffentlichungs-Assistenten mit einem Clouddienstprojekt verwenden. Sie können auch die Option **Remotedesktop für alle Rollen aktivieren** verwenden, wenn Sie nur als einzelner Entwickler arbeiten.

Wenn Sie als Teil eines Teams arbeiten, sollten Sie stattdessen Remotedesktop entweder im [Azure-Portal](cloud-services-role-enable-remote-desktop-new-portal.md) oder mittels [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) auf dem Azure-Clouddienst aktivieren.

Diese Empfehlung erfolgt aufgrund einer Änderung der Art, in der Visual Studio 2017 Version 15.5 und höher mit der Clouddienst-VM kommuniziert. Wenn Sie Remotedesktop über den Veröffentlichungs-Assistenten aktivieren, kommunizieren frühere Versionen von Visual Studio über das sogenannte „RDP-Plug-In" mit dem virtuellen Computer. Visual Studio 2017 Version 15.5 und höher kommuniziert stattdessen mithilfe der „RDP-Erweiterung“, was sicherer und flexibler ist. Diese Änderung ist auch konform mit der Tatsache, dass die Methoden zum Aktivieren von Remotedesktop mittels Azure-Portal und PowerShell auch die RDP-Erweiterung verwenden.

Wenn Visual Studio mit der RDP-Erweiterung kommuniziert, wird ein Kennwort im Klartextformat über TLS übertragen. Allerdings speichern die Konfigurationsdateien des Projekts nur ein verschlüsseltes Kennwort, das nur mit dem lokalen Zertifikat, das ursprünglich zum Verschlüsseln verwendet wurde, in Klartext entschlüsselt werden kann.

Wenn Sie das Clouddienstprojekt jedes Mal vom gleichen Entwicklungscomputer aus bereitstellen, ist dieses lokale Zertifikat verfügbar. In diesem Fall können Sie weiterhin die Option **Remotedesktop für alle Rollen aktivieren** im Veröffentlichungs-Assistenten verwenden.

Wenn Sie oder andere Entwickler das Clouddienstprojekt von verschiedenen Computern aus bereitstellen möchten, verfügen diese anderen Computer jedoch nicht über das erforderliche Zertifikat zum Entschlüsseln des Kennworts. Daraus resultiert folgende Fehlermeldung:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Jedes Mal, wenn Sie den Clouddienst bereitstellen, sollten Sie das Kennwort ändern, aber diese Aktion wird unpraktisch für jeden, der Remotedesktop verwendet.

Wenn Sie das Projekt gemeinsam mit einem Team nutzen, sollten Sie die Option im Veröffentlichungs-Assistenten deaktivieren und Remotedesktop stattdessen direkt über das [Azure-Portal](cloud-services-role-enable-remote-desktop-new-portal.md) oder mithilfe von [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) aktivieren.

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Bereitstellen von einem Buildserver aus mit Visual Studio 2017 Version 15.5 und höher

Sie können ein Clouddienstprojekt von einem Buildserver aus bereitstellen (z.B. mit Azure DevOps Services), auf dem Visual Studio 2017, Version 15.5 oder höher im Build-Agent installiert ist. Mit dieser Anordnung erfolgt die Bereitstellung vom gleichen Computer aus, auf dem das Verschlüsselungszertifikat verfügbar ist.

Um die RDP-Erweiterung von Azure DevOps Services zu verwenden, schließen Sie die folgenden Details in Ihre Buildpipeline ein:

1. Schließen Sie `/p:ForceRDPExtensionOverPlugin=true` in Ihre MSBuild-Argumente ein, um sicherzustellen, dass die Bereitstellung mit der RDP-Erweiterung anstelle des RDP-Plug-Ins funktioniert. Beispiel:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Fügen Sie nach Ihren Buildschritten den Schritt **Azure Cloud Service-Bereitstellung** hinzu, und legen Sie seine Eigenschaften fest.

1. Fügen Sie nach dem Bereitstellungsschritt einen **Azure Powershell**-Schritt hinzu, legen Sie für seine Eigenschaft **Anzeigename** „Azure-Bereitstellung: RDP-Erweiterung aktivieren“ (oder einen anderen geeigneten Namen) fest, und wählen Sie das entsprechende Azure-Abonnement aus.

1. Legen Sie für **Skripttyp** „Inline“ fest, und fügen Sie den folgenden Code in das Feld **Inlineskript** ein. (Sie können mit diesem Skript auch eine `.ps1`-Datei in Ihrem Projekt erstellen, für **Skripttyp** „Skriptdateipfad“ festlegen und in **Skriptpfad** den Pfad zur Datei angeben.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Herstellen einer Verbindung mit einer Azure-Rolle mithilfe von Remotedesktop

Nachdem Sie Ihren Clouddienst in Azure veröffentlicht und Remotedesktop aktiviert haben, können Sie sich mit dem Server-Explorer von Visual Studio bei der Clouddienst-VM anmelden:

1. Erweitern Sie in Server-Explorer den Knoten **Azure** und dann den Knoten für einen Clouddienst und eine seiner Rollen, um eine Liste der Instanzen anzuzeigen.

2. Klicken Sie mit der rechten Maustaste auf einen Instanzknoten, und wählen Sie **Mithilfe von Remotedesktop verbinden** aus.

3. Geben Sie den Benutzernamen und das zuvor erstellte Kennwort ein. Sie sind nun bei Ihrer Remotesitzung angemeldet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[Konfigurieren von Clouddiensten](cloud-services-how-to-configure-portal.md)
