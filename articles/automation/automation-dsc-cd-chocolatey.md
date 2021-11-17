---
title: Einrichten von Continuous Deployment mit Azure Automation und Chocolatey
description: In diesem Artikel erfahren Sie, wie Sie Continuous Deployment mit State Configuration und dem Chocolatey-Paket-Manager einrichten.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: e602ed172aa5bb222b2480fb89896e8877e3d713
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494304"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Einrichten von Continuous Deployment mit Chocolatey

In einer DevOps-Umgebung gibt es viele Tools, die an den verschiedenen Punkten der Continuous Integration-Pipeline als Hilfe dienen. Azure Automation [State Configuration](automation-dsc-overview.md) ist eine willkommene neue Option für DevOps-Teams. 

Azure Automation ist ein verwalteter Dienst in Microsoft Azure, mit dem Sie verschiedene Aufgaben automatisieren können, indem Sie Runbooks, Knoten und freigegebene Ressourcen verwenden, z. B. Anmeldeinformationen, Zeitpläne und globale Variablen. Azure Automation State Configuration erweitert diese Automatisierungsfunktion auf PowerShell DSC-Tools (Desired State Configuration). Hier ist eine gute [Übersicht](automation-dsc-overview.md).

In diesem Artikel wird das Einrichten von Continuous Deployment (CD) für einen Windows-Computer veranschaulicht. Sie können das Verfahren problemlos so erweitern, dass in der Rolle (z. B. einer Website) so viele Windows-Computer wie nötig enthalten sind, und von diesem Punkt aus noch eine Erweiterung auf zusätzliche Rollen durchführen.

