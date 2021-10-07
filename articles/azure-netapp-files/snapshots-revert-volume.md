---
title: Zurücksetzen eines Volumes mithilfe der Momentaufnahmewiederherstellung mit Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt, wie ein Volume mithilfe von Azure NetApp Files auf einen früheren Zustand zurückgesetzt wird.
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
ms.openlocfilehash: 7bf50ee981052424cf0a57f366b3e24b483eb5ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700384"
---
# <a name="revert-a-volume-using-snapshot-revert-with-azure-netapp-files"></a>Zurücksetzen eines Volumes mithilfe der Momentaufnahmewiederherstellung mit Azure NetApp Files

Mit der Funktion zum Wiederherstellen von [Momentaufnahmen](snapshots-introduction.md) können Sie schnell ein Volume wieder in den Zustand versetzen, in dem es sich befand, als eine bestimmte Momentaufnahme erstellt wurde. In den meisten Fällen ist das Wiederherstellen eines Volumes wesentlich schneller als das Wiederherstellen einzelner Dateien aus einer Momentaufnahme in das aktive Dateisystem. Im Vergleich zur Wiederherstellung einer Momentaufnahme auf einem neuen Volume ist es auch wesentliche effizienter hinsichtlich des Speicherplatzes. 

Die Option „Volume wiederherstellen“ finden Sie im Menü „Momentaufnahmen“ eines Volumes. Nachdem Sie eine Momentaufnahme für die Wiederherstellung ausgewählt haben, setzt Azure NetApp Files das Volume auf die Daten und Zeitstempel zurück, die es bei der Erstellung der ausgewählten Momentaufnahme enthielt. 

> [!IMPORTANT]
> Aktive Dateisystemdaten und Momentaufnahmen, die nach Erstellung der ausgewählten Momentaufnahme erstellt wurden, gehen verloren. Der Vorgang zum Wiederherstellen der Momentaufnahme ersetzt *alle* Daten im Zielvolume durch die Daten in der ausgewählten Momentaufnahme. Bei der Auswahl der Momentaufnahme sollten Sie auf die Inhalte und das Erstellungsdatum achten. Sie können den Vorgang zum Wiederherstellen der Momentaufnahme nicht zurücksetzen.

## <a name="steps"></a>Schritte

1. Rufen Sie das Menü **Momentaufnahmen** eines Volumes auf.  Klicken Sie mit der rechten Maustaste auf die Momentaufnahme, die Sie für die Wiederherstellung verwenden möchten. Klicken Sie auf **Volume wiederherstellen**. 

    ![Screenshot des Kontextmenüs einer Momentaufnahme.](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. Geben Sie im Fenster „Volume auf Momentaufnahme zurücksetzen“ den Namen des Volumes ein, und klicken Sie dann auf **Wiederherstellen**.   

    Das Volume wird nun anhand des Zeitpunkts der ausgewählten Momentaufnahme wiederhergestellt.

![Screenshot des Fensters „Volume auf Momentaufnahme zurücksetzen“.](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen über Momentaufnahmen](snapshots-introduction.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video „Azure NetApp Files Snapshots 101“](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files-Momentaufnahmen: Übersicht](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)