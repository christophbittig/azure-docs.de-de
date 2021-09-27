---
title: Beispiele für Azure-Rollenzuweisungsbedingungen (Vorschau) – Azure RBAC
titleSuffix: Azure Storage
description: Beispiel für Azure-Rollenzuweisungsbedingungen für die attributbasierte Zugriffssteuerung von Azure (Azure Attribute-Based Access Control, Azure ABAC)
services: storage
author: rolyon
ms.service: storage
ms.topic: conceptual
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: a04c205370ee07900b649ed0a3f6f4bf3a892685
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577822"
---
# <a name="example-azure-role-assignment-conditions-preview"></a>Beispiele für Azure-Rollenzuweisungsbedingungen (Vorschau)

> [!IMPORTANT]
> Azure ABAC und Azure-Rollenzuweisungsbedingungen befinden sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel werden einige Beispiele für Rollenzuweisungsbedingungen aufgeführt.

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu den Voraussetzungen für das Hinzufügen oder Bearbeiten von Rollenzuweisungsbedingungen finden Sie unter [Voraussetzungen für Bedingungen](../../role-based-access-control/conditions-prerequisites.md).

## <a name="example-1-read-access-to-blobs-with-a-tag"></a>Beispiel 1: Lesezugriff auf Blobs mit einem Tag

Diese Bedingung ermöglicht Benutzern das Lesen von Blobs mit dem Blobindex-Tagschlüssel „Project“ und dem Tagwert „Cascade“. Versuche, auf Blobs ohne dieses Schlüssel-Wert-Tag zuzugreifen, werden nicht zugelassen.

> [!TIP]
> Von Blobs wird auch das Speichern beliebiger benutzerdefinierter Schlüssel-Wert-Metadaten unterstützt. Metadaten sind zwar mit Blobindextags vergleichbar, für Bedingungen müssen jedoch Blobindextags verwendet werden. Weitere Informationen finden Sie unter [Verwalten und Finden von Azure-Blobdaten mit Blobindextags (Vorschau)](../blobs/storage-manage-find-blobs.md).

![Diagramm für Beispiel 1: Bedingung für Lesezugriff auf einen Blob mit einem Tag](./media/storage-auth-abac-examples/example-1.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
    )
)
```

#### <a name="azure-portal"></a>Azure-Portal

Diese Einstellungen müssen verwendet werden, um diese Bedingung über das Azure-Portal hinzuzufügen.

| Bedingung 1 | Einstellung |
| --- | --- |
| Aktionen | Inhalt aus einem Blob mit Tagbedingungen lesen |
| Attributquelle | Resource |
| attribute | Blobindextags [Werte in Schlüssel] |
| Schlüssel | {keyName} |
| Operator | StringEquals |
| Wert | {keyValue} |

![Screenshot für Beispiel 1: Bedingungs-Editor im Azure-Portal](./media/storage-auth-abac-examples/example-1-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

So fügen Sie diese Bedingung mithilfe von Azure PowerShell hinzu:

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

So können Sie diese Bedingung testen:

```azurepowershell
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
Get-AzStorageBlob -Container <containerName> -Blob <blobName> -Context $bearerCtx 
```

## <a name="example-2-new-blobs-must-include-a-tag"></a>Beispiel 2: Voraussetzen eines Tags für neue Blobs

Diese Bedingung erfordert, dass alle neuen Blobs den Blobindex-Tagschlüssel „Project“ und den Tagwert „Cascade“ enthalten müssen.

> [!TIP]
> Von Blobs wird auch das Speichern beliebiger benutzerdefinierter Schlüssel-Wert-Metadaten unterstützt. Metadaten sind zwar mit Blobindextags vergleichbar, für Bedingungen müssen jedoch Blobindextags verwendet werden. Weitere Informationen finden Sie unter [Verwalten und Finden von Azure-Blobdaten mit Blobindextags (Vorschau)](../blobs/storage-manage-find-blobs.md).

Es gibt zwei Berechtigungen, mit denen Sie neue Blobs erstellen können. Sie müssen dies also für beide durchführen. Fügen Sie diese Bedingung zu jeder Rollenzuweisung hinzu, die eine der folgenden Berechtigungen besitzt:

- /blobs/write (erstellen oder aktualisieren)
- /blobs/add/action (erstellen)

![Diagramm für Beispiel 2: Bedingung für das Vorhandensein von Tags in neuen Blobs](./media/storage-auth-abac-examples/example-2.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
 )
)
```

