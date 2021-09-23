---
title: Optionen für die Datenmigration von StorSimple-Geräten der Serie 5000–7000
description: Bietet eine Übersicht über die Optionen zum Migrieren von Daten aus StorSimple Serie 5000/7000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/02/2021
ms.author: alkohli
ms.openlocfilehash: 31fe8293abb25ed9a6f44660f32398dd44072c37
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123470110"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Optionen zum Migrieren von Daten aus StorSimple Serie 5000/7000 

> [!IMPORTANT]
> Ab dem 9. Juli 2019 wird die StorSimple-Serie 5000/7000 nicht mehr unterstützt. Es wird empfohlen, dass Kunden der StorSimple-Serie 5000/7000 zu einer der in diesem Dokument beschriebenen Alternativen migrieren.

Im Juli 2019 ist für StorSimple Serie 5000/7000 das [Ende des Supports](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) erreicht. Kunden, die StorSimple Serie 5000/7000 ausführen, können auf andere Erstanbieter-Hybriddienste von Azure aktualisieren. In diesem Artikel werden die verfügbaren Azure-Hybridoptionen zum Migrieren von Daten beschrieben. 

## <a name="migration-options"></a>Migrationsoptionen

Für Kunden der StorSimple-Serien 5000–7000 stehen Azure-Optionen und Optionen von Drittanbietern zur Verfügung.

### <a name="azure-options"></a>Azure-Optionen

#### <a name="upgrade-to-storsimple-8000-series"></a>Upgrade auf die StorSimple-Serie 8000

Ein Upgrade auf StorSimple Serie 8000 und damit weiterhin Verwendung der StorSimple-Plattform.  Für diesen Upgradepfad müssen Kunden ihre Geräte der Serie 5000/7000 durch Geräte der Serie 8000 ersetzen. Die Daten werden mit dem Migrationstool aus dem Gerät der Serie 5000/7000 migriert. Sobald die Migration erfolgreich abgeschlossen ist, speichern die Geräte der StorSimple-Serie 8000 weiterhin Daten in Azure Blob Storage. 

