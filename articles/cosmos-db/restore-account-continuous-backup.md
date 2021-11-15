---
title: Wiederherstellen eines Azure Cosmos DB-Kontos, das den fortlaufenden Sicherungsmodus verwendet
description: Erfahren Sie, wie Sie den Wiederherstellungszeitpunkt ermitteln und ein aktives oder gelöschtes Azure Cosmos DB-Konto wiederherstellen. Es wird gezeigt, wie Sie mithilfe des Ereignisfeeds den Wiederherstellungszeitpunkt ermitteln und das Konto über das Azure-Portal, mit PowerShell, der CLI oder einer Resource Manager-Vorlage wiederherstellen.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/03/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3161971323ebda6b55ec0fb423089d3115cd9c01
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562122"
---
# <a name="restore-an-azure-cosmos-db-account-that-uses-continuous-backup-mode"></a>Wiederherstellen eines Azure Cosmos DB-Kontos, das den fortlaufenden Sicherungsmodus verwendet
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Mithilfe des Azure Cosmos DB-Features „Zeitpunktwiederherstellung“ können Sie nach einer versehentlichen Änderung in einem Container ein gelöschtes Konto, eine Datenbank oder einen Container wiederherstellen oder aber eine Wiederherstellung in einer beliebigen Region (in der es Sicherungen gab) durchführen. Der fortlaufende Sicherungsmodus ermöglicht Ihnen die Wiederherstellung auf jeden beliebigen Zeitpunkt innerhalb der letzten 30 Tage.

