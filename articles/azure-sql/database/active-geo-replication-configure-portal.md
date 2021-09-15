---
title: 'Tutorial: Georeplikation und Failover im Portal'
description: Erfahren Sie, wie Sie die Georeplikation für eine SQL-Datenbank mithilfe des Azure-Portals oder der Azure CLI konfigurieren und ein Failover initiieren.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: BustosMSFT
ms.author: robustos
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: e94b9cb0628fca112f3bb9181035d6d60bbd7b74
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866742"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-azure-sql-database"></a>Tutorial: Konfigurieren der aktiven Georeplikation und des Failovers (Azure SQL-Datenbank)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Artikel wird veranschaulicht, wie Sie die [aktive Georeplikation für Azure SQL-Datenbank](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) über das [Azure-Portal](https://portal.azure.com) oder mithilfe der Azure CLI konfigurieren und ein Failover initiieren.

Bewährte Methoden für die Verwendung von Autofailover-Gruppen finden Sie unter [Bewährte Methoden für Azure SQL-Datenbank](auto-failover-group-overview.md#best-practices-for-sql-database) und [Bewährte Methoden für verwaltete Azure SQL-Instanz](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie die aktive Georeplikation über das Azure-Portal konfigurieren möchten, benötigen Sie folgende Ressource:

* Eine Datenbank in Azure SQL-Datenbank: Die primäre Datenbank, die in eine andere geografische Region repliziert werden soll.

> [!Note]
> Wenn Sie das Azure-Portal verwenden, können Sie nur eine sekundäre Datenbank im selben Abonnement wie die primäre Datenbank erstellen. Wenn eine sekundäre Datenbank in einem anderen Abonnement vorhanden sein muss, verwenden Sie die [REST-API zum Erstellen der Datenbank](/rest/api/sql/databases/createorupdate) oder die [ALTER DATABASE-Transact-SQL-API](/sql/t-sql/statements/alter-database-transact-sql).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Für die Konfiguration der aktiven Georeplikation benötigen Sie eine Datenbank in Azure SQL-Datenbank. Es ist die primäre Datenbank, die in eine andere geografische Region repliziert werden soll.

Bereiten Sie die Umgebung für die Azure CLI vor.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="add-a-secondary-database"></a>Hinzufügen einer sekundären Datenbank

Mit den folgenden Schritten wird eine neue sekundäre Datenbank in einer Partnerschaft für die Georeplikation erstellt.  

Zum Hinzufügen einer sekundären Datenbank müssen Sie der Besitzer oder Mitbesitzer des Abonnements sein.

Die sekundäre Datenbank hat den gleichen Namen wie die primäre Datenbank und standardmäßig auch die gleiche Dienstebene und Computegröße. Die sekundäre Datenbank kann eine Einzel- oder Pooldatenbank sein. Weitere Informationen finden Sie unter [DTU-basiertes Kaufmodell](service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](service-tiers-vcore.md).
Nachdem die sekundäre Datenbank erstellt und das Seeding ausgeführt wurde, beginnt die Replikation der Daten von der primären Datenbank in die neue sekundäre Datenbank.

> [!NOTE]
> Wenn die Partnerdatenbank bereits vorhanden ist (z. B. aufgrund der Beendigung einer vorherigen Georeplikationsbeziehung), tritt für den Befehl ein Fehler auf.

# <a name="portal"></a>[Portal](#tab/portal)

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Datenbank, die Sie für die Georeplikation einrichten möchten.
2. Wählen Sie auf der Seite SQL-Datenbank Ihre Datenbank aus, scrollen Sie zu **Datenverwaltung**, wählen Sie **Replikate** und dann **Replikat erstellen** aus.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-cli-create-geo-replica.png" alt-text="Konfigurieren der Georeplikation":::

3. Wählen oder erstellen Sie den Server für die sekundäre Datenbank und konfigurieren Sie bei Bedarf die **Compute und Speicher**-Optionen. Sie können eine beliebige Region für Ihren sekundären Server auswählen, wir empfehlen jedoch das [Regionspaar](../../best-practices-availability-paired-regions.md).

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-create-and-configure-replica.png" alt-text="{alt-text}":::

    Optional können Sie einem Pool für elastische Datenbanken eine sekundäre Datenbank hinzufügen. Um die sekundäre Datenbank in einem Pool zu erstellen, wählen Sie **Ja** neben der Option **Möchten Sie einen Pool für elastische SQL-Datenbanken verwenden?** und dann einen Pool auf dem Zielserver aus. Ein Pool muss bereits auf dem Zielserver vorhanden sein. Dieser Workflow erstellt keinen Pool.

4. Klicken Sie auf **Überprüfen und erstellen**, überprüfen Sie die Informationen, und klicken Sie dann auf **Erstellen**.
5. Die sekundäre Datenbank wird erstellt und der Bereitstellungsprozess beginnt.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-geo-replica-deployment.png" alt-text="Screenshot: Bereitstellungsstatus der sekundären Datenbank":::

6. Nach Abschluss der Bereitstellung wird der Status für die sekundäre Datenbank angezeigt.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-sql-database-secondary-status.png" alt-text="Screenshot: Status der sekundären Datenbank nach der Bereitstellung":::

7. Kehren Sie zur Seite der primären Datenbank zurück, und wählen Sie **Replikate** aus. Ihre sekundäre Datenbank ist unter **Georeplikate** aufgeführt.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-sql-db-geo-replica-list.png" alt-text="Screenshot: Primäre Replikate und Georeplikate der SQL-Datenbank":::

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wählen Sie die Datenbank aus, die Sie für die Georeplikation einrichten möchten. Sie benötigen die folgenden Informationen:
- Ihren ursprünglichen Azure SQL-Datenbanknamen
- Den Azure SQL-Servernamen
- Ihren Ressourcengruppennamen
- Den Namen des Servers, auf dem das neue Replikat erstellt werden soll

> [!NOTE]
> Die sekundäre Datenbank muss dieselbe Dienstebene wie die primäre Datenbank aufweisen.

Sie können eine beliebige Region für Ihren sekundären Server auswählen, wir empfehlen jedoch das [Regionspaar](../../best-practices-availability-paired-regions.md).

Führen Sie den Befehl [az sql db replica create](/cli/azure/sql/db/replica#az_sql_db_replica_create) aus.

```azurecli
az sql db replica create --resource-group ContosoHotel --server contosoeast --name guestlist --partner-server contosowest --family Gen5 --capacity 2 --secondary-type Geo
```

Optional können Sie einem Pool für elastische Datenbanken eine sekundäre Datenbank hinzufügen. Verwenden Sie den Parameter `--elastic-pool`, um die sekundäre Datenbank in einem Pool zu erstellen. Ein Pool muss bereits auf dem Zielserver vorhanden sein. Dieser Workflow erstellt keinen Pool.

Die sekundäre Datenbank wird erstellt und der Bereitstellungsprozess beginnt.

Wenn die Bereitstellung abgeschlossen ist, können Sie den Status der sekundären Datenbank überprüfen, indem Sie den Befehl [az sql db replica list-links](/cli/azure/sql/db/replica#az_sql_db_replica_list-links) ausführen:
    
```azurecli
az sql db replica list-links --name guestlist --resource-group ContosoHotel --server contosowest
```

---

## <a name="initiate-a-failover"></a>Initiieren eines Failovers

Für die sekundäre Datenbank kann ein Wechsel durchgeführt werden, bei dem sie zur primären Datenbank wird.

# <a name="portal"></a>[Portal](#tab/portal)  

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur primären Datenbank in der Georeplikationspartnerschaft.
2. Scrollen Sie zu **Datenverwaltung**, und wählen Sie dann **Replikate** aus.
3. Wählen Sie in der Liste **Georeplikate** die Datenbank aus, die die neue primäre Datenbank werden soll. Wählen Sie dann die Auslassungspunkte und anschließend **Erzwungenes Failover** aus.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-forced-failover.png" alt-text="Screenshot: Auswählen des erzwungenen Failovers in der Dropdownliste":::
4. Wählen Sie **Ja** aus, um das Failover zu beginnen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie den Befehl [az sql db replica set-primary](/cli/azure/sql/db/replica#az_sql_db_replica_set-primary) aus.

```azurecli
az sql db replica set-primary --name guestlist --resource-group ContosoHotel --server contosowest
```

---

Durch den Befehl wird die sekundäre Datenbank sofort in die primäre Rolle geändert. Dieser Vorgang sollte normalerweise innerhalb von 30 Sekunden oder weniger abgeschlossen sein.

Es gibt einen kurzer Zeitraum, in dem beide Datenbanken während des Rollenwechsels (ca. 0 bis 25 Sekunden) nicht verfügbar sind. Wenn die primäre Datenbank über mehrere sekundäre Datenbanken verfügt, werden die anderen sekundären Datenbanken durch den Befehl automatisch neu konfiguriert, sodass sie eine Verbindung mit der neuen primären Datenbank herstellen. Unter normalen Umständen dauert der gesamte Vorgang nicht länger als 1 Minute.

> [!NOTE]
> Dieser Befehl ist für die schnelle Wiederherstellung der Datenbank bei einem Ausfall bestimmt. Er löst ein Failover ohne Datensynchronisierung oder ein erzwungenes Failover aus.  Wenn die primäre Datenbank bei Ausgabe des Befehls online ist und gerade Commits für Transaktionen ausführt, können einige Daten verloren gehen.

## <a name="remove-secondary-database"></a>Entfernen einer sekundären Datenbank

Dieser Vorgang beendet die Replikation zur sekundären Datenbank dauerhaft und ändert die Rolle der sekundären Datenbank in eine normale Datenbank mit Lese-/Schreibzugriff. Wenn die Verbindung mit der sekundären Datenbank unterbrochen wird, ist der Befehl zwar erfolgreich, aber die sekundäre Datenbank wird erst mit Lese-/ Schreibzugriff versehen, nachdem die Verbindung wiederhergestellt wurde.

# <a name="portal"></a>[Portal](#tab/portal)  

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur primären Datenbank in der Georeplikationspartnerschaft.
2. Wählen Sie **Replikate** aus.
3. Wählen Sie in der Liste **Georeplikate** die Datenbank aus, die Sie aus der Georeplikationspartnerschaft entfernen möchten, klicken Sie auf die Auslassungspunkte und dann auf **Replikation beenden**.

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-stop-replication.png" alt-text="Screenshot: Auswählen von „Replikation beenden“ in der Dropdownliste":::
5. Ein Bestätigungsfenster wird geöffnet. Klicken Sie zum Entfernen der Datenbank aus der Georeplikationspartnerschaft auf **Ja**. (Setzen Sie sie auf eine Datenbank mit Lese-/ Schreibzugriff fest, die kein Teil der Replikation ist.)
 
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie den Befehl [az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete-link) aus.

```azurecli
az sql db replica delete-link --name guestlist --resource-group ContosoHotel --server contosoeast --partner-server contosowest
```

Bestätigen Sie, dass Sie den Vorgang durchführen möchten.

---

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur aktiven Georeplikation finden Sie unter [Aktive Georeplikation](active-geo-replication-overview.md).
* Informationen zu Autofailover-Gruppen finden Sie unter [Autofailover-Gruppen](auto-failover-group-overview.md).
* Eine Übersicht und verschiedene Szenarien zum Thema Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md).