---
title: Digitale Zwillinge und der Zwillingsgraph
titleSuffix: Azure Digital Twins
description: Erfahren Sie mehr über digitale Zwillinge und wie ihre Beziehungen einen Graphen für digitale Zwillinge bilden.
author: baanders
ms.author: baanders
ms.date: 10/20/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 33ad5a0eb684526184affba2d254bf20f66de930
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500239"
---
# <a name="digital-twins-and-their-twin-graph"></a>Digitale Zwillinge und ihr Zwillingsgraph

In diesem Artikel wird beschrieben, was **digitale Zwillinge** im Kontext von Azure Digital Twins sind und wie Beziehungen zwischen ihnen einen **Zwillingsgraphen** bilden können. In einer Azure Digital Twins-Lösung werden die Entitäten in Ihrer Umgebung durch **digitale Zwillinge** dargestellt. Ein digitaler Zwilling ist eine Instanz eines Ihrer benutzerdefinierten [Modelle](concepts-models.md). Er kann mit anderen digitalen Zwillingen über **Beziehungen** verbunden werden, um einen **Zwillingsgraphen** zu erhalten. Dieser Zwillingsgraph ist die Darstellung Ihrer gesamten Umgebung.

> [!TIP]
> „Azure Digital Twins“ ist der Name dieses Azure-Diensts. „Digitale Zwillinge“ oder einfach „Zwillinge“ bezieht sich einzelne Zwillingsknoten auf Ihrer Instanz des Diensts.

## <a name="digital-twins"></a>Digital Twins

Bevor Sie auf Ihrer Azure Digital Twins-Instanz einen digitalen Zwilling erstellen können, müssen Sie ein *Modell* in den Dienst hochladen. Mit einem Modell wird unter anderem die Gruppe mit den Eigenschaften, Telemetrienachrichten und Beziehungen beschrieben, über die ein bestimmter Zwilling verfügen kann. Informationen zu den Informationsarten, die in einem Modell definiert sind, finden Sie unter [Benutzerdefinierte Modelle](concepts-models.md).

Nachdem Sie ein Modell erstellt und hochgeladen haben, kann Ihre Client-App eine Instanz des entsprechenden Typs erstellen. Diese Instanz ist ein digitaler Zwilling. Nachdem Sie beispielsweise ein Modell vom Typ „Floor“ (Etage) erstellt haben, können Sie einen oder mehrere digitale Zwillinge erstellen, die diesen Typ nutzen (z. B. ein Zwilling vom Typ „Floor“ mit dem Namen „GroundFloor“, einen anderen mit dem Namen „Floor2“ usw.).

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

## <a name="relationships-a-graph-of-digital-twins"></a>Beziehungen: ein Graph für digitale Zwillinge

Zwillinge werden anhand ihrer Beziehungen zu einem Zwillingsgraphen vereint. Die Beziehungen, die ein Zwilling aufweisen kann, werden als Teil des Modells definiert.  

Beispielsweise kann für das Modell „Etage“ eine Beziehung vom Typ *contains* (enthält) definiert werden, die für Zwillinge vom Typ „Room“ (Zimmer) bestimmt ist. Mit dieser Definition können Sie mit Azure Digital Twins *contains*-Beziehungen von allen Floor-Zwillingen zu allen Room-Zwillingen erstellen (einschließlich Zwillingen mit Untertypen von Room). 

Das Ergebnis dieses Prozesses ist eine Gruppe mit Knoten (die digitalen Zwillinge), die über Edges (die Beziehungen) in einem Graphen verbunden sind.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit Beispielmodellen und -zwillingen." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

## <a name="create-with-the-apis"></a>Erstellen mit den APIs

In diesem Abschnitt wird gezeigt, wie das Erstellen digitaler Zwillinge und Beziehungen über eine Clientanwendung aussieht. Es sind .NET-Codebeispiele enthalten, die die [DigitalTwins-APIs](/rest/api/digital-twins/dataplane/twins) nutzen, um mehr Kontext darüber bereitzustellen, was innerhalb der verschiedenen Konzepte passiert.

### <a name="create-digital-twins"></a>Erstellen digitaler Zwillinge

Unten sehen Sie einen Ausschnitt des Clientcodes, in dem die [DigitalTwins-APIs](/rest/api/digital-twins/dataplane/twins) zum Instanziieren eines Zwillings vom Typ „Room“ (Zimmer) mit einer bei der Instanziierung definierten `twinId` verwendet wird.

Sie können die Eigenschaften eines Zwillings bei der Erstellung initialisieren oder später festlegen. Um einen Zwilling mit initialisierten Eigenschaften zu erstellen, erstellen Sie ein JSON-Dokument, das die erforderlichen Initialisierungswerte bereitstellt.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

