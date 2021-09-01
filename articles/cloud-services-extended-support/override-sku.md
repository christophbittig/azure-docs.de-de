---
title: Überschreiben von SKU-Informationen über CSCFG/CSDEF für Azure Cloud Services (erweiterter Support)
description: In diesem Artikel wird beschrieben, wie SKU-Informationen in CSCFG- und CSDEF-Dateien für Azure Cloud Services (erweiterte Unterstützung) überschrieben werden.
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31ff47cd4e110e62769678836bdd803b71369e0b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437877"
---
# <a name="override-sku-settings-in-cscfg-and-csdef-files-for-cloud-services-extended-support"></a>Überschreiben von SKU-Einstellungen in CSCFG- und CSDEF-Dateien für Cloud Services (erweiterte Unterstützung)

In diesem Artikel wird beschrieben, wie Sie die Rollengröße und die Anzahl der Instanzen in Azure Cloud Services mithilfe der **allowModelOverride**-Eigenschaft aktualisieren. Wenn Sie diese Eigenschaft verwenden, müssen Sie die Dienstkonfigurationsdateien (CSCFG-Dateien) und Dienstdefinitionsdateien (CSDEF-Dateien) nicht aktualisieren. Sie können den Clouddienst also hoch-, herunter-, auf- und abskalieren, ohne ihn neu packen und erneut bereitstellen zu müssen.

## <a name="set-the-allowmodeloverride-property"></a>Festlegen der allowModelOverride-Eigenschaft
Sie können die **allowModelOverride**-Eigenschaft auf `true` oder `false` festlegen. 
* Wenn **allowModelOverride** auf `true` festgelegt ist, werden durch den API-Aufruf die Rollengröße und die Anzahl der Instanzen für den Clouddienst aktualisiert, ohne die Werte anhand der CSDEF- und CSCFG-Dateien zu überprüfen. 
   > [!Note]
   > Die CSCFG-Datei wird aktualisiert, um die Anzahl der Rolleninstanzen anzugeben. Die CSDEF-Datei (eingebettet in die CSPKG-Datei) behält die alten Werte bei.

* Wenn **allowModelOverride** auf `false` festgelegt ist, wird durch den API-Aufruf ein Fehler ausgelöst, wenn die Werte für Rollengröße und Anzahl der Instanzen nicht mit den Werten in den CSDEF- und CSCFG-Dateien übereinstimmen.

Der Standardwert ist `false`. Wenn die Eigenschaft auf `false` zurückgesetzt wird, nachdem sie auf `true` festgelegt wurde, werden die CSDEF- und CSCFG-Dateien erneut überprüft.

In den folgenden Beispielen wird gezeigt, wie die **allowModelOverride**-Eigenschaft mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage), mit PowerShell oder mit dem SDK festgelegt wird.

### <a name="arm-template"></a>ARM-Vorlage
Wenn die **allowModelOverride**-Eigenschaft hier auf `true` festgelegt wird, wird der Clouddienst mit den im Abschnitt `roleProfile` definierten Rolleneigenschaften aktualisiert:
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
Wenn Sie den Switch `AllowModelOverride` für das neue Cmdlet `New-AzCloudService` festlegen, wird der Clouddienst mit den im Rollenprofil definierten SKU-Eigenschaften aktualisiert:
```powershell
New-AzCloudService ` 
-Name "ContosoCS" ` 
-ResourceGroupName "ContosOrg" ` 
-Location "East US" `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK
Wenn Sie die Variable `AllowModelOverride` auf `true` festlegen, wird der Clouddienst mit den im Rollenprofil definierten SKU-Eigenschaften aktualisiert:

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate("ContosOrg", "ContosoCS", cloudService);
```
### <a name="azure-portal"></a>Azure-Portal
Das Azure-Portal ermöglicht es Ihnen nicht, die **allowModelOverride**-Eigenschaft zu verwenden, um die Rollengröße und Instanzanzahl in den CSDEF- und CSCFG-Dateien zu überschreiben. 


## <a name="next-steps"></a>Nächste Schritte 
- Anzeigen der [Bereitstellungsvoraussetzungen](deploy-prerequisite.md) für Cloud Services (erweiterter Support).
- Anzeigen [häufig gestellter Fragen](faq.yml) zu Cloud Services (erweiterter Support).
