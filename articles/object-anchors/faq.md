---
title: Häufig gestellte Fragen
description: Dies sind häufig gestellte Fragen zum Azure Object Anchors-Dienst.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 18069157b4c7f38216c01649a33ed5f999dc7577
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638385"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Häufig gestellte Fragen zu Azure Object Anchors

Azure Object Anchors ermöglicht einer Anwendung mithilfe eines 3D-Modells das Erkennen eines Objekts in der physischen Welt und das Schätzen der räumlichen Lage nach den sechs Freiheitsgraden.

Weitere Informationen finden Sie unter [Azure Object Anchors-Übersicht](overview.md).

## <a name="product-faq"></a>Häufig gestellte Fragen zum Produkt
**F: Welche Empfehlungen gibt es für die zu verwendenden Objekte?**

**A:** Für Objekte werden die folgenden Merkmale empfohlen:

* Ein bis zehn Meter für jede Dimension
* Nicht symmetrisch und mit ausreichend geometrischen Variationen
* Niedrige Reflektivität (matte Oberflächen) mit heller Farbe
* Stationäre Objekte
* Keine oder kaum Artikulation
* Klare Hintergründe ohne bzw. mit nur wenig anderen herumliegenden Objekten
* Gescanntes Objekt sollte 1:1 dem Modell entsprechen, mit dem Sie trainiert haben

**F: Wie lauten die maximalen Objektabmessungen, die bei der Modellkonvertierung verarbeitet werden können?**

**A:** Alle Maße eines CAD-Modells sollten weniger als zehn Meter betragen. Weitere Informationen finden Sie im Abschnitt [ Ressourcenanforderungen](overview.md).

**F: Wie lautet die maximale CAD-Modellgröße, die bei der Konvertierung verarbeitet werden kann?**

**A:** Die Modelldateigröße sollte kleiner als 150 MB sein. Weitere Informationen finden Sie im Abschnitt [ Ressourcenanforderungen](overview.md).

**F: Welche CAD-Formate werden unterstützt?**

**A:** Zurzeit werden die Dateitypen `fbx`, `ply`, `obj`, `glb` und `gltf` unterstützt. Weitere Informationen finden Sie im Abschnitt [ Ressourcenanforderungen](overview.md).

**Q:Welche Schwerkraftrichtung und -einheit erfordert der Dienst für die Modellkonvertierung?**

