---
title: Verwenden von Bereitstellungsskripts in Vorlagen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Bereitstellungsskripts in Azure Resource Manager-Vorlagen verwenden.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: de462f24f05b93f40453cd74c85a41b5c11ac0d3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656301"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Verwenden von Bereitstellungsskripts in ARM-Vorlagen

Erfahren Sie, wie Sie Bereitstellungsskripts in Azure Resource Manager-Vorlagen (ARM-Vorlagen) verwenden. Bei einem neuen Ressourcentyp mit dem Namen `Microsoft.Resources/deploymentScripts` können Benutzer Skripts in Vorlagenbereitstellungen ausführen und die Ausführungsergebnisse überprüfen. Diese Skripts können zum Ausführen benutzerdefinierter Schritte wie der folgenden verwendet werden:

- Hinzufügen von Benutzern zu einem Verzeichnis
- Ausführen von Vorgängen auf Datenebene, z. B. Kopieren von Blobs oder Ausführen von Datenbankseeding
- Suchen und Validieren eines Lizenzschlüssels
- Erstellen eines selbstsignierten Zertifikats
- Erstellen eines Objekts in Azure AD
- Suchen von IP-Adressblöcken auf einem benutzerdefinierten System

Vorteile von Bereitstellungsskripts:

- Einfach zu codieren, zu verwenden und zu debuggen. Sie können Bereitstellungsskripts in Ihren bevorzugten Entwicklungsumgebungen entwickeln. Die Skripts können in Vorlagen oder in externe Skriptdateien eingebettet werden.
- Sie können die Skriptsprache und die Plattform angeben. Derzeit werden Azure PowerShell- und Azure CLI-Bereitstellungsskripts in der Linux-Umgebung unterstützt.
- Sie können Befehlszeilenargumente an das Skript übergeben.
- Sie können Skriptausgaben angeben und an die Bereitstellung zurückgeben.

