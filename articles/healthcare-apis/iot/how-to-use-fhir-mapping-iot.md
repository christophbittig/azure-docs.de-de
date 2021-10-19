---
title: FHIR-Zielzuordnung im IoT-Connector – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie die FHIR-Zielzuordnungsvorlage im IoT-Connector verwenden.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: jasteppe
ms.openlocfilehash: b04d746553a36eb05a7e9cc6a346ed27d6c46308
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992959"
---
# <a name="how-to-use-the-fhir-destination-mapping"></a>Verwenden der FHIR-Zielzuordnung

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel wird beschrieben, wie Sie den IoT-Connector mithilfe der Zielzuordnung Fast Healthcare Interoperability Resources (FHIR&#174;) konfigurieren.

## <a name="fhir-destination-mapping"></a>FHIR-Zielzuordnung

Nachdem der Geräteinhalt in ein normalisiertes Modell extrahiert wurde, werden die Daten dem Gerätebezeichner, dem Messtyp und dem Zeitraum entsprechend gesammelt und gruppiert. Die Ausgabe dieser Gruppierung wird zwecks Konvertierung in eine FHIR-Ressource (derzeit [Observation](https://www.hl7.org/fhir/observation.html)) gesendet. Die FHIR-Zielzuordnungsvorlage steuert, wie die Daten einer FHIR-Beobachtung zugeordnet werden. Sollte eine Observation-Ressource für einen bestimmten Zeitpunkt oder über einen Zeitraum von einer Stunde erstellt werden? Welche Codes sollen der Ressource hinzugefügt werden? Soll der Wert als [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) oder [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity) dargestellt werden? Diese Datentypen sind alle Optionen, die von den Konfigurationssteuerelementen für die FHIR-Zielzuordnung verwendet werden.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate

CodeValueFhirTemplate ist derzeit die einzige Vorlage, die derzeit in der FHIR-Zielzuordnung unterstützt wird.  Damit können Sie Codes, den tatsächlichen Zeitraum und den Beobachtungswert definieren. Mehrere Werttypen werden unterstützt: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept) und [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). Neben diesen konfigurierbaren Werten werden der Bezeichner für die Observation-Ressource und die Verknüpfung mit den richtigen Device- und Patient-Ressourcen automatisch verarbeitet.

| Eigenschaft | BESCHREIBUNG 
| --- | ---
|**TypeName**| Der Typ der Messung, an die diese Vorlage gebunden werden soll. Es muss mindestens eine Gerätezuordnungsvorlage vorhanden sein, die diesen Typ ausgibt.
|**PeriodInterval**|Der Zeitraum, für den die erstellte Beobachtung gelten soll. Unterstützte Werte sind 0 (sofort), 60 (eine Stunde), 1440 (ein Tag).
|**Kategorie**|Eine beliebige Anzahl von [CodeableConcept](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept)-Elementen, um den Typ der erstellten Beobachtung zu klassifizieren.
|**Codes**|Mindestens ein [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element, das auf die erstellte Beobachtung angewendet werden soll
|**Codes[].Code**|Der Code für das [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element
|**Codes[].System**|Das System für das [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element
|**Codes[].Display**|Die Anzeige des [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Elements
|**Wert**|Der Wert, der extrahiert und in der Beobachtung dargestellt werden soll. Weitere Informationen finden Sie unter [Werttypvorlagen](#value-type-templates).
|**Komponenten**|*Optional:* Mindestens eine Komponente, die bei der Beobachtung erstellt werden soll
|**Components[].Codes**|Mindestens ein [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element, das auf die Komponente angewendet werden soll
|**Components[].Value**|Der Wert, der extrahiert und in der Komponente dargestellt werden soll. Weitere Informationen finden Sie unter [Werttypvorlagen](#value-type-templates).

### <a name="value-type-templates"></a>Werttypvorlagen 

Im Folgenden finden Sie die derzeit unterstützten Werttypvorlagen:

#### <a name="sampleddata"></a>SampledData

Stellt den FHIR-Datentyp [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) dar. Beobachtungsmessungen werden in einen Wertstream geschrieben, der zu einem bestimmten Zeitpunkt beginnt und mithilfe des definierten Zeitraums vorwärts erhöht wird. Wenn kein Wert vorhanden ist, wird ein `E` in den Datenstrom geschrieben. Wenn aufgrund des Zeitraums zwei weitere Werte die gleiche Position im Datenstrom belegen, wird der neuere Wert herangezogen. Die gleiche Logik kommt zum Einsatz, wenn eine SampledData verwendende Beobachtung aktualisiert wird.

| Eigenschaft | BESCHREIBUNG 
| --- | ---
|**DefaultPeriod**|Der zu verwendende Standardzeitraum in Millisekunden 
|**Einheit**|Die Einheit, die für den Ursprung von SampledData festgelegt werden soll 

#### <a name="quantity"></a>Menge

Stellt den FHIR-Datentyp [Quantity](http://hl7.org/fhir/datatypes.html#Quantity) dar. Wenn mehr als ein Wert in der Gruppierung vorhanden ist, wird nur der erste Wert verwendet. Wenn ein neuer Wert empfangen wird, der derselben Beobachtung zugeordnet ist, wird der alte Wert überschrieben.

| Eigenschaft | BESCHREIBUNG 
| --- | --- 
|**Einheit**| Darstellung der Einheit
|**Code**| Codierte Version der Einheit
|**System**| Das System, das die codierte Form definiert

### <a name="codeableconcept"></a>CodeableConcept

Stellt den FHIR-Datentyp [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) dar. Der tatsächliche Wert wird nicht verwendet.

| Eigenschaft | BESCHREIBUNG 
| --- | --- 
|**Text**|Nur-Text-Darstellung 
|**Codes**|Mindestens ein [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element, das auf die erstellte Beobachtung angewendet werden soll
|**Codes[].Code**|Der Code für das [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element
|**Codes[].System**|Das System für das [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element
|**Codes[].Display**|Die Anzeige des [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Elements

### <a name="examples"></a>Beispiele

**Heartrate – SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```

**Steps – SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```

**Blood pressure – SampledData**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```

**Blood pressure – Quantity**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```

**Device removed – CodeableConcept**

```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
>[Verwenden der Gerätezuordnung](how-to-use-device-mapping-iot.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.