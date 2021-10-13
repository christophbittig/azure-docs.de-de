---
title: Hinzufügen eines NSX-T-Netzwerksegments
description: Hier finden Sie die Schritte zum Hinzufügen eines NSX-T-Netzwerksegments für Azure VMware Solution in NSX-T Manager.
ms.topic: include
ms.date: 03/13/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 1e6d50e1ca05ea15c4d6690965c112565dae76f5
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638774"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **Segments** (Netzwerk > Segmente) und dann **Add Segment** (Segment hinzufügen) aus. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Screenshot: Hinzufügen eines neuen Segments in NSX-T Manager":::

1. Geben Sie einen Namen für das Segment ein.

1. Wählen Sie unter **Connected Gateway** (Verbundenes Gateway) das T1-Gateway (TNTxx-T1) aus, und übernehmen Sie für **Typ** die Option „Flexible“ (Flexibel).

1. Wählen Sie die vorkonfigurierte **Transportzone** für die Überlagerung (TNTxx-OVERLAY-TZ) und anschließend **Set Subnets** (Subnetze festlegen) aus. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Screenshot: Segmentdetails zum Hinzufügen eines neuen NSX-T-Netzwerksegments":::

1. Geben Sie die IP-Adresse des Gateways ein, und wählen Sie dann **Add** (Hinzufügen) aus. 

   >[!IMPORTANT]
   >Die IP-Adresse muss Teil eines nicht überlappenden RFC1918-Adressblocks sein. Dadurch wird eine Verbindung mit den virtuellen Computern im neuen Segment sichergestellt.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Screenshot: IP-Adresse des Gateways für das neue Segment":::

1. Wählen Sie **Apply** (Übernehmen) und dann **Save** (Speichern) aus.

1. Wählen Sie **No** (Nein) aus, um die Option zur Fortsetzung der Konfiguration des Segments abzulehnen. 


