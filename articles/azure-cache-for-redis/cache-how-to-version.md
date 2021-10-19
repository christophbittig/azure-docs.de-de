---
title: Festlegen der Redis-Version für Azure Cache for Redis
description: Erfahren Sie mehr über das Konfigurieren der Redis-Version.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: 41d61ccce3602b1d3e823eb6a4c46afe39159c46
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728198"
---
# <a name="set-redis-version-for-azure-cache-for-redis"></a>Festlegen der Redis-Version für Azure Cache for Redis
In diesem Artikel erfahren Sie, wie Sie die Version der Redis-Software konfigurieren, die mit Ihrer Cache-Instanz verwendet werden soll. Azure Cache for Redis bietet die neueste Hauptversion von Redis und mindestens eine frühere Version. Diese Versionen werden regelmäßig aktualisiert, sobald neuere Redis-Software veröffentlicht wird. Sie können zwischen den beiden verfügbaren Versionen wählen. Beachten Sie, dass automatisch ein Upgrade Ihres Caches auf die nächste Version erfolgt, wenn die derzeit verwendete Version nicht mehr unterstützt wird.

> [!NOTE]
> Zur Zeit unterstützt Redis 6 keine Zugriffssteuerungsliste und keine Georeplikation zwischen einem Redis 4- und 6-Cache.
>

## <a name="prerequisites"></a>Voraussetzungen
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)

## <a name="create-a-cache-using-the-azure-portal"></a>Erstellen eines Caches über das Azure-Portal
Führen Sie die folgenden Schritte aus, um einen Cache zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann **Ressource erstellen** aus.
  
1. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann **Azure Cache for Redis** aus.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Auswählen von „Azure Cache for Redis“.":::
   
