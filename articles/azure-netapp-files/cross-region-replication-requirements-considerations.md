---
title: 'Regionsübergreifende Replikation mit Azure NetApp Files: Anforderungen und Überlegungen | Microsoft-Dokumentation'
description: Beschreibt die Anforderungen und Überlegungen zur Verwendung der regionsübergreifenden Volumereplikationsfunktionalität von Azure NetApp Files.
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
ms.date: 10/14/2021
ms.author: b-juche
ms.openlocfilehash: b098243c3f4cf160aadfb2fa001c23c4cad691bd
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042387"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Regionsübergreifende Replikation: Anforderungen und Überlegungen 

In diesem Artikel finden Sie Informationen zu Anforderungen und Überlegungen bei der Nutzung der Funktionalität [regionsübergreifender Volumereplikation](cross-region-replication-create-peering.md) von Azure NetApp Files.

## <a name="requirements-and-considerations"></a>Anforderungen und Überlegungen 

* Die Azure NetApp Files-Replikation ist nur in bestimmten festen Regionspaaren verfügbar. Siehe [Unterstützte Regionspaare](cross-region-replication-introduction.md#supported-region-pairs). 
* SMB-Volumes werden zusammen mit NFS-Volumes unterstützt. Die Replikation von SMB-Volumes erfordert eine Active Directory-Verbindung in den Quell- und Ziel-NetApp-Konten. Die AD-Zielverbindung muss Zugriff auf die DNS-Server oder ADDS-Domänencontroller haben, die aus dem delegierten Subnetz in der Zielregion erreichbar sind. Weitere Informationen finden Sie unter [Anforderungen für Active Directory-Verbindungen](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Das Zielkonto muss sich in einer anderen Region als das Quellvolume befinden. Sie können auch ein vorhandenes NetApp-Konto in einer anderen Region auswählen.  
* Das Replikationszielvolume ist schreibgeschützt, bis Sie ein [Failover auf die Zielregion](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume) durchführen, um das Zielvolume für Lese- und Schreibvorgänge zu aktivieren. 
* Die Azure NetApp Files-Replikation unterstützt derzeit keine mehreren Abonnements. Alle Replikationen müssen unter einem einzelnen Abonnement ausgeführt werden.
* Informationen zur maximalen Anzahl von regionsübergreifenden Replikationszielvolumes finden Sie unter [Ressourcenlimits](azure-netapp-files-resource-limits.md). Sie können ein Supportticket eröffnen, um eine [Erhöhung der Grenzwerte](azure-netapp-files-resource-limits.md#request-limit-increase) im Standardkontingent für Replikationszielvolumes (pro Abonnement in einer Region) anzufordern.
* Es kann bis zu fünf Minuten dauern, bis in der Oberfläche eine neu hinzugefügte Momentaufnahme auf dem Quellvolume angezeigt wird.  
* Kaskadierende und nach innen/außen auffächernde Topologien werden nicht unterstützt.
* Das Konfigurieren der Volumereplikation für Quellvolumes, die aus einer Momentaufnahme erstellt wurden, wird derzeit nicht unterstützt.
* Nachdem Sie die regionsübergreifende Replikation eingerichtet haben, erstellt der Replikationsprozess *Snapmirror-Momentaufnahmen*, um Verweise zwischen dem Quellvolume und dem Zielvolume bereitzustellen. Snapmirror-Momentaufnahmen werden automatisch zyklisch durchlaufen, wenn für jede inkrementelle Übertragung eine neue erstellt wird. Sie können Snapmirror-Momentaufnahmen erst löschen, nachdem die Replikationsbeziehung und das Volume gelöscht wurden. 
* Sie können manuelle Momentaufnahmen auf dem Quellvolume einer Replikationsbeziehung löschen, wenn die Replikationsbeziehung aktiv oder unterbrochen ist, und auch nachdem die Replikationsbeziehung gelöscht wurde. Sie können manuelle Momentaufnahmen für das Zielvolume erst löschen, wenn die Replikationsbeziehung unterbrochen wurde.
* Sie können ein Quell- oder Zielvolume der regionsübergreifenden Replikation nicht auf eine Momentaufnahme zurücksetzen. Die Funktion zum Zurücksetzen auf eine Momentaufnahme ist für Volumes in einer Replikationsbeziehung abgeblendet. 

## <a name="next-steps"></a>Nächste Schritte
* [Erstellen der Volumereplikation](cross-region-replication-create-peering.md)
* [Anzeigen des Integritätsstatus der Replikationsbeziehung](cross-region-replication-display-health-status.md)
* [Verwalten der Notfallwiederherstellung](cross-region-replication-manage-disaster-recovery.md)
* [Metriken für die Volumereplikation](azure-netapp-files-metrics.md#replication)
* [Löschen von Volumereplikationen oder Volumes](cross-region-replication-delete.md)
* [Problembehandlung für die regionsübergreifende Replikation](troubleshoot-cross-region-replication.md)


