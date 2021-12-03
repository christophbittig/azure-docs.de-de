---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: fa4d3974a016fc57624c2c51d9aaf703583f8764
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557288"
---
Abhängig von der Softwareversion, in der Ihr Data Box ausgeführt wird, müssen Sie möglicherweise verschiedene Schritte ausführen, um eine Verbindung mit der PowerShell-Schnittstelle des Geräts herzustellen. Der erste Schritt in all diesen Fällen wäre die Identifizierung der Softwareversion, die auf Ihrem Data Box ausgeführt wird.

### <a name="identify-software-version-of-the-device"></a>Ermitteln der Softwareversion des Geräts

> [!NOTE]
> Die Versionsnummer wird ab Version 4.9 auf der lokalen Benutzeroberfläche angezeigt. Wenn keine Versionsnummer auf Ihrer lokalen Benutzeroberfläche wie hier beschrieben angezeigt wird, wenden Sie sich an den Microsoft-Support, um die Versionsnummer zu ermitteln, und führen Sie dann die Schritte zum Herstellen einer Verbindung mit der PowerShell-Schnittstelle des Geräts aus.

Verwenden Sie eine der folgenden Methoden, um die auf Ihrem Gerät ausgeführte Softwareversion und die Seriennummer des Geräts zu identifizieren.

 - Gehen Sie zur **Anmeldeseite**. Die Softwareversion wird auf dieser Seite angezeigt.
 
    ![Seriennummer und Softwareversion auf der Anmeldeseite auf der lokalen Benutzeroberfläche von Data Box](media/data-box-connect-powershell-interface/device-serial-number-software-version-sign-in-local-ui.png)

 - Wählen Sie auf der lokalen Benutzeroberfläche das **Hilfesymbol** aus. Im **Hilfebereich** werden die Softwareversion und die Seriennummer des Geräts angezeigt. Notieren Sie sich sowohl die Seriennummer des Geräts als auch die Softwareversion.
 
    ![Seriennummer und Softwareversion im Hilfebereich auf der lokalen Benutzeroberfläche von Data Box](media/data-box-connect-powershell-interface/device-serial-number-software-version-help-pane-local-ui.png)



Die nächsten Schritte werden durch die Softwareversion bestimmt, die von Ihrem Data Box ausgeführt wird.
 
### <a name="v41-and-later"></a>[Version 4.1 und höher](#tab/c)

Für diese Softwareversionen müssen Sie zuerst Zertifikate auf dem Host installieren, der auf Ihr Data Box zugreift, und dann eine Verbindung mit der PowerShell-Schnittstelle des Geräts herstellen. Wenn Sie Zertifikate installieren, können Sie die Standardzertifikate verwenden, mit denen das Gerät ausgeliefert wird, oder Ihre eigenen Zertifikate verwenden. In jedem Fall unterscheiden sich die spezifischen Schritte, die Sie ausführen, geringfügig.


### <a name="step-1-install-certificate-on-host-or-client"></a>Schritt 1: Installieren des Zertifikats auf dem Host oder Client

#### <a name="use-default-certificates"></a>Standardzertifikate verwenden 

Führen Sie diese Schritte aus, wenn Sie die Standardzertifikate (im Lieferumfang des Geräts enthalten) auf Data Box verwenden.

1. Wechseln Sie im Azure-Portal zu Ihrer Data Box-Auftragsressource. Navigieren Sie zu **Allgemein > Gerätedetails**. Wählen Sie auf der Registerkarte **API-Zugriff auf das Gerät** **herunterladen** aus.

    ![Herunterladen des Zertifikats für Data Box im Azure-Portal](media/data-box-connect-powershell-interface/download-certificate-data-box-portal.png)

1. Kopieren Sie das Zertifikat auf den Host, der eine Verbindung mit Ihrem Data Box herstellt. Wenn Sie einen Windows Host verwenden, öffnen Sie Explorer. Klicken Sie mit der rechten Maustaste auf die Zertifikatdatei, die Sie auf den Host kopiert haben, und wählen Sie **Zertifikat installieren** aus. 

