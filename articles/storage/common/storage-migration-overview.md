---
title: Leitfaden zu Azure Storage
description: 'Übersicht über die Speichermigration: grundlegende Anleitungen für die Speichermigration'
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: 2d02399daf6a7ea56c41f936544697f9df91a62c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601271"
---
# <a name="azure-storage-migration-overview"></a>Übersicht über die Azure Storage-Migration

Dieser Artikel konzentriert sich auf Speichermigrationen zu Azure und bietet Anleitungen zu den folgenden Szenarien der Speichermigration:

- Migration von unstrukturierten Daten, wie z. B. Dateien und Objekten
- Migration von blockbasierten Geräten, z. B. Datenträgern und Storage Area Networks (SANs)

## <a name="migration-of-unstructured-data"></a>Migration unstrukturierter Daten

Die Migration unstrukturierter Daten umfasst folgende Szenarien:

- Dateimigration von Network Attached Storage (NAS) zu einem der Azure-Dateiangebote:
  - [Azure Files](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [Lösungen unabhängiger Softwareanbieter (Independent Software Vendor, ISV)](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md).
- Objektmigration von Objektspeicherlösungen zur Azure-Objektspeicherplattform:
  - [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)

### <a name="migration-phases"></a>Migrationsphasen

Eine vollständige Migration besteht aus verschiedenen Phasen: Ermittlung, Bewertung und Migration.

| Ermittlung | Bewertung | Migration |
| --------- | ---------- | --------- |
| – Ermitteln der zu migrierenden Quellen | – Bewerten des anwendbaren Zieldiensts <br> – Technische Aspekte und Kostenaspekte | – Erste Migrationsphase <br> – Neusynchronisierung <br> – Endgültiger Wechsel |

#### <a name="discovery-phase"></a>Ermittlungsphase

In der Ermittlungsphase bestimmen Sie alle Quellen, die migriert werden müssen, wie SMB-Freigaben, NFS-Exporte oder Objekt-Namespaces. Sie können diese Phase manuell ausführen oder automatisierte Tools verwenden.

#### <a name="assessment-phase"></a>Bewertungsphase

Die Bewertungsphase ist wichtig, um die verfügbaren Optionen für die Migration zu verstehen. Führen Sie die folgenden drei Schritte aus, um das Risiko bei der Migration zu verringern und häufige Fehler zu vermeiden:

| Schritte der Bewertungsphase                     | Optionen                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **Auswählen eines Zielspeicherdienstes**            | - Azure BLOB Storage und Data Lake Storage<br>- Azure Files<br>- Azure NetApp Files<br>- ISV-Lösungen |
| **Auswählen einer Migrationsmethode**                  | - Online<br>- Offline<br> - Kombination beider Optionen                                  |
| **Auswählen des besten Migrationstools für den Auftrag** | - Kommerzielle Tools (Azure und ISV)<br> - Open Source

Es gibt mehrere kommerzielle Tools (ISV), die Sie bei der Bewertungsphase unterstützen können. Sehen Sie sich die [Vergleichsmatrix an](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

##### <a name="choose-a-target-storage-service"></a>Auswählen eines Zielspeicherdienstes

Die Auswahl eines Zielspeicherdienstes hängt von der Anwendung oder den Benutzern ab, die auf die Daten zugreifen. Die richtige Wahl hängt von technischen und finanziellen Aspekten ab. Führen Sie zunächst eine technische Bewertung durch, um mögliche Ziele zu bewerten und zu ermitteln, welche Dienste die Anforderungen erfüllen. Führen Sie als nächstes eine finanzielle Bewertung durch, um die beste Option zu ermitteln.

Um die Auswahl des Zielspeicher-Dienstanbieters für die Migration zu erleichtern, sollten Sie die folgenden Aspekte der einzelnen Dienste auswerten:

- Protokollunterstützung
- Leistungsmerkmale
- Grenzwerte für den Zielspeicherdienst

Das folgende Diagramm ist eine vereinfachte Entscheidungsstruktur, die Sie in Ihrer Entscheidung für den am besten geeigneten Azure-Dateidienst unterstützt. Wenn systemeigene Azure-Dienste die Anforderungen nicht erfüllen, wird dies auf mehrere[unabhängige Softwarehersteller (ISV)](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md) zutreffen.

Nachdem Sie die technische Bewertung abgeschlossen und das richtige Ziel ausgewählt haben, führen Sie eine Kostenbewertung durch, um die kostengünstigste Option zu ermitteln.

![Grundlegende Entscheidungsstruktur bei der Auswahl des richtigen Dateidienstes](./media/storage-migration-overview/files-decision-tree.png)

Um die Entscheidungsstruktur einfach zu halten, sind die Grenzwerte für den Zielspeicherdienst nicht in das Diagramm integriert. Weitere Informationen zu den aktuellen Grenzwerten und dazu, ob Sie entsprechend Ihre Auswahl ändern müssen, finden Sie hier:

- [Speicherkontobegrenzungen](../../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)
- [Blob Storage-Grenzwerte](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-blob-storage-limits)
- [Skalierbarkeits- und Leistungsziele für Azure Files](../files/storage-files-scale-targets.md)
- [Ressourcengrenzwerte für Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-resource-limits.md)

Wenn eine der Grenzwerte einen Blocker für die Verwendung eines Dienstes darstellt, unterstützt Azure mehrere Speicheranbieter, die ihre Lösungen im Azure Marketplace anbieten. Informationen zu überprüften ISV-Partnern, die Dateidienste bereitstellen, finden Sie unter [Azure Storage Partner für den primären und sekundären Speicher](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md).

##### <a name="select-the-migration-method"></a>Auswählen der Migrationsmethode

Es gibt zwei grundlegende Migrationsmethoden für Speichermigrationen.

- **Online**. Die Online-Methode verwendet das Netzwerk für die Datenmigration. Es kann entweder das öffentliche Internet oder [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) verwendet werden. Wenn der Dienst keinen öffentlichen Endpunkt hat, müssen Sie ein VPN mit öffentlichem Internet verwenden.
- **Offline.** Die Offline-Methode verwendet eines der [Azure Data Box](https://azure.microsoft.com/services/databox/)-Geräte.

Die Entscheidung, eine Online Methode anstelle einer Offline Methode zu verwenden, hängt von der verfügbaren Netzwerkbandbreite ab. Die Online-Methode ist dann vorzuziehen, wenn ausreichend Netzwerkbandbreite zur Durchführung einer Migration innerhalb der benötigten Zeit vorhanden ist.

Es ist möglich, beide Methoden zu kombinieren: Die Offline-Methode für die anfängliche Massenmigration und die Online-Methode für die inkrementelle Migration von Änderungen. Die gleichzeitige Verwendung beider Methoden erfordert ein hohes Maß an Koordination und wird daher nicht empfohlen. Wenn Sie beide Methoden verwenden, isolieren Sie die Datensätze, die online migriert werden, von den Datensätzen, die offline migriert werden.

Weitere Informationen zu den verschiedenen Migrationsmethoden und Richtlinien finden Sie unter [Auswählen einer Azure-Lösung für die Datenübertragung](./storage-choose-data-transfer-solution.md) und [Migrieren zu Azure File Shares](../files/storage-files-migration-overview.md).

##### <a name="choose-the-best-migration-tool-for-the-job"></a>Auswählen des besten Migrationstools für den Auftrag

Es gibt verschiedene Migrationstools, mit denen Sie die Migration durchführen können. Es gibt Open Source-Tools, wie AzCopy, Robocopy, xcopy und rsync, und kommerzielle, also kostenpflichtige Tools. Die Liste der verfügbaren kommerziellen Tools und der Vergleich zwischen den Tools ist in unserer [Vergleichsmatrix](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)verfügbar.

Open Source-Tools eignen sich gut für kleinere Migrationen. Für die Migration von Windows-Dateiservern zu Azure Files empfiehlt Microsoft, mit der systemeigenen Funktionalität von Azure Files zu beginnen und [Azure File Sync](/windows-server/manage/windows-admin-center/azure/azure-file-sync)zu verwenden. Für komplexere Migrationen, die unterschiedlichen Quellen, große Datenmengen oder spezielle Anforderungen, wie Einschränkung der maximalen Bandbreite oder detaillierte Berichterstellung mit Auditfunktionen beinhalten, sind kommerzielle Tools die beste Wahl. Diese Tools vereinfachen die Migration und verringern das Risiko erheblich. Die meisten kommerziellen Tools können auch die Ermittlung durchführen, was eine wertvolle Eingabe für die Bewertung bereitstellt.

#### <a name="migration-phase"></a>Phase: Migration

Die Migrationsphase ist der abschließende Migrationsschritt, bei dem die Datenverschiebung und-Migration durchgeführt werden. In der Regel werden Sie die Migrationsphase mehrmals durchlaufen, um einen einfacheren Wechsel durchzuführen. Die Migrationsphase besteht aus folgenden Schritten:

1. **Erste Migrationsphase.** Beim ersten Migrationsschritt werden alle Daten aus der Quelle zum Ziel migriert. Bei diesem Schritt wird der Großteil der zu migrierenden Daten migriert.
2. **Neusynchronisierung.** Bei der Neusynchronisierung werden alle Daten migriert, die nach dem ersten Migrationsschritt geändert wurden. Sie können diesen Schritt mehrmals wiederholen, wenn es mehrere Änderungen gibt. Durch die Ausführung mehrerer Neusynchronisierungen soll die Zeit für den letzten Schritt verkürzt werden. Für inaktive Daten und für Daten, für die es keine Änderungen gibt (z. B. Sicherungs- oder Archivdaten), können Sie diesen Schritt überspringen.
3. **Abschließende Umstellung**. Mit der abschließenden Umstellung wird die aktive Verwendung der Daten von der Quelle zum Ziel umgestellt und die Quelle außer Kraft gesetzt.

Die Dauer der Migration für unstrukturierte Daten hängt von mehreren Aspekten ab. Neben der gewählten Methode sind die wichtigsten Faktoren die Gesamtgröße der Daten und die Verteilung der Dateigröße. Je größer der Datensatz, desto länger die Migrationszeit. Je kleiner die durchschnittliche Dateigröße, desto länger ist die Migrationszeit. Wenn Sie eine große Anzahl kleiner Dateien haben, sollten Sie diese ggf. in größeren Dateien (z. B. als eine .tar-oder .ZIP-Datei) archivieren, um die Gesamtmigrationszeit zu verringern.

## <a name="migration-of-block-based-devices"></a>Migration von blockbasierten Geräten

Die Migration von blockbasierten Geräten erfolgt in der Regel im Rahmen der Migration virtueller Computer oder physischer Hosts. Es ist ein gängiges Missverständnis, Entscheidungen zu Blockspeichern bis nach der Migration zu verzögern. Wenn Sie diese Entscheidungen im Voraus treffen und dabei die Anforderungen an die Workload berücksichtigen, führt dies zu einer reibungsloseren Migration in die Cloud.

Informationen zu den zu migrierenden Workloads und deren Übernahme finden Sie in der [Azure Disk Storage-Dokumentation](../../virtual-machines/disks-types.md)und auf der [Disk Storage Produktseite](https://azure.microsoft.com/services/storage/disks/#resources). Sie erfahren hier, welche Datenträger Ihren Anforderungen entsprechen, und lernen die neuesten Funktionen, wie das [Datenträgerbursting](../../virtual-machines/disk-bursting.md) kennen. Die Migration blockbasierter Geräte kann auf zwei Arten erfolgen:
- Informationen dazu, wie Sie die virtuellen Computer mit den zugrunde liegenden blockbasierten Geräten migrieren, finden Sie in der Dokumentation zu [Azure Migrate](../../migrate/index.yml).
- Verwenden Sie für die Migration von blockbasierten Geräten und komplexeren Anwendungsfällen [Cirrus Migrate Cloud](../solution-integration/validated-partners/data-management/cirrus-data-migration-guide.md).

## <a name="see-also"></a>Siehe auch

- [Auswählen einer Azure-Lösung für die Datenübertragung](./storage-choose-data-transfer-solution.md)
- [Vergleich der kommerziellen Migrationstools](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Migrieren zu Azure File Shares](../files/storage-files-migration-overview.md)
- [Migrieren zu Data Lake Storage mit WANdisco LiveData Platform für Azure](../blobs/migrate-gen2-wandisco-live-data-platform.md)
- [Kopieren oder Verschieben von Daten in Azure Storage mit AzCopy](./storage-use-azcopy-v10.md)
- [Migrieren von großen Datasätzen zu Azure Blob Storage mit AzReplicate](/samples/azure/azreplicate/azreplicate/)
