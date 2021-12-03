---
title: 'Azure Service Bus: Ablauf von Nachrichten'
description: In diesem Artikel werden der Ablauf und die Gültigkeitsdauer von Azure Service Bus-Nachrichten behandelt. Nach einer solchen Frist wird die Nachricht nicht mehr zugestellt.
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: 5534a2ee94db118f4429eaec2409683f68fa5547
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460525"
---
# <a name="message-expiration-time-to-live"></a>Nachrichtenablauf (Gültigkeitsdauer)
Die Nutzlast in einer Nachricht oder die in einer Nachricht an einen Empfänger übermittelten Befehle/Anforderungen unterliegen fast immer einer Form von Ablauffrist auf Anwendungsebene. Nach Ablauf einer solchen Frist wird der Inhalt nicht mehr zugestellt oder der gewünschte Vorgang nicht mehr ausgeführt.

Für Entwicklungs- und Testumgebungen, in denen Warteschlangen und Themen häufig im Rahmen von Teilläufen von Anwendungen oder Anwendungsteilen verwendet werden, ist es auch wünschenswert, dass nicht zugestellte Testnachrichten automatisch gesammelt werden, damit der nächste Testlauf davon unbeeinflusst starten kann.

Der Ablauf jeder einzelnen Nachricht kann durch das Festlegen der Systemeigenschaft **time-to-live** gesteuert werden, die eine relative Dauer angibt. Der Ablauf wird zu einem absoluten Zeitpunkt, an dem die Nachricht in die Warteschlange der Entität gestellt wird. Zu diesem Zeitpunkt nimmt die Eigenschaft **expires-at-utc** den Wert **enqueued-time-utc** + **time-to-live** an. Die Einstellung für die Gültigkeitsdauer (time-to-live, TTL) einer im Broker gespeicherten Nachricht wird nicht erzwungen, wenn keine Clients aktiv lauschen.

Nach Ablauf des **expires-at-utc**-Zeitpunkts können Nachrichten nicht mehr abgerufen werden. Die Ablaufzeit wirkt sich nicht auf Nachrichten aus, die momentan für die Zustellung gesperrt sind. Diese Nachrichten werden weiterhin normal behandelt. Wenn die Sperre abläuft oder die Nachricht abgebrochen wird, tritt der Ablauf sofort in Kraft.

Solange die Nachricht gesperrt ist, kann die Anwendung möglicherweise im Besitz einer Nachricht sein, die abgelaufen ist. Ob die Anwendung bereit ist, mit der Verarbeitung fortzufahren oder ob die Nachricht abgebrochen wird, bleibt dem Implementierer überlassen.

Eine extrem niedrige Gültigkeitsdauer (TTL) im Bereich von Millisekunden oder Sekunden kann dazu führen, dass Nachrichten ablaufen, bevor Empfängeranwendungen diese empfangen können. Erwägen Sie die höchste Gültigkeitsdauer, die für Ihre Anwendung funktioniert.

## <a name="entity-level-expiration"></a>Ablauf auf Entitätsebene
Alle Nachrichten, die in eine Warteschlange oder an ein Thema gesendet werden, unterliegen einer Standardablaufzeit, die auf Entitätsebene festgelegt wird. Sie kann auch im Portal während der Erstellung festgelegt und später angepasst werden. Die Standardablaufzeit wird für alle an die Entität gesendeten Nachrichten verwendet, bei denen time-to-live nicht explizit festgelegt ist. Die Standardablaufzeit dient auch als Obergrenze für den time-to-live-Wert. Nachrichten, die eine längere time-to-live-Ablaufzeit als der Standardwert haben, werden automatisch an den time-to-live-Standardnachrichtenwert angepasst, bevor sie in die Warteschlange eingereiht werden.

> [!NOTE]
> - Der Standardwert für die Gültigkeitsdauer (Time-To-Live, TTL) für eine im Broker gespeicherte Nachricht ist der größte mögliche Wert für eine ganze 64-Bit-Zahl mit Vorzeichen, wenn nichts anderes angegeben ist.
> - Für Messagingentitäten (Warteschlangen und Themen) ist die Standardablaufzeit ebenfalls der größte mögliche Wert für eine ganze 64-Bit-Zahl mit Vorzeichen bei den Service Bus-Dienstebenen „Standard“ und „Premium“. Im Tarif **Basic** beträgt die Standardablaufzeit **14 Tage** (dies ist auch der Höchstwert).
> - Wenn das Thema einen kleineren TTL-Wert als das Abonnement angibt, wird der TTL-Wert des Themas angewendet.

