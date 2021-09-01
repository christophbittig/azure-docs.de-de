---
title: Migrieren von Azure-Verwaltungstools von Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Verwaltungstools von Azure Deutschland zu Azure weltweit.
ms.topic: article
ms.date: 06/22/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurecli
ms.openlocfilehash: 157dded56e3323ed6389f84bd173ba435e8bdc5b
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2021
ms.locfileid: "112461757"
---
# <a name="migrate-management-tool-resources-to-global-azure"></a>Migrieren von Verwaltungstoolressourcen zu Azure weltweit

[!INCLUDE [closure info](../../includes/germany-closure-info.md)]

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Verwaltungstools von Azure Deutschland zu Azure weltweit migrieren können.

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager kann Sie beim Ausführen einer reibungslosen Migration unterstützen. E ist aber nicht möglich, Traffic Manager-Profile, die Sie in Azure Deutschland erstellt haben, nach Azure weltweit zu migrieren. (Während einer Migration migrieren Sie Traffic Manager-Endpunkte zur Zielumgebung, so dass Sie das Traffic Manager-Profil ohnehin aktualisieren müssen.)

Sie können zusätzliche Endpunkte in der Zielumgebung definieren, indem Sie den Traffic Manager verwenden, während er noch in der Quellumgebung ausgeführt wird. Wenn Traffic Manager in der neuen Umgebung ausgeführt wird, können Sie in der Quellumgebung weiterhin Endpunkte definieren, die Sie noch nicht migriert haben. Dieses Szenario wird als das [Blau-Grün-Szenario](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/) bezeichnet. Dieses Szenario umfasst die folgenden Schritte:

1. Erstellen Sie ein neues Traffic Manager-Profil in Azure weltweit.
1. Definieren Sie die Endpunkte in Azure Deutschland.
1. Ändern Sie Ihren DNS CNAME-Eintrag in das neue Traffic Manager-Profil.
1. Deaktivieren Sie das alte Traffic Manager-Profil.
1. Migrieren und konfigurieren Sie Endpunkte. Für jeden Endpunkt in Azure Deutschland:
   1. Migrieren Sie den Endpunkt zu Azure weltweit.
   1. Ändern Sie das Traffic Manager-Profil, um den neuen Endpunkt zu verwenden.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Traffic Manager-Tutorials](../traffic-manager/index.yml) durcharbeiten.
