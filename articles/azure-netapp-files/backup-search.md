---
title: Suchen von Sicherungen von Azure NetApp Files-Volumes | Microsoft-Dokumentation
description: Beschreibt, wie Sicherungen von Azure NetApp Files-Volumes auf Volumeebene und NetApp-Kontoebene angezeigt und gesucht werden.
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
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: 71fcf944f0664c1d3e4d07e96348d109042047a8
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095003"
---
# <a name="search-backups-of-azure-netapp-files-volumes"></a>Suchen von Sicherungen von Azure NetApp Files-Volumes

Sie können Sicherungen anzeigen und auf Volume- oder NetApp-Kontoebene nach einer bestimmten Sicherung suchen.

Die für Momentaufnahmen verwendeten Namen werden beibehalten, wenn die Momentaufnahmen gesichert werden. Namen von Momentaufnahmen enthalten das Präfix `daily`, `weekly` oder `monthly`. Sie enthalten auch den Zeitstempel der Erstellung der Momentaufnahme. Die erste Momentaufnahme bei aktiviertem Sicherungsfeature wird als *Basismomentaufnahme* bezeichnet, deren Namen das Präfix `snapmirror` enthält.

Wenn ein Volume gelöscht wird, werden seine Sicherungen weiterhin aufbewahrt. Die Sicherungen sind im Abschnitt „Sicherungen“ in den zugehörigen NetApp-Konten aufgeführt. Diese Liste enthält alle Sicherungen innerhalb des Abonnements (in allen NetApp-Konten) in der Region. Mit ihrer Hilfe kann eine Sicherung auf einem Volume in einem anderen NetApp-Konto im selben Abonnement wiederhergestellt werden.

## <a name="search-backups-at-volume-level"></a>Suchen von Sicherungen auf Volumeebene    

Sie können Sicherungen auf Volumeebene anzeigen und suchen:

1. Wählen Sie **Volumes** aus.

2. Navigieren Sie zu **Sicherungen,** um Sicherungen für das Volume anzuzeigen.   
    Die Spalte **Typ** zeigt, ob die Sicherung von einer *geplanten* (richtlinienbasierten) oder *manuellen* Sicherung generiert wird.

3. Geben Sie im Feld **Sicherungen suchen** den Sicherungsnamen ein, den Sie suchen möchten.  

    Eine Teilsuche wird unterstützt, sodass Sie nicht den gesamten Sicherungsnamen angeben müssen. Die Suche filtert die Sicherungen anhand der Suchzeichenfolge.

    ![Screenshot mit einer Liste der Sicherungen für ein Volume.](../media/azure-netapp-files/backup-search-volume-level.png)

## <a name="search-backups-at-netapp-account-level"></a>Suchen von Sicherungen auf NetApp-Kontoebene 

Sie können Sicherungen anzeigen und durchsuchen, die in allen NetApp-Konten in einer Region eines Abonnements einem Abonnement zugeordnet sind.   

1. Wählen Sie **NetApp-Konto** aus.

2. Navigieren Sie zu **Sicherungen**.
    Die Spalte **Typ** zeigt, ob die Sicherung von einer *geplanten* (richtlinienbasierten) oder *manuellen* Sicherung generiert wird. 

3. Geben Sie im Feld **Sicherungen suchen** den Sicherungsnamen ein, den Sie suchen möchten.

    * Die Sicherungsliste enthält alle Sicherungen aller Volumes (sowohl aktiv als auch gelöscht), die zu allen NetApp-Konten eines Abonnements gehören.

    * Die Sicherungsliste dient zum Wiederherstellen einer Sicherung auf einem neuen Volume und zum Löschen einer Sicherung. Der Workflow zum Wiederherstellen einer Sicherung entspricht der [Wiederherstellung einer Sicherung auf Volumeebene](backup-restore-new-volume.md). Der Workflow zum Löschen einer Sicherung entspricht der [Löschung einer Sicherung auf Volumeebene](backup-delete.md).

    * Mithilfe der empfohlenen bewährten Benennungskonvention für [manuelle Sicherungen](backup-configure-manual.md) können Sie ermitteln, zu welchem Volume die Sicherung gehört. Diese Benennungskonvention ist besonders nützlich, wenn die Sicherung zu einem Volume gehört, das gelöscht wurde (wobei Sicherungen beibehalten werden).

    ![Screenshot mit einer Liste der Sicherungen für ein NetApp-Konto.](../media/azure-netapp-files/backup-search-account-level.png)

## <a name="next-steps"></a>Nächste Schritte  

* [Grundlegendes zur Azure NetApp Files-Sicherung](backup-introduction.md)
* [Azure NetApp Files-Sicherung: Anforderungen und Überlegungen](backup-requirements-considerations.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Konfigurieren von richtlinienbasierten Sicherungen](backup-configure-policy-based.md)
* [Konfigurieren von manuellen Sicherungen](backup-configure-manual.md)
* [Verwalten von Sicherungsrichtlinien](backup-manage-policies.md)
* [Wiederherstellen einer Sicherung auf einem neuen Volume](backup-restore-new-volume.md)
* [Deaktivieren der Sicherungsfunktionalität für ein Volume](backup-disable.md)
* [Löschen von Sicherungen eines Volumes](backup-delete.md)
* [Volumesicherungsmetriken](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
