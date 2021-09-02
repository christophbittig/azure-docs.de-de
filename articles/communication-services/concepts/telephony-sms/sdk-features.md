---
title: Übersicht über das SMS SDK für Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hier finden Sie eine Übersicht über das SMS SDK und seine Funktionen.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5e30f7741352945fb634b2f8066a47f2c47c879b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258133"
---
# <a name="sms-sdk-overview"></a>Übersicht über das SMS SDK

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Mit den Azure Communication Services SMS SDKs können Sie Ihren Anwendungen SMS-Messagingfunktionen hinzufügen.

## <a name="sms-sdk-capabilities"></a>Funktionen der SMS SDKs

Die folgende Liste enthält die Features, die aktuell in unseren SDKs verfügbar sind:

| Featuregruppe | Funktion                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Grundlegende Funktionen | Senden und Empfangen von SMS-Nachrichten                                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Aktivieren von Zustellberichten für gesendete Nachrichten                                             | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Alle Zeichensätze (Sprach-/Unicode-Unterstützung)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Unterstützung langer Nachrichten (bis zu 2.048 Bytes)                                          | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Automatische Verkettung langer Nachrichten                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Senden von Nachrichten an mehrere Empfänger gleichzeitig                                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Unterstützung für Idempotenz                                                               | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Benutzerdefinierte Tags für Nachrichten                                                             | ✔️   | ✔️    | ✔️    | ✔️      |
| Ereignisse            | Verwenden von Event Grid, um Webhooks für den Empfang von eingehenden Nachrichten und Zustellberichten zu konfigurieren | ✔️   | ✔️    | ✔️    | ✔️      |
| Rufnummer      | Gebührenfreie Nummern                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN-Anrufe      | Hinzufügen von PSTN-Anruffunktionen zu Ihrer für SMS geeigneten gebührenfreien Telefonnummer                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Senden einer SMS-Nachricht](../../quickstarts/telephony-sms/send.md)

Die Artikel zu den folgenden Themen könnten Sie auch interessieren:

- Informationen zu allgemeinen [SMS-Konzepten](../telephony-sms/concepts.md)
- Beziehen einer für SMS geeigneten [Telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Telefonnummerntypen in Azure Communication Services](../telephony-sms/plan-solution.md)
