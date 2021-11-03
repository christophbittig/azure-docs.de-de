---
title: gRPC-Erweiterungsprotokoll – Azure
description: Azure Video Analyzer ermöglicht es Ihnen, seine Verarbeitungsfunktionen anhand eines Pipeline-Erweiterungsknotens zu erweitern. Der gRPC-Erweiterungsprozessor ermöglicht Erweiterungsszenarien anhand des hochleistungsfähigen, strukturierten, gRPC-basierten Protokolls.
ms.topic: reference
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: eea68be219ee44fdcc253cc03fc63ebcd7ce93c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094906"
---
# <a name="use-the-grpc-extension-protocol"></a>Verwenden Sie das gRPC-Erweiterungsprotokoll 

[!INCLUDE [header](includes/edge-env.md)]

Azure Video Analyzer ermöglicht es Ihnen, seine Pipeline-Verarbeitungsfunktionen anhand eines [Pipeline-Erweiterungsknotens](../pipeline-extension.md) zu erweitern. Der gRPC-Erweiterungsprozessor ermöglicht Erweiterungsszenarien unter Verwendung eines [hochleistungsfähigen, strukturierten, gRPC-basierten Protokolls](../pipeline-extension.md#grpc-extension-processor).

In diesem Artikel erfahren Sie mehr über die Verwendung des gRPC-Erweiterungsprotokolls zum Senden von Nachrichten zwischen dem Azure Video Analyzer-Modul und Ihrem gRPC-Server, der diese Nachrichten verarbeitet und Ergebnisse zurückgibt. gRPC ist ein modernes, hochleistungsfähiges Open Source-RPC-Framework, das in jeder Umgebung ausgeführt werden kann und plattformübergreifende und sprachübergreifende Kommunikation unterstützt. Der gRPC-Transportdienst verwendet bidirektionales HTTP/2-Streaming zwischen folgenden Komponenten:

* dem gRPC-Client (Azure Video Analyzer-Modul) und dem
* gRPC-Server (Ihre benutzerdefinierte Erweiterung)

Eine gRPC-Sitzung ist eine einzelne Verbindung zwischen gRPC-Client und gRPC-Server über den TCP/TLS-Port.
In einer einzelnen Sitzung geschieht Folgendes: Der Client sendet einen Mediendatenstrom-Deskriptor sowie Videoframes als Nachricht vom Typ [Protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) über die gRPC-Streamsitzung an den Server. Der Server überprüft den Datenstromdeskriptor, analysiert den Videoframe und gibt Rückschlussergebnisse als Protobuf-Nachricht zurück.

Es wird dringend empfohlen, dass die Antworten in Form von gültigen JSON-Dokumenten zurückgegeben werden, die dem vordefinierten Schema des Objektmodells des [Inferenz-Metadatenschemas](inference-metadata-schema.md) entsprechen. Dadurch wird die Interoperabilität mit anderen Komponenten und Szenarien wie Aufnahme und Wiedergabe von Video mit Inferenz-Metadaten besser gewährleistet.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/grpc-extension-protocol/grpc-external-srv.svg" alt-text="Azure Video Analyzer-Modul" lightbox="./media/grpc-extension-protocol/grpc-external-srv.svg":::

## <a name="implementing-grpc-protocol"></a>Implementieren des gRPC-Protokolls

### <a name="creating-a-grpc-connection"></a>Erstellen einer gRPC-Verbindung

Von der benutzerdefinierten Erweiterung muss der folgende gRPC-Dienst implementiert werden:

```
service MediaGraphExtension
    {
        rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
    }
```

Beim Aufruf wird ein bidirektionaler Stream für den Nachrichtenfluss zwischen der gRPC-Erweiterung und der Live-Pipeline vom Azure Video Analyzer geöffnet. Die erste Nachricht, die in diesem Datenstrom von jeder Partei gesendet wird, enthält einen Mediendatenstrom-Deskriptor (MediaStreamDescriptor), um die Informationen zu definieren, die in den folgenden Medienstichproben (MediaSamples) gesendet werden.

Zum Beispiel kann die Erweiterung die Nachricht (hier in JSON ausgedrückt) senden, um anzuzeigen, dass sie 416x416 rgb24-codierte Frames eingebettet in die gRPC-Nachrichten an die benutzerdefinierte Erweiterung senden wird.

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": 
    {
        "graph_identifier": 
        {
            "media_services_arm_id": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{video-analyzer-account-name}",
            "graph_instance_name": "{live-pipeline-name}",
            "graph_node_name": "{grpc-extension-node-name}"
        },
        "media_descriptor": 
        {
            "timescale": 90000,
            "video_frame_sample_format": 
            {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": 
                {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

Die benutzerdefinierte Erweiterung würde daraufhin mit folgender Nachricht antworten, um anzugeben, dass sie nur Rückschlüsse zurückgibt.

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": 
    {
        "extension_identifier": "customExtensionName"    
    }
}
```

Nachdem nun beide Seiten Mediendeskriptoren ausgetauscht haben, beginnt Azure Video Analyzer mit der Übertragung von Frames an den gRPC-Server.

> [!NOTE]
> Der gRPC-Server kann mit der Programmiersprache Ihrer Wahl implementiert werden.

### <a name="sequence-numbers"></a>Sequenznummern

Der gRPC-Erweiterungsknoten und die benutzerdefinierte Erweiterung verfügen jeweils über eigene Sequenznummern, die ihren Nachrichten zugewiesen werden. Diese Sequenznummern sollten mit „1“ beginnen und monoton ansteigen. ack_sequence_number kann ignoriert werden, wenn keine Nachricht quittiert wird, was bei der ersten gesendeten Nachricht vorkommen kann.

Eine Anforderung muss bestätigt werden, nachdem die entsprechende Nachricht vollständig verarbeitet wurde. Bei einer Übertragung mit gemeinsam genutztem Arbeitsspeicher wird dem Absender durch diese Bestätigung signalisiert, dass der gemeinsam genutzte Arbeitsspeicher freigegeben werden kann und dass der Empfänger nicht mehr darauf zugreift. Gemeinsam genutzter Arbeitsspeicher muss vom Absender bis zur Bestätigung vorgehalten werden.

### <a name="reading-embedded-content"></a>Lesen eingebetteter Inhalte

Eingebettete Inhalte können direkt aus der MediaSample-Nachricht über das Feld content_bytes ausgelesen werden. Die Daten werden gemäß dem MediaDescriptor codiert.

### <a name="reading-content-from-shared-memory"></a>Lesen von Inhalten aus gemeinsam genutztem Arbeitsspeicher

Bei der Übertragung von Inhalten über den gemeinsamen Speicher nimmt der Absender beim ersten Erstellen einer Session die SharedMemoryBufferTransferProperties in seinen MediaStreamDescriptor auf. Das sieht dann in etwa wie folgt aus (ausgedrückt in JSON):

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

Der Empfänger öffnet dann die Datei /dev/shm/inference_client_share_memory_2146989006636459346. Der Absender sendet eine MediaSample-Nachricht, die eine ContentReference enthält, die auf eine bestimmte Stelle in dieser Datei verweist.

```
{
    "timestamp": 143598615750000,
    "content_reference": 
    {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

Anschließend liest der Empfänger die Daten, die sich an diesem Ort in der Datei befinden.

Damit das Azure Video Analyzer-Edge-Modul über den gemeinsamen Speicher kommunizieren kann, muss der IPC-Modus des Containers ordnungsgemäß konfiguriert sein. Dafür gibt es mehrere Möglichkeiten. Hier finden Sie einige empfohlene Konfigurationen:

* Bei der Kommunikation mit einer auf dem Hostgerät ausgeführten gRPC-Rückschlussengine sollte der IPC-Modus auf „host“ festgelegt werden.
* Bei der Kommunikation mit einem gRPC-Server, der in einem anderen Azure IoT-Edge-Modul ausgeführt wird, sollte der IPC-Modus auf `shareable` für das Azure Video Analyzer Modul eingestellt sein und auf `container:avaedge` für die benutzerdefinierte Erweiterung, falls `avaedge` der Name des Azure Video Analyzer Moduls ist.

Das folgende Beispiel zeigt, wie dies bei Verwendung der ersten Option im Gerätezwilling aussehen kann:

```
"avaedge": 
{
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": 
  {
    "image": "mcr.microsoft.com/media/video-analyzer:1",
    "createOptions": 
      "HostConfig": 
      {
        "IpcMode": "shareable"
      }
  }
}
```

Weitere Informationen zu IPC-Modi finden Sie unter [IPC settings (--ipc)](https://docs.docker.com/engine/reference/run/#ipc-settings---ipc).

## <a name="video-analyzer-grpc-extension-contract-definitions"></a>Vertragsdefinitionen für die Azure Video Analyzer gRPC-Erweiterung

In diesem Abschnitt wird der gRPC-Vertrag definiert, der den Datenfluss definiert.

### <a name="protocol-messages"></a>Protokollnachrichten

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/grpc-extension-protocol/contract-definitions.png" alt-text="Azure Video Analyzer-Protokollmeldungen"  lightbox="./media/grpc-extension-protocol/contract-definitions.png":::

### <a name="client-authentication"></a>Clientauthentifizierung

Implementierer benutzerdefinierter Erweiterungen können die Authentizität eingehender gRPC-Verbindungen überprüfen, um sicherzustellen, dass sie vom gRPC-Erweiterungsknoten stammen. Vom Knoten wird ein Eintrag in den Anforderungsheadern bereitgestellt, der für die Überprüfung verwendet werden kann.

Hierzu können Anmeldeinformationen (Benutzername/Kennwort) verwendet werden. Beim Erstellen eines gRPC-Erweiterungsknotens werden die Anmeldedaten wie folgt angegeben:

```
{
  "@type": " #Microsoft.VideoAnalyzer.GrpcExtension ",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": " #Microsoft.VideoAnalyzer.UnsecuredEndpoint ",
    "url": "tcp://customExtension:8081",
    "credentials": 
    {
      "@type": "#Microsoft. VideoAnalyzer.UsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

Wenn die gRPC-Anforderung gesendet wird, wird der folgende Header in die Anforderungsmetadaten eingeschlossen, um eine HTTP-Standardauthentifizierung zu imitieren:

```
x-ms-authentication: Basic (Base64 Encoded username:password)
```

## <a name="configuring-inference-server-for-each-live-pipeline-over-grpc-extension"></a>Konfigurieren des Inferenz-Servers für jede Live-Pipeline anhand der gRPC-Erweiterung

Beim Konfigurieren Ihres Rückschlussservers brauchen Sie nicht für jedes KI-Modell, das innerhalb des Rückschlussservers verpackt ist, einen Knoten verfügbar zu machen. Stattdessen können Sie für eine Live-Pipeline die `extensionConfiguration`Eigenschaft des **GrpcExtension-** Knotens verwenden und bestimmen, wie das/die AI-Modell(e) ausgewählt werden soll(en). Während der Ausführung übergibt Azure Video Analyzer diese Zeichenfolge an den Inferenzserver, der sie zum Aufrufen des gewünschten KI-Modells verwenden kann. Bei diesem `extensionConfiguration property` handelt es sich um eine fakultative Eigenschaft und ist spezifisch für Ihre Implementierung des gRPC-Servers. Die Eigenschaft kann wie folgt verwendet werden:

```
{
  "@type": "#Microsoft.VideoAnalyzer.GrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
    "url": "${grpcExtensionAddress}",
    "credentials": 
    {
      "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
      "username": "${grpcExtensionUserName}",
      "password": "${grpcExtensionPassword}"
    }
  },
    // Optional server configuration string. This is server specific 
  "extensionConfiguration": "{Optional extension specific string}",
  "dataTransfer": 
  {
    "mode": "sharedMemory",
    "SharedMemorySizeMiB": "75"
  }
    //Other fields omitted
}
```

## <a name="using-grpc-over-tls"></a>Verwenden von gRPC über TLS

Eine für Rückschlüsse verwendete gRPC-Verbindung kann über TLS geschützt werden. Dies ist in Situationen nützlich, in denen die Sicherheit des Netzwerks zwischen dem Azure Video Analyzer und der Inferenz-Maschine nicht gewährleistet werden kann. Von TLS werden alle Inhalte verschlüsselt, die in die gRPC-Nachrichten eingebettet sind, was eine höhere CPU-Auslastung zur Folge hat, wenn Frames mit einer hohen Rate übertragen werden.

Die Verifizierungs-Optionen `IgnoreHostname` und `IgnoreSignature` werden von gRPC nicht unterstützt, daher muss das Serverzertifikat, das von der Inferenz-Maschine präsentiert wird, einen gemeinsamen Namen (CN) enthalten, der mit der IP-Adresse/dem Hostnamen in der Endpunkt-URL des gRPC-Erweiterungsknotens genau übereinstimmt.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Inferenz-Metadatenschema](inference-metadata-schema.md)
