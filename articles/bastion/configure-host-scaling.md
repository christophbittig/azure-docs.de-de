---
title: Hinzufügen von Skalierungseinheiten für die Hostskalierung
titleSuffix: Azure Bastion
description: Hier erfahren Sie, wie Sie Azure Bastion zusätzliche Instanzen (Skalierungseinheiten) hinzufügen können.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: d5087994a72b52ce091dac98987b8e0bc60e287e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224739"
---
# <a name="configure-host-scaling-preview"></a>Konfigurieren der Hostskalierung (Vorschau)

In diesem Artikel finden Sie Informationen zum Hinzufügen zusätzlicher Skalierungseinheiten (Instanzen) zu Azure Bastion, um zusätzliche gleichzeitige Clientverbindungen zu ermöglichen. Während der Vorschauphase kann diese Einstellung nur im Azure-Portal konfiguriert werden. Weitere Informationen zur Hostskalierung finden Sie unter [Konfigurationseinstellungen](configuration-settings.md#instance). 

## <a name="configuration-steps"></a>Konfigurationsschritte

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com) an.
1. Navigieren Sie im Azure-Portal zu Ihrem Bastionhost.
1. Zum Festlegen der Anzahl der Instanzen bei der Hostskalierung ist der Standard-Tarif erforderlich. Überprüfen Sie daher auf der Seite **Konfiguration** unter **Tarif**, ob der Tarif **Standard** festgelegt ist. Wenn der Tarif „Basic“ festgelegt ist, wählen Sie in der Dropdownliste die Option **Standard** aus. 

   :::image type="content" source="./media/configure-host-scaling/select-sku.png" alt-text="Screenshot: Tarif auswählen" lightbox="./media/configure-host-scaling/select-sku.png":::
1. Konfigurieren Sie die Skalierung, indem Sie die Anzahl der Instanzen anpassen. Jede Instanz entspricht einer Skalierungseinheit.

   :::image type="content" source="./media/configure-host-scaling/instance-count.png" alt-text="Screenshot: Schieberegler für Anzahl der Instanzen" lightbox="./media/configure-host-scaling/instance-count.png":::
1. Klicken Sie auf **Übernehmen**, um die Änderungen zu übernehmen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu Azure Bastion](bastion-faq.md).
