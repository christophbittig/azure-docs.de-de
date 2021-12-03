---
title: Speicherhierarchie von Azure NetApp Files | Microsoft-Dokumentation
description: Enthält Informationen zur Speicherhierarchie (einschließlich Azure NetApp Files-Konten, Kapazitätspools und Volumes).
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: 49ad214c8851546cb2340d7ad413f9369e9b8a01
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219942"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Speicherhierarchie von Azure NetApp Files

Vor der Erstellung eines Volumes in Azure NetApp Files müssen Sie zunächst einen Pool für bereitgestellte Kapazität erwerben und einrichten.  Um einen Kapazitätspool einrichten zu können, benötigen Sie ein NetApp-Konto. Die Informationen zur Speicherhierarchie sind beim Einrichten und Verwalten Ihrer Azure NetApp Files-Ressourcen hilfreich.

> [!IMPORTANT] 
> Azure NetApp Files unterstützt derzeit keine Ressourcenmigration zwischen Abonnements.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp-Konten

- Ein NetApp-Konto fungiert als administrative Gruppierung der einzelnen Kapazitätspools.  
- Ein NetApp-Konto ist nicht dasselbe wie Ihr allgemeines Azure-Speicherkonto. 
- Ein NetApp-Konto deckt einen regionalen Bereich ab.   
- Sie können über mehrere NetApp-Konten in einer Region verfügen, jedes NetApp-Konto ist aber an eine einzelne Region gebunden.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Kapazitätspools

Wenn Sie mit der Funktionsweise von Kapazitätspools vertraut sind, können Sie leichter die richtigen Typen von Kapazitätspools auswählen, die Sie zur Erfüllung Ihrer Speicheranforderungen benötigen. 

### <a name="general-rules-of-capacity-pools"></a>Allgemeine Regeln von Kapazitätspools

