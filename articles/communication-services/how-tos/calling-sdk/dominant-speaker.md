---
title: Aktivlautsprecher werden
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden Sie Azure Communication Services SDKs, um die aktiven Sprecher in einem Anruf zu rendern.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: cb2391db2f24eaf4dca4e0463ab508b27361fae0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700192"
---
# <a name="get-active-speakers-within-a-call"></a>Aktive Sprecher innerhalb eines Anrufs abfragen

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Während eines aktiven Anrufs können Sie eine Liste der aktiven Sprecher abrufen, um sie anders darzustellen oder anzuzeigen. Gehen Sie folgendermaßen vor:

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Ein Benutzerzugriffstoken zum Aktivieren des Anrufclients. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Zugriffstoken](../../quickstarts/access-tokens.md).
- Optional: Füllen Sie den Schnellstart aus, um [Ihrer Anwendung Sprachanrufe hinzuzufügen](../../quickstarts/voice-video-calling/getting-started-with-calling.md).

[!INCLUDE [Dominant Speaker JavaScript](./includes/dominant-speaker/dominant-speaker-web.md)]

## <a name="next-steps"></a>Nächste Schritte
- [Lernen Sie, wie man Videos verwaltet](./manage-video.md)
- [Lernen Sie, wie man Anrufe verwaltet](./manage-calls.md)
- [Lernen Sie das Aufzeichnen von Anrufen](./record-calls.md)