---
title: Dashboard „Regionsübergreifende Verschiebung“
description: Überwachen Sie Ihre Ressourcen, die regionsübergreifend verschoben werden, mithilfe des Dashboards „Regionsübergreifende Verschiebung“.
author: Aarthi-Vijayaraghavan
manager: sutalasi
ms.service: resource-move
ms.topic: how-to
ms.date: 08/30/2021
ms.author: AarthiV
ms.openlocfilehash: 9339b61135d4b8b21f88b35a842a080dac9f8e69
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621029"
---
# <a name="move-across-region-dashboard"></a>Dashboard „Regionsübergreifende Verschiebung“
In diesem Artikel wird beschrieben, wie Sie die Ressourcen, die Sie regionsübergreifend verschieben, über das Dashboard „Regionsübergreifende Verschiebung“ in Azure Resource Mover überwachen. 
## <a name="monitor-via-the-dashboard"></a>Überwachen über das Dashboard
1. Wählen Sie in **Azure Resource Mover** im linken Navigationsbereich **Übersicht** aus. Sie können zwischen zwei Seiten wechseln: **Erste Schritte** und **Dashboard „Regionsübergreifende Verschiebung“** . Die Seite **Erste Schritte** bietet Optionen zum Verschieben Ihrer Ressourcen zwischen Abonnements, über Ressourcengruppen hinweg und über Regionen hinweg.
Auf der Seite **Dashboard „Regionsübergreifende Verschiebung“** werden alle Überwachungsinformationen Ihrer regionsübergreifenden Verschiebung an einem zentralen Ort zusammengefasst.
    [![Dashboardregisterkarte „Regionsübergreifend verschieben“](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)](media\move-across-region-dashboard\move-across-region-dashboard-tab.png)
2. Das Dashboard listet alle von Ihnen erstellten Verschiebungskombinationen auf. Die folgenden beiden Abschnitte werden verwendet, um den Status Ihrer regionsübergreifenden Verschiebung zu erfassen.
    Überwachen Sie unter **Ressourcen nach Verschiebungsstatus** den Prozentsatz und die Anzahl der Ressourcen in den einzelnen Zuständen.
    Überwachen Sie unter **Fehlerzusammenfassung** die aktiven Fehler, die behoben werden müssen, bevor Sie erfolgreich in die Zielregion verschoben werden können.
    [![Abschnitt „Status und Probleme“](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)](media\move-across-region-dashboard\move-across-region-dashboard-status-issues.png)
> [!NOTE]
> Im Dashboard sind nur die Quell-Ziel-Kombinationen aufgeführt, die bereits in Ihrem ausgewählten Abonnement erstellt wurden.

3. Verwenden Sie die Filter, um Ihr bevorzugtes **Abonnement**, die **Quellregion** und die **Zielregion** auszuwählen.
    [![Filter](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)](media\move-across-region-dashboard\move-across-region-dashboard-filters.png)
4. Navigieren Sie zur Detailseite, indem Sie **Alle Ressourcen anzeigen** neben Quelle-Ziel auswählen.
    [![Details](media\move-across-region-dashboard\move-across-region-dashboard-details.png)](media\move-across-region-dashboard\move-across-region-dashboard-details.png)
## <a name="next-steps"></a>Nächste Schritte
[Informationen](about-move-process.md) zum Verschiebungsvorgang.
