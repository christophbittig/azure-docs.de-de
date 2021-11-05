---
title: Verwalten von Sicherheitsincidents in Microsoft Defender für Cloud | Microsoft-Dokumentation
description: Dieses Dokument dient Ihnen als Hilfe bei der Verwendung von Microsoft Defender für Cloud zum Verwalten von Sicherheitsincidents.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3977d95358c20b98551170394bcd846d0b4e8358
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021399"
---
# <a name="manage-security-incidents-in-microsoft-defender-for-cloud"></a>Verwalten von Sicherheitsincidents in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Das Selektieren und Untersuchen von Sicherheitswarnungen kann auch für sehr erfahrene Sicherheitsanalysten zeitaufwändig sein. Häufig ist nur schwer zu erkennen, wo überhaupt begonnen werden soll. 

Defender für Cloud nutzt [Analysen](./alerts-overview.md), um die Informationen der einzelnen [Sicherheitswarnungen](managing-and-responding-alerts.md) miteinander zu verbinden. Mithilfe dieser Verbindungen kann Defender für Cloud eine zentrale Übersicht über einen Angriff und die zugehörigen Warnungen bereitstellen, damit Sie die Aktionen des Angreifers und die betroffenen Ressourcen besser verstehen können.

Auf dieser Seite erhalten Sie einen Überblick über die Incidents in Defender für Cloud.

## <a name="what-is-a-security-incident"></a>Was ist ein Sicherheitsvorfall?

In Defender für Cloud ist ein Sicherheitsincident eine Aggregation aller Warnungen für eine Ressource, die [Kill Chain](alerts-reference.md#intentions)-Mustern entsprechen. Vorfälle werden auf der Seite [Sicherheitswarnungen](managing-and-responding-alerts.md) angezeigt. Wählen Sie einen Vorfall aus, um die zugehörigen Warnungen anzuzeigen und weitere Informationen zu erhalten.

## <a name="managing-security-incidents"></a>Verwalten von Sicherheitsvorfällen

1. Verwenden Sie auf der Seite „Warnungen“ von Defender für Cloud die Schaltfläche **Filter hinzufügen**, um nach dem Warnungsnamen **Sicherheitsincident in mehreren Ressourcen erkannt** zu filtern. 

    :::image type="content" source="media/incidents/locating-incidents.png" alt-text="Anzeigen der Incidents auf der Seite „Warnungen“ in Microsoft Defender für Cloud":::

    In der gefilterten Liste werden nur Incidents angezeigt. Beachten Sie, dass Sicherheitsvorfälle ein anderes Symbol als Sicherheitswarnungen aufweisen.

    :::image type="content" source="media/incidents/incidents-list.png" alt-text="Liste mit Incidents auf der Seite „Warnungen“ in Microsoft Defender für Cloud":::

1. Um die Details eines Incident anzuzeigen, wählen Sie einen Incident aus der Liste aus. Ein Seitenbereich mit weiteren Details des Incident wird angezeigt.

    :::image type="content" source="media/incidents/incident-quick-peek.png" alt-text="Seitenbereich mit Details zum Incident":::

1. Wenn Sie weitere Details anzeigen möchten, wählen Sie **Alle Informationen anzeigen** aus.

    [![Reagieren auf Sicherheitsincidents in Microsoft Defender für Cloud](media/incidents/incident-details.png)](media/incidents/incident-details.png#lightbox)

    Im linken Bereich der Seite mit dem Sicherheitsvorfall werden allgemeine Informationen über den Sicherheitsvorfall angezeigt: Titel, Schweregrad, Status, Zeitpunkt der Aktivität, Beschreibung und die betroffene Ressource. Neben der betroffenen Ressource sehen Sie die relevanten Azure-Tags. Verwenden Sie diese Tags, um den Organisationskontext der Ressource abzuleiten, wenn Sie die Warnung untersuchen.

    Der rechte Bereich enthält die Registerkarte **Warnungen** mit den Sicherheitswarnungen, die mit diesem Vorfall korreliert wurden. 

    >[!TIP]
    > Um weitere Informationen zu einer bestimmten Warnung zu erhalten, wählen Sie sie aus. 

    [![Registerkarte „Maßnahmen ergreifen“ des Incidents](media/incidents/incident-take-action-tab.png)](media/incidents/incident-take-action-tab.png#lightbox)

    Um zur Registerkarte **Maßnahmen ergreifen** zu wechseln, wählen Sie die Registerkarte oder die Schaltfläche am unteren Rand des rechten Bereichs aus. Verwenden Sie diese Registerkarte, um weitere Aktionen durchzuführen, wie z. B:
    - *Auswirkungen der Bedrohung minimieren*: Stellt manuelle Schritte zur Behebung dieses Sicherheitsvorfalls bereit.
    - *Künftige Angriffe verhindern*: Gibt Sicherheitsempfehlungen, um die Angriffsfläche zu verringern, den Sicherheitsstatus zu erhöhen und künftige Angriffe zu verhindern.
    - *Automatische Reaktion auslösen*: Bietet die Möglichkeit, eine Logik-App als Reaktion auf diesen Sicherheitsvorfall auszulösen.
    - *Ähnliche Warnungen unterdrücken*: Bietet die Möglichkeit, zukünftige Warnungen mit ähnlichen Merkmalen zu unterdrücken, wenn die Warnung für Ihre Organisation nicht relevant ist. 

   > [!NOTE]
   > Dieselbe Warnung kann als Teil eines Vorfalls vorhanden sein und als eigenständige Warnung angezeigt werden.

1. Führen Sie die für jede Warnung bereitgestellten Problembehandlungsschritte aus, um die Bedrohungen durch den Vorfall zu beseitigen.


## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurden die Funktionen für Sicherheitsincidents von Defender für Cloud beschrieben. Zugehörige Informationen finden Sie auf den folgenden Seiten:

- [Sicherheitswarnungen in Defender für Cloud](alerts-overview.md)
- [Verwalten von und Reagieren auf Sicherheitswarnungen](managing-and-responding-alerts.md)
