---
title: Verwalten der Notfallwiederherstellung mithilfe der regionsübergreifenden Azure NetApp Files-Replikation | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der regionsübergreifenden Azure NetApp Files-Replikation die Notfallwiederherstellung verwalten.
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
ms.topic: how-to
ms.date: 09/29/2021
ms.author: b-juche
ms.openlocfilehash: 4f1f766fbec4c9e09d1ebd5e982254cdcbd85403
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273323"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Verwalten der Notfallwiederherstellung mithilfe der regionsübergreifenden Replikation 

Mit einer fortlaufenden Replikation zwischen dem Quell- und dem Zielvolume (siehe [Erstellen von Volumereplikation](cross-region-replication-create-peering.md)) sind Sie für den Fall einer Notfallwiederherstellung vorbereitet. 

Bei einem derartigen Ereignis können Sie ein [Failover auf das Zielvolume](#fail-over-to-destination-volume) ausführen und dem Client so Lese-und Schreibzugriff darauf gewähren. 

Nach der Notfallwiederherstellung können Sie einen [Neusynchronisierung](#resync-replication)svorgang (resync) durchführen, um ein Failback auf das Quellvolume auszuführen. Stellen Sie anschließend die [Replikation von der Quelle zum Ziel](#reestablish-source-to-destination-replication) erneut her, und binden Sie das Quellvolume wieder ein, damit der Client darauf zugreifen kann. 

In den folgenden Abschnitten werden diese Schritte ausführlich beschrieben. 

## <a name="fail-over-to-destination-volume"></a>Failover auf Zielvolume

Zur Aktivierung des Zielvolumes (wenn Sie etwa ein Failover zur Zielregion ausführen möchten) müssen Sie das Replikationspeering unterbrechen und anschließend das Zielvolume einbinden.  

1. Wählen Sie zur Unterbrechung des Replikationspeerings zunächst das Zielvolume aus. Klicken Sie unter „Speicherdienst“ auf **Replikation**.  

2.  Überprüfen Sie die folgenden Felder, bevor Sie fortfahren:  
    * Stellen Sie sicher, dass bei „Spiegelungsstatus“ ***Gespiegelt*** angezeigt wird.   
        Fahren Sie mit der Unterbrechung des Replikationspeerings nicht fort, wenn bei „Spiegelungsstatus“ *Nicht initialisiert* angezeigt wird.
    * Stellen Sie sicher, dass bei „Beziehungsstatus“ ***Im Leerlauf*** angezeigt wird.   
        Fahren Sie mit der Unterbrechung des Replikationspeerings nicht fort, wenn bei „Beziehungsstatus“ *Übertragung wird ausgeführt* angezeigt wird.   

    Weitere Informationen finden Sie unter [Anzeigen des Integritätsstatus der Replikationsbeziehung](cross-region-replication-display-health-status.md). 

3.  Klicken Sie auf **Peering unterbrechen**.  

4.  Geben Sie **Ja** ein, wenn Sie dazu aufgefordert werden, und klicken Sie auf die Schaltfläche **Unterbrechen**. 

    ![Unterbrechen des Replikationspeerings](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Binden Sie das Zielvolume ein, indem Sie die Schritte unter [Einbinden oder Aufheben der Einbindung eines Volumes auf virtuellen Windows- oder Linux-Computern](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) ausführen.   
    Dadurch ermöglichen Sie dem Client, auf das Zielvolume zuzugreifen.

## <a name="resync-volumes-after-disaster-recovery"></a><a name="resync-replication"></a>Neusynchronisierung von Volumes nach der Notfallwiederherstellung

Nach der Notfallwiederherstellung können Sie das Quellvolume reaktivieren, indem Sie einen Neusynchronisierungsvorgang ausführen.  Bei einem Neusynchronisierungsvorgang wird der Replikationsprozess umgekehrt, und die Daten aus dem Zielvolume werden mit denen des Quellvolumes synchronisiert.  

> [!IMPORTANT] 
> Der Neusynchronisierungsvorgang synchronisiert die Quell- und Zielvolumes, indem das Quellvolume basierend auf den letzten verfügbaren allgemeinen Momentaufnahmen inkrementell mit den neuesten Updates vom Zielvolume aktualisiert wird. Durch diesen Vorgang wird vermieden, dass in den meisten Fällen das gesamte Volume synchronisiert werden muss, da nur Änderungen am Zielvolumen *nach* der letzten allgemeinen Momentaufnahme auf das Quellvolumen repliziert werden müssen.  
> 
> Der Neusynchronisierungsvorgang überschreibt alle neueren Daten (neuer als die häufigste Momentaufnahme) auf dem Quellvolumen mit den aktualisierten Zielvolumedaten. Sie werden über die Benutzeroberfläche vor einem möglichen Datenverlust gewarnt. Bevor der Neusynchronisierungsvorgang gestartet wird, werden Sie zu einer Bestätigung aufgefordert.  
> 
> Falls das Quellvolume den Notfall nicht übersteht und daher keine gemeinsame Momentaufnahme vorhanden ist, werden alle Daten im Ziel erneut mit einem neu erstellten Quellvolumen synchronisiert.


1. Wählen Sie zur erneuten Synchronisierung der Replikation das Volume *Quelle* aus. Klicken Sie unter „Speicherdienst“ auf **Replikation**. Klicken Sie anschließend auf **Erneut synchronisieren**.  

2. Geben Sie **Ja** ein, wenn Sie dazu aufgefordert werden, und klicken Sie auf **Neu synchronisieren**. 
 
    ![Erneutes Synchronisieren der Replikation](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. Überwachen Sie den Integritätsstatus des Quellvolumes, indem Sie die Schritte unter [Anzeigen des Integritätsstatus der Replikationsbeziehung](cross-region-replication-display-health-status.md) ausführen.   
    Zeigt der Integritätsstatus des Quellvolumes die folgenden Werte an, ist der Neusynchronisierungsvorgang abgeschlossen, und die am Zielvolume vorgenommenen Änderungen werden nun im Quellvolume erfasst:   

    * Spiegelungsstatus: *Gespiegelt*  
    * Übertragungsstaus: *Idle*  

## <a name="reestablish-source-to-destination-replication"></a>Wiederherstellen der Replikation von der Quelle zum Ziel

Nach Abschluss des Neusynchronisierungsvorgangs vom Ziel zur Quelle müssen Sie das Replikationspeering noch mal unterbrechen und die Replikation von der Quelle zum Ziel wiederherstellen. Binden Sie auch das Quellvolume wieder ein, damit der Client darauf zugreifen kann.  

1. Unterbrechen Sie das Replikationspeering:  
    a. Wählen Sie das Volume *Ziel* aus. Klicken Sie unter „Speicherdienst“ auf **Replikation**.  
    b. Überprüfen Sie die folgenden Felder, bevor Sie fortfahren:   
    * Stellen Sie sicher, dass bei „Spiegelungsstatus“ ***Gespiegelt*** angezeigt wird.   
    Fahren Sie mit der Unterbrechung des Replikationspeerings nicht fort, wenn bei „Spiegelungsstatus“ *Nicht initialisiert* angezeigt wird.  
    * Stellen Sie sicher, dass bei „Beziehungsstatus“ ***Im Leerlauf*** angezeigt wird.   
    Fahren Sie mit der Unterbrechung des Replikationspeerings nicht fort, wenn bei „Beziehungsstatus“ *Übertragung wird ausgeführt* angezeigt wird.    

        Weitere Informationen finden Sie unter [Anzeigen des Integritätsstatus der Replikationsbeziehung](cross-region-replication-display-health-status.md). 

    c. Klicken Sie auf **Peering unterbrechen**.   
    d. Geben Sie **Ja** ein, wenn Sie dazu aufgefordert werden, und klicken Sie auf die Schaltfläche **Unterbrechen**.  

2. Synchronisieren Sie das Quellvolume erneut mit dem Zielvolume:  
    a. Wählen Sie das Volume *Ziel* aus. Klicken Sie unter „Speicherdienst“ auf **Replikation**. Klicken Sie anschließend auf **Erneut synchronisieren**.   
    b. Geben Sie **Ja** ein, wenn Sie dazu aufgefordert werden, und klicken Sie auf die Schaltfläche **Erneut synchronisieren**.

3. Binden Sie das Quellvolume erneut ein, indem Sie die Schritte unter [Einbinden oder Aufheben der Einbindung eines Volumes auf virtuellen Windows- oder Linux-Computern](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) ausführen.  
    Dadurch ermöglichen Sie dem Client, auf das Quellvolume zuzugreifen.

## <a name="next-steps"></a>Nächste Schritte  

* [Regionsübergreifende Replikation](cross-region-replication-introduction.md)
* [Regionsübergreifende Replikation: Anforderungen und Überlegungen](cross-region-replication-requirements-considerations.md)
* [Anzeigen des Integritätsstatus der Replikationsbeziehung](cross-region-replication-display-health-status.md)
* [Ändern der Größe eines regionsübergreifenden Replikationszielvolumes](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Metriken für die Volumereplikation](azure-netapp-files-metrics.md#replication)
* [Löschen von Volumereplikationen oder Volumes](cross-region-replication-delete.md)
* [Problembehandlung für die regionsübergreifende Replikation](troubleshoot-cross-region-replication.md)

