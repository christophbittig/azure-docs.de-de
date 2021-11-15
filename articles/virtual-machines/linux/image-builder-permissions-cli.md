---
title: Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe der Azure CLI
description: Konfigurieren von Anforderungen für den Azure VM Image Builder-Dienst einschließlich Berechtigungen und Rechten mithilfe der Azure CLI
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 04/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 3f20276c8cb1f3b777f3cbb1400559cfe85294bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428252"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe der Azure CLI

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Wenn Sie sich für die (AIB) registrieren, wird dem AIB-Dienst die Berechtigung zum Erstellen, Verwalten und Löschen einer Stagingressourcengruppe (IT_ *) und zum Hinzufügen von Ressourcen erteilt, die für die Imageerstellung erforderlich sind. Dies erfolgt dadurch, dass im Rahmen einer erfolgreichen Registrierung ein AIB-Dienstprinzipalname (Service Principal Name, SPN) in Ihrem Abonnement verfügbar gemacht wird.

Damit Azure VM Image Builder Images an die verwalteten Images oder an eine Azure Compute Gallery (ehemals Shared Image Gallery) verteilen kann, müssen Sie eine benutzerseitig zugewiesene Azure-Identität erstellen, die über Berechtigungen zum Lesen und Schreiben von Images verfügt. Wenn Sie auf Azure Storage zugreifen, sind dafür Berechtigungen zum Lesen privater oder öffentlicher Container erforderlich.

Vor dem Erstellen eines Images müssen Berechtigungen und Rechte eingerichtet werden. In den folgenden Abschnitten erfahren Sie, wie mögliche Szenarios mithilfe der Azure CLI konfiguriert werden.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-azure-user-assigned-managed-identity"></a>Erstellen einer in Azure benutzerseitig zugewiesenen verwalteten Identität

Im Azure VM Image Builder-Dienst müssen Sie eine [in Azure benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) erstellen. Der Azure VM Image Builder-Dienst verwendet die benutzerseitig zugewiesene verwaltete Identität, um Images zu lesen, Images zu schreiben und auf Speicherkonten in Azure zuzugreifen. Sie gewähren der Identität die Berechtigung, bestimmte Aktionen für Ihr Abonnement ausführen zu dürfen.

> [!NOTE]
> Bisher hat der Azure VM Image Builder-Dienst die Dienstprinzipalnamen (Service Principal Name, SPN) des Azure VM Image Builder-Diensts verwendet, um Imageressourcengruppen Berechtigungen zu gewähren. Die Verwendung des SPN wird eingestellt. Verwenden Sie stattdessen eine benutzerseitig zugewiesene verwaltete Identität.

Im folgenden Beispiel sehen Sie, wie Sie eine in Azure benutzerseitig zugewiesene verwaltete Identität erstellen. Ersetzen Sie die Platzhaltereinstellungen, um Ihre Variablen festzulegen.

| Einstellung | BESCHREIBUNG |
|---------|-------------|
| \<Resource group\> | Hierbei handelt es sich um die Ressourcengruppe, in der die benutzerseitig verwaltete Identität erstellt werden soll. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
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

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=$(az account show --query id --output tsv)
# Resource group - image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Beispiel für eine vorhandene VNET-Rolle in Azure

Im folgenden Beispiel wird eine Azure-Rolle erstellt, die für Verwendung und Verteilung eines vorhandenen VNET-Images verwendet wird. Dann weisen Sie die benutzerdefinierte Rolle der benutzerseitig zugewiesenen verwalteten Identität für den Azure VM Image Builder-Dienst zu.

Zur Vereinfachung der Ersetzung der Werte im Beispiel legen Sie die folgenden Variablen zuerst fest. Ersetzen Sie die Platzhaltereinstellungen, um Ihre Variablen festzulegen.

| Einstellung | BESCHREIBUNG |
|---------|-------------|
| \<Subscription ID\> | ID Ihres Azure-Abonnements |
| \<Resource group\> | VNET-Ressourcengruppe |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=$(az account show --query id --output tsv)
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Verwenden der verwalteten Identität für den Zugriff auf Azure Storage

Wenn Sie eine nahtlose Authentifizierung mit Azure Storage und die Verwendung privater Container wünschen, benötigen Azure VM Image Builder und Azure eine benutzerseitig zugewiesene verwaltete Identität. Azure VM Image Builder verwenden die Identität für die Authentifizierung mit Azure Storage.

> [!NOTE]
> Azure VM Image Builder verwendet die Identität nur zum Zeitpunkt der Übermittlung der Imagevorlage. Die erstellte VM verfügt während der Imageerstellung nicht über Zugriff auf die Identität.

Verwenden Sie die Azure CLI, um die benutzerseitig zugewiesene verwaltete Identität zu erstellen.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

In der Azure VM Image Builder-Vorlage müssen Sie die benutzerseitig zugewiesene verwaltete Identität angeben.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Ersetzen Sie die folgenden Platzhaltereinstellungen:

| Einstellung | BESCHREIBUNG |
|---------|-------------|
| \<Region\> | Region der Vorlage |
| \<Resource group\> | Resource group |
| \<Storage account container\> | Name des Containers mit dem Speicherkonto |
| \<Subscription ID\> | Azure-Abonnement |

Weitere Informationen zur Verwendung einer benutzerseitig zugewiesenen verwalteten Identität finden Sie unter [Erstellen eines benutzerdefinierten Images mit einer benutzerseitig zugewiesenen verwaltete Identität in Azure für den nahtlosen Zugriff auf Azure Storage-Dateien](./image-builder-user-assigned-identity.md). In diesem Schnellstart durchlaufen Sie das Erstellen und Konfigurieren der benutzerseitig zugewiesenen verwalteten Identität für den Zugriff auf ein Speicherkonto.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Übersicht über Azure Image Builder](../image-builder-overview.md).
