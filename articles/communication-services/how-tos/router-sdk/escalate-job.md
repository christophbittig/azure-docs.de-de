---
title: Eskalieren eines Auftrags im Auftragsrouter
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden Sie Azure Communication Services SDKs, um Aufträge zu eskalieren.
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 3ed75c4e418e5e7494502e4f70d3c5419a5b162c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176033"
---
# <a name="escalate-a-job"></a>Eskalieren eines Auftrags

In diesem Leitfaden erfahren Sie, wie Sie einen Auftrag in einer Warteschlange mithilfe einer Ausnahmerichtlinie eskalieren.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Optional: Schließen Sie den Schnellstart für [die ersten Schritte mit dem Auftragsrouter](../../quickstarts/router/get-started-router.md) ab.
- Optional: Lesen Sie die the [Schrittanleitung zur Auftragsklassifizierung](job-classification.md)

## <a name="escalation-overview"></a>Eskalationsübersicht

Die Eskalation kann als verschiedene Verhaltensweisen auftreten, z. B. als das Verschieben eines Auftrags in eine andere Warteschlange und/oder das Angeben einer höheren Priorität. Aufträge mit einer höheren Priorität werden vor Aufträgen mit einer niedrigeren Priorität an Worker verteilt. In dieser Schrittanleitung wird eine Eskalationsrichtlinie und eine Klassifizierungsrichtlinie verwendet, um dieses Ziel zu erreichen.

## <a name="exception-policy-configuration"></a>Konfiguration der Ausnahmerichtlinie

Erstellen Sie eine Ausnahmerichtlinie, die Sie an die reguläre Warteschlange anfügen. Diese wird durch einen Timer ausgelöst und führt dazu, dass der Auftrag neu klassifiziert wird.

```csharp
// create the exception policy
await client.SetExceptionPolicyAsync(
    id: "Escalate_XBOX_Policy",
    name: "Add escalated label and reclassify XBOX Job requests after 5 minutes",
    rules: new List<ExceptionRule>()
    {
        new (
            id: "Escalated_Rule",
            trigger: new WaitTimeExceptionTrigger(TimeSpan.FromMinutes(5)),
            actions: new List<ExceptionAction>
            {
                new ReclassifyExceptionAction("EscalateReclassifyExceptionAction")
                {
                    LabelsToUpsert = new LabelCollection(
                        new Dictionary<string, object>
                    {
                        ["Escalated"] = true,
                    })
                }
            }
        )
    });
```

## <a name="classification-policy-configuration"></a>Konfiguration der Klassifizierungsrichtlinie

Erstellen Sie eine Klassifizierungsrichtlinie, die die neue Bezeichnung, die dem Auftrag hinzugefügt wurde, verarbeiten kann. Diese Richtlinie wertet die Bezeichnung `Escalated` aus und weist den Auftrag einer der Warteschlangen zu. Die Richtlinie verwendet auch [RulesEngine](../../concepts/router/router-rule-concepts.md), um die Priorität des Auftrags von `1` auf `10` erhöhen.

```csharp
await client.SetClassificationPolicyAsync(
    id: "Classify_XBOX_Voice_Jobs",
    name: "Classify XBOX Voice Jobs",
    queueSelector: new QueueIdSelector(
        new ExpressionRule(
            "If(job.Escalated = true, \"XBOX_Queue\", \"XBOX_Escalation_Queue\")")),
    workerSelectors: null,
    prioritizationRule: new ExpressionRule("If(job.Escalated = true, 10, 1)"),
    fallbackQueueId: "Default");
```

## <a name="queue-configuration"></a>Warteschlangenkonfiguration

Erstellen Sie die erforderlichen Warteschlangen für reguläre und eskalierte Aufträge, und weisen Sie die Ausnahmerichtlinie der regulären Warteschlange zu.

> [!NOTE]
> In diesem Schritt wird davon ausgegangen, dass Sie bereits eine Verteilungsrichtlinie mit dem Namen `Round_Robin_Policy` erstellt haben.

```csharp
// create a queue for regular Jobs and attach the exception policy
await client.SetQueueAsync(
    id: "XBOX_Queue",
    name: "XBOX Queue",
    distributionPolicyId: "Round_Robin_Policy",
    exceptionPolicyId: "XBOX_Escalate_Policy"
);

// create a queue for escalated Jobs
await client.SetQueueAsync(
    id: "XBOX_Escalation_Queue",
    name: "XBOX Escalation Queue",
    distributionPolicyId: "Round_Robin_Policy"
);
```

## <a name="job-lifecycle"></a>Auftragslebenszyklus

Wenn Sie den Auftrag übermitteln, geben Sie die Klassifizierungsrichtlinien-ID wie folgt an. In diesem speziellen Beispiel ist es erforderlich, einen Worker mit einer Bezeichnung namens `XBOX_Hardware` zu finden, deren Wert größer oder gleich der Zahl `7` ist.

```csharp
await client.CreateJobWithClassificationPolicyAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    classificationPolicyId: "Classify_XBOX_Voice_Jobs",
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "XBOX_Hardware",
            @operator: LabelOperator.GreaterThanEqual,
            value: 7)
    }
);
```

Nachdem die Konfiguration abgeschlossen ist und der Auftrag übermittelt werden kann, werden die folgenden Lebenszyklusschritte abgeschlossen:

1. Der Auftrag wird an den Auftragsrouter gesendet, und da eine Klassifizierungsrichtlinie angefügt ist, wird er ausgewertet und gibt sowohl `RouterJobReceived` als auch `RouterJobClassified` aus.
2. Als Nächstes beginnt der 5-Minuten-Timer zu laufen und wird schließlich ausgelöst, wenn kein Worker zugewiesen werden kann. Es wird angenommen, dass keine Workers registriert sind, was zu `RouterJobExceptionTriggered` und wieder zu `RouterJobClassified` führt.
3. An diesem Punkt ist der Auftrag in `XBOX_Escalation_Queue`, und die Priorität wird auf `10` festgelegt.