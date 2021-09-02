---
title: Herstellen einer Verbindung mit Azure Resource Manager auf Ihrem Azure Stack Edge-GPU-Gerät
description: Beschreibt, wie Sie mithilfe von Azure PowerShell eine Verbindung mit Azure Resource Manager herstellen, der auf Ihrem Azure Stack Edge Pro-GPU-Gerät ausgeführt wird.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/09/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ac064d53f4d85d8f517ef7bba67e88abe625f16
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515664"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-device"></a>Herstellen einer Verbindung mit Azure Resource Manager auf Ihrem Azure Stack Edge-Gerät

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager bietet eine Verwaltungsebene, die das Erstellen, Aktualisieren und Löschen von Ressourcen in Ihrem Azure-Abonnement ermöglicht. Das Azure Stack Edge-Gerät unterstützt die gleichen Azure Resource Manager-APIs zum Erstellen, Aktualisieren und Löschen von virtuellen Computern in einem lokalen Abonnement. Dadurch können Sie das Gerät in Übereinstimmung mit der Cloud verwalten. 

In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure PowerShell über Azure Resource Manager eine Verbindung mit den lokalen APIs auf Ihrem Azure Stack Edge-Gerät herstellen.


## <a name="endpoints-on-azure-stack-edge-device"></a>Endpunkte auf dem Azure Stack Edge-Gerät

In der folgenden Tabelle sind die verschiedenen Endpunkte, die auf dem Gerät verfügbar gemacht werden, die unterstützten Protokolle und die Ports für den Zugriff auf diese Endpunkte zusammengefasst. Im gesamten Artikel wird auf diese Endpunkte Bezug genommen.

| # | Endpunkt | Unterstützte Protokolle | Verwendeter Port | Syntaxelemente |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Herstellen einer Verbindung mit Azure Resource Manager zur Automatisierung |
| 2. | Sicherheitstokendienst | https | 443 | Authentifizieren über Zugriffs- und Aktualisierungstoken |
| 3. | Blob* | https | 443 | Herstellen einer Verbindung mit dem Blobspeicher über REST |

\* *Eine Verbindung mit dem Blobspeicherendpunkt ist nicht erforderlich für eine Verbindung mit Azure Resource Manager.*
 
## <a name="connecting-to-azure-resource-manager-workflow"></a>Herstellen einer Verbindung mit dem Azure Resource Manager-Workflow

Zum Herstellen einer Verbindung mit den lokalen APIs des Geräts mithilfe von Azure Resource Manager sind die folgenden Schritte erforderlich:

