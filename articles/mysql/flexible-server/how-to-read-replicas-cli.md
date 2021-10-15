---
title: Verwalten von Lesereplikaten auf flexiblen Azure Database for MySQL-Servern mithilfe der Azure-Befehlszeilenschnittstelle
description: Erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle Lesereplikate auf flexiblen Azure Database for MySQL-Servern einrichten und verwalten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77e9d66af8a03f9d1a55a59d9a355499a3019f29
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233899"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Erstellen und Verwalten von Lesereplikaten auf flexiblen Azure Database for MySQL-Servern mithilfe der Azure-Befehlszeilenschnittstelle

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Lesereplikate auf flexiblen Azure Database for MySQL-Servern befinden sich in der Vorschauphase.

In diesem Artikel erfahren Sie, wie Sie Lesereplikate auf flexiblen Azure Database for MySQL-Servern mithilfe der Azure-Befehlszeilenschnittstelle erstellen und verwalten. Weitere Informationen zu Lesereplikaten finden Sie in der [Übersicht](concepts-read-replicas.md).

> [!Note]
>
> * Auf Servern mit Hochverfügbarkeit werden keine Replikate unterstützt. 
>* Das Feature für Lesereplikate ist nur für flexible Azure Database for MySQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Quellserver einer der folgenden Tarife festgelegt ist.
>
> * Wenn GTID auf einem primären Server aktiviert ist (`gtid_mode` = ON), wird für neu erstellte Replikate GTID ebenfalls aktiviert, und es wird die GTID-Replikation verwendet. Weitere Informationen finden Sie unter [Globaler Transaktionsbezeichner (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid)

## <a name="azure-cli"></a>Azure CLI

Sie können Lesereplikate mithilfe der Azure CLI erstellen und verwalten.

### <a name="prerequisites"></a>Voraussetzungen

- [Installieren der Azure CLI 2.0](/cli/azure/install-azure-cli)
- Ein [flexibler Azure Database for MySQL-Server](quickstart-create-server-cli.md), der als Quellserver verwendet wird

### <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats

> [!IMPORTANT]
>Wenn Sie ein Replikat für eine Quelle erstellen, die keine vorhandenen Replikate hat, startet die Quelle zunächst neu, um sich auf die Replikation vorzubereiten. Beachten Sie dies, und führen Sie diese Vorgänge nicht zu Spitzenzeiten durch.

Ein Lesereplikatserver kann mit dem folgenden Befehl erstellt werden:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> Lesereplikate werden mit der gleichen Serverkonfiguration wie die Quelle erstellt. Die Replikatserverkonfiguration kann nach der Erstellung geändert werden. Der Replikatserver wird immer in derselben Ressourcengruppe, am selben Standort und im selben Abonnement wie der Quellserver erstellt. Wenn Sie einen Replikatserver in einer anderen Ressourcengruppe oder einem anderen Abonnement erstellen möchten, können Sie nach der Erstellung den [Replikatserver verschieben](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Für die Konfiguration des Replikatservers sollten mindestens die gleichen Werte verwendet werden wie für den Quellserver, damit das Replikat über genügend Kapazität verfügt.


### <a name="list-replicas-for-a-source-server"></a>Auflisten von Replikaten für einen Quellserver

Führen Sie den folgenden Befehl aus, um alle Replikate für einen bestimmten Quellserver anzuzeigen: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>Beenden der Replikation auf einem Replikatserver

> [!IMPORTANT]
>Das Beenden der Replikation auf einem Server kann nicht rückgängig gemacht werden. Wenn die Replikation zwischen einer Quelle und dem Replikat beendet wurde, kann dies nicht rückgängig gemacht werden. Der Replikatserver wird zu einem eigenständigen Server und unterstützt nun Lese- und Schreibvorgänge. Der Server kann nicht wieder in ein Replikat umgewandelt werden.

Die Replikation auf einem Lesereplikatserver kann mit dem folgenden Befehl beendet werden:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>Löschen eines Replikatservers

Zum Löschen eines Lesereplikatservers kann der Befehl **[az mysql server delete](/cli/azure/mysql/server)** ausgeführt werden.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Löschen eines Quellservers

> [!IMPORTANT]
>Wenn Sie einen Quellserver löschen, wird die Replikation auf allen Replikatservern beendet und der Quellserver selbst gelöscht. Replikatserver werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

Zum Löschen eines Quellservers können Sie den Befehl **[az mysql flexible-server delete](/cli/azure/mysql/flexible-server)** ausführen.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Lesereplikaten](concepts-read-replicas.md)