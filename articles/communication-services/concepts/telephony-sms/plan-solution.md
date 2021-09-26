---
title: Telefonnummerntypen in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hier erfahren Sie, wie Sie verschiedene Arten von Telefonnummern für SMS oder Telefonie verwenden.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.custom: references_regions
ms.service: azure-communication-services
ms.subservice: pstn
ms.openlocfilehash: 879dcefc787185b51ab6019c1ad8e2a5f8befb16
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636000"
---
# <a name="phone-number-types-in-azure-communication-services"></a>Telefonnummerntypen in Azure Communication Services

> [!IMPORTANT]
> Derzeit ist die Verfügbarkeit von Telefonnummern auf kostenpflichtige Azure-Abonnements, für die eine Abrechnungsadresse in den USA festgelegt ist, und auf Communication Services-Ressourcen beschränkt, deren Daten in den USA gespeichert werden. Telefonnummern können nicht mit Testkonten oder kostenlosem Azure-Guthaben erworben werden. Weitere Informationen finden Sie in diesem Dokument im Abschnitt [Abonnementberechtigung](#azure-subscriptions-eligibility).


Mit Azure Communication Services können Sie Telefonnummern verwenden, um Sprachanrufe zu tätigen und SMS-Nachrichten über das Telefonfestnetz (Public Switched Telephone Network, PSTN) zu versenden. In diesem Dokument werden die Telefonnummerntypen, Konfigurationsoptionen und die regionale Verfügbarkeit für die Planung Ihrer Telefonie- und SMS-Lösung mithilfe von Communication Services überprüft.

## <a name="azure-subscriptions-eligibility"></a>Berechtigung für Azure-Abonnements

Für den Erhalt einer Telefonnummer müssen Sie ein kostenpflichtiges Azure-Abonnement abgeschlossen haben. Telefonnummern können mit Testkonten oder kostenlosem Azure-Guthaben nicht erworben werden.

Derzeit ist die Verfügbarkeit von Telefonnummern auf Azure-Abonnements, für die eine Abrechnungsadresse in den USA festgelegt ist, und auf Communication Services-Ressourcen beschränkt, deren Daten in den USA gespeichert werden.


## <a name="number-types-and-features"></a>Typen und Features von Telefonnummern
Communication Services bietet zwei Arten von Telefonnummern an: **lokale** und **gebührenfreie**.

### <a name="local-numbers"></a>Lokale Nummern
Lokale (geografische) Nummern sind 10-stellige Telefonnummern, die aus den Ortsvorwahlen in den USA bestehen. Beispiel: `+1 (206) XXX-XXXX` ist eine lokale Nummer mit einer Ortsvorwahl von `206`. Diese Vorwahl ist der Stadt Seattle zugeordnet. Diese Telefonnummern werden im Allgemeinen von Einzelpersonen und lokalen Unternehmen verwendet. Azure Communication Services bietet lokale Nummern in den USA an. Über diese Nummern können Sie telefonieren, aber keine SMS-Nachrichten versenden.

### <a name="toll-free-numbers"></a>Gebührenfreie Nummern
Gebührenfreie Nummern sind 10-stellige Telefonnummern mit unterschiedlichen Vorwahlen, die von jeder Telefonnummer aus gebührenfrei angerufen werden können. Beispiel: `+1 (800) XXX-XXXX` ist eine gebührenfreie Nummer in der Region Nordamerika. Diese Telefonnummern werden im Allgemeinen für Kundendienstzwecke verwendet. Azure Communication Services bietet gebührenfreie Nummern in den USA an. Über diese Nummern können Sie telefonieren und SMS-Nachrichten senden. Gebührenfreie Nummern können nicht von Personen verwendet, sondern nur Anwendungen zugewiesen werden.

#### <a name="choosing-a-phone-number-type"></a>Auswählen eines Telefonnummerntyps

Wenn Ihre Telefonnummer von einer Anwendung verwendet wird (z. B. um Anrufe zu tätigen oder Nachrichten im Auftrag Ihres Diensts zu senden), können Sie eine gebührenfreie oder lokale (geografische) Nummer auswählen. Sie können eine gebührenfreie Nummer auswählen, wenn Ihre Anwendung SMS-Nachrichten versendet und/oder Anrufe tätigt.

Wenn Ihre Telefonnummer von einer Person (z. B. einem Benutzer Ihrer anrufenden Anwendung) verwendet wird, muss die lokale (geografische) Telefonnummer verwendet werden.

In der folgenden Tabelle sind diese Telefonnummerntypen zusammengefasst:

| Telefonnummerntyp | Beispiel                              | Länderverfügbarkeit    | Telefonnummernfähigkeit |Gängiger Anwendungsfall                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| Lokal (geografisch)        | +1 (Ortsvorwahl) XXX XX XX  | US                      | Anrufe (ausgehend) | Zuweisen von Telefonnummern zu Benutzern in Ihren Anwendungen  |
| Gebührenfrei         | +1 (gebührenfreie *Ortsvorwahl*) XXX XX XX | US                      | Anrufe (ausgehend), SMS (eingehend/ausgehend)| Zuweisen von Telefonnummern zu IVR-Systemen (Interactive Voice Response, Interaktive Sprachantwort) oder Bots, SMS-Anwendungen                                        |


### <a name="phone-number-capabilities-in-azure-communication-services"></a>Telefonnummernfunktionen in Azure Communication Services

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]

