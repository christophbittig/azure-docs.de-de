---
title: Konfigurieren von Runbookeingabeparametern in Azure Automation
description: In diesem Artikel wird beschrieben, wie Sie Runbookeingabeparameter konfigurieren, die es ermöglichen, dass Daten beim Start an ein Runbook übergeben werden.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b1b97aaba9a51679775338ecaa5e1215bfe3372b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031706"
---
# <a name="configure-runbook-input-parameters-in-automation"></a>Konfigurieren von Runbookeingabeparametern in Automation

Runbookeingabeparameter erhöhen die Flexibilität eines Runbooks, weil sie die Übergabe von Daten an das Runbook beim Startvorgang ermöglichen. Mit diesen Parametern können Runbookaktionen auf bestimmte Szenarien und Umgebungen abgestimmt werden. In diesem Artikel werden die Konfiguration und Verwendung von Eingabeparametern in Ihren Runbooks beschrieben.

Sie können Eingabeparameter für PowerShell-, PowerShell-Workflow, grafische und Python-Runbooks konfigurieren. Ein Runbook kann mehrere Parameter mit unterschiedlichen Datentypen oder gar keine Parameter aufweisen. Eingabeparameter können obligatorisch oder optional sein, und Sie können Standardwerte für optionale Parameter verwenden.

Sie weisen den Eingabeparametern für ein Runbook Werte zu, wenn Sie es starten. Sie können ein Runbook über das Azure-Portal, einen Webdienst oder die PowerShell starten. Sie haben auch die Möglichkeit, ein Runbook als ein untergeordnetes Runbook zu starten, das inline in einem anderen Runbook aufgerufen werden kann.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurieren von Eingabeparametern in PowerShell-Runbooks

PowerShell- und PowerShell-Workflow-Runbooks in Azure Automation unterstützen Eingabeparameter, die mit den folgenden Eigenschaften definiert werden. 

| **Eigenschaft** | **Beschreibung** |
|:--- |:--- |
| type |Erforderlich. Der für den Parameterwert erwartete Datentyp. Jeder .NET-Typ ist valide. |
| Name |Erforderlich. Der Name des Parameters. Dieser Name muss innerhalb des Runbooks eindeutig sein, muss mit einem Buchstaben beginnen und kann nur Buchstaben, Zahlen oder Unterstriche enthalten. |
| Obligatorisch. |Optional. Boolescher Wert, der angibt, ob für den Parameter ein Wert erforderlich ist. Wenn Sie hier „True“ festlegen, muss beim Start des Runbooks ein Wert angegeben werden. Wenn Sie hier „False“ festlegen, ist das Angeben eines Werts optional. Wenn Sie keinen Wert für die Eigenschaft `Mandatory` angeben, betrachtet PowerShell den Eingabeparameter standardmäßig als optional. |
| Standardwert |Optional. Ein Wert, der für den Parameter verwendet wird, wenn beim Start des Runbooks kein Wert als Eingabe übergeben wird. Das Runbook kann für jeden Parameter einen Standardwert festlegen. |

Windows PowerShell unterstützt neben den oben aufgeführten Eingabeparametern auch andere wie beispielsweise Validierung, Alias und Parametersätze. Azure Automation hingegen unterstützt zurzeit lediglich die aufgeführten Eingabeparametereigenschaften.

Betrachten wir als Beispiel eine Parameterdefinition in einem PowerShell-Workflow-Runbook. Diese Definition hat die folgende allgemeine Form, wobei mehrere Parameter durch Kommas getrennt werden.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Lassen Sie uns nun die Eingabeparameter für ein PowerShell-Workflow-Runbook konfigurieren, das Details virtueller Computer ausgibt – entweder eines einzelnen virtuellen Computers oder aller virtuellen Computer in einer Ressourcengruppe. Dieses Runbook hat, wie im folgenden Screenshot gezeigt, zwei Parameter: den Namen des virtuellen Computers (`VMName`) und den Namen der Ressourcengruppe (`resourceGroupName`).

