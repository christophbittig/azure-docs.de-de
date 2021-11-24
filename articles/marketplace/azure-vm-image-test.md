---
title: Testen eines Azure-VM-Images für Azure Marketplace
description: Erfahren Sie, wie Sie ein Azure-VM-Angebot in Azure Marketplace testen und übermitteln.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: ebolton-cyber
ms.author: edewebolton
ms.date: 03/10/2021
ms.openlocfilehash: 4e3e3b281e90236704ebd50cd8ee1420947c4d56
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331241"
---
# <a name="test-a-virtual-machine-image"></a>Testen eines VM-Images

In diesem Thema werden die Schritte erläutert, mit denen ein VM-Image für die Bereitstellung im Azure Marketplace getestet wird.

## <a name="deploy-an-azure-vm"></a>Bereitstellen einer Azure-VM

So stellen Sie einen virtuellen Computer aus der Azure Compute Gallery (früher als Shared Image Gallery bekannt) bereit:

1. Navigieren Sie zur Bildversion von Azure Compute Gallery.
1. Wählen Sie **Virtuellen Computer Erstellen** aus.
1. Geben Sie einen Namen für die VM an, und wählen Sie eine VM-Größe aus.
1. Klicken Sie auf **Überprüfen + erstellen**. Klicken Sie auf **Erstellen**, sobald die Überprüfung erfolgreich abgeschlossen ist.

