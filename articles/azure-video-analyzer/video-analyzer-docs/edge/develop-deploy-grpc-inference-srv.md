---
title: 'Entwickeln und Bereitstellen eines gRPC-Rückschlussservers: Azure Video Analyzer'
description: Dieser Artikel enthält Anleitungen zum Entwickeln und Bereitstellen eines gRPC-Rückschlussservers, der mit Azure Video Analyzer verwendet werden soll.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 344f4772bc2c48612c6f8ee62cac9d22a35f321b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284530"
---
# <a name="develop-and-deploy-grpc-inference-server"></a>Entwickeln und Bereitstellen eines gRPC-Rückschlussservers

[!INCLUDE [header](includes/edge-env.md)]

In diesem Artikel erfahren Sie, wie Sie beliebige KI-Modelle in einen gRPC-Rückschlussserver einbinden können, damit dieser über die Pipelineerweiterung in Azure Video Analyzer integriert werden kann.

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung

* Pipelineerweiterungen
* gRPC-Erweiterungsprotokoll
* Schema für Rückschlussmetadaten
* [Einführung in gRPC](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [proto3-Sprachführer](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Voraussetzungen

* Ein x86-64- oder ARM64-Gerät, auf dem eines der [unterstützten Linux-Betriebssysteme ausgeführt wird](../../../iot-edge/support.md#operating-systems), oder ein Windows-Gerät
* [Docker-Installation](https://docs.docker.com/desktop/#download-and-install) auf Ihrem Gerät
* Installation der [IoT Edge-Runtime](../../../iot-edge/how-to-install-iot-edge.md?tabs=linux)

## <a name="grpc-implementation-steps"></a>Schritte zur gRPC-Implementierung

Zum Erstellen eines gRPC-Rückschlussservers und zum Implementieren dieses Servers als Erweiterung mit Azure Video Analyzer werden die folgenden Schritte ausgeführt:

### <a name="setup-video-analyzer-module"></a>Einrichten des Video Analyzer-Moduls

Führen Sie die erforderlichen Schritte aus, um das Video Analyzer-Modul bereitzustellen und auf einem IoT Edge-Gerät auszuführen.

### <a name="high-level-implementation-steps"></a>Grundsätzliche Implementierungsschritte

1. Wählen Sie eine der zahlreichen Sprachen aus, die von gRPC unterstützt werden: C#, C++, Dart, Go, Java, Node, Objective-C, PHP, Python, Ruby.
1. Implementieren Sie einen gRPC-Server, der über die [proto3-Dateien](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) mit Azure Video Analyzer kommuniziert.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/inference-srv-container-process.svg" alt-text="gRPC-Server, der über die proto3-Dateien mit Azure Video Analyzer kommuniziert":::

    In diesem Dienst:
    1. Behandeln Sie den Austausch von Sitzungsbeschreibungsnachrichten zwischen dem Server und dem Client.
    1. Behandeln Sie [Beispielnachrichten](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto), und geben Sie Ergebnisse zurück.

        1. Rufen Sie die Rückschlussengine auf, die ein trainiertes Modell verwendet, um Rückschlüsse auf die eingehenden Nachrichten zu ziehen.
        1. Mithilfe der Datei [inferencing.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/inferencing.proto) können Sie Rückschlussergebnisse von der Engine empfangen, sie als Medienbeispiel packen und zurück an Video Analyzer senden.

            Alternativ dazu können Sie auch eine beliebige Medientransformationsfunktion für das Medienbeispiel aufrufen.
    1. Stellen Sie die gRPC-Serverimplementierung bereit. Hierfür gibt es zwei Methoden:

        1. Bereitstellen als IoT-Modul zusammen mit dem Video Analyzer-Modul
        1. Bereitstellen als IoT-Modul auf einem über das Netzwerk erreichbaren Knoten (lokal oder in der Cloud), der Daten mit dem Video Analyzer-Modul austauschen kann.
    1. Konfigurieren Sie eine Video-Analyse-Pipeline-Topologie mit dem Video-Analyse-Modul, und verweisen Sie es auf den gRPC-Server.

### <a name="recommendation"></a>Empfehlung

Beim Zusammenstellen auf demselben Knoten kann `shared memory` verwendet werden, um eine optimale Leistung zu erzielen. Dazu müssen Sie die Shared Memory-Funktionen von Linux verwenden, die von der Programmiersprache/Umgebung verfügbar gemacht werden.

1. Öffnen Sie das Shared Memory-Handle von Linux.
1. Greifen Sie beim Empfang eines Frames auf den Adressoffset innerhalb des gemeinsam genutzten Speicherbereichs zu.
1. Bestätigen Sie den Abschluss der Bildverarbeitung, damit der Speicher von Azure Video Analyzer wieder freigegeben werden kann.

> [!NOTE]
> Bei Verwendung eines gRPC-Erweiterungsmoduls für das Rückschließen mit gemeinsamem Speicher sollten sowohl das Video Analyzer-Edge-Modul als auch das Erweiterungsmodul unter demselben [Benutzer und derselben Gruppe](https://docs.docker.com/engine/reference/builder/#user) laufen

## <a name="create-a-grpc-inference-server"></a>Erstellen eines gRPC-Rückschlussservers

Sie erstellen nun ein IoT Edge-Modul (externe KI), das Videoframes von Video Analyzer akzeptiert. Dazu verwenden Sie Nachrichten vom Typ [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) über den gemeinsam genutzten Speicherbereich, klassifizieren die Frames als **dunkel** oder **hell**, und geben mithilfe des Schemas für Rückschlussmetadaten Rückschlussergebnisse an die IoT Hub-Nachrichtensenke in Video Analyzer zurück.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv/external-ai.png" alt-text="Erstellen eines IoT Edge-Moduls (externe KI)":::

Bei diesem gRPC-Rückschlussserver handelt es sich um eine .NET Core-Konsolenanwendung, die zum Verarbeiten der [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc)-Nachrichten erstellt wird, die zwischen Video Analyzer und der benutzerdefinierten KI gesendet werden. Nachfolgend wird der Nachrichtenfluss zwischen Video Analyzer und dem gRPC-Rückschlussserver erläutert:

1. Video Analyzer sendet einen Mediendatenstrom-Deskriptor (siehe [extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto)). Dieser definiert die Mediendatenstrom-Informationen, die – gefolgt von den Videoframes – als [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc)-Nachricht über die gRPC-Streamsitzung an den Server gesendet werden.
1. Der Server überprüft und bestätigt den Datenstromdeskriptor und richtet die gewünschte Datenübertragungsmethode ein.
1. Video Analyzer beginnt dann mit dem Senden der MediaSample-Dateien, die die Videoframes enthalten.
1. Der Server analysiert die Videoframes bei ihrem Eingang und beginnt unter Verwendung eines von Ihnen definierten Bildprozessors mit der Verarbeitung.
1. Der Server gibt dann Rückschlussergebnisse als [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc)-Nachrichten zurück, sobald sie verfügbar sind.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/grpc-external-srv.png" alt-text="Erstellen eines gRPC-Rückschlussservers":::

Die Videoframes können entweder über den [gemeinsam genutzten Speicherbereich](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) übertragen oder in die protobuf-Nachricht eingebettet werden. Der Datenübertragungsmodus kann in der Azure Video Analyzer-Pipelinetopologie konfiguriert werden, um zu bestimmen, wie Frames übertragen werden. Dazu wird das **dataTransfer**-Element der `GrpcExtension`-Eigenschaft konfiguriert, wie nachfolgend dargestellt:

Eingebettet:

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Gemeinsam genutzter Speicherbereich:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> Bei der Kommunikation über den gemeinsam genutzten Speicherbereich sollte der Wert von „IpcMode“ auf **shareable** festgelegt werden. Legen Sie im gRPC-Servermodul den Wert von „IpcMode“ auf **container:{CONTAINER_NAME}** fest. Diese Einstellungen müssen in der Bereitstellungsmanifestdatei vorgenommen werden, die zum Bereitstellen der Module in Azure IoT Hub verwendet wird. Nachfolgend sehen Sie ein Beispiel für die Containeroptionen, die beim Einrichten der IoT Edge-Module verwendet werden sollten.

Azure Video Analyzer-Modul:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

gRPC-Erweiterungsmodul:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:avaedge"
    }
}
```

> [!NOTE]
> Stellen Sie sicher, dass Sie in „grpcExtension“ auf den Bereich des gemeinsam genutzten Speichers von **container:avaedge** zugreifen können.

## <a name="sample-grpc-server"></a>gRPC-Beispielserver

Gehen Sie das Codebeispiel durch, um die Entwicklung eines gRPC-Servers nachzuvollziehen.

1. Klonen Sie das Repository über den GitHub-Link [https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp).
1. Starten Sie VSCode, und navigieren Sie zum Ordner „/src/edge/modules/grpcExtension“.
1. Nachfolgend werden die Dateien kurz näher erläutert:

    1. **Program.cs**: Dies ist der Einstiegspunkt der Anwendung. Diese Datei ist zuständig für das Initialisieren und Verwalten des gRPC-Servers, der als Host fungiert. In diesem Beispiel wird der Port zum Lauschen auf eingehende gRPC-Nachrichten von einem gRPC-Client (z. B. Azure Video Analyzer) durch das grpcBindings-Konfigurationselement in der Datei „appconfig.json“ angegeben.

        ```json
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\PipelineExtensionService.cs**: Diese Klasse ist für die Verarbeitung der [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc)-Nachrichten zuständig. Sie liest den Frame in der Nachricht, ruft ImageProcessor auf und schreibt die Rückschlussergebnisse.
      Nachdem Sie die Portverbindungen des gRPC-Servers konfiguriert und initialisiert haben, sehen Sie sich nun an, wie Sie die eingehenden gRPC-Nachrichten verarbeiten können.

        1. Nach dem Einrichten einer gRPC-Sitzung ist die erste Nachricht, die der gRPC-Server vom Client (Azure Video Analyzer) empfängt, ein MediaStreamDescriptor-Element, das in der Datei [extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto) definiert ist.

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        1. Bei dieser Serverimplementierung überprüft die Methode `ProcessMediaStreamDescriptor` die MediaStreamDescriptor-Eigenschaft „MediaDescriptor“ für eine Videodatei und richtet anschließend abhängig von der Angabe in der Topologie und der verwendeten Bereitstellungsvorlagendatei den Datenübertragungsmodus (Verwendung des gemeinsam genutzten Speicherbereichs oder des eingebetteten Frameübertragungsmodus) ein.
        1. Nach dem Eingang der Nachricht und der erfolgreichen Einrichtung des Datenübertragungsmodus gibt der gRPC-Server die MediaStreamDescriptor-Nachricht als Bestätigung an den Client zurück und stellt somit eine Verbindung zwischen dem Server und dem Client her.
        1. Nachdem die Bestätigung von Video Analyzer empfangen wurde, beginnt die Übertragung des Mediendatenstroms an den gRPC-Server. In dieser Serverimplementierung verarbeitet die Methode `ProcessMediaStream` das eingehende MediaStreamMessage-Element. MediaStreamMessage wird ebenfalls in [extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto) definiert.

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.


              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        1. Abhängig vom Wert von `batchSize` in der Datei *appconfig.json* empfängt der Server weitere Nachrichten und speichert die Videoframes in einer Liste. Sobald der Grenzwert für „batchSize“ erreicht ist, ruft die Funktion die Funktion oder Datei auf, die das Bild verarbeitet. Im aktuellen Fall ruft die Methode eine Datei mit dem Namen **BatchImageProcessor.cs** auf.
    1. **Processors\BatchImageProcessor.cs**: Diese Klasse ist für die Verarbeitung der Bilder verantwortlich. In diesem Beispiel wurde ein Bildklassifizierungsmodell verwendet. Für jedes Bild, das verarbeitet werden soll, wird der folgende Algorithmus verwendet:

        1. Konvertieren Sie das Bild in einem Bytearray zur Verarbeitung. Siehe die Methode `GetBytes(Bitmap image)`

            Der von Ihnen verwendete Beispielprozessor unterstützt nur JPG-codierte Bildframes und „None“ als Pixelformat. Für den Fall, dass der benutzerdefinierte Prozessor eine andere Codierung und/oder ein anderes Format unterstützt, aktualisieren Sie die Methode `IsMediaFormatSupported` der Prozessorklasse.
        1. Konvertieren Sie das Bild mithilfe der [ColorMatrix-Klasse](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1) in eine Graustufe. Siehe die Methode `ToGrayScale(Image source)`
        1. Wenn das Graustufenbild erstellt wurde, berechnen Sie den Durchschnitt der Graustufenbytes.
        1. Wenn der Durchschnittswert unter 127 liegt, wird das Bild als „dunkel“ klassifiziert, andernfalls wird es mit einem Konfidenzwert von 1,0 als „hell“ eingestuft. Siehe die Methode `ProcessImage(List<Image> images)`

    Sie können Ihre eigene Prozessorlogik hinzufügen, indem Sie entweder diese Klasse ändern oder eine neue Klasse hinzufügen und die Methode implementieren:

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images)
    ```

    Nachdem Sie die neue Klasse hinzugefügt haben, müssen Sie **PipelineExtensionService.cs** aktualisieren, damit Ihre Klasse instanziiert und die ProcessImage-Methode dafür aufgerufen wird, um die Verarbeitungslogik auszuführen.

## <a name="connect-with-video-analyzer-module"></a>Herstellen einer Verbindung mit dem Video Analyzer-Modul

Nachdem das gRPC-Erweiterungsmodul erstellt wurde, erstellen Sie nun die Pipelinetopologie und stellen diese bereit.

1. Führen Sie in Visual Studio Code [diese Anweisungen](../../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) aus, um sich bei Docker anzumelden.
1. Navigieren Sie in Visual Studio Code zu *src/edge*. Ihre *ENV*-Datei und einige Bereitstellungsvorlagendateien werden angezeigt.

    Die **Bereitstellungsvorlage** verweist auf das Bereitstellungsmanifest für das Edge-Gerät. Es enthält einige Platzhalterwerte. Die ENV-Datei enthält die Werte für diese Variablen.

    Wählen Sie dann **Erstellen und Übertragen der IoT Edge-Projektmappe per Push** aus. Verwenden Sie für diesen Schritt *src/edge/deployment.grpc.template.json*.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/build-push-iot-edge-solution.png" alt-text="Herstellen einer Verbindung mit dem Video Analyzer-Modul":::

    Durch diese Aktion wird das gRPC-Servermodul erstellt und das Image per Push an Azure Container Registry übertragen.
    Überprüfen Sie, ob die Umgebungsvariablen `CONTAINER_REGISTRY_USERNAME_myacr` und `CONTAINER_REGISTRY_PASSWORD_myacr` in der *ENV*-Datei definiert sein.
1. Navigieren Sie zum Ordner *src/cloud-to-device-console-app*. Darin finden Sie Ihre Datei *appsettings.json* und einige andere Dateien:

    * **c2d-console-app.csproj**: Die Projektdatei für Visual Studio Code.
    * **operations.json**: Eine Liste mit den Vorgängen, die vom Programm ausgeführt werden sollen.
    * **Program.cs**: Das Beispiel für den Programmcode. Mit diesem Code wird Folgendes durchgeführt:

        * Laden der App-Einstellungen
        * Aufrufen direkter Methoden, die vom Azure Video Analyzer-Modul verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen direkten Methoden aufrufen.
        * Anhalten der Ausführung, sodass Sie die Ausgabe des Programms im Terminalfenster und die vom Modul generierten Ereignisse im Ausgabefenster untersuchen können
        * Aufrufen direkter Methoden zur Bereinigung der Ressourcen
1. Bearbeiten Sie die Datei operations.json:

    * Ändern Sie den Link zur Pipelinetopologie:

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json`
        * Bearbeiten Sie unter `livePipelineSet` den Namen der Pipelinetopologie, damit er mit dem Wert im vorherigen Link übereinstimmt:<br/>`"topologyName": "EVRtoVideoSinkByGrpcExtension"`
        * Ändern Sie unter `pipelineTopologyDelete` den Namen:<br/>`"name": "EVRtoVideoSinkByGrpcExtension"`

    Die Topologie muss eine Erweiterungsadresse definieren. Sie können beispielsweise einen Parameter erstellen und im GrpcExtension-Knoten verwenden:
    * Erweiterungsadressparameter

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC AVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Konfiguration

        ```
        {
            "@apiVersion": "1.1",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.VideoAnalyzer.GrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "75"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.VideoAnalyzer.ImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests

Im Bereitstellungsmanifest werden die Module, die auf einem Edge-Gerät bereitgestellt werden, sowie die Konfigurationseinstellungen für diese Module definiert. Führen Sie die folgenden Schritte aus, um ein Manifest auf der Grundlage der Vorlagendatei zu generieren, und stellen Sie es dann auf dem Edge-Gerät bereit.
1. Mit diesem Schritt wird das IoT Edge-Bereitstellungsmanifest unter *src/edge/config/deployment.grpc.amd64.json* erstellt. Klicken Sie mit der rechten Maustaste auf diese Datei, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/create-deployment-single-device.png" alt-text="Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests":::

1. Als Nächstes bittet Visual Studio Code Sie, ein IoT Hub-Gerät auszuwählen. Wählen Sie den IoT Hub aus; dies sollte `avasample-iot-edge-device` sein.
In dieser Phase hat die Bereitstellung von Edge-Modulen auf Ihrem IoT Edge-Gerät begonnen. Aktualisieren Sie nach ungefähr 30 Sekunden Azure IoT Hub im unteren linken Bereich in Visual Studio Code. Nun sollte ein neues bereitgestelltes Modul mit dem Namen „avaextension“ angezeigt werden.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv/devices.png" alt-text="Neues Modul mit dem Namen „avaextension“ bereitgestellt":::

## <a name="next-steps"></a>Nächste Schritte

* Führen Sie die Schritte zum **Vorbereiten der Überwachung von Ereignissen** unter Schnellstart: Analysieren von Livevideos mithilfe eines eigenen HTTP-Modells aus, um das Beispiel auszuführen und die Ereignisse zu interpretieren.
* Sehen Sie sich auch unsere gRPC-Beispieltopologien an: [gRPCExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json) und [EVROnMotionPlusGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/motion-with-grpcExtension/topology.json).
