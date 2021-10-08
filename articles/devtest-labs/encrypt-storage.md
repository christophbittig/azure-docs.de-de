---
title: Verschlüsseln eines von einem Lab verwendeten Azure-Speicherkontos
description: Erfahren Sie, wie Sie die Verschlüsselung eines von einem Lab verwendeten Azure-Speichers in Azure DevTest Labs konfigurieren.
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 337cd45b20b9fe52bb487a3b36bde905541f840b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552583"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Verschlüsseln eines von einem Lab verwendeten Azure-Speichers in Azure DevTest Labs
Jedes Lab, das in Azure DevTest Labs erstellt wird, wird mit einem zugeordneten Azure-Speicherkonto erstellt. Dieses Speicherkonto dient den folgenden Zwecken: 

- Speichern von [Formulardokumenten](devtest-lab-manage-formulas.md), die zum Erstellen von virtuellen Computern verwendet werden können.
- Speichern von Artefaktergebnissen, die Bereitstellungs- und Erweiterungsprotokolle enthalten, die durch das Anwenden von Artefakten erstellt wurden. 
- [Hochladen von virtuellen Festplatten (VHDs) zum Erstellen benutzerdefinierter Images im Lab.](devtest-lab-create-template.md)
- Zwischenspeichern häufig verwendeter [Artefakte](add-artifact-vm.md) und [Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md) zum schnelleren Abruf während der Erstellung von virtuellen Computern/Umgebungen.

> [!NOTE]
> Die oben aufgeführten Informationen sind wichtig, damit das Lab funktioniert. Sie werden für die Lebensdauer des Labs(und derLab-Ressourcen) gespeichert, sofern sie nicht explizit gelöscht werden. Das manuelle Löschen dieser Ressourcen kann zu Fehlern beim Erstellen von Lab-VMs und/oder Formeln führen, die für zukünftige Verwendung unbrauchbar werden. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Ermitteln des Speicherkontos und Anzeigen seines Inhalts

1. Wählen Sie auf der Homepage für das Lab die **Ressourcengruppe** auf der Seite **Übersicht** aus. Die Seite **Ressourcengruppe** für die Ressourcengruppe, in der das Lab enthalten ist, sollte angezeigt werden. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Auswählen der Ressourcengruppe auf der Seite „Übersicht“":::
1. Wählen Sie das Azure-Speicherkonto des Labs aus. Die Namenskonvention für das Lab-Speicherkonto lautet: `a<labNameWithoutInvalidCharacters><4-digit number>`. Wenn der Name des Labs beispielsweise `contosolab` ist, könnte der Name des Speicherkontos `acontosolab7576` lauten. 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Auswählen des Speicherkontos in der Ressourcengruppe des Labs":::
3. Wählen Sie auf der Seite **Speicherkonto** die Option **Storage-Explorer (Vorschau)** im linken Menü aus, und wählen Sie dann **BLOBCONTAINER** aus, um relevante lab-bezogene Inhalte zu ermitteln. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Storage-Explorer (Vorschau)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>Verschlüsseln des Speicherkontos des Labs
Mit Azure Storage werden Ihre Daten beim persistenten Speichern in der Cloud automatisch verschlüsselt. Durch Azure Storage-Verschlüsselung werden Ihre Daten ausreichend geschützt, um den Sicherheits- und Complianceanforderungen Ihrer Organisation gerecht zu werden. Weitere Informationen finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md).

Daten im Lab-Speicherkonto werden mit **von Microsoft verwalteten Schlüsseln** verschlüsselt. Sie können von Microsoft verwaltete Schlüssel für die Verschlüsselung Ihrer Daten nutzen oder die Verschlüsselung mit Ihren eigenen Schlüsseln verwalten. Wenn Sie die Verschlüsselung mit ihren eigenen Schlüsseln für das Speicherkonto des Labs verwalten möchten, können Sie einen **kundenseitig verwalteten Schlüssel** mit Azure Key Vault angeben, der zum Verschlüsseln/Entschlüsseln von Daten im Blobspeicher und in Azure Files verwendet werden soll. Weitere Informationen zu kundenseitig verwalteten Schlüssels finden Sie unter [Verwenden kundenseitig verwalteter Schlüssel mit Azure Key Vault für die Verwaltung der Azure Storage-Verschlüsselung](../storage/common/customer-managed-keys-overview.md).