Sie können auch eine Hilfsklasse namens `BasicDigitalTwin` verwenden, um Eigenschaftenfelder als Alternative zur Verwendung eines Wörterbuchs direkt in einem „Zwillingsobjekt“ zu speichern. Weitere Informationen zur Hilfsklasse und Beispiele für deren Verwendung finden Sie im Abschnitt [Erstellen eines Digital Zwillings](how-to-manage-twin.md#create-a-digital-twin) von *Vorgehensweise: Verwalten digitaler Zwillinge*.

>[!NOTE]
>Obwohl Zwillingseigenschaften optional sind und nicht initialisiert werden müssen, **müssen** alle [Komponenten](concepts-models.md#elements-of-a-model) auf dem Zwilling beim Erstellen des Zwillings festgelegt werden. Es kann sich um leere Objekte handeln, aber die Komponenten selbst müssen vorhanden sein.

### <a name="create-relationships"></a>Erstellen von Beziehungen

Im Folgenden finden Sie Beispielclientcode, der die [DigitalTwins-APIs](/rest/api/digital-twins/dataplane/twins) verwendet, um eine Beziehung von einem digitalen Zwilling (dem „Quellzwilling“) zu einem anderen digitalen Zwilling (dem „Zielzwilling“) herzustellen.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

## <a name="json-representations-of-graph-elements"></a>JSON-Darstellungen von Graphelementen

Sowohl die Daten des digitalen Zwillings als auch die Beziehungsdaten werden im JSON-Format gespeichert. Das heißt, wenn Sie den [Zwillingsgraphen in Ihrer Azure Digital Twins-Instanz abfragen](how-to-query-graph.md), ist das Ergebnis eine JSON-Darstellung der von Ihnen erstellten digitalen Zwillinge und Beziehungen.

### <a name="digital-twin-json-format"></a>JSON-Format von digitalen Zwillingen

Bei der Darstellung als JSON-Objekt zeigt ein digitaler Zwilling die folgenden Felder an:

| Feldname | BESCHREIBUNG |
| --- | --- |
| `$dtId` | Eine vom Benutzer angegebene Zeichenfolge zur Darstellung der ID des digitalen Zwillings |
| `$etag` | Ein vom Webserver zugewiesenes Standard-HTTP-Feld |
| `$conformance` | Eine Enumeration mit dem Konformitätsstatus dieses digitalen Zwillings (*conformant* (konform), *non-conformant* (nicht konform), *unknown* (unbekannt)) |
| `<property-name>` | Den Wert einer Eigenschaft im JSON-Format (`string`, Zahlentyp oder Objekt) |
| `$relationships` | Die URL des Pfads zur Sammlung mit den Beziehungen. Dieses Feld ist nicht vorhanden, wenn der digitale Zwilling keine ausgehenden Beziehungsedges aufweist. |
| `$metadata.$model` | [Optional] Die ID der Modellschnittstelle, die diesen digitalen Zwilling kennzeichnet |
| `$metadata.<property-name>.desiredValue` | [Nur für schreibbare Eigenschaften] Der gewünschte Wert der angegebenen Eigenschaft |
| `$metadata.<property-name>.desiredVersion` | [Nur für schreibbare Eigenschaften] Die Version des gewünschten Werts |
| `$metadata.<property-name>.ackVersion` | Die von der Geräte-App, die den digitalen Zwilling implementiert, bestätigte Version |
| `$metadata.<property-name>.ackCode` | [Nur für schreibbare Eigenschaften] Der von der Geräte-App, die den digitalen Zwilling implementiert, zurückgegebene `ack`-Code |
| `$metadata.<property-name>.ackDescription` | [Nur für schreibbare Eigenschaften] Die von der Geräte-App, die den digitalen Zwilling implementiert, zurückgegebene `ack`-Beschreibung |
| `<component-name>` | Ein JSON-Objekt mit den Eigenschaftswerten und Metadaten der Komponente, die denen des Stammobjekts ähneln. Dieses Objekt ist auch vorhanden, wenn die Komponente keine Eigenschaften hat. |
| `<component-name>.<property-name>` | Den Wert der Komponenteneigenschaft im JSON-Format (`string`, Zahlentyp oder Objekt) |
| `<component-name>.$metadata` | Die Metadateninformationen für die Komponente, die dem Objekt `$metadata` auf der Stammebene ähneln |

Hier ist ein Beispiel für einen digitalen Zwilling angegeben, der als JSON-Objekt formatiert ist:

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Beziehung im JSON-Format

Bei der Darstellung als JSON-Objekt zeigt die Beziehung eines digitalen Zwillings die folgenden Felder an:

| Feldname | BESCHREIBUNG |
| --- | --- |
| `$relationshipId` | Eine vom Benutzer angegebene Zeichenfolge zur Darstellung der ID dieser Beziehung. Diese Zeichenfolge ist im Kontext des digitalen Quellzwillings eindeutig. Dies bedeutet, dass `sourceId` + `relationshipId` im Kontext der Azure Digital Twins-Instanz eindeutig ist. |
| `$etag` | Ein vom Webserver zugewiesenes Standard-HTTP-Feld |
| `$sourceId` | Die ID des digitalen Quellzwillings |
| `$targetId` | Die ID des digitalen Zielzwillings |
| `$relationshipName` | Den Namen der Beziehung |
| `<property-name>` | [Optional] Den Wert einer Eigenschaft dieser Beziehung im JSON-Format (`string`, Zahlentyp oder Objekt) |

Hier ist ein Beispiel für eine Beziehung angegeben, die als JSON-Objekt formatiert ist:

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie Graphelemente mit Azure Digital Twin-APIs verwalten können:
* [Verwalten digitaler Zwillinge](how-to-manage-twin.md)
* [Verwalten des Zwillingsgraphen und der Beziehungen](how-to-manage-graph.md)

Sie können sich auch damit vertraut machen, wie Sie Informationen für einen Azure Digital Twins-Zwillingsgraphen abfragen:
* [Abfragesprache](concepts-query-language.md)