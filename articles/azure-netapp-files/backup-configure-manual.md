---
title: Konfigurieren manueller Sicherungen für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt, wie Sie manuelle Sicherungen für Azure NetApp Files-Volumes konfigurieren.
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
ms.date: 11/10/2021
ms.author: b-juche
ms.openlocfilehash: 5b5d2cafbbd70e63e2b3a039e94f1fc66106203c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319591"
---
# <a name="configure-manual-backups-for-azure-netapp-files"></a>Konfigurieren manueller Sicherungen für Azure NetApp Files 

Die Azure NetApp Files-Sicherung unterstützt *richtlinienbasierte* (geplante) und *manuelle* (bedarfsbasierte) Sicherungen auf Volumeebene. Sie können beide Sicherungstypen auf demselben Volume verwenden. Während des Konfigurationsprozesses müssen Sie die Sicherungsfunktion für ein Azure NetApp Files-Volume aktivieren, bevor richtlinienbasierte Sicherungen oder manuelle Sicherungen erstellt werden können. 

In diesem Artikel wird das Konfigurieren der einzelnen Komponenten veranschaulicht. Informationen zur Konfiguration der richtlinienbasierten Sicherung finden Sie unter [Konfigurieren von richtlinienbasierten Sicherungen](backup-configure-policy-based.md).  