Informationen dazu, wie Sie kundenseitig verwaltete Schlüssel für Azure Storage-Verschlüsselung konfigurieren, finden Sie in den folgenden Artikeln: 

- [Azure portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure-Befehlszeilenschnittstelle](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Verwalten des Azure Blob Storage-Lebenszyklus
Wie bereits erwähnt, sind die im Speicherkonto des Labs gespeicherten Informationen wichtig, damit das Lab ohne Fehler ausgeführt werden kann. Diese Daten verbleiben (sofern sie nicht explizit gelöscht werden) abhängig von der Art der Daten im Speicherkonto des Labs für die Lebensdauer des Labs oder die Lebensdauer bestimmter virtueller Lab-Computer.

### <a name="uploaded-vhds"></a>Hochgeladene VHDs
Diese VHDs werden verwendet, um benutzerdefinierte Images zu erstellen. Wenn Sie sie entfernen, ist es nicht mehr möglich, benutzerdefinierte Images aus diesen VHDs zu erstellen.

### <a name="artifacts-cache"></a>Artefaktspeicher
Diese Caches werden jedes Mal neu erstellt, wenn Artefakte angewendet werden. Sie werden mit den neuesten Inhalten aus den jeweiligen referenzierten Repositorys aktualisiert. Wenn Sie diese Informationen also löschen, um speicherbezogene Ausgaben zu sparen, ist die Entlastung nur vorübergehend.

### <a name="azure-resource-manager-template-cache"></a>Azure Resource Manager-Vorlagencache
Diese Caches werden jedes Mal neu erstellt, wenn auf Azure Resource Manager basierende Vorlagenrepositorys verbunden und im Lab hochgefahren werden. Sie werden mit den neuesten Inhalten aus den jeweiligen referenzierten Repositorys aktualisiert. Wenn Sie diese Informationen also löschen, um speicherbezogene Ausgaben zu sparen, ist die Entlastung nur vorübergehend.

### <a name="formulas"></a>Formeln
Diese Dokumente werden verwendet, um die Option zum Erstellen von Formeln aus vorhandenen VMs und zum Erstellen von VMs aus Formeln zu unterstützen. Das Löschen dieser Formeldokumente kann zu Fehlern führen, wenn die folgenden Vorgänge ausgeführt werden:

- Erstellen einer Formel aus einer vorhandenen Lab-VM
- Erstellen oder Aktualisieren von Formeln 
- Erstellen einer VM aus einer Formel

### <a name="artifact-results"></a>Artefaktergebnisse
Bei der Anwendung von Artefakten kann die Größe der jeweiligen Artefaktergebnisse mit der Zeit zunehmen, je nach Anzahl und Art der Artefakte, die auf Lab-VMs ausgeführt werden. Daher möchten Sie als Lab-Besitzer ggf. den Lebenszyklus solcher Dokumente steuern. Weitere Informationen finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](../storage/blobs/lifecycle-management-overview.md).

> [!IMPORTANT]
> Es wird empfohlen, diesen Schritt durchzuführen, um die mit dem Azure Storage-Konto verbundenen Kosten zu reduzieren. 

Beispielsweise wird die folgende Regel verwendet, um einen Ablaufzeitraum von 90 Tagen speziell für Artefaktergebnisse festzulegen. Dadurch wird sichergestellt, dass ältere Artefaktergebnisse in regelmäßigen Intervallen aus dem Speicherkonto wiederverwendet werden.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte
Informationen dazu, wie Sie kundenseitig verwaltete Schlüssel für Azure Storage-Verschlüsselung konfigurieren, finden Sie in den folgenden Artikeln: 

- [Azure portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure-Befehlszeilenschnittstelle](../storage/common/customer-managed-keys-configure-key-vault.md)