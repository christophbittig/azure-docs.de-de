---
title: Problembehandlung mithilfe der Ressourcenprotokolle des Azure Web PubSub-Diensts
description: Hier erfahren Sie, wie Sie Ressourcenprotokolle abrufen und für die Problembehandlung verwenden können.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/22/2021
ms.openlocfilehash: 7e8d4c725c8e205f015774b8b5b01e26b5f89271
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478619"
---
# <a name="how-to-troubleshoot-with-resource-logs"></a>Problembehandlung mithilfe von Ressourcenprotokollen

In dieser Schrittanleitung wird gezeigt, wie Sie die Ressourcenprotokolle abrufen und für die Problembehandlung verwenden können.

## <a name="whats-the-resource-logs"></a>Was sind die Ressourcenprotokolle?

Die Ressourcenprotokolle bieten eine umfassendere Ansicht für Konnektivitäts-, Messaging- und HTTP-Anforderungsinformationen für die Azure Web PubSub-Dienstinstanz. Sie können für die Identifizierung von Problemen, die Nachverfolgung von Verbindungen, die Ablaufverfolgung für Nachrichten und HTTP-Anforderungen sowie für Analysen verwendet werden.

Es gibt drei Arten von Protokollen: Konnektivitätsprotokolle, Messagingprotokolle und HTTP-Anforderungsprotokolle.

### <a name="connectivity-logs"></a>Konnektivitätsprotokolle

Konnektivitätsprotokolle bieten ausführliche Informationen zu Azure Web PubSub-Hubverbindungen. Dies sind z. B. grundlegende Informationen (Benutzer-ID, Verbindungs-ID usw.) und Ereignisinformationen (Verbinden-, Trennen-, Abbrechen-Ereignis usw.). Aus diesem Grund ist das Konnektivitätsprotokoll hilfreich, um verbindungsbezogene Probleme zu beheben.

### <a name="messaging-logs"></a>Messagingprotokolle

Messagingprotokolle bieten Ablaufverfolgungsinformationen für die Azure Web PubSub-Hubnachrichten, die über den Azure Web PubSub-Dienst empfangen und gesendet werden. Zum Beispiel die Ablaufverfolgungs-ID und der Nachrichtentyp der Nachricht. In der Regel wird die Nachricht aufgezeichnet, wenn sie beim Dienst eintrifft oder ihn verlässt. Daher sind Messagingprotokolle hilfreich für die Behandlung von Problemen im Zusammenhang mit Nachrichten.

### <a name="http-request-logs"></a>HTTP-Anforderungsprotokolle

HTTP-Anforderungsprotokolle stellen Ablaufverfolgungsinformationen für HTTP-Anforderungen für den Azure Web PubSub-Dienst bereit, z. B. die HTTP-Methode und den Statuscode. In der Regel wird die HTTP-Anforderung im Protokoll erfasst, wenn sie beim Dienst eingeht oder ihn verlässt. Daher sind HTTP-Anforderungsprotokolle hilfreich bei der Behandlung von Problemen im Zusammenhang mit Anforderungen.

## <a name="capture-resource-logs-with-live-trace-tool"></a>Aufzeichnen von Ressourcenprotokollen mit dem Liveablaufverfolgungs-Tool 

Das Liveablaufverfolgungs-Tool des Azure Web PubSub-Diensts kann Ressourcenprotokolle in Echtzeit erfassen und ist hilfreich bei der Ablaufverfolgung mit der Entwicklungsumgebung der Kund*innen. Es kann Konnektivitätsprotokolle, Messagingprotokolle und HTTP-Anforderungsprotokolle aufzeichnen.

> [!NOTE]
> Die vom Liveablaufverfolgungs-Tool aufgezeichneten Echtzeit-Ressourcenprotokolle werden als Nachrichten (ausgehender Datenverkehr) abgerechnet.

> [!NOTE]
> Für die Azure Web PubSub-Dienstinstanz, die im kostenlosen Tarif (Free) erstellt wurde, unterliegt dem täglichen Limit für Nachrichten (ausgehender Datenverkehr).

### <a name="launch-the-live-trace-tool"></a>Starten des Liveablaufverfolgungs-Tools