**A:** Die Schwerkraftrichtung ist der Abwärtsvektor, der auf die Erde zeigt, und die Maßeinheit entspricht dem Maßstab des Modells. Bei der Konvertierung eines Modells ist es wichtig, [sicher zu stellen, dass die Richtung der Schwerkraft und die Maßeinheit der Anlage korrekt sind](./troubleshoot/object-detection.md#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct).

**F: Wie lange dauert es, ein CAD-Modell zu konvertieren?**

**A:** Für ein `ply`-Modell werden in der Regel 3 bis 15 Minuten benötigt. Wenn Sie Modelle in anderen Formaten übermitteln, müssen Sie je nach Dateigröße mit 15 bis 60 Minuten rechnen.

**F: Wie führe ich nach einem Modellkonvertierungsfehler eine Wiederherstellung aus?**

**A:** Ausführliche Informationen zu den verschiedenen Fehlercodes, die bei einem fehlerhaften Modellkonvertierungsauftrag angezeigt werden können, und deren Handhabung finden Sie auf der Seite mit den [Konvertierungsfehlercodes](.\model-conversion-error-codes.md).

**F: Welche Geräte werden von Object Anchors unterstützt?**

**A:** HoloLens 2.

**F: Welcher Betriebssystembuild sollte mit HoloLens ausgeführt werden?**

**A:** Betriebssystembuild 18363.720 oder höher, Veröffentlichung nach dem 12. März 2020.

  Weitere Informationen finden Sie unter [12. März 2020 – KB4551762 (Betriebssystembuilds 18362.720 und 18363.720)](https://support.microsoft.com/help/4551762).

**F: Wie lange dauert es, ein Objekt auf einem HoloLens-Gerät zu erkennen?**

**A:** Dies hängt von der Objektgröße und dem Scanvorgang ab. Versuchen Sie, die bewährten Methoden für einen gründlichen Scanvorgang zu befolgen, um Objekte schneller zu erkennen.
Die Erkennung kann bei kleineren Objekten, deren Seitenmaße jeweils zwei Meter nicht überschreiten, innerhalb von wenigen Sekunden erfolgen. Bei größeren Objekten wie Autos sollte der Benutzer komplett um diese Objekte herumgehen, um eine zuverlässige Erkennung sicherzustellen. Dies bedeutet, dass für die Erkennung mehrere Sekunden benötigt werden.

**F: Welche bewährte Methoden gibt es für die Verwendung von Object Anchors in einer HoloLens-Anwendung?**

**A:**

 1. Führen Sie eine Augenkalibrierung aus, um ein genaues Rendering zu ermöglichen.
 2. Stellen Sie sicher, dass der Raum eine ideale visuelle Struktur und gute Lichtverhältnisse aufweist.
 3. Verwenden Sie ein feststehendes Objekt, um das keine anderen Objekte herumliegen.
 4. Optional können Sie den Cache [Spatial Mapping](/windows/mixed-reality/spatial-mapping) (Räumliche Zuordnung) auf dem HoloLens-Gerät löschen.
 5. Scannen Sie das Objekt, indem Sie um es herumgehen. Stellen Sie sicher, dass der größte Teil des Objekts erfasst wird.
 6. Legen Sie einen ausreichend großen Suchbereich fest, um das Objekt abzudecken.
 7. Das Objekt sollte während der Erkennung nicht bewegt werden.
 8. Starten Sie die Objekterkennung, und visualisieren Sie das Rendering basierend auf der geschätzten räumlichen Lage.
 9. Sperren Sie erkannte Objekte, oder beenden Sie die Nachverfolgung, sobald die räumliche Lage stabil und genau ist, um die Batterie zu schonen.

**F: Welche Version des Mixed Reality-Toolkits (MRTK) sollte meine HoloLens Unity-Anwendung verwenden, um mit dem Object Anchors-Unity-SDK arbeiten zu können?**

**A:** Das Azure Object Anchors-Unity-SDK ist in keiner Weise vom Mixed Reality-Toolkit abhängig. Das bedeutet, dass Sie eine beliebige Version verwenden können. Weitere Informationen finden Sie in der [Einführung in MRTK für Unity](/windows/mixed-reality/develop/unity/mrtk-getting-started).

**F: Wie präzise ist die geschätzte räumliche Lage?**

**A:** Das hängt von der Objektgröße, dem Material, der Umgebung usw. ab. Bei kleinen Objekten kann die geschätzte räumliche Lage innerhalb eines Fehlers von 2 cm liegen. Bei großen Objekten wie Autos können Abweichungen von zwei bis acht Zentimetern vorliegen.

**F: Kann Object Anchors sich bewegende Objekte verarbeiten?**

**A:** Das Verarbeiten von **sich ständig bewegenden** oder **dynamischen** Objekten wird nicht unterstützt. Wir unterstützen Objekte an einer völlig neuen Position im Raum, nachdem sie physisch bewegt wurden, können sie jedoch während der Bewegung nicht nachverfolgen.

**F: Kann Object Anchors Verformungen oder Artikulationen verarbeiten?**

**A:** Dies ist teilweise möglich und hängt davon ab, inwieweit sich der Umriss bzw. die Geometrie des Objekts aufgrund der Verformung oder Artikulation verändert. Wenn sich die Geometrie des Objekts stark ändert, kann der Benutzer ein weiteres Modell für diese Konfiguration erstellen und zur Erkennung verwenden.

**Q: Wie viele verschiedene Modelle kann Object Anchors gleichzeitig erkennen?**

**A:** Wir unterstützen derzeit die gleichzeitige Erkennung von drei Modellen, um die bestmögliche Benutzerfreundlichkeit zu gewährleisten, aber wir setzen keine Begrenzung durch.

**F: Kann Object Anchors mehrere Instanzen desselben Objektmodells erkennen?**

**A:** Ja, wir unterstützen die Erkennung von bis zu drei Instanzen desselben Modelltyps, um eine optimale Benutzerfreundlichkeit zu gewährleisten, aber wir setzen keine Begrenzung durch. Sie können eine Objektinstanz pro Suchbereich erkennen. Durch den Aufruf von `ObjectQuery.SearchAreas.Add` können Sie weitere Suchbereiche zu einer Abfrage hinzufügen, um mehr Instanzen zu erkennen. Sie können `ObjectObserver.DetectAsync` mit mehreren Abfragen aufrufen, um mehrere Modelle zu erkennen.

**F: Was muss ich tun, wenn die Object Anchors-Runtime das Objekt nicht erkennen kann?**

**A:**  Es gibt viele Faktoren, die verhindern können, dass ein Objekt richtig erkannt wird: Umgebung, Modellkonvertierungskonfiguration, Abfrageeinstellungen usw. Lernen Sie mehr über die [Problembehandlung bei der Objekterkennung](./troubleshoot/object-detection.md).

**F: Wie wähle ich Objektabfrageparameter aus?**

**A:** Hier finden Sie einige [allgemeine Hinweise](./troubleshoot/object-detection.md#adjust-object-query-values) und eine detailliertere Anleitung für [schwierig zu erkennende Objekte](./detect-difficult-object.md).

**F: Wie rufe ich Object Anchors-Diagnosedaten vom HoloLens-Gerät ab?**

**A:** Die Anwendung kann den Speicherort der Diagnosedatenarchive ermitteln. Die Object Anchors-Beispiel-App schreibt Diagnosedaten in den Ordner **TempState**.

**F: Warum wird das Quellmodell nicht am physischen Objekt ausgerichtet, wenn die vom Object Anchors-Unity-SDK zurückgegebene räumliche Lage verwendet wird?**

**A:** Unity ändert beim Importieren eines Objektmodells möglicherweise das Koordinatensystem. Beim Konvertieren invertiert das Object Anchors-Unity-SDK beispielsweise die Z-Achse von einem rechtshändigen in ein linkshändiges Koordinatensystem, aber Unity führt ggf. eine zusätzliche Drehung der X- oder Y-Achse aus. Ein Entwickler kann diese zusätzliche Drehung ermitteln, indem er die Koordinatensysteme visualisiert und vergleicht.

**F: Wird eine 2D-Darstellung unterstützt?**

**A:** Da die Modelle geometriebasiert sind, wird nur die 3D-Darstellung unterstützt.

**F: Kann zwischen demselben Modell in unterschiedlichen Farben differenziert werden?**

**A:** Da unsere Algorithmen geometriebasiert sind, verhalten sich unterschiedliche Farben des gleichen Modells während der Erkennung nicht unterschiedlich.

**F: Kann ich Object Anchors ohne Internetverbindung nutzen?**

**A:**
* Für die Modellkonvertierung und das Training wird eine Internetverbindung benötigt, da diese Vorgänge in der Cloud durchgeführt werden.
* Laufzeitsitzungen erfolgen vollständig auf dem Gerät und erfordern keine Internetverbindung, da alle Berechnungen über HoloLens 2 erfolgen.

## <a name="privacy-faq"></a>Datenschutz: Häufig gestellte Fragen
**F: Wie werden Daten von Azure Object Anchors gespeichert?**

**A:** Es werden nur Systemmetadaten gespeichert, die im Ruhezustand mit einem von Microsoft verwalteten Datenverschlüsselungsschlüssel verschlüsselt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einige Antworten auf häufige Fragen gelernt, um die besten Ergebnisse bei der Verwendung von Azure Object Anchors zu erzielen.
Hier sind einige verwandte Artikel:

> [!div class="nextstepaction"]
> [Empfohlene Methoden](./best-practices.md)

> [!div class="nextstepaction"]
> [Problembehandlung bei der Objekterkennung](./troubleshoot/object-detection.md)
