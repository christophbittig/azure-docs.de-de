---
title: Verschieben eines Azure Recovery Services-Tresors in eine andere Region
description: In diesem Artikel erfahren Sie, wie Sie nach dem Verschieben der Ressourcen zwischen Regionen kontinuierliche Sicherungen sicherstellen.
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: how-to
ms.openlocfilehash: 2b3804a0a73329d7b2e4e2449fe773918791dcea
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264939"
---
# <a name="back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>Sichern von Ressourcen im Recovery Services-Tresor nach dem Verschieben zwischen Regionen

Azure Resource Mover unterstützt die Verschiebung von mehreren Ressourcen über Regionen hinweg. Wenn Sie Ihre Ressourcen aus einer Region in eine andere verschieben, können Sie sicherstellen, dass Ihre Ressourcen geschützt bleiben. Da Azure Backup den Schutz mehrerer Workloads unterstützt, müssen Sie möglicherweise einige Schritte ausführen, um weiterhin die gleiche Schutzebene in der neuen Region zu nutzen.

Die ausführlichen Schritte dazu finden Sie in den folgenden Abschnitten.

>[!Note]
>Azure Backup unterstützt derzeit nicht das Verschieben von Sicherungsdaten aus einem Recovery Services-Tresor in einen anderen. Um Ihre Ressource in der neuen Region zu schützen, muss die Ressource registriert und in einem neuen/vorhandenen Tresor in der neuen Region gespeichert werden. Wenn Sie Ihre Ressourcen von einer Region in eine andere verschieben, können die Sicherungsdaten in den vorhandenen Recovery Services-Datenspeichern in der älteren Region je nach Bedarf beibehalten oder gelöscht werden. Wenn Sie Daten in den alten Tresoren beibehalten möchten, entstehen entsprechend Sicherungsgebühren.

## <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>Sichern eines virtuellen Azure-Computers nach regionsübergreifendem Verschieben

Wenn ein virtueller Azure-Computer (VM), der durch einen Recovery Services-Tresor geschützt wurde, von einer Region in eine andere verschoben wird, kann er nicht mehr in den älteren Tresor gesichert werden. Für die Sicherungen im alten Tresor treten die Fehler **BCMV2VMNotFound** oder [**ResourceNotFound**](./backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found) auf. Informationen über den Schutz Ihrer VMs in der neuen Region finden Sie in den folgenden Abschnitten.

### <a name="prepare-to-move-azure-vms"></a>Vorbereiten der Verschiebung von Azure-VMs

Bevor Sie eine VM verschieben, stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:

1. Lesen Sie die [Voraussetzungen für die VM-Verschiebung](../resource-mover/tutorial-move-region-virtual-machines.md#prerequisites) und stellen Sie sicher, dass die VM für die Verschiebung geeignet ist.
1. Wählen Sie die VM auf der [Registerkarte **„Sicherungselemente“** des Dashboards](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) des vorhandenen Tresors aus und wählen Sie **„Stoppschutz“** , gefolgt von „Daten aufbewahren/löschen“ gemäß Ihren Anforderungen. Wenn die Sicherungsdaten für einen virtuellen Computer mit „Daten beibehalten“ beendet werden, bleiben die Wiederherstellungspunkte für immer erhalten und unterliegen keiner Richtlinie. Dadurch wird sichergestellt, dass Ihre Sicherungsdaten immer für die Wiederherstellung verfügbar sind.
   >[!Note]
   >Für die Beibehaltung von Daten im älteren Tresor werden Sicherungsgebühren berechnet. Wenn Sie Daten nicht mehr aufbewahren möchten, um die Abrechnung zu vermeiden, müssen Sie die beibehaltenen Sicherungsdaten mithilfe der Option [Daten löschen](./backup-azure-manage-vms.md#delete-backup-data) löschen.
1. Stellen Sie sicher, dass die VMs aktiviert sind. Alle Datenträger der VMs, die in der Zielregion verfügbar sein müssen, werden angeschlossen und in den VMs initialisiert.
1. Stellen Sie sicher, dass die VMs über die neuesten vertrauenswürdigen Stammzertifikate und eine aktualisierte Zertifikatswiderrufsliste (CRL) verfügen. Dazu gehen Sie wie folgt vor:
   - Installieren Sie auf Windows-VMs die aktuellen Windows-Updates.
   - Beachten Sie bei Linux-VMs die Anleitung des Distributors, um sicherzustellen, dass die Rechner über die neuesten Zertifikate und CRLs verfügen.
1. Erlauben Sie die ausgehende Verbindung von virtuellen Computern:
   - Wenn Sie einen URL-basierten Firewall-Proxy zur Kontrolle der ausgehenden Konnektivität verwenden, erlauben Sie den Zugriff auf [diese URLs](../resource-mover/support-matrix-move-region-azure-vm.md#url-access).
   - Wenn Sie Regeln der Netzwerksicherheitsgruppe (NSG) verwenden, um die ausgehende Konnektivität zu kontrollieren, erstellen Sie [diese Service-Tag-Regeln](../resource-mover/support-matrix-move-region-azure-vm.md#nsg-rules).

### <a name="move-azure-vms"></a>Verschieben von virtuellen Azure-Computern

Verschieben Sie Ihren virtuellen Computer mithilfe von [Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md) in eine neue Region.

### <a name="protect-azure-vms-using-azure-backup"></a>Schützen Sie Azure-VMs mit Azure Backup

Beginnen Sie mit dem Schützen Ihres virtuellen Computers in einem neuen oder vorhandenen Recovery Services-Tresor in der neuen Region. Wenn Sie ihre älteren Sicherungen wiederherstellen müssen, können Sie dies weiterhin über Ihren alten Recovery Services-Tresor durchführen, sofern Sie sich für die Beibehaltung der Sicherungsdaten entschieden haben. 

Mit den oben genannten Schritten soll sichergestellt werden, dass Ihre Ressourcen auch in der neuen Region gesichert werden.

## <a name="back-up-azure-file-share-after-moving-across-regions"></a>Sichern der Azure-Dateifreigabe nach regionsübergreifendem Verschieben

Azure Backup bietet heute [eine Lösung zur Verwaltung von Momentaufnahmen](./backup-afs.md) für Azure Files. Dies bedeutet, dass Sie die Dateifreigabedaten nicht in die Recovery Services-Tresore verschieben. Da die Momentaufnahmen nicht mit Ihrem Speicherkonto verschoben werden, verfügen Sie nur über alle Sicherungen (Momentaufnahmen) tatsächlich nur in der vorhandenen Region, und sie werden durch den vorhandenen Tresor geschützt. Wenn Sie jedoch Ihre Speicherkonten zusammen mit den Dateifreigaben über Regionen hinweg verschieben oder neue Dateifreigaben in der neuen Region erstellen, beachten Sie die folgenden Abschnitte, um sicherzustellen, dass sie durch Azure Backup geschützt sind.

### <a name="prepare-to-move-azure-file-share"></a>Vorbereiten des Umzugs von Azure-Dateifreigabe

Bevor Sie das Speicherkonto verschieben, stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:

1.  Siehe die [Voraussetzungen zum Verschieben des Speicherkontos](../storage/common/storage-account-move.md?tabs=azure-portal#prerequisites). 
1. Exportieren und ändern Sie eine Vorlage für die Ressourcenverschiebung. Weitere Informationen finden Sie unter [Speicherkonto für Regionsumzug](../storage/common/storage-account-move.md?tabs=azure-portal#prepare) vorbereiten.

### <a name="move-azure-file-share"></a>Verschieben einer Azure-Dateifreigabe

Um Ihre Speicherkonten zusammen mit den darin enthaltenen Azure-Dateifreigaben von einer Region in eine andere zu verschieben, siehe [„Verschieben eines Azure-Speicherkontos in eine andere Region“](../storage/common/storage-account-move.md).

>[!Note]
>Wenn die Azure-Dateifreigabe regionsübergreifend kopiert wird, werden die zugehörigen Momentaufnahmen nicht mit ihr verschoben. Um die Momentaufnahmedaten in die neue Region zu verschieben, müssen Sie die einzelnen Dateien und Verzeichnisse der Momentaufnahmen mit [AzCopy](../storage/common/storage-use-azcopy-files.md#copy-all-file-shares-directories-and-files-to-another-storage-account) in das Speicherkonto in der neuen Region verschieben.

### <a name="protect-azure-file-share-using-azure-backup"></a>Schützen einer Azure-Dateifreigabe mit „Azure Backup“

Beginnen Sie mit dem Schützen der Azure-Dateifreigabe, die in das neue Speicherkonto in einem neuen oder vorhandenen Recovery Services-Tresor in der neuen Region kopiert wurde.  

Nachdem die Azure-Dateifreigabe in die neue Region kopiert wurde, können Sie den Schutz beenden und die Momentaufnahmen (und die entsprechenden Wiederherstellungspunkte) der ursprünglichen Azure-Dateifreigabe entsprechend Ihren Anforderungen beibehalten/löschen. Wählen Sie dazu Ihre Dateifreigabe auf der Registerkarte [Sicherungselemente](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) des Dashboards des ursprünglichen Tresors aus. Wenn die Sicherungsdaten für eine Azure-Dateifreigabe mit „Daten beibehalten“ beendet werden, bleiben die Wiederherstellungspunkte für immer erhalten und unterliegen keiner Richtlinie.
   
Dadurch wird sichergestellt, dass Ihre Momentaufnahmen immer für die Wiederherstellung aus dem älteren Tresor verfügbar sind. 
 
## <a name="back-up-sql-serversap-hana-in-azure-vm-after-moving-across-regions"></a>Sichern von SQL Server/SAP HANA in einer Azure-VM nach einer regionsübergreifenden Verschiebung

Wenn Sie einen virtuellen Computer mit SQL- oder SAP HANA-Servern in eine andere Region verschieben, können die SQL- und SAP HANA-Datenbanken auf diesen VMs nicht mehr im Tresor der früheren Region gesichert werden. Um die SQL- und SAP HANA-Server zu schützen, die in Azure VM in der neuen Region laufen, lesen Sie die folgenden Abschnitte.

### <a name="prepare-to-move-sql-serversap-hana-in-azure-vm"></a>Vorbereiten der Verlagerung von SQL Server/SAP HANA in Azure VM

Bevor Sie SQL Server/SAP HANA, das in einer VM ausgeführt wird, in eine neue Region verschieben, müssen die folgenden Voraussetzungen erfüllt sein:

1. Lesen Sie die [Voraussetzungen für die VM-Verschiebung](../resource-mover/tutorial-move-region-virtual-machines.md#prerequisites) und stellen Sie sicher, dass die VM für die Verschiebung geeignet ist. 
1. Wählen Sie die VM auf der [Registerkarte Backup Items](./backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) des Dashboards des vorhandenen Tresors aus und wählen Sie _die Datenbanken_, für die das Backup gestoppt werden muss. Wählen Sie **Schutz beenden** und dann entsprechend Ihren Anforderungen „Daten aufbewahren/löschen“ aus. Wenn die Sicherungsdaten mit „Daten beibehalten“ beendet werden, bleiben die Wiederherstellungspunkte für immer erhalten und unterliegen keiner Richtlinie. Dadurch wird sichergestellt, dass Ihre Sicherungsdaten immer für die Wiederherstellung verfügbar sind.
   >[!Note]
   >Für die Beibehaltung von Daten im älteren Tresor werden Sicherungsgebühren berechnet. Wenn Sie Daten nicht mehr aufbewahren möchten, um die Abrechnung zu vermeiden, müssen Sie die beibehaltenen Sicherungsdaten mithilfe der Option [Daten löschen](./backup-azure-manage-vms.md#delete-backup-data) löschen.
1. Stellen Sie sicher, dass die zu verschiebenden VMs eingeschaltet sind. Alle Festplatten der VMs, die in der Zielregion verfügbar sein müssen, werden angeschlossen und in den VMs initialisiert.
1. Stellen Sie sicher, dass die VMs über die neuesten vertrauenswürdigen Stammzertifikate und eine aktualisierte Zertifikatswiderrufsliste (CRL) verfügen. Dazu gehen Sie wie folgt vor:
   - Installieren Sie auf Windows-VMs die aktuellen Windows-Updates.
   - Beachten Sie bei Linux-VMs die Anleitung des Distributors und stellen Sie sicher, dass die Rechner über die neuesten Zertifikate und CRLs verfügen.
1. Erlauben Sie die ausgehende Verbindung von virtuellen Computern:
   - Wenn Sie einen URL-basierten Firewall-Proxy zur Kontrolle der ausgehenden Konnektivität verwenden, erlauben Sie den Zugriff auf [diese URLs](../resource-mover/support-matrix-move-region-azure-vm.md#url-access).
   - Wenn Sie Regeln der Netzwerksicherheitsgruppe (NSG) verwenden, um die ausgehende Konnektivität zu kontrollieren, erstellen Sie [diese Service-Tag-Regeln](../resource-mover/support-matrix-move-region-azure-vm.md#nsg-rules).

### <a name="move-sql-serversap-hana-in-azure-vm"></a>SQL Server/SAP HANA in Azure VM verschieben

Verschieben Sie Ihren virtuellen Computer mithilfe von [Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md) in eine neue Region.

### <a name="protect-sql-serversap-hana-in-azure-vm-using-azure-backup"></a>Schützen Sie SQL Server/SAP HANA in Azure VM mit Azure Backup

Beginnen Sie mit dem Schützen des virtuellen Computers in einem neuen/vorhandenen Recovery Services-Tresor in der neuen Region. Wenn Sie ihre älteren Sicherungen wiederherstellen müssen, ist dies über Ihren alten Recovery Services-Tresor weiterhin möglich.
 
Mit den oben genannten Schritten soll sichergestellt werden, dass Ihre Ressourcen auch in der neuen Region gesichert werden.