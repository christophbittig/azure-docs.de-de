---
title: Behandeln von Volumefehlern für Azure NetApp Files | Microsoft-Dokumentation
description: Es werden Fehlermeldungen und Lösungen beschrieben, mit denen Sie Probleme mit Volumefehlern für Azure NetApp Files beheben können.
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
ms.topic: troubleshooting
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: a9df7577e1426fb355995cd4486338003a753c86
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534619"
---
# <a name="troubleshoot-volume-errors-for-azure-netapp-files"></a>Behandeln von Volumefehlern für Azure NetApp Files

In diesem Artikel werden Fehlermeldungen und Lösungen beschrieben, mit denen Sie Probleme mit Volumefehlern für Azure NetApp Files beheben können. 

## <a name="errors-for-volume-allocation"></a>Fehler bei der Volumezuordnung 

Wenn Sie ein neues Volume erstellen oder die Größe eines vorhandenen Volumes in Azure NetApp Files ändern, ordnet Microsoft Azure Ihrem Abonnement Speicher- und Netzwerkressourcen zu. Gelegentlich kann es zu Ausfällen bei der Ressourcenzuweisung kommen, weil die Nachfrage nach Azure-Diensten in bestimmten Regionen in einem unerwarteten Maße steigt.

In diesem Abschnitt werden die Ursachen einiger häufig auftretender Zuordnungsfehler erläutert und mögliche Korrekturmaßnahmen vorgeschlagen.

|     Fehlerbedingungen    |     Lösungen    |
|-|-|
|Es ist ein Fehler beim Erstellen neuer Volumes oder beim Ändern der Größe vorhandener Volumes aufgetreten. <br> Fehlermeldung: `There was a problem locating [or extending] storage  for the volume. Please retry the operation. If the problem persists, contact Support.` | Der Fehler zeigt an, dass bei dem Dienst ein Fehler aufgetreten ist, als er versucht hat, Ressourcen für diese Anforderung zuzuweisen. <br> Wiederholen Sie den Vorgang nach einiger Zeit. Wenden Sie sich an den Support, wenn das Problem weiterhin auftritt.|
|Es sind keine Speicher- oder Netzwerkkapazitäten mehr in einer Region für reguläre Volumes vorhanden. <br> Fehlermeldung: `There are currently insufficient resources available to create [or extend] a volume in this region. Please retry the operation. If the problem persists, contact Support.` | Der Fehler weist darauf hin, dass in der Region keine ausreichenden Ressourcen vorhanden sind, um Volumes zu erstellen oder deren Größe zu ändern. <br> Versuchen Sie eine der folgenden Problemumgehungen: <ul><li>Erstellen Sie das Volume in einem neuen VNet. Auf diese Weise wird vermieden, dass netzwerkbezogene Ressourcengrenzen erreicht werden.</li> <li>Wiederholen Sie den Vorgang später. Die Ressourcen wurden möglicherweise in der Zwischenzeit im Cluster, in der Region oder in der Zone freigegeben.</li></ul> |
|Es ist keine Speicherkapazität beim Erstellen eines Volumes mit auf `Standard` eingestellten Netzwerkfunktionen vorhanden. <br> Fehlermeldung: `No storage available with Standard network features, for the provided VNet.` | Der Fehler weist darauf hin, dass in der Region nicht genügend Ressourcen verfügbar sind, um Volumes mit `Standard` Netzwerkfunktionen zu erstellen. <br> Versuchen Sie eine der folgenden Problemumgehungen: <ul><li>Wenn keine `Standard` Netzwerkfunktionen erforderlich sind, erstellen Sie das Volume mit `Basic` Netzwerkfunktionen.</li> <li>Versuchen Sie, das Volume in einem neuen VNet zu erstellen. Auf diese Weise wird vermieden, dass netzwerkbezogene Ressourcengrenzen erreicht werden</li><li>Wiederholen Sie den Vorgang später.  Die Ressourcen wurden möglicherweise in der Zwischenzeit im Cluster, in der Region oder in der Zone freigegeben.</li></ul> |

## <a name="next-steps"></a>Nächste Schritte  

* [Erstellen eines NFS-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Erstellen eines SMB-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Erstellen eines Volumes mit dualem Protokoll für Azure NetApp Files](create-volumes-dual-protocol.md) 
* [Konfigurieren von Netzwerkfunktionen für ein Volume](configure-network-features.md)
