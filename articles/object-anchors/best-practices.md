---
title: Empfehlungen
description: Empfohlene bewährte Methoden zum Erzielen besserer Ergebnisse
author: ariye
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: 119837282409719a194341467a5d8164a46431b9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777581"
---
# <a name="best-practices"></a>Bewährte Methoden

Wir empfehlen Ihnen, einige dieser Schritte auszuprobieren, um die besten Ergebnisse zu erzielen.

## <a name="conversion"></a>Konvertierung

- Überprüfen Sie die Dimensionen Ihrer physischen Objekte. Azure Object Anchors funktionieren am besten für Objekte, deren kleinste Dimension im Bereich der empfohlenen 1-10 m liegt.
- Untersuchen Sie Ihr 3D-Modell in Software wie [**MeshLab**](https://www.meshlab.net/) auf die folgenden Details.
  - Stellen Sie sicher, dass das 3D-Modell über ein Dreiecksgittermodell verfügt, und das die Dreiecke auf der äußeren Oberfläche nach außen ausgerichtet sind. Das heißt, dass die Scheitelpunkte (Vertices) so ausgerichtet sein sollten, dass die Lotrechten (Normalen) in ihrer Ausrichtung nach außen der Rechte-Hand-Regel folgen.
  - Stellen Sie sicher, dass das 3D-Modell mit den richtigen Skalierungseinheiten in Bezug auf die physischen Objekte angegeben wird. Die Einheiten sollten sich aus den folgenden rekrutieren: ***Zentimeter, Dezimeter, Fuß, Zoll, Kilometer, Meter, Millimeter, Yard***.
  - Bestätigen Sie die nominale Schwerkraftrichtung, die der vertikalen Ausrichtung des Objekts in der realen Welt entspricht. Wenn die nach unten gerichtete Vertikale/Schwerkraft des Objekts -Y ist, verwenden Sie * **(0, -1, 0)** _ oder _*_ (0, 0, -1)_** für -Z und ebenso für jede andere Richtung.
  - Stellen Sie sicher, dass das 3D-Modell in einem der unterstützten Formate codiert ist: `.glb`, `.gltf`, `.ply`, `.fbx`, `.obj`.
- Die Verarbeitung eines Modells mit hoher Detailgenauigkeit (LOD, Level-of-Detail) durch unseren Modellkonvertierungsdienst kann viel Zeit in Anspruch nehmen. Zur Effizienzsteigerung können Sie Ihr 3D-Modell vorverarbeiten, um die Innenflächen zu entfernen.

## <a name="detection"></a>Erkennung

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- Das bereitgestellte Runtime-SDK benötigt einen benutzerseitig bereitgestellten Suchbereich, um nach den physischen Objekten zu suchen und diese zu erkennen. Der Suchbereich kann ein Begrenzungsrahmen, eine Kugel, ein Pyramidenstumpf (Frustum) oder eine beliebige Kombination daraus sein. Um eine falsche Erkennung zu vermeiden, legen Sie einen Suchbereich fest, der groß genug ist, um das Objekt abzudecken. Wenn Sie die bereitgestellten Beispiel-Apps verwenden, starten Sie die App mit einem Abstand von ca. zwei Metern von der nächstgelegenen Oberfläche.
- Bevor Sie die Azure Object Anchors-App auf einem HoloLens 2-Gerät starten, entfernen Sie alle Hologramme mithilfe der App **Einstellungen**.
  Navigieren Sie zu **System** -> **Holograms** („System“ > „Hologramme“), und klicken Sie dann auf **Remove all holograms** (Alle Hologramme entfernen), um mit einer neuen Karte zu beginnen.

  Durch das Löschen der Hologramme wird sichergestellt, dass Objekte an ihren aktuellen Positionen ordnungsgemäß erkannt werden können, falls sie vor Kurzem verschoben wurden.
- Scannen Sie das Objekt nach dem Entfernen der Hologramme und vor dem Starten der App, indem Sie mit der HoloLens in einem Abstand von ein bis zwei Metern 5-mal um das Objekt laufen.

  Das Vorscannen eines Objekts und einer Umgebung kann bei der Bereinigung von noch vorhandenen Oberflächen helfen, die basierend auf früheren Objekten und Scans erstellt wurden.
  Andernfalls sieht die App möglicherweise Geisteroberflächen, die zu einer ungenauen Ausrichtung Ihres 3D-Modells und der zugeordneten Hologramme führen. Durch das Vorscannen des Objekts kann auch die Azure Object Anchors-Erkennungswartezeit erheblich reduziert (z. B. von 30 auf 5 Sekunden).
- Bei dunklen und stark reflektierenden Objekten müssen Sie das Objekt mit weniger Abstand sowie aus mehreren Winkeln und verschiedenen Entfernungen scannen.
- Das bereitgestellte Runtime-SDK bietet ein paar Parameter, die Benutzern eine Feinabstimmung der Erkennung erlauben, wie in unseren Beispiel-Apps veranschaulicht. Die Standardparameter funktionieren für die meisten Objekte gut. Wenn Sie feststellen, dass Sie sie für bestimmte Objekte anpassen müssen, finden Sie hier einige Empfehlungen:
  - Verwenden Sie einen niedrigeren Schwellenwert für die Oberflächenabdeckung, wenn das physische Objekt groß, dunkel oder glänzend ist.
  - Lassen Sie eine kleine Skalierungsänderung (z. B. 0,1) für ein großes Objekt wie ein Auto zu.
  - Lassen Sie eine gewisse graduelle Abweichung zwischen der lokalen vertikalen Richtung des Objekts und der Schwerkraft zu, wenn sich das Objekt auf einer Schrägen befindet.

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie einige bewährte Methoden zum Erzielen der besten Ergebnisse bei der Verwendung von Azure Object Anchors zum Erkennen eines Objekts kennengelernt. Im Folgenden finden Sie einige verwandte Artikel:

> [!div class="nextstepaction"]
> [Problembehandlung bei der Objekterkennung](./troubleshoot/object-detection.md)
