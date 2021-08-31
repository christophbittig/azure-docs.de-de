---
title: Datei einfügen
description: Datei einfügen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: eff2a7eda24834389db7afc0a689767f20da045f
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423435"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Was geschieht während der Bereitstellung mit meiner App?

Alle offiziell unterstützten Bereitstellungsmethoden nehmen Änderungen an den Dateien im Ordner */home/site/wwwroot* Ihrer App vor. Diese Dateien werden zum Ausführen Ihrer App verwendet. Daher kann die Bereitstellung aufgrund gesperrter Dateien fehlschlagen. Die App kann sich während der Bereitstellung unvorhersehbar verhalten, da nicht alle Dateien gleichzeitig aktualisiert werden. Dieses Verhalten ist bei kundenorientierten Apps nicht erwünscht. Es gibt mehrere Möglichkeiten zum Umgehen dieser Probleme:

- [Führen Sie die App direkt aus dem ZIP-Paket aus](../articles/app-service/deploy-run-package.md), ohne Sie zu entpacken.
- Beenden Sie die App oder aktivieren Sie während der Bereitstellung den Offlinemodus der App. Weitere Informationen finden Sie unter [Deal with locked files during deployment (Umgang mit gesperrten Dateien während der Bereitstellung)](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Stellen Sie sie in einem [Stagingslot](../articles/app-service/deploy-staging-slots.md) bereit, bei dem [Automatisch tauschen](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) aktiviert ist. 
