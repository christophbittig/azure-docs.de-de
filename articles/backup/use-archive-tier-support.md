---
title: Verwenden der Archivspeicherebene
description: Erfahren Sie mehr über das Verwenden der Unterstützung der Archivspeicherebene für Azure Backup
ms.topic: conceptual
ms.date: 10/23/2021
ms.custom: devx-track-azurepowershell-azurecli
zone_pivot_groups: backup-client-powershelltier-clitier-portaltier
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 9b35cd9269ef75f7c97d576c85004a4fe0be6a34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094862"
---
# <a name="use-archive-tier-support"></a>Verwenden der Unterstützung der Archivspeicherebene


::: zone pivot="client-powershelltier"

In diesem Artikel werden das Verfahren zur Sicherung von langfristigen Aufbewahrungspunkten auf der Archivspeicherebene und Momentaufnahmen sowie die Standardebene mithilfe von PowerShell beschrieben.

## <a name="supported-workloads"></a>Unterstützte Workloads

| Arbeitsauslastungen | Operations |
| --- | --- |
| Azure Virtual Machines (Vorschau)   <br><br>  SQL Server auf Azure Virtual Machines   | <ul><li>Anzeigen von archivierbaren Wiederherstellungspunkten    </li><li>Anzeigen empfohlener Wiederherstellungspunkte (nur für Virtual Machines)  </li><li>Verschieben von archivierbaren Wiederherstellungspunkten   </li><li>Verschieben empfohlener Wiederherstellungspunkte (nur für Azure Virtual Machines)   </li><li>Anzeigen archivierter Wiederherstellungspunkte   </li><li>Wiederherstellen von archivierten Wiederherstellungspunkten   </li></ul> |

## <a name="get-started"></a>Erste Schritte

1. Laden Sie die [neueste](https://github.com/PowerShell/PowerShell/releases) Version von PowerShell von GitHub herunter.

1. Führen Sie das folgende Cmdlet in PowerShell aus:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.4.0 -AllowPrerelease -force
    ```

1. Stellen Sie mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Azure her.

1. Melden Sie sich bei Ihrem Abonnement an:

   ```azurepowershell
   Set-AzContext -Subscription "SubscriptionName"
   ```

1. Tresor abrufen:

    ```azurepowershell
    $vault = Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"
    ```

1. Liste der Sicherungselemente abrufen:

   - **Für Azure Virtual Machines**

       ```azurepowershell
       $BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"
       ```

   - **Für SQL Server in Azure Virtual Machines**

       ```azurepowershell
       $BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"
       ```

1. Rufen Sie das Sicherungselement ab.

   - **Für Azure Virtual Machines**

     ```azurepowershell
     $bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}
     ```

   - **Für SQL Server in Azure Virtual Machines**

     ```azurepowershell
     $bckItm = $BackupItemList | Where-Object {$_.FriendlyName -eq '<dbName>' -and $_.ContainerName -match '<vmName>'}
     ```

1. (Optional) Fügen Sie den Datumsbereich hinzu, für den Sie die Wiederherstellungspunkte anzeigen möchten. Wenn Sie beispielsweise die Wiederherstellungspunkte der letzten 120 Tage anzeigen möchten, verwenden Sie das folgende Cmdlet:

   ```azurepowershell
    $startDate = (Get-Date).AddDays(-120)
    $endDate = (Get-Date).AddDays(0) 
   ```
   >[!NOTE]
   >Um Wiederherstellungspunkte für einen anderen Zeitraum anzuzeigen, ändern Sie Start- und Enddatum entsprechend. <br><br> Standardmäßig werden die letzten 30 Tage verwendet.

## <a name="check-the-archivable-status-of-all-recovery-points"></a>Überprüfen des Archivierungsstatus aller Wiederherstellungspunkte

Sie können nun den Archivierungsstatus aller Wiederherstellungspunkte eines Sicherungselements mit dem folgenden Cmdlet überprüfen:

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() 

$rp | select RecoveryPointId, @{ Label="IsArchivable";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].IsReadyForMove}}, @{ Label="ArchivableInfo";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].AdditionalInfo}}
```

