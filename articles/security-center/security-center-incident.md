---
title: Verwalten von Sicherheitsvorfällen in Azure Security Center | Microsoft-Dokumentation
description: Dieses Dokument enthält hilfreiche Informationen zur Verwendung des Azure Security Centers zum Verwalten von Sicherheitsvorfällen.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: e385dec04db44e88ef5759cf8e2391b19b77dccc
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237522"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Verwalten von Sicherheitsvorfällen in Azure Security Center

Das Selektieren und Untersuchen von Sicherheitswarnungen kann auch für sehr erfahrene Sicherheitsanalysten zeitaufwändig sein. Häufig ist nur schwer zu erkennen, wo überhaupt begonnen werden soll. 

Security Center verwendet [Analysen](./security-center-alerts-overview.md), um die Informationen zwischen verschiedenen [Sicherheitswarnungen](security-center-managing-and-responding-alerts.md) zu verbinden. Mithilfe dieser Verbindungen kann Security Center eine Einzelansicht einer Angriffskampagne und der zugehörigen Warnungen bereitstellen, damit Sie die Aktionen des Angreifers und die betroffenen Ressourcen besser verstehen können.

Diese Seite enthält eine Übersicht über die Vorfälle in Security Center.

## <a name="what-is-a-security-incident"></a>Was ist ein Sicherheitsvorfall?

In Security Center ist ein Sicherheitsvorfall eine Aggregation aller Warnungen für eine Ressource, die [Kill Chain](alerts-reference.md#intentions)-Mustern entsprechen. Vorfälle werden auf der Seite [Sicherheitswarnungen](security-center-managing-and-responding-alerts.md) angezeigt. Wählen Sie einen Vorfall aus, um die zugehörigen Warnungen anzuzeigen und weitere Informationen zu erhalten.

## <a name="managing-security-incidents"></a>Verwalten von Sicherheitsvorfällen

1. Verwenden Sie auf der Seite „Warnungen“ von Security Center die Schaltfläche **Filter hinzufügen**, um nach dem Warnungsnamen **Sicherheitsincident in mehreren Ressourcen erkannt** zu filtern. 

    :::image type="content" source="media/security-center-incident/locating-incidents.png" alt-text="Suchen der Incidents auf der Seite „Warnungen“ in Azure Security Center":::

    In der gefilterten Liste werden nur Incidents angezeigt. Beachten Sie, dass Sicherheitsvorfälle ein anderes Symbol als Sicherheitswarnungen aufweisen.

    :::image type="content" source="media/security-center-incident/incidents-list.png" alt-text="Liste der Incidents auf der Seite „Warnungen“ in Azure Security Center":::

1. Um die Details eines Incident anzuzeigen, wählen Sie einen Incident aus der Liste aus. Ein Seitenbereich mit weiteren Details des Incident wird angezeigt.

    :::image type="content" source="media/security-center-incident/incident-quick-peek.png" alt-text="Seitenbereich mit Details zum Incident":::

1. Wenn Sie weitere Details anzeigen möchten, wählen Sie **Alle Informationen anzeigen** aus.

    [![Reagieren auf Sicherheitsincidents in Azure Security Center](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    Im linken Bereich der Seite mit dem Sicherheitsvorfall werden allgemeine Informationen über den Sicherheitsvorfall angezeigt: Titel, Schweregrad, Status, Zeitpunkt der Aktivität, Beschreibung und die betroffene Ressource. Neben der betroffenen Ressource sehen Sie die relevanten Azure-Tags. Verwenden Sie diese Tags, um den Organisationskontext der Ressource abzuleiten, wenn Sie die Warnung untersuchen.

    Der rechte Bereich enthält die Registerkarte **Warnungen** mit den Sicherheitswarnungen, die mit diesem Vorfall korreliert wurden. 

    >[!TIP]
    > Um weitere Informationen zu einer bestimmten Warnung zu erhalten, wählen Sie sie aus. 

    [![Registerkarte „Maßnahmen ergreifen“ des Incidents](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    Um zur Registerkarte **Maßnahmen ergreifen** zu wechseln, wählen Sie die Registerkarte oder die Schaltfläche am unteren Rand des rechten Bereichs aus. Verwenden Sie diese Registerkarte, um weitere Aktionen durchzuführen, wie z. B:
    - *Auswirkungen der Bedrohung minimieren*: Stellt manuelle Schritte zur Behebung dieses Sicherheitsvorfalls bereit.
    - *Künftige Angriffe verhindern*: Gibt Sicherheitsempfehlungen, um die Angriffsfläche zu verringern, den Sicherheitsstatus zu erhöhen und künftige Angriffe zu verhindern.
    - *Automatische Reaktion auslösen*: Bietet die Möglichkeit, eine Logik-App als Reaktion auf diesen Sicherheitsvorfall auszulösen.
    - *Ähnliche Warnungen unterdrücken*: Bietet die Möglichkeit, zukünftige Warnungen mit ähnlichen Merkmalen zu unterdrücken, wenn die Warnung für Ihre Organisation nicht relevant ist. 

   > [!NOTE]
   > Dieselbe Warnung kann als Teil eines Vorfalls vorhanden sein und als eigenständige Warnung angezeigt werden.

1. Führen Sie die für jede Warnung bereitgestellten Problembehandlungsschritte aus, um die Bedrohungen durch den Vorfall zu beseitigen.


## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurden die Sicherheitsvorfallfunktionen von Security Center erläutert. Zugehörige Informationen finden Sie auf den folgenden Seiten:

- [Sicherheitswarnungen in Security Center](security-center-alerts-overview.md)
- [Verwalten von und Reagieren auf Sicherheitswarnungen](security-center-managing-and-responding-alerts.md)