---
title: Überschreiben von Materialien während der Modellkonvertierung
description: Erläutert den Materialüberschreibungsworkflow zur Konvertierungszeit.
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: f99439ae71f8a66b9fc36b32603681e62f2224a9
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968247"
---
# <a name="override-materials-during-model-conversion"></a>Überschreiben von Materialien während der Modellkonvertierung

Die Materialeinstellungen im Quellmodell werden verwendet, um die [PBR-Materialien](../../overview/features/pbr-materials.md) zu definieren, die vom Renderer verwendet werden.
Manchmal gibt die [Standardkonvertierung](../../reference/material-mapping.md) nicht die gewünschten Ergebnisse aus, und Sie müssen Änderungen vornehmen.
Wenn ein Modell für die Verwendung in Azure Remote Rendering konvertiert wird, können Sie eine Materialüberschreibungsdatei bereitstellen, um anzupassen, wie die Materialkonvertierung pro Material durchgeführt wird.
Wird eine Datei namens `<modelName>.MaterialOverrides.json` im Eingabecontainer neben dem Eingabemodell `<modelName>.<ext>` gefunden, wird sie als Materialüberschreibungsdatei verwendet.

## <a name="the-override-file-used-during-conversion"></a>Die während der Konvertierung verwendete Überschreibungsdatei

Angenommen, ein Boxmodell verfügt über ein einzelnes Material mit dem Namen „Default“ (Standard).
Zusätzlich muss beispielsweise die Rückstrahlfarbe für die Verwendung in ARR angepasst werden.
In diesem Fall kann eine Datei `box.MaterialOverrides.json` wie folgt erstellt werden:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

Die Datei `box.MaterialOverrides.json` wird im Eingabecontainer neben `box.fbx` platziert, wodurch der Konvertierungsdienst angewiesen wird, die neuen Einstellungen anzuwenden.

### <a name="color-materials"></a>Farbmaterialien

Das [Farbmaterialmodell](../../overview/features/color-materials.md) beschreibt eine ständig beschattete Oberfläche, die unabhängig von der Beleuchtung ist.
Farbmaterialien sind z. B. nützlich für Medienobjekte, die von Fotogrammmetriealgorithmen erstellt werden.
In Materialüberschreibungsdateien kann ein Material als Farbmaterial deklariert werden, indem `unlit` auf `true` festgelegt wird.

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignorieren bestimmter Texturzuordnungen

Manchmal wünschen Sie möglicherweise, dass der Konvertierungsprozess bestimmte Texturzuordnungen ignoriert. Dies kann der Fall sein, wenn das Modell von einem Tool generiert wurde, das spezielle Zuordnungen generiert, die vom Renderer nicht richtig verstanden werden. Beispielsweise eine „OpacityMap“, die verwendet wird, um etwas anderes als Deckkraft zu definieren, oder ein Modell, in dem die „NormalMap“ als „BumpMap“ gespeichert wird. (In letzterem Fall möchten Sie „NormalMap“ ignorieren, was bewirkt, dass der Konverter „BumpMap“ als „NormalMap“ verwendet.)

Das Prinzip ist einfach. Fügen Sie einfach eine Eigenschaft namens `ignoreTextureMaps` hinzu, und fügen Sie eine beliebige Texturzuordnung hinzu, die Sie ignorieren möchten:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Die vollständige Liste der Texturzuordnungen, die Sie ignorieren können, finden Sie im nachfolgenden JSON-Schema.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>Anwenden derselben Überschreibungen auf mehrere Materialien

Standardmäßig gilt ein Eintrag in der Materialüberschreibungsdatei, wenn sein Name genau mit dem Materialnamen übereinstimmt.
Da eine Überschreibung üblicherweise für mehrere Materialien gilt, können Sie optional einen regulären Ausdruck als Eintragsnamen angeben.
Das Feld `nameMatching` hat einen Standardwert von `exact`, kann jedoch auf `regex` festgelegt werden, um anzugeben, dass der Eintrag auf jedes übereinstimmende Material angewendet werden soll.
Die verwendete Syntax ist identisch mit der Syntax, die für JavaScript verwendet wird. Das folgende Beispiel zeigt eine Überschreibung, die für Materialien mit Namen wie „Material2“, „Material01“ und „Material999“ gilt.

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

Höchstens ein Eintrag in einer Materialüberschreibungsdatei gilt für ein einzelnes Material.
Wenn für den Materialnamen eine genaue Entsprechung vorliegt (d. h. `nameMatching` fehlt oder ist gleich `exact`), wird dieser Eintrag ausgewählt.
Andernfalls wird der erste Regex-Eintrag in der Datei ausgewählt, der mit dem Materialnamen übereinstimmt.

### <a name="getting-information-about-which-entries-applied"></a>Abrufen von Informationen zu den angewendeten Einträgen

Die in den Ausgabecontainer geschriebene [Infodatei](get-information.md#information-about-a-converted-model-the-info-file) enthält Informationen über die Anzahl der bereitgestellten Überschreibungen und die Anzahl der überschriebenen Materialien.

## <a name="json-schema"></a>JSON-Schema

Das vollständige JSON-Schema für Materialdateien finden Sie hier. Mit Ausnahme von `unlit` und `ignoreTextureMaps` handelt es sich bei den verfügbaren Eigenschaften um eine Teilmenge der Eigenschaften, die in den Abschnitten zu den [Farbmaterial-](../../overview/features/color-materials.md) und [PBR-Materialmodellen](../../overview/features/pbr-materials.md) beschrieben werden.

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" },
            "transparencyWriteDepth": {"type" : "boolean" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Farbmaterialien](../../overview/features/color-materials.md)
* [PBR-Materialien](../../overview/features/pbr-materials.md)
