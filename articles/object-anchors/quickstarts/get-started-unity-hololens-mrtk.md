---
title: 'Schnellstart: Erstellen einer HoloLens-App mit Unity und MRTK'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie eine HoloLens Unity-App erstellen, indem Sie MRTK und Object Anchors verwenden.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 09/08/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 987c8933040b71c8053728f64da2964e02ac6fa3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607309"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>Schnellstart: Erstellen einer HoloLens-App mit Azure Object Anchors in Unity mit MRTK

In diesem Schnellstart erstellen Sie eine Unity HoloLens-App, die [Azure Object Anchors](../overview.md) verwendet. Azure Object Anchors ist ein verwalteter Clouddienst, der 3D-Ressourcen in KI-Modelle konvertiert, die objektbezogene Mixed Reality-Erlebnisse für HoloLens ermöglichen. Wenn Sie fertig sind, verfügen Sie über eine HoloLens-App, die mit Unity erstellt wurde und Objekte in der physischen Welt erkennen kann.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Vorbereiten von Unity-Buildeinstellungen
> * Exportieren des HoloLens-Visual Studio-Projekts
> * Bereitstellen und Ausführen der App auf einem HoloLens 2-Gerät.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

## <a name="open-the-sample-project"></a>Öffnen des Beispielprojekts

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Öffnen Sie das Projekt `quickstarts/apps/unity/mrtk` in Unity.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Configure Account](../../../includes/object-anchors-get-started-configure-account.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

Wenn Sie durch ein Dialogfeld „TMP Importer“ zum Importieren von TextMesh Pro-Ressourcen aufgefordert werden, wählen Sie „Import TMP Essentials“ (TMP Essentials importieren) aus.
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="Importieren von TextMesh Pro-Ressourcen":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

 Nach dem Unity-Begrüßungsbildschirm sollte ein weißer Begrenzungsrahmen angezeigt werden. Sie können Ihre Hand verwenden, um den Begrenzungsrahmen zu verschieben, zu skalieren oder zu drehen. Platzieren Sie den Rahmen so, dass das Objekt bedeckt ist, das Sie erkennen möchten.

Öffnen Sie das <a href="/windows/mixed-reality/mrtk-unity/features/ux-building-blocks/hand-menu" target="_blank">Handmenü</a>, und wählen Sie **Lock SearchArea** (SearchArea sperren) aus, um jede weitere Bewegung des Begrenzungsrahmens zu verhindern. Wählen Sie **Start Search** (Suche starten) aus, um die Objekterkennung zu starten. Wenn das Objekt erkannt wird, wird ein Gittermodell für das Objekt gerendert. Details zu einer erkannten Instanz werden auf dem Bildschirm angezeigt, z. B. der aktualisierte Zeitstempel und das Verhältnis der Oberflächenbedeckung. Wählen Sie **Stop Search** (Suche beenden) aus, um die Nachverfolgen zu beenden. Alle erkannten Instanzen werden entfernt.

#### <a name="the-app-menus"></a>Die App-Menüs

Sie können mit dem <a href="/windows/mixed-reality/mrtk-unity/features/ux-building-blocks/hand-menu" target="_blank">Handmenü</a> auch weitere Aktionen ausführen.

##### <a name="primary-menu"></a>Primäres Menü

* **Start Search/Stop Search (Suche starten/Suche beenden)** : Startet oder beendet den Objekterkennungsprozess.
* **Toggle Spatial Mapping (Räumliche Zuordnung umschalten)** : Rendering der räumlichen Zuordnung anzeigen/ausblenden. Diese Option kann unabhängig davon zum Debuggen verwendet werden, ob der Scan beendet ist.
* **Tracker Settings (Einstellungen für die Nachverfolgung)** : Schaltet die Aktivierung des Menüs mit den Einstellungen für die Nachverfolgung um.
* **Search Area Settings (Einstellungen für den Suchbereich)** : Schaltet die Aktivierung des Menüs mit den Einstellungen für den Suchbereich um.
* **Start Tracing (Ablaufverfolgung starten)** : Erfassen von Diagnosedaten und Speichern dieser Daten auf dem Gerät. Weitere Informationen finden Sie im Abschnitt **Debuggen von Erkennungsproblemen und Erfassen von Diagnosen**.
* **Upload Tracing (Ablaufverfolgung hochladen)** : Hochladen von Diagnosedaten in den Object Anchors-Dienst.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Primäres Unity-Handmenü":::

##### <a name="tracker-settings-menu"></a>Menü für Nachverfolgungseinstellungen

* **High Accuracy (Hohe Genauigkeit)** : Ein experimentelles Feature, das verwendet wird, um eine genauere Position zu erzielen. Wenn Sie diese Option aktivieren, werden während der Objekterkennung mehr Systemressourcen benötigt. Das Objektgittermodell wird in diesem Modus in rosa gerendert. Wählen Sie diese Schaltfläche erneut aus, um zurück in den normalen Nachverfolgungsmodus zu wechseln.
* **Relaxed Vertical Alignment (Gelockerte vertikale Ausrichtung)** : Wenn diese Option aktiviert ist, kann ein Objekt in einem nicht vertikalen Winkel erkannt werden. Nützlich zum Erkennen von Objekten auf Rampen.
* **Allow Scale Change (Skalierungsänderung zulassen)** : Ermöglicht der Nachverfolgung das Ändern der Größe des erkannten Objekts basierend auf Umgebungsinformationen.
* **Coverage Ratio Slider (Schieberegler für das Abdeckungsverhältnis)** : Passt den Anteil der Oberflächenpunkte an, die übereinstimmen müssen, damit die Nachverfolgung ein Objekt erkennt.  Niedrigere Werte ermöglichen der Nachverfolgung eine bessere Erkennung von Objekten, die für die HoloLens-Sensoren schwierig zu erkennen sind, z. B. dunkle Objekte oder stark reflektierende Objekte. Höhere Werte verringern die Häufigkeit falscher Erkennungen.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Unity-Handmenü für Nachverfolgung":::

##### <a name="search-area-settings-menu"></a>Menü für Suchbereichseinstellungen

* **Lock Search Area (Suchbereich sperren)** : Begrenzungsrahmen des Sperrbereichs, um eine versehentliche Bewegung durch die Hände zu verhindern.
* **Auto-Adjust Search Area (Suchbereich automatisch anpassen)** : Ermöglicht dem Suchbereich, sich selbst während der Objekterkennung neu zu positionieren.
* **Cycle Mesh (Gittermodell durchlaufen)** : Durchläuft die Visualisierung der geladenen Gittermodelle innerhalb des Suchbereichs.  Mit dieser Option können Benutzer das Suchfeld für schwer zu erkennende Objekte ausrichten.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Unity-Handmenü für Suchbereich":::

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Ausführliche exemplarische Vorgehensweise zum MRTK](in-depth-mrtk-walkthrough.md)

> [!div class="nextstepaction"]
> [Konzepte: SDK-Übersicht](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen](../faq.md)

> [!div class="nextstepaction"]
> [Konvertierungs-SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)

> [!div class="nextstepaction"]
> [Problembehandlung bei der Objekterkennung](../troubleshoot/object-detection.md)
