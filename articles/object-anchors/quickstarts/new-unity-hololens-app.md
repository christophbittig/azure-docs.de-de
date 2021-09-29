---
title: 'Schnellstart: Erstellen einer neuen HoloLens-Unity-App'
description: In diesem Schnellstart wird beschrieben, wie Sie eine HoloLens-Unity-App erstellen, die Object Anchors verwendet.
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 06/23/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: a83606d76beb4fd9cead19293636b9e5348d4990
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777562"
---
# <a name="quickstart-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-object-anchors"></a>Schnellstart: Ausführliche Anleitung zum Erstellen einer HoloLens-Unity-App mit Azure Object Anchors

In diesem Schnellstart wird beschrieben, wie Sie eine HoloLens-Unity-App erstellen, die [Azure Object Anchors](../overview.md) verwendet. Azure Object Anchors ist ein verwalteter Clouddienst, der 3D-Ressourcen in KI-Modelle konvertiert, die objektbezogene Mixed Reality-Erlebnisse für HoloLens ermöglichen. Wenn Sie fertig sind, verfügen Sie über eine HoloLens-App, die mit Unity erstellt wurde und Objekte in der physischen Welt erkennen kann.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie für diese Schnellstartanleitung sicher, dass Sie über Folgendes verfügen:

* Alle Voraussetzungen aus den Schnellstarts [Unity-HoloLens](get-started-unity-hololens.md) oder [Unity-HoloLens mit MRTK](get-started-unity-hololens-mrtk.md)
* <a href="https://unity3d.com/get-unity/download" target="_blank">Unity Hub mit Unity 2020.3.8f1 oder neuer</a>

## <a name="getting-started"></a>Erste Schritte

Wir werden zuerst unser Projekt und die Unity-Szene einrichten:

1. Starten Sie Unity Hub.
1. Wählen Sie **Neu** und dann Unity 2020.3.8f1 oder neuer aus.
1. Vergewissern Sie sich, dass **3D** ausgewählt ist.
1. Benennen Sie Ihr Projekt, und geben Sie einen **Speicherort** ein.
1. Klicken Sie auf **Erstellen**.
1. Nachdem der **Unity-Editor** geöffnet wurde, speichern Sie die leere Standardszene über **Datei** > **Speichern unter** in einer neuen Datei.
1. Wählen Sie den Ordner **Szenen** aus, nennen Sie die neue Szene **Main**, und klicken Sie auf die Schaltfläche **Speichern**.

## <a name="configure-as-uwp"></a>Konfigurieren als UWP

1. Wählen Sie **Datei -> Buildeinstellungen** aus.
1. Wählen Sie **Universelle Windows-Plattform** und dann **Plattform wechseln** aus.
1. Wenn vom Unity-Editor gemeldet wird, dass Sie zunächst einige Komponenten herunterladen müssen, müssen Sie diese herunterladen und installieren.

## <a name="install-mixed-reality-feature-tool-feature-packages"></a>Installieren der Featurepakete für das Mixed Reality-Featuretool

1. In der Dokumentation zum <a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality-Featuretool</a> erfahren Sie, wie Sie das Tool einrichten und verwenden.
1. Installieren Sie im Abschnitt **Plattformunterstützung** das Featurepaket **Mixed Reality OpenXR Plugin** in Version 1.0.0 oder neuer im Unity-Projektordner.
1. Installieren Sie im Abschnitt **Azure Mixed Reality Services** das Featurepaket **Microsoft Azure Object Anchors** im Unity-Projektordner.
1. Wechseln Sie zurück zu Ihrem **Unity-Editor**. Es kann einige Minuten dauern, bis die Featurepakete für das **Mixed Reality-Featuretool** installiert sind.
1. Es wird ein Dialogfeld angezeigt, in dem Sie aufgefordert werden, die Aktivierung des neuen Eingabesystems zu bestätigen.
1. Klicken Sie auf die Schaltfläche **Ja**.
1. Nachdem der Installationsvorgang abgeschlossen ist, wird Unity automatisch neu gestartet.

## <a name="configure-openxr"></a>Konfigurieren von OpenXR

1. Sie sollten sich weiterhin im Fenster **Buildeinstellungen** befindet.
1. Wählen Sie die Schaltfläche **Playereinstellungen** aus.
1. Das Fenster **Projekteinstellungen** wird geöffnet.
1. Wählen Sie den Eintrag **XR-Plug-In-Verwaltung** aus.
1. Befolgen Sie die Anweisungen in der Dokumentation zum <a href="/windows/mixed-reality/develop/unity/xr-project-setup#configuring-xr-plugin-management-for-openxr" target="_blank">Konfigurieren der XR-Plug-In-Verwaltung für OpenXR</a>, um **OpenXR** mit dem **Microsoft HoloLens-Featureset** in der Liste **Plug-In-Anbieter** einzurichten.

