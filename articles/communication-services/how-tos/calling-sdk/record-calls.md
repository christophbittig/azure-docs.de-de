---
title: Verwalten der Anrufaufzeichnung auf dem Client
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden Sie die Azure Communication Services SDKs, um die Anrufaufzeichnung auf dem Client zu verwalten.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.topic: how-to
ms.subservice: calling
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 5ef33ef46b208f66fa7d31b20bb603cd543880f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700303"
---
# <a name="manage-call-recording-on-the-client"></a>Verwalten der Anrufaufzeichnung auf dem Client

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Die [Anrufaufzeichnung](../../concepts/voice-video-calling/call-recording.md) ermöglicht es Ihren Benutzern, ihre Aufrufe mit Azure Communication Services aufzuzeichnen. Hier erfahren Sie, wie Sie die Aufzeichnung auf der Clientseite verwalten. Damit dies funktioniert, müssen Sie zunächst die [serverseitige](../../quickstarts/voice-video-calling/call-recording-sample.md) Aufzeichnung einrichten.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Ein Benutzerzugriffstoken zum Aktivieren des Anrufclients. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Zugriffstoken](../../quickstarts/access-tokens.md).
- Optional: Arbeiten Sie den Schnellstart zum [Hinzufügen von Sprachanrufen zu Ihrer Anwendung](../../quickstarts/voice-video-calling/getting-started-with-calling.md) durch.

::: zone pivot="platform-web"
[!INCLUDE [Record Calls Client-side JavaScript](./includes/record-calls/record-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Record Calls Client-side Android](./includes/record-calls/record-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Record Calls Client-side iOS](./includes/record-calls/record-calls-ios.md)]
::: zone-end

## <a name="next-steps"></a>Nächste Schritte
- [Informationen zum Verwalten von Anrufen](./manage-calls.md)
- [Lernen Sie das Verwalten von Videos](./manage-video.md)
- [Lernen, wie man Anrufe transkribiert](./call-transcription.md)