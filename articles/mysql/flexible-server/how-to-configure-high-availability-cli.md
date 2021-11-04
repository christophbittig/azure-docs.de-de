---
title: Verwalten zonenredundanter Hochverfügbarkeit – Azure-CLI – Azure Database for MySQL Flexible Server
description: In diesem Artikel wird beschrieben, wie Sie die zonenredundante Hochverfügbarkeit in Azure Database for MySQL Flexible Server mit Azure-CLI konfigurieren.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3ed931d0f972caa2e4a49012ad09afe9df9d3233
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422765"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Verwalten von zonenredundanter Hochverfügbarkeit für Azure Database für MySQL Flexible Server mit Azure CLI

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Der Artikel beschreibt, wie Sie die zonenredundante Hochverfügbarkeitskonfiguration zum Zeitpunkt der Servererstellung in Ihrem flexiblen Server aktivieren oder deaktivieren können. Sie können die zonenredundante Hochverfügbarkeit auch nach der Servererstellung deaktivieren. Das Aktivieren zonenredundanten Hochverfügbarkeit nach der Servererstellung wird nicht unterstützt.

Die Hochverfügbarkeitsfunktion stellt ein physisch getrenntes primäres und Standbyreplikat in unterschiedlichen Zonen bereit. Weitere Informationen finden Sie unter [Dokumentation zu Hochverfügbarkeitskonzepten](./concepts/../concepts-high-availability.md). Durch die Aktivierung/Deaktivierung der Hochverfügbarkeit werden keine Ihrer anderen Einstellungen geändert, einschließlich der VNET-Konfiguration, der Firewalleinstellungen und der Sicherungsaufbewahrung. Das Deaktivieren der Hochverfügbarkeit wirkt sich nicht auf die Konnektivität und die Vorgänge Ihrer Anwendung aus.

> [!IMPORTANT]
> Zonenredundante Hochverfügbarkeit ist in einer begrenzten Anzahl von Regionen verfügbar. Die unterstützten Regionen finden Sie [hier](./overview.md#azure-regions).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- Installieren Sie die Azure CLI, oder upgraden Sie sie auf die neueste Version. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).
- Melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az_login) beim Azure-Konto an. Beachten Sie die Eigenschaft **id**, die auf die **Abonnement-ID** für Ihr Azure-Konto verweist.

    ```azurecli-interactive
    az login
    ````

- Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem Sie den Server mit dem ```az account set```-Befehl erstellen.

    ```azurecli
    az account set --subscription <subscription id>
    ```

## <a name="enable-high-availability-during-server-creation"></a>Aktivieren von Hochverfügbarkeit während der Servererstellung

Sie können Server nur mit den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ mit Hochverfügbarkeit erstellen. Sie können zonenredundante Hochverfügbarkeit für einen Server nur während der Erstellung aktivieren.

**Syntax:**

   ```azurecli
    az mysql flexible-server create [--high-availability {Disabled, SameZone, ZoneRedundant}]
                                    [--sku-name]
                                    [--tier]
                                    [--resource-group]
                                    [--location]
                                    [--name]
   ```

**Beispiel:**

   ```azurecli
    az mysql flexible-server create --name myservername --sku-name Standard_D2ds_v4 --tier Genaralpurpose --resource-group myresourcegroup --high-availability ZoneRedundant --location eastus
   ```

## <a name="disable-high-availability"></a>Deaktivieren der Hochverfügbarkeit

Sie können die Hochverfügbarkeit mit dem Befehl [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) deaktivieren. Beachten Sie, dass das Deaktivieren von Hochverfügbarkeit nur unterstützt wird, wenn der Server mit Hochverfügbarkeit erstellt wurde.

```azurecli
az mysql flexible-server update [--high-availability {Disabled, SameZone, ZoneRedundant}]
                                [--resource-group]
                                [--name]
```
>[!Note]
>Wenn Sie von zonenredundanter Hochverfügbarkeit zu Hochverfügbarkeit in derselben Zone wechseln möchten, müssen Sie zuerst die Hochverfügbarkeit deaktivieren und dann dieselbe Zone aktivieren.

**Beispiel:**

   ```azurecli
    az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
   ```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Geschäftskontinuität](./concepts-business-continuity.md)
- Weitere Informationen zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)