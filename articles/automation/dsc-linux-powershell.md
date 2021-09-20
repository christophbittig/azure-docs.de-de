---
title: Anwenden von Azure Automation State Configuration für Linux mithilfe von PowerShell
description: In diesem Artikel erfahren Sie, wie Sie für einen virtuellen Linux-Computer den gewünschten Zustand mithilfe von Azure Automation State Configuration mit PowerShell konfigurieren.
ms.topic: conceptual
services: automation
ms.subservice: dsc
ms.date: 08/31/2021
ms.openlocfilehash: e4352c5c2cca0391e4e795b537af16c28ded6f6e
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123354462"
---
# <a name="configure-linux-desired-state-with-azure-automation-state-configuration-using-powershell"></a>Konfigurieren des gewünschten Zustands für Linux-Computer mit Azure Automation State Configuration mithilfe von PowerShell

In diesem Tutorial überprüfen Sie mithilfe von Azure Automation State Configuration mit PowerShell, ob ein virtueller Azure Linux-Computer mit einem gewünschten Zustand kompatibel ist. Bei dem gewünschten Zustand handelt es sich um die Frage, ob der Dienst „apache2“ auf dem Knoten vorhanden ist.
Mit Azure Automation State Configuration können Sie Konfigurationen für Ihre Computer festlegen und sicherstellen, dass sich diese Computer im Zeitverlauf in einem angegebenen Zustand befinden. Weitere Informationen zu State Configuration finden Sie unter [Übersicht über Azure Automation State Configuration](./automation-dsc-overview.md).

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> - Integrieren einer Azure Linux-VM in die Verwaltung mit Azure Automation DSC
> - Zusammenstellen einer Konfiguration
> - Installieren des Automation-Moduls von PowerShell
> - Importieren einer Konfiguration in Azure Automation
> - Kompilieren einer Konfiguration in eine Knotenkonfiguration
> - Zuweisen einer Knotenkonfiguration zu einem verwalteten Knoten
> - Ändern der Zuordnung der Knotenkonfiguration
> - Überprüfen des Konformitätsstatus eines verwalteten Knotens

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Automation-Konto. Weitere Informationen zu Automation-Konten finden Sie unter [Übersicht über die Automation-Kontoauthentifizierung](./automation-security-overview.md).
- Einen virtuellen Azure Resource Manager-Computer (VM), auf dem Ubuntu 18.04 LTS oder höher ausgeführt wird. Eine Anleitung zum Erstellen einer Azure Linux-VM finden Sie unter [Schnellstart: Erstellen eines virtuellen Linux-Computers in Azure mit PowerShell](../virtual-machines/windows/quick-create-powershell.md).
- Das [Az PowerShell-Modul](/powershell/azure/new-azureps-module-az), das auf dem Computer installiert ist, den Sie zum Schreiben, Kompilieren und Anwenden von State Configuration auf einem virtuellen Azure Linux-Zielcomputer verwenden. Stellen Sie sicher, dass Sie über die aktuelle Version verfügen. Führen Sie gegebenenfalls `Update-Module -Name Az` aus.

## <a name="create-a-configuration"></a>Erstellen einer Konfiguration

Wie Sie sehen, enthält der folgende Code die beiden [Knotenkonfigurationen](/powershell/scripting/dsc/configurations/configurations) `IsPresent` und `IsNotPresent`. Mit dieser Konfiguration wird in jedem Knotenblock die Ressource [nxPackage](/powershell/scripting/dsc/reference/resources/linux/lnxpackageresource) aufgerufen. Mit dieser Ressource wird das Vorhandensein des Pakets **apache2** verwaltet. Kopieren Sie in einem Text-Editor den folgenden Code in eine lokale Datei, und geben Sie ihr den Namen `LinuxConfig.ps1`:

