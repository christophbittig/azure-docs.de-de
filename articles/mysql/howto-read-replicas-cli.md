---
title: Verwalten von Lesereplikaten in Azure Database for MySQL mit der Azure-Befehlszeilenschnittstelle oder REST-API
description: Erfahren Sie, wie Sie mit der Azure CLI oder der REST-API Lesereplikate in Azure Database for MySQL einrichten und verwalten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c7f33156394b3dfde100014ace6d8b7f1cbc8caf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339345"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Informationen zum Erstellen und Verwalten von Lesereplikaten in Azure Database for MySQL mithilfe der Azure CLI und REST-API

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

In diesem Artikel erfahren Sie, wie Sie Lesereplikate im Azure Database for MySQL-Dienst mithilfe der Azure CLI und REST-API erstellen und verwalten. Weitere Informationen zu Lesereplikaten finden Sie in der [Übersicht](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Sie können Lesereplikate mithilfe der Azure CLI erstellen und verwalten.

### <a name="prerequisites"></a>Voraussetzungen

- [Installieren der Azure CLI 2.0](/cli/azure/install-azure-cli)
- Ein [Azure Database for MySQL-Server](quickstart-create-mysql-server-database-using-azure-portal.md), der als Quellserver verwendet wird. 

> [!IMPORTANT]
> Das Feature für Lesereplikate ist nur für Azure Database for MySQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Quellserver einer der folgenden Tarife festgelegt ist.

### <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats

> [!IMPORTANT]
> Wenn Sie ein Replikat für eine Quelle erstellen, die keine vorhandenen Replikate hat, startet die Quelle zunächst neu, um sich auf die Replikation vorzubereiten. Beachten Sie dies, und führen Sie diese Vorgänge nicht zu Spitzenzeiten durch.
>
>Wenn die GTID auf einem primären Server aktiviert ist (`gtid_mode` = ON), wird für neu erstellte Replikate die GTID ebenfalls aktiviert und die GTID-Replikation verwendet. Weitere Informationen finden Sie unter [Globaler Transaktionsbezeichner (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid).

Ein Lesereplikatserver kann mit dem folgenden Befehl erstellt werden:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Für den Befehl `az mysql server replica create` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| name | mydemoreplicaserver | Der Name des neuen Replikatservers, der erstellt wird. |
| source-server | mydemoserver | Der Name oder die ID des vorhandenen Quellservers, der repliziert werden soll |

Verwenden Sie den `--location`-Parameter, um ein regionsübergreifendes Lesereplikat zu erstellen. Im folgenden CLI-Beispiel wird das Replikat in der Region „USA, Westen“ erstellt.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Weitere Informationen zu den Regionen, in denen Sie ein Replikat erstellen können, finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md). 

> [!NOTE]
> * Der Befehl `az mysql server replica create` hat das Argument `--sku-name`, mit dem Sie die SKU (`{pricing_tier}_{compute generation}_{vCores}`) angeben können, während Sie mit der Azure-Befehlszeilenschnittstelle ein Replikat erstellen. </br>
> * Der primäre Server und das Lesereplikat sollten sich im gleichen Tarif („Universell“ oder „Arbeitsspeicheroptimiert“) befinden. </br>
> * Die Replikatserverkonfiguration kann nach der Erstellung noch geändert werden. Für die Konfiguration des Replikatservers sollten mindestens die gleichen Werte verwendet werden wie für den Quellserver, damit das Replikat über genügend Kapazität verfügt.


### <a name="list-replicas-for-a-source-server"></a>Auflisten von Replikaten für einen Quellserver

Führen Sie den folgenden Befehl aus, um alle Replikate für einen bestimmten Quellserver anzuzeigen: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Für den Befehl `az mysql server replica list` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver erstellt wird.  |
| Servername | mydemoserver | Der Name oder die ID des Quellservers. |

### <a name="stop-replication-to-a-replica-server"></a>Beenden der Replikation auf einem Replikatserver

> [!IMPORTANT]
> Das Beenden der Replikation auf einem Server kann nicht rückgängig gemacht werden. Wenn die Replikation zwischen einer Quelle und dem Replikat beendet wurde, kann dies nicht rückgängig gemacht werden. Der Replikatserver wird zu einem eigenständigen Server und unterstützt nun Lese- und Schreibvorgänge. Der Server kann nicht wieder in ein Replikat umgewandelt werden.

Die Replikation auf einem Lesereplikatserver kann mit dem folgenden Befehl beendet werden:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Für den Befehl `az mysql server replica stop` sind folgende Parameter erforderlich:

| Einstellung | Beispielwert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der der Replikatserver enthalten ist.  |
| name | mydemoreplicaserver | Der Name des Replikatservers, auf dem die Replikation beendet werden soll. |

### <a name="delete-a-replica-server"></a>Löschen eines Replikatservers

Zum Löschen eines Lesereplikatservers kann der Befehl **[az mysql server delete](/cli/azure/mysql/server)** ausgeführt werden.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Löschen eines Quellservers

> [!IMPORTANT]
> Wenn Sie einen Quellserver löschen, wird die Replikation auf allen Replikatservern beendet und der Quellserver selbst gelöscht. Replikatserver werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

Zum Löschen eines Quellservers können Sie den Befehl **[az mysql server delete](/cli/azure/mysql/server)** ausführen.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST-API
Sie können Lesereplikate mithilfe der [ Azure-REST-API](/rest/api/azure/) erstellen und verwalten.

### <a name="create-a-read-replica"></a>Erstellen eines Lesereplikats
Sie können ein Lesereplikat mithilfe der [Create-API](/rest/api/mysql/flexibleserver(preview)/servers/create) erstellen:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Weitere Informationen zu den Regionen, in denen Sie ein Replikat erstellen können, finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md). 

