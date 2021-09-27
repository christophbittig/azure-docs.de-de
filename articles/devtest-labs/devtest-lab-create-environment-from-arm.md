---
title: Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Vorlagen
description: Hier erfahren Sie, wie Sie in Azure DevTest Labs auf der Grundlage einer Azure Resource Manager-Vorlage Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen erstellen.
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: c1a83a5e8f471b68d1c512443371a9a592cfbc88
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587989"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen

Azure DevTest Labs-Umgebungen ermöglichen Benutzern, komplexe Infrastrukturen problemlos auf konsistente Weise innerhalb der Grenzen des Labs bereitzustellen. Sie können [Azure Resource Manager Vorlagen](../azure-resource-manager/templates/syntax.md) verwenden, um Umgebungen mit Ressourcensätzen in DevTest Labs zu erstellen. Diese Umgebungen können alle Azure-Ressourcen enthalten, die von Resource Manager-Vorlagen erstellt werden können.

Sie können einem Lab problemlos [jeweils einen virtuellen Computer (VM) hinzufügen](devtest-lab-add-vm.md), indem Sie das [Azure-Portal](https://portal.azure.com) verwenden. Szenarios wie Apps mit mehreren Ebenen oder eine SharePoint-Farm benötigen jedoch einen Mechanismus zum Erstellen mehrerer virtueller Computer in einem einzigen Schritt. Mit Azure Resource Manager-Vorlagen können Sie die Infrastruktur und Konfiguration Ihrer Azure-Lösung definieren und wiederholt mehrere virtuelle Computer in einem konsistenten Zustand bereitstellen.

Azure Resource Manager-Vorlagen bieten außerdem die folgenden Vorteile:

- Azure Resource Manager-Vorlagen werden direkt über Ihr Quellcodeverwaltungs-Repository (GitHub oder Azure Repos) geladen.
- Ihre Benutzer können eine Umgebung erstellen, indem sie genau wie bei anderen Arten von [VM-Grundlagen](devtest-lab-comparing-vm-base-image-types.md) eine konfigurierte Azure Resource Manager-Vorlage über das Azure-Portal auswählen.
- Sie können Azure PaaS-Ressourcen sowie virtuelle IaaS-Computer in einer Umgebung über eine Azure Resource Manager-Vorlage bereitstellen.
- Sie können die Umgebungskosten im Lab zusätzlich zu den einzelnen virtuellen Computern nachverfolgen, die unter Verwendung anderer Grundlagen erstellt wurden. Die PaaS-Ressourcen werden erstellt und in der Kostenüberwachung angezeigt. Das automatische Herunterfahren für virtuelle Computer gilt jedoch nicht für PaaS-Ressourcen.

Unter [Vorteile der Verwendung von Resource Manager-Vorlagen](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager) erfahren Sie mehr über die Vorteile der Verwendung von Resource Manager-Vorlagen zum Bereitstellen, Aktualisieren oder Löschen vieler Labressourcen in einem einzigen Vorgang.

> [!NOTE]
> Wenn Sie eine Resource Manager-Vorlage als Grundlage zum Erstellen von Lab-VMs verwenden, sind jedoch einige Unterschiede zwischen dem Erstellen mehrerer VMs und einer einzelnen VM zu berücksichtigen. Weitere Informationen finden Sie unter [Erstellen von virtuellen Computern mit einer Azure Resource Manager-Vorlage](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>Verwenden öffentlicher DevTest Labs-Umgebungen
Azure DevTest Labs verfügt über ein [öffentliches Repository von Azure Resource Manager-Vorlagen](https://github.com/Azure/azure-devtestlab/tree/master/Environments), die Sie zum Erstellen von Umgebungen verwenden können, ohne selbst eine Verbindung mit einer externen GitHub-Quelle herstellen zu müssen. Dieses öffentliche Repository ähnelt dem öffentlichen Repository von Artefakten, das im Azure-Portal für jedes Lab verfügbar ist, das Sie erstellen. Das Umgebungsrepository ermöglicht Ihnen den schnellen Einstieg in vordefinierte Umgebungsvorlagen mit wenigen Eingabeparametern. Diese Vorlagen bieten Ihnen einen reibungslosen Einstieg in die Verwendung von PaaS-Ressourcen in Labs.

Im öffentlichen Repository haben das DevTest Labs-Team und andere häufig verwendete Vorlagen wie Azure-Web-Apps, Service Fabric Cluster und eine Entwicklungsumgebung für SharePoint-Farmen erstellt und freigegeben. Sie können diese Vorlagen direkt verwenden oder Ihren Anforderungen entsprechend anpassen. Weitere Informationen finden Sie unter [Konfigurieren und Verwenden von Umgebungen in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md). Nachdem Sie Ihre eigenen Vorlagen erstellt haben, können Sie sie in diesem Repository speichern, um sie für andere Benutzer freizugeben, oder Sie können ein eigenes Git-Repository einrichten.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Erstellen Ihres eigenen Vorlagenrepositorys

Bei Infrastruktur als Code und Konfiguration als Code sollten Sie Umgebungsvorlagen in der Quellcodeverwaltung verwalten. Azure DevTest Labs wendet diese Vorgehensweise an und lädt alle Azure Resource Manager-Vorlagen direkt aus Ihrem GitHub- oder Azure Repos-Repository. Folglich können Sie Resource Manager-Vorlagen im gesamten Freigabezyklus (von der Test- bis zur Produktionsumgebung) verwenden.

Zum Organisieren von Azure Resource Manager-Vorlagen in einem Repository sind einige Regeln zu beachten:

- Sie müssen die Mastervorlagendatei *azuredeploy.json* nennen.

- Wenn Sie in einer Parameterdatei definierte Parameterwerte verwenden möchten, muss der Name der Parameterdatei *azuredeploy.parameters.json* lauten.

  Sie können mithilfe der Parameter `_artifactsLocation` und `_artifactsLocationSasToken` den URI-Wert „parametersLink“ erstellen, der DevTest Labs das automatische Verwalten geschachtelter Vorlagen ermöglicht. Weitere Informationen finden Sie unter [Bereitstellen geschachtelter Azure Resource Manager-Vorlagen für Testumgebungen](deploy-nested-template-environments.md).

- Sie können Metadaten definieren, um den Anzeigenamen und die Beschreibung der Vorlage wie folgt in einer Datei namens *metadata.json* anzugeben:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Zentrale Azure Resource Manager-Vorlagendateien](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Hinzufügen von Vorlagenrepositorys zum Lab

Nachdem Sie Ihr Repository erstellt und konfiguriert haben, können Sie es mit dem Azure-Portal dem Lab hinzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
1. Wählen Sie in der Liste der Labs das gewünschte Lab aus.
1. Wählen Sie im Bereich **Übersicht** des Labs die Option **Konfiguration und Richtlinien** aus.

   ![Konfiguration und Richtlinien](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Wählen Sie in der Einstellungsliste **Konfiguration und Richtlinien** die Option **Repositorys** aus. Für alle Labs wird automatisch das Repository **Public Artifact Repo** generiert und mit dem [öffentlichen DevTest Labs-GitHub-Repository](https://github.com/Azure/azure-devtestlab) verbunden.

1. Um Ihr Azure Resource Manager-Vorlagenrepository hinzuzufügen, wählen Sie **Hinzufügen** aus.

   ![Öffentliches Repository](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Geben Sie im Bereich **Repositorys** die folgenden Informationen ein:

   - **Name**: Geben Sie einen Repositorynamen ein, der im Lab verwendet werden soll.
   - **Git-Klon-URL**: Geben Sie die Git-HTTPS-Klon-URL aus GitHub oder Azure Repos ein.
   - **Branch** (optional): Geben Sie den Branchnamen für den Zugriff auf Ihre Azure Resource Manager-Vorlagendefinitionen ein.
   - **Persönliches Zugriffstoken**: Geben Sie das persönliche Zugriffstoken für den sicheren Zugriff auf Ihr Repository ein.
     - Um Ihr Token aus Azure Repos abzurufen, wählen Sie unter Ihrem Profil **Benutzereinstellungen** > **Sicherheit** > **Persönliche Zugriffstoken** aus.
     - Um Ihr Token aus GitHub abzurufen, wählen Sie unter Ihrem Profil **Einstellungen** > **Entwicklereinstellungen** > **Persönliche Zugriffstoken** aus.
   - **Ordnerpfade**: Geben Sie entweder für Ihre Artefaktdefinitionen oder Ihre Azure Resource Manager-Vorlagendefinitionen den zu Ihrem Git-Klon-URI relativen Ordnerpfad ein.

1. Wählen Sie **Speichern** aus.

   ![Hinzufügen eines neuen Repositorys](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Nachdem Sie dem Lab eine Azure Resource Manager-Vorlage hinzugefügt haben, können Ihre Lab-Benutzer mithilfe der Vorlage Umgebungen erstellen.

## <a name="configure-access-rights-for-lab-users"></a>Konfigurieren von Zugriffsrechten für Lab-Benutzer

Lab-Benutzer verfügen standardmäßig über die Rolle **Leser**, sodass sie die Ressourcen in einer Umgebungsressourcengruppe nicht ändern können. Beispielsweise können sie ihre Ressourcen nicht anhalten oder starten.

Führen Sie die folgenden Schritte aus, um Lab-Benutzern die Rolle **Mitwirkender** zu geben, sodass sie die Ressourcen in ihren Umgebungen bearbeiten können:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Bereich **Übersicht** des Labs **Konfiguration und Richtlinien** aus, und wählen Sie dann **Lab-Einstellungen** aus.

1. Wählen Sie im Bereich **Labeinstellungen** die Option **Mitwirkender** und dann **Speichern** aus, um Lab-Benutzern Schreibberechtigungen zu erteilen.

   ![Konfigurieren von Zugriffsrechten für Lab-Benutzer](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

Im nächsten Abschnitt erfahren Sie, wie Sie Umgebungen auf der Grundlage einer Azure Resource Manager-Vorlage erstellen.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Erstellen von Umgebungen aus Vorlagen im Azure-Portal

Nachdem Sie dem Lab eine Azure Resource Manager-Vorlage hinzugefügt haben, können Ihre Lab-Benutzer mit den folgenden Schritten im Azure-Portal Umgebungen erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.

1. Wählen Sie in der Liste der Labs das gewünschte Lab aus.

1. Wählen Sie auf der Seite des Labs die Option **Hinzufügen** aus.

1. Im Bereich **Basis auswählen** werden die Basisimages, die Sie mit Ihren Azure Resource Manager-Vorlagen verwenden können, zuerst aufgeführt. Wählen Sie die gewünschte Azure Resource Manager-Vorlage aus.

   ![Auswählen einer Grundlage](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. Geben Sie im Bereich **Hinzufügen** einen **Umgebungsnamen** ein, der für Umgebungsbenutzer angezeigt werden soll.

   Die Azure Resource Manager-Vorlage definiert die restlichen Eingabefelder. Wenn die Vorlagendatei *azuredeploy.parameter.json* Standardwerte definiert, werden diese Werte in den Eingabefeldern angezeigt.

   Für Parameter vom Typ *sichere Zeichenfolge* können Sie die Geheimnisse aus Ihrer Azure Key Vault-Instanz verwenden. Informationen zum Speichern von Geheimnissen in einem Schlüsseltresor und deren Verwendung beim Erstellen von Lab-Ressourcen finden Sie unter [Speichern von Geheimnissen in einem Schlüsseltresor in Azure DevTest Labs](devtest-lab-store-secrets-in-key-vault.md).  

   ![Hinzufügen eines Bereichs](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Die folgenden Parameterwerte werden auch dann nicht in den Eingabefeldern angezeigt, wenn die Vorlage sie angibt. Stattdessen werden im Formular leere Eingabefelder angezeigt, in die Lab-Benutzer beim Erstellen der Umgebung Werte eingeben müssen.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. Wählen Sie **Hinzufügen** aus, um die Umgebung zu erstellen.

   Die Bereitstellung der Umgebung beginnt umgehend, und der Status wird in der Liste **Meine virtuellen Computer** angezeigt. Das Lab erstellt automatisch eine neue Ressourcengruppe, um alle in der Azure Resource Manager-Vorlage definierten Ressourcen bereitzustellen.

1. Wählen Sie die erstellte Umgebung in der Liste **Meine virtuellen Computer** aus, um den Ressourcengruppenbereich zu öffnen, und erkunden Sie alle Ressourcen, die die Umgebung bereitgestellt hat.

   ![Umgebungsressourcen](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Sie können die Umgebung auch erweitern, um lediglich die Liste der VMs anzuzeigen, die die Umgebung bereitgestellt hat.

   ![Liste mit Ihren virtuellen Computern](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Wählen Sie eine der Umgebungen aus, um die verfügbaren Aktionen anzuzeigen. Hierzu zählt beispielsweise das Anwenden von Artefakten, das Anfügen von Datenträgern oder das Anpassen des Zeitpunkts für das automatische Herunterfahren.

   ![Umgebungsaktionen](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatisieren der Umgebungserstellung mit PowerShell

Es ist möglich, im Azure-Portal einem Lab eine einzelne Umgebung hinzuzufügen. Wenn jedoch ein Entwicklungs-oder Testszenario mehrere Umgebungen erstellen muss, ist die automatisierte Bereitstellung eine bessere Lösung.

Bevor Sie fortfahren, stellen Sie sicher, dass eine Azure Resource Manager-Vorlage vorhanden ist, die die zu erstellenden Ressourcen definiert. [Fügen Sie die Vorlage einem Git-Repository hinzu, konfigurieren Sie sie](#configure-your-own-template-repositories), und [fügen Sie das Repository dem Lab hinzu](#add-template-repositories-to-the-lab).

Das folgende Beispielskript erstellt eine Umgebung in Ihrem Lab. Die Kommentare erleichtern Ihnen, das Skript zu verstehen.

1. Speichern Sie das folgende PowerShell-Beispielskript als *deployenv.ps1* auf Ihrer Festplatte.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Führen Sie das Skript wie folgt aus, und verwenden Sie dabei ihre spezifischen Werte für SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (Ordner im Git-Repository) und EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Sie können auch die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen mit Resource Manager-Vorlagen bereitzustellen. Informationen hierzu finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Nur Benutzer mit einer Berechtigung der Art „Labbesitzer“ können VMs aus einer Resource Manager-Vorlage erstellen, indem sie Azure PowerShell nutzen. Wenn Sie die VM-Erstellung per Resource Manager-Vorlage automatisieren möchten und nur über Benutzerberechtigungen verfügen, können Sie den CLI-Befehl [az lab vm create](/cli/azure/lab/vm#az_lab_vm_create) verwenden.

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Einschränkungen der Resource Manager-Vorlage in DevTest Labs

Berücksichtigen Sie diese Einschränkungen bei der Verwendung von Resource Manager-Vorlagen in DevTest Labs:

- Sie können mithilfe von Lab-VMs, die anhand einer Resource Manager-Vorlage erstellt wurden, keine Formeln oder benutzerdefinierten Images erstellen.

- Die meisten Richtlinien werden nicht beachtet, wenn Sie Resource Manager-Vorlagen bereitstellen.

Beispielsweise könnte eine Labrichtlinie festgelegt sein, laut der ein Benutzer nur fünf VMs erstellen kann. Allerdings kann ein Benutzer eine Resource Manager-Vorlage bereitstellen, die Dutzende von virtuellen Computern erstellt. Zu den Richtlinien, die nicht beachtet werden, zählen:

  - Anzahl der VMs pro Benutzer

  - Anzahl der Premium-VMs pro Lab-Benutzer

  - Anzahl der Premium-Datenträger pro Lab-Benutzer

## <a name="next-steps"></a>Nächste Schritte
- Nach der Erstellung einer VM können Sie im Verwaltungsbereich der VM die Option **Verbinden** auswählen, um eine Verbindung mit der VM herzustellen.
- Anzeigen und Verwalten von Ressourcen in einer Umgebung durch Auswählen der Umgebung in der Liste **My virtual machines** (Meine virtuellen Computer) in Ihrem Lab.
- Sehen Sie sich die [Azure Resource Manager-Vorlagen aus dem Katalog mit Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates) an.