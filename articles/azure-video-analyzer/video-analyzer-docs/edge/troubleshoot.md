---
title: Fehlerbehebung bei Azure Video Analyzer - Azure
description: Dieser Artikel beschreibt die Schritte zur Fehlerbehebung für das Azure Video Analyzer Edge-Modul.
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 38451ae2fe8d42e335a887d093bbab9322d13d81
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398153"
---
# <a name="troubleshoot-azure-video-analyzer"></a>Fehlerbehebung beim Azure Video Analyzer

![Edgesymbol](media/env-icon/edge.png)  
Alternativ können Sie die [Problembehandlung im Dienst lesen.](../cloud/troubleshoot.md)

---

In diesem Artikel werden Schritte zur Fehlerbehebung für das Azure Video Analyzer Edge-Modul beschrieben.

## <a name="troubleshoot-deployment-issues"></a>Problembehandlung bei Bereitstellungsproblemen

### <a name="diagnostics"></a>Diagnose

Als Teil Ihrer Video Analyzer-Bereitstellung richten Sie Azure-Ressourcen wie IoT Hub und IoT Edge-Geräte ein. Vergewissern Sie sich im ersten Schritt der Problemdiagnose immer, dass das Edge-Gerät ordnungsgemäß eingerichtet ist, indem Sie diese Anweisungen befolgen:

1. [Ausführen des Befehls `check`](../../../iot-edge/troubleshoot.md#run-the-check-command)
1. [Überprüfen Ihrer IoT Edge-Version](../../../iot-edge/troubleshoot.md#check-your-iot-edge-version)
1. [Überprüfen des Status von IoT Edge Security Manager und der zugehörigen Protokolle](../../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Anzeigen der Nachrichten, die den IoT Edge-Hub durchlaufen](../../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub)
1. [Neustarten von Containern](../../../iot-edge/troubleshoot.md#restart-containers)
1. [Überprüfen der Konfigurationsregeln für Firewall und Port](../../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules)

### <a name="issues-when-using-arm-template"></a>Probleme bei der Verwendung von ARM-Vorlagen

Wenn Sie bei der Verwendung der ARM-Vorlage (Schaltfläche `Deploy to Azure`) auf Probleme stoßen, verwenden Sie die Schritte in diesem [Schnellstart](get-started-detect-motion-emit-events-portal.md), um die erforderlichen Ressourcen manuell bereitzustellen. 

### <a name="pre-deployment-issues"></a>Probleme vor der Bereitstellung

Wenn die Edge-Infrastruktur in Ordnung ist, können Sie nach Problemen in der Bereitstellungsmanifestdatei suchen. Um das Azure Video Analyzer-Modul zusammen mit anderen IoT-Modulen auf dem Azure IoT-Edge-Gerät bereitzustellen, verwenden Sie ein Bereitstellungsmanifest, das den IoT-Edge-Hub, den IoT-Edge-Agenten und andere Module sowie deren Eigenschaften enthält. Sie können den folgenden Befehl zum Bereitstellen der Manifestdatei verwenden:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id avasample-iot-edge-device --content <path-to-deployment_manifest.json>
```

Wenn der JSON-Code nicht ordnungsgemäß geformt ist, erhalten Sie möglicherweise den folgenden Fehler: &nbsp;&nbsp;&nbsp; **Failed to parse JSON from file: '\<deployment manifest.json\>' for argument 'content' with exception: "Extra data: line 101 column 1 (char 5325)"** (Das Parsen von JSON aus der Datei ist fehlgeschlagen: für Argument „content“ mit Ausnahme: „Zusätzliche Daten: Zeile 101 Spalte 1 (Char 5325))“

Wenn dieser Fehler auftritt, empfiehlt es sich, den JSON-Code auf fehlende Klammern oder andere Probleme in der Dateistruktur zu überprüfen. Zum Überprüfen der Dateistruktur können Sie einen Client wie [Notepad++ mit dem JSON Viewer-Plug-In](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) oder ein Onlinetool wie [JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/) verwenden.

### <a name="during-deployment-iot-edge-runtime-response"></a>Während der Bereitstellung: IoT Edge Runtime Antwort

Nachdem das Video Analyzer-Modul korrekt auf dem IoT Edge-Gerät bereitgestellt wurde, können Sie das Azure-Portal verwenden, um eine Diagnose der [IoT Edge Runtime](../../../iot-edge/iot-edge-runtime.md) durchzuführen.

1. Navigieren Sie im Azure-Portal zu dem IoT Hub, der mit Ihrem IoT Edge-Gerät verbunden ist.
1. Suchen Sie nach **Automatische Geräteverwaltung**, und wählen Sie **IoT Edge** aus.
1. Wählen Sie in der Liste der Edge-Geräte das Gerät aus, das Sie diagnostizieren möchten, und öffnen Sie das Management-Blade.

   ![Screenshot des Azure-Portals, in dem eine Liste der Edge-Geräte angezeigt wird](./media/troubleshoot/ava-sample-device.png)

1. Prüfen Sie, ob der Antwortcode der IoT Edge Runtime _200-OK_ lautet. Andere Antwortcodes sind:

   - 400 – Die Bereitstellungskonfiguration ist falsch formatiert oder ungültig.
   - 417 – Für das Gerät ist keine Bereitstellungskonfiguration festgelegt.
   - 412 – Die Schemaversion der Bereitstellungskonfiguration ist ungültig.
   - 406 – das IoT Edge-Gerät ist offline oder sendet keine Statusberichte.
   - 500 – in der IoT Edge-Runtime ist ein Fehler aufgetreten.

   > [!TIP]
   > Wenn in Ihrer Umgebung Probleme bei der Ausführung von Azure IoT Edge-Modulen auftreten, nutzen Sie **[Azure IoT Edge: Standarddiagnoseschritte](../../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** als Leitfaden zur Problembehandlung und Diagnose.

### <a name="post-deployment-reported-properties-and-direct-methods"></a>Nach der Bereitstellung: Gemeldete Eigenschaften und direkte Methoden

Wählen Sie im Azure-Portal das Management Blade für das Video Analyzer-Edge-Modul aus und öffnen Sie es.
Überprüfen Sie zunächst die gemeldeten und die gewünschten Eigenschaften, damit Sie wissen, ob die Moduleigenschaften mit der Bereitstellung synchronisiert wurden. Wenn dies nicht der Fall ist, können Sie Ihr IoT Edge-Gerät neu starten. Wenn die Spalten **Spezifiziert in Bereitstellung** und **Berichtet durch Gerät** _Ja_ anzeigen, können Sie direkte Methoden auf dem Videoanalysator-Modul aufrufen, indem Sie auf die Menüoption **Direkte Methode** klicken.

Sie können die Anleitung [Direkte Methoden](direct-methods.md) verwenden, um einige Methoden aufzurufen, insbesondere einfache wie `pipelineTopologyList`. Der Leitfaden gibt darüber hinaus die erwartete Nutzlast für Anforderungen und Antworten sowie Fehlercodes an. Wenn die einfachen direkten Methoden erfolgreich sind, können Sie sicher sein, dass das Video Analyzer edge Modul funktionsfähig ist.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/troubleshoot/direct-method.png" alt-text="Screenshot der Direktmethoden-Anzeige für das Azure IoT-Edge-Modul." lightbox="./media/troubleshoot/direct-method.png":::

Berücksichtigen Sie dabei Folgendes:
1. Wenn Sie einen Status `501 code` empfangen, überprüfen Sie, ob der Name der direkten Methode korrekt ist. Wenn der Name der Methode und die Nutzlast der Anfrage korrekt sind, sollten Sie Ergebnisse mit einem Erfolgscode von 200 erhalten.
1. Wenn die Angabe der Anforderungsnutzlast ungenau ist, erhalten Sie den Status „`400 code`“ und eine Antwortnutzlast, die den Fehlercode und eine Meldung angibt, die Sie bei der Diagnose des Problems bei Ihrem direkten Methodenaufrufe unterstützen soll.

### <a name="post-deployment-diagnostic-logs-for-issues"></a>Nach der Bereitstellung: Diagnoseprotokolle für Probleme

Die Containerprotokolle für Ihr IoT-Edge-Modul sollten Diagnoseinformationen enthalten, die bei der Behebung von Problemen helfen, die bei der Analyse von Live-Videos auftreten. Möglicherweise können Sie [ die Containerprotokolle auf Probleme prüfen](../../../iot-edge/troubleshoot.md#check-container-logs-for-issues) und das Problem selbst diagnostizieren.

Wenn Sie sämtliche bereits genannten Überprüfungen ausgeführt haben und weiterhin Probleme auftreten, sammeln Sie [mit dem Befehl `support bundle`](../../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) Protokolle aus dem IoT Edge-Gerät, damit das Azure-Team weitere Analysen durchführen kann. Sie können sich zwecks Support [an uns wenden](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) und die gesammelten Protokolle übermitteln.

## <a name="common-error-resolutions"></a>Lösungen für allgemeine Fehler

Das Video Analyzer-Edge-Modul arbeitet mit den IoT-Edge-Agent- und Hub-Modulen zusammen. Einige der häufigsten Fehler, die bei der Bereitstellung auftreten, werden durch Probleme mit der zugrunde liegenden IoT-Infrastruktur verursacht. Zu diesen Fehlern zählen die folgenden:

- [Der IoT Edge-Agent wird nach ungefähr einer Minute beendet](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
- [Der IoT Edge-Agent kann nicht auf das Image eines Moduls zugreifen (403)](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
- [Das IoT Edge-Agent-Modul meldet „empty config file“, und auf dem Gerät werden keine Module gestartet](../../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
- [Der IoT Edge-Hub kann nicht gestartet werden](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
- [Fehler beim Daemon für die IoT Edge-Sicherheit aufgrund eines ungültigen Hostnamens](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
- [Das Senden einer Nachricht vom Azure Video Analyzer oder jegliches andere benutzerdefiniertes Azure IoT-Edge-Modul schlägt fehl mit dem Fehler 404](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
- [Das IoT Edge-Modul wird erfolgreich bereitgestellt und verschwindet dann vom Gerät](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

  > [!TIP]
  > Wenn in Ihrer Umgebung Probleme bei der Ausführung von Azure IoT Edge-Modulen auftreten, nutzen Sie **[Azure IoT Edge: Standarddiagnoseschritte](../../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** als Leitfaden zur Problembehandlung und Diagnose.

Wenn es weitere Probleme gibt, bei denen Sie Hilfe benötigen, **[erfassen Sie Protokolle, und übermitteln Sie ein Supportticket](#collect-logs-for-submitting-a-support-ticket)** . Sie können uns auch erreichen, indem Sie eine E-Mail an **[videoanalyzerhelp@microsoft.com](mailto:videoanalyzerhelp@microsoft.com)** senden.

### <a name="working-with-external-modules"></a>Arbeiten mit externen Modulen

Mit Pipeline-Erweiterungsprozessoren können Sie die Pipeline erweitern, um Daten von anderen IoT Edge-Modulen über HTTP- oder gRPC-Protokolle zu senden und zu empfangen. Als [spezifisches Beispiel](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/httpExtension) kann diese Live-Pipeline Videobilder als Bilder an ein externes Inferenzmodul wie Yolo v3 senden und JSON-basierte Analyseergebnisse über das HTTP-Protokoll empfangen. In einer solchen Topologie bildet in den meisten Fällen der IoT Hub das Ziel für die Ereignisse. In Situationen, in denen Sie keine Rückschlussereignisse auf dem Hub sehen können, prüfen Sie die folgenden Punkte:

- Überprüfen Sie, ob es sich bei dem Hub, in dem die Live-Pipeline veröffentlicht, und dem Hub, den Sie untersuchen, um denselben Hub handelt. Wenn Sie mehrere Bereitstellungen erstellen, haben Sie es auch mit mehreren Hubs zu tun und suchen möglicherweise auf dem falschen nach Ereignissen.
- Überprüfen Sie im Azure-Portal, ob das externe Modul bereitgestellt ist und ausgeführt wird. Im Beispielbild hier handelt es sich bei rtspsim, yolov3, tinyyolov3 und logAnalyticsAgent um IoT Edge-Module, die extern zum avaedge-Modul ausgeführt werden.

  [![Screenshot: Ausführungsstatus von Modulen in Azure IoT Hub](./media/troubleshoot/iot-hub-azure.png)](./media/troubleshoot/iot-hub-azure.png#lightbox)

- Überprüfen Sie, ob Sie Ereignisse an den richtigen URL-Endpunkt senden. Der externe KI-Container macht eine URL und einen Port verfügbar, über die bzw. den er die Daten von POST-Anforderungen empfängt und zurückgibt. Diese URL wird als `endpoint: url`-Eigenschaft des HTTP-Erweiterungsprozessors angegeben. Wie Sie am Beispiel der [Topologie-URL](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json) sehen, ist der Endpunkt auf den Parameter für die Rückschluss-URL festgelegt. Vergewissern Sie sich, dass der Standardwert für den Parameter bzw. der übergebene Wert korrekt ist. Sie können die Funktionalität mithilfe der Client-URL (cURL) testen.

  Im Folgenden finden Sie als Beispiel einen Yolo v3-Container, der auf einem lokalen Computer mit der IP-Adresse 172.17.0.3 ausgeführt wird.

  ```shell
  curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
  ```

  Zurückgegebenes Ergebnis:

  ```json
  {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
  ```

  > [!TIP]
  > Verwenden Sie den **[Docker-Befehl „inspect“](https://docs.docker.com/engine/reference/commandline/inspect/)** , um die IP-Adresse des Computers zu ermitteln.

- Wenn Sie eine oder mehrere Live-Pipelines ausführen, die den Pipeline-Erweiterungsprozessor verwenden, sollten Sie das `samplingOptions`-Feld verwenden, um die Bildrate pro Sekunde (fps) des Videofeeds zu steuern.

  - In bestimmten Situationen mit hoher CPU-Auslastung/Arbeitsspeicherbelegung des Edge-Computers können bestimmte Rückschlussereignisse verloren gehen. Um dies zu vermeiden, legen Sie einen niedrigen Wert für die `maximumSamplesPerSecond`-Eigenschaft im Feld `samplingOptions` fest. Sie können es in jeder Instanz der Pipeline auf 0,5 („maximumSamplesPerSecond“: „0.5“) einstellen und dann die Instanz erneut ausführen, um auf Inferenz-Events im Hub zu prüfen.

### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Mehrere parallel ausgeführte direkte Methoden: Zeitlimitüberschreitung

Azure Video Analyzer bietet ein direktes methodenbasiertes Programmiermodell, mit dem Sie mehrere Topologien und mehrere Pipelines einrichten können. Als Teil der Topologie- und Pipeline-Einrichtung rufen Sie mehrere direkte Methodenaufrufe auf dem IoT Edge-Modul auf. Wenn Sie diese mehrere Methodenaufrufe parallel aufrufen, insbesondere die, welche die Pipelines starten und stoppen, kann ein Timeout-Fehler wie der folgende auftreten:

Die Assembly-Initialisierungsmethode „Microsoft.Media.VideoAnalyzer.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync“ hat eine Ausnahme ausgelöst. Microsoft.Azure.Devices.Common.Exceptions.IotHubException: <br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution.`

Es wird empfohlen, direkte Methoden _nicht_ parallel aufzurufen. Rufen Sie Methoden stattdessen sequenziell auf, indem Sie einen Methodenaufruf erst dann starten, wenn der vorherige beendet ist.

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Sammeln von Protokollen zum Übermitteln eines Supporttickets

Wenn die Probleme mit der Self-Service-Problembehandlung nicht gelöst werden können, navigieren Sie zum Azure-Portal, und [öffnen Sie ein Supportticket](../../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Es kann sein, dass diese Protokolldateien personenbezogene Informationen (Personally Identifiable Information, PII) enthalten, beispielsweise Ihre IP-Adresse. Alle lokalen Kopien der Protokolle werden gelöscht, sobald wir die Untersuchung abgeschlossen haben und das Supportticket schließen.

Zum Sammeln der relevanten Protokolle, die dem Ticket hinzugefügt werden sollen, befolgen Sie die folgenden Anweisungen der Reihe nach, und laden Sie die Protokolldateien im Bereich **Details** der Supportanfrage hoch.

1. [Konfigurieren Sie das Live Azure Video Analyzer-Moduls zum Sammeln ausführlicher Protokolle](#configure-video-analyzer-module-to-collect-verbose-logs)
1. [Aktivieren Sie Debugprotokolle](#video-analyzer-edge-module-debug-logs).
1. Reproduzieren Sie das Problem.
1. Starten Sie das Video Analyzer-Edgemodul neu. 
    > [!NOTE]
    > Dieser Schritt ist erforderlich, um das Edgemodul ordnungsgemäß zu beenden und alle Protokolldateien in einem verwendbaren Format abzurufen, ohne Ereignisse zu löschen.   
    
    Verwenden Sie auf dem IoT Edge-Gerät den folgenden Befehl, nachdem Sie `<avaedge>` durch den Namen Ihres Video Analyzer-Edge-Moduls ersetzt haben:
    
    ```cmd
    sudo iotedge restart <avaedge>
    ```

   Sie können Module auch remote über das Azure-Portal neu starten. Weitere Informationen finden Sie unter [Überwachen und Beheben von Problemen bei IoT Edge-Geräten über das Azure-Portal](../../../iot-edge/troubleshoot-in-portal.md).
1. Stellen Sie im Portal auf der Seite **IoT Hub** eine Verbindung mit dem virtuellen Computer her.

   1. Zippen Sie alle Dateien im Ordner _debugLogs_. Diese Protokolldateien sind nicht für die Selbstdiagnose gedacht. Sie dienen dem Azure-Technikteam zum Analysieren Ihrer Probleme.

      - Stellen Sie sicher, dass Sie im folgenden Befehl **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** durch den Speicherort der Debugprotokolle auf dem Edge-Gerät ersetzen, das Sie zuvor in **Schritt 2** eingerichtet haben.

        ```
        sudo apt install zip unzip
        zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE
        ```

   1. Fügen Sie die Datei _debugLogs.zip_ an das Supportticket an.

1. Führen Sie den [Support Bundle-Befehl](#use-the-support-bundle-command) aus, sammeln Sie die Protokolle, und fügen Sie sie an das Supportticket an.

### <a name="configure-video-analyzer-module-to-collect-verbose-logs"></a>Konfigurieren Sie das Live Azure Video Analyzer-Modul zum Sammeln ausführlicher Protokolle

Konfigurieren Sie Ihr Live Azure Video Analyzer-Modul für das Sammeln ausführlicher Protokolle, indem Sie `logLevel` und `logCategories` wie folgt einstellen:

```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Sie können dies auf verschiedene Weise tun:

- Im **Microsoft Azure-Portal**, indem Sie die Modul-Identitäts-Zwillings-Eigenschaften des Videoanalysator-Moduls [![Module Identity Twin Properies" aktualisieren.](media/troubleshoot/module-twin.png)](media/troubleshoot/module-twin.png#lightbox)
- Oder Sie fügen in Ihrer **Bereitstellungs-Manifest**-Datei diese Einträge in den Eigenschaftsknoten des Azure Video Analyzer-Moduls ein

### <a name="use-the-support-bundle-command"></a>Verwenden des Befehls „support-bundle“

Wenn Sie Protokolle von einem IoT Edge-Gerät sammeln müssen, verwenden Sie dazu am einfachsten den Befehl `support-bundle`. Dieser Befehl sammelt Folgendes:

- Modulprotokolle
- IoT Edge Security Manager- und Container-Engine-Protokolle
- IoT Edge-Prüfung auf JSON-Ausgaben
- Nützliche Debuginformationen

1. Führen Sie den Befehl `support-bundle` mit dem Flag _--since_ aus, um anzugeben, welchen Zeitraum die Protokolle abdecken sollen. Beispielsweise werden mit „2h“ die Protokolle der letzten zwei Stunden abgerufen. Sie können den Wert dieses Flags ändern, um Protokolle für andere Zeiträume einzuschließen.

   ```shell
   sudo iotedge support-bundle --since 2h
   ```

   Dieser Befehl erstellt eine Datei namens _support_bundle.zip_ in dem Verzeichnis, in dem Sie den Befehl ausführen.

1. Fügen Sie die Datei _support_bundle.zip_ an das Supportticket an.

### <a name="video-analyzer-edge-module-debug-logs"></a>Debug-Protokolle des Edge-Moduls von Video Analyzer

Gehen Sie wie folgt vor, um das Video Analyzer-Edge-Modul so zu konfigurieren, dass es Debug-Protokolle erzeugt:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer IoT Hub-Instanz.
1. Wählen Sie im linken Bereich die Option **IoT Edge** aus.
1. Wählen Sie in der Liste der Geräte die ID des Zielgeräts aus.
1. Wählen Sie oben im Bereich die Option **Module festlegen** aus.

   ![Screenshot der Schaltfläche „Module festlegen“ im Azure-Portal](media/troubleshoot/set-modules.png)

1. Suchen Sie im Abschnitt **IoT Edge Modules** nach **avaedge** und wählen Sie Letzteres aus.
1. Wählen Sie **Zwilling der Modulkennung** aus. Ein bearbeitbarer Bereich wird geöffnet.
1. Fügen Sie unter **desired key** das folgende Schlüssel-Wert-Paar hinzu:

   `"DebugLogsDirectory": "/var/lib/videoanalyzer/logs"`

   > [!NOTE]
   > Dieser Befehl bindet die Protokollordner zwischen dem Edge-Gerät und dem Container. Wenn Sie die Protokolle an einem anderen Speicherort auf dem Gerät sammeln möchten:
   >
   > 1. Erstellen Sie eine Bindung für den Speicherort des Debugprotokolls im Abschnitt **Binds**, wobei **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** und **$DEBUG_LOG_LOCATION** durch den von Ihnen gewünschten Speicherort ersetzt werden: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
   > 2. Verwenden Sie den folgenden Befehl, wobei **$DEBUG_LOG_LOCATION** durch den im vorherigen Schritt verwendeten Speicherort ersetzt wird: `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`

1. Wählen Sie **Speichern** aus.

1. Sie können die Protokollsammlung beenden, indem Sie den Wert unter **Zwilling der Modulkennung** auf _NULL_ festlegen. Wechseln Sie zurück zur Seite **Zwilling der Modulkennung**, und aktualisieren Sie den folgenden Parameter wie folgt:

   `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Bewährte Methoden für die Protokollierung

[Überwachung und Protokollierung](monitor-log-edge.md) sollten dabei helfen, die Taxonomie zu verstehen und Protokolle zu erstellen, die bei der Fehlersuche mit Azure Video Analyzer unterstützen.

Da sich die gRPC-Serverimplementierung von Sprache zu Sprache unterscheidet, gibt es kein Standardverfahren zum Hinzufügen von Protokollierung im Server.

Wenn Sie beispielsweise einen gRPC-Server mithilfe von .NET Core erstellen, fügt der gRPC-Dienst Protokolle unter der Kategorie **Grpc** hinzu. Zum Aktivieren detaillierter Protokolle von gRPC konfigurieren Sie die Grpc-Präfixe in Ihrer appsettings.json-Datei auf die Stufe „Debug“, indem Sie dem Unterabschnitt „LogLevel“ in „Logging“ die folgenden Elemente hinzufügen:

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information",
      "Grpc": "Debug"
       }
  }
}
```

Sie können dies auch mithilfe von „ConfigureLogging“ in der Startup.cs-Datei konfigurieren:

```
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {

           logging.AddFilter("Grpc", LogLevel.Debug);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });

```

[Protokollierung und Diagnose in gRPC auf .NET](/aspnet/core/grpc/diagnostics?view=aspnetcore-5.0&preserve-view=true) bietet einige Anleitungen zum Sammeln von Diagnoseprotokollen von einem gRPC-Server.

### <a name="a-failed-grpc-connection"></a>Fehlerhafte gRPC-Verbindung

Wenn eine Pipeline aktiv ist und von einer Kamera streamt, wird die Verbindung vom Azure Video Analyzer aufrechterhalten.

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Überwachung und Lastenausgleich von CPU- und GPU-Ressourcen, wenn diese Ressourcen zu Engpässen werden

Das Video Analyzer-Edge-Modul überwacht keine Hardware-Ressourcen und bietet auch keine solche Überwachung. Entwickler müssen die Lösungen des Hardwareherstellers verwenden. Wenn Sie jedoch Kubernetes-Container verwenden, können Sie das Gerät mithilfe des [Kubernetes-Dashboards](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) überwachen.

Die Dokumentation zu gRPC in .NET Core enthält außerdem einige wertvolle Informationen zu [Bewährten Methoden für die Leistung](/aspnet/core/grpc/performance?view=aspnetcore-5.0&preserve-view=true) und zum [Lastenausgleich](/aspnet/core/grpc/performance?view=aspnetcore-5.0&preserve-view=true#load-balancing).

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Problembehandlung eines Rückschlussservers, wenn dieser keine Frames empfängt und Sie einen „unbekannten“ Protokollfehler erhalten

Sie haben verschiedene Möglichkeiten, weitere Informationen zu diesem Problem zu erhalten.

- Fügen Sie die Protokollkategorie **mediaPipeline** in die gewünschten Eigenschaften des Video Analyzer-Moduls ein, und stellen Sie sicher, dass der Protokolliergrad auf `Information` eingestellt ist.
- Zum Testen der Netzwerkkonnektivität können Sie auf dem Edgegerät den folgenden Befehl ausführen.

  ```
  sudo docker exec avaedge /bin/bash -c "apt update; apt install -y telnet; telnet <inference-host> <inference-port>"
  ```

  Wenn der Befehl eine kurze Zeichenfolge ungeordneter Zeichen zurückgibt, konnte Telnet erfolgreich eine Verbindung zu Ihrem Rückschlussserver herstellen und einen binären gRPC-Kanal öffnen. Wenn Sie dies nicht sehen, meldet Telnet einen Netzwerkfehler.

- Auf Ihrem Rückschlussserver können Sie zusätzliche Protokollierung in der gRPC-Bibliothek aktivieren. Diese können Ihnen weitere Informationen über den gRPC-Kanal selbst geben. Die dazu erforderlichen Aktionen unterscheiden sich je nach Sprache; hier sind die Anweisungen für [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1).

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Auswählen weiterer Bilder aus dem gRPC-Puffer, ohne das Ergebnis für den ersten Puffer zurückzusenden

Im Rahmen des gRPC-Datenübertragungsvertrags sollten alle Nachrichten bestätigt werden, die von Azure Video Analyzer an den gRPC-Rückschlussserver gesendet werden. Den Empfang eines Einzelbilds nicht zu bestätigen, stellt eine Verletzung des Datenvertrags dar und kann zu unerwünschten Situationen führen.

Um Ihren gRPC-Server mit dem Video Analyzer-Edge-Modul zu verwenden, kann für eine optimale Leistung ein gemeinsamer Speicher verwendet werden. Dazu müssen Sie die Shared Memory-Funktionen von Linux verwenden, die von der Programmiersprache/Umgebung verfügbar gemacht werden.

1. Öffnen Sie das Shared Memory-Handle von Linux.
1. Greifen Sie beim Empfang eines Frames auf den Adressoffset innerhalb des gemeinsam genutzten Speicherbereichs zu.
1. Bestätigen Sie die Beendigung der Bildverarbeitung, damit der Speicher vom Video Analyzer Edge Modul wieder freigegeben werden kann.

   > [!NOTE]
   > Wenn Sie den Empfang des Frames für Azure Video Analyzer lange Zeit verzögert, kann dies dazu führen, dass sich der gemeinsame Speicher füllt und Datenverluste verursacht.

1. Speichern Sie jedes Einzelbild auf dem Rückschlussserver in einer Datenstruktur Ihrer Wahl (Liste, Array usw.).
1. Anschließend können Sie Ihre Verarbeitungslogik ausführen, wenn Sie die gewünschte Anzahl Einzelbilder empfangen haben.
1. Rückgabe des Ergebnisses der Inferenz an das Video Analyzer-Edge-Modul, wenn es fertig ist.

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Ereignisbasierte Videoaufzeichnung in der Cloud und Wiedergabe aus der Cloud](record-event-based-live-video.md)