```powershell
Configuration LinuxConfig
{
    Import-DscResource -ModuleName 'nx'

    Node IsPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Present'
            PackageManager    = 'Apt'
        }
    }

    Node IsNotPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Absent'
        }
    }
}
```

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich von Ihrem Computer aus mit dem PowerShell-Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

## <a name="initialize-variables"></a>Initialisieren der Variablen

Überarbeiten Sie für eine höhere Effizienz und eine geringere Fehlerquote beim Ausführen der Cmdlets ggf. den PowerShell-Code unten, und führen Sie ihn aus.

| Variable | Wert |
|---|---|
|$resourceGroup| Ersetzen Sie `yourResourceGroup` durch den Namen Ihrer Ressourcengruppe.|
|$automationAccount| Ersetzen Sie `yourAutomationAccount` durch den Namen Ihres Automation-Kontos.|
|$VM| Ersetzen Sie `yourVM` durch den Namen Ihrer Azure Linux-VM.|
|$configurationName| Belassen Sie den Namen `LinuxConfig`. Dies ist der Name der Konfiguration, die in diesem Tutorial verwendet wird.|
|$nodeConfigurationName0|Lassen Sie den Namen `LinuxConfig.IsNotPresent` unverändert. Dies ist der Name einer Knotenkonfiguration, die in diesem Tutorial verwendet wird.|
|$nodeConfigurationName1|Lassen Sie den Namen `LinuxConfig.IsPresent` unverändert. Dies ist der Name einer Knotenkonfiguration, die in diesem Tutorial verwendet wird.|
|$moduleName|Lassen Sie den Namen `nx` unverändert. Dies ist der Name des PowerShell-Moduls, das in diesem Tutorial für DSC verwendet wird.|
|$moduleVersion| Laden Sie die neueste Versionsnummer für `nx` aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/nx) herunter. In diesem Tutorial wird Version `1.0` verwendet.|

```powershell
$resourceGroup = "yourResourceGroup"
$automationAccount = "yourAutomationAccount"
$VM = "yourVM"
$configurationName = "LinuxConfig"
$nodeConfigurationName0 = "LinuxConfig.IsNotPresent"
$nodeConfigurationName1 = "LinuxConfig.IsPresent"
$moduleName = "nx"
$moduleVersion = "1.0"
```

## <a name="install-nx-module"></a>Installieren des Moduls „nx“

Azure Automation nutzt eine Reihe von PowerShell-Modulen, um Cmdlets in Runbooks und DSC-Ressourcen in DSC-Konfigurationen zu aktivieren. Das Modul **nx** enthält die DSC-Ressourcen für Linux. Installieren Sie das Modul **nx** mithilfe des Cmdlets [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule). Weitere Informationen zu Modulen finden Sie unter [Verwalten von Modulen in Azure Automation](./shared-resources/modules.md). Führen Sie den folgenden Befehl aus:

```powershell
New-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName `
    -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

Die Ausgabe sollte ähnlich wie die folgende aussehen:

   :::image type="content" source="media/dsc-linux-powershell/new-azautomationmodule-output.png" alt-text="Ausgabe des Befehls „New-AzAutomationModule“":::

Wenn Sie die Installation überprüfen möchten, führen Sie den folgenden Befehl aus:

```powershell
Get-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName 
```

## <a name="import-configuration-to-azure-automation"></a>Importieren der Konfiguration in Azure Automation

Rufen Sie das Cmdlet [Import-AzAutomationDscConfiguration](/powershell/module/az.automation/import-azautomationdscconfiguration) auf, um die Konfiguration in Ihr Automation-Konto hochzuladen. Ersetzen Sie den Wert für `-SourcePath` durch den tatsächlichen Pfad, und führen Sie dann den folgenden Befehl aus:

```powershell
Import-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -SourcePath "path\LinuxConfig.ps1" `
   -Published
