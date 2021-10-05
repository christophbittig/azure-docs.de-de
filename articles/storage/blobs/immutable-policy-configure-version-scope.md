---
title: Konfigurieren von Unveränderlichkeitsrichtlinien für Blobversionen (Vorschau)
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie eine Unveränderlichkeitsrichtlinie konfigurieren, die für eine Blobversion gilt (Vorschau). Unveränderlichkeitsrichtlinien bieten WORM-Unterstützung (Write Once, Read Many) für Blob Storage, indem Daten in einem nicht löschbaren, nicht änderbaren Zustand gespeichert werden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/10/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 7a7ded3df993034963f06b81a0908e68821688cb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584292"
---
# <a name="configure-immutability-policies-for-blob-versions-preview"></a>Konfigurieren von Unveränderlichkeitsrichtlinien für Blobversionen (Vorschau)

Unveränderlicher Speicher für Azure Blob Storage ermöglicht es Benutzern, unternehmenskritische Daten in einem WORM-Zustand (Write Once, Read Many) zu speichern. Im WORM-Zustand können Daten für einen vom Benutzer angegebenen Zeitraum nicht geändert oder gelöscht werden. Durch Konfigurieren von Unveränderlichkeitsrichtlinien für Blobdaten können Sie Ihre Daten vor Überschreibungen und Löschungen schützen. Unveränderlichkeitsrichtlinien umfassen zeitbasierte Aufbewahrungsrichtlinien und Aufbewahrungrichtlinien für juristische Zwecke. Weitere Informationen zu Unveränderlichkeitsrichtlinien für Blob Storage finden Sie unter [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](immutable-storage-overview.md).

Eine Unveränderlichkeitsrichtlinie kann entweder auf eine einzelne Blobversion (Vorschau) oder auf einen Container beschränkt werden. In diesem Artikel wird beschrieben, wie Sie eine Unveränderlichkeitsrichtlinie auf Versionsebene konfigurieren. Informationen zum Konfigurieren von Unveränderlichkeitsrichtlinien auf Containerebene finden Sie unter [Konfigurieren von Unveränderlichkeitsrichtlinien für Container](immutable-policy-configure-container-scope.md).

Das Konfigurieren einer Unveränderlichkeitsrichtlinie auf Versionsebene ist ein zweistufiger Prozess:

