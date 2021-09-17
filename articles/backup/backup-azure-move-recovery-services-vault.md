---
title: Verschieben von Recovery Services-Tresoren in Azure Backup
description: Hier finden Sie Anweisungen zum Verschieben eines Recovery Services-Tresors zwischen Azure-Abonnements und Ressourcengruppen.
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 3659bac2382225ca62421c9ef852b181382afd83
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101177"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Verschieben eines Recovery Services-Tresors zwischen Azure-Abonnements und Ressourcengruppen

In diesem Artikel wird das Verschieben eines für Azure Backup konfigurierten Recovery Services-Tresor zwischen Azure-Abonnements oder in eine andere Ressourcengruppe im selben Abonnement erläutert. Sie können Azure-Portal oder PowerShell zum Verschieben eines Recovery Services-Tresors verwenden.

## <a name="supported-regions"></a>Unterstützte Regionen

Alle öffentlichen und souveränen Regionen werden unterstützt, mit Ausnahme von „Frankreich, Mitte“, „Frankreich, Süden“, „Deutschland, Nordosten“, „Deutschland, Mitte“, „China, Norden“, „China, Norden2“, „China, Osten“ und „China, Osten2“.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Voraussetzungen für das Verschieben eines Recovery Services-Tresors

- Während des Verschiebens des Tresors zwischen den Ressourcengruppen sind sowohl die Quell- als auch die Zielgruppen gesperrt und verhindern das Schreiben und Löschen. Weitere Informationen finden Sie in [diesem Artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Nur das Administratorabonnement verfügt über die Berechtigungen, einen Tresor zu verschieben.
- Beim Verschieben von Tresoren zwischen Abonnements muss sich das Zielabonnement in einem aktivierten Status im selben Mandanten wie das Quellabonnement befinden. Informationen zum Verschieben eines Tresors in ein anderes Azure AD-Verzeichnis finden Sie unter [Übertragen von Abonnements in ein anderes Verzeichnis](../role-based-access-control/transfer-subscription.md) und in den [häufig gestellten Fragen zum Recovery Services-Tresor](/azure/backup/backup-azure-backup-faq#recovery-services-vault).
- Sie benötigen die Berechtigung zum Ausführen der Schreibvorgänge in der Zielressourcengruppe.
- Das Verschieben des Tresors ändert nur die Ressourcengruppe. Der Recovery Services-Tresor befindet sich an demselben Speicherort und kann nicht geändert werden.
- Sie können nur einen Recovery Services-Tresor pro Region verschieben.
- Wenn ein virtueller Computer nicht mit dem Recovery Services-Tresor zwischen Abonnements oder in eine neue Ressourcengruppe verschoben wird, bleiben die aktuellen VM-Wiederherstellungspunkte im Tresor intakt, bis sie ablaufen.
- Unabhängig davon, ob die VM mit dem Tresor verschoben wird oder nicht, können Sie die VM immer anhand des Sicherungsverlaufs im Tresor wiederherstellen.
- Azure Disk Encryption setzt voraus, dass der Schlüsseltresor und die VMs zur selben Azure-Region und zum selben Azure-Abonnement gehören.
- Informationen zum Verschieben eines virtuellen Computers mit verwalteten Datenträgern finden Sie in diesem [Artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Die Optionen zum Verschieben von Ressourcen, die über das klassische Modell bereitgestellt wurden, unterscheiden sich in Abhängigkeit davon, ob Sie die Ressourcen innerhalb eines Abonnements oder in ein neues Abonnement verschieben. Weitere Informationen finden Sie in [diesem Artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Für den Tresor definierte Sicherungsrichtlinien werden beibehalten, nachdem der Tresor zwischen Abonnements oder in eine neue Ressourcengruppe verschoben worden ist.
- Sie können nur Tresore verschieben, die eine der folgenden Typen von Sicherungselementen enthalten. Alle Sicherungselemente von Typen, die nicht unten aufgeführt sind, müssen beendet werden, und die Daten müssen vor dem Verschieben des Tresors dauerhaft gelöscht werden.
  - Azure Virtual Machines
  - Microsoft Azure Recovery Services-Agent (MARS)
  - Microsoft Azure Backup Server (MABS)
  - Data Protection Manager (DPM)
- Wenn Sie einen Tresor mit VM-Sicherungsdaten abonnementübergreifend verschieben, müssen Sie Ihre VMs in dasselbe Abonnement verschieben und denselben Namen (wie im alten Abonnement) für die VM-Zielressourcengruppe verwenden, um weiterhin Sicherungen durchführen zu können.

> [!NOTE]
> Das Verschieben von Recovery Services-Tresoren für Azure Backup zwischen Azure-Regionen wird nicht unterstützt.<br><br>
> Wenn Sie virtuelle (Azure IaaS, Hyper-V, VMware) oder physische Computer für die Notfallwiederherstellung mit **Azure Site Recovery** konfiguriert haben, wird der Verschiebevorgang blockiert. Falls Sie Tresore für Azure Site Recovery verschieben möchten, helfen Ihnen die Informationen zum manuellen Verschieben von Tresoren in [diesem Artikel](../site-recovery/move-vaults-across-regions.md) weiter.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Verwenden des Azure-Portals zum Verschieben eines Recovery Services-Tresors in eine andere Ressourcengruppe

So verschieben Sie einen Recovery Services-Tresor und seine zugeordneten Ressourcen in eine andere Ressourcengruppe:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie in der Liste der **Recovery Services-Tresore** den Tresor aus, den Sie verschieben möchten. Wenn das Tresordashboard geöffnet wird, wird es wie in der folgenden Abbildung dargestellt angezeigt.

   ![Recovery Services-Tresor öffnen](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Wählen Sie das Dropdownsymbol aus, falls die **Essentials**-Informationen für Ihren Tresor nicht angezeigt werden. Jetzt sollten die Essentials-Informationen für Ihren Tresor angezeigt werden.

   ![Registerkarte „Essentials“-Informationen](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Wählen Sie im Übersichtsmenü des Tresors **Ändern** neben **Ressourcengruppe** aus, um den Bereich **Ressourcen verschieben** zu öffnen.

   ![Ressourcengruppe ändern](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Im Bereich **Ressourcen verschieben** für den ausgewählten Tresor sollten die optionalen verwandten Ressourcen durch das Aktivieren des Kontrollkästchens verschoben werden. Dies ist in der folgenden Abbildung dargestellt.

   ![Abonnement verschieben](./media/backup-azure-move-recovery-services/move-resource.png)

5. Um die Zielressourcengruppe hinzuzufügen, wählen Sie in der Dropdownliste **Ressourcengruppe** eine vorhandene Ressource aus, oder wählen Sie die Option **Neue Gruppe erstellen** aus.

   ![Resource erstellen](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Bestätigen Sie nach dem Hinzufügen der Ressourcengruppe mit der Option **Mir ist bewusst, dass Tools und Skripts, die den verschobenen Ressourcen zugeordnet sind, erst funktionieren, wenn ich sie auf die Verwendung neuer Ressourcen-IDs und Namespacereferenzen aktualisiere**, und wählen Sie dann **OK** aus, um das Verschieben des Tresors abzuschließen.

   ![Bestätigungsmeldung](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Verwenden des Azure-Portals zum Verschieben eines Recovery Services-Tresors in ein anderes Abonnement

Sie können einen Recovery Services-Tresor und die zugehörigen Ressourcen in ein anderes Abonnement verschieben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie in der Liste der Recovery Services-Tresore den Tresor aus, den Sie verschieben möchten. Wenn das Tresordashboard geöffnet wird, wird es wie in der folgenden Abbildung dargestellt angezeigt.

    ![Recovery Services-Tresor öffnen](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Wählen Sie das Dropdownsymbol aus, falls die **Essentials**-Informationen für Ihren Tresor nicht angezeigt werden. Jetzt sollten die Essentials-Informationen für Ihren Tresor angezeigt werden.

    ![Registerkarte „Essentials“-Informationen](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Wählen Sie im Übersichtsmenü des Tresors **Ändern** neben **Abonnement** aus, um den Bereich **Ressourcen verschieben** zu öffnen.

   ![Abonnement ändern](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Wählen Sie die zu verschiebenden Ressourcen aus. Hier sollten Sie die Option **Alles markieren** verwenden, um alle aufgeführten optionalen Ressourcen auszuwählen.

   ![Ressource verschieben](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Wählen Sie das Zielabonnement, in das der Tresor verschoben werden soll, in der Dropdownliste **Abonnement** aus.
6. Um die Zielressourcengruppe hinzuzufügen, wählen Sie in der Dropdownliste **Ressourcengruppe** eine vorhandene Ressource aus, oder wählen Sie die Option **Neue Gruppe erstellen** aus.

   ![Abonnement hinzufügen](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Wählen Sie zur Bestätigung **Mir ist bewusst, dass Tools und Skripts, die den verschobenen Ressourcen zugeordnet sind, erst funktionieren, wenn ich sie auf die Verwendung neuer Ressourcen-IDs aktualisiere** aus, und wählen Sie dann **OK** aus.

> [!NOTE]
> Das Szenario „Abonnementübergreifende Sicherung“ (RS-Tresor und geschützte virtuelle Computer befinden sich in verschiedenen Abonnements) wird nicht unterstützt. Außerdem kann die Speicherredundanzoption von lokalem redundantem Speicher (LRS) zu globalem redundantem Speicher (GRS) und umgekehrt während des Verschiebevorgangs für den Tresor nicht geändert werden.

## <a name="use-azure-portal-to-back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>Nutzung von Azure-Portal zum Sichern von Ressourcen im Recovery Services-Tresor nach dem Verschieben zwischen Regionen

Azure Resource Mover unterstützt die Verschiebung von mehreren Ressourcen über Regionen hinweg. Wenn Sie Ihre Ressourcen aus einer Region in eine andere verschieben, können Sie sicherstellen, dass Ihre Ressourcen geschützt bleiben. Da Azure Backup den Schutz mehrerer Workloads unterstützt, müssen Sie möglicherweise einige Schritte ausführen, um weiterhin die gleiche Schutzebene in der neuen Region zu nutzen.

Die ausführlichen Schritte dazu finden Sie in den folgenden Abschnitten.

>[!Note]
>Azure Backup unterstützt derzeit nicht das Verschieben von Sicherungsdaten aus einem Recovery Services-Tresor in einen anderen. Um Ihre Ressource in der neuen Region zu schützen, muss die Ressource registriert und in einem neuen/vorhandenen Tresor in der neuen Region gespeichert werden. Wenn Sie Ihre Ressourcen aus einer Region in eine andere verschieben, können Sicherungsdaten in Ihren vorhandenen Recovery Services-Tresoren in der älteren Region entsprechend Ihren Anforderungen beibehalten/gelöscht werden. Wenn Sie Daten in den alten Tresoren beibehalten möchten, entstehen entsprechend Sicherungsgebühren.

### <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>Sichern eines virtuellen Azure-Computers nach regionsübergreifendem Verschieben

Wenn ein virtueller Azure-Computer (VM), der durch einen Recovery Services-Tresor geschützt wurde, von einer Region in eine andere verschoben wird, kann er nicht mehr in den älteren Tresor gesichert werden. Für die Sicherungen im alten Tresor treten die Fehler **BCMV2VMNotFound** oder [**ResourceNotFound**](/azure/backup/backup-azure-vms-troubleshoot#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found) auf.

Führen Sie die folgenden Schritte aus, um Ihren virtuellen Computer in der neuen Region zu schützen:

1. Bevor Sie die VM verschieben, [wählen Sie sie auf der Registerkarte **Sicherungselemente** im Dashboard des vorhandenen Tresors aus](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud), und wählen Sie **Schutz beenden**, gefolgt von „Daten beibehalten/löschen“ (je nach Anforderung). Wenn die Sicherungsdaten für einen virtuellen Computer mit „Daten beibehalten“ beendet werden, bleiben die Wiederherstellungspunkte für immer erhalten und unterliegen keiner Richtlinie. Dadurch wird sichergestellt, dass Ihre Sicherungsdaten immer für die Wiederherstellung verfügbar sind.

   >[!Note]
   >Für die Beibehaltung von Daten im älteren Tresor werden Sicherungsgebühren berechnet. Wenn Sie Daten nicht mehr aufbewahren möchten, um die Abrechnung zu vermeiden, müssen Sie die beibehaltenen Sicherungsdaten mithilfe der Option [Daten löschen](/azure/backup/backup-azure-manage-vms#delete-backup-data) löschen.

1. Verschieben Sie Ihren virtuellen Computer mithilfe von [Azure Resource Mover](/azure/resource-mover/tutorial-move-region-virtual-machines) in eine neue Region.

1. Beginnen Sie mit dem Schützen Ihres virtuellen Computers in einem neuen oder vorhandenen Recovery Services-Tresor in der neuen Region.
   Wenn Sie ihre älteren Sicherungen wiederherstellen müssen, können Sie dies weiterhin über Ihren alten Recovery Services-Tresor durchführen, sofern Sie sich für die Beibehaltung der Sicherungsdaten entschieden haben. 

Mit den oben genannten Schritten soll sichergestellt werden, dass Ihre Ressourcen auch in der neuen Region gesichert werden.

### <a name="back-up-azure-file-share-after-moving-across-regions"></a>Sichern der Azure-Dateifreigabe nach regionsübergreifendem Verschieben

Informationen zum Verschieben ihrer Speicherkonten zusammen mit den darin enthaltenen Dateifreigaben aus einer Region in eine andere finden Sie unter [Verschieben eines Azure Storage-Kontos in eine andere Region](/azure/storage/common/storage-account-move).

>[!Note]
>Wenn die Azure-Dateifreigabe regionsübergreifend kopiert wird, werden die zugehörigen Momentaufnahmen nicht mit ihr verschoben. Um die Momentaufnahmedaten in die neue Region zu verschieben, müssen Sie die einzelnen Dateien und Verzeichnisse der Momentaufnahmen mit [AzCopy](/azure/storage/common/storage-use-azcopy-files#copy-all-file-shares-directories-and-files-to-another-storage-account) in das Speicherkonto in der neuen Region verschieben.

Azure Backup bietet heute [eine Lösung zur Verwaltung von Momentaufnahmen](/azure/backup/backup-afs#discover-file-shares-and-configure-backup) für Azure Files. Dies bedeutet, dass Sie die Dateifreigabedaten nicht in die Recovery Services-Tresore verschieben. Da die Momentaufnahmen nicht mit Ihrem Speicherkonto verschoben werden, verfügen Sie nur über alle Sicherungen (Momentaufnahmen) tatsächlich nur in der vorhandenen Region, und sie werden durch den vorhandenen Tresor geschützt. Sie können jedoch sicherstellen, dass die neuen Dateifreigaben, die Sie in der neuen Region erstellen, mit folgenden Schritten durch Azure Backup geschützt werden:

1. Beginnen Sie mit dem Schützen der Azure-Dateifreigabe, die in das neue Speicherkonto in einem neuen oder vorhandenen Recovery Services-Tresor in der neuen Region kopiert wurde.  

1. Nachdem die Azure-Dateifreigabe in die neue Region kopiert wurde, können Sie den Schutz beenden und die Momentaufnahmen (und die entsprechenden Wiederherstellungspunkte) der ursprünglichen Azure-Dateifreigabe entsprechend Ihren Anforderungen beibehalten/löschen. Wählen Sie dazu Ihre Dateifreigabe auf der Registerkarte [Sicherungselemente](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) des Dashboards des ursprünglichen Tresors aus. Wenn die Sicherungsdaten für eine Azure-Dateifreigabe mit „Daten beibehalten“ beendet werden, bleiben die Wiederherstellungspunkte für immer erhalten und unterliegen keiner Richtlinie.
   
   Dadurch wird sichergestellt, dass Ihre Momentaufnahmen immer für die Wiederherstellung aus dem älteren Tresor verfügbar sind. 
 
### <a name="back-up-sql-server-in-azure-vmsap-hana-in-azure-vm"></a>Sichern von SQL Server in Azure VM/SAP HANA in Azure VM

Wenn Sie einen virtuellen Computer mit SQL- oder SAP HANA-Servern in eine andere Region verschieben, können die SQL- und SAP HANA-Datenbanken auf diesen VMs nicht mehr im Tresor der früheren Region gesichert werden. Führen Sie die folgenden Schritte aus, um die auf einem virtuellen Azure-Computer in der neuen Region ausgeführten SQL- und SAP HANA-Server zu schützen:
 
1. Bevor Sie einen virtuellen Computer, auf dem SQL Server/SAP HANA ausgeführt wird, in eine neue Region verschieben, wählen Sie ihn auf der Registerkarte [Sicherungselemente](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud) des Dashboards des vorhandenen Tresors aus, und wählen Sie dann _die Datenbanken_ aus, für die die Sicherung beendet werden muss. Wählen Sie **Schutz beenden** und dann entsprechend Ihren Anforderungen „Daten aufbewahren/löschen“ aus. Wenn die Sicherungsdaten mit „Daten beibehalten“ beendet werden, bleiben die Wiederherstellungspunkte für immer erhalten und unterliegen keiner Richtlinie. Dadurch wird sichergestellt, dass Ihre Sicherungsdaten immer für die Wiederherstellung verfügbar sind.

   >[!Note]
   >Für die Beibehaltung von Daten im älteren Tresor werden Sicherungsgebühren berechnet. Wenn Sie Daten nicht mehr aufbewahren möchten, um die Abrechnung zu vermeiden, müssen Sie die beibehaltenen Sicherungsdaten mithilfe der Option [Daten löschen](/azure/backup/backup-azure-manage-vms#delete-backup-data) löschen.

1. Verschieben Sie den virtuellen Computer, auf dem SQL Server/SAP HANA ausgeführt wird, mit [Azure Resource Mover](/azure/resource-mover/tutorial-move-region-virtual-machines) in die neue Region.

1. Beginnen Sie mit dem Schützen des virtuellen Computers in einem neuen/vorhandenen Recovery Services-Tresor in der neuen Region. Wenn Sie ihre älteren Sicherungen wiederherstellen müssen, ist dies über Ihren alten Recovery Services-Tresor weiterhin möglich.
 
Mit den oben genannten Schritten soll sichergestellt werden, dass Ihre Ressourcen auch in der neuen Region gesichert werden.

## <a name="use-powershell-to-move-recovery-services-vault"></a>Verwenden von PowerShell zum Verschieben eines Recovery Services-Tresors

Um einen Recovery Services-Tresor in eine andere Ressourcengruppe zu verschieben, verwenden Sie das `Move-AzureRMResource`-Cmdlet. `Move-AzureRMResource` benötigt den Ressourcennamen und den Typ der Ressource. Sie erhalten beides über das `Get-AzureRmRecoveryServicesVault`-Cmdlet.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Um Ressourcen in ein anderes Abonnement zu verschieben, schließen Sie den `-DestinationSubscriptionId`-Parameter ein.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Nach dem Ausführen der obigen Cmdlets werden Sie aufgefordert, zu bestätigen, dass Sie die angegebenen Ressourcen verschieben möchten. Geben Sie **J** zur Bestätigung ein. Nach einer erfolgreichen Validierung wird die Ressource verschoben.

## <a name="use-cli-to-move-recovery-services-vault"></a>Verwenden der CLI zum Verschieben eines Recovery Services-Tresors

Um einen Recovery Services-Tresor in eine andere Ressourcengruppe zu verschieben, verwenden Sie das folgende Cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Um Ressourcen in ein neues Abonnement zu verschieben, geben Sie den Parameter`--destination-subscription-id` an.

## <a name="post-migration"></a>Nach der Migration

1. Legen Sie die Zugriffssteuerungen für die Ressourcengruppen fest, bzw. überprüfen Sie sie.  
2. Die Backup-Funktion zur Berichterstellung und Überwachung muss nach Abschluss des Verschiebevorgangs für den Tresor neu konfiguriert werden. Die vorherige Konfiguration geht während des Verschiebevorgangs verloren.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Verschieben Sie einen virtuellen Azure-Computer in einen anderen Recovery Services-Tresor. 

Wenn Sie einen virtuellen Azure-Computer verschieben möchten, auf dem Azure Backup aktiviert ist, haben Sie zwei Möglichkeiten. Diese hängen von Ihren Geschäftsanforderungen ab:

- [Zuvor gesicherte Daten müssen nicht aufbewahrt werden](#dont-need-to-preserve-previous-backed-up-data)
- [Zuvor gesicherte Daten müssen aufbewahrt werden](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Zuvor gesicherte Daten müssen nicht aufbewahrt werden

Zum Schutz von Workloads in einem neuen Tresor müssen der aktuelle Schutz und die Daten im alten Tresor gelöscht und die Sicherungen erneut konfiguriert werden.

>[!WARNING]
>Der folgende Vorgang ist destruktiv und kann nicht rückgängig gemacht werden. Alle Sicherungsdaten und Sicherungselemente, die dem geschützten Server zugeordnet sind, werden dauerhaft gelöscht. Gehen Sie daher mit Bedacht vor.

**Beenden und Löschen des aktuellen Schutzes für den alten Tresor:**

1. Deaktivieren Sie in den Eigenschaften des Tresors vorläufiges Löschen. Führen Sie [die folgenden Schritte](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) aus, um das vorläufige Löschen zu deaktivieren.

2. Beenden Sie den Schutz, und löschen Sie die Sicherungen aus dem aktuellen Tresor. Wählen Sie im Dashbordmenü des Tresors die Option **Sicherungselemente** aus. Die hier aufgelisteten Elemente, die in den neuen Tresor verschoben werden müssen, müssen zusammen mit den zugehörigen Sicherungsdaten entfernt werden. Weitere Informationen finden Sie unter [Löschen von geschützten Elementen in der Cloud](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) und [Löschen von geschützten lokalen Elementen](backup-azure-delete-vault.md#delete-protected-items-on-premises).

3. Wenn Sie planen, Azure-Dateifreigaben (AFS), SQL-Server oder SAP HANA-Server zu verschieben, müssen Sie auch deren Registrierung aufheben. Wählen Sie im Dashbordmenü des Tresors die Option **Sicherungsinfrastruktur** aus. Weitere Informationen finden Sie unter [Aufheben der Registrierung von SQL-Servern](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [Aufheben der Registrierung von Speicherkonten, die mit Azure-Dateifreigaben verknüpft sind](manage-afs-backup.md#unregister-a-storage-account) und [Aufheben der Registrierung einer SAP HANA-Instanz](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

4. Nachdem Sie diese aus dem alten Tresor entfernt haben, fahren Sie mit dem Konfigurieren der Sicherungen für Ihre Workload im neuen Tresor fort.

### <a name="must-preserve-previous-backed-up-data"></a>Zuvor gesicherte Daten müssen aufbewahrt werden

Wenn Sie die aktuell geschützten Daten im alten Tresor aufbewahren und den Schutz in einem neuen Tresor fortsetzen müssen, gibt es für einige der Workloads nur eingeschränkte Optionen:

- Bei MARS können Sie [Beendigung des Schutzes mit Beibehaltung der Daten](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) auswählen und den Agent im neuen Tresor registrieren.

  - Der Azure Backup-Dienst behält weiterhin alle vorhandenen Wiederherstellungspunkte des alten Tresors bei.
  - Die Beihaltung der Wiederherstellungspunkte im alten Tresor ist kostenpflichtig.
  - Für die Wiederherstellung gesicherter Daten können Sie nur nicht abgelaufene Wiederherstellungspunkte im alten Tresor auswählen.
  - Im neuen Tresor muss ein neues Erstreplikat der Daten erstellt werden.

- Bei einem virtuellen Azure-Computer können Sie [Beendigung des Schutzes mit Beibehaltung der Daten](backup-azure-manage-vms.md#stop-protecting-a-vm) für den virtuellen Computer im alten Tresor auswählen, den virtuellen Computer in eine andere Ressourcengruppe verschieben und dann den virtuellen Computer im neuen Tresor schützen. Weitere Informationen zum Verschieben eines virtuellen Computers in eine andere Ressourcengruppe finden Sie unter [Anleitungen und Einschränkungen](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md).

  Ein virtueller Computer kann jeweils nur in einem Tresor geschützt werden. Der virtuelle Computer in der neuen Ressourcengruppe kann aber im neuen Tresor geschützt werden, da er als anderer virtueller Computer betrachtet wird.

  - Der Azure Backup-Dienst behält die Wiederherstellungspunkte bei, die im alten Tresor gesichert wurden.
  - Die Beibehaltung der Wiederherstellungspunkte im alten Tresor ist kostenpflichtig (Einzelheiten finden Sie unter [Azure Backup – Preise](azure-backup-pricing.md)).
  - Sie können den virtuellen Computer bei Bedarf aus dem alten Tresor wiederherstellen.
  - Die erste Sicherung des virtuellen Computers in der neuen Ressourcengruppe im neuen Tresor erfolgt als Erstreplikat.

## <a name="next-steps"></a>Nächste Schritte

Sie können viele verschiedene Arten von Ressourcen zwischen Ressourcengruppen und Abonnements verschieben.

Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).