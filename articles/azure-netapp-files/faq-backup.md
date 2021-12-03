---
title: Häufig gestellte Fragen zur Azure NetApp Files-Sicherung | Microsoft-Dokumentation
description: Beantwortet häufig gestellte Fragen (FAQs) zur Verwendung des Azure NetApp Files-Sicherungsfeatures.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: cec399e00215994e55e06430f2170d8c19c20b03
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270552"
---
# <a name="azure-netapp-files-backup-faqs"></a>Häufig gestellte Fragen zur Azure NetApp Files-Sicherung

In diesem Artikel werden häufig gestellte Fragen (FAQs) zur Verwendung des [Azure NetApp Files-Sicherung](backup-introduction.md)sfeatures beantwortet. 

## <a name="when-do-my-backups-occur"></a>Wann werden meine Sicherungen durchgeführt?   

Azure NetApp Files-Sicherungen werden innerhalb eines zufälligen Zeitraums gestartet, nachdem die Frequenz einer Sicherungsrichtlinie eingegeben wurde. Beispielsweise werden wöchentliche Sicherungen sonntags innerhalb eines zufällig zugewiesenen Intervalls nach 00:00 Uhr initiiert. Diese Zeitsteuerung kann von den Benutzer*innen derzeit nicht geändert werden. Die Baselinesicherung wird initiiert, sobald Sie dem Volume die Sicherungsrichtlinie zuweisen.

## <a name="what-happens-if-a-backup-operation-encounters-a-problem"></a>Was geschieht, wenn bei einem Sicherungsvorgang ein Problem auftritt?

Wenn während eines Sicherungsvorgangs ein Problem auftritt, wird der Vorgang für die Azure NetApp Files-Sicherung automatisch erneut ausgeführt, ohne dass eine Benutzerinteraktion erforderlich ist. Wenn bei Wiederholungsversuchen weiterhin Fehler auftreten, meldet die Azure NetApp Files-Sicherung, dass der Vorgang nicht erfolgreich durchgeführt wurde.

## <a name="can-i-change-the-location-or-storage-tier-of-my-backup-vault"></a>Kann ich den Speicherort oder die Speicherebene meines Sicherungstresors ändern?

Nein, Azure NetApp Files verwaltet den Speicherort der Sicherungsdaten automatisch in Azure Storage. Weder dieser Speicherort noch die Azure-Speicherebene kann vom Benutzer geändert werden.

## <a name="what-types-of-security-are-provided-for-the-backup-data"></a>Welche Arten von Sicherheit werden für die Sicherungsdaten bereitgestellt?

Azure NetApp Files verwendet während der Codierung der empfangenen Sicherungsdaten die AES-256-Bit-Verschlüsselung. Darüber hinaus werden die verschlüsselten Daten mithilfe von HTTPS-TLSv1.2-Verbindungen sicher an den Azure-Speicher übertragen. Die Azure NetApp Files-Sicherung ist von der integrierten Funktion des Azure Storage-Kontos für die Verschlüsselung ruhender Daten zum Speichern der Sicherungsdaten abhängig.

## <a name="what-happens-to-the-backups-when-i-delete-a-volume-or-my-netapp-account"></a>Was geschieht mit den Sicherungen, wenn ich ein Volume oder mein NetApp-Konto lösche? 

 Wenn Sie ein Azure NetApp Files-Volume löschen, werden die Sicherungen beibehalten. Wenn Sie die Sicherungen nicht beibehalten möchten, deaktivieren Sie die Sicherungen vor dem Löschen des Volumes. Wenn Sie ein NetApp-Konto löschen, werden die Sicherungen weiterhin beibehalten und unter anderen NetApp-Konten desselben Abonnements angezeigt, sodass sie noch für die Wiederherstellung verfügbar sind. Wenn Sie alle NetApp-Konten unter einem Abonnement löschen, müssen Sie sicherstellen, dass Sie Sicherungen deaktivieren, bevor Sie alle Volumes unter allen NetApp-Konten löschen.

## <a name="whats-the-systems-maximum-backup-retries-for-a-volume"></a>Welche maximale Anzahl von wiederholten Sicherungsversuchen für ein Volume gibt das System vor?  

Das System unternimmt bei der Verarbeitung eines geplanten Sicherungsauftrags 10 Wiederholungsversuche. Wenn der Auftrag fehlschlägt, gibt das System einen Fehler für den Sicherungsvorgang aus. Bei geplanten Sicherungen (basierend auf der konfigurierten Richtlinie) versucht das System, die Daten einmal pro Stunde zu sichern. Wenn neue Momentaufnahmen verfügbar sind, die nicht übertragen wurden (oder bei denen beim letzten Versuch ein Fehler aufgetreten ist), werden diese Momentaufnahmen bei der Übertragung berücksichtigt. 

## <a name="next-steps"></a>Nächste Schritte  

- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Häufig gestellte Fragen zum Netzwerk](faq-networking.md)
- [Häufig gestellte Fragen zur Sicherheit](faq-security.md)
- [Häufig gestellte Fragen zur Leistung](faq-performance.md)
- [Häufig gestellte Fragen zu NFS](faq-nfs.md)
- [Häufig gestellte Fragen zu SMB](faq-smb.md)
- [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)
- [Häufig gestellte Fragen zu Datenmigration und -schutz](faq-data-migration-protection.md)
- [Häufig gestellte Fragen zur App-Resilienz](faq-application-resilience.md)
- [Häufig gestellte Fragen zur Integration](faq-integration.md)
