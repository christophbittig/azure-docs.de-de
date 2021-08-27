---
title: Unterstützung für Archivspeicherebene
description: Informationen zur Unterstützung der Zugriffsebene „Archiv“ für Azure Backup
ms.topic: conceptual
ms.date: 08/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ccb85c42685f962da3c9faf098d7847a93f4de74
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122353675"
---
# <a name="archive-tier-support"></a>Unterstützung für Archivspeicherebene

Kunden setzen zum Speichern von Sicherungsdaten, darunter auch Sicherungsdaten für die langfristige Aufbewahrung, auf Azure Backup. Die Anforderungen an die Aufbewahrungsdauer sind dabei in den Konformitätsregeln des jeweiligen Unternehmens definiert. In den meisten Fällen erfolgt nur selten ein Zugriff auf die älteren Sicherungsdaten, die ausschließlich zu Konformitätszwecken gespeichert werden.

Azure Backup unterstützt neben Momentaufnahmen und der Standardzugriffsebene auch die Sicherung von Wiederherstellungspunkten für die langfristige Aufbewahrung auf der Zugriffsebene „Archiv“.

## <a name="scope"></a>`Scope`

Unterstützte Workloads:

- Virtuelle Azure-Computer
  - Nur monatliche und jährliche Wiederherstellungspunkte. Tägliche und wöchentliche Wiederherstellungspunkte werden nicht unterstützt.
  - Alter: mindestens drei (3) Monate auf der Tresorstandardebene
  - Restliche Aufbewahrungsdauer: mindestens sechs (6) Monate
  - Keine aktiven täglichen und wöchentlichen Abhängigkeiten
- SQL Server auf virtuellen Azure-Computern
  - Nur vollständige Wiederherstellungspunkte. Protokolle und differenzielle Sicherungen werden nicht unterstützt.
  - Alter: mindestens 45 Tage auf der Tresorstandardebene
  - Restliche Aufbewahrungsdauer: mindestens sechs (6) Monate
  - Keine Abhängigkeiten

Unterstützte Clients:

- Die Funktion wird mithilfe von PowerShell bereitgestellt.