1. Führen Sie die Installationsschritte über den **Zertifikatimport-Assistenten** aus. Ausführliche Schritte finden Sie unter [Importieren von Zertifikaten in den Client](../articles/databox/data-box-bring-your-own-certificates.md#import-certificates-to-client). 

1. Fügen Sie der Hostdatei einen Eintrag hinzu, mit dem die Geräte-IP-Adresse dem FQDN des Geräts zugeordnet wird. Das Format des Eintrags wäre: 

    `<Device IP address>  <Device serial number>.microsoftdatabox.com`

    Geben Sie die Seriennummer des Geräts aus dem vorherigen Schritt an. 

    ![Abrufen der Data Box-Seriennummer von der lokalen Benutzeroberfläche](media/data-box-connect-powershell-interface/get-device-serial-number-portal.png)
 

#### <a name="use-your-own-certificates"></a>Verwenden Ihrer eigenen Zertifikate 

Führen Sie diese Schritte aus, wenn Sie Ihre eigenen Zertifikate auf Data Box bringen.
 
1. Installieren Sie das Signaturkettenzertifikat, das Sie mit sich bringen, auf dem Hostcomputer, den Sie mit dem Data Box verbinden. Öffnen Sie das Zertifikat über den Explorer, klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Zertifikat installieren** aus. 
1. Führen Sie die Schritte unter [Importieren von Zertifikaten auf den Client](../articles/databox/data-box-bring-your-own-certificates.md#import-certificates-to-client) aus und installieren Sie das Zertifikat unter Lokaler Computer/Stamm. 
1. Fügen Sie der Hostdatei einen Eintrag hinzu, mit dem die Geräte-IP-Adresse dem FQDN des Geräts zugeordnet wird. Das Format des Eintrags wäre: 

    `<Device IP address>  <Name>.<DNS domain>` 

    Um den **Namen** und die **DNS-Domäne** für Ihr Gerät abzurufen, wechseln Sie auf der lokalen Benutzeroberfläche zur Seite **Zertifikate.** 
1. Um die Zertifikate hochzuladen, wechseln Sie auf der lokalen Benutzeroberfläche zur Seite **Zertifikate.** Wählen Sie **+ Zertifikate hinzufügen** aus, und geben Sie Ihr Zertifikat an. Siehe [Hochladen von Zertifikaten](../articles/databox/data-box-bring-your-own-certificates.md#add-certificates-to-device).
 

### <a name="step-2-connect-to-the-powershell-interface"></a>Schritt 2: Herstellen einer Verbindung mit der PowerShell-Schnittstelle


1. Legen Sie den `$Name` Parameter fest.

    ```powershell
    $Name = <Name>.<DNS domain>
    ```
    Geben Sie den **Namen** und die **DNS-Domäne** für Ihr Gerät aus den vorherigen Schritten an.

1. Fügen Sie Ihren Data Box mithilfe des `$Name` Parameters zur `TrustedHosts` Liste hinzu. Typ:
 
    ```powershell
    Set-Item wsman:\localhost\Client\TrustedHosts $Name 
    ```

1. Abrufen des Kennworts für die lokale Benutzeroberfläche aus dem Azure-Portal. Erstellen Sie eine sichere Zeichenfolge mit dem Kennwort.

    ```powershell
    $Pwd = ConvertTo-SecureString <Password from Azure portal> -AsPlainText -Force 
    ```
   <!--

    ```powershell
    $Cred = New-Object System.Management.Automation.PSCredential("<ipv4_address of databox>\StorSimpleAdmin",$Pwd) 
    ```-->

1. Stellen Sie die Verbindung her.

    ```powershell
    Enter-PSSession -ComputerName $Name -ConfigurationName Minishell -Credential ~\StorSimpleAdmin -UseSSL  
    ```
    Beispielausgabe:

    ```powershell
    PS C:\Users\Administrator> winrm quickconfig
    WinRM service is already running on this machine.
    WinRM is already set up for remote management on this computer.
    PS C:\Users\Administrator> $Name = "by506b4b5d0790.microsoftdatabox.com"
    PS C:\Users\Administrator> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    PS C:\Users\Administrator> Enter-PSSession -ComputerName $Name -Credential ~\StorSimpleAdmin -ConfigurationName Minishell -UseSSL
    WARNING: Please engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    [by506b4b5d0790.microsoftdatabox.com]: PS>
    ```

### <a name="skip-certificate-validation"></a>Zertifikatüberprüfung überspringen

Wenn Sie die Zertifikate nicht verwenden (nicht empfohlen), können Sie die Zertifikatvalidierung überspringen, indem Sie die Sitzungsoptionen verwenden: `-SkipCACheck -SkipCNCheck -SkipRevocationCheck`.

Führen Sie die folgenden Schritte aus, um die Zertifikatüberprüfung zu überspringen:

1. Legen Sie den `$Name` Parameter fest.

    ```powershell
    $Name = <Name>.<DNS domain>
    ```
 
1. Verwenden Sie Sitzungsoptionen, wenn Sie eine PowerShell-Sitzung öffnen.

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    ```
1. Stellen Sie die Verbindung her.

    ```powershell
    Enter-PSSession -ComputerName $Name -ConfigurationName Minishell -Credential ~\StorSimpleAdmin -UseSSL -SessionOption $sessOptions 
    ```

### <a name="v30-to-v40"></a>[v3.0 bis v4.0](#tab/b)

1. Öffnen Sie eine PowerShell-Sitzung mit **Als Administrator ausführen**. 
1. Fügen Sie Ihren Data Box mithilfe der IP-Adresse zur `TrustedHosts` Liste hinzu. Typ:

    ```powershell
    Set-Item Wsman:\localhost\Client\TrustedHosts <IPv4_address for your Data Box> 
    ``` 

1. Abrufen des Kennworts für Ihre Data Box-Auftragsressource im Azure-Portal. 

1. Konvertieren Sie das im Nur-Text-Format angegebene Kennwort in eine sichere Zeichenfolge. Typ:

    ```powershell
    $Pwd = ConvertTo-SecureString <Password from Azure portal> -AsPlainText -Force 
    ```
1. Geben Sie den Benutzernamen und das Kennwort für die Sitzung an, und erstellen Sie ein Anmeldeinformationsobjekt. Der Standardbenutzername ist `StorSimpleAdmin` und das Kennwort, das Sie im vorherigen Schritt erhalten haben.

    ```powershell
    $Cred = New-Object System.Management.Automation.PSCredential("~\StorSimpleAdmin",$Pwd)
    ``` 
1. Starten Sie eine Sitzung mit dem Gerät.

    ```powershell
    Enter-PSSession -Computer <IPv4_address for your Data Box> -ConfigurationName Minishell -Credential $Cred 
    ```

    Hier sehen Sie eine Beispielausgabe.
    
    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    
    PS C:\WINDOWS\system32> Set-Item wsman:\localhost\Client\TrustedHosts "10.128.45.52"
    
    WinRM Security Configuration.
    This command modifies the TrustedHosts list for the WinRM client. The computers in the
    TrustedHosts list might not be authenticated. The client might send credential information to
     these computers. Are you sure that you want to modify this list?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    PS C:\WINDOWS\system32> $Pwd = ConvertTo-SecureString "Password1" -AsPlainText -Force
    PS C:\WINDOWS\system32> $Cred = New-Object System.Management.Automation.PSCredential("~\StorSimpleAdmin",$Pwd)
    PS C:\WINDOWS\system32> Enter-PSSession -Computer "10.128.45.52" -ConfigurationName Minishell -Credential $Cred
    WARNING: Please engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    [10.128.45.52]: PS>
    ```




  

