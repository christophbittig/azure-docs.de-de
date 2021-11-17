---
title: Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration
description: In diesem Artikel wird das Kompilieren von DSC-Konfigurationen (Desired State Configuration, Konfiguration des gewünschten Zustands) für Azure Automation beschrieben.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 636bb2c91330cb147c367ca7143f988986076d73
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493531"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration

Sie können DSC-Konfigurationen (Desired State Configuration, Konfiguration des gewünschten Zustands) folgendermaßen mit Azure Automation State Configuration kompilieren:

- Kompilierungsdienst für Azure State Configuration
  - Einfache Methode mit interaktiver Benutzeroberfläche
   - Einfaches Nachverfolgen des Auftragsstatus

- Windows PowerShell
  - Aufruf in Windows PowerShell auf der lokalen Arbeitsstation oder im Builddienst
  - Integration in Pipeline für Entwicklungstests
  - Bereitstellen komplexer Parameterwerte
  - Arbeiten mit Knoten- und Nicht-Knotendaten nach Maß
  - Beträchtliche Leistungsverbesserung

Sie können auch Azure Resource Manager-Vorlagen mit der Azure-Erweiterung zum Konfigurieren des gewünschten Zustands (Desired State Configuration, DSC) verwenden, um Konfigurationen auf Ihre Azure-VMs zu pushen. Die Azure DSC-Erweiterung nutzt das VM-Agent-Framework von Azure zur Übermittlung und Inkraftsetzung von DSC-Konfigurationen auf virtuellen Azure-Computern sowie zur Erstellung entsprechender Berichte. Weitere Informationen zur Kompilierung mit Azure Resource Manager-Vorlagen finden Sie in [Desired State Configuration-Erweiterung mit Azure Resource Manager-Vorlagen](../virtual-machines/extensions/dsc-template.md#details). 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Kompilieren einer DSC-Konfiguration in Azure State Configuration

### <a name="portal"></a>Portal

1. Klicken Sie im Automation-Konto auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf die Registerkarte **Konfigurationen** und dann auf den Namen der Konfiguration, die kompiliert werden soll.
1. Klicken Sie auf **Kompilieren**.
1. Wenn die Konfiguration keine Parameter enthält, werden Sie gefragt, ob Sie sie kompilieren möchten. Wenn die Konfiguration Parameter enthält, wird das Blatt **Konfiguration kompilieren** geöffnet, auf dem Sie Parameterwerte angeben können.
1. Die Seite „Kompilierungsauftrag“ wird geöffnet, auf der Sie den Status des Kompilierungsauftrags nachverfolgen können. Sie können auf dieser Seite auch die Knotenkonfigurationen (MOF-Konfigurationsdokumente) nachverfolgen, die der Auftrag auf dem Azure Automation State Configuration-Pullserver platziert.

### <a name="azure-powershell"></a>Azure PowerShell

Sie können [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) verwenden, um die Kompilierung mit Windows PowerShell zu beginnen. Der folgende Beispielcode startet die Kompilierung einer DSC-Konfiguration namens **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` gibt ein Kompilierungsauftragsobjekt zurück, das zum Nachverfolgen des Auftragsstatus verwendet werden kann. Anschließend können Sie dieses Kompilierungsauftragsobjekt mit [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) verwenden, um den Status des Kompilierungsauftrags zu ermitteln, und mit [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration), um seine Streams (Ausgabe) anzuzeigen. Das folgende Beispiel startet die Kompilierung der SampleConfig-Konfiguration, wartet, bis die Kompilierung abgeschlossen ist, und zeigt dann die Datenströme an.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Deklarieren grundlegender Parameter

Die Parameterdeklaration in DSC-Konfigurationen, einschließlich der Parametertypen und -eigenschaften, funktioniert genauso wie in Azure Automation-Runbooks. Informationen zu Runbookparametern finden Sie unter [Starten eines Runbooks in Azure Automation](./start-runbooks.md) .

Im folgenden Beispiel werden die Parameter `FeatureName` und `IsPresent` verwendet, um die Werte der Eigenschaften in der während der Kompilierung generierten Knotenkonfiguration **ParametersExample.sample** zu ermitteln.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Sie können DSC-Konfigurationen kompilieren, die grundlegende Parameter im Azure Automation State Configuration-Portal oder in Azure PowerShell verwenden.

#### <a name="portal"></a>Portal

Im Portal können Sie Parameterwerte eingeben, nachdem Sie auf **Kompilieren** geklickt haben.

![Konfigurationsparameter für das Kompilieren](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell erfordert Parameter in einer [Hashtabelle](/powershell/module/microsoft.powershell.core/about/about_hash_tables), in der der Schlüssel dem Parameternamen entspricht und der Wert gleich dem Parameterwert ist.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Informationen zum Übergeben von `PSCredential`-Objekten als Parameter finden Sie unter [Anmeldeinformationsassets](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilieren von Konfigurationen, die zusammengesetzte Ressourcen enthalten, in Azure Automation

Das Feature **Zusammengesetzte Ressourcen** ermöglicht Ihnen, die DSC-Konfigurationen als geschachtelte Ressourcen innerhalb einer Konfiguration zu verwenden. Dieses Feature ermöglicht die Anwendung mehrerer Konfigurationen auf eine einzelne Ressource. Unter [Zusammengesetzte Ressourcen: Verwenden einer DSC-Konfiguration als Ressource](/powershell/scripting/dsc/resources/authoringresourcecomposite) erfahren Sie mehr über zusammengesetzte Ressourcen.

> [!NOTE]
> Damit Konfigurationen mit zusammengesetzten Ressourcen ordnungsgemäß kompiliert werden, müssen Sie zunächst alle DSC-Ressourcen, die für die zusammengesetzten Elemente erforderlich sind, in Azure Automation importieren. Das Hinzufügen einer zusammengesetzten DSC-Ressource unterscheidet sich nicht vom Hinzufügen eines PowerShell-Moduls zu Azure Automation. Das Verfahren ist in [Verwenden von Modulen in Azure Automation](./shared-resources/modules.md) dokumentiert.

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Verwalten von ConfigurationData beim Kompilieren von Konfigurationen in Azure Automation

Bei `ConfigurationData` handelt es sich um einen integrierten DSC-Parameter, mit dem Sie bei Verwendung von PowerShell DSC die Konfiguration der Struktur jeglicher umgebungsspezifischen Konfiguration trennen können. Weitere Informationen finden Sie unter [Separating „What“ from „Where“ in PowerShell DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/) (Trennen des „Was“ vom „Wo“ in PowerShell DSC).

> [!NOTE]
> Sie können beim Kompilieren in Azure Automation State Configuration `ConfigurationData` mit Azure PowerShell verwenden, jedoch nicht im Azure-Portal.

In der folgenden DSC-Beispielkonfiguration wird `ConfigurationData` über die Schlüsselwörter `$ConfigurationData` und `$AllNodes` verwendet. Für dieses Beispiel benötigen Sie außerdem das Modul [xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/).

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Sie können die vorherige DSC-Konfiguration mit Windows PowerShell kompilieren. Das folgende Skript fügt dem Pulldienst von Azure Automation State Configuration die beiden Knotenkonfigurationen hinzu: **ConfigurationDataSample.MyVM1** und **ConfigurationDataSample.MyVM3**

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Arbeiten mit Ressourcen in Azure Automation während der Kompilierung

Objektverweise in Azure Automation State Configuration und Runbooks sind gleich. Weitere Informationen finden Sie unter

- [Zertifikate](./shared-resources/certificates.md)
- [Verbindungen](automation-connections.md)
- [Anmeldeinformationen](./shared-resources/credentials.md)
- [Variablen](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Anmeldeinformationen

DSC-Konfigurationen in Azure Automation können mithilfe des Cmdlets `Get-AutomationPSCredential` auf Automation-Anmeldeinformationsobjekte verweisen. Wenn eine Konfiguration einen Parameter aufweist, der ein `PSCredential`-Objekt angibt, können Sie `Get-AutomationPSCredential` verwenden, indem Sie den Zeichenfolgennamen eines Azure Automation-Anmeldeinformationsobjekts an das Cmdlet übergeben, um die Anmeldeinformationen abzurufen. Verwenden Sie dieses Objekt anschließend für den Parameter, der das `PSCredential`-Objekt erfordert. Im Hintergrund wird das Azure Automation-Anmeldeinformationsobjekt mit diesem Namen abgerufen und an die Konfiguration übergeben. Das nachstehende Beispiel stellt die praktische Anwendung dieses Szenarios dar.

Um die Sicherheit von Anmeldeinformationen in Knotenkonfigurationen (MOF-Konfigurationsdokumente) zu gewährleisten, müssen diese Informationen in der MOF-Datei der Knotenkonfiguration verschlüsselt werden. Derzeit müssen Sie PowerShell DSC die Berechtigung zum Ausgeben von Anmeldeinformationen im Klartext während der MOF-Generierung der Knotenkonfiguration erteilen. PowerShell DSC ist nicht bekannt, dass Azure Automation die gesamte MOF-Datei nach der Generierung über einen Kompilierungsauftrag verschlüsselt.

Sie können PowerShell DSC mitteilen, dass es in Ordnung ist, wenn Anmeldeinformationen in den mithilfe von Konfigurationsdaten generierten MOF-Dateien der Knotenkonfiguration im Klartext ausgegeben werden. Übergeben Sie `PSDscAllowPlainTextPassword = $true` über `ConfigurationData` für jeden Knotenblocknamen, der in der DSC-Konfiguration aufgeführt ist und Anmeldeinformationen verwendet.

Das folgende Beispiel zeigt eine DSC-Konfiguration, die ein Automation-Anmeldeinformationsobjekt verwendet.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Sie können die vorherige DSC-Konfiguration über PowerShell kompilieren. Der folgende PowerShell-Code fügt dem Azure Automation State Configuration-Pullserver zwei Knotenkonfigurationen hinzu: **CredentialSample.MyVM1** und **CredentialSample.MyVM2** hinzu.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Wenn die Kompilierung abgeschlossen ist, erhalten Sie möglicherweise die Fehlermeldung `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.`, die Sie ignorieren können.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Kompilieren Ihrer DSC-Konfiguration in Windows PowerShell

Der Prozess zum Kompilieren von DSC-Konfigurationen in Windows PowerShell ist im Artikel [Schreiben, Kompilieren und Anwenden einer Konfiguration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration) zu PowerShell DSC beschrieben.
Sie können diesen Prozess auf der Arbeitsstation eines Entwicklers oder in einem Builddienst wie [Azure DevOps](https://dev.azure.com) ausführen. Anschließend können Sie die MOF-Dateien, die beim Kompilieren der Konfiguration erstellt werden, in den Azure State Configuration-Dienst importieren.

Beim Kompilieren in Windows PowerShell steht auch die Option zum Signieren von Konfigurationsinhalten zur Verfügung. Der DSC-Agent überprüft eine signierte Knotenkonfiguration lokal auf einem verwalteten Knoten. Durch die Überprüfung wird sichergestellt, dass die auf den Knoten angewandte Konfiguration aus einer autorisierten Quelle stammt.

Sie können auch Knotenkonfigurationen (MOF-Dateien) importieren, die außerhalb von Azure kompiliert wurden. Der Import schließt das Kompilieren auf der Arbeitsstation eines Entwicklers oder in einem Dienst wie [Azure DevOps](https://dev.azure.com) ein. Dieser Ansatz bietet mehrere Vorteile, z. B. hinsichtlich Leistung und Zuverlässigkeit.

> [!NOTE]
> Knotenkonfigurationsdateien dürfen nicht größer als 1 MB sein, damit sie in Azure Automation importiert werden können.

Weitere Informationen zum Signieren von Knotenkonfigurationen finden Sie unter [Verbesserungen in WMF 5.1 – Signieren von Konfigurationen und Modulen](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importieren von Knotenkonfigurationen im Azure-Portal

1. Klicken Sie in Ihrem Automation-Konto unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite „Zustandskonfiguration (DSC)“ auf die Registerkarte **Konfigurationen** und dann auf **Hinzufügen**.
1. Klicken Sie auf der Seite „Importieren“ auf das Ordnersymbol neben dem Feld **Knotenkonfigurationsdatei**, um eine MOF-Knotenkonfigurationsdatei auf dem lokalen Computer zu suchen.

   ![Suchen einer lokalen Datei](./media/automation-dsc-compile/import-browse.png)

1. Geben Sie im Feld **Konfigurationsname** einen Namen ein. Dieser Name muss mit dem Namen der Konfiguration übereinstimmen, aus der die Knotenkonfiguration kompiliert wurde.
1. Klicken Sie auf **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importieren einer Knotenkonfiguration mit Azure PowerShell

Sie können das Cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) verwenden, um eine Knotenkonfiguration in Ihr Automation-Konto zu importieren.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Wie Sie DSC-Konfigurationen kompilieren und sie anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration](automation-dsc-compile.md).
- Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Ein Anwendungsbeispiel für State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Einrichten von Continuous Deployment mit Chocolatey](automation-dsc-cd-chocolatey.md).
