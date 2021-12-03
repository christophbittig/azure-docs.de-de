---
title: Verwalten von Datenbankkonten in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Azure Cosmos DB Ressourcen mit dem Azure-Portal, PowerShell, der CLI und Azure Resource Manager-Vorlagen verwalten.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: 6046468281878f8ae6b9ac9f802bb16fd378c616
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434140"
---
# <a name="manage-an-azure-cosmos-account-using-the-azure-portal"></a>Erstellen eines Azure Cosmos DB-Kontos über das Azure-Portal

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In diesem Artikel wird beschrieben, wie mit dem Azure-Portal verschiedene Aufgaben in einem Azure Cosmos-Konto verwaltet werden. 

> [!TIP]
> Azure Cosmos DB kann auch mit anderen Azure-Verwaltungsclients verwaltet werden, einschließlich [Azure PowerShell](manage-with-powershell.md), [Azure CLI](sql/manage-with-cli.md), [Azure Resource Manager-Vorlagen](./manage-with-templates.md)und [Bicep](sql/manage-with-bicep.md).

## <a name="create-an-account"></a>Erstellen eines Kontos

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount.md)]

## <a name="addremove-regions-from-your-database-account"></a>Hinzufügen/Entfernen von Regionen für Ihr Datenbankkonto

> [!TIP]
> Wenn eine neue Region hinzugefügt wird, müssen alle Daten vollständig repliziert und in die neue Region committet werden, bevor die Region als verfügbar markiert wird. Wie lange dieser Vorgang dauert, hängt davon ab, wie viele Daten in dem Konto gespeichert werden. Wenn ein [asynchroner Durchsatzskalierungsvorgang](scaling-provisioned-throughput-best-practices.md#background-on-scaling-rus) ausgeführt wird, wird der Vorgang zum horizontalen Hochskalieren des Durchsatzes angehalten und automatisch fortgesetzt, sobald der Vorgang zum Hinzufügen/Entfernen von Regionen abgeschlossen ist. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.

1. Navigieren Sie zu Ihrem Azure Cosmos-Konto, und öffnen Sie das Menü **Daten global replizieren**.

1. Wenn Sie Regionen hinzufügen möchten, wählen Sie auf der Karte die Sechsecke mit der Beschriftung **+** aus, die den gewünschten Regionen entsprechen. Alternativ können Sie zum Hinzufügen einer Region die Option **+ Region hinzufügen** und anschließend eine Region aus dem Dropdownmenü auswählen.

1. Wenn Sie Regionen entfernen möchten, entfernen Sie sie von der Karte, indem Sie die blauen, mit einem Häkchen versehenen Sechsecke auswählen. Alternativ können Sie auch neben der Region auf der rechten Seite das Papierkorbsymbol (🗑) auswählen.

1. Wählen Sie zum Speichern der Änderungen **OK** aus.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Menü zum Hinzufügen oder Entfernen von Regionen":::

Im Schreibmodus mit einer einzelnen Region kann die Schreibregion nicht entfernt werden. In diesem Fall muss erst ein Failover auf eine andere Region durchgeführt werden, um die aktuelle Schreibregion löschen zu können.

Im Schreibmodus mit mehreren Regionen können Sie beliebige Regionen hinzufügen oder entfernen, solange noch mindestens eine Region vorhanden ist.

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Konfigurieren mehrerer Schreibregionen

Öffnen Sie die Registerkarte **Daten global replizieren**, und wählen Sie **Aktivieren** aus, um Schreibvorgänge für mehrere Regionen zu aktivieren. Nachdem Sie Schreibvorgänge für mehrere Regionen aktiviert haben, werden alle Leseregionen, die aktuell in Ihrem Konto konfiguriert sind, zu Lese- und Schreibregionen.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Screenshot eines Azure Cosmos-Kontos mit Schreibvorgängen in mehreren Regionen":::

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Aktivieren des automatischen Failovers für Ihr Azure Cosmos-Konto

Die Option für automatisches Failover ermöglicht Azure Cosmos DB das Failover auf die Region mit der höchsten Failoverpriorität ohne Benutzereingriff, sollte bei einer Region die Nichtverfügbarkeit eintreten. Wenn automatisches Failover aktiviert ist, kann die Regionspriorität geändert werden. Das Konto muss über zwei oder mehr Regionen verfügen, um automatisches Failover zu aktivieren.

1. Öffnen Sie in Ihrem Azure Cosmos-Konto den Bereich **Daten global replizieren**.

2. Wählen Sie im oberen Bereich die Option **Automatisches Failover** aus.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menü „Daten global replizieren“":::

3. Vergewissern Sie sich im Bereich **Automatisches Failover**, dass **Automatisches Failover aktivieren** auf **EIN** festgelegt ist. 

4. Wählen Sie **Speichern** aus.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Portalmenü für automatisches Failover":::

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Festlegen von Failoverprioritäten für Ihr Azure Cosmos-Konto

Nachdem ein Cosmos-Konto für automatisches Failover konfiguriert wurde, kann die Failoverpriorität für Regionen geändert werden.

> [!IMPORTANT]
> Die Schreibregion (Failoverpriorität null) kann nicht geändert werden, wenn das Konto für automatisches Failover konfiguriert ist. Um die Schreibregion zu ändern, müssen Sie das automatische Failover deaktivieren und ein manuelles Failover ausführen.

1. Öffnen Sie in Ihrem Azure Cosmos-Konto den Bereich **Daten global replizieren**.

2. Wählen Sie im oberen Bereich die Option **Automatisches Failover** aus.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menü „Daten global replizieren“":::

3. Vergewissern Sie sich im Bereich **Automatisches Failover**, dass **Automatisches Failover aktivieren** auf **EIN** festgelegt ist.

4. Ziehen Sie zum Ändern der Failoverpriorität die Leseregionen mithilfe der drei Punkte, die auf der linken Seite der Zeile angezeigt werden, wenn Sie darauf zeigen.

5. Wählen Sie **Speichern** aus.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Portalmenü für automatisches Failover":::

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Ausführen eines manuellen Failovers für ein Azure Cosmos-Konto

> [!IMPORTANT]
> Damit dieser Vorgang erfolgreich ausgeführt wird, muss das Azure Cosmos-Konto für manuelles Failover konfiguriert sein.

> [!NOTE]
> Wenn Sie einen manuellen Failovervorgang ausführen, während ein [asynchroner Durchsatzskalierungsvorgang](scaling-provisioned-throughput-best-practices.md#background-on-scaling-rus) aktiv ist, wird der Vorgang zum horizontalen Hochskalieren des Durchsatzes angehalten. Er wird automatisch fortgesetzt, sobald der Failovervorgang abgeschlossen ist.

1. Navigieren Sie zu Ihrem Azure Cosmos-Konto, und öffnen Sie das Menü **Daten global replizieren**.

2. Wählen Sie im oberen Bereich des Menüs die Option **Manuelles Failover** aus.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menü „Daten global replizieren“":::

3. Wählen Sie im Menü **Manuelles Failover** Ihre neue Schreibregion aus. Aktivieren Sie das Kontrollkästchen, um anzugeben, dass Sie wissen, dass diese Option Ihre Schreibregion ändert.

4. Wählen Sie **OK** aus, um das Failover zu starten.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Portalmenü für manuelles Failover":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen und Beispiele zum Verwalten von Azure-Cosmos-Konto sowie Datenbank und Containern finden Sie in den folgenden Artikeln:

* [Verwalten von Azure Cosmos DB mithilfe von Azure PowerShell](manage-with-powershell.md)
* [Verwalten von Azure Cosmos DB mithilfe der Azure-Befehlszeilenschnittstelle](sql/manage-with-cli.md)
* [Erstellen und Verwalten von Azure Cosmos DB mithilfe von Azure Resource Manager-Vorlagen](./manage-with-templates.md)
* [Verwalten von Azure Cosmos DB mithilfe von Bicep](sql/manage-with-bicep.md)
