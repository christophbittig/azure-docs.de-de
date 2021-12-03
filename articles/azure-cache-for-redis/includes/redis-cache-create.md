---
title: Datei einfügen
description: include file
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: 74b23eb8560a830e7e07a86fe745b94619eeeaf0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440322"
---
1. Melden Sie sich zum Erstellen eines Caches beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Ressource erstellen** aus.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Im linken Navigationsbereich ist „Ressource erstellen“ hervorgehoben.":::

1. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann **Azure Cache for Redis** aus.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="Unter „Neu“ sind die Optionen „Datenbanken“ und „Azure Cache for Redis“ hervorgehoben.":::

1. Konfigurieren Sie auf der Seite **Neuer Redis Cache** die Einstellungen für den neuen Cache.

   | Einstellung      | Wert auswählen  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. |
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr Cache und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. |
   | **DNS-Name** | Geben Sie einen eindeutigen Namen ein. | Der Cachename muss zwischen 1 und 63 Zeichen lang sein und darf nur Ziffern, Buchstaben und Bindestriche enthalten. Der Name muss mit einer Zahl oder einem Buchstaben beginnen und enden und darf keine aufeinanderfolgenden Bindestriche enthalten. Der *Hostname* Ihrer Cache-Instanz lautet *\<DNS name>.redis.cache.windows.net*. |
   | **Location** | Öffnen Sie die Dropdownliste, und wählen Sie einen Standort aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren Cache verwenden. |
   | **Cachetyp** | Öffnen Sie die Dropdownliste, und wählen Sie einen [Tarif](https://azure.microsoft.com/pricing/details/cache/) aus. |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](../cache-overview.md) (Was ist Azure Cache for Redis?). |

1. Wählen Sie die Registerkarte **Netzwerk** oder unten auf der Seite die Schaltfläche **Netzwerk** aus.

1. Wählen Sie auf der Registerkarte **Netzwerk** Ihre Konnektivitätsmethode aus.

1. Wählen Sie die Registerkarte **Weiter: Erweitert** oder unten auf der Seite die Schaltfläche **Weiter: Erweitert** aus.

1. Aktivieren Sie auf der Registerkarte **Erweitert** für eine Basic- oder Standard-Cache-Instanz die Aktivierungsoption, wenn Sie einen TLS-fremden Port aktivieren möchten. Sie können auch die zu verwendende Redis-Version auswählen, entweder 4 oder 6.

    :::image type="content" source="media/redis-cache-create/cache-redis-version.png" alt-text="Redis-Version 4 oder 6":::

1. Konfigurieren Sie auf der Registerkarte **Advanced** für eine Premium-Cache-Instanz die Einstellungen für einen TLS-fremden Port, Clustering und Datenpersistenz. Sie können auch die zu verwendende Redis-Version auswählen, entweder 4 oder 6.

1. Wählen Sie die Registerkarte **Weiter: Tags** oder unten auf der Seite die Schaltfläche **Weiter: Tags** aus.

1. Geben Sie optional auf der Registerkarte **Tags** den Namen und den Wert ein, wenn Sie die Ressource kategorisieren möchten.

1. Klicken Sie auf **Überprüfen und erstellen**. Sie werden zur Registerkarte „Überprüfen und erstellen“ weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

1. Wenn die grüne Meldung „Validierung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit.

<!-- Comment to dirty file. -->