---
title: Datei einfügen
description: Datei einfügen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/19/2021
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 974e3bdf99cb2fe1adb075d03105b68e28632b3a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446586"
---
> [!IMPORTANT]
> In einer Containerregistrierung, die den Zugriff auf private Endpunkte, ausgewählte Subnetze oder IP-Adressen einschränkt, sind einige Funktionen möglicherweise nicht verfügbar oder erfordern eine umfangreichere Konfiguration. 
> * Wenn der öffentliche Netzwerkzugriff auf eine Registrierung deaktiviert ist, erfordert der Zugriff auf die Registrierung durch bestimmte [vertrauenswürdige Dienste](../articles/container-registry/allow-access-trusted-services.md) einschließlich Azure Security Center die Aktivierung einer Netzwerkeinstellung, um die Netzwerkregeln zu umgehen.
> * Instanzen bestimmter Azure-Dienste, darunter Azure DevOps Services, können derzeit nicht auf die Containerregistrierung zugreifen.
> * Wenn die Registrierung einen genehmigten privaten Endpunkt hat und der öffentliche Netzwerkzugriff deaktiviert ist, können Repositorys und Tags außerhalb des virtuellen Netzwerks nicht über das Azure-Portal, die Azure CLI oder andere Tools aufgelistet werden.