![Automation: PowerShell-Workflow](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

In dieser Parameterdefinition sind die Eingabeparameter einfache Parameter vom Typ „Zeichenfolge“.

Beachten Sie, dass PowerShell- und PowerShell-Workflow-Runbooks alle einfachen Typen und komplexe Typen wie `Object` oder `PSCredential` als Eingabeparameter unterstützen. Wenn Ihr Runbook einen Objekteingabeparameter aufweist, müssen Sie eine PowerShell-Hashtabelle mit Name-Wert-Paaren verwenden, um einen Wert als Eingabe zu übergeben. Ihr Runbook weist beispielsweise den folgenden Parameter auf.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

In diesem Fall können Sie den folgenden Wert an den Parameter übergeben.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Wenn Sie keinen Wert an einen optionalen Zeichenfolgenparameter mit dem Standardwert NULL übergeben, ist der Wert des Parameters eine leere Zeichenfolge anstelle von NULL.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurieren von Eingabeparametern in grafischen Runbooks

Um die Konfiguration von Eingabeparametern für ein grafisches Runbook zu illustrieren, erstellen wir ein grafisches Runbook, das Details zu virtuellen Computern – entweder eines einzelnen oder aller virtuellen Computer innerhalb einer Ressourcengruppe – ausgibt. Details finden Sie unter [Mein erstes grafisches Runbook](./learn/powershell-runbook-managed-identity.md).

Ein grafisches Runbook verwendet diese wesentlichen Runbook-Aktivitäten:

* Konfiguration des ausführenden Azure-Kontos für die Authentifizierung bei Azure. 
* Definition eines [Get-AzVM](/powershell/module/az.compute/get-azvm)-Cmdlets, um VM-Eigenschaften abzurufen.
* Verwenden Sie die Aktivität [Write-Output](/powershell/module/microsoft.powershell.utility/write-output), um die Namen der virtuellen Computer auszugeben. 

Die Aktivität `Get-AzVM` definiert zwei Eingaben: den VM-Namen und den Namen der Ressourcengruppe. Da diese Namen bei jedem Start des Runbooks unterschiedlich sein können, müssen Sie Ihrem Runbook Eingabeparameter hinzufügen, um diese Eingaben zu akzeptieren. Siehe [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md).

Führen Sie zum Konfigurieren der Eingabeparameter diese Schritte aus.

1. Wählen Sie auf der Seite „Runbooks“ das grafische Runbook aus, und klicken Sie dann auf **Bearbeiten**.
2. Klicken Sie im grafischen Editor auf die Schaltfläche **Eingabe und Ausgabe** und anschließend auf **Eingabe hinzufügen**, um den Eingabeparameterbereich für das Runbook zu öffnen.

   ![Automation: Grafisches Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Im Steuerelement „Eingabe und Ausgabe“ wird eine Liste der für das Runbook definierten Eingabeparameter angezeigt. Hier können Sie entweder einen neuen Eingabeparameter hinzufügen oder die Konfiguration eines bestehenden Eingabeparameters bearbeiten. Um einen neuen Parameter für das Runbook hinzuzufügen, klicken Sie auf **Eingabe hinzufügen**, um das Blatt **Runbook-Eingabeparameter** zu öffnen, auf dem Sie Parameter mithilfe der in [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md) definierten Eigenschaften konfigurieren können.

    ![Neue Eingabe hinzufügen](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Erstellen Sie zwei Parameter mit den folgenden Eigenschaften, die von der Aktivität `Get-AzVM` verwendet werden, und klicken Sie dann auf **OK**.

   * Parameter 1:
        * **Name** -- **VMName**
        * **Typ** – Zeichenfolge
        * **Obligatorisch** -- **Nein**

   * Parameter 2:
        * **Name** -- **resourceGroupName**
        * **Typ** – Zeichenfolge
        * **Obligatorisch** -- **Nein**
        * **Standardwert** -- **Benutzerdefiniert**
        * Benutzerdefinierter Standardwert: Der Name der Ressourcengruppe, die die virtuellen Computer enthält.

5. Zeigen Sie die Parameter im Steuerelement „Eingabe und Ausgabe“ an. 
6. Klicken Sie erneut auf **OK**, und klicken Sie dann auf **Speichern**.
7. Klicken Sie auf **Veröffentlichen**, um Ihr Runbook zu veröffentlichen.

### <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurieren von Eingabeparametern in Python-Runbooks

Im Gegensatz zu PowerShell, PowerShell-Workflow und grafischen Runbooks akzeptieren Python-Runbooks keine benannten Parameter. Der Runbook-Editor analysiert alle Eingabeparameter als Array von Argumentwerten. Sie können auf das Array zugreifen, indem Sie das `sys`-Modul in Ihr Python-Skript importieren und dann das `sys.argv`-Array verwenden. Wichtig: Das erste Element des Arrays (`sys.argv[0]`) ist der Name des Skripts. Somit ist `sys.argv[1]` der erste eigentliche Eingabeparameter.

Ein Beispiel für die Verwendung von Eingabeparametern in einem Python-Runbook finden Sie unter [Mein erstes Python-Runbook in Azure Automation](./learn/automation-tutorial-runbook-textual-python-3.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Eingabeparametern in Runbooks Werte zuweisen

In diesem Abschnitt werden mehrere Möglichkeiten zum Übergeben von Werten an Eingabeparameter in Runbooks beschrieben. Sie können bei folgenden Gelegenheiten Parameterwerte zuweisen:

* [Starten eines Runbooks](#start-a-runbook-and-assign-parameters)
* [Testen eines Runbooks](#test-a-runbook-and-assign-parameters)
* [Verknüpfen eines Zeitplans für das Runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Erstellen eines Webhooks für das Runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Starten eines Runbooks und Zuweisen von Parametern

Ein Runbook kann auf viele verschiedene Arten gestartet werden: Über das Azure-Portal, mit einem Webhook, mit PowerShell-Cmdlets, mit der REST-API oder mit dem SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Starten eines veröffentlichten Runbooks über das Azure-Portal und Zuweisen von Parametern

Wenn Sie im Azure-Portal das [Runbook starten](start-runbooks.md#start-a-runbook-with-the-azure-portal), wird das Blatt **Runbook starten** geöffnet, und Sie können Werte für die von Ihnen erstellten Parameter eingeben.

![Starten unter Verwendung des Portals](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

In der Bezeichnung unterhalb des Eingabefelds können Sie die Eigenschaften sehen, die festgelegt wurden, um Parameterattribute zu definieren, z. B. obligatorisch oder optional, Typ, Standardwert. In der Hilfesprechblase neben dem Namen des Parameters werden ebenfalls alle wichtigen Informationen definiert, die Sie benötigen, um Entscheidungen über Parametereingabewerte zu treffen. 

> [!NOTE]
> Zeichenfolgenparameter unterstützen leere Werte vom Typ „Zeichenfolge“. Die Eingabe von `[EmptyString]` in das Feld für den Eingabeparameter bewirkt, dass eine leere Zeichenfolge an den Parameter übergeben wird. Außerdem unterstützen Zeichenfolgenparameter keine „Null“. Wenn Sie an einen Zeichenfolgenparameter keinen Wert übergeben, interpretiert PowerShell dies als Null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Starten eines veröffentlichten Runbooks mit PowerShell-Cmdlets und Zuweisen von Parametern

* **Azure Resource Manager-Cmdlets**: Sie können ein Automation-Runbook, das in einer Ressourcengruppe erstellt wurde, mit [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) starten.

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlets für das klassische Azure-Bereitstellungsmodell:** Sie können ein Automation-Runbook, das in einer Standardressourcengruppe erstellt wurde, mit [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook) starten.
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Wenn Sie ein Runbook mit PowerShell-Cmdlets starten, wird der Standardparameter `MicrosoftApplicationManagementStartedBy` mit dem Wert `PowerShell` erstellt. Sie können diesen Parameter im Bereich „Auftragsdetails“ anzeigen.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Starten eines Runbooks mit einem SDK und Zuweisen von Parametern

* **Azure Resource Manager-Methode:** Sie können ein Runbook mit dem SDK einer Programmiersprache starten. Unten befindet sich ein C#-Codeausschnitt um ein Runbook in Ihrem Automation-Konto zu starten. Sie können den kompletten Code in unserer [GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)ansehen.

  ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
  {
    var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
      {
        Runbook = new RunbookAssociationProperty
        {
          Name = runbookName
        },
        Parameters = parameters
      }
    });
    return response.Job;
  }
  ```

* **Methode für das klassische Azure-Bereitstellungsmodell:** Sie können ein Runbook mit dem SDK einer Programmiersprache starten. Unten befindet sich ein C#-Codeausschnitt um ein Runbook in Ihrem Automation-Konto zu starten. Sie können den kompletten Code in unserer [GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)ansehen.

  ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
  {
    var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
      {
        Runbook = new RunbookAssociationProperty
        {
          Name = runbookName
        },
        Parameters = parameters
      }
    });
    return response.Job;
  }
  ```

  Zum Starten dieser Methode erstellen Sie ein Wörterbuch, um die Runbook-Parameter `VMName` und `resourceGroupName` und deren Werte zu speichern. Starten Sie jetzt das Runbook. Unten befindet sich ein C#-Codeausschnitt um die oben definierte Methode aufzurufen.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Starten eines Runbooks mit REST-API und Zuweisen von Parametern

Sie können einen Runbookauftrag mit der REST-API von Azure Automation erstellen und starten. Hierzu wird die `PUT`-Methode mit dem folgenden Anforderungs-URI verwendet: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

Ersetzen Sie in der Anforderungs-URI die folgenden Parameter:

* `subscriptionId`: Die ID Ihres Azure-Abonnements.  
* `resourceGroupName`: Der Name der Ressourcengruppe für das Automation-Konto.
* `automationAccountName`: Der Name des Automation-Kontos, das im angegebenen Clouddienst gehostet wird.  
* `jobName`: Die GUID für den Auftrag. Die GUIDs in PowerShell können mithilfe von `[GUID]::NewGuid().ToString()*` erstellt werden.

Um Parameter an den Runbookauftrag zu übergeben, verwenden Sie den Anforderungstext. Die folgenden Informationen werden im JSON-Format benötigt:

* Runbookname: Erforderlich. Der Name des Runbooks für den zu startenden Auftrag.  
* Runbookparameter: Optional. Ein Wörterbuch der Parameterliste im Format (Name, Wert), wobei der Name vom Typ „Zeichenfolge“ sein sollte, und als Wert jeder gültige JSON-Wert infrage kommt.

Wenn Sie das zuvor erstellte Runbook **Get-AzureVMTextual** mit `VMName` und `resourceGroupName` als Parameter starten möchten, verwenden Sie das folgende JSON-Format für den Anforderungstext.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Wenn der Auftrag erfolgreich erstellt wurde, erhalten Sie als Antwort den HTTP-Statuscode 201. Weitere Informationen zu Antwortheadern und Antworttext finden Sie unter [Erstellen eines Runbookauftrags mit der REST-API](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testen eines Runbooks und Zuweisen von Parametern

Wenn Sie zum [Testen der Entwurfsversion Ihres Runbooks](./manage-runbooks.md) die Testoption verwenden, wird die Seite „Test“ geöffnet. Verwenden Sie diese Seite, um Werte für die Parameter zu konfigurieren, die Sie erstellt haben.

![Testen und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Verknüpfen eines Zeitplans mit einem Runbook und Zuweisen von Parametern

Sie können einen [Zeitplan mit Ihrem Runbook verknüpfen](./shared-resources/schedules.md) , damit Ihr Runbook zu einem bestimmten Zeitpunkt startet. Beim Erstellen des Zeitplans weisen Sie Eingabeparameter zu, und das Runbook verwendet diese Werte, wenn es vom Zeitplan gestartet wird. Sie können den Zeitplan erst speichern, wenn alle erforderlichen Parameterwerte bereitgestellt sind.

![Planen und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Erstellen eines Webhooks für ein Runbook, und Zuweisen von Parametern

Sie können einen [Webhook](automation-webhooks.md) für Ihr Runbook erstellen und Runbookeingabeparameter konfigurieren. Sie können den Webhook erst speichern, wenn alle erforderlichen Parameterwerte bereitgestellt sind.

![Erstellen eines Webhooks und Zuweisen von Parametern](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Wenn Sie einen Webhook verwenden, um ein Runbook auszuführen, wird der vordefinierte Eingabeparameter `[WebhookData](automation-webhooks.md)` zusammen mit den von Ihnen definierten Eingabeparametern gesendet. 

![WebhookData-Parameter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Übergeben eines JSON-Objekts an ein Runbook

Es kann hilfreich sein, Daten, die Sie an ein Runbook übergeben möchten, in einer JSON-Datei zu speichern. Sie können z. B. eine JSON-Datei erstellen, die alle Parameter enthält, die an ein Runbook übergeben werden sollen. Zu diesem Zweck müssen Sie den JSON-Code in eine Zeichenfolge und diese dann in ein PowerShell-Objekt konvertieren, bevor Sie es anschließend an das Runbook übergeben.

Dieser Abschnitt verwendet ein Beispiel, bei dem ein PowerShell-Skript[Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) aufruft, um ein PowerShell-Runbook zu starten, mit dem der Inhalt der JSON-Datei an das Runbook übergeben wird. Das PowerShell-Runbook startet einen virtuellen Azure-Computer, indem die Parameter für den virtuellen Computer aus dem JSON-Objekt abgerufen werden.

### <a name="create-the-json-file"></a>Erstellen der JSON-Datei

Geben Sie den folgenden Code in eine Textdatei ein, und speichern Sie diese als **test.json** auf dem lokalen Computer.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Erstellen des Runbooks

Erstellen Sie ein neues PowerShell-Runbook mit dem Namen **Test-Json** in Azure Automation.

Damit die JSON-Daten akzeptiert werden, muss das Runbook ein Objekt als Eingabeparameter annehmen. Das Runbook kann dann die in der JSON-Datei definierten Eigenschaften verwenden.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup -DefaultProfile $AzureContext
```

Wenn Sie möchten, dass das Runbook mit der systemseitig zugewiesenen verwalteten Identität ausgeführt wird, lassen Sie den Code unverändert. Wenn Sie lieber eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, gehen Sie wie folgt vor:
1. Entfernen Sie `$AzureContext = (Connect-AzAccount -Identity).context` aus Zeile 10.
1. Fügen Sie stattdessen `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` ein.
1. Geben Sie die Client-ID ein.

Speichern und veröffentlichen Sie dieses Runbook in Ihrem Automation-Konto.

### <a name="call-the-runbook-from-powershell"></a>Aufrufen des Runbooks in PowerShell

Jetzt können Sie das Runbook auf dem lokalen Computer mithilfe von Azure PowerShell aufrufen. 

1. Melden Sie sich wie gezeigt bei Azure an. Anschließend werden Sie zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Für PowerShell-Runbooks sind `Add-AzAccount` und `Add-AzureRMAccount` Aliase für `Connect-AzAccount`. Beachten Sie, dass diese Aliase für grafische Runbooks nicht verfügbar sind. Ein grafisches Runbook kann nur `Connect-AzAccount` selbst verwenden.

1. Rufen Sie den Inhalt der gespeicherten JSON-Datei ab, und konvertieren Sie ihn in eine Zeichenfolge. `JsonPath` gibt den Pfad an, unter dem Sie die JSON-Datei gespeichert haben.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Konvertieren Sie den Zeichenfolgeninhalt von `$json` in ein PowerShell-Objekt.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Erstellen Sie eine Hashtabelle der Parameter für `Start-AzAutomationRunbook`. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Beachten Sie, dass Sie den Wert von `Parameters` auf das PowerShell-Objekt festlegen, in dem die Werte aus der JSON-Datei enthalten sind.
1. Starten Sie das Runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Vorbereiten eines Textrunbooks finden Sie unter [Bearbeiten von Textrunbooks in Azure Automation](automation-edit-textual-runbook.md).
* Informationen zum Vorbereiten eines grafischen Runbooks finden Sie unter [Erstellen von grafischen Azure Automation-Runbooks](automation-graphical-authoring-intro.md).