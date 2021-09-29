---
title: 'Schnellstart: Aktualisieren der Schnellstart-App auf Unity 2020'
description: In diesem Schnellstart wird beschrieben, wie Sie die Schnellstart-App auf Unity 2020 aktualisieren, um eine HoloLens-Unity-App zu erstellen, die Object Anchors verwendet.
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 06/23/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: b628aab20e08460ea6d023651098227aa3390d5e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791990"
---
# <a name="quickstart-upgrade-quickstart-app-to-unity-2020"></a>Schnellstart: Aktualisieren der Schnellstart-App auf Unity 2020

In diesem Schnellstart führen Sie ein Upgrade einer Unity-HoloLens-App, die [Azure Object Anchors](../overview.md) verwendet, von Unity 2019 auf Unity 2020 durch. Azure Object Anchors ist ein verwalteter Clouddienst, der 3D-Ressourcen in KI-Modelle konvertiert, die objektbezogene Mixed Reality-Erlebnisse für HoloLens ermöglichen. Wenn Sie fertig sind, verfügen Sie über eine HoloLens-App, die mit Unity erstellt wurde und Objekte in der physischen Welt erkennen kann.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Aktualisieren der App von Unity 2019 auf Unity 2020
> * Aktualisieren von Paketabhängigkeiten
> * Aktualisieren von Unity-Buildeinstellungen

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie für diese Schnellstartanleitung sicher, dass Sie über Folgendes verfügen:

* Alle Voraussetzungen aus den Schnellstarts [Unity-HoloLens](get-started-unity-hololens.md) oder [Unity-HoloLens mit MRTK](get-started-unity-hololens-mrtk.md)
* <a href="https://unity3d.com/get-unity/download" target="_blank">Unity Hub mit Unity 2020.3.8f1 oder neuer</a>

## <a name="open-and-upgrade-the-sample-project"></a>Öffnen und Aktualisieren des Beispielprojekts

