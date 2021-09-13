---
title: Aktualisieren einer SKU
titleSuffix: Azure Bastion
description: Hier erfahren Sie, wie Sie den Tarif für eine SKU von „Basic“ in „Standard“ ändern.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: 5ed0dd6ad86b5f7758f0e1972867876eedc3b51a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225945"
---
# <a name="upgrade-a-sku-preview"></a>Durchführen eines Upgrades für eine SKU (Vorschau)

In diesem Artikel erfahren Sie, wie Sie den Tarif einer SKU von „Basic“ in „Standard“ ändern können. Nach dem Upgrade können Sie den Tarif „Basic“ für die SKU nur noch wiederherstellen, indem Sie Bastion löschen und neu konfigurieren. Während der Vorschauphase kann diese Einstellung nur im Azure-Portal konfiguriert werden. Weitere Informationen zur Hostskalierung finden Sie unter [Konfigurationseinstellungen: SKUs](configuration-settings.md#skus). 

## <a name="configuration-steps"></a>Konfigurationsschritte

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com) an.
1. Navigieren Sie im Azure-Portal zu Ihrem Bastionhost.
1. Wählen Sie auf der Seite **Konfiguration** in der Dropdownliste **Tarif** die Option **Standard** aus.

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="Screenshot: Dropdownliste für die Auswahl des Tarifs, „Standard“ ausgewählt" lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. Klicken Sie auf **Übernehmen**, um die Änderungen zu übernehmen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Konfiguration finden Sie unter [Konfigurationseinstellungen](configuration-settings.md).
* Lesen Sie die [häufig gestellten Fragen zu Bastion](bastion-faq.md).
