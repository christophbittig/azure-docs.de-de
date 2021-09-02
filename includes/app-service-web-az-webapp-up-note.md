---
title: include file
description: include file
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: d26b8d131d5a8ff54b12fc5817718ddd381b720b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/20/2021
ms.locfileid: "122643265"
---
> [!NOTE]
> Der Befehl `az webapp up` bewirkt Folgendes:
>
>- Erstellen einer [Standardressourcengruppe](/cli/azure/group#az_group_create)
>
>- Erstellen eines standardmäßigen [App Service-Plans](/cli/azure/appservice/plan#az_appservice_plan_create)
>
>- [Erstellen einer App](/cli/azure/webapp#az_webapp_create) mit dem angegebenen Namen
>
>- [Bereitstellen von ZIP-Dateien](../articles/app-service/deploy-zip.md) aus dem aktuellen Arbeitsverzeichnis für die App
>