---
title: Auftragsverteilungskonzepte für Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über die Klassifizierungskonzepte für Azure Communication Services Auftragsrouter.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: c31c0831f625f65075f3c1f5cde2647ebc73ce64
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308425"
---
# <a name="job-classification-concepts"></a>Konzepte der Auftragsklassifizierung

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Der Azure Communication Services Auftragsrouter verwendet einen Prozess namens **Klassifizierung**, wenn ein Auftrag übermittelt wird. In diesem Artikel werden die verschiedenen Möglichkeiten der Auftragsklassifizierung beschrieben und wie sich dieser Prozess auf sie auswirkt.

## <a name="job-classification-overview"></a>Übersicht über die Auftragsklassifizierung

Der Auftragsrouter verwendet zwei primäre Methoden zum Klassifizieren eines Auftrags: statisch oder dynamisch. Wenn die aufrufende Anwendung über Kenntnisse zu Warteschlangen-ID, Priorität oder Workerauswahl verfügt, kann der Auftrag ohne Klassifizierungsrichtlinie übermittelt werden. Wird als **statische Klassifizierung** bezeichnet. Wenn Sie es vorziehen, den Auftragsrouter über die Warteschlangen-ID entscheiden zu lassen, kann eine Klassifizierungsrichtlinie verwendet werden, um die Eigenschaften des Auftrags zu ändern. Wird als **dynamische Klassifizierung** bezeichnet.

Wenn Sie einen Auftrag mit dem Auftragsrouter SDK übermitteln, führt der Klassifizierungsprozess dazu, dass ein Ereignis an Ihr Azure Communication Services Event Grid-Abonnement gesendet wird. Die im Rahmen des Klassifizierungslebenszyklus generierten Ereignisse geben Aufschluss darüber, welche Aktionen der Auftragsrouter ausführt. Eine erfolgreiche Klassifizierung erzeugt z. B. **RouterJobClassified**, und ein Fehler erzeugt **RouterJobClassificationFailed**.

Beim Klassifizieren eines Auftrags werden optional die folgenden Eigenschaften festgelegt:

- Warteschlangen-ID
- Priorität
- Worker-Selektoren

## <a name="static-classification"></a>Statische Klassifizierung

Wenn Sie einen Auftrag mit einer vordefinierten Warteschlangen-ID, Priorität und Worker-Selektoren übermitteln, können Sie schnell beginnen. Der Auftragsrouter ändert diese Eigenschaften nach dem Übermitteln des Auftrags nur, wenn Sie ihn aktualisieren, indem Sie vor der Zuweisung zu einem Worker eine Klassifizierungsrichtlinie angeben. Sie können die Klassifizierungsrichtlinieneigenschaft eines Auftrags nach der Übermittlung aktualisieren, wodurch der dynamische Klassifizierungsprozess ausgelöst wird.

> [!NOTE]
> Sie haben die Möglichkeit, das Ergebnis der dynamischen Klassifizierung zu überschreiben, indem Sie das Auftragsrouter-SDK verwenden, um die Auftragseigenschaften manuell zu aktualisieren. Beispielsweise können Sie zunächst eine statische Warteschlangen-ID angeben, dann den Auftrag mit einer Klassifizierungsrichtlinien-ID aktualisieren, damit er dynamisch klassifiziert wird, und dann die Warteschlangen-ID überschreiben.

## <a name="dynamic-classification"></a>Dynamische Klassifizierung

Wenn Sie beim Übermitteln eines Auftrags eine Klassifizierungsrichtlinie angeben, kann der Auftragsrouter die Warteschlangen-ID und Priorität dynamisch zuweisen und möglicherweise die Worker-Selektoren ändern. Diese Art der Klassifizierung ist von Vorteil, da die aufrufende Anwendung bei Runtime keine Kenntnis der Auftragseigenschaften einschließlich der Warteschlangen-ID benötigt.

### <a name="queue-selectors"></a>Warteschlangen-Selektoren

Eine Klassifizierungsrichtlinie kann auf einen `QueueSelector` verweisen, der vom Klassifizierungsprozess verwendet wird, um zu bestimmen, welche Warteschlangen-ID für einen bestimmten Auftrag ausgewählt wird. Die folgenden `QueueSelector`-Typen sind im Auftragsrouter vorhanden und gelten für den Warteschlangenauswahlprozess während der Klassifizierung:

