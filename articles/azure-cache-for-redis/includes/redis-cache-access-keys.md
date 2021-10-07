---
title: Datei einfügen
description: include file
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: c1f5aae79daf4cf8fb3a447eba5538212e3dc327
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533802"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Abrufen von Hostname, Ports und Zugriffsschlüsseln vom Azure-Portal

Um eine Verbindung mit einer Azure Cache for Redis-Instanz herzustellen, benötigen Cacheclients den Hostnamen, die Ports und einen Schlüssel für den Cache. Von einigen Clients wird unter Umständen mit etwas anderen Namen auf diese Elemente verwiesen. Sie können den Hostnamen, Ports und Zugriffsschlüssel vom [Azure-Portal](https://portal.azure.com) abrufen.

- Wählen Sie **Zugriffsschlüssel** aus, um die Zugriffsschlüssel aus dem linken Navigationsbereich des Cache abzurufen. 
  
  ![Azure Cache for Redis-Schlüssel](media/redis-cache-access-keys/redis-cache-keys.png)

- Wählen Sie **Eigenschaften** aus, um den Hostnamen und die Ports aus dem linken Navigationsbereich des Cache abzurufen. Der Hostname hat das Format *\<DNS name>.redis.cache.windows.net*.

  ![Azure Cache for Redis-Eigenschaften](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

