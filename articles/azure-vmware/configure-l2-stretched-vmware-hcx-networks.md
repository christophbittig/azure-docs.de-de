---
title: Konfigurieren von DHCP in VMware HCX-Netzwerken mit L2-Stretching
description: Erfahren Sie, wie DHCP-Anforderungen von Azure VMware Solution-VMs an einen Nicht-NSX-T-DHCP-Server gesendet werden.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 05/28/2021
ms.openlocfilehash: c59df75e70bf8913575b70b80048b14ae42d62a2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343535"
---
# <a name="configure-dhcp-on-l2-stretched-vmware-hcx-networks"></a>Konfigurieren von DHCP in VMware HCX-Netzwerken mit L2-Stretching

DHCP kann für VMs in einem gestreckten VMware HCX L2-Netzwerk nicht verwendet werden, wenn der DHCP-Server sich in einem lokalen Rechenzentrum befindet. Der Grund hierfür ist, dass NSX standardmäßig den Durchlauf aller DHCP-Anforderungen durch das gestreckte L2-Netzwerk blockiert. Um daher DHCP-Anforderungen von Azure VMware Solution-VMs an einen Nicht-NSX-T-DHCP-Server zu senden, müssen Sie DHCP in VMware HCX-Netzwerken mit L2-Stretching konfigurieren.

1. (Optional) Wenn Sie den Segmentnamen der L2-Erweiterung finden müssen:

   1. Melden Sie sich bei Ihrem lokalen vCenter an, und wählen Sie unter **Home** die Option **HCX** aus.

   1. Wählen Sie **Network Extension** (Netzwerkerweiterung) unter **Services** (Dienste) aus.

   1. Wählen Sie die Netzwerkerweiterung aus, die DHCP-Anforderungen von Azure VMware Solution an das lokale System unterstützen soll.

   1. Notieren Sie sich den Namen des Zielnetzwerks.

      :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Screenshot: Netzwerkerweiterung im VMware vSphere-Client" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. Wählen Sie in NSX-T Manager **Networking** > **Segments** > **Segment Profiles** (Netzwerk > Segmente > Segmentprofile) aus.

1. Wählen Sie **Add Segment Profile** (Segmentprofil hinzufügen) und dann **Segment Security** (Segmentsicherheit) aus.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="Screenshot: Hinzufügen eines Segmentprofils in NSX-T" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Geben Sie einen Namen und ein Tag an, und legen Sie dann den **BPDU Filter** auf „Ein“ und alle DHCP-Umschaltflächen auf „Aus“ fest.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="Screenshot: Aktivierter BPDU-Filter und deaktivierte DHCP-Umschaltflächen" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::
    
   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Screenshot: Feld „Segment Security“ (Segmentsicherheit)" lightbox="media/manage-dhcp/edit-segment-security.png":::
