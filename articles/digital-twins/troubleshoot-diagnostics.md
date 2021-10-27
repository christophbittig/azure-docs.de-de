---
title: 'Problembehandlung: Diagnoseprotokolle'
titleSuffix: Azure Digital Twins
description: In diesem Artikel erfahren Sie, wie Sie die Protokollierung mit Diagnoseeinstellungen aktivieren und die Protokolle für eine sofortige Anzeige abfragen. Erfahren Sie außerdem mehr über die Protokollkategorien und ihre Schemas.
author: baanders
ms.author: baanders
ms.date: 9/24/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 89b7c741ce75a629de99e3337428027429bce5b7
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131769"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logs"></a>Problembehandlung von Azure Digital Twins: Diagnoseprotokolle

In diesem Artikel erfahren Sie, wie Sie Diagnoseeinstellungen im [Azure-Portal](https://portal.azure.com) konfigurieren, einschließlich der zu erfassenden Protokolltypen und ihrem Speicherort (z. B. in Log Analytics oder einem Speicherkonto Ihrer Wahl). Sie können die Protokolle anschließend abfragen, um schnell die passenden Einblicke zu gewinnen.

Azure Digital Twins kann **Protokolle** für Ihre Dienstinstanz zur Überwachung von Leistung, Zugriff und anderen Daten erfassen. Anhand dieser Protokolle können Sie eine Vorstellung davon erhalten, was in Ihrer Azure Digital Twins-Instanz geschieht, und die Ursachen von Problemen analysieren, ohne den Azure-Support kontaktieren zu müssen.

Dieser Artikel enthält darüber hinaus Informationen zu allen **Protokollkategorien**, die von Azure Digital Twins erfasst werden können, und deren **Schemas**.

## <a name="turn-on-diagnostic-settings"></a>Aktivieren von Diagnoseeinstellungen 

Aktivieren Sie Diagnoseeinstellungen, um mit dem Erfassen von Protokollen in Ihrer Azure Digital Twins-Instanz zu beginnen. Sie können auch das Ziel auswählen, in dem die exportierten Protokolle gespeichert werden sollen. Im Folgenden wird beschrieben, wie Sie Diagnoseeinstellungen für Ihre Azure Digital Twins-Instanz aktivieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer Azure Digital Twins-Instanz. Sie finden sie, indem Sie ihren Namen auf der Suchleiste des Portals eingeben. 

2. Wählen Sie im Menü die Option **Diagnoseeinstellungen** und dann **Diagnoseeinstellung hinzufügen** aus.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot der Seite „Diagnoseeinstellungen“ im Azure-Portal und der Schaltfläche zum Hinzufügen." lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Geben Sie auf der daraufhin geöffneten Seite die folgenden Werte ein:
     * **Name der Diagnoseeinstellung:** Benennen Sie die Diagnoseeinstellungen.
     * **Kategoriedetails:** Wählen Sie aus, welche Vorgänge Sie überwachen möchten, und aktivieren Sie die Kontrollkästchen für die Diagnose dieser Vorgänge. Die Vorgänge, für die über die Diagnoseeinstellungen Berichte erstellt werden können, sind die folgenden:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Weitere Details zu diesen Kategorien und den darin enthaltenen Informationen finden Sie im Abschnitt [Protokollkategorien](#log-categories) weiter unten.
     * **Zieldetails:** Wählen Sie aus, wohin die Protokolle gesendet werden sollen. Sie können eine beliebige Kombination der drei Optionen auswählen:
        - An Log Analytics senden
        - In einem Speicherkonto archivieren
        - An einen Event Hub streamen

        Möglicherweise werden Sie aufgefordert, weitere Details einzugeben, wenn diese für Ihr ausgewähltes Ziel erforderlich sind.  
    
4. Speichern Sie die neuen Einstellungen. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot der Seite „Diagnoseeinstellung“ im Azure-Portal, auf der der Benutzer Informationen zu einer Diagnoseeinstellung eingetragen hat." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

Neue Einstellungen werden in etwa zehn Minuten wirksam. Anschließend werden die Protokolle im konfigurierten Ziel auf der Seite **Diagnoseeinstellungen** für Ihre Instanz angezeigt. 

Ausführlichere Informationen zu Diagnoseeinstellungen und deren Einrichtungsoptionen finden Sie unter [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="view-and-query-logs"></a>Anzeigen und Abfragen von Protokollen

Nach dem Konfigurieren der Speicherdetails Ihrer Azure Digital Twins-Protokolle können Sie **benutzerdefinierte Abfragen** für sie schreiben, um Erkenntnisse zu generieren und Probleme zu beheben. Der Dienst stellt außerdem einige Beispielabfragen zur Verfügung, die Ihnen den Einstieg erleichtern können, indem sie Antworten auf häufig gestellte Fragen von Kunden zu ihren Instanzen geben.

Nachfolgend wird beschrieben, wie Sie die Protokolle für Ihre Instanz abfragen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer Azure Digital Twins-Instanz. Sie finden sie, indem Sie ihren Namen auf der Suchleiste des Portals eingeben. 

2. Wählen Sie im Menü die Option **Protokolle** aus, um die Seite für Protokollabfragen zu öffnen. Die Seite wird mit einem Fenster mit dem Namen *Abfragen* geöffnet.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Screenshot der Seite „Protokolle“ für eine Azure Digital Twins-Instanz im Azure-Portal mit überlagertem Fenster „Abfragen“, in dem vordefinierten Abfragen angezeigt werden." lightbox="media/troubleshoot-diagnostics/logs.png":::

    Diese Abfragen sind vorgefertigte Beispiele, die für verschiedene Protokolle geschrieben wurden. Sie können eine der Abfragen auswählen, um sie in den Abfrage-Editor zu laden, und die Abfrage ausführen, um diese Protokolle für Ihre Instanz anzuzeigen.

    Sie können das Fenster *Abfragen* auch ohne Ausführen einer Abfrage schließen, um direkt zur Seite mit dem Abfrage-Editor zu gelangen, auf der Sie benutzerdefinierten Abfragecode schreiben oder bearbeiten können.

3. Nachdem Sie das Fenster *Abfragen* verlassen haben, wird die Hauptseite des Abfrage-Editors angezeigt. Hier können Sie den Text der Beispielabfragen anzeigen und bearbeiten oder eigene Abfragen von Grund auf neu schreiben.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Screenshot der Seite „Protokolle“ für eine Azure Digital Twins-Instanz im Azure-Portal. Sie enthält eine Liste mit Protokollen, Abfragecode und Abfrageverlauf." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    Linker Bereich: 
    - Die Registerkarte *Tabellen* zeigt die verschiedenen [Protokollkategorien](#log-categories) von Azure Digital Twins, die Sie in Ihren Abfragen verwenden können. 
    - Die Registerkarte *Abfragen* enthält die Beispielabfragen, die Sie in den Editor laden können.
    - Mithilfe der Registerkarte *Filter* können Sie eine gefilterte Ansicht der Daten festlegen, die von der Abfrage zurückgegeben werden.

Ausführlichere Informationen zu Protokollabfragen und deren Erstellung finden Sie in der [Übersicht über Protokollabfragen in Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="log-categories"></a>Protokollkategorien

Nachfolgend finden Sie weitere Details zu den Protokollkategorien, die von Azure Digital Twins erfasst werden.

| Protokollkategorie | BESCHREIBUNG |
| --- | --- |
| ADTModelsOperation | Protokollieren aller API-Aufrufe in Bezug auf Modelle |
| ADTQueryOperation | Protokollieren aller API-Aufrufe in Bezug auf Abfragen |
| ADTEventRoutesOperation | Protokolliert alle API-Aufrufe in Bezug auf Ereignisrouten sowie ausgehende Ereignisse von Azure Digital Twins an einen Endpunktdienst wie Event Grid, Event Hubs und Service Bus. |
| ADTDigitalTwinsOperation | Protokollieren aller API-Aufrufe in Bezug auf einzelne Zwillinge |

Jede Protokollkategorie besteht aus Schreib-, Lese-, Lösch- und Aktionsvorgängen. Diese Kategorien lassen sich REST-API-Aufrufen wie folgt zuordnen:

| Ereignistyp | REST-API-Vorgänge |
| --- | --- |
| Schreiben | PUT und PATCH |
| Lesen | GET |
| Löschen | Delete |
| Aktion | POST |

Im Folgenden finden Sie eine umfassende Liste der Vorgänge und entsprechenden [REST-API-Aufrufe für Azure Digital Twins](/rest/api/azure-digitaltwins/), die in jeder Kategorie protokolliert werden. 

>[!NOTE]
> Jede Protokollkategorie enthält mehrere Vorgänge/REST-API-Aufrufe. In der folgenden Tabelle ist jede Protokollkategorie allen darunter aufgeführten Vorgänge/REST-API-Aufrufen zugeordnet, anschließend folgt die nächste Kategorie. 

| Protokollkategorie | Vorgang | REST-API-Aufrufe und andere Ereignisse |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | API zum Aktualisieren von Digital Twin-Modellen |
|  | Microsoft.DigitalTwins/models/read | APIs zum Abrufen nach ID und Auflisten von Digital Twin-Modellen |
|  | Microsoft.DigitalTwins/models/delete | API zum Löschen von Digital Twin-Modellen |
|  | Microsoft.DigitalTwins/models/action | API zum Hinzufügen von Digital Twin-Modellen |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | API zum Abfragen von Digital Twin-Instanzen |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | API zum Hinzufügen von Ereignisrouten |
|  | Microsoft.DigitalTwins/eventroutes/read | APIs zum Abrufen nach ID und Auflisten von Ereignisrouten |
|  | Microsoft.DigitalTwins/eventroutes/delete | API zum Löschen von Ereignisrouten |
|  | Microsoft.DigitalTwins/eventroutes/action | Fehler beim Veröffentlichen von Ereignissen in einem Endpunktdienst (kein API-Aufruf). |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | Vorgänge zum Hinzufügen, Hinzufügen von Beziehungen, Aktualisieren und Aktualisieren von Komponenten von Digital Twins |
|  | Microsoft.DigitalTwins/digitaltwins/read | Vorgänge zum Abrufen nach ID, Abrufen von Komponenten, Abrufen der Beziehung nach ID, Auflisten eingehender Beziehungen und Auflisten von Beziehungen von Digital Twins |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Vorgänge zum Löschen und Löschen von Beziehungen von Digital Twins |
|  | Microsoft.DigitalTwins/digitaltwins/action | Senden von Komponententelemetrie und Senden von Telemetrie von Digital Twins |

## <a name="log-schemas"></a>Protokollschemas 

Jede Protokollkategorie verfügt über ein Schema, das definiert, wie Ereignisse in dieser Kategorie gemeldet werden. Jeder einzelne Protokolleintrag wird als Text gespeichert und als JSON-Blob formatiert. Die Felder im Protokoll und die JSON-Beispieltexte werden für jeden unten angegebenen Protokolltyp bereitgestellt. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` und `ADTQueryOperation` verwenden ein konsistentes API-Protokollschema. `ADTEventRoutesOperation` erweitert das Schema so, dass es ein `endpointName`-Feld in den Eigenschaften enthält.

### <a name="api-log-schemas"></a>API-Protokollschemas

Dieses Protokollschema ist für `ADTDigitalTwinsOperation`, `ADTModelsOperation` und `ADTQueryOperation` konsistent. Dasselbe Schema wird auch für `ADTEventRoutesOperation` verwendet, mit **Ausnahme** des `Microsoft.DigitalTwins/eventroutes/action` Vorgangsnamens (weitere Informationen zu diesem Schema finden Sie im nächsten Abschnitt: [Ausgangsprotokollschemas](#egress-log-schemas)).

Das Schema enthält Informationen, die für API-Aufrufe an eine Azure Digital Twins-Instanz relevant sind.

Im Folgenden finden Sie die Feld- und Eigenschaftsbeschreibungen für API-Protokolle.

| Feldname | Datentyp | BESCHREIBUNG |
|-----|------|-------------|
| `Time` | Datetime | Datum und Uhrzeit des Auftretens dieses Ereignisses in UTC |
| `ResourceId` | String | Azure Resource Manager-Ressourcen-ID für die Ressource, in der das Ereignis aufgetreten ist |
| `OperationName` | String  | Typ der Aktion, die während des Ereignisses ausgeführt wurde |
| `OperationVersion` | String | Während des Ereignisses verwendete API-Version |
| `Category` | String | Typ der auszugebenden Ressource |
| `ResultType` | String | Ergebnis des Ereignisses |
| `ResultSignature` | String | HTTP-Statuscode für das Ereignis |
| `ResultDescription` | String | Weitere Details zum Ereignis |
| `DurationMs` | String | Zeit für die Ausführung des Ereignisses in Millisekunden |
| `CallerIpAddress` | String | Maskierte Quell-IP-Adresse für das Ereignis |
| `CorrelationId` | Guid | Vom Kunden angegebener eindeutiger Bezeichner für das Ereignis |
| `ApplicationId` | Guid | Bei der Bearer-Autorisierung verwendete Anwendungs-ID |
| `Level` | Int | Protokollschweregrad des Ereignisses |
| `Location` | String | Region, in der das Ereignis aufgetreten ist |
| `RequestUri` | Uri | Während des Ereignisses verwendeter Endpunkt |
| `TraceId` | String | `TraceId` aus dem [W3C-Ablaufverfolgungskontext](https://www.w3.org/TR/trace-context/). Die ID der gesamten Ablaufverfolgung, die zur systemübergreifenden eindeutigen Identifizierung einer verteilten Ablaufverfolgung verwendet wird |
| `SpanId` | String | `SpanId` aus dem [W3C-Ablaufverfolgungskontext](https://www.w3.org/TR/trace-context/). Die ID dieser Anforderung in der Ablaufverfolgung |
| `ParentId` | String | `ParentId` aus dem [W3C-Ablaufverfolgungskontext](https://www.w3.org/TR/trace-context/). Eine Anforderung ohne übergeordnete ID ist der Stamm der Ablaufverfolgung. |
| `TraceFlags` | String | `TraceFlags` aus dem [W3C-Ablaufverfolgungskontext](https://www.w3.org/TR/trace-context/). Steuert Ablaufverfolgungsflags wie Sampling, Ablaufverfolgungsebene usw. |
| `TraceState` | String | `TraceState` aus dem [W3C-Ablaufverfolgungskontext](https://www.w3.org/TR/trace-context/). Zusätzliche herstellerspezifische Informationen zur Ablaufverfolgungsidentifizierung, die verschiedene Systeme für die verteilte Ablaufverfolgung umfassen sollen. |

Im Folgenden finden Sie Beispiel-JSON-Texte für diese Arten von Protokollen.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": 8,
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "80",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

Hier sehen Sie einen JSON-Beispieltext für `ADTEventRoutesOperation`, der **nicht** vom `Microsoft.DigitalTwins/eventroutes/action`-Typ ist (weitere Informationen zu diesem Schema finden Sie im nächsten Abschnitt: [Ausgangsprotokollschemas](#egress-log-schemas)).

```json
  {
    "time": "2020-10-30T22:18:38.0708705Z",
    "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
    "operationName": "Microsoft.DigitalTwins/eventroutes/write",
    "operationVersion": "2020-10-31",
    "category": "EventRoutesOperation",
    "resultType": "Success",
    "resultSignature": "204",
    "resultDescription": "",
    "durationMs": 42,
    "callerIpAddress": "212.100.32.*",
    "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
    "identity": {
      "claims": {
        "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
      }
    },
    "level": "4",
    "location": "southcentralus",
    "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/EventRoutes/egressRouteForEventHub?api-version=2020-10-31",
    "properties": {},
    "traceContext": {
      "traceId": "95ff77cfb300b04f80d83e64d13831e7",
      "spanId": "b630da57026dd046",
      "parentId": "9f0de6dadae85945",
      "traceFlags": "01",
      "tracestate": "k1=v1,k2=v2"
    }
  },
```

### <a name="egress-log-schemas"></a>Ausgangsprotokollschemas

Das folgende Beispiel ist das Schema für `ADTEventRoutesOperation`-Protokolle, die für den Vorgangsnamen `Microsoft.DigitalTwins/eventroutes/action` spezifisch sind. Diese enthalten Details zu Ausnahmen und den API-Vorgängen für Ausgangsendpunkte, die mit einer Instanz von Azure Digital Twins verbunden sind.

|Feldname | Datentyp | BESCHREIBUNG |
|-----|------|-------------|
| `Time` | Datetime | Datum und Uhrzeit des Auftretens dieses Ereignisses in UTC |
| `ResourceId` | String | Azure Resource Manager-Ressourcen-ID für die Ressource, in der das Ereignis aufgetreten ist |
| `OperationName` | String  | Typ der Aktion, die während des Ereignisses ausgeführt wurde |
| `Category` | String | Typ der auszugebenden Ressource |
| `ResultDescription` | String | Weitere Details zum Ereignis |
| `CorrelationId` | Guid | Vom Kunden angegebener eindeutiger Bezeichner für das Ereignis |
| `ApplicationId` | Guid | Bei der Bearer-Autorisierung verwendete Anwendungs-ID |
| `Level` | Int | Protokollschweregrad des Ereignisses |
| `Location` | String | Region, in der das Ereignis aufgetreten ist |
| `TraceId` | String | `TraceId` aus dem [W3C-Ablaufverfolgungskontext](https://www.w3.org/TR/trace-context/). Die ID der gesamten Ablaufverfolgung, die zur systemübergreifenden eindeutigen Identifizierung einer verteilten Ablaufverfolgung verwendet wird |
| `SpanId` | String | `SpanId` aus dem [W3C-Ablaufverfolgungskontext](https://www.w3.org/TR/trace-context/). Die ID dieser Anforderung in der Ablaufverfolgung |
| `ParentId` | String | `ParentId` aus dem [W3C-Ablaufverfolgungskontext](https://www.w3.org/TR/trace-context/). Eine Anforderung ohne übergeordnete ID ist der Stamm der Ablaufverfolgung. |
| `TraceFlags` | String | `TraceFlags` aus dem [W3C-Ablaufverfolgungskontext](https://www.w3.org/TR/trace-context/). Steuert Ablaufverfolgungsflags wie Sampling, Ablaufverfolgungsebene usw. |
| `TraceState` | String | `TraceState` aus dem [W3C-Ablaufverfolgungskontext](https://www.w3.org/TR/trace-context/). Zusätzliche herstellerspezifische Informationen zur Ablaufverfolgungsidentifizierung, die verschiedene Systeme für die verteilte Ablaufverfolgung umfassen sollen. |
| `EndpointName` | String | Name des Ausgangsendpunkts, der in Azure Digital Twins erstellt wurde |

Im Folgenden finden Sie Beispiel-JSON-Texte für diese Arten von Protokollen.

#### <a name="adteventroutesoperation-for-microsoftdigitaltwinseventroutesaction"></a>ADTEventRoutesOperation für Microsoft.DigitalTwins/eventroutes/action

Hier sehen Sie einen JSON-Beispieltext für `ADTEventRoutesOperation` vom Typ `Microsoft.DigitalTwins/eventroutes/action`.

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "operationVersion": "",
  "category": "EventRoutesOperation",
  "resultType": "",
  "resultSignature": "",
  "resultDescription": "Unable to send EventHub message to [myPath] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "durationMs": -1,
  "callerIpAddress": "",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "",
  "properties": {
    "endpointName": "myEventHub"
  },
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
},
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Konfiguration von Diagnosen finden Sie unter [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](../azure-monitor/essentials/platform-logs-overview.md).
* Informationen zu den Azure Digital Twins-Metriken finden Sie unter [Troubleshooting: Metrics](troubleshoot-metrics.md) (Problembehandlung: Metriken).
* Informationen zur Aktivierung von Warnungen für Ihre Metriken finden Sie unter [Problembehandlung von Warnungen](troubleshoot-alerts.md).