---
title: Aktivieren von Pushbenachrichtigungen für Anrufe
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden Sie Azure Communication Services-SDKs, um Pushbenachrichtigungen für Anrufe zu aktivieren.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 87e9aa1741808e60af14eb32e1837bc87c280062
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635715"
---
# <a name="enable-push-notifications-for-calls"></a>Aktivieren von Pushbenachrichtigungen für Anrufe

Hier erfahren Sie, wie Sie Pushbenachrichtigungen für Azure Communication Services-Anrufe aktivieren. Wenn Sie diese einrichten, werden Ihre Benutzer informiert, wenn ein Anruf für sie eingegangen ist, den sie dann beantworten können.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Ein Benutzerzugriffstoken zum Aktivieren des Anrufclients. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Zugriffstoken](../../quickstarts/access-tokens.md).
- Optional: Durchlaufen Sie den Schnellstart zum [Hinzufügen von Sprachanrufen zu Ihrer Anwendung](../../quickstarts/voice-video-calling/getting-started-with-calling.md).

::: zone pivot="platform-android"
[!INCLUDE [Enable push notifications Android](./includes/push-notifications/push-notifications-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Enable push notifications iOS](./includes/push-notifications/push-notifications-ios.md)]
::: zone-end

## <a name="next-steps"></a>Nächste Schritte
- [Informationen zum Abonnieren von Ereignissen](./events.md)
- [Informationen zum Verwalten von Anrufen](./manage-calls.md)
- [Informationen zum Verwalten von Video](./manage-video.md)
