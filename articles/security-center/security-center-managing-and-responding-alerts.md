---
title: Verwalten von Sicherheitswarnungen in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument erfahren Sie, wie Sie Azure Security Center-Funktionen verwenden, um Sicherheitswarnungen zu verwalten und auf diese zu reagieren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: dbed9883fbddc2efdaaa8ad252578afa928167bd
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237254"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center

In diesem Thema erfahren Sie, wie Sie Security Center-Warnungen anzeigen und verarbeiten und Ihre Ressourcen schützen.

Erweiterte Erkennungen, die Sicherheitswarnungen auslösen, sind nur in Azure Defender verfügbar. Eine kostenlose Testversion ist verfügbar. Informationen zum Durchführen eines Upgrades finden Sie unter [Aktivieren von Azure Defender](enable-azure-defender.md).

## <a name="what-are-security-alerts"></a>Was sind Sicherheitswarnungen?
Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von verbundenen Partnerlösungen wie Lösungen zum Schutz von Firewalls und Endpunkten, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Eine Liste mit priorisierten Sicherheitswarnungen wird im Security Center zusammen mit den Informationen angezeigt, die Sie zum schnellen Untersuchen des Problems benötigen, und Schritten zum Reagieren auf einen Angriff.

Informationen zu den verschiedenen Arten von Warnungen finden Sie unter [Sicherheitswarnungen (Referenzhandbuch)](alerts-reference.md).

Eine Übersicht darüber, wie Warnungen von Security Center generiert werden, finden Sie unter [Erkennen von und Reagieren auf Bedrohungen mit Azure Security Center](security-center-alerts-overview.md).


## <a name="manage-your-security-alerts"></a>Verwalten von Sicherheitswarnungen

1. Wählen Sie in Security Center auf der Seite „Übersicht“ entweder oben auf der Seite die Kachel **Sicherheitswarnungen** oder auf der Randleiste den Link „Sicherheitswarnungen“ aus.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="So gelangen Sie von der Azure Security Center-Seite „Übersicht“ auf die Seite „Sicherheitswarnungen“":::

    Die Seite „Sicherheitswarnungen“ wird geöffnet.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Liste der Sicherheitswarnungen von Azure Security Center":::

1. Wählen Sie zum Filtern der Liste der Warnungen einen der relevanten Filter aus. Optional können Sie mit der Option **Filter hinzufügen** weitere Filter hinzufügen.

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Hinzufügen von Filtern zur Warnungsansicht" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    Die Liste wird entsprechend den von Ihnen ausgewählten Filteroptionen aktualisiert. Diese Filterung kann sehr nützlich sein. Es könnte beispielsweise sein, dass Sie während der Untersuchung einer möglichen Sicherheitsverletzung im System die Sicherheitswarnungen überprüfen möchten, die in den letzten 24 Stunden aufgetreten sind.


## <a name="respond-to-security-alerts"></a>Reagieren auf Sicherheitswarnungen

1. Wählen Sie in der Liste **Sicherheitswarnungen** eine Warnung aus. Daraufhin wird ein Seitenbereich geöffnet, in dem eine Beschreibung der Warnung und alle betroffenen Ressourcen angezeigt werden. 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="Minidetailansicht einer Sicherheitswarnung":::

    > [!TIP]
    > Wenn dieser Seitenbereich geöffnet ist, können Sie die Liste der Warnungen schnell überprüfen, indem Sie auf der Tastatur die NACH-OBEN- oder NACH-UNTEN-TASTE drücken.

1. Wenn Sie weitere Informationen anzeigen möchten, wählen Sie **Vollständige Details anzeigen** aus.

    Im linken Bereich der Seite mit der Sicherheitswarnung werden allgemeine Informationen zur Sicherheitswarnung angezeigt: Titel, Schweregrad, Status, Zeitpunkt der Aktivität, Beschreibung der verdächtigen Aktivität und die betroffene Ressource. Neben der betroffenen Ressource finden Sie die Azure-Tags für die Ressource. Verwenden Sie diese Tags, um den Organisationskontext der Ressource abzuleiten, wenn Sie die Warnung untersuchen.

    Auf der rechten Seite befindet sich die Registerkarte **Warnungsdetails**, die detaillierte Informationen zur Warnung anzeigt, die Ihnen beim Untersuchen des Problems helfen: IP-Adressen, Dateien, Prozesse und vieles mehr.
     
    ![Vorschläge für Maßnahmen bei Sicherheitswarnungen](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Im rechten Bereich finden Sie auch die Registerkarte **Aktion ausführen**. Über diese Registerkarte können Sie weitere Aktionen in Bezug auf die Sicherheitswarnung ausführen. Folgende Aktionen sind möglich:
    - *Auswirkungen der Bedrohung minimieren*: Stellt manuelle Schritte zur Behebung dieser Sicherheitswarnung bereit.
    - *Künftige Angriffe verhindern*: Gibt Sicherheitsempfehlungen, um die Angriffsfläche zu verringern, den Sicherheitsstatus zu erhöhen und so künftige Angriffe zu verhindern.
    - *Automatische Reaktion auslösen*: Bietet die Möglichkeit, eine Logik-App als Reaktion auf diese Sicherheitswarnung auszulösen.
    - *Ähnliche Warnungen unterdrücken*: Bietet die Möglichkeit, zukünftige Warnungen mit ähnlichen Merkmalen zu unterdrücken, wenn die Warnung für Ihre Organisation nicht relevant ist.

    ![Registerkarte „Aktion ausführen“](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Weitere Informationen

In diesem Dokument haben Sie erfahren, wie Sie Sicherheitswarnungen anzeigen. Verwandte Informationen finden Sie auf den folgenden Seiten:

- [Konfigurieren von Warnungsunterdrückungsregeln](alerts-suppression-rules.md)
- [Automatisieren der Reaktionen auf Security Center-Trigger](workflow-automation.md)
- [Sicherheitswarnungen (Referenzhandbuch)](alerts-reference.md)
