---
title: Installieren von Azure Spatial Anchors für Unity
description: Konfigurieren eines Unity-Projekts für die Verwendung von Azure Spatial Anchors
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/12/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 945a95054f281e0a5920232a729de4087d1deab6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490492"
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

### <a name="hololens-only"></a>nur HoloLens

#### <a name="configure-your-unity-project-xr-settings"></a>Konfigurieren Sie die Einstellungen Ihres Unity-Projekts XR
Bei der Entwicklung von MixedReality-Apps auf HoloLens müssen Sie die XR-Konfiguration in Unity einrichten. Weitere Informationen finden Sie unter [Einrichten der XR-Konfiguration - Mixed Reality | Microsoft Dokumentation](/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr) und [Auswahl einer Unity-Version und eines XR-Plugins - Mixed Reality | Microsoft Docs](/windows/mixed-reality/develop/unity/choosing-unity-version).

Azure Spatial Anchors SDK Versionen 2.9.0 oder früher bieten nur Unterstützung für das Windows XR Plugin (com.unity.xr.windowsmr), und daher hat das Azure Spatial Anchors windows Paket eine explizite Abhängigkeit von dem Windows XR Plugin.

Azure Spatial Anchors SDK Versionen 2.10.0 oder höher bieten Unterstützung sowohl für das Mixed Reality OpenXR Plugin ([com.microsoft.mixedreality.openxr](https://dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging?_a=package&feed=Unity-packages&view=overview&package=com.microsoft.mixedreality.openxr&protocolType=Npm)) als auch für das Windows XR Plugin ([com.unity.xr.windowsmr](https://docs.unity3d.com/Manual/com.unity.xr.windowsmr.html)). Je nach Wahl müssen Sie entweder das Paket com.microsoft.mixedreality.openxr oder das Paket com.unity.xr.windowsmr in Ihr Projekt einbinden.

#### <a name="configure-your-unity-project-capabilities"></a>Konfigurieren Sie die Fähigkeiten Ihres Unity-Projekts

Stellen Sie sicher, dass Sie die folgenden Funktionen in Ihrem Unity-Projekt aktivieren:
- SpatialPerception
- InternetClient
- PrivateNetworkClientServer

> [!WARNING]
> Wenn die PrivateNetworkClientServer-Fähigkeit nicht aktiviert ist, kann es zu einem Fehler bei der Abfrage von Ankern kommen, wenn das Gerät ein Netz verwendet, das als privat konfiguriert ist.
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