Führen Sie die Schritte der Schnellstarts [Unity-HoloLens](get-started-unity-hololens.md) oder [Unity-HoloLens mit MRTK](get-started-unity-hololens-mrtk.md) aus, um das [Beispielrepository](https://github.com/Azure/azure-object-anchors) zu klonen und das Azure Object Anchors-Paket für Unity herunterzuladen.

Öffnen Sie Unity Hub. Wählen Sie die Schaltfläche **Hinzufügen** und dann eines der Projekte `quickstarts/apps/unity/basic` oder `quickstarts/apps/unity/mrtk` aus. Wählen Sie anschließend in der Spalte **Unity-Version** in der Dropdownliste die Version von Unity 2020 aus, die auf Ihrem Computer installiert ist. Wählen Sie in der Spalte **Zielplattform** die Option **Universelle Windows-Plattform** aus. Wählen Sie abschließend die Spalte **Projektname** aus, und öffnen Sie das Beispiel in Unity.

:::image type="content" source="./media/upgrade-unity-2020.png" alt-text="Upgrade auf Unity 2020":::

Es wird ein Dialogfeld angezeigt, in dem Sie aufgefordert werden, das Upgrade Ihres Projekts zu bestätigen. Wählen Sie die Schaltfläche **Bestätigen** aus.

:::image type="content" source="./media/confirm-unity-upgrade.png" alt-text="Bestätigen des Unity-Upgrades":::

## <a name="upgrade-package-dependencies"></a>Aktualisieren von Paketabhängigkeiten

Nachdem der Upgradevorgang abgeschlossen ist, wird der **Unity-Editor** geöffnet.

In der Dokumentation zum <a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality-Featuretool</a> erfahren Sie, wie Sie das Tool einrichten und verwenden.

Installieren Sie im Abschnitt **Plattformunterstützung** das Featurepaket **Mixed Reality OpenXR Plugin** in Version 1.0.0 oder neuer im Unity-Projektordner. Wenn Sie mit dem Projekt `quickstarts/apps/unity/mrtk` arbeiten, öffnen Sie auch den Abschnitt **Mixed Reality-Toolkit**, suchen Sie die Featurepakete **Mixed Reality Toolkit Foundation** und **Mixed Reality Toolkit Tools**, und aktualisieren Sie diese auf Version 2.7.0 oder höher.

Wechseln Sie zurück zu Ihrem **Unity-Editor**. Es kann einige Minuten dauern, bis die Featurepakete für das **Mixed Reality-Featuretool** installiert sind.

Es wird ein Dialogfeld angezeigt, in dem Sie aufgefordert werden, die Aktivierung des neuen Eingabesystems zu bestätigen. Klicken Sie auf die Schaltfläche **Ja**.

:::image type="content" source="./media/new-input-system.png" alt-text="Neues Eingabesystem":::

 Wenn Sie in einem Dialogfeld aufgefordert werden, MRTK-Shader zu überschreiben, wählen Sie **Ja** aus.

:::image type="content" source="./media/mrtk-shaders.png" alt-text="MRTK-Shader":::

Nachdem der Installationsvorgang abgeschlossen ist, wird Unity automatisch neu gestartet.

## <a name="update-configuration-settings"></a>Konfigurationseinstellungen aktualisieren

Befolgen Sie im **Unity-Editor** die Dokumentation zum <a href="/windows/mixed-reality/develop/unity/xr-project-setup#configuring-xr-plugin-management-for-openxr" target="_blank">Konfigurieren der XR-Plug-In-Verwaltung für OpenXR</a>, um die **XR-Plug-In-Verwaltung** in Ihren **Projekteinstellungen** einzurichten. Befolgen Sie dann die Dokumentation zur <a href="/windows/mixed-reality/develop/unity/xr-project-setup#optimization" target="_blank">Optimierung</a>, um die empfohlenen Projekteinstellungen für die HoloLens 2 anzuwenden.

## <a name="update-mrtk-settings"></a>Aktualisieren der MRTK-Einstellungen

Wenn Sie mit dem Projekt `quickstarts/apps/unity/mrtk` arbeiten, müssen Sie auch MRTK anpassen. Führen Sie in diesem Fall die unten angegebenen Schritte aus. Fahren Sie andernfalls mit dem Abschnitt **Erstellen, Bereitstellen und Ausführen der App** fort.

Navigieren Sie im **Unity-Editor** zu `Assets/MixedReality.AzureObjectAnchors/Scenes`, und öffnen Sie **AOASampleScene**. Wählen Sie im Bereich **Hierarchie** das **MixedRealityToolkit**-Objekt aus.

:::image type="content" source="./media/open-sample-scene.png" alt-text="Öffnen der Beispielszene":::

Wählen Sie im Bereich **Inspektor** die Schaltfläche **Kamera** aus, und ändern Sie das Profil von **ObsoleteXRSDKCameraProfile** in **DefaultMixedRealityCameraProfile**.

:::image type="content" source="./media/update-camera-profile.png" alt-text="Aktualisieren des Kameraprofils":::

Wählen Sie im Bereich **Inspektor** die Schaltfläche **Eingabe** aus, und erweitern Sie die Dropdownliste **Eingabedatenanbieter**. Befolgen Sie dann die Dokumentation zum <a href="/windows/mixed-reality/mrtk-unity/configuration/getting-started-with-mrtk-and-xrsdk#configuring-mrtk-for-the-xr-sdk-pipeline" target="_blank">Konfigurieren des MRTK für die XR SDK-Pipeline</a>, um die richtigen Eingabedatenanbieter (**OpenXRDeviceManager** und **WindowsMixedRealityDeviceManager**) einzurichten.

:::image type="content" source="./media/update-input-profile.png" alt-text="Aktualisieren des Eingabeprofils":::

## <a name="build-deploy-and-run-the-app"></a>Erstellen, Bereitstellen und Ausführen der App

Ihr Projekt wurde jetzt vollständig auf Unity 2020 aktualisiert. Befolgen Sie die Anweisungen in den Schnellstarts [Unity-HoloLens](get-started-unity-hololens.md) oder [Unity-HoloLens mit MRTK](get-started-unity-hololens-mrtk.md), um die App zu erstellen, bereitzustellen und auszuführen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte: SDK-Übersicht](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen](../faq.md)

> [!div class="nextstepaction"]
> [Konvertierungs-SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
