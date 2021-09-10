---
title: Einrichten von Previewfunktionen im Azure-Abonnement
description: Beschreibt das Auflisten, Registrieren oder Aufheben der Registrierung von Previewfunktionen in Ihrem Azure-Abonnement für einen Ressourcenanbieter.
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9d7b705e4db7c6556eea4b9fd3cbe1916ec257a4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429921"
---
# <a name="set-up-preview-features-in-azure-subscription"></a>Einrichten von Previewfunktionen im Azure-Abonnement

In diesem Artikel wird gezeigt, wie Sie Previewfunktionen in Ihrem Azure-Abonnement verwalten. Mit Previewfunktionen können Sie neue Funktionen nutzen, bevor sie veröffentlicht werden. Einige Previewfunktionen sind für jeden verfügbar, der sich dafür entscheiden möchte. Andere Previewfunktionen erfordern eine Genehmigung durch das Produktteam.

Azure Feature Exposure Control (AFEC) ist über den Namespace [Microsoft.Features](/rest/api/resources/features) verfügbar. Previewfunktionen haben folgendes Format für die Ressourcen-ID:

`Microsoft.Features/providers/{resourceProviderNamespace}/features/{featureName}`

## <a name="list-preview-features"></a>Auflisten von Previewfunktionen

Sie können alle Previewfunktionen und deren Registrierungszustände für ein Azure-Abonnement auflisten.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Das Portal zeigt nur dann eine Previewfunktion an, wenn sich der Dienst, der die Funktion besitzt, explizit für die Previewfunktionen-Verwaltungserfahrung entschieden hat.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Geben Sie im Suchfeld _Abonnements_ ein, und wählen Sie **Abonnements** aus.

    :::image type="content" source="./media/preview-features/search.png" alt-text="Suche im Azure-Portal.":::

1. Wählen Sie den Link für den Namen Ihres Abonnements aus.

    :::image type="content" source="./media/preview-features/subscriptions.png" alt-text="Azure-Abonnement auswählen.":::

1. Wählen Sie im linken Menü unter **Einstellungen** die Option **Previewfunktionen** aus.

    :::image type="content" source="./media/preview-features/preview-features-menu.png" alt-text="Azure-Menü „Previewfunktionen“.":::

1. Es wird eine Liste der verfügbaren Previewfunktionen mit dem jeweiligen aktuellen Registrierungsstatus angezeigt.

    :::image type="content" source="./media/preview-features/preview-features-list.png" alt-text="Liste der Previewfunktionen im Azure-Portal.":::

