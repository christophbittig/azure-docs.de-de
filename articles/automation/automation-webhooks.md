---
title: Starten eines Azure Automation-Runbooks über einen Webhook
description: In diesem Artikel erfahren Sie, wie Sie mit einem Webhook ein Runbook in Azure Automation über einen HTTP-Aufruf starten.
services: automation
ms.subservice: process-automation
ms.date: 07/21/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e79feb7b45500a53bc7e13557aedcd1c0410554b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026447"
---
# <a name="start-a-runbook-from-a-webhook"></a>Starten eines Runbooks über einen Webhook

Mit einem Webhook kann ein externer Dienst ein bestimmtes Runbook in Azure Automation über eine einfache HTTP-Anforderung starten. Externe Dienste umfassen Azure DevOps Services, GitHub, Azure Monitor-Protokolle und benutzerdefinierte Anwendungen. Ein solcher Dienst kann einen Webhook verwenden, um ein Runbook zu starten, ohne die vollständige Azure Automation-API zu implementieren. Unter [Starten eines Runbooks in Azure Automation](./start-runbooks.md) können Sie Webhooks mit anderen Methoden zum Starten eines Runbooks vergleichen.

> [!NOTE]
> Die Verwendung eines Webhooks zum Starten eines Python-Runbooks wird nicht unterstützt.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Informationen zu den Clientanforderungen für TLS 1.2 mit Webhooks finden Sie unter [TLS 1.2 für Azure Automation](automation-managing-data.md#tls-12-for-azure-automation).

## <a name="webhook-properties"></a>Eigenschaften von Webhooks

Die folgende Tabelle beschreibt die Eigenschaften, die Sie für einen Webhook konfigurieren müssen.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| Name |Name des Webhooks. Sie können einen beliebigen Namen vergeben, da er nicht für den Client verfügbar gemacht wird. Sie benötigen den Namen nur zur Identifizierung des Runbooks in Azure Automation. Es empfiehlt sich, den Webhook entsprechend dem Client zu benennen, der ihn verwenden wird. |
| URL |URL des Webhooks. Dies ist die eindeutige Adresse, die ein Client mit einer HTTP POST-Anforderung aufruft, um das mit dem Webhook verknüpfte Runbook zu starten. Sie wird beim Erstellen des Webhooks automatisch generiert. Sie können keine benutzerdefinierte URL angeben. <br> <br> Die URL enthält ein Sicherheitstoken, das es einem Drittanbietersystem ermöglicht, das Runbook ohne weitere Authentifizierung aufzurufen. Behandeln Sie die URL daher wie ein Kennwort. Aus Sicherheitsgründen können Sie die URL im Azure-Portal nur zu dem Zeitpunkt anzeigen, zu dem der Webhook erstellt wird. Sie sollten die URL zur späteren Verwendung an einem sicheren Ort speichern. |
| Ablaufdatum | Das Ablaufdatum des Webhooks, nach dem er nicht mehr verwendet werden kann. Sie können das Ablaufdatum nach dem Erstellen des Webhooks ändern, solange der Webhook noch nicht abgelaufen ist. |
| Aktiviert | Diese Einstellung gibt an, dass der Webhook bei der Erstellung standardmäßig aktiviert ist. Wenn Sie diese Eigenschaft deaktivieren, kann der Webhook von keinem Client verwendet werden. Sie können diese Eigenschaft beim Erstellen des Webhooks oder zu einem beliebigen späteren Zeitpunkt festlegen. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parameter, die verwendet werden, wenn der Webhook ein Runbook startet

Ein Webhook kann Werte für Runbookparameter definieren, die verwendet werden, wenn das Runbook gestartet wird. Der Webhook muss Werte für alle obligatorischen Parameter des Runbooks einschließen und kann auch Werte für optionale Parameter umfassen. Ein Parameterwert, der für einen Webhook konfiguriert wurde, kann auch nach der Erstellung des Webhooks geändert werden. Mehrere mit einem einzelnen Runbook verknüpften Webhooks können verschiedene Parameterwerte für das Runbook verwenden. Wenn ein Client ein Runbook mithilfe eines Webhooks startet, können die im Webhook definierten Parameterwerte nicht überschrieben werden.

Um Daten vom Client zu empfangen, unterstützt das Runbook einen einzelnen Parameter mit dem Namen `WebhookData`. Dieser Parameter definiert ein Objekt, das Daten enthält, die der Client in eine POST-Anforderung einschließt.

![WebhookData-Eigenschaften](media/automation-webhooks/webhook-data-properties.png)

Der `WebhookData`-Parameter hat folgende Eigenschaften:

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| WebhookName | Name des Webhooks |
| RequestHeader | Die Hashtabelle mit den Headern der eingehenden POST-Anforderung |
| RequestBody | Der Text der eingehenden POST-Anforderung. In diesem Text werden sämtliche Datenformatierungen beibehalten, z. B. Zeichenfolgen, JSON, XML oder formularcodierte Daten. Das Runbook muss so verfasst werden, dass es mit dem erwarteten Datenformat funktioniert. |

Zur Unterstützung des Parameters `WebhookData` ist keine Konfiguration des Webhooks erforderlich, und das Runbook muss ihn nicht akzeptieren. Wenn das Runbook den Parameter nicht definiert, werden alle vom Client gesendeten Details der Anforderung ignoriert.

> [!NOTE]
> Beim Aufrufen eines Webhooks muss der Client für den Fall, dass der Aufruf fehlschlägt, immer alle Parameterwerte speichern. Bei einem Netzwerkausfall oder einem Verbindungsproblem kann die Anwendung fehlgeschlagene Webhook-Aufrufe nicht abrufen.

Wenn Sie beim Erstellen des Webhooks einen Wert für `WebhookData` festlegen, wird dieser überschrieben, wenn der Webhook das Runbook mit den Daten aus der POST-Anforderung des Clients startet. Dies geschieht selbst dann, wenn die Anwendung keine Daten in den Anforderungstext einbezieht.

Wenn Sie ein Runbook starten, das `WebhookData` mit einem anderen Mechanismus als einem Webhook definiert, können Sie einen Wert für `WebhookData` angeben, der vom Runbook erkannt wird. Bei diesem Wert sollte es sich um ein Objekt mit den gleichen [Eigenschaften](#webhook-properties) wie dem Parameter `WebhookData` handeln, damit das Runbook damit genauso funktioniert, wie mit `WebhookData`-Objekten, die über einen Webhook übergeben werden.

Wenn Sie beispielsweise das folgende Runbook aus dem Azure-Portal starten und Webhook-Beispieldaten zum Testen übergeben möchten, müssen Sie die Daten in der Benutzeroberfläche als JSON übergeben.

![WebhookData-Parameter von der Benutzeroberfläche](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Für das nächste Beispiel für ein Runbook definieren wir die folgenden Eigenschaften für `WebhookData`:

* **WebhookName**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Nun übergeben wir das folgende JSON-Objekt in der Benutzeroberfläche für den Parameter `WebhookData`. Dieses Beispiel mit Wagenrücklauf und Zeilenumbruchzeichen entspricht dem vom Webhook übergebenen Format.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![WebhookData-Startparameter von der Benutzeroberfläche](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation protokolliert die Werte aller Eingabeparameter mit dem Runbookauftrag. Somit werden alle vom Client in der Webhookanforderung bereitgestellten Eingaben protokolliert und stehen jedem Benutzer mit Zugriff auf den Automatisierungsauftrag zur Verfügung. Aus diesem Grund sollten Sie sorgfältig überlegen, welche vertraulichen Daten Sie in Webhookaufrufe einschließen.

## <a name="webhook-security"></a>Webhook-Sicherheit

Die Sicherheit eines Webhooks beruht auf dem Schutz seiner URL, die ein Sicherheitstoken enthält, mit dem der Webhook aufgerufen werden kann. Azure Automation führt keinerlei Authentifizierung der Anforderung durch, sofern diese über die richtige URL erfolgt. Aus diesem Grund sollten Ihre Client keine Webhooks für Runbooks verwenden, die sehr vertrauliche Vorgänge ausführen, ohne eine alternative Möglichkeit zur Überprüfung der Anforderung zu verwenden.

Sehen Sie sich die folgenden Strategien an:

* Sie können Logik in ein Runbook einschließen, um zu bestimmen, ob es von einem Webhook aufgerufen wird. Lassen Sie das Runbook die Eigenschaft `WebhookName` des Parameters `WebhookData` überprüfen. Das Runbook kann eine weitere Überprüfung durchführen, indem es in den Eigenschaften `RequestHeader`oder `RequestBody` nach bestimmten Informationen sucht.

* Lassen Sie das Runbook eine externe Bedingung überprüfen, wenn es eine Webhookanforderung empfängt. Betrachten Sie z. B. ein Runbook, das von GitHub aufgerufen wird, sobald ein neuer Commit für ein GitHub-Repository vorhanden ist. Das Runbook kann eine Verbindung mit GitHub herstellen, um zu überprüfen, ob ein neuer Commit durchgeführt wurde, bevor es mit der Ausführung fortfährt.

* Azure Automation unterstützt Diensttags für virtuelle Azure-Netzwerke (genauer gesagt: [GuestAndHybridManagement](../virtual-network/service-tags-overview.md)). Sie können Diensttags verwenden, um Netzwerkzugriffssteuerungen für [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#security-rules) oder [Azure Firewall](../firewall/service-tags.md) zu definieren und Webhooks innerhalb Ihres virtuellen Netzwerks auszulösen. Diensttags können anstelle von spezifischen IP-Adressen verwendet werden, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen **GuestAndHybridManagement** im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den Automation-Dienst zulassen oder verweigern. Dieses Diensttag weist keine Unterstützung für das Zulassen einer präziseren Steuerung durch das Einschränken von IP-Adressbereichen auf eine bestimmte Region auf.

## <a name="create-a-webhook"></a>Erstellen eines Webhooks

Ein Webhook erfordert ein veröffentlichtes Runbook. In dieser exemplarischen Vorgehensweise wird eine geänderte Version des Runbooks verwendet, das unter [Erstellen eines Azure Automation-Runbooks](./learn/powershell-runbook-managed-identity.md) erstellt wurde. Bearbeiten Sie Ihr PowerShell-Runbook mit dem folgenden Code, um die Schritte auszuführen:

```powershell
param
(
    [Parameter(Mandatory=$false)]
    [object] $WebhookData
)

if ($WebhookData.RequestBody) { 
    $names = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        foreach ($x in $names)
        {
            $name = $x.Name
            Write-Output "Hello $name"
        }
}
else {
    Write-Output "Hello World!"
}
```

Speichern und veröffentlichen Sie dann das überarbeitete Runbook. In den folgenden Beispielen wird gezeigt, wie Sie einen Webhook mithilfe von Azure-Portal, PowerShell und REST erstellen.

### <a name="from-the-portal"></a>Über das Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie im Azure-Portal zu Ihrem Automation-Konto.

1. Wählen Sie unter **Prozessautomatisierung** die Option **Runbooks** aus, um die Seite **Runbooks** zu öffnen.

1. Wählen Sie Ihr Runbook in der Liste aus, um die Seite **Übersicht zu** für Runbooks zu öffnen.

1. Wählen Sie **Webhook hinzufügen** aus, um die Seite **Webhook hinzufügen** zu öffnen.

   :::image type="content" source="media/automation-webhooks/add-webhook-icon.png" alt-text="Übersichtsseite für Runbooks mit hervorgehobener Seite „Webhook hinzufügen“.":::

1. Wählen Sie auf der Seite **Webhook hinzufügen** die Option **Neuen Webhook erstellen** aus.

   :::image type="content" source="media/automation-webhooks/add-webhook-page-create.png" alt-text="Seite „Webhook erstellen“ mit hervorgehobener Schaltfläche „Erstellen“.":::

1. Geben Sie den **Namen** für den Webhook ein. Das Ablaufdatum für das Feld **Läuft ab** wird standardmäßig auf ein Jahr ab dem aktuellen Datum festgelegt.

1. Klicken Sie auf das Symbol zum Kopieren, oder drücken Sie <kbd>STRG+C</kbd>, um die URL des Webhooks zu kopieren. Speichern Sie dann die URL an einem sicheren Speicherort.

    :::image type="content" source="media/automation-webhooks/create-new-webhook.png" alt-text="Seite „Webhook erstellen“ mit hervorgehobener URL.":::

    > [!IMPORTANT]
    > Nachdem Sie den Webhook erstellt haben, können Sie die URL nicht erneut abrufen. Stellen Sie sicher, dass Sie sie wie oben beschrieben kopieren und sichern.

1. Klicken Sie auf **OK**, um zur Seite **Webhook hinzufügen** zurückzukehren.

1. Wählen Sie auf der Seite **Webhook hinzufügen** die Option **Parameter und Ausführungseinstellungen konfigurieren** aus, um die Seite **Parameter** zu öffnen.

   :::image type="content" source="media/automation-webhooks/add-webhook-page-parameters.png" alt-text="Seite „Webhook hinzufügen“ mit hervorgehobenen Parametern.":::

1. Überprüfen Sie die Seite **Parameter**. Für das in diesem Artikel verwendete Beispielrunbook sind keine Änderungen erforderlich. Klicken Sie auf **OK**, um zur Seite **Webhook hinzufügen** zurückzukehren.

1. Wählen Sie auf der Seite **Webhook hinzufügen** die Option **Erstellen** aus. Der Webhook wird erstellt, und Sie kehren zur Runbookseite **Übersicht** zurück.

### <a name="using-powershell"></a>PowerShell

1. Stellen Sie sicher, dass Sie die neueste Version des [Az-Moduls](/powershell/azure/new-azureps-module-az) von PowerShell installiert haben.

1. Melden Sie sich interaktiv mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Azure an, und befolgen Sie die Anweisungen.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. Erstellen Sie mit dem Cmdlet [New-AzAutomationWebhook](/powershell/module/az.automation/new-azautomationwebhook) einen Webhook für ein Automation-Runbook. Geben Sie einen geeigneten Wert für die Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    # Initialize variables with your relevant values
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $runbook = "runbookName"
    $psWebhook = "webhookName"
    
    # Create webhook
    $newWebhook = New-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook `
        -RunbookName $runbook `
        -IsEnabled $True `
        -ExpiryTime "12/31/2022" `
        -Force
    
    # Store URL in variable; reveal variable
    $uri = $newWebhook.WebhookURI
    $uri
    ```

   Die Ausgabe ist eine URL, die in etwa wie die folgende aussieht: `https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd`

1. Sie können den Webhook auch mit dem PowerShell-Cmdlet [Get-AzAutomationWebhook](/powershell/module/az.automation/get-azautomationwebhook) überprüfen.

    ```powershell
    Get-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

### <a name="using-rest"></a>Verwenden von REST

Der PUT-Befehl ist unter [Webhook – Erstellen oder Aktualisieren](/rest/api/automation/webhook/create-or-update) dokumentiert. In diesem Beispiel wird das PowerShell-Cmdlet [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) verwendet, um die PUT-Anforderung zu senden.

1. Erstellen Sie eine Datei namens `webhook.json`, und fügen Sie den folgenden Code in die Datei ein:

    ```json
    {
      "name": "RestWebhook",
      "properties": {
        "isEnabled": true,
        "expiryTime": "2022-03-29T22:18:13.7002872Z",
        "runbook": {
          "name": "runbookName"
        }
      }
    }
    ```

   Ändern Sie vor der Ausführung den Wert für die Eigenschaft **runbook:name** in den tatsächlichen Namen Ihres Runbooks. Weitere Informationen zu diesen Eigenschaften finden Sie unter [Eigenschaften von Webhooks](#webhook-properties).

1. Stellen Sie sicher, dass Sie die neueste Version des [Az-Moduls](/powershell/azure/new-azureps-module-az) von PowerShell installiert haben.

1. Melden Sie sich interaktiv mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) bei Azure an, und befolgen Sie die Anweisungen.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. Geben Sie einen geeigneten Wert für die Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    # Initialize variables
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $runbook = "runbookName"
    $restWebhook = "webhookName"
    $file = "path\webhook.json"

    # consume file
    $body = Get-Content $file
    
    # Craft Uri
    $restURI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount/webhooks/$restWebhook`?api-version=2015-10-31"
    ```

1. Führen Sie das folgende Skript aus, um ein Zugriffstoken abzurufen. Wenn Ihr Zugriffstoken abgelaufen ist, müssen Sie das Skript erneut ausführen.

    ```powershell
    # Obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    ```

1. Führen Sie das folgende Skript aus, um den Webhook mithilfe der REST-API zu erstellen.

    ```powershell
    # Invoke the REST API
    # Store URL in variable; reveal variable
    $response = Invoke-RestMethod -Uri $restURI -Method Put -Headers $authHeader -Body $body
    $webhookURI = $response.properties.uri
    $webhookURI
    ```

   Die Ausgabe ist eine URL, die in etwa wie die folgende aussieht: `https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd`

1. Sie können auch [Webhook – Abrufen](/rest/api/automation/webhook/get) verwenden, um den durch seinen Namen identifizierten Webhook abzurufen. Sie können die folgenden PowerShell-Befehle ausführen:

    ```powershell
    $response = Invoke-RestMethod -Uri $restURI -Method GET -Headers $authHeader
    $response | ConvertTo-Json
    ```

## <a name="use-a-webhook"></a>Verwenden eines Webhooks

In diesem Beispiel wird das PowerShell-Cmdlet [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) verwendet, um die POST-Anforderung an den neuen Webhook zu senden.

1. Bereiten Sie Werte vor, die als Text für den Webhookaufruf an das Runbook übergeben werden. Für relativ einfache Werte können Sie ein Skript für die Werte wie folgt erstellen:

    ```powershell
    $Names  = @(
                @{ Name="Hawaii"},
                @{ Name="Seattle"},
                @{ Name="Florida"}
            )
    
    $body = ConvertTo-Json -InputObject $Names
    ```

1. Bei größeren Mengen können Sie eine Datei verwenden. Erstellen Sie eine Datei namens `names.json`, und fügen Sie den folgenden Code in die Datei ein:

    ```json
    [
        { "Name": "Hawaii" },
        { "Name": "Florida" },
        { "Name": "Seattle" }
    ]
    ```

    Ändern Sie den Wert der Variablen `$file` in den tatsächlichen Pfad zur JSON-Datei, bevor Sie die folgenden PowerShell-Befehle ausführen.

    ```powershell
    # Revise file path with actual path
    $file = "path\names.json"
    $bodyFile = Get-Content -Path $file 
    ```

1. Führen Sie die folgenden PowerShell-Befehle zum Aufrufen des Webhooks mithilfe der REST-API aus.

    ```powershell
    $response = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $body -UseBasicParsing
    $response
    
    $responseFile = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $bodyFile -UseBasicParsing
    $responseFile
    ```

   Zur Veranschaulichung wurden zwei Aufrufe für die beiden verschiedenen Methoden zum Erstellen des Textkörpers durchgeführt. Verwenden Sie für die Produktion nur eine Methode.  Die Ausgabe sollte in etwa wie folgt aussehen (nur eine Ausgabe wird gezeigt):

   :::image type="content" source="media/automation-webhooks/webhook-post-output.png" alt-text="Ausgabe des Webhookaufrufs.":::

    Der Client empfängt einen der folgenden Rückgabecodes als Antwort auf die `POST`-Anforderung.

    | Code | Text | BESCHREIBUNG |
    |:--- |:--- |:--- |
    | 202 |Zulässig |Die Anforderung wurde akzeptiert, und das Runbook wurde erfolgreich in die Warteschlange gestellt. |
    | 400 |Ungültige Anforderung |Die Anforderung wurde aus einem der folgenden Gründe nicht akzeptiert: <ul> <li>Der Webhook ist abgelaufen.</li> <li>Der Webhook ist deaktiviert.</li> <li>Das Token in der URL ist ungültig.</li>  </ul> |
    | 404 |Nicht gefunden |Die Anforderung wurde aus einem der folgenden Gründe nicht akzeptiert: <ul> <li>Der Webhook wurde nicht gefunden.</li> <li>Das Runbook wurde nicht gefunden.</li> <li>Das Konto wurde nicht gefunden.</li>  </ul> |
    | 500 |Interner Serverfehler |Die URL ist gültig, es ist jedoch ein Fehler aufgetreten. Senden Sie die Anforderung erneut. |

    Wenn die Anforderung erfolgreich ist, enthält die Antwort des Webhooks wie im Folgenden dargestellt die Auftrags-ID im JSON-Format. Sie enthält eine einzelne Auftrags-ID, wobei das JSON-Format Potenzial für künftige Verbesserungen bietet.

    ```json
    {"JobIds":["<JobId>"]}
    ```

1. Das PowerShell-Cmdlet [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) wird zum Abrufen der Ausgabe verwendet. Die [Azure Automation-API](/rest/api/automation/job) kann auch genutzt werden.

    ```powershell
    #isolate job ID
    $jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
    
    # Get output
    Get-AzAutomationJobOutput `
        -AutomationAccountName $automationAccount `
        -Id $jobid `
        -ResourceGroupName $resourceGroup `
        -Stream Output
    ```

   Die Ausgabe sollte in etwa wie folgt aussehen:

   :::image type="content" source="media/automation-webhooks/webhook-job-output.png" alt-text="Ausgabe des Webhookauftrags.":::

## <a name="update-a-webhook"></a>Aktualisieren eines Webhooks

Wenn ein Webhook erstellt wird, hat er einen Gültigkeitszeitraum von 10 Jahren, nach dem er automatisch abläuft. Wenn ein Webhook abgelaufen ist, kann er nicht reaktiviert werden. Sie können ihn nur entfernen und anschließend neu erstellen. Sie können einen Webhook verlängern, der seine Ablaufzeit noch nicht erreicht hat. Führen Sie die folgenden Schritte aus, um einen Webhook zu verlängern.

1. Navigieren Sie zu dem Runbook, das den Webhook enthält.
1. Wählen Sie unter **Ressourcen** erst **Webhooks** und dann den Webhook aus, den Sie verlängern möchten.
1. Wählen Sie auf der Seite **Webhook** ein neues Ablaufdatum und eine neue Ablaufzeit und dann **Speichern** aus.

Überprüfen Sie den API-Aufruf [Webhook – Aktualisieren](/rest/api/automation/webhook/update) und das PowerShell-Cmdlet [Set-AzAutomationWebhook](/powershell/module/az.automation/set-azautomationwebhook) auf weitere Änderungsmöglichkeiten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es folgen Beispiele für das Entfernen eines Webhooks aus einem Automation-Runbook.

- Mithilfe von PowerShell kann das [Cmdlet Remove-AzAutomationWebhook](/powershell/module/az.automation/remove-azautomationwebhook) wie unten gezeigt verwendet werden. Es wird keine Ausgabe zurückgegeben.

    ```powershell
    Remove-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

- Mithilfe von REST kann die REST-API [Webhook – Löschen](/rest/api/automation/webhook/delete) wie unten gezeigt verwendet werden.

    ```powershell
    Invoke-WebRequest -Method Delete -Uri $restURI -Headers $authHeader
    ```

   Die Ausgabe `StatusCode        : 200` steht für einen erfolgreichen Löschvorgang.

## <a name="create-runbook-and-webhook-with-arm-template"></a>Erstellen eines Runbooks und Webhooks mit einer ARM-Vorlage

Automation-Webhooks können auch mit [Azure Resource Manager](../azure-resource-manager/templates/overview.md)-Vorlagen erstellt werden. Mit dieser Beispielvorlage werden ein Automation-Konto, vier Runbooks und ein Webhook für das benannte Runbook erstellt.

1. Erstellen Sie eine Datei namens `webhook_deploy.json`, und fügen Sie den folgenden Code in die Datei ein:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "automationAccountName": {
                "type": "String",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "webhookName": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Name"
                }
            },
            "runbookName": {
                "type": "String",
                "metadata": {
                    "description": "Runbook Name for which webhook will be created"
                }
            },
            "WebhookExpiryTime": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Expiry time"
                }
            },
            "_artifactsLocation": {
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/",
                "type": "String",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            }
        },
        "resources": [
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "sku": {
                        "name": "Free"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('runbookName')]",
                        "location": "[resourceGroup().location]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "Sample Runbook",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "webhooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('webhookName')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]",
                            "[parameters('runbookName')]"
                        ],
                        "properties": {
                            "isEnabled": true,
                            "expiryTime": "[parameters('WebhookExpiryTime')]",
                            "runbook": {
                                "name": "[parameters('runbookName')]"
                            }
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "webhookUri": {
                "type": "String",
                "value": "[reference(parameters('webhookName')).uri]"
            }
        }
    }
    ```

1. Mit dem folgenden PowerShell-Codebeispiel wird die Vorlage von Ihrem Computer aus bereitgestellt. Geben Sie einen geeigneten Wert für die Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    $resourceGroup = "resourceGroup"
    $templateFile = "path\webhook_deploy.json"
    $armAutomationAccount = "automationAccount"
    $armRunbook = "ARMrunbookName"
    $armWebhook = "webhookName"
    $webhookExpiryTime = "12-31-2022"
    
    New-AzResourceGroupDeployment `
        -Name "testDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $armAutomationAccount `
        -runbookName $armRunbook `
        -webhookName $armWebhook `
        -WebhookExpiryTime $webhookExpiryTime
    ```

   > [!NOTE]
   > Aus Sicherheitsgründen wird der URI nur bei der erstmaligen Bereitstellung einer Vorlage zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Auslösen eines Runbooks mithilfe einer Warnung finden Sie unter [Verwenden einer Warnung zum Auslösen eines Azure Automation-Runbooks](automation-create-alert-triggered-runbook.md).