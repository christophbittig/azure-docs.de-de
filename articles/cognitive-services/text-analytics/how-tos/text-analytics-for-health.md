---
title: Verwenden von Text Analytics for Health
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie medizinische Informationen mithilfe von Text Analytics for Health aus unstrukturiertem klinischem Text extrahiert und bezeichnet werden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2021
ms.author: aahi
ms.openlocfilehash: 5b1883b06ae234ed8a4f9adf949cf26919f7b877
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113550154"
---
# <a name="how-to-use-text-analytics-for-health"></a>Gewusst wie: Verwenden von Text Analytics for Health

> [!IMPORTANT] 
> Text Analytics for Health ist eine Funktion, die „WIE BESEHEN“ und „MIT ALLEN MÄNGELN“ zur Verfügung gestellt wird. Text Analytics for Health ist nicht für die Verwendung als medizinisches Hilfsmittel, zur klinischen Unterstützung, als Diagnosetool oder als sonstige Technologie für den Einsatz in Diagnose, Therapie, Entschärfung, Behandlung oder Prävention von Krankheiten oder medizinischen Fällen vorgesehen oder verfügbar, und Microsoft erteilt keine Lizenzen oder Rechte zur Nutzung dieser Funktion für die beschriebenen Zwecke. Diese Funktion ist nicht als Ersatz für professionelle medizinische Beratung oder medizinische Gutachten, Diagnosen, Behandlungen oder das klinische Urteilsvermögen einer medizinischen Fachkraft konzipiert oder vorgesehen und sollte nicht als solcher eingesetzt werden. Jede Verwendung von Text Analytics for Health liegt in der alleinigen Verantwortung des Kunden. Der Kunde muss separat alle Quellvokabulare lizenzieren, die er gemäß den Bedingungen verwenden möchte, die für den [Anhang des UMLS-Metathesaurus-Lizenzvertrags](https://www.nlm.nih.gov/research/umls/knowledge_sources/metathesaurus/release/license_agreement_appendix.html) oder einen zukünftigen entsprechenden Link festgelegt sind. Der Kunde ist für die Einhaltung dieser Lizenzbedingungen verantwortlich, einschließlich aller geografischen oder anderen anwendbaren Einschränkungen.


Text Analytics for Health ist ein Feature des Textanalyse-API-Diensts, der relevante medizinische Informationen aus unstrukturierten Texten wie Arztbriefen, Entlassungszusammenfassungen, klinischen Dokumenten und elektronischen Gesundheitsakten extrahiert und bezeichnet.  Es gibt zwei Möglichkeiten zur Nutzung dieses Diensts: 

* [Die webbasierte API (asynchron)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Einen Docker-Container (synchron)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Features

Text Analytics for Health führt Erkennung benannter Entitäten (Named Entity Recognition, NER), Beziehungsextrahierung, Entitätsnegation und Entitätsverknüpfung in englischsprachigem Text aus, um tiefere Einblicke in unstrukturierten klinischen und biomedizinischen Text zu erhalten.

### <a name="named-entity-recognition"></a>[Erkennung benannter Entitäten](#tab/ner)

Die Erkennung benannter Entitäten erkennt Wörter und Ausdrücke in unstrukturiertem Text, die einem oder mehr semantischen Typen zugeordnet werden können, wie etwa Diagnose, Namen von Medikamenten, Symptom/Auffälligkeit oder Alter.

> [!div class="mx-imgBorder"]
> ![Health NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Beziehungsextrahierung](#tab/relation-extraction)

Die Beziehungsextrahierung identifiziert sinnvolle Verbindungen zwischen Begriffen, die im Text erwähnt werden. Beispielsweise wird die Beziehung „Zeit der Erkrankung“ durch Zuordnen eines Erkrankungsnamens zu einer Zeit ermittelt, oder eine Beziehung wird durch Zuordnen einer Abkürzung mit der vollständigen Beschreibung ermittelt.  

> [!div class="mx-imgBorder"]
> ![Health RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entitätsverknüpfung](#tab/entity-linking)

Durch Entitätsverknüpfung werden verschiedene Entitäten unterschieden, indem im Text erwähnte benannte Entitäten Konzepten aus einer vordefinierten Konzeptdatenbank, einschließlich des Unified Medical Language System (UMLS), zugeordnet werden. Medizinischen Konzepten wird als zusätzliche Form der Normalisierung auch eine bevorzugte Benennung zugewiesen.

> [!div class="mx-imgBorder"]
> ![Health EL](../media/ta-for-health/health-entity-linking.png)

Text Analytics for Health unterstützt die Verknüpfung mit den Vokabularen für Gesundheit und Biomedizin in der Metathesaurus Knowledge Source des Unified Medical Language System ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)).

### <a name="assertion-detection"></a>[Assertionserkennung](#tab/assertion-detection) 

Die Bedeutung medizinischer Inhalte wird in hohem Maße durch Modifizierer beeinflusst, z. B. negative oder bedingte Assertionen, die bei Fehldarstellung kritische Auswirkungen haben können. Text Analytics for Health unterstützt drei Kategorien der Assertionserkennung für Entitäten im Text: 

* Sicherheit
* Bedingte
* Korrelation

> [!div class="mx-imgBorder"]
> ![Health NEG](../media/ta-for-health/assertions.png)

---

Die vollständige Liste der unterstützten Entitäten finden Sie in den von Text Analytics for Health zurückgegeben [Entitätskategorien](../named-entity-types.md?tabs=health). Informationen zu Zuverlässigkeitsbewertungen finden Sie unter dem [Hinweis zur Transparenz der Textanalyse](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

### <a name="supported-languages"></a>Unterstützte Sprachen

Text Analytics for Health unterstützt nur Dokumente in englischer Sprache. 

## <a name="using-the-docker-container"></a>Verwenden des Docker-Containers 

Wenn Sie den Text Analytics for Health-Container in Ihrer eigenen Umgebung ausführen möchten, folgen Sie diesen [Anleitungen zum Herunterladen und Installieren des Containers](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Verwenden der Clientbibliothek

Die neueste Vorabversion der Text Analytics-Clientbibliothek ermöglicht es Ihnen, Text Analytics for Health mithilfe eines Clientobjekts aufzurufen. Weitere Informationen finden Sie in der Referenzdokumentation und in den Beispielen auf GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Senden einer REST-API-Anforderung 

### <a name="preparation"></a>Vorbereitung

Sie benötigen JSON-Dokumente im folgenden Format: ID, Text und Sprache. 

Ein Dokument darf maximal 5.120 Zeichen enthalten. Die maximal zulässige Anzahl von Dokumenten in einer Sammlung finden Sie im Artikel [Datengrenzwerte und Ratenbegrenzungen für die Textanalyse-API](../concepts/data-limits.md?tabs=version-3) unter „Konzepte“. Die Sammlung wird im Hauptteil der Anforderung übermittelt. Wenn Ihr Text diesen Grenzwert überschreitet, können Sie ihn ggf. in separate Anforderungen aufteilen. Unterteilen Sie Text in die einzelnen Sätze, aus denen er besteht, um optimale Ergebnisse zu erzielen.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Strukturieren der API-Anforderung für die gehostete asynchrone Web-API

Sowohl für den Container als auch die gehostete Web-API müssen Sie eine POST-Anforderung erstellen. Sie können mithilfe von [Postman](text-analytics-how-to-call-api.md), einem cURL-Befehl oder der **API-Testkonsole** in der [Referenz zur gehosteten API für Text Analytics for Health](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Health) schnell eine POST-Anforderung erstellen und an die gehostete Web-API in Ihrer gewünschten Region senden. Auf dem API-Endpunkt (v3.1) kann der boolesche Abfrageparameter `loggingOptOut` verwendet werden, um die Protokollierung zu Problembehandlungszwecken zu aktivieren.  Der Standardwert ist TRUE, wenn er in der Anforderungsabfrage nicht angegeben ist.

Senden Sie die POST-Anforderung an `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/health/jobs`. Nachstehend finden Sie ein Beispiel für eine JSON-Datei, die an den POST-Text der „Textanalyse für Gesundheit“-API-Anforderung angefügt ist:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Antwort der gehosteten asynchronen Web-API 

Da mithilfe dieser POST-Anforderung ein Auftrag für den asynchronen Vorgang übermittelt wird, enthält das Antwortobjekt keinen Text.  Sie benötigen jedoch den Wert des „operation-location“-Schlüssels in den Antwortheadern, um eine GET-Anforderung zum Überprüfen des Auftragsstatus und der Ausgabe zu erstellen.  Nachstehend finden Sie ein Beispiel für den Wert des „operation-location“-Schlüssels im Antwortheader der POST-Anforderung:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/health/jobs/<jobID>`

Wenn Sie den Auftragsstatus überprüfen möchten, senden Sie eine GET-Anforderung an die URL im Wert für den „operation-location“-Schlüsselheader der POST-Antwort.  Mithilfe der folgenden Status können Sie den Status eines Auftrag anzeigen: `NotStarted`, `running`, `succeeded`, `failed`, `rejected`, `cancelling` und `cancelled`.  

Sie können einen Auftrag mit dem Status `NotStarted` oder `running` mit einem HTTP-Aufruf „DELETE“ in derselben URL abbrechen wie die GET-Anforderung.  Weitere Informationen zum DELETE-Aufruf finden Sie in der [Referenz zur gehosteten API für Text Analytics for Health](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/CancelHealthJob).

Das nachstehende Beispiel zeigt die Antwort auf eine GET-Anforderung.  Die Ausgabe steht zum Abrufen zur Verfügung, bis `expirationDateTime` (24 Stunden ab der Uhrzeit, zu der der Auftrag erstellt wurde) nach Bereinigung der Ausgabe verstrichen ist.

```json
{
    "jobId": "69081148-055b-4f92-977d-115df343de69",
    "lastUpdateDateTime": "2021-07-06T19:06:03Z",
    "createdDateTime": "2021-07-06T19:05:41Z",
    "expirationDateTime": "2021-07-07T19:05:41Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 0.99
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.98
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-05-15"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Strukturieren der API-Anforderung für den Container

Sie können [mithilfe von Postman](text-analytics-how-to-call-api.md) oder der unten aufgeführten cURL-Beispielanforderung eine Abfrage an den von Ihnen bereitgestellten Container senden und darin die Variable `serverURL` durch den geeigneten Wert ersetzen.  Beachten Sie, dass die Version der API in der URL für den Container anders als diejenige der gehosteten API ist.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Der folgende JSON-Code ist ein Beispiel für eine JSON-Datei, die an den POST-Text der Text Analytics for Health-API-Anforderung angefügt ist:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Container-Antworttext

Der folgende JSON-Code ist ein Beispiel für den Text Analytics for Health-API-Antworttext aus dem synchronen containerisierten Aufruf:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>Assertionsausgabe

Text Analytics for Health gibt Assertionsmodifizierer zurück. Dies sind Informationsattribute, die medizinischen Konzepten zugewiesen sind und das Verständnis des Kontexts der Konzepte im Text vertiefen. Diese Modifizierer werden in drei Kategorien unterteilt, die jeweils einen anderen Aspekt betreffen und einen Satz von sich gegenseitig ausschließenden Werten enthalten. Jeder Entität wird nur ein Wert pro Kategorie zugewiesen. Der häufigste Wert für jede Kategorie ist der Standardwert. Die vom Dienst ausgegebene Antwort enthält nur Assertionsmodifizierer, die sich vom Standardwert unterscheiden.

**CERTAINTY**: Gibt an, ob das Konzept vorhanden ist. Gibt außerdem den Grad der Gewissheit im Text in Bezug auf das Vorhandensein des Konzepts (eindeutig oder möglich) an.
*   **Positive** [Standard]: Das Konzept ist vorhanden oder ist aufgetreten.
* **Negative**: Das Konzept ist jetzt nicht vorhanden oder ist nie aufgetreten.
* **Positive_Possible**: Das Konzept ist wahrscheinlich vorhanden, aber es gibt einen bestimmten Grad an Ungewissheit.
* **Negative_Possible**: Das Konzept ist wahrscheinlich nicht vorhanden, aber es gibt einen bestimmten Grad an Ungewissheit.
* **Neutral_Possible**: Das Konzept ist vorhanden oder nicht vorhanden, mit dem gleichen Grad an Gewissheit für beide Möglichkeiten.

**CONDITIONALITY**: Gibt an, ob das Vorhandensein eines Konzepts von bestimmten Bedingungen abhängt. 
*   **None** [Standard]: Das Konzept ist ein Fakt und weder hypothetisch noch von bestimmten Bedingungen abhängig.
*   **Hypothetical**: Das Konzept kann in Zukunft entstehen oder auftreten.
*   **Conditional**: Das Konzept ist vorhanden oder tritt nur unter bestimmten Bedingungen auf.

**ASSOCIATION**: Gibt an, ob das Konzept der im Text beschriebenen Person oder einer anderen Person zugeordnet ist.
*   **Subject** [Standard]: Das Konzept ist der im Text beschriebenen Person (in der Regel der Patient) zugeordnet.
*   **Someone_Else**: Das Konzept ist einer anderen als der im Text beschriebenen Person zugeordnet.


Die Assertionserkennung stellt negierte Entitäten als negativen Wert für die Kategorie „certainty“ dar, wie im folgenden Beispiel:

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty": "negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id": "0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>Ausgabe der Beziehungsextraktion

Text Analytics for Health erkennt die Beziehungen zwischen unterschiedlichen Konzepten, einschließlich der Beziehungen zwischen Attribut und Entität (z. B. Informationen zur Körperstruktur, Medikamentendosierung) und zwischen Entitäten (z. B. Abkürzungserkennung).

**ABBREVIATION**

**BODY_SITE_OF_CONDITION**

**BODY_SITE_OF_TREATMENT**

**COURSE_OF_CONDITION**

**COURSE_OF_EXAMINATION**

**COURSE_OF_MEDICATION**

**COURSE_OF_TREATMENT**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**EXAMINATION_FINDS_CONDITION**

**EXPRESSION_OF_GENE**

**EXPRESSION_OF_VARIANT**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_CONDITION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**MUTATION_TYPE_OF_GENE**

**MUTATION_TYPE_OF_VARIANT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**SCALE_OF_CONDITION**

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

**VARIANT_OF_GENE**

> [!NOTE]
> * Beziehungen, die auf „CONDITION“ verweisen, verweisen auf den Entitätstyp „DIAGNOSIS“ oder den Entitätstyp „SYMPTOM_OR_SIGN“.
> * Beziehungen, die auf „MEDICATION“ verweisen, verweisen auf den Entitätstyp „MEDICATION_NAME“ oder den Entitätstyp „SYMPTOM_OR_SIGN“.
> * Beziehungen, die auf „TIME“ verweisen, verweisen auf den Entitätstyp „TIME“ oder den Entitätstyp „DATE“.

Die Ausgabe der Beziehungsextraktion enthält URI-Verweise und zugewiesene Rollen der Entitäten des Beziehungstyps. Beispiel:

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>Weitere Informationen

* [Übersicht über die Textanalyse](../overview.md)
* [Kategorien benannter Entitäten](../named-entity-types.md)
* [Neuigkeiten](../whats-new.md)
