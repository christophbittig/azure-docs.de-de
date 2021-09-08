---
title: 'Schnellstart: Speech SDK für Java (Windows, Linux, macOS): Plattformeinrichtung – Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Leitfaden, um Ihre Plattform für die Verwendung von Java (Windows, Linux, macOS) mit dem Speech Service SDK einzurichten.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 12fb77f06fc7e417078ef6fffbc155dc2384846e
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544209"
---
In diesem Leitfaden erfahren Sie, wie Sie das [Sprach-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) für 64-Bit-Java 8 JRE installieren. Wenn Sie nur den Paketnamen benötigen, um selbständig einzusteigen, ist das Java SDK im zentralen Maven-Repository nicht verfügbar. Unabhängig davon, ob Sie Gradle oder eine Abhängigkeitsdatei vom Typ `pom.xml` verwenden, müssen Sie ein benutzerdefiniertes Repository hinzufügen, das auf `https://csspeechstorage.blob.core.windows.net/maven/` verweist. (Der Paketname ist weiter unten angegeben.)

> [!NOTE]
> Weitere Informationen zum SDK für sprachaktivierte Geräte und zum Roobo-Gerät finden Sie unter [SDK für sprachaktivierte Geräte](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

- Das Java-Sprach-SDK-Paket ist für die folgenden Betriebssysteme verfügbar:
  - Windows: Nur 64-Bit
  - Mac: macOS X Version 10.13 oder höher
  - Linux: Zeigen Sie die Liste mit den [unterstützten Linux-Distributionen und Zielarchitekturen](~/articles/cognitive-services/speech-service/speech-sdk.md) an.

## <a name="prerequisites"></a>Voraussetzungen

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) oder [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (erfordert, dass Java bereits installiert ist)

- Unter Windows benötigen Sie [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) für Ihre Plattform. Bei der Erstinstallation ist möglicherweise ein Neustart erforderlich.

- Sehen Sie sich unter Linux die [Systemanforderungen und Setupanweisungen](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk) an.

## <a name="gradle-config"></a>Gradle-Konfiguration

Gradle-Konfigurationen erfordern sowohl ein benutzerdefiniertes Repository als auch einen expliziten Verweis auf die Abhängigkeitserweiterung `.jar`.

```groovy
// build.gradle

repositories {
    maven {
        url "https://csspeechstorage.blob.core.windows.net/maven/"
    }
}

dependencies {
    implementation group: 'com.microsoft.cognitiveservices.speech', name: 'client-sdk', version: "1.18.0", ext: "jar"
}
```

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Erstellen eines Eclipse-Projekts und Installieren des Sprach-SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [windows](../quickstart-list.md)]
