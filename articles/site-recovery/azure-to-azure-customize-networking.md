---
title: Anpassen von Netzwerkkonfigurationen für eine Failover-VM | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über das Anpassen von Netzwerkkonfigurationen für eine Failover-VM in der Replikation von virtuellen Azure-Computern mit Azure Site Recovery.
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 10/01/2021
ms.author: rishjai
ms.openlocfilehash: 79a33226da71071d8985daf723dde7314116ce37
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475131"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Anpassen der Netzwerkkonfigurationen des virtuellen Azure-Zielcomputers

Dieser Artikel bietet eine Anleitung zum Anpassen der Netzwerkkonfigurationen auf Azure-Ziel-VMs für die Replikation und Wiederherstellung von Azure-VMs zwischen verschiedenen Regionen mithilfe von [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Vorbereitung

Erfahren Sie, wie Site Recovery die Notfallwiederherstellung für [dieses Szenario](azure-to-azure-architecture.md) bereitstellt.

## <a name="supported-networking-resources"></a>Unterstützte Netzwerkressourcen

Beim Replizieren von virtuellen Azure-Computern können die folgenden Konfigurationen für Schlüsselressourcen für den virtuellen Failovercomputer bereitgestellt werden:

- [Interner Lastenausgleich](../load-balancer/load-balancer-overview.md)
- [Öffentliche IP-Adresse](../virtual-network/public-ip-addresses.md)
- [Sekundäre IP-Adresse](../virtual-network/ip-services/virtual-network-multiple-ip-addresses-portal.md)
- [Netzwerksicherheitsgruppe](../virtual-network/manage-network-security-group.md) für das Subnetz und für den Netzwerkadapter

## <a name="prerequisites"></a>Voraussetzungen

- Sie sollten die Konfigurationen für die Wiederherstellung unbedingt im Voraus planen.
- Erstellen Sie die Netzwerkressourcen im Voraus. Geben Sie sie als Eingabe an, damit der Azure Site Recovery-Dienst diese Einstellungen beachten und sicherstellen kann, dass die Failover-VM diese Einstellungen einhält.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Anpassen der Netzwerkkonfigurationen für Failover und Testfailover

1. Wechseln Sie zu **Replizierte Elemente**. 
2. Wählen Sie die gewünschte Azure-VM aus.
3. Wählen Sie **Netzwerk** und **Bearbeiten** aus. Sie werden feststellen, dass die Konfigurationseinstellungen des Netzwerkadapters die entsprechenden Ressourcen an der Quelle enthalten. 

    :::image type="content" source="./media/azure-to-azure-customize-networking/edit-networking-properties.png" alt-text="Anpassen der Netzwerkkonfigurationen für das Failover" lightbox="./media/azure-to-azure-customize-networking/edit-networking-properties-expanded.png":::

4. Wählen Sie ein virtuelles Netzwerk für den Testfailover aus.
5. Wählen Sie die Registerkarte NIC aus, die Sie konfigurieren möchten. Wählen Sie jetzt die entsprechenden vorab erstellten Ressourcen am Testfailover- und Failover-Standort aus.

    :::image type="content" source="./media/azure-to-azure-customize-networking/nic-drilldown.png" alt-text="Bearbeiten der Konfiguration des Netzwerkadapters" lightbox="./media/azure-to-azure-customize-networking/nic-drilldown-expanded.png":::

6. Klicken Sie auf **OK**.

Site Recovery berücksichtigt diese Einstellungen nun und stellt sicher, dass die VM bei einem Failover über den entsprechenden Netzwerkadapter mit der ausgewählten Ressource verbunden ist.

Wenn Sie den Testfailover über den Wiederherstellungsplan auslösen, wird immer das virtuelle Azure-Netzwerk gefragt. Dieses virtuelle Netzwerk wird für den Testfailover für die Computer verwendet, für die keine vorkonfigurierten Testfailover-Einstellungen vorhanden waren.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="unable-to-view-or-select-a-resource"></a>Eine Ressource kann nicht angezeigt oder ausgewählt werden.

Wenn Sie eine Netzwerkressource nicht auswählen oder anzeigen können, überprüfen Sie Folgendes:

- Das Zielfeld für eine Netzwerkressource ist nur aktiviert, wenn die Quell-VM eine entsprechende Eingabe enthielt. Dies basiert auf dem Prinzip, dass Sie bei einem Notfallwiederherstellungsszenario entweder die genau gleiche oder eine zentral herunterskalierte Version Ihrer Quelle benötigen.
- Für alle Netzwerkressourcen werden einige Filter in der Dropdownliste angewendet, um sicherzustellen, dass die Failover-VM sich selbst an die ausgewählte Ressource anfügen kann und die Failoverzuverlässigkeit beibehalten wird. Diese Filter basieren auf denselben Netzwerkbedingungen, die bei der Konfiguration der Quell-VM überprüft wurden.

Überprüfungen des internen Lastenausgleichs:

- Das Abonnement und die Region des Lastenausgleichs und der Ziel-VM sollten identisch sein.
- Das virtuelle Netzwerk, das dem internen Lastenausgleich zugeordnet ist, und das der Ziel-VM sollten identisch sein.
- Die SKU der öffentlichen IP-Adresse der Ziel-VM und die SKU des internen Lastenausgleichs sollten identisch sein.
- Wenn die Ziel-VM für die Platzierung in einer Verfügbarkeitszone konfiguriert ist, überprüfen Sie, ob das Lastenausgleichsmodul zonenredundant oder Teil einer Verfügbarkeitszone ist. (Lastenausgleichsmodule mit der Basic-SKU unterstützen keine Zonen und werden in diesem Fall nicht in der Dropdownliste angezeigt.)
- Stellen Sie sicher, dass der interne Lastenausgleich über einen vorab erstellten Back-End-Pool und eine Front-End-Konfiguration verfügt.

Öffentliche IP-Adresse:

- Das Abonnement und die Region der öffentlichen IP-Adresse und der Ziel-VM sollten identisch sein.
- Die SKU der öffentlichen IP-Adresse der Ziel-VM und die SKU des internen Lastenausgleichs sollten identisch sein.

Netzwerksicherheitsgruppe:
- Das Abonnement und die Region der Netzwerksicherheitsgruppe und der Ziel-VM sollten identisch sein.


> [!WARNING]
> Wenn die Ziel-VM einer Verfügbarkeitsgruppe zugeordnet ist, müssen Sie die öffentliche IP-Adresse und den internen Lastenausgleich der gleichen SKU wie die der öffentlichen IP-Adresse und des internen Lastenausgleichs des anderen virtuellen Computers in der Verfügbarkeitsgruppe zuordnen. Andernfalls ist das Failover möglicherweise nicht erfolgreich.