---
title: Zuordnen von Skillausgabefeldern
titleSuffix: Azure Cognitive Search
description: Exportieren Sie die angereicherten Inhalte, die von einem Skillset erstellt wurden, indem Sie die Ausgabefelder den Feldern in einem Suchindex zuordnen.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 0e1db5f12e83a88697db38a6ddd77edab445f6c0
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122350771"
---
# <a name="map-enrichment-output-to-fields-in-a-search-index"></a>Zuordnen der Anreicherungsausgabe zu Feldern in einem Suchindex

![Indexerphasen](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "Indexerphasen")

In diesem Artikel erfahren Sie, wie Sie angereicherte Eingabefelder Ausgabefeldern in einem durchsuchbaren Index zuordnen. Nachdem Sie ein [Skillset definiert](cognitive-search-defining-skillset.md) haben, müssen Sie die Ausgabefelder aller Skills zuordnen, die Werte direkt für ein bestimmtes Feld in Ihrem Suchindex bereitstellen.

Ausgabefeldzuordnungen sind für das Verschieben von Inhalt aus angereicherten Dokumenten in den Index erforderlich.  Das angereicherte Dokument ist eine Informationsstruktur, und selbst wenn der Support für komplexe Typen im Index verfügbar ist, sollten Sie die Informationen von der angereicherten Struktur in einen einfacheren Typ (z. B. ein Array von Zeichenfolgen) transformieren. Mithilfe von Ausgabefeldzuordnungen können Sie Datenformtransformationen ausführen, indem Sie Informationen vereinfachen. Ausgabefeldzuordnungen erfolgen immer nach der Skillsetausführung. Die Ausführung dieser Phase ist aber auch möglich, wenn kein Skillset definiert ist.

Beispiele für Ausgabefeldzuordnungen:

* Im Rahmen Ihres Skillsets haben Sie die Namen von Organisationen extrahiert, die auf den einzelnen Seiten Ihres Dokuments erwähnt werden. Als Nächstes möchten Sie diese Organisationsnamen jeweils einem Feld Ihres Index vom Typ „Edm.Collection(Edm.String)“ zuordnen.

* Im Rahmen Ihres Skillsets haben Sie einen neuen Knoten mit dem Namen „document/translated_text“ erstellt. Sie möchten die Informationen zu diesem Knoten einem bestimmten Feld in Ihrem Index zuordnen.

* Sie verfügen nicht über ein Skillset, sondern indizieren einen komplexen Typ über eine Cosmos DB-Datenbank. Sie möchten einen Knoten mit diesem komplexen Typ erreichen und ihn einem Feld in Ihrem Index zuordnen.

> [!NOTE]
> Ausgabefeldzuordnungen gelten nur für Suchindizes. Für Indexer, die [Wissensspeicher](knowledge-store-concept-intro.md) erstellen, werden Ausgabefeldzuordnungen ignoriert.

## <a name="use-outputfieldmappings"></a>Verwenden von „outputFieldMappings“

Um Felder zuzuordnen, fügen Sie `outputFieldMappings` zu Ihrer Indexerdefinition hinzu, wie unten gezeigt:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

Der Text der Anforderung ist wie folgt strukturiert:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

Legen Sie für jede Ausgabefeldzuordnung den Speicherort der Daten der angereicherten Dokumentstruktur (sourceFieldName) und den Namen des im Index referenzierten Feldes (targetFieldName) fest. Weisen Sie alle [Zuordnungsfunktionen](search-indexer-field-mappings.md#field-mapping-functions) zu, die Sie benötigen, um den Inhalt eines Felds zu transformieren, bevor es im Index gespeichert wird.

## <a name="flattening-information-from-complex-types"></a>Vereinfachen von Informationen komplexer Typen 

Der Pfad in einem sourceFieldName kann ein Element oder mehrere Elemente darstellen. Im obigen Beispiel stellt ```/document/content/sentiment``` einen einzelnen numerischen Wert dar, während ```/document/content/organizations/*/description``` für mehrere Organisationsbeschreibungen steht. 

In Fällen, in denen es mehrere Elemente gibt, werden diese zu einem Array „vereinfacht“, das jedes der Elemente enthält. 

Für das Beispiel ```/document/content/organizations/*/description``` würden die Daten im Feld *Beschreibungen* demnach wie ein flaches Array von Beschreibungen aussehen, bevor sie indiziert werden:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Es handelt sich um ein wichtiges Prinzip. Daher stellen wir ein weiteres Beispiel zur Verfügung. Stellen Sie sich vor, dass Sie über ein Array komplexer Typen als Teil der Anreicherungsstruktur verfügen. Nehmen wir an, es gibt ein Member namens customEntities, das über ein Array komplexer Typen wie das nachfolgend beschriebene verfügt.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Angenommen, Ihr Index verfügt über ein Feld mit dem Namen „Krankheiten“ vom Typ „Collection(EDM.String)“, in dem Sie die Namen der Entitäten speichern möchten. 

Hierfür können Sie das Symbol "\*" einfach wie folgt verwenden:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Dieser Vorgang „vereinfacht“ jeden Namen der customEntities-Elemente in ein einzelnes Array von Zeichenfolgen, z. B.:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Ihre angereicherten Felder den durchsuchbaren Feldern zugeordnet haben, können Sie die Feldattribute für jedes der durchsuchbaren Felder [als Teil der Indexdefinition](search-what-is-an-index.md) festlegen.

Weitere Informationen zu Feldzuordnungen finden Sie unter [Feldzuordnungen in Indexern der kognitiven Azure-Suche](search-indexer-field-mappings.md).