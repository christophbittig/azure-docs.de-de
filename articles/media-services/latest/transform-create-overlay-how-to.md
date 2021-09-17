---
title: 'Gewusst wie: Erstellen einer Bildüberlagerung'
description: Erfahren Sie, wie Sie eine Bildüberlagerung erstellen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 532e26e486e758b3fd8079f6f61f3e00e82dbdca
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662302"
---
# <a name="how-to-create-an-image-overlay"></a>Gewusst wie: Erstellen einer Bildüberlagerung

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Mit Media Services können Sie ein Video mit einem Bild, einer Audiodatei oder einem anderen Video überlagern. Als Eingabe muss genau eine Bilddatei angegeben werden. Sie können eine Bilddatei im JPG-, PNG-, GIF- oder BMP-Format oder eine Audiodatei (z. B. eine WAV-, MP3-, WMA- oder M4A-Datei) oder eine Videodatei in einem unterstützten Format angeben.


## <a name="prerequisites"></a>Voraussetzungen

* Sammeln Sie die Kontoinformationen, die Sie zum Konfigurieren der Datei *appSettings.json* im Beispiel benötigen. Wenn Sie sich nicht sicher sind, wie dies zu tun ist, finden Sie Informationen hierzu im [Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md). Die folgenden Werte werden in der Datei *appsettings.json* erwartet.

```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
```

Wenn Sie sich mit dem Erstellen von Transformationen noch nicht auskennen, sollten Sie die folgenden Aktivitäten ausführen:

* Lesen Sie [Codieren von Video- und Audiodaten mit Media Services](encode-concept.md).
* Lesen Sie [Codieren mit einer benutzerdefinierten Transformation – .NET](transform-custom-presets-how-to.md). Führen Sie die Schritte in diesem Artikel aus, um .NET einzurichten, das für die Arbeit mit Transformationen erforderlich ist, und kehren Sie dann hierher zurück, um ein vordefiniertes Beispiel für Überlagerungen auszuprobieren.
* Weitere Informationen finden Sie im [Referenzdokument zu Transformationen](/rest/api/media/transforms).

Sobald Sie sich mit Transformationen vertraut gemacht haben, laden Sie das Beispiel für Überlagerungen herunter.

## <a name="overlays-preset-sample"></a>Vordefiniertes Beispiel für Überlagerungen

Klonen Sie das .NET-Beispielrepository von Media Services.

```bash
    git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

Navigieren Sie zum Projektmappenordner, und starten Sie Visual Studio Code oder Visual Studio 2019.

Eine Reihe von Codierungsbeispielen ist im Ordner VideoEncoding verfügbar. Öffnen Sie das Projekt im Projektmappenordner [VideoEncoding/Encoding_OverlayImage](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_OverlayImage), um die Verwendung von Überlagerungen zu lernen.

Das Beispielprojekt enthält zwei Mediendateien. Eine Videodatei und ein Logobild, mit dem das Video überlagert werden soll.
* ignite.mp4
* cloud.png

In diesem Beispiel erstellen wir zunächst eine benutzerdefinierte Transformation, die das Video in der CreateCustomTransform-Methode mit dem Bild überlagern kann.  Mit der Eigenschaft *[Filter](/rest/api/media/transforms/create-or-update#filters)* von *[StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)* weisen wir eine neue Filtersammlung zu, die die Einstellungen zur Videoüberlagerung enthält.

Ein [VideoOverlay](/rest/api/media/transforms/create-or-update#videooverlay) enthält die Eigenschaft *InputLabel*, die erforderlich ist, um Zuordnungen aus der Liste der an den Auftrag übermittelten Auftragseingabedateien vorzunehmen und die richtige Eingabequelldatei zu suchen, die als Überlagerungsbild oder -video verwendet werden soll.  Beim Übermitteln des Auftrags wird der gleiche Bezeichnungsname verwendet, um der Einstellung hier in der Transformation zu entsprechen. Im Beispiel verwenden wir den Bezeichnungsnamen „logo“ wie in der Zeichenfolgenkonstante *OverlayLabel*.

Der folgende Codeausschnitt zeigt, wie die Transformation formatiert wird, um eine Überlagerung zu verwenden.

```csharp
new TransformOutput
                {
                    Preset = new StandardEncoderPreset
                    {
                        Filters = new Filters
                        {
                            Overlays = new List<Overlay>
                            {
                                new VideoOverlay
                                {
                                    InputLabel = OverlayLabel,   // same as the one used in the JobInput to identify which asset is the overlay image
                                    Position = new Rectangle( "1200","670") // left, top position of the overlay in absolute pixel position relative to the source videos resolution. 
    
                                }
                            }
                        },
                        Codecs = new List<Codec>
                        {
                            new AacAudio
                            {
                            },
                            new H264Video
                            {
                                KeyFrameInterval = TimeSpan.FromSeconds(2),
                                Layers = new List<H264Layer>
                                {
                                    new H264Layer
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 1000000, // 1Mbps
                                        Width = "1280",
                                        Height = "720"
                                    },
                                    new H264Layer   // Adding a second layer to see that the image also is scaled and positioned the same way on this layer. 
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 600000, // 600 kbps
                                        Width = "480",
                                        Height = "270"
                                    }
                                }
                            }
                        },
                        Formats = new List<Format>
                        {
                            new Mp4Format
                            {
                                FilenamePattern = "{Basename}_{Bitrate}{Extension}",
                            }
                        }
                    }
                }