![Kontinuierliche Bereitstellung für virtuelle IaaS-Computer](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Allgemeines

Dieser Bereich ist recht komplex, aber glücklicherweise lässt er sich in zwei Hauptprozesse unterteilen:

- Das Schreiben und Testen von Code und das anschließende Erstellen und Veröffentlichen von Installationspaketen für größere und kleinere Versionen des Systems.
- Das Erstellen und Verwalten von virtuellen Computern, mit denen der Code in den Paketen installiert und ausgeführt wird.  

Nachdem diese beiden Kernprozesse eingerichtet wurden, ist es nur noch ein kleiner Schritt zum automatischen Aktualisieren des Pakets auf Ihren VMs, wenn neue Versionen erstellt und bereitgestellt werden.

## <a name="component-overview"></a>Übersicht über die Komponenten

Paket-Manager (z. B. [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool)) sind in der Linux-Welt recht bekannt, aber dies gilt nicht so sehr für die Windows-Welt.
[Chocolatey](https://chocolatey.org/) ist ein Beispiel hierfür, und der [Blog von Scott Hanselman](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) zu diesem Thema stellt eine gute Einführung dar. Zusammenfassend lässt sich sagen, dass Sie mit Chocolatey Pakete über die Befehlszeile aus einem zentralen Paketrepository auf einem Windows-Betriebssystem installieren können. Sie können ein eigenes Repository erstellen und verwalten, und Chocolatey kann Pakete aus allen Repositorys installieren, die Sie angeben.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) ist ein PowerShell-Tool, mit dem Sie die gewünschte Konfiguration für einen Computer deklarieren können. Wenn Sie beispielsweise Chocolatey und IIS installieren und Port 80 öffnen möchten und Version 1.0.0 Ihrer Website installiert werden soll, wird diese Konfiguration vom DSC Local Configuration Manager (LCM) implementiert. Ein DSC-Pullserver enthält ein Repository mit Konfigurationen für Ihre Computer. Der LCM jedes Computers checkt sich regelmäßig ein, um zu überprüfen, ob die Konfiguration mit der gespeicherten Konfiguration übereinstimmt. Es kann entweder der Status gemeldet werden, oder es kann versucht werden, den Computer wieder auf den Stand der gespeicherten Konfiguration zu bringen. Sie können die gespeicherte Konfiguration auf dem Pullserver bearbeiten, um einen Computer oder eine Gruppe von Computern auf den Stand der geänderten Konfiguration zu bringen.

Eine DSC-Ressource ist ein Codemodul mit speziellen Funktionen, z. B. zur Verwaltung von Netzwerken, Active Directory oder SQL Server. Die Chocolatey DSC-Ressource kann zum Zugreifen auf einen NuGet-Server (unter anderem), Herunterladen von Paketen, Installieren von Paketen usw. verwendet werden. Der [PowerShell-Katalog](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title) enthält noch viele weitere DSC-Ressourcen. Sie installieren diese Module auf Ihrem Pullserver für Azure Automation State Configuration, damit sie für die Konfigurationen verwendet werden können.

Resource Manager-Vorlagen bieten eine deklarative Möglichkeit zum Generieren Ihrer Infrastruktur, z. B. für Netzwerke, Subnetze, Netzwerksicherheit und -routing, Lastenausgleich, Netzwerkschnittstellenkarten, virtuelle Computer usw. In [diesem Artikel](../azure-resource-manager/management/deployment-models.md) wird das Resource Manager-Bereitstellungsmodell (deklarativ) mit dem (imperativen) Azure Service Management-Bereitstellungsmodell (ASM oder klassisch) verglichen. In diesem Artikel geht es um die Bereiche Kernressourcenanbieter: Compute, Speicher und Netzwerk.

Ein wichtiges Merkmal einer Resource Manager-Vorlage ist die Möglichkeit, bei der Bereitstellung eines virtuellen Computers eine VM-Erweiterung darauf zu installieren. Eine VM-Erweiterung verfügt über bestimmte Funktionen, z. B. das Ausführen eines benutzerdefinierten Skripts, das Installieren einer Antivirensoftware oder das Ausführen eines DSC-Konfigurationsskripts. Es gibt noch viele andere Arten von VM-Erweiterungen.

## <a name="quick-trip-around-the-diagram"></a>Kurzvorstellung des Diagramms

Sie beginnen oben und schreiben Ihren Code, führen die Erstellung und das Testen durch und erstellen dann ein Installationspaket. Chocolatey kann verschiedene Arten von Installationspaketen verarbeiten, z. B. MSI, MSU, ZIP. Außerdem verfügen Sie über die volle Leistungsfähigkeit von PowerShell zum Durchführen der eigentlichen Installation, falls die nativen Funktionen von Chocolatey nicht ausreichen sollten. Legen Sie das Paket an einem erreichbaren Ort ab, z. B. in einem Paketrepository. In diesem Anwendungsbeispiel wird ein öffentlicher Ordner in einem Azure Blob Storage-Konto verwendet, aber er kann sich an einem beliebigen Ort befinden. Chocolatey arbeitet standardmäßig mit NuGet-Servern und einigen anderen zusammen, was die Verwaltung der Paketmetadaten betrifft. Die Optionen werden in [diesem Artikel](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) beschrieben. Im Anwendungsbeispiel wird NuGet verwendet. Bei „Nuspec“ handelt es sich um Metadaten zu Ihren Paketen. Die NUSPEC-Informationen werden in einem NuPkg kompiliert und auf einem NuGet-Server gespeichert. Wenn Ihre Konfiguration ein Paket anhand des Namens anfordert und auf einen NuGet-Server verweist, verwendet die Chocolatey DSC-Ressource auf dem virtuellen Computer das Paket und installiert es. Sie können auch eine bestimmte Version eines Pakets anfordern.

Im Bildbereich links unten ist eine Azure Resource Manager-Vorlage dargestellt. In diesem Anwendungsbeispiel wird der virtuelle Computer von der VM-Erweiterung beim Pullserver für Azure Automation State Configuration als Knoten registriert. Die Konfiguration wird zweimal auf dem Pullserver gespeichert: einmal als Nur-Text und einmal als kompilierte MOF-Datei. Im Azure-Portal ist die MOF-Datei eine Knotenkonfiguration (im Gegensatz zu einer normalen Konfiguration). Es ist das Artefakt, das einem Knoten zugeordnet ist, damit dem Knoten die Konfiguration bekannt ist. Die Details unten verdeutlichen, wie Sie die Knotenkonfiguration dem Knoten zuweisen.

Das Erstellen der NUSPEC-Datei und ihr Kompilieren und Speichern auf einem NuGet-Server bedeutet keinen großen Aufwand. Und Sie verwalten bereits virtuelle Computer. 

Der nächste Schritt des Continuous Deployment erfordert das Einrichten des Pullservers (einmalig), das Registrieren Ihrer Knoten bei diesem (einmalig) und das Erstellen und Speichern der Konfiguration auf dem Server (am Anfang). Wenn die Pakete dann aktualisiert und im Repository bereitgestellt werden, müssen Sie nur die Konfiguration und die Knotenkonfiguration nach Bedarf auf dem Pullserver aktualisieren.

Falls Sie nicht mit einer Resource Manager-Vorlage beginnen, ist dies auch in Ordnung. Es stehen PowerShell-Befehle zur Verfügung, mit denen Sie Ihre virtuellen Computer beim Pullserver registrieren können. Weitere Informationen finden Sie unter [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Informationen zum Anwendungsbeispiel

Im Anwendungsbeispiel in diesem Artikel wird von einem virtuellen Computer aus einem generischen Windows Server 2012 R2-Image aus dem Azure-Katalog ausgegangen. Sie können mithilfe eines beliebigen gespeicherten Image starten und dann mit der Optimierung mithilfe der DSC-Konfiguration beginnen.
Das Ändern einer Konfiguration, die in ein Image integriert ist, ist aber deutlich schwieriger als das dynamische Aktualisieren der Konfiguration per DSC.

Sie müssen keine Resource Manager-Vorlage und dann die VM-Erweiterung verwenden, um dieses Verfahren mit Ihren virtuellen Computern zu nutzen. Und Ihre virtuellen Computer müssen nicht unter Azure vorhanden sein, um in den Genuss der CD-Verwaltung zu kommen. Lediglich Chocolatey muss installiert sein, und der lokale Konfigurations-Manager (LCM) muss für den virtuellen Computer konfiguriert werden, damit er informiert ist, wo sich der Pullserver befindet.

Wenn Sie ein Paket auf einem virtuellen Computer aktualisieren, der sich in der Produktion befindet, müssen Sie diesen virtuellen Computer aus der Rotation nehmen, während das Update installiert wird. Die Vorgehensweise kann dabei stark variieren. Beispielsweise können Sie bei einem virtuellen Computer hinter einem Azure Load Balancer einen benutzerdefinierten Test hinzufügen. Richten Sie dies so ein, dass der Testendpunkt während der Aktualisierung des virtuellen Computers eine „400“ zurückgibt. Die erforderliche Optimierung für diese Änderung kann sich innerhalb Ihrer Konfiguration befinden. Dies gilt auch für den Wechsel zurück zur Rückgabe einer „200“, nachdem das Update abgeschlossen ist.

Den vollständigen Quellcode für dieses Anwendungsbeispiel finden Sie in [diesem Visual Studio-Projekt](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) bei GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Schritt 1: Einrichten des Pullservers und des Automation-Kontos

An einer authentifizierten (`Connect-AzAccount`) PowerShell-Befehlszeile. (Die Einrichtung des Pullservers kann einige Minuten dauern.)

```azurepowershell-interactive
New-AzResourceGroup -Name MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount -ResourceGroupName MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES -Name MY-AUTOMATION-ACCOUNT
```

Sie können Ihr Automation-Konto in einer der folgenden Regionen (auch als Standort bezeichnet) einrichten: „USA, Osten 2“, „USA, Süden-Mitte“, „US Gov Virginia“, „Europa, Westen“, „Asien, Südosten“, „Japan, Osten“, „Indien, Mitte“, „Australien, Südosten“, „Kanada, Mitte“, „Europa, Norden“.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Schritt 2: Optimieren der VM-Erweiterung für die Resource Manager-Vorlage

Details zur VM-Registrierung (mit der PowerShell DSC-VM-Erweiterung) finden Sie in dieser [Azure-Schnellstartvorlage](https://azure.microsoft.com/blog/automating-vm-configuration-using-powershell-dsc-extension/).
In diesem Schritt wird Ihr neuer virtueller Computer beim Pullserver in der Liste der Zustandskonfigurationsknoten registriert. Ein Teil dieser Registrierung ist das Angeben der Knotenkonfiguration, die auf den Knoten angewendet wird. Diese Knotenkonfiguration muss auf dem Pullserver noch nicht vorhanden sein. Es ist also in Ordnung, dass dies in Schritt 4 zum ersten Mal durchgeführt wird. Hier in Schritt 2 müssen Sie sich aber für den Namen des Knotens und den Namen der Konfiguration entschieden haben. In diesem Anwendungsbeispiel lautet der Knoten „isvbox“, und die Konfiguration lautet „ISVBoxConfig“. Der Name für die Knotenkonfiguration (zum Angeben in „DeploymentTemplate.json“) lautet also „ISVBoxConfig.isvbox“.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Schritt 3: Hinzufügen von erforderlichen DSC-Ressourcen auf dem Pullserver

Der PowerShell-Katalog wird genutzt, um DSC-Ressourcen in Ihrem Azure Automation-Konto zu installieren.
Navigieren Sie zur gewünschten Ressource, und klicken Sie auf die Schaltfläche „In Azure Automation bereitstellen“.

![PowerShell-Katalog-Beispiel](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Ein weiteres Verfahren, das erst kürzlich im Azure-Portal hinzugefügt wurde, ermöglicht das Pullen neuer Module oder das Aktualisieren bereits vorhandener Module. Klicken Sie auf die Ressource „Automation-Konto“, auf die Kachel „Ressourcen“ und schließlich auf die Kachel „Module“. Über das Symbol „Katalog durchsuchen“ können die Liste mit den im Katalog enthaltenen Modulen aufrufen, Details anzeigen und letztlich einen Import in Ihr Automation-Konto durchführen. Dadurch können Sie Ihre Module von Zeit zu Zeit problemlos auf den neuesten Stand bringen. Das Importfeature überprüft zudem Abhängigkeiten mit anderen Modulen, um zu gewährleisten, dass alles synchron ist.

Es gibt auch ein manuelles Verfahren, das nur einmal pro Ressource verwendet wird, sofern Sie später kein Upgrade ausführen möchten. Weitere Informationen zum Erstellen von PowerShell-Integrationsmodulen finden Sie unter [Erstellen von Integrationsmodulen für Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>Die Ordnerstruktur eines PowerShell-Integrationsmoduls für einen Windows-Computer unterscheidet sich etwas von der Ordnerstruktur, die von Azure Automation erwartet wird. 

1. Installieren Sie [Windows Management Framework v5](https://aka.ms/wmf5latest) (unter Windows 10 nicht erforderlich).

2. Installieren Sie das Integrationsmodul.

    ```azurepowershell-interactive
    Install-Module -Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Kopieren Sie den Modulordner aus **C:\Programme\WindowsPowerShell\Modules\MODULNAME** in einen temporären Ordner.

4. Löschen Sie Beispiele und Dokumentation aus dem Hauptordner.

5. Zippen Sie den Hauptordner, und benennen Sie die ZIP-Datei mit dem Namen des Ordners.

6. Speichern Sie die ZIP-Datei an einem zugänglichen HTTP-Speicherort, z. B. in Blob Storage in einem Azure Storage-Konto.

7. Führen Sie den folgenden Befehl aus.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME -ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

Im enthaltenen Beispiel werden diese Schritte für cChoco und xNetworking implementiert. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Schritt 4: Hinzufügen der Knotenkonfiguration auf dem Pullserver

Es ist nichts Besonderes zu beachten, wenn Sie Ihre Konfiguration zum ersten Mal in den Pullserver importieren und die Kompilierung durchführen. Alle nachfolgenden Import- und Kompiliervorgänge derselben Konfiguration sehen dann genau gleich aus. Bei jeder Aktualisierung des Pakets und der Umstellung auf die Produktion führen Sie diesen Schritt aus, nachdem Sie sichergestellt haben, dass die Konfigurationsdatei korrekt ist – einschließlich der neuen Version Ihres Pakets. Dies ist die Konfigurationsdatei **ISVBoxConfig.ps1**:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Dies ist das Skript **New-ConfigurationScript.ps1** (für die Verwendung des Az-Moduls geändert):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published -Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Diese Schritte führen zu einer neuen Knotenkonfiguration mit dem Namen **ISVBoxConfig.isvbox**, die auf dem Pullserver gespeichert wird. Der Name der Knotenkonfiguration wird als `configurationName.nodeName` erstellt.

## <a name="step-5-create-and-maintain-package-metadata"></a>Schritt 5: Erstellen und Verwalten von Paketmetadaten

Für jedes Paket, das Sie im Paketrepository speichern, benötigen Sie eine NUSPEC-Datei, in der das Paket beschrieben wird. Sie muss auf Ihrem NuGet-Server kompiliert und gespeichert werden. Dieser Prozess ist [hier](https://docs.nuget.org/create/creating-and-publishing-a-package)beschrieben. 

Sie können **MyGet.org** als NuGet-Server verwenden. Sie können diesen Dienst erwerben, aber es steht auch eine kostenlose Starter-SKU zur Verfügung. Unter [NuGet](https://www.nuget.org/) finden Sie Anweisungen zum Installieren Ihres eigenen NuGet-Servers für Ihre privaten Pakete.

## <a name="step-6-tie-it-all-together"></a>Schritt 6: Zusammenfügen aller Komponenten

Bei jedem Bestehen des Qualitätssicherungstests durch eine Version und anschließender Genehmigung für die Bereitstellung wird das Paket erstellt, und die NUSPEC-Datei und das Nupkg werden aktualisiert und auf dem NuGet-Server bereitgestellt. Darüber hinaus muss die Konfiguration (Schritt 4) aktualisiert werden, damit sie mit der neuen Versionsnummer übereinstimmt. Sie muss dann an den Pullserver gesendet und kompiliert werden.

Ab diesem Punkt liegt es an den virtuellen Computern, die von dieser Konfiguration abhängig sind, das Update abzurufen und zu installieren. Diese Updates sind einfach und umfassen nur ein oder zwei Codezeilen in PowerShell. Bei Azure DevOps sind einige davon in Buildaufgaben gekapselt, die in einem Build miteinander verkettet werden können. Dieser [Artikel](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) enthält hierzu weitere Details. In diesem [GitHub-Repository](https://github.com/Microsoft/vso-agent-tasks) finden Sie Details zu den verfügbaren Buildaufgaben.

## <a name="related-articles"></a>Verwandte Artikel
* [Azure Automation DSC – Übersicht](automation-dsc-overview.md)
* [Integrieren von Computern für die Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Übersicht über Azure Automation State Configuration](automation-dsc-overview.md).
- Eine Einführung in dieses Feature finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Wie Sie DSC-Konfigurationen kompilieren und sie anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration](automation-dsc-compile.md).
- Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
