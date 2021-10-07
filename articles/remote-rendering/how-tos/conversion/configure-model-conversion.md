---
title: Konfigurieren der Modellkonvertierung
description: Enthält eine Beschreibung aller Parameter der Modellkonvertierung.
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 263531d24d50c27309163f0671a41ff7aacd36c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601366"
---
# <a name="configure-the-model-conversion"></a>Konfigurieren der Modellkonvertierung

In diesem Kapitel sind die Optionen für die Modellkonvertierung dokumentiert.

## <a name="settings-file"></a>Einstellungsdatei

Wenn im Eingangscontainer neben dem Eingangsmodell `<modelName>.<ext>` eine Datei mit dem Namen `<modelName>.ConversionSettings.json` gefunden wird, wird mit ihr eine weitere Konfiguration für den Prozess zur Modellkonvertierung angegeben.
Beispielsweise wird `box.ConversionSettings.json` ggf. bei der Konvertierung von `box.gltf` verwendet.

Für den Inhalt der Datei sollte das folgende JSON-Schema erfüllt sein:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

Ein Beispiel für die Datei `box.ConversionSettings.json` ist:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometrieparameter

* `scaling`: Mit diesem Parameter wird ein Modell einheitlich skaliert. Die Skalierung kann genutzt werden, um ein Modell zu vergrößern oder zu verkleinern, z. B. zum Anzeigen eines Gebäudemodells auf einer Tischplatte.
Die Skalierung ist auch dann wichtig, wenn ein Modell in anderen Einheiten als Meter definiert ist, da die Rendering-Engine Meter erwartet.
Wenn zum Definieren eines Modells beispielsweise Zentimeter verwendet wurden, sollte es in der richtigen Größe gerendert werden, wenn eine Skalierung mit dem Wert 0,01 angewendet wird.
Bei einigen Quelldatenformaten (z. B. FBX) wird auf die Einheitenskalierung hingewiesen. In diesem Fall wird das Modell bei der Konvertierung implizit auf „Meter“ als Einheit skaliert. Die implizite Skalierung durch das Quellformat wird zusätzlich zum Skalierungsparameter angewendet.
Der abschließende Skalierungsfaktor wird auf die Scheitelpunkte der Geometrie und die lokalen Transformationen der Szenengraphknoten angewendet. Die Skalierung für die Transformation der Stammentität bleibt unverändert.

* `recenterToOrigin`: Gibt an, dass ein Modell so konvertiert werden sollte, dass der Begrenzungsrahmen am Ursprung zentriert ist.
Wenn ein Quellmodell gegenüber dem Ursprung weit versetzt wurde, können Probleme mit der Gleitkommagenauigkeit zu Artefakten beim Rendering führen.
Ein Zentrieren des Modells kann in dieser Situation hilfreich sein.

* `opaqueMaterialDefaultSidedness`: Vom Renderingmodul wird vorausgesetzt, dass undurchsichtige Materialien doppelseitig sind.
Wenn diese Annahme für ein bestimmtes Modell nicht zutrifft, sollte dieser Parameter auf „SingleSided“ festgelegt werden. Weitere Informationen zu Rendering vom Typ :::no-loc text="single sided"::: finden Sie unter [Einseitiges Rendering](../../overview/features/single-sided-rendering.md).

### <a name="material-de-duplication"></a>Deduplizierung von Materialien

* `deduplicateMaterials`: Dieser Parameter aktiviert oder deaktiviert die automatische Deduplizierung von Materialien, die die gleichen Eigenschaften und Texturen aufweisen. Die Deduplizierung erfolgt nach der Verarbeitung von Materialüberschreibungen. Der Webhook ist standardmäßig aktiviert.

* Wenn ein Modell selbst nach der Deduplizierung noch mehr als 65.535 Materialien aufweist, versucht der Dienst, Materialien mit ähnlichen Eigenschaften zusammenzuführen. Als letztes Mittel werden alle Materialien, die den Grenzwert überschreiten, durch ein rotes Fehlermaterial ersetzt.

![Die Abbildung zeigt zwei Würfel aus 68.921 farbigen Dreiecken.](media/mat-dedup.png?raw=true)