Wenn Sie den Parameter `azure.replication_support` auf einem universellen oder arbeitsspeicheroptimierten Quellserver nicht auf **REPLICA** festgelegt und den Server nicht neu gestartet haben, tritt ein Fehler auf. Führen Sie diese beiden Schritte aus, bevor Sie ein Replikat erstellen.

Ein Replikat wird mit den gleichen Compute- und Speichereinstellungen erstellt wie der Master. Nach dem Erstellen des Replikats können einige der Einstellungen unabhängig vom Quellserver geändert werden: die Computegeneration, die virtuellen Kerne, der Speicher und der Aufbewahrungszeitraum für Sicherungen. Auch der Tarif kann unabhängig geändert werden, allerdings nicht in den oder aus dem Tarif „Basic“.


> [!IMPORTANT]
> Ändern Sie vor der Aktualisierung einer Quellservereinstellung auf einen neuen Wert die entsprechende Replikateinstellung in den gleichen oder einen höheren Wert. Diese Aktion sorgt dafür, dass das Replikat mit allen Änderungen auf dem Masterserver Schritt halten kann.

### <a name="list-replicas"></a>Auflisten von Replikaten
Sie können die Replikatliste eines Quellservers mithilfe der [API zum Auflisten von Replikaten](/rest/api/mysql/flexibleserver(preview)/replicas/listbyserver) anzeigen:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Beenden der Replikation auf einem Replikatserver
Sie können die Replikation zwischen einem Quellserver und einem Lesereplikat mithilfe der [Update-API](/rest/api/mysql/flexibleserver(preview)/servers/update) beenden.

Das Beenden der Replikation zwischen einem Quellserver und einem Lesereplikat kann nicht rückgängig gemacht werden. Das Lesereplikat wird zu einem eigenständigen Server, der sowohl Lese- als auch Schreibvorgänge unterstützt. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>Löschen eines Quell- oder Replikatservers
Zum Löschen eines Quell- oder Replikatservers wird die [Lösch-API](/rest/api/mysql/flexibleserver(preview)/servers/delete) verwendet:

Wenn Sie einen Quellserver löschen, wird die Replikation in alle Lesereplikate beendet. Die Lesereplikate werden zu eigenständigen Servern, die nun Lese- und Schreibvorgänge unterstützen.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```

### <a name="known-issue"></a>Bekanntes Problem

Es gibt zwei Generationen von Speicher, die von Servern der Ebenen „Universell“ und „Arbeitsspeicheroptimiert“ verwendet werden: universelle Speicher v1 (unterstützen bis zu 4 TB) und universelle Speicher v2 (unterstützen bis zu 16 TB Speicher).
Quellserver und Replikatserver sollten denselben Speichertyp aufweisen. Da [universeller Speicher v2](./concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage) nicht in allen Regionen verfügbar ist, müssen Sie sicherstellen, dass Sie die richtige Replikatregion auswählen, wenn Sie den Standort mit der Befehlszeilenschnittstelle oder der REST-API für die Erstellung von Lesereplikaten verwenden. Informationen zur Ermittlung des Speichertyps Ihres Quellservers finden Sie unter [Wie ermittle ich den Speichertyp, auf dem mein Server ausgeführt wird?](./concepts-pricing-tiers.md#how-can-i-determine-which-storage-type-my-server-is-running-on). 

Wenn Sie eine Region auswählen, in der Sie kein Lesereplikat für Ihren Quellserver erstellen können, tritt das Problem wie in der folgenden Abbildung auf, dass die Bereitstellung weiter ausgeführt wird, bis ein Timeout mit dem Fehler *„Der Ressourcenbereitstellungsvorgang wurde nicht innerhalb des zulässigen Timeoutzeitraums abgeschlossen“* auftritt.

[ :::image type="content" source="media/howto-read-replicas-cli/replcia-cli-known-issue.png" alt-text="Fehler beim Lesereplikat an der Befehlszeilenschnittstelle":::](media/howto-read-replicas-cli/replcia-cli-known-issue.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Lesereplikaten](concepts-read-replicas.md)