1. Öffnen Sie das Azure-Portal. 
2. Aktivieren Sie in Ihrer Azure Web PubSub-Dienstinstanz auf der Seite **Einstellungen für die Liveablaufverfolgung** die Option **Live-Ablaufverfolgung aktivieren**, wenn diese deaktiviert sein sollte.
3. Aktivieren Sie alle Protokollkategorien, die Sie benötigen.
4. Klicken Sie auf die Schaltfläche **Speichern**, und warten Sie, bis die Einstellungen übernommen wurden.
5. Klicken Sie auf *Tool für die Liveablaufverfolgung öffnen*.

    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-logs-with-live-trace-tool.png" alt-text="Screenshot: Starten des Liveablaufverfolgungs-Tools":::

### <a name="capture-the-resource-logs"></a>Aufzeichnen der Ressourcenprotokolle

Das Liveablaufverfolgungs-Tool bietet einige grundlegende Funktionen, mit denen Sie die Ressourcenprotokolle für die Problembehandlung aufzeichnen können.

* **Capture** (Aufzeichnen): Beginnen Sie mit dem Aufzeichnen der Echtzeit-Ressourcenprotokolle über die Azure Web PubSub-Instanz mit dem Liveablaufverfolgungs-Tool.
* **Clear** (Löschen): Löschen Sie die aufgezeichneten Echtzeit-Ressourcenprotokolle.
* **Log filter** (Protokollfilter): Im Liveablaufverfolgungs-Tool können Sie die aufgezeichneten Echtzeit-Ressourcenprotokolle nach einem bestimmten Schlüsselwort filtern. Das allgemeine Trennzeichen (z. B. Leerzeichen, Komma, Semikolon usw.) wird als Teil des Schlüsselworts behandelt. 
* **Status**: Der Status zeigt an, ob das Liveablaufverfolgungs-Tool mit der jeweiligen Instanz verbunden oder von dieser getrennt ist.

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/live-trace-tool-capture.png" alt-text="Screenshot: Aufzeichnen von Ressourcenprotokollen mit dem Liveablaufverfolgungs-Tool":::

Die vom Liveablaufverfolgungs-Tool aufgezeichneten Echtzeit-Ressourcenprotokolle enthalten ausführliche Informationen für die Problembehandlung. 

| Name | BESCHREIBUNG |
| ------------ |  ------------------------ | 
| Time | Protokollereigniszeit |
| Protokollebene | Protokollereignisebene (Ablaufverfolgung/Debuggen/Information/Warnung/Fehler) |
| Veranstaltungsname | Vorgangsname des Ereignisses. |
| `Message` | Ausführliche Meldung des Protokollereignisses. |
| Ausnahme | Die Laufzeitausnahme des Azure Web PubSub-Diensts |
| Hub | Vom Benutzer definierter Hubname |
| Verbindungs-ID | Identität der Verbindung. |
| Benutzerkennung | Identität des Benutzers. |
| IP | Die IP-Adresse des Clients |
| Routenvorlage | Die Routenvorlage der API |
| HTTP-Methode | Die HTTP-Methode (POST/GET/PUT/DELETE) |
| URL | Der Uniform Resource Locator |
| Ablaufverfolgungs-ID | Der eindeutige Bezeichner für den Aufruf |
| Statuscode | Der HTTP-Antwortcode |
| Duration | Die Dauer zwischen dem Empfang und der Verarbeitung der Anforderung |
| Header | Die zusätzlichen Informationen, die vom Client und vom Server mit einer HTTP-Anforderung oder -Antwort übergeben werden |

Sobald der Azure Web PubSub-Dienst allgemein verfügbar ist, unterstützt das Liveablaufverfolgungs-Tool auch das Exportieren der Protokolle in einem bestimmten Format und hilft Ihnen dann bei deren Freigabe für andere Personen zur Problembehandlung. 

## <a name="capture-resource-logs-with-azure-monitor"></a>Erfassen von Ressourcenprotokollen mit Azure Monitor

### <a name="how-to-enable-resource-logs"></a>Aktivieren von Ressourcenprotokollen

