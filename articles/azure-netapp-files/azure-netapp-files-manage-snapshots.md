---
title: Erstellen einer bedarfsgesteuerten Momentaufnahme mit Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie bedarfsgesteuerte Momentaufnahmen mit Azure NetApp Files erstellen.
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
ms.date: 10/25/2021
ms.author: b-juche
ms.openlocfilehash: c6bbc11fc77de516d2e163d1463444f2a3c0210e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069936"
---
# <a name="create-an-on-demand-snapshot-for-a-volume"></a>Erstellen einer Momentaufnahme bei Bedarf für ein Volume

Azure NetApp Files unterstützt das Erstellen bedarfsbasierter [Momentaufnahmen](snapshots-introduction.md) und das Verwenden von Momentaufnahmerichtlinien zum Planen der automatischen Erstellung von Momentaufnahmen. Sie können auch eine [Momentaufnahme auf einem neuen Volume wiederherstellen](snapshots-restore-new-volume.md), [eine einzelne Datei mithilfe eines Clients wiederherstellen](snapshots-restore-file-client.md) oder [ein vorhandenes Volume mithilfe einer Momentaufnahme wiederherstellen](snapshots-revert-volume.md). In diesem Artikel wird erläutert, wie Sie eine bedarfsgesteuerte Momentaufnahme für ein Volume erstellen. 

> [!NOTE] 
> * Überlegungen zur Verwaltung von Momentaufnahmen bei der regionsübergreifenden Replikation finden Sie unter [Regionsübergreifende Replikation: Anforderungen und Überlegungen](cross-region-replication-requirements-considerations.md).
> * Sie können das Tool für konsistente Momentaufnahmen in Azure-Anwendungen (AzAcSnap) verwenden, um Datenschutz für Datenbanken von Drittanbietern zu aktivieren. Weitere Informationen finden Sie unter [Tool für konsistente Momentaufnahmen in Azure-Anwendungen](azacsnap-introduction.md).
 
## <a name="steps"></a>Schritte

1.  Wechseln Sie zu dem Volume, für das Sie eine Momentaufnahme erstellen möchten. Klicken Sie auf **Momentaufnahmen**.

    ![Screenshot: Navigieren zum Blatt mit Momentaufnahmen.](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klicken Sie auf **+ Momentaufnahme hinzufügen**, um feine Momentaufnahme bei Bedarf für ein Volume zu erstellen.

    ![Screenshot: Hinzufügen einer Momentaufnahme.](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Geben Sie im Fenster „Neue Momentaufnahme“ der neu erstellten Momentaufnahme einen Namen an.   

    ![Screenshot: Fenster „Neue Momentaufnahme“.](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klicken Sie auf **OK**. 

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Momentaufnahmen](snapshots-introduction.md)
* [Verwalten von Momentaufnahmerichtlinien in Azure NetApp Files](snapshots-manage-policy.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video „Azure NetApp Files Snapshots 101“](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [Worum handelt es sich beim Tool für konsistente Momentaufnahmen in Azure-Anwendungen?](azacsnap-introduction.md)
