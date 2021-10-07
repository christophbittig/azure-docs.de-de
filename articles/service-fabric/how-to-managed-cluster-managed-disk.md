---
title: Auswählen verwalteter Datenträgertypen für verwaltete Service Fabric-Clusterknoten
description: Erfahren Sie, wie Sie verwaltete Datenträgertypen für verwaltete Service Fabric-Clusterknoten auswählen und in einer ARM-Vorlage konfigurieren.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: c9daef02b9249d44d5d202f5066bac40f807ec58
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546328"
---
# <a name="select-managed-disk-types-for-service-fabric-managed-cluster-nodes"></a>Auswählen verwalteter Datenträgertypen für verwaltete Service Fabric-Clusterknoten

Verwaltete Azure Service Fabric-Cluster verwenden verwaltete Datenträger für alle Speicheranforderungen, einschließlich Anwendungsdaten, für Szenarien wie zuverlässige Sammlungen und Akteure. Verwaltete Azure-Datenträger sind Speichervolumes auf Blockebene, die von Azure verwaltet und mit Azure-Virtual Machines verwendet werden. Verwaltete Datenträger sind wie physische Datenträger in einem lokalen Server, aber der Unterschied besteht darin, dass sie virtualisiert sind. Bei verwalteten Datenträgern müssen Sie lediglich die Datenträgergröße sowie den Datenträgertyp angeben und den Datenträger bereitstellen. Nachdem Sie den Datenträger bereitgestellt haben, übernimmt Azure den Rest. Weitere Informationen zu verwalteten Datenträgern finden Sie in der [Einführung in verwaltete Azure-Datenträger](../virtual-machines/managed-disks-overview.md).

## <a name="managed-disk-types"></a>Verwalteter Datenträgertypen

Verwaltete Azure Service Fabric-Cluster unterstützen die folgenden verwalteten Datenträgertypen:
* HDD Standard
    * Lokal redundanter HDD Standard-Speicher. Am besten für Sicherungen, nicht kritischen und seltenen Zugriff geeignet. 
* SSD Standard *Standard*
    * Lokal redundanter SSD Standard-Speicher. Am besten für Webserver, wenig genutzte Unternehmensanwendungen und Enticklung/Testen geeignet.
* SSD Premium *Kompatibel mit bestimmten VM-Größen*. Weitere Informationen finden Sie unter [SSD Premium](../virtual-machines/disks-types.md#premium-ssd).
    * Lokal redundanter SSD Premium-Speicher. Am besten für Produktions- und leistungsabhängige Workloads geeignet.

>[!NOTE]
> Alle temporären Datenträger, die der VM-Größe zugeordnet sind, werden *nicht* zum Speichern von Service Fabric- oder anwendungsbezogenen Daten verwendet.

## <a name="specifying-a-service-fabric-managed-cluster-disk-type"></a>Angeben eines verwalteten Service Fabric-Clusterdatenträgertyps

Um einen verwalteten Service Fabric-Clusterdatenträgertyp anzugeben, müssen Sie den folgenden Wert in die Definition der verwalteten Clusterressource aufnehmen.

* Den Wert der Eigenschaft **dataDiskType**, der angibt, welcher verwaltete Datenträgertyp für Ihre Knoten verwendet werden soll.

Mögliche Werte:
* „Standard_LRS“
* „StandardSSD_LRS“
* „Premium_LRS“
>[!NOTE]
> Nicht alle verwalteten Datenträgertypen sind für alle VM-Größen verfügbar. Weitere Informationen finden Sie unter [Welche Datenträgertypen sind in Azure verfügbar?](../virtual-machines/disks-types.md)

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "dataDiskType": "StandardSSD_LRS"
}
```

Es sind Beispielvorlagen verfügbar, die diese Spezifikation enthalten: [Vorlagen für verwaltete Service Fabric-Cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates)
