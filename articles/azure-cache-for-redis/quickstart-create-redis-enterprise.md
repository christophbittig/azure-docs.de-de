---
title: 'Schnellstart: Erstellen einer Redis Cache-Instanz im Enterprise-Tarif'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie eine Azure Cache for Redis-Instanz mit Enterprise-Tarifen erstellen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 7733f9df7f314dfdc0369075b96477e301825ce6
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537328"
---
# <a name="quickstart-create-a-redis-enterprise-cache"></a>Schnellstart: Erstellen einer Redis Cache-Instanz im Enterprise-Tarif

Enterprise-Tarife für Azure Cache for Redis bieten vollständig integrierte und verwaltete Instanzen von [Redis Enterprise](https://redislabs.com/redis-enterprise/) in Azure. Diese neuen Ebenen lauten:

* Enterprise: Bei diesem Tarif wird flüchtiger Speicher (DRAM) auf einem virtuellen Computer als Datenspeicher verwendet.
* Enterprise Flash: Bei diesem Tarif wird eine Kombination aus flüchtigem und nicht flüchtigem Speicher (Non-Volatile Memory, NVMe oder SSD) als Datenspeicher verwendet.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement. Erstellen Sie ein [Konto](https://azure.microsoft.com/), falls Sie noch keines haben. Weitere Informationen finden Sie unter [Spezielle Überlegungen zu Enterprise-Tarifen](cache-overview.md#special-considerations-for-enterprise-tiers).

## <a name="create-a-cache"></a>Erstellen eines Caches

1. Melden Sie sich zum Erstellen eines Caches beim Azure-Portal an, und wählen Sie **Ressource erstellen** aus.

1. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann **Azure Cache for Redis** aus.

   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Auswählen von Azure Cache for Redis":::

1. Konfigurieren Sie auf der Seite **Neuer Redis Cache** die Einstellungen für den neuen Cache.

   | Einstellung      |  Wert auswählen  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. |
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr Cache und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. |
   | **DNS-Name** | Geben Sie einen Namen ein, der in der Region eindeutig ist. | Der Cachename muss zwischen 1 und 63 Zeichen lang sein und darf nur Ziffern, Buchstaben und Bindestriche enthalten. Der Name muss mit einer Zahl oder einem Buchstaben beginnen und enden und darf keine aufeinanderfolgenden Bindestriche enthalten. Der *Hostname* Ihrer Cache-Instanz lautet *\<DNS name\>.\<Azure region\>.redisenterprise.cache.azure.net*. |
   | **Standort** | Öffnen Sie die Dropdownliste, und wählen Sie einen Standort aus. | Enterprise-Tarife sind in ausgewählten Azure-Regionen verfügbar. |
   | **Cachetyp** | Wählen Sie in der Dropdownliste einen Tarif vom Typ *Enterprise* oder *Enterprise Flash* sowie eine Größe aus. |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. |

   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Enterprise-Tarif: Registerkarte „Grundlagen“":::

   > [!IMPORTANT]
   > Bevor Sie fortfahren, stellen Sie sicher, dass Sie **Bestimmungen** auswählen.
   >

1. Wählen Sie **Weiter: Netzwerk** aus, und überspringen Sie den Schritt.

1. Wählen Sie **Weiter: Erweitert** aus, und legen Sie die **Clusteringrichtlinie** für einen nicht gruppierten Cache auf **Enterprise** fest. Aktivieren Sie **Nur Nicht-TLS-Zugriff**, wenn Sie beabsichtigen, eine Verbindung mit dem neuen Cache ohne Verwendung von TLS herzustellen. Das Deaktivieren von TLS wird jedoch **nicht** empfohlen.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Screenshot: Registerkarte „Erweitert“ auf Enterprise-Ebene":::

   > [!NOTE]
   > Redis Enterprise unterstützt zwei Clusteringrichtlinien. Verwenden Sie die **Enterprise**-Richtlinie, um mithilfe der regulären Redis-API auf Ihren Cache zuzugreifen, und **OSS**, die OSS-Cluster-API.
   >

   > [!NOTE]
   > Module können nach dem Erstellen der Cache-Instanz nicht mehr geändert werden. Die Einstellung ist „Nur erstellen“.
   >

1. Wählen Sie **Weiter: Tags** aus, und überspringen Sie den Schritt.

1. Klicken Sie auf **Weiter: Überprüfen + erstellen**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Enterprise-Tarif: Registerkarte „Überprüfen und erstellen“":::

1. Überprüfen Sie die Einstellungen, und wählen Sie **Erstellen** aus.

   Die Erstellung des Caches dauert eine Weile. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung wurde beschrieben, wie Sie eine Azure Cache for Redis-Instanz mit Enterprise-Tarif erstellen.

> [!div class="nextstepaction"]
> [Erstellen einer ASP.NET-Web-App, die eine Azure Cache for Redis-Instanz verwendet](./cache-web-app-howto.md)
