---
title: Bereitstellen eines virtuellen Computers mit vertrauenswürdigem Start
description: Stellen Sie einen virtuellen Computer mit vertrauenswürdigem Start bereit.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 10/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 0212c21a90caa533003d2304cfded72eb2370b49
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286049"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled"></a>Bereitstellen eines virtuellen Computers mit aktiviertem vertrauenswürdigem Start

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen

Der [vertrauenswürdige Start](trusted-launch.md) ist eine Möglichkeit zur Verbesserung der Sicherheit von VMs der [Generation 2](generation-2.md). Der vertrauenswürdige Start schützt vor erweiterten und permanenten Angriffstechniken, indem Infrastrukturtechnologien wie vTPM und sicherer Start kombiniert werden.

## <a name="prerequisites"></a>Voraussetzungen 

- Sie müssen [Ihr Abonnement in Microsoft Defender für Cloud integrieren](https://azure.microsoft.com/services/security-center/?&ef_id=CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE:G:s&OCID=AID2200277_SEM_CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE:G:s&gclid=CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE#overview), falls dies noch nicht erfolgt ist. Microsoft Defender für Cloud verfügt über ein kostenloses Kontingent, das sehr nützliche Hinweise für unterschiedliche Azure- und Hybridressourcen bietet. Beim vertrauenswürdigen Start wird Defender für Cloud genutzt, um eine Reihe von Empfehlungen zur Integrität der virtuellen Computer zu erhalten. 

- Weisen Sie Ihrem Abonnement Azure-Richtlinieninitiativen zu. Diese Richtlinieninitiativen müssen nur einmal pro Abonnement zugewiesen werden. Dadurch werden automatisch alle erforderlichen Erweiterungen auf allen unterstützten VMs installiert. 
    - Konfigurieren Sie die Voraussetzungen, um den Gastnachweises für virtuelle Computer mit „vertrauenswürdigem Start“ zu aktivieren 

    - Computer zur automatischen Installation der Azure Monitor- und Azure Security-Agents auf VMs konfigurieren 

 
## <a name="deploy-a-trusted-vm"></a>Bereitstellen einer vertrauenswürdigen VM
Erstellen Sie einen virtuellen Computer, für den vertrauenswürdiger Start aktiviert ist. Wählen Sie eine der folgenden Option aus:

### <a name="portal"></a>[Portal](#tab/portal)

1. Melden Sie sich beim Azure-[Portal](https://portal.azure.com) an.
2. Suchen Sie nach **Virtuellen Computern**.
3. Wählen Sie unter **Dienste** die Option **Virtuelle Computer** aus.
4. Wählen Sie auf der Seite **Virtuelle Computer** die Option **Hinzufügen** und dann **Virtueller Computer** aus.
5. Stellen Sie sicher, dass unter **Projektdetails** das richtige Abonnement ausgewählt ist.
6. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie einen Namen für Ihre Ressourcengruppe an, oder wählen Sie in der Dropdownliste eine vorhandene Ressourcengruppe aus.
7. Geben Sie unter **Details zur Instanz** einen Namen für den virtuellen Computer ein, und wählen Sie eine Region aus, die den [vertrauenswürdigen Start](trusted-launch.md#limitations) unterstützt.
1. Wählen Sie als **Sicherheitstyp** die Option **Vertrauenswürdiger Start von virtuellen Computern** aus. Dadurch werden zwei weitere Optionen angezeigt: **Sicherer Start** und **vTPM**. Wählen Sie die passenden Optionen für Ihre Bereitstellung aus.
    :::image type="content" source="media/trusted-launch/security.png" alt-text="Screenshot: Optionen für vertrauenswürdigen Start":::
3. Wählen Sie unter **Image** ein Image aus den **empfohlenen Gen 2-Images mit Kompatibilität für vertrauenswürdigen Start** aus. Eine Liste finden Sie unter [Images, die vertrauenswürdigen Start unterstützen](trusted-launch.md#limitations). 
   > [!TIP]
   > Sollte die Gen 2-Version des gewünschten Images in der Dropdownliste nicht angezeigt werden, wählen Sie **Alle Images anzeigen** aus und ändern Sie dann den Filter **Sicherheitstyp** auf **vertrauenswürdiger Start**.
13. Wählen Sie eine VM-Größe, die den vertrauenswürdigen Start unterstützt. Die Liste der unterstützten Größen finden Sie [hier](trusted-launch.md#limitations).
14. Geben Sie die Informationen für das **Administratorkonto** und dann die **Regeln für eingehende Ports** ein.
15. Wählen Sie unten auf der Seite die Option **Überprüfen + erstellen** aus.
16. Auf der Seite zum **Erstellen eines virtuellen Computers** werden die Details des virtuellen Computers angezeigt, den Sie bereitstellen möchten. Klicken Sie auf **Erstellen**, sobald die Anzeige Überprüfung erfolgreich absolviert angezeigt wird.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Sceenshot der Validierungsseite, die anzeigt, dass die Optionen für den vertrauenswürdigen Start enthalten sind.":::


Die Bereitstellung des virtuellen Computers dauert ein paar Minuten.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Achten Sie darauf, dass Sie die aktuelle Version von Azure CLI verwenden. 

Melden Sie sich mit `az login` bei Azure an.  

```azurecli-interactive
az login 
```

Erstellen Sie einen virtuellen Computer mit vertrauenswürdigem Start. 

```azurecli-interactive
az group create -n myresourceGroup -l eastus 
az vm create \ 
   --resource-group myResourceGroup \ 
   --name myVM \ 
   --image UbuntuLTS \ 
   --admin-username azureuser \ 
   --generate-ssh-keys \ 
   --SecurityType trustedLaunch \ 
   --EnableSecureBoot true \  
   --EnableVtpm true \
```
 
Für vorhandene VMs können Sie die Einstellungen für den sicheren Start und vTPM aktivieren oder deaktivieren. Das Aktualisieren des virtuellen Computers mit den Einstellungen für sicheren Start und vTPM löst einen automatischen Neustart aus.

```azurecli-interactive
az vm update \  
   --resource-group myResourceGroup \ 
   --name myVM \ 
   --EnableSecureBoot \  
   --EnableVtpm 
```  

### <a name="powershell"></a>[PowerShell](#tab/powershell)


Um einen virtuellen Computer mit vertrauenswürdigem Start bereitstellen zu können, muss er zunächst mithilfe des Cmdlets `Set-AzVmSecurityType` mit `TrustedLaunch` aktiviert werden. Anschließend können Sie das Cmdlet Set-AzVmUefi verwenden, um die Konfiguration von vTPM und sicherem Start festzulegen. Verwenden Sie den folgenden Codeausschnitt als Schnellstart. Denken Sie daran, die Werte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen. 

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "West US"
$vmName = "myTrustedVM"
$vmSize = Standard_B2s
$publisher = "MicrosoftWindowsServer"
$offer = "WindowsServer"
$sku = "2019-Datacenter"
$version = latest
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize 

$vm = Set-AzVMOperatingSystem `
   -VM $vm -Windows `
   -ComputerName $vmName `
   -Credential $cred `
   -ProvisionVMAgent `
   -EnableAutoUpdate 

$vm = Add-AzVMNetworkInterface -VM $vm `
   -Id $NIC.Id 

$vm = Set-AzVMSourceImage -VM $vm `
   -PublisherName $publisher `
   -Offer $offer `
   -Skus $sku `
   -Version $version 

$vm = Set-AzVMOSDisk -VM $vm `
   -StorageAccountType "StandardSSD_LRS" `
   -CreateOption "FromImage" 

$vm = Set-AzVmSecurityType -VM $vm `
   -SecurityType "TrustedLaunch" 

$vm = Set-AzVmUefi -VM $vm `
   -EnableVtpm $true `
   -EnableSecureBoot $true 

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vm 
```
 


### <a name="template"></a>[Vorlage](#tab/template)

Sie können VMs für den vertrauenswürdigen Start mithilfe einer Schnellstartvorlage bereitstellen:

**Linux**

[![In Azure bereitstellen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**

[![In Azure bereitstellen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2FcreateUiDefinition.json)

---

## <a name="verify-or-update-your-settings"></a>Überprüfen oder Aktualisieren Ihrer Einstellungen

Sie können die Konfiguration für den vertrauenswürdigen Start für einen vorhandenen virtuellen Computer anzeigen, indem Sie die Seite  **Übersicht**  für den virtuellen Computer im Portal aufrufen. In der Registerkarte **Eigenschaften** wird der Status der Eigenschaften für den vertrauenswürdigen Start angezeigt:

:::image type="content" source="media/trusted-launch/overview-properties.png" alt-text="Screenshot: Eigenschaften des virtuellen Computers für den vertrauenswürdigen Start":::

Um die Konfiguration des vertrauenswürdigen Starts zu ändern, wählen Sie im Menü auf der linken Seite  im Abschnitt  **Einstellungen**  die Option  **Konfiguration** aus. Sie können Sicherer Start und vTPM im Abschnitt Sicherheitstyp: Vertrauenswürdiger Start aktivieren oder deaktivieren. Wählen Sie abschließend im oberen Bereich der Seite Speichern aus. 

:::image type="content" source="media/trusted-launch/update.png" alt-text="Screenshot: Kontrollkästchen zum Ändern der Einstellungen für den vertrauenswürdigen Start":::

Wenn der virtuelle Computer ausgeführt wird, erhalten Sie eine Meldung, dass der virtuelle Computer neu gestartet wird. Wählen Sie  **Ja**  aus und warten Sie, bis die VM neu gestartet wird, damit die Änderungen wirksam werden. 


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über VMs für [vertrauenswürdigen Start](trusted-launch.md) und [Generation 2](generation-2.md)-VMs.
