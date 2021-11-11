---
title: Behandlung von Problemen mit verwalteten Identitäten für Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie Probleme beheben, wenn Sie eine verwaltete Identität mit einem Automation-Konto verwenden.
services: automation
ms.subservice: ''
ms.date: 10/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: f26c10468322992a5b3fbc302739ff827e841bba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443659"
---
# <a name="troubleshoot-azure-automation-managed-identity-issues"></a>Behandlung von Problemen mit verwalteten Identitäten für Azure Automation

In diesem Artikel werden Lösungen für Probleme beschrieben, die bei Verwendung einer verwalteten Identität mit Ihrem Automation-Konto auftreten können. Allgemeine Informationen zur Verwendung von verwalteten Identitäten mit Automation-Konten finden Sie unter [Übersicht über die Azure Automation-Kontoauthentifizierung](../automation-security-overview.md#managed-identities).

## <a name="scenario-fail-to-get-msi-token-for-account"></a>Szenario: Fehler beim Abrufen des MSI-Tokens für das Konto

### <a name="issue"></a>Problem

Wenn Sie in Ihrem Automation-Konto mit einer benutzerseitig zugewiesenen verwalteten Identität arbeiten, erhalten Sie eine ähnliche Fehlermeldung wie diese: `Failed to get MSI token for account a123456b-1234-12a3-123a-aa123456aa0b`.

### <a name="cause"></a>Ursache

Verwenden einer benutzerseitig zugewiesenen verwalteten Identität vor dem Aktivieren einer systemseitig zugewiesenen verwalteten Identität für Ihr Automation-Konto.

### <a name="resolution"></a>Lösung

Aktivieren einer systemseitig zugewiesenen verwalteten Identität für Ihr Automation-Konto. Verwenden Sie dann die benutzerseitig zugewiesene verwaltete Identität.  

## <a name="scenario-attempt-to-use-managed-identity-with-automation-account-fails"></a>Szenario: Fehler bei dem Versuch, eine verwaltete Identität mit einem Automation-Konto zu verwenden

### <a name="issue"></a>Problem

Bei dem Versuch, mit verwalteten Identitäten in Ihrem Automation-Konto zu arbeiten, tritt ein Fehler wie der folgende auf:

```error
Connect-AzureRMAccount : An error occurred while sending the request. At line:2 char:1 + Connect-AzureRMAccount -Identity + 
CategoryInfo : CloseError: (:) [Connect-AzureRmAccount], HttpRequestException + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>Ursache

Die häufigste Ursache dafür ist, dass Sie die Identität vor dem Versuch, sie zu verwenden, nicht aktiviert haben. Um dies zu überprüfen, führen Sie das folgende PowerShell-Runbook im betroffenen Automation-Konto aus.

```powershell
resource= "?resource=https://management.azure.com/"
$url = $env:IDENTITY_ENDPOINT + $resource
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER)
$Headers.Add("Metadata", "True")

try
{
    $Response = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
}
catch
{
    $StatusCode = $_.Exception.Response.StatusCode.value__
    $stream = $_.Exception.Response.GetResponseStream()
    $reader = New-Object System.IO.StreamReader($stream)
    $responseBody = $reader.ReadToEnd()
    
    Write-Output "Request Failed with Status: $StatusCode, Message: $responseBody"
}
```

Wenn das Problem darin besteht, dass Sie die Identität vor der Verwendung nicht aktiviert haben, sollten Sie ein ähnliches Ergebnis wie das folgende erhalten:

`Request Failed with Status: 400, Message: {"Message":"No managed identity was found for Automation account xxxxxxxxxxxx"}`

### <a name="resolution"></a>Lösung

Sie müssen eine Identität für Ihr Automation-Konto aktivieren, bevor Sie den Dienst für verwaltete Identitäten verwenden können. Siehe [Aktivieren einer verwalteten Identität für Ihr Azure Automation-Konto](../enable-managed-identity-for-automation.md)

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Ihr Problem nicht mit diesem Artikel lösen konnten, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Wenden Sie sich an [@AzureSupport](https://twitter.com/azuresupport) – Dies ist das offizielle Microsoft Azure-Konto, über das die Azure-Community Zugriff auf die richtigen Ressourcen erhält: Antworten, Support und Experten.
* Erstellen Sie einen Azure-Supportfall. Wechseln Sie zur [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und wählen Sie **Support erhalten** aus.