>[!Note]
>Die Unterstützung der Zugriffsebene „Archiv“ für SQL Server auf Azure-VMs ist jetzt allgemein verfügbar in „Europa, Norden“, „Indien, Mitte“, „Asien, Südosten“ und „Australien, Osten“. Eine ausführliche Liste der unterstützten Regionen finden Sie in der [Supportmatrix](#support-matrix).    <br><br>    Für die verbleibenden Regionen für SQL Server in Azure-VMs befindet sich die Unterstützung der Zugriffsebene „Archiv“ in der eingeschränkten öffentlichen Vorschau. Die Unterstützung der Zugriffsebene „Archiv“ für Azure Virtual Machines befindet sich ebenfalls in der eingeschränkten öffentlichen Vorschau. Verwenden Sie diesen [Link](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR463S33c54tEiJLEM6Enqb9UNU5CVTlLVFlGUkNXWVlMNlRPM1lJWUxLRy4u), um sich für die eingeschränkte öffentliche Vorschauversion zu registrieren.

## <a name="get-started-with-powershell"></a>Erste Schritte mit PowerShell

1. Laden Sie die [neueste](https://github.com/PowerShell/PowerShell/releases) Version von PowerShell von GitHub herunter.

1. Führen Sie in PowerShell den folgenden Befehl aus:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.4.0 -AllowPrerelease -force
    ```

1. Stellen Sie mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Azure her.
1. Melden Sie sich bei Ihrem Abonnement an:

   `Set-AzContext -Subscription "SubscriptionName"`

1. Tresor abrufen:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. Liste der Sicherungselemente abrufen:

    - Gehen Sie bei virtuellen Azure-Computern wie folgt vor:

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"`

    - Gehen Sie bei SQL Server auf virtuellen Azure-Computern wie folgt vor:

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"`

1. Rufen Sie das Sicherungselement ab.

    - Gehen Sie bei virtuellen Azure-Computern wie folgt vor:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Gehen Sie bei SQL Server auf virtuellen Azure-Computern wie folgt vor:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

1. Fügen Sie den Datumsbereich hinzu, für den Sie die Wiederherstellungspunkte anzeigen möchten. Wenn Sie beispielsweise die Wiederherstellungspunkte der letzten 124 Tage bis zu den letzten 95 Tagen anzeigen möchten, verwenden Sie den folgenden Befehl:

   ```azurepowershell
    $startDate = (Get-Date).AddDays(-124)
    $endDate = (Get-Date).AddDays(-95) 

    ```
    >[!NOTE]
    >Um Wiederherstellungspunkte für einen anderen Zeitraum anzuzeigen, ändern Sie Start- und Enddatum entsprechend.
## <a name="use-powershell"></a>Verwenden von PowerShell

### <a name="check-archivable-recovery-points"></a>Überprüfen von archivierbaren Wiederherstellungspunkten

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $true -TargetTier VaultArchive
```

Damit werden alle Wiederherstellungspunkte aufgelistet, die einem bestimmten Sicherungselement zugeordnet sind, das in das Archiv verschoben werden kann (vom Startdatum zum Enddatum). Sie können auch Startdatum und Enddatum ändern.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Überprüfen, warum ein Wiederherstellungspunkt nicht in das Archiv verschoben werden kann

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $false -TargetTier VaultArchive
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Dabei entspricht `$rp[0]` dem Wiederherstellungspunkt, der nicht archiviert werden kann und den Sie überprüfen möchten.

Beispielausgabe:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Überprüfen des empfohlenen Satzes archivierbarer Punkte (nur für virtuelle Azure-Computer)

Die einem virtuellen Computer zugeordneten Wiederherstellungspunkte sind naturgemäß inkrementell. Wenn ein bestimmter Wiederherstellungspunkt in das Archiv verschoben wird, wird er zuerst in eine vollständige Sicherung konvertiert und dann archiviert. Daher richten sich die mit dem Verschieben ins Archiv verbundenen Kosteneinsparungen nach der Änderungsrate der Datenquelle.

Aus diesem Grund stellt Azure Backup einen empfohlenen Satz von Wiederherstellungspunkten bereit, die bei einer gemeinsamen Verschiebung zu Kosteneinsparungen führen können.

>[!NOTE]
>Die Kosteneinsparungen richten sich nach einer Vielzahl von Bedingungen und sind häufig nicht einmal bei zwei Instanzen identisch.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Verschieben in das Archiv

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[0] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Dabei ist `$rp[0]` der erste Wiederherstellungspunkt in der Liste. Wenn Sie andere Wiederherstellungspunkte verschieben möchten, verwenden Sie `$rp[1]`, `$rp[2]`, usw.

Mit diesem Befehl wird ein archivierbarer Wiederherstellungspunkt in das Archiv verschoben. Zurückgegeben wird ein Auftrag, der zum Nachverfolgen des Verschiebevorgangs, sowohl im Portal als auch mit PowerShell, verwendet werden kann.

### <a name="view-archived-recovery-points"></a>Anzeigen archivierter Wiederherstellungspunkte

Mit diesem Befehl werden alle archivierten Wiederherstellungspunkte zurückgegeben.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

### <a name="restore-with-powershell"></a>Wiederherstellen mit PowerShell

Für Wiederherstellungspunkte im Archiv stellt Azure Backup eine integrierte Wiederherstellungsmethode bereit.

Bei der integrierten Wiederherstellung handelt es sich um einen zweistufigen Vorgang. Zuerst werden die im Archiv gespeicherten Wiederherstellungspunkte aktiviert und temporär für einen Zeitraum von 10 bis 30 Tagen (auch als Aktivierungsdauer bezeichnet) auf der Tresorstandardebene gespeichert. Der Standardwert beträgt 15 Tage. Bei der Aktivierung gibt es zwei unterschiedliche Prioritäten: Standardpriorität und hohe Priorität. Weitere Informationen hierzu finden Sie unter [Aktivierungspriorität](../storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier).

>[!NOTE]
>
>- Die einmal ausgewählte Aktivierungsdauer kann nicht geändert werden, und die aktivierten Wiederherstellungspunkte befinden sich während der Aktivierungsdauer auf der Standardzugriffsebene.
>- Der zusätzliche Aktivierungsschritt verursacht Kosten.

Weitere Informationen zu den verschiedenen Wiederherstellungsmethoden für virtuelle Azure-Computer finden Sie unter [Wiederherstellen eines virtuellen Azure-Computers mit PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Zum Wiederherstellen von SQL Server führen Sie [diese Schritte](backup-azure-sql-automation.md#restore-sql-dbs) aus. Der Befehl `Restore-AzRecoveryServicesBackupItem` erfordert zwei zusätzliche Parameter: **RehydrationDuration** und **RehydrationPriority**.

### <a name="view-jobs-from-powershell"></a>Anzeigen von Aufträgen in PowerShell

Verwenden Sie das folgende PowerShell-Cmdlet, um die Verschiebungs- und Wiederherstellungsaufträge anzuzeigen:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>Verwenden des Portals

### <a name="check-archived-recovery-point"></a>Überprüfen archivierter Wiederherstellungspunkte

Sie können jetzt alle Wiederherstellungspunkte anzeigen, die in das Archiv verschoben wurden.

![Alle Wiederherstellungspunkte.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Wiederherstellen im Portal

Bei Wiederherstellungspunkten, die ins Archiv verschoben wurden, müssen Sie bei der Wiederherstellung die Parameter für die Aktivierungsdauer und die Aktivierungspriorität hinzufügen.

![Wiederherstellen im Azure-Portal.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Anzeigen von Aufträgen im Portal

![Anzeigen von Aufträgen im Portal.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Ändern des Schutzes

Es gibt zwei Möglichkeiten, den Schutz für eine Datenquelle zu ändern:

- Bearbeiten einer vorhandenen Richtlinie
- Schützen der Datenquelle durch eine neue Richtlinie

In beiden Fällen wird die neue Richtlinie auf alle älteren Wiederherstellungspunkte, die sich auf der Standard- und der Archivebene befinden, angewendet. Möglicherweise werden durch Änderung der Richtlinie ältere Wiederherstellungspunkte gelöscht.

Für Wiederherstellungspunkte, die ins Archiv verschoben werden, unterliegen für einen Zeitraum von 180 Tagen einer Gebühr für vorzeitiges Löschen. Die Gebühren werden anteilmäßig abgerechnet. Beim Löschen eines Wiederherstellungspunkts, der sich keine 180 Tage im Archiv befunden hat, werden Kosten für 180 Tage abzüglich der Anzahl der Tage auf der Standardebene in Rechnung gestellt.

Für Wiederherstellungspunkte, die sich nicht mindestens sechs Monate im Archiv befunden haben, wird beim Löschen eine Gebühr für vorzeitiges Löschen fällig.

## <a name="stop-protection-and-delete-data"></a>Beenden des Schutzes und Löschen der Daten

Durch das Beenden des Schutzes und das Löschen der Daten werden alle Wiederherstellungspunkte gelöscht. Bei Wiederherstellungspunkten im Archiv, die sich keine 180 Tage auf der Archivebene befunden haben, verursacht ein Löschvorgang Kosten für vorzeitiges Löschen.

## <a name="support-matrix"></a>Unterstützungsmatrix

| Arbeitsauslastungen | Vorschau | Allgemein verfügbar |
| --- | --- | --- |
| SQL Server auf Azure-VM | „USA, Osten“, „USA, Osten 2“, „USA, Mitte“, „USA, Süden-Mitte“, „USA, Westen“, „USA, Westen 2“, „USA, Westen-Mitte“, „USA, Norden-Mitte“, „Brasilien, Süden“, „Kanada, Osten“, „Kanada, Mitte“, „Europa, Westen“, „Vereinigtes Königreich, Süden“, „Vereinigtes Königreich, Westen“, „Asien, Osten“, „Japan, Osten“, „Indien, Süden“ | „Australien, Osten“, „Indien, Mitte“, „Europa, Norden“, „Asien, Südosten“ |
| Azure-VMs | „USA, Osten“, „USA, Osten 2“, „USA, Mitte“, „USA, Süden-Mitte“, „USA, Westen“, „USA, Westen 2“, „USA, Westen-Mitte“, „USA, Norden-Mitte“, „Brasilien, Süden“, „Kanada, Osten“, „Kanada, Mitte“, „Europa, Westen“, „Vereinigtes Königreich, Süden“, „Vereinigtes Königreich, Westen“, „Asien, Osten“, „Japan, Osten“, „Indien, Süden“, „Asien, Südosten“, „Australien, Osten“, „Indien, Mitte“, „Europa, Norden“ | Keine |

## <a name="error-codes-and-troubleshooting-steps"></a>Fehlercodes und Schritte zur Problembehandlung

Es gibt mehrere Fehlercodes, die auftreten, wenn ein Wiederherstellungspunkt nicht in das Archiv verschoben werden kann.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Fehlermeldung**: Der Typ des Wiederherstellungspunkts ist nicht für eine Verschiebung in das Archiv berechtigt.

**Beschreibung**: Dieser Fehlercode wird angezeigt, wenn der ausgewählte Typ des Wiederherstellungspunkts nicht berechtigt ist, in das Archiv verschoben zu werden.

**Empfohlene Aktion**: Überprüfen Sie [hier](#scope) die Berechtigung des Wiederherstellungspunkts.

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Fehlermeldung**: Der Wiederherstellungspunkt enthält aktive Abhängigkeiten für die Wiederherstellung und ist nicht berechtigt, in das Archiv verschoben zu werden.

**Beschreibung**: Der ausgewählte Wiederherstellungspunkt enthält aktive Abhängigkeiten und kann daher nicht in das Archiv verschoben werden.

**Empfohlene Aktion**: Überprüfen Sie [hier](#scope) die Berechtigung des Wiederherstellungspunkts.

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Fehlermeldung**: Der Wiederherstellungspunkt ist nicht berechtigt, in das Archiv verschoben zu werden, weil die bisherige Lebensdauer auf der Tresorstandardebene nicht der erforderlichen Mindestdauer entspricht.

**Beschreibung**: Der Wiederherstellungspunkt muss bei virtuellen Azure-Computern mindestens drei Monate und bei SQL Server mindestens 45 Tage auf der Standardzugriffsebene gespeichert gewesen sein.

**Empfohlene Aktion**: Überprüfen Sie [hier](#scope) die Berechtigung des Wiederherstellungspunkts.

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Fehlermeldung**: Die restliche Lebensdauer des Wiederherstellungspunkts liegt unter dem erforderlichen Mindestwert.

**Beschreibung**:Die erforderliche Mindestlebensdauer für einen Wiederherstellungspunkt, die zum Verschieben in das Archiv berechtigt, beträgt sechs Monate.

**Empfohlene Aktion**: Überprüfen Sie [hier](#scope) die Berechtigung des Wiederherstellungspunkts.

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Fehlermeldung**: Der Wiederherstellungspunkt ist nicht berechtigt, in das Archiv verschoben zu werden, weil er bereits auf die Archivebene verschoben wurde.

**Beschreibung**: Der ausgewählte Wiederherstellungspunkt befindet sich bereits im Archiv. Daher ist er nicht berechtigt, in das Archiv verschoben zu werden.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Fehlermeldung**: Der Datenquellentyp ist für die Empfehlungs-API nicht berechtigt.

**Beschreibung**: Die Empfehlungs-API ist nur für virtuelle Azure-Computer geeignet. Dies trifft nicht auf den ausgewählten Datenquellentyp zu.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Fehlermeldung**: Der Wiederherstellungspunkt wurde bereits aktiviert. Die Aktivierung ist für diesen Wiederherstellungspunkt nicht zulässig.

**Beschreibung**: Der ausgewählte Wiederherstellungspunkt wurde bereits aktiviert.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Fehlermeldung**: Der Wiederherstellungspunkt ist nicht berechtigt, in das Archiv verschoben zu werden.

**Beschreibung**:Der ausgewählte Wiederherstellungspunkt ist nicht berechtigt, in das Archiv verschoben zu werden.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**Fehler** **meldung**: Der Wiederherstellungspunkt im Archiv ist nicht aktiviert. Wiederholen Sie die Wiederherstellung, nachdem die Aktivierung des Wiederherstellungspunkts im Archiv abgeschlossen ist.

**Beschreibung**: Der Wiederherstellungspunkt ist nicht aktiviert. Versuchen Sie, die Wiederherstellung nach dem Aktivieren des Wiederherstellungspunkts auszuführen.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Fehler** **meldung**: Die Aktivierung wird nur für Wiederherstellungspunkte im Archiv unterstützt.

**Beschreibung**: Für den ausgewählten Wiederherstellungspunkt ist keine Aktivierung zulässig.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Fehlermeldung**: Die Aktivierung des Wiederherstellungspunkts im Archiv wird bereits durchgeführt.

**Beschreibung**: Die Aktivierung des ausgewählten Wiederherstellungspunkts wird bereits durchgeführt.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Fehlermeldung**: Der Wiederherstellungspunkt kann nicht auf die Archivebene verschoben werden, da die von der Richtlinie vorgegebene Aufbewahrungsdauer nicht ausreicht.

**Empfohlene Aktion**: Aktualisieren Sie die Richtlinie für das geschützte Element mit der entsprechenden Aufbewahrungseinstellung, und wiederholen Sie dann den Vorgang.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Fehlermeldung**: Es wird noch ermittelt, ob dieser Wiederherstellungspunkt verschoben werden kann.

**Beschreibung**: Es muss noch ermittelt werden, ob der Wiederherstellungspunkt verschoben werden kann.

**Empfohlene Aktion**: Warten Sie einen Moment, und wiederholen Sie dann den Vorgang.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Was passiert mit den Wiederherstellungspunkten im Archiv, wenn ich den Schutz beende und die Daten behalte?

Der Wiederherstellungspunkt bleibt dauerhaft im Archiv gespeichert. Weitere Informationen finden Sie unter [Auswirkungen auf Wiederherstellungspunkte beim Beenden des Schutzes](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

### <a name="is-cross-region-restore-supported-from-archive-tier"></a>Wird die regionsübergreifende Wiederherstellung von der Zugriffsebene „Archiv“ unterstützt?

Wenn Sie Ihre Daten in GRS-Tresoren von der Zugriffsebene „Standard“ in die Zugriffsebene „Archiv“ verschieben, werden die Daten in das GRS-Archiv verschoben. Dies gilt auch, wenn die regionsübergreifende Wiederherstellung aktiviert ist. Sobald Sicherungsdaten in die Zugriffsebene „Archiv“ verschoben wurden, können Sie die Daten nicht mehr in der gekoppelten Region wiederherstellen. Fällt eine Region aus, stehen die Sicherungsdaten in der sekundären Region jedoch für die Wiederherstellung zur Verfügung. 

Beim Wiederherstellen eines Wiederherstellungspunkts in der Zugriffsebene „Archiv“ in der primären Region wird der Wiederherstellungspunkt in die Zugriffsebene „Standard“ kopiert und sowohl in der primären als auch in der sekundären Region gemäß der Dauer der Aktivierung aufbewahrt. Sie können die regionsübergreifende Wiederherstellung aus diesen aktivierten Wiederherstellungspunkten ausführen.

## <a name="next-steps"></a>Nächste Schritte

- [Preise für Azure Backup](azure-backup-pricing.md)