## <a name="set-capabilities"></a>Festlegen von Funktionen

1. Sie sollten sich weiterhin im Fenster **Projekteinstellungen** befindet.
1. Wählen Sie den Eintrag **Player** aus.
1. Stellen Sie im Bereich **Inspektor** für die **Playereinstellungen** sicher, dass das Symbol **UWP-Einstellungen** ausgewählt ist.
1. Stellen Sie im Abschnitt „Funktionen“ der **Veröffentlichungseinstellungen** sicher, dass **InternetClientServer**, **WebCam** und **SpatialPerception** ausgewählt sind.

## <a name="set-up-the-project-settings"></a>Einrichten der Projekteinstellungen

1. Sie sollten sich weiterhin im Fenster **Projekteinstellungen** befindet.
1. Wählen Sie den Eintrag **Qualität** aus.
1. Wählen Sie in der Spalte unter dem **Universelle Windows-Plattform**-Logo den Pfeil in der Zeile **Standard** und dann **Sehr niedrig** aus. Sie wissen, dass die Einstellung ordnungsgemäß angewandt wurde, wenn das Feld in der Spalte **Universelle Windows-Plattform** und der Zeile **Sehr niedrig** grün ist.
1. Schließen Sie die Fenster **Projekteinstellungen** und **Buildeinstellungen**.
1. Befolgen Sie die Dokumentation zur <a href="/windows/mixed-reality/develop/unity/xr-project-setup#optimization" target="_blank">Optimierung</a>, um die empfohlenen Projekteinstellungen für die HoloLens 2 anzuwenden.

## <a name="set-up-the-main-virtual-camera"></a>Einrichten der virtuellen Hauptkamera

1. Wählen Sie im Bereich **Hierarchie** die Option **Hauptkamera** aus.
1. Legen Sie im **Inspector** die Transformationsposition auf **0,0,0** fest.
1. Suchen Sie die Eigenschaft **Flags löschen** und ändern Sie das Dropdown von **Skybox** in **Volltonfarbe**.
1. Wählen Sie das Feld **Hintergrund** aus, um den Farbwähler zu öffnen.
1. Legen Sie **R, G, B und A** auf **0** fest.
1. Ändern Sie die Eigenschaft **Vordere Clippingebene** in 0,1.
1. Wählen Sie **Komponente hinzufügen** aus, suchen Sie nach dem **Tracked Pose Driver** (Treiber für Haltungsnachverfolgung), und fügen Sie ihn hinzu.
1. Wählen Sie **Datei** > **Speichern** aus.

## <a name="trying-it-out"></a>Ausprobieren

Um zu testen, dass alles funktioniert, erstellen Sie Ihre App in **Unity**, und stellen Sie sie aus **Visual Studio** bereit. Gehen Sie hierzu wie in Kapitel 6 des <a href="/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio" target="_blank">**MR-Grundlagen 100: Erste Schritte mit Unity**-Kurses</a> vor. Es sollte der Unity-Startbildschirm und anschließend eine leere Anzeige angezeigt werden.

## <a name="create-your-script"></a>Erstellen Ihres Skripts

1. Erstellen Sie im Bereich **Projekt** einen neuen Ordner namens **Skripts** unter dem Ordner **Assets** (Ressourcen).
1. Klicken Sie mit der rechten Maustaste auf den Ordner, und wählen Sie dann **Erstellen >** , **C#-Skript** aus. Benennen Sie es mit **ObjectSearch**.
1. Wechseln Sie zu **GameObject** -> **Leer erstellen**.
1. Wählen Sie es aus, und benennen Sie es im **Inspektor** von **GameObject** in **Object Observer** um.
1. Wählen Sie **Komponente hinzufügen** aus, suchen Sie nach dem Skript **ObjectSearch**, und fügen Sie es hinzu.
1. Wählen Sie **Datei** > **Speichern** aus.

## <a name="start-implementing-your-app"></a>Starten der Implementierung Ihrer App

Sie können mit dem Hinzufügen Ihres eigenen Codes im Skript **ObjectSearch** beginnen. Verwenden Sie dazu das Object Anchors Runtime SDK. Nutzen Sie die [SDK-Übersicht](../concepts/sdk-overview.md) als Ausgangspunkt, um die Grundlagen zu erlernen und es anhand des Beispielcodes auszuprobieren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte: SDK-Übersicht](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen](../faq.md)

> [!div class="nextstepaction"]
> [Konvertierungs-SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
