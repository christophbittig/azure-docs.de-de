---
title: Gerätezuordnungen in IoT Connector – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie Gerätezuordnungsvorlagen mit Azure Healthcare-APIs konfigurieren und IoT Connector.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 4b86742fb3bb5cc5c642b4d97b96c6aebc7c887c
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936578"
---
# <a name="how-to-use-device-mappings"></a>Verwenden von Gerätezuordnungen

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

> [!TIP]
> Sehen Sie sich das [IoMT Connector Data Mapper-Tool zum](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) Bearbeiten, Testen und Behandeln von Problemen bei Der IoT-Connector geräte- und FHIR-Zielzuordnungen an. Exportieren Sie Zuordnungen zum Hochladen in den IoT-Connector im Azure-Portal oder verwenden Sie sie mit der [Open-Source-Version des](https://github.com/microsoft/iomt-fhir) IoT-Connectors.

In diesem Artikel wird beschrieben, wie Sie den IoT-Connector mithilfe von Gerätezuordnungen konfigurieren.

Der IoT-Connector erfordert zwei Arten von JSON-basierten Zuordnungen. Der erste heißt **Gerätezuordnung** und ist dafür zuständig, die an den Azure Event Hubs-Endpunkt `devicedata` gesendeten Gerätenutzlasten zuzuordnen. Mit dieser Art von Vorlage werden Typen, Gerätebezeichner, gemessene Zeitdaten sowie Messwerte extrahiert. 

Der zweite Typ, **Fast Healthcare Interoperability Resources (FHIR&#174;),** steuert die Zuordnung für die FHIR-Ressource. Mit dieser Vorlagenart können Sie die Länge des Beobachtungszeitraums, den zum Speichern der Werte verwendeten FHIR-Datentyp sowie die Terminologiecodes konfigurieren. 

Die beiden Zuordnungstypen werden basierend auf ihrem Typ zu einem JSON-Dokument zusammengesetzt. Diese JSON-Dokumente werden ihrem IoT-Connector dann über die Azure-Portal. Das Dokument Gerätezuordnung wird über die Seite **Gerätezuordnung** und das FHIR-Zielzuordnungsdokument über die **Seite Ziel** hinzugefügt.

> [!NOTE]
> Zuordnungen werden in einem zugrunde liegenden Blobspeicher gespeichert und pro Computeausführung aus dem Blob geladen. Nach dem Update sollten sie sofort wirksam werden. 

## <a name="device-mappings-overview"></a>Übersicht über Gerätezuordnungen

Gerätezuordnungen bieten Funktionen zum Extrahieren von Gerätenachrichteninhalten in ein allgemeines Format für die weitere Auswertung. Jede empfangene Gerätenachricht wird anhand aller Gerätezuordnungsvorlagen ausgewertet. 

Eine einzelne eingehende Gerätenachricht kann in mehrere ausgehende Nachrichten getrennt werden, die später verschiedenen Beobachtungen im FHIR-Dienst zugeordnet werden. 

Das Ergebnis ist ein normalisiertes Datenobjekt, das den Wert oder die Werte darstellt, die von den Vorlagen analysiert werden. 

Das normalisierte Datenmodell verfügt über einige erforderliche Eigenschaften, die gefunden und extrahiert werden müssen:

|Eigenschaft|Beschreibung|
|--------|-----------|
|**Type**|Der Name/Typ, der die Messung klassifiziert. Dieser Wert wird verwendet, um eine Bindung an die erforderliche FHIR-Zielzuordnung zu erstellen. Mehrere Zuordnungen können an denselben Typ ausgegeben werden, sodass Sie verschiedene Darstellungen auf mehreren Geräten einer einzelnen gemeinsamen Ausgabe zuordnen können.|
|**OccurenceTimeUtc**|Der Zeitpunkt, zu dem die Messung vorgenommen wurde|
|**DeviceId**|Der Bezeichner für das Gerät. Dieser Wert sollte mit einem Bezeichner für die Geräteressource übereinstimmen, die im Ziel-FHIR-Dienst vorhanden ist.|
|**Eigenschaften**|Extrahieren Sie mindestens eine Eigenschaft, damit der Wert in der erstellten Observation-Ressource gespeichert werden kann. Eigenschaften sind eine Sammlung von Schlüssel-Wert-Paaren, die bei der Normalisierung extrahiert wurden.|

> [!IMPORTANT]
> Das vollständige normalisierte Modell wird von der [IMeasurement-Schnittstelle](https://github.com/microsoft/iomt-fhir/blob/master/src/lib/Microsoft.Health.Fhir.Ingest.Schema/IMeasurement.cs) definiert.

Im Folgenden finden Sie konzeptionelle Beispiele dafür, was während der Normalisierung und des Transformationsprozesses innerhalb des IoT-Connectors geschieht:

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="IoT-Datennormalisierungsfluss – Beispiel1" lightbox="media/iot-data-normalization-high-level.png":::

:::image type="content" source="media/concepts-iot-mapping-templates/normalization-example.png" alt-text="IoT-Datennormalisierungsfluss – Beispiel2" lightbox="media/concepts-iot-mapping-templates/normalization-example.png":::

Die Inhaltsnutzlast selbst ist eine Azure Event Hubs-Nachricht, die aus drei Teilen besteht: Body, Properties und SystemProperties. `Body` besteht aus einem Bytearray, das eine UTF-8-codierte Zeichenfolge darstellt. Während der Vorlagenauswertung wird das Bytearray automatisch in den Zeichenfolgenwert konvertiert. `Properties` ist eine Schlüssel-Wert-Sammlung, die vom Nachrichtenersteller verwendet wird. `SystemProperties` ist ebenfalls eine Schlüssel-Wert-Sammlung, die vom Azure Event Hubs-Framework reserviert wird und deren Einträge automatisch aufgefüllt werden.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
## <a name="mapping-with-jsonpath"></a>Zuordnung mit JSONPath

Die fünf derzeit unterstützten Geräteinhaltszuordnungstypen basieren auf JSONPath, um beide mit den erforderlichen Zuordnungs- und extrahierten Werten zu übereinstimmen. Weitere Informationen zu JSONPath finden Sie [hier.](https://goessner.net/articles/JsonPath/) Alle fünf Vorlagentypen verwenden die [JSON .NET-Implementierung zum](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) Auflösen von JSONPath-Ausdrücken.

Sie können eine oder mehrere Vorlagen in der Gerätezuordnungsvorlage definieren. Jede empfangene Event Hub-Gerätenachricht wird anhand aller Gerätezuordnungsvorlagen ausgewertet. 

Eine einzelne eingehende Gerätenachricht kann in mehrere ausgehende Nachrichten getrennt werden, die später verschiedenen Beobachtungen im FHIR-Dienst zugeordnet werden. 

Es gibt verschiedene Vorlagentypen, die beim Erstellen der Gerätezuordnungsdatei verwendet werden können.

|Name                                                                     | BESCHREIBUNG                                                                   |  
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------|
|[JsonPathContentTemplate](./how-to-use-jsonpath-content-mappings.md)     |Eine Vorlage, die das Schreiben von Ausdrücken mit JsonPath unterstützt                  
|[CollectionContentTemplate](./how-to-use-collection-content-mappings.md) |Eine Vorlage, die zum Darstellen einer Liste von Vorlagen verwendet wird, die während der Normalisierung verwendet werden.                                                            |                                                           
|[CalculatedContentTemplate](./how-to-use-calculated-functions-mappings.md)|Eine Vorlage, die das Schreiben von Ausdrücken mit einer von mehreren Ausdruckssprachen unterstützt. Unterstützt die Datentransformation über die Verwendung von JmesPath-Funktionen.|
|[IotJsonPathContentTemplate](./how-to-use-iot-jsonpath-content-mappings.md)|Eine Vorlage, die von Azure Iot Hub gesendete Nachrichten oder das Legacyfeature "Daten exportieren" von Azure Iot Central unterstützt.|
|[IotCentralJsonPathContentTemplate](./how-to-use-iot-central-json-content-mappings.md)|Eine Vorlage, die Nachrichten unterstützt, die über das Feature "Daten exportieren" von Azure Iot Central gesendet werden.| 

> [!TIP]
> Informationen zur Behebung häufiger Fehler und [Probleme finden](./iot-troubleshoot-guide.md) Sie im Leitfaden zur Problembehandlung für IoT-Connectors. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Gerätezuordnungen verwenden. Informationen zur Verwendung von FHIR-Zielzuordnungen finden Sie unter

>[!div class="nextstepaction"]
>[Verwenden von FHIR-Zielzuordnungen](how-to-use-fhir-mappings.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
