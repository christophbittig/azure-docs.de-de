---
title: Verwalten von Anrufen
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden Sie Azure Communication Services SDKs, um Anrufe zu verwalten.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 4abf92e2d3003d8944ea75c411bc9699716e4684
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700305"
---
# <a name="manage-calls"></a>Verwalten von Anrufen

Lernen Sie, wie Sie Anrufe mit den Azure Communication Services SDKS verwalten können. Wir erfahren, wie Sie Anrufe tätigen sowie ihre Teilnehmer und Eigenschaften verwalten.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Ein `User Access Token`, um den Anrufclient zu aktivieren. Weitere Informationen zum [Abrufen eines `User Access Token`](../../quickstarts/access-tokens.md)
- Optional: Gehen Sie den Schnellstart [Erste Schritte beim Hinzufügen von Anruffunktionen zu einer Anwendung](../../quickstarts/voice-video-calling/getting-started-with-calling.md) durch.

::: zone pivot="platform-web"
[!INCLUDE [Manage Calls JavaScript](./includes/manage-calls/manage-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Manage Calls Android](./includes/manage-calls/manage-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Manage Calls iOS](./includes/manage-calls/manage-calls-ios.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Manage Calls Windows](./includes/manage-calls/manage-calls-windows.md)]
::: zone-end

## <a name="next-steps"></a>Nächste Schritte
- [Informationen zum Verwalten von Video](./manage-video.md)
- [Informationen zum Aufzeichnen von Anrufen](./record-calls.md)
- [Informationen zum Transkribieren von Anrufen](./call-transcription.md)