1. Aktivieren Sie zunächst die Unterstützung der Unveränderlichkeit auf Versionsebene für einen neuen oder vorhandenen Container. Ausführliche Informationen finden Sie unter [Aktivieren der Unterstützung der Unveränderlichkeit auf Versionsebene für einen Container](#enable-support-for-version-level-immutability-on-a-container).
1. Konfigurieren Sie als Nächstes eine zeitbasierte Aufbewahrungsrichtlinie oder eine Aufbewahrungrichtlinie für juristische Zwecke, die für eine oder mehrere Blobversionen in diesem Container gilt.

> [!IMPORTANT]
> Unveränderlichkeitsrichtlinien auf Versionsebene befinden sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren von zeitbasierten Aufbewahrungsrichtlinien auf Versionsebene muss die Blobversionsverwaltung für das Speicherkonto aktiviert werden. Informationen zum Aktivieren der Blobversionsverwaltung finden Sie unter [Aktivieren und Verwalten der Blobversionsverwaltung (Vorschau)](versioning-enable.md).

Informationen zu unterstützten Speicherkontokonfigurationen für Unveränderlichkeitsrichtlinien auf Versionsebene finden Sie unter [Unterstützte Kontokonfigurationen](immutable-storage-overview.md#supported-account-configurations).

## <a name="enable-support-for-version-level-immutability-on-a-container"></a>Aktivieren der Unterstützung der Unveränderlichkeit auf Versionsebene für einen Container

Damit Sie eine zeitbasierte Aufbewahrungsrichtlinie auf eine Blobversion anwenden können, müssen Sie die Unterstützung der Unveränderlichkeit auf Versionsebene aktivieren. Es ist möglich, sowohl neue als auch vorhandene Container so zu konfigurieren, dass die Unveränderlichkeit auf Versionsebene unterstützt wird. Ein vorhandener Container muss jedoch einem Migrationsprozess unterzogen werden, um die Unterstützung zu aktivieren.

Beachten Sie, dass durch die Aktivierung der Unterstützung der Unveränderlichkeit auf Versionsebene für einen Container keine die Daten in diesem Container nicht unveränderlich werden. Sie müssen darüber hinaus entweder eine Standardunveränderlichkeitsrichtlinie für den Container oder eine Unveränderlichkeitsrichtlinie für eine bestimmte Blobversion konfigurieren.

### <a name="enable-version-level-immutability-for-a-new-container"></a>Aktivieren der Unveränderlichkeit auf Versionsebene für einen neuen Container

Um eine Unveränderlichkeitsrichtlinie auf Versionsebene zu verwenden, müssen Sie zunächst explizit die WORM-Unterstützung auf Versionsebene für den Container aktivieren. Sie können die WORM-Unterstützung auf Versionsebene entweder beim Erstellen des Containers oder beim Hinzufügen einer Unveränderlichkeitsrichtlinie auf Versionsebene zu einem vorhandenen Container aktivieren.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um im Azure-Portal einen Container zu erstellen, der Unveränderlichkeit auf Versionsebene unterstützt:

1. Navigieren Sie im Azure-Portal zur Seite **Container** für Ihr Speicherkonto, und wählen Sie **Hinzufügen** aus.
1. Geben Sie im Dialogfeld **Neuer Container** einen Namen für den Container ein, und erweitern Sie dann den Abschnitt **Erweitert**.
1. Wählen Sie **Unterstützung der Unveränderlichkeit auf Versionsebene aktivieren** aus, um die Unveränderlichkeit auf Versionsebene für den Container zu aktivieren.

    :::image type="content" source="media/immutable-policy-configure-version-scope/create-container-version-level-immutability.png" alt-text="Screenshot: Erstellen eines Containers mit aktivierter Unveränderlichkeit auf Versionsebene":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um einen Container zu erstellen, der Unveränderlichkeit auf Versionsniveau mit PowerShell unterstützt, installieren Sie zunächst das [Modul Az.Storage](https://www.powershellgallery.com/packages/Az.Storage/3.10.1-preview), Version 3.10.1-Preview.

Rufen Sie anschließend den Befehl **New-AzRmStorageContainer** mit dem Parameter `-EnableImmutableStorageWithVersioning` auf, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Platzhalter in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
# Create a container with version-level immutability support.
$container = New-AzRmStorageContainer -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -EnableImmutableStorageWithVersioning

# Verify that version-level immutability support is enabled for the container
$container.ImmutableStorageWithVersioning
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um einen Container zu erstellen, der die Unveränderbarkeit auf Versionsniveau mit Azure CLI unterstützt, installieren Sie zunächst Azure CLI Version 2.27 oder höher. Weitere Informationen zur Installation von Azure CLI finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

Rufen Sie anschließend den Befehl [az storage container-rm create](/cli/azure/storage/container-rm#az_storage_container_rm_create) auf und geben Sie dabei den Parameter `--enable-vlw` an. Denken Sie daran, die Platzhalter in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
# Create a container with version-level immutability support.
az storage container-rm create \
    --name <container> \
    --storage-account <storage-account> \
    --resource-group <resource-group> \
    --enable-vlw

# Verify that version-level immutability support is enabled for the container
az storage container-rm show \
    --storage-account <storage-account> \
    --name <container> \
    --query '[immutableStorageWithVersioning.enabled]' \
    --output tsv
```

---

### <a name="migrate-an-existing-container-to-support-version-level-immutability"></a>Migrieren eines vorhandenen Containers zur Unterstützung der Unveränderlichkeit auf Versionsebene

Zum Konfigurieren von Unveränderlichkeitsrichtlinien auf Versionsebene für einen vorhandenen Container müssen Sie den Container migrieren, damit ein unveränderlicher Speicher auf Versionsebene unterstützt wird. Die Containermigration kann einige Zeit in Anspruch nehmen und kann nicht rückgängig gemacht werden.

Um einen vorhandenen Container zur Unterstützung von Unveränderlichkeitsrichtlinien auf Versionsebene zu migrieren, muss für den Container eine zeitbasierte Aufbewahrungsrichtlinie auf Containerebene konfiguriert sein. Die Migration schlägt fehl, es sei denn, der Container verfügt über eine vorhandene Richtlinie. Das Aufbewahrungsintervall für die Richtlinie auf Containerebene wird als Aufbewahrungsintervall für die Standardrichtlinie auf Versionsebene für den Container beibehalten.

Wenn im Container eine Aufbewahrungrichtlinie für juristische Zwecke auf Containerebene vorhanden ist, kann er erst migriert werden, nachdem die Aufbewahrungrichtlinie für juristische Zwecke entfernt wurde.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um einen Container zur Unterstützung von unveränderlichem Speicher auf Versionsebene im Azure-Portal zu migrieren:

1. Navigieren Sie zum gewünschten Container.
1. Wählen Sie die Schaltfläche **Mehr** auf der rechten Seite und dann **Zugriffsrichtlinie** aus.
1. Wählen Sie unter **Unveränderlicher Blobspeicher** die Option **Richtlinie hinzufügen** aus.
1. Wählen Sie für das Feld **Richtlinientyp** den Typ *Zeitbasierte Aufbewahrung* aus, und geben Sie das Aufbewahrungsintervall an.
1. Wählen Sie **Unveränderlichkeit auf Versionsebene aktivieren** aus.
1. Wählen Sie **OK** aus, um eine Richtlinie auf Containerebene mit dem angegebenen Aufbewahrungszeitraum zu erstellen, und beginnen Sie dann mit der Migration zur Unterstützung der Unveränderlichkeit auf Versionsebene.

    :::image type="content" source="media/immutable-policy-configure-version-scope/migrate-existing-container.png" alt-text="Screenshot: Migration eines vorhandenen Containers zur Unterstützung der Unveränderlichkeit auf Versionsebene":::

Während der Migration wird der Bereich der Richtlinie für den Container als *Container* angezeigt.

:::image type="content" source="media/immutable-policy-configure-version-scope/container-migration-in-process.png" alt-text="Screenshot von Containermigration im Gange":::

Nach Abschluss der Migration wird der Bereich der Richtlinie für den Container als *Version* angezeigt. Die angezeigte Richtlinie ist eine Standardrichtlinie für den Container, die automatisch für alle Blobversionen gilt, die anschließend im Container erstellt wurden. Die Standardrichtlinie kann für jede Version überschrieben werden, indem eine benutzerdefinierte Richtlinie für diese Version angegeben wird.

:::image type="content" source="media/immutable-policy-configure-version-scope/container-migration-complete.png" alt-text="Screenshot von abgeschlossener Containermigration":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um einen Container zu migrieren, um unveränderlichen Speicher auf Versionsniveau mit PowerShell zu unterstützen, stellen Sie zunächst sicher, dass eine zeitbasierte Aufbewahrungsrichtlinie auf Containerebene für den Container existiert. Rufen Sie [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) auf, um eine zu erstellen.

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
   -StorageAccountName <storage-account> `
   -ContainerName <container> `
   -ImmutabilityPeriod <retention-interval-in-days>
```

Rufen Sie als Nächstes den Befehl **Invoke-AzRmStorageContainerImmutableStorageWithVersioningMigration** auf, um den Container zu migrieren. Fügen Sie den Parameter `-AsJob` hinzu, um den Befehl asynchron auszuführen. Die asynchrone Ausführung des Vorgangs wird empfohlen, da die Migration einige Zeit in Anspruch nehmen kann.

```azurepowershell
$migrationOperation = Invoke-AzRmStorageContainerImmutableStorageWithVersioningMigration `
    -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -AsJob
```

Lesen Sie die **JobStateInfo.State**-Eigenschaft des Vorgangs, um den Status des Vorgangs mit langer Laufzeit zu überprüfen.

```azurepowershell
$migrationOperation.JobStateInfo.State
```

Wenn der Container keine zeitbasierte Aufbewahrungsrichtlinie besitzt, wenn Sie versuchen, zur Unveränderlichkeit auf Versionsebene zu migrieren, schlägt der Vorgang fehl. Im folgenden Beispiel wird der Wert der **JobStateInfo.State-Eigenschaft** überprüft und die Fehlermeldung angezeigt, wenn der Vorgang fehlgeschlagen ist, weil die Richtlinie auf Containerebene nicht vorhanden ist.

```azurepowershell
if ($migrationOperation.JobStateInfo.State -eq "Failed") {
Write-Host $migrationOperation.Error
}
The container <container-name> must have an immutability policy set as a default policy
before initiating container migration to support object level immutability with versioning.
```

Überprüfen Sie nach Abschluss der Migration die **Output**-Eigenschaft des Vorgangs, um festzustellen, ob die Unterstützung für Unveränderlichkeit auf Versionsebene aktiviert ist.

```azurepowershell
$migrationOperation.Output
```

Weitere Informationen zu PowerShell-Aufträgen finden Sie unter [Ausführen von Azure PowerShell Cmdlets in PowerShell-Aufträgen](/powershell/azure/using-psjobs).

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um einen Container zu migrieren, um unveränderlichen Speicher auf Versionsniveau mit Azure CLI zu unterstützen, stellen Sie zunächst sicher, dass eine zeitbasierte Aufbewahrungsrichtlinie auf Containerebene für den Container existiert. Rufen Sie [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) auf, um eine zu erstellen.

```azurecli
az storage container immutability-policy create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days>
```

Rufen Sie als Nächstes den Befehl [az storage container-rm migrate-vlw](/cli/azure/storage/container-rm#az_storage_container_rm_migrate_vlw) auf, um den Container zu migrieren. Fügen Sie den Parameter `--no-wait` hinzu, um den Befehl asynchron auszuführen. Die asynchrone Ausführung des Vorgangs wird empfohlen, da die Migration einige Zeit in Anspruch nehmen kann.

```azurecli
az storage container-rm migrate-vlw \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --name <container> \
    --no-wait
```

Lesen Sie den Wert von der **migrationState**-Eigenschaft des Vorgangs, um den Status des Vorgangs mit langer Laufzeit zu überprüfen.

```azurecli
az storage container-rm show \
    --storage-account <storage-account> \
    --name <container> \
    --query '[immutableStorageWithVersioning.migrationState]' \
    --output tsv
```

---

## <a name="configure-a-time-based-retention-policy-on-a-container"></a>Konfigurieren einer zeitbasierten Aufbewahrungsrichtlinie für einen Container

Nachdem ein Container für Unveränderlichkeit auf Versionsebene aktiviert wurde, können Sie eine zeitbasierte Standardaufbewahrungsrichtlinie auf Versionsebene für den Container angeben. Nachdem Sie eine Standardrichtlinie für einen Container angegeben haben, gilt diese Richtlinie standardmäßig für alle neuen Blobversionen, die im Container erstellt werden. Sie können die Standardrichtlinie für jede einzelne Blobversion im Container überschreiben.

Die Standardrichtlinie wird nicht automatisch auf Blobversionen angewendet, die vor der Konfiguration der Standardrichtlinie vorhanden waren.

Wenn Sie einen vorhandenen Container zur Unterstützung der Unveränderlichkeit auf Versionsebene migriert haben, wird die Richtlinie auf Containerebene, die vor der Migration wirksam war, zu einer Standardrichtlinie auf Versionsebene für den Container migriert.

### <a name="configure-a-default-time-based-retention-policy-on-a-container"></a>Konfigurieren einer zeitbasierten Standardaufbewahrungsrichtlinie für einen Container

Verwenden Sie zum Konfigurieren einer Standard-Unveränderlichkeitsrichtlinie auf Versionsebene für einen Container die Azure-Portal, PowerShell, Azure CLI oder eines der Azure Storage SDKs. Stellen Sie sicher, dass Sie die Unterstützung der Unveränderlichkeit auf Versionsebene für den Container aktiviert haben, wie unter Aktivieren der [Unterstützung für Unveränderlichkeit auf Versionsebene](#enable-support-for-version-level-immutability-on-a-container) für einen Container beschrieben ist.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um im Azure-Portal eine Standardrichtlinie für Unveränderlichkeit auf Versionsebene für einen Container konfigurieren:

1. Navigieren Sie im Azure-Portal zur Seite **Container**, und suchen Sie den Container, auf den Sie die Richtlinie anwenden möchten.
1. Wählen Sie die Schaltfläche **Mehr** rechts neben dem Containernamen und dann **Zugriffsrichtlinie** aus.
1. Wählen Sie im Dialogfeld **Zugriffsrichtlinie** im Abschnitt **Unveränderliche Blobspeicher** die Option **Richtlinie hinzufügen** aus.
1. Wählen Sie **Zeitbasierte Aufbewahrungsrichtlinie** aus, und geben Sie das Aufbewahrungsintervall an.
1. Wählen Sie bei Bedarf **Zusätzliche geschützte Anfügevorgänge zulassen** aus, um Schreibvorgänge zum Anfügen von Blobs zu aktivieren, die durch eine Unveränderlichkeitsrichtlinie geschützt sind. Weitere Informationen finden Sie unter [Zulassen von Schreibvorgängen in geschützten Anfügeblobs](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).
1. Wählen Sie **OK** aus, um die Standardrichtlinie auf den Container anzuwenden.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-default-retention-policy-container.png" alt-text="Screenshot: Konfigurieren einer Standardaufbewahrungsrichtlinie auf Versionsebene für einen Container":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rufen Sie zum Konfigurieren einer Unveränderlichkeitsrichtlinie auf Versionsebene für einen Container mit PowerShell den Befehl [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) auf.

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
   -StorageAccountName <storage-account> `
   -ContainerName <container> `
   -ImmutabilityPeriod <retention-interval-in-days> `
   -AllowProtectedAppendWrite $true
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um eine Standardrichtlinie für unveränderliche Daten auf Versionsebene für einen Container mit Azure CLI konfigurieren, rufen Sie den Befehl [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) auf.

```azurecli
az storage container immutability-policy create \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days> \
    --allow-protected-append-writes true
```

---

### <a name="determine-the-scope-of-a-retention-policy-on-a-container"></a>Bestimmen des Bereichs einer Aufbewahrungsrichtlinie für einen Container

Führen Sie die folgenden Schritte aus, um im Azure-Portal den Geltungsbereich einer zeitbasierten Aufbewahrungsrichtlinie zu bestimmen:

1. Navigieren Sie zum gewünschten Container.
1. Wählen Sie die Schaltfläche **Mehr** auf der rechten Seite und dann **Zugriffsrichtlinie** aus.
1. Suchen Sie unter **Unveränderlicher Blobspeicher** das Feld **Bereich**. Wenn der Container mit einer Standardaufbewahrungsrichtlinie auf Versionsebene konfiguriert ist, ist der Bereich wie in der folgenden Abbildung gezeigt auf *Version* festgelegt:

    :::image type="content" source="media/immutable-policy-configure-version-scope/version-scoped-retention-policy.png" alt-text="Screenshot: Für einen Container konfigurierte Standardaufbewahrungsrichtlinie auf Versionsebene":::

1. Wenn der Container mit einer Aufbewahrungsrichtlinie auf Containerebene konfiguriert ist, ist der Bereich wie in der folgenden Abbildung gezeigt auf *Container* festgelegt:

    :::image type="content" source="media/immutable-policy-configure-version-scope/container-scoped-retention-policy.png" alt-text="Screenshot: Für einen Container konfigurierte Aufbewahrungsrichtlinie auf Containerebene":::

## <a name="configure-a-time-based-retention-policy-on-an-existing-version"></a>Konfigurieren einer zeitbasierten Aufbewahrungsrichtlinie für eine vorhandene Version

Zeitbasierte Aufbewahrungsrichtlinien bewirken, dass Blobdaten für ein angegebenes Intervall im WORM-Zustand bleiben. Weitere Informationen zu zeitbasierten Aufbewahrungsrichtlinien finden Sie unter [Zeitbasierte Aufbewahrungsrichtlinien für unveränderliche Blobdaten](immutable-time-based-retention-policy-overview.md).

Es gibt drei Optionen zum Konfigurieren einer zeitbasierten Aufbewahrungsrichtlinie für eine Blobversion:

- Option 1: Sie können eine Standardrichtlinie konfigurieren, die für den Container und somit standardmäßig für alle Objekte im Container gilt. Objekte im Container übernehmen die Standardrichtlinie, sofern diese nicht explizit außer Kraft gesetzt wird, indem Sie eine Richtlinie für eine einzelne Blobversion konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren einer zeitbasierten Standardaufbewahrungsrichtlinie für einen Container](#configure-a-default-time-based-retention-policy-on-a-container).
- Option 2: Sie können eine Richtlinie für die aktuelle Version des Blobs konfigurieren. Diese Richtlinie kann eine Standardrichtlinie außer Kraft setzen, die für den Container konfiguriert ist, sofern eine solche vorhanden und nicht gesperrt ist. Standardmäßig übernehmen alle früheren Versionen, die nach der Konfiguration der Richtlinie erstellt wurden, die Richtlinie der aktuellen Version des Blobs. Weitere Informationen finden Sie unter [Konfigurieren einer Aufbewahrungsrichtlinie für die aktuelle Version eines Blobs](#configure-a-retention-policy-on-the-current-version-of-a-blob).
- Option 3: Sie können eine Richtlinie für eine frühere Version eines Blobs konfigurieren. Diese Richtlinie kann eine Standardrichtlinie außer Kraft setzen, die für die aktuelle Version konfiguriert ist, sofern eine solche vorhanden und nicht gesperrt ist. Weitere Informationen finden Sie unter [Konfigurieren einer Aufbewahrungsrichtlinie für eine frühere Version eines Blobs](#configure-a-retention-policy-on-a-previous-version-of-a-blob).

Weitere Informationen zur Blobversionsverwaltung finden Sie unter [Blobversionsverwaltung](versioning-overview.md).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Im Azure-Portal wird eine Liste von Blobs angezeigt, wenn Sie zu einem Container navigieren. Jedes angezeigte Blob stellt die aktuelle Version des Blobs dar. Sie können auf eine Liste früherer Versionen zugreifen, indem Sie die Schaltfläche **Mehr** für ein Blob und dann **Vorherige Versionen anzeigen** auswählen.

### <a name="configure-a-retention-policy-on-the-current-version-of-a-blob"></a>Konfigurieren einer Aufbewahrungsrichtlinie für die aktuelle Version eines Blobs

Führen Sie die folgenden Schritte aus, um eine zeitbasierte Aufbewahrungsrichtlinie für die aktuelle Version eines Blobs zu konfigurieren:

1. Navigieren Sie zu dem Container, der das Zielblob enthält.
1. Wählen Sie die Schaltfläche **Mehr** rechts neben dem Blobnamen und dann **Zugriffsrichtlinie** aus. Wenn für die frühere Version bereits eine zeitbasierte Aufbewahrungsrichtlinie konfiguriert wurde, wird diese im Dialogfeld **Zugriffsrichtlinie** angezeigt.
1. Wählen Sie im Dialogfeld **Zugriffsrichtlinie** im Abschnitt **Unveränderliche Blobversionen** die Option **Richtlinie hinzufügen** aus.
1. Wählen Sie **Zeitbasierte Aufbewahrungsrichtlinie** aus, und geben Sie das Aufbewahrungsintervall an.
1. Wählen Sie **OK** aus, um die Richtlinie auf die aktuelle Version des Blob anzuwenden.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-version.png" alt-text="Screenshot: Konfigurieren einer Aufbewahrungsrichtlinie für die aktuelle Version eines Blobs":::

Sie können die Eigenschaften für ein Blob anzeigen, um festzustellen, ob eine Richtlinie für die aktuelle Version aktiviert ist. Wählen Sie das Blob aus, navigieren Sie dann zur Registerkarte **Übersicht**, und suchen Sie die Eigenschaft **Unveränderlichkeitsrichtlinie auf Versionsebene** . Wenn eine Richtlinie aktiviert ist, zeigt die Eigenschaft **Aufbewahrungszeitraum** den Ablaufzeitpunkt für die Richtlinie an. Beachten Sie, dass eine Richtlinie entweder für die aktuelle Version konfiguriert ist oder vom übergeordneten Container des Blobs übernommen werden kann, wenn eine Standardrichtlinie wirksam ist.

:::image type="content" source="media/immutable-policy-configure-version-scope/view-version-level-retention-policy-portal.png" alt-text="Screenshot: Eigenschaften der Unveränderlichkeitsrichtlinie für eine Blobversion im Azure-Portal":::

### <a name="configure-a-retention-policy-on-a-previous-version-of-a-blob"></a>Konfigurieren einer Aufbewahrungsrichtlinie für eine frühere Version eines Blobs

Sie können auch eine zeitbasierte Aufbewahrungsrichtlinie für eine frühere Version eines Blobs konfigurieren. Eine frühere Version ist immer unveränderlich, da sie nicht geändert werden kann. Eine frühere Version kann jedoch gelöscht werden. Eine zeitbasierte Aufbewahrungsrichtlinie schützt vor dem Löschen, solange sie in Kraft ist.

Führen Sie die folgenden Schritte aus, um eine zeitbasierte Aufbewahrungsrichtlinie für eine frühere Version eines Blobs zu konfigurieren:

1. Navigieren Sie zu dem Container, der das Zielblob enthält.
1. Wählen Sie das Blob aus, und navigieren Sie dann zur Registerkarte **Versionen**.
1. Suchen Sie die Zielversion, und wählen Sie dann die Schaltfläche **Mehr** sowie **Zugriffsrichtlinie** aus. Wenn für die frühere Version bereits eine zeitbasierte Aufbewahrungsrichtlinie konfiguriert wurde, wird diese im Dialogfeld **Zugriffsrichtlinie** angezeigt.
1. Wählen Sie im Dialogfeld **Zugriffsrichtlinie** im Abschnitt **Unveränderliche Blobversionen** die Option **Richtlinie hinzufügen** aus.
1. Wählen Sie **Zeitbasierte Aufbewahrungsrichtlinie** aus, und geben Sie das Aufbewahrungsintervall an.
1. Wählen Sie **OK** aus, um die Richtlinie auf die aktuelle Version des Blob anzuwenden.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-previous-version.png" alt-text="Screenshot: Konfigurieren eine Aufbewahrungsrichtlinie für eine frühere Blobversion im Azure-Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um eine zeitbasierte Aufbewahrungsrichtlinie für eine Blobversion mit PowerShell zu konfigurieren, rufen Sie den Befehl **Set-AzStorageBlobImmutabilityPolicy** auf.

Das folgende Beispiel zeigt, wie Sie eine nicht gesperrte Richtlinie für die aktuelle Version eines Blobs konfigurieren. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName <resource-group> `
        -Name <storage-account>).Context

Set-AzStorageBlobImmutabilityPolicy -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -ExpiresOn "2021-09-01T12:00:00Z" `
    -PolicyMode Unlocked
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um mithilfe der Azure-Befehlszeilenschnittstelle eine zeitbasierte Aufbewahrungsrichtlinie für eine Blobversion zu konfigurieren, müssen Sie zunächst mindestens Version 0.6.1 der Erweiterung *storage-blob-preview* installieren.

```azurecli
az extension add --name storage-blob-preview
```

Weitere Informationen zum Installieren von Erweiterungen für die Azure-Befehlszeilenschnittstelle finden Sie unter [Installieren und Verwalten von Azure CLI-Erweiterungen](/cli/azure/azure-cli-extensions-overview).

Rufen Sie als Nächstes den Befehl **az storage blob immutability-policy set** auf, um die zeitbasierte Aufbewahrungsrichtlinie zu konfigurieren. Das folgende Beispiel zeigt, wie Sie eine nicht gesperrte Richtlinie für die aktuelle Version eines Blobs konfigurieren. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob immutability-policy set \
    --expiry-time 2021-09-20T08:00:00Z \
    --policy-mode Unlocked \
    --container <container> \
    --name <blob-version> \
    --account-name <storage-account> \
    --auth-mode login
```

---

## <a name="configure-a-time-based-retention-policy-when-uploading-a-blob"></a>Konfigurieren einer zeitbasierten Aufbewahrungsrichtlinie beim Hochladen eines Blobs

Wenn Sie das Azure-Portal verwenden, um ein Blob in einen Container hochzuladen, der Unveränderlichkeit auf Versionsebene unterstützt, haben Sie mehrere Optionen zum Konfigurieren einer zeitbasierten Aufbewahrungsrichtlinie für das neue Blob:

- Option 1: Wenn eine Standardaufbewahrungsrichtlinie für den Container konfiguriert ist, können Sie das Blob mit der Richtlinie des Containers hochladen. Diese Option ist standardmäßig ausgewählt, wenn für den Container eine Aufbewahrungsrichtlinie vorhanden ist.
- Option 2: Wenn eine Standardaufbewahrungsrichtlinie für den Container konfiguriert ist, können Sie die Standardrichtlinie außer Kraft setzen, indem Sie entweder eine benutzerdefinierte Aufbewahrungsrichtlinie für das neue Blob definieren oder das Blob ohne Richtlinie hochladen.
- Option 3: Wenn keine Standardrichtlinie für den Container konfiguriert ist, können Sie das Blob mit einer benutzerdefinierten Richtlinie oder ohne Richtlinie hochladen.

Führen Sie die folgenden Schritte aus, um beim Hochladen eines Blobs eine zeitbasierte Aufbewahrungsrichtlinie zu konfigurieren:

1. Navigieren Sie zum gewünschten Container, und wählen Sie **Hochladen** aus.
1. Erweitern Sie im Dialogfeld **Blob hochladen** den Abschnitt **Erweitert**.
1. Konfigurieren Sie die zeitbasierte Aufbewahrungsrichtlinie für das neue Blob im Feld **Aufbewahrungsrichtlinie**. Wenn eine Standardrichtlinie für den Container konfiguriert ist, ist diese Richtlinie standardmäßig ausgewählt. Sie können auch eine benutzerdefinierte Richtlinie für das Blob angeben.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-blob-upload.png" alt-text="Screenshot: Optionen zum Konfigurieren einer Aufbewahrungsrichtlinie beim Hochladen von Blobs im Azure-Portal":::

## <a name="modify-or-delete-an-unlocked-retention-policy"></a>Ändern oder Löschen einer entsperrten Aufbewahrungsrichtlinie

Sie können eine nicht gesperrte zeitbasierte Aufbewahrungsrichtlinie ändern, um das Aufbewahrungsintervall zu verkürzen oder zu verlängern. Außerdem können Sie eine nicht gesperrte Richtlinie löschen. Das Bearbeiten oder Löschen einer nicht gesperrten zeitbasierten Aufbewahrungsrichtlinie für eine Blobversion wirkt sich nicht auf Richtlinien aus, die für andere Versionen wirksam sind. Wenn eine zeitbasierte Standardaufbewahrungsrichtlinie für den Container wirksam ist, übernimmt die Blobversion mit der geänderten oder gelöschten Richtlinie keine Einstellungen mehr vom Container.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um eine nicht gesperrte, zeitbasierte Aufbewahrungsrichtlinie im Azure-Portal zu ändern:

1. Finden Sie den Zielcontainer oder die Zielversion. Wählen Sie die Schaltfläche **Mehr** und dann **Zugriffsrichtlinie** aus.
1. Finden Sie die vorhandene entsperrte Unveränderlichkeitsrichtlinie. Wählen Sie die Schaltfläche **Mehr** und dann **Bearbeiten** im Menü aus.

    :::image type="content" source="media/immutable-policy-configure-version-scope/edit-existing-version-policy.png" alt-text="Screenshot: Bearbeiten einer vorhandenen zeitbasierten Aufbewahrungsrichtlinie auf Versionsebene im Azure-Portal":::

1. Geben Sie den neuen Zeitpunkt (Datum und Uhrzeit) für den Richtlinienablauf an.

Um die entsperrte Richtlinie zu löschen, wählen Sie **Löschen** im Menü **Mehr** aus.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um eine entsperrte zeitbasierte Aufbewahrungsrichtlinie mit PowerShell zu ändern, rufen Sie den Befehl **Set-AzStorageBlobImmutabilityPolicy** für die Blobversion mit dem neuen Datum und der neuen Uhrzeit für den Ablauf der Richtlinien auf. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
$containerName = "<container>"
$blobName = "<blob>"

# Get the previous blob version.
$blobVersion = Get-AzStorageBlob -Container $containerName `
    -Blob $blobName `
    -VersionId "2021-08-31T00:26:41.2273852Z" `
    -Context $ctx

# Extend the retention interval by five days.
$blobVersion = $blobVersion |
    Set-AzStorageBlobImmutabilityPolicy -ExpiresOn (Get-Date).AddDays(5) `

# View the new policy parameters.
$blobVersion.BlobProperties.ImmutabilityPolicy
```

Um eine entsperrte Aufbewahrungsrichtlinie zu löschen, rufen Sie den Befehl **Remove-AzRmStorageContainerImmutabilityPolicy** auf.

```azurepowershell
$blobVersion = $blobVersion | Remove-AzStorageBlobImmutabilityPolicy
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um eine nicht gesperrte zeitbasierte Aufbewahrungsrichtlinie mit PowerShell zu ändern, rufen Sie den Befehl **az storage blob immutability-policy set** für die Blobversion mit dem neuen Datum und der neuen Uhrzeit für den Richtlinienablauf auf. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob immutability-policy set \
    --expiry-time 2021-10-0T18:00:00Z \
    --policy-mode Unlocked \
    --container <container> \
    --name <blob-version> \
    --account-name <storage-account> \
    --auth-mode login
```

Um eine nicht gesperrte Aufbewahrungsrichtlinie zu löschen, rufen Sie den Befehl **az storage blob immutability-policy delete** auf.

```azurecli
az storage blob immutability-policy delete \
    --container <container> \
    --name <blob-version> \
    --account-name <storage-account> \
    --auth-mode login
```

---

## <a name="lock-a-time-based-retention-policy"></a>Sperren einer zeitbasierten Aufbewahrungsrichtlinie

Wenn Sie eine zeitbasierte Aufbewahrungsrichtlinie abschließend getestet haben, können Sie die Richtlinie sperren. Eine gesperrte Richtlinie ist mit SEC 17a-4(f) und anderen gesetzlichen Bestimmungen konform. Sie können das Aufbewahrungsintervall für eine gesperrte Richtlinie bis zu fünfmal verlängern, aber nicht verkürzen.

Nachdem eine Richtlinie gesperrt wurde, können Sie diese nicht mehr löschen. Sie können das Blob jedoch löschen, nachdem das Aufbewahrungsintervall abgelaufen ist.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Um eine Richtlinie über das Azure-Portal zu sperren, führen Sie die folgenden Schritte aus:

1. Finden Sie den Zielcontainer oder die Zielversion. Wählen Sie die Schaltfläche **Mehr** und dann **Zugriffsrichtlinie** aus.
1. Suchen Sie im Abschnitt **Unveränderliche Blobversionen** die vorhandene, nicht gesperrte Richtlinie. Wählen Sie die Schaltfläche **Mehr** und dann **Richtlinie sperren** im Menü aus.
1. Bestätigen Sie, dass Sie die Richtlinie sperren möchten.

    :::image type="content" source="media/immutable-policy-configure-version-scope/lock-policy-portal.png" alt-text="Screenshot: Sperren einer zeitbasierten Aufbewahrungsrichtlinie im Azure-Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um eine Richtlinie mit PowerShell zu sperren, rufen Sie den Befehl **Set-AzStorageBlobImmutabilityPolicy** auf, und setzen Sie den **PolicyMode**-Parameter auf *Locked* (Gesperrt).

Das folgende Beispiel zeigt, wie eine Richtlinie gesperrt wird, indem das gleiche Aufbewahrungsintervall angegeben wird, das für die entsperrte Richtlinie wirksam war. Sie können den Ablauftermin auch zum Zeitpunkt der Sperre der Richtlinie ändern.

```azurepowershell
# Get the previous blob version.
$blobVersion = Get-AzStorageBlob -Container $containerName `
    -Blob $blobName `
    -VersionId "2021-08-31T00:26:41.2273852Z" `
    -Context $ctx

$blobVersion = $blobVersion |
    Set-AzStorageBlobImmutabilityPolicy `
        -ExpiresOn $blobVersion.BlobProperties.ImmutabilityPolicy.ExpiresOn `
        -PolicyMode Locked
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um eine Richtlinie mit PowerShell zu sperren, rufen Sie den Befehl **az storage blob immutability-policy set** auf und legen den `--policy-mode`-Parameter auf *Locked* (Gesperrt) fest. Sie können den Ablauftermin auch zum Zeitpunkt der Sperre der Richtlinie ändern.

```azurecli
az storage blob immutability-policy set \
    --expiry-time 2021-10-0T18:00:00Z \
    --policy-mode Locked \
    --container <container> \
    --name <blob-version> \
    --account-name <storage-account> \
    --auth-mode login
```

---

## <a name="configure-or-clear-a-legal-hold"></a>Konfigurieren oder Löschen einer Aufbewahrungrichtlinie für juristische Zwecke

Bei einer Aufbewahrungrichtlinie für juristische Zwecke werden unveränderliche Daten gespeichert, bis die Aufbewahrungrichtlinie für juristische Zwecke explizit gelöscht wird. Weitere Informationen zu Aufbewahrungsrichtlinien für juristische Zwecke finden Sie im Artikel zur [Aufbewahrung von unveränderlichen Blobdaten für juristische Zwecke](immutable-legal-hold-overview.md).

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um im Azure-Portal eine Aufbewahrungsrichtlinie für juristische Zwecke für einen Container zu konfigurieren:

1. Suchen Sie die Zielversion. Dabei kann es sich um die aktuelle Version oder eine frühere Version eines Blobs handeln. Wählen Sie die Schaltfläche **Mehr** und dann **Zugriffsrichtlinie** aus.
1. Wählen Sie im Abschnitt **Unveränderliche Blobversionen** die Option **Richtlinie hinzufügen** aus.
1. Wählen Sie **Aufbewahrungrichtlinie für juristische Zwecke** als Richtlinientyp und dann **OK** aus, um die Auswahl zu übernehmen.

Die folgende Abbildung zeigt eine aktuelle Version eines Blobs, für das sowohl eine zeitbasierte Aufbewahrungsrichtlinie als auch eine Aufbewahrungrichtlinie für juristische Zwecke konfiguriert ist.

:::image type="content" source="media/immutable-policy-configure-version-scope/configure-legal-hold-blob-version.png" alt-text="Screenshot: konfigurierte Aufbewahrungrichtlinie für juristische Zwecke für eine Blobversion":::

Navigieren Sie zum Löschen einer Aufbewahrungrichtlinie für juristische Zwecke zum Dialogfeld **Zugriffsrichtlinie**, und wählen Sie die Schaltfläche **Mehr** und dann **Löschen** aus.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rufen Sie den Befehl **Set-AzStorageBlobLegalHold** auf, um eine Sperrfrist für eine Blob-Version mit PowerShell zu konfigurieren oder aufzuheben.

```azurepowershell
# Set a legal hold
Set-AzStorageBlobLegalHold -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -EnableLegalHold

# Clear a legal hold
Set-AzStorageBlobLegalHold -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -DisableLegalHold
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Rufen Sie den Befehl **az storage blob set-legal-hold** auf, um mithilfe der Azure-Befehlszeilenschnittstelle eine Sperrfrist für eine Blobversion zu konfigurieren oder aufzuheben.

```azurecli
# Set a legal hold
az storage blob set-legal-hold \
    --legal-hold \
    --container <container> \
    --name <blob-version> \
    --account-name <account-name> \
    --auth-mode login

# Clear a legal hold
az storage blob set-legal-hold \
    --legal-hold false \
    --container <container> \
    --name <blob-version> \
    --account-name <account-name> \
    --auth-mode login
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](immutable-storage-overview.md)
- [Zeitbasierte Aufbewahrungsrichtlinien für unveränderliche Blobdaten](immutable-time-based-retention-policy-overview.md)
- [Gesetzliche Aufbewahrungspflichten für unveränderliche Blobdaten](immutable-legal-hold-overview.md)
