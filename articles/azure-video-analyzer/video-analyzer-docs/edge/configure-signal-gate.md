---
title: Konfigurieren eines Signaltors für die ereignisbasierte Videoaufzeichnung - Azure
description: In diesem Artikel wird erläutert, wie Sie ein Signaltor in einer Pipeline konfigurieren.
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4fbb530cc4cf0c4b5ea4871c922b7fff6c75ec93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095444"
---
# <a name="configuring-a-signal-gate-for-event-based-video-recording"></a>Konfigurieren eines Signaltors für ereignisbasierte Videoaufzeichnungen

[!INCLUDE [header](includes/edge-env.md)]

Innerhalb einer Pipeline können Sie mit einem [Signaltor-Prozessorknoten](../pipeline.md#signal-gate-processor) Medien von einem Knoten zu einem anderen weiterleiten, wenn das Tor durch ein Ereignis ausgelöst wird. Wenn das Gate ausgelöst wird, öffnet es sich und erlaubt die Übertragung von Mediendaten für einen festgelegten Zeitraum. Wenn keine Ereignisse vorhanden sind, die das Gate auslösen, wird es geschlossen, und der Mediendatenfluss wird beendet. Sie können den Signalgateprozessor für ereignisbasierte Videoaufzeichnungen verwenden.

> [!NOTE]
> Auf den Knoten eines Signalgateprozessors muss unmittelbar eine Videosenke oder Dateisenke folgen.

In diesem Artikel erfahren Sie, wie Sie einen Signalgateprozessor konfigurieren.

## <a name="suggested-prereading"></a>Empfohlene Vorablektüre

- [Pipeline-Topologie](../pipeline.md)
- [Ereignisbasierte Videoaufzeichnung](../event-based-video-recording-concept.md)

## <a name="problem"></a>Problem

Ein Benutzer möchte eine Aufzeichnung zu einem bestimmten Zeitpunkt vor oder nach der Auslösung des Gates durch ein Ereignis starten. Der Benutzer kennt die akzeptable Wartezeit im System. Daher möchte er die Wartezeit des Signalgateprozessors angeben. Er möchte auch die minimale und die maximale Dauer der Aufzeichnung angeben, unabhängig davon, wie viele neue Ereignisse empfangen werden.
 
### <a name="use-case-scenario"></a>Anwendungsfall

Angenommen, Sie möchten jedes Mal ein Video aufzeichnen, wenn die Vordertür Ihres Gebäudes geöffnet wird. Die Aufzeichnung soll folgende Anforderungen erfüllen: 

- Sie soll die *X* Sekunden enthalten, bevor die Tür geöffnet wird. 
- Sie soll mindestens *Y* Sekunden dauern, wenn die Tür nicht erneut geöffnet wird. 
- Sie soll höchstens *Z* Sekunden dauern, wenn die Tür wiederholt geöffnet wird. 
 
Sie wissen, dass Ihr Türsensor eine Wartezeit von *K* Sekunden aufweist. Um die Wahrscheinlichkeit zu verringern, dass Ereignisse als zu spät eingetroffen ignoriert werden, möchten Sie mindestens *K* Sekunden Karenzzeit für das Eintreffen der Ereignisse einräumen.

## <a name="solution"></a>Lösung

Um dieses Problem zu lösen, ändern Sie die Parameter Ihres Signalgateprozessors.

Zum Konfigurieren eines Signalgateprozessors verwenden Sie die folgenden vier Parameter:

- Zeitfenster zur Auswertung der Aktivierung
- Offset für das Aktivierungssignal
- Mindestens gültiges Zeitfenster für die Aktivierung
- Höchstens gültiges Zeitfenster für die Aktivierung

Wenn der Signalgateprozessor ausgelöst wird, bleibt das Gate für den Mindestzeitraum der Aktivierung offen. Das Aktivierungsereignis beginnt beim Zeitstempel des frühesten Ereignisses plus Offset für das Aktivierungssignal. 

Wenn der Signalgateprozessor erneut ausgelöst wird, während das Gate noch offen ist, wird der Timer zurückgesetzt, und das Gate bleibt mindestens für den Mindestzeitraum der Aktivierung offen. Das Signalgate bleibt nie länger offen, als der maximale Zeitraum der Aktivierung dauert. 

Ein Ereignis (Ereignis 1), das gemäß Medienzeitstempel vor einem anderen Ereignis (Ereignis 2) eintritt, wird möglicherweise ignoriert, wenn das System Verzögerungen aufweist und Ereignis 1 nach Ereignis 2 im Signalgateprozessor eintrifft. Wenn Ereignis 1 nicht zwischen dem Eintreffen von Ereignis 2 und dem Zeitfenster zur Auswertung der Aktivierung eintrifft, wird es ignoriert. Es wird nicht durch den Signalgateprozessor geleitet. 

Für jedes Ereignis werden Korrelations-IDs festgelegt. Diese IDs werden ab dem ursprünglichen Ereignis festgelegt. Sie sind sequenziell für jedes folgende Ereignis.

> [!IMPORTANT]
> Die Medienzeit basiert auf dem Medienzeitstempel des Zeitpunkts, zu dem ein Ereignis im Medium auftritt. Die Reihenfolge, in der Ereignisse am Signalgate eintreffen, entspricht möglicherweise nicht der Reihenfolge, in der die Ereignisse zur Medienzeit eintreffen.

### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>Parameter basierend auf dem physischen Zeitpunkt des Eintreffens von Ereignissen am Signalgate

* **minimumActivationTime (kürzeste mögliche Dauer einer Aufzeichnung)** : Die Mindestanzahl von Sekunden, für die ein Signalgate nach dem Auslösen für den Empfang neuer Ereignisse offen bleibt, sofern es nicht durch die MaximumActivationTime unterbrochen wird.
* **maximumActivationTime (längste mögliche Dauer einer Aufzeichnung)** : Die maximale Anzahl von Sekunden ab dem ursprünglichen Ereignis, für die das Signalgate nach dem Auslösen für den Empfang neuer Ereignisse offen bleibt, unabhängig davon, welche Ereignisse empfangen werden.
* **activationSignalOffset**: Die Anzahl von Sekunden zwischen der Aktivierung des Signalgateprozessors und dem Start der Videoaufzeichnung. Dieser Wert ist in der Regel negativ, weil er die Aufzeichnung vor dem auslösenden Ereignis startet.
* **activationEvaluationWindow**: Die Anzahl von Sekunden (in Medienzeit) ab dem ursprünglichen auslösenden Ereignis, in denen ein Ereignis, das vor dem ursprünglichen Ereignis eingetreten ist, beim Signalgateprozessor eintreffen muss, bevor es als zu spät eingetroffen ignoriert wird.

> [!NOTE]
> Als *zu spät eingetroffen* gilt jedes Ereignis, das nach Ablauf des Zeitfensters zur Auswertung der Aktivierung, aber vor dem ursprünglichen Ereignis in Medienzeit eintrifft.

### <a name="limits-of-parameters"></a>Grenzwerte der Parameter

* **activationEvaluationWindow**: 0 bis 10 Sekunden
* **activationSignalOffset**: -1 bis 1 Minute
* **minimumActivationTime**: 10 Sekunden bis 1 Stunde
* **maximumActivationTime**: 10 Sekunden bis 1 Stunde

Im vorliegenden Szenario würden Sie die Parameter folgendermaßen festlegen:

* **activationEvaluationWindow**: *K* Sekunden
* **activationSignalOffset**: *-X* Sekunden
* **minimumActivationWindow**: *Y* Sekunden
* **maximumActivationWindow**: *Z* Sekunden

Hier sehen Sie ein Beispiel dafür, wie der **Signaltor-Prozessor**-Knotenabschnitt in einer Pipeline-Topologie mit den folgenden Parameterwerten aussehen würde:

* **activationEvaluationWindow**: 1 Sekunde
* **activationSignalOffset**: -5 Sekunden
* **minimumActivationTime**: 20 Sekunden
* **maximumActivationTime**: 40 Sekunden

> [!IMPORTANT]
> Für alle Parameterwerte wird das [ISO 8601-Format für Zeitspannen](https://en.wikipedia.org/wiki/ISO_8601#Durations) erwartet. Beispiel: PT1S = 1 Sekunde.

```
"processors":              
[
          {
            "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```

Sehen wir uns jetzt an, wie diese Konfiguration des Signalgateprozessors sich in verschiedenen Aufzeichnungsszenarien verhält.

### <a name="recording-scenarios"></a>Aufzeichnungsszenarien

**Ein Ereignis aus einer Quelle (*normale Aktivierung*)**

Ein Signalgateprozessor, der ein einziges Ereignis empfängt, führt zu einer Aufzeichnung, die 5 Sekunden (Aktivierungssignal = 5 Sekunden) vor dem Eintreffen des Ereignisses am Gate startet. Der Rest der Aufzeichnung dauert 20 Sekunden (Mindestzeitraum der Aktivierung = 20 Sekunden), da vor dem Ende des Mindestzeitraums der Aktivierung keine weiteren Ereignisse eintreffen, die das Gate erneut auslösen.

Beispieldiagramm:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/normal-activation.svg" alt-text="Diagramm der normalen Aktivierung eines einzigen Ereignisses aus einer einzigen Quelle":::

* Dauer der Aufzeichnung = -Offset + minimumActivationTime = [E1 + Offset, E1 + minimumActivationTime]

**Zwei Ereignisse aus einer einzigen Quelle (*erneut ausgelöste Aktivierung*)**

Ein Signalgateprozessor, der zwei Ereignisse empfängt, führt zu einer Aufzeichnung, die 5 Sekunden (Aktivierungssignaloffset = 5 Sekunden) vor dem Eintreffen des Ereignisses am Gate startet. Ereignis 2 trifft 5 Sekunden nach Ereignis 1 ein. Da Ereignis 2 vor dem Ende des Mindestzeitraums der Aktivierung von Ereignis 1 (20 Sekunden) eintrifft, wird das Gate erneut ausgelöst. Der Rest der Aufzeichnung dauert 20 Sekunden (Mindestzeitraum der Aktivierung = 20 Sekunden), da vor dem Ende des Mindestzeitraums der Aktivierung von Ereignis 2 keine weiteren Ereignisse eintreffen, die das Gate erneut auslösen.

Beispieldiagramm:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/retriggering-activation.svg" alt-text="Diagramm der erneut ausgelösten Aktivierung von zwei Ereignissen aus einer einzigen Quelle":::

* Dauer der Aufzeichnung = -Offset + (Eintreffen von Ereignis 2 - Eintreffen von Ereignis 1) + minimumActivationTime

***N* Ereignisse aus einer einzigen Quelle (*maximale Aktivierung*)**

Ein Signalgateprozessor, der *N* Ereignisse empfängt, führt zu einer Aufzeichnung, die 5 Sekunden (Aktivierungssignaloffset = 5 Sekunden) vor dem Eintreffen des ersten Ereignisses am Gate startet. Da jedes Ereignis vor dem Ende des Mindestzeitraums der Aktivierung (20 Sekunden nach dem vorherigen Ereignis) eintrifft, wird das Gate fortlaufend erneut ausgelöst. Es bleibt offen, bis der maximale Zeitraum der Aktivierung (40 Sekunden nach dem ersten Ereignis) abgelaufen ist. Dann wird das Gate geschlossen und akzeptiert keine neuen Ereignisse mehr.

Beispieldiagramm:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate/maximum-activation.svg" alt-text="Diagramm des maximalen Zeitraums der Aktivierung von N Ereignissen aus einer einzigen Quelle":::
 
* Dauer der Aufzeichnung = -Offset + maximumActivationTime

> [!IMPORTANT]
> In den obigen Diagrammen wird davon ausgegangen, dass jedes Ereignis sowohl in physischer als auch in Medienzeit im selben Moment eintrifft. Anders gesagt: Es wird angenommen, dass kein Ereignis zu spät eintrifft.

### <a name="naming-video-or-files"></a>Benennen von Videos oder Dateien

Pipelines ermöglichen die Aufzeichnung von Videos in der Cloud oder als MP4-Dateien auf dem Edge-Gerät. Diese können durch [fortlaufende Videoaufzeichnung](use-continuous-video-recording.md) oder [ereignisbasierte Videoaufzeichnung](record-event-based-live-video.md) generiert werden.

Die empfohlene Namensstruktur für die Aufzeichnung in der Cloud besteht im Benennen der Videoressource als `<anytext>-${System.TopologyName}-${System.PipelineName}`. Eine festgelegte Live-Pipeline kann nur eine Verbindung mit einer RTSP-fähigen IP-Kamera herstellen, und Sie sollten die Eingabe dieser Kamera in einer Videoressource aufzeichnen. Sie können z. B. `VideoName` in den Videosenken wie nachfolgend beschrieben, einstellen:

```
"VideoName": "sampleVideo-${System.TopologyName}-${System.PipelineName}"
```
Beachten Sie, dass das Ersetzungsmuster durch das `$`-Zeichen gefolgt von geschweiften Klammern **${Variablenname}** definiert wird.

Für die Aufzeichnung von MP4-Dateien auf dem Edge-gerät mit ereignisbasierter Aufzeichnung können Sie Folgendes verwenden:

```
"fileNamePattern": "sampleFilesFromEVR-${System.TopologyName}-${System.PipelineName}-${fileSinkOutputName}-${System.Runtime.DateTime}"
```

> [!Note]
> Im Beispiel oben ist die Variable **fileSinkOutputName** der Name einer Beispielvariable, den Sie bei der Erstellung einer Live-Pipeline definieren. Dies ist **keine** Systemvariable. Beachten Sie, dass durch die Verwendung von **DateTime** ein einzigartiger MP4-Dateiname für jedes Ereignis sichergestellt wird.

#### <a name="system-variables"></a>Systemvariablen

Folgende systemseitig definierte Variablen können Sie u. a. verwenden:

| Systemvariable        | Beschreibung                                                  | Beispiel              |
| :--------------------- | :----------------------------------------------------------- | :------------------- |
| System.Runtime.DateTime        | UTC-Datum/-Uhrzeit im dateikonformen ISO8601-Format (Basisdarstellung YYYYMMDDThhmmss). | 20200222T173200Z     |
| System.Runtime.PreciseDateTime | UTC-Datum/-Uhrzeit im dateikonformen ISO8601-Format mit Millisekunden (Basisdarstellung YYYYMMDDThhmmss.sss). | 20200222T173200.123Z |
| System.TopologyName    | Vom Benutzer angegebener Name der ausgeführten Pipeline-Topologie.          | IngestAndRecord      |
| System.PipelineName    | Vom Benutzer angegebener Name der ausgeführten Live-Pipeline.          | camera001            |

> [!Tip]
> System.Runtime.PreciseDateTime und System.Runtime.DateTime können beim Benennen von Videos in der Cloud nicht verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie das [Tutorial zur ereignisbasierten Videoaufzeichnung](record-event-based-live-video.md) durch. Bearbeiten Sie zunächst die Datei [topology.json](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json). Ändern Sie die Parameter für den signalgateProcessor-Knoten, und gehen Sie dann den Rest des Tutorials durch. Sehen Sie sich die Videoaufzeichnungen an, um die Auswirkungen der verschiedenen Parameter zu analysieren.