```

Die Ausgabe sollte ähnlich wie die folgende aussehen:

   :::image type="content" source="media/dsc-linux-powershell/import-azautomationdscconfiguration-output.png" alt-text="Ausgabe des Befehls „Import-AzAutomationDscConfiguration“":::

Wenn Sie die Konfiguration Ihres Automation-Kontos anzeigen möchten, führen Sie den folgenden Befehl aus:

```powershell
Get-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $configurationName
```

## <a name="compile-configuration-in-azure-automation"></a>Kompilieren der Konfiguration in Azure Automation

Bevor Sie einen gewünschten Zustand auf einen Knoten anwenden können, muss die Konfiguration, die diesen Zustand definiert, in eine oder mehrere Knotenkonfigurationen kompiliert werden.  Rufen Sie das Cmdlet [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) auf, um die Konfiguration `LinuxConfig` in Azure Automation zu kompilieren. Weitere Informationen zur Kompilierung finden Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration](./automation-dsc-compile.md). Führen Sie den folgenden Befehl aus:

```powershell
Start-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

Die Ausgabe sollte ähnlich wie die folgende aussehen:

   :::image type="content" source="media/dsc-linux-powershell/start-azautomationdsccompilationjob-output.png" alt-text="Ausgabe des Befehls „Start-AzAutomationDscCompilationJob“":::

Wenn Sie den Kompilierungsauftrag für Ihr Automation-Konto anzeigen möchten, führen Sie den folgenden Befehl aus:

