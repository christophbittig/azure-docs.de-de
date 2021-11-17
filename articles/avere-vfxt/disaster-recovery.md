---
title: Anleitung zur Notfallwiederherstellung für Avere vFXT for Azure
description: Es wird beschrieben, wie Sie Daten in Avere vFXT for Azure vor dem versehentlichen Löschen oder vor Ausfällen schützen.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 5ae2ac35cc01ed8dfbc34ab2ff90117c46298980
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325560"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Anleitung zur Notfallwiederherstellung für Avere vFXT for Azure

In diesem Artikel werden Strategien zum Schützen Ihres Avere vFXT for Azure-Workflows beschrieben, und er enthält eine Anleitung zum Sichern von Daten, um die Wiederherstellung nach Fehlern oder Ausfällen zu ermöglichen.

Bei Avere vFXT for Azure werden Daten temporär im eigenen Speicher abgelegt. Daten werden langfristig in Back-End-Speichersystemen gespeichert: in lokalen Hardwaresystemen, Azure-Blobspeichercontainern oder an beiden Orten.

Berücksichtigen Sie beim Schützen vor Ausfällen und möglichem Datenverlust diese vier Aspekte:

* Schützen vor Ausfallzeiten, wenn ein Avere vFXT for Azure-System nicht verfügbar ist
* Schützen von Daten im Clustercache
* Schützen von Daten im Back-End-NAS-Hardwarespeicher
* Schützen von Daten im Back-End-Azure-Blobcloudspeicher

