---
title: Konfigurieren von richtlinienbasierten Sicherungen für Azure NetApp Files | Microsoft Docs
description: Beschreibt die Konfiguration von richtlinienbasierten (geplanten) Sicherungen für Azure NetApp Files Volumes.
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
ms.date: 10/13/2021
ms.author: b-juche
ms.openlocfilehash: 06d7e334205de142b60f73d1e98f0c4167c65b7d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993148"
---
# <a name="configure-policy-based-backups-for-azure-netapp-files"></a>Konfigurieren Sie richtlinienbasierte Sicherungen für Azure NetApp Dateien 

Die Azure NetApp Files-Sicherung unterstützt *richtlinienbasierte* (geplante) und *manuelle* (bedarfsbasierte) Sicherungen auf Volumeebene. Sie können beide Sicherungstypen auf demselben Volume verwenden. Während des Konfigurationsprozesses müssen Sie die Sicherungsfunktion für ein Azure NetApp Files-Volume aktivieren, bevor richtlinienbasierte Sicherungen oder manuelle Sicherungen erstellt werden können. 

Dieser Artikel zeigt Ihnen, wie Sie richtlinienbasierte Sicherungen konfigurieren.  Zur Konfiguration manueller Sicherungen siehe [„Manuelle Sicherungen konfigurieren“](backup-configure-manual.md).  

