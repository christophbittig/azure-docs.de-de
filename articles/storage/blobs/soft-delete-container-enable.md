---
title: Aktivieren und Verwalten des vorläufigen Löschens für Container
titleSuffix: Azure Storage
description: Aktivieren Sie das vorläufige Löschen für Container, sodass Sie Ihre Daten leichter wiederherstellen können, wenn diese irrtümlich geändert oder gelöscht wurden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 208f4ff6b43a722e14a788d052350117aeac56dc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562493"
---
# <a name="enable-and-manage-soft-delete-for-containers"></a>Aktivieren und Verwalten des vorläufigen Löschens für Container

Vorläufiges Löschen von Containern schützt Ihre Daten vor versehentlichem oder irrtümlichem Ändern oder Löschen. Wenn das vorläufige Löschen für Container in einem Speicherkonto aktiviert ist, kann ein Container und dessen Inhalt nach dem Löschen innerhalb eines von Ihnen angegebenen Aufbewahrungszeitraums wiederhergestellt werden. Ausführlichere Informationen zum vorläufigen Löschen von Containern finden Sie unter [Vorläufiges Löschen für Container](soft-delete-container-overview.md).

Um einen vollumfänglichen Datenschutz zu erzielen, empfiehlt Microsoft auch die Aktivierung des vorläufigen Löschens für Blobs sowie die Blobversionsverwaltung. Informationen zum Aktivieren des vorläufigen Löschens für Blobs finden Sie unter [Aktivieren und Verwalten des vorläufigen Löschens für Blobs](soft-delete-blob-enable.md). Informationen zum Aktivieren der Blobversionsverwaltung finden Sie unter [Blobversionsverwaltung](versioning-overview.md).

## <a name="enable-container-soft-delete"></a>Aktivieren des vorläufigen Löschens von Containern

Sie können das vorläufige Löschen von Containern für das Speicherkonto jederzeit im Azure-Portal, mit PowerShell, der Azure CLI oder einer Azure Resource Manager-Vorlage aktivieren oder deaktivieren. Microsoft empfiehlt, den Aufbewahrungszeitraum für das vorläufige Löschen von Containern auf mindestens sieben Tage festzulegen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Um das vorläufige Löschen von Containern für Ihr Speicherkonto im Azure-Portal zu aktivieren, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Speicherkonto.
1. Unter **Datenverwaltung** finden Sie die Einstellungen zum **Datenschutz**.
1. Wählen Sie **Vorläufiges Löschen für Container aktivieren** aus.
1. Geben Sie eine Beibehaltungsdauer zwischen einem Tag und 365 Tagen an.
1. Speichern Sie die Änderungen.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Screenshot: Aktivieren des vorläufigen Löschens von Containern im Azure-Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um das vorläufige Löschen für Container mit PowerShell zu ermöglichen, installieren Sie zunächst mindestens Version 3.9.0 des Moduls [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage). Rufen Sie als Nächstes den Befehl **Enable-AzStorageContainerDeleteRetentionPolicy** auf, und geben Sie die Anzahl der Tage für den Aufbewahrungszeitraum an. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell-interactive
Enable-AzStorageContainerDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Um vorläufiges Löschen von Containern zu deaktivieren, rufen Sie den Befehl **Disable-AzStorageContainerDeleteRetentionPolicy** auf.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um vorläufiges Löschen von Containern mit Azure CLI zu aktivieren, installieren Sie zuerst Azure CLI Version 2.26.0 oder höher. Rufen Sie als Nächstes den Befehl [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) auf, und geben Sie die Anzahl der Tage für den Aufbewahrungszeitraum an. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account blob-service-properties update \
    --enable-container-delete-retention true \
    --container-delete-retention-days 7 \
    --account-name <storage-account> \
    --resource-group <resource_group>
```

Um vorläufiges Löschen von Containern zu deaktivieren, geben Sie für den `false`-Parameter `--enable-container-delete-retention` an.

# <a name="template"></a>[Vorlage](#tab/template)

Wenn Sie das vorläufige Löschen von Containern mithilfe einer Azure Resource Manager-Vorlage aktivieren möchten, erstellen Sie eine Vorlage, mit der die Eigenschaft **containerDeleteRetentionPolicy** festgelegt wird. Die folgenden Schritte beschreiben, wie eine Vorlage im Azure-Portal erstellt wird.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.
1. Geben Sie in **Marketplace durchsuchen** den Begriff **Vorlagenbereitstellung** ein, und drücken Sie dann die **EINGABETASTE**.
1. Wählen Sie **Vorlagenbereitstellung** aus, klicken Sie auf **Erstellen**, und wählen Sie dann **Eigene Vorlage im Editor erstellen** aus.
1. Fügen Sie folgenden JSON-Code im Vorlagen-Editor ein. Ersetzen Sie den Platzhalter `<account-name>` durch den Namen Ihres Speicherkontos.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

1. Geben Sie den Aufbewahrungszeitraum an. Der Standardwert ist 7.
1. Speichern Sie die Vorlage.
1. Geben Sie die Ressourcengruppe des Kontos an, und wählen Sie die Schaltfläche **Überprüfen und erstellen** aus, um die Vorlage bereitzustellen und das vorläufige Löschen für Container zu aktivieren.

---

## <a name="view-soft-deleted-containers"></a>Anzeigen von vorläufig gelöschten Containern

Wenn das vorläufige Löschen aktiviert ist, können Sie vorläufig gelöschte Container im Azure-Portal anzeigen. Vorläufig gelöschte Container sind während des angegebenen Aufbewahrungszeitraums sichtbar. Nach Ablauf des Aufbewahrungszeitraums wird ein vorläufig gelöschter Container endgültig gelöscht und ist nicht mehr sichtbar.

Um vorläufig gelöschte Container im Azure-Portal anzuzeigen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto, und zeigen Sie die Liste Ihrer Container an.
1. Legen Sie den Schalter „Gelöschte Container anzeigen“ so fest, dass gelöschte Container in der Liste angezeigt werden.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Screenshot: Anzeigen von vorläufig gelöschten Containern im Azure-Portal":::

## <a name="restore-a-soft-deleted-container"></a>Wiederherstellen eines vorläufig gelöschten Containers

Sie können einen vorläufig gelöschten Container und dessen Inhalte innerhalb des Aufbewahrungszeitraums wiederherstellen. Um einen vorläufig gelöschten Container im Azure-Portal wiederherzustellen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto, und zeigen Sie die Liste Ihrer Container an.
1. Öffnen Sie das Kontextmenü für den Container, den Sie wiederherstellen möchten, und wählen Sie die Option **Wiederherstellen** aus.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Screenshot: Wiederherstellen eines vorläufig gelöschten Containers im Azure-Portal":::

## <a name="next-steps"></a>Nächste Schritte

- [Vorläufiges Löschen für Container](soft-delete-container-overview.md)
- [Vorläufiges Löschen für Blobs](soft-delete-blob-overview.md)
- [Blobversionsverwaltung](versioning-overview.md)
