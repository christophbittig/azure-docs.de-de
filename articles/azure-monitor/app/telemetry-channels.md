---
title: Telemetriekanäle in Azure Application Insights | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Telemetriekanäle in Azure Application Insights SDKs für .NET und .NET Core anpassen.
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: 3723896da2cd14762fd3fe81fa50caaa4b74b0b8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250709"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetriekanäle in Application Insights

Telemetriekanäle sind ein integraler Bestandteil der [Azure Application Insights SDKs](./app-insights-overview.md). Hiermit wird die Pufferung und Übertragung von Telemetriedaten an den Application Insights-Dienst verwaltet. Die .NET- und .NET Core-Versionen der SDKs verfügen über zwei integrierte Telemetriekanäle: `InMemoryChannel` und `ServerTelemetryChannel`. In diesem Artikel werden beide Kanäle ausführlich beschrieben. Außerdem sind Angaben dazu enthalten, wie das Kanalverhalten angepasst werden kann.

## <a name="what-are-telemetry-channels"></a>Was sind Telemetriekanäle?

Telemetriekanäle sind für das Puffern und Senden von Telemetrieelementen an den Application Insights-Dienst verantwortlich, wo sie für Abfragen und Analysen gespeichert werden. Ein Telemetriekanal ist jede Klasse, die die Schnittstelle [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel) implementiert.

Die `Send(ITelemetry item)`-Methode eines Telemetriekanals wird aufgerufen, nachdem alle Telemetrieinitialisierer und Telemetrieprozessoren aufgerufen wurden. Daher erreichen Elemente, die von einem Telemetrieprozessor verworfen wurden, den Kanal nicht. Mit `Send()` werden die Elemente in der Regel nicht sofort an das Back-End gesendet. Normalerweise werden Sie im Arbeitsspeicher gepufferten und für eine effiziente Übertragung in Batches gesendet.

[Live Metrics Stream](live-stream.md) verfügt auch über einen benutzerdefinierten Kanal, der für das Livestreaming von Telemetriedaten verwendet wird. Dieser Kanal ist vom regulären Telemetriekanal unabhängig, und dieses Dokument trifft nicht auf ihn zu.

## <a name="built-in-telemetry-channels"></a>Integrierte Telemetriekanäle

Im Lieferumfang der Application Insights SDKs für .NET und .NET Core sind zwei integrierte Kanäle enthalten:

* `InMemoryChannel`: Ein einfacher Kanal, der Elemente im Arbeitsspeicher puffert, bis sie gesendet werden. Elemente werden im Arbeitsspeicher gepuffert, und dieser wird einmal alle 30 Sekunden geleert oder sobald 500 Elemente gepuffert sind. Dieser Kanal bietet minimale Zuverlässigkeitsgarantien, da das Senden von Telemetriedaten nach einem Fehler nicht wiederholt wird. Dieser Kanal bewahrt auch keine Elemente auf einem Datenträger, sodass nicht gesendete Elemente beim Herunterfahren der Anwendung (ordnungsgemäß oder nicht) dauerhaft verloren gehen. Dieser Kanal implementiert eine `Flush()`-Methode, mit der das synchrone Leeren aller Telemetrieelemente aus dem Arbeitsspeicher erzwungen werden kann. Dieser Kanal eignet sich gut für Anwendungen mit kurzer Ausführungszeit, bei denen eine synchrone Leerung ideal ist.

    Dieser Kanal ist Bestandteil des größeren NuGet-Pakets „Microsoft.ApplicationInsights“ und ist der Standardkanal, der vom SDK verwendet wird, wenn keine andere Konfiguration vorhanden ist.

