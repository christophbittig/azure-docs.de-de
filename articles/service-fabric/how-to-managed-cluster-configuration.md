---
title: Konfigurieren Ihres verwalteten Service Fabric-Clusters
description: Erfahren Sie, wie Sie Ihren verwalteten Service Fabric-Cluster für automatische Betriebssystemupgrades, NSG-Regeln und mehr konfigurieren.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: aad982def0a1dd2e4b6f847a72e270337abcd460
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286030"
---
# <a name="service-fabric-managed-cluster-configuration-options"></a>Konfigurationsoptionen für verwaltete Service Fabric-Cluster

Zusätzlich zur Auswahl der [SKUs für verwaltete Service Fabric-Cluster](overview-managed-cluster.md#service-fabric-managed-cluster-skus) beim Erstellen des Clusters gibt es eine Reihe weiterer Möglichkeiten, sie zu konfigurieren:

* Hinzufügen einer [VM-Skalierungsgruppenerweiterung](how-to-managed-cluster-vmss-extension.md) zu einem verwalteten Knotentyp
* Konfigurieren eines [verfügbarkeitszonenübergreifenden](how-to-managed-cluster-availability-zones.md) Clusters
* Konfigurieren der [Netzwerkeinstellungen](how-to-managed-cluster-networking.md) für Cluster
* Konfigurieren eines Knotentyps für [große VM-Skalierungsgruppen](how-to-managed-cluster-large-virtual-machine-scale-sets.md)
* Konfigurieren der [verwalteten Identität](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) für Clusterknotentypen
* Aktivieren der [Betriebssystem- und Datenträgerverschlüsselung](how-to-managed-cluster-enable-disk-encryption.md) für Clusterknoten
* Konfigurieren der [automatischen Skalierung](how-to-managed-cluster-autoscale.md) für einen sekundären Knotentyp
* [Manuelles Skalieren eines Knotentyps](how-to-managed-cluster-modify-node-type.md#scale-a-node-type-manually-with-portal)
* Aktivieren [automatischer Betriebssystemupgrades](how-to-managed-cluster-modify-node-type.md#enable-automatic-os-image-upgrades) für Clusterknotentypen
* Ändern des für einen Knotentyp verwendeten [Betriebssystemimages](how-to-managed-cluster-modify-node-type.md#modify-the-os-image-for-a-node-type-with-portal)
* Konfigurieren der [Platzierungseigenschaften](how-to-managed-cluster-modify-node-type.md#configure-placement-properties-for-a-node-type-with-portal) für einen Knotentyp
* Auswählen der [SKU für den verwalteten Datenträger](how-to-managed-cluster-managed-disk.md) des Clusters
* Konfigurieren von [Upgradeoptionen für die Runtime](how-to-managed-cluster-upgrades.md) im Cluster


## <a name="next-steps"></a>Nächste Schritte

[Verwaltete Service Fabric-Cluster (Übersicht)](overview-managed-cluster.md)

[Service Fabric cluster templates](https://github.com/Azure-Samples/service-fabric-cluster-templates) (Service Fabric-Clustervorlagen)
