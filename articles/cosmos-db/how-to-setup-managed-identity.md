---
title: Konfigurieren Sie verwaltete Identitäten mit Azure AD für Ihr Azure Cosmos DB-Konto
description: Erfahren Sie, wie Sie verwaltete Identitäten mit Azure Active Directory für Ihr Azure Cosmos DB-Konto konfigurieren
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2021
ms.author: thweiss
ms.openlocfilehash: 826afef5d637278628146af8a35f78232e5b3531
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132919"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Konfigurieren Sie verwaltete Identitäten mit Azure Active Directory für Ihr Azure Cosmos DB-Konto
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Dieser Artikel zeigt, wie Sie eine verwaltete Identität für Azure Cosmos DB-Konten erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie mit verwalteten Identitäten für Azure-Ressourcen noch nicht vertraut sind, lesen Sie [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md). Informationen über verwaltete Identitätstypen finden Sie unter [Verwaltete Identitätstypen](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).
- Um verwaltete Identitäten einzurichten, muss Ihr Konto über die [Rolle DocumentDB Account Contributor](../role-based-access-control/built-in-roles.md#documentdb-account-contributor) verfügen.

## <a name="add-a-system-assigned-identity"></a>Hinzufügen einer systemseitig zugewiesenen Identität

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Um eine vom System zugewiesene verwaltete Identität für ein bestehendes Azure Cosmos DB-Konto zu aktivieren, navigieren Sie zu Ihrem Konto im Azure-Portal und wählen Sie **Identität** aus dem linken Menü.

:::image type="content" source="./media/how-to-setup-managed-identity/identity-tab.png" alt-text="Der Menüeintrag Identität" border="true":::

Stellen Sie unter dem Abschnitt **System zugewiesen** den **Status** auf Ein und wählen Sie **Speichern**. Sie werden aufgefordert, die Erstellung der systemzugewiesenen verwalteten Identität zu bestätigen.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-system-assigned.png" alt-text="Aktivierung einer vom System zugewiesenen Identität" border="true":::

Sobald die Identität erstellt und zugewiesen wurde, können Sie ihre Objekt-ID (Haupt-ID) abrufen.

:::image type="content" source="./media/how-to-setup-managed-identity/system-identity-enabled.png" alt-text="Abrufen der Objekt-ID einer vom System zugewiesenen Identität" border="true":::

### <a name="using-an-azure-resource-manager-arm-template"></a>Verwenden einer Azure-Ressourcenmanager (ARM) Vorlage

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie ein `apiVersion` oder `2021-03-15` höher verwenden, wenn Sie mit verwalteten Identitäten arbeiten.

Wenn Sie eine vom System zugewiesene Identität für ein neues oder vorhandenes Azure Cosmos DB Konto aktivieren möchten, fügen Sie die folgende Eigenschaft in die Ressourcendefinition ein:

```json
"identity": {
    "type": "SystemAssigned"
}
```

Der `resources` Abschnitt Ihrer ARM-Vorlage sollte dann wie folgt aussehen:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
]
```

Sobald Ihr Azure Cosmos DB-Konto erstellt oder aktualisiert wurde, wird es die folgende Eigenschaft anzeigen:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Um beim Erstellen eines neuen Azure Cosmos DB-Kontos eine systemseitig zugewiesene Identität zu aktivieren, fügen Sie die Option `--assign-identity` hinzu:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity
```

Sie können über den Befehl `az cosmosdb identity assign` auch einem vorhandenen Konto eine systemseitig zugewiesene Identität hinzufügen:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
```

Sobald Ihr Azure Cosmos DB-Konto erstellt oder aktualisiert wurde, können Sie die zugewiesene Identität mit dem Befehl `az cosmosdb identity show` abrufen:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity show \
    -n $accountName \
    -g $resourceGroupName
```

