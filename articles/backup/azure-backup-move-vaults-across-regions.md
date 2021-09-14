---
title: Verschieben eines Azure Recovery Services-Tresors in eine andere Region
description: In diesem Artikel erfahren Sie, wie Sie nach dem Verschieben der Ressourcen zwischen Regionen kontinuierliche Sicherungen sicherstellen.
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: faf6ad49234d9753a0479151bf931714a1eba226
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115489"
---
# <a name="back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>Sichern von Ressourcen im Recovery Services-Tresor nach dem Verschieben zwischen Regionen

Azure Resource Mover unterstützt die Verschiebung von mehreren Ressourcen über Regionen hinweg. Wenn Sie Ihre Ressourcen aus einer Region in eine andere verschieben, können Sie sicherstellen, dass Ihre Ressourcen geschützt bleiben. Da Azure Backup den Schutz mehrerer Workloads unterstützt, müssen Sie möglicherweise einige Schritte ausführen, um weiterhin die gleiche Schutzebene in der neuen Region zu nutzen.

Die ausführlichen Schritte dazu finden Sie in den folgenden Abschnitten.

>[!Note]
>Azure Backup unterstützt derzeit nicht das Verschieben von Sicherungsdaten aus einem Recovery Services-Tresor in einen anderen. Um Ihre Ressource in der neuen Region zu schützen, muss die Ressource registriert und in einem neuen/vorhandenen Tresor in der neuen Region gespeichert werden. Wenn Sie Ihre Ressourcen aus einer Region in eine andere verschieben, können Sicherungsdaten in Ihren vorhandenen Recovery Services-Tresoren in der älteren Region entsprechend Ihren Anforderungen beibehalten/gelöscht werden. Wenn Sie Daten in den alten Tresoren beibehalten möchten, entstehen entsprechend Sicherungsgebühren.

## <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>Sichern eines virtuellen Azure-Computers nach regionsübergreifendem Verschieben