> [!NOTE]
> Wenn Sie eine VM über eine VHD-Datei erstellen müssen, befolgen Sie die Anweisungen in diesen Artikeln: [Vorbereiten einer Azure Resource Manager-Vorlage](#connect-the-certification-tool-to-a-vm-image) oder [Bereitstellen einer Azure-VM mithilfe von PowerShell](#how-to-use-powershell-to-consume-the-self-test-api).

In diesem Artikel wird beschrieben, wie Sie ein VM-Image im kommerziellen Marketplace testen und übermitteln, um sicherzustellen, dass es die aktuellen Anforderungen für die Veröffentlichung im Azure Marketplace erfüllt.

Führen Sie die folgenden Schritte aus, bevor Sie Ihr VM-Angebot übermitteln:

- Stellen Sie mithilfe Ihres generalisierten Images eine Azure-VM bereit. Weitere Informationen hierzu finden Sie unter [Erstellen eines virtuellen Computers mit einer genehmigten Basis](azure-vm-create-using-approved-base.md) oder [Erstellen eines virtuellen Computers mit einem eigenen Image](azure-vm-create-using-own-image.md).
- Durchführen von Überprüfungen

## <a name="run-validations"></a>Durchführen von Überprüfungen

Es gibt zwei Möglichkeiten, Überprüfungen für das bereitgestellte Image auszuführen.

### <a name="use-certification-test-tool-for-azure-certified"></a>Verwenden des „Certification Test Tool for Azure Certified“

#### <a name="download-and-run-the-certification-test-tool"></a>Herunterladen und Ausführen des Tools zum Testen der Zertifizierung

Das Certification Test Tool for Azure Certified wird auf einem lokalen Windows-Computer ausgeführt, aber es wird eine Azure-basierte Windows- oder Linux-VM getestet. Das Tool zertifiziert, ob Ihr VM-Benutzerimage mit Microsoft Azure genutzt werden kann und ob während der Vorbereitung Ihrer virtuellen Festplatte die Richtlinien und Anforderungen erfüllt wurden.

1. Laden Sie das neueste [Certification Test Tool for Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299) herunter, und installieren Sie es.
2. Öffnen Sie das Zertifizierungstool, und wählen Sie dann **Neuen Test starten** aus.
3. Geben Sie auf dem Bildschirm Testinformationen einen **Testnamen** für den Testlauf ein.
4. Wählen Sie die Plattform für Ihre VM aus (entweder **Windows Server** oder **Linux**). Die Auswahl der Plattform wirkt sich auf die restlichen Optionen aus.
5. Aktivieren Sie das Kontrollkästchen **Für Azure SQL-Datenbank testen**, wenn Ihre VM diesen Datenbankdienst verwendet.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Verbinden des Zertifizierungstools mit einem VM-Image

1. Wählen Sie den Modus für die SSH-Authentifizierung aus: „Kennwortauthentifizierung“ oder „Authentifizierung per Schlüsseldatei“.
2. Geben Sie bei Verwendung der Authentifizierung per Kennwort Werte für den **VM-DNS-Namen**, den **Benutzernamen** und das **Kennwort** ein. Sie können auch die Standardnummer für den SSH-Port ändern.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Zeigt die Auswahl von VM-Testinformationen.":::

3. Wenn Sie die Authentifizierung per Schlüsseldatei verwenden, geben Sie Werte für den VM-DNS-Namen, den Benutzernamen und den Speicherort des privaten Schlüssels ein. Sie können auch eine Passphrase angeben oder die Standardnummer für den SSH-Port ändern.
4. Geben Sie den vollqualifizierten VM-DNS-Namen ein (z. B. MyVMName.Cloudapp.net).
5. Geben Sie **Benutzername** und **Kennwort** ein.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Zeigt die Auswahl des VM-Benutzernamens und -Kennworts.":::

6. Wählen Sie **Weiter** aus.

#### <a name="run-a-certification-test"></a>Ausführen eines Zertifizierungstests

Nachdem Sie die Parameterwerte für Ihr VM-Image im Zertifizierungstool angegeben haben, wählen Sie „Verbindung testen“ aus, um eine gültige Verbindung mit Ihrer VM herzustellen. Klicken Sie nach dem Überprüfen der Verbindung auf **Weiter**, um den Test zu starten. Nach Abschluss des Tests werden die Ergebnisse in einer Tabelle angezeigt. In der Spalte „Status“ wird für jeden Test „Bestanden“, „Fehler“ oder „Warnung“ angezeigt. Wenn einer der Tests nicht erfolgreich durchgeführt wird, wird das Image nicht zertifiziert. Überprüfen Sie in diesem Fall die Anforderungen und Fehlermeldungen, nehmen Sie die vorgeschlagenen Änderungen vor, und führen Sie den Test erneut durch.

Geben Sie nach Abschluss des automatisierten Tests auf dem Bildschirm Fragebogen auf den Registerkarten Allgemeine Bewertung und Kernelanpassung weitere Informationen zu Ihrem VM-Image an, und wählen Sie anschließend Weiter aus.

Im letzten Bildschirm können Sie zusätzliche Informationen angeben, z. B. SSH-Zugriffsinformationen für ein Linux-VM-Image und Erläuterungen für nicht erfolgreiche Bewertungen bei Ausnahmen.

Wählen Sie abschließend Bericht generieren aus, um zusätzlich zu Ihren Antworten im Fragebogen auch die Testergebnisse und Protokolldateien für die ausgeführten Testfälle herunterzuladen.
> [!Note]
> Einige Herausgeber verfügen über Szenarien, in denen VMs gesperrt werden müssen, da Software wie Firewalls auf der VM installiert ist. Laden Sie in diesem Fall das [Certified-Testtool](https://aka.ms/AzureCertificationTestTool) herunter, und übermitteln Sie den Bericht an den Partner Center-[Support](https://aka.ms/marketplacepublishersupport).

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Nutzen der Selbsttest-API mithilfe von PowerShell

### <a name="on-linux-os"></a>Linux-Betriebssystem

Aufrufen der API in PowerShell:

1. Verwenden Sie den Befehl „Invoke-WebRequest“, um die API aufzurufen.
2. Die Methode ist Post und der Inhaltstyp JSON, wie im folgenden Codebeispiel und der Bildschirmaufnahme dargestellt.
3. Geben Sie die Textparameter im JSON-Format an.

Das folgende Beispiel veranschaulicht einen PowerShell-Aufruf der API:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Im Folgenden ein Beispiel für das Aufrufen der API in PowerShell:

[![Bildschirm mit einem Beispiel für das Aufrufen der API in PowerShell](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Bei Verwendung des vorherigen Beispiels können Sie den JSON-Code abrufen und analysieren, um die folgenden Details zu erhalten:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>In diesem Beispielbildschirm mit `$res.Content` sind die Details der Testergebnisse im JSON-Format dargestellt:

[![Bildschirm mit einem Beispiel für das Aufrufen der API in PowerShell mit Details der Testergebnisse](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Hier ein Beispiel für JSON-Testergebnisse, die in einem JSON-Onlineviewer angezeigt werden (z. B. [Code Beautify](https://codebeautify.org/jsonviewer) oder [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Weitere Testergebnisse in einem JSON-Onlineviewer](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Windows-Betriebssystem

Aufrufen der API in PowerShell:

1. Verwenden Sie den Befehl „Invoke-WebRequest“, um die API aufzurufen.
2. Die Methode lautet „Post“ und der Inhaltstyp JSON, wie im folgenden Codebeispiel und Beispielbildschirm dargestellt.
3. Erstellen Sie die Body-Parameter im JSON-Format.

Das folgende Codebeispiel veranschaulicht einen PowerShell-Aufruf der API:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Diese Beispielbildschirme zeigen ein Beispiel für das Aufrufen der API in PowerShell:

**Mit SSH-Schlüssel**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Aufrufen der API in PowerShell mit einem SSH-Schlüssel":::

**Mit Kennwort**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Aufrufen der API in PowerShell mit einem Kennwort":::

<br>Bei Verwendung des vorherigen Beispiels können Sie den JSON-Code abrufen und analysieren, um die folgenden Details zu erhalten:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>In diesem Bildschirm mit `$res.Content` sind die Details der Testergebnisse im JSON-Format dargestellt:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Details der Testergebnisse im JSON-Format":::

<br>Hier ein Beispiel für Testergebnisse, die in einem JSON-Onlineviewer angezeigt werden (z. B. [Code Beautify](https://codebeautify.org/jsonviewer) oder [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Testergebnisse in einem JSON-Onlineviewer](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Nutzen der Selbsttest-API unter dem Linux-Betriebssystem mithilfe von CURL

In diesem Beispiel wird CURL verwendet, um einen POST-API-Aufruf an Azure Active Directory und die Selfhost-VM auszuführen.

1. Anfordern eines Azure AD-Tokens für die Authentifizierung bei einer Selfhost-VM

   Stellen Sie sicher, dass die Werte in der CURL-Anforderung durch die richtigen Werte ersetzt werden.

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   Im Folgenden ein Beispiel für die Antwort auf die Anforderung:

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Übermitteln einer Anforderung für die Selbsttest-VM

   Stellen Sie sicher, dass das Bearertoken und die Parameter durch die richtigen Werte ersetzt werden.

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Beispielantwort auf den API-Aufruf der Selbsttest-VM

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Nächste Schritte

- Melden Sie sich bei [Partner Center](https://go.microsoft.com/fwlink/?linkid=2166002) an.
