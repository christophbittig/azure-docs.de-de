---
title: Migrieren eines Azure Cosmos DB-Kontos vom zyklischen Sicherungsmodus zum fortlaufenden Sicherungsmodus
description: Azure Cosmos DB unterstützt derzeit nur eine irreversible Migration vom zyklischen Sicherungsmodus zum fortlaufenden Sicherungsmodus, die nicht rückgängig gemacht werden kann. Nach der Migration vom zyklischen Sicherungsmodus zum fortlaufenden Sicherungsmodus können Sie von den Vorteilen des kontinuierlichen Modus profitieren.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2021
ms.author: sngun
ms.topic: how-to
ms.reviewer: sngun
ms.openlocfilehash: 3ac1ae98dfa920a4b4060905e99b7378fcdb3414
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476298"
---
# <a name="migrate-an-azure-cosmos-db-account-from-periodic-to-continuous-backup-mode"></a>Migrieren eines Azure Cosmos DB-Kontos vom zyklischen Sicherungsmodus zum fortlaufenden Sicherungsmodus
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB-Konten mit Sicherungsrichtlinien im zyklischen Sicherungsmodus können über das [Azure-Portal](#portal), die [CLI](#cli), [PowerShell](#powershell) oder [Resource Manager-Vorlagen](#ARM-template) zum fortlaufenden Sicherungsmodus migriert werden. Die Migration vom zyklischen zum fortlaufenden Modus ist eine irreversible Migration, die nicht rückgängig gemacht werden kann. Nach der Migration vom zyklischen Sicherungsmodus zum fortlaufenden Sicherungsmodus können Sie von den Vorteilen des kontinuierlichen Modus profitieren.

Im Folgenden finden Sie die wichtigsten Gründe für eine Migration zum fortlaufenden Modus:

* Die Möglichkeit, eine Self-Service-Wiederherstellung über das Azure-Portal, die CLI oder PowerShell durchzuführen.
* Die Möglichkeit, im Zeitfenster der letzten 30 Tage eine sekundengenaue Wiederherstellung durchzuführen.
* Die Möglichkeit, sicherzustellen, dass die Sicherung innerhalb eines Zeitraums über Shards oder Partitionsschlüsselbereiche hinweg konsistent ist.
* Die Möglichkeit, einen Container, eine Datenbank oder das gesamte Konto wiederherzustellen, wenn der Container, die Datenbank oder das Konto gelöscht oder geändert wurde.
* Die Möglichkeit, die gewünschten Ereignisse im Container, in der Datenbank oder im Konto auszuwählen und zu entscheiden, wann die Wiederherstellung erfolgen soll.

> [!NOTE]
> Die Migrationsfunktion ist eine irreversible Aktion, die nicht rückgängig gemacht werden kann. Das bedeutet, dass Sie nach einem Wechsel vom zyklischen zum fortlaufenden Modus nicht wieder zum zyklischen Modus zurückkehren können.
>
> Sie können ein Konto nur dann zum fortlaufenden Sicherungsmodus migrieren, wenn die folgenden Bedingungen erfüllt sind. Überprüfen Sie außerdem [die Einschränkungen für die Point-in-Time-Wiederherstellung](continuous-backup-restore-introduction.md#current-limitations), bevor Sie Ihr Konto migrieren:
>
> * Das Konto ist vom Typ SQL-API oder API für MongoDB.
> * Das Konto verfügt über eine einzelne Schreibregion.
> * Das Konto ist nicht mit kundenseitig verwalteten Schlüsseln (Customer Managed Keys, CMK) aktiviert.
> * Für das Konto sind keine Analysespeicher aktiviert.

## <a name="permissions"></a>Berechtigungen

Zum Durchführen der Migration benötigen Sie die Berechtigung `Microsoft.DocumentDB/databaseAccounts/write` für das zu migrierende Konto.  

## <a name="pricing-after-migration"></a>Preise nach der Migration

Nachdem Sie Ihr Konto zum fortlaufenden Sicherungsmodus migriert haben, fallen andere Kosten an als für den zyklischen Sicherungsmodus. Eine fortlaufende Sicherung ist wesentlich kostengünstiger als zyklische Sicherungen. Weitere Informationen finden Sie im Beispiel für die [Preise für Sicherungen im fortlaufenden Modus](continuous-backup-restore-introduction.md#continuous-backup-pricing).

## <a name="migrate-using-portal"></a><a id="portal"></a> Migrieren mithilfe des Portals

Führen Sie die folgenden Schritte aus, um Ihr Konto vom zyklischen Sicherungsmodus zum Modus der fortlaufenden Sicherung zu migrieren:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto, und öffnen Sie den Bereich **Features**. Wählen Sie **Fortlaufende Sicherung** und dann **Aktivieren** aus.

   :::image type="content" source="./media/migrate-continuous-backup/enable-backup-migration.png" alt-text="Migrieren zum fortlaufenden Modus über das Azure-Portal" lightbox="./media/migrate-continuous-backup/enable-backup-migration.png":::

1. Während die Migration ausgeführt wird, wird der Status **Ausstehend** angezeigt. Nach Abschluss der Migration wechselt der Status zu **Ein**. Die Dauer der Migration richtet sich nach der Größe der Daten in Ihrem Konto.

   :::image type="content" source="./media/migrate-continuous-backup/migration-status.png" alt-text="Überprüfen des Migrationsstatus im Azure-Portal" lightbox="./media/migrate-continuous-backup/migration-status.png":::

## <a name="migrate-using-powershell"></a><a id="powershell"></a>Migrieren mit PowerShell

Installieren Sie die [neueste Version von Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) bzw. eine höhere Version als 6.2.0. Führen Sie dann die folgenden Schritte aus:

1. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

1. Migrieren Sie Ihr Konto vom zyklischen zum fortlaufenden Sicherungsmodus:

   ```azurepowershell-interactive
   Update-AzCosmosDBAccount ` 
     -ResourceGroupName "myrg" ` 
     -Name "myAccount" `
     -BackupPolicyType Continuous
   ```

### <a name="check-the-migration-status"></a>Überprüfen des Migrationstatus

Führen Sie den folgenden Befehl aus und prüfen SIe die Eigenschaften **status**, **targetType** des Objekts **backupPolicy**. Der Status wird nach Beginn der Migration als „wird ausgeführt“ angezeigt:

```azurepowershell-interactive
az cosmosdb show -n "myAccount" -g "myrg"
```

:::image type="content" source="./media/migrate-continuous-backup/migration-status-started-powershell.png" alt-text="Überprüfen des Migrationsstatus mit dem PowerShell-Befehl":::

Wenn die Migration abgeschlossen ist, ändert sich der Sicherungstyp in **Fortlaufend**. Führen Sie denselben Befehl erneut aus, um den Status zu überprüfen:

```azurepowershell-interactive
az cosmosdb show -n "myAccount" -g "myrg"
```

:::image type="content" source="./media/migrate-continuous-backup/migration-status-complete-powershell.png" alt-text="Nach Abschluss der Migration ändert sich der Sicherungstyp in „Fortlaufend“.":::

## <a name="migrate-using-cli"></a><a id="cli"></a>Migrieren mithilfe der CLI

1. Installieren Sie die neueste Version der Azure CLI:

   * Wenn Sie noch nicht über die CLI verfügen, [installieren](/cli/azure/) Sie die neueste Version der Azure CLI bzw. eine höhere Version als 2.26.0.
   * Wenn die Azure CLI bereits installiert ist, verwenden Sie den Befehl `az upgrade`, um ein Upgrade auf die aktuelle Version durchzuführen.
   * Alternativ dazu können Sie auch die Cloud Shell im Azure-Portal verwenden.

1. Melden Sie sich bei Ihrem Azure-Konto an, und führen Sie den folgenden Befehl aus, um Ihr Konto zum fortlaufenden Modus zu migrieren:

   ```azurecli-interactive
   az login

   az cosmosdb update -n <myaccount> -g <myresourcegroup> --backup-policy-type continuous
   ```

1. Nach erfolgreichem Abschluss der Migration zeigt die Ausgabe, dass die Eigenschaft „type“ des backupPolicy-Objekts auf „Continuous“ festgelegt ist.

   ```console
    {
      "apiProperties": null,
      "backupPolicy": {
        "type": "Continuous"
      },
      "capabilities": [],
      "connectorOffer": null,
      "consistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxIntervalInSeconds": 5,
        "maxStalenessPrefix": 100
      },
    …
    }
   ```

## <a name="migrate-using-resource-manager-template"></a><a id="ARM-template"></a> Migrieren mithilfe einer Resource Manager-Vorlage

Um mithilfe einer ARM-Vorlage zum fortlaufenden Sicherungsmodus zu migrieren, suchen Sie in der Vorlage den Abschnitt „backupPolicy“, und aktualisieren Sie die Eigenschaft `type`. Ein Beispiel: In Ihrer vorhandenen Vorlage ist die Sicherungsrichtlinie folgendermaßen als JSON-Objekt festgelegt:

```json
"backupPolicy": {
   "type": "Periodic",
   "periodicModeProperties": {
   "backupIntervalInMinutes": 240,
   "backupRetentionIntervalInHours": 8
   }
},
```

Ersetzen Sie dieses Objekt durch das folgende JSON-Objekt:

```json
"backupPolicy": {
   "type": "Continuous"
},
```

Stellen Sie als Nächstes die Vorlage mithilfe von Azure PowerShell oder der CLI bereit. Im folgenden Beispiel wird gezeigt, wie die Vorlage mit einem CLI-Befehl bereitgestellt wird:

```azurecli
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="what-to-expect-during-and-after-migration"></a>Was ist während und nach der Migration zu erwarten?

Während der Migration vom zyklischen zum fortlaufenden Modus können Sie keine Vorgänge auf Steuerungsebene ausführen, die Aktualisierungen oder Löschungen auf Kontoebene durchführen. Beispielsweise können Vorgänge wie das Hinzufügen oder Entfernen von Regionen, ein Kontofailover, eine Aktualisierung von Sicherungsrichtlinien usw. nicht ausgeführt werden, während die Migration läuft. Die Dauer der Migration richtet sich nach der Größe der Daten und der Anzahl von Regionen in Ihrem Konto. Wiederherstellungsaktionen in den migrierten Konten sind nur ab dem Zeitpunkt erfolgreich, an dem die Migration erfolgreich abgeschlossen wurde.

Sie können Ihr Konto wiederherstellen, nachdem die Migration abgeschlossen ist. Wenn die Migration beispielsweise um 13:00 Uhr MEZ abgeschlossen ist, können Sie ab 13:00 Uhr MEZ Zeitpunktwiederherstellungen ausführen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

#### <a name="does-the-migration-only-happen-at-the-account-level"></a>Erfolgt die Migration nur auf Kontoebene?
Ja.

#### <a name="which-accounts-can-be-targeted-for-backup-migration"></a>Welche Konten können als Ziel für eine Sicherungsmigration verwendet werden?
Derzeit unterstützen SQL API- und API für MongoDB-Konten mit Einzelschreibregion die Migration, die gemeinsam genutzten Durchsatz, bereitgestellten Durchsatz oder bereitgestellten Durchsatz mit Autoskalierung verwenden.

Eine Migration von Konten mit Analysespeichern, mehreren Schreibregionen und kundenseitig verwalteten Schlüsseln (Customer Managed Keys, CMKs) wird nicht unterstützt.

#### <a name="does-the-migration-take-time-what-is-the-typical-time"></a>Dauert die Migration lang? Wie lang in der Regel?
Die Migration nimmt einige Zeit in Anspruch. Die Dauer richtet sich nach der Größe der Daten und der Anzahl von Regionen in Ihrem Konto. Sie können den Migrationsstatus mithilfe von Azure CLI- oder PowerShell-Befehlen abrufen. Bei umfangreichen Konten mit mehreren Terabyte an Daten kann die Migration einige Tage dauern.

#### <a name="does-the-migration-cause-any-availability-impactdowntime"></a>Wirkt sich die Migration auf die Verfügbarkeit aus oder verursacht sie Ausfallzeiten?
Nein, der Migrationsvorgang findet im Hintergrund statt und wirkt sich nicht auf Clientanforderungen aus. Allerdings müssen während der Migration einige Vorgänge im Back-End ausgeführt werden, die länger dauern können, wenn das Konto sehr ausgelastet ist.

#### <a name="what-happens-if-the-migration-fails-will-i-still-get-the-periodic-backups-or-get-the-continuous-backups"></a>Was passiert, wenn die Migration nicht erfolgreich abgeschlossen werden kann? Erhalte ich weiterhin zyklische Sicherungen oder fortlaufende Sicherungen?
Sobald der Migrationsprozess gestartet wurde, wechselt das Konto in den fortlaufenden Modus.  Wenn die Migration nicht erfolgreich durchgeführt wird, müssen Sie den Migrationsvorgang so lange erneut initiieren, bis er erfolgreich abgeschlossen ist.

#### <a name="how-do-i-perform-a-restore-to-a-timestamp-beforeduringafter-the-migration"></a>Wie führe ich vor/während/nach der Migration eine Wiederherstellung auf einen bestimmten Zeitstempel durch?
Angenommen, Sie starten die Migration zum Zeitpunkt t1, und der Prozess ist zum Zeitpunkt t5 beendet, können Sie keinen Wiederherstellungszeitstempel zwischen t1 und t5 verwenden.

Zum Wiederherstellen auf einen Zeitpunkt nach t5 können Sie – da sich Ihr Konto jetzt im fortlaufenden Modus befindet – den Vorgang über das Azure-Portal, mit der CLI oder in PowerShell durchführen, genauso wie Sie es normalerweise bei einem fortlaufenden Konto tun. Diese Self-Service-Wiederherstellungsanforderung kann erst ausgeführt werden, nachdem die Migration abgeschlossen ist.

Wenn Sie eine Wiederherstellung auf einen Zeitpunkt vor t1 durchführen müssen, öffnen Sie ein Supportticket, genauso wie Sie es normalerweise bei einem fortlaufenden Konto tun. Nach der Migration haben Sie bis zu 30 Tage Zeit, um zyklische Wiederherstellungen auszuführen.  Während dieser 30 Tage können Sie Wiederherstellungen basierend auf den Beibehaltungs- und Intervalleinstellungen ausführen, die vor der Migration in Ihrem Konto für Sicherungen galten.  Ein Beispiel: Wenn Ihre Sicherungen so konfiguriert waren, dass 24 Exemplare mit Intervallen von jeweils einer Stunde beibehalten wurden, können Sie eine Wiederherstellung auf jeden beliebigen Zeitpunkt zwischen [t1 minus 24 Stunden] und [t1] ausführen.

#### <a name="which-account-level-control-plane-operations-are-blocked-during-migration"></a>Welche Vorgänge auf Konto- bzw. Steuerungsebene sind während der Migration blockiert?
Vorgänge wie Hinzufügen/Entfernen von Regionen, Failover, Ändern der Sicherungsrichtlinie oder zu Datenverschiebung führende Durchsatzänderungen sind während der Migration blockiert.

#### <a name="if-the-migration-fails-for-some-underlying-issue-would-it-still-block-the-control-plane-operation-until-it-is-retried-and-completed-successfully"></a>Wenn die Migration aufgrund eines zugrunde liegenden Problems nicht abgeschlossen werden kann, sind Vorgänge auf Steuerungsebene weiterhin blockiert, bis die Migration erneut versucht und erfolgreich abgeschlossen wurde?
Vorgänge auf Steuerungsebene werden durch eine nicht abgeschlossene Migration nicht blockiert. Wenn die Migration nicht abgeschlossen wurde, empfiehlt es sich, sie zu wiederholen, bis sie erfolgreich abgeschlossen ist, bevor Sie andere Vorgänge auf Steuerungsebene ausführen.

#### <a name="is-it-possible-to-cancel-the-migration"></a>Kann die Migration abgebrochen werden?
Es ist nicht möglich, die Migration abzubrechen, da es sich um einen irreversiblen Vorgang handelt.

#### <a name="is-there-a-tool-that-can-help-estimate-migration-time-based-on-the-data-usage-and-number-of-regions"></a>Gibt es ein Tool, mit dem sich die Dauer der Migration basierend auf der Datennutzung und Anzahl von Regionen schätzen lässt?
Es gibt kein Tool zur Schätzung der Dauer. Aus unseren Testläufen konnten wir aber ableiten, dass die Migration einer einzelnen Region mit 1 TB Daten etwa eineinhalb Stunden dauert.

Bei Konten mit mehreren Regionen lässt sich die Gesamtdatengröße mit `Number_of_regions * Data_in_single_region` berechnen.

#### <a name="since-the-continuous-backup-mode-is-now-ga-would-you-still-recommend-restoring-a-copy-of-your-account-and-try-migration-on-the-copy-before-deciding-to-migrate-the-production-account"></a>Da die fortlaufende Sicherung jetzt allgemein verfügbar ist, empfehlen Sie dennoch weiterhin, eine Kopie des Kontos wiederherzustellen und die Migration mit dieser Kopie zu testen, bevor das Produktionskonto migriert wird?
Vor dem Migrieren von Produktionskonten empfiehlt es sich, das Feature des fortlaufenden Sicherungsmodus zu testen, um zu ermitteln, ob es erwartungsgemäß funktioniert. Die Migration ist irreversibel, kann also nicht rückgängig gemacht werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Modus fortlaufender Sicherungen finden Sie in den folgenden Artikeln:

* [Fortlaufende Sicherung: Einführung](continuous-backup-restore-introduction.md)

* [Fortlaufende Sicherung: Ressourcenmodell](continuous-backup-restore-resource-model.md)

* Wiederherstellen eines Kontos über das [Azure-Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), die [CLI](restore-account-continuous-backup.md#restore-account-cli) oder [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template)

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen?
   * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](convert-vcore-to-request-unit.md) 
   * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)
