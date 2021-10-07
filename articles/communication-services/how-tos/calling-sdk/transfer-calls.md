---
title: Durchstellen von Anrufen
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden Sie Azure Communication Services-SDKs, um Anrufe durchzustellen.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: e68c81a88c972cc05ffafea0d94e82c84de4268f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700302"
---
# <a name="transfer-calls"></a>Durchstellen von Anrufen

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Während eines aktiven Anrufs können Sie den Anruf an eine andere Person oder Nummer durchstellen. Hier erfahren Sie, wie Sie dazu vorgehen. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Ein Benutzerzugriffstoken zum Aktivieren des Anrufclients. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Zugriffstoken](../../quickstarts/access-tokens.md).
- Optional: Durchlaufen Sie den Schnellstart zum [Hinzufügen von Sprachanrufen zu Ihrer Anwendung](../../quickstarts/voice-video-calling/getting-started-with-calling.md).

[!INCLUDE [Transfer calls Client-side JavaScript](./includes/transfer-calls/transfer-calls-web.md)]

## <a name="next-steps"></a>Nächste Schritte
- [Informationen zum Verwalten von Anrufen](./manage-calls.md)
- [Informationen zum Verwalten von Video](./manage-video.md)
- [Informationen zum Aufzeichnen von Anrufen](./record-calls.md)
- [Informationen zum Transkribieren von Anrufen](./call-transcription.md)