#### <a name="azure-portal"></a>Azure-Portal

Diese Einstellungen müssen verwendet werden, um diese Bedingung über das Azure-Portal hinzuzufügen.

| Bedingung 1 | Einstellung |
| --- | --- |
| Aktionen | In ein Blob mit Blobindextags schreiben<br/>Inhalt in ein Blob mit Blobindextags schreiben |
| Attributquelle | Anforderung |
| Attribut | Blobindextags [Werte in Schlüssel] |
| Schlüssel | {keyName} |
| Operator | StringEquals |
| Wert | {keyValue} |

![Screenshot für Beispiel 2: Bedingung 1 im Editor des Azure-Portals](./media/storage-auth-abac-examples/example-2-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

So fügen Sie diese Bedingung mithilfe von Azure PowerShell hinzu:

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

So können Sie diese Bedingung testen:

```azurepowershell
$localSrcFile = # path to an example file, can be an empty txt
$ungrantedTag = @{'Project'='Baker'}
$grantedTag = @{'Project'='Cascade'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example2 -Blob "Example2.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example2 -Blob "Example2.txt" -Tag $grantedTag -Context $bearerCtx
```

## <a name="example-3-existing-blobs-must-have-tag-keys"></a>Beispiel 3: Voraussetzen von Tagschlüsseln für vorhandene Blobs

Diese Bedingung erfordert, dass alle vorhandenen Blobs mit mindestens einem der zulässigen Blobindex-Tagschlüssel (Project oder Program) gekennzeichnet werden. Diese Bedingung ist nützlich, um vorhandene Blobs mit Governance zu ergänzen.

> [!TIP]
> Von Blobs wird auch das Speichern beliebiger benutzerdefinierter Schlüssel-Wert-Metadaten unterstützt. Metadaten sind zwar mit Blobindextags vergleichbar, für Bedingungen müssen jedoch Blobindextags verwendet werden. Weitere Informationen finden Sie unter [Verwalten und Finden von Azure-Blobdaten mit Blobindextags (Vorschau)](../blobs/storage-manage-find-blobs.md).

Es gibt zwei Berechtigungen, mit denen Sie Tags in vorhandenen Blobs aktualisieren können. Sie müssen dies also für beide durchführen. Fügen Sie diese Bedingung zu jeder Rollenzuweisung hinzu, die eine der folgenden Berechtigungen besitzt:

- /blobs/write (aktualisieren oder erstellen, erstellen darf nicht ausgeschlossen werden)
- /blobs/tags/write

![Diagramm für Beispiel 3: Bedingung für das Vorhandensein von Tagschlüsseln in bestehenden Blobs](./media/storage-auth-abac-examples/example-3.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}
 )
)
```

#### <a name="azure-portal"></a>Azure-Portal

Diese Einstellungen müssen verwendet werden, um diese Bedingung über das Azure-Portal hinzuzufügen.

| Bedingung 1 | Einstellung |
| --- | --- |
| Aktionen | In ein Blob mit Blobindextags schreiben<br/>Blobindextags schreiben |
| Attributquelle | Anforderung |
| Attribut | Blobindextags [Schlüssel] |
| Operator | ForAllOfAnyValues:StringEquals |
| Wert | {keyName1}<br/>{keyName2} |

![Screenshot für Beispiel 3: Bedingung 1 im Editor des Azure-Portals](./media/storage-auth-abac-examples/example-3-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

So fügen Sie diese Bedingung mithilfe von Azure PowerShell hinzu:

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&`$keys`$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

So können Sie diese Bedingung testen:

```azurepowershell
$localSrcFile = # path to an example file, can be an empty txt
$ungrantedTag = @{'Mode'='Baker'}
$grantedTag = @{'Program'='Alpine';'Project'='Cascade'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example3 -Blob "Example3.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example3 -Blob "Example3.txt" -Tag $grantedTag -Context $bearerCtx
```

## <a name="example-4-existing-blobs-must-have-a-tag-key-and-values"></a>Beispiel 4: Voraussetzen von Tagschlüssel und Tagwerten in vorhandenen Blobs

Diese Bedingung erfordert, dass alle vorhandenen Blobs über den Blobindex-Tagschlüssel „Project“ und die Tagwerte „Cascade“, „Baker“ oder „Skagit“ verfügen. Diese Bedingung ist nützlich, um vorhandene Blobs mit Governance zu ergänzen.

> [!TIP]
> Von Blobs wird auch das Speichern beliebiger benutzerdefinierter Schlüssel-Wert-Metadaten unterstützt. Metadaten sind zwar mit Blobindextags vergleichbar, für Bedingungen müssen jedoch Blobindextags verwendet werden. Weitere Informationen finden Sie unter [Verwalten und Finden von Azure-Blobdaten mit Blobindextags (Vorschau)](../blobs/storage-manage-find-blobs.md).

Es gibt zwei Berechtigungen, mit denen Sie Tags in vorhandenen Blobs aktualisieren können. Sie müssen dies also für beide durchführen. Fügen Sie diese Bedingung zu jeder Rollenzuweisung hinzu, die eine der folgenden Berechtigungen besitzt:

- /blobs/write (aktualisieren oder erstellen, erstellen darf nicht ausgeschlossen werden)
- /blobs/tags/write

![Diagramm für Beispiel 4: Bedingung für das Vorhandensein von Tagschlüsseln und Tagwerten in bestehenden Blobs](./media/storage-auth-abac-examples/example-4.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAnyOfAnyValues:StringEquals {'Project'}
  AND
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] ForAllOfAnyValues:StringEquals {'Cascade', 'Baker', 'Skagit'}
 )
)
```

#### <a name="azure-portal"></a>Azure-Portal

Diese Einstellungen müssen verwendet werden, um diese Bedingung über das Azure-Portal hinzuzufügen.

| Bedingung 1 | Einstellung |
| --- | --- |
| Aktionen | In ein Blob mit Blobindextags schreiben<br/>Blobindextags schreiben |
| Attributquelle | Anforderung |
| Attribut | Blobindextags [Schlüssel] |
| Operator | ForAnyOfAnyValues:StringEquals |
| Wert | {keyName} |
| Operator | Und |
| **Ausdruck 2** |  |
| Attributquelle | Anforderung |
| Attribut | Blobindextags [Werte in Schlüssel] |
| Schlüssel | {keyName} |
| Operator | ForAllOfAnyValues:StringEquals |
| Wert | {keyValue1}<br/>{keyValue2}<br/>{keyValue3} |

![Screenshot für Beispiel 4: Bedingung 1 im Editor des Azure-Portals](./media/storage-auth-abac-examples/example-4-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

So fügen Sie diese Bedingung mithilfe von Azure PowerShell hinzu:

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&`$keys`$&] ForAnyOfAnyValues:StringEquals {'Project'} AND @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] ForAllOfAnyValues:StringEquals {'Cascade', 'Baker', 'Skagit'}))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

