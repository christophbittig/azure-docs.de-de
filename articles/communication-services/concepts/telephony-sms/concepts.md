---
title: SMS-Konzepte in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie Informationen zu SMS-Konzepten in Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 036a3de91d39337353eebf7c3d46b1c659fe7cd5
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255310"
---
# <a name="sms-concepts"></a>SMS-Konzepte

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services ermöglicht das Senden und Empfangen von SMS-Textnachrichten unter Verwendung der Communication Services SMS SDKs. Diese SDKs können zur Unterstützung von Kundendienstszenarien, Terminerinnerungen, zweistufiger Authentifizierung und anderer Echtzeitkommunikationsanforderungen verwendet werden. Das SMS-Feature von Communication Services ermöglicht das zuverlässige Senden von Nachrichten sowie die Bereitstellung von Zustellbarkeits- und Antwortmetriken.

Zu den wichtigsten Features der Azure Communication Services SMS SDKs gehören folgende:

-  **Unkomplizierte Einrichtungsumgebung** zum Hinzufügen von SMS-Funktionen zu Ihren Anwendungen
- **Unterstützung von Hochgeschwindigkeitsnachrichten** über gebührenfreie Nummern für A2P-Anwendungsfälle (Application to Person) in den USA
- Unterstützung von **Massennachrichten**, um das gleichzeitige Senden von Nachrichten an mehrere Empfänger zu ermöglichen
- **Bidirektionale Konversationen** zur Unterstützung von Szenarien wie Kundensupport, Warnungen und Terminerinnerungen
- **Zuverlässige Zustellung** mit Zustellberichten in Echtzeit für Nachrichten, die von Ihrer Anwendung gesendet wurden
- **Analysen** zur Nachverfolgung von SMS-Verwendungsmustern
- **Unterstützung von Abmeldungen**, um Abmeldungen für gebührenfreie Nummern automatisch zu erkennen und zu berücksichtigen. Abmeldungen für gebührenfreie Nummern in den USA werden von US-Netzbetreibern vorgeschrieben und durchgesetzt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Senden einer SMS-Nachricht](../../quickstarts/telephony-sms/send.md)

Die Artikel zu den folgenden Themen könnten Sie auch interessieren:

- Machen Sie sich mit dem [SMS SDK](../telephony-sms/sdk-features.md) vertraut.
- Beziehen einer für SMS geeigneten [Telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Telefonnummerntypen in Azure Communication Services](../telephony-sms/plan-solution.md)
