---
title: Abrechnungsmodell des Azure Web PubSub-Dienstes
description: Eine Übersicht über die wichtigsten Konzepte zum Abrechnungsmodell des Azure Web PubSub-Dienstes.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: e7bce9f9c5ac81d7f722e76162f38e4b0178a157
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131994989"
---
# <a name="billing-model-of-azure-web-pubsub-service"></a>Abrechnungsmodell des Azure Web PubSub-Dienstes

Das Abrechnungsmodell für den Azure Web PubSub-Dienst basiert auf der Anzahl der Einheiten und der Größe des ausgehenden Datenverkehrs (Nachrichtenanzahl). In diesem Artikel wird erläutert, wie Einheiten und ausgehender Datenverkehr (Nachrichtenanzahl) definiert und für die Abrechnung gezählt werden.

## <a name="how-units-are-counted-with-billing-model"></a>Wie Einheiten bei dem Abrechnungsmodell gezählt werden

### <a name="what-is-the-connection"></a>Um welche Verbindung handelt es sich?

Die Verbindung zwischen Client und Server. Sie können die Verbindungsanzahl im Azure-Portal überwachen. Die Ansicht „Verbindung (Max)“ zeigt die maximale Anzahl von Verbindungen in einem bestimmten Zeitraum an. 

### <a name="what-is-the-unit"></a>Wie lautet die Einheit?

Die Einheit ist ein abstraktes Konzept der Funktion des Azure Web PubSub-Dienstes. Jede Einheit unterstützt höchstens 1.000 gleichzeitige Verbindungen. Jede Azure Web PubSub-Dienstinstanz kann 1, 2, 5, 10, 20, 50 oder 100 Einheiten umfassen. Die Einheitenanzahl gibt an, wie viele Verbindungen Ihre Instanz des Web PubSub-Dienstes akzeptieren kann.

###  <a name="how-units-are-counted-with-billing-model"></a>Wie Einheiten bei dem Abrechnungsmodell gezählt werden

Die Einheiten werden basierend auf der Anzahl der Einheiten und der Nutzungszeit (Sekunden) der Einheiten gezählt und täglich abgerechnet. 

Angenommen, Sie verfügen beispielsweise über eine Azure Web PubSub-Dienstinstanz mit 5 Einheiten, skalieren von 10:00 bis 16:00 Uhr auf 10 Einheiten hoch und skalieren dann nach 16:00 Uhr wieder auf 5 Einheiten zurück. Es ergeben sich 5 Einheiten für 18 Stunden und 10 Einheiten für 6 Stunden an einem bestimmten Tag.

> Nutzung der Einheiten für die Abrechnung = (5 Einheiten * 18 Stunden + 10 Einheiten * 6 Stunden) / 24 Stunden = 6,25 Einheiten/Tag

## <a name="how-outbound-traffic-is-counted-with-billing-model"></a>Wie ausgehender Datenverkehr mit dem Abrechnungsmodell gezählt wird

### <a name="what-is-inboundoutbound-traffic"></a>Ein-/ausgehender Netzwerkdatenverkehr 

Der ausgehende Datenverkehr ist die Nachricht, die vom Azure Web PubSub-Dienst gesendet wird. Sie können den ausgehenden Datenverkehr über das Azure-Portal überwachen. In der Ansicht „Ausgehender Datenverkehr (Gesamt)“ wird die aggregierte Größe ausgehender Nachrichten (Bytes) in einem bestimmten Zeitraum angezeigt.

- Die Nachrichten, die vom Dienst an Empfänger gesendet werden.
- Die Nachrichten, die vom Dienst an die Upstream-Webhooks gesendet werden.
- Die Ressourcenprotokolle mit dem [Liveablaufverfolgungs-Tool](./howto-troubleshoot-resource-logs.md#capture-resource-logs-with-live-trace-tool). 

Der eingehende Datenverkehr ist die Nachricht, die an den Azure Web PubSub-Dienst gesendet wird. 

- Die Nachrichten, die von Clients an den Dienst gesendet werden.
- Die Nachrichten, die von Server oder Funktionen an den Dienst gesendet werden.

### <a name="what-is-message-count"></a>Was ist die Nachrichtenanzahl?

Die Nachrichtenanzahl zu Abrechnungszwecken ist ein abstraktes Konzept und definiert als Größe des ausgehenden Datenverkehrs (Bytes) unterteilt in 2 KB. Beispielsweise wird Datenverkehr mit 100 KB als 50 Nachrichten gezählt.  

### <a name="how-traffic-is-counted-with-billing-model"></a>Wie ausgehender Datenverkehr mit dem Abrechnungsmodell gezählt wird

Bei der Abrechnung wird nur der ausgehende Datenverkehr gezählt. 

Angenommen, Sie verfügen über eine Anwendung mit dem Azure Web PubSub-Dienst und Azure Functions. Ein Benutzer überträgt 4 KB Daten an 10 Verbindungen in einer Gruppe. Es ergibt 4 KB für Upstream von Dienst zu Funktion und 40 KB für Dienstübertragungen an 10 Verbindungen.

> Ausgehender Datenverkehr für die Abrechnung = 4 KB (Upstreamdatenverkehr) + 4 KB * 10 (Dienstübertragung an Client-Datenverkehr) = 44 KB

> Äquivalente Nachrichtenanzahl = 44 KB / 2 KB = 22

Der Azure Web PubSub-Dienst bietet auch ein tägliches kostenloses Kontingent für ausgehenden Datenverkehr (Nachrichtenanzahl) basierend auf der Nutzung der Einheiten. Der ausgehende Datenverkehr (Nachrichtenanzahl) über das kostenlose Kontingent hinaus ist der zusätzliche ausgehende Datenverkehr (zusätzliche Nachrichten). Beispielsweise beträgt das kostenlose Kontingent 2.000.000 KB ausgehender Datenverkehr (1.000.000 Nachrichten) pro Einheit/Tag.

Im vorherigen Beispiel für die Einheitennutzung verwendet die Anwendung 6,25 Einheiten pro Tag, um das tägliche kostenlose Kontingent von 12.500.000 KB ausgehenden Datenverkehr (6,25 Millionen Nachrichten) zu gewährleisten. Die Abbildung des täglichen ausgehenden Datenverkehrs beträgt 30.000.000 KB (15 Millionen Nachrichten). Die zusätzlichen Nachrichten sind 17.500.000 KB ausgehender Datenverkehr (8,75 Millionen Nachrichten). Daher werden Ihnen 6,25 Standardeinheiten und 8,75 zusätzliche Nachrichteneinheiten für den Tag in Rechnung gestellt.

## <a name="pricing"></a>Preise 

Der Azure Web PubSub-Dienst bietet mehrere Tarife mit unterschiedlichen Preisen. Wenn Sie wissen, wie die Anzahl der Einheiten und die Größe des ausgehenden Datenverkehrs (Nachrichtenanzahl) mit dem Abrechnungsmodell gezählt werden, können Sie weitere Preisdetails unter [Azure Web PubSub service pricing (Preise für den Azure Web PubSub-Dienst)](https://azure.microsoft.com/pricing/details/web-pubsub) erhalten.