Zwei Würfel aus 68.921 farbigen Dreiecken. Links: Vor der Deduplizierung mit 68.921 Farbmaterialien. Rechts: Nach der Deduplizierung mit 64.000 Farbmaterialien. Der Grenzwert beträgt 65.535 Material. (Siehe [Grenzwerte](../../reference/limits.md).)

### <a name="color-space-parameters"></a>Farbraumparameter

Vom Renderingmodul wird erwartet, dass sich die Farbwerte im linearen Raum befinden.
Wenn ein Modell mit dem Gamma-Raum definiert wird, sollten diese Optionen auf „true“ festgelegt werden.

* `gammaToLinearMaterial`: Dient zum Konvertieren der Materialfarben aus dem Gamma-Raum in den linearen Raum.
* `gammaToLinearVertex`: Dient zum Konvertieren der :::no-loc text="vertex":::-Farben aus dem Gamma-Raum in den linearen Raum.

> [!NOTE]
> Für FBX-Dateien sind diese Einstellungen standardmäßig auf `true` festgelegt. Für alle anderen Dateitypen wird `false` als Standardwert verwendet.

### <a name="scene-parameters"></a>Szenenparameter

* `sceneGraphMode`: Definiert, wie der Szenengraph in der Quelldatei konvertiert wird:
  * `dynamic` (Standard): Alle Objekte in der Datei werden als [Entitäten](../../concepts/entities.md) in der API verfügbar gemacht und können unabhängig voneinander transformiert und neuen übergeordneten Objekten zugewiesen werden. Die Knotenhierarchie zur Runtime ist mit der Struktur in der Quelldatei identisch.
  * `static`: Ähnlich wie `dynamic`, aber Objekte im Szenendiagramm können zur Laufzeit nicht anderen Objekten als neuen übergeordneten Objekten zugewiesen werden. Bei dynamischen Modellen mit vielen beweglichen Teilen (beispielsweise einer „Explosionsansicht“) generiert die Option `dynamic` ein Modell, das effizienter gerendert werden kann, der Modus `static` lässt jedoch weiterhin die Transformation einzelner Teile zu. Falls keine dynamische Zuordnung neuer übergeordneter Objekte erforderlich ist, ist die Option `static` am besten für Modelle mit vielen Einzelteilen geeignet.
  * `none`: Der Szenengraph wird zu einem Objekt reduziert.

Jeder Modus weist eine andere Runtimeleistung auf. Im Modus `dynamic` ist der Leistungsaufwand linear von der Anzahl von [Entitäten](../../concepts/entities.md) im Graphen abhängig. Dies gilt auch, wenn kein Teil verschoben wird. Verwenden Sie den Modus `dynamic` nur, wenn viele Teile oder große Unterdiagramme gleichzeitig bewegt werden müssen, beispielsweise bei einer animierten „Explosionsansicht“.

Der Modus `static` exportiert außerdem das vollständige Szenendiagramm. Bei [räumlichen Abfragen](../../overview/features/spatial-queries.md) werden einzelne Teile zurückgegeben, und jedes Teil kann mithilfe von [Zustandsüberschreibungen](../../overview/features/override-hierarchical-state.md) geändert werden. In diesem Modus ist der Runtime-Mehraufwand pro Objekt vernachlässigbar. Er eignet sich ideal für große Szenen, in denen Sie objektspezifische Überprüfungen, gelegentliche Transformationsänderungen an einzelnen Teilen, aber keine Neuzuordnung zu übergeordneten Objekten benötigen.

Im Modus `none` fällt der geringste Runtime-Mehraufwand an, und es werden etwas kürzere Ladedauern erreicht. Die Überprüfung oder Transformation einzelner Objekte ist in diesem Modus nicht möglich. Ein Beispiel für einen Anwendungsfall sind Fotogrammetrie-Modelle, die gar nicht über einen aussagekräftigen Szenengraphen verfügen.

> [!TIP]
> Bei vielen Anwendungen werden mehrere Modelle geladen. Sie sollten die Konvertierungsparameter für jedes Modell je nach Nutzung optimieren. Wenn Sie beispielsweise ein PKW-Modell anzeigen möchten, das vom Benutzer auseinandergenommen und im Detail untersucht werden kann, müssen Sie es im Modus `dynamic` konvertieren. Falls Sie den PKW zusätzlich in einem Ausstellungsraum platzieren möchten, können Sie das Modell mit der Einstellung `static` oder auch `none` für `sceneGraphMode` konvertieren.

