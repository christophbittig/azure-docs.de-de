---
title: Einen Job klassifizieren
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDKs verwenden, um die Eigenschaften eines Jobs zu ändern
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 0648334ef4baef6c753afa222a5532356ad67590
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075037"
---
# <a name="classifying-a-job"></a>Klassifizierung eines Jobs

Lernen Sie, eine Klassifizierungsrichtlinie in Job Router zu verwenden, um die Warteschlange und die Priorität dynamisch aufzulösen und gleichzeitig Worker-Selektoren an einen Job anzuhängen.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Optional: Schließen Sie den Schnellstart ab, um [mit Job Router loszulegen](../../quickstarts/router/get-started-router.md)

## <a name="static-classification"></a>Statische Klassifizierung

Wenn Sie einen Job mit dem SDK erstellen, geben Sie nur die Selektoren für Warteschlange, Priorität und Arbeiter an; diese Methode wird als **statische Klassifizierung** bezeichnet. Das folgende Beispiel würde einen Job in der `XBOX_DEFAULT_QUEUE` mit einer Priorität von `1` platzieren und verlangen, dass die Arbeiter eine Fähigkeit von `XBOX_Hardware` größer oder gleich `5` haben.

> [!NOTE]
> Ein Job kann [nach der Übermittlung neu klassifiziert werden](#reclassify-a-job-after-submission), auch wenn er ursprünglich ohne Klassifizierungsrichtlinie erstellt wurde. In diesem Fall bewertet Job Router das Verhalten der Richtlinie anhand der **Labels** und nimmt die notwendigen Anpassungen an der Warteschlange, der Priorität und den Worker-Selektoren vor.

```csharp
var job = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    channelReference: "12345",
    queueId: queue.Value.Id,
    priority: 1,
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "Location",
            @operator: LabelOperator.Equal,
            value: "Edmonton")
    });

// returns a new GUID such as: 4ad7f4b9-a0ff-458d-b3ec-9f84be26012b
```

## <a name="dynamic-classification"></a>Dynamische Klassifizierung

Wie oben beschrieben, besteht eine einfache Möglichkeit, einen Job einzureichen, darin, die Prioritäts-, Warteschlangen- und Worker-Selektoren bei der Einreichung anzugeben. Dabei muss der Absender über diese Eigenschaften Bescheid wissen. Um zu vermeiden, dass der Absender explizite Kenntnisse über das Innenleben des Job Routers hat, kann der Absender eine **Klassifizierungsrichtlinie** zusammen mit einer generischen **Labels**-Sammlung angeben, um das dynamische Verhalten aufzurufen.

### <a name="create-a-classification-policy"></a>Erstellen einer Klassifizierungsrichtlinie

Die folgende Klassifizierungsrichtlinie verwendet die Low-Code-Sprache [PowerFx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/), um sowohl die Warteschlange als auch die Priorität auszuwählen. Der Ausdruck versucht, das Job-Label mit der Bezeichnung `Region` gleich `NA` zu finden, was dazu führt, dass der Job in die Warteschlange `XBOX_NA_QUEUE` eingeordnet wird, wenn er gefunden wird, andernfalls in die Warteschlange `XBOX_DEFAULT_QUEUE`.  Wurde auch `XBOX_DEFAULT_QUEUE` nicht gefunden, wird der Auftrag automatisch in die durch `fallbackQueueId` definierte Ausweichwarteschlange `DEFAULT_QUEUE` gestellt.  Zusätzlich wird die Priorität `10` sein, wenn ein Label namens `Hardware_VIP` gefunden wurde, andernfalls ist es `1`.

```csharp
var policy = await client.SetClassificationPolicyAsync(
    id: "XBOX_NA_QUEUE_Priority_1_10",
    name: "Select XBOX Queue and set priority to 1 or 10",
    queueSelector: new QueueIdSelector(
        new ExpressionRule("If(job.Region = \"NA\", \"XBOX_NA_QUEUE\", \"XBOX_DEFAULT_QUEUE\")")
    ),
    workerSelectors: new List<LabelSelectorAttachment>
    {
        new StaticLabelSelector(
            new LabelSelector(
                key: "Language",
                @operator: LabelOperator.Equal,
                value: "English")
        )
    },
    prioritizationRule: new ExpressionRule("If(job.Hardware_VIP = true, 10, 1)"),
    fallbackQueueId: "DEFAULT_QUEUE"
);
```

### <a name="submit-the-job"></a>Übermitteln des Auftrags

Das folgende Beispiel veranlasst die Klassifizierungsrichtlinie, die Job-Labels zu bewerten. Das Ergebnis ist, dass der Auftrag in die Warteschlange mit dem Namen `XBOX_NA_QUEUE` gestellt und die Priorität auf `1` gesetzt wird.

```csharp
var dynamicJob = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    channelReference: "my_custom_reference_number",
    classificationPolicyId: "XBOX_NA_QUEUE_Priority_1_10",
    labels: new LabelCollection()
    {
        { "Region", "NA" },
        { "Caller_Id", "tel:7805551212" },
        { "Caller_NPA_NXX", "780555" },
        { "XBOX_Hardware", 7 }
    }
);

// returns a new GUID such as: 4ad7f4b9-a0ff-458d-b3ec-9f84be26012b
```

## <a name="reclassify-a-job-after-submission"></a>Neuklassifizierung eines Auftrags nach der Übermittlung

Nachdem der Job Router einen Auftrag empfangen und mit Hilfe einer Richtlinie klassifiziert hat, können Sie ihn mit Hilfe des SDK neu klassifizieren. Das folgende Beispiel zeigt eine Möglichkeit, die Priorität des Auftrags auf `10` zu erhöhen, indem Sie einfach die **Job-ID** angeben, die `ReclassifyJobAsync`-Methode aufrufen und das `Hardware_VIP`-Label einfügen.

```csharp
var reclassifiedJob = await client.ReclassifyJobAsync(
    jobId: "4ad7f4b9-a0ff-458d-b3ec-9f84be26012b",
    classificationPolicyId: null,
    labelsToUpdate: new LabelCollection()
    {
        { "Hardware_VIP", true }
    }
);
```
