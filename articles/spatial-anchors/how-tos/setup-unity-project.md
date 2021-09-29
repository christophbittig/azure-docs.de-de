---
title: Installieren von Azure Spatial Anchors für Unity
description: Konfigurieren eines Unity-Projekts für die Verwendung von Azure Spatial Anchors
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/30/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 5a3c8c3369890bdbbd30a98f6f76c88b9358dcdf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791800"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurieren von Azure Spatial Anchors in einem Unity-Projekt

In diesem Leitfaden lernen Sie die ersten Schritte mit dem Azure Spatial Anchors SDK in Ihrem Unity-Projekt kennen.

## <a name="project-requirements"></a>Anforderungen des Projekts

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Konfigurieren eines Projekts

Bevor Sie das Azure Spatial Anchors SDK in Ihr Unity-Projekt einschließen, stellen Sie sicher, dass Sie die [erforderlichen](#project-requirements) Pakete über den Unity Package Manager installieren.

### <a name="download-asa-packages"></a>Herunterladen von ASA-Paketen
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>Importieren von ASA-Paketen
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="hololens-only-configure-your-unity-project-xr-settings"></a>Nur HoloLens: Konfigurieren der XR-Einstellungen Ihres Unity-Projekts
Bei der Entwicklung von MixedReality-Apps auf HoloLens müssen Sie die XR-Konfiguration in Unity einrichten. Weitere Informationen dazu finden Sie hier: [Einrichten Ihrer XR-Konfiguration - Mixed Reality | Microsoft-Dokumentation](/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr)     und [Auswählen einer Unity-Version und eines XR-Plugins - Mixed Reality | Microsoft-Dokumentation](/windows/mixed-reality/develop/unity/choosing-unity-version).

Das Azure Spatial Anchors SDK 2.9 (oder älter) unterstützt nur das Windows-XR-Plugin (com.unity.xr.windowsmr). Daher weist das Unity-Paket für Azure Spatial Anchor HoloLens eine explizite Abhängigkeit vom Paket com.unity.xr.windowsmr auf.

Das Azure Spatial Anchors SDK Version 2.10.0 (oder höher) unterstützt sowohl das Mixed Reality OpenXR-Plugin ([com.microsoft.mixedreality.openxr](https://dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging?_a=package&feed=Unity-packages&view=overview&package=com.microsoft.mixedreality.openxr&protocolType=Npm)) als auch das Windows-XR-Plugin ([com.unity.xr.windowsmr](https://docs.unity3d.com/Manual/com.unity.xr.windowsmr.html)). Sie müssen entweder das com.microsoft.mixedreality.openxr- oder das com.unity.xr.windowsmr-Paket in ein Projekt Ihrer Wahl integrieren.

### <a name="android-only-configure-the-maintemplategradle-file"></a>Nur Android: Konfigurieren der Datei „maintemplate. gradle“

1. Wechseln Sie zu **Bearbeiten** > **Projekteinstellungen** > **Player**.
2. Wählen Sie im **Inspector-Bereich** für **Playereinstellungen** das **Android**-Symbol aus.
3. Aktivieren Sie unter dem Abschnitt **Erstellen** das Kontrollkästchen **Custom Main Gradle Template** (Benutzerdefinierte Gradle-Hauptvorlage), um eine benutzerdefinierte Gradle-Vorlage unter `Assets\Plugins\Android\mainTemplate.gradle` zu erstellen.
4. Öffne in einem Text-Editor die Datei `mainTemplate.gradle`.
5. Fügen Sie im Abschnitt `dependencies` die folgenden Abhängigkeiten ein:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Wenn alles fertig ist, sollte Ihr `dependencies`-Abschnitt ungefähr wie folgt aussehen:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vorgehensweise: Erstellen und Suchen von Ankern in Unity](./create-locate-anchors-unity.md)