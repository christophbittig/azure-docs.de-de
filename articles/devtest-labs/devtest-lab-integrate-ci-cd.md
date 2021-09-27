---
title: Integrieren von Azure DevTest Labs in Azure Pipelines
description: Erfahren Sie, wie Sie Azure DevTest Labs in Ihre Azure Pipelines für Continuous Integration und Continuous Delivery integrieren
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c226523e9239b096cfd04990833d7c6cb1290400
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641353"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integrieren von Azure DevTest Labs in Ihre CI/CD-Pipeline in Azure Pipelines

Sie können die Erweiterung *Azure DevTest Labs-Aufgaben* verwenden, um Ihren Azure Pipelines Continuous Integration und Continuous Delivery (CI/CD)-Build und Ihre Releasepipelines in Azure DevTest Labs zu integrieren. Die Erweiterung installiert mehrere Aufgaben, darunter: 

- Erstellen eines virtuellen Computers (VM)
- Erstellen eines benutzerdefinierten Images von einem virtuellen Computer
- Löschen einer VM 

Diese Aufgaben ermöglichen es beispielsweise, eine *Golden Image*-VM für eine bestimmte Testaufgabe schnell bereitzustellen und die VM nach Abschluss des Tests wieder zu löschen.

In diesem Artikel wird gezeigt, wie Sie mithilfe von Azure DevTest Labs-Aufgaben einer Releasepipeline eine VM erstellen und bereitstellen, ein benutzerdefiniertes Image erstellen und anschließend die VM löschen. In Ihren eigenen benutzerdefinierten Pipelines zum Erstellen, Testen und Bereitstellen müssten Sie diese Aufgaben normalerweise einzeln ausführen.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Registrieren Sie sich bei Ihrer [Azure DevOps](https://dev.azure.com)-Organisation, oder melden Sie sich dort an, und [erstellen Sie darin ein Projekt](/vsts/organizations/projects/create-project). 
  
- Installieren Sie die Erweiterung „Azure DevTest Labs-Aufgaben“ über den Visual Studio Marketplace.
  
  1. Wechseln Sie zu [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Wählen Sie **Kostenlos erhalten** aus.
  1. Wählen Sie in der Dropdownliste Ihre Azure DevOps-Organisation und dann **Installieren** aus. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Erstellen der Vorlage zum Erstellen einer Azure-VM 

In diesem Abschnitt wird beschrieben, wie Sie die Azure Resource Manager-Vorlage erstellen, mit deren Hilfe Sie bei Bedarf eine Azure-VM erstellen.

1. Um eine Resource Manager-Vorlage in Ihrem Abonnement zu erstellen, führen Sie das Verfahren unter [Verwenden einer Resource Manager-Vorlage](devtest-lab-use-resource-manager-template.md) aus.
   
1. Bevor Sie die Resource Manager-Vorlage generieren, fügen Sie beim Erstellen der VM das [WinRM-Artefakt](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) hinzu. Für Bereitstellungsaufgaben wie *Azure-Dateikopiervorgang* und *PowerShell auf Zielcomputern* ist WinRM-Zugriff erforderlich. Das WinRM-Artefakt erfordert einen Hostnamen als Parameter, bei dem es sich um den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) der VM handeln muss. 
   
   > [!NOTE]
   > Wenn Sie WinRM mit einer freigegebenen IP-Adresse verwenden, müssen Sie eine NAT-Regel hinzufügen, mit der dem WinRM-Port ein externer Port zugeordnet wird. Die NAT-Regel ist nicht erforderlich, wenn Sie die VM mit einer öffentlichen IP-Adresse erstellen.
   
   
1. Speichern Sie die Vorlage als eine Datei mit dem Namen *CreateVMTemplate.json* auf Ihrem Computer.
   
1. Checken Sie die Vorlage in Ihr Quellcodeverwaltungssystem ein.

## <a name="create-a-script-to-get-vm-properties"></a>Erstellen eines Skripts zum Abrufen von VM-Eigenschaften

Wenn Sie Aufgabenschritte wie *Azure-Dateikopiervorgang* oder *PowerShell auf Zielcomputern* in der Releasepipeline ausführen, erfasst das folgende Skript die Werte, die Sie zum Bereitstellen einer App auf einer VM benötigen. Normalerweise verwenden Sie diese Aufgaben zum Bereitstellen Ihrer App auf einer Azure-VM. Die Aufgaben erfordern Werte wie den Namen der VM-Ressourcengruppe, die IP-Adresse und den vollqualifizierten Domänennamen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

So erstellen Sie die Skriptdatei:

1. Öffnen Sie einen Text-Editor, und fügen Sie das folgende Skript ein.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Speichern Sie die Datei unter einem Namen wie *GetLabVMParams.ps1*, und checken Sie sie in Ihr Quellcodeverwaltungssystem ein. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Erstellen einer Releasepipeline in Azure-Pipelines

So erstellen Sie eine neue Releasepipeline:

1. Wählen Sie auf Ihrer Azure DevOps-Projektseite im linken Navigationsbereich **Pipelines** > **Releases** aus.
1. Wählen Sie **Neue Pipeline** aus.
1. Scrollen Sie unter **Wählen Sie eine Vorlage aus** nach unten, und wählen Sie **Leere Stufe** und dann **Anwenden** aus.

### <a name="add-and-set-variables"></a>Hinzufügen und Festlegen von Variablen

Die Pipelineaufgaben verwenden die Werte, die Sie der VM beim Erstellen der Resource Manager-Vorlage im Azure-Portal zugewiesen haben. 

So fügen Sie Variablen für die Werte hinzu: 

1. Wählen Sie auf der Seite „Pipeline“ die Registerkarte **Variablen** aus.
   
1. Wählen Sie bei jeder Variablen **Hinzufügen** aus, und geben Sie den Namen und Wert ein:
   
   |Name|Wert|
   |---|---|
   |*vmName*|Der VM-Name, den Sie in der Resource Manager-Vorlage zugewiesen haben|
   |*userName*|Der Benutzername für den Zugriff auf die VM|
   |*password*|Das Kennwort für den Benutzernamen. Wählen Sie das Schlosssymbol aus, um das Kennwort auszublenden und zu schützen.

### <a name="create-a-devtest-labs-vm"></a>Erstellen einer DevTest Labs-VM

Der nächste Schritt ist das Erstellen der Golden Image-VM, die für zukünftige Bereitstellungen verwendet werden soll. Sie erstellen die VM in Ihrer Azure DevTest Labs-Instanz mithilfe der Aufgabe *Azure DevTest Labs – VM erstellen*.

1. Wählen Sie auf der Registerkarte **Pipeline** der Releasepipeline den durch einen Link verbundenen Text in **Stufe 1** bis **Stufenaufgaben anzeigen** und dann das Pluszeichen **+** neben **Agentauftrag** aus. 
   
1. Wählen Sie unter **Aufgaben hinzufügen** die Option **Azure DevTest Labs – VM erstellen** und dann **Hinzufügen** aus. 
   
1. Wählen Sie im linken Bereich **Azure DevTest Labs-VM erstellen** aus. 

1. Füllen Sie im rechten Bereich das Formular wie folgt aus:
   
   |Feld|Wert|
   |---|---|
   |**Azure RM-Abonnement**|Wählen Sie in der Dropdownliste **Verfügbare Azure-Dienstverbindungen** oder **Verfügbare Azure-Abonnements** eine Dienstverbindung bzw. ein Abonnement und dann bei Bedarf **Autorisieren** aus.<br /><br />**Hinweis:** Informationen zum Erstellen einer Verbindung mit eingeschränkteren Berechtigungen zu Ihrem Azure-Abonnement finden Sie unter [Azure Resource Manager-Dienstendpunkt](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Labname**|Wählen Sie den Namen eines vorhandenen Labs aus, in dem die Lab-VM erstellt werden soll.|
   |**Vorlagenname**|Geben Sie den vollständigen Pfad und Namen der Vorlagendatei ein, die Sie in Ihrem Quellcoderepository gespeichert haben. Zur Vereinfachung des Pfads können Sie integrierte Eigenschaften verwenden, z.B.:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Vorlagenparameter**|Geben Sie die Parameter für die Variablen ein, die Sie zuvor definiert haben:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Ausgabevariablen** > **Lab-VM-ID**|Geben Sie die Variable für die erstellte Lab-VM-ID ein. Wenn Sie den Standardwert, **labVMId**, verwenden, können Sie in nachfolgenden Aufgaben mit *$(labVMId)* auf die Variable verweisen.<br /><br />Sie können einen anderen Namen als den Standardnamen erstellen, doch denken Sie daran, in nachfolgenden Aufgaben den richtigen Namen zu verwenden. Sie können die Lab-VM-ID in der folgenden Form schreiben:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Erfassen der Details der DevTest Labs-VM

Führen Sie das zuvor erstellte Skript aus, um die Details der DevTest Labs-VM zu erfassen. 

1. Wählen Sie auf der Registerkarte **Pipeline** der Releasepipeline den durch einen Link verbundenen Text in **Stufe 1** bis **Stufenaufgaben anzeigen** und dann das Pluszeichen **+** neben **Agentauftrag** aus. 
   
1. Wählen Sie unter **Aufgaben hinzufügen** die Option **Azure PowerShell** und dann **Hinzufügen** aus. 
   
1. Wählen Sie im Bereich auf der linken Seite **Azure PowerShell script: FilePath** (Azure PowerShell-Skript: FilePath) aus. 
   
1. Füllen Sie im rechten Bereich das Formular wie folgt aus:
   
   |Feld|Wert|
   |---|---|
   |**Azure-Verbindungstyp**|Wählen Sie **Azure Resource Manager** aus.|
   |**Azure-Abonnement**|Wählen Sie Ihre Dienstverbindung oder Ihr Abonnement aus.| 
   |**Skripttyp**|Wählen Sie **Pfad zur Skriptdatei** aus.|
   |**Skriptpfad**|Geben Sie den vollständigen Pfad und Namen des PowerShell-Skripts ein, das Sie in Ihrem Quellcoderepository gespeichert haben. Zur Vereinfachung des Pfads können Sie integrierte Eigenschaften verwenden, z.B.:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Skriptargumente**|Geben Sie den Namen der Variablen *labVmId* ein, die von der vorherigen Aufgabe aufgefüllt wurde, z.B.:<br /><br />`-labVmId '$(labVMId)'`|

Das Skript erfasst die erforderlichen Werte und speichert sie in Umgebungsvariablen innerhalb der Releasepipeline, sodass Sie in nachfolgenden Schritten problemlos auf die Werte verweisen können.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Erstellen eines VM-Images aus der DevTest Labs-VM

Die nächste Aufgabe ist das Erstellen eines Image der neu bereitgestellten VM in Ihrer Azure DevTest Labs-Instanz. Sie können das Image anschließend dazu verwenden, um bei Bedarf Kopien der VM zu erstellen, wenn Sie eine Entwicklungsaufgabe ausführen oder einige Tests durchführen möchten. 

1. Wählen Sie auf der Registerkarte **Pipeline** der Releasepipeline den durch einen Link verbundenen Text in **Stufe 1** bis **Stufenaufgaben anzeigen** und dann das Pluszeichen **+** neben **Agentauftrag** aus. 
   
1. Wählen Sie unter **Aufgaben hinzufügen** die Option **Azure DevTest Labs – Benutzerdefiniertes Image erstellen** und dann **Hinzufügen** aus. 
   
1. Konfigurieren Sie die Aufgabe wie folgt:
   
   |Feld|Wert|
   |---|---|
   |**Azure RM-Abonnement**|Wählen Sie Ihre Dienstverbindung oder Ihr Abonnement aus.|
   |**Labname**|Wählen Sie den Namen eines vorhandenen Labs aus, in dem das Image erstellt werden soll.|
   |**Name des benutzerdefinierten Images**|Geben Sie einen Namen für das benutzerdefinierte Image ein.|
   |**Beschreibung** (optional)|Geben Sie eine Beschreibung ein, um die spätere Auswahl des richtigen Images zu erleichtern.|
   |**Quell-Lab-VM** > **Quell-Lab-VM-ID**|Wenn Sie den Standardnamen der Variablen „LabVMId“ geändert haben, geben Sie sie hier ein. Der Standardwert lautet **$(labVMId)**.|
   |**Ausgabevariablen** > **ID des benutzerdefinierten Images**|Sie können den Standardnamen der Variablen bei Bedarf bearbeiten.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Bereitstellen Ihrer App in der DevTest Labs-VM (optional)

Sie können Aufgaben hinzufügen, um Ihre App in der neuen DevTest Labs-VM bereitzustellen. Die Aufgaben, mit denen Sie die App normalerweise bereitstellen, sind *Azure-Dateikopiervorgang* und *PowerShell auf Zielcomputern*.

Die VM-Informationen, die Sie für die Parameter dieser Aufgaben benötigen, werden innerhalb der Releasepipeline in den drei Konfigurationsvariablen **labVmRgName**, **labVMIpAddress** und **labVMFqdn** gespeichert. Wenn Sie nur mit der Erstellung einer DevTest Labs-VM und einem benutzerdefinierten Image experimentieren möchten, ohne eine App darin bereitzustellen, können Sie diesen Schritt überspringen.

### <a name="delete-the-vm"></a>Löschen der virtuellen Computer

Die letzte Aufgabe ist das Löschen der VM, die Sie in der Azure DevTest Labs-Instanz bereitgestellt haben. Normalerweise würden Sie die VM löschen, nachdem Sie die erforderlichen Entwicklungsaufgaben oder Tests auf der bereitgestellten VM ausgeführt haben. 

1. Wählen Sie auf der Registerkarte **Pipeline** der Releasepipeline den durch einen Link verbundenen Text in **Stufe 1** bis **Stufenaufgaben anzeigen** und dann das Pluszeichen **+** neben **Agentauftrag** aus. 
   
1. Wählen Sie unter **Aufgaben hinzufügen** die Option **Azure DevTest Labs – VM löschen** und dann **Hinzufügen** aus. 
   
1. Konfigurieren Sie die Aufgabe wie folgt:
   
   - Wählen Sie unter **Azure RM-Abonnement** Ihre Dienstverbindung oder Ihr Abonnement aus. 
   - Wenn Sie bei **Lab-VM-ID** den Standardnamen der Variablen „LabVMId“ geändert haben, geben Sie sie hier ein. Der Standardwert lautet **$(labVMId)**.
   
### <a name="save-the-release-pipeline"></a>Speichern der Releasepipeline

So speichern Sie die neue Releasepipeline:

1. Wählen Sie auf der Seite „Releasepipeline“ den Namen **Neue Releasepipeline** aus, und geben Sie einen neuen Namen für die Pipeline ein. 
   
1. Wählen Sie oben rechts das Symbol **Speichern** aus. 

## <a name="create-and-run-a-release"></a>Erstellen und Ausführen eines Releases

So erstellen und führen Sie ein Release mithilfe der neuen Pipeline aus:

1. Wählen Sie oben rechts auf der Seite „Releasepipeline“ **Release erstellen** aus. 
   
1. Wählen Sie unter **Artifacts** den aktuellen Build und dann **Erstellen** aus.
   
1. Aktualisieren Sie in jeder Releasestufe die Ansicht Ihrer DevTest Labs-Instanz im Azure-Portal, um die Erstellung der VM, die Erstellung des Images und die Löschung der VM anzuzeigen.

Immer wenn Sie VMs benötigen, können Sie sie mithilfe des benutzerdefinierten Images erstellen.

## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich über das [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md).
- Sehen Sie sich im [öffentlichen DevTest Labs-GitHub-Repository](https://github.com/Azure/azure-quickstart-templates) weitere Resource Manager-Schnellstartvorlagen für die DevTest Labs-Automatisierung an.
- Sehen Sie sich bei Bedarf die Seite [Azure DevOps-Problembehandlung](/azure/devops/pipelines/troubleshooting) an.
 