Azure Web PubSub unterstützt derzeit die Integration in [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

1. Navigieren Sie zum Azure-Portal.
2. Klicken Sie auf der Seite **Diagnoseeinstellungen** Ihrer Azure Web PubSub-Dienstinstanz auf den Link **+ Diagnoseeinstellung hinzufügen**.
3. Geben Sie im Feld **Name der Diagnoseeinstellung** den Namen der Einstellung ein.
4. Wählen Sie unter **Kategoriedetails** die benötigten Protokollkategorien aus.
5. Aktivieren Sie unter **Zieldetails** das Kontrollkästchen bei **In ein Speicherkonto archivieren**.
6. Klicken Sie auf die Schaltfläche **Speichern**, um die Diagnoseeinstellung zu speichern.

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-list.png" alt-text="Screenshot: Anzeigen von Diagnoseeinstellungen und Erstellen einer neuen Einstellung":::

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-details.png" alt-text="Screenshot: Konfigurieren der Diagnoseeinstellungsdetails":::

> [!NOTE]
> Das Speicherkonto sollte sich in derselben Region wie der Azure Web PubSub-Dienst befinden.

### <a name="archive-to-a-storage-account"></a>In einem Speicherkonto archivieren

Die Protokolle werden in dem im Bereich **Diagnoseeinstellung** konfigurierten Speicherkonto gespeichert. Ein Container mit dem Namen `insights-logs-<CATEGORY_NAME>` wird automatisch erstellt, um Ressourcenprotokolle zu speichern. In dem Container werden Protokolle in der Datei `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` gespeichert. Im Grunde stellt der Pfad eine Kombination aus `resource ID` und `Date Time` dar. Die Protokolldateien werden anhand der `hour` aufgeteilt. Daher sind die Minuten immer `m=00`.

Alle Protokolle werden im JavaScript Object Notation (JSON)-Format gespeichert. Jeder Eintrag enthält Zeichenfolgenfelder im nachfolgend beschriebenen Format.

JSON-Zeichenfolgen im Archivprotokoll enthalten Elemente, die in den folgenden Tabellen aufgeführt sind:

**Format**

Name | BESCHREIBUNG
------- | -------
time | Protokollereigniszeit
level | Protokollereignisebene
resourceId | Ressourcen-ID Ihres Azure SignalR Service.
location | Standort Ihres Azure SignalR Service.
category | Kategorie des Protokollereignisses.
operationName | Vorgangsname des Ereignisses.
callerIpAddress | IP-Adresse Ihres Servers/Clients.
properties | Detaillierte Eigenschaften im Zusammenhang mit diesem Protokollereignis. Weitere Details finden Sie in der unten stehenden Tabelle.

**Eigenschaftentabelle**

Name | BESCHREIBUNG
------- | -------
collection | Sammlung des Protokollereignisses. Zulässige Werte sind `Connection`, `Authorization` und `Throttling`.
connectionId | Identität der Verbindung.
userId | Identität des Benutzers.
message | Ausführliche Meldung des Protokollereignisses.
Hub | Vom Benutzer definierter Hubname |
routeTemplate | Die Routenvorlage der API |
httpMethod | Die HTTP-Methode (POST/GET/PUT/DELETE) |
url | Der Uniform Resource Locator |
traceId | Der eindeutige Bezeichner für den Aufruf |
statusCode | Der HTTP-Antwortcode |
duration | Die Dauer zwischen dem Empfang und der Verarbeitung der Anforderung |
headers | Die zusätzlichen Informationen, die vom Client und vom Server mit einer HTTP-Anforderung oder -Antwort übergeben werden |

Es folgt ein Codebeispiel für eine JSON-Zeichenfolge im Archivierungsprotokoll:

```json
{
  "properties": {
    "message": "Connection started",
    "collection": "Connection",
    "connectionId": "LW61bMG2VQLIMYIVBMmyXgb3c418200",
    "userId": null
  },
  "operationName": "ConnectionStarted",
  "category": "ConnectivityLogs",
  "level": "Informational",
  "callerIpAddress": "167.220.255.79",
  "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYGROUP/PROVIDERS/MICROSOFT.SIGNALRSERVICE/WEBPUBSUB/MYWEBPUBSUB",
  "time": "2021-09-17T05:25:05Z",
  "location": "westus"
}
```

## <a name="troubleshoot-with-the-resource-logs"></a>Problembehandlung mit den Ressourcenprotokollen

Bei einer unerwarteten Zunahme oder Abnahme der Anzahl von Verbindungen können Sie die Ressourcenprotokolle für die Problembehandlung verwenden. Typische Probleme sind häufig unerwartete Auslastungsveränderungen bei Verbindungen, das Erreichen von Verbindungslimits bei Verbindungen sowie Autorisierungsfehler.

### <a name="unexpected-connection-number-changes"></a>Unerwartete Änderungen der Verbindungsanzahl

#### <a name="unexpected-connection-dropping"></a>Unerwartete Verbindungsunterbrechungen

Wenn eine Verbindung getrennt wird, erfassen die Ressourcenprotokolle dieses Trennungsereignis in `operationName` mit `ConnectionAborted` oder `ConnectionEnded`.

Der Unterschied zwischen `ConnectionAborted` und `ConnectionEnded` besteht darin, dass `ConnectionEnded` eine erwartete Verbindungstrennung ist, die vom Client oder Server ausgelöst wird. Während es sich bei `ConnectionAborted` in der Regel um ein unerwartetes Verbindungsunterbrechungsereignis handelt, und der Grund für den Abbruch wird in `message` bereitgestellt.

Die Gründe für Abbrüche sind in der folgenden Tabelle aufgeführt:

| `Reason` | BESCHREIBUNG |
| ------- | ------- |
| Anzahl der Verbindungen erreicht das Limit. | Die Anzahl der Verbindungen erreicht das Limit Ihres aktuellen Tarifs. Erwägen Sie, die Diensteinheit hochzuskalieren.
| Erneutes Laden des Diensts, Wiederherstellen der Verbindung. | Azure Web PubSub-Dienst wird neu geladen. Sie müssen Ihren eigenen Mechanismus für das Wiederverbinden implementieren oder manuell eine neue Verbindung mit dem Azure Web PubSub-Dienst herstellen. |
| Vorübergehender interner Serverfehler. | Ein vorübergehender Fehler tritt im Azure Web PubSub-Dienst auf; sollte automatisch wiederhergestellt werden.

#### <a name="unexpected-connection-growing"></a>Unerwarteter Anstieg der Verbindungsauslastung.

Um unerwartete Anstiege der Verbindungsauslastung zu behandeln, müssen Sie zuerst die zusätzlichen Verbindungen ausfiltern. Sie können Ihrer Testclientverbindung eine eindeutige Testbenutzer-ID hinzufügen. Verifizieren Sie diese dann in den Ressourcenprotokollen. Wenn Sie dort mehr als eine Clientverbindung mit derselben Testbenutzer-ID oder IP-Adresse vorfinden, ist es wahrscheinlich, dass der Client mehr Verbindungen erstellt und unterhält als erwartet. Überprüfen Sie die Clientseite.

### <a name="authorization-failure"></a>Autorisierungsfehler

Wenn Sie bei Clientanforderungen die Rückgabe „401 – Nicht autorisiert“ erhalten, überprüfen Sie Ihre Ressourcenprotokolle. Wenn `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` auftritt, bedeutet dies, dass alle Zielgruppen in Ihrem Zugriffstoken ungültig sind. Versuchen Sie, die im Protokoll vorgeschlagenen gültigen Zielgruppen zu verwenden.

### <a name="throttling"></a>Drosselung

Wenn Sie feststellen, dass Sie keine Clientverbindungen mit dem Azure Web PubSub-Dienst herstellen können, überprüfen Sie Ihre Ressourcenprotokolle. Wenn im Ressourcenprotokoll `Connection count reaches limit` (Anzahl von Verbindungen erreicht Obergrenze) steht, stellen Sie zu viele Verbindungen mit dem Azure Web PubSub-Dienst her, sodass die Obergrenze für die Anzahl von Verbindungen erreicht ist. Erwägen Sie die Hochskalierung Ihrer Azure Web PubSub-Dienstinstanz. Wenn im Ressourcenprotokoll `Message count reaches limit` (Anzahl von Nachrichten erreicht Obergrenze) steht, bedeutet dies, dass Sie den Free-Tarif verwenden und das Nachrichtenkontingent aufgebraucht ist. Wenn Sie weitere Nachrichten senden möchten, sollten Sie für die Azure Web PubSub-Dienstinstanz einen Wechsel in den Standardtarif erwägen, um zusätzliche Nachrichten zu senden. Weitere Informationen finden Sie unter [Azure Web PubSub-Dienst – Preise](https://azure.microsoft.com/pricing/details/web-pubsub/).
