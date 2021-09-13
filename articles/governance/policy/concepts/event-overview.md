---
title: Reagieren auf Azure Policy-Ereignisse vom Typ „Richtlinienzustandsänderung“
description: Verwenden Sie Azure Event Grid, um App Policy-Ereignisse zu abonnieren, mit denen Anwendungen auf Zustandsänderungen reagieren können, ohne dass komplizierter Code erforderlich ist.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 4cd443882b3d1f9ffdcf0c317b6012654e0780c2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351520"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Reagieren auf Azure Policy-Ereignisse vom Typ „Richtlinienzustandsänderung“

Mit Azure Policy Ereignissen können Anwendungen auf Statusänderungen reagieren. Diese Integration erfolgt ohne komplizierten Code oder teure und ineffiziente Abruf-Dienste. Stattdessen werden Ereignisse über [Azure Event Grid](../../../event-grid/index.yml) an Abonnenten wie [Azure Functions](../../../azure-functions/index.yml), [Azure Logic Apps](../../../logic-apps/index.yml) oder sogar Ihren eigenen benutzerdefinierten HTTP-Listener gepusht.
Vor allem bezahlen Sie nur für die tatsächliche Nutzung.

Azure Richtlinie-Ereignisse werden an den Azure Event Grid-Dienst gesendet, der dank umfangreicher Wiederholungsrichtlinien und der Zustellung unzustellbarer Nachrichten zuverlässige Zustelldienste für Ihre Anwendungen bietet. Weitere Informationen finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](../../../event-grid/delivery-and-retry.md).

Das übliche Azure-Richtlinien-Ereignisszenario ist die Verfolgung, wenn sich der Konformitätsstatus einer Ressource während der Richtlinienbewertung ändert. Die ereignisbasierte Architektur ist eine effiziente Möglichkeit, auf diese Änderungen zu reagieren, anstatt den Konformitäts Zustand von Ressourcen nach einem festgelegten Zeitplan zu überprüfen.

> [!NOTE]
> Azure-Richtlinien-Zustandsänderungsereignisse werden an das Event Grid gesendet, nachdem ein [Bewertungsauslöser](../how-to/get-compliance-data.md#evaluation-triggers) die Ressourcenbewertung abgeschlossen hat.

Ein vollständiges Tutorial finden [Sie unter Ereignisse zur Änderung des Routenrichtlinienstatus an Event Grid mit Azure CLI](../tutorials/route-state-change-events.md) .

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="Event Grid-Modell von Quellen und Handlern" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>Verfügbare Azure Policy-Ereignisse

Event Grid verwendet [Ereignisabonnements](../../../event-grid/concepts.md#event-subscriptions) zum Weiterleiten von Ereignisnachrichten an Abonnenten. Azure Policy-Ereignisabonnements können drei Arten von Ereignissen enthalten:

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.PolicyInsights.PolicyStateCreated | Wird ausgelöst, wenn ein Zustand der Richtlinienkonformität erstellt wird. |
| Microsoft.PolicyInsights.PolicyStateChanged | Wird ausgelöst, wenn ein Status der Richtlinienkonformität geändert wird. |
| Microsoft.PolicyInsights.PolicyStateDeleted | Wird ausgelöst, wenn ein Status der Richtlinienkonformität gelöscht wird. |

## <a name="event-schema"></a>Ereignisschema

Azure-Richtlinienereignisse enthalten alle Informationen, die Sie benötigen, um auf Änderungen an Ihren Daten zu reagieren. Sie können ein Azure Richtlinien-Ereignis identifizieren, `eventType` wenn die Eigenschaft mit „Microsoft.PolicyInsights“ beginnt.
Weitere Informationen zur Verwendung von Event Grid-Ereigniseigenschaften finden Sie unter [Event Grid-Ereignisschema](../../../event-grid/event-schema.md).

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| `id` | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| `topic` | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| `subject` | Zeichenfolge | Die vollständig qualifizierte ID der Ressource, für die die Änderung des Konformitätsstatus gilt, einschließlich des Ressourcennamens und des Ressourcentyps. Verwendet das Format, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `data` | Objekt | Azure Policy-Ereignisdaten. |
| `data.timestamp` | Zeichenfolge | Die Zeit (in UTC), zu der die Ressource von Azure Policy gescannt wurde. Verwenden Sie zum Sortieren von Ereignissen diese Eigenschaft anstelle der Eigenschaften der obersten Ebene `eventTime` oder `time`. |
| `data.policyAssignmentId` | Zeichenfolge | Die Ressourcen-ID der Richtlinienzuweisung. |
| `data.policyDefinitionId` | Zeichenfolge | Die Ressourcen-ID der Richtliniendefinition. |
| `data.policyDefinitionReferenceId` | Zeichenfolge | Die Verweis-ID für die Richtliniendefinition innerhalb der Initiativendefinition, wenn die Richtlinienzuweisung für eine Initiative vorgesehen ist. Kann leer sein. |
| `data.complianceState` | Zeichenfolge | Der Konformitätszustand der Ressource in Bezug auf die Richtlinienzuweisung. |
| `data.subscriptionId` | Zeichenfolge | Die Abonnement-ID der Ressource. |
| `data.complianceReasonCode` | Zeichenfolge | Der Konformitätsursachencode. Kann leer sein. |
| `eventType` | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| `eventTime` | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| `dataVersion` | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| `metadataVersion` | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Im folgenden finden Sie ein Beispiel für ein Änderungs Ereignis für den Richtlinienstatus:

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

Weitere Informationen finden Sie unter [Azure Policy-Ereignisschema](../../../event-grid/event-schema-policy.md).

## <a name="practices-for-consuming-events"></a>Methoden zum Nutzen von Ereignissen

Anwendungen, die Azure-Richtlinienereignisse verarbeiten, sollten diese empfohlenen Praktiken befolgen:

> [!div class="checklist"]
> - Mehrere Abonnements können zum Weiterleiten von Ereignissen an denselben Ereignishandler konfiguriert werden. Gehen Sie daher nicht davon aus, dass Ereignisse aus einer bestimmten Quelle stammen. Überprüfen Sie stattdessen das Thema der Nachricht, um sicherzustellen, dass die Richtlinienzuweisung, die Richtliniendefinition und die Ressource, für die das Zustandsänderungsereignis gilt, bekannt sind.
> - Überprüfen Sie `eventType` und gehen Sie nicht davon aus, dass alle Ereignisse, die Sie erhalten, die Typen sind, die Sie erwarten.
> - Verwenden `data.timestamp` Sie, um die Reihenfolge der Ereignisse in Azure Policy `eventTime` anstelle `time` der Eigenschaften der obersten Ebene zu bestimmen.
> - Verwenden Sie das Betreff-Feld, um auf die Ressource zuzugreifen, die eine Änderung des Richtlinienstatus hatte.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Event Grid und probieren Sie Ereignisse der Azure Policy Zustandsänderungen aus:

- [Weiterleiten von Ereignissen vom Typ „Richtlinienzustandsänderung“ an Event Grid mithilfe der Azure CLI](../tutorials/route-state-change-events.md)
- [Azure Policy als Event Grid-Quelle](../../../event-grid/event-schema-policy.md)
- [Einführung in Azure Event Grid](../../../event-grid/overview.md)
