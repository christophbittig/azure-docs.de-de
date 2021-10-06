---
title: Datenaufbewahrung und -speicherung in Azure Application Insights | Microsoft-Dokumentation
description: Hinweis zur Datenaufbewahrung und Datenschutzrichtlinie
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: d0ef5131d97b16e63e40c903d456713eddb84218
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232720"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Datensammlung, -aufbewahrung und -speicherung in Application Insights

Wenn Sie das [Azure Application Insights][start] SDK in Ihrer App installieren, werden Telemetriedaten für Ihre App an die Cloud gesendet. Als Entwickler möchten Sie natürlich genau wissen, welche Daten gesendet werden, was mit den Daten geschieht und wie Sie die Kontrolle darüber behalten. Dabei ist insbesondere interessant, ob womöglich sensible Daten übermittelt werden, wo sie gespeichert werden und wie sicher die Daten sind. 

Zunächst die kurze Antwort:

* Bei Verwendung der Standardtelemetriemodule in der vorkonfigurierten Form ist es äußerst unwahrscheinlich, dass sensible Daten an den Dienst übermittelt werden. Bei der Telemetrie dreht sich alles um Lade-, Leistungs- und Nutzungsmetriken sowie um Ausnahmeberichte und andere Diagnosedaten. In den Diagnoseberichten sind als Benutzerdaten hauptsächlich URLs sichtbar. Ihre App sollte jedoch in keinem Fall sensible Daten im Nur-Text-Format in eine URL einfügen.
* Sie können Code schreiben, der zusätzliche benutzerdefinierte Telemetriedaten sendet, die Sie bei der Diagnose und Überwachung der Nutzung unterstützen. (Diese Erweiterbarkeit ist ein großartiges Feature von Application Insights.) Der Code kann theoretisch so geschrieben werden, dass versehentlich auch persönliche und andere sensible Daten einbezogen werden. Falls Ihre Anwendung mit solchen Daten arbeitet, müssen Sie sämtlichen Code, den Sie schreiben, einer gründlichen Prüfung unterziehen.
* Beim Entwickeln und Testen Ihrer App können Sie problemlos überprüfen, was vom SDK gesendet wird. Die Daten erscheinen in den Debugging-Ausgabefenstern von IDE und Browser.
* Sie können den Speicherort auswählen, wenn Sie eine neue Application Insights-Ressource erstellen. Weitere Informationen über die Verfügbarkeit von Application Insights pro Region finden Sie [hier](https://azure.microsoft.com/global-infrastructure/services/?products=all).
*   Überprüfen Sie die gesammelten Daten, da hierzu möglicherweise Daten zählen, die in einigen Fällen zulässig sind, in anderen hingegen nicht.  Ein gutes Beispiel dafür ist der Gerätename. Der Gerätename eines Servers hat keine datenschutzrelevanten Auswirkungen und ist hilfreich, der Gerätename eines Smartphones oder Laptops kann sich jedoch auf die Privatsphäre beziehen, und er ist möglicherweise weniger hilfreich. Ein SDK, das hauptsächlich auf Server abzielt, würde den Gerätenamen standardmäßig erfassen, und dies müsste sowohl für normale Ereignisse als auch Ausnahmen überschrieben werden.

Im Rest dieses Artikels werden die obigen Antworten näher erläutert. Der Artikel ist in sich geschlossen, sodass Sie ihn auch Kollegen zeigen können, die nicht direkt Ihrem Team angehören.

## <a name="what-is-application-insights"></a>Was ist Application Insights?
[Azure Application Insights][start] ist ein Dienst von Microsoft, der Sie beim Optimieren der Leistung und Benutzerfreundlichkeit Ihrer aktiven Anwendung unterstützt. Er überwacht Ihre Anwendung während der gesamten Ausführung – sowohl in der Testphase als auch nach der Veröffentlichung oder Bereitstellung. Application Insights erstellt Diagramme und Tabellen, die beispielsweise Aufschluss darüber geben, zu welchen Tageszeiten das Benutzerinteresse besonders groß ist, wie gut die App reagiert und wie gut sie von externen Diensten versorgt wird, von denen sie unter Umständen abhängig ist. Im Falle von Abstürzen, Fehlern oder Leistungsproblemen können Sie die Telemetriedaten im Detail durchsuchen, um die Fehlerursache zu ermitteln. Darüber hinaus informiert Sie der Dienst per E-Mail, falls sich die Verfügbarkeit oder Leistung Ihrer App ändert.

Diese Funktionen erhalten Sie, indem Sie das Application Insights-SDK in Ihrer Anwendung installieren und es so in den Code integrieren. Während der App-Ausführung überwacht das SDK den Betrieb und sendet Telemetriedaten an den Application Insights-Dienst. Hierbei handelt es sich um einen von [Microsoft Azure](https://azure.com)gehosteten Clouddienst. (Application Insights kann aber nicht nur für in Azure gehostete Anwendungen, sondern für jede beliebige Anwendung verwendet werden.)

Der Application Insights-Dienst speichert und analysiert die Telemetriedaten. Wenn Sie sich die Analyse ansehen oder die gespeicherten Telemetriedaten durchsuchen möchten, melden Sie sich bei Ihrem Azure-Konto an, und öffnen Sie die Application Insights-Ressource für Ihre Anwendung. Sie können auch anderen Mitgliedern Ihres Teams oder bestimmten Azure-Abonnenten Zugriff auf die Daten gewähren.

Die Daten können vom Application Insights-Dienst exportiert werden (etwa in eine Datenbank oder in externe Tools). Für jedes Tool muss ein spezieller Schlüssel angegeben werden, den Sie über den Dienst erhalten. Der Schlüssel kann bei Bedarf widerrufen werden. 

Application Insights SDKs stehen für eine Reihe von Anwendungstypen zur Verfügung. Hierzu zählen auf eigenen Java EE- oder ASP.NET-Servern oder in Azure gehostete Webdienste, Webclients (also der innerhalb einer Webseite ausgeführte Code), Desktop-Apps und -Dienste sowie Geräte-Apps (etwa für Windows Phone, iOS und Android). Alle diese Anwendungen senden Telemetriedaten an den gleichen Dienst.

## <a name="what-data-does-it-collect"></a>Welche Daten werden dabei gesammelt?
Es werden drei Datenquellen verwendet:

* Das SDK, das Sie entweder [bei der Entwicklung](./asp-net.md) oder [zur Laufzeit](./status-monitor-v2-overview.md) in Ihre App integrieren. Für die unterschiedlichen Anwendungstypen stehen jeweils unterschiedliche SDKs zur Verfügung. Darunter ist auch ein [SDK für Webseiten](./javascript.md), das zusammen mit der Seite in den Browser des Endbenutzers geladen wird.
  
  * Jedes SDK verfügt über eine Reihe von [Modulen](./configuration-with-applicationinsights-config.md), die mithilfe unterschiedlicher Techniken verschiedene Arten von Telemetriedaten erfassen.
  * Wenn Sie das SDK während der Entwicklung installieren, können Sie zusätzlich zu den Standardmodulen die API des SDK verwenden, um Ihre eigenen Telemetriedaten zu senden. Diese benutzerdefinierten Telemetriedaten können beliebige Daten enthalten, die Sie senden möchten.
* Einige Webserver verfügen auch über Agents, die gemeinsam mit der App ausgeführt werden und Telemetriedaten zu CPU-, Arbeitsspeicher- und Netzwerkauslastung senden. Solche Agents können etwa bei Azure-VMs, Docker-Hosts und [Java EE-Servern](java-2x-agent.md) vorhanden sein.
* [Verfügbarkeitstests](./monitor-web-app-availability.md) werden von Microsoft in regelmäßigen Abständen Anforderungen an Ihre Web-App gesendet. Die Ergebnisse werden dann an den Application Insights-Dienst gesendet.

### <a name="what-kinds-of-data-are-collected"></a>Welche Arten von Daten werden gesammelt?
Die gesammelten Daten lassen sich in folgende Hauptkategorien unterteilen:

* [Webserver-Telemetriedaten](./asp-net.md) : HTTP-Anforderungen.  URI, Anforderungsverarbeitungsdauer, Antwortcode, Client-IP-Adresse. `Session id`.
* [Webseiten](./javascript.md) : Seiten-, Benutzer- und Sitzungszähler. Seitenladezeiten. Ausnahmen. AJAX-Aufrufe.
* Leistungsindikatoren: Arbeitsspeicher-, CPU-, E/A-, Netzwerkauslastung.
* Client- und Serverkontext: Betriebssystem, Gebietsschema, Gerätetyp, Browser, Bildschirmauflösung.
* [Ausnahmen](./asp-net-exceptions.md) und Abstürze: **Stapelabbilder**, `build id`, CPU-Typ. 
* [Abhängigkeiten](./asp-net-dependencies.md) : Aufrufe an externe Dienste wie etwa REST, SQL und AJAX. URI oder Verbindungszeichenfolge, Dauer, Erfolg, Befehl.
* [Verfügbarkeitstests](./monitor-web-app-availability.md) : Testdauer und Schritte, Antworten.
* [Ablaufverfolgungsprotokolle](./asp-net-trace-logs.md) und [benutzerdefinierte Telemetriedaten](./api-custom-events-metrics.md) - **alles, was Sie als Code in Ihre Protokolle oder Telemetrie integrieren**.

[Weitere Details](#data-sent-by-application-insights)

## <a name="how-can-i-verify-whats-being-collected"></a>Wie kann ich überprüfen, was gesammelt wird?
Wenn Sie die App mit Visual Studio entwickeln, führen Sie sie im Debugmodus (F5) aus. Die Telemetriedaten werden im Ausgabefenster angezeigt. Dort können Sie sie kopieren und zur einfachen Überprüfung als JSON formatieren. 

![Screenshot: Ausführung der App im Debugmodus in Visual Studio](./media/data-retention-privacy/06-vs.png)

Es gibt auch eine lesbarere Ansicht im Fenster „Diagnose“.

Öffnen Sie bei einer Webseite das Debuggingfenster Ihres Browsers.

![Drücken Sie F12, und öffnen Sie die Registerkarte „Netzwerk“.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Kann ich Code schreiben, um die Telemetriedaten vor dem Senden zu filtern?
Hierzu können Sie ein [Telemetriedaten-Prozessor-Plug-In](./api-filtering-sampling.md)schreiben.

## <a name="how-long-is-the-data-kept"></a>Wie lange werden Daten aufbewahrt?
Rohdatenpunkte (also Elemente, die Sie in Analytics abfragen und in Search überprüfen können) werden bis zu 730 Tage lang aufbewahrt. Sie können eine [Aufbewahrungsdauer](./pricing.md#change-the-data-retention-period) von 30, 60, 90, 120, 180, 270, 365, 550 oder 730 Tagen auswählen. Wenn Sie Daten länger als 730 Tage aufbewahren möchten, können Sie sie während der Datenerfassung mit [Fortlaufender Export](./export-telemetry.md) in ein Speicherkonto kopieren. 

Für Daten, die länger als 90 Tage aufbewahrt werden, fallen zusätzliche Gebühren an. Weitere Informationen zu Application Insights-Preisen finden Sie in der [Preisübersicht für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Aggregierte Daten (d.h. Zählungen, Mittelwerte und andere statistischen Daten, die im Metrik-Explorer angezeigt werden) werden im Maß von 1 Minute für 90 Tage aufbewahrt.

[Debugmomentaufnahmen](./snapshot-debugger.md) werden 15 Tage lang gespeichert. Diese Aufbewahrungsrichtlinie wird für jede Anwendung separat festgelegt. Wenn Sie diesen Wert erhöhen möchten, können Sie eine Erhöhung anfordern, indem Sie einen Supportfall im Azure-Portal eröffnen.

## <a name="who-can-access-the-data"></a>Wer kann auf die Daten zugreifen?
Die Daten sind für Sie und, wenn Sie über ein Unternehmenskonto verfügen, für die Teammitglieder sichtbar. 

Sie und Ihre Teammitglieder können die Daten exportieren und an andere Speicherorte kopieren oder Sie an andere Personen weiterleiten.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Wie verwendet Microsoft die Daten, die meine App an Application Insights sendet?
Microsoft verwendet die Daten nur dazu, Ihnen den Dienst bereitstellen zu können.

## <a name="where-is-the-data-held"></a>Wo werden die Daten gespeichert?
* Sie können den Speicherort auswählen, wenn Sie eine neue Application Insights-Ressource erstellen. Weitere Informationen über die Verfügbarkeit von Application Insights pro Region finden Sie [hier](https://azure.microsoft.com/global-infrastructure/services/?products=all).

## <a name="how-secure-is-my-data"></a>Wie sicher sind meine Daten?
Application Insights ist ein Azure-Dienst. Sicherheitsrichtlinien werden im Whitepaper [Azure Security, Privacy, and Compliance (Azure-Sicherheit, -Datenschutz und -Kompatibilität)](https://go.microsoft.com/fwlink/?linkid=392408) beschrieben.

Die Daten werden auf Microsoft Azure-Servern gespeichert. Für Konten im Azure-Portal sind die Kontobeschränkungen im Dokument [Trusted Cloud: Microsoft Azure Security, Privacy, and Compliance](https://go.microsoft.com/fwlink/?linkid=392408)(in englischer Sprache) beschrieben.

Der Zugriff auf Ihre Daten durch Microsoft-Mitarbeiter ist eingeschränkt. Wir werden nur mit Ihrer Erlaubnis und ausschließlich dann auf Ihre Daten zugreifen, wenn es erforderlich ist, Sie bei der Verwendung von Application Insights zu unterstützen. 

Aggregierte Daten in den Anwendungen unserer Kunden (z. B. Datenraten und die durchschnittliche Größe von Ablaufverfolgungen) werden zur Verbesserung von Application Insights verwendet.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Könnte die Telemetrie eines anderen Benutzers meine Application Insights-Daten beeinträchtigen?
Sie könnten zusätzliche Telemetrie mit dem Instrumentationsschlüssel im Code Ihrer Webseiten an Ihr Konto senden. Bei genügend zusätzlichen Daten würden die Metriken die Leistung und Nutzung Ihrer App nicht ordnungsgemäß darstellen.

Wenn Sie Code für andere Projekte freigeben, sollten Sie Ihren Instrumentationsschlüssel entfernen.

## <a name="is-the-data-encrypted"></a>Werden die Daten verschlüsselt?
Alle Daten sind im Ruhezustand und während der Übertragung zwischen Rechenzentren verschlüsselt.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Werden die Daten während der Übertragung von meiner Anwendung zu Application Insights-Servern verschlüsselt?
Ja, wir verwenden HTTPS zum Senden von Daten an das Portal aus nahezu allen SDKs, darunter Webserver, Geräte und HTTPS-Webseiten. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>Erstellt das SDK einen temporären lokalen Speicher?

Ja, bestimmte Telemetriekanäle speichern Daten dauerhaft lokal, wenn ein Endpunkt nicht erreicht werden kann. Weiter unten in diesem Artikel erfahren Sie, welche Frameworks und Telemetriekanäle betroffen sind.

Telemetriekanäle, die lokalen Speicher nutzen, erstellen temporäre Dateien in den Verzeichnissen TEMP oder APPDATA, die auf das Konto beschränkt sind, in dem Ihre Anwendung ausgeführt wird. Dies kann passieren, wenn ein Endpunkt vorübergehend nicht verfügbar war oder das Drosselungslimit erreicht wurde. Sobald das Problem gelöst wurde, setzt der Telemetriekanal das Senden aller neuen und dauerhaft gespeicherten Daten fort.

Diese dauerhaft gespeicherten Daten werden nicht lokal verschlüsselt. Falls dies ein Problem darstellt, überprüfen Sie die Daten, und schränken Sie die Sammlung von privaten Daten ein. (Weitere Informationen finden Sie unter [Exportieren und Löschen personenbezogener Daten](../logs/personal-data-mgmt.md#how-to-export-and-delete-private-data).)

Wenn ein Kunde dieses Verzeichnis mit bestimmten Sicherheitsanforderungen konfigurieren muss, kann diese Konfiguration pro Framework erfolgen. Stellen Sie sicher, dass der Prozess, der Ihre Anwendung ausführt, über Schreibzugriff auf dieses Verzeichnis verfügt. Stellen Sie aber außerdem sicher, dass dieses Verzeichnis geschützt ist, um zu verhindern, dass Telemetriedaten von Benutzern gelesen werden, die dafür nicht zugelassen sind.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` wird für die dauerhafte Speicherung von Daten verwendet. Dieser Speicherort kann nicht über das config-Verzeichnis konfiguriert werden, und die Zugriffsberechtigungen für diesen Ordner sind auf einen bestimmten Benutzer mit den erforderlichen Anmeldeinformationen beschränkt. (Weitere Informationen finden Sie unter [Implementierung](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.NET

Standardmäßig verwendet `ServerTelemetryChannel` den lokalen Ordner des aktuellen Benutzers für App-Daten (`%localAppData%\Microsoft\ApplicationInsights`) oder den temporären Ordner (`%TMP%`). (Näheres dazu finden Sie in der [Implementierung](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84).)


Per Konfigurationsdatei:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Per Code:

- Entfernen Sie „ServerTelemetryChannel“ aus der Konfigurationsdatei.
- Fügen Sie diesen Codeausschnitt zu Ihrer Konfiguration hinzu:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Standardmäßig verwendet `ServerTelemetryChannel` den lokalen Ordner des aktuellen Benutzers für App-Daten (`%localAppData%\Microsoft\ApplicationInsights`) oder den temporären Ordner (`%TMP%`). (Näheres dazu finden Sie in der [Implementierung](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84).) 

In einer Linux-Umgebung ist der lokale Speicher deaktiviert, sofern kein Speicherordner angegeben wird.

> [!NOTE]
> Ab Version 2.15.0-beta3 wird nun automatisch lokaler Speicher für Linux, Mac und Windows erstellt. Für Nicht-Windows-Systeme erstellt das SDK automatisch einen lokalen Speicherordner auf der Grundlage der folgenden Logik:
> - `${TMPDIR}`: Wenn die Umgebungsvariable `${TMPDIR}` festgelegt ist, wird dieser Speicherort verwendet.
> - `/var/tmp`: Wenn der vorherige Speicherort nicht vorhanden ist, versuchen wir `/var/tmp`.
> - `/tmp`: Wenn die beiden vorherigen Speicherorte nicht vorhanden sind, versuchen wir `tmp`. 
> - Wenn keiner dieser Speicherorte vorhanden ist, wird kein lokaler Speicher erstellt, wodurch eine manuelle Konfiguration weiterhin erforderlich ist. [Vollständige Details zur Implementierung.](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860)

Der folgende Codeausschnitt zeigt, wie Sie `ServerTelemetryChannel.StorageFolder` in der `ConfigureServices()`-Methode Ihrer `Startup.cs`-Klasse festlegen:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Weitere Informationen finden Sie unter [AspNetCore Custom Configuration](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

Standardmäßig wird `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` für die dauerhafte Speicherung von Daten verwendet. Die Berechtigungen für den Zugriff auf diesen Ordner sind auf den aktuellen Benutzer und Administratoren beschränkt. (Näheres dazu finden Sie in der [Implementierung](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts).)

Das Ordnerpräfix `appInsights-node` kann überschrieben werden, indem der Laufzeitwert der statischen Variablen `Sender.TEMPDIR_PREFIX` geändert wird, die sich in [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384) befindet.

### <a name="javascript-browser"></a>JavaScript (Browser)

Der [HTML5-Sitzungsspeicher](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) wird verwendet, um Daten dauerhaft zu speichern. Es werden zwei separate Puffer verwendet: `AI_buffer` und `AI_sent_buffer`. Telemetriedaten, die als Batch verarbeitet werden und darauf warten, gesendet zu werden, werden in `AI_buffer` gespeichert. Die soeben gesendeten Telemetriedaten werden in `AI_sent_buffer` abgelegt, bis der Erfassungsserver antwortet, dass sie erfolgreich empfangen wurden. Wurden die Telemetriedaten erfolgreich empfangen, werden sie aus allen Puffern entfernt. Bei vorübergehenden Fehlern (z. B. wenn ein Benutzer die Netzwerkkonnektivität verliert) verbleiben die Telemetriedaten in `AI_buffer`, bis sie erfolgreich empfangen wurden oder der Erfassungsserver antwortet, dass sie ungültig sind (z. B. ein ungültiges Schema aufweisen oder zu alt sind).

Telemetriepuffer können deaktiviert werden, indem [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) auf `false` festgelegt wird. Wenn der Sitzungsspeicher ausgeschaltet ist, wird stattdessen ein lokales Array als dauerhafter Speicher verwendet. Da das JavaScript SDK auf einem Clientgerät ausgeführt wird, kann der Benutzer über die Entwicklertools des Browsers auf diesen Speicherort zugreifen.

### <a name="opencensus-python"></a>OpenCensus Python

Das OpenCensus Python SDK verwendet standardmäßig den aktuellen Benutzerordner `%username%/.opencensus/.azure/`. Die Berechtigungen für den Zugriff auf diesen Ordner sind auf den aktuellen Benutzer und Administratoren beschränkt. (Näheres dazu finden Sie in der [Implementierung](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py).) Der Ordner mit Ihren beibehaltenen Daten wird nach der Python-Datei benannt, die die Telemetriedaten generiert hat.

Sie können den Speicherort Ihrer Speicherdatei ändern, indem Sie den Parameter `storage_path` an den Konstruktor des Exporters übergeben, den Sie verwenden.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Wie sende ich mit TLS 1.2 Daten an Application Insights?

Um die Sicherheit von Daten bei der Übertragung an die Application Insights-Endpunkte sicherzustellen, wird Kunden dringend empfohlen, ihre Anwendung so zu konfigurieren, dass mindestens Transport Layer Security (TLS) 1.2 verwendet wird. Bei älteren Versionen von TLS/Secure Sockets Layer (SSL) wurde ein Sicherheitsrisiko festgestellt. Sie funktionieren aus Gründen der Abwärtskompatibilität zwar noch, werden jedoch **nicht empfohlen**, und die Industrie ist bestrebt, diese älteren Protokolle schnell auszumustern. 

Das [PCI Security Standards Council](https://www.pcisecuritystandards.org/) hat den [30. Juni 2018 als Termin](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) für die Deaktivierung älterer Versionen von TLS/SSL und das Upgrade auf sicherere Protokolle festgelegt. Wenn Azure keine Legacyunterstützung mehr anbietet und Ihre Anwendung/Clients nicht mindestens über TLS 1.2 kommunizieren können, können Sie keine Daten an Application Insights senden. Welche Methode Sie verwenden, um die TLS-Unterstützung Ihrer Anwendung zu testen und zu überprüfen, hängt vom Betriebssystem bzw. von der Plattform sowie von der Sprache bzw. vom Framework ab, die von Ihrer Anwendung verwendet werden.

Wir empfehlen nicht, Ihre Anwendung explizit so einzurichten, dass nur TLS 1.2 verwendet wird, es sei denn, dies ist erforderlich. Denn dadurch können Sicherheitsfeatures auf Plattformebene deaktiviert werden, mit deren Hilfe neuere, sicherere Protokolle wie TLS 1.3 automatisch erkannt und genutzt werden können, sobald diese verfügbar sind. Es wird empfohlen, gründlich zu überprüfen, ob der Code einer Anwendung Hartcodierungen bestimmter TLS/SSL-Versionen enthält.

### <a name="platformlanguage-specific-guidance"></a>Plattform-/sprachspezifischer Leitfaden

|Plattform/Sprache | Support | Weitere Informationen |
| --- | --- | --- |
| Azure App Services  | Wird unterstützt, möglicherweise ist eine Konfiguration erforderlich. | Unterstützung wurde im April 2018 angekündigt. Informationen zur Ankündigung finden Sie in den [Konfigurationsdetails](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!).  |
| Azure Function-Apps | Wird unterstützt, möglicherweise ist eine Konfiguration erforderlich. | Unterstützung wurde im April 2018 angekündigt. Informationen zur Ankündigung finden Sie in den [Konfigurationsdetails](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!). |
|.NET | Wird unterstützt, Konfiguration hängt von der Version ab. | Ausführliche Informationen zur Konfiguration für .NET 4.7 und frühere Versionen finden Sie in [diesen Anweisungen](/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Statusmonitor | Wird unterstützt, Konfiguration erforderlich | Statusmonitor greift zur Unterstützung von TLS 1.2 auf die [Betriebssystemkonfiguration](/windows-server/security/tls/tls-registry-settings) + [.NET-Konfiguration](/dotnet/framework/network-programming/tls#support-for-tls-12) zurück.
|Node.js |  Wird in Version 10.5.0 unterstützt, möglicherweise ist eine Konfiguration erforderlich. | Informationen zu anwendungsspezifischen Konfigurationen finden Sie in der [offiziellen Dokumentation zu TLS/SSL bei Node.js](https://nodejs.org/api/tls.html). |
|Java | Wird unterstützt, JDK-Unterstützung für TLS 1.2 seit [JDK 6 Update 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) und [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | Bei JDK 8 wird [standardmäßig TLS 1.2 verwendet](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Linux-Distributionen greifen zur Unterstützung von TLS 1.2 tendenziell auf [OpenSSL](https://www.openssl.org) zurück.  | Überprüfen Sie anhand des [OpenSSL-Änderungsprotokolls](https://www.openssl.org/news/changelog.html), ob Ihre Version von OpenSSL unterstützt wird.|
| Windows 8.0 bis 10 | Wird unterstützt und ist standardmäßig aktiviert. | Zur Bestätigung, dass Sie weiterhin die [Standardeinstellungen](/windows-server/security/tls/tls-registry-settings) verwenden.  |
| Windows Server 2012 bis 2016 | Wird unterstützt und ist standardmäßig aktiviert. | Zur Bestätigung, dass Sie weiterhin die [Standardeinstellungen](/windows-server/security/tls/tls-registry-settings) verwenden. |
| Windows 7 SP1 und Windows Server 2008 R2 SP1 | Wird unterstützt, ist jedoch standardmäßig deaktiviert. | Details zur Aktivierung finden Sie auf der Seite [Transport Layer Security (TLS) registry settings (Registrierungseinstellungen für Transport Layer Security (TLS))](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2008 SP2 | Unterstützung für TLS 1.2 muss aktualisiert werden. | Informationen hierzu finden Sie unter [Update zum Hinzufügen der Unterstützung von TLS 1.1 und TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) in Windows Server 2008 SP2. |
|Windows Vista | Nicht unterstützt. | –

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Prüfen, welche Version von OpenSSL von Ihrer Linux-Distribution ausgeführt wird

Zum Prüfen, welche Version von OpenSSL installiert ist, öffnen Sie das Terminal und führen Sie folgenden Befehl aus:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Ausführen einer TLS-1.2-Testtransaktion unter Linux

Zum Ausführen eines vorläufigen Tests, um festzustellen, ob Ihr Linux-System über TLS 1.2 kommunizieren kann, öffnen Sie das Terminal und führen Sie folgenden Befehl aus:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Personenbezogene Daten, die in Application Insights gespeichert sind

In unserem [Artikel über personenbezogene Daten in Application Insights](../logs/personal-data-mgmt.md) wird dieses Thema ausführlich besprochen.

#### <a name="can-my-users-turn-off-application-insights"></a>Können meine Benutzer Application Insights abschalten?
Nicht direkt. Wir bieten keinen Schalter, mit dem Ihre Benutzer Application Insights abschalten können.

Sie können jedoch eine solche Funktion in Ihrer Anwendung implementieren. Alle SDKs enthalten eine API-Einstellung, mit der die Telemetrie-Erfassung deaktiviert wird. 

## <a name="data-sent-by-application-insights"></a>Von Application Insights gesendete Daten
Die SDKs sind je nach Plattform unterschiedlich, und es gibt verschiedene Komponenten, die Sie installieren können. (Weitere Informationen finden Sie unter [Application Insights – Übersicht][start].) Jede Komponente sendet unterschiedliche Daten.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klassen von in verschiedenen Szenarien gesendeten Daten

| Aktion | Gesammelte Datenklassen (siehe nächste Tabelle) |
| --- | --- |
| [Hinzufügen des Application Insights SDK zu einem .NET-Webprojekt][greenbrown] |ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Ausnahmen**<br/>Sitzung<br/>users |
| [Installieren des Statusmonitors auf IIS][redfield] |Abhängigkeiten<br/>ServerContext<br/>Inferred<br/>Perf counters |
| [Hinzufügen des Application Insights SDK zu einer Java-Web-App][java] |ServerContext<br/>Inferred<br/>Anforderung<br/>Sitzung<br/>users |
| [Hinzufügen des JavaScript SDK zur Webseite][client] |ClientContext <br/>Inferred<br/>Seite<br/>ClientPerf<br/>Ajax |
| [Definieren von Standardeigenschaften][apiproperties] |**Properties** für alle standardmäßigen und benutzerdefinierten Ereignisse |
| [Aufrufen von TrackMetric][api] |Numerische Werte<br/>**Eigenschaften** |
| [Aufrufen von Track*][api] |Ereignisname<br/>**Eigenschaften** |
| [Aufrufen von TrackException][api] |**Ausnahmen**<br/>Stapelabbild<br/>**Eigenschaften** |
| SDK kann keine Daten sammeln. Beispiel: <br/> – auf Leistungsindikatoren kann nicht zugegriffen werden<br/> – Ausnahme beim Telemetrieinitialisierer |SDK-Diagnose |

Weitere Informationen zu [SDKs für andere Plattformen][platforms] finden Sie in den entsprechenden Dokumenten.

#### <a name="the-classes-of-collected-data"></a>Die Klassen der gesammelten Daten

| Gesammelte Datenklasse | Umfasst (keine vollständige Liste) |
| --- | --- |
| **Eigenschaften** |**Alle Daten – bestimmt durch Code** |
| DeviceContext |`Id`, IP, Gebietsschema, Gerätemodell, Netzwerk, Netzwerktyp, OEM-Name, Bildschirmauflösung, Rolleninstanz, Rollenname, Gerätetyp |
| ClientContext |Betriebssystem, Gebietsschema, Sprache, Netzwerk, Fensterauflösung |
| Sitzung |`session id` |
| ServerContext |Computername, Gebietsschema, Betriebssystem, Gerät, Benutzersitzung, Benutzerkontext, Vorgang |
| Inferred |Geolocation anhand IP-Adresse, Zeitstempel, Betriebssystem, Browser |
| Metriken |Metrikname und -wert |
| Events |Ereignisname und -wert |
| PageViews |URL und Seitenname oder Bildschirmname |
| Client perf |URL-/Seitenname, Browserladezeit |
| Ajax |HTTP-Aufrufe von der Webseite an den Server |
| Requests |URL, Dauer, Antwortcode |
| Abhängigkeiten |Typ (SQL, HTTP,...), Verbindungszeichenfolge oder URI Sync/Async, Dauer, Erfolg, SQL-Anweisung (mit Statusmonitor) |
| **Ausnahmen** |Typ, **Meldung**, Aufrufliste, Quelldatei, Zeilennummer, `thread id` |
| Crashes |`Process id`, `parent process id`, `crash thread id`; Anwendungspatch, `id`, Build; Typ der Ausnahme, Adresse, Ursache; abgeblendete Symbole und Registerkarten, binäre Start- und Endadressen, binärer Name und Pfad, CPU-Typ |
| Trace |**Meldung** und Schweregrad |
| Perf counters |Prozessorzeit, verfügbarer Speicher, Anforderungsrate, Ausnahmerate, private Bytes verarbeiten, E/A-Rate, Anforderungsdauer, Länge der Anforderungswarteschlange |
| Verfügbarkeit |Webtestantwortcode, Dauer der einzelnen Testschritte, Testname, Zeitstempel, Erfolg, Antwortzeit, Testverzeichnis |
| SDK-Diagnose |Ablaufverfolgungsmeldung oder Ausnahme |

Sie können [einige der Daten durch Bearbeiten von „ApplicationInsights.config“ abschalten][config].

> [!NOTE]
> Die Client-IP-Adresse wird zum Ableiten des geografischen Standorts verwendet. Die IP-Daten werden jedoch standardmäßig nicht mehr gespeichert, und es werden ausschließlich Nullen in das entsprechende Feld geschrieben. Wenn Sie mehr über den Umgang mit personenbezogenen Daten wissen möchten, empfehlen wir diesen [Artikel](../logs/personal-data-mgmt.md#application-data). Wenn Sie IP-Adressdaten speichern müssen, werden Sie im [Artikel zur IP-Adressensammlung](./ip-collection.md) durch die jeweiligen Optionen geführt.

## <a name="can-i-modify-or-update-data-after-it-has-been-collected"></a>Kann ich Daten ändern oder aktualisieren, nachdem Sie gesammelt wurden?

Nein, Daten sind schreibgeschützt und können nur über die Bereinigungsfunktion gelöscht werden. Weitere Informationen finden Sie im [Leitfaden für personenbezogene Daten, die in Log Analytics und Application Insights gespeichert sind](../logs/personal-data-mgmt.md#delete).

## <a name="credits"></a>Guthaben
Dieses Produkt enthält GeoLite2-Daten, die von MaxMind erstellt wurden und unter [https://www.maxmind.com](https://www.maxmind.com) verfügbar sind.



<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiproperties]: ./api-custom-events-metrics.md#properties
[client]: ./javascript.md
[config]: ./configuration-with-applicationinsights-config.md
[greenbrown]: ./asp-net.md
[java]: ./java-in-process-agent.md
[platforms]: ./platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ./status-monitor-v2-overview.md
[start]: ./app-insights-overview.md