Die Bereitstellungsskriptressource ist nur in den Regionen verfügbar, in denen Azure Container Instances verfügbar ist.  Informationen dazu finden Sie unter [Ressourcenverfügbarkeit für Azure Container Instances in Azure-Regionen](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Für die Skriptausführung und Problembehandlung werden ein Speicherkonto und eine Containerinstanz benötigt. Sie haben die Möglichkeit, ein vorhandenes Speicherkonto anzugeben. Andernfalls wird das Speicherkonto zusammen mit der Containerinstanz vom Skriptdienst automatisch erstellt. Die beiden automatisch erstellten Ressourcen werden normalerweise vom Skriptdienst gelöscht, wenn die Ausführung des Bereitstellungsskripts beendet ist. Die Ressourcen werden Ihnen in Rechnung gestellt, bis sie gelöscht werden. Weitere Informationen finden Sie unter [Bereinigen von Bereitstellungsskriptressourcen](#clean-up-deployment-script-resources).

> [!NOTE]
> Wiederholungslogik für die Azure-Anmeldung ist jetzt in das Wrapperskript integriert. Wenn Sie Berechtigungen in derselben Vorlage wie Ihre Bereitstellungsskripts erteilen, wiederholt der Bereitstellungsskriptdienst die Anmeldung 10 Minuten lang mit einem Intervall von 10 Sekunden, bis die Rollenzuweisung für verwaltete Identitäten repliziert wird.

### <a name="microsoft-learn"></a>Microsoft Learn

Weitere Informationen zum Toolkit für ARM-Vorlagentests und praktische Anleitungen finden Sie in **Microsoft Learn** unter [Erweitern von ARM-Vorlagen mithilfe von Bereitstellungsskripts](/learn/modules/extend-resource-manager-template-deployment-scripts).

## <a name="configure-the-minimum-permissions"></a>Konfigurieren der mindestens erforderlichen Berechtigungen

Für die Bereitstellungsskript-API-Version 2020-10-01 oder höher sind zwei Prinzipale an der Ausführung des Bereitstellungsskripts beteiligt:

- **Bereitstellungsprinzipal** (dar zur Bereitstellung der Vorlage genutzte Prinzipal): Dieser Prinzipal wird verwendet, um zugrunde liegende Ressourcen, die für die Ausführung der Bereitstellungsskriptressource erforderlich sind – ein Speicherkonto und eine Azure-Containerinstanz zu erstellen. Weisen Sie dem Bereitstellungsprinzipal eine benutzerdefinierte Rolle mit den folgenden Eigenschaften zu, um die Berechtigungen mit den geringsten Rechten zu konfigurieren:

    ```json
    {
      "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
      "description": "Configure least privilege for the deployment principal in deployment script",
      "type": "customRole",
      "IsCustom": true,
      "permissions": [
        {
          "actions": [
            "Microsoft.Storage/storageAccounts/*",
            "Microsoft.ContainerInstance/containerGroups/*",
            "Microsoft.Resources/deployments/*",
            "Microsoft.Resources/deploymentScripts/*"
          ],
        }
      ],
      "assignableScopes": [
        "[subscription().id]"
      ]
    }
    ```

    Wenn die Azure Storage- und die Azure Container Instance-Ressourcenanbieter nicht registriert sind, müssen Sie auch `Microsoft.Storage/register/action` und `Microsoft.ContainerInstance/register/action` hinzufügen.

- **Bereitstellungsskriptprinzipal:** Dieser Prinzipal ist nur erforderlich, wenn sich das Bereitstellungsskript bei Azure authentifizieren und Azure CLI PowerShell aufrufen muss. Es gibt zwei Möglichkeiten, den Bereitstellungsskriptprinzipal anzugeben:

  - Spezifizieren Sie eine benutzerseitig zugewiesene verwaltete Identität in der `identity` Eigenschaft (siehe [ Stichprobenvorlagen](#sample-templates)). Wenn angegeben, ruft der Skriptdienst `Connect-AzAccount -Identity` auf, bevor das Bereitstellungsskript aufruft. Die verwaltete Identität muss über den erforderlichen Zugriff verfügen, um den Vorgang im Skript abzuschließen. Zurzeit wird nur eine benutzerseitig zugewiesene verwaltete Identität für die `identity` Eigenschaft unterstützt. Verwenden Sie die zweite Methode in dieser Liste, um sich mit einer anderen Identität anzumelden.
  - Übergeben Sie die Anmeldeinformationen für den Dienstprinzipal als sichere Umgebungsvariablen, und rufen Sie dann [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) oder [az login](/cli/azure/reference-index?view=azure-cli-latest#az_login&preserve-view=true) im Bereitstellungsskript auf.

  Wenn eine verwaltete Identität verwendet wird, muss für den Bereitstellungsprinzipal die Rolle **Operator für verwaltete Identität** (eine integrierte Rolle) der Ressource der verwalteten Identität zugewiesen sein.

## <a name="sample-templates"></a>Beispielvorlagen

Nachfolgend finden Sie ein JSON-Beispiel. Weitere Informationen finden Sie im neuesten [Vorlagenschema](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Dieses Beispiel dient zu Demonstrationszwecken. Die Eigenschaften `scriptContent` und `primaryScriptUri` können nicht zusammen in einer Vorlage enthalten sein.

> [!NOTE]
> _scriptContent_ zeigt ein Skript mit mehreren Zeilen an.  Das Azure-Portal und die Azure DevOps-Pipeline können keine Bereitstellungsskripts mit mehreren Zeilen analysieren. Sie können entweder die PowerShell-Befehle (mit Semikolons oder _\\r\\n_ bzw. _\\n_) zu einer Zeile verketten oder die `primaryScriptUri`-Eigenschaft mit einer externen Skriptdatei verwenden. Es stehen viele kostenlose Escape-/Unescapetools für JSON-Zeichenfolgen zur Verfügung. Beispiel: [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html)

Details zu Eigenschaftswerten:

- `identity`: Für die API-Version 2020-10-01 oder höher des Bereitstellungsskripts ist eine vom Benutzer zugewiesene verwaltete Identität optional, es sei denn, Sie müssen Azure-spezifische Aktionen im Skript ausführen.  Für die API-Version 2019-10-01-preview ist eine verwaltete Identität erforderlich, da der Bereitstellungsskriptdienst diese verwendet, um die Skripts auszuführen. Wenn die Identitätseigenschaft angegeben wird, ruft der Skriptdienst `Connect-AzAccount -Identity` auf, bevor das Benutzerskript aufgerufen wird. Zurzeit wird nur eine benutzerseitig zugewiesene verwaltete Identität unterstützt. Um sich mit einer anderen Identität anzumelden, können Sie [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) im Skript aufrufen.
- `kind`: Geben Sie den Typ des Skripts an. Zurzeit werden Azure PowerShell- und Azure CLI-Skripts unterstützt. Die Werte sind **AzurePowerShell** und **AzureCLI**.
- `forceUpdateTag`: Wenn Sie diesen Wert zwischen Vorlagenbereitstellungen ändern, wird das Bereitstellungsskript erneut ausgeführt. Die beiden Funktionen `newGuid()` und `utcNow()` können nur mit dem Standardwert eines Parameters verwendet werden. Weitere Informationen finden Sie unter [Mehrmaliges Ausführen des Skripts](#run-script-more-than-once).
- `containerSettings`: Geben Sie die Einstellungen zum Anpassen der Azure-Containerinstanz an. Im Bereitstellungsskript muss eine neue Azure-Containerinstanz angegeben werden. Sie können keine vorhandene Azure-Containerinstanz angeben. Sie können jedoch den Namen der Containergruppe mithilfe von `containerGroupName` anpassen. Falls nicht angegeben, wird der Gruppenname automatisch generiert.
- `storageAccountSettings`: Geben Sie die Einstellungen zur Verwendung eines vorhandenen Speicherkontos an. Wenn `storageAccountName` nicht angegeben ist, wird ein Speicherkonto automatisch erstellt. Weitere Informationen finden Sie unter [Verwenden eines vorhandenen Speicherkontos](#use-existing-storage-account).
- `azPowerShellVersion`/`azCliVersion`: Geben Sie die zu verwendende Modulversion an. Eine Liste mit den unterstützten Azure PowerShell-Versionen finden Sie [hier](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Eine Liste mit den unterstützten Azure CLI-Versionen finden Sie [hier](https://mcr.microsoft.com/v2/azure-cli/tags/list).

  >[!IMPORTANT]
  > Das Bereitstellungsskript verwendet die verfügbaren CLI-Images von Microsoft Container Registry (MCR). Das Zertifizieren eines CLI-Images für das Bereitstellungsskript dauert ungefähr einen Monat. Verwenden Sie nicht die CLI-Versionen, die innerhalb von 30 Tagen veröffentlicht wurden. Die Veröffentlichungsdaten für die Images finden Sie unter [Versionshinweise für die Azure CLI](/cli/azure/release-notes-azure-cli). Wenn eine nicht unterstützte Version verwendet wird, werden in der Fehlermeldung die unterstützten Versionen aufgelistet.

- `arguments`: Geben Sie die Parameterwerte an. Die Werte werden durch Leerzeichen voneinander getrennt.

  In Bereitstellungsskripts werden die Argumente mithilfe des Systemaufrufs [CommandLineToArgvW](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) in ein Zeichenfolgenarray unterteilt. Dieser Schritt ist erforderlich, weil die Argumente als [Befehlseigenschaft](/rest/api/container-instances/containergroups/createorupdate#containerexec) an Azure Container Instance übergeben werden. Die Befehlseigenschaft ist ein Zeichenfolgenarray.

  Wenn die Argumente Escapezeichen enthalten, verwenden Sie [JsonEscaper](https://www.jsonescaper.com/), um die Zeichen mit doppelten Escapezeichen zu versehen. Fügen Sie Ihre Originalzeichenfolge mit Escapezeichen in das Tool ein, und wählen Sie dann **Escape** (Mit Escapezeichen versehen) aus.  Daraufhin wird von dem Tool eine Zeichenfolge mit doppelten Escapezeichen ausgegeben. In der vorherigen Beispielvorlage wird beispielsweise das Argument `-name \"John Dole\"` verwendet. Die Zeichenfolge mit Escapezeichen lautet `-name \\\"John Dole\\\"`.

  Wenn Sie einen ARM-Vorlagenparameter vom Typ „Objekt“ als Argument übergeben möchten, konvertieren Sie das Objekt mithilfe der [string()](./template-functions-string.md#string)-Funktion in eine Zeichenfolge, und verwenden Sie anschließend die [replace()](./template-functions-string.md#replace)-Funktion, um alle Vorkommen von `\"` durch `\\\"` zu ersetzen. Beispiel:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Weitere Informationen finden Sie in der [Beispielvorlage](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- `environmentVariables`: Geben Sie die Umgebungsvariablen an, die an das Skript übergeben werden sollen. Weitere Informationen finden Sie unter [Entwickeln von Bereitstellungsskripts](#develop-deployment-scripts).
- `scriptContent`: Geben Sie den Skriptinhalt an. Wenn Sie ein externes Skript ausführen möchten, verwenden Sie stattdessen `primaryScriptUri`. Beispiele finden Sie unter [Verwenden von Inlineskripts](#use-inline-scripts) und [Verwenden externer Skripts](#use-external-scripts).
- `primaryScriptUri`: Geben Sie eine öffentlich zugängliche URL zum primären Bereitstellungsskript mit unterstützten Dateierweiterungen an. Weitere Informationen finden Sie unter [Verwenden externer Skripts](#use-external-scripts).
- `supportingScriptUris`: Geben Sie ein Array öffentlich zugänglicher URLs zu unterstützenden Dateien an, die in `scriptContent` oder `primaryScriptUri` aufgerufen werden. Weitere Informationen finden Sie unter [Verwenden externer Skripts](#use-external-scripts).
- `timeout`: Geben Sie die maximal zulässige Ausführungsdauer für das Skript im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601) an. Der Standardwert ist **P1D**.
- `cleanupPreference`. Geben Sie die Einstellung für das Bereinigen der Bereitstellungsressourcen an, nachdem die Skriptausführung beendet wurde. Die Standardeinstellung ist **Always**. Damit werden die Ressourcen unabhängig vom Endzustand (Erfolg, Fehler, Abbruch) gelöscht. Weitere Informationen finden Sie unter [Bereinigen von Bereitstellungsskriptressourcen](#clean-up-deployment-script-resources).
- `retentionInterval`: Geben Sie das Intervall an, das vom Dienst für die Aufbewahrung der Bereitstellungsskriptressourcen verwendet wird, nachdem das Bereitstellungsskript einen Beendigungszustand erreicht. Die Bereitstellungsskriptressourcen werden gelöscht, wenn dieser Zeitraum abgelaufen ist. Die Dauer basiert auf dem [ISO 8601-Muster](https://en.wikipedia.org/wiki/ISO_8601). Der Aufbewahrungszeitraum liegt zwischen 1 und 26 Stunden (PT26H). Diese Eigenschaft wird verwendet, wenn `cleanupPreference` auf **OnExpiration** festgelegt ist. Weitere Informationen finden Sie unter [Bereinigen von Bereitstellungsskriptressourcen](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Weitere Beispiele

- [Beispiel 1:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json) In diesem Beispiel wird ein Schlüsseltresor erstellt und ein Bereitstellungsskript verwendet, um dem Schlüsseltresor ein Zertifikat zuzuweisen.
- [Beispiel 2:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json) In diesem Beispiel werden eine Ressourcengruppe auf der Abonnementebene und ein Schlüsseltresor in der Ressourcengruppe erstellt. Anschließend wird ein Bereitstellungsskript verwendet, um dem Schlüsseltresor ein Zertifikat zuzuweisen.
- [Beispiel 3:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json) In diesem Beispiel wird eine benutzerseitig verwaltete Identität erstellt. Der Identität wird die Rolle „Mitwirkender“ auf der Ressourcengruppenebene zugewiesen, und es wird ein Schlüsseltresor erstellt. Anschließend wird ein Bereitstellungsskript verwendet, um dem Schlüsseltresor ein Zertifikat zuzuweisen.

## <a name="use-inline-scripts"></a>Verwenden von Inlineskripts

Für die folgende Vorlage wurde eine Ressource mit dem Typ `Microsoft.Resources/deploymentScripts` definiert. Der hervorgehobene Teil ist das Inlineskript.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Da die Inlinebereitstellungsskripts in doppelte Anführungszeichen eingeschlossen sind, muss für Zeichenfolgen innerhalb der Bereitstellungsskripts ein umgekehrter Schrägstrich ( **&#92;** ) als Escapezeichen verwendet werden, oder sie müssen in einfache Anführungszeichen eingeschlossen werden. Sie können auch, wie im vorherigen JSON-Beispiel gezeigt, eine Zeichenfolgenersetzung in Erwägung ziehen.

Das Skript akzeptiert einen Parameter und gibt den Parameterwert aus. `DeploymentScriptOutputs` wird zum Speichern von Ausgaben verwendet. Im Abschnitt „outputs“ zeigt die Zeile `value` an, wie auf die gespeicherten Werte zugegriffen wird. `Write-Output` wird zum Debuggen verwendet. Informationen zum Zugreifen auf die Ausgabedatei finden Sie unter [Überwachen von Bereitstellungsskripts und Behandeln von Problemen](#monitor-and-troubleshoot-deployment-scripts). Beschreibungen der Eigenschaften finden Sie unter [Beispielvorlagen](#sample-templates).

Wählen Sie zum Ausführen des Skripts **Jetzt testen** aus, um die Cloud Shell zu öffnen, und fügen Sie anschließend den folgenden Code in den Shellbereich ein.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

Die Ausgabe sieht wie folgt aus:

![Resource Manager-Vorlage: Ausgabe des Hallo-Welt-Bereitstellungsskripts](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Verwenden externer Skripts

Neben Inlineskripts können Sie auch externe Skriptdateien verwenden. Es werden nur primäre PowerShell-Skripts mit der Dateierweiterung _ps1_ unterstützt. Bei CLI-Skripts können die primären Skripts beliebige (oder gar keine) Erweiterungen besitzen, solange es sich bei den Skripts um gültige Bash-Skripts handelt. Um externe Skriptdateien zu verwenden, ersetzen Sie `scriptContent` durch `primaryScriptUri`. Beispiel:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Weitere Informationen finden Sie in der [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Die externen Skriptdateien müssen zugänglich sein. Generieren Sie zum Sichern Ihrer in Azure Storage-Konten gespeicherten Skriptdateien ein SAS-Token, und fügen Sie es in den URI für die Vorlage ein. Legen Sie die Ablaufzeit so fest, dass ausreichend Zeit für die Bereitstellung bleibt. Weitere Informationen finden Sie unter [Bereitstellen einer privaten ARM-Vorlage mit SAS-Token](./secure-template-with-sas-token.md).

Es ist Ihre Aufgabe, die Integrität der Skripts zu gewährleisten, auf die vom Bereitstellungsskript verwiesen wird, entweder `primaryScriptUri` oder `supportingScriptUris`. Verweisen Sie nur auf Skripts, denen Sie vertrauen.

## <a name="use-supporting-scripts"></a>Verwenden unterstützender Skripts

Sie können komplizierte Logik in unterstützende Skriptdateien aufteilen. Die `supportingScriptUris`-Eigenschaft ermöglicht Ihnen, bei Bedarf ein Array von URIs für die unterstützenden Skriptdateien bereitzustellen:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Unterstützende Skriptdateien können sowohl aus Inlineskripts als auch aus primären Skriptdateien aufgerufen werden. Unterstützende Skriptdateien unterliegen keinen Einschränkungen in Bezug auf die Dateierweiterung.

Die unterstützenden Dateien werden zur Laufzeit in `azscripts/azscriptinput` kopiert. Verwenden Sie den relativen Pfad, um aus Inlineskripts und primären Skriptdateien auf die unterstützenden Dateien zu verweisen.

## <a name="work-with-outputs-from-powershell-script"></a>Arbeiten mit Ausgaben von PowerShell-Skripts

Die folgende Vorlage zeigt, wie Werte zwischen zwei `deploymentScripts`-Ressourcen übergeben werden:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

In der ersten Ressource definieren Sie eine Variable mit dem Namen `$DeploymentScriptOutputs`, die Sie zum Speichern der Ausgabewerte verwenden. Um aus einer anderen Ressource in der Vorlage auf den Ausgabewert zuzugreifen, verwenden Sie Folgendes:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>Arbeiten mit Ausgaben von CLI-Skripts

Anders als das PowerShell-Bereitstellungsskript stellt die CLI-/Bash-Unterstützung keine allgemeine Variable zum Speichern von Skriptausgaben bereit. Stattdessen ist eine Umgebungsvariable namens `AZ_SCRIPTS_OUTPUT_PATH` vorhanden, in der der Speicherort der Skriptausgabedatei gespeichert wird. Wenn ein Bereitstellungsskript aus einer Resource Manager-Vorlage ausgeführt wird, wird diese Umgebungsvariable von der Bash-Shell automatisch für Sie festgelegt. Der Wert von `AZ_SCRIPTS_OUTPUT_PATH` lautet */mnt/azscripts/azscriptoutput/scriptoutputs.json*.

Die Ausgaben von Bereitstellungsskripts müssen am Speicherort `AZ_SCRIPTS_OUTPUT_PATH` gespeichert werden, und es muss sich bei den Ausgaben um ein gültiges JSON-Zeichenfolgenobjekt handeln. Der Inhalt der Datei muss als Schlüssel-Wert-Paar gespeichert werden. Beispielsweise wird ein Array von Zeichenfolgen als `{ "MyResult": [ "foo", "bar"] }` gespeichert.  Das Speichern nur der Arrayergebnisse (z. B. `[ "foo", "bar" ]`) ist ungültig.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

Im vorherigen Beispiel wird [jq](https://stedolan.github.io/jq/) verwendet. Dies ist in den Containerimages enthalten. Weitere Informationen finden Sie unter [Konfigurieren der Entwicklungsumgebung](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Verwenden eines vorhandenen Speicherkontos

Für die Skriptausführung und Problembehandlung werden ein Speicherkonto und eine Containerinstanz benötigt. Sie haben die Möglichkeit, ein vorhandenes Speicherkonto anzugeben. Andernfalls wird das Speicherkonto zusammen mit der Containerinstanz vom Skriptdienst automatisch erstellt. Die Voraussetzungen für die Verwendung eines vorhandenen Speicherkontos:

- Unterstützte Speicherkontotypen:

    | SKU             | Unterstützte Typen     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Storage, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Storage, StorageV2 |
    | Standard_RAGRS  | Storage, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Diese Kombinationen unterstützen Dateifreigaben. Weitere Informationen finden Sie unter [Erstellen einer Azure-Dateifreigabe](../../storage/files/storage-how-to-create-file-share.md) und [Speicherkontoübersicht](../../storage/common/storage-account-overview.md).

- Firewallregeln für Speicherkonten werden noch nicht unterstützt. Weitere Informationen finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](../../storage/common/storage-network-security.md).
- Der Bereitstellungsprinzipal muss über Berechtigungen zum Verwalten des Speicherkontos verfügen. Dies umfasst das Lesen, Erstellen und Löschen von Dateifreigaben.

Um ein vorhandenes Speicherkonto anzugeben, fügen Sie dem Eigenschaftselement von `Microsoft.Resources/deploymentScripts` den folgenden JSON-Code hinzu:

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: Geben Sie den Namen des Speicherkontos an.
- `storageAccountKey`: Geben Sie einen der Speicherkontoschlüssel an. Sie können den Schlüssel mit der [listKeys()](./template-functions-resource.md#listkeys)-Funktion abrufen. Beispiel:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Ein vollständiges Beispiel für die Definition von `Microsoft.Resources/deploymentScripts` finden Sie unter [Beispielvorlagen](#sample-templates).

Wenn ein vorhandenes Speicherkonto zum Einsatz kommt, erstellt der Skriptdienst eine Dateifreigabe mit einem eindeutigen Namen. Informationen dazu, wie der Skriptdienst die Dateifreigabe bereinigt, finden Sie unter [Bereinigen von Ressourcen für Bereitstellungsskripts](#clean-up-deployment-script-resources).

## <a name="develop-deployment-scripts"></a>Entwickeln von Bereitstellungsskripts

### <a name="handle-non-terminating-errors"></a>Behandeln von Fehlern ohne Abbruch

Sie können steuern, wie PowerShell auf Fehler ohne Abbruch reagieren soll, indem Sie die Variable `$ErrorActionPreference` in Ihrem Bereitstellungsskript verwenden. Wenn die Variable in Ihrem Bereitstellungsskript nicht festgelegt ist, wird der Standardwert **Continue** (Fortsetzen) verwendet.

Tritt bei dem Skript ein Fehler auf, wird der Bereitstellungsstatus der Ressource ungeachtet der Einstellung von `$ErrorActionPreference` auf **Fehler** festgelegt.

### <a name="use-environment-variables"></a>Verwenden von Umgebungsvariablen

Das Bereitstellungsskript verwendet diese Umgebungsvariablen:

|Umgebungsvariable|Standardwert|System-reserviert|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|OnExpiration|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|J|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptinput|J|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|J|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell: userscript.ps1; Azure CLI: userscript.sh|J|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|J|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|J|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|scriptoutputs.json|J|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|executionresult.json|J|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/subscriptions/|N|

Weitere Informationen zur Verwendung von `AZ_SCRIPTS_OUTPUT_PATH` finden Sie unter [Verwenden der Ausgaben vom CLI-Skript](#work-with-outputs-from-cli-script).

### <a name="pass-secured-strings-to-deployment-script"></a>Übergeben von sicheren Zeichenfolgen an Bereitstellungsskripts

Das Festlegen von Umgebungsvariablen (EnvironmentVariable) in Ihren Containerinstanzen ermöglicht es Ihnen, eine dynamische Konfiguration der Anwendung oder des Skripts bereitzustellen, die bzw. das vom Container ausgeführt wird. Das Bereitstellungsskript verarbeitet nicht gesicherte und gesicherte Umgebungsvariablen auf dieselbe Weise wie Azure Container Instance. Weitere Informationen finden Sie unter [Festlegen von Umgebungsvariablen in Container Instances](../../container-instances/container-instances-environment-variables.md#secure-values). Ein Beispiel finden Sie unter [Beispielvorlagen](#sample-templates).

Die maximal zulässige Größe für Umgebungsvariablen beträgt 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Überwachen von Bereitstellungsskripts und Behandeln von Problemen

Der Skriptdienst erstellt für die Ausführung von Skripts im Hintergrund ein [Speicherkonto](../../storage/common/storage-account-overview.md) (es sei denn, Sie geben ein vorhandenes Speicherkonto an) und eine [Containerinstanz](../../container-instances/container-instances-overview.md). Wenn diese Ressourcen automatisch vom Skriptdienst erstellt werden, haben die Namen der Ressourcen das Suffix `azscripts`.

![Resource Manager-Vorlage: Namen von Bereitstellungsskriptressourcen](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Das Benutzerskript, die Ausführungsergebnisse und die stdout-Datei werden in den Dateifreigaben des Speicherkontos gespeichert. Ein Ordner namens `azscripts` ist vorhanden. In diesem Ordner sind zwei weitere Ordner für die Eingabe- und Ausgabedateien enthalten: `azscriptinput` und `azscriptoutput`.

Der Ausgabeordner enthält die Datei _executionresult.json_ und die Skriptausgabedatei. Sie können die Fehlermeldung der Skriptausführung in _executionresult.json_ anzeigen. Die Ausgabedatei wird nur erstellt, wenn das Skript erfolgreich ausgeführt wurde. Der Eingabeordner enthält eine PowerShell-Skriptdatei des Systems und die Bereitstellungsskriptdateien der Benutzer. Sie können die Skriptdatei für die Benutzerbereitstellung durch eine überarbeitete Version ersetzen und das Bereitstellungsskript erneut aus der Azure-Containerinstanz ausführen.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Nachdem Sie eine Bereitstellungsskriptressource bereitgestellt haben, wird sie im Azure-Portal unter der Ressourcengruppe aufgeführt. Der folgende Screenshot zeigt die **Übersichtsseite** einer Bereitstellungsskriptressource:

![Resource Manager-Vorlage: Übersicht über das Bereitstellungsskript im Portal](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Auf der Übersichtsseite werden einige wichtige Informationen der Ressource wie **Bereitstellungsstatus**, **Speicherkonto**, **Containerinstanz** und **Protokolle** angezeigt.

Über das Menü auf der linken Seite können Sie den Inhalt des Bereitstellungsskripts, die an das Skript übergebenen Argumente und die Ausgabe anzeigen. Außerdem können Sie eine Vorlage für das Bereitstellungsskript exportieren (einschließlich des Bereitstellungsskripts).

### <a name="use-powershell"></a>Verwenden von PowerShell

Mit Azure PowerShell können Sie Bereitstellungsskripts im Abonnement- oder Ressourcengruppenbereich verwalten:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): Dient zum Abrufen oder Auflisten von Bereitstellungsskripts.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): Dient zum Abrufen des Protokolls einer Bereitstellungsskriptausführung.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): Dient zum Entfernen eines Bereitstellungsskripts und der zugehörigen Ressourcen.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): Dient zum Speichern des Protokolls einer Bereitstellungsskriptausführung auf dem Datenträger.

Die Ausgabe von `Get-AzDeploymentScript` sieht in etwa wie folgt aus:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Mithilfe der Azure-Befehlszeilenschnittstelle

Über die Azure-Befehlszeilenschnittstelle können Sie Bereitstellungsskripts im Abonnement- oder Ressourcengruppenbereich verwalten:

- [az deployment-scripts delete](/cli/azure/deployment-scripts#az_deployment_scripts_delete): Dient zum Löschen eines Bereitstellungsskripts.
- [az deployment-scripts list](/cli/azure/deployment-scripts#az_deployment_scripts_list): Dient zum Auflisten aller Bereitstellungsskripts.
- [az deployment-scripts show](/cli/azure/deployment-scripts#az_deployment_scripts_show): Dient zum Abrufen eines Bereitstellungsskripts.
- [az deployment-scripts show-log](/cli/azure/deployment-scripts#az_deployment_scripts_show_log): Dient zum Anzeigen von Bereitstellungsskriptprotokollen.

Die Ausgabe des Befehls „list“ sieht in etwa wie folgt aus:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Verwenden der REST-API

Mithilfe der REST-API können Sie die Bereitstellungsinformationen der Bereitstellungsskriptressourcen auf Ressourcengruppenebene und Abonnementebene abrufen:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

Im folgenden Beispiel wird [ARMClient](https://github.com/projectkudu/ARMClient) verwendet:

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

Die Ausgabe sieht in etwa wie folgt aus:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

Die folgende REST-API gibt das Protokoll zurück:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Dies funktioniert nur, bevor die Bereitstellungsskriptressourcen gelöscht werden.

Wählen Sie im Portal die Option **Ausgeblendete Typen anzeigen** aus, um die deploymentScripts-Ressource anzuzeigen.

![Resource Manager-Vorlage: Bereitstellungsskript, „Ausgeblendete Typen anzeigen“, Portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Bereinigen von Bereitstellungsskriptressourcen

Für die Skriptausführung und Problembehandlung werden ein Speicherkonto und eine Containerinstanz benötigt. Sie haben die Möglichkeit, ein vorhandenes Speicherkonto anzugeben. Andernfalls wird ein Speicherkonto zusammen mit einer Containerinstanz vom Skriptdienst automatisch erstellt. Die beiden automatisch erstellten Ressourcen werden vom Skriptdienst gelöscht, sobald die Ausführung des Bereitstellungsskripts beendet ist. Die Ressourcen werden Ihnen in Rechnung gestellt, bis sie gelöscht werden. Preisinformationen finden Sie unter [Container Instances – Preise](https://azure.microsoft.com/pricing/details/container-instances/) und [Azure Storage – Preise](https://azure.microsoft.com/pricing/details/storage/).

Der Lebenszyklus dieser Ressourcen wird durch die folgenden Eigenschaften in der Vorlage gesteuert:

- `cleanupPreference`: Dies ist die Einstellung für die Bereinigung, wenn die Skriptausführung einen Beendigungszustand erreicht. Die unterstützten Werte sind:

  - **Always:** Die Ressourcen werden gelöscht, sobald die Skriptausführung beendet ist. Wenn ein vorhandenes Speicherkonto zum Einsatz kommt, löscht der Skriptdienst die im Speicherkonto erstellte Dateifreigabe. Da die Ressource `deploymentScripts` auch nach der Bereinigung der Ressourcen noch vorhanden sein kann, werden die Ergebnisse der Skriptausführung (beispielsweise stdout, Ausgaben und Rückgabewerte) durch den Skriptdienst gespeichert, bevor die Ressourcen gelöscht werden.
  - **OnSuccess:** Die Ressourcen werden automatisch gelöscht, wenn die Skriptausführung erfolgreich war. Wenn ein vorhandenes Speicherkonto verwendet wird, entfernt der Skriptdienst die Dateifreigabe nur bei erfolgreicher Skriptausführung. Sie können weiterhin auf die Ressourcen zugreifen, um Debuginformationen zu finden.
  - **OnExpiration:** Die automatisch erstellten Ressourcen werden erst nach Ablauf der Einstellung `retentionInterval` gelöscht. Wenn ein vorhandenes Speicherkonto zum Einsatz kommt, entfernt der Skriptdienst die Dateifreigabe und behält das Speicherkonto bei.

- `retentionInterval`: Geben Sie das Zeitintervall an, für das eine Skriptressource aufbewahrt wird und nach dem sie als abgelaufen gilt und gelöscht wird.

> [!NOTE]
> Es wird nicht empfohlen, das Speicherkonto und die Containerinstanz zu verwenden, die vom Skriptdienst für andere Zwecke generiert werden. Die beiden Ressourcen werden abhängig vom Lebenszyklus des Skripts möglicherweise entfernt.

Die Containerinstanz und das Speicherkonto werden gemäß der Option `cleanupPreference` gelöscht. Wenn das Skript jedoch fehlschlägt und `cleanupPreference` nicht auf **Always** (Immer) festgelegt ist, führt der Bereitstellungsprozess den Container automatisch eine Stunde lang aus. Sie können diese Stunde für die Problembehandlung des Skripts nutzen. Wenn Sie den Container nach erfolgreichen Bereitstellungen beibehalten möchten, fügen Sie dem Skript einen Energiesparmodusschritt hinzu. Fügen Sie z. B. [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) am Ende des Skripts hinzu. Wenn Sie den Energiesparmodusschritt nicht hinzufügen, wird der Container auf einen Terminalzustand festgelegt. Auf ihn kann dann nicht zugegriffen werden, auch wenn er noch nicht gelöscht wurde.

## <a name="run-script-more-than-once"></a>Mehrmaliges Ausführen des Skripts

Die Ausführung des Bereitstellungsskripts ist ein idempotenter Vorgang. Wenn keine der `deploymentScripts`-Ressourceneigenschaften (einschließlich des Inlineskripts) geändert werden, wird das Skript nicht ausgeführt, wenn Sie die Vorlage erneut bereitstellen. Der Bereitstellungsskriptdienst vergleicht die Ressourcennamen in der Vorlage mit den vorhandenen Ressourcen in derselben Ressourcengruppe. Wenn Sie dasselbe Bereitstellungsskript mehrmals ausführen möchten, haben Sie zwei Möglichkeiten:

- Wählen Sie den Namen Ihrer `deploymentScripts`-Ressource aus. Verwenden Sie z. B. die Vorlagenfunktion [utcNow](./template-functions-date.md#utcnow) als Ressourcennamen oder als Teil des Ressourcennamens. Wenn Sie den Ressourcennamen ändern, wird eine neue `deploymentScripts`-Ressource erstellt. Diese ist sinnvoll, um den Verlauf der Skriptausführung zu protokollieren.

    > [!NOTE]
    > Die `utcNow`-Funktion kann nur für den Standardwert eines Parameters verwendet werden.

- Geben Sie einen anderen Wert in der `forceUpdateTag`-Eigenschaft der Vorlage an. Verwenden Sie beispielsweise als Wert `utcNow`.

> [!NOTE]
> Schreiben Sie idempotente Bereitstellungsskripts. Dadurch wird sichergestellt, dass bei versehentlicher erneuter Ausführung keine Systemänderungen auftreten. Wenn das Bereitstellungsskript z. B. zum Erstellen einer Azure-Ressource verwendet wird, vergewissern Sie sich, dass die Ressource vor dem Erstellen nicht vorhanden ist, damit das Skript erfolgreich ausgeführt werden kann und Sie die Ressource nicht erneut erstellen.

## <a name="configure-development-environment"></a>Konfigurieren der Entwicklungsumgebung

Sie können ein vorkonfiguriertes Containerimage als Entwicklungsumgebung für Ihr Bereitstellungsskript verwenden. Weitere Informationen finden Sie unter [Konfigurieren der Entwicklungsumgebung für Bereitstellungsskripts in Vorlagen](./deployment-script-template-configure-dev.md).

Nachdem das Skript erfolgreich getestet wurde, können Sie es als Bereitstellungsskript in Ihren Vorlagen verwenden.

## <a name="deployment-script-error-codes"></a>Fehlercodes von Bereitstellungsskripts

| Fehlercode | BESCHREIBUNG |
|------------|-------------|
| DeploymentScriptInvalidOperation | Die Bereitstellungsskript-Ressourcendefinition in der Vorlage enthält ungültige Eigenschaftsnamen. |
| DeploymentScriptResourceConflict | Eine Bereitstellungsskriptressource, die sich in einem Nicht-Terminalzustand befindet und deren Ausführung 1 Stunde noch nicht überschritten hat, kann nicht gelöscht werden. Oder es kann nicht dasselbe Bereitstellungsskript mit demselben Ressourcenbezeichner (selbes Abonnement, selber Ressourcengruppenname und selber Ressourcenname), aber unterschiedlichem Skripttextinhalt gleichzeitig erneut ausgeführt werden. |
| DeploymentScriptOperationFailed | Interner Fehler beim Bereitstellungsskriptvorgang. Wenden Sie sich an den Microsoft-Support. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | Der Zugriffsschlüssel wurde nicht für das vorhandene Speicherkonto angegeben.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Eine vom Bereitstellungsskriptdienst erstellte Containergruppe wurde extern geändert, und es wurden ungültige Container hinzugefügt. |
| DeploymentScriptContainerGroupInNonterminalState | Mindestens zwei Bereitstellungsskriptressourcen verwenden denselben Azure-Containerinstanznamen in derselben Ressourcengruppe, und einer von ihnen hat die Ausführung noch nicht abgeschlossen. |
| DeploymentScriptStorageAccountInvalidKind | Das vorhandene Speicherkonto des Typs „BlobBlobStorage“ oder „BlobStorage“ unterstützt keine Dateifreigaben und kann nicht verwendet werden. |
| DeploymentScriptStorageAccountInvalidKindAndSku | Das vorhandene Speicherkonto unterstützt keine Dateifreigaben. Eine Liste der unterstützten Speicherkontotypen finden Sie unter [Verwenden eines vorhandenen Speicherkontos](#use-existing-storage-account). |
| DeploymentScriptStorageAccountNotFound | Das Speicherkonto ist nicht vorhanden oder wurde von einem externen Prozess oder Tool gelöscht. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | Das angegebene Speicherkonto besitzt einen Dienstendpunkt. Ein Speicherkonto mit einem Dienstendpunkt wird nicht unterstützt. |
| DeploymentScriptStorageAccountInvalidAccessKey | Für das vorhandene Speicherkonto wurde ein ungültiger Zugriffsschlüssel angegeben. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Ungültiges Speicherkonto-Schlüsselformat. Weitere Informationen finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../../storage/common/storage-account-keys-manage.md). |
| DeploymentScriptExceededMaxAllowedTime | Die Dauer der Ausführung des Bereitstellungsskripts hat den in der Ressourcendefinition des Bereitstellungsskripts angegebenen Timeoutwert überschritten. |
| DeploymentScriptInvalidOutputs | Die Bereitstellungsskriptausgabe ist kein gültiges JSON-Objekt. |
| DeploymentScriptContainerInstancesServiceLoginFailure | Die vom Benutzer zugewiesene verwaltete Identität konnte sich nach 10 Versuchen in einem Intervall von jeweils 1 Minute nicht anmelden. |
| DeploymentScriptContainerGroupNotFound | Eine vom Bereitstellungsskriptdienst erstellte Containergruppe wurde von einem externen Tool oder Prozess gelöscht. |
| DeploymentScriptDownloadFailure | Fehler beim Herunterladen eines unterstützenden Skripts. Siehe [Verwenden unterstützender Skripts](#use-supporting-scripts).|
| DeploymentScriptError | Das Benutzerskript hat einen Fehler ausgelöst. |
| DeploymentScriptBootstrapScriptExecutionFailed | Das Bootstrapskript hat einen Fehler ausgelöst. Das Bootstrapskript ist das Systemskript, das die Ausführung des Bereitstellungsskripts orchestriert. |
| DeploymentScriptExecutionFailed | Unbekannter Fehler während der Ausführung des Bereitstellungsskripts. |
| DeploymentScriptContainerInstancesServiceUnavailable | Beim Erstellen der Azure-Containerinstanz (ACI) hat ACI den Fehler „Dienst nicht verfügbar“ ausgelöst. |
| DeploymentScriptContainerGroupInNonterminalState | Beim Erstellen der Azure-Containerinstanz (ACI) verwendet ein anderes Bereitstellungsskript denselben ACI-Namen im selben Bereich (selbes/r Abonnement, Ressourcengruppenname und Ressourcenname). |
| DeploymentScriptContainerGroupNameInvalid | Der angegebene Name der Azure-Containerinstanz (ACI) entspricht nicht den ACI-Anforderungen. Siehe [Behandeln von häufigen Problemen in Azure Container Instances](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Bereitstellungsskripts verwenden. Ein Tutorial zu Bereitstellungsskripts finden Sie unter:

> [!div class="nextstepaction"]
> [Tutorial: Verwenden von Bereitstellungsskripts in Azure Resource Manager-Vorlagen](./template-tutorial-deployment-script.md).

> [!div class="nextstepaction"]
> [Learn-Modul: Erweitern von ARM-Vorlagen mithilfe von Bereitstellungsskripts](/learn/modules/extend-resource-manager-template-deployment-scripts/)
