---
title: Finden von Informationen zu Verschlüsselungsschlüsseln
titleSuffix: Azure Cognitive Search
description: Rufen Sie den Namen und die in einem Index oder einer Synonymzuordnung verwendete Verschlüsselungsschlüsselversion ab, um den Schlüssel in Azure Key Vault verwalten zu können.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/21/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2f34d653a698a7ef2ee3dee21d46345ed9a7301a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003816"
---
# <a name="find-encrypted-objects-and-information"></a>Finden von verschlüsselten Objekten und Informationen

Bei Azure Cognitive Search werden kundenseitig verwaltete Verschlüsselungsschlüssel erstellt, gespeichert und in Azure Key Vault verwaltet. Wenn Sie ermitteln müssen, ob ein Objekt verschlüsselt ist oder welcher Schlüsselname oder welche Version in Azure Key Vault verwendet wurde, rufen Sie die Eigenschaft **encryptionKey** über die REST-API oder ein SDK aus der Objektdefinition in Ihrem Suchdienst ab.

Objekte, die nicht mit einem kundenseitig verwalteten Schlüssel verschlüsselt sind, weisen eine leere **encryptionKey**-Eigenschaft auf. Andernfalls wird Ihnen möglicherweise eine Definition ähnlich dem folgenden Beispiel angezeigt.

```json
"encryptionKey": {
"keyVaultUri": "https://demokeyvault.vault.azure.net",
"keyVaultKeyName": "myEncryptionKey",
"keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
"accessCredentials": {
    "applicationId": "00000000-0000-0000-0000-000000000000",
    "applicationSecret": "myApplicationSecret"
    }
}
```

Das **encryptionKey**-Konstrukt ist für alle verschlüsselten Objekte identisch. Hierbei handelt es sich um eine Eigenschaft der ersten Ebene, die sich auf derselben Ebene wie Name und Beschreibung befindet.

## <a name="get-the-admin-api-key"></a>Abrufen des Admin-API-Schlüssels

Bevor Sie Objektdefinitionen aus einem Suchdienst abrufen können, müssen Sie einen Administrator-API-Schlüssel bereitstellen. Administrator-API-Schlüssel sind für Anforderungen erforderlich, die Objektdefinitionen und Metadaten abfragen. Der einfachste Weg, den Admin-API-Schlüssel zu erhalten, ist über das Portal.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und öffnen Sie die Übersichtsseite des Suchdiensts.

1. Klicken Sie auf der linken Seite auf **Schlüssel**, und kopieren Sie eine Admin-API. Zum Abrufen von Index und Synonymzuordnungen ist ein Administratorschlüssel erforderlich.

Wechseln Sie für die restlichen Schritte zu PowerShell und der REST-API. Im Portal werden keine Verschlüsselungsschlüsselinformationen für Objekte angezeigt.

## <a name="retrieve-object-properties"></a>Abrufen von Objekteigenschaften

Führen Sie in PowerShell und REST die folgenden Befehle aus, um die Variablen einzurichten und Objektdefinitionen abzurufen. 

Alternativ können Sie auch die Azure SDKs für [.NET](/dotnet/api/azure.search.documents.indexes.searchindexclient.getindexes), [Python](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient), [JavaScript](/javascript/api/@azure/search-documents/searchindexclient) und [Java](/java/api/com.azure.search.documents.indexes.searchindexclient.getindex) verwenden.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Nächste Schritte

Wir empfehlen die [Aktivierung der Protokollierung](../key-vault/general/logging.md) in Azure Key Vault, damit Sie die Schlüsselverwendung überwachen können.

Weitere Informationen zur Verwendung von Azure Key Vault oder zum Konfigurieren der kundenseitig verwalteten Verschlüsselung finden Sie hier:

+ [Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe von PowerShell](../key-vault/secrets/quick-create-powershell.md)

+ [Konfigurieren von kundenseitig verwalteten Schlüsseln für die Datenverschlüsselung in Azure Cognitive Search](search-security-manage-encryption-keys.md)
