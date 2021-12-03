---
title: Micro-Agent-Konfigurationen (Vorschau)
description: Der Collector sendet alle aktuellen Daten unmittelbar nach jeder Konfigurationsänderung. Die Änderungen werden dann übernommen.
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: 2db49df42644ca2ef0cff3d38768ffa6df4defae
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293609"
---
# <a name="micro-agent-configurations-preview"></a>Micro-Agent-Konfigurationen (Vorschau)

In diesem Artikel werden die verschiedenen Arten von Konfigurationen beschrieben, die der Micro-Agent unterstützt. Kunden können den Micro-Agent so konfigurieren, dass er den Anforderungen ihrer Geräte und Netzwerkumgebungen entspricht.  

Das Verhalten des Micro-Agents wird durch eine Reihe von Modulzwillingeigenschaften konfiguriert. Sie können den Micro-Agent so konfigurieren, dass er Ihren Anforderungen am besten entspricht. Beispielsweise können Sie Ereignisse automatisch ausschließen, den Energieverbrauch minimieren und die Netzwerkbandbreite reduzieren.

Nach jeder Änderung der Konfiguration sendet der Collector sofort alle nicht gesendeten Ereignisdaten. Nachdem die Daten gesendet wurden, werden die Änderungen angewendet, und alle Collectors werden neu gestartet.

> [!Note]
> Der Aggregationsmodus wird unterstützt, ist aber nicht konfigurierbar.

## <a name="event-based-collectors-configurations"></a>Konfigurationen von ereignisbasierten Collectors

Diese Konfigurationen umfassen Prozess- und Netzwerkaktivitätscollectors.

| Einstellungsname | Einstellungsoption | BESCHREIBUNG | Standardeinstellung |
|--|--|--|--|
| **Intervall** | „Hoch“, „Mittel“ oder „Niedrig“. | Definieren Sie die Häufigkeit des Sendens. | Medium |
| **Aggregationsmodus** | „True“ oder „False“ | Gibt an, ob die Ereignisaggregation für ein identisches Ereignis zu verarbeiten ist.  | True |
| **Cachegröße** | FIFO-Zyklus | Die Anzahl der zwischen den Daten gesammelten Ereignisse wird gesendet. | 256 |
| **Deaktivieren des Collectors** | „True“ oder „False“ | Gibt an, ob der Collector betriebsbereit ist. | False |

## <a name="trigger-based-collectors-configurations"></a>Konfigurationen von triggerbasierten Collectors

Diese Konfigurationen umfassen Systeminformationen und Baselinecollectors.

| Einstellungsname | Einstellungsoption | BESCHREIBUNG | Standardeinstellung |
|--|--|--|--|
| **Intervall** | „Hoch“, „Mittel“ oder „Niedrig“. | Die Häufigkeit, mit der Daten gesendet werden. | Niedrig |
| **Deaktivieren des Collectors** | „True“ oder „False“ | Gibt an, ob der Collector betriebsbereit ist. | False |

## <a name="general-configuration"></a>Allgemeine Konfiguration

Definieren Sie die Häufigkeit, mit der Nachrichten für die einzelnen Prioritätsebenen gesendet werden. Die Standardwerte sind unten aufgeführt:

| Häufigkeit | Zeitraum (in Minuten) |
|--|--|
| Niedrig | 1440 (24 Stunden) |
| Medium | 120 (2 Stunden) |
| Hoch | 30 (0,5 Stunden) |

Um die Anzahl der an die Cloud gesendeten Nachrichten zu verringern, sollte jede Priorität als ein Vielfaches der darunter liegenden Priorität festgelegt werden. Beispiel: Hoch: 60 Minuten, Mittel: 120 Minuten, Niedrig: 480 Minuten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur [Micro-Agent-Ereigniserfassung (Vorschau)](concept-event-aggregation.md).