```json
{
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="add-a-user-assigned-identity"></a>Hinzufügen einer benutzerseitig zugewiesenen Identität

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Um eine vom Benutzer zugewiesene verwaltete Identität in einem bestehenden Azure Cosmos DB-Konto zu aktivieren, navigieren Sie zu Ihrem Konto im Azure-Portal und wählen Sie **Identität** im linken Menü.

:::image type="content" source="./media/how-to-setup-managed-identity/identity-tab.png" alt-text="Der Menüeintrag Identität" border="true":::

Wählen Sie unter dem Abschnitt **Benutzer zugewiesen** die Optionen **+ Hinzufügen**.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-user-assigned-1.png" alt-text="Aktivierung einer benutzerzugewiesenen Identität" border="true":::

Suchen und wählen Sie alle Identitäten, die Sie Ihrem Azure Cosmos DB-Konto zuweisen möchten, und wählen Sie dann **Hinzufügen**.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-user-assigned-2.png" alt-text="Auswahl aller zuzuweisenden Identitäten" border="true":::

### <a name="using-an-azure-resource-manager-arm-template"></a>Verwenden einer Azure-Ressourcenmanager (ARM) Vorlage

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie ein `apiVersion` oder `2021-03-15` höher verwenden, wenn Sie mit verwalteten Identitäten arbeiten.

Um eine vom Benutzer zugewiesene Identität für ein neues oder bestehendes Azure Cosmos DB-Konto zu aktivieren, fügen Sie die folgende Eigenschaft in die Ressourcendefinition ein:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<identity-resource-id>": {}
    }
}
```

Der `resources` Abschnitt Ihrer ARM-Vorlage sollte dann wie folgt aussehen:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "<identity-resource-id>": {}
            }
        },
        // ...
    },
    // ...
]
```

Nachdem Ihr Azure Cosmos DB Konto erstellt oder aktualisiert wurde, wird die folgende Eigenschaft angezeigt:

```json
"identity": {
    "type": "UserAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Um eine vom Benutzer zugewiesene Identität zu aktivieren, während Sie ein neues Azure Cosmos DB-Konto erstellen, fügen Sie die Option `--assign-identity` hinzu und übergeben die Ressourcen-ID der Identität, die Sie zuweisen möchten:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity <identity-resource-id>
```

Sie können auch eine vom Benutzer zugewiesene Identität zu einem bestehenden Konto hinzufügen, indem Sie den Befehl `az cosmosdb identity assign` verwenden:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
    --identities <identity-resource-id>
```

Nachdem Ihr Azure Cosmos DB-Konto erstellt oder aktualisiert wurde, können Sie die zugewiesene Identität mit dem Befehl `az cosmosdb identity show` abrufen:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity show \
    -n $accountName \
    -g $resourceGroupName
```

```json
{
    "type": "UserAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="remove-a-system-assigned-or-user-assigned-identity"></a>Entfernen einer dem System oder dem Benutzer zugewiesenen Identität

### <a name="using-an-azure-resource-manager-arm-template"></a>Verwenden einer Azure-Ressourcenmanager (ARM) Vorlage

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie ein `apiVersion` oder `2021-03-15` höher verwenden, wenn Sie mit verwalteten Identitäten arbeiten.

Legen Sie den `type` der Eigenschaft `identity` auf `None` fest, um eine systemseitig zugewiesene Identität aus Ihrem Azure Cosmos DB-Konto zu entfernen:

```json
"identity": {
    "type": "None"
}
```

### <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Um alle verwalteten Identitäten aus Ihrem Azure Cosmos DB-Konto zu entfernen, verwenden Sie den Befehl `az cosmosdb identity remove`:

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity remove \
    -n $accountName \
    -g $resourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen finden Sie [hier](../active-directory/managed-identities-azure-resources/overview.md).
- Erfahren Sie mehr über [kundenverwaltete Schlüssel auf Azure Cosmos DB](how-to-setup-cmk.md)
