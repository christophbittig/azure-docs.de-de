---
title: 'Konfigurieren von MSIX App Attach für Azure Virtual Desktop mit PowerShell-Skripts: Azure'
description: Erfahren Sie, wie Sie PowerShell-Skripts für MSIX App Attach für Azure Virtual Desktop erstellen.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6b54afdb77bd1fc3a958b959dad4fcb030e1fe2f
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122350884"
---
# <a name="create-powershell-scripts-for-msix-app-attach"></a>Erstellen von PowerShell-Skripts für das MSIX-Feature zum Anfügen von Apps

In diesem Thema erfahren Sie, wie Sie PowerShell-Skripts für MSIX App Attach einrichten.

## <a name="install-certificates"></a>Installieren von Zertifikaten

Sie müssen Zertifikate auf allen Sitzungshosts im Hostpool installieren, auf denen die Apps aus Ihren MSIX App Attach-Paketen gehostet werden.

Wenn Ihre App ein Zertifikat verwendet, das nicht öffentlich vertrauenswürdig ist oder selbst signiert wurde, wird dieses wie folgt installiert:

1. Klicken Sie mit der rechten Maustaste auf das Paket, und wählen Sie **Eigenschaften** aus.
2. Wählen Sie im daraufhin angezeigten Fenster die Registerkarte **Digitale Signaturen** aus. Die Liste auf der Registerkarte sollte nur ein Element enthalten. Wählen Sie dieses Element aus, um es hervorzuheben, und wählen Sie **Details** aus.
3. Wenn das Fenster mit den Details zur digitalen Signatur angezeigt wird, klicken Sie auf die Registerkarte **Allgemein**, dann auf **View Certificate** (Zertifikat anzeigen) und schließlich auf **Install certificate** (Zertifikat installieren).
4. Wenn das Installationsprogramm geöffnet wird, wählen Sie **Lokaler Computer** als Speicherort aus, und klicken Sie dann auf **Weiter**.
5. Wenn Sie vom Installationsprogramm gefragt werden, ob Sie zulassen möchten, dass die App Änderungen an Ihrem Gerät vornimmt, wählen Sie **Ja** aus.
6. Wählen Sie **Alle Zertifikate in folgendem Speicher speichern** und dann **Durchsuchen** aus.
7. Wenn das Fenster zum Auswählen des Zertifikatspeichers angezeigt wird, wählen Sie **Vertrauenswürdige Personen** aus, und klicken Sie dann auf **OK**.
8. Klicken Sie auf **Weiter** und dann auf **Fertig stellen**.

## <a name="enable-microsoft-hyper-v"></a>Aktivieren von Microsoft Hyper-V

Microsoft Hyper-V muss aktiviert sein, da der Befehl `Mount-VHD` für das Staging und der Befehl `Dismount-VHD` zum Aufheben des Stagings erforderlich ist.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Diese Änderung erfordert, dass Sie den virtuellen Computer neu starten.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Vorbereiten von PowerShell-Skripts für das MSIX-Feature zum Anfügen von Apps

Das MSIX-Feature zum Anfügen von Apps weist vier verschiedene Phasen auf, die in der folgenden Reihenfolge ausgeführt werden müssen:

1. Phase
2. Register
3. Registrierung aufheben
4. Staging aufheben

In jeder Phase wird ein PowerShell-Skript erstellt. Die Beispielskripts für die einzelnen Phasen sind [hier](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach) verfügbar.

### <a name="stage-powershell-script"></a>Stagen eines PowerShell-Skripts

Bevor Sie die PowerShell-Skripts aktualisieren, stellen Sie sicher, dass Sie über die Volume-GUID des Volumes auf der VHD verfügen. So rufen Sie die Volume-GUID ab:

1.  Öffnen Sie die Netzwerkfreigabe, in der sich die VHD innerhalb des virtuellen Computers befindet, auf dem Sie das Skript ausführen möchten.

2.  Klicken Sie mit der rechten Maustaste auf die VHD, und wählen Sie **Einbinden** aus. Dadurch wird die VHD in einen Laufwerkbuchstaben eingebunden.

3.  Nachdem Sie die VHD eingebunden haben, wird das Fenster **Datei-Explorer** geöffnet. Erfassen des übergeordneten Ordners und Aktualisieren der Variable **$parentFolder**

    >[!NOTE]
    >Wenn kein übergeordneter Ordner angezeigt wird, bedeutet dies, dass MSIX nicht ordnungsgemäß erweitert wurde. Wiederholen Sie den vorherigen Abschnitt, und versuchen Sie es noch mal.

