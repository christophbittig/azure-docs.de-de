---
title: Fehler und Aktionen im Azure Chaos Studio
description: Hier finden Sie Informationen zu Fehlern und Aktionen im Azure Chaos Studio. Was ist der Unterschied zwischen einem Fehler und einer Aktion? Wie definieren Sie einen Fehler?
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 70d212a7d9677c0068670d2954897d7e1f27b137
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372433"
---
# <a name="faults-and-actions-in-azure-chaos-studio"></a>Fehler und Aktionen im Azure Chaos Studio

Im Chaos Studio wird jede Aktivität, die ein Teil des Experimentes ist, als **Aktion** bezeichnet. Die üblichste Aktion ist ein **Fehler**. In diesem Abschnitt werden Aktionen und Fehler sowie die Eigenschaften der einzelnen Aktionen und Fehler beschrieben.

## <a name="experiment-actions"></a>Experimentaktionen

Eine Aktion ist jede Aktivität, die als ein Teil eines Chaos-Experiments orchestriert wird. Aktionen sind in Schritte und Verzweigungen unterteilt, sodass Aktionen entweder sequenziell oder parallel ausgeführt werden können. Jede Aktion verfügt über die folgenden Eigenschaften:
* **Name**: Die jeweilige Aktion, die stattfindet. Ein Name weist in der Regel die Form eines URN für die Aktion auf, z. B. „urn:
* **Typ**: Die Art und Weise, wie die Aktion ausgeführt wird. Aktionen können entweder *kontinuierlich* sein, d. h., dass die Aktion ununterbrochen über eine Zeitspanne ausgeführt wird (z. B. eine hohe CPU-Belastung für 10 Minuten), oder auch *diskret*, d. h., dass die Aktion nur ein Mal stattfindet (z. B. Neustarten einer Redis-Cache-Instanz).

## <a name="types-of-actions"></a>Arten von Aktionen

Es gibt zwei verschiedenartige Aktionen in Chaos Studio:
- **Fehler**: Diese Aktion führt zu einer Unterbrechung in einer oder mehreren Ressourcen.
- **Zeitverzögerungen**: Diese Aktion „wartet“ ohne Beeinflussung von Ressourcen. Es ist von Nutzen, zwischen den Fehlern zu pausieren und darauf zu warten, dass ein System durch den vorherigen Fehler beeinflusst wird.

## <a name="faults"></a>Fehler

Fehler sind die gängigste Aktion in Chaos Studio. Fehler verursachen eine Unterbrechung in einem System, sodass Sie überprüfen können, ob das System mit dieser Unterbrechung effektiv umgeht, ohne die Verfügbarkeit zu beeinflussen. Fehler können destruktiv sein (z. B. das Abbrechen eines Prozesses), Belastung anwenden (z. B. hohe Auslastung eines virtuellen Arbeitsspeichers), Latenz hinzufügen oder eine Konfigurationsänderung verursachen. Außer einem Namen und einem Typ, können Fehler auch eine *Dauer* (bei kontinuierlichen Aktionen) sowie *Parameter* aufweisen. Parameter beschreiben, wie der Fehler angewendet werden soll, und sind spezifisch für den Fehlernamen. Ein Parameter für den Cosmos DB-Failoverfehler ist beispielsweise der Lesebereich, der bei einem Fehler des Schreibbereichs zum Schreibbereich hochgestuft wird. Einige Parameter sind erforderlich, während andere optional sind.

Fehler sind abhängig vom Zieltyp entweder *agentenbasiert* oder *dienstspezifisch*. Für agentenbasierte Fehler muss der Chaos-Studio-Agent auf einem virtuellen Computer oder einer virtuellen Computer-Skalierungsgruppe installiert sein. Der Agent ist sowohl für Windows als auch für Linux verfügbar, aber nicht alle Fehler sind auf beiden Betriebssystemen verfügbar. Informationen zu den Fehlern, die im jeweiligen Betriebssystem unterstützt werden, finden Sie in der [Fehlerbibliothek](chaos-studio-fault-library.md). Für dienstspezifische Fehler ist kein Agent erforderlich, sie werden direkt für eine Azure-Ressource ausgeführt.

Fehler enthalten auch den Namen des Selektors, der die Ressourcen beschreibt, für die der Fehler ausgeführt wird. Weitere Informationen zu Selektoren finden Sie [im Artikel zu Chaos-Experimenten](chaos-studio-chaos-experiments.md). Ein Fehler kann sich nur auf eine Ressource auswirken, wenn ein Onboarding der Ressource als Ziel durchgeführt wurde und die entsprechende Fehlerfunktion für die Ressource aktiviert ist.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun die Aktionen und Fehler kennen gelernt haben, können Sie folgende Aufgabe durchführen:
- [Erstellen und Ausführen des ersten Experiments](chaos-studio-tutorial-service-direct-portal.md)
