---
title: include file
description: include file
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560099"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Abrufen von Hostname, Ports und Zugriffsschlüsseln vom Azure-Portal

Um eine Verbindung mit einer Azure Cache for Redis-Instanz herzustellen, benötigen Cacheclients den Hostnamen, die Ports und einen Schlüssel für den Cache. Von einigen Clients wird unter Umständen mit etwas anderen Namen auf diese Elemente verwiesen. Sie können den Hostnamen, Ports und Zugriffsschlüssel vom [Azure-Portal](https://portal.azure.com) abrufen.

- Wählen Sie **Zugriffsschlüssel** aus, um die Zugriffsschlüssel aus dem linken Navigationsbereich des Cache abzurufen. 
  
  ![Azure Cache for Redis-Schlüssel](media/redis-cache-access-keys/redis-cache-keys.png)

- Wählen Sie **Eigenschaften** aus, um den Hostnamen und die Ports aus dem linken Navigationsbereich des Cache abzurufen. Der Hostname hat das Format *\<DNS name>.redis.cache.windows.net*.

  ![Azure Cache for Redis-Eigenschaften](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

