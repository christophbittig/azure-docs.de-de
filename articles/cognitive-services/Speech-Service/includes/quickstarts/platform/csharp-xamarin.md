---
title: 'Schnellstart: Speech SDK für C# (Xamarin): Plattformeinrichtung – Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Leitfaden, um Ihre Plattform mit dem Speech Service SDK für C# (Xamarin) einzurichten.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: eb5d842d5934a46b98a7707b6f567c26445df70e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253874"
---
In diesem Leitfaden erfahren Sie, wie Sie das [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) für [Xamarin](/xamarin/get-started/what-is-xamarin) installieren – eine Open-Source-Plattform zur Entwicklung moderner und leistungsfähiger Anwendungen für iOS, Android und Windows mit .NET. Führen Sie `Install-Package Microsoft.CognitiveServices.Speech` in der NuGet-Konsole aus, wenn Sie nur den Paketnamen benötigen und selbständig einsteigen möchten.

> [!NOTE]
> Das Speech SDK für Xamarin unterstützt Windows Desktop (x86 und x64) oder die universelle Windows-Plattform (x86, x64, ARM/ARM64) sowie Android (x86, ARM32/64) und iOS (x64-Simulator und ARM64).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist Folgendes erforderlich:

* Unter Windows benötigen Sie [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) für Ihre Plattform. Bei der Erstinstallation ist möglicherweise ein Neustart erforderlich.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Erstellen eines Visual Studio-Projekts und Installieren des Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

Das Speech SDK ist jetzt installiert. Das in den vorherigen Schritten erstellte Projekt „helloworld“ kann nun gelöscht oder wiederverwendet werden.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [windows](../quickstart-list.md)]
