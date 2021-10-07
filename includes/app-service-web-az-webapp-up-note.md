---
title: Datei einfügen
description: include file
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/14/2021
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: afaacb79906c07893d835d654b00815ea97199d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577322"
---
> [!NOTE]
> Der Befehl `az webapp up` bewirkt Folgendes:
>
>- Erstellen einer [Standardressourcengruppe](/cli/azure/group#az_group_create)
>
>- Erstellen eines [App Service-Plans](/cli/azure/appservice/plan#az_appservice_plan_create)
>
>- [Erstellen einer App](/cli/azure/webapp#az_webapp_create) mit dem angegebenen Namen
>
>- [Bereitstellen eines ZIP-Pakets](../articles/app-service/deploy-zip.md#deploy-a-zip-package) aller Dateien aus dem aktuellen Arbeitsverzeichnis [mit aktivierter Buildautomatisierung](../articles/app-service/deploy-zip.md#enable-build-automation-for-zip-deploy)
>
>- Lokales Zwischenspeichern der Parameter in der Datei *.azure/config*, sodass Sie sie später bei der Bereitstellung mit `az webapp up` oder anderen Azure CLI-Befehlen nicht erneut angeben müssen. Die zwischengespeicherten Werte werden standardmäßig automatisch verwendet.
>