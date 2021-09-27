---
title: SMS FAQ
titleSuffix: An Azure Communication Services concept document
description: SMS FAQ
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: 7b10040aa8aabd16d84326c73bd422f9b66ef04e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635981"
---
# <a name="sms-faq"></a>SMS FAQ

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>Können Kunden Azure-Kommunikationsdienste zu Notfallzwecken verwenden?

Azure Communication Services unterstützt die Text-to-911-Funktionalität in den Vereinigten Staaten nicht, aber es ist möglich, dass Sie gemäß den Regeln der Federal Communications Commission (FCC) dazu verpflichtet sind.  Sie sollten prüfen, ob die Text-to-911-Regeln der FCC für Ihren Dienst oder Ihre Anwendung gelten. In dem Umfang, in dem Sie von diesen Regeln abgedeckt werden, sind Sie dafür verantwortlich, 911-Textnachrichten an die Notfall-Callcenter weiterzuleiten, die Sie anfordern. Es steht Ihnen frei, Ihr eigenes Text-to-911-Zustellungsmodell zu bestimmen, aber ein von der FCC akzeptierter Ansatz beinhaltet den automatischen Start des nativen Dialers auf dem mobilen Gerät des Benutzers, um 911-Texte über den zugrunde liegenden Mobilfunkanbieter zuzustellen.

## <a name="are-there-any-limits-on-sending-messages"></a>Gibt es Grenzwerte für das Senden von Nachrichten?

Um sicherzustellen, dass wir weiterhin die hohe Qualität des Service anbieten, die mit unseren SLAs konsistent ist, wendet Azure Communication Services die Begrenzung der Datenübertragungsrate an (unterschiedlich für jedes Primitive). Entwickler, die unsere APIs über den Grenzwert hinaus anrufen, erhalten die Antwort 429 HTTP-Status Code. Wenn Ihr Unternehmen Anforderungen hat, die die Begrenzung der Datenübertragungsrate überschreiten, senden Sie eine E-Mail an phone@microsoft.com .

Begrenzung der Datenübertragungs Rate für SMS:

|Vorgang|Bereich|Zeitraum (Zeiträume)| Limit (Anforderung #) | Nachrichteneinheiten pro Minute|
|---------|-----|-------------|-------------------|-------------------------|
|Nachricht senden|Pro Zahl|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Wie behandelt Azure Communication Services Opt-Outs für gebührenfreie Nummern?

Abmeldungen für gebührenfreie Nummern in den USA werden von US-Netzbetreibern vorgeschrieben und durchgesetzt.
- STOP – Wenn ein SMS-Empfänger sich abmelden möchte, kann er „STOP“ an die gebührenfreie Nummer senden. Der Netzbetreiber sendet die folgende Standardantwort für STOP: „NETZWERK-MSG: Sie haben mit dem Wort „stop“ geantwortet, wodurch alle von dieser Nummer gesendeten Texte gesperrt werden. Antworten Sie mit „unstop“, um wieder Nachrichten zu erhalten.
- START/UNSTOP – Wenn der Empfänger Textnachrichten von einer gebührenfreien Nummer erneut abonnieren möchte, kann er „START“ oder „UNSTOP“ an die gebührenfreie Nummer senden. Der Netzbetreiber sendet die folgende Standardantwort für „START/UNSTOP“: „NETZWERKNACHRICHT: You have replied „unstop“ and will begin receiving messages again from this number.“ (Sie haben mit „unstop“ geantwortet und werden wieder Nachrichten von dieser Nummer erhalten.)
- Azure Communication Services erkennt die STOP-Nachricht und blockiert alle weiteren Nachrichten an den Empfänger. Im Zustellbericht wird eine fehlerhafte Zustellung mit der Statusmeldung „Sender blocked for given recipient“ (Absender für bestimmten Empfänger gesperrt) angezeigt.
- Die STOP-, UNSTOP- und START-Nachrichten werden an Sie weitergeleitet. Von Azure Communication Services wird die Überwachung und Implementierung dieser Abmeldungen empfohlen, um sicherzustellen, dass nicht weiter versucht wird, Nachrichten an Empfänger zu senden, die sich von Ihrer Kommunikation abgemeldet haben.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Wie kann ich Nachrichten mit Azure Communication Services empfangen?

Azure Communication Services-Kunden können Azure Event Grid verwenden, um eingehende Nachrichten zu empfangen. Führen Sie diese [Schnellstart](../../quickstarts/telephony-sms/handle-sms-events.md) Anleitung aus, um Ihr Event Grid für den Empfang von Nachrichten einzurichten.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>Kann ich lange Nachrichten senden/empfangen (>2048 Zeichen)?

Azure Communication Services unterstützt das Senden und empfangen von langen Nachrichten über SMS. Allerdings können einige drahtlos Träger oder Geräte beim Empfang von langen Nachrichten anders agieren.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>Wie werden die Nachrichten an die Festnetznummern behandelt?

In Nordamerika prüft Azure Communication Services nicht auf Festnetznummern und versucht, die Zustellung an Carrier zu senden. Kunden werden in Rechnung gestellt, dass Nachrichten an die Festnetznummern gesendet werden. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>Kann ich Nachrichten an mehrere Empfänger senden?


Ja, Sie können eine Anfrage mit mehreren Empfängern stellen. Befolgen Sie diese [Schnellstart](../../quickstarts/telephony-sms/send.md?pivots=programming-language-csharp)-Anleitung, um Nachrichten an mehrere Empfänger zu senden.

##  <a name="i-received-a-http-status-202-from-the-send-sms-api-but-the-sms-didnt-reach-my-phone-what-do-i-do-now"></a>Ich habe über die API zum Senden einer SMS den HTTP-Status 202 empfangen, aber keine SMS auf meinem Telefon erhalten – was kann ich tun?

Der vom Dienst zurückgegebene Status 202 bedeutet, dass Ihre Nachricht zur Übermittlung in die Warteschlange eingereiht und nicht zugestellt wurde. Nutzen Sie diesen [Schnellstart](../../quickstarts/telephony-sms/handle-sms-events.md), um Ereignisse im Zusammenhang mit Zustellberichten zu abonnieren und eine Problembehandlung durchzuführen. Nach der Konfiguration der Ereignisse können Sie anhand des Felds „deliveryStatus“ Ihres Zustellberichts überprüfen, ob die Übermittlung erfolgreich war oder nicht.
