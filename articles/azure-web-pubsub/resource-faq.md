---
title: Häufig gestellte Fragen zum Azure Web PubSub-Dienst
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure Web PubSub.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 11/11/2021
ms.openlocfilehash: dc8dd5e25a6b6aeb94d674156af2edd9dbcd008d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345601"
---
# <a name="azure-web-pubsub-service-faq"></a>Häufig gestellte Fragen zum Azure Web PubSub-Dienst

Hier finden Sie häufig gestellte Fragen zum Azure Web PubSub-Dienst. 

## <a name="is-azure-web-pubsub-service-ready-for-production-use"></a>Ist Azure Web PubSub bereit für den Einsatz in der Produktion?
Ja, der Azure Web PubSub-Dienst ist allgemein verfügbar.

## <a name="how-do-i-choose-between-azure-signalr-service-and-azure-web-pubsub-service"></a>Nach welchen Kriterien wähle ich zwischen Azure SignalR Service und dem Azure Web PubSub-Dienst?

Sowohl [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service) als auch der [Azure Web PubSub-Dienst](https://azure.microsoft.com/services/web-pubsub) helfen Kunden beim einfachen Erstellen von Echtzeitwebanwendungen mit hoher Skalierbarkeit und Verfügbarkeit, und ermöglichen es Kunden, sich auf ihre Geschäftslogik zu konzentrieren, anstatt die Messaginginfrastruktur zu verwalten. Im Allgemeinen können Sie Azure SignalR Service wählen, wenn Sie bereits die SignalR-Bibliothek zum Erstellen von Echtzeitanwendungen verwenden. Wenn Sie stattdessen nach einer generischen Lösung suchen, um Echtzeitanwendungen basierend auf WebSocket und dem Veröffentlichen-Abonnieren-Muster zu erstellen, können Sie den Azure Web PubSub-Dienst wählen. Der Azure Web PubSub-Dienst ist **kein** Ersatz für Azure SignalR Service. Sie sind für verschiedene Szenarios vorgesehen.

Azure SignalR Service ist in folgenden Fällen besser geeignet:  

- Sie verwenden bereits ASP.NET oder ASP.NET Core SignalR, nutzen hauptsächlich .NET oder müssen eine Integration mit dem .NET-Ökosystem (z. B. Blazor) vornehmen.
- Für Ihre Plattform ist ein SignalR-Client verfügbar. 
- Sie benötigen ein eingerichtetes Protokoll, das eine Vielzahl von Aufrufmustern (RPC und Streaming), Transporte (WebSocket, vom Server gesendete Ereignisse und lange Abrufintervalle) und einen Client unterstützt, der die Verbindungslebensdauer in Ihrem Namen verwaltet. 

Der Azure Web PubSub-Dienst ist in folgenden Situationen besser geeignet:  

- Sie müssen Echtzeitanwendungen basierend auf der WebSocket-Technologie erstellen oder über WebSocket veröffentlichen und abonnieren.
- Sie möchten Ihr eigenes Unterprotokoll erstellen oder vorhandene erweiterte Protokolle über WebSocket verwenden (z. B. MQTT, AMQP über WebSocket). 
- Sie suchen einen einfachen Server, der z. B. Nachrichten an den Client sendet, ohne das konfigurierte Back-End zu durchlaufen.  

##  <a name="where-does-my-data-reside"></a>Wo befinden sich meine Daten?

Azure Web PubSub ist ein Datenverarbeitungsdienst. Inhalte von Kunden werden nicht gespeichert, und Data Residency wird durch den Entwurf garantiert. Wenn Sie Azure Web PubSub in Verbindung mit anderen Azure-Diensten (z. B. Azure Storage) zu Diagnosezwecken verwenden, finden Sie in [diesem Whitepaper](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) Informationen zum Aufrechterhalten von Data Residency in Azure-Regionen.