### <a name="physics-parameters"></a>Physikparameter

* `generateCollisionMesh`: Falls Sie für ein Modell Unterstützung für [räumliche Abfragen](../../overview/features/spatial-queries.md) benötigen, muss diese Option aktiviert sein. Im schlimmsten Fall kann es bei der Erstellung eines Kollisionsgittermodells zu einer Verdoppelung der Konvertierungsdauer kommen. Das Laden von Modellen mit Kollisionsgittermodellen dauert länger, und bei Verwendung eines Szenengraphen vom Typ `dynamic` fällt auch mehr Aufwand in Bezug auf die Runtimeleistung an. Zur Erzielung einer optimalen Gesamtleistung sollten Sie diese Option für alle Modelle deaktivieren, für die Sie keine räumlichen Abfragen benötigen.

### <a name="unlit-materials"></a>Unbeleuchtete Materialien

* `unlitMaterials`: Bei der Konvertierung wird standardmäßig versucht, [PBR-Materialien](../../overview/features/pbr-materials.md) zu erstellen. Bei dieser Option wird das Konvertierungsmodul angewiesen, alle Materialien stattdessen als [Farbmaterialien](../../overview/features/color-materials.md) zu behandeln. Falls Sie bereits über Daten mit Beleuchtung verfügen, z. B. bei Modellen auf Grundlage der Fotogrammetrie, können Sie mit dieser Option schnell die richtige Konvertierung für alle Materialien erzwingen, ohne dass eine [Überschreibung für jedes Material](override-materials.md) einzeln durchgeführt werden muss.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Konvertieren aus älteren FBX-Formaten mit einem Phong-Materialmodell

* `fbxAssumeMetallic`: Bei älteren Versionen des FBX-Formats werden die Materialien mit einem Phong-Materialmodell definiert. Beim Konvertierungsprozess muss abgeleitet werden, wie diese dem [PBR-Modell](../../overview/features/pbr-materials.md) des Renderers zugeordnet sind. Normalerweise funktioniert dies gut. Es kann aber zu Mehrdeutigkeiten kommen, wenn ein Material keine Texturen aufweist und über hohe Glanzwerte und eine andere Albedofarbe als Grau verfügt. Unter diesen Umständen muss bei der Konvertierung zwischen den folgenden Optionen gewählt werden: der Priorisierung der hohen Glanzwerte, der Definition eines stark reflektierenden metallischen Materials, bei dem sich die Albedofarbe quasi auflöst, oder der Priorisierung der Albedofarbe, indem beispielsweise ein glänzender farbiger Kunststoff definiert wird. Standardmäßig wird beim Konvertierungsprozess vorausgesetzt, dass hohe Glanzwerte in Fällen, in denen Mehrdeutigkeit besteht, ein metallisches Material implizieren. Dieser Parameter kann auf `false` festgelegt werden, um dies umzukehren.

### <a name="coordinate-system-overriding"></a>Überschreibung des Koordinatensystems

* `axis`: Dient zum Außerkraftsetzen der Einheitenvektoren des Koordinatensystems. Die Standardwerte lauten wie folgt: `["+x", "+y", "+z"]`. Theoretisch verfügt das FBX-Format über einen Header, in dem diese Vektoren definiert sind, und diese Informationen werden vom Konvertierungsmodul zum Transformieren der Szene verwendet. Vom glTF-Format wird auch ein festes Koordinatensystem definiert. In der Praxis enthalten einige Objekte entweder fehlerhafte Informationen im Header oder wurden mit einer anderen Koordinatensystemkonvention gespeichert. Mit dieser Option können Sie das Koordinatensystem außer Kraft setzen, um dies zu lösen. Beispiel: Mit `"axis" : ["+x", "+z", "-y"]` werden die Z- und die Y-Achse vertauscht, und die „Händigkeit“ des Koordinatensystems wird beibehalten, indem die Richtung der Y-Achse invertiert wird.

### <a name="node-meta-data"></a>Knotenmetadaten