So können Sie diese Bedingung testen:

```azurepowershell
$localSrcFile = <pathToLocalFile>
$ungrantedTag = @{'Project'='Alpine'}
$grantedTag1 = @{'Project'='Cascade'}
$grantedTag2 = @{'Project'='Baker'}
$grantedTag3 = @{'Project'='Skagit'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag1 -Context $bearerCtx
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag2 -Context $bearerCtx
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag3 -Context $bearerCtx
```

## <a name="example-5-read-write-or-delete-blobs-in-named-containers"></a>Beispiel 5: Lesen, Schreiben oder Löschen von Blobs in benannten Containern

Diese Bedingung ermöglicht Benutzern das Lesen, Schreiben oder Löschen von Blobs in Speichercontainern namens blobs-example-container. Diese Bedingung ist nützlich für die Freigabe bestimmter Speichercontainer für andere Benutzer in einem Abonnement.

Es gibt vier Berechtigungen zum Lesen, Schreiben und Löschen vorhandener Blobs. Sie müssen dies also für alle Berechtigungen durchführen. Fügen Sie diese Bedingung zu jeder Rollenzuweisung hinzu, die eine der folgenden Berechtigungen besitzt:

- /blobs/delete
- /blobs/read
- /blobs/write (aktualisieren oder erstellen)
- /blobs/add/action (erstellen)

Untervorgänge werden in dieser Bedingung nicht verwendet, da diese nur benötigt werden, wenn Bedingungen auf Grundlage von Tags erstellt werden.