Weitere Informationen zum Migrieren von Daten mithilfe von StorSimple Serie 8000 finden Sie unter [Migrieren von Daten von einem Gerät der Serie StorSimple 5000/7000 zu einem Gerät der Serie 8000](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrieren zur Azure-Dateisynchronisierung

Diese völlig neue Migrationsoption ermöglicht es Kunden, die Dateifreigaben ihrer Organisation in Azure Files zu speichern. Diese Dateifreigaben werden dann mithilfe der Azure-Dateisynchronisierung (Azure File Sync, AFS) für den lokalen Zugriff zentralisiert. AFS kann auf einem Windows Server-Host bereitgestellt werden. Die tatsächliche Datenmigration wird dann als Hostkopie oder mit dem Migrationstool ausgeführt.

Weitere Informationen zum Migrieren von Daten mithilfe der Azure-Dateisynchronisierung finden Sie unter [Migrieren von Daten aus StorSimple Serie 5000/7000 zur Azure-Dateisynchronisierung](../storage/files/storage-files-migration-storsimple-8000.md).

#### <a name="migrate-to-azure-netapp-files"></a>Migrieren zu Azure NetApp Files

Die Kunden der StorSimple-Serien 5000 bis 7000 können zu Azure NetApp Files (ANF) mit NetApp Global File Cache (GFC) migrieren, um kritische Daten weiterhin in Azure zu speichern und gleichzeitig Inhalte an Remotestandorten zu verwalten. Kunden können IT-Speicher und -Infrastruktur optimieren und vereinfachen, indem sie die unstrukturierten Daten in Microsoft Azure mithilfe von Azure NetApp Files zentralisieren, um schnellen lokalen und geografisch verteilten Zugriff mit dem Global File Cache von NetApp zu ermöglichen.

Eine Übersicht über die Funktionen, Bereitstellungsmethoden und Migration finden Sie unter [Referenzarchitektur: Global verteilte Enterprise-Dateifreigabe mit Azure NetApp Files und NetApp Global File Cache](https://f.hubspotusercontent20.net/hubfs/525875/r3_NA-581-0521-Ref-Arch-ANF-GFC-StorSimple%20(1)%20(1)%20(2).pdf) von NetApp.<!--Not included: 1) Partnership with MS; 2) How to initiate migration with NetApp (other providers point people to their site).-->

### <a name="third-party-options"></a>Drittanbieteroptionen

#### <a name="migrate-to-panzura-freedom-nas"></a>Migration zu Panzura Freedom NAS

Kunden von StorSimple 5000–7000 können zu Panzura Freedom NAS migrieren, um ihre Daten in Azure zu speichern. Die Panzura Freedom-Lösung stellt eine NAS-Lösung bereit, die Datencenter, Unternehmen sowie öffentliche und private Clouds umfasst. Die Lösung ermöglicht lokale, Hybrid- und In-Cloud-Datenworkflows für NFS, SMB und mobile Clients. 

Diese Migration wird von Panzura unterstützt, und können Kunden als ersten Schritt Migrationsunterstützung auf der [Panzura-Website](https://panzura.com/storsimple-migration/) anfordern.

#### <a name="migrate-to-cohesity"></a>Migrieren zu Cohesity

Cohesity ermöglicht es Ihnen, Daten von Ihrer aktuellen StorSimple 5000–7000-Plattform zu Cohesity DataPlatform in Azure zu migrieren. Cohesity DataPlatform ist eine softwaredefinierte, für das Web skalierte Lösung, die Dateien, Sicherungen, Objekte und VMs in einer einzigen nativen Cloudlösung konsolidiert. Nach der Migration zu DataPlatform können Sie Daten und Apps von der Cloud bis zum Kern mithilfe eines einzelnen Transparenzbereichs verwalten, schützen und bereitstellen. Beginnen Sie mit Cohesity mit höchstens drei Knoten. 

Erfahren Sie mehr zur [Migration zu Cohesity DataPlatform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migrieren zu Nasuni

Nasuni erleichtert es für Kunden von StorSimple 5000-7000, zu migrieren und ihre Daten in Azure zu speichern.  Nasuni ist eine führende Lösung für Azure-basierte NAS-Speicher, die Kunden die Leistung und Sicherheit bietet, die sie bei lokalen Lösungen erwarten – mit Wirtschaftlichkeit und Skalierung der Cloud.  Zusätzlich zum Hochleistungsdatenspeicher erledigen Nasuni und Azure Sicherung und Datenwiederherstellung, während Sie Ihre Daten auf der ganzen Welt mit zentralisierter Dateispeicherverwaltung freigeben und daran zusammenarbeiten können. 

Nasuni verfügt über die Erfahrung, damit Ihre Migration möglichst einfach wird – beginnen Sie noch heute: https://www.nasuni.com/blog-migrating-off-storsimple/

## <a name="migration---frequently-asked-questions"></a>Häufig gestellte Fragen zur Migration

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>Q. Wenn ist das Dienstende der StorSimple-Geräte der Serie 5000/7000 erreicht? 

A. Das [Dienstende](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) der StorSimple-Serie 5000/7000 ist Juli 2019 erreicht. Das Dienstende bedeutet, dass Microsoft nach Juli 2019 weder für Hardware noch Software dieser Geräte Support leisten kann. Sie sollten jetzt unbedingt beginnen, einen Plan zum Migrieren der Daten von Ihren Geräten zu formulieren.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. Was geschieht mit den Daten, die ich in Azure gespeichert habe?  

A. Sie können die Daten weiterhin in Azure verwenden, nachdem Sie sie zu einem neueren Dienst migriert haben. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. Was geschieht mit den Daten, die ich lokal auf meinem StorSimple-Gerät gespeichert habe? 

A. Die auf dem lokalen Gerät gespeicherten Daten können wie in den Dokumenten für die Migration beschrieben auf den neueren Dienst kopiert werden.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>Q. Was geschieht, wenn ich mein Gerät der StorSimple-Serie 5000/7000 behalten möchte? 

A. Die Dienste könnten zwar weiterhin funktionieren, aber Microsoft wird nicht mehr in der Lage sein, Support für Hardware und Software zu leisten. Die Migration wird im Interesse der Geschäftskontinuität dringend empfohlen.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>Q. Welche Optionen sind verfügbar, um Daten von StorSimple-Geräten der Serie 5000/7000 zu migrieren? 

A. Abhängig von ihren Szenarios können Benutzer der StorSimple-Serie 5000/7000 die folgenden Migrationsoptionen nutzen. 

 - **Upgrade auf Serie 8000**: Verwenden Sie diese Option, wenn Sie die StorSimple-Plattform weiterhin nutzen möchten. 
 - **Migrieren zur Azure-Dateisynchronisierung**: Verwenden Sie diese Option, wenn Sie zum nativen Azure-Format wechseln möchten. Sie können die Azure-Dateisynchronisierung zur zentralen Verwaltung von Dateifreigaben verwenden. 

Um hier nicht aufgeführte Migrationsoptionen zu erörtern, kontaktieren Sie den Microsoft-Support.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. Wird die Migration zu anderen Speicherlösungen unterstützt?

A. Ja. Migration zu anderen Speicherlösungen mithilfe der Hostkopie der Daten wird unterstützt.

### <a name="q-is-migration-supported-by-microsoft"></a>Q. Wird die Migration von Microsoft unterstützt? 

A. Die Migration von der Serie 5000 oder 7000 wird vollständig unterstützt. Microsoft empfiehlt Ihnen generell, sich vor Beginn der Migration an den Support zu wenden. Die Migration ist derzeit ein unterstützter Vorgang. Wenn Sie beabsichtigen, Daten von Ihrem StorSimple-Gerät der Serie 5000/7000 zu migrieren, [öffnen Sie ein Supportticket](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>Q. Welches Preismodell gilt für die beiden Migrationsoptionen?

A. Die Kosten für die Migration variieren abhängig von der gewählten Option. Die Migration selbst ist zwar kostenlos, aber wenn Sie sich für ein Upgrade auf die StorSimple-Serie 8000 entscheiden, entstehen Kosten für das Hardwaregerät. 

Ebenso können bei Nutzung der Azure-Dateisynchronisierung Abonnementgebühren für den Dienst anfallen. In jedem Fall müssen Kunden auch die laufenden Speicherkosten zahlen. Eine Schätzung können Sie anhand der folgenden Informationen vornehmen: 
- [StorSimple-Preise](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS-Preise]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>Q.  Wie lange dauert es, bis die Migration abgeschlossen ist?

A. Die Dauer der Datenmigration hängt von der Menge der Daten und der ausgewählten Upgradeoption ab. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>Q. An welchem Datum endet der Support für die StorSimple 8000-Serie?

A. Das Datum, an dem der Support für die StorSimple 8000-Serie endet, wird [hier](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) veröffentlicht.


## <a name="next-steps"></a>Nächste Schritte
 - [Migrieren von Daten von einem Gerät der Serie StorSimple 5000/7000 zu einem Gerät der Serie 8000](storsimple-8000-migrate-from-5000-7000.md)
 - [Migrieren von Daten aus StorSimple Serie 5000/7000 zur Azure-Dateisynchronisierung](../storage/files/storage-files-migration-storsimple-8000.md)
