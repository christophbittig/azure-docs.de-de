---
title: Aktivieren von Azure Automation State Configuration
description: In diesem Artikel wird beschrieben, wie Sie Computer mit Azure Automation State Configuration für die Verwaltung einrichten.
services: automation
ms.service: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 12/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fc14ccca588a51271f3cb52c90904375b6739802
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484439"
---
# <a name="enable-azure-automation-state-configuration"></a>Aktivieren von Azure Automation State Configuration

In diesem Thema wird beschrieben, wie Sie Computer mit Azure Automation State Configuration für die Verwaltung einrichten können. Ausführliche Informationen zu diesem Dienst finden Sie in der [Übersicht über Azure Automation State Configuration](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Aktivieren von Azure-VMs

Mit Azure Automation State Configuration können Sie Azure-VMs problemlos für die Konfigurationsverwaltung aktivieren, indem Sie das Azure-Portal, Azure Resource Manager-Vorlagen oder PowerShell verwenden. Ohne Remotezugriff eines Administrators auf den virtuellen Computer registriert die Azure-VM-Erweiterung für DSC im Hintergrund den virtuellen Computer bei Azure Automation State Configuration. Da die Azure-Erweiterung asynchron ausgeführt wird, werden die Schritte zum Überprüfen des Fortschritts unter [Überprüfen des Status der VM-Einrichtung](#check-status-of-vm-setup) beschrieben.

> [!NOTE]
>Beim Bereitstellen von DSC für einen Linux-Knoten wird der Ordner **/tmp** verwendet. Module wie `nxautomation` werden temporär zur Überprüfung heruntergeladen, bevor Sie an den entsprechenden Speicherorten installiert werden. Zum Sicherstellen, dass die Module ordnungsgemäß installiert werden, benötigt der Log Analytics-Agent für Linux Lese-/Schreibberechtigungen für den Ordner **/tmp**.<br><br>
>Der Log Analytics-Agent für Linux wird als Benutzer `omsagent` ausgeführt. Damit der `omsagent`-Benutzer die erforderliche Schreibberechtigung erhält, führen Sie den Befehl `setfacl -m u:omsagent:rwx /tmp` aus.

### <a name="enable-a-vm-using-azure-portal"></a>Aktivieren eines virtuellen Computers über das Azure-Portal

So aktivieren Sie einen virtuellen Azure-Computer über das [Azure-Portal](https://portal.azure.com/) für State Configuration:

1. Navigieren Sie zu dem Azure Automation-Konto, in dem Sie VMs aktivieren möchten. 

2. Klicken Sie auf der State Configuration-Seite auf der Registerkarte **Knoten** auf **Hinzufügen**.

3. Wählen Sie einen virtuellen Computer zur Aktivierung aus.

4. Wenn auf dem Computer nicht die PowerShell-Erweiterung für DSC installiert ist und der Energiezustand ausgeführt wird, klicken Sie auf **Verbinden**.

5. Geben Sie unter **Registrierung** die [Werte des lokalen Konfigurations-Managers von PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) ein, die für Ihren Anwendungsfall erforderlich sind. Optional können Sie eine Knotenkonfiguration eingeben, die dem virtuellen Computer zugewiesen werden soll.

![Aktivieren der VM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Aktivieren eines virtuellen Computers mithilfe von Azure Resource Manager-Vorlagen

Sie können einen virtuellen Computer mit Azure Resource Manager-Vorlagen für State Configuration installieren und aktivieren. Unter [Vom DSC-Dienst verwalteter Server](https://azure.microsoft.com/resources/templates/automation-configuration/) finden Sie eine Beispielvorlage zum Aktivieren eines vorhandenen virtuellen Computers für State Configuration. Wenn Sie eine VM-Skalierungsgruppe verwalten, finden Sie Informationen in der Beispielvorlage [Von Azure Automation verwaltete VM-Skalierungsgruppenkonfiguration](https://azure.microsoft.com/resources/templates/vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Aktivieren von Computern mit PowerShell

Sie können das Cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) in PowerShell verwenden, um VMs für State Configuration zu aktivieren. 

> [!NOTE]
>Das Cmdlet `Register-AzAutomationDscNode` ist jedoch zurzeit nur für Computer mit Windows implementiert, da es nur die Windows-Erweiterung auslöst.

### <a name="register-vms-across-azure-subscriptions"></a>Registrieren von VMs über Azure-Abonnements hinweg

Die beste Methode zum Registrieren virtueller Computer aus anderen Azure-Abonnements ist die Verwendung der DSC-Erweiterung in einer Azure Resource Manager-Bereitstellungsvorlage. Beispiele finden Sie in [Erweiterung zum Konfigurieren des gewünschten Zustands mit Azure Resource Manager-Vorlagen](../virtual-machines/extensions/dsc-template.md).

Informationen zum Suchen von Registrierungsschlüssel und Registrierungs-URL, die als Parameter in der Vorlage verwendet werden, finden Sie unter [Sicheres Aktivieren von Computern mithilfe der Registrierung](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Aktivieren physischer/virtueller Windows-Computer

Sie können Windows-Server, die lokal oder in anderen Cloudumgebungen (einschließlich AWS EC2-Instanzen) ausgeführt werden, für Azure Automation State Configuration aktivieren. Die Server müssen allerdings über [ausgehenden Zugriff auf Azure](automation-dsc-overview.md#network-planning) verfügen.

1. Stellen Sie sicher, dass die neueste Version von [WMF 5](https://aka.ms/wmf5latest) auf den Computern installiert ist, die Sie für Azure Automation State Configuration aktivieren möchten. Außerdem muss WMF 5 auf dem Computer installiert sein, den Sie zum Aktivieren der Computer verwenden.
1. Befolgen Sie die Anweisungen im Abschnitt [Generieren von DSC-Metakonfigurationen](#generate-dsc-metaconfigurations), um einen Ordner mit den erforderlichen DSC-Metakonfigurationen zu erstellen. 
1. Verwenden Sie das folgende Cmdlet, um die PowerShell DSC-Metakonfigurationen remote auf die Computer anzuwenden, die Sie aktivieren möchten. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Wenn Sie die PowerShell DSC-Metakonfigurationen nicht remote anwenden können, kopieren Sie den Ordner **metaconfigurations** auf die Computer, die Sie aktivieren möchten. Fügen Sie dann Code zum Aufrufen von [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) lokal auf den Computern hinzu.
1. Stellen Sie über das Azure-Portal oder mithilfe von Cmdlets sicher, dass die Computer als State Configuration-Knoten angezeigt werden, die in Ihrem Azure Automation-Konto registriert sind.

## <a name="enable-physicalvirtual-linux-machines"></a>Aktivieren physischer/virtueller Linux-Computer

Sie können Linux-Server, die lokal oder in anderen Cloudumgebungen ausgeführt werden, für State Configuration aktivieren. Die Server müssen allerdings über [ausgehenden Zugriff auf Azure](automation-dsc-overview.md#network-planning) verfügen.

1. Stellen Sie sicher, dass die neueste Version von [PowerShell Desired State Configuration für Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) auf den Computern installiert ist, die Sie für State Configuration konfigurieren möchten.
2. Gehen Sie folgendermaßen vor, wenn die [Standardwerte des lokalen Konfigurations-Managers von PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) zu Ihrem Anwendungsfall passen und Sie Computer so aktivieren möchten, dass diese sowohl Informationen von State Configuration abrufen als auch Informationen an State Configuration senden:

   - Verwenden Sie `Register.py` auf jedem zu aktivierenden Linux-Computer, um den Computer mit den Standardeinstellungen des lokalen Konfigurations-Managers von PowerShell DSC zu aktivieren.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Informationen zum Suchen von Registrierungsschlüssel und Registrierungs-URL für Ihr Automation-Konto finden Sie im Abschnitt [Sicheres Aktivieren von Computern mithilfe der Registrierung](#enable-machines-securely-using-registration).

3. Wenn die Standardwerte des lokalen Konfigurations-Managers (LCM) von PowerShell DSC nicht zu Ihrem Anwendungsfall passen, oder Sie Computer so aktivieren möchten, dass diese nur Informationen an Azure Automation State Configuration senden, befolgen Sie die Schritte 4 bis 7. Fahren Sie andernfalls direkt mit Schritt 7 fort.

4. Befolgen Sie die Anweisungen im Abschnitt [Generieren von DSC-Metakonfigurationen](#generate-dsc-metaconfigurations), um einen Ordner mit den erforderlichen DSC-Metakonfigurationen zu erstellen.

5. Stellen Sie sicher, dass die neueste Version von [WMF 5](https://aka.ms/wmf5latest) auf den Computern installiert ist, die Sie zum Aktivieren Ihrer Computer für State Configuration verwenden.

6. Fügen Sie Code wie den folgenden hinzu, um die PowerShell DSC-Metakonfigurationen remote auf die zu aktivierenden Computer anzuwenden.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Wenn Sie die PowerShell DSC-Metakonfigurationen nicht remote anwenden können, kopieren Sie die Metakonfigurationen für die Remotecomputer aus dem in Schritt 4 beschriebenen Ordner auf die Linux-Computer.

8. Fügen Sie Code hinzu, um `Set-DscLocalConfigurationManager.py` lokal auf jedem für State Configuration zu aktivierenden Linux-Computer aufzurufen.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Stellen Sie über das Azure-Portal oder mithilfe von Cmdlets sicher, dass die Computer, die Sie aktivieren möchten, jetzt als DSC-Knoten angezeigt werden, die in Ihrem Azure Automation-Konto registriert sind.

## <a name="generate-dsc-metaconfigurations"></a>Generieren von DSC-Metakonfigurationen

Sie können eine [DSC-Metakonfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig) generieren, um jeden beliebigen Computer für State Configuration zu aktivieren. Mit dieser Konfiguration wird der DSC-Agent dazu aufgefordert, von Azure Automation State Configuration zu pullen und/oder Informationen an Azure Automation State Configuration zu senden. Sie können eine DSC-Metakonfiguration für Azure Automation State Configuration sowohl über eine PowerShell DSC-Konfiguration als auch über die Azure Automation PowerShell-Cmdlets generieren.

> [!NOTE]
> DSC-Metakonfigurationen enthalten die notwendigen Geheimnisse, um einen Computer in einem Automation-Konto für die Verwaltung zu aktivieren. Stellen Sie den ordnungsgemäßen Schutz aller von Ihnen generierten DSC-Metakonfigurationen sicher oder löschen Sie diese nach der Verwendung.

Die Proxy-Unterstützung für Metakonfigurationen wird durch den [lokalen Konfigurations-Manager](/powershell/scripting/dsc/managing-nodes/metaconfig) gesteuert, bei dem es sich um die Windows PowerShell DSC-Engine handelt. Der LCM wird auf allen Zielknoten ausgeführt und ist zuständig für das Aufrufen der Konfigurationsressourcen, die in einem DSC-Metakonfigurationsskript enthalten sind. Sie können die Proxy-Unterstützung in eine Metakonfiguration einschließen, indem Sie Definitionen der Eigenschaften `ProxyURL` und `ProxyCredential` wie erforderlich in die Blöcke `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb` und `ReportServerWeb` einbeziehen. Ein Beispiel für die URL-Einstellung ist `ProxyURL = "http://172.16.3.6:3128";`. Die `ProxyCredential`-Eigenschaft wird auf ein `PSCredential`-Objekt festgelegt, wie unter [Verwalten von Anmeldeinformationen in Azure Automation](shared-resources/credentials.md) beschrieben. 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generieren von DSC-Metakonfigurationen mithilfe einer DSC-Konfiguration

1. Öffnen Sie VS Code (oder Ihren bevorzugten Editor) als Administrator auf einem Computer in Ihrer lokalen Umgebung. Auf dem Computer muss die neueste Version von [WMF 5](https://aka.ms/wmf5latest) installiert sein.
1. Kopieren Sie das folgende Skript in Ihre lokale Umgebung. Dieses Skript enthält eine PowerShell DSC-Konfiguration zum Erstellen von Metakonfigurationen und einen Befehl zum Starten der Metakonfigurationserstellung.

    > [!NOTE]
    > Bei State Configuration-Knotenkonfigurationsnamen wird im Azure-Portal die Groß-/Kleinschreibung beachtet. Bei abweichender Groß-/Kleinschreibung wird der Knoten nicht auf der Registerkarte **Knoten** angezeigt.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Geben Sie den Registrierungsschlüssel und die URL für Ihr Automation-Konto sowie die Namen der Computer ein, die aktiviert werden sollen. Alle anderen Parameter sind optional. Informationen zum Suchen von Registrierungsschlüssel und Registrierungs-URL für Ihr Automation-Konto finden Sie im Abschnitt [Sicheres Aktivieren von Computern mithilfe der Registrierung](#enable-machines-securely-using-registration).

1. Wenn die Computer zwar DSC-Statusinformationen an Azure Automation State Configuration senden, jedoch keine Konfigurations- oder PowerShell-Module abrufen sollen, legen Sie den `ReportOnly`-Parameter auf TRUE fest.

1. Wenn `ReportOnly` nicht festgelegt wurde, senden die Computer DSC-Statusinformationen an Azure Automation State Configuration und pullen Konfigurations- oder PowerShell-Module. Legen Sie die Parameter in den Blöcken `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb` und `ReportServerWeb` entsprechend fest.

1. Führen Sie das Skript aus. Ihr Arbeitsverzeichnis sollte nun einen Ordner namens **DscMetaConfigs** mit den PowerShell DSC-Metakonfigurationen der (als Administrator) zu aktivierenden Computer enthalten.

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generieren von DSC-Metakonfigurationen mithilfe von Azure Automation-Cmdlets

Wenn die Standardwerte des LCM von PowerShell DSC zu Ihrem Anwendungsfall passen und Sie Computer so aktivieren möchten, dass diese sowohl Informationen von Azure Automation State Configuration abrufen als auch Informationen an Azure Automation State Configuration senden, können Sie die benötigten DSC-Metakonfigurationen mit den Azure Automation-Cmdlets einfacher generieren.

1. Öffnen Sie die PowerShell-Konsole oder VS Code als Administrator auf einem Computer in Ihrer lokalen Umgebung.
2. Stellen Sie mithilfe von [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) eine Verbindung mit Azure Resource Manager her.
3. Laden Sie von dem Automation-Konto, in dem Sie Knoten einrichten möchten, die PowerShell DSC-Metakonfigurationen für die Computer herunter, die Sie aktivieren möchten.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Ihr Arbeitsverzeichnis sollte nun einen Ordner namens **DscMetaConfigs** mit den PowerShell DSC-Metakonfigurationen der (als Administrator) zu aktivierenden Computer enthalten.

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Sicheres Aktivieren von Computern mithilfe der Registrierung

Sie können Computer sicher über das WMF 5-DSC-Registrierungsprotokoll für ein Azure Automation-Konto aktivieren. Mit diesem Protokoll kann sich ein DSC-Knoten bei einem PowerShell DSC-Pull- oder Berichtsserver, einschließlich Azure Automation State Configuration, authentifizieren. Der Knoten wird beim Server unter der Registrierungs-URL registriert und mit einem Registrierungsschlüssel authentifiziert. Während der Registrierung handeln der DSC-Knoten und der DSC-Pull-/Berichtsserver ein eindeutiges Zertifikat für den Knoten aus, das nach der Registrierung zur Authentifizierung beim Server verwendet wird. Durch diesen Prozess wird verhindert, dass aktivierte Knoten die Identität eines anderen Knotens annehmen, wenn z. B. ein Knoten kompromittiert wurde und böswillige Absichten verfolgt. Nach der Registrierung wird der Registrierungsschlüssel nicht wieder für die Authentifizierung verwendet und vom Knoten gelöscht.

Die für das DSC-Registrierungsprotokoll erforderlichen Informationen finden Sie im Azure-Portal in den **Kontoeinstellungen** unter **Schlüssel**. 

![Azure Automation-Schlüssel und -URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Die Registrierungs-URL finden Sie unter dem Feld „URL“ auf der Seite „Schlüssel“.
- Beim Registrierungsschlüssel handelt es sich um den Wert des Felds **Primärer Zugriffsschlüssel** oder **Sekundärer Zugriffsschlüssel** auf der Seite „Schlüssel“. Beide Schlüssel können verwendet werden.

Sie können den primären und den sekundären Zugriffsschlüssel eines Automation-Kontos jederzeit auf der Seite „Schlüssel“ erneut generieren, was für zusätzliche Sicherheit sorgt. Durch die Neugenerierung der Schlüssel wird verhindert, dass bei zukünftigen Knotenregistrierungen vorherige Schlüssel verwendet werden.

## <a name="re-register-a-node"></a>Erneutes Registrieren eines Knotens

Nach der Registrierung eines Computers als DSC-Knoten in Azure Automation State Configuration gibt es mehrere Gründe, warum Sie diesen Knoten in Zukunft möglicherweise erneut registrieren müssen.

- **Zertifikatverlängerung:** Bei älteren Windows Server-Versionen als Windows Server 2019 handelt jeder Knoten automatisch ein eindeutiges Zertifikat für die Authentifizierung aus, das nach einem Jahr abläuft. Wenn ein Zertifikat ohne Verlängerung abläuft, kann der Knoten nicht mehr mit Azure Automation kommunizieren und wird als `Unresponsive` gekennzeichnet. Gegenwärtig kann das PowerShell DSC-Registrierungsprotokoll Zertifikate nicht automatisch verlängern, wenn sie sich dem Ablaufdatum nähern, weshalb Sie die Knoten nach einem Jahr noch mal registrieren müssen. Stellen Sie vor einer erneuten Registrierung sicher, dass auf jedem Knoten WMF 5 RTM ausgeführt wird. 

    Eine erneute Registrierung, die frühestens 90 Tage vor dem Ablaufzeitpunkt des Zertifikats oder zu einem beliebigen Zeitpunkt nach dessen Ablaufzeitpunkt durchgeführt wird, hat zur Folge, dass ein neues Zertifikat generiert und verwendet wird. Eine Lösung für dieses Problem ist in Windows Server 2019 und höher enthalten.

- **Änderungen an DSC-LCM-Werten:** Möglicherweise müssen Sie die [Werte des LCM von PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) ändern, die Sie während der ersten Registrierung des Knotens festgelegt haben, z. B. `ConfigurationMode`. Diese DSC-Agent-Werte können Sie derzeit nur über eine erneute Registrierung ändern. Die einzige Ausnahme ist der Knotenkonfigurationswert, der dem Knoten zugewiesen wurde. Diesen können Sie direkt in Azure Automation DSC ändern.

Sie können einen Knoten auf die gleiche Weise wie bei der ersten Registrierung des Knotens mithilfe einer der in diesem Dokument beschriebenen Methoden erneut registrieren. Sie müssen die Registrierung eines Knotens in Azure Automation DSC nicht aufheben, bevor Sie ihn erneut registrieren.

## <a name="check-status-of-vm-setup"></a>Überprüfen des Status der VM-Einrichtung

Mit State Configuration können Sie problemlos Azure-VMs für die Konfigurationsverwaltung aktivieren. Die Azure-VM-Erweiterung für DSC wird verwendet, um virtuelle Computer bei Azure Automation DSC zu registrieren. Da die Azure-VM-Erweiterung für DSC asynchron ausgeführt wird, können die Überwachung des Fortschritts und die Problembehandlung bei der Ausführung wichtig sein.

> [!NOTE]
> Beim Aktivieren einer Azure Windows-VM für State Configuration mithilfe der Azure-VM-Erweiterung für DSC kann es bis zu einer Stunde dauern, bis in Azure Automation angezeigt wird, dass die VMs registriert sind. Der Grund für diese Verzögerung ist die zum Aktivieren virtueller Computer für State Configuration erforderliche Installation von WMF 5 auf dem virtuellen Computer durch die Azure-VM-Erweiterung für DSC.

So zeigen Sie den Status der Azure-VM-Erweiterung für DSC an:

1. Navigieren Sie im Azure-Portal zu dem virtuellen Computer, der aktiviert wird.
2. Klicken Sie unter **Einstellungen** auf **Erweiterungen**. 
3. Wählen Sie dann je nach Betriebssystem **DSC** oder **DSCForLinux** aus. 
4. Weitere Details erhalten Sie, indem Sie auf **Detaillierten Status anzeigen** klicken.

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Wie Sie DSC-Konfigurationen kompilieren und sie anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration](automation-dsc-compile.md).
- Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Ein Anwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Einrichten von Continuous Deployment mit Chocolatey](automation-dsc-cd-chocolatey.md).
- Informationen zur Behandlung von Problemen finden Sie unter [Problembehandlung bei Azure Automation State Configuration](./troubleshoot/desired-state-configuration.md).
