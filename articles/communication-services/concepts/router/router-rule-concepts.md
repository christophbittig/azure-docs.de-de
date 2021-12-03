---
title: Konzepte der Routerregel-Engine für Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über die Regel-Engines für Azure Communication Services Auftragsrouter.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5d53f2492888323107fa539283f128ed6b6ebd7c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319223"
---
# <a name="job-router-rules-engine-concepts"></a>Konzepte der Auftragsrouter-Regel-Engine

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services Auftragsrouter verwendet eine erweiterbare Regel-Engine, um Daten zu verarbeiten und Entscheidungen über Ihre Aufträge und Worker zu treffen. In diesem Dokument wird behandelt, was die Regel-Engine tut und warum Sie sie in Ihrer Implementierung anwenden möchten.

## <a name="rules-engine-overview"></a>Übersicht über Regel-Engine

Die Steuerung des Verhaltens Ihrer Implementierung kann häufig eine komplexe Entscheidungsfindung umfassen. Der Auftragsrouter bietet eine flexible Möglichkeit, das Verhalten programmgesteuert mit verschiedenen Regel-Engines aufzurufen. Die Regel-Engines des Auftragsrouters verwenden im Allgemeinen einen Satz von **Bezeichnungen,** die für Objekte wie einen Auftrag, eine Warteschlange oder einen Worker als Eingabe definiert sind, wenden die Regel an und erzeugen eine Ausgabe.

> [!NOTE]
> Obwohl die Regel-Engine in der Regel Bezeichnungen als Eingabe verwendet, kann sie auch Werte wie eine Warteschlangen-ID festlegen, ohne Bezeichnungen auszuwerten.

Je nachdem, wo Sie Regeln im Auftragsrouter anwenden, kann das Ergebnis variieren. Beispielsweise kann eine Klassifizierungsrichtlinie eine Warteschlangen-ID basierend auf den Bezeichnungen auswählen, die in der Eingabe eines Auftrags definiert sind. In einem anderen Beispiel kann eine Verteilungsrichtlinie den besten Worker mithilfe einer benutzerdefinierten Bewertungsregel finden, die von definiert `RouterRule` wird.

### <a name="example-use-a-static-rule-in-a-classification-policy-to-set-the-queue-id"></a>Beispiel: Verwenden einer statischen Regel in einer Klassifizierungsrichtlinie zum Festlegen der Warteschlangen-ID

In diesem Beispiel kann ein `StaticRule` vom Typ `RouterRule` verwendet werden, um die Warteschlangen-ID aller Aufträge festzulegen, die auf die Klassifizierungsrichtlinien-ID `XBOX_QUEUE_POLICY` verweisen.

```csharp
await client.SetClassificationPolicyAsync(
    id: "XBOX_QUEUE_POLICY",
    queueSelector: new QueueIdSelector(new StaticRule("XBOX"))
)
```
## <a name="routerrule-types"></a>RouterRule-Typen

Die folgenden `RouterRule` Typen sind im Auftragsrouter vorhanden, um Flexibilität bei der Verarbeitung Ihrer Aufträge zu bieten.

**Statische Regel:** Diese Regel kann verwendet werden, um einen statischen Wert anzugeben, z. B. das Auswählen einer bestimmten Warteschlangen-ID.

**Ausdrucksregel:** Eine Ausdrucksregel verwendet die [PowerFx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/)-Programmiersprache, um Ihre Regel als Inlineausdruck zu definieren.

**Azure-Funktionsregel:** Wenn Sie einen URI und einen `AzureFunctionRuleCredential` angeben, ermöglicht diese Regel dem Auftragsrouter, die Eingabebezeichnungen als Nutzlast zu übergeben und mit einem Ausgabewert zurückzukehren. Dieser Regeltyp kann verwendet werden, wenn Ihre Anforderungen komplex sind.

> [!NOTE]
> Obwohl die **Direct Map-Regel** Teil des Auftragsrouter-SDK ist, wird sie derzeit nur in `NearestQueueLabelSelector` unterstützt.