> [!IMPORTANT]
> Das Azure NetApp Files-Sicherungsfeature befindet sich derzeit in der Vorschauphase. Sie müssen eine Wartelistenanforderung für den Zugriff auf das Feature über die Seite für **[Public Preview der Azure NetApp Files-Sicherung](https://aka.ms/anfbackuppreviewsignup)** übermitteln. Warten Sie auf eine offizielle Bestätigungs-E-Mail des Azure NetApp Files-Teams, bevor Sie das Azure NetApp Files-Sicherungsfeature verwenden.

## <a name="about-policy-based-backups"></a>Informationen zu richtlinienbasierten Sicherungen  

Ein Datenträger muss über die folgenden zwei Arten von Richtlinien verfügen, damit die richtlinienbasierte Sicherungsfunktion funktioniert:  

* Eine *Momentaufnahmenrichtlinie*, die steuert, wann Momentaufnahmen für das Datenträger erstellt werden.
* Eine *Sicherungsrichtlinie,* die steuert, welche Momentaufnahmen für die Sicherung im Azure-Speicher verwendet werden sollen.

Eine Momentaufnahmerichtlinie verarbeitet das Erstellen von Momentaufnahmen auf dem Volume. Sie wird von der Sicherungsfunktion verwendet, um die Momentaufnahmen in Azure Storage zu sichern.   

Sicherungen sind langwierige Vorgänge. Das System plant Sicherungen auf der Grundlage der primären Arbeitslast (die eine höhere Priorität erhält) und führt Sicherungen im Hintergrund aus. Je nach Größe des zu sichernden Volumens kann eine Sicherung stundenlang im Hintergrund laufen. Es gibt keine Möglichkeit, den Startzeitpunkt für Sicherungen zu wählen. Der Dienst führt die Sicherungen auf der Grundlage der internen Planungs- und Optimierungslogik durch. 

Durch die Zuweisung einer Richtlinie wird eine Baselinemomentaufnahme erstellt, der den aktuellen Zustand des Volumes darstellt, und die Momentaufnahme wird an den Azure-Speicher übertragen. Die Baselinemomentaufnahme wird mit einem Namen erstellt, der mit `snapmirror` beginnt. Diese Baselinemomentaufnahme wird automatisch gelöscht, wenn die erste geplante Sicherung abgeschlossen ist (je nach Richtlinie). Wenn die Sicherungsrichtlinie an einen Datenträger angehängt ist, ist die Sicherungsliste leer, bis die Baselinemomentaufnahme übertragen ist. Wenn die Sicherung abgeschlossen ist, erscheint der Eintrag für die Baselinesicherung in der Liste der Sicherungen für das Volume. Nach der Baselineübertragung wird die Liste täglich auf der Grundlage der Richtlinien aktualisiert Eine leere Liste von Sicherungen gibt an, dass die Baselinesicherung ausgeführt wird. Wenn auf einem Volume bereits manuelle Sicherungen vorhanden sind, bevor Sie eine Sicherungsrichtlinie zuweisen, wird die Baselinemomentaufnahme nicht erstellt. Eine Baselinemomentaufnahme wird nur erstellt, wenn das Volume über keine vorherigen Sicherungen verfügt.

## <a name="configure-and-apply-a-snapshot-policy"></a>Konfigurieren und Anwenden einer Momentaufnahmerichtlinie  

Sie müssen eine Momentaufnahmerichtlinie erstellen und die Snapshot-Richtlinie dem Volume zuordnen, das Sie sichern möchten. Eine einzelne Momentaufnahmerichtlinie kann an mehrere Volumes angehängt werden. Änderungen an der Momentaufnahmerichtlinie können die Sicherungsfunktionalität eines Volumes beeinträchtigen. 

1. Melden Sie sich am Azure-Portal an und navigieren Sie zu **Azure NetApp Files**.    
2. Wählen Sie Ihr Azure NetApp Files-Konto aus.   
3. **Momentaufnahmerichtlinie** auswählen   

    ![Screenshot, der zeigt, wie Sie zur Option „Momentaufnahmerichtlinie“ navigieren.](../media/azure-netapp-files/backup-navigate-snapshot-policy.png)   

4.  Klicken Sie auf **Momentaufnahmerichtlinie hinzufügen**.
5.  Geben Sie auf der angezeigten Seite Momentaufnahmerichtlinie die Anzahl der Momentaufnahmen und den Zeitplan für die Erstellung von Momentaufnahmen für das Volume an. Klicken Sie auf **Speichern**.  

    Derzeit können mit der Sicherungsfunktion nur tägliche, wöchentliche und monatliche Momentaufnahmen gesichert werden. (Stündliche Sicherungen werden nicht unterstützt).   

    * Für eine tägliche Momentaufnahmekonfiguration geben Sie die Tageszeit an, zu der Sie die Momentaufnahme erstellen möchten. 
    * Für eine wöchentliche Momentaufnahmekonfiguration geben Sie den Wochentag und die Uhrzeit an, zu der Sie die Momentaufnahme erstellen möchten. 
    * Für eine monatliche Momentaufnahmekonfiguration geben Sie den Tag des Monats und die Uhrzeit an, an dem die Momentaufnahme erstellt werden soll. 
    * Geben Sie für jede Momentaufnahmekonfiguration die Anzahl der Momentaufnahme an, die Sie behalten möchten.

    Wenn Sie beispielsweise tägliche Sicherungen wünschen, müssen Sie eine Momentaufnahmerichtlinie mit einem täglichen Momentaufnahmezeitplan und einer täglichen Momentaufnahmeanzahl konfigurieren und dann diese tägliche Momentaufnahmerichtlinie auf das Volume anwenden. Wenn Sie die Momentaufnahmerichtlinie ändern oder die tägliche Momentaufnahmekonfiguration löschen, werden keine neuen täglichen Momentaufnahmen erstellt, was dazu führt, dass keine täglichen Sicherungen durchgeführt werden. Das gleiche Verfahren und Verhalten gilt für wöchentliche und monatliche Sicherungen.  

    Stellen Sie sicher, dass jede Momentaufnahme eine eindeutige Momentaufnahme-Plan-Konfiguration hat. Azure NetApp Files verhindert, dass Sie die letzte Sicherung löschen können. Wenn mehrere Momentaufnahmen dieselbe Zeit haben (z. B. dieselbe tägliche und wöchentliche Zeitplankonfiguration), betrachtet Azure NetApp Files sie als die neuesten Momentaufnahmen, und das Löschen dieser Sicherungen wird verhindert.  

    Das folgende Beispiel zeigt eine tägliche Momentaufnahmerichtlinienkonfiguration: 

    ![Screenshot: Konfiguration der täglichen Momentaufnahmerichtlinie](../media/azure-netapp-files/backup-daily-snapshot-policy.png)

6.  Wenden Sie die Momentaufnahmerichtlinie auf das Volume an, das Sie sichern möchten:  

    1. Gehen Sie zur Seite **Volumes**, klicken Sie mit der rechten Maustaste auf das Volume, auf das Sie die Momentaufnahmenrichtlinie anwenden möchten, und wählen Sie **Bearbeiten**.   
        ![Screenshot, der das Menü zur Bearbeitung der Lautstärke zeigt.](../media/azure-netapp-files/backup-volume-edit-menu.png)   

    2. Wählen Sie im Fenster Bearbeiten unter **Momentaufnahmenrichtlinie** die Richtlinie aus, die Sie anwenden möchten. Klicken Sie auf **OK**.   
        ![Der Screenshot zeigt das Fenster Bearbeiten mit dem Pulldown-Menü Momentaufnahmenrichtlinie.](../media/azure-netapp-files/backup-volume-edit-snapshot-policy.png)    

## <a name="configure-a-backup-policy"></a>Konfigurieren einer Sicherungsrichtlinie

Mit einer Sicherungsrichtlinie kann ein Volume in regelmäßigen Abständen gesichert werden.  

Sie müssen eine Sicherungsrichtlinie erstellen und die Sicherungsrichtlinie mit dem zu sichernden Volume verknüpfen. Eine einzelne Sicherungsrichtlinie kann an mehrere Volumes angehängt werden. Sicherungen können entweder durch Deaktivierung der Richtlinie oder durch Deaktivierung von Sicherungen auf Volume-Ebene vorübergehend ausgesetzt werden. Sicherungen können auch auf Volume-Ebene vollständig deaktiviert werden, was zu einer Bereinigung aller zugehörigen Daten im Azure-Speicher führt. Eine Sicherungsrichtlinie kann nicht gelöscht werden, wenn sie an ein Volume angehängt ist.

So aktivieren Sie eine richtlinienbasierte (geplante) Sicherung: 

1. Melden Sie sich am Azure-Portal an und navigieren Sie zu **Azure NetApp Files**. 
2. Wählen Sie Ihr Azure NetApp Files-Konto aus.
3. Wählen Sie **Sicherungen** aus. 

    ![Screenshot, der zeigt, wie Sie zur Option Sicherungen navigieren.](../media/azure-netapp-files/backup-navigate.png)

4. Wählen Sie **Sicherungsrichtlinien** aus.
5. Wählen Sie **Hinzufügen**. 
6. Geben Sie auf der Seite **Sicherungsrichtlinie** den Namen der Sicherungsrichtlinie an.  Geben Sie die Anzahl der Sicherungen ein, die Sie für tägliche, wöchentliche und monatliche Sicherungen aufbewahren möchten. Klicken Sie auf **Speichern**.

    ![Screenshot, der das Fenster Sicherungsrichtlinie zeigt.](../media/azure-netapp-files/backup-policy-window-daily.png)

    * Wenn Sie eine Sicherungsrichtlinie konfigurieren und an das Volume anhängen, ohne eine Momentaufnahmenrichtlinie anzuhängen, funktioniert die Sicherung nicht ordnungsgemäß. Es wird nur eine Baselinemomentaufnahme auf den Azure-Speicher übertragen. 
    * Stellen Sie sicher, dass Sie für jede konfigurierte Sicherungsrichtlinie (z. B. tägliche Sicherungen) eine entsprechende Momentaufnahmenrichtlinienkonfiguration (z. B. tägliche Momentaufnahme) haben.
    * Die Sicherungsrichtlinie ist von der Momentaufnahmenrichtlinie abhängig. Wenn Sie noch keine Momentaufnahmenrichtlinie erstellt haben, können Sie beide Richtlinien gleichzeitig konfigurieren, indem Sie das Kontrollkästchen **Momentaufnahmenrichtlinie** erstellen im Fenster Sicherungsrichtlinie aktivieren.   

        ![Der Screenshot zeigt das Fenster Sicherungsrichtlinie mit ausgewählter Schnappschuss-Richtlinie.](../media/azure-netapp-files/backup-policy-snapshot-policy-option.png)
 
### <a name="example-of-a-valid-configuration"></a>Beispiel für eine gültige Konfiguration

Die folgende Beispielkonfiguration zeigt Ihnen, wie Sie eine Datenschutzrichtlinie auf dem Volume mit 5 neuesten täglichen Momentaufnahmen, 4 neuesten wöchentlichen Momentaufnahmen und 3 neuesten monatlichen Momentaufnahmen auf dem Volume konfigurieren. Bei dieser Konfiguration werden 15 aktuelle tägliche Momentaufnahmen, 6 aktuelle wöchentliche Momentaufnahmen und 4 aktuelle monatliche Momentaufnahmen gesichert.

* Momentaufnahmerichtlinie   
    Täglich: `Number of Snapshots to Keep = 5`   
    Wöchentlich: `Number of Snapshots to Keep = 4`   
    Monatlich: `Number of Snapshots to Keep = 3`
* Sicherungsrichtlinie:   
    Täglich: `Daily Backups to Keep = 15`   
    Wöchentlich: `Weekly Backups to Keep = 6`   
    Monatlich: `Monthly Backups to Keep = 4`

### <a name="example-of-an-invalid-configuration"></a>Beispiel für eine ungültige Konfiguration

In der folgenden Beispielkonfiguration ist eine Sicherungsrichtlinie für tägliche Sicherungen konfiguriert, aber die Momentaufnahmenrichtlinie verfügt nicht über eine entsprechende Konfiguration. Infolgedessen werden keine täglichen Momentaufnahmen erstellt, die von der Sicherungsrichtlinie gesichert werden. Bei dieser Konfiguration würden nur wöchentliche und monatliche Momentaufnahmen gesichert werden.

* Momentaufnahmerichtlinie   
    Wöchentlich: `Number of Snapshots to Keep = 4`   
    Monatlich: `Number of Snapshots to Keep = 3`   
* Sicherungsrichtlinie:   
    Täglich: `Daily Backups to Keep = 15`   
    Wöchentlich: `Weekly Backups to Keep = 6`   
    Monatlich: `Monthly Backups to Keep = 4`   

## <a name="enable-backup-functionality-for-a-volume-and-assign-a-backup-policy"></a>Aktivieren der Sicherungsfunktionalität für ein Volume und Zuweisen einer Sicherungsrichtlinie

Für jedes Azure NetApp Files-Volume muss zuerst die Sicherungsfunktion aktiviert werden, bevor Sicherungen (richtlinienbasiert oder manuell) erstellt werden können. 

Nachdem Sie die Sicherungsfunktionalität aktiviert haben, müssen Sie einem Volume eine Sicherungsrichtlinie zuweisen, damit richtlinienbasierte Sicherungen wirksam werden. (Bei manuellen Sicherungen ist eine Sicherungsrichtlinie optional).

So aktivieren Sie die Sicherungsfunktion für ein Volume:  

1. Wechseln Sie zu **Volumes** und wählen Sie das Volume aus, für das Sie die Sicherung aktivieren möchten.
2. Wählen Sie **Konfigurieren** aus.
3. Schalten Sie auf der Seite Sicherungen konfigurieren die Einstellung **Aktiviert** auf **Ein** um.
4. Weisen Sie im Dropdown-Menü **Momentaufnahmenrichtlinie** die Momentaufnahmenrichtlinie zu, die für das Volume verwendet werden soll. 
5. Weisen Sie im Dropdown-Menü **Sicherungsrichtlinie** die für das Volume zu verwendende Sicherungsrichtlinie zu. Klicken Sie auf **OK**.

    Die Tresorinformationen sind bereits vorausgefüllt.  

    ![Der Screenshot zeigt das Fenster „Sicherungen konfigurieren".](../media/azure-netapp-files/backup-configure-window.png)


## <a name="next-steps"></a>Nächste Schritte  

* [Grundlegendes zur Azure NetApp Files-Sicherung](backup-introduction.md)
* [Azure NetApp Files-Sicherung: Anforderungen und Überlegungen](backup-requirements-considerations.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Konfigurieren von manuellen Sicherungen](backup-configure-manual.md)
* [Verwalten von Sicherungsrichtlinien](backup-manage-policies.md)
* [Durchsuchen von Sicherungen](backup-search.md)
* [Wiederherstellen einer Sicherung auf einem neuen Volume](backup-restore-new-volume.md)
* [Deaktivieren der Sicherungsfunktionalität für ein Volume](backup-disable.md)
* [Sicherungen eines Volumes löschen](backup-delete.md)
* [Metriken zur Volume-Sicherung](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)


