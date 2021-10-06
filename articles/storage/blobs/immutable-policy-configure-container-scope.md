---
title: Konfigurieren von Unveränderlichkeitsrichtlinien für Container
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie eine Unveränderlichkeitsrichtlinie konfigurieren, die für einen Container gilt. Unveränderlichkeitsrichtlinien bieten WORM-Unterstützung (Write Once, Read Many) für Blob Storage, indem Daten in einem nicht löschbaren, nicht änderbaren Zustand gespeichert werden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a28699af7167bfbc6ffd9a00d64b4fbfd593693
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389378"
---
# <a name="configure-immutability-policies-for-containers"></a>Konfigurieren von Unveränderlichkeitsrichtlinien für Container

Unveränderlicher Speicher für Azure Blob Storage ermöglicht es Benutzern, unternehmenskritische Daten in einem WORM-Zustand (Write Once, Read Many) zu speichern. Im WORM-Zustand können Daten für einen vom Benutzer angegebenen Zeitraum nicht geändert oder gelöscht werden. Durch Konfigurieren von Unveränderlichkeitsrichtlinien für Blobdaten können Sie Ihre Daten vor Überschreibungen und Löschungen schützen. Unveränderlichkeitsrichtlinien umfassen zeitbasierte Aufbewahrungsrichtlinien und Aufbewahrungrichtlinien für juristische Zwecke. Weitere Informationen zu Unveränderlichkeitsrichtlinien für Blob Storage finden Sie unter [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](immutable-storage-overview.md).

Eine Unveränderlichkeitsrichtlinie kann entweder auf eine einzelne Blobversion (Vorschau) oder auf einen Container beschränkt werden. In diesem Artikel wird beschrieben, wie Sie eine Unveränderlichkeitsrichtlinie auf Containerebene konfigurieren. Informationen zum Konfigurieren von Unveränderlichkeitsrichtlinien auf Versionsebene finden Sie unter [Konfigurieren von Unveränderlichkeitsrichtlinien für Blobversionen (Vorschau)](immutable-policy-configure-version-scope.md).

## <a name="configure-a-retention-policy-on-a-container"></a>Konfigurieren einer Aufbewahrungsrichtlinie für einen Container

Zum Konfigurieren einer zeitbasierten Aufbewahrungsrichtlinie für einen Container verwenden Sie das Azure-Portal, die PowerShell oder die Azure CLI. Sie können eine Aufbewahrungsrichtlinie auf Containerebene für eine Dauer von 1 bis 146.000 Tagen konfigurieren.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Zum Konfigurieren einer zeitbasierten Aufbewahrungsrichtlinie für einen Container über das Azure-Portal führen Sie die folgenden Schritte aus:

1. Navigieren Sie zum gewünschten Container.
1. Wählen Sie die Schaltfläche **Mehr** auf der rechten Seite und dann **Zugriffsrichtlinie** aus.
1. Wählen Sie im Abschnitt **Unveränderlicher Blobspeicher** die Option **Richtlinie hinzufügen** aus.
1. Wählen Sie für das Feld **Richtlinientyp** den Typ **Zeitbasierte Aufbewahrung** aus, und geben Sie den Aufbewahrungszeitraum in Tagen an.
1. Um eine Richtlinie mit Containergültigkeitsbereich zu erstellen, aktivieren Sie nicht das Kontrollkästchen **Unveränderlichkeit auf Versionsebene aktivieren**.
1. Wählen Sie bei Bedarf **Zusätzliche geschützte Anfügevorgänge zulassen** aus, um Schreibvorgänge zum Anfügen von Blobs zu aktivieren, die durch eine Unveränderlichkeitsrichtlinie geschützt sind. Weitere Informationen finden Sie unter [Zulassen von Schreibvorgängen in geschützten Anfügeblobs](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).

    :::image type="content" source="media/immutable-policy-configure-container-scope/configure-retention-policy-container-scope.png" alt-text="Screenshot, der das Konfigurieren einer für einen Container gültigen Unveränderlichkeitsrichtlinie zeigt.":::

Nachdem Sie die Unveränderlichkeitsrichtlinie konfiguriert haben, sehen Sie, dass sich ihr Gültigkeitsbereich auf den Container erstreckt:

