---
title: Wiederherstellen einer Momentaufnahme auf einem neuen Volume mithilfe von Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel wird das Erstellen eines neuen Volumes aus einer Momentaufnahme mithilfe von Azure NetApp Files beschrieben.
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
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 1d6c74acffe68fb75fbad72a4e0ad052f4c4cef0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327422"
---
# <a name="restore-a-snapshot-to-a-new-volume-using-azure-netapp-files"></a>Wiederherstellen einer Momentaufnahme auf einem neuen Volume mithilfe von Azure NetApp Files

[Momentaufnahmen](snapshots-introduction.md) ermöglichen die Zeitpunktwiederherstellung von Volumes. Derzeit können Sie eine Momentaufnahme nur auf einem neuen Volume wiederherstellen. 

## <a name="steps"></a>Schritte

1. Klicken Sie im Bereich „Volume“ auf **Momentaufnahmen**, um die Liste der Momentaufnahmen anzuzeigen. 
2. Klicken Sie mit der rechten Maustaste auf die Momentaufnahme, die Sie wiederherstellen möchten, und klicken Sie dann im Menü auf die Option **Auf neuem Volume wiederherstellen**.  

    ![Screenshot: Menü für die Wiederherstellung auf neuem Volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Geben Sie im Fenster „Volume erstellen“ Informationen für das neue Volume an:  
    * **Name**   
        Geben Sie den Namen für das Volume an, das Sie erstellen möchten.  
        
        Der Name muss innerhalb einer Ressourcengruppe eindeutig sein. Er muss mindestens drei Zeichen lang sein.  Er darf beliebige alphanumerische Zeichen enthalten.

    * **Kontingent**  
        Geben Sie die Menge an logischem Speicherplatz an, den Sie dem Volume zuordnen möchten.  

    ![Screenshot: Fenster „Volume erstellen“](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Klicken Sie auf **Überprüfen und erstellen**.  Klicken Sie auf **Erstellen**.   
    Das neue Volume verwendet das gleiche Protokoll wie die Momentaufnahme.   
    Das neue Volume, auf dem die Momentaufnahme wiederhergestellt wurde, wird auf dem Blatt „Volumes“ angezeigt.

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Momentaufnahmen](snapshots-introduction.md)
* [Überwachen von Volume- und Momentaufnahmemetriken](azure-netapp-files-metrics.md#volumes)
* [Problembehandlung für Momentaufnahmenrichtlinien](troubleshoot-snapshot-policies.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video „Azure NetApp Files Snapshots 101“](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files-Momentaufnahmen: Übersicht](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)
