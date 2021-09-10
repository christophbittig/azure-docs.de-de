---
title: Verwenden des Liveablaufverfolgungs-Tools für Azure SignalR Service
description: Erfahren Sie, wie Sie das Liveablaufverfolgungs-Tool für Azure SignalR Service verwenden.
author: wanlwanl
ms.author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: 5a1cf026759a8112901624d9db89d7c344930cb8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297426"
---
# <a name="how-to-use-live-trace-tool-for-azure-signalr-service"></a>Verwenden des Liveablaufverfolgungs-Tools für Azure SignalR Service

Das Liveablaufverfolgungs-Tool ist eine einzelne Webanwendung für die Erfassung und Anzeige von Liveablaufverfolgungen in Azure SignalR Service. Die Liveablaufverfolgungen können in Echtzeit ohne jede Abhängigkeit von anderen Diensten erfasst werden.
Sie können das Feature der Liveablaufverfolgung mit einem einzigen Klick aktivieren und deaktivieren. Sie können auch eine Protokollkategorie auswählen, die Sie interessiert.

> [!NOTE]
> Beachten Sie, dass Liveablaufverfolgungen als ausgehende Nachrichten gezählt werden.

## <a name="launch-the-live-trace-tool"></a>Starten des Liveablaufverfolgungs-Tools

1. Öffnen Sie das Azure-Portal.
2. Aktivieren Sie **Liveablaufverfolgung aktivieren**.
3. Klicken Sie auf der Symbolleiste auf die Schaltfläche **Speichern**, und warten Sie, bis die Änderungen in Kraft treten.
4. Wählen Sie auf der Seite **Diagnoseeinstellungen** Ihrer Azure Web PubSub-Dienstinstanz die Option **Liveablaufverfolgungs-Tool öffnen** aus. 

    :::image type="content" source="media/signalr-howto-troubleshoot-live-trace/live-traces-with-live-trace-tool.png" alt-text="Screenshot: Starten des Liveablaufverfolgungs-Tools":::

## <a name="capture-live-traces"></a>Erfassen von Liveablaufverfolgungen

Das Liveablaufverfolgungs-Tool bietet einige grundlegende Funktionen, mit denen Sie zur Problembehandlung Liveablaufverfolgungen erfassen können.

* **Erfassen**: Beginnen Sie mit der Erfassung der Echtzeit-Liveablaufverfolgungen von der Azure Web PubSub-Instanz mit dem Liveablaufverfolgungs-Tool.
* **Löschen**: Löschen Sie die erfassten Echtzeit-Liveablaufverfolgungen.
* **Exportieren**: Exportieren Sie die Liveablaufverfolgungen in eine Datei. Das derzeit unterstützte Dateiformat ist CSV.
* **Protokollfilter**: Mit dem Liveablaufverfolgungs-Tool können Sie die erfassten Echtzeit-Liveablaufverfolgungen mit einem bestimmten Schlüsselwort filtern. Das allgemeine Trennzeichen (z. B. Leerzeichen, Komma, Semikolon usw.) wird als Teil des Schlüsselworts behandelt. 
* **Status**: Der Status zeigt an, ob das Liveablaufverfolgungs-Tool mit der jeweiligen Instanz verbunden oder von dieser getrennt ist.

:::image type="content" source="./media/signalr-howto-troubleshoot-live-trace/live-trace-tool-capture.png" alt-text="Screenshot: Erfassen von Liveablaufverfolgungen mit dem Liveablaufverfolgungs-Tool":::

Die vom Liveablaufverfolgungs-Tool erfassten Echtzeit-Liveablaufverfolgungen enthalten ausführliche Informationen für die Problembehandlung. 

| Name | BESCHREIBUNG |
| ------------ |  ------------------------ | 
| Time | Protokollereigniszeit |
| Protokollebene | Protokollereignisebene (Ablaufverfolgung/Debuggen/Information/Warnung/Fehler) |
| Veranstaltungsname | Vorgangsname des Ereignisses. |
| `Message` | Ausführliche Meldung des Protokollereignisses. |
| Ausnahme | Die Laufzeitausnahme des Azure Web PubSub-Diensts |
| Hub | Vom Benutzer definierter Hubname |
| Verbindungs-ID | Identität der Verbindung. |
| Verbindungs-ID | Typ der Verbindung. Zulässige Werte sind `Server` (Verbindungen zwischen Server und Dienst) und `Client` (Verbindungen zwischen Client und Dienst).|
| Benutzerkennung | Identität des Benutzers. |
| IP | Die IP-Adresse des Clients |
| Servergebunden (Server Sticky) | Routingmodus des Clients. Zulässige Werte sind `Disabled`, `Preferred` und `Required`. Weitere Informationen finden Sie unter [ServerStickyMode](https://github.com/Azure/azure-signalr/blob/master/docs/run-asp-net-core.md#serverstickymode). |
| Transport | Der Transporttyp, den der Client verwenden kann, um HTTP-Anforderungen zu senden. Zulässige Werte sind `WebSockets`, `ServerSentEvents` und `LongPolling`. Weitere Informationen finden Sie unter [HttpTransportType](/dotnet/api/microsoft.aspnetcore.http.connections.httptransporttype). |

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie erfahren, wie Sie das Liveablaufverfolgungs-Tool verwenden. Informationen zur Behandlung allgemeiner Probleme finden Sie hier:
* Leitfäden zur Problembehandlung: Informationen zur Behandlung typischer Probleme bei Liveablaufverfolgungen finden Sie im [Problembehandlungsleitfaden](./signalr-howto-troubleshoot-guide.md).
* Methoden zur Problembehandlung: Informationen dazu, wie Sie per Selbstdiagnose die Ursache eines Problems finden oder es eingrenzen können, finden Sie in der [Einführung in Methoden zur Problembehandlung](./signalr-howto-troubleshoot-method.md).