## <a name="check-archivable-recovery-points"></a>Überprüfen von archivierbaren Wiederherstellungspunkten

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $true -TargetTier VaultArchive
```

Damit werden alle Wiederherstellungspunkte aufgelistet, die einem bestimmten Sicherungselement zugeordnet sind, das in das Archiv verschoben werden kann (vom Startdatum zum Enddatum). Sie können auch Startdatum und Enddatum ändern.

## <a name="check-why-a-recovery-point-cant-be-moved-to-archive"></a>Überprüfen, warum ein Wiederherstellungspunkt nicht in das Archiv verschoben werden kann

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $false -TargetTier VaultArchive
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Dabei entspricht `$rp[0]` dem Wiederherstellungspunkt, der nicht archiviert werden kann und den Sie überprüfen möchten.

**Beispielausgabe**

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

## <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Überprüfen des empfohlenen Satzes archivierbarer Punkte (nur für virtuelle Azure-Computer)

Die einem virtuellen Computer zugeordneten Wiederherstellungspunkte sind inkrementell. Wenn Sie einen bestimmten Wiederherstellungspunkt in das Archiv verschieben, wird er zuerst in eine vollständige Sicherung konvertiert und dann archiviert. Daher richten sich die mit dem Verschieben ins Archiv verbundenen Kosteneinsparungen nach der Änderungsrate der Datenquelle.

Daher bietet Azure Backup einen empfohlenen Satz von Wiederherstellungspunkten, die Kosten sparen können, wenn sie zusammen verschoben werden.

>[!NOTE]
>- Die Kosteneinsparungen hängen von verschiedenen Faktoren ab und können für die einzelnen Instanzen unterschiedlich sein.
>- Kosteneinsparungen sind nur möglich, wenn Sie alle Wiederherstellungspunkte, die im Empfehlungssatz enthalten sind, auf die Tresorarchivebene verschieben.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

## <a name="move-to-archive"></a>Verschieben in das Archiv

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[0] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Dabei ist `$rp[0]` der erste Wiederherstellungspunkt in der Liste. Wenn Sie andere Wiederherstellungspunkte verschieben möchten, verwenden Sie `$rp[1]`, `$rp[2]`, usw.

Mit diesem Cmdlet wird ein archivierbarer Wiederherstellungspunkt in das Archiv verschoben. Zurückgegeben wird ein Auftrag, der zum Nachverfolgen des Verschiebevorgangs, sowohl im Portal als auch mit PowerShell, verwendet werden kann.

## <a name="view-archived-recovery-points"></a>Anzeigen archivierter Wiederherstellungspunkte

Dieses Cmdlet gibt alle archivierten Wiederherstellungspunkte zurück.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-with-powershell"></a>Wiederherstellen mit PowerShell

Für Wiederherstellungspunkte im Archiv stellt Azure Backup eine integrierte Wiederherstellungsmethode bereit.
Bei der integrierten Wiederherstellung handelt es sich um einen zweistufigen Vorgang.

1. Umfasst die Aktivierung der Wiederherstellungspunkte, die im Archiv gespeichert sind.
2. Vorübergehende Speicherung auf der Tresorstandardebene für eine Dauer (auch Aktivierungsdauer genannt) von 10 bis 30 Tagen. Der Standardwert beträgt 15 Tage. Bei der Aktivierung gibt es zwei unterschiedliche Prioritäten: Standardpriorität und hohe Priorität. Weitere Informationen hierzu finden Sie unter [Aktivierungspriorität](../storage/blobs/archive-rehydrate-overview.md#rehydration-priority).

>[!NOTE]
>
>- Die einmal ausgewählte Aktivierungsdauer kann nicht geändert werden, und die aktivierten Wiederherstellungspunkte befinden sich während der Aktivierungsdauer auf der Standardebene.
>- Der zusätzliche Aktivierungsschritt verursacht Kosten.

Weitere Informationen zu den verschiedenen Wiederherstellungsmethoden für virtuelle Azure-Computer finden Sie unter [Wiederherstellen eines virtuellen Azure-Computers mit PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Zum Wiederherstellen von SQL Server führen Sie [diese Schritte](backup-azure-sql-automation.md#restore-sql-dbs) aus. Für das Cmdlet `Restore-AzRecoveryServicesBackupItem` sind zwei zusätzliche Parameter erforderlich: `RehydrationDuration` und `RehydrationPriority`.

## <a name="view-jobs"></a>Anzeigen von Aufträgen

Verwenden Sie das folgende PowerShell-Cmdlet, um die Verschiebungs- und Wiederherstellungsaufträge anzuzeigen:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="move-recovery-points-to-archive-tier-at-scale"></a>Verschieben großer Mengen von Wiederherstellungspunkten in die Archivebene

Sie können jetzt Beispielskripts verwenden, um Vorgänge in großem Maßstab auszuführen. [Erfahren Sie mehr](https://github.com/hiaga/Az.RecoveryServices/blob/master/README.md) über das Ausführen von Beispielskripts. Sie können die Skripts [hier](https://github.com/hiaga/Az.RecoveryServices) herunterladen.

Sie können die folgenden Vorgänge mithilfe der von Azure Backup bereitgestellten Beispielskripts ausführen:

- Verschieben aller in Frage kommenden Wiederherstellungspunkte für eine bestimmte Datenbank/alle Datenbanken eines SQL-Servers auf dem virtuellen Azure-Computer auf die Archivebene.
- Verschieben aller empfohlenen Wiederherstellungspunkte für einen bestimmten virtuellen Azure-Computer auf die Archivebene.
 
Sie können auch ein Skript gemäß Ihren Anforderungen schreiben oder die obigen Beispielskripts ändern, um die erforderlichen Sicherungselemente abzurufen.

::: zone-end



::: zone pivot="client-clitier"

In diesem Artikel werden das Verfahren zur Sicherung von langfristigen Aufbewahrungspunkten auf der Archivspeicherebene und Momentaufnahmen sowie die Standardebene mithilfe der Befehlszeilenschnittstelle (CLI) beschrieben.

## <a name="supported-workloads"></a>Unterstützte Workloads

| Arbeitsauslastungen | Operations |
| --- | --- |
| Azure Virtual Machines (Vorschau)   <br><br>  SQL Server auf Azure Virtual Machines   <br><br>   SAP HANA in Azure Virtual Machines | <ul><li> Anzeigen von archivierbaren Wiederherstellungspunkten       </li><li>Anzeigen empfohlener Wiederherstellungspunkte (nur für Virtual Machines)    </li><li>Verschieben von archivierbaren Wiederherstellungspunkten    </li><li>Verschieben empfohlener Wiederherstellungspunkte (nur für Azure Virtual Machines)    </li><li>Anzeigen archivierter Wiederherstellungspunkte    </li><li>Wiederherstellen von archivierten Wiederherstellungspunkten </li></ul> |


## <a name="get-started"></a>Erste Schritte

1. Download/Upgrade der AZ CLI-Version auf 2.26.0 oder höher 

   1. Befolgen Sie die [Anweisungen](/cli/azure/install-azure-cli) zum erstmaligen Installieren der CLI.
   1. Führen Sie „az --upgrade“ aus, um ein Upgrade einer bereits installierten Version durchzuführen.

2. Melden Sie sich mithilfe des folgenden Befehls an:

   ```azurecli
   az login
   ```

3. Festlegen des Abonnementkontexts:

   ```azurecli
   az account set –s <subscriptionId>
   ```
## <a name="view-archivable-recovery-points"></a>Anzeigen von archivierbaren Wiederherstellungspunkten

Sie können die archivierbaren Wiederherstellungspunkte mit den folgenden Befehlen auf die Tresorarchivebene verschieben. [Erfahren Sie mehr](archive-tier-support.md#supported-workloads) über die Berechtigungskriterien.

- **Für Azure Virtual Machines**

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureIaasVM} --workload-type {VM}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

- Für SQL Server in Azure Virtual Machines

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

- Für SAP HANA in Azure Virtual Machines

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

## <a name="check-why-a-recovery-point-isnt-archivable"></a>Überprüfen, warum ein Wiederherstellungspunkt nicht archiviert werden kann

Führen Sie den folgenden Befehl aus:

```azurecli
az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload / AzureIaasVM} --workload-type {MSSQL / SAPHANA / VM}  --query [].{Name:name,move_ready:properties.recoveryPointMoveReadinessInfo.ArchivedRP.isReadyForMove,additional_details: properties.recoveryPointMoveReadinessInfo.ArchivedRP.additionalInfo
```

Sie erhalten eine Liste aller Wiederherstellungspunkte, unabhängig davon, ob sie archivierbar sind, und den Grund, warum sie nicht archivierbar sind.

## <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Überprüfen des empfohlenen Satzes archivierbarer Punkte (nur für virtuelle Azure-Computer)

Führen Sie den folgenden Befehl aus:

```azurecli
az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type { AzureIaasVM} --workload-type {VM} --recommended-for-archive
```

[Erfahren Sie mehr](archive-tier-support.md#archive-recommendations-only-for-azure-virtual-machines) über den Empfehlungssatz.

>[!Note]
>- Kosteneinsparungen hängen von verschiedenen Faktoren ab und können für die einzelnen Instanzen unterschiedlich sein.
>- Sie können Kosteneinsparungen nur sicherstellen, wenn alle im Empfehlungssatz enthaltenen Wiederherstellungspunkte auf die Tresorarchivebene verschoben werden.

## <a name="move-to-archive"></a>Verschieben in das Archiv

Sie können die archivierbaren Wiederherstellungspunkte mit den folgenden Befehlen auf die Tresorarchivebene verschieben. Der name-Parameter im Befehl sollte den Namen eines archivierbaren Wiederherstellungspunkts enthalten.

- **Für Azure Virtual Machines**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type { AzureIaasVM} --workload-type {VM} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```
- **Für SQL Server in Azure Virtual Machines**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```
- **Für SAP HANA in Azure Virtual Machines**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```

## <a name="view-archived-recovery-points"></a>Anzeigen archivierter Wiederherstellungspunkte

Verwenden Sie die folgenden Befehle:

- **Für Azure Virtual Machines**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload } --workload-type {VM} -- tier {VaultArchive}
    ```
- **Für SQL Server in Azure Virtual Machines**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL} -- tier {VaultArchive}
    ```
