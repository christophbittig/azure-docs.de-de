---
title: 'Schnellstart: Speech SDK für C# Unity: Plattformeinrichtung – Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Leitfaden, um Ihre Plattform mit dem Speech Service SDK für C# Unity einzurichten.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: eur
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: 5e2a9f88914e83300207895d73c6c0c9d2ddd6cb
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502340"
---
In diesem Leitfaden erfahren Sie, wie Sie das [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) für [Unity](https://unity3d.com/) installieren.

> [!NOTE]
> Das Speech SDK für Unity unterstützt Windows Desktop (x86 und x64) oder die universelle Windows-Plattform (x86, x64, ARM/ARM64) sowie Android (x86, ARM32/64), iOS (x64-Simulator und ARM64) und Mac (x64).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

- Unter Windows benötigen Sie [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) für Ihre Plattform. Bei der Erstinstallation ist möglicherweise ein Neustart erforderlich.
- [Mindestens Unity 2018.3](https://store.unity.com/), wobei [Unity 2019.1 auch UWP ARM64 unterstützt](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Visual Studio 2017 ab Version 15.9 ist ebenfalls akzeptabel.
- Installieren Sie zur Unterstützung von Windows ARM64 die [optionalen Buildtools für ARM64 und das Windows 10 SDK für ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
- Unter Android benötigen Sie ein für die Entwicklung aktiviertes ARM-basiertes Android-Gerät (API 23: ab Android 6.0 Marshmallow) mit einem funktionsfähigen Mikrofon.
- Unter iOS benötigen Sie ein für die Entwicklung aktiviertes iOS-Gerät (ARM64) mit einem funktionsfähigen Mikrofon.
- Unter macOS benötigen Sie ein Mac-Gerät (x64) und die neueste LTS-Version von Unity 2019 (oder höher) für die integrierte Unterstützung des Mikrofonzugriffs in den Unity Player-Einstellungen.

## <a name="install-the-speech-sdk"></a>Installieren des Speech SDK

Führen Sie die folgenden Schritte aus, um das Speech SDK für Unity zu installieren:

1. Laden Sie das als Unity-Ressourcenpaket (UNITYPACKAGE-Datei) vorliegende [Speech SDK für Unity](https://aka.ms/csspeech/unitypackage) herunter, und öffnen Sie es. Das Paket sollte bereits mit Unity verknüpft sein. Nach dem Öffnen des Ressourcenpakets wird das Dialogfeld **Import Unity Package** (Unity-Paket importieren) angezeigt. Unter Umständen muss ein leeres Projekt erstellt und geöffnet werden, damit dieser Schritt funktioniert.

   [![Dialogfeld „Import Unity Package“ (Unity-Paket importieren) im Unity-Editor](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Vergewissern Sie sich, dass alle Dateien ausgewählt sind, und wählen Sie **Import** (Importieren) aus. Daraufhin wird das Unity-Ressourcenpaket in Ihr Projekt importiert.

Weitere Informationen zum Importieren von Ressourcenpaketen in Unity finden Sie in der [Unity-Dokumentation](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [windows](../quickstart-list.md)]