4.  Öffnen Sie den übergeordneten Ordner. Wenn das Paket ordnungsgemäß erweitert wurde, wird ein Ordner mit dem gleichen Namen wie das Paket angezeigt. Aktualisieren Sie die Variable **$packageName**, sodass sie mit dem Namen dieses Ordners übereinstimmt.

    Beispiel: `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Öffnen Sie eine Eingabeaufforderung, und geben Sie **mountvol** ein. Mit diesem Befehl wird eine Liste von Volumes und deren GUIDs angezeigt. Kopieren Sie die GUID des Volumes, für das der Laufwerkbuchstabe mit dem Laufwerk übereinstimmt, in das Sie die VHD in Schritt 2 eingebunden haben.

    Wenn Sie in diesem Beispiel Ihre VHD in Laufwerk C: eingebunden haben, sollten Sie z.B. in dieser Beispielausgabe für den Befehl „mountvol“ den Wert über `C:\` kopieren:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Aktualisieren Sie die Variable **$volumeGuid** mit der soeben kopierten Volume-GUID.

7. Öffnen Sie eine PowerShell-Eingabeaufforderung mit Administratorrechten, und aktualisieren Sie das folgende PowerShell-Skript mit den Variablen, die für Ihre Umgebung gelten.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName 
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>Registrieren eines PowerShell-Skripts

Um das Registrierungsskript auszuführen, führen Sie die folgenden PowerShell-Cmdlets mit den Platzhalterwerten aus, die durch Werte ersetzt wurden, die für Ihre Umgebung gelten.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>Aufheben der Registrierung eines PowerShell-Skripts

Ersetzen Sie in diesem Skript den Platzhalter für **$packageName** durch den Namen des Pakets, das Sie testen.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>Aufheben des Stagings des PowerShell-Skripts

Ersetzen Sie in diesem Skript den Platzhalter für **$packageName** durch den Namen des Pakets, das Sie testen. In einer Produktionsbereitstellung wäre es am besten, dies beim Herunterfahren auszuführen.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

>[!NOTE]
>Sie können das Gerät herunterfahren, auch wenn der **$volumeGuid**-Punkt nach Ausführung des Skripts zur Aufhebung des Stagings erhalten bleibt.

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Einrichten von Simulationsskripts für den Agent für das Feature zum Anfügen von Apps

Nachdem Sie die Skripts erstellt haben, können Benutzer diese manuell ausführen oder sie so einrichten, dass sie automatisch als Skripts zum Starten, Anmelden, Abmelden und Herunterfahren ausgeführt werden. Weitere Informationen zu diesen Arten von Skripts finden Sie unter [Verwenden von Skripts zum Starten, Herunterfahren, Anmelden und Abmelden in der Gruppenrichtlinie](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Jedes dieser automatischen Skripts führt eine Phase der Skripts zum Anfügen von Apps aus:

- Das Startskript führt das Stagingskript aus.
- Das Anmeldeskript führt das Registrierungsskript aus.
- Das Abmeldeskript führt das Skript zum Aufheben der Registrierung aus.
- Das Skript zum Herunterfahren führt das Skript zum Aufheben des Stagings aus.

>[!NOTE]
>Sie können den Aufgabenplaner mit dem Stagingskript ausführen. Legen Sie zum Ausführen des Skripts den Aufgabenauslöser auf **Beim Start des Computers** fest, und aktivieren Sie dann **Mit höchsten Privilegien ausführen**.

## <a name="use-packages-offline"></a>Offlineverwendung von Paketen

Bei der Verwendung von Paketen aus [Microsoft Store for Business](https://businessstore.microsoft.com/) oder [Microsoft Store for Education](https://educationstore.microsoft.com/) in Ihrem Netzwerk oder auf Geräten, die nicht mit dem Internet verbunden sind, müssen Sie die Paketlizenzen aus dem Microsoft Store abrufen und auf Ihrem Gerät installieren, um die App erfolgreich ausführen zu können. Wenn Ihr Gerät online ist und eine Verbindung mit Microsoft Store for Business herstellen kann, sollten die erforderlichen Lizenzen automatisch heruntergeladen werden. Wenn Sie jedoch offline sind, müssen Sie die Lizenzen manuell einrichten.

Um die Lizenzdateien zu installieren, müssen Sie ein PowerShell-Skript verwenden, das die MDM_EnterpriseModernAppManagement_StoreLicenses02_01-Klasse im WMI-Bridge-Anbieter aufruft.

So richten Sie die Lizenzen für die Offlineverwendung ein:

1. Laden Sie das App-Paket, die Lizenzen und die erforderlichen Frameworks aus Microsoft Store for Business herunter. Sie benötigen sowohl die codierten als auch die nicht codierten Lizenzdateien. Ausführliche Downloadanweisungen finden Sie [hier](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Aktualisieren Sie die folgenden Variablen im Skript für Schritt 3:
      1. `$contentID` ist der ContentID-Wert aus der nicht codierten Lizenzdatei (XML-Datei). Sie können die Lizenzdatei in einem Text-Editor Ihrer Wahl öffnen.
      2. `$licenseBlob` ist die vollständige Zeichenfolge für das Lizenzierungsblob in der codierten Lizenzdatei (BIN-Datei). Sie können die codierte Lizenzdatei in einem Text-Editor Ihrer Wahl öffnen.
3. Führen Sie das folgende Skript an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten aus. Ein guter Zeitpunkt, um die Lizenzinstallation durchzuführen, ist am Ende des [Stagingskripts](#stage-powershell-script), das ebenfalls an einer Eingabeaufforderung mit Administratorberechtigungen ausgeführt werden muss.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Nächste Schritte

Dieses Feature wird zurzeit nicht unterstützt, aber Sie können der Community in der [Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) Fragen stellen.

Sie können Ihr Feedback bezüglich Azure Virtual Desktop auch im [Azure Virtual Desktop-Feedback-Hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) hochladen.