* `metadataKeys`: Ermöglicht die Angabe von bestimmten Schlüsseln für Eigenschaften von Knotenmetadaten, die Sie im Konvertierungsergebnis beibehalten möchten. Sie können genaue Schlüssel oder Platzhalterschlüssel angeben. Platzhalterschlüssel weisen das Format „ABC*“ auf und stimmen mit allen Schlüsseln überein, die mit „ABC“ beginnen. Die unterstützten Metadatenwerttypen sind `bool`, `int`, `float` und `string`.

    Bei GLTF-Dateien stammen diese Daten aus dem [extras-Objekt auf Knoten](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras). Bei FBX-Dateien stammen diese Daten aus den `Properties70`-Daten in `Model nodes`. Weitere Informationen finden Sie in der Dokumentation Ihres Tools für 3D-Medienobjekte.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex":::-Format

Es ist möglich, das :::no-loc text="vertex":::-Format für ein Gittermodell anzupassen, um auf Kosten der Genauigkeit Arbeitsspeicher zu sparen. Ein geringerer Speicherbedarf ermöglicht Ihnen das Laden größerer Modelle oder das Erzielen einer besseren Leistung. Je nach Ihren Daten kann ein falsches Format aber erhebliche Auswirkungen auf die Renderingqualität haben.

> [!CAUTION]
> Eine Änderung des :::no-loc text="vertex":::-Formats sollte nur das letzte Mittel sein, wenn im Speicher für die Modelle nicht mehr genug Platz ist oder wenn die höchstmögliche Leistung erreicht werden soll. Durch Änderungen kann es leicht zu Artefakten beim Rendern kommen – sowohl offensichtlicher als auch subtiler Art. Wenn Sie nicht wissen, worauf Sie achten müssen, sollten Sie die Standardeinstellung nicht ändern.

Die folgenden Anpassungen sind möglich:

* Bestimmte Datenströme können explizit eingebunden oder ausgeschlossen werden.
* Die Genauigkeit von Datenströmen kann verringert werden, um den Speicherbedarf zu reduzieren.

Der folgende `vertex`-Abschnitt in der `.json`-Datei ist optional. Für jeden Teil, der nicht explizit angegeben ist, greift der Konvertierungsdienst auf die Standardeinstellung zurück.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Wenn Sie für eine Komponente die Einstellung `NONE` erzwingen, ist sichergestellt, dass das Ausgabegittermodell nicht über den entsprechenden Datenstrom verfügt.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>Komponentenformate pro :::no-loc text="vertex":::-Datenstrom

Die folgenden Formate sind für die jeweiligen Komponenten zulässig:

| :::no-loc text="Vertex":::-Komponente | Unterstützte Formate (Fett = Standardeinstellung) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, NONE |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **NONE**|
|normal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|tangent| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, NONE |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, NONE |

#### <a name="supported-component-formats"></a>Unterstützte Komponentenformate

Für die Formate gilt der folgende Speicherbedarf:

| Format | Beschreibung | Bytes pro „:::no-loc text="vertex":::“ |
|:-------|:------------|:---------------|
|32_32_FLOAT|Vollständige Gleitkommagenauigkeit mit zwei Komponenten|8
|16_16_FLOAT|Halbe Gleitkommagenauigkeit mit zwei Komponenten|4
|32_32_32_FLOAT|Vollständige Gleitkommagenauigkeit mit drei Komponenten|12
|16_16_16_16_FLOAT|Halbe Gleitkommagenauigkeit mit vier Komponenten|8
|8_8_8_8_UNSIGNED_NORMALIZED|Byte mit vier Komponenten, auf Bereich `[0; 1]` normalisiert|4
|8_8_8_8_SIGNED_NORMALIZED|Byte mit vier Komponenten, auf Bereich `[-1; 1]` normalisiert|4

#### <a name="best-practices-for-component-format-changes"></a>Bewährte Methoden für Änderungen des Komponentenformats

* `position`: Die geringere Genauigkeit reicht nur in seltenen Fällen aus. Mit **16_16_16_16_FLOAT** kommt es auch bei kleinen Modellen zu erkennbaren Quantisierungartefakten.
* `normal`, `tangent`, `binormal`: Diese Werte werden normalerweise zusammen geändert. Sofern es bei der normalen Quantisierung nicht zu erkennbaren Beleuchtungsartefakten kommt, gibt es keinen Grund, die Genauigkeit hierfür zu erhöhen. In einigen Fällen können diese Komponenten aber auf **NONE** festgelegt werden:
  * `normal`, `tangent` und `binormal` werden nur benötigt, wenn mindestens ein Material des Modells beleuchtet sein soll. Bei ARR ist dies der Fall, wenn für das Modell zu irgendeinem Zeitpunkt ein [PBR-Material](../../overview/features/pbr-materials.md) verwendet wird.
  * `tangent` und `binormal` werden nur benötigt, wenn für ein beleuchtetes Material eine normale Mapping-Textur verwendet wird.
