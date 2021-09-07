---
title: Gewusst wie ... in Azure Application Insights | Microsoft-Dokumentation
description: Häufig gestellte Fragen in Application Insights
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 3c74add1d426a6d1e383ed90388bb319a8244e05
ms.sourcegitcommit: 8154d7f8642d783f637cf6d857b4abbe28033f53
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/12/2021
ms.locfileid: "113616314"
---
# <a name="how-do-i--in-application-insights"></a>Gewusst wie – in Application Insights
## <a name="get-an-email-when-"></a>Wie erhalte ich eine E-Mail-Nachricht, wenn...
### <a name="email-if-my-site-goes-down"></a>E-Mail, wenn die Website nicht mehr funktioniert
Richten Sie einen [Webtest zur Verfügbarkeit](./monitor-web-app-availability.md)ein.

### <a name="email-if-my-site-is-overloaded"></a>E-Mail, wenn meine Website überlastet ist
Richten Sie eine [Warnung](../alerts/alerts-log.md) zur **Serverantwortzeit** ein. Geeignet ist in der Regel ein Schwellenwert zwischen 1 und 2 Sekunden.

![Screenshot, der zeigt, wie eine Warnung für die Serverantwortzeit festgelegt wird](./media/how-do-i/030-server.png)

Auch die Rückgabe von Fehlercodes kann ein Hinweis auf die Belastung Ihrer App sein. Richten Sie eine Warnung über **Anforderungsfehler** ein.

Wenn Sie eine Warnung für **Serverausnahmen** einrichten möchten, müssen Sie möglicherweise [einige zusätzliche Konfigurationsschritte](./asp-net-exceptions.md) vornehmen, damit die Daten einsehbar sind.

### <a name="email-on-exceptions"></a>E-Mail für Ausnahmen
1. [Einrichten der Ausnahmeüberwachung](./asp-net-exceptions.md)
2. [Einrichten einer Warnung](../alerts/alerts-log.md) für die Metrik der Anzahl von Ausnahmen

### <a name="email-on-an-event-in-my-app"></a>E-Mail über ein Ereignis in meiner App
Angenommen, Sie möchten eine E-Mail erhalten, wenn ein bestimmtes Ereignis eintritt. Diese Funktion ist in Application Insights nicht in dieser Form verfügbar, aber es kann [eine Warnung gesendet werden, wenn eine Metrik einen Schwellenwert überschreitet](../alerts/alerts-log.md).

