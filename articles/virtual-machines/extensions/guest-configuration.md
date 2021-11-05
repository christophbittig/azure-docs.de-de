---
title: Gastkonfigurationserweiterung
description: Hier erfahren Sie mehr über die Erweiterung, die zum Überprüfen/Konfigurieren von Einstellungen auf virtuellen Computern verwendet wird.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 25e167f80ff209a60b30113ffe681e033cc612e1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131046525"
---
# <a name="overview-of-the-guest-configuration-extension"></a>Übersicht über die Gastkonfigurationserweiterung

Die Gastkonfigurationserweiterung ist eine Azure Policy-Komponente, die Überprüfungs- und Konfigurationsvorgänge auf virtuellen Computern ausführt.
Richtlinien wie Definitionen von Sicherheitsbaselines für [Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) und [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) können Einstellungen auf Computern erst überprüfen, wenn die Erweiterung installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

Damit sich der Computer beim Gastkonfigurationsdienst authentifizieren kann, muss der Computer über eine [vom System zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) verfügen.
Die Identitätsanforderung auf einem virtuellen Computer ist erfüllt, wenn die folgende Eigenschaft festgelegt ist.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>Betriebssysteme

Die Unterstützung für die Gastkonfigurationserweiterung ist identisch mit der Betriebssystemunterstützung, die für [die End-to-End-Lösung dokumentiert](../../governance/policy/concepts/guest-configuration.md#supported-client-types) ist.

### <a name="internet-connectivity"></a>Internetkonnektivität

Der von der Gastkonfigurationserweiterung installierte Agent muss Inhaltspakete erreichen können, die unter Gastkonfigurationszuweisungen aufgeführt sind, und den Status an den Gastkonfigurationsdienst melden.
Der Computer kann eine Verbindung über ausgehenden HTTPS-Datenverkehr über TCP-Port 443 herstellen, oder, wenn eine Verbindung über private Netzwerke bereitgestellt wird.
Weitere Informationen zu privaten Netzwerken finden Sie in folgenden Artikeln:

- [Gastkonfiguration: Kommunikation über private Verbindung in Azure](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Verwenden privater Endpunkte für Azure Storage](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Wie kann ich eine Erweiterung installieren?

Der Instanzname der Erweiterung muss auf „AzurePolicyforWindows“ oder „AzurePolicyforLinux“ festgelegt werden, da die oben genannten Richtlinien diese spezifischen Zeichenfolgen erfordern.

Standardmäßig werden alle Bereitstellungen auf die neueste Version aktualisiert. Der Wert der Eigenschaft _autoUpgradeMinorVersion_ ist standardmäßig auf „true“ festgelegt, sofern nichts anderes angegeben ist. Sie müssen sich keine Gedanken über das Aktualisieren Ihres Codes machen, wenn neue Versionen der Erweiterung veröffentlicht werden.

## <a name="automatic-upgrade"></a>automatische Upgrade

Die Gastkonfigurationserweiterung unterstützt die Eigenschaft `enableAutomaticUpgrade`. Wenn diese Eigenschaft auf `true` festgelegt ist, führt Azure automatisch ein Upgrade auf die neueste Version der Erweiterung durch, sobald zukünftige Releases verfügbar werden. Weitere Informationen finden Sie auf der Seite [Automatisches Erweiterungsupgrade für VMs und Skalierungsgruppen in Azure](../automatic-extension-upgrade.md).

### <a name="azure-policy"></a>Azure Policy

Zum Bereitstellen der neuesten Version der Erweiterung einschließlich der Identitätsanforderungen in großem Umfang [weisen Sie die Azure-Richtlinie zu](../../governance/policy/assign-policy-portal.md):

[Schaffen Sie die Voraussetzungen, um Richtlinien für die Gastkonfiguration auf virtuellen Computern zu aktivieren](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json).

### <a name="azure-cli"></a>Azure CLI

So stellen Sie die Erweiterung für Linux bereit:


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM --enable-auto-upgrade true
```

So stellen Sie die Erweiterung für Windows bereit:

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM --enable-auto-upgrade true
```

### <a name="powershell"></a>PowerShell

So stellen Sie die Erweiterung für Linux bereit:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM' -EnableAutomaticUpgrade $true
```

So stellen Sie die Erweiterung für Windows bereit:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM' -EnableAutomaticUpgrade $true
```

### <a name="resource-manager-template"></a>Resource Manager-Vorlage

So stellen Sie die Erweiterung für Linux bereit:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2020-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "enableAutomaticUpgrade": true, 
    "settings": {},
    "protectedSettings": {}
  }
}
```

So stellen Sie die Erweiterung für Windows bereit:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2020-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "enableAutomaticUpgrade": true, 
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="bicep"></a>Bicep

So stellen Sie die Erweiterung für Linux bereit:

```bicep
resource virtualMachine 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: 'VMName'
}
resource windowsVMGuestConfigExtension 'Microsoft.Compute/virtualMachines/extensions@2020-12-01' = {
  parent: virtualMachine
  name: 'AzurePolicyforLinux'
  location: resourceGroup().location
  properties: {
    publisher: 'Microsoft.GuestConfiguration'
    type: 'ConfigurationforLinux'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    enableAutomaticUpgrade: true
    settings: {}
    protectedSettings: {}
  }
}
```

So stellen Sie die Erweiterung für Windows bereit:

```bicep
resource virtualMachine 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: 'VMName'
}
resource windowsVMGuestConfigExtension 'Microsoft.Compute/virtualMachines/extensions@2020-12-01' = {
  parent: virtualMachine
  name: 'AzurePolicyforWindows'
  location: resourceGroup().location
  properties: {
    publisher: 'Microsoft.GuestConfiguration'
    type: 'ConfigurationforWindows'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    enableAutomaticUpgrade: true
    settings: {}
    protectedSettings: {}
  }
}
```

### <a name="terraform"></a>Terraform

So stellen Sie die Erweiterung für Linux bereit:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                       = "AzurePolicyforLinux"
  virtual_machine_id         = "myVMID"
  publisher                  = "Microsoft.GuestConfiguration"
  type                       = "ConfigurationforLinux"
  type_handler_version       = "1.0"
  auto_upgrade_minor_version = "true"
}
```

