---
title: Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe von PowerShell
description: Konfigurieren von Anforderungen für den Azure VM Image Builder-Dienst einschließlich Berechtigungen und Rechten mithilfe von PowerShell
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/05/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e310a12248ab0f17c66de2561e090125cbec392e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444533"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe von PowerShell

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Wenn Sie sich für die (AIB) registrieren, wird dem AIB-Dienst die Berechtigung zum Erstellen, Verwalten und Löschen einer Stagingressourcengruppe (IT_ *) und zum Hinzufügen von Ressourcen erteilt, die für die Imageerstellung erforderlich sind. Dies erfolgt dadurch, dass im Rahmen einer erfolgreichen Registrierung ein AIB-Dienstprinzipalname (Service Principal Name, SPN) in Ihrem Abonnement verfügbar gemacht wird.

Damit Azure VM Image Builder Images an die verwalteten Images oder an eine Azure Compute Gallery (ehemals Shared Image Gallery) verteilen kann, müssen Sie eine benutzerseitig zugewiesene Azure-Identität erstellen, die über Berechtigungen zum Lesen und Schreiben von Images verfügt. Wenn Sie auf Azure Storage zugreifen, sind dafür Berechtigungen zum Lesen privater oder öffentlicher Container erforderlich.

Vor dem Erstellen eines Images müssen Berechtigungen und Rechte eingerichtet werden. In den folgenden Abschnitten erfahren Sie, wie mögliche Szenarios mithilfe von PowerShell konfiguriert werden.

## <a name="create-an-azure-user-assigned-managed-identity"></a>Erstellen einer in Azure benutzerseitig zugewiesenen verwalteten Identität

Im Azure VM Image Builder-Dienst müssen Sie eine [in Azure benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) erstellen. Der Azure VM Image Builder-Dienst verwendet die benutzerseitig zugewiesene verwaltete Identität, um Images zu lesen, Images zu schreiben und auf Speicherkonten in Azure zuzugreifen. Sie gewähren der Identität die Berechtigung, bestimmte Aktionen für Ihr Abonnement ausführen zu dürfen.

> [!NOTE]
> Bisher hat der Azure VM Image Builder-Dienst die Dienstprinzipalnamen (Service Principal Name, SPN) des Azure VM Image Builder-Diensts verwendet, um Imageressourcengruppen Berechtigungen zu gewähren. Die Verwendung des SPN wird eingestellt. Verwenden Sie stattdessen eine benutzerseitig zugewiesene verwaltete Identität.

Im folgenden Beispiel sehen Sie, wie Sie eine in Azure benutzerseitig zugewiesene verwaltete Identität erstellen. Ersetzen Sie die Platzhaltereinstellungen, um Ihre Variablen festzulegen.

| Einstellung | BESCHREIBUNG |
|---------|-------------|
| \<Resource group\> | Hierbei handelt es sich um die Ressourcengruppe, in der die benutzerseitig verwaltete Identität erstellt werden soll. |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

Weitere Informationen zu in Azure benutzerseitig zugewiesenen Identitäten finden Sie in der Dokumentation [Erstellen, Auflisten oder Löschen einer vom Benutzer zugewiesenen verwalteten Identität mithilfe der Azure-Befehlszeilenschnittstelle](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) im Abschnitt zum Erstellen einer Identität.

## <a name="allow-image-builder-to-distribute-images"></a>Berechtigungen für den Azure VM Image Builder-Dienst zum Verteilen von Images

Damit der Azure VM Image Builder-Dienst Images verteilen kann (Verwaltete Images/Azure Compute Gallery), muss er über die Berechtigung verfügen, die Images in diese Ressourcengruppen einzufügen. Zum Gewähren der erforderlichen Berechtigungen müssen Sie eine benutzerseitig zugewiesene verwaltete Identität erstellen und ihr Rechte für die Ressourcengruppe erteilen, in der das Image erstellt wird. Der Azure VM Image Builder-Dienst hat **keine** Berechtigung, auf Ressourcen in anderen Ressourcengruppen des Abonnements zuzugreifen. Sie müssen explizite Aktionen ausführen, um den Zugriff zu gestatten, damit für Ihre Builds keine Fehler auftreten.