In diesem Artikel wird beschrieben, wie Sie den Wiederherstellungszeitpunkt ermitteln und ein aktives oder gelöschtes Azure Cosmos DB-Konto wiederherstellen. Es wird gezeigt, wie Sie das Konto über das [Azure-Portal](#restore-account-portal), mit [PowerShell](#restore-account-powershell), der [CLI](#restore-account-cli) oder einer [Resource Manager-Vorlage](#restore-arm-template) wiederherstellen.

## <a name="restore-an-account-using-azure-portal"></a><a id="restore-account-portal"></a>Wiederherstellen eines Kontos über das Azure-Portal

### <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Wiederherstellen eines aktiven Kontos nach versehentlicher Änderung

Sie können über das Azure-Portal ein gesamtes aktives Konto oder ausgewählte Datenbanken und Container darin wiederherstellen. Führen Sie die folgenden Schritte zum Wiederherstellen Ihrer Daten aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto, und öffnen Sie das Blatt **Zeitpunktwiederherstellung**.

   > [!NOTE]
   > Das Wiederherstellungsblatt im Azure-Portal wird nur aufgefüllt, wenn Sie die Berechtigung `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` haben. Informationen zum Festlegen dieser Berechtigung finden Sie im Artikel [Backup and restore permissions](continuous-backup-restore-permissions.md) (Berechtigungen zum Sichern und Wiederherstellen).

1. Füllen Sie die folgenden Details für die Wiederherstellung aus:

   * **Wiederherstellungspunkt (UTC)**  – Ein Zeitstempel innerhalb der letzten 30 Tage. Das Konto sollte zu diesem Zeitstempel vorhanden sein. Sie können den Wiederherstellungspunkt in UTC (koordinierte Weltzeit) angeben. Der Wert kann bis zu der Sekunde gehen, zu der Sie die Wiederherstellung durchführen möchten. Wählen Sie den Link **Hier klicken** aus, um Hilfe zum [Identifizieren des Wiederherstellungspunkts](#event-feed) zu erhalten.

   * **Ort** – Die Zielregion, in der das Konto wiederhergestellt wird. Das Konto sollte in dieser Region zum angegebenen Zeitstempel vorhanden sein (z. B. „USA, Westen“ oder „USA, Osten“). Ein Konto kann nur in den Regionen wiederhergestellt werden, in denen das Quellkonto vorhanden war.

   * **Ressource wiederherstellen** – Sie können entweder **Gesamtes Konto** oder eine(n) **ausgewählte(n) Datenbank/Container** zum Wiederherstellen auswählen. Die Datenbanken und Container sollten zum angegebenen Zeitstempel vorhanden sein. Basierend auf dem ausgewählten Wiederherstellungspunkt und Ort werden Wiederherstellungsressourcen aufgefüllt. Dies ermöglicht es dem Benutzer, bestimmte Datenbanken oder Container auszuwählen, die wiederhergestellt werden müssen.

   * **Ressourcengruppe** – Die Ressourcengruppe, unter der das Zielkonto erstellt und wiederhergestellt wird. Die Ressourcengruppe muss bereits vorhanden sein.

   * **Wiederherstellungszielkonto** – Der Name des Zielkontos. Der Name des Zielkontos muss denselben Richtlinien wie beim Erstellen eines neuen Kontos folgen. Dieses Konto wird durch den Wiederherstellungsvorgang in derselben Region erstellt, in der Ihr Quellkonto vorhanden ist.
 
   :::image type="content" source="./media/restore-account-continuous-backup/restore-live-account-portal.png" alt-text="Stellen Sie ein aktives Konto nach einer versehentlichen Änderung über das Azure-Portal wieder her." border="true" lightbox="./media/restore-account-continuous-backup/restore-live-account-portal.png":::

1. Nachdem Sie die obengenannten Parameter ausgewählt haben, wählen Sie die Schaltfläche **Senden** aus, um eine Wiederherstellung zu starten. Die Wiederherstellungskosten sind eine einmalige Gebühr, die auf der Datengröße und den Kosten für den Sicherungsspeicher in der ausgewählten Region basiert. Weitere Informationen finden Sie im Abschnitt [Preise](continuous-backup-restore-introduction.md#continuous-backup-pricing).

Das Löschen des Quellkontos während der Wiederherstellung kann zu einem Fehler bei der Wiederherstellung führen.

### <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Verwenden des Ereignisfeeds zum Identifizieren der Wiederherstellungszeit

Wenn Sie beim Eingeben der Zeit für den Wiederherstellungpunkt im Azure-Portal Hilfe beim Ermitteln des Wiederherstellungspunkts benötigen, wählen Sie den Link **Hier klicken** aus, um das Blatt mit dem Ereignisfeed zu öffnen. Der Ereignisfeed bietet eine Liste mit vollständiger Genauigkeit der Erstellungs-, Ersetzungs- und Löschereignisse für Datenbanken und Container des Quellkontos. 

Wenn Sie beispielsweise auf den Zeitpunkt wiederherstellen möchten, bevor ein bestimmter Container gelöscht oder aktualisiert wurde, überprüfen Sie diesen Ereignisfeed. Ereignisse werden in chronologisch absteigender zeitlicher Reihenfolge angezeigt, in der sie aufgetreten sind, mit den aktuellen Ereignissen ganz oben. Sie können die Ergebnisse durchsuchen und die Uhrzeit vor oder nach dem Ereignis auswählen, um Ihre Uhrzeit weiter einzugrenzen.

:::image type="content" source="./media/restore-account-continuous-backup/event-feed-portal.png" alt-text="Verwenden Sie den Ereignisfeed zum Identifizieren der Zeit des Wiederherstellungspunkts." border="true" lightbox="./media/restore-account-continuous-backup/event-feed-portal.png":::

> [!NOTE]
> Änderungen an den Elementressourcen werden im Ereignisfeed nicht angezeigt. Sie können in den letzten 30 Tagen (solange es das Konto zu diesem Zeitpunkt gibt) jederzeit einen beliebigen Zeitstempel für die Wiederherstellung manuell angeben.

### <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Wiederherstellen eines gelöschten Kontos

Über das Azure-Portal können Sie ein gelöschtes Konto innerhalb von 30 Tagen nach dem Löschvorgang vollständig wiederherstellen. Führen Sie die folgenden Schritte zum Wiederherstellen eines gelöschten Kontos aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Suchen Sie in der globalen Suchleiste nach *Azure Cosmos DB*-Ressourcen. Dort werden alle Ihre vorhandenen Konten aufgelistet.
1. Wählen Sie als Nächstes die Schaltfläche **Wiederherstellen** aus. Auf dem Blatt „Wiederherstellen“ wird eine Liste der gelöschten Konten angezeigt, die innerhalb des Aufbewahrungszeitraums wiederhergestellt werden können. Dieser umfasst 30 Tage ab dem Löschzeitpunkt.
1. Wählen Sie das Konto aus, das Sie wiederherstellen möchten.

   :::image type="content" source="./media/restore-account-continuous-backup/restore-deleted-account-portal.png" alt-text="Stellen Sie ein gelöschtes Konto über das Azure-Portal wieder her." border="true" lightbox="./media/restore-account-continuous-backup/restore-deleted-account-portal.png":::

   > [!NOTE]
   > Das Wiederherstellungsblatt im Azure-Portal wird nur aufgefüllt, wenn Sie die Berechtigung `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` haben. Informationen zum Festlegen dieser Berechtigung finden Sie im Artikel [Backup and restore permissions](continuous-backup-restore-permissions.md) (Berechtigungen zum Sichern und Wiederherstellen).

1. Wählen Sie ein Konto für die Wiederherstellung aus, und geben Sie die folgenden Details zum Wiederherstellen eines gelöschten Kontos ein:

   * **Wiederherstellungspunkt (UTC)**  – Ein Zeitstempel innerhalb der letzten 30 Tage. Das Konto sollte zu diesem Zeitstempel vorhanden gewesen sein. Geben Sie den Wiederherstellungspunkt in UTC (koordinierte Weltzeit) an. Der Wert kann bis zu der Sekunde gehen, zu der Sie die Wiederherstellung durchführen möchten.

   * **Ort** – Die Zielregion, in der das Konto wiederhergestellt werden muss. Das Quellkonto sollte in dieser Region zum angegebenen Zeitstempel vorhanden sein. Beispiel: „USA, Westen“ oder „USA, Osten“.  

   * **Ressourcengruppe** – Die Ressourcengruppe, in der das Zielkonto erstellt und wiederhergestellt wird. Die Ressourcengruppe muss bereits vorhanden sein.

   * **Wiederherstellungszielkonto** – Der Name des Zielkontos muss denselben Richtlinien wie beim Erstellen eines neuen Kontos folgen. Dieses Konto wird durch den Wiederherstellungsvorgang in derselben Region erstellt, in der Ihr Quellkonto vorhanden ist.

### <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Nachverfolgen des Status eines Wiederherstellungsvorgangs

Wählen Sie nach dem Einleiten eines Wiederherstellungsvorgangs das Glockensymbol für **Benachrichtigung** in der oberen rechten Ecke des Portals aus. Daraufhin wird ein Link eingeblendet, über den der Status des gerade wiederhergestellten Kontos angezeigt wird. Während die Wiederherstellung ausgeführt wird, lautet der Status des Kontos *Wird erstellt*. Nach Abschluss des Wiederherstellungsvorgangs wird der Kontostatus in *Online* geändert.

:::image type="content" source="./media/restore-account-continuous-backup/track-restore-operation-status.png" alt-text="Der Status des wiederhergestellten Kontos wird nach Abschluss des Vorgangs von „Wird erstellt“ in „Online“ geändert." border="true" lightbox="./media/restore-account-continuous-backup/track-restore-operation-status.png":::

### <a name="get-the-restore-details-from-the-restored-account"></a>Erhalten Sie die Wiederherstellungsdetails aus dem wiederhergestellten Konto

Nach Abschluss des Wiederherstellungsvorgangs möchten Sie möglicherweise die Details des Quellkontos, von dem aus Sie die Wiederherstellung durchgeführt haben oder den Zeitpunkt der Wiederherstellung erfahren.

Gehen Sie wie folgt vor, um die Wiederherstellungsdetails aus dem Azure-Portal abzurufen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an und navigieren Sie zum wiederhergestellten Konto.

1. Navigieren Sie zum Bereich **Vorlage exportieren**. Es wird eine JSON-Vorlage geöffnet, die dem wiederhergestellten Konto entspricht.

1. Das Objekt **resources** > **properties** > **restoreParameters** (Ressourcen / Eigenschaften / Parameter wiederherstellen) enthält die Wiederherstellungsdetails. Mit **restoreTimestampInUtc** erhalten Sie den Zeitpunkt, zu dem das Konto wiederhergestellt wurde und **databasesToRestore** zeigt die spezifische Datenbank und den Container an, aus denen das Konto wiederhergestellt wurde.

## <a name="restore-an-account-using-azure-powershell"></a><a id="restore-account-powershell"></a>Wiederherstellen eines Kontos mithilfe von Azure PowerShell

Installieren Sie vor der Wiederherstellung des Kontos die [neueste Version von Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) oder eine höhere Version als 6.2.0. Stellen Sie dann eine Verbindung mit Ihrem Azure-Konto her, und wählen Sie das erforderliche Abonnement mit den folgenden Befehlen aus:

1. Melden Sie sich bei Azure mit folgendem Befehl an:

   ```azurepowershell
   Connect-AzAccount
   ```

1. Wählen Sie ein bestimmtes Abonnement mit folgendem Befehl aus:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>

### <a id="trigger-restore-ps"></a>Trigger a restore operation

The following cmdlet is an example to trigger a restore operation with the restore command by using the target account, source account, location, resource group, and timestamp:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "RestoredAccountName" `
  -SourceDatabaseAccountName "SourceDatabaseAccountName" `
  -RestoreTimestampInUtc "UTCTime" `
  -Location "AzureRegionName"

```

**Beispiel 1:** Wiederherstellen des gesamten Kontos:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Beispiel 2:** Wiederherstellen bestimmter Sammlungen und Datenbanken. In diesem Beispiel werden die Sammlungen *MyCol1*, *MyCol2* aus *MyDB1* und die gesamte Datenbank *MyDB2* wiederhergestellt, die alle Container darunter enthält.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB1" -CollectionName "MyCol1", "MyCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "SourceSql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

### <a name="get-the-restore-details-from-the-restored-account"></a>Erhalten Sie die Wiederherstellungsdetails aus dem wiederhergestellten Konto

Importieren Sie das Modul `Az.CosmosDB` und führen Sie den folgenden Befehl aus, um die Wiederherstellungsdetails abzurufen. Der restoreTimestamp befindet sich unter dem Objekt „restoreParameters“:

```azurepowershell
Get-AzCosmosDBAccount -ResourceGroupName MyResourceGroup -Name MyCosmosDBDatabaseAccount 
```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Aufzählen von wiederherstellbaren Ressourcen für die SQL-API

Mithilfe von Enumerations-Cmdlets können Sie die Ressourcen ermitteln, die für die Wiederherstellung zu verschiedenen Zeitstempeln zur Verfügung stehen. Außerdem bieten sie einen Feed von Schlüsselereignissen für die wiederherstellbaren Konto-, Datenbank- und Containerressourcen.

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>Auflisten aller Konten, die im aktuellen Abonnement wiederhergestellt werden können

Führen Sie den PowerShell-Befehl `Get-AzCosmosDBRestorableDatabaseAccount` zum Auflisten aller Konten aus, die im aktuellen Abonnement wiederhergestellt werden können.

Die Antwort enthält alle Datenbankkonten (sowohl aktive als auch gelöschte), die wiederhergestellt werden können, sowie die Regionen, aus denen sie wiederhergestellt werden können.

```json
{
    "accountName": "SampleAccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "location": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "location": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Genauso wie bei `CreationTime` oder `DeletionTime` für das Konto gibt es auch für die Region Werte für `CreationTime` und `DeletionTime`. Diese Zeiten ermöglichen Ihnen, die richtige Region und einen gültigen Zeitbereich für die Wiederherstellung in dieser Region auszuwählen.

#### <a name="list-all-the-versions-of-sql-databases-in-a-live-database-account"></a>Auflisten aller Versionen von SQL-Datenbanken in einem aktiven Datenbankkonto

Durch das Auflisten aller Versionen von Datenbanken können Sie die richtige Datenbank in einem Szenario auswählen, in dem die tatsächliche Zeit, in der die Datenbank vorhanden war, unbekannt ist.

Führen Sie den folgenden PowerShell-Befehl aus, um alle Versionen von Datenbanken aufzulisten. Dieser Befehl funktioniert nur bei aktiven Konten. Die Parameter `DatabaseAccountInstanceId` und `Location` werden aus der Antwort des Cmdlets `Get-AzCosmosDBRestorableDatabaseAccount` von den Eigenschaften `name` und `location` abgeleitet. Das `DatabaseAccountInstanceId`-Attribut verweist auf die `instanceId`-Eigenschaft des wiederhergestellten Quelldatenbankkontos:

```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -Location "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>Auflisten aller Versionen von SQL-Containern einer Datenbank in einem aktiven Datenbankkonto

Führen Sie den folgenden Befehl aus, um alle Versionen von SQL-Containern aufzulisten. Dieser Befehl funktioniert nur bei aktiven Konten. Der Parameter `DatabaseRId` ist die `ResourceId` der Datenbank, die Sie wiederherstellen möchten. Dies ist der Wert des Attributs `ownerResourceid` aus der Antwort des Cmdlets `Get-AzCosmosdbSqlRestorableDatabase`. Die Antwort enthält auch eine Liste der Vorgänge, die für alle Container in dieser Datenbank ausgeführt wurden.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"

```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>Suchen von Datenbanken oder Containern, die zu jedem beliebigen Zeitstempel wiederhergestellt werden können

Verwenden Sie den folgenden Befehl, um die Liste der Datenbanken oder Container abzurufen, die mit einem bestimmten Zeitstempel wiederhergestellt werden können. Dieser Befehl funktioniert nur bei aktiven Konten.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

### <a name="enumerate-restorable-resources-in-api-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Auflisten von wiederherstellbaren Ressourcen in API für MongoDB

Mithilfe der unten beschriebenen Enumerationsbefehle können Sie die Ressourcen ermitteln, die für verschiedene Zeitstempel wiederhergestellt werden können. Außerdem bieten sie einen Feed von Schlüsselereignissen für die wiederherstellbaren Konto-, Datenbank- und Containerressourcen. Diese Befehle funktionieren nur bei aktiven Konten und ähneln SQL-API-Befehlen, aber mit `MongoDB` statt `sql` im Befehlsnamen.

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>Auflisten aller Versionen von MongoDB-Datenbanken in einem aktiven Datenbankkonto

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US"

```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>Auflisten aller Versionen von MongoDB-Sammlungen einer Datenbank in einem aktiven Datenbankkonto

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>Auflisten aller Ressourcen eines MongoDB-Datenbankkontos, die an einem bestimmten Zeitstempel und in einer bestimmten Region wiederhergestellt werden können

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-an-account-using-azure-cli"></a><a id="restore-account-cli"></a>Wiederherstellen eines Kontos mithilfe der Azure CLI

Bevor Sie das Konto wiederherstellen, installieren Sie die Azure CLI anhand der folgenden Schritte:

1. Installieren der neuesten Version der Azure-Befehlszeilenschnittstelle

   * Installieren Sie die neueste Version der [Azure CLI](/cli/azure/install-azure-cli) oder eine höhere Version als 2.26.0.
   * Wenn Sie die Befehlszeilenschnittstelle bereits installiert haben, führen Sie den Befehl `az upgrade` aus, um ein Update auf die neueste Version auszuführen. Dieser Befehl funktioniert nur mit einer Befehlszeilenschnittstellenversion über Version 2.11. Wenn Sie über eine frühere Version verfügen, installieren Sie die neueste Version über den obigen Link.

1. Anmelden und Auswählen Ihres Abonnements

   * Melden Sie sich bei Ihrem Azure-Konto mit dem Befehl `az login` an.
   * Wählen Sie mit dem Befehl `az account set -s <subscriptionguid>` das gewünschte Abonnement aus.

### <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore-cli"></a>Auslösen eines Wiederherstellungsvorgangs über die Befehlszeilenschnittstelle

Die einfachste Möglichkeit zum Auslösen einer Wiederherstellung besteht darin, den Wiederherstellungsbefehl mit den Namen von Ziel- und Quellkonto, der Ressourcengruppe, dem Zeitstempel (UTC) und optional den Namen von Datenbank und Container auszugeben. Im Folgenden finden Sie einige Beispiele für das Auslösen des Wiederherstellungsvorgangs:

1. Erstellen Sie ein neues Azure Cosmos DB-Konto, indem Sie ein bereits vorhandenes Konto wiederherstellen.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Erstellen Sie ein neues Azure Cosmos DB-Konto, indem Sie nur ausgewählte Datenbanken und Container aus einem vorhandenen Datenbankkonto wiederherstellen.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=Collection1 Collection2 \
    --databases-to-restore name=MyDB2 collections=Collection3 Collection4

   ```

### <a name="get-the-restore-details-from-the-restored-account"></a>Erhalten Sie die Wiederherstellungsdetails aus dem wiederhergestellten Konto

Führen Sie den folgenden Befehl aus, um die Wiederherstellungsdetails zu erhalten. Der restoreTimestamp befindet sich unter dem Objekt „restoreParameters“:

```azurecli-interactive
az cosmosdb show --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup
```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Auflisten von wiederherstellbaren Ressourcen für die SQL-API

Mithilfe der unten beschriebenen Enumerationsbefehle können Sie die Ressourcen ermitteln, die für verschiedene Zeitstempel wiederhergestellt werden können. Außerdem bieten sie einen Feed von Schlüsselereignissen für die wiederherstellbaren Konto-, Datenbank- und Containerressourcen.

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>Auflisten aller Konten, die im aktuellen Abonnement wiederhergestellt werden können

Führen Sie den folgenden CLI-Befehl aus, um alle Konten aufzulisten, die im aktuellen Abonnement wiederhergestellt werden können.

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "Pitracct"
```

Die Antwort enthält alle Datenbankkonten (sowohl Live- als auch gelöschte Daten), die wiederhergestellt werden können, sowie die Regionen, aus denen sie wiederhergestellt werden können:

```json
{
    "accountName": "Pitracct",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Genauso wie bei `CreationTime` oder `DeletionTime` für das Konto gibt es auch für die Region Werte für `CreationTime` und `DeletionTime`. Diese Zeiten ermöglichen Ihnen, die richtige Region und einen gültigen Zeitbereich für die Wiederherstellung in dieser Region auszuwählen.

#### <a name="list-all-the-versions-of-databases-in-a-live-database-account"></a>Auflisten aller Versionen von Datenbanken in einem aktiven Datenbankkonto

Durch das Auflisten aller Versionen von Datenbanken können Sie die richtige Datenbank auswählen, wenn in Ihrem Szenario die tatsächliche Zeit, in der die Datenbank vorhanden war, unbekannt ist.

Führen Sie den folgenden CLI-Befehl aus, um alle Versionen von Datenbanken aufzulisten. Dieser Befehl funktioniert nur bei aktiven Konten. Die Parameter `instance-id` und `location` werden aus der Antwort des Befehls `az cosmosdb restorable-database-account list` von den Eigenschaften `name` und `location` abgeleitet. Das instanceId-Attribut ist auch eine Eigenschaft des Quelldatenbankkontos, das wiederhergestellt wird:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Die Befehlsausgabe zeigt nun an, wann eine Datenbank erstellt und gelöscht wurde.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>Auflisten aller Versionen von SQL-Containern einer Datenbank in einem aktiven Datenbankkonto

Führen Sie den folgenden Befehl aus, um alle Versionen von SQL-Containern aufzulisten. Dieser Befehl funktioniert nur bei aktiven Konten. Der Parameter `database-rid` ist die `ResourceId` der Datenbank, die Sie wiederherstellen möchten. Dies ist der Wert des Attributs `ownerResourceid` aus der Antwort des Befehls `az cosmosdb sql restorable-database list`.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Die Befehlsausgabe enthält die Liste der Vorgänge, die für alle Container in dieser Datenbank ausgeführt wurden:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>Suchen von Datenbanken oder Containern, die für einen beliebigen Zeitstempel wiederhergestellt werden können

Verwenden Sie den folgenden Befehl, um die Liste der Datenbanken oder Container abzurufen, die mit einem bestimmten Zeitstempel wiederhergestellt werden können. Dieser Befehl funktioniert nur bei aktiven Konten.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

### <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Auflisten von wiederherstellbaren Ressourcen für ein MongoDB-API-Konto

Mithilfe der unten beschriebenen Enumerationsbefehle können Sie die Ressourcen ermitteln, die für verschiedene Zeitstempel wiederhergestellt werden können. Außerdem bieten sie einen Feed von Schlüsselereignissen für die wiederherstellbaren Konto-, Datenbank- und Containerressourcen. Diese Befehle funktionieren nur für aktive Konten.

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>Auflisten aller Versionen von MongoDB-Datenbanken in einem aktiven Datenbankkonto

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>Auflisten aller Versionen von MongoDB-Sammlungen einer Datenbank in einem aktiven Datenbankkonto

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>Auflisten aller Ressourcen eines MongoDB-Datenbankkontos, die an einem bestimmten Zeitstempel und in einer bestimmten Region wiederhergestellt werden können

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore-arm-template"></a>Wiederherstellen mithilfe der Resource Manager-Vorlage

Sie können ein Konto auch mithilfe der Resource Manager-Vorlage wiederherstellen. Beziehen Sie beim Definieren der Vorlage die folgenden Parameter mit ein:

* Festlegen des `createMode`-Parameters auf *Restore*
* Definieren Sie `restoreParameters`, und beachten Sie, dass der Wert `restoreSource` aus der Ausgabe des Befehls `az cosmosdb restorable-database-account list` für Ihr Quellkonto extrahiert wird. Das Attribut „Instance ID“ (Instanz-ID) für Ihren Kontonamen wird für die Wiederherstellung verwendet.
* Legen Sie den `restoreMode`-Parameter auf *PointInTime* fest, und konfigurieren Sie den `restoreTimestampInUtc`-Wert.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Stellen Sie als Nächstes die Vorlage mithilfe von Azure PowerShell oder der CLI bereit. Im folgenden Beispiel wird gezeigt, wie die Vorlage mit einem CLI-Befehl bereitgestellt wird:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Nächste Schritte

* Bereitstellen der fortlaufenden Sicherung über das [Azure-Portal](provision-account-continuous-backup.md#provision-portal), mit [PowerShell](provision-account-continuous-backup.md#provision-powershell), der [CLI](provision-account-continuous-backup.md#provision-cli) oder [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template)
* [Migrieren eines Kontos von der regelmäßigen Sicherung zur fortlaufenden Sicherung](migrate-continuous-backup.md)
* [Ressourcenmodell des fortlaufenden Sicherungsmodus](continuous-backup-restore-resource-model.md)
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.