* `texcoord0`, `texcoord1`: Für Texturkoordinaten kann eine reduzierte Genauigkeit (**16_16_FLOAT**) verwendet werden, wenn sich die Werte im Bereich `[0; 1]` befinden und die jeweiligen Texturen eine maximale Größe von 2.048 x 2.048 Pixel aufweisen. Bei Überschreitung dieser Grenzwerte leidet die Qualität des Textur-Mappings.

#### <a name="example"></a>Beispiel

Angenommen, Sie verfügen über ein Fotogrammetriemodell, bei dem die Beleuchtung in die Texturen integriert ist. Zum Rendern des Modells werden hierbei nur die :::no-loc text="vertex":::-Positionen und Texturkoordinaten benötigt.

Standardmäßig wird vom Konvertierungsmodul vorausgesetzt, dass Sie für ein Modell ggf. irgendwann PBR-Materialien nutzen. Daher werden für Sie Daten vom Typ `normal`, `tangent` und `binormal` generiert. Die Speichernutzung pro Scheitelpunkt beträgt daher `position` (12 Byte) + `texcoord0` (8 Byte) + `normal` (4 Byte) + `tangent` (4 Byte) + `binormal` (4 Byte) = 32 Byte. Größere Modelle dieses Typs können ohne Weiteres mehrere Millionen Scheitelpunkte (:::no-loc text="vertices":::) aufweisen. Dies führt zu Modellen, die mehrere Gigabytes an Arbeitsspeicher belegen können. Diese hohen Datenmengen wirken sich auf die Leistung aus, und unter Umständen ist nicht genügend Arbeitsspeicher vorhanden.

Da Sie wissen, dass Sie für das Modell niemals eine dynamische Beleuchtung benötigen und dass alle Texturkoordinaten im Bereich `[0; 1]` liegen, können Sie `normal`, `tangent` und `binormal` auf `NONE` und `texcoord0` auf die halbe Genauigkeit (`16_16_FLOAT`) festlegen, sodass sich nur 16 Bytes pro Scheitelpunkt (:::no-loc text="vertex":::) ergeben. Indem Sie die Gittermodelldaten halbieren, können Sie größere Modelle laden und ggf. die Leistung steigern.

## <a name="memory-optimizations"></a>Arbeitsspeicheroptimierungen

Der Arbeitsspeicherbedarf geladener Inhalte kann zu einem Engpass im Renderingsystem werden. Eine zu große Arbeitsspeichernutzlast kann die Renderingleistung beeinträchtigen oder dazu führen, dass das Modell gar nicht geladen wird. In diesem Absatz werden einige wichtige Strategien zur Verringerung des Speicherbedarfs erläutert.

### <a name="instancing"></a>Instanziierung

Bei der Instanziierung werden Meshes für Teile mit unterschiedlichen räumlichen Transformationen wiederverwendet, sodass nicht jeder Teil auf seine eigene individuelle Geometrie verweist. Die Instanziierung hat erhebliche Auswirkungen auf den Speicherbedarf.
Anwendungsfälle für die Instanziierung wären beispielsweise die Schrauben in einem Motorenmodell oder Stühle in einem Architekturmodell.

> [!NOTE]
> Die Instanziierung kann den Arbeitsspeicherbedarf (und somit die Ladezeiten) erheblich verbessern. Die Verbesserungen bei der Renderingleistung sind jedoch zu vernachlässigen.

Die Instanziierung wird vom Konvertierungsdienst berücksichtigt, wenn Teile in der Quelldatei entsprechend markiert sind. Bei der Konvertierung erfolgt jedoch keine zusätzliche tiefgreifende Analyse der Meshdaten, um wiederverwendbare Teile zu identifizieren. Somit sind das Inhaltserstellungstool und dessen Exportpipeline für eine ordnungsgemäße Instanziierung entscheidend.