Abgelaufene Nachrichten können optional in eine [Warteschlange für unzustellbare Nachrichten](service-bus-dead-letter-queues.md) verschoben werden. Sie können diese Einstellung programmgesteuert oder mithilfe des Azure-Portals konfigurieren. Wenn die Option deaktiviert bleibt, werden abgelaufene Nachrichten verworfen. Abgelaufene Nachrichten, die in die Warteschlange für unzustellbare Nachrichten verschoben werden, können durch die Auswertung der [dead-letter reason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq)-Eigenschaft, die der Broker in den Benutzereigenschaften speichert, von anderen unzustellbaren Nachrichten unterschieden werden. 

In dem oben genannten Fall, in dem die Nachricht vor dem Ablaufen geschützt ist, solange sie gesperrt ist und das Flag für die Entität festgelegt ist, wird die Nachricht in die Warteschlange für unzustellbare Nachrichten verschoben, sobald die Sperre aufgehoben wird oder abläuft. Sie wird jedoch nicht verschoben, wenn die Nachricht erfolgreich verarbeitet wurde. Dann wird davon ausgegangen, dass die Anwendung sie trotz des nominalen Ablaufs erfolgreich abgearbeitet hat.

Die Kombination aus time-to-live und automatischer (und transaktionaler) Funktion für unzustellbare Nachrichten bei Ablauf ist ein wertvolles Instrument, um Vertrauen bei der Frage zu schaffen, ob ein Auftrag, der einem Handler oder einer Gruppe von Handlern innerhalb einer Frist erteilt wurde, bei Erreichen der Frist zur Verarbeitung abgerufen wird.

Stellen Sie sich beispielsweise eine Website vor, die Aufträge zuverlässig in einem Back-End mit eingeschränkter Skalierung ausführen muss, und bei der es gelegentlich zu Datenverkehrsspitzen kommt oder die von Verfügbarkeitszeiträumen dieses Back-Ends isoliert werden soll. Im Regelfall verschiebt der serverseitige Handler für die übermittelten Benutzerdaten die Informationen in eine Warteschlange und erhält anschließend eine Antwort, die die erfolgreiche Verarbeitung der Transaktion in einer Antwortwarteschlange bestätigt. Wenn es eine Datenverkehrsspitze gibt und der Back-End-Handler seine Backlogelemente nicht rechtzeitig verarbeiten kann, werden die abgelaufenen Aufträge an die Warteschlange für unzustellbare Nachrichten zurückgegeben. Der interaktive Benutzer kann benachrichtigt werden, dass der angeforderte Vorgang etwas länger als gewöhnlich dauert. Die Anforderung kann dann in eine andere Warteschlange für einen Verarbeitungspfad gestellt werden, woraufhin das endgültige Verarbeitungsergebnis per E-Mail an den Benutzer gesendet wird. 


## <a name="temporary-entities"></a>Temporäre Entitäten

Service Bus-Warteschlangen, -Themen und -Abonnements können als temporäre Entitäten erstellt werden, die automatisch entfernt werden, sobald sie für einen angegebenen Zeitraum nicht verwendet wurden.
 
Die automatische Bereinigung ist in Entwicklungs- und Testszenarien sinnvoll, in denen Entitäten dynamisch erstellt und nach der Nutzung nicht bereinigt werden, da der Test- oder Debuglauf unterbrochen wurde. Sie ist auch nützlich, wenn eine Anwendung dynamische Entitäten erstellt, wie z.B. eine Antwortwarteschlange, um Antworten wieder in einen Webserverprozess oder in einem anderen relativ kurzlebigen Objekt zu empfangen. In diesem Fall ist es schwierig, diese Entitäten zuverlässig zu bereinigen, wenn die Objektinstanz nicht mehr vorhanden ist.

Die Funktion wird mit der Eigenschaft **Automatisches Löschen bei Leerlauf** für den Namespace aktiviert. Diese Eigenschaft wird auf die Dauer festgelegt, die eine Entität inaktiv (im Leerlauf) sein muss, bevor sie automatisch gelöscht wird. Der Mindestwert für diese Eigenschaft lautet 5 Minuten.
 
## <a name="idleness"></a>Leerlauf

In den folgenden Situationen werden Entitäten als inaktiv (im Leerlauf befindlich) betrachtet:

- Warteschlangen
    - Keine Sendevorgänge  
    - Keine Empfangsvorgänge  
    - Keine Aktualisierungen der Warteschlange  
    - Keine geplanten Nachrichten  
    - Keine Suchvorgänge/Vorschau 
- Themen  
    - Keine Sendevorgänge  
    - Keine Aktualisierungen des Themas  
    - Keine geplanten Nachrichten 
    - Keine Vorgänge für Abonnements des Themas (wie unten dargestellt)
- Abonnements
    - Keine Empfangsvorgänge  
    - Keine Aktualisierungen des Abonnements  
    - Keine Hinzufügung neuer Regeln zum Abonnement  
    - Keine Suchvorgänge/Vorschau  
 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie in folgenden Themen:

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)
