---
title: Erstellen eines benutzerdefinierten Images aus einer VHD-Datei mit Azure PowerShell
description: Automatisieren Sie das Erstellen von benutzerdefinierten Images in Azure DevTest-Labs aus VHD-Dateien mithilfe von PowerShell.
ms.topic: how-to
ms.date: 10/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2185309194b04d1b76ca84daea19e92e7c017463
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469041"
---
# <a name="create-a-custom-image-from-a-vhd-file-with-powershell"></a>Erstellen eines benutzerdefinierten Images aus einer VHD-Datei mithilfe von PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="powershell-steps"></a>PowerShell-Schritte

Die folgenden Schritte führen Sie durch die Erstellung eines benutzerdefinierten Images aus einer VHD-Datei mithilfe von Azure PowerShell:

1. Um sich bei Eingabeaufforderung seitens PowerShell in Ihrem Azure-Konto anzumelden, verwenden Sie das Cmdlet **Connect-AzAccount**:

   ```powershell
   Connect-AzAccount
   ```

1. Wählen Sie mit dem Cmdlet **Select-AzSubscription** Ihr gewünschtes Azure-Abonnement aus. Ersetzen Sie \<subscription ID> durch Ihr Abonnement-ID-GUID.

   ```powershell
   $subscriptionId = '<subscription ID>'
   Select-AzSubscription -SubscriptionId $subscriptionId
   ```

1. Rufen Sie das Lab-Objekt durch Aufrufen des Cmdlets **Get-AzResource** ab. Ersetzen Sie die Platzhalter \<lab resource group name> und \<lab name> durch Ihren eigenen Ressourcengruppen- und Lab-Namen.

   ```powershell
   $labRg = '<lab resource group name>'
   $labName = '<lab name>'
   $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
   ```

1. Ersetzen Sie den folgenden Platzhalter für die Variable **$vhdUri** durch den URI Ihrer hochgeladenen VHD-Datei. Sie können den URI der VHD-Datei von der Blobseite im Speicherkonto des Labs im Azure-Portal abrufen. Ein Beispiel für einen VHD-URI ist: `https://acontosolab1234.blob.core.windows.net/uploads/myvhd.vhd`.

   ```powershell
   $vhdUri = '<VHD URI>'
   ```

1. Erstellen Sie das benutzerdefinierte Image mit dem Cmdlet **New-AzResourceGroupDeployment**. Ersetzen Sie die Platzhalter \<custom image name> und \<custom image description> durch den von Ihnen gewünschten Namen und gewünschte Beschreibung.

   ```powershell
   $customImageName = '<custom image name>'
   $customImageDescription = '<custom image description>'

   $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

   New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
   ```

## <a name="complete-powershell-script"></a>Vollständiges PowerShell-Skript

Die Kombination der vorherigen Schritte erzeugt das folgende PowerShell-Skript, das ein benutzerdefiniertes Image aus einer VHD-Datei erstellt. Um das Skript zu verwenden, ersetzen Sie die folgenden Platzhalter durch Ihre eigenen Werte:

- \<subscription ID>
- \<lab resource group name>
- \<lab name>
- \<VHD URI>
- \<custom image name>
- \<custom image description>

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<subscription ID>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<lab resource group name>'
$labName = '<lab name>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<VHD URI>'

# Set the custom image name and description values.
$customImageName = '<custom image name>'
$customImageDescription = '<custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="next-steps"></a>Nächste Schritte

- [Vergleich zwischen benutzerdefinierten Images und Formeln in DevTest Labs](devtest-lab-comparing-vm-base-image-types.md)
- [Copying Custom Images between Azure DevTest Labs (Kopieren benutzerdefinierter Images zwischen Azure DevTest Labs)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)
