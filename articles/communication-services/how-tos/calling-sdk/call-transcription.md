---
title: Anzeige des Anruftranskriptionstatus auf dem Client
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden Sie Azure Communication Services SDKs, um den Status der Anruftranskription anzuzeigen
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 9304f7905b12362f8026e59af8cf294ed76fc42e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635739"
---
# <a name="display-call-transcription-state-on-the-client"></a>Anzeige des Anruftranskriptionstatus auf dem Client

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Wenn Sie die Anruftranskription verwenden, möchten Sie Ihre Benutzer vielleicht wissen lassen, dass ein Anruf gerade transkribiert wird. Gehen Sie folgendermaßen vor:

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Ein Benutzerzugriffstoken zum Aktivieren des Anrufclients. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Zugriffstoken](../../quickstarts/access-tokens.md).
- Optional: Füllen Sie den Schnellstart aus, um [Sprachanrufe zu Ihrer Anwendung hinzuzufügen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-android"
[!INCLUDE [Call transcription client-side Android](./includes/call-transcription/call-transcription-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Call transcription client-side iOS](./includes/call-transcription/call-transcription-ios.md)]
::: zone-end

## <a name="next-steps"></a>Nächste Schritte
- [Lernen Sie das Verwalten von Videos](./manage-video.md)
- [Lernen Sie das Verwalten von Anrufen](./manage-calls.md)
- [Lernen Sie das Aufzeichnen von Anrufen](./record-calls.md)