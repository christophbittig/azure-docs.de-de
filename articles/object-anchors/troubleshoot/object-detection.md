---
title: Problembehandlung bei der Objekterkennung
description: Behebung von Problemen bei der Erkennung eines Objekts mit Azure Object Anchors.
author: craigktreasure
manager: rgarcia
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: troubleshooting
ms.service: azure-object-anchors
ms.openlocfilehash: b70babbf3f105903cc7b14d175908b972bde9158
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601993"
---
# <a name="troubleshooting-object-detection"></a>Problembehandlung bei der Objekterkennung

Dieser Artikel geht davon aus, dass Sie bereits ein 3D-Modell in ein Azure Object Anchors-Erkennungsmodell konvertiert und das Modell erfolgreich in eine Anwendung geladen haben.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

* Vergewissern Sie sich, dass das zu erkennende Modell innerhalb der unterstützten Größe (1-10 Meter) liegt, um ein optimales Ergebnis zu erzielen.
* Stellen Sie sicher, dass der Raum ausreichend Struktur aufweist, indem Sie einige Plakate aufhängen.
* Entfernen Sie die aktuellen Hologramme, um die Karte wie unten beschrieben [zurückzusetzen](#remove-holograms-to-reset-the-map).
* Scannen Sie das Objekt noch genauer.
* Geben Sie einen engen Begrenzungsrahmen als Suchbereich ein, der alle oder die meisten Objekte enthält.
* Löschen Sie den Cache für die räumliche Zuordnung und scannen Sie das Objekt erneut.
* Vergewissern Sie sich, dass bei der Modellkonvertierung die richtige Schwerkraftrichtung und Asset-Dimension verwendet wurden, wie [unten](#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct) beschrieben.
* Prüfen Sie das Erkennungsmodell visuell, wie [unten](#visually-inspect-the-detection-models-mesh) beschrieben.
* Anpassung der Modellabfragewerte wie beschrieben [unten](#adjust-object-query-values).
* Capture von Diagnosen wie beschrieben [unten](#capture-diagnostics).

### <a name="remove-holograms-to-reset-the-map"></a>Hologramme entfernen, um die Karte zurückzusetzen

Wenn bei der Erkennung von Objekten eines der folgenden Probleme auftritt, kann das Entfernen und Zurücksetzen der Karte das Problem beheben:
* Invertierte Ausrichtung
* Falsche Pose
* Gekipptes Modell

Um Hologramme zu entfernen und die Karte zurückzusetzen, öffnen Sie die App **Einstellungen** und gehen Sie zu **System** -> **Hologramme**. Wählen Sie dann **Alle Hologramme entfernen**, um mit einer neuen Karte zu beginnen.

Durch das Löschen der Hologramme wird sichergestellt, dass Objekte in ihrer aktuellen Position richtig erkannt werden, falls sie kürzlich bewegt wurden.

Scannen Sie Ihre Umgebung erneut, indem Sie mit der HoloLens in der Umgebung herumlaufen. Gehen Sie einige Male in einem Abstand von 1-2 Metern um die Objekte herum, die Sie erkennen möchten.

### <a name="ensure-the-gravity-direction-and-asset-dimension-unit-are-correct"></a>Stellen Sie sicher, dass die Schwerkraftrichtung und die Maßeinheit des Assets korrekt sind.

Wenn Sie ein 3D-Modell zur Konvertierung mit dem Object Anchors Conversion SDK (siehe [hier](../quickstarts/get-started-model-conversion.md)) einreichen, müssen Sie die richtige Schwerkraftrichtung (`Gravity`) und Maßeinheit (`AssetDimensionUnit`) für Ihr 3D-Modell eingeben. Wenn diese Werte nicht korrekt sind, ist es unwahrscheinlich, dass Object Anchors Ihr Objekt richtig erkennt.

Die Gravitationsrichtung ist der Abwärtsvektor, der auf die Erde zeigt. Bei CAD-Modellen ist die Richtung der Schwerkraft in der Regel das Gegenteil der Richtung „Nach oben“. In vielen Fällen steht „+Z“ für die Aufwärtsrichtung, sodass in diesem Fall „–Z“ oder `Vector3(0.0, 0.0, -1.0)` die Richtung der Schwerkraft darstellen würden. Bei der Bestimmung der Schwerkraft sollten Sie auch die Ausrichtung berücksichtigen, in der das Modell während der Laufzeit zu sehen sein wird. Wenn Sie versuchen, einen Stuhl in der realen Welt auf einer flachen Oberfläche zu erkennen, könnte die Schwerkraft `Vector3(0.0, 0.0, -1.0)` sein. Wenn der Stuhl mit einer Neigung von 45 Grad positioniert ist, könnte die Schwerkraft `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)` sein.

Die Richtung der Schwerkraft kann mit einem 3D-Rendering-Tool wie [MeshLab](http://www.meshlab.net/) bestimmt werden.

Die Maßeinheit stellt den Maßstab des Modells dar. Die unterstützten Einheiten finden Sie mithilfe der Enumeration **Microsoft.Azure.ObjectAnchors.Conversion.AssetLengthUnit**.

Sie können auch die Anweisungen [hier](../visualize-converted-model.md) befolgen, um ein Erkennungsmodell in Unity zu visualisieren und visuell zu prüfen, ob Schwerkraftrichtung und -maßstab korrekt aussehen.

### <a name="visually-inspect-the-detection-models-mesh"></a>Visuelle Überprüfung des Meshes des Erkennungsmodells

Manchmal kann es hilfreich sein, das Mesh des Erkennungsmodells visuell zu inspizieren, um eventuelle Probleme mit der Ausrichtung, dem Maßstab oder den Features zu erkennen. Folgen Sie den Anweisungen [hier](../visualize-converted-model.md), um ein konvertiertes Modell in Unity zu visualisieren.

### <a name="adjust-object-query-values"></a>Anpassen der Objektabfragewerte

* Geben Sie genaue Suchbereiche an, um das vollständige Objekt präzise zu erfassen und die Geschwindigkeit und Genauigkeit bei der Erkennung zu erhöhen.
* Der Standardwert `ObjectQuery.MinSurfaceCoverage` ist oft ausreichend, aber Sie können auch einen kleineren Wert verwenden, um eine schnellere Erkennung zu erreichen.
* Verwenden Sie einen kleinen Wert für `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees`, wenn das Objekt voraussichtlich nach oben-rechts zeigt.
* Eine App sollte immer ein `1:1`-Objektmodell für die Erkennung verwenden. Die Skalierung sollte in der Regel nahe 1 liegen und im Idealfall eine maximale Abweichung von einem Prozent aufweisen. Eine App kann `ObjectQuery.MaxScaleChange` auf `0` oder `0.1` festlegen, um die Skalierungsschätzung zu aktivieren oder zu deaktivieren und die räumliche Instanzlage qualitativ zu bewerten.
* Weitere Informationen finden Sie unter [Erkennen eines schwierigen Objekts](../detect-difficult-object.md).

### <a name="capture-diagnostics"></a>Capture-Diagnose

Die Anwendung kann Diagnosearchive mit dem Objekt [ObjectDiagnosticsSession](../concepts/sdk-overview.md#objectdiagnosticssession) erfassen und speichern.

Die [Unity-Beispielanwendung mit MRTK](../quickstarts/get-started-unity-hololens-mrtk.md) schreibt Diagnosen in den Ordner **TempState**. Sie können eine Diagnosesitzung starten, indem Sie das <a href="/windows/mixed-reality/mrtk-unity/features/ux-building-blocks/hand-menu" target="_blank">Handmenü</a> öffnen, **Start Tracing** wählen, einen Erkennungsversuch reproduzieren und dann **Stop Tracing** wählen, um das Diagnosearchiv zu speichern. Sie können dann [Windows Geräteportal](/windows/mixed-reality/develop/platform-capabilities-and-apis/using-the-windows-device-portal) verwenden, um das Diagnosearchiv aus dem Ordner **TempState** der App abzurufen.

Das Diagnosearchiv kann dann mit uns geteilt werden, damit wir bei der Fehlersuche helfen können.

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung zur Fehlerbehebung haben Sie gelernt, wie Sie die Erkennung eines physischen Objekts mit Azure Object Anchors beheben können.
Hier sind einige verwandte Artikel:

> [!div class="nextstepaction"]
> [Erkennen eines schwierigen Objekts](../detect-difficult-object.md)

> [!div class="nextstepaction"]
> [Visualisieren eines Object Anchors-Modells](../visualize-converted-model.md)
