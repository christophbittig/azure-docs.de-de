---
title: Wiederherstellen einer Datei aus einer Momentaufnahme mithilfe eines Clients und Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie eine Datei aus einer Momentaufnahme über einen Client wiederhergestellt wird, auf dem das Volume mithilfe von Azure NetApp Files eingebunden ist.
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
ms.openlocfilehash: db7336901ae349a6a50f71e2adf0102100344871
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700203"
---
# <a name="restore-a-file-from-a-snapshot-using-a-client-with-azure-netapp-files"></a>Wiederherstellen einer Datei aus einer Momentaufnahme mithilfe eines Clients und Azure NetApp Files

[Momentaufnahmen](snapshots-introduction.md) ermöglichen eine Zeitpunktwiederherstellung von Volumes. Wenn Sie nicht [die gesamte Momentaufnahme auf einem Volume wiederherstellen](snapshots-restore-new-volume.md) möchten, haben Sie die Möglichkeit, eine Datei aus einer Momentaufnahme wiederherzustellen, und zwar mithilfe eines Clients, der das Volume eingebunden hat.  

Das eingebundene Volume enthält ein Momentaufnahmeverzeichnis namens `.snapshot` (in NFS-Clients) oder `~snapshot` (bei SMB-Clients), auf das der Client Zugriff hat. Das Momentaufnahmeverzeichnis enthält Unterverzeichnisse, die den Momentaufnahmen des Volumes entsprechen. Jedes Unterverzeichnis enthält die Dateien der Momentaufnahme. Wenn Sie versehentlich eine Datei löschen oder überschreiben, können Sie die Datei im übergeordneten Lese-/Schreibverzeichnis wiederherstellen, indem Sie die Datei aus einem Unterverzeichnis der Momentaufnahme in das Lese-/Schreibverzeichnis kopieren. 

Sie können den Zugriff auf die Momentaufnahmeverzeichnisse mithilfe der [Option „Momentaufnahmepfad ausblenden“](snapshots-edit-hide-path.md) steuern. Mit dieser Option wird gesteuert, ob das Verzeichnis auf den Clients ausgeblendet werden soll. Daher steuert sie auch den Zugriff auf Dateien und Ordner in Momentaufnahmen.  

In NFSV4.1 wird das Verzeichnis `.snapshot` (`ls -la`) nicht angezeigt. Wenn die Option „Momentaufnahmepfad ausblenden“ jedoch nicht festgelegt ist, können Sie immer noch über NFSV4.1 auf das Verzeichnis `.snapshot` zugreifen, indem Sie den Befehl `cd <snapshot-path>` in der Befehlszeile des Clients verwenden. 

## <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Wiederherstellen einer Datei mithilfe eines Linux-NFS-Clients 

1. Führen Sie den Linux-Befehl `ls` aus, um die Datei aufzulisten, die Sie aus dem Verzeichnis `.snapshot` wiederherstellen möchten. 

    Beispiel:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Führen Sie den Befehl `cp` aus, um die Datei in das übergeordnete Verzeichnis zu kopieren.  

    Beispiel: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

## <a name="restore-a-file-by-using-a-windows-client"></a>Wiederherstellen einer Datei mithilfe eines Windows-Clients 

1. Wenn das Verzeichnis `~snapshot` des Volumes ausgeblendet ist, [blenden Sie ausgeblendete Elemente](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) im übergeordneten Verzeichnis ein, um `~snapshot` anzuzeigen.

    ![Screenshot: Ausgeblendete Elemente eines Verzeichnisses.](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Navigieren Sie zum Unterverzeichnis innerhalb von `~snapshot`, um die Datei zu finden, die Sie wiederherstellen möchten.  Klicken Sie mit der rechten Maustaste auf die Datei. Wählen Sie **Kopieren** aus.  

    ![Screenshot: Kopieren einer Datei zur Wiederherstellung.](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Kehren Sie zum übergeordneten Verzeichnis zurück. Klicken Sie mit der rechten Maustaste in das übergeordnete Verzeichnis, und wählen Sie `Paste` aus, um die Datei in das Verzeichnis einzufügen.

    ![Screenshot: Einfügen einer Datei zur Wiederherstellung.](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. Sie können auch mit der rechten Maustaste auf das übergeordnete Verzeichnis klicken, **Eigenschaften** auswählen, auf die Registerkarte **Vorherige Versionen** klicken, um die Liste der Momentaufnahmen anzuzeigen, und **Wiederherstellen** auswählen, um eine Datei wiederherzustellen.  

    ![Screenshot: Eigenschaften vorheriger Versionen.](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Momentaufnahmen](snapshots-introduction.md) 
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video „Azure NetApp Files Snapshots 101“](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files-Momentaufnahmen: Übersicht](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)