Wenn ein virtueller Azure-Computer (VM), der durch einen Recovery Services-Tresor geschützt wurde, von einer Region in eine andere verschoben wird, kann er nicht mehr in den älteren Tresor gesichert werden. Für die Sicherungen im alten Tresor treten die Fehler **BCMV2VMNotFound** oder [**ResourceNotFound**](/azure/backup/backup-azure-vms-troubleshoot#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found) auf.

Führen Sie die folgenden Schritte aus, um Ihren virtuellen Computer in der neuen Region zu schützen:

1. Bevor Sie die VM verschieben, [wählen Sie sie auf der Registerkarte **Sicherungselemente** im Dashboard des vorhandenen Tresors aus](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud), und wählen Sie **Schutz beenden**, gefolgt von „Daten beibehalten/löschen“ (je nach Anforderung). Wenn die Sicherungsdaten für einen virtuellen Computer mit „Daten beibehalten“ beendet werden, bleiben die Wiederherstellungspunkte für immer erhalten und unterliegen keiner Richtlinie. Dadurch wird sichergestellt, dass Ihre Sicherungsdaten immer für die Wiederherstellung verfügbar sind.

   >[!Note]
   >Für die Beibehaltung von Daten im älteren Tresor werden Sicherungsgebühren berechnet. Wenn Sie Daten nicht mehr aufbewahren möchten, um Kosten zu vermeiden, müssen Sie die beibehaltenen Sicherungsdaten mithilfe der Option [Daten löschen](/azure/backup/backup-azure-manage-vms#delete-backup-data) löschen.

1. Verschieben Sie Ihren virtuellen Computer mithilfe von [Azure Resource Mover](/azure/resource-mover/tutorial-move-region-virtual-machines) in eine neue Region.

1. Beginnen Sie mit dem Schützen Ihres virtuellen Computers in einem neuen oder vorhandenen Recovery Services-Tresor in der neuen Region.
   Wenn Sie ihre älteren Sicherungen wiederherstellen müssen, können Sie dies weiterhin über Ihren alten Recovery Services-Tresor durchführen, sofern Sie sich für die Beibehaltung der Sicherungsdaten entschieden haben. 

Mit den oben genannten Schritten soll sichergestellt werden, dass Ihre Ressourcen auch in der neuen Region gesichert werden.

## <a name="back-up-azure-file-share-after-moving-across-regions"></a>Sichern der Azure-Dateifreigabe nach regionsübergreifendem Verschieben

Informationen zum Verschieben ihrer Speicherkonten zusammen mit den darin enthaltenen Dateifreigaben aus einer Region in eine andere finden Sie unter [Verschieben eines Azure Storage-Kontos in eine andere Region](/azure/storage/common/storage-account-move).

>[!Note]
>Wenn die Azure-Dateifreigabe regionsübergreifend kopiert wird, werden die zugehörigen Momentaufnahmen nicht mit ihr verschoben. Um die Momentaufnahmedaten in die neue Region zu verschieben, müssen Sie die einzelnen Dateien und Verzeichnisse der Momentaufnahmen mit [AzCopy](/azure/storage/common/storage-use-azcopy-files#copy-all-file-shares-directories-and-files-to-another-storage-account) in das Speicherkonto in der neuen Region verschieben.

Azure Backup bietet heute [eine Lösung zur Verwaltung von Momentaufnahmen](/azure/backup/backup-afs#discover-file-shares-and-configure-backup) für Azure Files. Dies bedeutet, dass Sie die Dateifreigabedaten nicht in die Recovery Services-Tresore verschieben. Da die Momentaufnahmen nicht mit Ihrem Speicherkonto verschoben werden, verfügen Sie nur über alle Sicherungen (Momentaufnahmen) tatsächlich nur in der vorhandenen Region, und sie werden durch den vorhandenen Tresor geschützt. Sie können jedoch sicherstellen, dass die neuen Dateifreigaben, die Sie in der neuen Region erstellen, mit folgenden Schritten durch Azure Backup geschützt werden:

1. Beginnen Sie mit dem Schützen der Azure-Dateifreigabe, die in das neue Speicherkonto in einem neuen oder vorhandenen Recovery Services-Tresor in der neuen Region kopiert wurde.  

1. Nachdem die Azure-Dateifreigabe in die neue Region kopiert wurde, können Sie den Schutz beenden und die Momentaufnahmen (und die entsprechenden Wiederherstellungspunkte) der ursprünglichen Azure-Dateifreigabe entsprechend Ihren Anforderungen beibehalten/löschen. Wählen Sie dazu Ihre Dateifreigabe auf der Registerkarte [Sicherungselemente](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) des Dashboards des ursprünglichen Tresors aus. Wenn die Sicherungsdaten für eine Azure-Dateifreigabe mit „Daten beibehalten“ beendet werden, bleiben die Wiederherstellungspunkte für immer erhalten und unterliegen keiner Richtlinie.
   
   Dadurch wird sichergestellt, dass Ihre Momentaufnahmen immer für die Wiederherstellung aus dem älteren Tresor verfügbar sind. 
 
## <a name="back-up-sql-server-in-azure-vmsap-hana-in-azure-vm"></a>Sichern von SQL Server in Azure VM/SAP HANA in Azure VM

Wenn Sie einen virtuellen Computer mit SQL- oder SAP HANA-Servern in eine andere Region verschieben, können die SQL- und SAP HANA-Datenbanken auf diesen VMs nicht mehr im Tresor der früheren Region gesichert werden. Führen Sie die folgenden Schritte aus, um die auf einem virtuellen Azure-Computer in der neuen Region ausgeführten SQL- und SAP HANA-Server zu schützen:
 
1. Bevor Sie einen virtuellen Computer, auf dem SQL Server/SAP HANA ausgeführt wird, in eine neue Region verschieben, wählen Sie ihn auf der Registerkarte [Sicherungselemente](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) des Dashboards des vorhandenen Tresors aus, und wählen Sie dann _die Datenbanken_ aus, für die die Sicherung beendet werden muss. Wählen Sie **Schutz beenden** und dann entsprechend Ihren Anforderungen „Daten aufbewahren/löschen“ aus. Wenn die Sicherungsdaten mit „Daten beibehalten“ beendet werden, bleiben die Wiederherstellungspunkte für immer erhalten und unterliegen keiner Richtlinie. Dadurch wird sichergestellt, dass Ihre Sicherungsdaten immer für die Wiederherstellung verfügbar sind.

   >[!Note]
   >Für die Beibehaltung von Daten im älteren Tresor werden Sicherungsgebühren berechnet. Wenn Sie Daten nicht mehr aufbewahren möchten, um Kosten zu vermeiden, müssen Sie die beibehaltenen Sicherungsdaten mithilfe der Option [Daten löschen](/azure/backup/backup-azure-manage-vms#delete-backup-data) löschen.

1. Verschieben Sie den virtuellen Computer, auf dem SQL Server/SAP HANA ausgeführt wird, mit [Azure Resource Mover](/azure/resource-mover/tutorial-move-region-virtual-machines) in die neue Region.

1. Beginnen Sie mit dem Schützen des virtuellen Computers in einem neuen/vorhandenen Recovery Services-Tresor in der neuen Region. Wenn Sie ihre älteren Sicherungen wiederherstellen müssen, ist dies über Ihren alten Recovery Services-Tresor weiterhin möglich.
 
Mit den oben genannten Schritten soll sichergestellt werden, dass Ihre Ressourcen auch in der neuen Region gesichert werden.