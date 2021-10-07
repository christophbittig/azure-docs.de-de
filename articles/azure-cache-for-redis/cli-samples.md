---
title: Verwalten von Azure Cache for Redis mit der Azure CLI
description: 'Azure CLI-Beispiele für die Verwaltung von Azure Cache for Redis: Erstellen eines Caches, Löschen eines Caches, Abrufen von Cachedetails, Hostname, Ports und Schlüssel, Verbinden einer Web-App.'
author: curib
ms.author: cauribeg
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: f6a3b1701a73c4b71ee07b0110761566a4b2e93d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535809"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Verwalten von Azure Cache for Redis mit der Azure CLI

Die folgende Tabelle enthält Links zu Bash-Skripts, die mithilfe der Azure CLI erstellt wurden.

| Cache erstellen | BESCHREIBUNG |
| ------------ | ----------- |
| [Erstellen eines Caches](./scripts/create-cache.md) | Erstellt eine Ressourcengruppe und einen Azure Cache for Redis im Basic-Tarif. |
| [Erstellen eines Premium-Caches mit Clustering](./scripts/create-premium-cache-cluster.md) | Erstellt eine Ressourcengruppe und einen Cache im Premium-Tarif mit aktiviertem Clustering.|
| [Abrufen von Cachedetails](./scripts/show-cache.md) | Ruft die Details einer Azure Cache for Redis-Instanz samt Bereitstellungsstatus ab. |
| [Abrufen von Hostname, Ports und Schlüsseln](./scripts/cache-keys-ports.md) | Ruft den Hostnamen, die Ports und Schlüssel einer Azure Cache for Redis-Instanz ab. |
|**Web-App plus Cache**| **Beschreibung**|
| [Herstellen einer Verbindung zwischen einer Web-App und einer Azure Cache for Redis-Instanz](./../app-service/scripts/cli-connect-to-redis.md) | Erstellt eine Azure-Web-App und einen Azure Cache for Redis und fügt dann die Redis-Verbindungsdetails zu den App-Einstellungen hinzu. |
|**Löschen eines Caches**| **Beschreibung** |
| [Löschen eines Caches](./scripts/delete-cache.md) | Löscht eine Azure Cache for Redis-Instanz.  |

Weitere Informationen zur Azure CLI finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) und [Erste Schritte mit der Azure CLI](/cli/azure/get-started-with-azure-cli).