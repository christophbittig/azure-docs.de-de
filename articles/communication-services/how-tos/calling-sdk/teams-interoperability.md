---
title: Teilnehmen an einer Teams-Besprechung
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden Sie Azure Communication Services-SDKs zum Teilnehmen an einer Teams-Besprechung.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: 0b2469def75cc54efc9efced2c25587b466f4cf0
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063625"
---
# <a name="join-a-teams-meeting"></a>Teilnehmen an einer Teams-Besprechung

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Azure Communication Services-SDKs können Ihren Benutzern ermöglichen, an regelmäßigen Microsoft Teams-Besprechungen teilzunehmen. Gehen Sie dabei folgendermaßen vor:

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Ein Benutzerzugriffstoken zum Aktivieren des Anrufclients. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Zugriffstoken](../../quickstarts/access-tokens.md).
- Optional: Durchlaufen Sie den Schnellstart zum [Hinzufügen von Sprachanrufen zu Ihrer Anwendung](../../quickstarts/voice-video-calling/getting-started-with-calling.md).

> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Betarelease des ACS Calling Web SDK.

Verwenden Sie für die Teilnahme an einer Teams-Besprechung die Methode `join`, und übergeben Sie einen Besprechungslink oder die Daten einer Besprechung.

Teilnehmen über einen Besprechungslink:

```js
const locator = { meetingLink: '<MEETING_LINK>'}
const call = callAgent.join(locator);
```

Teilnehmen über Besprechungsdaten:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

Teilnehmen mithilfe der Besprechungs-ID (dies befindet sich derzeit in der eingeschränkten Vorschauphase):

```js
const locator = { meetingId: '<MEETING_ID>'}
const call = callAgent.join(locator);
```

## <a name="next-steps"></a>Nächste Schritte
- [Informationen zum Verwalten von Anrufen](./manage-calls.md)
- [Informationen zum Verwalten von Video](./manage-video.md)
- [Informationen zum Durchstellen von Anrufen](./transfer-calls.md)
