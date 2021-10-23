---
title: Installieren von Language Packs auf Windows 11 Enterprise-VMs in Azure Virtual Desktop – Azure
description: So installieren Sie Language Packs für Windows 11 Enterprise-VMs in Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 10/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 365d2821ce0fec8fdf901ad4dc663e8325267803
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536621"
---
# <a name="add-languages-to-a-windows-11-enterprise-image"></a>Hinzufügen von Sprachen zu einem Windows 11-Enterprise-Image

Es ist wichtig, sicherzustellen, dass Benutzer in Ihrer Organisation aus der ganzen Welt Ihre Azure Virtual Desktop-Bereitstellung verwenden können. Daher können Sie das Windows 11 Enterprise-Image, das Sie für Ihre virtuellen Computer (VMs) verwenden, so anpassen, dass es unterschiedliche Sprachpakete bietet. Ab Windows 11 können Benutzerkonten ohne Administratorrechte jetzt sowohl eine Anzeigesprache als auch die entsprechenden Sprachfeatures hinzufügen. Dieses Feature hat den Vorteil, dass Sie keine Language Packs für Benutzer in einem persönlichen Hostpool mehr vorab installieren müssen. Bei gepoolten Hostpools empfehlen wir jedoch weiterhin, die Sprachen hinzuzufügen, die Sie einem benutzerdefinierten Image hinzufügen möchten. Die Anweisungen in diesem Artikel gelten sowohl für Single-Session- als auch für Multi-Session-Versionen von Windows 11 Enterprise.

Wenn Ihre Organisation Benutzer mit mehreren verschiedenen Sprachen umfasst, haben Sie zwei Möglichkeiten:

- Sie erstellen einen dedizierten Hostpool mit einem benutzerdefinierten Image für jede Sprache.
- Sie haben mehrere Benutzer mit unterschiedlichen Sprachen im selben Hostpool.

Die zweite Option ist im Hinblick auf Ressourcen und Kosten effizienter, erfordert jedoch einige zusätzliche Arbeitsschritte. Dieser Artikel zeigt Ihnen Schritt für Schritt, wie Sie ein Image erstellen, dass Benutzer mit allen möglichen Sprachen und Lokalisierungsanforderungen unterstützen kann.

## <a name="requirements"></a>Requirements (Anforderungen)

Bevor Sie einer Windows 11 Enterprise-VM Sprachen hinzufügen können, benötigen Sie Folgendes:

