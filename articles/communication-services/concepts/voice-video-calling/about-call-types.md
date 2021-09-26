---
title: Sprach- und Videokonzepte in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie Informationen zu Anruftypen von Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 9fd2de1bdcd89f027db9a3fd74ef40111f540ced
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635962"
---
# <a name="voice-and-video-concepts"></a>Sprach- und Videokonzepte

[!INCLUDE [SDP Plan B Deprecation Notice](../../includes/plan-b-sdp-deprecation.md)]

Mit Azure Communication Services können Sie Sprach- und Videoanrufe (1:1 oder in der Gruppe) tätigen und empfangen. Es können sowohl andere Geräte mit Internetverbindung als auch herkömmliche Telefone angerufen werden. Mit den JavaScript, Android oder iOS SDKs von Communication Services können Sie Anwendungen erstellen, die es Benutzern ermöglichen, sich privat oder in Gruppendiskussionen miteinander zu unterhalten. Von Azure Communication Services werden aus- und eingehende Anrufe mit Diensten oder Bots unterstützt.

## <a name="call-types-in-azure-communication-services"></a>Anruftypen in Azure Communication Services

In Azure Communication Services können verschiedene Arten von Anrufen verwendet werden. Die Art der verwendeten Anrufe hat Auswirkungen auf das Signalschema, auf die Datenverkehrsflüsse für Medien sowie auf das Preismodell.

### <a name="voice-over-ip-voip"></a>Sprachtelefonie über IP (Voice over IP, VoIP)

Wenn ein Benutzer Ihrer Anwendung einen anderen Benutzer Ihrer Anwendung über eine Internet- oder Datenverbindung anruft, wird der Anruf über Voice over IP (VoIP) getätigt. In diesem Fall werden sowohl Signale als auch Medien über das Internet übertragen.

### <a name="public-switched-telephone-network-pstn"></a>Telefonfestnetz (Public Switched Telephone Network, PSTN)

Wenn Ihre Benutzer mit einer herkömmlichen Telefonnummer interagieren, werden Anrufe als Sprachanrufe über das Telefonfestnetz (Public Switched Telephone Network, PSTN) abgewickelt. Für aus- und eingehende PSTN-Anrufe müssen Ihrer Azure Communication Services-Ressource Telefoniefunktionen hinzugefügt werden. In diesem Fall wird für Signale und Medien eine Kombination aus IP- und PSTN-basierten Technologien verwendet, um die Verbindung zwischen den Benutzern herzustellen.

### <a name="one-to-one-call"></a>1:1-Anruf

Ein 1:1-Anruf in Azure Communication Services erfolgt, wenn einer Ihrer Benutzer unter Verwendung eines unserer SDKs eine Verbindung mit einem anderen Benutzer herstellt. Bei dem Anruf kann es sich um einen VoIP- oder um einen PSTN-Anruf handeln.

### <a name="group-call"></a>Gruppenanruf

Ein Gruppenanruf in Azure Communication Services erfolgt, wenn mindestens drei Teilnehmer eine Verbindung miteinander herstellen. An einem Gruppenanruf kann eine beliebige Kombination aus Benutzern mit VoIP- oder PSTN-Verbindung teilnehmen. Ein 1:1-Anruf kann durch Hinzufügen weiterer Teilnehmer in einen Gruppenanruf konvertiert werden. Einer dieser Teilnehmer kann ein Bot sein.

### <a name="supported-video-standards"></a>Unterstützte Videostandards
Wir unterstützen H.264 (MPEG-4).

### <a name="video-quality"></a>Videoqualität
In den nativen SDKs (iOS, Android) wird maximal Full HD mit 1080p unterstützt. Für das Web SDK (JS) wird Standard HD mit 720p unterstützt. Die Qualität hängt von der verfügbaren Bandbreite ab.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit Anrufen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Weitere Informationen finden Sie in den folgenden Artikeln:
- Machen Sie sich mit allgemeinen [Anrufflows](../call-flows.md) vertraut.
- [Telefonnummerntypen](../telephony-sms/plan-solution.md)
- Informieren Sie sich über die [Funktionen des Calling SDK](../voice-video-calling/calling-sdk-features.md).