- Lesen Sie [Was ist Traffic Manager?](../traffic-manager/traffic-manager-overview.md)
- Informieren Sie sich über das [Erstellen eines Traffic Manager-Profils](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Erfahren Sie mehr über das [Blau-Grün-Szenario](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/).

## <a name="azure-backup"></a>Azure Backup

Der Azure Backup-Dienst bietet einfache, sichere und kostengünstige Lösungen, um Ihre Daten zu sichern und aus der Microsoft Azure-Cloud wiederherzustellen. Das Verschieben von Sicherungsdaten ist jetzt über PowerShell-Cmdlets von „Deutschland, Mitte“ (GEC) und „Deutschland, Nordosten“ (GNE) nach „Deutschland, Westen-Mitte“ (GWC) aktiviert.

### <a name="prerequisite-for-moving-hybrid-workloads"></a>Voraussetzungen für das Verschieben von Hybridworkloads

Sobald der Verschiebevorgang gestartet wird, werden Sicherungen in den vorhandenen Tresoren beendet. Daher ist es wichtig, Ihre Daten in einem neuen Tresor in GWC für Hybridworkloads (DPM-Server(Data Protection Manager), Azure Backup Server (MABS), Microsoft Azure Recovery Services (MARS)) zu schützen, bevor Sie mit dem Verschieben von Sicherungsdaten aus den Regionen beginnen.
So beginnen Sie mit dem Schützen in einem neuen Tresor:

1. Erstellen Sie einen neuen Tresor (VaultN) in GWC.
1. Registrieren Sie Ihren DPM-Server/MABS-/MARS-Agent erneut bei VaultN.
1. Weisen Sie die Richtlinie zu, und beginnen Sie mit dem Erstellen von Sicherungen.

Die erste Sicherung ist eine vollständige Kopie, gefolgt von inkrementellen Sicherungen.

>[!Important]
>- Stellen Sie vor dem Initiieren der Verschiebung von Sicherungsdaten sicher, dass die erste vollständige Sicherung in VaultN abgeschlossen wurde.
>- Verwalten Sie für DPM/MABS die Passphrase aus dem ursprünglichen Tresor an einem sicheren Speicherort, da Sie die gleiche Passphrase zum Wiederherstellen von Daten aus dem Zieltresor benötigen. Ohne die ursprüngliche Passphrase ist die Wiederherstellung aus dem Quelltresor nicht möglich.

### <a name="step-1-download-the-resources"></a>Schritt 1: Herunterladen der Ressourcen

Laden Sie die erforderlichen Ressourcen herunter, und installieren Sie sie.

1. [Laden](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.3) Sie die neueste Version von PowerShell (PowerShell 7) herunter.
1. Verwenden Sie Version 4.2.0 des Az.RecoveryServices-Moduls, die in Azure Cloud Shell verfügbar ist.
1. [Aktualisieren](https://aka.ms/azurebackup_agent) Sie alle MARS-Agents auf die neueste Version.
1. Überprüfen Sie Ihre Passphrase. Wenn Sie sie neu generieren müssen, befolgen Sie die [Validierungsschritte](https://support.microsoft.com/topic/mandatory-update-for-azure-backup-for-microsoft-azure-recovery-services-agent-411e371c-aace-e134-de6b-bf9fda48026e#section-3).

### <a name="step-2-create-a-target-vault-in-gwc"></a>Schritt 2: Erstellen eines Zieltresors in GWC

Erstellen Sie einen Zieltresor (Vault 2) in GWC. Informationen zum Erstellen eines Tresors finden Sie unter [Erstellen und Konfigurieren eines Recovery Services-Tresors](../backup/backup-create-rs-vault.md).

>[!Note]
>- Stellen Sie sicher, dass der Tresor keine geschützten Elemente enthält.
>- Stellen Sie sicher, dass der Zieltresor über die erforderliche Redundanz verfügt: lokal redundanter Speicher (LRS) oder georedundanten Speicher (GRS).

### <a name="step-3---use-powershell-to-trigger-backup-data-move"></a>Schritt 3: Verwenden von PowerShell zum Auslösen des Verschiebens von Sicherungsdaten

#### <a name="get-the-source-vault-from-gne-or-gec"></a>Abrufen des Quelltresors aus GNE oder GEC

Führen Sie die folgenden Cmdlets aus:

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$srcVault = Get-AzRecoveryServicesVault -name “srcVault” -ResourceGroupName “TestSourceRG”`

>[!Note]
>- `srcVault` = Quelltresor
>- `TestSourceRG` = Quellressourcengruppe

#### <a name="get-the-target-vault-in-gwc"></a>Abrufen des Zieltresors in GWC

Führen Sie die folgenden Cmdlets aus:

1. `Connect-AzAccount`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$trgVault = Get-AzRecoveryServicesVault -name “targetVault” -ResourceGroupName “TestTargetRG”`

>[!Note]
>- `targetVault` = Zieltresor
>- `TestTargetRG` = Testressourcengruppe

#### <a name="perform-validation"></a>Ausführen der Überprüfung
 
Führen Sie die folgenden Cmdlets aus:

1. `$validated = $false`
1. `$validated = Test-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault`

#### <a name="initializeprepare-ds-move"></a>Initialisieren/Vorbereiten der DS-Verschiebung

Führen Sie die folgenden Cmdlets aus:

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -SubscriptionName $srcSub`
1. ```azurepowershell
   if($validated) {
           $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault
                       }
   ```
1. `$corr`

#### <a name="trigger-ds-move"></a>Auslösen der DS-Verschiebung

Führen Sie die folgenden Cmdlets aus:

1. `Connect-AzAccount`
1. `Set-AzContext -SubscriptionName $trgSub`
1. `Copy-AzRecoveryServicesVault - CorrelationIdForDataMove $corr -SourceVault $srcVault -TargetVault $trgVault -Force`

Sie können den Vorgangs mit dem Cmdlet `Get-AzRecoveryServicesBackupJob` überwachen.

>[!Note] 
>- Während des Verschiebens von Sicherungsdaten werden alle Sicherungselemente in einen vorübergehenden Zustand verschoben. In diesem Zustand werden die neuen Wiederherstellungspunkte (RPs) nicht erstellt, und alte RPs werden nicht bereinigt.
>- Da dieses Feature in GEC und GNE aktiviert ist, wird empfohlen, diese Schritte für einen kleinen Tresor durchzuführen und die Verschiebung zu überprüfen. Führen Sie bei Erfolg diese Schritte für alle Tresore aus.
>- Neben der Verschiebung der Sicherungsdaten für den gesamten Tresor wird die Verschiebung pro Container (VMs, DPM- und MABS-Server und MARS-Agents) ausgelöst. Verfolgen Sie den Fortschritt der Verschiebungen pro Container im Abschnitt **Aufträge** nach. 

 ![Überwachen des Fortschritts von Verschiebungsaufträgen.](./media/germany-migration-management-tools/track-move-jobs.png)

Während des Verschiebens werden die folgenden Aktionen für den Quelltresor blockiert:

- Neue geplante Sicherungen
- Beenden Sie die Sicherung mit dem Löschen von Daten.
- Löschen von Daten
- Fortsetzen der Sicherung
- Richtlinie ändern

### <a name="step-4-check-the-status-of-the-move-job"></a>Schritt 4: Überprüfen des Verschiebeauftrags

Der Vorgang zum Verschieben von Sicherungsdaten erfolgt pro Container. Bei Azure-VM-Sicherungen werden die VM-Sicherungen als Container betrachtet. Um den Fortschritt des Vorgangs zum Verschieben von Sicherungsdaten anzugeben, wird für jeden Container ein Auftrag erstellt.

Führen Sie zum Überwachen der Aufträge die folgenden Cmdlets aus:

1. `Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `$Jobs = Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `Get-AzRecoveryServicesBackupJobDetail -Job $Jobs[0] -VaultId $trgVault.ID`
1. `$JobDetails.ErrorDetails`

### <a name="step-5-post-move-operations"></a>Schritt 5: Vorgänge nach dem Verschieben

Nachdem der Vorgang zum Verschieben von Sicherungsdaten für alle Container in den Zieltresor abgeschlossen ist, ist keine weitere Aktion für VM-Sicherungen erforderlich.



#### <a name="verify-the-movement-of-containers-is-complete"></a>Überprüfen, dass die Containerbewegung abgeschlossen ist

Um zu überprüfen, ob alle Container aus dem Quelltresor in den Zieltresor verschoben wurden, navigieren Sie zum Zieltresor, und überprüfen Sie, ob alle Container in diesem Tresor vorhanden sind.

Führen Sie das folgende Cmdlet aus, um alle VMs aufzulisten, die aus dem Quelltresor in den Zieltresor verschoben wurden:

```azurepowershell
Get-AzRecoveryServicesBackupContainer -BackupManagementType “AzureVM” -VaultId $trgVault.ID
```

#### <a name="verify-the-movement-of-policies-is-complete"></a>Überprüfen, ob das Verschieben von Richtlinien abgeschlossen wurde

Nachdem die Sicherungsdaten erfolgreich in die neue Region verschoben wurden, werden alle Richtlinien, die auf Azure-VM-Sicherungselemente im Quelltresor angewendet wurden, auf den Zieltresor angewendet.

Um zu überprüfen, ob alle Richtlinien aus dem Quelltresor in den Zieltresor verschoben wurden, navigieren Sie zum Zieltresor, und führen Sie das folgende Cmdlet aus, um die Liste aller verschobenen Richtlinien abzurufen:

```azurepowershell
Get-AzRecoveryServicesBackupProtectionPolicy -VaultId $trgVault.ID
```

Diese Richtlinien gelten auch nach dem Verschieben weiterhin für Ihre Sicherungsdaten, sodass die Lebenszyklusverwaltung der verschobenen Wiederherstellungspunkte fortgesetzt wird.

Um eine plötzliche Bereinigung mehrerer Wiederherstellungspunkte zu vermeiden (die möglicherweise während des Verschiebens abgelaufen sind oder unmittelbar nach dem Verschieben ablaufen), wird die Bereinigung älterer Wiederherstellungspunkte (RPs) für einen Zeitraum von 10 Tagen nach dem Verschieben angehalten. Während dieses Zeitraums werden Ihnen die zusätzlichen Daten, die durch die alten RPs anfallen, nicht in Rechnung gestellt.

>[!Important]
>Wenn Sie diese älteren RPs wiederherstellen müssen, stellen Sie sie sofort nach dem Verschieben von Sicherungsdaten innerhalb dieses 10-tägigen Zeitraums wieder her. Sobald dieser Sicherheitszeitraum abgeschlossen ist, werden die auf jedes Sicherungselement angewendeten Richtlinien wirksam und erzwingen die Bereinigung der alten RPs.

#### <a name="restore-operations"></a>Wiederherstellungsvorgänge

**Wiederherstellen virtueller Azure-Computer**

Für virtuelle Azure-Computer können Sie die Wiederherstellung von den Wiederherstellungspunkten im Zieltresor ausführen.

#### <a name="configure-mars-agent"></a>Konfigurieren des MARS-Agents

1. Registrieren Sie sich erneut beim Zieltresor.
1. Führen Sie die Wiederherstellung von den Wiederherstellungspunkten aus.
1. Registrieren Sie sich nach der Wiederherstellung erneut beim neuen Tresor (VaultN), und setzen Sie Sicherungen fort.

>[!Note]
>Während der MARS-Agent beim Zieltresor registriert ist, werden keine neuen Sicherungen durchgeführt.

#### <a name="configure-dpmmabs"></a>Konfigurieren von DPM/MABS

**Empfohlen**

Verwenden Sie die externe DPM-Methode, um die Wiederherstellung durchzuführen. Weitere Informationen finden Sie unter [Wiederherstellen von Daten aus Azure Backup Server](../backup/backup-azure-alternate-dpm-server.md).

>[!Note]
>- OLR (Original-Location Recovery) wird nicht unterstützt.
>- Sicherungen werden in VaultN für alle registrierten Computer fortgesetzt.

**Andere Option**

Für OLR (Original-Location Recovery):

1. Registrieren Sie den DPM-Server/MABS erneut beim Zieltresor.
1. Führen Sie den Wiederherstellungsvorgang aus.
1. Registrieren Sie den DPM-Server/MABS erneut beim neuen Tresor.

>[!Note]
>Einschränkungen bei der Verwendung von DPM: <br><br> <ul><li>Der Sicherungsvorgang für alle Computer, die beim DPM-Server registriert sind, wird beendet, wenn Sie den DPM-Server mit dem Zieltresor verbinden.</li><li>Nachdem der DPM-Server nach der Wiederherstellung erneut beim neuen Tresor registriert wurde, werden Konsistenzprüfungen durchgeführt (die Zeit, die zum Abschließen dieses Vorgangs benötigt wird, hängt von der Datenmenge ab), bevor Sicherungen fortgesetzt werden.</li></ul>

### <a name="error-codes"></a>Fehlercodes

#### <a name="usererrorconflictingdatamoveonvault"></a>UserErrorConflictingDataMoveOnVault 

**Meldung**: Zurzeit wird ein weiterer Vorgang zum Verschieben für den Tresor ausgeführt. 

**Szenario**: Sie versuchen, den Datenverschiebevorgang für einen Quelltresor auszuführen, während ein anderer Datenverschiebevorgang bereits für den gleichen Quelltresor ausgeführt wird.

**Empfohlene Aktion**: Warten Sie, bis der aktuelle Vorgang zum Verschieben von Daten abgeschlossen ist, und versuchen Sie es noch mal.

#### <a name="usererroroperationnotallowedduringdatamove"></a>UserErrorOperationNotAllowedDuringDataMove

**Meldung**: Dieser Vorgang ist unzulässig, weil zurzeit ein Vorgang zum Verschieben von Daten durchgeführt wird. 

**Szenarien**: Während des Datenverschiebevorgangs sind die folgenden Vorgänge im Quelltresor unzulässig:
 
- Beenden der Sicherung unter Beibehaltung der Daten 
- Beenden Sie die Sicherung mit dem Löschen von Daten. 
- Löschen von Sicherungsdaten. 
- Sicherung fortsetzen 
- Ändern der Richtlinie.

**Empfohlene Aktion**: Warten Sie, bis der Vorgang zum Verschieben von Daten abgeschlossen ist, und versuchen Sie es noch mal. [Weitere Informationen](#azure-backup) zu den unterstützten Vorgängen.

#### <a name="usererrornocontainersfoundfordatamove"></a>UserErrorNoContainersFoundForDataMove 

**Meldung**: In diesem Tresor sind keine Container vorhanden, die für den Vorgang zum Verschieben von Daten unterstützt werden. 

**Szenarien**: Diese Meldung wird angezeigt, wenn Folgendes zutrifft:

- Der Quelltresor verfügt über gar keine Container. 
- Der Quelltresor verfügt nur über nicht unterstützte Container. 
- Der Quelltresor enthält alle Container, die zuvor in einen Zieltresor verschoben wurden, und Sie haben IgnoreMoved = true in der API übergeben.

**Empfohlene Aktion**: [Informieren](#azure-backup) Sie sich über die unterstützten Container für die Datenverschiebung.

#### <a name="usererrordatamovenotsupportedatcontainerlevel"></a>UserErrorDataMoveNotSupportedAtContainerLevel 

**Meldung**: Der Vorgang zum Verschieben von Daten wird auf Containerebene nicht unterstützt. 

**Szenario**: Sie haben einen Datenschiebevorgang auf Containerebene ausgewählt. 

**Empfohlene Aktion**: Versuchen Sie den Vorgang der Datenverschiebung auf Tresorebene.

### <a name="usererrordatamovenotallowedcontainer-registrationinprogress"></a>UserErrorDataMoveNotAllowedContainer RegistrationInProgress 

**Meldung**: Der Vorgang zum Verschieben von Daten ist unzulässig, weil zurzeit ein Containerregistrierungsvorgang im Quelltresor durchgeführt wird. 

**Szenario**: Ein Containerregistrierungsvorgang wird im Quelltresor ausgeführt, als Sie versucht haben, Daten zu verschieben. 

**Empfohlene Aktion**: Versuchen Sie den Datenschiebevorgang nach einiger Zeit.

#### <a name="usererrordatamovenotallowedtargetvaultnotempty"></a>UserErrorDataMoveNotAllowedTargetVaultNotEmpty 

**Meldung**: Der Vorgang zum Verschieben von Daten ist unzulässig, weil im Zieltresor bereits einige Container registriert sind. 

**Szenario**: Für den ausgewählten Zieltresor sind bereits einige Container registriert. 

**Empfohlene Aktion**: Versuchen Sie den Datenschiebevorgang für einen leeren Zieltresor.

#### <a name="usererrorunsupportedsourceregionfordatamove"></a>UserErrorUnsupportedSourceRegionForDataMove 

**Meldung**: Der Vorgang zum Verschieben von Daten wird für diese Region nicht unterstützt. 

**Szenario**: Die Zielregion ist ungültig.

**Empfohlene Aktion**: Überprüfen Sie die [Liste der unterstützten Regionen](#azure-backup) für die Verschiebung von Daten.

#### <a name="usererrorunsupportedtargetregionfordatamove"></a>UserErrorUnsupportedTargetRegionForDataMove 

**Meldung**: Der Vorgang zum Verschieben von Daten in diese Region wird nicht unterstützt.

**Szenario**: Die Zielregion-ID ist ungültig. 

**Empfohlene Aktion**: Überprüfen Sie die [Liste der unterstützten Regionen](#azure-backup) für die Verschiebung von Daten.


#### <a name="usererrordatamovetargetvaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveTargetVaultWithPrivate EndpointNotSupported 

**Meldung**: Die Daten können nicht verschoben werden, weil der ausgewählte Quelltresor private Endpunkte enthält. 

**Szenario**: Private Endpunkte sind im Zieltresor aktiviert. 

**Empfohlene Aktion**: Löschen Sie die privaten Endpunkte, und wiederholen Sie den Verschiebevorgang. [Weitere Informationen](#azure-backup) zu den unterstützten Vorgängen.

### <a name="usererrordatamovesourcevaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveSourceVaultWithPrivate EndpointNotSupported 

**Meldung**: Die Daten können nicht verschoben werden, weil der ausgewählte Quelltresor private Endpunkte enthält.

**Szenario**: Private Endpunkte sind im Quelltresor aktiviert.

**Empfohlene Aktion**: Löschen Sie die privaten Endpunkte, und wiederholen Sie den Verschiebevorgang. [Weitere Informationen](../backup/private-endpoints.md#deleting-private-endpoints) zu den unterstützten Vorgängen.

#### <a name="usererrordatamovesourcevaultwithcmk-notsupported"></a>UserErrorDataMoveSourceVaultWithCMK NotSupported 

**Meldung**: Die Daten können nicht verschoben werden, weil für den ausgewählten Quelltresor Verschlüsselung aktiviert ist. 

**Szenario**: Kundenseitig verwaltete Schlüssel (Customer-Managed Keys, CMK) sind im Quelltresor aktiviert.

**Empfohlene Aktion**: [Informieren](#azure-backup) Sie sich über die unterstützten Vorgänge.

#### <a name="usererrordatamovetargetvaultwithcmknotsupported"></a>UserErrorDataMoveTargetVaultWithCMKNotSupported 

**Meldung**: Die Daten können nicht verschoben werden, weil für den ausgewählten Zieltresor die Verschlüsselung aktiviert ist. 

**Szenario**: Kundenseitig verwaltete Schlüssel (Customer-Managed Keys, CMK) sind im Zieltresor aktiviert.

**Empfohlene Aktion**: [Informieren](#azure-backup) Sie sich über die unterstützten Vorgänge.

## <a name="scheduler"></a>Scheduler

Azure Scheduler wird eingestellt. Wenn Sie Planungsaufträge erstellen möchten, können Sie stattdessen [Azure Logic Apps](../logic-apps/logic-apps-overview.md) im globalen Azure verwenden.

Weitere Informationen finden Sie unter:

- Erfahren Sie mehr, indem Sie die [Tutorials zu Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md) durcharbeiten.
- Lesen Sie [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="network-watcher"></a>Network Watcher

Das Migrieren einer Azure Network Watcher-Instanz von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es wird empfohlen, eine neue Network Watcher-Instanz in Azure weltweit zu erstellen und zu konfigurieren. Vergleichen Sie dann die Ergebnisse zwischen der alten und der neuen Umgebung. 

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Network Watcher-Tutorials](../network-watcher/index.yml) durcharbeiten.
- Im Artikel [Übersicht über Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) finden Sie weitere Informationen zu diesem Thema.
- Erfahren Sie mehr über [Datenflussprotokolle für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-portal.md).
- Erfahren Sie mehr über [Verbindungsmonitor](../network-watcher/connection-monitor.md).

## <a name="site-recovery"></a>Site Recovery

Sie können Ihr aktuelles Azure Site Recovery-Setup nicht zu Azure weltweit migrieren. Sie müssen eine neue Site Recovery-Lösung in Azure weltweit einrichten.

Weitere Informationen zu Site Recovery und Informationen dazu, wie Sie virtuelle Computer von Azure Deutschland zu Azure weltweit migrieren, finden Sie unter [Migration von Compute-Ressourcen von Azure Deutschland zu Azure weltweit](./germany-migration-compute.md#compute-iaas).

Frischen Sie Ihre Kenntnisse auf, indem Sie die folgenden Schritt-für-Schritt-Tutorials durcharbeiten:

- [Azure zu Azure: Notfallwiederherstellung](../site-recovery/azure-to-azure-about-networking.md)
- [VMware zu Azure: Notfallwiederherstellung](../site-recovery/site-recovery-deployment-planner.md)
- [Hyper-V zu Azure: Notfallwiederherstellung](../site-recovery/hyper-v-deployment-planner-overview.md)

## <a name="azure-policies"></a>Azure-Richtlinien

Sie können Richtlinien nicht direkt von Azure Deutschland zu Azure weltweit migrieren. Während einer Migration wird der Gültigkeitsbereich von zugewiesenen Richtlinien üblicherweise geändert. Dies trifft insbesondere zu, wenn sich das Abonnement in der Zielumgebung unterscheidet, wie in diesem Szenario. Sie haben aber die Möglichkeit, Richtliniendefinitionen beizubehalten und sie in Azure weltweit wiederzuverwenden.

Führen Sie in der Azure CLI den folgenden Befehl aus, um alle Richtlinien in Ihrer aktuellen Umgebung aufzulisten.

> [!NOTE]
> Wechseln Sie in der Azure CLI unbedingt in die Umgebung „AzureGermanCloud“, bevor Sie die folgenden Befehle ausführen.


```azurecli
az policy definition list --query '[].{Type:policyType,Name:name}' --output table
```

Exportieren Sie nur Richtlinien, für die **PolicyType** den Wert **Custom** hat. Exportieren Sie **policyRule** in eine Datei. Im folgenden Beispiel wird die benutzerdefinierte Richtlinie „Allow Germany Central Only“ (Kurzversion `allowgconly`) in eine Datei im aktuellen Ordner exportiert: 

```azurecli
az policy definition show --name allowgconly --output json --query policyRule > policy.json
```

Ihre Exportdatei entspricht etwa dem folgenden Beispiel:

```json
{
  "if": {
    "not": {
      "equals": "germanycentral",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

Wechseln Sie nun in Umgebung von Azure weltweit. Ändern Sie die Richtlinienregel, indem Sie die Datei bearbeiten. Ändern Sie beispielsweise `germanycentral` in `westeurope`.

```json
{
  "if": {
    "not": {
      "equals": "westeurope",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

Erstellen Sie die neue Richtlinie:

```azurecli
cat policy.json |az policy definition create --name "allowweonly" --rules @-
```

Sie haben nun eine neue Richtlinie namens `allowweonly`. Die Richtlinie lässt nur „Europa, Westen“ als Region zu.

Weisen Sie die Richtlinie den Bereichen in Ihrer neuen Umgebung entsprechend zu. Sie können die alten Zuordnungen in Azure Deutschland dokumentieren, indem Sie den folgenden Befehl ausführen:

```azurecli
az policy assignment list
```

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie das [Tutorial zu Azure-Richtlinien](../governance/policy/tutorials/create-and-manage.md) durcharbeiten.
- Erfahren Sie, wie Sie [Richtlinien über die Azure CLI anzeigen](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-azure-cli) oder [Richtlinien über PowerShell anzeigen](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-powershell).
- Erfahren Sie, wie Sie [eine Richtliniendefinition über die Azure CLI erstellen](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-azure-cli) oder [eine Richtliniendefinition über PowerShell erstellen](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-powershell).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="where-can-i-move-the-backup-data"></a>Wo kann ich die Sicherungsdaten verschieben?

Sie können Ihre Sicherungsdaten aus Recovery Services-Tresoren (RSVs) in „Deutschland, Mitte“ (GEC) und „Deutschland, Nordosten“ (GNE) nach „Deutschland, Westen-Mitte“ (GWC) verschieben.

### <a name="what-backup-data-can-i-move"></a>Welche Sicherungsdaten kann ich verschieben?

Ab dem 21. Juni 2021 können Sie die Sicherungsdaten für die folgenden Workloads aus einer Region in eine andere verschieben:

- Azure-VMs
- Hybridworkloads
- Datei-/Ordnersicherung mit dem MARS-Agent (Microsoft Azure Recovery Services)
- DPM-Server (Data Protection Manager)
- Azure Backup Server (MABS)

### <a name="how-can-i-move-backup-data-to-another-region"></a>Wie kann ich Sicherungsdaten in eine andere Region verschieben?

Um sicherzustellen, dass Daten in den vorhandenen Regionen nicht verloren gehen, ist für Azure Backup das Verschieben von Sicherungsdaten aus GEC und GNE in GWC aktiviert.

Während der Migration werden Sicherungen in GEC und GNE beendet. Daher ist es wichtig, die Workloads in der neuen Region zu schützen, bevor Sie den Migrationsvorgang starten.

### <a name="what-to-do-if-the-backup-data-move-operation-fails"></a>Was ist zu tun, wenn beim Verschieben der Sicherungsdaten ein Fehler auftritt?

Für die Sicherungsdaten können die folgenden Fehlerszenarien auftreten:

| Fehlermeldungen    | Ursachen |
| --- | --- |
| Geben Sie einen leeren Zieltresor an. Der Zieltresor sollte keine Sicherungselemente oder Sicherungscontainer enthalten. | Sie haben einen Zieltresor ausgewählt, der bereits über einige geschützte Elemente verfügt. |
| Azure Backup-Daten dürfen nur in unterstützte Zielregionen verschoben werden. | Sie haben einen Zieltresor aus einer Region ausgewählt, die keine der unterstützten Regionen für das Verschieben ist. |

Sie müssen die Sicherung von Grund auf wiederholen, indem Sie den gleichen Befehl (unten angegeben) mit einem neuen leeren Zieltresor ausführen, oder Sie können versuchen, fehlerhafte Elemente erneut aus dem Quelltresor zu verschieben, indem Sie sie mit einem Flag angeben.

```azurepowershell
   if($validated) {
                              $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault -RetryOnlyFailed
                      }
```

### <a name="is-there-a-cost-involved-in-moving-this-backup-data"></a>Sind Kosten mit dem Verschieben dieser Sicherungsdaten verbunden?

Nein. Es sind keine zusätzlichen Kosten für das Verschieben Ihrer Sicherungsdaten aus einer Region in eine andere verbunden. Azure Backup trägt die Kosten für das regionsübergreifende Verschieben von Daten. Nach Abschluss des Verschiebevorgangs gilt nur ein 10-tägiger Zeitraum, in dem keine Kosten anfallen. Nach diesem Zeitraum beginnt die Abrechnung im Zieltresor.

### <a name="if-i-face-issues-in-moving-backup-data-whom-should-i-contact"></a>An wen sollte ich mich wenden, wenn beim Verschieben von Sicherungsdaten Probleme auftreten?

Wenn Sie Probleme mit dem Verschieben von Sicherungsdaten aus GEC oder GNE in GWC haben, schreiben Sie uns unter [GESupportAzBackup@microsoft.com](mailto:GESupportAzBackup@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [Medien](./germany-migration-media.md)
