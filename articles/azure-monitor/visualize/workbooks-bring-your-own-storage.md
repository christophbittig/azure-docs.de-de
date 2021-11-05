---
title: 'Azure Monitor-Arbeitsmappen: Verwenden des eigenen Speichers'
description: Hier erfahren Sie, wie Sie Ihre Arbeitsmappe schützen können, indem Sie den Inhalt der Arbeitsmappe in Ihrem Speicher speichern.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: af32a91344d2201899757d806019f70eccd736ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072273"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Verwenden des eigenen Speichers zum Speichern von Arbeitsmappen

Es kommt vor, dass Sie über eine Abfrage oder eine Geschäftslogik verfügen, die Sie schützen möchten. Arbeitsmappen bieten eine Option zum Schützen der Arbeitsmappen, indem der Inhalt der Arbeitsmappen in Ihrem Speicher gespeichert wird. Das Speicherkonto kann dann mit von Microsoft verwalteten Schlüsseln verschlüsselt werden, oder Sie können die Verschlüsselung verwalten, indem Sie Ihre eigenen Schlüssel bereitstellen. [Siehe Azure-Dokumentation zur Speicherdienstverschlüsselung.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Speichern der Arbeitsmappe mit verwalteten Identitäten

1. Bevor Sie die Arbeitsmappe in Ihrem Speicher speichern können, müssen Sie eine verwaltete Identität erstellen („Alle Dienste“ -> „Verwaltete Identitäten“) und ihr `Storage Blob Data Contributor`-Zugriff auf Ihr Speicherkonto erteilen. [Siehe Azure-Dokumentation zu verwalteten Identitäten.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Screenshot: Hinzufügen einer Rollenzuweisung](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Erstellen einer neuen Arbeitsmappe
3. Wählen Sie die Schaltfläche **Speichern** aus, um die Arbeitsmappe zu speichern.
4. Aktivieren Sie das Kontrollkästchen `Save content to an Azure Storage Account`, um den Inhalt in einem Azure-Speicherkonto zu speichern.

    ![Screenshot: Dialogfeld **Speichern**](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Wählen Sie das gewünschte Speicherkonto und den gewünschten Container aus. Die Liste „Speicherkonto“ stammt aus dem oben ausgewählten Abonnement.

    ![Screenshot eines Dialogfelds **Speichern** mit einer Speicheroption](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Wählen Sie dann **Ändern** aus, um eine zuvor erstellte verwaltete Identität auszuwählen.

    [![Screenshot des Dialogfelds zum Ändern der Identität](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Klicken Sie nach dem Auswählen Ihrer Speicheroptionen auf **Speichern**, um Ihre Arbeitsmappe zu speichern.

## <a name="limitations"></a>Einschränkungen

- Wenn Sie Inhalt in benutzerdefiniertem Speicher speichern, ist es nicht möglich, einzelne Teile der Arbeitsmappe an ein Dashboard anzuheften, weil die einzelnen Pins geschützte Informationen im Dashboard selbst enthalten würden. Wenn Sie benutzerdefinierten Speicher verwenden, können Sie nur Verknüpfungen mit der Arbeitsmappe selbst an Dashboards anheften.
- Sobald Sie eine Arbeitsmappe in einem benutzerdefinierten Speicher gespeichert haben, wird sie immer im benutzerdefinierten Speicher gespeichert, und dies kann nicht deaktiviert werden. Wenn Sie die Arbeitsmappe an einem anderen Speicherort speichern möchten, können Sie „Speichern unter“ verwenden und auswählen, dass die Kopie nicht im benutzerdefinierten Speicher gespeichert wird.
- Arbeitsmappen in einer Application Insights-Ressource sind „Legacyarbeitsmappen“ und unterstützen keine benutzerdefinierten Speicher. Die neuesten Arbeitsmappen in einer Application Insights-Ressource befinden sich in der Auswahl „... Weitere“. Für Legacyarbeitsmappen sind beim Speichern keine Abonnementoptionen verfügbar.

   ![Screenshot der Legacyarbeitsmappe](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie in Arbeitsmappen eine [Kartenvisualisierung](workbooks-map-visualizations.md) erstellen.
- Erfahren Sie, wie [Gruppen in Arbeitsmappen](../visualize/workbooks-groups.md) verwendet werden.