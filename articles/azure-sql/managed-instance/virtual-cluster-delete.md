---
title: Löschen eines Subnetzes nach dem Löschen von SQL Managed Instance
description: Hier erfahren Sie, wie Sie nach dem Löschen einer Instanz von Azure SQL Managed Instance auch ein virtuelles Azure-Netzwerk löschen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: d61a3f5a3dac8cfb6215bdfd6ff1c457c4e14150
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214446"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Löschen eines Subnetzes nach dem Löschen einer Instanz von Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dieser Artikel enthält Richtlinien zum manuellen Löschen eines Subnetzes nach dem Löschen der letzten darin enthaltenen Instanz von Azure SQL Managed Instance.

SQL Managed Instances werden in [virtuellen Clustern](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) bereitgestellt. Jeder virtuelle Cluster ist mit einem Subnetz verknüpft und wird zusammen mit der Erstellung der ersten Instanz bereitgestellt. Auf die gleiche Weise wird ein virtueller Cluster automatisch zusammen mit der Löschung der letzten Instanz entfernt, so dass das Subnetz leer und bereit zum Entfernen ist. Es ist keine manuelle Aktion auf dem virtuellen Cluster erforderlich, um das Subnetz freizugeben. Nachdem der letzte virtuelle Cluster gelöscht wurde, können Sie das Subnetz löschen

In seltenen Fällen kann der Erstellungsvorgang fehlschlagen und zu einer Bereitstellung eines leeren virtuellen Clusters führen. Außerdem, da die Erstellung von Instanzen[abgebrochen werden kann](management-operations-cancel.md), ist es möglich, dass ein virtueller Cluster mit darin befindlichen Instanzen in einem fehlgeschlagenen Zustand bereitgestellt wird. Die Entfernung des virtuellen Clusters wird in diesen Fällen automatisch veranlasst und im Hintergrund entfernt.

> [!NOTE]
> Für die Aufbewahrung eines leeren virtuellen Clusters oder von Instanzen, die nicht erstellt werden konnten, fallen keine Kosten an.

> [!IMPORTANT]
> - Damit der Löschvorgang erfolgreich ist, sollte der virtuelle Cluster keine SQL Managed Instances enthalten. Dies gilt nicht für Instanzen, deren Erstellung fehlgeschlagen ist. 
> - Das Löschen eines virtuellen Clusters ist ein zeitintensiver Vorgang mit einer Dauer von etwa 1,5 Stunden. (Aktuelle Angaben zur Löschdauer für virtuelle Cluster finden Sie unter [SQL Managed Instance-Verwaltungsvorgänge](management-operations-overview.md).) Während dieser Zeit ist der virtuelle Cluster im Portal weiterhin sichtbar, bis der Vorgang abgeschlossen wurde.
> - Es kann nur ein Löschvorgang für den virtuellen Cluster ausgeführt werden. Alle nachfolgenden vom Kunden initiierten Löschanforderungen führen zu einem Fehler, da der Löschvorgang bereits ausgeführt wird.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Löschen eines virtuellen Clusters aus dem Azure-Portal

> [!IMPORTANT]
> Ab dem 1. September 2021. werden alle virtuellen Cluster automatisch entfernt, wenn die letzte Instanz des Clusters gelöscht wurde. Eine manuelle Entfernung des virtuellen Clusters ist nicht mehr erforderlich.

Suchen Sie nach den Ressourcen eines virtuellen Clusters, um diesen über das Azure-Portal zu löschen.

> [!div class="mx-imgBorder"]
> ![Screenshot: Azure-Portal mit hervorgehobenem Suchfeld](./media/virtual-cluster-delete/virtual-clusters-search.png)

Nachdem Sie den virtuellen Cluster gefunden haben, den Sie löschen möchten, wählen Sie diese Ressource aus, und klicken Sie auf **Löschen**. Sie werden aufgefordert, das Löschen des virtuellen Clusters zu bestätigen.

> [!div class="mx-imgBorder"]
> ![Screenshot: Dashboard „Virtuelle Cluster“ im Azure-Portal mit hervorgehobener Option „Löschen“](./media/virtual-cluster-delete/virtual-clusters-delete.png)

In den Azure-Portalbenachrichtigungen wird eine Bestätigung dafür angezeigt, dass die Anforderung zum Löschen des virtuellen Clusters erfolgreich übermittelt wurde. Der eigentliche Löschvorgang dauert etwa 1,5 Stunden. Während dieser Zeit ist der virtuelle Cluster im Portal weiterhin sichtbar. Nach Abschluss des Vorgangs ist der virtuelle Cluster nicht mehr sichtbar, und das zugehörige Subnetz wird zur erneuten Verwendung freigegeben.

> [!TIP]
> Wenn im virtuellen Cluster keine SQL Managed Instances angezeigt werden und Sie den virtuellen Cluster nicht löschen können, vergewissern Sie sich, dass kein Instanzenbereitstellungsvorgang mehr ausgeführt wird. Dies umfasst gestartete und abgebrochene Bereitstellungen, die noch nicht abgeschlossen sind. Der Grund: Diese Vorgänge verwenden weiterhin den virtuellen Cluster, was dazu führt, dass er nicht gelöscht werden kann. Sehen Sie auf der Registerkarte **Bereitstellungen** der Ressourcengruppe nach, in der die Instanz bereitgestellt wurde, um festzustellen, ob eine Bereitstellung im Gange ist. Warten Sie in diesem Fall, bis die Bereitstellung abgeschlossen ist, und löschen Sie dann die SQL Managed Instance. Der virtuelle Cluster wird im Rahmen der Entfernung der Instanz synchron gelöscht.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Löschen eines virtuellen Clusters mithilfe der API

Verwenden Sie die in der [Methode zum Löschen virtueller Cluster](/rest/api/sql/virtualclusters/delete) angegebenen URI-Parameter, um einen virtuellen Cluster mithilfe der API zu löschen.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Was ist Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Machen Sie sich mit der [Verbindungsarchitektur in SQL Managed Instance](connectivity-architecture-overview.md) vertraut.
- Unter [Ändern eines vorhandenen virtuellen Netzwerks für SQL Managed Instance](vnet-existing-add-subnet.md) erfahren Sie, wie Sie Änderungen vornehmen können.
- Ein Tutorial zum Erstellen eines virtuellen Netzwerks, dem Erstellen von Azure SQL Managed Instance und dem Wiederherstellen einer Datenbank aus einer Datenbanksicherung finden Sie unter [Erstellen einer Instanz von Azure SQL Managed Instance (Portal)](instance-create-quickstart.md).
- Informationen zu Problemen im Zusammenhang mit dem DNS finden Sie unter [Konfigurieren eines benutzerdefinierten DNS](custom-dns-configure.md).
