---
title: Resource Manager-Vorlagen-Testtoolkit
description: Erfahren Sie, wie Sie das ARM-Vorlagen-Testtoolkit (Azure Resource Manager) für Ihre Vorlage ausführen. Mit dem Toolkit können Sie festzustellen, ob Sie die empfohlenen Vorgehensweisen implementiert haben.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 5c53ede7df0fd8ba24ef82e7de5a793a4e55f204
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393275"
---
# <a name="use-arm-template-test-toolkit"></a>Verwenden des Resource Manager-Vorlagen-Testtoolkits

Das [ARM-Vorlagen-Testtoolkit](https://aka.ms/arm-ttk) (Azure Resource Manager) überprüft, ob Ihre Vorlage die empfohlenen Vorgehensweisen umsetzt. Wenn Ihre Vorlage nicht mit den empfohlenen Vorgehensweisen kompatibel ist, wird eine Liste mit Warnungen ausgegeben, die vorgeschlagene Änderungen enthält. Mit dem Testtoolkit erfahren Sie, wie Sie häufige Probleme bei der Vorlagenentwicklung vermeiden. In diesem Artikel wird beschrieben, wie Sie das Testtoolkit ausführen und Tests hinzufügen oder entfernen. Weitere Informationen zur Ausführungsweise von Tests oder eines bestimmten Tests finden Sie unter [Testparameter](#test-parameters).

Das Toolkit ist ein Satz von PowerShell-Skripts, die über einen Befehl in PowerShell oder in der Befehlszeilenschnittstelle ausgeführt werden können. Diese Tests werden empfohlen, sind aber nicht erforderlich. Sie können entscheiden, welche Tests für Ihre Ziele relevant sind, und anpassen, welche Tests ausgeführt werden.

Das Toolkit enthält vier Testsätze:

- [Testfälle für ARM-Vorlagen](template-test-cases.md)
- [Testfälle für Parameterdateien](parameter-file-test-cases.md)
- [Testfälle für „createUiDefinition.json“](createUiDefinition-test-cases.md)
- [Testfälle für alle Dateien](all-files-test-cases.md)

## <a name="install-on-windows"></a>Installieren unter Windows

1. Wenn Sie noch nicht über PowerShell verfügen, [installieren Sie PowerShell unter Windows](/powershell/scripting/install/installing-powershell-core-on-windows).

1. [Laden Sie die neueste ZIP-Datei](https://aka.ms/arm-ttk-latest) für das Testtoolkit herunter, und extrahieren Sie sie.

1. Starten Sie PowerShell.

1. Navigieren Sie zu dem Ordner, in dem Sie das Testtoolkit extrahiert haben. Navigieren Sie in diesem Ordner zum Ordner _arm-ttk_.

1. Wenn die [Ausführungsrichtlinie](/powershell/module/microsoft.powershell.core/about/about_execution_policies) Skripts aus dem Internet blockiert, müssen Sie die Blockierung der Skriptdateien deaktivieren. Vergewissern Sie sich, dass Sie sich im Ordner _arm-ttk_ befinden.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importieren Sie das Modul.

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. Verwenden Sie den folgenden Befehl, um die Tests auszuführen:

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Installation unter Linux

1. Wenn Sie noch nicht über PowerShell verfügen, [installieren Sie PowerShell unter Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. [Laden Sie die neueste ZIP-Datei](https://aka.ms/arm-ttk-latest) für das Testtoolkit herunter, und extrahieren Sie sie.

1. Starten Sie PowerShell.

   ```bash
   pwsh
   ```

1. Navigieren Sie zu dem Ordner, in dem Sie das Testtoolkit extrahiert haben. Navigieren Sie in diesem Ordner zum Ordner _arm-ttk_.

1. Wenn die [Ausführungsrichtlinie](/powershell/module/microsoft.powershell.core/about/about_execution_policies) Skripts aus dem Internet blockiert, müssen Sie die Blockierung der Skriptdateien deaktivieren. Vergewissern Sie sich, dass Sie sich im Ordner _arm-ttk_ befinden.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importieren Sie das Modul.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Verwenden Sie den folgenden Befehl, um die Tests auszuführen:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>Installieren unter macOS

1. Wenn Sie noch nicht über PowerShell verfügen, [installieren Sie PowerShell unter macOS](/powershell/scripting/install/installing-powershell-core-on-macos).

1. Installieren Sie `coreutils`:

   ```bash
   brew install coreutils
   ```

1. [Laden Sie die neueste ZIP-Datei](https://aka.ms/arm-ttk-latest) für das Testtoolkit herunter, und extrahieren Sie sie.

1. Starten Sie PowerShell.

   ```bash
   pwsh
   ```

1. Navigieren Sie zu dem Ordner, in dem Sie das Testtoolkit extrahiert haben. Navigieren Sie in diesem Ordner zum Ordner _arm-ttk_.

1. Wenn die [Ausführungsrichtlinie](/powershell/module/microsoft.powershell.core/about/about_execution_policies) Skripts aus dem Internet blockiert, müssen Sie die Blockierung der Skriptdateien deaktivieren. Vergewissern Sie sich, dass Sie sich im Ordner _arm-ttk_ befinden.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importieren Sie das Modul.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Verwenden Sie den folgenden Befehl, um die Tests auszuführen:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>Ergebnisformat

Bestandene Tests werden in **Grün** mit vorangestelltem `[+]` angezeigt.

Fehlerhafte Tests werden in **Rot** mit vorangestelltem `[-]` angezeigt.

Tests mit einer Warnung werden in **Gelb** und mit vorangestelltem `[?]` angezeigt.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="Anzeigen von Testergebnissen.":::

Die Testergebnisse sind:

```powershell
deploymentTemplate
[+] adminUsername Should Not Be A Literal (6 ms)
[+] apiVersions Should Be Recent In Reference Functions (9 ms)
[-] apiVersions Should Be Recent (6 ms)
    Api versions must be the latest or under 2 years old (730 days) - API version 2019-06-01 of
    Microsoft.Storage/storageAccounts is 760 days old
    Valid Api Versions:
    2021-04-01
    2021-02-01
    2021-01-01
    2020-08-01-preview

[+] artifacts parameter (4 ms)
[+] CommandToExecute Must Use ProtectedSettings For Secrets (9 ms)
[+] DependsOn Best Practices (5 ms)
[+] Deployment Resources Must Not Be Debug (6 ms)
[+] DeploymentTemplate Must Not Contain Hardcoded Uri (4 ms)
[?] DeploymentTemplate Schema Is Correct (6 ms)
    Template is using schema version '2015-01-01' which has been deprecated and is no longer
    maintained.
```

## <a name="test-parameters"></a>Testparameter

Wenn Sie den `-TemplatePath`-Parameter angeben, sucht das Toolkit in diesem Ordner nach einer Vorlage mit dem Namen _azuredeploy.json_ oder _maintemplate.json_. Es testet zunächst diese Vorlage und anschließend alle anderen Vorlagen im Ordner und seinen Unterordnern. Die anderen Vorlagen werden als verknüpfte Vorlagen getestet. Wenn Ihr Pfad eine Datei mit dem Namen [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md) enthält, werden Tests ausgeführt, die für die Benutzeroberflächendefinition relevant sind. Tests werden auch für Parameterdateien und alle JSON-Dateien im Ordner ausgeführt.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Fügen Sie den Parameter `-File` hinzu, um eine Datei in diesem Ordner zu testen. Der Ordner muss jedoch weiterhin über eine Hauptvorlage mit dem Namen _azuredeploy.json_ oder _maintemplate.json_ verfügen.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Standardmäßig werden alle Tests ausgeführt. Verwenden Sie den Parameter `-Test`, um einzelne Tests anzugeben, die ausgeführt werden sollen, und geben Sie den Testnamen an. Die Testnamen finden Sie unter [ARM-Vorlagen](template-test-cases.md), [Parameterdateien](parameter-file-test-cases.md), [createUiDefinition.js](createUiDefinition-test-cases.md) und [alle Dateien](all-files-test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Anpassen der Tests

Sie können die Standardtests anpassen oder eigene Tests erstellen. Wenn Sie einen Test dauerhaft entfernen möchten, löschen Sie die Datei _.test.ps1_ aus dem Ordner.

Das Toolkit verfügt über vier Ordner, die die Standardtests enthalten, die für bestimmte Dateitypen ausgeführt werden:

- ARM-Vorlagen: _\arm-ttk\testcases\deploymentTemplate_
- Parameterdateien: _\arm-ttk\testcases\deploymentParameters_
- [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md): _\arm-ttk\testcases\CreateUIDefinition_
- Alle Dateien: _\arm-ttk\testcases\AllFiles_

### <a name="add-a-custom-test"></a>Hinzufügen eines benutzerdefinierten Tests

Wenn Sie einen eigenen Test hinzufügen möchten, erstellen Sie eine Datei mit der Namenskonvention: _Your-Custom-Test-Name.test.ps1_.

Der Test kann die Vorlage als Objektparameter oder Zeichenfolgenparameter erhalten. In der Regel können Sie nur das eine oder das andere, nicht aber beides verwenden.

Verwenden Sie Objektparameter, wenn Sie die Eigenschaften eines Abschnitts der Vorlage durchgehen müssen. Ein Test, der den Objektparameter verwendet, weist das folgende Format auf:

```powershell
param(
  [Parameter(Mandatory=$true,Position=0)]
  [PSObject]
  $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

Das Vorlagenobjekt weist die folgenden Eigenschaften auf:

- `$schema`
- `contentVersion`
- `parameters`
- `variables`
- `resources`
- `outputs`

Sie können beispielsweise eine Auflistung von Parametern mithilfe von `$TemplateObject.parameters` abrufen.

Verwenden Sie Zeichenfolgenparameter, wenn Sie einen Zeichenfolgenvorgang für die gesamte Vorlage ausführen müssen. Ein Test, der den Zeichenfolgenparameter verwendet, weist das folgende Format auf:

```powershell
param(
  [Parameter(Mandatory)]
  [string]
  $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Sie können beispielsweise einen regulären Ausdruck des Zeichenfolgenparameters ausführen, um festzustellen, ob eine bestimmte Syntax verwendet wird.

Wenn Sie mehr über das Implementieren des Tests erfahren möchten, sehen Sie sich die anderen Tests in diesem Ordner an.

## <a name="integrate-with-azure-pipelines"></a>Integration in Azure Pipelines

Sie können das Testtoolkit zu Ihrer Azure Pipeline hinzufügen. Mit einer Pipeline können Sie den Test bei jeder Aktualisierung der Vorlage oder als Teil des Bereitstellungsprozesses ausführen.

Die einfachste Möglichkeit zum Hinzufügen des Testtoolkits zu Ihrer Pipeline besteht in Erweiterungen von Drittanbietern. Hierfür sind die folgenden beiden Erweiterungen verfügbar:

- [Run ARM TTK Tests](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
- [ARM Template Tester](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

Sie können auch eigene Aufgaben implementieren. Das folgende Beispiel zeigt, wie Sie das Testtoolkit herunterladen.

```json
{
  "environment": {},
  "enabled": true,
  "continueOnError": false,
  "alwaysRun": false,
  "displayName": "Download TTK",
  "timeoutInMinutes": 0,
  "condition": "succeeded()",
  "task": {
    "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
    "versionSpec": "2.*",
    "definitionType": "task"
  },
  "inputs": {
    "targetType": "inline",
    "filePath": "",
    "arguments": "",
    "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
    "errorActionPreference": "stop",
    "failOnStderr": "false",
    "ignoreLASTEXITCODE": "false",
    "pwsh": "true",
    "workingDirectory": ""
  }
}
```

Im nächsten Beispiel sehen Sie, wie die Tests ausgeführt werden.

```json
{
  "environment": {},
  "enabled": true,
  "continueOnError": true,
  "alwaysRun": false,
  "displayName": "Run Best Practices Tests",
  "timeoutInMinutes": 0,
  "condition": "succeeded()",
  "task": {
    "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
    "versionSpec": "2.*",
    "definitionType": "task"
  },
  "inputs": {
    "targetType": "inline",
    "filePath": "",
    "arguments": "",
    "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
    "errorActionPreference": "continue",
    "failOnStderr": "true",
    "ignoreLASTEXITCODE": "false",
    "pwsh": "true",
    "workingDirectory": ""
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den Vorlagentests finden Sie unter [Testfälle für ARM-Vorlagen](template-test-cases.md).
- Informationen zum Testen von Parameterdateien finden Sie unter [Testfälle für Parameterdateien](parameters.md).
- Unter [Testfälle für „createUiDefinition.json“](createUiDefinition-test-cases.md) finden Sie createUiDefinition-Tests.
- Informationen zu Tests für alle Dateien finden Sie unter [Testfälle für alle Dateien](all-files-test-cases.md).
- Ein Microsoft Learn-Modul, das die Verwendung des Testtoolkits behandelt, finden Sie unter [Überprüfen von Azure-Ressourcen mit dem Testtoolkit für ARM-Vorlagen](/learn/modules/arm-template-test/).