1. Konfigurieren Sie auf der Seite **Grundlagen** die Einstellungen für den neuen Cache.
   
    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. | 
    | **Ressourcengruppe** | Wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr Cache und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. | 
    | **DNS-Name** | Geben Sie einen global eindeutigen Namen ein. | Der Cachename muss zwischen 1 und 63 Zeichen lang sein und darf nur Zahlen, Buchstaben und Bindestriche enthalten. Der Name muss mit einer Zahl oder einem Buchstaben beginnen und enden und darf keine aufeinanderfolgenden Bindestriche enthalten. Der *Hostname* Ihrer Cache-Instanz lautet *\<DNS name>.redis.cache.windows.net*. | 
    | **Location** | Wählen Sie einen Standort aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren Cache verwenden. |
    | **Cachetyp** | Wählen Sie einen [Tarif und eine Größe für den Cache](https://azure.microsoft.com/pricing/details/cache/) aus. |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](cache-overview.md) (Was ist Azure Cache for Redis?). |
   
1. Wählen Sie auf der Seite **Erweitert** eine zu verwendende Redis-Version aus.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redis-Version.":::

1. Klicken Sie auf **Erstellen**. 
   
    Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit.


## <a name="create-a-cache-using-azure-powershell"></a>Erstellen eines Caches mithilfe von Azure PowerShell

```azurepowershell
    New-AzRedisCache -ResourceGroupName "ResourceGroupName" -Name "CacheName" -Location "West US 2" -Size 250MB -Sku "Standard" -RedisVersion "6"
```
Weitere Informationen zum Verwalten von Azure Cache for Redis mit Azure PowerShell finden Sie [hier](cache-how-to-manage-redis-cache-powershell.md).

## <a name="create-a-cache-using-azure-cli"></a>Erstellen eines Caches mithilfe der Azure CLI

```azurecli-interactive
az redis create --resource-group resourceGroupName --name cacheName --location westus2 --sku Standard --vm-size c0 --redisVersion="6"
```
Weitere Informationen zum Verwalten von Azure Cache for Redis mit der Azure CLI finden Sie [hier](cli-samples.md).

## <a name="upgrade-an-existing-redis-4-cache-to-redis-6"></a>Durchführen eines Upgrades für einen vorhandenen Redis 4-Cache auf Redis 6
Azure Cache for Redis unterstützt das Upgrade der Hauptversion Ihres Redis Cache-Servers von Redis 4 auf Redis 6. Beachten Sie, dass das Upgrade dauerhaft ist und zu einer kurzen Verbindungsunterbrechung führen kann. Als Vorsichtsmaßnahme empfehlen wir, die Daten aus Ihrem vorhandenen Redis 4-Cache zu exportieren und Ihre Clientanwendung vor dem Upgrade mit einem Redis 6-Cache in einer niedrigeren Umgebung zu testen. Ausführliche Informationen zum Exportieren finden Sie [hier](cache-how-to-import-export-data.md).

> [!NOTE]
> Beachten Sie, dass ein Upgrade bei einem Cache mit einem Georeplikationslink nicht unterstützt wird, sodass Sie die Verknüpfung Ihrer Cache-Instanzen vor dem Upgrade manuell aufheben müssen. 
>

Gehen Sie folgendermaßen vor, um ein Upgrade für Ihren Cache durchzuführen:

1. Suchen Sie im Azure-Portal nach **Azure Cache for Redis**. Drücken Sie dann die EINGABETASTE, oder wählen Sie den Eintrag in den Suchvorschlägen aus.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Suche nach „Azure Cache for Redis“":::

1. Wählen Sie die Cache-Instanz aus, für die Sie ein Upgrade von Redis 4 auf Redis 6 durchführen möchten.

1. Wählen Sie im linken Bildschirmbereich **Erweiterte Einstellung** aus. 

1. Wenn Ihre Cache-Instanz für ein Upgrade berechtigt ist, sollte das folgende blaue Banner angezeigt werden. Wenn Sie fortfahren möchten, wählen Sie den Text im Banner aus.

    :::image type="content" source="media/cache-how-to-version/blue-banner-upgrade-cache.png" alt-text="Blaues Banner, das besagt, dass Sie für Ihren Redis 6-Cache ein Upgrade mit zusätzlichen Features und Befehlen durchführen können, die die Produktivität der Entwickler und die Benutzerfreundlichkeit erhöhen. Das Upgrade Ihrer Cache-Instanz kann nicht rückgängig gemacht werden.":::
    
1. Daraufhin wird ein Dialogfeld angezeigt, in dem Sie darüber informiert werden, dass das Upgrade dauerhaft ist und zu einer kurzen Verbindungsunterbrechung führen kann. Wählen Sie „Ja“ aus, wenn Sie ein Upgrade für Ihre Cache-Instanz durchführen möchten.

    :::image type="content" source="media/cache-how-to-version/dialog-version-upgrade.png" alt-text="Dialogfeld mit weiteren Informationen zum Upgrade für Ihren Cache":::

1. Um den Status des Upgrades zu überprüfen, navigieren Sie zu **Übersicht**.

    :::image type="content" source="media/cache-how-to-version/upgrade-status.png" alt-text="Die Übersicht zeigt den Status des Caches, für den das Upgrade durchgeführt wird.":::

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="what-features-arent-supported-with-redis-6"></a>Welche Features werden mit Redis 6 nicht unterstützt?

Zur Zeit unterstützt Redis 6 keine Zugriffssteuerungsliste und keine Georeplikation zwischen einem Redis 4- und 6-Cache.

### <a name="can-i-change-the-version-of-my-cache-after-its-created"></a>Kann ich die Version meines Caches ändern, nachdem er erstellt wurde?

Sie können für Ihre vorhandenen Redis 4-Caches ein Upgrade auf Redis 6 durchführen. Weitere Informationen finden Sie [hier](#upgrade-an-existing-redis-4-cache-to-redis-6). Beachten Sie, dass das Upgrade Ihrer Cache-Instanz dauerhaft ist und Sie Ihre Redis 6-Caches nicht auf Redis 4-Caches herabstufen können.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Redis 6-Features finden Sie unter [Diving Into Redis 6.0 by Redis](https://redis.com/blog/diving-into-redis-6/) (Eintauchen in Redis 6.0 von Redis).
- So erfahren Sie mehr über Azure Cache for Redis-Features:

> [!div class="nextstepaction"]
> [Premium-Dienstebenen für Azure Cache for Redis](cache-overview.md#service-tiers)