Für die meisten Telefonnummern können die Funktionen beliebig zusammengestellt werden. Diese Funktionen können ausgewählt werden, wenn Sie Ihre Telefonnummern in Azure Communication Services leasen.

Welche Funktionen Ihnen zur Verfügung stehen, hängt von dem Land ab, in dem Sie tätig sind, von Ihrem Anwendungsfall und von der Art der Telefonnummer, die Sie gewählt haben. Diese Funktionen variieren aufgrund der rechtlichen Anforderungen je nach Land/Region. Azure Communication Services bietet die folgenden Telefonnummernfunktionen:

- **Unidirektionale ausgehende SMS:** Diese Option ermöglicht es Ihnen, SMS-Nachrichten an Ihre Benutzer zu senden. Sie ist nützlich für die Benachrichtigung und Szenarien mit zweistufiger Authentifizierung.
- **Bidirektionale ein- und ausgehende SMS:** Diese Option ermöglicht es Ihnen, Nachrichten von Ihren Benutzern mithilfe von Telefonnummern zu senden und zu empfangen. Diese Option ist in Kundendienstszenarien hilfreich.
- **Unidirektionale ausgehende Telefonanrufe:** Diese Option ermöglicht es Ihnen, Ihre Benutzer anzurufen und die Anrufer-ID für ausgehende Anrufe, die von Ihrem Dienst getätigt werden, zu konfigurieren. Diese Option ist in Kundendienst- und Sprachbenachrichtigungsszenarien hilfreich.

## <a name="countryregion-availability"></a>Verfügbarkeit in Land/Region

In der folgenden Tabelle wird gezeigt, wo Sie verschiedene Telefonnummerntypen sowie die mit diesen Telefonnummerntypen verbundenen ein- und ausgehenden Anruf- und SMS-Features abrufen können.

|Nummerntyp| Abrufen von Nummern in | Tätigen von Anrufen nach                                        | Empfangen von Anrufen aus*                                    |Senden von Nachrichten nach       | Empfangen von Nachrichten aus |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| Lokal (geografisch)  | US                 | USA, Kanada, Vereinigtes Königreich, Deutschland, Frankreich, +weitere**| USA, Kanada, Vereinigtes Königreich, Deutschland, Frankreich, +weitere** |Nicht verfügbar| Nicht verfügbar |
| Gebührenfrei | US                 | US                                                   | US                                                    |US                | US |

*Derzeit können Sie nur Anrufe an eine Microsoft-Nummer empfangen, die einem Telefoniekanalbot zugewiesen ist. Weitere Informationen zum Telefoniekanal finden Sie [here](/azure/bot-service/bot-service-channel-connect-telephony) **Weitere Details zu Anrufzielen und Preisen finden Sie auf der [Seite mit der Preisübersicht](../pricing.md).


## <a name="next-steps"></a>Nächste Schritte

### <a name="quickstarts"></a>Schnellstarts

- [Beschaffen einer Telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Tätigen eines Anrufs](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Senden einer SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Dokumentation

- [Sprach- und Videokonzepte](../voice-video-calling/about-call-types.md)
- [Telefoniekonzepte](./telephony-concept.md)
- [Anrufabläufe](../call-flows.md)
- [Preise](../pricing.md)
