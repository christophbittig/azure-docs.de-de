---
title: Grundlegendes zu Volumekontingenten für Azure NetApp Files | Microsoft-Dokumentation
description: Enthält eine Übersicht über Volumekontingente. Zusätzlich sind Verweise zu Informationen zur Überwachung und Verwaltung der Volume- und Poolkapazität vorhanden.
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
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: ffcc36cecc4dbba32a8f3927ab88a5632018a9bb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219752"
---
# <a name="understand-volume-quota"></a>Grundlegendes zum Volumekontingent

Dieser Artikel enthält eine Übersicht über das Volumekontingent für Azure NetApp Files. Darüber hinaus enthält er Verweise auf Details, mit denen Sie die Kapazität eines Volume- oder Kapazitätspools überwachen und verwalten können.  

## <a name="behaviors-of-volume-quota"></a>Verhalten des Volumekontingents 

* Die Speicherkapazität eines Azure NetApp Files-Volumes ist auf die festgelegte Größe (Kontingent) des Volumes beschränkt. 

* Wenn der Volumeverbrauch das Maximum erreicht, wird weder die Größe des Volumes noch des zugrunde liegenden Kapazitätspools automatisch erhöht. Stattdessen erhält das Volume den Zustand „Kein Speicherplatz mehr“. Sie können jedoch [die Größe des Kapazitätspools oder eines Volumes nach Bedarf ändern](azure-netapp-files-resize-capacity-pools-or-volumes.md). Sie sollten die [Kapazität eines Volumes](monitor-volume-capacity.md) und den zugrunde liegenden Kapazitätspool aktiv überwachen.

* Je nach Typ des Kapazitätspools wirkt sich die Größe (Kontingent) eines Azure NetApp Files-Volumes auf seine Bandbreitenleistung und die bereitgestellte Kapazität aus.  Ausführlichere Informationen finden Sie unter [QoS-Typen für Kapazitätspools: QoS-Typ „Automatisch“ (Auto)](azure-netapp-files-understand-storage-hierarchy.md#qos_types). 

* Die für [Volumemomentaufnahmen](snapshots-introduction.md) genutzte Kapazität zählt zum bereitgestellten Speicherplatz auf dem Volume. 

* Das Volumekontingent gilt nicht für ein [Replikationszielvolume](cross-region-replication-introduction.md).

* Informationen zur Berechnung des Kapazitätsverbrauchs und der zugehörigen Überschreitung finden Sie unter [Kostenmodell für Azure NetApp Files](azure-netapp-files-cost-model.md).

## <a name="next-steps"></a>Nächste Schritte

* [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md)
* [Überwachen der Kapazität eines Volumes](monitor-volume-capacity.md)
* [Ändern der Größe eines Kapazitätspools oder Volumes](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)