**QueueLabelSelector:** Wenn Sie eine Auftragsrouterwarteschlange erstellen, können Sie Bezeichnungen angeben, um den Warteschlangenauswahlprozess während der Auftragsklassifizierung zu unterstützen. Dieser Selektortyp verwendet eine Sammlung von `LabelSelectorAttachment`-Typen, um während des Klassifizierungsprozesses die größte Flexibilität bei der Auswahl der Warteschlange zu bieten. Verwenden Sie diese Auswahl, damit der Auftragsklassifizierungsprozess die Warteschlangen-ID basierend auf ihren Bezeichnungen auswählen kann. Weitere Informationen finden Sie im [untenstehenden](#using-labels-and-selectors-in-classification) Abschnitt.

**QueueIdSelector:** Dieser Selektor ermöglicht die Verwendung einer von vielen Regel-Engines, um die Warteschlangen-ID des Auftrags basierend auf dem Ergebnis der Regel zu bestimmen. Weitere Informationen finden Sie auf der Seite [RouterRule concepts (RouterRule-Konzepte).](router-rule-concepts.md)

### <a name="worker-selectors"></a>Worker-Selektoren

Eine Workerauswahl in einer Klassifizierungsrichtlinie enthält eine Auflistung von `LabelSelectorAttachment`-Typen, die vom Klassifizierungsprozess verwendet wird, um Workerselektoren basierend auf den Bezeichnungen an einen Auftrag anzufügen. Weitere Informationen finden Sie im [untenstehenden](#using-labels-and-selectors-in-classification) Abschnitt.

### <a name="prioritization-rule"></a>Priorisierungsregel

Die Priorität eines Auftrags kann während der Klassifizierung mithilfe einer von vielen Regel-Engines aufgelöst werden. Ähnelt der Funktionsweise von `QueueIdSelector`. Weitere Informationen finden Sie auf der Seite [RouterRule concepts (RouterRule-Konzepte).](router-rule-concepts.md)

## <a name="using-labels-and-selectors-in-classification"></a>Verwenden von Bezeichnungen und Selektoren in der Klassifizierung

Der Auftragsrouter verwendet das Schlüssel-Wert-Paar „Bezeichnungen“ eines Auftrags, eines Workers und einer Warteschlange, um verschiedene Entscheidungen zum Routing zu treffen. Bei Verwendung von `LabelSelectorAttachment` in einem `QueueSelector` verhält es sich wie ein Filter. Wenn sie im Kontext von `WorkerSelectors` verwendet wird, werden Selektoren an den anfänglichen Satz angefügt, der mit dem Auftrag erstellt wurde. Jeder der folgenden `LabelSelectorAttachment`-Typen kann verwendet werden:

**Statische Bezeichnungsauswahl:** Fügt immer den angegebenen `LabelSelector` an.

**Bedingte Bezeichnungsauswahl:** Wertet eine durch eine Regel definierte Bedingung aus.  Wenn sie in `true` aufgelöst wird, wird die angegebene Auflistung von Selektoren angewendet.

**Passthrough-Bezeichnungsauswahl:** Verwendet einen Schlüssel und `LabelOperator`, um zu überprüfen, ob der Schlüssel vorhanden ist. Dieser Selektor kann in `QueueLabelSelector` verwendet werden, um eine Warteschlange basierend auf dem Satz von Bezeichnungen abzugleichen. Bei Verwendung mit `WorkerSelectors` wird das Schlüssel-Wert-Paar des Auftrags an den `WorkerSelectors` des Auftrags angefügt.

**Regelbezeichnungsauswahl:** Gibt eine Sammlung von Selektoren aus einer von vielen Regel-Engines aus. Weitere Informationen finden Sie auf der Seite [RouterRule concepts (RouterRule-Konzepte).](router-rule-concepts.md)

**Bezeichnungsauswahl nach gewichteten Zuordnungen:** Eine Auflistung von `WeightedAllocation`-Objekten, die jeweils eine prozentbasierte Gewichtung und eine Auflistung von Selektoren angeben, die basierend auf der Gewichtungszuordnung angewendet werden sollen. Angenommen, Sie möchten, dass 30 % der Aufträge an „Contoso“ und 70 % der Aufträge an „Fabrikam“ gehen.

## <a name="reclassifying-a-job"></a>Erneutes Klassifizieren eines Auftrags
Nachdem ein Auftrag klassifiziert wurde, kann er auf folgende Weise neu klassifiziert werden:

1. Sie können die Auftragsbezeichnungen aktualisieren, wodurch der Auftragsrouter die neuen Bezeichnungen mit der vorherigen Klassifizierungsrichtlinie auswertet.
2. Sie können die Klassifizierungsrichtlinien-ID eines Auftrags aktualisieren, wodurch der Auftragsrouter den vorhandenen Auftrag anhand der neuen Richtlinie verarbeitet.
3. Ein Ausnahmerichtlinien-**Trigger** kann eine **Maßnahme** zur Neuklassifizierung eines Auftrags anfordern 

> [!NOTE]
> Das Auftragsrouter-SDK enthält eine `UpdateJobLabels`-Methode, die einfach die Bezeichnungen aktualisiert, ohne dass der Auftragsrouter den Neuklassifizierungsprozess ausführt.
