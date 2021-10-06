---
title: Verwalten von Videos während Anrufen
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden Sie Azure Communication Services SDKs, um Videoanrufe zu verwalten.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 536dc2233ca0ae0773c9e953d35bee6ee4a4da40
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700304"
---
# <a name="manage-video-during-calls"></a>Verwalten von Videos während Anrufen

Lernen Sie, wie Sie Videoanrufe mit den Azure Communication Services SDKS verwalten können. Sie lernen, wie Sie den Empfang und das Senden von Videos während eines Anrufs verwalten können.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Ein Benutzerzugriffstoken zum Aktivieren des Anrufclients. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Zugriffstoken](../../quickstarts/access-tokens.md).
- Optional: Schließen Sie den Schnellstart ab, um [Ihrer Anwendung Sprachanrufe hinzuzufügen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-web"
[!INCLUDE [Manage Video Calls JavaScript](./includes/manage-video/manage-video-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Manage Video Calls Android](./includes/manage-video/manage-video-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Manage Video Calls iOS](./includes/manage-video/manage-video-ios.md)]
::: zone-end

## <a name="next-steps"></a>Nächste Schritte
- [Lernen, wie man Anrufe verwaltet](./manage-calls.md)
- [Lernen, wie man Anrufe aufzeichnet](./record-calls.md)
- [Lernen, wie man Anrufe transkribiert](./call-transcription.md)