:::image type="content" source="media/immutable-policy-configure-container-scope/view-retention-policy-container-scope.png" alt-text="Screenshot, der eine vorhandene Unveränderlichkeitsrichtlinie zeigt, die für den Container gilt.":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um eine zeitbasierte Aufbewahrungsrichtlinie für einen Container mit PowerShell zu konfigurieren, rufen Sie den Befehl [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) auf, und geben Sie das Aufbewahrungsintervall in Tagen an. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 10
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um eine zeitbasierte Aufbewahrungsrichtlinie für einen Container mit Azure CLI zu konfigurieren, rufen Sie den Befehl [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) auf, und geben Sie das Aufbewahrungsintervall in Tagen an. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage container immutability-policy create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period 10
```

---

## <a name="modify-an-unlocked-retention-policy"></a>Ändern einer nicht gesperrten Aufbewahrungsrichtlinie

Sie können eine nicht gesperrte, zeitbasierte Aufbewahrungsrichtlinie ändern, um das Aufbewahrungsintervall zu verkürzen oder zu verlängern und um zusätzliche Schreibvorgänge zum Anfügen von Blobs im Container zuzulassen. Außerdem können Sie eine nicht gesperrte Richtlinie löschen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um eine nicht gesperrte, zeitbasierte Aufbewahrungsrichtlinie im Azure-Portal zu ändern:

1. Navigieren Sie zum gewünschten Container.
1. Wählen Sie die Schaltfläche **Mehr** und dann **Zugriffsrichtlinie** aus.
1. Suchen Sie im Abschnitt **Unveränderliche Blobversionen** die vorhandene, nicht gesperrte Richtlinie. Wählen Sie die Schaltfläche **Mehr** und dann **Bearbeiten** im Menü aus.
1. Geben Sie ein neues Aufbewahrungsintervall für die Richtlinie an. Sie können auch **Zusätzliche geschützte Anfügevorgänge zulassen** auswählen, um Schreibvorgänge in geschützte Anfügeblobs zuzulassen.

    :::image type="content" source="media/immutable-policy-configure-container-scope/modify-retention-policy-container-scope.png" alt-text="Screenshot, der zeigt, wie Sie eine nicht gesperrten, zeitbasierte Aufbewahrungsrichtlinie ändern.":::

Um eine nicht gesperrte Richtlinie zu löschen, wählen Sie die Schaltfläche **Mehr** und dann **Löschen** aus.

> [!NOTE]
> Sie können Unveränderlichkeitsrichtlinien auf Versionsebene (Vorschau) aktivieren, indem Sie das Kontrollkästchen „Unveränderlichkeit auf Versionsebene aktivieren“ aktivieren. Weitere Informationen zum Aktivieren von Unveränderlichkeitsrichtlinien auf Versionsebene finden Sie unter [Konfigurieren von Unveränderlichkeitsrichtlinien für Blobversionen (Vorschau)](immutable-policy-configure-version-scope.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um eine nicht gesperrte Richtlinie zu ändern, rufen Sie zuerst die Richtlinie ab, indem Sie den Befehl [Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy) aufrufen. Als Nächstes rufen Sie den Befehl [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) auf, um die Richtlinie zu aktualisieren. Schließen Sie das neue Aufbewahrungsintervall in Tagen und den Parameter `-ExtendPolicy` ein. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 21 `
    -AllowProtectedAppendWrite true `
    -Etag $policy.Etag `
    -ExtendPolicy
```

Um eine nicht gesperrte Richtlinie zu löschen, rufen Sie den Befehl [Remove-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/remove-azrmstoragecontainerimmutabilitypolicy) auf.

```azurepowershell
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um eine nicht gesperrte, zeitbasierte Aufbewahrungsrichtlinie mit Azure CLI zu ändern, rufen Sie den Befehl [az storage container immutability-policy extend](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_extend) auf, und geben Sie das neue Aufbewahrungsintervall in Tagen an. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
$etag=$(az storage container immutability-policy show \
        --account-name <storage-account> \
        --container-name <container> \
        --query etag \
        --output tsv)

az storage container immutability-policy extend \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period 21 \
    --if-match $etag \
    --allow-protected-append-writes true
```

Um eine nicht gesperrte Richtlinie zu löschen, rufen Sie den Befehl [az storage container immutability-policy delete](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_delete) auf.

---

## <a name="lock-a-time-based-retention-policy"></a>Sperren einer zeitbasierten Aufbewahrungsrichtlinie

Wenn Sie eine zeitbasierte Aufbewahrungsrichtlinie abschließend getestet haben, können Sie die Richtlinie sperren. Eine gesperrte Richtlinie ist mit SEC 17a-4(f) und anderen gesetzlichen Bestimmungen konform. Sie können das Aufbewahrungsintervall für eine gesperrte Richtlinie bis zu fünfmal verlängern, aber nicht verkürzen.

Nachdem eine Richtlinie gesperrt wurde, können Sie diese nicht mehr löschen. Sie können das Blob jedoch löschen, nachdem das Aufbewahrungsintervall abgelaufen ist.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um eine Richtlinie über das Azure-Portal zu sperren:

1. Navigieren Sie zu einem Container mit einer nicht gesperrten Richtlinie.
1. Suchen Sie im Abschnitt **Unveränderliche Blobversionen** die vorhandene, nicht gesperrte Richtlinie. Wählen Sie die Schaltfläche **Mehr** und dann **Richtlinie sperren** im Menü aus.
1. Bestätigen Sie, dass Sie die Richtlinie sperren möchten.

:::image type="content" source="media/immutable-policy-configure-container-scope/lock-retention-policy.png" alt-text="Screenshot: Sperren einer zeitbasierten Aufbewahrungsrichtlinie im Azure-Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um eine Richtlinie mit PowerShell zu sperren, rufen Sie zunächst den Befehl [Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy) auf, um das ETag der Richtlinie abzurufen. Rufen Sie als Nächstes den Befehl [Lock-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/lock-azrmstoragecontainerimmutabilitypolicy) auf, und übergeben Sie den ETag-Wert, um die Richtlinie zu sperren. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container> `
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um eine Richtlinie mit Azure CLI zu sperren, rufen Sie zunächst den Befehl [az storage container immutability-policy show](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_show) auf, um das ETag der Richtlinie abzurufen. Rufen Sie als Nächstes den Befehl [az storage container immutability-policy lock](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_lock) auf, und übergeben Sie den ETag-Wert, um die Richtlinie zu sperren. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
$etag=$(az storage container immutability-policy show \
        --account-name <storage-account> \
        --container-name <container> \
        --query etag \
        --output tsv)

