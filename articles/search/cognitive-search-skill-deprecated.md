---
title: Veraltete kognitive Skills
titleSuffix: Azure Cognitive Search
description: Diese Seite enthält eine Liste der kognitiven Skills, die als veraltet gelten und in naher Zukunft in den Skillsets der kognitiven Azure-Suche nicht mehr unterstützt werden.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: 884d5cd8d2a1318162a09a39a7ef03bb8b8f612e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631740"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Veraltete kognitive Skills in Azure Cognitive Search

In diesem Dokument werden die kognitiven Qualifikationen beschrieben, die als veraltet betrachtet werden. Verwenden Sie für den Inhalt die folgende Anleitung:

* Skillname: Name des Skills, der als veraltet markiert wird, entspricht dem Attribut @odata.type.
* Letzte verfügbare API-Version: Die letzte Version der öffentlichen Azure Cognitive Search-API, über die Qualifikationsgruppen mit der entsprechenden veralteten Qualifikation erstellt/aktualisiert werden können. Indexer mit angefügten Skillsets, deren Skills auch in zukünftigen API-Versionen bis zum Ende des Supports ausgeführt werden, ab diesem Datum dann aber zu Fehlern führen.
* Ende des Supports: Der Tag, nach dem der entsprechende Skill als nicht mehr unterstützt gilt und nicht mehr funktioniert. Vorher erstellte Qualifikationsgruppen sollten funktionsfähig bleiben. Benutzern wird jedoch empfohlen, von der als veraltet markierten Qualifikation zu einer anderen zu migrieren.
* Empfehlungen: Migrationspfad zum Verwenden einer unterstützten Qualifikation. Benutzern wird empfohlen, den Empfehlungen zu folgen, um weiterhin Support zu erhalten.