![Diagramm für Beispiel 5: Bedingung zum Lesen, Schreiben oder Löschen von Blobs in benannten Containern](./media/storage-auth-abac-examples/example-5.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'
 )
)
```

#### <a name="azure-portal"></a>Azure-Portal

Diese Einstellungen müssen verwendet werden, um diese Bedingung über das Azure-Portal hinzuzufügen.

| Bedingung 1 | Einstellung |
| --- | --- |
| Aktionen | Löschen eines Blobs<br/>Blob lesen<br/>In Blob schreiben<br/>Blob oder Momentaufnahme erstellen oder Daten anfügen |
| Attributquelle | Resource |
| attribute | Containername |
| Operator | StringEquals |
| Wert | {containerName} |

![Screenshot für Beispiel 5: Bedingung 1 im Editor des Azure-Portals](./media/storage-auth-abac-examples/example-5-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

So fügen Sie diese Bedingung mithilfe von Azure PowerShell hinzu:

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

So können Sie diese Bedingung testen:

```azurepowershell
$localSrcFile = <pathToLocalFile>
$grantedContainer = "blobs-example-container"
$ungrantedContainer = "ungranted"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Ungranted Container actions
$content = Set-AzStorageBlobContent -File $localSrcFile -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Get-AzStorageBlobContent -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Remove-AzStorageBlob -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
# Granted Container actions
$content = Set-AzStorageBlobContent -File $localSrcFile -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Remove-AzStorageBlob -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
```

## <a name="example-6-read-access-to-blobs-in-named-containers-with-a-path"></a>Beispiel 6: Lesezugriff auf Blobs in benannten Containern mit einem Pfad

Diese Bedingung ermöglicht den Lesezugriff auf Speichercontainer mit dem Namensmuster blobs-example-container und dem Blobpfad readonly/*. Diese Bedingung ist nützlich, wenn andere Benutzer im Abonnement Lesezugriff auf bestimmte Teile von Speichercontainern erhalten sollen.

Fügen Sie diese Bedingung zu jeder Rollenzuweisung hinzu, die die folgende Berechtigung besitzt:

- /blobs/read

![Diagramm für Beispiel 6: Bedingung für Lesezugriff auf Blobs in benannten Containern mit einem Pfad](./media/storage-auth-abac-examples/example-6.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'
        AND
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'
    )
)
```

#### <a name="azure-portal"></a>Azure-Portal

Diese Einstellungen müssen verwendet werden, um diese Bedingung über das Azure-Portal hinzuzufügen.

| Bedingung 1 | Einstellung |
| --- | --- |
| Aktionen | Blob lesen |
| Attributquelle | Resource |
| attribute | Containername |
| Operator | StringEquals |
| Wert | {containerName} |
| **Ausdruck 2** |  |
| Operator | Und |
| Attributquelle | Resource |
| Attribut | Blobpfad |
| Operator | StringLike |
| Wert | {pathString} |