- Ein Kapazitätspool wird anhand der bereitgestellten Kapazität gemessen.   
    Weitere Informationen finden Sie unter [QoS-Typen für Kapazitätspools](#qos_types).  
- Die Kapazität wird durch die festen SKUs bereitgestellt, die Sie erworben haben (beispielsweise eine Kapazität von 4 TiB).
- Ein Kapazitätspool kann lediglich einen einzelnen Servicelevel aufweisen.  
- Jeder einzelne Kapazitätspool kann nur zu einem einzelnen NetApp-Konto gehören. Innerhalb eines NetApp-Kontos können jedoch mehrere Kapazitätspools vorhanden sein.  
- Ein Kapazitätspool kann nicht zwischen NetApp-Konten verschoben werden.   
  Im [Konzeptdiagramm der Speicherhierarchie](#conceptual_diagram_of_storage_hierarchy) weiter unten kann beispielsweise der Kapazitätspool 1 nicht aus dem NetApp-Konto für „USA, Osten“ in das NetApp-Konto für „USA, Westen 2“ verschoben werden.  
- Ein Kapazitätspool kann erst gelöscht werden, wenn alle Volumes innerhalb des Kapazitätspools gelöscht wurden.

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>QoS-Typen für Kapazitätspools

Der QoS-Typ (Quality of Service, Servicequalität) ist ein Attribut eines Kapazitätspools. Azure NetApp Files bietet zwei QoS-Typen von Kapazitätspools: *automatisch (Standard)* und *manuell*. 

#### <a name="automatic-or-auto-qos-type"></a>QoS-Typ *Automatisch („Auto“)*  

Beim Erstellen eines Kapazitätspools wird standardmäßig der QoS-Typ „Auto“ verwendet.

In einem QoS-Kapazitätspool vom Typ „Auto“ wird der Durchsatz den Volumes im Pool automatisch zugewiesen. Dies erfolgt proportional zum Größenkontingent, das den Volumes zugewiesen ist. 

Der maximale Durchsatz, der einem Volume zugewiesen ist, richtet sich nach dem Servicelevel des Kapazitätspools und dem Größenkontingent des Volumes. Unter [Dienstebenen für Azure NetApp Files](azure-netapp-files-service-levels.md) finden Sie ein Beispiel für eine Berechnung.

Überlegungen zur Leistung von QoS-Typen finden Sie unter [Überlegungen zur Leistung für Azure NetApp Files](azure-netapp-files-performance-considerations.md).

#### <a name="manual-qos-type"></a>QoS-Typ *Manuell*  

Wenn Sie [einen Kapazitätspool erstellen](azure-netapp-files-set-up-capacity-pool.md), können Sie angeben, dass für den Kapazitätspool der manuelle QoS-Typ verwendet werden soll. Sie können auch [einen vorhandenen Kapazitätspool ändern](manage-manual-qos-capacity-pool.md#change-to-qos), sodass der manuelle QoS-Typ verwendet wird. *Beim Festlegen des QoS-Kapazitätstyps auf „Manuell“ handelt es sich um eine permanente Änderung.* Das bedeutet, dass ein QoS-Kapazitätspool vom Typ „Manuell“ nicht auf den QoS-Kapazitätspooltyp „Automatisch“ umgestellt werden kann. 

Bei einem manuellen QoS-Kapazitätspool können Sie die Kapazität und den Durchsatz für ein Volume unabhängig voneinander zuweisen. Informationen zum Mindest- und Höchstdurchsatz finden Sie unter [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md#resource-limits). Der Gesamtdurchsatz aller Volumes, die mit einem Kapazitätspool mit dem QoS-Typ „Manuell“ erstellt werden, ist durch den Gesamtdurchsatz des Pools begrenzt.  Dieser wird anhand der Kombination von Poolgröße und Serviceleveldurchsatz ermittelt.  Ein Kapazitätspool mit 4 TiB und dem Servicelevel „Ultra“ verfügt beispielsweise über eine Gesamtdurchsatzkapazität von 512 MiB/s (4 TiB x 128 MiB/s/TiB), die für die Volumes verfügbar ist.

##### <a name="example-of-using-manual-qos"></a>Beispiel für die Verwendung von manuellem QoS

Wenn Sie einen manuellen QoS-Kapazitätspool mit z. B. einem SAP HANA-System, einer Oracle-Datenbankinstanz oder anderen Workloads verwenden, die mehrere Volumes erfordern, kann der Kapazitätspool verwendet werden, um diese Anwendungsvolumes zu erstellen.  Jedes Volume kann die individuelle Größe und den individuellen Durchsatz bereitstellen, um die Anwendungsanforderungen zu erfüllen.  Ausführliche Informationen zu den Vorteilen finden Sie unter [Beispiele zur Durchsatzbegrenzung für Volumes in einem manuellen QoS-Kapazitätspool](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool).  

## <a name="volumes"></a><a name="volumes"></a>Volumes

- Ein Volume wird anhand des logischen Kapazitätsverbrauchs gemessen und ist skalierbar. 
- Der Kapazitätsverbrauch eines Volumes wird mit der bereitgestellten Kapazität des dazugehörigen Pools verrechnet.
- Der Durchsatzverbrauch eines Volumes wird auf den verfügbaren Durchsatz des zugehörigen Pools angerechnet. Weitere Informationen finden Sie unter [QoS-Typ „Manuell“](#manual-qos-type).
- Jedes Volume gehört zu einem einzelnen Pool, ein Pool kann jedoch mehrere Volumes enthalten. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Konzeptdiagramm der Speicherhierarchie 
Das folgende Beispiel zeigt die Beziehungen zwischen Azure-Abonnement, NetApp-Konten, Kapazitätspools und Volumes.   

![Konzeptdiagramm der Speicherhierarchie](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Nächste Schritte

- [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Dienstebenen für Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Überlegungen zur Leistung für Azure NetApp Files](azure-netapp-files-performance-considerations.md)
- [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)
- [Verwalten eines manuellen QoS-Kapazitätspools](manage-manual-qos-capacity-pool.md)