Wenn Sie [Microsoft.Skills.Text.EntityRecognitionSkill](#microsoftskillstextentityrecognitionskill) verwenden, hilft Ihnen dieser Artikel dabei, Ihr Skillset für die Verwendung des Skills [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) zu aktualisieren, der allgemein verfügbar ist und neue Features einführt. 

Wenn Sie [Microsoft.Skills.Text.SentimentSkill](#microsoftskillstextsentimentskill) verwenden, hilft Ihnen dieser Artikel dabei, Ihr Skillset für die Verwendung des Skills [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md) zu aktualisieren, der allgemein verfügbar ist und neue Features einführt. 

Wenn Sie [Microsoft.Skills.Text.NamedEntityRecognitionSkill](#microsoftskillstextnamedentityrecognitionskill) verwenden, hilft Ihnen dieser Artikel dabei, Ihr Skillset für die Verwendung des Skills [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) zu aktualisieren, der allgemein verfügbar ist und neue Features einführt.

## <a name="microsoftskillstextentityrecognitionskill"></a>Microsoft.Skills.Text.EntityRecognitionSkill

### <a name="last-available-api-version"></a>Letzte verfügbare API-Version

2021-04-30-Preview

### <a name="end-of-support"></a>Ende des Supports

31. August 2024

### <a name="recommendations"></a>Empfehlungen 

Verwenden Sie stattdessen [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md). Dieser Skill enthält die meisten Funktionen von EntityRecognitionSkill, jedoch mit höherer Qualität. Die komplexen Ausgabefelder enthalten zudem umfangreichere Informationen.

Um zu [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) zu migrieren, müssen Sie eine oder mehrere der folgenden Änderungen an Ihrer Skilldefinition vornehmen. Ein Update der Qualifikationsdefinition können Sie mit der [API zum Aktualisieren von Qualifikationsgruppen](/rest/api/searchservice/update-skillset) vornehmen.

1. *(Erforderlich)* Ändern Sie `@odata.type` von `"#Microsoft.Skills.Text.EntityRecognitionSkill"` in `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`.

2. *(Optional:)* Der `includeTypelessEntities`-Parameter wird nicht mehr unterstützt, da der neue Skill immer nur Entitäten mit bekannten Typen zurückgibt. Wenn Ihre vorherige Skilldefinition also darauf verweist, sollte er jetzt entfernt werden.

3. *(Optional:)* Wenn Sie die Ausgabe `namedEntities` verwenden, gibt es einige geringfügige Änderungen an den Eigenschaftennamen.
    1. `value` wird umbenannt in `text`.
    2. `confidence` wird umbenannt in `confidenceScore`.

    Wenn Sie genau die gleichen Eigenschaftennamen generieren müssen, fügen Sie einen [ShaperSkill](cognitive-search-skill-shaper.md) hinzu, um die Ausgabe mit den erforderlichen Namen umzugestalten. Beispielsweise benennt dieser ShaperSkill die Eigenschaften in ihre alten Werte um.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntitiesV3/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntitiesV3/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntitiesV3/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntitiesV3/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "namedEntities"
            }
        ]
    }
    ```

4. *(Optional:)* Wenn Sie die Ausgabe `entities` verwenden, um Entitäten mit bekannten Entitäten zu verknüpfen, ist diese Funktion jetzt ein neuer Skill: [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md). Fügen Sie Ihrem Skillset den Skill zur Entitätsverknüpfung hinzu, um die verknüpften Entitäten zu generieren. Es gibt auch einige geringfügige Änderungen an den Eigenschaftennamen der Ausgabe `entities` zwischen `EntityRecognitionSkill` und dem neuen `EntityLinkingSkill`.
    1. `wikipediaId` wird umbenannt in `id`.
    2. `wikipediaLanguage` wird umbenannt in `language`.
    3. `wikipediaUrl` wird umbenannt in `url`.
    4. Die Eigenschaften `type` und `subtype` werden nicht mehr zurückgegeben.

    Wenn Sie genau die gleichen Eigenschaftennamen generieren müssen, fügen Sie einen [ShaperSkill](cognitive-search-skill-shaper.md) hinzu, um die Ausgabe mit den erforderlichen Namen umzugestalten. Beispielsweise benennt dieser ShaperSkill die Eigenschaften in ihre alten Werte um.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "LinkedEntitiesShaper",
        "description": "LinkedEntitiesShaper",
        "context": "/document/linkedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/linkedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "name",
                        "source": "/document/linkedEntitiesV3/*/name"
                    },
                    {
                        "name": "wikipediaId",
                        "source": "/document/linkedEntitiesV3/*/id"
                    },
                    {
                        "name": "wikipediaLanguage",
                        "source": "/document/linkedEntitiesV3/*/language"
                    },
                    {
                        "name": "wikipediaUrl",
                        "source": "/document/linkedEntitiesV3/*/url"
                    },
                    {
                        "name": "bingId",
                        "source": "/document/linkedEntitiesV3/*/bingId"
                    },
                    {
                        "name": "matches",
                        "source": "/document/linkedEntitiesV3/*/matches"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

5. *(Optional)* Wenn Sie den Parameter `categories` nicht explizit angeben, kann `EntityRecognitionSkill V3` neben den von `EntityRecognitionSkill` unterstützten Kategorietypen einen anderen Typ zurückgeben. Wenn dieses Verhalten nicht erwünscht ist, müssen Sie den Parameter `categories` explizit auf `["Person", "Location", "Organization", "Quantity", "Datetime", "URL", "Email"]` festlegen.

    _Beispieldefinitionen für die Migration_

    * Einfache Migration

        _(Vorher) Definition „EntityRecognitionSkill“_

        ```json
        {   
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _(Nachher) Definition „V3.EntityRecognitionSkill“_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * Komplizierte Migration

        _(Vorher) Definition „EntityRecognitionSkill“_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "includeTypelessEntities": true,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntities"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        }
        ```
    
        _(Nachher) Definition „V3.EntityRecognitionSkill“_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntitiesV3"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntitiesV3/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntitiesV3/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntitiesV3/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntitiesV3/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "entities",
                    "targetName": "linkedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "LinkedEntitiesShaper",
            "description": "LinkedEntitiesShaper",
            "context": "/document/linkedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/linkedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "name",
                            "source": "/document/linkedEntitiesV3/*/name"
                        },
                        {
                            "name": "wikipediaId",
                            "source": "/document/linkedEntitiesV3/*/id"
                        },
                        {
                            "name": "wikipediaLanguage",
                            "source": "/document/linkedEntitiesV3/*/language"
                        },
                        {
                            "name": "wikipediaUrl",
                            "source": "/document/linkedEntitiesV3/*/url"
                        },
                        {
                            "name": "bingId",
                            "source": "/document/linkedEntitiesV3/*/bingId"
                        },
                        {
                            "name": "matches",
                            "source": "/document/linkedEntitiesV3/*/matches"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="microsoftskillstextsentimentskill"></a>Microsoft.Skills.Text.SentimentSkill

### <a name="last-available-api-version"></a>Letzte verfügbare API-Version

2021-04-30-Preview

### <a name="end-of-support"></a>Ende des Supports

31. August 2024

### <a name="recommendations"></a>Empfehlungen 

Verwenden Sie stattdessen [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md). Er bietet ein verbessertes Modell und die Möglichkeit, Opinion Mining oder eine aspektbasierte Stimmung hinzuzufügen. Da der Skill erheblich komplexer ist, unterscheiden sich auch die Ausgaben stark.

Um zu [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md) zu migrieren, müssen Sie eine oder mehrere der folgenden Änderungen an Ihrer Skilldefinition vornehmen. Ein Update der Qualifikationsdefinition können Sie mit der [API zum Aktualisieren von Qualifikationsgruppen](/rest/api/searchservice/update-skillset) vornehmen.

> [!NOTE]
> Die Skillausgaben für „V3.SentimentSkill“ sind nicht mit der Indexdefinition kompatibel, die auf „SentimentSkill“ basiert. Sie müssen an der Indexdefinition, dem Skillset (spätere Skilleingaben und/oder Wissensspeicherprojektionen) und den Feldzuordnungen der Indexerausgabe Änderungen vornehmen, um den Stimmungsskill durch die neue Version zu ersetzen.

1. *(Erforderlich)* Ändern Sie `@odata.type` von `"#Microsoft.Skills.Text.SentimentSkill"` in `"#Microsoft.Skills.Text.V3.SentimentSkill"`.

2. *(Erforderlich:)* „V3.SentimentSkill“ stellt einen `positive`, `neutral` und `negative` Score für den Gesamttext und die gleichen Scores für jeden Satz im Gesamttext zur Verfügung, während der frühere „SentimentSkill“ nur ein einzelnes Double bereitgestellt hat, das zwischen 0,0 (negativ) und 1,0 (positiv) für den gesamten Text lag. Sie müssen Ihre Indexdefinition aktualisieren, um die drei doppelten Werte anstelle eines einzelnen Scores zu akzeptieren, und sicherstellen, dass alle Ihre Downstream-Skilleingaben, Wissensspeicherprojektionen und Ausgabefeldzuordnungen den Namensänderungen entsprechen.

Es wird empfohlen, den alten „SentimentSkill“ vollständig durch „V3.SentimentSkill“ zu ersetzen, Ihre Downstream-Skilleingaben, Wissensspeicherprojektionen, Feldzuordnungen der Indexerausgabe und Indexdefinitionen so zu aktualisieren, dass sie dem neuen Ausgabeformat entsprechen, und den Indexer zurückzusetzen, damit alle Ihre Dokumente in Zukunft konsistente Stimmungsergebnisse erhalten.

> [!NOTE]
> Wenn Sie zusätzliche Hilfe beim Aktualisieren Ihrer Anreicherungspipeline benötigen, um die neueste Version des Stimmungsskills zu verwenden, oder wenn das Zurücksetzen Ihres Indexers keine Option für Sie ist, legen Sie eine neue [Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) an, damit wir direkt mit Ihnen zusammenarbeiten können.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Letzte verfügbare API-Version

Vorschauversion vom 11.11.2017

### <a name="end-of-support"></a>Ende des Supports

31. August 2024

### <a name="recommendations"></a>Empfehlungen 

Verwenden Sie stattdessen [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md). Diese Qualifikation enthält die meisten Funktionen von „NamedEntityRecognitionSkill“, jedoch mit höherer Qualität. Die komplexen Ausgabefelder enthalten zudem umfangreichere Informationen.

Um zu [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) zu migrieren, müssen Sie eine oder mehrere der folgenden Änderungen an Ihrer Skilldefinition vornehmen. Ein Update der Qualifikationsdefinition können Sie mit der [API zum Aktualisieren von Qualifikationsgruppen](/rest/api/searchservice/update-skillset) vornehmen.

1. *(Erforderlich)* Ändern Sie `@odata.type` von `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` in `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"`.

2. *(Optional)* Wenn Sie die `entities`-Ausgabe nutzen möchten, verwenden Sie stattdessen die komplexe Sammlungsausgabe `namedEntities` von `EntityRecognitionSkill V3`. Es gibt auch einige geringfügige Änderungen an den Eigenschaftennamen der neuen komplexen Ausgabe von `namedEntities`:
    1. `value` wird umbenannt in `text`.
    2. `confidence` wird umbenannt in `confidenceScore`.
    
    Wenn Sie genau die gleichen Eigenschaftennamen generieren müssen, fügen Sie einen [ShaperSkill](cognitive-search-skill-shaper.md) hinzu, um die Ausgabe mit den erforderlichen Namen umzugestalten. Beispielsweise benennt dieser ShaperSkill die Eigenschaften in ihre alten Werte um.

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntities",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntities/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntities/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntities/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntities/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntities/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

3. *(Optional)* Wenn Sie den Parameter `categories` nicht explizit angeben, kann `EntityRecognitionSkill V3` neben den von `NamedEntityRecognitionSkill` unterstützten Kategorietypen einen anderen Typ zurückgeben. Wenn dieses Verhalten nicht erwünscht ist, müssen Sie den Parameter `categories` explizit auf `["Person", "Location", "Organization"]` festlegen.

    _Beispieldefinitionen für die Migration_

    * Einfache Migration

        _(Vorher) Definition „NamedEntityRecognitionSkill“_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _(Nachher) Definition „V3.EntityRecognitionSkill“_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * Etwas kompliziertere Migration

        _(Vorher) Definition „NamedEntityRecognitionSkill“_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "entities"
                }
            ]
        }
        ```

        _(Nachher) Definition „V3.EntityRecognitionSkill“_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntities",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntities/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntities/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntities/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntities/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntities/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Skill „Entitätserkennung“ (V3)](cognitive-search-skill-entity-recognition-v3.md)
+ [Skill „Stimmung“ (V3)](cognitive-search-skill-sentiment-v3.md)