-   Eine Azure-VM mit installiertem Windows 11 Enterprise
-   Ein ISO für Sprachen und optionale Features. Sie können das ISO unter [Windows 11 Language and Optional Features LoF ISO](https://software-download.microsoft.com/download/sg/22000.1.210604-1628.co_release_amd64fre_CLIENT_LOF_PACKAGES_OEM.iso) herunterladen.
-   Eine Azure Files-Freigabe oder eine Dateifreigabe auf einer Windows-Dateiserver-VM

>[!NOTE]
>Der Zugriff auf die Dateifreigabe (Repository) muss von der Azure-VM aus möglich sein, die Sie zum Erstellen des benutzerdefinierten Images verwenden werden.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Erstellen eines Inhaltsrepositorys für Sprachpakete und Features bei Bedarf (FODs)

So erstellen Sie das Inhaltsrepository, das Sie zum Hinzufügen von Sprachen und Features zu Ihrer VM verwenden:

1. Öffnen Sie die VM, der Sie Sprachen hinzufügen möchten, in Azure.

2. Öffnen Sie die heruntergeladene ISO-Datei in [Anforderungen](#requirements), und binden Sie sie in der VM ein.

3. Erstellen Sie einen Ordner in der Dateifreigabe.

4. Kopieren Sie den gesamten Inhalt des Ordners **LanguagesAndOptionalFeatures** in der ISO in den erstellten Ordner.

     >[!NOTE]
     > Wenn Sie nur eingeschränkten Speicher haben, können Sie die eingebundene ISO-Datei „Sprachen und optionale Features“ als Repository verwenden. Informationen zum Erstellen eines Repositorys finden Sie unter [Erstellen eines benutzerdefinierten FOD- und Language Pack-Repositorys](/windows-hardware/manufacture/desktop/languages-overview#build-a-custom-fod-and-language-pack-repository).

     >[!IMPORTANT]
     > Einige Sprachen erfordern zusätzliche Schriftarten, die in Satellitenpaketen mit anderen Benennungskonventionen enthalten sind. Beispielsweise enthalten die Namen der Dateien für japanische Schrift die Zeichenfolge „Jpan“.
     >
     > [!div class="mx-imgBorder"]
     > ![Beispiel der Sprachpakete für Japanisch mit der Sprachkennung „Jpan“ im Dateinamen](media/language-pack-example.png)

5. Legen Sie die Berechtigungen für die Freigabe des Inhaltsrepositorys für Sprachen so fest, dass Sie über Lesezugriff von der VM aus verfügen, die Sie zum Erstellen des benutzerdefinierten Images verwenden möchten.

## <a name="create-a-custom-windows-11-enterprise-image-manually"></a>Manuelles Erstellen eines benutzerdefinierten Windows 11 Enterprise-Images

Führen Sie die folgenden Schritte aus, um ein benutzerdefiniertes Image zu erstellen:

1. Stellen Sie eine Azure-VM bereit. Dann navigieren Sie zum Azure-Katalog und wählen die von Ihnen verwendete aktuelle Version von Windows 11 Enterprise aus.

2. Nachdem Sie die VM bereitgestellt haben, stellen Sie mithilfe von RDP als lokaler Administrator eine Verbindung her.

3. Stellen Sie eine Verbindung zum Dateifreigabe-Repository her, das Sie unter [Erstellen eines Inhaltsrepositorys für Sprachpakete und Features bei Bedarf (FODs)](#create-a-content-repository-for-language-packages-and-features-on-demand) erstellt haben, und binden Sie es in ein Laufwerk ein (z. B. Laufwerk E).

4. Führen Sie in einer PowerShell-Sitzung mit erhöhten Rechten das folgende PowerShell-Skript aus, um Language Packs und Satellitenpakete unter Windows 11 Enterprise zu installieren:
   
   ```powershell
   ########################################################
   ## Add Languages to running Windows Image for Capture##
   ########################################################
   ##Disable Language Pack Cleanup##
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\MUI\" -TaskName "LPRemove"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\LanguageComponentsInstaller" -TaskName "Uninstallation"
   reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Control Panel\International" /v "BlockCleanupOfUnusedPreinstalledLangPacks" /t REG_DWORD /d 1 /f

   ##Set Language Pack Content Stores##
   $LIPContent = "E:"

   ##Set Path of CSV File##
   $CSVFile = "Windows-10-1809-FOD-to-LP-Mapping-Table.csv"
   $filePath = (Get-Location).Path + "/$CSVFile"

   ##Import Necesarry CSV File##
   $FODList = Import-Csv -Path $filePath -Delimiter ";"

   ##Set Language (Target)##
   $targetLanguage = "es-es"

   $sourceLanguage = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Source Lang' -ne $targetLanguage} | Select-Object -Property 'Source Lang' -Unique).'Source Lang'
   if(!($sourceLanguage)){
       $sourceLanguage = $targetLanguage
   }

   $langGroup = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Lang Group:' -ne ""} | Select-Object -Property 'Lang Group:' -Unique).'Lang Group:'

   ##List of additional features to be installed##
   $additionalFODList = @(
       "$LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~~.cab", 
       "$LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-SnippingTool-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-Lip-Language_x64_$sourceLanguage.cab" ##only if applicable##
   )
   
   $additionalCapabilityList = @(
    "Language.Basic~~~$sourceLanguage~0.0.1.0",
    "Language.Handwriting~~~$sourceLanguage~0.0.1.0",
    "Language.OCR~~~$sourceLanguage~0.0.1.0",
    "Language.Speech~~~$sourceLanguage~0.0.1.0",
    "Language.TextToSpeech~~~$sourceLanguage~0.0.1.0"
    )

    ##Install all FODs or fonts from the CSV file###
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Client-Language-Pack_x64_$sourceLanguage.cab
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Lip-Language-Pack_x64_$sourceLanguage.cab
    foreach($capability in $additionalCapabilityList){
       Dism /Online /Add-Capability /CapabilityName:$capability /Source:$LIPContent
    }

    foreach($feature in $additionalFODList){
    Dism /Online /Add-Package /PackagePath:$feature
    }

    if($langGroup){
    Dism /Online /Add-Capability /CapabilityName:Language.Fonts.$langGroup~~~und-$langGroup~0.0.1.0 
    }

    ##Add installed language to language list##
    $LanguageList = Get-WinUserLanguageList
    $LanguageList.Add("$targetlanguage")
    Set-WinUserLanguageList $LanguageList -force
    ```

    >[!NOTE]
    >In diesem Beispielskript wird der Sprachcode Spanisch (es-es) verwendet. Um automatisch die passenden Dateien für eine andere Sprache zu installieren, ändern Sie den Parameter *$targetLanguage* in den korrekten Sprachcode. Eine Liste aller Sprachcodes finden Sie unter [Verfügbare Language Packs für Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

    Je nach Anzahl der Sprachen, die installiert werden müssen, kann es etwas dauern, bis das Skript abgeschlossen wird. Nach der erstmaligen Einrichtung können Sie auch zusätzliche Sprachen installieren. Führen Sie dazu das Skript mit einem anderen *$targetLanguage*-Parameter aus.

5. Um automatisch die richtigen Installationsdateien auszuwählen, laden Sie die Tabelle [Verfügbare Windows 10 1809-Sprachen und -Features bei Bedarf (FODs)](https://download.microsoft.com/download/7/6/0/7600F9DC-C296-4CF8-B92A-2D85BAFBD5D2/Windows-10-1809-FOD-to-LP-Mapping-Table.xlsx ) als CSV-Datei herunter und speichern sie im selben Ordner, in dem sich das PowerShell-Skript befindet.

6. Sobald die Skriptausführung abgeschlossen ist, vergewissern Sie sich, dass die Sprachpakete ordnungsgemäß installiert wurden. Dazu navigieren Sie zu **Start** > **Einstellungen** > **Uhrzeit und Sprache** > **Sprache**. Wenn die Sprachdateien vorhanden sind, ist die Einrichtung abgeschlossen.

7. Wenn die VM beim Installieren von Sprachen mit dem Internet verbunden ist, müssen Sie einen Bereinigungsprozess ausführen, um nicht benötigte Sprachpakete zu entfernen. Führen Sie die folgenden Befehle aus, um die Dateien zu bereinigen:

    ```powershell
    ##Cleanup to prepare sysprep##
    Remove-AppxPackage -Package Microsoft.LanguageExperiencePackes-ES_22000.8.13.0_neutral__8wekyb3d8bbwe

    Remove-AppxPackage -Package Microsoft.OneDriveSync_22000.8.13.0_neutral__8wekyb3d8bbwe
    ```

    Um andere Sprachpakete zu bereinigen, ersetzen Sie „es-ES“ durch einen anderen Sprachcode.

8. Trennen Sie nach Abschluss der Bereinigung die Freigabe. 

## <a name="finish-customizing-your-image"></a>Abschließen der Imageanpassung

Nachdem Sie die Language Packs installiert haben, können Sie weitere Software installieren, die Sie dem angepassten Image hinzufügen möchten.

Sobald Sie die Anpassung des Images abgeschlossen haben, müssen Sie das Tools für die Systemvorbereitung (sysprep) ausführen.

Zum Ausführen von Sysprep gehen Sie folgendermaßen vor:

1. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus, um das Image zu generalisieren:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Wenn Probleme auftreten, prüfen Sie die Datei **SetupErr.log** auf Laufwerk C unter **Windows** > **System32** > **Sysprep** > **Panther**. Befolgen Sie anschließend die Anweisungen unter [Sysprep schlägt in Zusammenhang mit Microsoft Store Apps fehl](/troubleshoot/windows-client/deployment/sysprep-fails-remove-or-update-store-apps), um die Probleme Ihrer Konfiguration zu beheben.

3. Wenn die Einrichtung erfolgreich ist, halten Sie die VM an und erfassen Sie sie in einem verwalteten Image. Folgen Sie dazu den Anweisungen unter [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](../virtual-machines/windows/capture-image-resource.md).

4. Sie können jetzt das angepasste Image zum Bereitstellen eines Azure Virtual Desktop-Hostpools verwenden. Informationen dazu, wie Sie einen Hostpool bereitstellen, finden Sie im [Tutorial: Erstellen eines Hostpools mit dem Azure-Portal](create-host-pools-azure-marketplace.md).

>[!NOTE]
>Wenn ein Benutzer seine Anzeigesprache ändert, muss er sich aus der Azure Virtual Desktop-Sitzung abmelden und erneut anmelden. Er muss sich über das Startmenü abmelden.

## <a name="next-steps"></a>Nächste Schritte

Unter [Hinzufügen von Language Packs zu Windows 10-Multi-Session-VMs](language-packs.md) erfahren Sie, wie Sie Language Packs für Windows 10-Multi-Session-VMs installieren.

Eine Liste der bekannten Probleme finden Sie unter [Hinzufügen von Sprachen unter Windows 10: Bekannte Probleme](/windows-hardware/manufacture/desktop/language-packs-known-issue).