Sie müssen der benutzerseitig zugewiesenen verwalteten Identität keine Berechtigungen eines Mitwirkenden für die Ressourcengruppe zuweisen, damit Images verteilt werden können. Die benutzerseitig zugewiesene verwaltete Identität benötigt jedoch die folgenden `Actions`-Berechtigungen in Azure in der Verteilungsressourcengruppe:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Bei der Verteilung an Azure Compute Gallery benötigen Sie außerdem Folgendes:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Berechtigung zum Anpassen vorhandener Images

Damit der Azure VM Image Builder-Dienst Images aus benutzerdefinierten Quellimages erstellen kann (verwaltete Images/Azure Compute Gallery), muss er über die Berechtigung verfügen, die Images in diesen Ressourcengruppen zu lesen. Zum Gewähren der erforderlichen Berechtigungen müssen Sie eine benutzerseitig zugewiesene verwaltete Identität erstellen und ihr Rechte für die Ressourcengruppe erteilen, in der sich das Image befindet.

So erstellen Sie ein Image aus einem vorhandenen benutzerdefinierten Image:

```Actions
Microsoft.Compute/galleries/read
```

Erstellen aus einer vorhandenen Azure Compute Gallery-Version:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Berechtigung zum Anpassen von Images in Ihren VNETs

Der Azure VM Image Builder-Dienst hat die Funktion, ein vorhandenes VNET in Ihrem Abonnement bereitzustellen und zu verwenden. Insofern ist der Zugriff für Anpassungen für verknüpfte Ressourcen möglich.

Sie müssen der benutzerseitig zugewiesenen verwalteten Identität keine Berechtigungen eines Mitwirkenden für die Ressourcengruppe zuweisen, damit eine VM für ein vorhandenes VNET bereitgestellt werden kann. Die benutzerseitig zugewiesene verwaltete Identität benötigt jedoch die folgenden `Actions`-Berechtigungen in Azure für die VNET-Ressourcengruppe:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Erstellen einer Rollendefinition in Azure

Die folgenden Beispiele erstellen eine Rollendefinition in Azure auf Grundlage der Aktionen, die in den vorherigen Abschnitten beschrieben wurden. Diese Beispiele werden auf Ebene der Ressourcengruppe angewendet. Bewerten und testen Sie, ob die Granularität der Beispiele ausreichend für Ihre Anforderungen ist. Für Ihr Szenario ist möglicherweise eine genauere Abstimmung für eine bestimmte Azure Compute Gallery-Instanz nötig.

Die Aktionen für Images ermöglichen Lese- und Schreibvorgänge. Entscheiden Sie, was für Ihre Umgebung geeignet ist. Erstellen Sie beispielsweise eine Rolle, die es dem Azure VM Image Builder-Dienst ermöglicht, Images aus der Ressourcengruppe *example-rg-1* zu lesen und Images in die Ressourcengruppe *example-rg-2* zu schreiben.

### <a name="custom-image-azure-role-example"></a>Beispiel für eine benutzerdefinierte Rolle für ein Image in Azure

Im folgenden Beispiel wird eine Azure-Rolle erstellt, die für Verwendung und Verteilung eines benutzerdefinierten Quellimages verwendet wird. Dann weisen Sie die benutzerdefinierte Rolle der benutzerseitig zugewiesenen verwalteten Identität für den Azure VM Image Builder-Dienst zu.

Zur Vereinfachung der Ersetzung der Werte im Beispiel legen Sie die folgenden Variablen zuerst fest. Ersetzen Sie die Platzhaltereinstellungen, um Ihre Variablen festzulegen.

| Einstellung | BESCHREIBUNG |
|---------|-------------|
| \<Subscription ID\> | ID Ihres Azure-Abonnements |
| \<Resource group\> | Ressourcengruppe für benutzerdefiniertes Image |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>Beispiel für eine vorhandene VNET-Rolle in Azure

Im folgenden Beispiel wird eine Azure-Rolle erstellt, die für Verwendung und Verteilung eines vorhandenen VNET-Images verwendet wird. Dann weisen Sie die benutzerdefinierte Rolle der benutzerseitig zugewiesenen verwalteten Identität für den Azure VM Image Builder-Dienst zu.

Zur Vereinfachung der Ersetzung der Werte im Beispiel legen Sie die folgenden Variablen zuerst fest. Ersetzen Sie die Platzhaltereinstellungen, um Ihre Variablen festzulegen.

| Einstellung | BESCHREIBUNG |
|---------|-------------|
| \<Subscription ID\> | ID Ihres Azure-Abonnements |
| \<Resource group\> | VNET-Ressourcengruppe |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Übersicht über Azure Image Builder](../image-builder-overview.md).