Warnungen können für [benutzerdefinierte Metriken](./api-custom-events-metrics.md#trackmetric), jedoch nicht für benutzerdefinierte Ereignisse eingerichtet werden. Mithilfe von Code können Sie eine Metrik erhöhen, wenn das Ereignis eintritt:

```csharp
telemetry.TrackMetric("Alarm", 10);
```

oder:

```csharp
var measurements = new Dictionary<string,double>();
measurements ["Alarm"] = 10;
telemetry.TrackEvent("status", null, measurements);
```

Da Warnungen zwei Zustände aufweisen, müssen Sie einen niedrigen Wert senden, wenn das Ende der Warnung erreicht ist:

```csharp
telemetry.TrackMetric("Alarm", 0.5);
```

Erstellen Sie ein Diagramm im [Metrik-Explorer](../essentials/metrics-charts.md) , um Ihre Warnung anzuzeigen:

![Screenshot, der zeigt, wie Sie im Metrik-Explorer ein Diagramm zum Anzeigen Ihrer Warnung erstellen](./media/how-do-i/010-alarm.png)

Richten Sie jetzt eine Warnung ein, die ausgelöst wird, wenn die Metrik für kurze Zeit einen mittleren Wert überschreitet:

![Screenshot, der zeigt, wie Sie festlegen, dass eine Warnung ausgelöst wird, wenn die Metrik für kurze Zeit einen mittleren Wert überschreitet](./media/how-do-i/020-threshold.png)

Legen Sie den Durchschnittszeitraum auf den Mindestwert fest.

Sie erhalten E-Mails, wenn die Metrik den Schwellenwert überschreitet und auch unterschreitet.

Zu berücksichtigende Punkte:

* Eine Warnung verfügt über zwei Zustände "Warnung" und "Fehlerfrei". Der Status wird nur ausgewertet, wenn eine Metrik empfangen wird.
* Nur bei einer Änderung des Status wird eine E-Mail gesendet. Aus diesem Grund müssen Sie Metriken mit sowohl hohen als auch niedrigen Werten senden.
* Zur Auswertung der Warnung wird der Durchschnitt der empfangenen Werte über den vorherigen Zeitraum zugrunde gelegt. Dies erfolgt immer dann, wenn eine Metrik empfangen wird, sodass E-Mails öfter gesendet werden können, als durch den angegebenen Zeitraum festgelegt.
* Da sowohl für den Zustand "Warnung" als auch für den Zustand "Fehlerfrei" E-Mails gesendet werden, sollten Sie das einmalige Ereignis als Vorfall mit zwei Zuständen betrachten. Beispiel: Statt eines Ereignisses mit dem Status "Abgeschlossen" verwenden Sie den Zustand "In Bearbeitung" und erhalten E-Mails am Anfang und Ende des betreffenden Auftrags.

### <a name="set-up-alerts-automatically"></a>Automatisches Einrichten von Warnungen
[Erstellen neuer Warnungen mithilfe von PowerShell](../alerts/alerts-log.md)

## <a name="use-powershell-to-manage-application-insights"></a>Verwalten von Application Insights mithilfe von PowerShell
* [Erstellen neuer Ressourcen](./create-new-resource.md#creating-a-resource-automatically)
* [Erstellen neuer Warnungen](../alerts/alerts-log.md)

## <a name="separate-telemetry-from-different-versions"></a>Trennen der Telemetriedaten von verschiedenen Versionen

* Mehrere Rollen in einer App: Verwenden Sie eine einzelne Application Insights-Ressource, und filtern Sie nach [cloud_Rolename](./app-map.md).
* Trennen von Entwicklungs-, Test- und Produktionsversionen: Verwenden Sie verschiedene Application Insights-Ressourcen. Übernehmen Sie die Instrumentierungsschlüssel aus „web.config“. [Weitere Informationen](./separate-resources.md)
* Berichterstellung für Buildversionen: Fügen Sie mithilfe eines Telemetrieinitialisierers eine Eigenschaft hinzu. [Weitere Informationen](./separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Überwachen von Back-End-Servern und Desktop-Apps
[Verwenden Sie das Windows Server-SDK-Modul](./windows-desktop.md).

## <a name="visualize-data"></a>Visualisieren von Daten
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Dashboard mit Metriken aus mehreren Apps
* Passen Sie Ihr Diagramm im [Metrik-Explorer](../essentials/metrics-charts.md) an, und speichern Sie es als Favorit. Heften Sie es an das Azure-Dashboard an.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Dashboard mit Daten aus anderen Quellen und Application Insights
* [Exportieren Sie die Telemetrie in Power BI](./export-power-bi.md).

oder

* Verwenden Sie SharePoint als Dashboard, und zeigen Sie Daten in SharePoint-Webparts an. [Nutzen Sie fortlaufenden Export und Stream Analytics für den Export in SQL](./code-sample-export-sql-stream-analytics.md).  Überprüfen Sie die Datenbank mithilfe von PowerView, und erstellen Sie ein SharePoint-Webpart für PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Herausfiltern anonymer oder authentifizierter Benutzer
Wenn die Benutzer sich anmelden, können Sie die [ID für authentifizierte Benutzer](./api-custom-events-metrics.md#authenticated-users) festlegen. (Dies erfolgt nicht automatisch.)

Sie können anschließend folgende Aktionen durchführen:

* Suchen nach bestimmten Benutzer-IDs

![Screenshot, der die Optionen für das Suchen nach bestimmten Benutzer-IDs zeigt](./media/how-do-i/110-search.png)

* Filtern von Metriken für anonyme oder authentifizierte Benutzer

![Screenshot, der das Filtern von Metriken für anonyme oder authentifizierte Benutzer zeigt](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Ändern von Eigenschaftennamen oder -werten
Erstellen Sie einen [Filter](./api-filtering-sampling.md#filtering). So können Sie Telemetrie ändern oder filtern, bevor sie von Ihrer App aus an Application Insights gesendet wird.

## <a name="list-specific-users-and-their-usage"></a>Auflisten bestimmter Benutzer und deren Nutzung
Wenn Sie nur [nach bestimmten Benutzern suchen möchten](#search-specific-users), können Sie die [ID für authentifizierte Benutzer](./api-custom-events-metrics.md#authenticated-users) festlegen.

Wenn Sie eine Liste von Benutzern mit bestimmten Daten erstellen möchten, z. B., welche Seiten sich die Benutzer ansehen oder wie oft sie sich anmelden, haben Sie zwei Möglichkeiten:

* [Legen Sie die ID für authentifizierte Benutzer fest](./api-custom-events-metrics.md#authenticated-users), [exportieren Sie die Daten in eine Datenbank](./code-sample-export-sql-stream-analytics.md), und analysieren Sie dort die Benutzerdaten mit den entsprechenden Tools.
* Wenn Sie nur über eine kleine Anzahl von Benutzern verfügen, senden Sie benutzerdefinierte Ereignisse oder Metriken, verwenden Sie dabei die relevanten Daten als Metrikwert oder Ereignisname, und legen Sie die Benutzer-ID als Eigenschaft fest. Um Seitenansichten zu analysieren, ersetzen Sie den standardmäßigen JavaScript-trackPageView-Aufruf. Um die serverseitige Telemetrie zu analysieren, fügen Sie die Benutzer-ID mithilfe eines Telemetrieinitialisierers allen Servertelemetriedaten hinzu. Sie können dann Metriken und Suchvorgänge nach der Benutzer-ID filtern und segmentieren.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Reduzierung des Datenverkehrs von einer App an Application Insights
* Deaktivieren Sie in [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) alle nicht benötigten Module, z.B. die Leistungsindikatorsammlung.
* Verwenden Sie im SDK [Stichprobenerstellung und Filterung](./api-filtering-sampling.md) .
* Begrenzen Sie für Ihre Webseiten die Anzahl der AJAX-Aufrufe, die für jede Seitenansicht gemeldet werden. Fügen Sie im Skriptausschnitt hinter `instrumentationKey:...` Folgendes ein: `,maxAjaxCallsPerView:3` (oder eine geeignete Anzahl).
* Berechnen Sie bei Verwendung von [TrackMetric](./api-custom-events-metrics.md#trackmetric)das Aggregat der Batches von Metrikwerten vor dem Senden des Ergebnisses. Eine Überladung von TrackMetric() ist dafür vorgesehen.

Erfahren Sie mehr über [Preise und Kontingente](./pricing.md).

## <a name="disable-telemetry"></a>Telemetrie deaktivieren
So können Sie die Sammlung und Übermittlung von Telemetriedaten aus dem Server **dynamisch beenden und starten** :

### <a name="aspnet-classic-applications"></a>Klassische ASP.NET-Anwendungen

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Andere Anwendungen
Es wird nicht empfohlen, den `TelemetryConfiguration.Active`-Singleton in Konsolenanwendungen oder ASP.NET Core-Anwendungen zu verwenden.
Wenn Sie die `TelemetryConfiguration`-Instanz selbst erstellt haben, legen Sie `DisableTelemetry` auf `true` fest.

Für ASP.NET Core-Anwendungen können Sie auf die `TelemetryConfiguration`-Instanz mit [ASP.NET Core-Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection/) zugreifen. Weitere Einzelheiten finden Sie im Artikel [ApplicationInsights für ASP.NET Core-Anwendungen](./asp-net-core.md).

## <a name="disable-selected-standard-collectors"></a>Deaktivieren der ausgewählten Standardcollectors
Sie können Standardcollectors (z. B. Leistungsindikatoren, HTTP-Anforderungen oder Abhängigkeiten) deaktivieren.

* **ASP.NET-Anwendungen**: Löschen oder kommentieren Sie die relevanten Zeilen in [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) aus.
* **ASP.NET Core-Anwendungen**: Übernehmen Sie die Konfigurationsoptionen für Telemetriemodule in [ApplicationInsights ASP.NET Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="view-system-performance-counters"></a>Anzeigen von Systemleistungsindikatoren
Zu den Metriken, die Sie im Metrik-Explorer anzeigen können, zählt u. a. eine Gruppe von Systemleistungsindikatoren. Mehrere dieser Leistungsindikatoren werden auf dem vordefinierten Blatt **Server** angezeigt.

![Öffnen Sie die Application Insights-Ressource, und klicken Sie auf "Server".](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Wenn keine Leistungsindikatordaten angezeigt werden
* **IIS-Server** auf Ihrem eigenen oder auf einem virtuellen Computer. [Installieren Sie den Azure Monitor Application Insights-Agent.](./status-monitor-v2-overview.md)
* **Azure-Website** – Leistungsindikatoren werden noch nicht unterstützt. Sie können jedoch mehrere Metriken über die Systemsteuerung der Azure-Website standardmäßig abrufen.
* **Unix-Server** - [Installieren Sie collectd](java-2x-collectd.md)

### <a name="to-display-more-performance-counters"></a>Anzeigen weiterer Leistungsindikatoren
* Fügen Sie zunächst [ein neues Diagramm hinzu](../essentials/metrics-charts.md) , und prüfen Sie dann, ob der gewünschte Leistungsindikator im angebotenen grundlegenden Satz enthalten ist.
* Wenn dies nicht der Fall ist, [fügen Sie den Leistungsindikator dem über das Leistungsindikatormodul erfassten Satz hinzu](./performance-counters.md).