![Screenshot für Beispiel 6: Bedingung 1 im Editor des Azure-Portals](./media/storage-auth-abac-examples/example-6-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

So fügen Sie diese Bedingung mithilfe von Azure PowerShell hinzu:

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' AND @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

So können Sie diese Bedingung testen:

```azurepowershell
$grantedContainer = "blobs-example-container"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to get ungranted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "Ungranted.txt" -Context $bearerCtx
# Try to get granted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "readonly/Example6.txt" -Context $bearerCtx
```

## <a name="example-7-write-access-to-blobs-in-named-containers-with-a-path"></a>Beispiel 7: Schreibzugriff auf Blobs in benannten Containern mit einem Pfad

Mit dieser Bedingung kann ein Partner (ein Azure AD-Gastbenutzer) Dateien in Speichercontainern namens Contosocorp unter dem Pfad uploads/contoso/* ablegen. Diese Bedingung ist nützlich, damit andere Benutzer Daten in Speichercontainern ablegen können.

Fügen Sie diese Bedingung zu jeder Rollenzuweisung hinzu, die die folgenden Berechtigungen besitzt:

- /blobs/write (erstellen oder aktualisieren)
- /blobs/add/action (erstellen)

![Diagramm für Beispiel 7: Bedingung für Schreibzugriff auf Blobs in benannten Containern mit einem Pfad](./media/storage-auth-abac-examples/example-7.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'contosocorp'
  AND
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'uploads/contoso/*'
 )
)
```

#### <a name="azure-portal"></a>Azure-Portal

Diese Einstellungen müssen verwendet werden, um diese Bedingung über das Azure-Portal hinzuzufügen.

| Bedingung 1 | Einstellung |
| --- | --- |
| Aktionen | In Blob schreiben<br/>Blob oder Momentaufnahme erstellen oder Daten anfügen |
| Attributquelle | Resource |
| attribute | Containername |
| Operator | StringEquals |
| Wert | {containerName} |
| **Ausdruck 2** |  |
| Operator | Und |
| Attributquelle | Resource |
| Attribut | Blobpfad |
| Operator | StringLike |
| Wert | {pathString} |

![Screenshot für Beispiel 7: Bedingung 1 im Editor des Azure-Portals](./media/storage-auth-abac-examples/example-7-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

So fügen Sie diese Bedingung mithilfe von Azure PowerShell hinzu:

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'contosocorp' AND @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'uploads/contoso/*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

So können Sie diese Bedingung testen:

```azurepowershell
$grantedContainer = "contosocorp"
$localSrcFile = <pathToLocalFile>
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to set ungranted blob
$content = Set-AzStorageBlobContent -Container $grantedContainer -Blob "Example7.txt" -Context $bearerCtx -File $localSrcFile
# Try to set granted blob
$content = Set-AzStorageBlobContent -Container $grantedContainer -Blob "uploads/contoso/Example7.txt" -Context $bearerCtx -File $localSrcFile
```

## <a name="example-8-read-access-to-blobs-with-a-tag-and-a-path"></a>Beispiel 8: Lesezugriff auf Blobs mit einem Tag und einem Pfad

Diese Bedingung ermöglicht es einem Benutzer, Blobs mit dem Blobindex-Tagschlüssel „Program“, dem Tagwert „Alpine“ und dem Blobpfad logs *zu lesen. Der Blobpfad logs* enthält auch den Blobnamen.

> [!TIP]
> Von Blobs wird auch das Speichern beliebiger benutzerdefinierter Schlüssel-Wert-Metadaten unterstützt. Metadaten sind zwar mit Blobindextags vergleichbar, für Bedingungen müssen jedoch Blobindextags verwendet werden. Weitere Informationen finden Sie unter [Verwalten und Finden von Azure-Blobdaten mit Blobindextags (Vorschau)](../blobs/storage-manage-find-blobs.md).

Fügen Sie diese Bedingung zu jeder Rollenzuweisung hinzu, die die folgende Berechtigung besitzt:

- /blobs/read

![Diagramm für Beispiel 8: Bedingung für Lesezugriff auf einen Blob mit einem Tag und einem Pfad](./media/storage-auth-abac-examples/example-8.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Program<$key_case_sensitive$>] StringEquals 'Alpine'
    )
)
AND
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'logs*'
    )
)
```

#### <a name="azure-portal"></a>Azure-Portal

Diese Einstellungen müssen verwendet werden, um diese Bedingung über das Azure-Portal hinzuzufügen.

| Bedingung 1 | Einstellung |
| --- | --- |
| Aktionen | Inhalt aus einem Blob mit Tagbedingungen lesen |
| Attributquelle | Resource |
| Attribut | Blobindextags [Werte im Schlüssel] |
| Schlüssel | {keyName} |
| Operator | StringEquals |
| Wert | {keyValue} |

![Screenshot für Beispiel 8: Bedingung 1 im Editor des Azure-Portals](./media/storage-auth-abac-examples/example-8-condition-1-portal.png)

| Bedingung 2 | Einstellung |
| --- | --- |
| Aktionen | Blob lesen |
| Attributquelle | Resource |
| Attribut | Blobpfad |
| Operator | StringLike |
| Wert | {pathString} |

![Screenshot für Beispiel 8: Bedingung 2 im Editor des Azure-Portals](./media/storage-auth-abac-examples/example-8-condition-2-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

So fügen Sie diese Bedingung mithilfe von Azure PowerShell hinzu:

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Program<`$key_case_sensitive`$>] StringEquals 'Alpine')) AND ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'logs*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

So können Sie diese Bedingung testen:

```azurepowershell
$grantedContainer = "contosocorp"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to get ungranted blobs
# Wrong name but right tags
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "AlpineFile.txt" -Context $bearerCtx
# Right name but wrong tags
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "logsAlpine.txt" -Context $bearerCtx
# Try to get granted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "logs/AlpineFile.txt" -Context $bearerCtx
```

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Hinzufügen einer Rollenzuweisungsbedingung zum Einschränken des Zugriffs auf Blobs mit dem Azure-Portal (Vorschau)](storage-auth-abac-portal.md)
- [Aktionen und Attribute für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)](storage-auth-abac-attributes.md)
- [Format und Syntax von Azure-Rollenzuweisungsbedingungen (Vorschau)](../../role-based-access-control/conditions-format.md)
