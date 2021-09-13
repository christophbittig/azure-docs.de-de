---
title: Leitfaden für Zeichnungspakete in Microsoft Azure Maps Creator (Preview)
description: Hier erfahren Sie, wie Sie ein Zeichnungspaket für den Azure Maps-Konvertierungsdienst erstellen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: eb74ffb55536b18e48a4b5dcea83fac4db20abe0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346056"
---
# <a name="conversion-drawing-package-guide"></a>Leitfaden zum Konvertieren von Zeichnungspaketen

In diesem Leitfaden erfahren Sie, wie Sie Ihr Zeichnungspaket für den [Azure Maps-Konvertierungsdienst](/rest/api/maps/v2/conversion) mit bestimmten CAD-Befehlen erstellen, um Ihre DWG-Dateien und die Manifestdatei für den Konvertierungsdienst ordnungsgemäß zu erstellen.

Vergewissern Sie sich zunächst, dass das Zeichnungspaket im .zip Format vorliegt und die folgenden Dateien enthält:

* Mindestens eine Zeichnungsdatei im DWG-Format
* Eine Manifestdatei, die DWG-Dateien und Gebäudemetadaten beschreibt

Wenn Sie nicht über ein eigenes Paket verfügen, das Sie zusammen mit diesem Leitfaden verwenden können, können Sie das [Beispielzeichnungspaket](https://github.com/Azure-Samples/am-creator-indoor-data-examples) herunterladen.

Sie können eine beliebige CAD-Software auswählen, um Zeichnungsdateien für Gebäude zu öffnen und zu erstellen. Die Erstellung gemäß diesem Leitfaden erfolgt jedoch mit der AutoCAD®-Software von Autodesk. Alle Befehle, auf die in diesem Leitfaden verwiesen wird, sind für die Ausführung mit der AutoCAD®-Software von Autodesk vorgesehen.  

>[!TIP]
>Weitere Informationen zu den Anforderungen für Zeichnungspakete, die in diesem Leitfaden nicht behandelt werden, finden Sie unter [Anforderungen für Zeichnungspakete](drawing-requirements.md).

## <a name="glossary-of-terms"></a>Glossarbegriffe

Nachfolgend finden Sie einige relevante Begriffe und Definitionen, die für die Inhalte dieses Leitfadens wichtig sind.

| Begriff  | Definition |
|:-------|:------------|
| Ebene | Eine AutoCAD-DWG-Ebene aus der Zeichnungsdatei|
| Entität | Eine AutoCAD-DWG-Entität aus der Zeichnungsdatei |
| Ebene  |Ein Gebäudebereich in einer bestimmten Höhe. Hierbei kann es sich beispielsweise um ein Stockwerk handeln.    |
| Funktion | Ein Objekt, das eine Geometrie mit weiteren Metadateninformationen kombiniert. |
| Featureklassen | Eine allgemeine Blaupause für Features. So ist beispielsweise eine *Einheit* eine Featureklasse, während es sich bei einem *Büro* um ein Feature handelt. |

## <a name="step-1-dwg-file-requirements"></a>Schritt 1: Anforderungen für DWG-Dateien

Achten Sie beim Erstellen von Gebäudezeichnungsdateien für den Konvertierungsdienst darauf, dass Sie die folgenden vorläufigen Anforderungen und Empfehlungen einhalten:

* Gebäudezeichnungsdateien müssen im DWG-Format gespeichert werden. Hierbei handelt es sich um das native Dateiformat der AutoCAD®-Software von Autodesk.

* Der Konvertierungsdienst kann mit dem DWG-Dateiformat von AutoCAD verwendet werden.AC1032 ist die interne Formatversion für die DWG-Dateien. Es empfiehlt sich, als interne DWG-Dateiformatversion AC1032 auszuwählen.

* Eine DWG-Datei kann nur eine einzelne Etage enthalten. Eine Etage eines Gebäudes muss in einer eigenen separaten DWG-Datei bereitgestellt werden.  Wenn Ihr Gebäude also fünf Etagen aufweist, müssen Sie fünf separate DWG-Dateien erstellen.

## <a name="step-2-prepare-the-dwg-files"></a>Schritt 2: Erstellen der DWG-Dateien

In diesem Teil des Leitfadens erfahren Sie, wie Sie CAD-Befehle verwenden, um sicherzustellen, dass Ihre DWG-Dateien die Anforderungen des Konvertierungsdiensts erfüllen.

Sie können eine beliebige CAD-Software auswählen, um Zeichnungsdateien für Gebäude zu öffnen und zu erstellen. Die Erstellung gemäß diesem Leitfaden erfolgt jedoch mit der AutoCAD®-Software von Autodesk. Alle Befehle, auf die in diesem Leitfaden verwiesen wird, sind für die Ausführung mit der AutoCAD®-Software von Autodesk vorgesehen.  

### <a name="bind-external-references"></a>Verknüpfen externer Verweise

Jede Etage eines Gebäudes muss in Form einer einzelnen DWG-Datei bereitgestellt werden. Wenn keine externen Verweise vorhanden sind, muss kein weiterer Schritt ausgeführt werden. Wenn jedoch externe Verweise vorhanden sind, müssen diese mit einer einzelnen Zeichnung verknüpft werden. Sie können den Befehl `XREF` verwenden, um einen externen Verweis zu verknüpfen. Nach der Verknüpfung wird jede externe Verweiszeichnung als Blockverweis hinzugefügt. Wenn Sie Änderungen an einer dieser Ebenen vornehmen müssen, denken Sie daran, die Blockverweise mit dem Befehl `XPLODE` aufzulösen.

### <a name="unit-of-measurement"></a>Unit of measure (Maßeinheit)

Die Zeichnungen können mit einer beliebigen Maßeinheit erstellt werden. Allerdings muss für alle Zeichnungen dieselbe Maßeinheit verwendet werden. Wenn also für eine Etage des Gebäudes Millimeter verwendet werden, müssen die Angaben für alle anderen Etagen (Zeichnungen) ebenfalls in Millimetern erfolgen. Sie können die Maßeinheit mit dem Befehl `UNITS` überprüfen oder ändern.

Die folgende Abbildung zeigt das Fenster „Zeichnungseinheiten“ in der AutoCAD®-Software von Autodesk, mit der Sie die Maßeinheit überprüfen können.  

:::image border="true" type="content" source="./media/drawing-package-guide/units.png" alt-text="Fenster „Zeichnungseinheiten“ in der AutoCAD®-Software von Autodesk":::

### <a name="alignment"></a>Ausrichtung

Jede Etage eines Gebäudes wird als einzelne DWG-Datei bereitgestellt. Daher ist es möglich, dass die Etagen nicht perfekt ausgerichtet sind, wenn sie übereinander gestapelt werden. Der Azure Maps-Konvertierungsdienst erfordert, dass alle Zeichnungen am physischen Raum ausgerichtet sind. Zum Überprüfen der Ausrichtung verwenden Sie einen Referenzpunkt, der sich über Etagen erstrecken kann, z. B. einen Aufzug oder eine Säule, der/die sich über mehrere Etagen erstreckt. Sie können alle Etagen anzeigen, indem Sie eine neue Zeichnung öffnen und dann den Befehl `XATTACH` verwenden, um alle Etagenzeichnungen zu laden. Wenn Sie Ausrichtungsprobleme beheben müssen, können Sie die Referenzpunkte und den Befehl `MOVE` verwenden, um die Etagen neu auszurichten, die neu ausgerichtet werden müssen.

### <a name="layers"></a>Ebenen

Vergewissern Sie sich, dass jede Zeichnungsebene Entitäten einer Featureklasse enthält. Wenn eine Ebene Entitäten für Wände enthält, kann sie keine anderen Features wie Einheiten oder Türen aufweisen.  Eine Featureklasse kann jedoch auf mehrere Ebenen aufgeteilt werden. In der Zeichnung können z. B. drei Ebenen vorhanden sein, die Wandentitäten enthalten.

Darüber hinaus verfügt jede Ebene über eine Liste der unterstützten Entitätstypen; alle anderen Typen werden ignoriert. Wenn die Ebene für die Beschriftung der Einheit beispielsweise nur einzeiligen Text unterstützt, wird ein mehrzeiliger Text oder eine Polylinie auf derselben Ebene ignoriert.

Ausführlichere Informationen zu Ebenen und Featureklassen finden Sie unter [Anforderungen für Zeichnungspakete](drawing-requirements.md).

### <a name="exterior-layer"></a>Exterior-Schicht

Aus jeder äußeren Ebene (oder aus mehreren Ebenen) wird ein Feature einer einzigen Ebene erstellt. Dieses Ebenenfeature definiert den Umfang der Ebene. Es ist wichtig sicherzustellen, dass die Entitäten in der äußeren Ebene die Anforderungen der Ebene erfüllen. Beispielsweise wird eine geschlossene Polylinie unterstützt, eine offene Polylinie jedoch nicht. Wenn die äußere Ebene aus mehreren Liniensegmenten besteht, müssen diese als geschlossene Polylinie bereitgestellt werden. Wenn Sie mehrere Liniensegmente miteinander verbinden möchten, wählen Sie alle Liniensegmente aus, und verwenden Sie den Befehl `JOIN`.

Die folgende Abbildung stammt aus dem Beispielpaket und zeigt die äußere Ebene des Gebäudes in Rot. Die Einheitenebene ist zur Erleichterung der Visualisierung deaktiviert.

:::image border="true" type="content" source="./media/drawing-package-guide/exterior.png" alt-text="Äußere Ebene eines Gebäudes":::

### <a name="unit-layer"></a>Unit-Schicht

Bei Einheiten handelt es sich um betretbare Gebäudebereiche wie Büros, Flure, Treppenhäuser und Aufzüge.Zum Darstellen der einzelnen Einheiten ist der Typ einer geschlossenen Entität wie Polygon, geschlossene Polylinie, Kreis oder geschlossene Ellipse erforderlich. Daher ergeben Wände und Türen allein keine Einheit, da es keine Entität gibt, die die Einheit darstellt.  

Die folgende Abbildung stammt aus dem [Beispielzeichnungspaket](https://github.com/Azure-Samples/am-creator-indoor-data-examples) und zeigt die Einheitenbeschriftungsebene und die Einheitenebene in Rot. Alle anderen Ebenen sind zur Erleichterung der Visualisierung deaktiviert. Außerdem ist eine Einheit ausgewählt, damit veranschaulicht werden kann, dass jede Einheit aus einer geschlossenen Polylinie besteht.  

:::image border="true" type="content" source="./media/drawing-package-guide/unit.png" alt-text="Einheitenebene eines Gebäudes":::

### <a name="unit-label-layer"></a>Einheitenbeschriftungsebene

Wenn Sie einer Einheit eine Namenseigenschaft hinzufügen möchten, müssen Sie eine separate Ebene für Einheitenbeschriftungen hinzufügen. Beschriftungen müssen als einzeilige Textentitäten bereitgestellt werden, die innerhalb der Grenzen einer Einheit liegen. Der Manifestdatei muss eine entsprechende Einheiteneigenschaft hinzugefügt werden, in der `unitName` mit dem Inhalt des Texts übereinstimmt.  Informationen zu allen unterstützten Einheiteneigenschaften finden Sie unter [`unitProperties`](#unitproperties).

### <a name="door-layer"></a>Door-Schicht

Türen sind optional. Türen können jedoch verwendet werden, wenn Sie die Zugangspunkte für eine Einheit darstellen möchten. Türen können auf beliebige Weise gezeichnet werden, wenn es sich um einen von der Türebene unterstützten Entitätstyp handelt. Die Tür muss die Begrenzung einer Einheit überlappen, und der überlappende Rand der Einheit wird dann als Öffnung in der Einheit behandelt.  

Die folgende Abbildung stammt aus dem [Beispielzeichnungspaket](https://github.com/Azure-Samples/am-creator-indoor-data-examples) und zeigt eine Einheit mit einer Tür (in Rot), die über die Begrenzung der Einheit gezeichnet wurde.

:::image border="true" type="content" source="./media/drawing-package-guide/door.png" alt-text="Türebene eines Gebäudes":::

### <a name="wall-layer"></a>Wall-Schicht

Die Wandebene soll die physischen Ausdehnungen eines Gebäudes darstellen, z. B. in Form von Wänden und Säulen. Vom Azure Maps-Konvertierungsdienst werden Wände als physische Baukonstruktionen erkannt, die ein Hindernis für den Verlauf von Wegen darstellen. Vor diesem Hintergrund sollte eine Wand als physische Baukonstruktion interpretiert werden, die sichtbar ist, aber durch die man nicht hindurchgehen kann. Alles, was nicht sichtbar ist, wird auf dieser Ebene nicht erfasst. Wenn eine Wand innere Wände oder Säulen aufweist, sollte nur die äußere Schicht erfasst werden.  

## <a name="step-3-prepare-the-manifest"></a>Schritt 3: Erstellen des Manifests

Das Zeichnungspaketmanifest ist eine JSON-Datei. Das Manifest liefert dem Azure Maps-Konvertierungsdienst Informationen darüber, wie die DWG-Dateien und Metadaten des Gebäudes gelesen werden sollen. Beispiele für diese Informationen sind die spezifischen Informationen, die jede DWG-Ebene enthält, oder der geografische Standort des Gebäudes.

Zum Erzielen einer erfolgreichen Konvertierung müssen alle „erforderlichen“ Eigenschaften festgelegt werden. Im [Beispielzeichnungspaket](https://github.com/Azure-Samples/am-creator-indoor-data-examples) finden Sie eine exemplarische Manifestdatei. In diesem Leitfaden werden Eigenschaften, die vom Manifest unterstützt werden, nicht behandelt. Weitere Informationen zu Manifesteigenschaften finden Sie in den [Manifestdateieigenschaften](drawing-requirements.md#manifest-file-requirements).

### <a name="building-levels"></a>Gebäudeebenen

Die Gebäudeebene gibt an, welche DWG-Datei für welche Ebene verwendet werden soll. Eine Ebene muss über einen Ebenennamen und eine Ordnungszahl verfügen, die die vertikale Reihenfolge der einzelnen Ebenen bezeichnet. Jedes Gebäude muss über eine Ordnungszahl 0 verfügen. Dies ist das Erdgeschoss eines Gebäudes. Eine Ordnungszahl 0 muss auch dann bereitgestellt werden, wenn die Zeichnungen nur einige Stockwerke eines Gebäudes belegen. Beispielsweise können Etagen 15–17 als Ordnungszahlen 0–2 definiert werden.

Das folgende Beispiel stammt aus dem [Beispielzeichnungspaket](https://github.com/Azure-Samples/am-creator-indoor-data-examples). Das Gebäude verfügt über drei Ebenen: Keller, Erdgeschoss und Ebene 2. Der Dateiname enthält den vollständigen Dateinamen und Pfad der Datei relativ zur Manifestdatei im .zip-Zeichnungspaket.  

```json
    "buildingLevels": { 
      "levels": [ 
       { 
           "levelName": "Basement", 
           "ordinal": -1, 
           "filename": "./Basement.dwg" 
            }, { 

            "levelName": "Ground", 
            "ordinal": 0, 
            "filename": "./Ground.dwg" 
            }, { 

            "levelName": "Level 2", 
            "ordinal": 1, 
             "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
```

### <a name="georeference"></a>georeference

Das `georeference`-Objekt wird verwendet, um anzugeben, wo sich das Gebäude geografisch befindet und in welchem Umfang das Gebäude gedreht werden soll. Der Ursprungspunkt der Zeichnung sollte mit dem Breiten- und Längengrad übereinstimmen, die mit dem `georeference`-Objekt bereitgestellt werden. Der Winkel im Uhrzeigersinn zwischen geografisch Nord und der vertikalen Achse (Y-Achse) der Zeichnung, angegeben in Grad.  

### <a name="dwglayers"></a>dwgLayers

Das `dwgLayers`-Objekt wird zum Angeben der Namen von DWG-Ebenen verwendet, auf denen Featureklassen vorhanden sind. Damit Sie ein anhand von Eigenschaften konvertiertes Gebäude erhalten, ist es wichtig, die richtigen Ebenennamen bereitzustellen. Beispielsweise muss eine DWG-Wandebene als Wandebene und nicht als Einheitenebene bereitgestellt werden. Die Zeichnung kann auch andere Ebenen aufweisen, z. B. Möbel oder Rohrleitungen; diese werden jedoch vom Azure Maps-Konvertierungsdienst ignoriert, wenn sie im Manifest nicht angegeben sind.  

Das folgende Beispiel zeigt das `dwgLayers`-Objekt im Manifest.  

```json
"dwgLayers": { 
        "exterior": [ 
            "OUTLINE" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    } 
```

Die folgende Abbildung zeigt die Ebenen der entsprechenden DWG-Zeichnung, die in der AutoCAD®-Software von Autodesk angezeigt wird.

:::image border="true" type="content" source="./media/drawing-package-guide/layer.png" alt-text="DwgLayers in der AutoCAD®-Software von Autodesk":::

### <a name="unitproperties"></a>unitProperties

Mit dem `unitProperties`-Objekt können Sie andere Eigenschaften für eine Einheit festlegen, die in der DWG-Datei nicht festgelegt werden können. Beispiele hierfür sind Verzeichnisinformationen einer Einheit oder der Kategorietyp einer Einheit. Eine Einheiteneigenschaft wird einer Einheit zugeordnet, wenn das `unitName`-Objekt mit der Beschriftung auf der Ebene `unitLabel` übereinstimmt.  

Die folgende Abbildung stammt aus dem [Beispielzeichnungspaket](https://github.com/Azure-Samples/am-creator-indoor-data-examples). Sie zeigt die Einheitenbeschriftung an, die der Einheiteneigenschaft im Manifest zugeordnet ist.

:::image border="true" type="content" source="./media/drawing-package-guide/unit-property.png" alt-text="Einheitenbeschriftung, die der Einheiteneigenschaft im Manifest zugeordnet wird":::

Der folgende Codeausschnitt zeigt das Einheiteneigenschaftsobjekt, das der Einheit zugeordnet ist.  

```json
 "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
```

## <a name="step-4-prepare-the-drawing-package"></a>Schritt 4: Erstellen des Zeichnungspakets

Sie sollten nun alle DWG-Zeichnungen so erstellt haben, dass die Anforderungen des Azure Maps-Konvertierungsdiensts erfüllt werden. Eine Manifestdatei wurde auch erstellt, die dazu beiträgt, das Gebäude zu beschreiben. Alle Dateien müssen in eine einzelne Archivdatei mit der Erweiterung `.zip` gezippt werden. Es ist wichtig, dass die Manifestdatei mit `manifest.json` benannt und im Stammverzeichnis des ZIP-Pakets platziert wird. Alle anderen Dateien können sich in einem beliebigen Verzeichnis des ZIP-Pakets befinden, wenn der Dateiname den relativen Pfad zum Manifest enthält. Ein Beispiel für ein Zeichnungspaket finden Sie im [Beispielzeichnungspaket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Erstellen von Gebäudeplänen mithilfe von Creator](tutorial-creator-indoor-maps.md)