```

Wenn Sie den Auftrag an die Transformation übermitteln, müssen Sie zunächst die beiden Eingaberessourcen erstellen.

* Ressource 1: In diesem Beispiel wird als erste Ressource die lokale Videodatei „ignite.mp4“ erstellt. Dieses Video verwenden wir als Hintergrund der Komposition und überlagern es mit einem Logobild. 
* Ressource 2: In diesem Beispiel enthält die zweite (in der Variablen overlayImageAsset gespeicherte) Ressource die PNG-Datei, die für das Logo verwendet werden soll. Dieses Bild wird während der Codierung über dem Video positioniert.

Wenn der Auftrag in der *SubmitJobAsync*-Methode erstellt wird, erstellen wir zunächst ein JobInput-Array mithilfe eines List<>-Objekts.  Die Liste enthält die Verweise auf die beiden Quellressourcen.

Um zu identifizieren und abzugleichen, welche Eingaberessource im in der obigen Transformation definierten Filter als Überlagerung verwendet werden soll, verwenden wir erneut den Bezeichnungsnamen „logo“ für den Abgleich. Der Bezeichnungsname wird JobInputAsset für das PNG-Bild hinzugefügt. Dadurch wird der Transformation mitgeteilt, welche Ressource beim Überlagerungsvorgang verwendet werden soll. Sie können dieselbe Transformation mit unterschiedlichen in Media Services gespeicherten Ressourcen wiederverwenden, die verschiedene Logos oder Grafiken enthalten, die Sie überlagern möchten, und einfach den an den Auftrag übergebenen Ressourcennamen ändern, während Sie denselben Bezeichnungsnamen „logo“ für die Transformation verwenden, um ihn abzugleichen.

``` csharp
    // Add both the Video and the Overlay image assets here as inputs to the job.
    List<JobInput> jobInputs = new List<JobInput>() {
        new JobInputAsset(assetName: inputAssetName),
        new JobInputAsset(assetName: overlayAssetName, label: OverlayLabel)
    };
```

Führen Sie das Beispiel aus, indem Sie das Projekt in Visual Studio Code im Fenster „Ausführen und Debuggen“ auswählen. Das Beispiel gibt den Fortschritt des Codierungsvorgangs aus und lädt schließlich den Inhalt in den Ordner „/Output“ in Ihrem Projektstammverzeichnis herunter, oder bei einer vollständigen Visual Studio-Version auch in Ihren Ordner „/bin/Output“. 

Das Beispiel veröffentlicht auch den Inhalt für das Streaming und gibt die vollständigen HLS-, DASH- und Smooth Streaming-Manifestdatei-URLs aus, die in jedem kompatiblen Player verwendet werden können.  Sie können auch einfach die Manifest-URL in die [Azure Media Player-Demo](http://ampdemo.azureedge.net/) kopieren und die mit „/manifest“ endende URL in das URL-Feld einfügen und dann auf *Player aktualisieren* klicken.

## <a name="api-references"></a>API-Referenzen

* [VideoOverlay-Objekt](/rest/api/media/transforms/create-or-update#videooverlay)
* [Filter](/rest/api/media/transforms/create-or-update#filters)
* [StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)


[!INCLUDE [reference dotnet sdk references](./includes/reference-dotnet-sdk-references.md)]

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
