---
title: Bereitstellen eines Kontos mit fortlaufender Sicherung und Zeitpunktwiederherstellung in Azure Cosmos DB
description: Erfahren Sie, wie Sie ein Konto mit fortlaufender Sicherung und Zeitpunktwiederherstellung über das Azure-Portal, mit PowerShell, der CLI und Resource Manager-Vorlagen bereitstellen.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78996e58111d380778e8d02673f518720250e6fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355958"
---
# <a name="provision-an-azure-cosmos-db-account-with-continuous-backup-and-point-in-time-restore"></a>Bereitstellen eines Azure Cosmos DB-Kontos mit fortlaufender Sicherung und Zeitpunktwiederherstellung 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Mithilfe des Azure Cosmos DB-Features „Zeitpunktwiederherstellung“ können Sie nach einer versehentlichen Änderung in einem Container ein gelöschtes Konto, eine Datenbank oder einen Container wiederherstellen oder aber eine Wiederherstellung in einer beliebigen Region (in der es Sicherungen gab) durchführen. Der fortlaufende Sicherungsmodus ermöglicht Ihnen die Wiederherstellung auf jeden beliebigen Zeitpunkt innerhalb der letzten 30 Tage.

In diesem Artikel wird beschrieben, wie Sie ein Konto mit fortlaufender Sicherung und Zeitpunktwiederherstellung über das [Azure-Portal](#provision-portal), mit [PowerShell](#provision-powershell), der [CLI](#provision-cli) und [Resource Manager-Vorlagen](#provision-arm-template) bereitstellen.

> [!NOTE]
> Sie können ein Konto mit fortlaufenden Sicherungsmodus nur bereitstellen, wenn die folgenden Bedingungen zutreffen:
>
> * Das Konto ist vom Typ SQL-API oder API für MongoDB.
> * Das Konto verfügt über eine einzelne Schreibregion.
> * Das Konto ist nicht mit kundenseitig verwalteten Schlüsseln (Customer Managed Keys, CMK) aktiviert.

## <a name="provision-using-azure-portal"></a><a id="provision-portal"></a>Bereitstellen über das Azure-Portal

Wählen Sie beim Erstellen eines neuen Azure Cosmos DB-Kontos auf der Registerkarte **Sicherungsrichtlinie** den Modus **Fortlaufend** aus, um die Funktion „Zeitpunktwiederherstellung“ für das neue Konto zu aktivieren. Bei der Zeitpunktwiederherstellung werden Daten in einem neuen Konto wiederhergestellt. Zurzeit können Sie keine Daten in einem vorhandenen Konto wiederherstellen.

:::image type="content" source="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png" alt-text="Bereitstellen eines Azure Cosmos DB-Kontos mit fortlaufender Sicherung." border="true" lightbox="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png":::

## <a name="provision-using-azure-powershell"></a><a id="provision-powershell"></a>Bereitstellen mithilfe von Azure PowerShell

Installieren Sie vor der Bereitstellung des Kontos die [neueste Version von Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) oder eine höhere Version als 6.2.0. Stellen Sie dann eine Verbindung mit Ihrem Azure-Konto her, und wählen Sie das erforderliche Abonnement mit den folgenden Befehlen aus:

1. Melden Sie sich bei Azure mit folgendem Befehl an:

   ```azurepowershell
   Connect-AzAccount
   ```

1. Wählen Sie ein bestimmtes Abonnement mit folgendem Befehl aus:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

#### <a name="sql-api-account"></a><a id="provision-powershell-sql-api"></a>SQL-API-Konto

Fügen Sie zum Bereitstellen eines Kontos mit fortlaufender Sicherung das Argument `-BackupPolicyType Continuous` zusammen mit dem regulären Bereitstellungsbefehl hinzu.

Das folgende Cmdlet ist ein Beispiel für das Konto *Pitracct* mit einer einzelnen Schreibregion und einer Richtlinie für die fortlaufende Sicherung, das in der Region *USA, Westen* unter der Ressourcengruppe *MyRG* erstellt wurde:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct" `
  -ApiKind "Sql"
      
```

#### <a name="api-for-mongodb"></a><a id="provision-powershell-mongodb-api"></a>API für MongoDB

Das folgende Cmdlet ist ein Beispiel für das Konto *Pitracct* für die fortlaufende Sicherung, das in der Region *USA, Westen* unter der Ressourcengruppe *MyRG* erstellt wurde:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "Pitracct" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="provision-using-azure-cli"></a><a id="provision-cli"></a>Bereitstellen mithilfe der Azure CLI

Bevor Sie das Konto bereitstellen, installieren Sie die Azure CLI anhand der folgenden Schritte:

1. Installieren der neuesten Version der Azure-Befehlszeilenschnittstelle

   * Installieren Sie die neueste Version der [Azure CLI](/cli/azure/install-azure-cli) oder eine höhere Version als 2.26.0.
   * Wenn Sie die Befehlszeilenschnittstelle bereits installiert haben, führen Sie den Befehl `az upgrade` aus, um ein Update auf die neueste Version auszuführen. Dieser Befehl funktioniert nur mit einer Befehlszeilenschnittstellenversion über Version 2.11. Wenn Sie über eine frühere Version verfügen, installieren Sie die neueste Version über den obigen Link.

1. Anmelden und Auswählen Ihres Abonnements

   * Melden Sie sich bei Ihrem Azure-Konto mit dem Befehl `az login` an.
   * Wählen Sie mit dem Befehl `az account set -s <subscriptionguid>` das gewünschte Abonnement aus.

### <a name="sql-api-account"></a><a id="provision-cli-sql-api"></a>SQL-API-Konto

Zum Bereitstellen eines SQL-API-Kontos mit fortlaufender Sicherung sollte zusammen mit dem regulären Bereitstellungsbefehl das zusätzliche Argument `--backup-policy-type Continuous` übergeben werden. Der folgende Befehl ist ein Beispiel für ein Konto namens *Pitracct* mit einer einzelnen Schreibregion und einer Richtlinie für die fortlaufende Sicherung, das in der Region *USA, Westen* unter der Ressourcengruppe *MyRG* erstellt wurde:

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --resource-group MyRG \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

### <a name="api-for-mongodb"></a><a id="provision-cli-mongo-api"></a>API für MongoDB

Der folgende Befehl ist ein Beispiel für ein Konto namens *Pitracct* mit einer einzelnen Schreibregion und einer Richtlinie für die fortlaufende Sicherung, das in der Region *USA, Westen* unter der Ressourcengruppe *MyRG* erstellt wurde:

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --kind MongoDB \
  --resource-group MyRG \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-using-resource-manager-template"></a><a id="provision-arm-template"></a>Bereitstellen mithilfe einer Resource Manager-Vorlage

Sie können mithilfe von Azure Resource Manager-Vorlagen ein Azure Cosmos DB-Konto im kontinuierlichen Modus bereitstellen. Wenn Sie die Vorlage für die Bereitstellung eines Kontos definieren, beziehen Sie den `backupPolicy`-Parameter mit ein, wie im folgenden Beispiel gezeigt:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Stellen Sie als Nächstes die Vorlage mithilfe von Azure PowerShell oder der CLI bereit. Im folgenden Beispiel wird gezeigt, wie die Vorlage mit einem CLI-Befehl bereitgestellt wird:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="next-steps"></a>Nächste Schritte

* [Wiederherstellen eines aktiven oder gelöschten Azure Cosmos DB-Kontos](restore-account-continuous-backup.md)
* [Migrieren eines Kontos von der regelmäßigen Sicherung zur fortlaufenden Sicherung](migrate-continuous-backup.md)
* [Ressourcenmodell des fortlaufenden Sicherungsmodus](continuous-backup-restore-resource-model.md)
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.