Jeder Avere vFXT for Azure-Kunde muss einen eigenen umfassenden Plan für die Notfallwiederherstellung erstellen, in dem diese Punkte abgedeckt sind. Sie können in die Anwendungen, die Sie mit dem vFXT-Cluster verwenden, auch Resilienz implementieren. Weitere Informationen finden Sie mit den Links unter [Nächste Schritte](#next-steps).

## <a name="protect-against-downtime"></a>Schützen vor Ausfallzeiten

Redundanz ist wie folgt in das Avere vFXT for Azure-Produkt integriert:

* Der Cluster ist hoch verfügbar, und für einzelne Knoten kann mit nur geringfügigen Störungen ein Failover ausgeführt werden.
* Im Cache geänderte Daten werden zur langfristigen Speicherung regelmäßig in Back-End-Kernspeichereinheiten (Hardware-NAS oder Azure Blob) geschrieben.

Jeder Avere vFXT for Azure-Cluster muss sich in einer separaten Verfügbarkeitszone befinden, aber Sie können redundante Cluster in unterschiedlichen Zonen oder Regionen verwenden, damit bei einem regionalen Ausfall schnell der Zugriff ermöglicht werden kann.

Darüber hinaus können Sie Speichercontainer in mehreren Regionen anordnen, falls Sie Bedenken haben, dass der ununterbrochene Zugriff auf die Daten nicht möglich ist. Beachten Sie hierbei aber, dass für Transaktionen zwischen Regionen eine höhere Latenz und höhere Kosten als für Transaktionen gilt, die innerhalb einer Region durchgeführt werden.

## <a name="protect-data-in-the-cluster-cache"></a>Schützen von Daten im Clustercache

Zwischengespeicherte Daten werden vor dem regulären Herunterfahren immer in die Kernspeichereinheiten geschrieben, aber beim ungeplanten Herunterfahren können geänderte Daten im Cache verloren gehen.

Falls Sie den Cluster nur zum Optimieren von Dateilesevorgängen verwenden, besteht nicht die Gefahr, dass Änderungen verloren gehen. Wenn Sie den Cluster auch zum Zwischenspeichern von Dateiänderungen (Schreibvorgängen) nutzen, sollten Sie überprüfen, ob ggf. die [Cacherichtlinien](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) der Kernspeichereinheiten angepasst werden müssen.<!-- link to legacy doc --> Darin können Sie angeben, wie häufig Daten in den Langzeitspeicher geschrieben werden.

Im Allgemeinen sollte es in Ihrem Wiederherstellungsplan um die Sicherung der Back-End-Speichersysteme gehen, die mehr Daten enthalten und normalerweise wichtiger sind, was die Wiederherstellung Ihres Workflows nach einem Ausfall betrifft.

## <a name="protect-data-in-nas-core-filers"></a>Schützen von Daten in NAS-Kernspeichereinheiten

Nutzen Sie bewährte Methoden zum Schützen von Dateien, die in einer lokalen Kernspeichereinheit auf NAS-Hardware gespeichert sind, z. B. Momentaufnahmen und vollständige Sicherungen gemäß der Empfehlung des NAS-Anbieters. Eine Beschreibung der Notfallwiederherstellung für diese Kernspeichereinheiten würde den Rahmen dieses Artikels sprengen.

## <a name="protect-data-in-azure-blob-storage"></a>Schützen von Daten in Azure-Blobspeicher

Bei Avere vFXT for Azure wird lokal redundanter Speicher (LRS) für Azure-Blob-Kernspeichereinheiten genutzt. Dies bedeutet, dass die Daten in Ihren Blobcontainern zum Schutz vor vorübergehenden Hardwareausfällen in einem Rechenzentrum automatisch kopiert werden.

Dieser Abschnitt enthält Tipps dazu, wie Sie Ihre Daten in Blobspeicher noch besser vor seltenen regionsweiten Ausfällen oder dem versehentlichen Löschen schützen.

Bewährte Methoden zum Schützen von Daten in Azure-Blobspeicher:

* Kopieren Sie Ihre kritischen Daten häufig in ein anderes Speicherkonto in einer anderen Region (gemäß Ihrem Plan für die Notfallwiederherstellung).
* Kontrollieren Sie den Zugriff auf die Daten aller Zielsysteme, um das versehentliche Löschen oder Beschädigungen zu verhindern. Erwägen Sie den Einsatz von [Ressourcensperren](../azure-resource-manager/management/lock-resources.md) für Datenspeicher.
* Aktivieren Sie für Ihre Blob-Kernspeichereinheiten das Feature [Cloudmomentaufnahme](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html) von Avere vFXT for Azure.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Kopieren der Daten von Avere vFXT-Kernspeichereinheiten in ein Sicherungskonto

Führen Sie die folgenden Schritte aus, um eine Datensicherung unter einem anderen Konto einzurichten.

1. Generieren Sie bei Bedarf einen neuen Verschlüsselungsschlüssel, und speichern Sie ihn an einem sicheren Ort außerhalb der betroffenen Systeme.

   Wenn Ihre Daten vom Avere vFXT for Azure-Cluster verschlüsselt werden, sollten Sie einen neuen Verschlüsselungsschlüssel generieren, bevor Sie die Daten in ein anderes Speicherkonto kopieren. Bewahren Sie diesen Schlüssel und das zugehörige Kennwort an einem sicheren Ort auf, der von einem regionalen Ausfall nicht betroffen ist.

   Sie müssen diesen Schlüssel angeben, wenn Sie den Container einem Cluster hinzufügen. Dies gilt auch, wenn Sie ihn dem ursprünglichen Cluster erneut hinzufügen.

   Lesen Sie den Artikel zu den [Verschlüsselungseinstellungen für die Cloud](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html),<!-- link to legacy doc site --> um ausführliche Informationen zu erhalten.

   Falls für Ihren Container nur die integrierte Verschlüsselung von Azure verwendet wird, können Sie diesen Schritt überspringen.

1. Entfernen Sie die Kernspeichereinheit aus dem System. Hierdurch wird der Cluster gezwungen, alle geänderten Daten in den Back-End-Speicher zu schreiben.

   Sie müssen die Kernspeichereinheit nach dem Sichern zwar wieder hinzufügen, aber durch das Entfernen können Sie auf bestmögliche Weise sicherstellen, dass alle Daten vollständig auf das Back-End geschrieben werden. (Bei der Option „Suspend“ (Anhalten) kann es passieren, dass geänderte Daten im Cache verbleiben.) <!-- xxx true? or just metadata? -->

   Notieren Sie sich den Namen und die Verbindungsinformationen der Kernspeichereinheit (auf der Seite **Namespace** in der Systemsteuerung), damit Sie die Replikation durchführen können, wenn Sie den Container nach dem Sichern wieder hinzufügen.

   Verwenden Sie die Systemsteuerung des Clusters, um die Kernspeichereinheit zu entfernen. [Öffnen Sie die Systemsteuerung des Clusters](avere-vfxt-cluster-gui.md), und wählen Sie **Core filer** > **Manage core filers** (Kernspeichereinheit > Kernspeichereinheiten verwalten) aus. Suchen Sie nach dem Speichersystem, das Sie sichern möchten, und verwenden Sie die Schaltfläche **Entfernen**, um es aus dem Cluster zu löschen.

1. Erstellen Sie einen neuen, leeren Blobspeichercontainer unter einem anderen Speicherkonto in einer anderen Region.

1. Verwenden Sie ein beliebiges geeignetes Tool, um die Daten der Kernspeichereinheit in den neuen Container zu kopieren. Beim Kopieren müssen die Daten ohne Änderungen repliziert werden, und das von Avere vFXT for Azure verwendete proprietäre Format des Clouddateisystems darf nicht beschädigt werden. Beispiele für Azure-basierte Tools sind [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md) und [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Nachdem Sie die Daten in den Sicherungscontainer kopiert haben, fügen Sie den ursprünglichen Container wieder dem Cluster hinzu. Dies ist unter [Konfigurieren von Speicher](avere-vfxt-add-storage.md) beschrieben.

   * Verwenden Sie den gleichen Namen bzw. die gleichen Verbindungsinformationen für die Kernspeichereinheit, damit die Clientworkflows nicht geändert werden müssen.
   * Legen Sie den Wert von **Bucket contents** (Bucketinhalt) auf die vorhandene Datenoption fest.
   * Wenn der Container vom Cluster verschlüsselt wurde, müssen Sie für seinen Inhalt den aktuellen Verschlüsselungsschlüssel eingeben. (Dies ist der Schlüssel, den Sie in Schritt 1 aktualisiert haben.)

Für Sicherungen, die nach der ersten Sicherung durchgeführt werden, müssen Sie keinen neuen Speichercontainer erstellen. Sie sollten aber erwägen, bei jedem Sicherungsvorgang einen neuen Verschlüsselungsschlüssel zu generieren, um sicherzustellen, dass der aktuelle Schlüssel an einem Ihnen bekannten Ort gespeichert ist.

### <a name="access-a-backup-data-source-during-an-outage"></a>Zugreifen auf eine Quelle mit Sicherungsdaten während eines Ausfalls

Gehen Sie wie folgt vor, um über einen Avere vFXT for Azure-Cluster auf den Sicherungscontainer zuzugreifen:

1. Erstellen Sie bei Bedarf einen neuen Avere vFXT for Azure-Cluster in einer nicht betroffenen Region.

   > [!TIP]
   > Beim Erstellen eines Avere vFXT for Azure-Clusters können Sie eine Kopie der Erstellungsvorlage und der Parameter speichern. Wenn Sie diese Informationen beim Erstellen Ihres primären Clusters speichern, können Sie sie nutzen, um einen Ersatzcluster mit den gleichen Eigenschaften zu erstellen. Klicken Sie auf der Seite [Zusammenfassung](avere-vfxt-deploy.md#validation-and-purchase) auf den Link **Vorlage und Parameter herunterladen**. Speichern Sie die Informationen in einer Datei, bevor Sie den Cluster erstellen.

1. Fügen Sie eine neue Cloud-Kernspeichereinheit hinzu, die auf den duplizierten Blobcontainer verweist.

   Geben Sie im Assistenten für die Erstellung von Kernspeichereinheiten unter der Einstellung **Bucket contents** (Bucketinhalt) unbedingt an, dass der Zielcontainer bereits Daten enthält. (Sie sollten eine Warnung erhalten, falls Sie diese Einstellung versehentlich auf **Empty** (Leer) festgelegt lassen.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Aktualisieren Sie bei Bedarf die Clients, damit anstelle des ursprünglichen Clusters der neue Cluster bzw. eine neue Kernspeichereinheit bereitgestellt wird. (Wenn Sie die Ersatz-Kernspeichereinheit mit dem gleichen Namen und Verbindungspfad wie für den ursprünglichen Container hinzufügen, müssen Sie die Clientprozesse nur aktualisieren, wenn Sie den neuen Cluster unter einer neuen IP-Adresse bereitstellen.)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Anpassen der Einstellungen für Avere vFXT for Azure finden Sie unter [Clusteroptimierung](avere-vfxt-tuning.md).
* Erfahren Sie mehr zur Notfallwiederherstellung und zur Erstellung resilienter Anwendungen in Azure:

  * [Technischer Leitfaden zur Resilienz in Azure](/azure/architecture/reliability/architect)
  * [Wiederherstellung nach einer regionsweiten Dienstunterbrechung](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Notfallwiederherstellung und Hochverfügbarkeit für Azure-Anwendungen](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->
