---
title: Datei einfügen
description: Datei einfügen
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5c3a653d66ddfb00833f2b90d61f08a51697fd46
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2021
ms.locfileid: "113131849"
---
> [!NOTE]
> Für eine Web-App kann nach 20 Minuten Inaktivität ein Timeout auftreten, und dieser Zeitgeber kann nur durch Anforderungen an die tatsächliche Web-App zurückgesetzt werden. Das Anzeigen der Konfiguration der App im Azure-Portal oder das Senden von Anforderungen an die Website mit erweiterten Tools (`https://<app_name>.scm.azurewebsites.net`) führt nicht zum Zurücksetzen des Zeitgebers. Wenn Sie für Ihre Web-App eine kontinuierliche oder zeitplanbasierte Ausführung festlegen oder ereignisgesteuerte Trigger verwenden, aktivieren Sie auf der Azure-Konfigurationsseite Ihrer Web-App die Einstellung **Always On**. Die Einstellung „Always On“ sorgt dafür, dass diese Arten von WebJobs zuverlässig ausgeführt werden. Dieses Feature steht nur in den [Tarifen](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) „Basic“, „Standard“ und „Premium“ zur Verfügung.