So stellen Sie die Erweiterung für Windows bereit:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                       = "AzurePolicyforWindows"
  virtual_machine_id         = "myVMID"
  publisher                  = "Microsoft.GuestConfiguration"
  type                       = "ConfigurationforWindows"
  type_handler_version       = "1.0"
  auto_upgrade_minor_version = "true"
}
```

## <a name="settings"></a>Einstellungen

Es ist nicht erforderlich, Einstellungen oder Eigenschaften mit geschützten Einstellungen in die Erweiterung aufzunehmen.
Alle diese Informationen werden vom Agent aus Ressourcen für die [Gastkonfigurationszuweisung](/rest/api/guestconfiguration/guestconfigurationassignments) abgerufen. Beispielsweise werden die Eigenschaften [ConfigurationUri](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation), [Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode) und [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) pro Konfiguration und nicht in der VM-Erweiterung verwaltet.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Azure Policy-Gastkonfiguration finden Sie in den [Informationen zur Gastkonfiguration von Azure Policy](../../governance/policy/concepts/guest-configuration.md).
* Weitere Informationen zur Funktionsweise des Linux-Agents und der Erweiterungen finden Sie unter [Informationen zu Erweiterungen und Features für virtuelle Computer für Linux](features-linux.md).
* Weitere Informationen zur Funktionsweise des Windows-Gast-Agents und der Erweiterungen finden Sie unter [Azure VM-Erweiterungen und -Features für Windows](features-windows.md).  
* Informationen zur Installation des Windows-Gast-Agents finden Sie unter [Azure Virtual Machine Agent overview](agent-windows.md) (Übersicht über den Agent für virtuelle Azure-Computer).  
* Informationen zur Installation des Linux-Agents finden Sie unter [Understanding and using the Azure Linux Agent](agent-linux.md) (Grundlegendes zum Azure Linux-Agent und seiner Verwendung).  