| Schrittnummer | Ausgeführter Schritt | .. Ausführungsort |
| --- | --- | --- |
| 1. | [Konfigurieren Ihres Azure Stack Edge-Geräts](#step-1-configure-azure-stack-edge-device) | Lokale Web-UI |
| 2. | [Erstellen und Installieren von Zertifikaten](#step-2-create-and-install-certificates) | Windows-Client/lokale Webbenutzeroberfläche |
| 3. | [Überprüfen und Konfigurieren der Voraussetzungen](#step-3-install-powershell-on-the-client) | Windows-Client |
| 4. | [Einrichten von Azure PowerShell auf dem Client](#step-4-set-up-azure-powershell-on-the-client) | Windows-Client |
| 5. | [Ändern der Hostdatei für die Endpunktnamensauflösung](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows-Client oder DNS-Server |
| 6. | [Überprüfen der Auflösung des Endpunktnamens](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows-Client |
| 7. | [Überprüfen der Verbindung mit Azure Resource Manager mithilfe von Azure PowerShell-Cmdlets](#step-7-set-azure-resource-manager-environment) | Windows-Client |

In den folgenden Abschnitten werden die einzelnen oben genannten Schritte zum Herstellen einer Verbindung mit Azure Resource Manager ausführlich erläutert.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass auf dem Client, der zum Herstellen der Verbindung mit dem Gerät über Azure Resource Manager verwendet wird, TLS 1.2 verwendet wird. Weitere Informationen finden Sie unter [Konfigurieren von TLS 1.2 auf Windows-Clients für den Zugriff auf das Azure Stack Edge-Gerät](azure-stack-edge-gpu-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-device"></a>Schritt 1: Konfigurieren des Azure Stack Edge-Geräts 

Führen Sie auf der lokalen Webbenutzeroberfläche Ihres Azure Stack Edge-Geräts die folgenden Schritte aus.

1. Geben Sie die Netzwerkeinstellungen für das Azure Stack Edge-Gerät an. 

    ![Seite „Netzwerkeinstellungen“ der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Notieren Sie sich die IP-Adresse des Geräts. Sie verwenden diese IP-Adresse später.

2. Konfigurieren Sie den Gerätenamen und die DNS-Domäne auf der Seite **Gerät**. Notieren Sie sich den Gerätenamen und die DNS-Domäne, da Sie sie später benötigen.

    ![Seite „Gerät“ der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > Anhand des Gerätenamens und der DNS-Domäne werden die Endpunkte gebildet, die verfügbar gemacht werden.
    > Verwenden Sie die Azure Resource Manager- und Blobendpunkte auf der Seite **Gerät** auf der lokalen Benutzeroberfläche.


## <a name="step-2-create-and-install-certificates"></a>Schritt 2: Erstellen und Installieren von Zertifikaten

Durch Zertifikate wird sichergestellt, dass die Kommunikation vertrauenswürdig ist. Auf dem Azure Stack Edge-Gerät werden selbstsignierte Appliance-, Blob- und Azure Resource Manager-Zertifikate automatisch generiert. Optional können Sie eigene signierte Blob- und Azure Resource Manager-Zertifikate verwenden.

Wenn Sie ein eigenes signiertes Zertifikat verwenden, benötigen Sie auch die zugehörige Signaturkette des Zertifikats. Für die Signaturkette, Azure Resource Manager und die Blobzertifikate auf dem Gerät benötigen Sie auch die entsprechenden Zertifikate auf dem Clientcomputer für die Authentifizierung und die Kommunikation mit dem Gerät.

Zum Herstellen der Verbindung mit Azure Resource Manager müssen Sie die Signaturketten- und Endpunktzertifikate erstellen oder abrufen, diese Zertifikate auf dem Windows-Client importieren und schließlich die Zertifikate auf das Gerät hochladen.

### <a name="create-certificates"></a>Erstellen von Zertifikaten

Nur für Test- und Entwicklungszwecke können Sie Windows PowerShell verwenden, um Zertifikate auf dem lokalen System zu erstellen. Beachten Sie beim Erstellen der Zertifikate für den Client die folgenden Vorgaben:

1. Zunächst müssen Sie ein Stammzertifikat für die Signaturkette erstellen. Weitere Informationen finden Sie unter [Erstellen von Signaturkettenzertifikaten](azure-stack-edge-gpu-create-certificates-powershell.md#create-signing-chain-certificate).

2. Dann können Sie die Endpunktzertifikate für Azure Resource Manager und das Blob erstellen (optional). Diese Endpunkte finden Sie auf der Seite **Gerät** in der lokalen Webbenutzeroberfläche. Weitere Informationen finden Sie in den Schritten zum [Erstellen von Endpunktzertifikaten](azure-stack-edge-gpu-create-certificates-powershell.md#create-signed-endpoint-certificates).

3. Achten Sie bei allen diesen Zertifikaten darauf, dass der Antragstellername und der alternative Antragstellername den folgenden Vorgaben entsprechen:

    |type |Antragstellername  |Alternativer Antragstellername  |Beispiel für Antragstellername |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blobspeicher*|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Einzelnes Zertifikat für mehrere alternative Antragstellernamen für beide Endpunkte|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

\* Blobspeicher ist nicht erforderlich, um eine Verbindung mit Azure Resource Manager herzustellen. Er wird hier aufgeführt, falls Sie lokale Speicherkonten auf Ihrem Gerät erstellen.

Weitere Informationen zu Zertifikaten erhalten Sie unter [Hochladen von Zertifikaten auf Ihr Gerät und Importieren von Zertifikaten auf die Clients, die auf Ihr Gerät zugreifen](azure-stack-edge-gpu-manage-certificates.md).

### <a name="upload-certificates-on-the-device"></a>Hochladen von Zertifikaten auf das Gerät

Die im vorherigen Schritt erstellten Zertifikate befinden sich im persönlichen Speicher auf dem Client. Diese Zertifikate müssen auf dem Client in entsprechende Formatdateien exportiert werden, die dann auf das Gerät hochgeladen werden können.

1. Das Stammzertifikat muss als Datei im DER-Format mit der Dateierweiterung *.cer* exportiert werden. Ausführliche Schritte finden Sie unter [Exportieren von Zertifikaten als Datei im CER-Format](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format).

2. Die Endpunktzertifikate müssen als *PFX*-Dateien mit privaten Schlüsseln exportiert werden. Ausführliche Schritte finden Sie unter [Exportieren von Zertifikaten als PFX-Datei mit privaten Schlüsseln](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key).

3. Das Stamm- und die Endpunktzertifikate werden dann über die Option **+ Zertifikat hinzufügen** auf der Seite **Zertifikate** der lokalen Webbenutzeroberfläche auf das Gerät hochgeladen. Führen Sie zum Hochladen der Zertifikate die Schritte unter [Hochladen von Zertifikaten](azure-stack-edge-gpu-manage-certificates.md#upload-certificates) aus.


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Importieren von Zertifikaten auf dem Client, auf dem Azure PowerShell ausgeführt wird

Für den Windows-Client, auf dem die Azure Resource Manager-APIs aufgerufen werden, muss eine Vertrauensstellung mit dem Gerät eingerichtet werden. Dazu müssen die im vorherigen Schritt erstellten Zertifikate auf dem Windows-Client in den entsprechenden Zertifikatspeicher importiert werden.

1. Das Stammzertifikat, das Sie im DER-Format mit der Erweiterung *.cer* exportiert haben, sollte nun in die vertrauenswürdigen Stammzertifizierungsstellen auf dem Clientsystem importiert werden. Ausführliche Schritte finden Sie unter [Importieren von Zertifikaten in den Speicher „Vertrauenswürdige Stammzertifizierungsstellen“](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format).

2. Die Endpunktzertifikate, die Sie mit der Erweiterung *.pfx* exportiert haben, müssen als *CER*-Dateien exportiert werden. Diese *CER*-Datei wird dann in den Zertifikatspeicher **Persönlich** des Systems importiert. Ausführliche Schritte finden Sie unter [Importieren von Zertifikaten in den persönlichen Speicher](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format).

## <a name="step-3-install-powershell-on-the-client"></a>Schritt 3: Installieren von PowerShell auf dem Client 

### <a name="az"></a>[Az](#tab/Az)

Der Windows-Client muss die folgenden Voraussetzungen erfüllen:


1. PowerShell Version 5.0 muss ausgeführt werden. Sie müssen PowerShell Version 5.0 installiert haben. Führen Sie das folgende Cmdlet aus, um die PowerShell-Version auf Ihrem System zu überprüfen:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Prüfen Sie, ob die **Hauptversion** 5.0 oder höher verwendet wird.

    Falls Sie über eine veraltete Version verfügen, helfen Ihnen die Informationen unter [Aktualisieren einer vorhandenen Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell) weiter.

    Wenn PowerShell 5.0 nicht installiert ist, führen Sie die Schritte unter [Installieren von Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true) aus.

    Nachfolgend sehen Sie eine Beispielausgabe.

    ```output
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      19041  906
    ```
    
1. Sie können auf den PowerShell-Katalog zugreifen.

    Führen Sie PowerShell als Administrator aus. Überprüfen Sie, ob die PowerShellGet-Version älter als 2.2.3 ist. Überprüfen Sie außerdem, ob `PSGallery` als Repository registriert ist.

    ```powershell
    Install-Module PowerShellGet –MinimumVersion 2.2.3
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Nachfolgend sehen Sie eine Beispielausgabe.
    
    ```output
    PS C:\windows\system32> Install-Module PowerShellGet –MinimumVersion 2.2.3
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```    

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Der Windows-Client muss die folgenden Voraussetzungen erfüllen:


1. PowerShell Version 5.0 muss ausgeführt werden. Sie müssen PowerShell Version 5.0 installiert haben. PowerShell Core wird nicht unterstützt. Führen Sie das folgende Cmdlet aus, um die PowerShell-Version auf Ihrem System zu überprüfen:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Prüfen Sie, ob die **Hauptversion** 5.0 oder höher verwendet wird.

    Falls Sie über eine veraltete Version verfügen, helfen Ihnen die Informationen unter [Aktualisieren einer vorhandenen Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell) weiter.

    Wenn PowerShell 5.0 nicht installiert ist, führen Sie die Schritte unter [Installieren von Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true) aus.

    Nachfolgend sehen Sie eine Beispielausgabe.

    ```output
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. Sie können auf den PowerShell-Katalog zugreifen.

    Führen Sie PowerShell als Administrator aus. Überprüfen Sie, ob `PSGallery` als Repository registriert ist.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Nachfolgend sehen Sie eine Beispielausgabe.
    
    ```output
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
---    
Wenn das Repository nicht vertrauenswürdig ist oder Sie weitere Informationen benötigen, finden Sie weitere Informationen unter [Überprüfen der Verfügbarkeit des PowerShell-Katalogs](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>Schritt 4: Einrichten von Azure PowerShell auf dem Client 

### <a name="az"></a>[Az](#tab/Az)

Sie installieren Azure PowerShell-Module auf dem Client, der für Ihr Gerät verwendet wird.

1. Führen Sie PowerShell als Administrator aus. Sie benötigen Zugriff auf den PowerShell-Katalog. 


1. Vergewissern Sie sich zunächst, dass auf Ihrem Client keine Versionen der Module `AzureRM` und `Az` vorhanden sind. Sie können dies herausfinden, indem Sie die folgenden Befehle ausführen:

    ```powershell
    # Check existing versions of AzureRM modules
    Get-InstalledModule -Name AzureRM -AllVersions

    # Check existing versions of Az modules
    Get-InstalledModule -Name Az -AllVersions
    ```

    Wenn bereits Versionen vorhanden sind, deinstallieren Sie diese mit dem Cmdlet `Uninstall-Module`. Weitere Informationen finden Sie unter 
    - [Deinstallieren des AzureRM-Moduls](/powershell/azure/uninstall-az-ps?view=azps-6.0.0&preserve-view=true#uninstall-the-az-module).
    - [Deinstallieren von Az-Modulen](/powershell/azure/uninstall-az-ps?view=azps-6.0.0&preserve-view=true#uninstall-the-azurerm-module).

1. Führen Sie den folgenden Befehl aus, um die erforderlichen Azure PowerShell-Module aus dem PowerShell-Katalog zu installieren:

    - Wenn Ihr Client PowerShell Core, Version 7.0 und höher, verwendet:

        ```powershell
        # Install the Az.BootStrapper module. Select Yes when prompted to install NuGet.
        
        Install-Module -Name Az.BootStrapper
        
        # Install and import the API Version Profile into the current PowerShell session.
        
        Use-AzProfile -Profile 2020-09-01-hybrid -Force
        
        # Confirm the installation of PowerShell
        Get-Module -Name "Az*" -ListAvailable
        ```
    
    - Wenn Ihr Client PowerShell 5.1 und höher verwendet:
        
        ```powershell
        #Install the Az module version 1.10.0
        
        Install-Module –Name Az –RequiredVersion 1.10.0    
        ```

3.  Stellen Sie sicher, dass nach der Installation das Az-Modul in der Version 1.10.0 ausgeführt wird. 
   

    Wenn Sie PowerShell Core 7.0 und höher verwendet haben, zeigt die folgende Beispielausgabe an, dass die Az-Module der Version 1.10.0 erfolgreich installiert wurden.
    
    ```output
   
    PS C:\windows\system32> Install-Module -Name Az.BootStrapper
    PS C:\windows\system32> Use-AzProfile -Profile 2020-09-01-hybrid -Force
    Loading Profile 2020-09-01-hybrid
    PS C:\windows\system32> Get-Module -Name "Az*" -ListAvailable
    ```

    Wenn Sie PowerShell 5.1 und höher verwendet haben, zeigt die folgende Beispielausgabe an, dass die Az-Module der Version 1.10.0 erfolgreich installiert wurden.
     
    ```powershell
    PS C:\WINDOWS\system32> Get-InstalledModule -Name Az -AllVersions
    Version              Name                                Repository           Description
    -------              ----                                ----------           ------
    1.10.0               Az                                  PSGallery            Mic...  
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Sie installieren Azure PowerShell-Module auf dem Client, der für Ihr Gerät verwendet wird.

1. Führen Sie PowerShell als Administrator aus. Sie benötigen Zugriff auf den PowerShell-Katalog. 


2. Führen Sie den folgenden Befehl aus, um die erforderlichen Azure PowerShell-Module aus dem PowerShell-Katalog zu installieren:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Achten Sie darauf, dass nach der Installation das Azure-RM-Modul in der Version 2.5.0 ausgeführt wird. 
    Wenn eine Version des Azure-RM-Moduls vorhanden ist, die nicht der erforderlichen Version entspricht, deinstallieren Sie diese Version mit dem folgenden Befehl:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Die erforderliche Version muss anschließend erneut installiert werden.
   

    Nachfolgend sehen Sie eine Beispielausgabe, die anzeigt, dass die AzureRM-Module der Version 2.5.0 erfolgreich installiert wurden.
    
    ```powershell
    PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
    PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Loading Profile 2019-03-01-hybrid
    PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
     
        Directory: C:\Program Files\WindowsPowerShell\Modules
     
    ModuleType Version    Name                          ExportedCommands
    ---------- -------    ----                          ----------------
    Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
    Script     2.5.0      AzureRM
    Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
    Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
    Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
    Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
    Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
    Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
    Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
    Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
    Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
    Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
    Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
    Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...
    
     
        Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
     
    ModuleType Version    Name                            ExportedCommands
    ---------- -------    ----                           ----------------
    Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
    ```
---   

## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>Schritt 5: Ändern der Hostdatei für die Endpunktnamensauflösung 

Nun fügen Sie die IP-Adresse des Geräts zu folgenden Elementen hinzu:

- in der Hostdatei auf dem Client oder
- in der DNS-Serverkonfiguration

> [!IMPORTANT]
> Es wird empfohlen, die DNS-Serverkonfiguration für die Endpunktnamensauflösung zu ändern.

Gehen Sie auf dem Windows-Client zum Herstellen der Verbindung mit dem Gerät wie folgt vor:

1. Starten Sie **Editor** als Administrator, und öffnen Sie die Datei **hosts** unter „C:\Windows\System32\Drivers\etc“.

    ![Datei „hosts“ in Windows-Explorer](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)

2. Fügen Sie in der Datei **hosts** die folgenden Einträge hinzu, und ersetzen Sie dabei die entsprechenden Werte für Ihr Gerät: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > Der Eintrag in der Datei „hosts“ muss genau mit dem Wert übereinstimmen, der für die Verbindung mit Azure Resource Manager in einem späteren Schritt angegeben wird. Achten Sie darauf, dass der Eintrag für die DNS-Domäne in Kleinbuchstaben erfolgt. Um die Werte für `<appliance name>` und `<DNS domain>` abzurufen, wechseln Sie auf der lokalen Benutzeroberfläche Ihres Geräts zur Seite **Geräte**.

    Sie haben die IP-Adresse des Geräts von der lokalen Webbenutzeroberfläche in einem früheren Schritt gespeichert.

    Der Eintrag `login.<appliance name>.<DNS domain>` ist der Endpunkt für den Sicherheitstokendienst (STS). STS wird für die Erstellung, Validierung, Erneuerung und den Abbruch von Sicherheitstoken verwendet. Mithilfe des Sicherheitstokendiensts werden das Zugriffstoken und das Aktualisierungstoken erstellt, die für die fortlaufende Kommunikation zwischen dem Gerät und dem Client verwendet werden.

    Der Endpunkt für den Blobspeicher ist optional, wenn eine Verbindung mit Azure Resource Manager hergestellt wird. Dieser Endpunkt ist erforderlich, wenn Daten über Speicherkonten an Azure übertragen werden.

3. Verwenden Sie die folgende Abbildung als Referenz. Speichern Sie die Datei **hosts**.

    ![Datei „hosts“ in Editor](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>Schritt 6: Überprüfen der Endpunktnamensauflösung auf dem Client

Überprüfen Sie, ob der Endpunktname auf dem Client aufgelöst wird, über den eine Verbindung mit dem Gerät hergestellt wird.

1. Sie können das Befehlszeilen-Hilfsprogramm `ping.exe` verwenden, um zu überprüfen, ob der Endpunktname aufgelöst wurde. Bei Angabe einer IP-Adresse gibt der Befehl `ping` den TCP/IP-Hostnamen des von Ihnen \'nachverfolgten Computers zurück.

    Fügen Sie wie im folgenden Beispiel gezeigt der Befehlszeile den Schalter `-a` hinzu. Wenn der Hostname zurückgegeben werden kann, werden auch diese potenziell nützlichen Informationen in der Antwort zurückgegeben.

    ![Ping an der Eingabeaufforderung](media/azure-stack-edge-gpu-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>Schritt 7: Festlegen der Azure Resource Manager-Umgebung

### <a name="az"></a>[Az](#tab/Az)

Legen Sie die Azure Resource Manager-Umgebung fest, und überprüfen Sie, ob die Kommunikation zwischen Gerät und Client über Azure Resource Manager einwandfrei funktioniert. Führen Sie für diese Überprüfung die folgenden Schritte aus:


1. Verwenden Sie das Cmdlet `Add-AzEnvironment`, um sicherzustellen, dass die Kommunikation über Azure Resource Manager ordnungsgemäß funktioniert und die API-Aufrufe den für Azure Resource Manager angegebenen Port 443 durchlaufen.

    Mit dem Cmdlet `Add-AzEnvironment` werden Endpunkte und Metadaten hinzugefügt, damit über Azure Resource Manager-Cmdlets eine Verbindung mit einer neuen Instanz von Azure Resource Manager hergestellt werden kann. 


    > [!IMPORTANT]
    > Bei der Azure Resource Manager-Endpunkt-URL, die Sie im folgenden Cmdlet angeben, wird die Groß- und Kleinschreibung beachtet. Achten Sie darauf, dass die Endpunkt-URL vollständig in Kleinbuchstaben angegeben ist und der Angabe in der Datei „hosts“ entspricht. Wenn die Groß- und Kleinschreibung nicht übereinstimmt, wird ein Fehler angezeigt.

    ```powershell
    Add-AzEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Nachfolgend sehen Sie eine Beispielausgabe:
    
    ```output
    PS C:\WINDOWS\system32> Add-AzEnvironment -Name AzASE -ARMEndpoint "https://management.myasegpu.wdshcsso.com/"
    
    Name  Resource Manager Url                      ActiveDirectory Authority
    ----  --------------------                      -------------------------
    AzASE https://management.myasegpu.wdshcsso.com/ https://login.myasegpu.wdshcsso.c...
    ```

2. Legen Sie die Umgebung als Azure Stack Edge und den für Azure Resource Manager-Aufrufe zu verwendenden Port auf 443 fest. Die Umgebung wird auf zwei Arten definiert:

    - Richten Sie die Umgebung ein. Geben Sie folgenden Befehl ein:

        ```powershell
        Set-AzEnvironment -Name <Environment Name>
        ```
        
        Hier sehen Sie eine Beispielausgabe.

        ```output
        PS C:\WINDOWS\system32> Set-AzEnvironment -Name AzASE

        Name  Resource Manager Url                      ActiveDirectory Authority
        ----  --------------------                      -------------------------
        AzASE https://management.myasegpu.wdshcsso.com/ https://login.myasegpu.wdshcsso.c...     
        ```
        Weitere Informationen finden Sie unter [Set-AzEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true).    

    - Definieren Sie die Inlineumgebung für jedes ausgeführte Cmdlet. Dadurch wird sichergestellt, dass alle API-Aufrufe die richtige Umgebung durchlaufen. Standardmäßig durchlaufen die Aufrufe die öffentliche Azure-Umgebung, sie sollen stattdessen jedoch die Umgebung durchlaufen, die Sie für das Azure Stack Edge-Gerät festgelegt haben.

    - Weitere Informationen finden Sie unter [Wechseln von Az-Umgebungen](#switch-environments).

2. Rufen Sie die APIs des lokalen Geräts auf, um die Verbindungen mit Azure Resource Manager zu authentifizieren. 

    1. Diese Anmeldeinformationen gelten für ein lokales Computerkonto und werden ausschließlich für den API-Zugriff verwendet.

    2. Über den Befehl `login-AzAccount` oder `Connect-AzAccount` können Sie eine Verbindung herstellen. 

        1. Geben Sie für die Anmeldung den folgenden Befehl ein. 
        
            ```powershell
            $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            Connect-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            ```

            Verwenden Sie die Mandanten-ID c0257de7-538f-415c-993a-1b87a031879d, da sie in dieser Instanz hartcodiert ist.
            Verwenden Sie den folgenden Benutzernamen und das folgende Kennwort.

            - **Benutzername** - *EdgeArmUser*

            - **Kennwort** - [Legen Sie das Kennwort für Azure Resource Manager fest](azure-stack-edge-gpu-set-azure-resource-manager-password.md), und melden Sie sich mit diesem Kennwort an. 
       


            Hier sehen Sie eine Beispielausgabe für `Connect-AzAccount`:

            ```output
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzASE
            
            PS C:\windows\system32>
            ```                   
        
            Alternativ kann für die Anmeldung das Cmdlet `login-AzAccount` verwendet werden. 
            
            `login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            Hier sehen Sie eine Beispielausgabe. 
         
            ```output
            PS C:\WINDOWS\system32> login-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account               SubscriptionName              TenantId
            -------               ----------------              --------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a...            
            
            PS C:\WINDOWS\system32>
            ```
3. Verwenden Sie den Befehl `Get-AzResource`, um zu überprüfen, ob die Verbindung mit dem Gerät funktioniert. Dieser Befehl sollte alle Ressourcen zurückgeben, die lokal auf dem Gerät vorhanden sind.

    Hier sehen Sie eine Beispielausgabe.

    ```output
    PS C:\WINDOWS\system32> Get-AzResource
    
    Name              : aseimagestorageaccount
    ResourceGroupName : ase-image-resourcegroup
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ase-image-resourcegroup/providers/Microsoft.Storage/storageac
                        counts/aseimagestorageaccount
    Tags              :
    
    Name              : myaselinuxvmimage1
    ResourceGroupName : ASERG
    ResourceType      : Microsoft.Compute/images
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Compute/images/myaselinuxvmimage1
    Tags              :
    
    Name              : ASEVNET
    ResourceGroupName : ASERG
    ResourceType      : Microsoft.Network/virtualNetworks
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET
    Tags              :
    
    PS C:\WINDOWS\system32>  
    ```

   

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Legen Sie die Azure Resource Manager-Umgebung fest, und überprüfen Sie, ob die Kommunikation zwischen Gerät und Client über Azure Resource Manager einwandfrei funktioniert. Führen Sie für diese Überprüfung die folgenden Schritte aus:


1. Verwenden Sie das Cmdlet `Add-AzureRmEnvironment`, um sicherzustellen, dass die Kommunikation über Azure Resource Manager ordnungsgemäß funktioniert und die API-Aufrufe den für Azure Resource Manager angegebenen Port 443 durchlaufen.

    Mit dem Cmdlet `Add-AzureRmEnvironment` werden Endpunkte und Metadaten hinzugefügt, damit über Azure Resource Manager-Cmdlets eine Verbindung mit einer neuen Instanz von Azure Resource Manager hergestellt werden kann. 


    > [!IMPORTANT]
    > Bei der Azure Resource Manager-Endpunkt-URL, die Sie im folgenden Cmdlet angeben, wird die Groß- und Kleinschreibung beachtet. Achten Sie darauf, dass die Endpunkt-URL vollständig in Kleinbuchstaben angegeben ist und der Angabe in der Datei „hosts“ entspricht. Wenn die Groß- und Kleinschreibung nicht übereinstimmt, wird ein Fehler angezeigt.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Nachfolgend sehen Sie eine Beispielausgabe:
    
    ```output
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Legen Sie die Umgebung als Azure Stack Edge und den für Azure Resource Manager-Aufrufe zu verwendenden Port auf 443 fest. Die Umgebung wird auf zwei Arten definiert:

    - Richten Sie die Umgebung ein. Geben Sie folgenden Befehl ein:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Weitere Informationen finden Sie unter [Set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true).

    - Definieren Sie die Inlineumgebung für jedes ausgeführte Cmdlet. Dadurch wird sichergestellt, dass alle API-Aufrufe die richtige Umgebung durchlaufen. Standardmäßig durchlaufen die Aufrufe die öffentliche Azure-Umgebung, sie sollen stattdessen jedoch die Umgebung durchlaufen, die Sie für das Azure Stack Edge-Gerät festgelegt haben.

    - Weitere Informationen finden Sie unter [Wechseln von AzureRM-Umgebungen](#switch-environments).

2. Rufen Sie die APIs des lokalen Geräts auf, um die Verbindungen mit Azure Resource Manager zu authentifizieren. 

    1. Diese Anmeldeinformationen gelten für ein lokales Computerkonto und werden ausschließlich für den API-Zugriff verwendet.

    2. Über den Befehl `login-AzureRMAccount` oder `Connect-AzureRMAccount` können Sie eine Verbindung herstellen. 

        1. Geben Sie für die Anmeldung den folgenden Befehl ein. Die Mandanten-ID in dieser Instanz ist hartcodiert: c0257de7-538f-415c-993a-1b87a031879d. Verwenden Sie den folgenden Benutzernamen und das folgende Kennwort.

            - **Benutzername** - *EdgeArmUser*

            - **Kennwort** - [Legen Sie das Kennwort für Azure Resource Manager fest](azure-stack-edge-gpu-set-azure-resource-manager-password.md), und melden Sie sich mit diesem Kennwort an. 

            ```output
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Alternativ kann für die Anmeldung das Cmdlet `login-AzureRmAccount` verwendet werden. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            Nachfolgend finden Sie eine Beispielausgabe des Befehls. 
         
            ```output
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```
---

Wenn Probleme mit Ihren Azure Resource Manager-Verbindungen haben, lesen Sie [Problembehandlung für Azure Resource Manager](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md). 

> [!IMPORTANT]
> Die Verbindung mit Azure Resource Manager läuft alle 1,5 Stunden oder beim Neustart des Azure Stack Edge-Geräts ab. In diesem Fall geben alle ausgeführten Cmdlets eine Fehlermeldung mit dem Inhalt zurück, dass keine Verbindung mit Azure mehr besteht. Sie müssen sich erneut anmelden.

## <a name="switch-environments"></a>Wechseln von Umgebungen

Möglicherweise müssen Sie zwischen zwei Umgebungen wechseln.

### <a name="az"></a>[Az](#tab/Az)

Führen Sie den Befehl `Disconnect-AzAccount` aus, um zu einer anderen `AzEnvironment`-Umgebung zu wechseln. Wenn Sie `Set-AzEnvironment` und `Login-AzAccount` ohne `Disconnect-AzAccount` verwenden, wird die Umgebung nicht gewechselt.  

In den folgenden Beispielen wird der Wechsel zwischen den beiden Umgebungen `AzASE1` und `AzASE2` gezeigt.

Listen Sie zunächst alle auf dem Client vorhandenen Umgebungen auf.


```output
PS C:\WINDOWS\system32> Get-AzEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Stellen Sie dann fest, über welche Umgebung zurzeit eine Verbindung mit Azure Resource Manager besteht.

```output
PS C:\WINDOWS\system32> Get-AzContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Trennen Sie nun die Verbindung über die aktuelle Umgebung, bevor Sie zu der anderen Umgebung wechseln.


```output
PS C:\WINDOWS\system32> Disconnect-AzAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, ...], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Melden Sie sich bei der anderen Umgebung an. Die Beispielausgabe wird unten gezeigt.

```output
PS C:\WINDOWS\system32> Login-AzAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Führen Sie das folgende Cmdlet aus, um zu überprüfen, mit welcher Umgebung eine Verbindung besteht.

```output
PS C:\WINDOWS\system32> Get-AzContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
Damit sind Sie zu der vorgesehenen Umgebung gewechselt.

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Führen Sie den Befehl `Disconnect-AzureRmAccount` aus, um zu einer anderen `AzureRmEnvironment`-Umgebung zu wechseln. Wenn Sie `Set-AzureRmEnvironment` und `Login-AzureRmAccount` ohne `Disconnect-AzureRmAccount` verwenden, wird die Umgebung nicht gewechselt.  

In den folgenden Beispielen wird der Wechsel zwischen den beiden Umgebungen `AzDBE1` und `AzDBE2` gezeigt.

Listen Sie zunächst alle auf dem Client vorhandenen Umgebungen auf.


```output
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Stellen Sie dann fest, über welche Umgebung zurzeit eine Verbindung mit Azure Resource Manager besteht.

```output
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Trennen Sie nun die Verbindung über die aktuelle Umgebung, bevor Sie zu der anderen Umgebung wechseln.


```output
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, ...], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Melden Sie sich bei der anderen Umgebung an. Die Beispielausgabe wird unten gezeigt.

```output
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Führen Sie das folgende Cmdlet aus, um zu überprüfen, mit welcher Umgebung eine Verbindung besteht.

```output
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
---

Damit sind Sie zu der vorgesehenen Umgebung gewechselt.

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung für Azure Resource Manager](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)
- [Bereitstellen virtueller Computer auf Ihrem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)
