---
title: Das Importieren einer WebSocket-API mithilfe des Azure-Portals | Microsoft-Dokumentation
titleSuffix: ''
description: Erfahren Sie, wie das API Management das WebSocket beim Hinzufügen einer WebSocket-API und WebSocket-Einschränkungen unterstützt.
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.date: 11/2/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 027a87a7502f551b7fb97d52a732a90bc0b8aa45
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065682"
---
# <a name="import-a-websocket-api"></a>Importieren einer WebSocket-API

Mit der WebSocket-API-Lösung von API Management können Sie nun WebSocket- und REST-APIs mit API Management verwalten, schützen, beobachten und verfügbar machen. Außerdem können Sie einen zentralen Hub zum Entdecken und Nutzen aller APIs bereitstellen. Die API-Herausgeber können schnell eine WebSocket-API in das API Management hinzufügen, durch:
* Eine einfache Geste im Azure-Portal und 
* Die Management-API und der Azure Resource Manager. 

Sie können WebSocket-APIs schützen, indem Sie vorhandene Zugriffssteuerungsrichtlinien wie [JWT-Validierung](./api-management-access-restriction-policies.md#ValidateJWT) anwenden. Sie können die WebSocket-APIs auch mithilfe der API-Testkonsolen im Azure-Portal und im Entwicklerportal testen. Auf der Grundlage vorhandener Beobachtungsfunktionen stellt das API Management Metriken und Protokolle für die Überwachung und die Problembehandlung von WebSocket-APIs bereit. 

In diesem Artikel werden die folgenden Themen behandelt:
> [!div class="checklist"]
> * Das Verstehen des Websocket-Passthrough-Flusses.
> * Das Hinzufügen einer WebSocket-API in Ihre API Management-Instanz.
> * Testen der WebSocket-API
> * Anzeigen der Metriken und Protokolle für die WebSocket-API
> * Erfahren Sie mehr über die WebSocket-API-Einschränkungen.

## <a name="prerequisites"></a>Voraussetzungen

- Eine bestehende API Management-Instanz. [Erstellen Sie eine, falls nicht schon geschehen](get-started-create-service-instance.md).
- Eine WebSocket-API. 

## <a name="websocket-passthrough"></a>WebSocket-Passthrough

Das API Management unterstützt den WebSocket-Passthrough. 

:::image type="content" source="./media/websocket-api/websocket-api-passthrough.png" alt-text="Visuelle Darstellung des WebSocket-Passthrough-Flusses":::

Während des WebSocket-Passthrough stellt die Clientanwendung eine WebSocket-Verbindung mit dem API Management-Gateway her. Das API Management-Gateway stellt dann eine Verbindung mit den entsprechenden Back-End-Diensten her. Das API Management vermittelt dann die WebSocket Client-Server-Nachrichten.

1. Die Clientanwendung sendet eine WebSocket Handshake-Anforderung an das APIM-Gateway, die den OnHandshake-Vorgang aufruft.
1. Das APIM-Gateway sendet eine WebSocket-Handshake-Anforderung an den entsprechenden Back-End-Dienst.
1. Der Back-End-Dienst aktualisiert eine Verbindung mit WebSocket.
1. Das APIM-Gateway aktualisiert die entsprechende Verbindung mit WebSocket.
1. Nachdem das Verbindungspaar eingerichtet wurde, vermittelt das APIM die Nachrichten zwischen der Clientanwendung und dem Back-End-Dienst hin und her.
1. Die Clientanwendung sendet eine Nachricht an das APIM-Gateway.
1. Das APIM-Gateway leitet die Nachricht an den Back-End-Dienst weiter.
1. Der Back-End-Dienst sendet eine Nachricht an das APIM-Gateway.
1. Das APIM-Gateway leitet die Nachricht an die Clientanwendung.
1. Wenn eine der beiden Seiten sich abmeldet, trennt das APIM die entsprechende Verbindung.

> [!NOTE]
> Die clientseitigen und backend-seitigen Verbindungen bestehen aus einer 1:1-Zuordnung. 

## <a name="onhandshake-operation"></a>Der OnHandshake-Vorgang

Wenn eine Clientanwendung versucht, eine WebSocket-Verbindung mit einem Back-End-Dienst herzustellen, sendet sie per [WebSocket-Protokoll](https://tools.ietf.org/html/rfc6455)zunächst eine [öffnende Handshake-Anforderung](https://tools.ietf.org/html/rfc6455#page-6). Jede WebSocket-API in dem API Management verfügt über einen OnHandshake-Vorgang. Der OnHandshake ist ein unveränderlicher, nicht entfernbarer, automatisch erstellter Systemvorgang. Mit dem OnHandshake-Vorgang können die API-Herausgeber diese Handshake-Anforderungen abfangen und die API Management-Richtlinien auf sie anwenden.

:::image type="content" source="./media/websocket-api/onhandshake-screen.png" alt-text="Ein Bildschirmbeispiel für einen „OnHandshake“":::

## <a name="add-a-websocket-api"></a>Das Hinzufügen einer WebSocket-API

1. Navigieren Sie zu Ihrer API Management-Instanz.
1. Wählen Sie im Seitennavigationsmenü im Abschnitt **APIs** die Option **APIs** aus.
1. Wählen Sie unter **Definieren einer neuen API** das **WebSocket**-Symbol aus.
1. Wählen Sie im Dialogfeld **Vollständig** aus, und füllen Sie die erforderlichen Formularfelder aus.

    | Feld | BESCHREIBUNG |
    |----------------|-------|
    | Anzeigename | Das ist der Name, mit dem Ihre WebSocket-API angezeigt wird. |
    | Name | Das ist der Rohname der WebSocket-API. Er wird automatisch ausgefüllt, wenn Sie den Anzeigenamen eingeben. |
    | Die WebSocket-URL | Die Basis-URL mit ihrem Websocket-Namen. Beispiel: *ws://example.com/your-socket-name* |
    | URL-Schema | Übernehmen Sie die Standardeinstellung. |
    | API-URL-Suffix| Fügen Sie ein URL-Suffix hinzu, um diese spezifische API in dieser API Management-Instanz zu identifizieren. Es muss in dieser APIM-Instanz eindeutig sein. |
    | Produkte | Ordnen Sie Ihre WebSocket-API einem Produkt zu, um es zu veröffentlichen. |
    | Gateways | Ordnen Sie Ihre WebSocket-API den vorhandenen Gateways zu. |
 
1. Klicken Sie auf **Erstellen**.

## <a name="test-your-websocket-api"></a>Testen Ihrer WebSocket-API

1. Navigieren Sie zu Ihrer WebSocket-API.
1. Klicken Sie in Ihrer WebSocket-API auf den onHandshake-Vorgang.
1. Klicken Sie auf die Registerkarte **Test**, um auf die Testkonsole zuzugreifen. 
1. Geben Sie optional Abfragezeichenfolgenparameter an, die für den WebSocket-Handshakevorgang benötigt werden.

    :::image type="content" source="./media/websocket-api/test-websocket-api.png" alt-text="Beispiel zum Testen einer API":::

1. Klicken Sie auf **Verbinden**.
1. Zeigen Sie den Verbindungsstatus unter **Ausgabe** an.
1. Geben Sie den Wert unter **Nutzdaten** ein. 
1. Klicken Sie auf **Senden**.
1. Zeigen Sie empfangene Meldungen unter **Ausgabe** an.
1. Wiederholen Sie die vorherigen Schritte, um verschiedene Nutzdaten zu testen.
1. Wenn der Test abgeschlossen ist, klicken Sie auf **Verbindung trennen**.

## <a name="view-metrics-and-logs"></a>Anzeigen von Metriken und Protokollen

Verwenden Sie Standardfunktionen für API Management und Azure Monitor zum [Überwachen](api-management-howto-use-azure-monitor.md) von WebSocket-APIs:

* Anzeigen von API-Metriken in Azure Monitor
* Aktivieren Sie optional Diagnoseeinstellungen, um API Management-Gatewayprotokolle zu erfassen und anzuzeigen, die WebSocket-API-Vorgänge enthalten.

Der folgende Screenshot zeigt beispielsweise aktuelle WebSocket-API-Antworten mit Code `101` aus der Tabelle **ApiManagementGatewayLogs**. Diese Ergebnisse zeigen den erfolgreichen Wechsel der Anforderungen vom TCP- zum WebSocket-Protokoll an.

:::image type="content" source="./media/websocket-api/query-gateway-logs.png" alt-text="Abfrageprotokolle für WebSocket-API-Anforderungen":::

## <a name="limitations"></a>Einschränkungen

Weiter unten finden Sie die aktuellen Einschränkungen der WebSocket-Unterstützung im API Management:

* Die WebSocket-APIs werden im Consumption-Tarif noch nicht unterstützt.
* Die WebSocket-APIs werden im [selbstgehosteten Gateway](./self-hosted-gateway-overview.md) noch nicht unterstützt.
* Die Azure CLI, PowerShell und das SDK unterstützen derzeit keine Verwaltungsvorgänge der WebSocket-APIs.

### <a name="unsupported-policies"></a>Nicht unterstützte Richtlinien

Die folgenden Richtlinien werden nicht von dem OnHandshake unterstützt und können nicht auf den OnHandshake-Vorgang angewendet werden:
*  Modellantwort
* Aus Cache abrufen
* Store to cache
* Allow cross-domain calls
* CORS
* JSONP
* Anforderungsmethode festlegen
* Text festlegen
* XML zu JSON konvertieren
* Convert JSON to XML (Konvertieren von JSON in XML)
* Das Transformieren von XML mithilfe von XSLT
* Inhalt prüfen
* Validieren von Parametern
* Validieren von Headern
* Validieren von Statuscodes

> [!NOTE]
> Wenn Sie die Richtlinien in höheren Geltungsbereichen (d. h. global oder Produkt) angewendet haben und sie von einer WebSocket-API über die Richtlinie geerbt wurden, werden sie zur Laufzeit übersprungen.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Transformieren und Schützen einer veröffentlichten API](transform-api.md)
