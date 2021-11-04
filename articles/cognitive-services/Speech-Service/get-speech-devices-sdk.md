---
title: Abrufen des SDK für sprachaktivierte Geräte
titleSuffix: Azure Cognitive Services
description: Der Speech-Dienst kann mit verschiedensten Geräten und Audioquellen verwendet werden. Jetzt können Sie einen Schritt weiter gehen und Ihre Sprachanwendungen mit angepasster Hardware und Software nutzen. In diesem Artikel erfahren Sie, wie Sie auf das Speech Devices SDK zugreifen und mit der Entwicklung beginnen.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: eur
ms.openlocfilehash: 76ef359ea125ccd17de1ff0670390eb7e89d4290
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509306"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Abrufen des Cognitive Services Speech-Geräte-SDK

Beim Speech Devices SDK handelt es sich um eine vorab optimierte Bibliothek für die Verwendung mit speziell entwickelten Development Kits und verschiedenen Mikrofonarraykonfigurationen.

## <a name="choose-a-development-kit"></a>Auswählen eines Development Kits

|Geräte|Spezifikation|BESCHREIBUNG|Szenarien|
|--|--|--|--|
|[Azure Percept Audio DK](../../azure-percept/overview-azure-percept-audio.md)<br>[Setup](../../azure-percept/quickstart-percept-dk-unboxing.md) / [Schnellstart](../../azure-percept/quickstart-percept-audio-setup.md)![Azure Percept Audio DK](./media/speech-devices-sdk/azure-percept-audio.png)|Lineares Array mit 4 Mikrofonen und XMOS-Codec. <br> Linux| Ein Zubehörgerät, das Ihrem Edgegerät sprachbezogene KI-Funktionen hinzufügt. Es umfasst einen vorkonfigurierten Audioprozessor sowie ein lineares 4-Mikrofon-Array und ermöglicht damit die Nutzung von Sprachbefehlen, Schlüsselworterkennung und Fernfeldspracherkennung über Azure Cognitive Services. Dieses Gerät wird mit Azure Percept DK, Azure Percept Studio und weiteren Azure Edge-Verwaltungsdiensten bereitgestellt, um eine nahtlose Integration in unser leistungsstärkstes und kompaktes SDK für All-in-One-Sprachgeräte zu ermöglichen.|Unterhaltungstranskription, Robotik, Smart Building, Fertigung, Landwirtschaft|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Setup](../../kinect-dk/set-up-azure-kinect-dk.md) / [Schnellstart](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|Array mit 7 Mikrofonen, RGB und Tiefenkameras <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows)/[Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|Ein Spatial Computing-DK mit erweiterten KI-Sensoren (künstliche Intelligenz) zur Entwicklung ausgereifter Modelle für maschinelles Sehen sowie Sprachmodelle. Es kombiniert ein erstklassiges räumliches Mikrofonarray und eine Tiefenkamera mit einer Videokamera und einem Lagesensor – alles in einem kleinen Gerät mit mehreren Modi, Optionen und SDKs für die nahtlose Integration in Azure Cognitive Services.|Unterhaltungstranskription, Robotik, Smart Building|
|[Urbetter Dev Kit](http://www.urbetter.com/products_56/278.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|Array mit 7 Mikrofonen, ARM SoC, WLAN, Ethernet, HDMI, USB-Kamera <br>Linux|Ein branchenübliches Speech Devices SDK zur Anpassung des Microsoft-Mikrofonarrays und zur Unterstützung erweiterter E/A wie HDMI/Ethernet und weiterer USB-Peripheriegeräte <br> [Kontakt mit Urbetter aufnehmen](http://www.urbetter.com/products_56/278.html)|Unterhaltungstranskription, Bildung, Kliniken, Roboter, OTT-Box, Sprach-Agent, Drive-through|
|[Roobo Smart Audio Dev Kit](http://ddk.roobo.com)<br>[Setup](speech-devices-sdk-roobo-v1.md) / [Schnellstart](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|Array mit 7 Mikrofonen, ARM SoC, WLAN, Audioausgang, EA <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|Das erste Speech Devices SDK zur Anpassung des Microsoft-Mikrofonarrays und Front Processing SDK für die Entwicklung hochwertiger Transkriptions- und Sprachszenarien|Unterhaltungstranskription, Smart Speaker, Sprach-Agent, Wearable|
|Roobo Smart Audio Dev Kit 2<br>[Einrichtung](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|Array mit 7 Mikrofonen, ARM SoC, WLAN, Bluetooth, EA <br>Linux|Das Speech Devices SDK der 2. Generation, das ein alternatives Betriebssystem und mehr Features in einem kostengünstigen Referenzentwurf umfasst|Unterhaltungstranskription, Smart Speaker, Sprach-Agent, Wearable|


## <a name="download-the-speech-devices-sdk"></a>Herunterladen des Speech-Geräte-SDK

Laden Sie das [Speech Devices SDK](./speech-devices-sdk.md) herunter.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit dem Speech-Geräte-SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android)
