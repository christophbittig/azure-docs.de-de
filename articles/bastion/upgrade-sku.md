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
ms.custom: ignite-fall-2021
ms.openlocfilehash: f88ab798618608b8dbb0e27fb772a82a5199a889
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080857"
---
# <a name="upgrade-a-sku"></a>Aktualisieren einer SKU

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
