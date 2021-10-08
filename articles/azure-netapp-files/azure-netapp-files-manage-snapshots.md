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
ms.date: 08/12/2021
ms.author: b-juche
ms.openlocfilehash: 2a17ed67830dfcfc3f1c5c3cbd6cc06ceedc8028
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614180"
---
# <a name="create-an-on-demand-snapshot-for-a-volume"></a>Erstellen einer Momentaufnahme bei Bedarf für ein Volume

Azure NetApp Files unterstützt das Erstellen bedarfsbasierter [Momentaufnahmen](snapshots-introduction.md) und das Verwenden von Momentaufnahmerichtlinien zum Planen der automatischen Erstellung von Momentaufnahmen. Sie können auch eine [Momentaufnahme auf einem neuen Volume wiederherstellen](snapshots-restore-new-volume.md), [eine einzelne Datei mithilfe eines Clients wiederherstellen](snapshots-restore-file-client.md) oder [ein vorhandenes Volume mithilfe einer Momentaufnahme wiederherstellen](snapshots-revert-volume.md). In diesem Artikel wird erläutert, wie Sie eine bedarfsgesteuerte Momentaufnahme für ein Volume erstellen. 

> [!NOTE] 
> Überlegungen zur Verwaltung von Momentaufnahmen bei der regionsübergreifenden Replikation finden Sie unter [Regionsübergreifende Replikation: Anforderungen und Überlegungen](cross-region-replication-requirements-considerations.md).
 
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
