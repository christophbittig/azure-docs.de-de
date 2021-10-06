---
title: Azure Kubernetes Service als Event Grid-Quelle (Vorschau)
description: In diesem Artikel wird die Verwendung von Azure Kubernetes Service als Event Grid-Ereignisquelle beschrieben. Er enthält das Schema sowie Links zu Tutorials und Artikeln mit Vorgehensweisen.
author: zr-msft
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: zarhoads
ms.openlocfilehash: 84eb67d9df14efefe753fe40da938e0a7f4f2c6c
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129423913"
---
# <a name="azure-kubernetes-service-aks-as-an-event-grid-source-preview"></a>Azure Kubernetes Service (AKS) als Event Grid-Quelle (Vorschau)

In diesem Artikel werden die Eigenschaften und das Schema für AKS-Ereignisse beschrieben.  Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md). Außerdem erhalten Sie eine Liste mit Schnellstartanleitungen und Tutorials, in denen AKS als Ereignisquelle verwendet wird.

[!INCLUDE [preview features callout](../aks/includes/preview/preview-callout.md)]

## <a name="available-event-types"></a>Verfügbare Ereignistypen

Von AKS werden folgende Ereignistypen ausgegeben:

|    Ereignistyp                                             |    BESCHREIBUNG                                                       |
|-----------------------------------------------------------|----------------------------------------------------------------------|
| Microsoft.ContainerService.NewKubernetesVersionAvailable  | Wird ausgelöst, wenn die Liste verfügbarer Kubernetes-Versionen aktualisiert wird. |

## <a name="properties-common-to-all-events"></a>Eigenschaften, die allen Ereignissen gemeinsam sind

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)
Wenn ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an den Endpunkt, der über ein entsprechendes Abonnement verfügt.
In diesem Abschnitt wird anhand eines Beispiels gezeigt, wie diese Daten für jedes Ereignis aussehen. Jedes Ereignis weist die folgenden Daten auf oberster Ebene auf:

|     Eigenschaft          |     type     |     BESCHREIBUNG                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    Zeichenfolge    |    Vollständiger Ressourcenpfad zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt.                                      |
|    `subject`            |    Zeichenfolge    |    Vom Herausgeber definierter Pfad zum Ereignisbetreff.                                                                                              |
|    `eventType`          |    Zeichenfolge    |    Einer der registrierten Ereignistypen für die Ereignisquelle.                                                                                  |
|    `eventTime`          |    Zeichenfolge    |    Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters.                                                                         |
|    `id`                 |    Zeichenfolge    |    Eindeutiger Bezeichner für das Ereignis.                                                                                                            |
|    `data`               |    Objekt (object)    |    Ereignisdaten für Blob Storage.                                                                                                                    |
|    `dataVersion`        |    Zeichenfolge    |    Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion.                                                          |
|    `metadataVersion`    |    Zeichenfolge    |    Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt.    |

# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

Wenn ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an den Endpunkt, der über ein entsprechendes Abonnement verfügt.
In diesem Abschnitt wird anhand eines Beispiels gezeigt, wie diese Daten für jedes Ereignis aussehen. Jedes Ereignis weist die folgenden Daten auf oberster Ebene auf:

|     Eigenschaft          |     type     |     BESCHREIBUNG                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    Zeichenfolge    |    Vollständiger Ressourcenpfad zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt.                                      |
|    `subject`            |    Zeichenfolge    |    Vom Herausgeber definierter Pfad zum Ereignisbetreff.                                                                                              |
|    `type`          |    Zeichenfolge    |    Einer der registrierten Ereignistypen für die Ereignisquelle.                                                                                  |
|    `time`          |    Zeichenfolge    |    Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters.                                                                         |
|    `id`                 |    Zeichenfolge    |    Eindeutiger Bezeichner für das Ereignis.                                                                                                            |
|    `data`               |    Objekt (object)    |    Ereignisdaten für Blob Storage.                                                                                                                    |
| `specversion` | Zeichenfolge | Version der CloudEvents-Schemaspezifikation. |

---

## <a name="example-events"></a>Beispielereignisse

### <a name="newkubernetesversionavailable"></a>NewKubernetesVersionAvailable

# <a name="event-grid-event-schema"></a>[Event Grid-Ereignisschema](#tab/event-grid-event-schema)

```json
{
    "topic": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "eventType": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "dataVersion": "1",
    "metadataVersion": "1",
    "eventTime": "2021-07-01T04:52:57.0000000Z"
}
```
# <a name="cloud-event-schema"></a>[Cloudereignisschema](#tab/cloud-event-schema)

```json

{
    "source": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "type": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "specversion": "1.0",
    "time": "2021-07-01T04:52:57.0000000Z"
}
```

---

Das Datenobjekt enthält die folgenden Eigenschaften:

|    Eigenschaft                        | type   | BESCHREIBUNG                                                  |
|------------------------------------|--------|--------------------------------------------------------------|
| `latestSupportedKubernetesVersion` | Zeichenfolge | Die neueste unterstützte Version von Kubernetes, die verfügbar ist.        |
| `latestStableKubernetesVersion`    | Zeichenfolge | Die neueste stabile unterstützte Version von Kubernetes, die verfügbar ist. |
| `lowestMinorKubernetesVersion`     | Zeichenfolge | Die niedrigste unterstützte Version von Kubernetes, die verfügbar ist.        |
| `latestPreviewKubernetesVersion`   | Zeichenfolge | Die neueste verfügbare Vorschauversion von Kubernetes.          |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).