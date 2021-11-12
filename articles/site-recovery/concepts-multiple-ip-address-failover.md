---
title: Konfigurieren des Failovers mehrerer IP-Adressen mit Azure Site Recovery
description: Beschreibt, wie das Failover sekundärer IP-Konfigurationen für virtuelle Azure-Computer konfiguriert wird.
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: rishjai
ms.openlocfilehash: a7437650c11797d5a00c7c8684138632555b05a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478884"
---
# <a name="configure-failover-of-multiple-ip-addresses-with-azure-site-recovery"></a>Konfigurieren des Failovers mehrerer IP-Adressen mit Azure Site Recovery

Jeder NIC, die an einen virtuellen Computer angefügt ist, ist mindestens eine IP-Konfiguration zugeordnet. Jeder Konfiguration wird eine statische oder dynamische private IP-Adresse zugewiesen. Jeder Konfiguration kann auch eine öffentliche IP-Adressressource zugeordnet sein. Einer öffentlichen IP-Adressressource ist entweder eine dynamische oder eine statische IP-Adresse zugewiesen. Weitere Informationen zu [IP-Adressen in Azure](../virtual-network/ip-services/public-ip-addresses.md) finden Sie im Abschnitt IP-Adressen in Azure.

Heute konfiguriert Site Recovery automatisch das Failover der primären IP-Konfiguration. In diesem Abschnitt wird beschrieben, wie Sie das Failover sekundärer IP-Konfigurationen mit Site Recovery konfigurieren. Bitte beachten Sie, dass dies nur für virtuelle Azure-Computer unterstützt wird.

## <a name="configure-secondary-ip-address-failover-via-azure-portal"></a>Konfigurieren des Failovers sekundärer IP-Adressen über das Azure-Portal

Site Recovery konfiguriert automatisch das Failover Ihrer primären IP-Konfiguration, wenn Sie die Replizierung für den virtuellen Computer aktivieren. Sekundäre IP-Konfigurationen müssen nach Abschluss des Vorgangs „Replizierung aktivieren“ manuell konfiguriert werden. Dazu benötigen Sie einen geschützten virtuellen Computer, der über mindestens eine sekundäre IP-Konfiguration verfügt.

Führen Sie die Schritte wie beschrieben aus:
1. Navigieren Sie auf das Blatt **Netzwerk** auf der Seite Replizierte Elemente.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/network-tab.png" alt-text="Blatt Replizierte Elemente":::
    

2. Der folgende hervorgehobene Text wird angezeigt.  Klicken Sie auf **Bearbeiten**, um Änderungen vorzunehmen.
 
    :::image type="content" source="./media/concepts-multiple-ip-address-failover/network-edit.png" alt-text="Bearbeitungsmodus der Registerkarte &quot;Netzwerk&quot;" lightbox="./media/concepts-multiple-ip-address-failover/network-edit-expanded.png":::    

3. Klicken Sie auf „+IP-Konfigurationen“. Es werden zwei Optionen angezeigt: entweder alle IP-Konfigurationen oder selektiv IP-Konfigurationen hinzufügen.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/add-ip-configurations.png" alt-text="IP-Konfigurationen hinzufügen":::

4. Wenn Sie auf **Alle sekundären IP-Konfigurationen hinzufügen** (Add all secondary IP Configurations) klicken, werden alle im Raster unten angezeigt und Sie können sie dann nach Belieben konfigurieren.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/add-all-ip-configurations.png" alt-text="Alle IP-Konfigurationen hinzufügen" lightbox="./media/concepts-multiple-ip-address-failover/add-all-ip-configurations-expanded.png":::    

5. Wenn Sie alternativ auf **Sekundäre IP-Konfigurationen auswählen und hinzufügen** (Select and add secondary IP Configurations) klicken, wird ein Blatt geöffnet, auf dem Sie IP-Konfigurationen auswählen und hinzufügen können, die Sie für das Failover konfigurieren möchten.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/select-and-add-ip-configurations.png" alt-text="Auswählen und Hinzufügen von IP-Konfigurationen":::

Nun können Sie für jede hinzugefügte IP-Konfiguration die Werte für das private IP, das öffentliche IP und den Back-End-Pool für Failover und Testfailover separat konfigurieren. Vergessen Sie nicht, die Änderungen zu speichern, nachdem Sie alles erledigt haben.


## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Traffic Manager mit Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md).
- Informieren Sie sich ausführlicher über [Traffic Manager-Routingmethoden](../traffic-manager/traffic-manager-routing-methods.md).
- Informieren Sie sich ausführlicher über [Wiederherstellungspläne](site-recovery-create-recovery-plans.md) zum Automatisieren des Anwendungsfailovers.