az storage container immutability-policy lock \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --if-match $etag
```

---

## <a name="configure-or-clear-a-legal-hold"></a>Konfigurieren oder Löschen einer Aufbewahrungrichtlinie für juristische Zwecke

Bei einer Aufbewahrungrichtlinie für juristische Zwecke werden unveränderliche Daten gespeichert, bis die Aufbewahrungrichtlinie für juristische Zwecke explizit gelöscht wird. Weitere Informationen zu Aufbewahrungsrichtlinien für juristische Zwecke finden Sie im Artikel zur [Aufbewahrung von unveränderlichen Blobdaten für juristische Zwecke](immutable-legal-hold-overview.md).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um im Azure-Portal eine Aufbewahrungsrichtlinie für juristische Zwecke für einen Container zu konfigurieren:

1. Navigieren Sie zum gewünschten Container.
1. Wählen Sie die Schaltfläche **Mehr** und dann **Zugriffsrichtlinie** aus.
1. Wählen Sie im Abschnitt **Unveränderliche Blobversionen** die Option **Richtlinie hinzufügen** aus.
1. Wählen Sie **Aufbewahrungrichtlinie für juristische Zwecke** als Richtlinientyp und dann **OK** aus, um die Auswahl zu übernehmen.

Die folgende Abbildung zeigt einen Container, für den sowohl eine zeitbasierte Aufbewahrungsrichtlinie als auch eine Aufbewahrungrichtlinie für juristische Zwecke konfiguriert ist.

:::image type="content" source="media/immutable-policy-configure-container-scope/retention-policy-legal-hold-container-scope.png" alt-text="Screenshot, der einen Container zeigt, für den sowohl eine zeitbasierte Aufbewahrungsrichtlinie als auch eine Aufbewahrungrichtlinie für juristische Zwecke konfiguriert ist.":::

Navigieren Sie zum Löschen einer Aufbewahrungrichtlinie für juristische Zwecke zum Dialogfeld **Zugriffsrichtlinie**, und wählen Sie die Schaltfläche **Mehr** und dann **Löschen** aus.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um mit der PowerShell eine Aufbewahrungrichtlinie für juristische Zwecke für einen Container zu konfigurieren, rufen Sie den Befehl [Add-AzRmStorageContainerLegalHold](/powershell/module/az.storage/add-azrmstoragecontainerlegalhold) auf. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
Add-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -Tag <tag1>,<tag2>,...
```

Um eine Aufbewahrungrichtlinie für juristische Zwecke zu löschen, rufen Sie den Befehl [Remove-AzRmStorageContainerLegalHold](/powershell/module/az.storage/remove-azrmstoragecontainerlegalhold) auf:

```azurepowershell
Remove-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -Tag <tag1>,<tag2>,...
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um mit der Azure CLI eine Aufbewahrungrichtlinie für juristische Zwecke für einen Container zu konfigurieren, rufen Sie den Befehl [az storage container legal-hold set](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_set) auf. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage container legal-hold set \
    --tags tag1 tag2 \
    --container-name <container> \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

Um eine Aufbewahrungrichtlinie für juristische Zwecke zu löschen, rufen Sie den Befehl [az storage container legal-hold clear](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_clear) auf:

```azurecli
az storage container legal-hold clear \
    --tags tag1 tag2 \
    --container-name <container> \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](immutable-storage-overview.md)
- [Zeitbasierte Aufbewahrungsrichtlinien für unveränderliche Blobdaten](immutable-time-based-retention-policy-overview.md)
- [Gesetzliche Aufbewahrungspflichten für unveränderliche Blobdaten](immutable-legal-hold-overview.md)
- [Konfigurieren von Unveränderlichkeitsrichtlinien für Blobversionen (Vorschau)](immutable-policy-configure-version-scope.md)
