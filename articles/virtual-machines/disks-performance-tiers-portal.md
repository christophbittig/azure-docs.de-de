---
title: Ändern der Leistung von verwalteten Azure-Datenträgern über das Azure-Portal
description: Erfahren Sie, wie Sie über das Azure-Portal Leistungsstufen für neue und vorhandene verwaltete Datenträger ändern.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1320ec212a94245e42c63f583d37b33eaa76224e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433294"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Ändern Ihrer Leistungsstufe über das Azure-Portal

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Beschränkungen

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Erste Schritte

### <a name="new-disks"></a>Neue Datenträger

Die folgenden Schritte zeigen, wie Sie die Leistungsstufe Ihres Datenträgers ändern können, wenn Sie den Datenträger zum ersten Mal erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu dem virtuellen Computer, für den Sie einen neuen Datenträger erstellen möchten.
1. Wenn Sie den neuen Datenträger auswählen, wählen Sie zuerst die Größe des benötigten Datenträgers aus.
1. Nachdem Sie eine Größe ausgewählt haben, wählen Sie eine andere Leistungsstufe aus, um die Leistung zu ändern.
1. Wählen Sie **OK** aus, um den Datenträger zu erstellen.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Der Screenshot für das Blatt zur Datenträgererstellung, wobei ein Datenträger und die Dropdownliste der Leistungsstufe hervorgehoben ist." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


### <a name="change-the-performance-tier-of-an-existing-disk-without-downtime"></a>Ändern der Leistungsstufe eines vorhandenen Datenträgers ohne Downtime

Sie können Ihre Leistungsstufe auch ohne Downtime ändern, damit Sie Ihre VM nicht freigeben oder Ihren Datenträger trennen müssen, um die Stufe zu ändern.

### <a name="prerequisites"></a>Voraussetzungen

Sie müssen das Feature für Ihr Abonnement aktivieren, bevor Sie die Leistungsstufe eines Datenträgers ohne Downtime ändern können. Führen Sie die folgenden Schritte aus, um das Feature für Ihr Abonnement zu aktivieren:

1.  Führen Sie den folgenden Befehl aus, um das Feature für Ihr Abonnement zu registrieren.

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  Überprüfen Sie mithilfe des folgenden Befehls, ob der Registrierungsstatus **Registriert** lautet (dies kann einige Minuten dauern), bevor Sie das Feature ausprobieren.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

### <a name="change-performance-tier"></a>Ändern der Leistungsstufe

Nachdem das Feature registriert wurde, können Sie die Leistungsstufen des jeweiligen Datenträgers ohne Downtime ändern.

1. Melden Sie sich über den folgenden Link beim Azure-Portal an: [https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview).
1. Navigieren Sie zu dem virtuellen Computer, der den Datenträger enthält, den Sie ändern möchten.
1. Auswählen des Datenträgers
1. Wählen Sie die **Größe und Leistung** aus.
1. Wählen Sie in der Dropdownliste **Leistungsstufen** eine andere Stufe als die aktuelle Leistungsstufe des Datenträgers aus.
1. Wählen Sie **Größe ändern** aus.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Der Screenshot für das Blatt zu Größe und Leistung, wobei die Leistungsstufe hervorgehoben ist." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die Größe eines Datenträgers ändern müssen, um die höheren Leistungsstufen zu nutzen, lesen Sie die folgenden Artikel:

- [Erweitern von virtuellen Festplatten auf virtuellen Linux-Computern mit der Azure-CLI](linux/expand-disks.md)
- [Erweitern eines verwalteten Datenträgers, der an einen virtuellen Windows-Computer angefügt ist](windows/expand-os-disk.md)