```powershell
Get-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

Warten Sie, bis der Kompilierungsauftrag abgeschlossen ist, bevor Sie mit dem nächsten Schritt fortfahren. Die Konfiguration muss in eine Knotenkonfiguration kompiliert werden, bevor sie einem Knoten zugewiesen werden kann. Führen Sie den folgenden Code aus, um den Zustand alle fünf Sekunden zu überprüfen:

```powershell
while ((Get-AzAutomationDscCompilationJob `
         -ResourceGroupName $resourceGroup `
         -AutomationAccountName $automationAccount `
         -ConfigurationName $configurationName).Status -ne "Completed") 
{
   Write-Output "Wait"
   Start-Sleep -Seconds 5
}
Write-Output "Compilation complete"
```

Wenn Sie die Metadaten der Knotenkonfiguration nach Abschluss des Kompilierungsauftrags anzeigen möchten, führen Sie den folgenden Befehl aus:

```powershell
Get-AzAutomationDscNodeConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount
```

## <a name="register-the-azure-linux-vm-for-an-automation-account"></a>Registrieren der Azure Linux-VM für ein Automation-Konto

Registrieren Sie den virtuellen Azure Linux-Computer als DSC-Knoten (Desired State Configuration) für das Azure Automation-Konto. Das Cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) unterstützt nur virtuelle Computer, auf denen das Windows-Betriebssystem ausgeführt wird. Der virtuelle Azure Linux-Computer muss zunächst für DSC konfiguriert werden. Eine ausführliche Anleitung finden Sie unter [Erste Schritte mit DSC für Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

1. Erstellen Sie mithilfe von PowerShell ein Python-Skript mit dem Registrierungsbefehl für die spätere Ausführung auf Ihrer Azure Linux-VM. Führen Sie hierzu den folgenden Code aus:

   ```powershell
    $primaryKey = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).PrimaryKey
    
    $URL = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).Endpoint
    
    Write-Output "sudo /opt/microsoft/dsc/Scripts/Register.py $primaryKey $URL"
   ```

   Mit diesen Befehlen werden der primäre Zugriffsschlüssel und die URL des Automation-Kontos abgerufen und im Registrierungsbefehl verkettet. Vergewissern Sie sich, dass Sie alle Wagenrückläufe aus der Ausgabe entfernt haben. Dieser Befehl wird in einem späteren Schritt verwendet.

1. Stellen Sie eine Verbindung mit Ihrer Azure Linux-VM her. Wenn Sie ein Kennwort verwendet haben, können Sie die Syntax unten verwenden. Wenn Sie ein öffentliches/privates Schlüsselpaar verwendet haben, finden Sie eine ausführliche Anleitung unter [SSH unter Linux](./../virtual-machines/linux/mac-create-ssh-keys.md). Mit den anderen Befehlen rufen Sie Informationen darüber ab, welche Pakete installiert werden können, und welche Updates für derzeit installierte Pakete verfügbar sind, und Sie installieren Python.

   ```cmd
   ssh user@IP

   sudo apt-get update
   sudo apt-get install -y python
   ```

1. Installieren Sie Open Management Infrastructure (OMI). Weitere Informationen zu OMI finden Sie unter [Open Management Infrastructure](https://github.com/Microsoft/omi). Informieren Sie sich über das neueste [Release](https://github.com/Microsoft/omi/releases). Überarbeiten Sie ggf. die unten angegebene Releaseversion, und führen Sie dann die Befehle in Ihrer SSH-Sitzung aus:

   ```bash
   wget https://github.com/microsoft/omi/releases/download/v1.6.8-0/omi-1.6.8-0.ssl_110.ulinux.x64.deb

   sudo dpkg -i ./omi-1.6.8-0.ssl_110.ulinux.x64.deb
   ```

1. Installieren Sie PowerShell Desired State Configuration für Linux. Weitere Informationen finden Sie unter [DSC für Linux](https://github.com/microsoft/PowerShell-DSC-for-Linux). Informieren Sie sich über das neueste [Release](https://github.com/microsoft/PowerShell-DSC-for-Linux/releases). Überarbeiten Sie ggf. die unten angegebene Releaseversion, und führen Sie dann die Befehle in Ihrer SSH-Sitzung aus:

   ```bash
   wget https://github.com/microsoft/PowerShell-DSC-for-Linux/releases/download/v1.2.1-0/dsc-1.2.1-0.ssl_110.x64.deb

   sudo dpkg -i ./dsc-1.2.1-0.ssl_110.x64.deb
   ```

1. Registrieren Sie nun den Knoten mithilfe des Python-Skripts `sudo /opt/microsoft/dsc/Scripts/Register.py <Primary Access Key> <URL>`, das Sie in Schritt 1 erstellt haben. Führen Sie die Befehle in Ihrer SSH-Sitzung aus. Die Ausgabe sollte ähnlich wie die folgende aussehen:

   ```output
   instance of SendConfigurationApply
   {
        ReturnValue=0
   }
   
   ```

1. Wenn Sie die Registrierung in PowerShell überprüfen möchten, führen Sie den folgenden Befehl aus:

   ```powershell
     Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM  
   ```

   Die Ausgabe sollte ähnlich wie die folgende aussehen:

      :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnode-output.png" alt-text="Ausgabe des Befehls „Get-AzAutomationDscNode“":::

## <a name="assign-a-node-configuration"></a>Zuweisen einer Knotenkonfiguration

Rufen Sie das Cmdlet [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) auf, um die Zuordnung der Knotenkonfiguration festzulegen. Führen Sie die folgenden Befehle aus:

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $VM

# Set node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName0 `
   -NodeId $node.Id `
   -Force
```

Die Ausgabe sollte ähnlich wie die folgende aussehen:

   :::image type="content" source="media/dsc-linux-powershell/set-azautomationdscnode-output.png" alt-text="Ausgabe des Befehls „Set-AzAutomationDscNode“":::

## <a name="modify-the-node-configuration-mapping"></a>Ändern der Zuordnung der Knotenkonfiguration

Rufen Sie das Cmdlet [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) auf, um die Zuordnung der Knotenkonfiguration zu ändern. Hier wird die aktuelle Zuordnung der Knotenkonfiguration von `LinuxConfig.IsNotPresent` in `LinuxConfig.IsPresent` geändert. Führen Sie den folgenden Befehl aus:

```powershell
# Modify node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName1 `
   -NodeId $node.Id `
   -Force
```

## <a name="check-the-compliance-status-of-a-managed-node"></a>Überprüfen des Konformitätsstatus eines verwalteten Knotens

Bei jeder Konsistenzprüfung für einen verwalteten Knoten durch State Configuration sendet der Knoten einen Statusbericht zurück an den Pullserver. Im folgenden Beispiel wird mithilfe des Cmdlets [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) ein Bericht über den Konformitätsstatus eines verwalteten Knotens abgerufen:

```powershell
Get-AzAutomationDscNodeReport `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeId $node.Id `
   -Latest
```

Die Ausgabe sollte ähnlich wie die folgende aussehen:

   :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnodereport-output.png" alt-text="Ausgabe des Befehls „Get-AzAutomationDscNodeReport“":::

Sie müssen möglicherweise bis zu 30 Minuten auf den ersten Bericht warten, nachdem Sie einen Knoten aktiviert haben. Weitere Informationen zu Berichtsdaten finden Sie unter [Verwenden eines DSC-Berichtsservers](/powershell/scripting/dsc/pull-server/reportserver).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie die folgenden Schritte aus, um die Ressourcen, die Sie für dieses Tutorial erstellt haben und nicht mehr benötigen, zu löschen.

1. Entfernen Sie den DSC-Knoten aus der Verwaltung durch ein Automation-Konto. Sie können zwar einen Knoten nicht mit PowerShell registrieren, doch Sie können eine solche Registrierung mit PowerShell aufheben. Führen Sie die folgenden Befehle aus:

    ```powershell
    # Get the ID of the DSC node
    $NodeID = (Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM).Id
    
    Unregister-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Id $NodeID `
       -Force

    # Verify using the same command from Register the Azure Linux VM for an Automation account. A blank response indicates success.
    Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM
    ```

1. Entfernen Sie die Metadaten aus den DSC-Knotenkonfigurationen in Automation. Führen Sie die folgenden Befehle aus:

    ```powershell
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0 `
       -IgnoreNodeMappings `
       -Force
    
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1 `
       -IgnoreNodeMappings `
       -Force

    # Verify using the same command from Compile configuration in Azure Automation.
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0
    
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1
    ```

      Eine erfolgreiche Entfernung erkennen Sie an einer Ausgabe ähnlich der folgenden: `Get-AzAutomationDscNodeConfiguration : NodeConfiguration LinuxConfig.IsNotPresent not found`.

1. Entfernen Sie die DSC-Konfiguration aus Automation. Führen Sie den folgenden Befehl aus:

    ```powershell
    Remove-AzAutomationDscConfiguration `
       -AutomationAccountName $automationAccount `
       -ResourceGroupName $resourceGroup `
       -Name $configurationName `
       -Force

    # Verify using the same command from Import configuration to Azure Automation.
    Get-AzAutomationDscConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $configurationName
    ```

   Eine erfolgreiche Entfernung erkennen Sie an einer Ausgabe ähnlich der folgenden: `Get-AzAutomationDscConfiguration : Operation returned an invalid status code 'NotFound'`.

1. Entfernen Sie das Modul „nx“ aus Automation. Führen Sie den folgenden Befehl aus:

    ```powershell
    Remove-AzAutomationModule `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $moduleName -Force

    # Verify using the same command from Install nx module.
    Get-AzAutomationModule `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $moduleName
    ```

   Eine erfolgreiche Entfernung erkennen Sie an einer Ausgabe ähnlich der folgenden: `Get-AzAutomationModule : The module was not found. Module name: nx.`.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mithilfe von Azure Automation State Configuration mit PowerShell überprüft, ob eine Azure Linux-VM mit einem gewünschten Zustand kompatibel ist. Eine ausführlichere Anleitung zum Erstellen von Konfigurationen finden Sie unter:

> [!div class="nextstepaction"]
> [Erstellen von DSC-Konfigurationen](./compose-configurationwithcompositeresources.md)