1. Unter **Previewfunktionen** können Sie die Liste nach **Name**, **Zustand (State)** und **Typ (Type)** filtern:

    - **Nach Name filtern**: Muss Text aus dem Namen einer Previewfunktion enthalten, nicht den **Anzeigenamen**.
    - **Zustand**: Wählen Sie das Dropdownmenü und dann einen Zustand aus. Das Portal filtert nicht nach **Nicht registriert**.
    - **Typ**: Wählen Sie das Dropdownmenü und dann einen Typ aus.

    :::image type="content" source="./media/preview-features/filter.png" alt-text="Filtern von Previewfunktionen im Azure-Portal.":::

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um alle Previewfunktionen des Abonnements aufzulisten, verwenden Sie den Befehl [az feature list](/cli/azure/feature#az_feature_list).

Das Standardausgabeformat für Azure CLI ist JSON. Weitere Informationen zu weiteren Ausgabeformaten finden Sie unter [Ausgabeformate für Azure CLI-Befehle](/cli/azure/format-output-azure-cli).

```azurecli-interactive
az feature list
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "NotRegistered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

Um die Ausgabe nach einem bestimmten Ressourcenanbieter zu filtern, verwenden Sie den Parameter `namespace`. In diesem Beispiel gibt der Parameter `output` ein Tabellenformat an.

```azurecli-interactive
az feature list --namespace Microsoft.Compute --output table
```

```Output
Name                                                RegistrationState
-------------------------------------------------   -------------------
Microsoft.Compute/AHUB                              Unregistered
Microsoft.Compute/AllowManagedDisksReplaceOSDisk    Registered
Microsoft.Compute/AllowPreReleaseRegions            Pending
Microsoft.Compute/InGuestPatchVMPreview             NotRegistered
```

Um die Ausgabe nach einer bestimmten Previewfunktion zu filtern, verwenden Sie den Befehl [az feature show](/cli/azure/feature#az_feature_show).

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  NotRegistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um alle Previewfunktionen des Abonnements aufzulisten, verwenden Sie das Cmdlet [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature).

```azurepowershell-interactive
Get-AzProviderFeature -ListAvailable
```

```Output
FeatureName      ProviderName     RegistrationState
-----------      ------------     -----------------
betaAccess       Microsoft.AAD    NotRegistered
previewAccess    Microsoft.AAD    Registered
tipAccess        Microsoft.AAD    Pending
testAccess       Microsoft.AAD    Unregistered
```

Um die Ausgabe nach einem bestimmten Ressourcenanbieter zu filtern, verwenden Sie den Parameter `ProviderNamespace`. In der Standardausgabe werden nur die registrierten Funktionen angezeigt. Um alle Previewfunktionen für einen Ressourcenanbieter anzuzeigen, verwenden Sie den Parameter `ListAvailable` zusammen mit dem Parameter `ProviderNamespace`.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -ListAvailable
```

```Output
FeatureName                          ProviderName        RegistrationState
-----------                          ------------        -----------------
AHUB                                 Microsoft.Compute   Unregistered
AllowManagedDisksReplaceOSDisk       Microsoft.Compute   Registered
AllowPreReleaseRegions               Microsoft.Compute   Pending
InGuestPatchVMPreview                Microsoft.Compute   NotRegistered
```

Sie können die Ausgabe nach einer bestimmten Previewfunktion filtern, indem Sie den Parameter `FeatureName` verwenden.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   NotRegistered
```

---

## <a name="register-preview-feature"></a>Registrieren einer Previewfunktion

Registrieren Sie eine Previewfunktion in Ihrem Azure-Abonnement, um weitere Funktionen für einen Ressourcenanbieter verfügbar zu machen. Einige Previewfunktionen erfordern eine Genehmigung.

Nachdem eine Previewfunktion in Ihrem Abonnement registriert wurde, wird einer von zwei Zustände angezeigt: **Registriert** oder **Ausstehend**.

- Für eine Previewfunktion, die keine Genehmigung erfordert, ist der Zustand **Registriert**.
- Wenn eine Previewfunktion eine Genehmigung erfordert, ist der Registrierungszustand **Ausstehend**.
  - Um eine Genehmigung anzufordern, übermitteln Sie eine [Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md).
  - Nachdem die Registrierung genehmigt wurde, ändert sich der Zustand der Previewfunktion in **Registriert**.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Geben Sie im Suchfeld _Abonnements_ ein, und wählen Sie **Abonnements** aus.
1. Wählen Sie den Link für den Namen Ihres Abonnements aus.
1. Wählen Sie im linken Menü unter **Einstellungen** die Option **Previewfunktionen** aus.
1. Wählen Sie den Link für die Previewfunktion aus, die Sie registrieren möchten.
1. Wählen Sie **Registrieren**.

    :::image type="content" source="./media/preview-features/register.png" alt-text="Azure-Portal: Previewfunktion registrieren.":::

1. Klicken Sie auf **OK**.

Der Bildschirm **Previewfunktionen** wird aktualisiert, und der **Zustand** der Previewfunktion wird angezeigt.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Zum Registrieren einer Previewfunktion verwenden Sie den Befehl [az feature register](/cli/azure/feature#az_feature_register).

```azurecli-interactive
az feature register --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Registering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

Um den Status der Registrierung anzuzeigen, verwenden Sie den Befehl `az feature show`.

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Registered
```

> [!NOTE]
> Wenn der Registrierungsbefehl ausgeführt wird, wird eine Meldung angezeigt, dass nach der Registrierung der Funktion `az provider register --namespace <provider-name>` ausgeführt werden soll, um die Änderungen weiterzugeben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um eine Previewfunktion zu registrieren, verwenden Sie das Cmdlet[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature).

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registering
```

Um den Status der Registrierung anzuzeigen, verwenden Sie das Cmdlet `Get-AzProviderFeature`.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registered
```

---

## <a name="unregister-preview-feature"></a>Aufheben der Registrierung einer Previewfunktion

Wenn Sie mit der Verwendung einer Previewfunktion fertig sind, heben Sie deren Registrierung in Ihrem Azure-Abonnement auf. Nach dem Aufheben der Registrierung der Funktion sehen Sie möglicherweise zwei verschiedene Status. Wenn Sie die Registrierung über das Portal aufheben, wird der Status auf **Nicht registriert** festgelegt. Wenn Sie die Registrierung über die Azure CLI, PowerShell oder REST-API aufheben, wird der Status auf **Registrierung aufgehoben** festgelegt. Der Status ist anders, da das Portal die Funktionsregistrierung löscht, die Befehle aber die Registrierung der Funktion aufheben. In beiden Fällen ist die Funktion in Ihrem Abonnement nicht mehr verfügbar. In beiden Fällen können Sie die Funktion erneut aktivieren, indem Sie sie erneut registrieren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Sie können die Registrierung von Previewfunktionen unter **Previewfunktionen** aufheben. Der **Zustand** ändert sich in **Nicht registriert**.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Geben Sie im Suchfeld _Abonnements_ ein, und wählen Sie **Abonnements** aus.
1. Wählen Sie den Link für den Namen Ihres Abonnements aus.
1. Wählen Sie im linken Menü unter **Einstellungen** die Option **Previewfunktionen** aus.
1. Wählen Sie den Link für die Previewfunktion aus, deren Registrierung Sie aufheben möchten.
1. Wählen Sie **Registrierung aufheben** aus.

    :::image type="content" source="./media/preview-features/unregister.png" alt-text="Azure-Portal: Registrierung einer Previewfunktion aufheben.":::

1. Klicken Sie auf **OK**.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Registrierung einer Previewfunktion aufzuheben, verwenden Sie den Befehl [az feature unregister](/cli/azure/feature#az_feature_unregister). Der Zustand `RegistrationState` ändert sich in **Nicht registriert**.

```azurecli-interactive
az feature unregister --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Unregistering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

Um den Status der Registrierungsaufhebung anzuzeigen, verwenden Sie den Befehl `az feature show`.

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

> [!NOTE]
> Wenn der Befehl zur Aufhebung der Registrierung ausgeführt wird, wird eine Meldung angezeigt, dass nach der Aufhebung der Registrierung der Funktion `az provider register --namespace <provider-name>` ausgeführt werden soll, um die Änderungen weiterzugeben.

Um Previewfunktionen zu finden, deren **Registrierung aufgehoben** ist, verwenden Sie den folgenden Befehl. Ersetzen Sie `<ResourceProvider.Name>` durch einen Anbieternamen wie `Microsoft.Compute`.

Im folgenden Beispiel wird eine Previewfunktion mit **aufgehobener Registrierung** für den Ressourcenanbieter `Microsoft.Compute` angezeigt.

```azurecli-interactive
az feature list --namespace <ResourceProvider.Name> --query "[?properties.state=='Unregistered'].{Name:name, RegistrationState:properties.state}" --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Registrierung einer Previewfunktion aufzuheben, verwenden Sie das Cmdlet[Unregister-AzProviderFeature](/powershell/module/az.resources/unregister-azproviderfeature). Der Zustand `RegistrationState` ändert sich in **Nicht registriert**.

```azurepowershell-interactive
Unregister-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistering
```

Um den Status der Registrierungsaufhebung anzuzeigen, verwenden Sie das Cmdlet `Get-AzProviderFeature`.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

Im folgenden Beispiel wird eine Previewfunktion mit **aufgehobener Registrierung** für den Ressourcenanbieter `Microsoft.Compute` angezeigt.

```azurepowershell-interactive
Get-AzProviderFeature  -ProviderNamespace "Microsoft.Compute" -ListAvailable | Where-Object { $_.RegistrationState -eq "Unregistered" }
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

---

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Verwendung von REST-API-Aufrufen sowie zum Auflisten, Registrieren oder Aufheben der Registrierung von Previewfunktionen finden Sie in der Dokumentation der [Features](/rest/api/resources/features).
- Weitere Informationen zum Registrieren eines Ressourcenanbieters finden Sie unter [Azure-Ressourcenanbieter und -typen](resource-providers-and-types.md).
- Eine Liste, die Ressourcenanbieter zu Azure-Diensten zuordnet, finden Sie unter [Ressourcenanbieter für Azure-Dienste](azure-services-resource-providers.md).