> [!IMPORTANT]
> Das Azure NetApp Files-Sicherungsfeature befindet sich derzeit in der Vorschauphase. Sie müssen eine Wartelistenanforderung für den Zugriff auf das Feature über die Seite für **[Public Preview der Azure NetApp Files-Sicherung](https://aka.ms/anfbackuppreviewsignup)** übermitteln. Warten Sie auf eine offizielle Bestätigungs-E-Mail des Azure NetApp Files-Teams, bevor Sie das Azure NetApp Files-Sicherungsfeature verwenden.

## <a name="about-manual-backups"></a>Informationen zu manuellen Sicherungen  

Für jedes Azure NetApp Files-Volume muss zuerst die Sicherungsfunktion aktiviert werden, bevor Sicherungen (richtlinienbasiert oder manuell) erstellt werden können.   

Nachdem Sie die Sicherungsfunktion aktiviert haben, haben Sie die Möglichkeit, ein Volume manuell zu sichern. Eine manuelle Sicherung erstellt eine Zeitpunktmomentaufnahme des aktiven Dateisystems und sichert diese Momentaufnahme im Azure-Speicherkonto.

In der folgenden Liste sind die Verhaltensweisen manueller Sicherungen zusammengefasst:  

* Sie können manuelle Sicherungen auf einem Volume auch dann erstellen, wenn das Volume bereits für Sicherungen aktiviert und mit Sicherungsrichtlinien konfiguriert ist.  Es kann allerdings nur eine ausstehende manuelle Sicherungsanforderung für das Volume vorhanden sein. Wenn Sie eine Sicherungsrichtlinie zuweisen und die Baselineübertragung noch ausgeführt wird, wird die Erstellung einer manuellen Sicherung blockiert, bis die Baselineübertragung abgeschlossen ist.

* Wenn Sie keine vorhandene Momentaufnahme angeben, die für eine Sicherung verwendet werden soll, wird beim Erstellen einer manuellen Sicherung automatisch eine Momentaufnahme auf dem Volume generiert. Die Momentaufnahme wird dann in Azure Storage übertragen. Die auf dem Volume erstellte Momentaufnahme wird beibehalten, bis die nächste manuelle Sicherung erstellt wird. Während des nachfolgenden manuellen Sicherungsvorgangs werden ältere Momentaufnahmen bereinigt. Sie können die Momentaufnahme, die für die letzte manuelle Sicherung generiert wurde, nicht löschen. 

## <a name="enable-backup-functionality"></a>Aktivieren der Sicherungsfunktionalität

Wenn Sie dies noch nicht getan haben, aktivieren Sie die Sicherungsfunktionalität für das Volume, bevor Sie manuelle Sicherungen erstellen: 

1. Wechseln Sie zu **Volumes**, und wählen Sie das spezifische Volume aus, für das Sie die Sicherung aktivieren möchten.
2. Wählen Sie **Konfigurieren** aus.
3. Schalten Sie auf der Seite „Sicherung konfigurieren“ die Einstellung **Aktiviert** auf **Ein** um.   
    Das Feld „Tresor“ ist bereits ausgefüllt. 
4. Klicken Sie auf **OK**.   

![Screenshot der Einstellung „Aktiviert“ im Fenster „Sicherungen konfigurieren“.](../media/azure-netapp-files/backup-configure-enabled.png)

## <a name="create-a-manual-backup-for-a-volume"></a>Erstellen einer manuellen Sicherung für ein Volume

1. Wechseln Sie zu **Volumes**, und wählen Sie das Volume aus, für das Sie eine manuelle Sicherung aktivieren möchten.
2. Wählen Sie **Sicherung hinzufügen** aus.
3. Im angezeigten Fenster „Neue Sicherung“:   

    1. Geben Sie einen Sicherungsnamen im Feld **Name** an.   
    
        * Namen manueller Sicherungen müssen zwischen 3 und 256 Zeichen lang sein.   
        * Als bewährte Methode stellen Sie dem eigentlichen Sicherungsnamen ein Präfix im folgenden Format voran. Auf diese Weise können Sie die manuelle Sicherung identifizieren, falls das Volume gelöscht wird (mit erhaltenen Sicherungen).   

            `NetAppAccountName-CapacityPoolName-VolumeName`   

            Angenommen, das NetApp-Konto ist `account1`, der Kapazitätspool ist `pool1`, und der Volumename lautet `vol1`. Dann kann eine manuelle Sicherung wie folgt benannt werden:    

            `account1-pool1-vol1-backup1`   

            Wenn Sie eine kürzere Form für den Sicherungsnamen verwenden, stellen Sie sicher, dass er weiterhin Informationen enthält, die das NetApp-Konto, den Kapazitätspool und den Volumenamen für die Anzeige in der Sicherungsliste identifizieren.
            
    2. Wenn Sie eine vorhandene Momentaufnahme für die Sicherung verwenden möchten, wählen Sie die Option **Vorhandene Momentaufnahme verwenden** aus.  Wenn Sie diese Option verwenden, stellen Sie sicher, dass das Feld „Name“ dem vorhandenen Momentaufnahmenamen entspricht, der für die Sicherung verwendet wird. 

4. Klicken Sie auf **Erstellen**. 

    Wenn Sie eine manuelle Sicherung erstellen, wird auch eine Momentaufnahme auf dem Volume mit dem Namen erstellt, den Sie für die Sicherung angegeben haben. Diese Momentaufnahme stellt den aktuellen Zustand des aktiven Dateisystems dar. Sie wird in Azure Storage übertragen. Wenn die Sicherung abgeschlossen ist, wird der Eintrag für die manuelle Sicherung in der Liste der Sicherungen für das Volume angezeigt.

![Screenshot des Fenster „Neue Sicherung“.](../media/azure-netapp-files/backup-new.png)


## <a name="next-steps"></a>Nächste Schritte  

* [Grundlegendes zur Azure NetApp Files-Sicherung](backup-introduction.md)
* [Azure NetApp Files-Sicherung: Anforderungen und Überlegungen](backup-requirements-considerations.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Konfigurieren von richtlinienbasierten Sicherungen](backup-configure-policy-based.md)
* [Verwalten von Sicherungsrichtlinien](backup-manage-policies.md)
* [Durchsuchen von Sicherungen](backup-search.md)
* [Wiederherstellen einer Sicherung auf einem neuen Volume](backup-restore-new-volume.md)
* [Deaktivieren der Sicherungsfunktionalität für ein Volume](backup-disable.md)
* [Sicherungen eines Volumes löschen](backup-delete.md)
* [Metriken zur Volume-Sicherung](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)


