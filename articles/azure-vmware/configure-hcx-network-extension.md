---
title: Erstellen einer HCX-Netzwerkerweiterung
description: Erfahren Sie, wie Sie Netzwerke aus Ihrer lokalen Umgebung auf Azure VMware Solution erweitern.
ms.topic: how-to
ms.date: 09/07/2021
ms.openlocfilehash: 81e984975712e76033102c030093fc94abb2796f
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456193"
---
# <a name="create-a-hcx-network-extension"></a>Erstellen einer HCX-Netzwerkerweiterung

Dies ist ein optionaler Schritt zum Erweitern von Netzwerken aus Ihrer lokalen Umgebung um Azure VMware Solution.

1. Wählen Sie unter **Services** (Dienste) Folgendes aus: **Network Extension** > **Create a Network Extension** („Netzwerkerweiterung“ > „Netzwerkerweiterung erstellen“).

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Screenshot: Auswahlmöglichkeiten für das Starten der Erstellung einer Netzwerkerweiterung." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Wählen Sie jedes der Netzwerke aus, die Sie um Azure VMware Solution erweitern möchten, und wählen Sie dann **Next** (Weiter) aus.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Screenshot: Auswahl eines Netzwerks.":::

1. Geben Sie die lokale Gateway-IP-Adresse für jedes der Netzwerke ein, die Sie erweitern möchten, ein, und wählen Sie dann **Submit** (Senden) aus.

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Screenshot: Eintrag einer Gateway-IP-Adresse.":::

   Es dauert einige Minuten, bis die Netzwerkerweiterung abgeschlossen ist. Sobald dies der Fall ist, erfolgt die Statusänderung in **Extension complete** (Erweiterung abgeschlossen).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Screenshot: Status „Erweiterung abgeschlossen“.":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die HCX-Netzwerkerweiterung konfiguriert haben, können Sie sich auch über Folgendes informieren:

- [Leitfaden zu VMware HCX Mobility Optimized Networking (MON)](vmware-hcx-mon-guidance.md)
