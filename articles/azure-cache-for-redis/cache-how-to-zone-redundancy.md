---
title: Aktivieren der Zonenredundanz für Azure Cache for Redis
description: Hier erfahren Sie, wie Sie Zonenredundanz für Ihre Azure Cache for Redis-Instanzen im Premium- und Enterprise-Tarif einrichten.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 522cf0ad115e1149d7746ecadef96ae9494a1d8c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339953"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis"></a>Aktivieren der Zonenredundanz für Azure Cache for Redis
In diesem Artikel erfahren Sie, wie Sie eine zonenredundante Azure Cache-Instanz über das Azure-Portal konfigurieren.

Azure Cache for Redis im Standard-, Premium- und Enterprise-Tarif bietet integrierte Redundanz durch das Hosting jedes Caches auf zwei dedizierten virtuellen Computern (VMs). Obwohl sich diese VMs in separaten [Azure-Fehler- und Updatedomänen befinden](../virtual-machines/availability.md) und hoch verfügbar sind, sind Sie anfällig für Ausfälle auf Rechenzentrumsebene. Azure Cache for Redis unterstützt auch Zonenredundanz im Premium- und Enterprise-Tarif. Ein zonenredundanter Cache wird auf VMs ausgeführt, die auf mehrere [Verfügbarkeitszonen](../availability-zones/az-overview.md) verteilt sind. Dies bietet höhere Resilienz und Verfügbarkeit.

> [!NOTE]
> Die Datenübertragung zwischen Azure-Verfügbarkeitszonen wird mit den [Standardbandbreitensätzen](https://azure.microsoft.com/pricing/details/bandwidth/) abgerechnet.

## <a name="prerequisites"></a>Voraussetzungen
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>Erstellen eines Caches
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
    | **Cachetyp** | Wählen Sie einen Cache im [Premium- oder Enterprise-Tarif](https://azure.microsoft.com/pricing/details/cache/) aus. |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](cache-overview.md) (Was ist Azure Cache for Redis?). |
   
1. Wählen Sie auf der Seite **Erweitert** für einen Cache im Premium-Tarif die Option **Replikatanzahl** aus.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Replikatanzahl":::

1. Wählen Sie **Verfügbarkeitszonen** aus. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Verfügbarkeitszonen":::

1. Konfigurieren Sie Ihre Einstellungen für das Clustering und/oder die RDB-Persistenz.  

    > [!NOTE]
    > Bei der Zonenredundanz wird derzeit keine AOF-Persistenz unterstützt, und sie funktioniert nicht zusammen mit der Georeplikation.
    >

1. Klicken Sie auf **Erstellen**. 
   
    Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit.
   
    > [!NOTE]
    > Verfügbarkeitszonen können nicht mehr geändert oder aktiviert werden, nachdem ein Cache erstellt wurde. 
    >

## <a name="zone-redundancy-faq"></a>Häufig gestellte Fragen zur Zonenredundanz

- [Warum kann ich die Zonenredundanz beim Erstellen eines Premium-Caches nicht aktivieren?](#why-cant-i-enable-zone-redundancy-when-creating-a-premium-cache)
- [Warum kann ich während der Cacheerstellung nicht alle drei Zonen auswählen?](#why-cant-i-select-all-three-zones-during-cache-create)
- [Kann ich meinen vorhandenen Premium-Cache aktualisieren, um Zonenredundanz zu verwenden?](#can-i-update-my-existing-premium-cache-to-use-zone-redundancy)
- [Wie viel kostet die Replikation meiner Daten über verschiedene Azure-Verfügbarkeitszonen hinweg?](#how-much-does-it-cost-to-replicate-my-data-across-azure-availability-zones)

### <a name="why-cant-i-enable-zone-redundancy-when-creating-a-premium-cache"></a>Warum kann ich die Zonenredundanz beim Erstellen eines Premium-Caches nicht aktivieren?

Zonenredundanz ist nur in Azure-Regionen verfügbar, die über Verfügbarkeitszonen verfügen. Eine aktuelle Liste finden Sie unter [Azure-Regionen mit Verfügbarkeitszonen](../availability-zones/az-region.md#azure-services-supporting-availability-zones).

### <a name="why-cant-i-select-all-three-zones-during-cache-create"></a>Warum kann ich während der Cacheerstellung nicht alle drei Zonen auswählen?

Ein Premium-Cache verfügt standardmäßig über einen primären Knoten und einen Replikatknoten. Zum Konfigurieren der Zonenredundanz für mehr als zwei Verfügbarkeitszonen müssen Sie dem Cache, den Sie erstellen, [weitere Replikate](cache-how-to-multi-replicas.md) hinzufügen.

### <a name="can-i-update-my-existing-premium-cache-to-use-zone-redundancy"></a>Kann ich meinen vorhandenen Premium-Cache aktualisieren, um Zonenredundanz zu verwenden?

Nein, dies wird zurzeit nicht unterstützt.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-availability-zones"></a>Wie viel kostet die Replikation meiner Daten über verschiedene Azure-Verfügbarkeitszonen hinweg?

Bei Verwendung von Zonenredundanz, die mit mehreren Verfügbarkeitszonen konfiguriert ist, werden Daten vom primären Cacheknoten in einer Zone auf die anderen Knoten in einer oder mehreren anderen Zonen repliziert. Die Datenübertragungsgebühr entspricht den Kosten für die ausgehenden Netzwerkdatendaten, die über die ausgewählten Verfügbarkeitszonen übertragen werden. Weitere Informationen finden Sie unter [Bandbreite – Preisdetails](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Azure Cache for Redis-Features.

> [!div class="nextstepaction"]
> [Premium-Dienstebenen für Azure Cache for Redis](cache-overview.md#service-tiers)
