---
title: Verwaltete Service Fabric-Cluster
description: Verwaltete Service Fabric-Cluster sind eine Weiterentwicklung des Azure Service Fabric-Clusterressourcenmodells, das die Bereitstellung und Clusterverwaltung optimiert.
ms.topic: overview
ms.date: 10/22/2021
ms.openlocfilehash: 5c94254e470e625a844b345c452e252fcd69b626
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308122"
---
# <a name="service-fabric-managed-clusters"></a>Verwaltete Service Fabric-Cluster

Verwaltete Service Fabric-Cluster sind eine Weiterentwicklung des Azure Service Fabric-Clusterressourcenmodells, das Ihr Bereitstellungs- und Clusterverwaltungsszenario optimiert.

Die ARM-Vorlage (Azure Resource Model) für herkömmliche Service Fabric-Cluster erfordert, dass Sie eine Clusterressource neben einer Reihe von unterstützenden Ressourcen definieren, die alle richtig „verbunden“ sein müssen (bei der Bereitstellung und während des gesamten Lebenszyklus des Clusters), damit der Cluster und Ihre Dienste ordnungsgemäß funktionieren. Im Gegensatz dazu besteht das Kapselungsmodell für verwaltete Service Fabric-Cluster aus einer einzelnen, *von Service Fabric verwalteten Clusterressource*. Alle zugrunde liegenden Ressourcen für den Cluster werden abstrahiert und von Azure in Ihrem Auftrag verwaltet.

**Herkömmliches Service Fabric-Clustermodell**
![Herkömmliches Service Fabric-Clustermodell][sf-composition]

**Verwaltetes Service Fabric-Clustermodell**
![Gekapseltes Service Fabric-Clustermodell][sf-encapsulation]

Im Hinblick auf Größe und Komplexität entspricht die ARM-Vorlage für einen verwalteten Service Fabric-Cluster etwa 100 Zeilen JSON-Code, während für die Definition eines typischen Service Fabric-Clusters etwa 1.000 Zeilen erforderlich sind:

| Service Fabric-Ressourcen | Ressourcen eines verwalteten Service Fabric-Clusters |
|----------|-----------|
| Service Fabric-Cluster | Verwalteter Service Fabric-Cluster |
| VM-Skalierungsgruppe(n) | |
| Load Balancer | |
| Öffentliche IP-Adresse | |
| Speicherkonten | |
| Virtuelles Netzwerk | |

## <a name="service-fabric-managed-cluster-advantages"></a>Vorteile verwalteter Service Fabric-Cluster
Verwaltete Service Fabric-Cluster bieten im Vergleich zu herkömmlichen Clustern eine Reihe von Vorteilen, darunter:

**Vereinfachte Clusterbereitstellung und -verwaltung**
- Bereitstellen und Verwalten einer einzelnen Azure-Ressource
- Verwaltung von Clusterzertifikaten und automatische Rotation in 90 Tagen
- Vereinfachte Skalierungsvorgänge
- Unterstützung für automatisches Upgrade für Betriebssystemimages
- In-Place-Unterstützung von Betriebssystem-SKU-Änderungen

**Vermeiden von Betriebsfehlern**
- Vermeiden von Konfigurationskonflikten mit zugrunde liegenden Ressourcen
- Blockieren unsicherer Vorgänge (z. B. Löschen eines Seedknotens)

**Standardmäßig bewährte Methoden**
- Vereinfachte Zuverlässigkeits- und Dauerhaftigkeitseinstellungen

Für verwaltete Service Fabric-Cluster fallen abgesehen von den Kosten für zugrunde liegende Ressourcen, die für den Cluster erforderlich sind, keine weiteren Kosten an, und für verwaltete Cluster gilt die gleiche Service Fabric-SLA.

> [!NOTE]
> Es gibt keinen Migrationspfad von vorhandenen Service Fabric-Clustern zu verwalteten Clustern. Wenn Sie diesen neuen Ressourcentyp verwenden möchten, müssen Sie einen neuen verwalteten Service Fabric-Cluster erstellen.

## <a name="service-fabric-managed-cluster-skus"></a>SKUs für verwaltete Service Fabric-Cluster

Verwaltete Service Fabric-Cluster sind sowohl in der SKU „Basic“ als auch „Standard“ verfügbar.

| Feature | Basic | Standard |
| ------- | ----- | -------- |
| Netzwerkressource (SKU für [Load Balancer](../load-balancer/skus.md), [Öffentliche IP-Adresse](../virtual-network/ip-services/public-ip-addresses.md)) | Basic | Standard |
| Mindestanzahl Knoten (VM-Instanz) | 3 | 5 |
| Maximale Anzahl Knoten pro Knotentyp | 100 | 1000 |
| Maximale Anzahl Knotentypen | 1 | 20 |
| Hinzufügen/Entfernen von Knotentypen | Nein | Ja |
| Zonenredundanz | Nein | Ja |

## <a name="feature-support"></a>Featureunterstützung

Die Funktionen verwalteter Cluster werden noch erweitert. Weitere Informationen finden Sie unter [Konfigurationsoptionen für verwaltete Service Fabric-Cluster](how-to-managed-cluster-configuration.md) oder [Microsoft.ServiceFabric-Ressourcentypen](/azure/templates/microsoft.servicefabric/allversions) für verwaltete Cluster.


## <a name="next-steps"></a>Nächste Schritte

Um erste Schritte mit verwalteten Service Fabric-Clustern auszuführen, probieren Sie den folgenden Schnellstart aus:

> [!div class="nextstepaction"]
> [Erstellen eines verwalteten Service Fabric-Clusters](quickstart-managed-cluster-template.md)

Referenz zum [Konfigurieren verwalteter Cluster](how-to-managed-cluster-configuration.md)

[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png