* `ServerTelemetryChannel`: Ein komplexerer Kanal, der über Wiederholungsrichtlinien verfügt und Daten auf einem lokalen Datenträger speichern kann. Dieser Kanal wiederholt das Senden von Telemetriedaten, wenn vorübergehende Fehler auftreten. Dieser Kanal verwendet auch lokalen Datenträgerspeicher, um Elemente während Netzwerkausfällen oder bei großen Telemetriedatenmengen auf dem Datenträger aufzubewahren. Aufgrund dieser Wiederholungsmechanismen und des lokalen Datenträgerspeichers gilt dieser Kanal als zuverlässiger und wird für alle Produktionsszenarien empfohlen. Dieser Kanal ist die Standardeinstellung für [ASP.NET](./asp-net.md)- und [ASP.NET Core](./asp-net-core.md)-Anwendungen, die gemäß der offiziellen Dokumentation konfiguriert sind. Dieser Kanal ist für Serverszenarien mit lang andauernden Prozessen optimiert. Die von diesem Kanal implementierte [`Flush()`](#which-channel-should-i-use)-Methode ist nicht synchron.

    Dieser Kanal wird als NuGet-Paket „Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel“ geliefert und ist bei Verwendung des NuGet-Pakets „Microsoft.ApplicationInsights.Web“ oder „Microsoft.ApplicationInsights.AspNetCore“ automatisch enthalten.

## <a name="configure-a-telemetry-channel"></a>Konfigurieren eines Telemetriekanals

Sie konfigurieren einen Telemetriekanal, indem Sie ihn auf die aktive Telemetriekonfiguration festlegen. Bei ASP.NET-Anwendungen schließt die Konfiguration das Festlegen der Telemetriekanalinstanz auf `TelemetryConfiguration.Active` oder das Ändern von `ApplicationInsights.config` ein. Bei ASP.NET Core-Anwendungen schließt die Konfiguration das Hinzufügen des Kanals zum Container für die Abhängigkeitsinjektion ein.

In den folgenden Abschnitten werden Beispiele für die Konfiguration der `StorageFolder`-Einstellung des Kanals bei verschiedenen Anwendungstypen gezeigt. `StorageFolder` ist nur eine der konfigurierbaren Einstellungen. Die vollständige Liste der Konfigurationseinstellungen finden Sie im [Abschnitt mit den Einstellungen](#configurable-settings-in-channels) weiter unten in diesem Artikel.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfiguration mit „ApplicationInsights.config“ für ASP.NET-Anwendungen

Der folgende Abschnitt von [ApplicationInsights.config](configuration-with-applicationinsights-config.md) zeigt die Konfiguration des Kanals `ServerTelemetryChannel`, bei der `StorageFolder` auf einen benutzerdefinierten Speicherort festgelegt ist:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguration im Code für ASP.NET-Anwendungen

Der folgende Code richtet eine Instanz von „ServerTelemetryChannel“ so ein, dass `StorageFolder` auf einen benutzerdefinierten Speicherort festgelegt ist. Fügen Sie diesen Code am Anfang der Anwendung hinzu, typischerweise in der `Application_Start()`-Methode in „Global.aspx.cs“.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguration im Code für ASP.NET Core-Anwendungen

Ändern Sie die `ConfigureServices`-Methode der `Startup.cs`-Klasse wie hier gezeigt:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Die Konfiguration des Kanals mithilfe von `TelemetryConfiguration.Active` wird für ASP.NET Core-Anwendungen nicht unterstützt.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfiguration im Code für .NET/.NET Core Konsolenanwendungen

Bei Konsolen-Apps ist der Code für .NET und .NET Core identisch:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Funktionsdetails zu ServerTelemetryChannel

`ServerTelemetryChannel` speichert eingehende Elemente in einem Puffer im Arbeitsspeicher. Die Elemente werden serialisiert, komprimiert und einmal alle 30 Sekunden, oder sobald n 500 Elemente gepuffert wurden, in einer `Transmission`-Instanz gespeichert. Eine einzelne `Transmission`-Instanz enthält bis zu 500 Elemente und stellt einen Batch von Telemetriedaten dar, die über einen einzigen HTTPS-Aufruf an den Application Insights-Dienst gesendet werden.

Standardmäßig können maximal 10 `Transmission`-Instanzen parallel gesendet werden. Wenn Telemetriedaten schneller eintreffen oder das Netzwerk bzw. das Application Insights-Back-End langsam ist, werden `Transmission`-Instanzen im Arbeitsspeicher gespeichert. Die Standardkapazität dieses `Transmission`-Puffers im Arbeitsspeicher beträgt 5 MB. Wenn die Kapazität des Arbeitsspeichers überschritten ist, werden `Transmission`-Instanzen auf einem lokalen Datenträger bis zu einem Maximum von 50 MB gespeichert. `Transmission`-Instanzen werden auch bei Auftreten von Netzwerkproblemen auf dem lokalen Datenträger gespeichert. Nur die Elemente, die auf einem lokalen Datenträger gespeichert sind, überstehen einen Absturz der Anwendung. Sie werden gesendet, sobald die Anwendung wieder gestartet wird. Wenn weiterhin Netzwerkprobleme bestehen, verwendet `ServerTelemetryChannel` eine exponentielle Backofflogik im Bereich von 10 Sekunden bis zu einer Stunde, bevor erneut versucht wird, Telemetriedaten zu senden. 

## <a name="configurable-settings-in-channels"></a>Konfigurierbare Einstellungen für die Kanäle

Die vollständige Liste der konfigurierbaren Einstellungen für jeden Kanal finden Sie in den folgenden Artikeln:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Nachfolgend sind die am häufigsten verwendeten Einstellungen für `ServerTelemetryChannel` aufgeführt:

1. `MaxTransmissionBufferCapacity`: Die maximale Menge an Arbeitsspeicher in Byte, die vom Kanal zum Puffern von Übertragungen im Arbeitsspeicher verwendet wird. Wenn diese Kapazitätsgrenze erreicht ist, werden neue Elemente direkt auf dem lokalen Datenträger gespeichert. Der Standardwert ist 5 MB. Das Festlegen eines höheren Werts führt zu einer geringeren Datenträgerauslastung, aber denken Sie daran, dass Elemente im Arbeitsspeicher bei einem Absturz der Anwendung verloren gehen.

1. `MaxTransmissionSenderCapacity`: Die maximale Anzahl von `Transmission`-Instanzen, die gleichzeitig an Application Insights gesendet werden. Der Standardwert ist 10. Diese Einstellung kann mit einem höheren Wert konfiguriert werden. Dies wird empfohlen, wenn große Mengen von Telemetriedaten generiert werden. Große Mengen treten in der Regel bei Auslastungstests auf oder wenn die Stichprobenerstellung deaktiviert ist.

1. `StorageFolder`: Der Ordner, der vom Kanal verwendet wird, um Elemente bei Bedarf auf dem Datenträger zu speichern. Unter Windows wird entweder %LOCALAPPDATA% oder %TEMP% verwendet, wenn kein anderer Pfad explizit angegeben wird. In anderen Umgebungen als Windows müssen Sie einen gültigen Speicherort angeben, da sonst keine Telemetriedaten auf dem lokalen Datenträger gespeichert werden.

## <a name="which-channel-should-i-use"></a>Welchen Kanal sollte ich verwenden?

`ServerTelemetryChannel` wird für die meisten Produktionsszenarien mit Anwendungen mit langer Ausführungsdauer empfohlen. Die `Flush()`-Methode, die von `ServerTelemetryChannel` implementierte wird, ist nicht synchron und garantiert auch nicht, dass alle ausstehenden Elemente aus dem Arbeitsspeicher oder vom Datenträger gesendet werden. Wenn Sie diesen Kanal in Szenarien verwenden, in denen die Anwendung heruntergefahren werden soll, wird empfohlen, nach dem Aufruf von `Flush()` eine Verzögerung einzuleiten. Die genaue Dauer der möglicherweise erforderlichen Verzögerung ist nicht vorhersagbar. Sie hängt von Faktoren ab, wie z.B. der Menge an Elementen oder `Transmission`-Instanzen im Arbeitsspeicher, der Menge auf dem Datenträger, der Menge in Übertragung an das Back-End, und ob sich der Kanal in der Mitte von exponentiellen Backoff-Szenarien befindet.

Wenn Sie eine synchrone Leerung durchführen müssen, wird die Verwendung von `InMemoryChannel` empfohlen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Garantiert der Application Insights-Kanal die Übermittlung von Telemetriedaten? Wenn nicht, in welchen Szenarien können Telemetriedaten verloren gehen?

Die kurze Antwort ist, dass keiner der integrierten Kanäle als Transaktionstyp die Übermittlung von Telemetriedaten an das Back-End garantiert. `ServerTelemetryChannel` bietet im Vergleich zu `InMemoryChannel` eine zuverlässigere Übermittlung, führt aber auch nur den bestmöglichen Versuch durch, Telemetriedaten zu senden. Telemetriedaten können in verschiedenen Situationen verloren gehen. Dazu gehören diese häufigen Szenarien:

1. Elemente im Arbeitsspeicher gehen verloren, wenn die Anwendung abstürzt.

1. Telemetriedaten gehen bei länger andauernden Netzwerkproblemen verloren. Telemetriedaten werden bei Netzwerkausfällen oder bei Auftreten Problemen mit dem Application Insights-Back-End auf dem lokalen Datenträger gespeichert. Allerdings werden Elemente verworfen, die älter als 48 Stunden sind.

1. Die Standardspeicherorte auf dem Datenträger zum Speichern von Telemetriedaten unter Windows sind %LOCALAPPDATA% oder %TEMP%. Diese Speicherorte befinden sich normalerweise lokal auf dem Computer. Wenn die Anwendung physisch von einem Speicherort an einen anderen migriert, gehen alle Telemetriedaten verloren, die am ursprünglichen Speicherort gespeichert sind.

1. In Azure-Web-Apps unter Windows ist der Standardspeicherort auf dem Datenträger „D:\local\LocalAppData“. Dieser Speicherort ist nicht permanent. Er wird bei App-Neustarts, horizontalem Hochskalieren und ähnlichen Vorgängen gelöscht, was zu einem Verlust der dort gespeicherte Telemetriedaten führt. Sie können die Standardeinstellung überschreiben und eine Speicherung an einem permanenten Speicherort wie „D:\home“ angeben. Solche permanenten Speicherorte basieren jedoch auf Remotespeicher und können daher langsam sein.

Es ist zwar weniger wahrscheinlich, aber dennoch möglich, dass der Kanal doppelte Telemetrieelemente verursacht. Dies ist dann der Fall, wenn `ServerTelemetryChannel` aufgrund eines Netzwerkfehlers/-timeouts einen erneuten Versuch unternimmt, obwohl die Telemetriedaten an das Back-End übermittelt wurden, die Antwort aber aufgrund von Netzwerkproblemen verloren ging oder ein Timeout auftrat.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Kann ServerTelemetryChannel auf anderen Systemen als Windows verwendet werden?

Obwohl der Name des Pakets und des Namespace „WindowsServer“ beinhaltet, wird dieser Kanal auf anderen Systemen als Windows mit folgender Ausnahme unterstützt. Auf anderen Systemen als Windows erstellt der Kanal standardmäßig keinen lokalen Speicherordner. Sie müssen einen lokalen Speicherordner erstellen und den Kanal für die Verwendung dieses Ordners konfigurieren. Nachdem die lokale Speicherung konfiguriert wurde, funktioniert der Kanal auf allen Systemen gleich.

> [!NOTE]
> Ab Version 2.15.0-beta3 wird nun automatisch lokaler Speicher für Linux, Mac und Windows erstellt. Für Nicht-Windows-Systeme erstellt das SDK automatisch einen lokalen Speicherordner auf der Grundlage der folgenden Logik:
> - `${TMPDIR}`: Wenn die Umgebungsvariable `${TMPDIR}` festgelegt ist, wird dieser Speicherort verwendet.
> - `/var/tmp`: Wenn der vorherige Speicherort nicht vorhanden ist, versuchen wir `/var/tmp`.
> - `/tmp`: Wenn die beiden vorherigen Speicherorte nicht vorhanden sind, versuchen wir `tmp`. 
> - Wenn keiner dieser Speicherorte vorhanden ist, wird kein lokaler Speicher erstellt, wodurch eine manuelle Konfiguration weiterhin erforderlich ist. [Vollständige Details zur Implementierung.](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860)

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Erstellt das SDK einen temporären lokalen Speicher? Werden die Daten beim Speichern verschlüsselt?

Das SDK speichert Telemetrieelemente bei Netzwerkproblemen oder Drosselung im lokalen Speicher. Diese Daten werden nicht lokal verschlüsselt.

Bei Windows-Systemen erstellt das SDK automatisch einen temporären lokalen Ordner im Verzeichnis %TEMP% oder %LOCALAPPDATA% und schränkt den Zugriff auf Administratoren und den aktuellen Benutzer ein.

Bei anderen Systemen als Windows wird vom SDK kein lokaler Speicher automatisch erstellt, sodass standardmäßig keine Daten lokal gespeichert werden.

> [!NOTE]
> Ab Version 2.15.0-beta3 wird nun automatisch lokaler Speicher für Linux, Mac und Windows erstellt. 

 Sie können selbst ein Speicherverzeichnis erstellen und den Kanal für dessen Verwendung konfigurieren. In diesem Fall müssen Sie sicherstellen, dass das Verzeichnis gesichert ist.
Lesen Sie weitere Informationen zum [Datenschutz](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Open Source SDK
Wie jedes SDK für Application Insights sind Kanäle Open Source. Lesen Sie den Code, tragen Sie zum Code bei, oder melden Sie Probleme im [offiziellen GitHub-Repository](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Nächste Schritte

* [Stichproben](./sampling.md)
* [SDK-Problembehandlung](./asp-net-troubleshoot-no-data.md)

