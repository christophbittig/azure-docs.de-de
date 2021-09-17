---
title: 'Azure PowerShell-Beispiele: Aktualisieren einer Instanz von Azure Cloud Services (erweiterter Support)'
description: Beispielskripts zum Aktualisieren von Azure Cloud Services-Bereitstellungen (erweiterter Support)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 22d02ef7c395ae6f7cdbbf739ed5107436bd169c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751092"
---
# <a name="update-an-azure-cloud-service-extended-support"></a>Aktualisieren eines Azure-Clouddiensts (erweiterter Support)

In diesen Beispielen werden verschiedene Möglichkeiten zum Aktualisieren einer vorhandenen Azure Cloud Services-Bereitstellung (erweiterter Support) behandelt.

## <a name="add-an-extension-to-existing-cloud-service"></a>Hinzufügen einer Erweiterung zu einer vorhandenen Cloud Services-Instanz
Die folgenden Befehle fügen eine RDP-Erweiterung zu einem bereits bestehenden Clouddienst namens ContosoCS hinzu, der zur Ressourcengruppe ContosOrg gehört.
```powershell
# Create RDP extension object
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Add RDP extension to existing cloud service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $rdpExtension
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-all-extensions-from-a-cloud-service"></a>Entfernen aller Erweiterungen aus einer Cloud Services-Instanz
Die folgenden Befehle entfernen alle Erweiterungen aus dem bestehenden Clouddienst namens ContosoCS, der zur Ressourcengruppe ContosOrg gehört.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Set extension to empty list
$cloudService.ExtensionProfile.Extension = @()
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-the-remote-desktop-extension-from-cloud-service"></a>Entfernen der Remotedesktoperweiterung aus Cloud Services
Die folgenden Befehle entfernen die RDP-Erweiterung aus dem bestehenden Clouddienst namens ContosoCS, der zur Ressourcengruppe ContosOrg gehört.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Remove extension by name RDPExtension
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension | Where-Object { $_.Name -ne "RDPExtension" }
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="scale-out--scale-in-role-instances"></a>Horizontales Skalieren/Abskalieren von Rolleninstanzen
Die folgenden Befehle zeigen, wie die Anzahl der Rolleninstanzen für den Clouddienst namens ContosoCS, der zur Ressourcengruppe ContosOrg gehört, ab- und aufskaliert werden kann.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Scale-out all role instance count by 1
$cloudService.RoleProfile.Role | ForEach-Object {$_.SkuCapacity += 1}

# Scale-in ContosoFrontend role instance count by 1
$role = $cloudService.RoleProfile.Role | Where-Object {$_.Name -eq "ContosoFrontend"}
$role.SkuCapacity -= 1

# Update cloud service configuration as per the new role instance count
$cloudService.Configuration = $configuration

# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Cloud Services (erweiterter Support) finden Sie unter [Informationen zu Azure Cloud Services (erweiterter Support)](overview.md).
- Besuchen Sie das [Beispielrepository zu Cloud Services (erweiterter Support)](https://github.com/Azure-Samples/cloud-services-extended-support).