Um zu testen, ob Instanziierungsinformationen bei der Konvertierung erhalten bleiben, können Sie sich einfach das Element `numMeshPartsInstanced` in der [Ausgabestatistik](get-information.md#example-info-file) ansehen. Ist der Wert für `numMeshPartsInstanced` größer Null, werden Meshes instanzübergreifend gemeinsam genutzt.

#### <a name="example-instancing-setup-in-3ds-max"></a>Beispiel: Instanziierungseinrichtung in 3DS Max

[Autodesk 3DS Max](https://www.autodesk.de/products/3ds-max) verfügt über unterschiedliche Modi zum Klonen von Objekten. Diese heißen **`Copy`** , **`Instance`** und **`Reference`** und weisen jeweils ein anderes Verhalten hinsichtlich der Instanziierung in der exportierten Datei vom Typ `.fbx` auf.

![Klonen in 3DS Max](./media/3dsmax-clone-object.png)

* **`Copy`** : In diesem Modus wird das Mesh geklont. Es findet also keine Instanziierung statt (`numMeshPartsInstanced` = 0).
* **`Instance`** : Von den beiden Objekten wird das gleiche Mesh verwendet. Es erfolgt also eine Instanziierung (`numMeshPartsInstanced` = 1).
* **`Reference`** : Da auf die Geometrien unterschiedliche Modifizierer angewendet werden können, wird vom Exportprogramm ein konservativer Ansatz gewählt, und es findet keine Instanziierung statt (`numMeshPartsInstanced` = 0).


### <a name="depth-based-composition-mode"></a>Tiefenbasierter Kompositionsmodus

Falls der Arbeitsspeicher ein kritischer Faktor ist, konfigurieren Sie den Renderer mit dem [Modus „DepthBasedComposition“](../../concepts/rendering-modes.md#depthbasedcomposition-mode). In diesem Modus wird die GPU-Nutzlast auf mehrere GPUs verteilt.

### <a name="decrease-vertex-size"></a>Verringern der Scheitelpunktgröße

Wie im Abschnitt [Bewährte Methoden für Änderungen des Komponentenformats](configure-model-conversion.md#best-practices-for-component-format-changes) erläutert, lässt sich der Speicherbedarf durch Anpassen des Scheitelpunktformats verringern. Diese Option sollte jedoch das letzte Mittel sein.

### <a name="texture-sizes"></a>Texturgrößen

Die Texturdatenmenge kann abhängig von der Art des Szenarios den für Meshdaten genutzten Arbeitsspeicher übersteigen. Dies kann beispielsweise bei Fotogrammetriemodellen der Fall sein.
In der Konvertierungskonfiguration gibt es keine Möglichkeit zum automatischen Herunterskalieren von Texturen. Bei Bedarf muss die Texturskalierung als clientseitiger Vorverarbeitungsschritt durchgeführt werden. Im Konvertierungsschritt wird jedoch ein geeignetes [Texturkomprimierungsformat](/windows/win32/direct3d11/texture-block-compression-in-direct3d-11) ausgewählt:

* `BC1` für nicht transparente Farbtexturen
* `BC7` für Quellfarbtexturen mit Alphakanal

Da der Speicherbedarf beim Format `BC7` doppelt so hoch ist wie beim Format `BC1`, muss darauf geachtet werden, dass von den Eingabetexturen nicht unnötig ein Alphakanal bereitgestellt wird.

## <a name="typical-use-cases"></a>Typische Anwendungsfälle

Die Ermittlung von gut geeigneten Importeinstellungen für einen bestimmten Anwendungsfall kann ein aufwendiger Prozess sein. Andererseits können die Konvertierungseinstellungen auch große Auswirkungen auf die Runtimeleistung haben.

Es gibt bestimmte Klassen von Anwendungsfällen, die für bestimmte Optimierungen geeignet sind. Unten sind einige Beispiele angegeben.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Anwendungsfall: Architekturvisualisierung/Große Landschaftskarten

* Diese Arten von Szenen sind meist statischer Art. Dies bedeutet, dass sie keine beweglichen Teile benötigen. `sceneGraphMode` kann daher auf `static` oder auch auf `none` festgelegt werden, um die Runtimeleistung zu verbessern. Im Modus `static` kann der Stammknoten der Szene trotzdem verschoben, gedreht und skaliert werden, um beispielsweise dynamisch zwischen einer 1:1-Skalierung (für die Ich-Perspektive) und einer Darstellung auf einer Tischplatte umzuschalten.

* Beim Verschieben von Teilen bedeutet dies in der Regel auch, dass Sie Unterstützung für Raycasting oder andere [räumliche Abfragen](../../overview/features/spatial-queries.md) benötigen, damit Sie diese Teile überhaupt auswählen können. Falls Sie keine Verschiebungen durchführen möchten, ist die Wahrscheinlichkeit dagegen hoch, dass Sie hierfür auch keine räumlichen Abfragen nutzen müssen. Aus diesem Grund können Sie das Flag `generateCollisionMesh` deaktivieren. Diese Umschaltung hat eine erhebliche Auswirkung die Konvertierungsdauer, die Ladedauer und auch auf den Aktualisierungsaufwand pro Frame während der Runtime.

* Wenn für die Anwendung keine [Schnittebenen](../../overview/features/cut-planes.md) (Cut planes) verwendet werden, sollte das Flag `opaqueMaterialDefaultSidedness` deaktiviert werden. Dies führt normalerweise zu einem Leistungszuwachs von 20 bis 30 %. Schnittebenen können weiter genutzt werden, aber beim Betrachten der inneren Teile eines Objekts sind keine rückwärtigen Flächen vorhanden, die ggf. störend wirken können. Weitere Informationen zu Rendering vom Typ :::no-loc text="single sided"::: finden Sie unter [Einseitiges Rendering](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Anwendungsfall: Fotogrammetriemodelle

Beim Rendern von Fotogrammetriemodellen wird normalerweise kein Szenengraph benötigt, sodass Sie `sceneGraphMode` auf `none` festlegen können. Da diese Modelle nur selten über einen komplexen Szenengraphen verfügen, sollte diese Option aber keine größere Auswirkung haben.

Es wird keine dynamische Beleuchtung benötigt, weil sie bereits in die Texturen integriert ist. Deshalb gilt Folgendes:

* Legen Sie das Flag `unlitMaterials` auf `true` fest, um alle Materialien in unbeleuchtete [Farbmaterialien](../../overview/features/color-materials.md) zu verwandeln.
* Entfernen Sie nicht benötigte Daten aus dem Scheitelpunktformat. Sehen Sie sich hierzu das obige [Beispiel](#example) an.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Anwendungsfall: Visualisierung von kompakten Maschinen usw.

Bei diesen Anwendungsfällen weisen die Modelle häufig einen sehr hohen Detailgrad bei kleinem Volumen auf. Der Renderer wurde speziell optimiert, um diese Fälle gut verarbeiten zu können. Die meisten Optimierungen für den vorherigen Anwendungsfall gelten hier aber nicht:

* Da einzelne Teile auswählbar und verschiebbar sein sollten, muss für `sceneGraphMode` die Einstellung `dynamic` beibehalten werden.
* Das Raycasting ist meist integraler Bestandteil der Anwendung, sodass Kollisionsgittermodelle generiert werden müssen.
* Schnittebenen sehen besser aus, wenn das Flag `opaqueMaterialDefaultSidedness` aktiviert ist.

## <a name="deprecated-features"></a>Veraltete Features

Das Bereitstellen von Einstellungen mithilfe der nicht modellspezifischen Datei `conversionSettings.json` wird weiterhin unterstützt, ist jedoch veraltet.
Verwenden Sie stattdessen den modellspezifischen Dateinamen `<modelName>.ConversionSettings.json`.

Die Verwendung einer `material-override`-Einstellung zum Identifizieren einer [Materialüberschreibungsdatei](override-materials.md) in der Datei mit den Konvertierungseinstellungen wird noch unterstützt, ist aber veraltet. Verwenden Sie stattdessen den modellspezifischen Dateinamen `<modelName>.MaterialOverrides.json`.

## <a name="next-steps"></a>Nächste Schritte

* [Modellkonvertierung](model-conversion.md)
* [Farbmaterialien](../../overview/features/color-materials.md)
* [PBR-Materialien](../../overview/features/pbr-materials.md)
* [Überschreiben von Materialien während der Modellkonvertierung](override-materials.md)