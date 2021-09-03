---
title: Hinzufügen von Replikaten zu Azure Cache for Redis
description: Erfahren Sie, wie Sie weitere Replikate für Ihre Azure Cache for Redis-Instanzen im Premium-Tarif hinzufügen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: d92591e335a24aa50de081c5a001801f22c92dba
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349544"
---
# <a name="add-replicas-to-azure-cache-for-redis"></a>Hinzufügen von Replikaten zu Azure Cache for Redis
In diesem Artikel erfahren Sie, wie Sie eine Azure Cache-Instanz mit weiteren Replikaten über das Azure-Portal einrichten.

Azure Cache for Redis im Standard- und Premium-Tarif bietet Redundanz durch das Hosten jedes Caches auf zwei dedizierten virtuellen Computern (VMs). Diese VMs werden als primäre VM und als Replikat konfiguriert. Wenn die primäre VM nicht mehr verfügbar ist, erkennt das Replikat dies und übernimmt automatisch als neue primäre VM. Sie können jetzt die Anzahl der Replikate in einem Premium-Cache auf bis zu drei erhöhen, sodass Sie insgesamt vier VMs zur Unterstützung eines Caches nutzen können. Das Vorhandensein mehrerer Replikate führt zu einer höheren Resilienz als bei einem einzelnen Replikat.

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
    | **Cachetyp** | Wählen Sie einen Cache im [Premium-Tarif](https://azure.microsoft.com/pricing/details/cache/) aus. |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](cache-overview.md) (Was ist Azure Cache for Redis?). |
   
1. Wählen Sie auf der Seite **Erweitert** die Option **Replikatanzahl** aus.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Replikatanzahl.":::
    
    > [!NOTE]
    > Derzeit können Sie keine AOF-Persistenz (Datei mit nur Anfügen) oder Georeplikation mit mehreren Replikaten (mehr als ein Replikat) verwenden.
    >

1. Behalten Sie bei den anderen Optionen die Standardeinstellungen bei. 

1. Klicken Sie auf **Erstellen**.
   
    Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit.

    > [!NOTE]
    > Die Anzahl der Replikate in einem Cache kann nicht geändert werden, nachdem er erstellt wurde.
    >

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Azure Cache for Redis-Features.

> [!div class="nextstepaction"]
> [Premium-Dienstebenen für Azure Cache for Redis](cache-overview.md#service-tiers)