- **Für SAP HANA in Azure Virtual Machines**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA} -- tier {VaultArchive}
    ```

## <a name="restore"></a>Restore 

Führen Sie die folgenden Befehle aus:

- **Für Azure Virtual Machines**

    ```azurecli
    az backup restore restore-disks -g {rg} -v {vault} -c {container} -i {item} --rp-name {rp} --storage-account {storage_account} --rehydration-priority {Standard / High} --rehydration-duration {rehyd_dur}
    ```

- **For SQL Server in virtuellen Azure-Computern/SAP HANA in virtuellen Azure-Computern**

    ```azurecli
    az backup recoveryconfig show --resource-group saphanaResourceGroup \
        --vault-name saphanaVault \
        --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
        --item-name saphanadatabase;hxe;hxe \
        --restore-mode AlternateWorkloadRestore \
        --rp-name 7660777527047692711 \
        --target-item-name restored_database \
        --target-server-name hxehost \
        --target-server-type HANAInstance \
        --workload-type SAPHANA \
        --output json


    az backup restore restore-azurewl -g {rg} -v {vault} --recovery-config {recov_config} --rehydration-priority {Standard / High} --rehydration-duration {rehyd_dur}
    ```

::: zone-end



::: zone pivot="client-portaltier"

In diesem Artikel werden das Verfahren zur Sicherung von langfristigen Aufbewahrungspunkten auf der Archivspeicherebene und Momentaufnahmen sowie die Standardebene über das Azure-Portal beschrieben.

## <a name="supported-workloads"></a>Unterstützte Workloads

| Arbeitsauslastungen | Operations |
| --- | --- |
| Virtueller Azure-Computer | <ul><li>Anzeigen archivierter Wiederherstellungspunkte    </li><li>Wiederherstellen von archivierten Wiederherstellungspunkten   </li><li>Anzeigen der Archivverschiebung und von Wiederherstellungsaufträgen </li></ul> |
| SQL Server in Azure Virtual Machines/ <br> SAP HANA in Azure Virtual Machines | <ul><li>Anzeigen archivierter Wiederherstellungspunkte    </li><li>Verschieben der gesamten archivierbaren Wiederherstellung in das Archiv   </li><li>Wiederherstellen von archivierten Wiederherstellungspunkten   </li><li>Anzeigen der Archivverschiebung und von Wiederherstellungsaufträgen</li></ul> |

## <a name="view-archived-recovery-points"></a>Anzeigen archivierter Wiederherstellungspunkte

Sie können jetzt alle Wiederherstellungspunkte anzeigen, die in das Archiv verschoben wurden.

:::image type="content" source="./media/use-archive-tier-support/view-recovery-points-list-inline.png" alt-text="Screenshot: Liste von Wiederherstellungspunkten" lightbox="./media/use-archive-tier-support/view-recovery-points-list-expanded.png":::


## <a name="move-archivable-recovery-points-for-a-particular-sqlsap-hana-database"></a>Verschieben archivierbarer Wiederherstellungspunkte für eine bestimmte SQL/SAP HANA-Datenbank

Sie können jetzt alle Wiederherstellungspunkte für eine bestimmte SQL/SAP HANA-Datenbank ein einem Durchgang verschieben.

Folgen Sie diesen Schritten:

1. Wählen Sie das Sicherungselement (Datenbank in SQL Server oder SAP HANA in Azure Virtual Machines) aus, dessen Wiederherstellungspunkte Sie auf die Tresorarchivebene verschieben möchten.

2. **Klicken Sie hier**, um Wiederherstellungspunkte anzuzeigen, die älter als sieben Tage sind.

   :::image type="content" source="./media/use-archive-tier-support/view-old-recovery-points-inline.png" alt-text="Screenshot: Prozess zum Anzeigen von Wiederherstellungspunkten, die älter als sieben Tage sind" lightbox="./media/use-archive-tier-support/view-old-recovery-points-expanded.png":::

3. Um alle in Frage kommenden archivierbaren Punkte anzuzeigen, die ins Archiv verschoben werden können, wählen Sie _Langfristige Aufbewahrungspunkte können ins Archiv verschoben werden. Um alle „geeigneten Wiederherstellungspunkte“ auf die Archivspeicherebene zu verschieben, klicken Sie hier_.

   :::image type="content" source="./media/use-archive-tier-support/view-all-eligible-archivable-points-for-move-inline.png" alt-text="Screenshot: Prozess zum Anzeigen aller geeigneten archivierbaren Punkte, die in das Archiv verschoben werden sollen" lightbox="./media/use-archive-tier-support/view-all-eligible-archivable-points-for-move-expanded.png":::

   Alle archivierbaren Wiederherstellungspunkte werden angezeigt.


   [Erfahren Sie mehr](archive-tier-support.md#supported-workloads) über Berechtigungskriterien.

3. Klicken Sie auf **Move Recovery Points to archive** (Wiederherstellungspunkte in Archiv verschieben), um alle Wiederherstellungspunkte auf die Tresorarchivebene zu verschieben.

   :::image type="content" source="./media/use-archive-tier-support/move-all-recovery-points-to-vault-inline.png" alt-text="Screenshot: Option zum Starten des Verschiebungsprozesses aller Wiederherstellungspunkte auf die Tresorarchivebene" lightbox="./media/use-archive-tier-support/move-all-recovery-points-to-vault-expanded.png":::

   >[!Note]
   >Mit dieser Option werden alle archivierbaren Wiederherstellungspunkte in das Tresorarchiv verschoben.

Sie können den Status unter den Sicherungsaufträgen überwachen.

## <a name="restore"></a>Restore

Zum Wiederherstellen der Wiederherstellungspunkte, die in das Archiv verschoben werden, müssen Sie die erforderlichen Parameter für die Aktivierungsdauer und Aktivierungspriorität hinzufügen.

:::image type="content" source="./media/use-archive-tier-support/restore-in-portal.png" alt-text="Screenshot: Prozess zum Wiederherstellen von Wiederherstellungspunkten im Portal":::

## <a name="view-jobs"></a>Anzeigen von Aufträgen

:::image type="content" source="./media/use-archive-tier-support/view-jobs-portal.png" alt-text="Screenshot: Prozess zum Anzeigen von Aufträgen im Portal":::

## <a name="view-archive-usage-in-vault-dashboard"></a>Anzeigen der Archivnutzung im Tresordashboard

Sie können die Archivnutzung auch im Tresordashboard anzeigen.

:::image type="content" source="./media/use-archive-tier-support/view-archive-usage-in-vault-dashboard.png" alt-text="Screenshot: Archivnutzung im Tresordashboard":::


::: zone-end

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung bei Fehlern auf der Archivspeicherebene](troubleshoot-archive-tier.md)
